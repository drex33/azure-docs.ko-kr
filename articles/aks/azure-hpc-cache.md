---
title: Azure HPC Cache Azure Kubernetes Service 통합
description: HPC Cache Azure Kubernetes Service 통합하는 방법에 대해 알아봅니다.
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 208147894b5fb483bc9ebcc49c01fd93013c3a3d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491529"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>Azure HPC Cache Azure Kubernetes Service 통합

[Azure HPC Cache][hpc-cache] HPC(고성능 컴퓨팅) 작업을 위해 데이터에 대한 액세스 속도를 높입니다. Azure에서 파일을 캐싱하여 Azure HPC Cache는 기존 워크플로에 대한 클라우드 컴퓨팅의 확장성을 제공합니다. 이 문서에서는 AKS(Azure Kubernetes Service)와 Azure HPC Cache 통합하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> AKS 클러스터는 [Azure HPC Cache 지원하는 지역에][hpc-cache-regions]있어야 합니다.

또한 Azure CLI 버전 2.7 이상도 설치하고 구성해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.  HPC Cache Azure CLI 사용에 대한 자세한 내용은 [hpc-cache-cli-prerequisites를] 참조하세요.

또한 hpc-cache Azure CLI 확장을 설치해야 합니다.  다음 작업을 수행하세요.

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>Azure HPC Cache 설정

이 섹션에서는 HPC Cache 만들고 구성하는 단계를 설명합니다.

### <a name="1-find-the-aks-node-resource-group"></a>1. AKS 노드 리소스 그룹 찾기

먼저 [az aks show][az-aks-show] 명령을 사용하여 리소스 그룹 이름을 가져온 다음 `--query nodeResourceGroup` 쿼리 매개 변수를 추가합니다. 동일한 리소스 그룹에 HPC Cache 만듭니다.

다음 예제에서는 리소스 그룹 이름 *myResourceGroup* 에서 *myAKSCluster라는 AKS* 클러스터의 노드 리소스 그룹 이름을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. 캐시 서브넷 만들기

HPC Cache 실행하기 전에 충족해야 하는 많은 [필수 조건이][hpc-cache-prereqs] 있습니다.  가장 중요한 것은 캐시에 64개 이상의 IP 주소를 사용할 수 있는 *전용* 서브넷이 필요하다는 것입니다. 이 서브넷은 다른 VM 또는 컨테이너를 호스트해서는 안됩니다.  이 서브넷은 AKS 노드에서 액세스할 수 있어야 합니다.

전용 HPC Cache 서브넷을 만듭니다.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

*Microsoft.StorageCache* 리소스 공급자를 등록합니다.

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> 리소스 공급자 등록을 완료하는 데 다소 시간이 걸릴 수 있습니다.

### <a name="3-create-the-hpc-cache"></a>3. HPC Cache 만들기

1단계의 노드 리소스 그룹 및 AKS 클러스터와 동일한 지역에 HPC Cache 만듭니다. [az hpc-cache create][az-hpc-cache-create]를 사용합니다.

> [!NOTE]
> HPC Cache 만드는 데 약 20분이 걸립니다.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. 스토리지 계정 및 새 컨테이너 만들기

Blob Storage 컨테이너에 대한 Azure Storage 계정을 만듭니다.  HPC Cache Blob Storage 컨테이너에 저장된 콘텐츠를 캐시합니다.

> [!IMPORTANT]
> 고유한 스토리지 계정 이름을 선택해야 합니다.  'uniquestorageaccount'를 고유한 것으로 바꿉니다.

선택한 스토리지 계정 이름을 사용할 수 있는지 확인합니다.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

스토리지 계정 내에 Blob 컨테이너를 만듭니다.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --auth-mode login
```

Azure HPC Cache 서비스 계정에 스토리지 계정 및 Blob 컨테이너에 액세스할 수 있는 권한을 제공합니다.

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. 스토리지 대상 구성

스토리지 대상으로 HPC Cache Blob 컨테이너를 추가합니다.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. 클라이언트 부하 분산 설정

클라이언트용 IP 주소에 대한 Azure 프라이빗 DNS 영역을 만듭니다.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

라운드 로빈 DNS 이름을 만듭니다.

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>AKS 영구 볼륨 만들기

영구 `pv-nfs.yaml` [볼륨을][persistent-volume]정의하는 파일을 만듭니다.

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

먼저 Kubernetes 클러스터에 대한 자격 증명이 있는지 확인합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

*서버* 및 *경로* 를 이전 단계에서 만든 NFS(네트워크 파일 시스템) 볼륨의 값으로 업데이트합니다. [kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨을 만듭니다.

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 영구 볼륨의 상태가 **사용 가능인지** 확인합니다.

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

영구 `pvc-nfs.yaml` [볼륨 클레임을][persistent-volume-claim]정의하는 를 만듭니다. 예:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 100Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구적 볼륨 클레임을 만듭니다.

```console
kubectl apply -f pvc-nfs.yaml
```

영구적 볼륨 클레임의 상태가 [kubectl describe][kubectl-describe] 명령을 사용하여 **바인딩되었는지** 확인합니다.

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>Pod를 HPC Cache 탑재

영구적 `nginx-nfs.yaml` 볼륨 클레임을 사용하는 Pod를 정의하는 파일을 만듭니다. 예를 들면 다음과 같습니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```console
kubectl apply -f nginx-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 Pod가 실행 중인지 확인합니다.

```console
kubectl describe pod nginx-nfs
```

Pod에 연결하기 위해 [kubectl exec][kubectl-exec]를 사용하여 Pod에 해당 볼륨이 탑재되었는지 확인한 다음, `df -h` 명령으로 볼륨이 탑재되었는지 확인합니다.

```console
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

### <a name="running-applications-as-non-root"></a>루트가 아닌 애플리케이션으로 실행

루트가 아닌 사용자로 애플리케이션을 실행해야 하는 경우 디렉터리를 다른 사용자에게 보내기 위해 루트 스쿼시를 사용하지 않도록 설정해야 할 수 있습니다.  루트가 아닌 사용자는 파일 시스템에 액세스하려면 디렉터리를 소유해야 합니다.  사용자가 디렉터리를 소유하려면 루트 사용자가 해당 사용자에게 디렉터리를 만들어야 하지만 HPC Cache 루트를 스쿼시하는 경우 루트 사용자(UID 0)가 익명 사용자에 매핑되기 때문에 이 작업이 거부됩니다.  루트 스쿼시 및 클라이언트 액세스 정책에 대한 자세한 내용은 [여기를 참조하십시오.][hpc-cache-access-policies]

### <a name="sending-feedback"></a>피드백 보내기

많은 의견 부탁드립니다.  에 피드백 또는 질문을 <aks-hpccache-feed@microsoft.com> 보내주세요.

## <a name="next-steps"></a>다음 단계

* Azure HPC Cache 대한 자세한 내용은 HPC Cache [개요를 참조하세요.][hpc-cache]
* AKS에서 NFS를 사용하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)를 사용하여 NFS(네트워크 파일 시스템) Linux 서버 볼륨 수동 생성 및 사용][aks-nfs]을 참조하세요.

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
