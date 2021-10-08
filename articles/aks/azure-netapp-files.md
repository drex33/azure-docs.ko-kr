---
title: Azure NetApp Files Azure Kubernetes Service | 통합 Microsoft Docs
description: Azure Kubernetes Service 사용하여 Azure NetApp Files 프로비전하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/07/2021
ms.openlocfilehash: f4b9c8316ff9cd77017fc49dbe6846ed840f5afb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658978"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files와 Azure Kubernetes Service의 통합

영구적 볼륨은 Kubernetes Pod와 함께 사용하기 위해 프로비전된 스토리지 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 [AKS(Azure Kubernetes Service)][anf] 클러스터의 Pod에서 사용할 Azure NetApp Files 볼륨을 만드는 방법을 보여줍니다.

[Azure NetApp Files][anf]는 Azure에서 실행되는 엔터프라이즈급, 고성능, 요금제 파일 스토리지 서비스입니다. Kubernetes 사용자는 Kubernetes 워크로드에 Azure NetApp Files 볼륨을 사용할 때 두 가지 옵션을 사용할 수 있습니다.

* **정적으로** Azure NetApp Files 볼륨 만들기: 이 시나리오에서는 AKS 외부에서 볼륨을 만듭니다. 볼륨은 /Azure UI를 사용하여 만든 `az` 다음 를 만들어 Kubernetes에 `PersistentVolume` 노출됩니다. 정적으로 생성된 ANF 볼륨에는 많은 제한 사항(예: 확장할 수 없음, 과도 프로비전 필요 등)이 있으며 대부분의 사용 사례에는 권장되지 않습니다.
* 요청 **시** Azure NetApp Files 볼륨 만들기 , Kubernetes를 통해 오케스트레이션: 이 방법은 Kubernetes를 통해 직접 여러 볼륨을 만들기 위한 기본 작업 **모드이며** [Astra Trident](https://docs.netapp.com/us-en/trident/index.html)를 사용하여 수행됩니다. Astra Trident는 Kubernetes를 통해 기본적으로 볼륨을 프로비전하는 데 도움이 되는 CSI 규격 동적 스토리지 오케스트레이터입니다.

대부분의 사용 사례에서는 CSI 드라이버를 사용하여 AKS 워크로드에서 Azure NetApp Files 볼륨을 직접 사용하는 **것이 좋습니다.** 이 요구 사항은 Kubernetes용 오픈 소스 동적 스토리지 오케스트레이터인 Astra Trident를 사용하여 충족됩니다. Astra Trident는 Kubernetes용으로 특별히 빌드된 엔터프라이즈급 스토리지 오케스트레이터로, NetApp에서 완벽하게 지원됩니다. 스토리지 프로비저닝을 자동화하여 Kubernetes 환경에서 스토리지에 대한 액세스를 간소화합니다. 소비자는 Azure NetApp Files 위해 Astra Trident의 CSI 드라이버를 활용하여 기본 세부 정보를 추상화하고 주문형 볼륨을 생성/확장/스냅샷할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> 또한 AKS 클러스터는 [Azure NetApp Files를 지원하는 지역에 있어야][anf-regions] 합니다.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="prerequisites"></a>사전 요구 사항

Azure NetApp Files 사용하는 경우 다음과 같은 고려 사항이 적용됩니다.

* Azure NetApp Files는 [선택한 Azure 지역][anf-regions]에서만 사용할 수 있습니다.
* AKS 클러스터의 초기 배포 후 정적 또는 동적으로 Azure NetApp Files 볼륨을 프로비전하도록 선택할 수 있습니다.
* Azure NetApp Files 동적 프로비저닝을 사용하려면 [Astra Trident](https://docs.netapp.com/us-en/trident/index.html) 버전 19.07 이상 버전을 설치하고 구성합니다.

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

볼륨을 정적 또는 동적으로 프로비전할 수 있습니다. 두 옵션 모두 아래에서 자세히 설명합니다.

## <a name="provision-azure-netapp-files-volumes-statically"></a>정적으로 Azure NetApp Files 볼륨 프로비전

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

## <a name="provision-azure-netapp-files-volumes-dynamically"></a>동적으로 Azure NetApp Files 볼륨 프로비전

### <a name="install-and-configure-astra-trident"></a>Astra Trident 설치 및 구성

볼륨을 동적으로 프로비전하려면 Astra Trident를 설치해야 합니다. Astra Trident는 Kubernetes용으로 특별히 빌드된 NetApp의 동적 스토리지 프로비저닝 장치입니다. Astra Trident의 업계 표준 [CSI(컨테이너 Storage 인터페이스)](https://kubernetes-csi.github.io/docs/) 드라이버를 사용하여 Kubernetes 애플리케이션의 스토리지 사용을 간소화합니다. Astra Trident는 Kubernetes 클러스터에 Pod로 배포하고 Kubernetes 워크로드에 대한 동적 스토리지 오케스트레이션 서비스를 제공합니다.

[설명서] 에서 자세히 알아볼 수 https://docs.netapp.com/us-en/trident/index.html) 있습니다.

다음 단계를 진행하기 전에 다음을 수행해야 합니다.

1. **Astra Trident** 를 설치합니다. Trident는 operator/Helm chart/를 사용하여 설치할 수 `tridentctl` 있습니다. 아래에 제공된 지침에서는 운영자를 사용하여 Astra Trident를 설치하는 방법을 설명합니다. 다른 설치 방법의 작동 방식을 알아보려면 [설치 가이드를 참조하세요.](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html)

2. **백 엔드 를 만듭니다.** Azure NetApp Files 구독 및 볼륨을 만들어야 하는 위치에 대해 Astra Trident에 지시하기 위해 백 엔드가 만들어집니다. 이 단계에서는 이전 단계에서 만든 계정에 대한 세부 정보가 필요합니다.

#### <a name="install-astra-trident-using-the-operator"></a>운영자를 사용하여 Astra Trident 설치

이 섹션에서는 운영자를 사용하여 Astra Trident를 설치하는 작업을 안내합니다. 다른 방법 중 하나를 사용하여 설치하도록 선택할 수도 있습니다.

* [Helm 차트](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-operator.html).
* [tridentctl](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy-tridentctl.html).

각 옵션의 작동 방식을 이해하고 가장 적합한 옵션을 식별하려면 [Trident 배포를](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html) 참조하세요.

[GitHub 리포지토리에서](https://github.com/NetApp/trident/releases)Astra Trident를 다운로드합니다. 원하는 버전에서 선택하고 설치 관리자 번들을 다운로드합니다.

```console
#Download Astra Trident

$  wget https://github.com/NetApp/trident/releases/download/v21.07.1/trident-installer-21.07.1.tar.gz
$  tar xzvf trident-installer-21.07.1.tar.gz
```
를 사용하여 연산자를 `deploy/bundle.yaml` 배포합니다.

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

`TridentOrchestrator`Astra Trident를 설치할 를 만듭니다.

```console
$ kubectl apply -f trident-installer/deploy/crds/tridentorchestrator_cr.yaml

tridentorchestrator.trident.netapp.io/trident created 
```

연산자는 사양에 제공된 매개 변수를 사용하여 를 `TridentOrchestrator` 설치합니다. 광범위한 [설치](https://docs.netapp.com/us-en/trident/trident-get-started/kubernetes-deploy.html) 및 백 엔드 가이드에서 구성 매개 변수 및 예제 [백 엔드에](https://docs.netapp.com/us-en/trident/trident-use/backends.html)대해 알아볼 수 있습니다.

Astra Trident가 설치되어 있는 것을 확인합니다. 

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

Astra Trident가 설치되면 Azure NetApp Files 구독을 가리키는 백 엔드를 만듭니다.

```console
$  kubectl apply -f trident-installer/sample-input/backends-samples/azure-netapp-files/backend-anf.yaml -n trident

secret/backend-tbc-anf-secret created
tridentbackendconfig.trident.netapp.io/backend-tbc-anf created
```

명령을 실행하기 전에 `backend-anf.yaml` 다음과 같이 Azure NetApp Files 구독에 대한 세부 정보를 포함하도록 업데이트해야 합니다.

* `subscriptionID` Azure NetApp Files 사용하도록 설정된 Azure 구독의 경우 입니다. Component 
* `tenantID``clientID`Azure NetApp Files 서비스에 대한 충분한 권한이 있는 `clientSecret` AD(Azure Active Directory)의 [앱 등록에서](../active-directory/develop/howto-create-service-principal-portal.md) , 및 앱 등록은 Azure에서 미리 정의된 또는 역할을 수행해야 `Owner` `Contributor` 합니다.
* 위임된 서브넷이 하나 이상 포함된 Azure 위치입니다.

또한 다른 서비스 수준을 제공하도록 선택할 수 있습니다. Azure NetApp Files 표준, Premium 및 Ultra의 세 가지 [서비스 수준을](../azure-netapp-files/azure-netapp-files-service-levels.md)제공합니다.

### <a name="create-a-storageclass"></a>StorageClass 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Azure NetApp Files 볼륨을 사용하려면 스토리지 클래스를 만들어야 합니다. 라는 파일을 만들고 `anf-storageclass.yaml` 아래에 제공된 매니페스트에 복사합니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만듭니다.

```console
$  kubectl apply -f anf-storageclass.yaml

storageclass/azure-netapp-files created

$  kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
azure-netapp-files   csi.trident.netapp.io   Delete          Immediate           false                  3s
```

### <a name="create-a-persistentvolumeclaim"></a>PersistentVolumeClaim 만들기

PVC(PersistentVolumeClaim)는 사용자의 스토리지 요청입니다. PersistentVolumeClaim을 만들면 Astra Trident는 자동으로 Azure NetApp Files 볼륨을 만들고 Kubernetes 워크로드에서 사용할 수 있도록 합니다.

라는 파일을 만들고 `anf-pvc.yaml` 다음 매니페스트를 제공합니다. 이 예제에서는 *ReadWriteMany* 인 1-TiB 볼륨이 만들어집니다.

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

PVC를 만든 후 Pod를 회전하여 Azure NetApp Files 볼륨에 액세스할 수 있습니다. 아래 매니페스트를 사용하여 이전 단계에서 만든 Azure NetApp Files 볼륨을 탑재하는 NGINX Pod를 정의할 수 있습니다. 이 예제에서 볼륨은 에 `/mnt/data` 탑재됩니다.

다음 매니페스트를 포함하는 라는 파일을 `anf-nginx-pod.yaml` 만듭니다.

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

Kubernetes는 이제 볼륨이 탑재되고 의 컨테이너 내에서 액세스할 수 있는 Pod를 `nginx` `/mnt/data` 만들었습니다. 를 사용하여 Pod에 대한 이벤트 로그를 확인합니다. `kubectl describe`

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

Astra Trident는 다음과 같은 Azure NetApp Files 있는 많은 기능을 지원합니다.

* [볼륨 확장](https://docs.netapp.com/us-en/trident/trident-use/vol-expansion.html)
* [주문형 볼륨 스냅샷](https://docs.netapp.com/us-en/trident/trident-use/vol-snapshots.html)
* [볼륨 가져오기](https://docs.netapp.com/us-en/trident/trident-use/vol-import.html)

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
