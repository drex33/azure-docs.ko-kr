---
title: Azure Kubernetes Service 비밀용 Secretscrets Store CSI Driver 사용
description: Secrets Store CSI Driver를 사용하여 비밀 저장소를 AKS(Azure Kubernetes Service)와 통합하는 방법에 대해 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: 83895ebe6f8cf330650eef8167ee3bdd06660869
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656439"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>AKS(Azure Kubernetes Service) 클러스터(미리 보기)에서 Kubernetes용 Secretscrets Store CSI Driver 사용

Kubernetes용 Secrets Store CSI Driver를 사용하면 [CSI 볼륨][kube-csi]을 통해 Kubernetes 클러스터와 Azure Key Vault를 비밀 저장소로 통합할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 시작하기 전에 [Azure CLI](/cli/azure/install-azure-cli-windows) 및 *aks-preview* 확장의 최신 버전을 설치합니다.

### <a name="supported-kubernetes-versions"></a>지원되는 Kubernetes 버전

이 기능에 권장되는 최소 Kubernetes 버전은 1.18입니다. 

## <a name="features"></a>기능

- CSI 볼륨을 사용하여 Pod에 비밀, 키 및/또는 인증서 탑재
- CSI 인라인 볼륨 지원(Kubernetes 버전 v1.15+)
- 단일 볼륨으로 여러 비밀 저장소 개체 탑재 지원
- SecretProviderClass CRD를 사용하여 Pod 이식성 지원
- Windows 컨테이너 지원
- Kubernetes Secrets와 동기화(Secrets Store CSI Driver v0.0.10+)
- 탑재된 콘텐츠 및 동기화된 Kubernetes 비밀의 자동 회전 지원(Secrets Store CSI Driver v0.0.15+)

## <a name="register-the-aks-azurekeyvaultsecretsprovider-preview-feature"></a>`AKS-AzureKeyVaultSecretsProvider` 미리 보기 기능 등록

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Secrets Store CSI Driver를 사용할 수 있는 AKS 클러스터를 만들려면 구독에서 `AKS-AzureKeyVaultSecretsProvider` 기능 플래그를 사용하도록 설정해야 합니다.

`AKS-AzureKeyVaultSecretsProvider`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-AzureKeyVaultSecretsProvider"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzureKeyVaultSecretsProvider')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

*aks-preview* Azure CLI 확장 버전 0.5.9 이상도 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 확장이 이미 설치되어 있는 경우 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 최신 버전으로 업데이트합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>Secrets Store CSI Driver 지원으로 AKS 클러스터 만들기

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

Secrets Store CSI Driver 기능을 사용하여 AKS 클러스터를 만들려면 추가 기능 `azure-keyvault-secrets-provider`와 함께 [az aks create][az-aks-create] 명령을 사용합니다.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

`azurekeyvaultsecretsprovider-*`라는 Azure 리소스에 액세스하기 위해 추가 기능에서 사용자가 할당한 관리 ID를 생성합니다. 이 ID를 사용하여 비밀이 저장될 Azure Key Vault에 연결할 수 있습니다. 출력에 있는 ID의 `clientId`를 적어 둡니다.

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>Secrets Store CSI Driver 지원으로 기존 AKS 클러스터 업그레이드

Secrets Store CSI Driver 기능을 사용하여 기존 AKS 클러스터를 업그레이드하려면 추가 기능 `azure-keyvault-secrets-provider`와 함께 [az aks enable-addons][az-aks-enable-addons] 명령을 사용합니다.

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

위에서 설명한 대로 추가 기능에서는 Azure Key Vault에 인증하는 데 사용할 수 있는 사용자가 할당한 관리 ID를 만듭니다.

## <a name="verify-secrets-store-csi-driver-installation"></a>Secrets Store CSI 드라이버 설치 확인

위는 Secrets Store CSI Driver 및 Azure Key Vault 공급자를 노드에 설치합니다. kube-system 네임스페이스에서 레이블이 secrets-store-csi-driver 및 secrets-store-provider-azure인 모든 Pod를 나열하여 완료를 확인하고, 출력이 다음과 유사하게 표시되는지 확인합니다.

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

## <a name="enabling-and-disabling-autorotation"></a>자동 회전 사용 및 사용 안 함

> [!NOTE]
> 이 옵션을 사용하는 경우 Secrets Store CSI Driver가 2분마다 변경 내용을 폴링하여 SecretProviderClass의 secretObjects에 정의된 Pod 탑재 및 Kubernetes 비밀을 업데이트합니다.

비밀 자동 회전을 사용하도록 설정하려면 클러스터를 만들 때 `enable-secret-rotation` 플래그를 사용합니다.

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

또는 추가 기능을 사용하도록 설정된 기존 클러스터를 업데이트합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

사용하지 않으려면 `disable-secret-rotation` 플래그를 사용합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Azure Key Vault 만들기 또는 기존 Azure Key Vault 사용

AKS 클러스터 외에도 비밀 콘텐츠를 포함하는 Azure Key Vault 리소스가 필요합니다. Key Vault의 이름은 전역적으로 고유해야 합니다.

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault는 키, 비밀 및 인증서를 저장할 수 있습니다. 이 예제에서는 `ExampleSecret`이라는 일반 텍스트 비밀을 설정합니다.

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

다음 섹션에서 사용하기 위해 다음 속성을 기록해 둡니다.

- Key Vault의 비밀 개체 이름
- 개체 유형(비밀, 키 또는 인증서)
- Azure Key Vault 리소스의 이름
- 구독이 속한 Azure 테넌트 ID

## <a name="provide-identity-to-access-azure-key-vault"></a>Azure Key Vault에 액세스할 ID 제공

이전 단계의 값을 사용하여 권한을 설정하여 추가 기능에서 만든 관리 ID가 keyvault 개체에 액세스할 수 있도록 합니다.

```azurecli
az keyvault set-policy -n <keyvault-name> --<object-type>-permissions get --spn <client-id>
```

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>사용자 고유의 SecretProviderClass 개체 만들기 및 적용

AKS 클러스터에 대한 Secretscrets Store CSI Driver를 사용 및 구성하려면 SecretProviderClass 사용자 지정 리소스를 만듭니다. `objects` 배열이 Azure Key Vault 인스턴스에 저장한 개체와 일치하는지 확인합니다.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: <keyvault-name>
spec:
  provider: azure
  parameters:
    keyvaultName: "<keyvault-name>"       # The name of the Azure Key Vault
    useVMManagedIdentity: "true"         
    userAssignedIdentityID: "<client-id>" # The clientId of the addon-created managed identity
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: <secret-name>       # In this example, 'ExampleSecret' 
          objectAlias: <secret-alias>     # [OPTIONAL] specify the filename of the object when written to disk - defaults to objectName if not provided
          objectType: secret              # Object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
    tenantId: "<tenant-id>"               # the tenant ID containing the Azure Key Vault instance
```

자세한 내용은 [사용자 고유의 SecretProviderClass 개체 만들기][sample-secret-provider-class]를 참조하세요. 위에서 기록한 값을 사용해야 합니다.

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>클러스터에 SecretProviderClass 적용

다음으로, 생성한 SecretProviderClass를 배포합니다. 예를 들면 다음과 같습니다.

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>클러스터의 배포 YAML 업데이트 및 적용

클러스터가 새 사용자 지정 리소스를 사용하고 있는지 확인하려면 배포 YAML을 업데이트합니다. 예를 들면 다음과 같습니다.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
  - name: busybox
    image: k8s.gcr.io/e2e-test-images/busybox:1.29
    command:
      - "/bin/sleep"
      - "10000"
    volumeMounts:
    - name: secrets-store-inline
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "<keyvault-name>"
```

업데이트된 배포를 클러스터에 적용합니다.

```bash
kubectl apply -f ./my-deployment.yaml
```

## <a name="validate-the-secrets"></a>비밀 유효성 검사

Pod가 시작된 후에는 배포 YAML에 지정된 볼륨 경로에 탑재된 콘텐츠를 사용할 수 있습니다.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="disable-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 Secrets Store CSI 드라이버 사용 안 함

기존 클러스터에서 Secrets Store CSI 드라이버 기능을 사용하지 않으려면 `azure-keyvault-secrets-provider` 플래그와 함께 [az aks disable-addons][az-aks-disable-addons] 명령을 사용합니다.

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

## <a name="next-steps"></a>다음 단계
<!-- Add a context sentence for the following links -->
AKS 클러스터에서 CSI Secrets Store Driver를 사용하는 방법을 학습한 후에는 다음 리소스를 참조하세요.

- [AKS에서 Azure Disks 및 Azure Files에 대한 CSI 드라이버 사용][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
