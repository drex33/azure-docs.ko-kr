---
title: Azure Kubernetes Service와 Azure NetApp Files 통합 | Microsoft Docs
description: Azure Kubernetes Service를 사용 하 여 Azure NetApp Files를 프로 비전 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/05/2021
ms.openlocfilehash: d5d79a02a883b504eec57a9658e9ea7f0694d796
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613456"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files와 Azure Kubernetes Service의 통합

영구적 볼륨은 Kubernetes Pod와 함께 사용하기 위해 프로비전된 스토리지 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 AKS (Azure Kubernetes Service) 클러스터에서 pod에 사용할 [Azure NetApp Files][anf] 볼륨을 만드는 방법을 보여 줍니다.

[Azure NetApp Files][anf]는 Azure에서 실행되는 엔터프라이즈급, 고성능, 요금제 파일 스토리지 서비스입니다. Kubernetes 사용자는 Kubernetes 워크 로드에 Azure NetApp Files 볼륨을 사용 하는 경우 두 가지 옵션을 제공 합니다.

* **정적** Azure NetApp Files 볼륨 만들기:이 시나리오에서는 볼륨 만들기가 AKS 외부에서 이루어집니다. 볼륨은/Azure UI를 사용 하 여 만든 `az` 다음를 만들어 Kubernetes에 노출 `PersistentVolume` 합니다. 정적으로 만들어진 ANF 볼륨에는 많은 제한 사항이 있습니다 (예: 확장, 과도 프로 비전 등). 대부분의 사용 사례에는 사용 하지 않는 것이 좋습니다.
* **주문형** Azure NetApp Files 볼륨 만들기, Kubernetes를 통한 오케스트레이션:이 방법은 Kubernetes를 통해 직접 여러 볼륨을 만드는 데 사용 되는 **기본 설정** 모드 이며 [Astra Trident](https://docs.netapp.com/us-en/trident/index.html)를 사용 하 여 수행 됩니다. Astra Trident는 기본적으로 Kubernetes를 통해 볼륨을 프로 비전 하는 데 도움이 되는 CSI 규격 동적 저장소 orchestrator입니다.

CSI 드라이버를 사용 하 여 AKS 워크 로드에서 Azure NetApp Files 볼륨을 직접 사용 하는 것은 대부분의 사용 사례에서 **매우 권장** 됩니다. 이 요구 사항은 Kubernetes에 대 한 오픈 소스 동적 저장소 orchestrator 인 Astra Trident를 사용 하 여 수행 됩니다. Astra Trident는 NetApp에서 완전히 지 원하는 엔터프라이즈급 저장소 orchestrator 용도의 Kubernetes 용으로 작성 되었습니다. 저장소 프로 비전을 자동화 하 여 Kubernetes 환경에서 저장소에 대 한 액세스를 간소화 합니다. 소비자는 Azure NetApp Files에 대 한 Astra Trident의 CSI 드라이버를 활용 하 여 요청 시 기본 세부 정보 및 만들기/확장/스냅숏 볼륨을 추상화할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> 또한 AKS 클러스터는 [Azure NetApp Files를 지원하는 지역에 있어야][anf-regions] 합니다.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="prerequisites"></a>필수 구성 요소

Azure NetApp Files 사용 하는 경우 다음 사항을 고려해 야 합니다.

* Azure NetApp Files는 [선택한 Azure 지역][anf-regions]에서만 사용할 수 있습니다.
* AKS 클러스터의 초기 배포 후에 Azure NetApp Files 볼륨을 정적 또는 동적으로 프로 비전 하도록 선택할 수 있습니다.
* Azure NetApp Files에서 동적 프로 비전을 사용 하려면 [Astra Trident](https://netapp-trident.readthedocs.io/) 버전 19.07 이상을 설치 하 고 구성 합니다.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files 구성

*Microsoft.NetApp* 리소스 공급자를 등록합니다.

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 이 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다.

AKS와 함께 사용할 Azure NetApp 계정을 만들 때 **노드** 리소스 그룹에서 계정을 만들어야 합니다. 먼저 [az aks show][az-aks-show] 명령을 사용하여 리소스 그룹 이름을 가져온 다음 `--query nodeResourceGroup` 쿼리 매개 변수를 추가합니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster* 에 대한 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[az netappfiles account create][az-netappfiles-account-create]를 사용하여 **노드** 리소스 그룹과 AKS 클러스터와 동일한 지역에 Azure NetApp Files 계정을 만듭니다. 다음 예제에서는 *MC_myResourceGroup_myAKSCluster_eastus* 리소스 그룹 및 *eastus* 지역에 *myaccount1* 이라는 계정을 만듭니다.

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[az netappfiles pool create][az-netappfiles-pool-create]를 사용하여 새 용량 풀을 만듭니다. 다음 예제에서는 크기가 4TB이고 *프리미엄* 서비스 수준의 *mypool1* 이라는 이름의 새 용량 풀을 만듭니다.

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[az network vnet subnet create][az-network-vnet-subnet-create]를 사용하여 [Azure NetApp Files에 위임할][anf-delegate-subnet] 서브넷을 만듭니다. *이 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

볼륨은 정적 또는 동적으로 프로 비전 할 수 있습니다. 두 옵션에 대해서는 아래에서 자세히 설명 합니다.

## <a name="provision-azure-netapp-files-volumes-statically"></a>정적으로 Azure NetApp Files 볼륨 프로 비전

[az netappfiles volume create][az-netappfiles-volume-create]를 사용하여 볼륨을 만듭니다.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

### <a name="create-the-persistentvolume"></a>PersistentVolume 만들기

[az netappfiles volume show][az-netappfiles-volume-show]를 사용하여 볼륨의 세부 정보 나열

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

PersistentVolume을 정의하는 `pv-nfs.yaml`을 만듭니다. 이전 명령에서 `path`를 *creationToken* 으로, `server`를 *ipAddress* 로 대체합니다. 예를 들면 다음과 같습니다.

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

*서버* 및 *경로* 를 이전 단계에서 만든 NFS(네트워크 파일 시스템) 볼륨의 값으로 업데이트합니다. [kubectl apply][kubectl-apply] 명령을 사용하여 PersistentVolume을 만듭니다.

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 PersistentVolume의 *Status* 가 *Available* 인지 확인합니다.

```console
kubectl describe pv pv-nfs
```

### <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim 만들기

PersistentVolume을 정의하는 `pvc-nfs.yaml`을 만듭니다. 예를 들면 다음과 같습니다.

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
      storage: 1Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 PersistentVolumeClaim을 만듭니다.

```console
kubectl apply -f pvc-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 PersistentVolumeClaim의 *Status* 가 *Bound* 인지 확인합니다.

```console
kubectl describe pvc pvc-nfs
```

### <a name="mount-with-a-pod"></a>Pod를 사용하여 탑재

PersistentVolumeClaim을 사용하는 Pod를 정의하는 `nginx-nfs.yaml`을 만듭니다. 예를 들면 다음과 같습니다.

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
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
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

[kubectl describe][kubectl-describe] 명령을 사용하여 Pod가 *Running* 인지 확인합니다.

```console
kubectl describe pod nginx-nfs
```

Pod에 연결하기 위해 [kubectl exec][kubectl-exec]를 사용하여 Pod에 해당 볼륨이 탑재되었는지 확인한 다음, `df -h` 명령으로 볼륨이 탑재되었는지 확인합니다.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>동적으로 Azure NetApp Files 볼륨 프로 비전

### <a name="install-and-configure-astra-trident"></a>Astra Trident 설치 및 구성

볼륨을 동적으로 프로 비전 하려면 Astra Trident를 설치 해야 합니다. Astra Trident는 Kubernetes 용으로 작성 된 NetApp의 동적 저장소 provisioner. Astra Trident의 업계 표준 [CSI (Container Storage Interface)](https://kubernetes-csi.github.io/docs/) 드라이버를 사용 하 여 Kubernetes 응용 프로그램의 저장소 사용량을 간소화 합니다. Astra Trident는 Kubernetes 클러스터에서 pod로 배포 하 고 Kubernetes 워크 로드에 대 한 동적 저장소 오케스트레이션 서비스를 제공 합니다.

[설명서](https://netapp-trident.readthedocs.io/en/latest/index.html)에서 자세히 알아볼 수 있습니다.

다음 단계로 진행 하기 전에 다음을 수행 해야 합니다.

1. **Astra Trident를 설치** 합니다. Trident는 연산자/투구 차트/를 사용 하 여 설치할 수 있습니다 `tridentctl` . 아래에 제공 된 지침은 연산자를 사용 하 여 Astra Trident를 설치 하는 방법을 설명 합니다. 다른 설치 방법의 작동 방식에 대 한 자세한 내용은 [설치 가이드](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html)를 참조 하세요.

2. **백 엔드를 만듭니다**. Azure NetApp Files 구독 및 볼륨을 만들어야 하는 위치에 대해 Astra Trident에 지시 하기 위해 백 엔드가 생성 됩니다. 이 단계에서는 이전 단계에서 만든 계정에 대 한 세부 정보가 필요 합니다.

#### <a name="install-astra-trident-using-the-operator"></a>연산자를 사용 하 여 Astra Trident 설치

이 섹션에서는 연산자를 사용 하 여 Astra Trident를 설치 하는 과정을 안내 합니다. 다른 방법 중 하나를 사용 하 여를 설치 하도록 선택할 수도 있습니다.

* [투구 차트](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/operator-deploy.html#deploy-trident-operator-by-using-helm).
* [trientctl](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/tridentctl-deploy.html).

각 옵션이 작동 하는 방식을 이해 하 고 가장 적합 한 작업을 식별 하려면 [Trident 배포](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) 를 참조 하세요.

[GitHub 리포지토리에서](https://github.com/NetApp/trident/releases)Astra Trident를 다운로드 합니다. 원하는 버전을 선택 하 고 설치 관리자 번들을 다운로드 합니다.

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
을 사용 하 여 연산자를 배포 `deploy/bundle.yaml` 합니다.

```console
$  kubectl create ns trident

namespace/trident created

$  kubectl apply -f trident-installer/deploy/bundle.yaml -n trident

serviceaccount/trident-operator created
clusterrole.rbac.authorization.k8s.io/trident-operator created
clusterrolebinding.rbac.authorization.k8s.io/trident-operator created
deployment.apps/trident-operator created
podsecuritypolicy.policy/tridentoperatorpods created
```

`TridentOrchestrator`Astra Trident를 설치 하려면를 만듭니다.

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

운영자는 사양에 제공 된 매개 변수를 사용 하 여를 설치 합니다 `TridentOrchestrator` . 광범위 한 [설치](https://netapp-trident.readthedocs.io/en/latest/kubernetes/deploying/deploying.html) 및 [백 엔드 가이드](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/backends/index.html)에서 구성 매개 변수 및 예제 백 엔드에 대해 알아볼 수 있습니다.

Astra Trident가 설치 되었는지 확인 합니다. 

```console
$  kubectl describe torc trident
Name:         trident
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  trident.netapp.io/v1
Kind:         TridentOrchestrator
...
Spec:
  Debug:      true
  Namespace:  trident
Status:
  Current Installation Params:
    IPv6:                       false
    Autosupport Hostname:
    Autosupport Image:          netapp/trident-autosupport:21.01
    Autosupport Proxy:
    Autosupport Serial Number:
    Debug:                      true
    Enable Node Prep:           false
    Image Pull Secrets:
    Image Registry:
    k8sTimeout:           30
    Kubelet Dir:          /var/lib/kubelet
    Log Format:           text
    Silence Autosupport:  false
    Trident Image:        netapp/trident:21.07.1
  Message:                Trident installed
  Namespace:              trident
  Status:                 Installed
  Version:                v21.07.1
Events:
  Type    Reason      Age   From                        Message
  ----    ------      ----  ----                        -------
  Normal  Installing  74s   trident-operator.netapp.io  Installing Trident
  Normal  Installed   67s   trident-operator.netapp.io  Trident installed
```

### <a name="create-a-backend"></a>백 엔드 만들기

Astra Trident를 설치한 후 Azure NetApp Files 구독을 가리키는 백 엔드를 만듭니다.

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

명령을 실행 하기 전에 `backend-anf.yaml` 다음과 같은 Azure NetApp Files 구독에 대 한 세부 정보를 포함 하도록 업데이트 해야 합니다.

* `subscriptionID` Azure NetApp Files 사용 하도록 설정 된 Azure 구독입니다. Component 
* `tenantID``clientID` `clientSecret` Azure NetApp Files 서비스에 대 한 충분 한 권한이 있는 Azure Active Directory (AD)의 [앱 등록](../active-directory/develop/howto-create-service-principal-portal.md) 에서, 및입니다. 앱 등록은 `Owner` `Contributor` Azure에서 미리 정의 된 또는 역할을 수행 해야 합니다.
* 하나 이상의 위임 된 서브넷을 포함 하는 Azure 위치입니다.

또한 다른 서비스 수준을 제공 하도록 선택할 수 있습니다. Azure NetApp Files는 표준, Premium 및 Ultra의 세 가지 [서비스 수준을](../azure-netapp-files/azure-netapp-files-service-levels.md)제공 합니다.

### <a name="create-a-storageclass"></a>StorageClass 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Azure NetApp Files 볼륨을 사용 하려면 저장소 클래스를 만들어야 합니다. 이라는 파일을 만들고 `anf-storageclass.yaml` 아래 제공 된 매니페스트에 복사 합니다.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-netapp-files
provisioner: csi.trident.netapp.io
parameters:
  backendType: "azure-netapp-files"
  fsType: "nfs"
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 저장소 클래스를 만듭니다.

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>PersistentVolumeClaim 만들기

PersistentVolumeClaim (PVC)는 사용자가 저장소에 대 한 요청입니다. PersistentVolumeClaim를 만들 때 Astra Trident는 자동으로 Azure NetApp Files 볼륨을 만들고 Kubernetes 워크 로드에서 사용할 수 있도록 합니다.

이라는 파일을 만들고 `anf-pvc.yaml` 다음 매니페스트를 제공 합니다. 이 예제에서는 *Readwritemany* 인 TiB 볼륨을 만듭니다.

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: anf-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: azure-netapp-files
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 클레임을 만듭니다.

```console
$  kubectl apply -f anf-pvc.yaml

persistentvolumeclaim/anf-pvc created

$  kubectl get pvc
kubectl get pvc -n trident
NAME      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS         AGE
anf-pvc   Bound    pvc-bffa315d-3f44-4770-86eb-c922f567a075   1Ti        RWO            azure-netapp-files   62s
```

### <a name="use-the-persistent-volume"></a>영구적 볼륨 사용

PVC를 만든 후 pod를 분리 하 여 Azure NetApp Files 볼륨에 액세스할 수 있습니다. 아래 매니페스트를 사용 하 여 이전 단계에서 만든 Azure NetApp Files 볼륨을 탑재 하는 NGINX pod를 정의할 수 있습니다. 이 예제에서는 볼륨이에 탑재 됩니다 `/mnt/data` .

다음 매니페스트를 포함 하는 라는 파일을 만듭니다 `anf-nginx-pod.yaml` .

```yml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: mcr.microsoft.com/oss/nginx/nginx:latest1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/data"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: anf-pvc
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```console
$  kubectl apply -f anf-nginx-pod.yaml

pod/nginx-pod created
```

이제 Kubernetes에서 볼륨을 탑재 하 고의 컨테이너 내에서 액세스할 수 있는 pod를 만들었습니다 `nginx` `/mnt/data` . 다음을 사용 하 여 pod에 대 한 이벤트 로그를 확인 합니다 `kubectl describe` .

```console
$  kubectl describe pod nginx-pod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  anf-pvc
    ReadOnly:   false
  default-token-k7952:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-k7952
    Optional:    false
[...]
Events:
  Type    Reason                  Age   From                     Message
  ----    ------                  ----  ----                     -------
  Normal  Scheduled               15s   default-scheduler        Successfully assigned trident/nginx-pod to brameshb-non-root-test
  Normal  SuccessfulAttachVolume  15s   attachdetach-controller  AttachVolume.Attach succeeded for volume "pvc-bffa315d-3f44-4770-86eb-c922f567a075"
  Normal  Pulled                  12s   kubelet                  Container image "mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine" already present on machine
  Normal  Created                 11s   kubelet                  Created container nginx
  Normal  Started                 10s   kubelet                  Started container nginx
```

Astra Trident는 다음과 같은 Azure NetApp Files의 많은 기능을 지원 합니다.

* [볼륨 확장](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/vol-expansion.html)
* [주문형 볼륨 스냅숏](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/snapshots.html)
* [볼륨 가져오기](https://netapp-trident.readthedocs.io/en/latest/kubernetes/operations/tasks/volumes/import.html)

## <a name="next-steps"></a>다음 단계

* Azure NetApp Files에 대한 자세한 내용은 [Azure NetApp Files란?][anf]을 참조하세요.

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netapp-files-dynamic]: azure-netapp-files-dynamic.md
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
