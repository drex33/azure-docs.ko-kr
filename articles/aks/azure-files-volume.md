---
title: Azure Files 공유를 수동으로 만들기
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 여러 Pod에 동시에 사용할 Azure Files가 포함된 볼륨을 수동으로 만드는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/08/2021
ms.openlocfilehash: c68783cd614ca5dc1a569f17365992a378d225b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566548"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure Files 공유를 사용하여 수동으로 볼륨을 만들고 사용합니다.

컨테이너 기반 애플리케이션은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. 여러 Pod에서 동일한 스토리지 볼륨에 동시에 액세스해야 하는 경우 Azure Files에서 [SMB(서버 메시지 블록) 프로토콜][smb-overview]을 사용하여 연결할 수 있습니다. 이 문서에서는 Azure Files 공유를 수동으로 만들고 AKS의 Pod에 첨부하는 방법을 보여 줍니다.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Files를 Kubernetes 볼륨으로 사용하려면 먼저 Azure Storage 계정과 파일 공유를 만들어야 합니다. 다음 명령은 *myAKSShare* 라는 리소스 그룹, 스토리지 계정, *aksshare* 라는 파일 공유를 만듭니다.

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

스크립트 출력의 끝에 표시된 스토리지 계정 이름 및 키를 기록해 두세요. 다음 단계 중 하나로 Kubernetes 볼륨을 작성할 때 이 값이 필요합니다.

## <a name="create-a-kubernetes-secret"></a>Kubernetes 비밀 만들기

이전 단계에서 작성된 파일 공유에 액세스하려면 Kubernetes에 자격 증명이 필요합니다. 자격 증명은 [Kubernetes 비밀][kubernetes-secret]에 저장됩니다. Kubernetes 비밀은 Kubernetes Pod를 만들 때 참조됩니다.

`kubectl create secret` 명령을 사용하여 비밀을 작성하세요. 다음 예제는 *azure-secret* 이라는 공유를 만들고 이전 단계에서 *azurestorageaccountname* 및 *azurestorageaccountkey* 를 생성합니다. 기존 Azure Storage 계정을 사용하려면 계정 이름과 키를 입력합니다.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>파일 공유를 인라인 볼륨으로 탑재
> 참고: 인라인 `azureFile` 볼륨은 Pod와 같은 네임스페이스의 비밀에만 액세스할 수 있습니다. 다른 비밀 네임스페이스를 지정하려면 대신 다음 영구 볼륨 예제를 사용하세요.

Azure Files 공유를 Pod에 탑재하려면 컨테이너 사양에서 볼륨을 구성합니다. 다음 내용이 포함된 `azure-files-pod.yaml`이라는 새 파일을 만듭니다. 파일 공유 이름 또는 비밀 이름을 변경한 경우 *shareName* 및 *secretName* 을 업데이트하세요. 원하는 경우, 파일 공유가 Pod에 마운트되는 경로인 `mountPath`를 업데이트하세요. Windows Server 컨테이너의 경우 *‘D:’* 와 같이 Windows 경로 규칙을 사용하여 *mountPath* 를 지정합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

`kubectl` 명령을 사용하여 Pod을 작성합니다.

```console
kubectl apply -f azure-files-pod.yaml
```

이제 */mnt/azure* 에서 마운트된 Azure Files 공유를 사용하여 실행 중인 Pod이 있습니다. `kubectl describe pod mypod`을 사용하여 공유가 제대로 마운트되었는지 확인할 수 있습니다. 압축된 다음 예제 출력에서는 컨테이너에 탑재된 볼륨을 보여 줍니다.

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>파일 공유를 영구적 볼륨으로 탑재
 - 탑재 옵션

*fileMode* 및 *dirMode* 의 기본값은 Kubernetes 버전 1.15 이상의 경우 *0777* 입니다. 다음 예제에서는 *PersistentVolume* 개체에 *0755* 를 설정합니다.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

탑재 옵션을 업데이트하려면 *PersistentVolume* 을 사용하여 *azurefile-mount-options-pv.yaml* 파일을 만듭니다. 예를 들면 다음과 같습니다.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

*PersistentVolume* 을 사용하는 *PersistentVolumeClaim* 을 사용하여 *azurefile-mount-options-pvc.yaml* 파일을 만듭니다. 예를 들면 다음과 같습니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

`kubectl` 명령을 사용하여 *PersistentVolume* 및 *PersistentVolumeClaim* 을 만듭니다.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

*PersistentVolumeClaim* 이 만들어지고 *PersistentVolume* 에 바인딩되어 있는지 확인합니다.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

*PersistentVolumeClaim* 을 참조하고 Pod를 업데이트하도록 컨테이너 사양을 업데이트합니다. 예를 들면 다음과 같습니다.

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

Pod 사양을 현재 위치에서 업데이트할 수 없으므로 `kubectl` 명령을 사용하여 Pod를 삭제한 후 다시 만들어야 합니다.

```console
kubectl delete pod mypod

kubectl apply -f azure-files-pod.yaml
```

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS에서 스토리지 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조하세요.

Azure Files와 상호 작용하는 AKS 클러스터에 대한 자세한 내용은 [Azure Files에 대한 Kubernetes 플러그 인][kubernetes-files]을 참조하세요.

스토리지 클래스 매개 변수는 [정적 프로비전(사용자 고유의 파일 공유 가져오기)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share)을 참조하세요.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
