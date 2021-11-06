---
title: Azure Kubernetes Service 비밀에 Azure Key Vault Provider for Secrets Store CSI 드라이버 사용
description: 비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버를 사용하여 비밀 저장소를 AKS(Azure Kubernetes Service)와 통합하는 방법에 대해 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: cdab72e0a1633aa75a5594acf9b506d944f0eaa0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893010"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터에서 비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자 사용

비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버를 사용하면 [CSI 볼륨을][kube-csi]통해 kubernetes 클러스터와 비밀 저장소로 Azure Key Vault 통합할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 시작하기 전에 Azure CLI 버전이 >= 인지 `2.30.0` 확인하거나 [최신 버전을 설치합니다.](/cli/azure/install-azure-cli)

### <a name="supported-kubernetes-versions"></a>지원되는 Kubernetes 버전

최소 권장 Kubernetes 버전은 [롤링 Kubernetes 버전 지원 창][kubernetes-version-support]를 기반으로 합니다. N-2 이상 최신 버전을 실행하고 있는지 확인합니다.

## <a name="features"></a>기능

- CSI 볼륨을 사용하여 Pod에 비밀, 키 및/또는 인증서 탑재
- CSI 인라인 볼륨 지원
- 단일 볼륨으로 여러 비밀 저장소 개체 탑재 지원
- SecretProviderClass CRD를 사용하여 Pod 이식성 지원
- Windows 컨테이너 지원
- Kubernetes 비밀과 동기화
- 탑재된 콘텐츠 및 동기화된 Kubernetes 비밀의 자동 회전을 지원합니다.

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버 지원을 사용하여 AKS 클러스터 만들기

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버 기능을 사용하여 AKS 클러스터를 만들려면 추가 기능과 함께 [az aks create][az-aks-create] 명령을 `azure-keyvault-secrets-provider` 사용합니다.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

`azurekeyvaultsecretsprovider-*`라는 Azure 리소스에 액세스하기 위해 추가 기능에서 사용자가 할당한 관리 ID를 생성합니다. 이 예제에서는 이 ID를 사용하여 비밀이 저장되는 Azure Key Vault 연결하지만 다른 [ID 액세스 방법을][identity-access-methods] 사용할 수 있습니다. 출력에 있는 ID의 `clientId`를 적어 둡니다.

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

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버 지원을 통해 기존 AKS 클러스터 업그레이드

비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버 기능을 사용하여 기존 AKS 클러스터를 업그레이드하려면 추가 기능과 함께 [az aks enable-addons][az-aks-enable-addons] 명령을 사용합니다. `azure-keyvault-secrets-provider`

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

위에서 설명한 대로 추가 기능에서는 Azure Key Vault에 인증하는 데 사용할 수 있는 사용자가 할당한 관리 ID를 만듭니다.

## <a name="verify-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>비밀 저장소용 Azure Key Vault 공급자 CSI 드라이버 설치 확인

위의 내용은 비밀 저장소 CSI 드라이버와 Azure Key Vault 공급자를 노드에 설치합니다. `secrets-store-csi-driver`kube-system 네임스페이스에 및 레이블이 있는 모든 Pod를 나열하여 완성을 `secrets-store-provider-azure` 확인하고 출력이 다음과 유사한지 확인합니다.

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAME                                     READY   STATUS    RESTARTS   AGE
aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

비밀 저장소 CSI 드라이버 Pod 및 Azure Key Vault 공급자 Pod가 클러스터의 노드 풀에 있는 각 노드에서 실행되고 있는지 확인합니다.

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

비밀 저장소 CSI 드라이버를 사용하면 다음 메서드가 Azure Key Vault 인스턴스에 액세스할 수 있습니다.
- [Pod ID Azure Active Directory][aad-pod-identity]
- 사용자 또는 시스템 할당 관리 ID

선택한 메서드에 대한 Azure Key Vault 액세스하기 위한 [ID를 제공하는][identity-access-methods] 단계를 수행합니다.

## <a name="validate-the-secrets"></a>비밀 유효성 검사

Pod가 시작된 후에는 배포 YAML에 지정된 볼륨 경로에 탑재된 콘텐츠를 사용할 수 있습니다.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtaining-certificates-and-keys"></a>인증서 및 키 얻기

Azure Key Vault 디자인은 키, 비밀 및 인증서를 뚜렷하게 구분합니다. Key Vault 서비스의 인증서 기능은 키 및 비밀 기능을 사용하도록 설계되었습니다. Key Vault 인증서가 만들어지면 주소 지정 가능한 키와 암호도 동일한 이름으로 만들어집니다. 키는 키 작업을 허용하고 비밀은 인증서 값을 비밀로 검색할 수 있도록 허용합니다. Key Vault 인증서에는 공용 x509 인증서 메타데이터도 포함됩니다. Azure Key Vault 인증서의 퍼블릭 부분과 프라이빗 부분을 모두 비밀에 저장합니다. SecretProviderClass에서 를 지정하여 각 개별 구성 요소를 가져올 수 `objectType` 있습니다. 다음 표에서는 인증서와 연결된 다양한 리소스에 매핑되는 개체를 보여 줍니다.

|Object|반환 값|전체 인증서 체인을 반환합니다.|
|---|---|---|
|`key`|PEM 형식의 공개 키|해당 없음|
|`cert`|PEM 형식의 인증서|아니요|
|`secret`|PEM 형식의 프라이빗 키 및 인증서|예|

## <a name="disable-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 비밀 저장소 CSI 드라이버에 대한 Azure Key Vault 공급자 사용 안 함

> [!NOTE]
> 추가 기능 사용을 비활성화하기 전에 사용 중인 가 없는지 `SecretProviderClass` 확인합니다. 가 있는 동안 추가 기능을 사용하지 않도록 설정하면 `SecretProviderClass` 오류가 발생합니다.

기존 클러스터에서 Azure Key Vault Provider for Secrets Store CSI 드라이버 기능을 사용하지 않도록 설정하려면 플래그와 함께 [az aks disable-addons][az-aks-disable-addons] 명령을 사용합니다. `azure-keyvault-secrets-provider`

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> 추가 기능 사용 안 함을 선택하면 기존 워크로드에 문제가 없으며 탑재된 비밀에 업데이트가 표시되지 않습니다. Pod가 다시 시작되거나 강화 이벤트의 일부로 새 Pod가 만들어지면 드라이버가 더 이상 실행되지 않으므로 Pod가 시작되지 않습니다.

## <a name="additional-configuration-options"></a>추가 구성 옵션

### <a name="enabling-and-disabling-autorotation"></a>자동 회전 사용 및 사용 안 함

> [!NOTE]
> 사용하도록 설정하면 Azure Key Vault Provider for Secrets Store CSI 드라이버는 정의된 회전 폴링 간격에 따라 주기적으로 변경 내용을 폴링하여 SecretProviderClass의 secretObjects에 정의된 Pod 탑재 및 Kubernetes 비밀을 업데이트합니다. 기본 회전 폴링 간격은 2m입니다.

비밀 자동 회전을 사용하도록 설정하려면 클러스터를 만들 때 `enable-secret-rotation` 플래그를 사용합니다.

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

또는 추가 기능을 사용하도록 설정된 기존 클러스터를 업데이트합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

사용자 지정 회전 간격을 지정하려면 플래그 를 사용합니다. `rotation-poll-interval`

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

사용하지 않으려면 `disable-secret-rotation` 플래그를 사용합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>탑재된 콘텐츠를 Kubernetes 비밀과 동기화

경우에 따라 탑재된 콘텐츠를 미러하는 Kubernetes 비밀을 만들 수 있습니다.

SecretProviderClass를 만들 때 필드를 사용하여 `secretObjects` Kubernetes 비밀의 원하는 상태를 정의합니다.

> [!NOTE]
> 이는 전체 예제가 아닙니다. 선택한 ID 액세스 방법을 지원하려면 이 예제를 수정해야 Azure Key Vault.

> [!NOTE]
> 비밀은 비밀을 탑재하는 Pod를 시작한 후에만 동기화됩니다. Kubernetes 비밀 기능과의 동기화에만 의존하는 것은 작동하지 않습니다. 비밀을 사용하는 모든 Pod가 삭제되면 Kubernetes 비밀도 삭제됩니다.


> [!NOTE]
> 의 가 `objectName` `secretObjects` 탑재된 콘텐츠의 파일 이름과 일치하는지 확인합니다. 가 대신 사용되는 경우 `objectAlias` 개체 별칭과 일치해야 합니다.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObject defines the desired state of synced K8s secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync. this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes Secret object
    type: Opaque                              # type of the Kubernetes Secret object e.g. Opaque, kubernetes.io/tls
```

#### <a name="set-environment-variables-to-reference-kubernetes-secrets"></a>Kubernetes 비밀을 참조하도록 환경 변수 설정

Kubernetes 비밀이 만들어지면 Kubernetes 비밀을 참조하는 Pod에서 환경 변수를 설정할 수 있습니다.

> [!NOTE]
> 이는 전체 예제가 아닙니다. 선택한 ID 액세스 방법을 지원하려면 이 예제를 수정해야 Azure Key Vault.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: foosecret
            key: username
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-sync"
```

## <a name="metrics"></a>메트릭

### <a name="azure-key-vault-provider"></a>Azure Key Vault Provider

메트릭은 포트 8898에서 Prometheus를 통해 제공되지만 이 포트는 기본적으로 Pod 외부에 노출되지 않습니다. 를 사용하여 localhost를 통해 메트릭에 액세스합니다. `kubectl port-forward`

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

다음 표에서는 비밀 저장소 CSI 드라이버에 대한 Azure Key Vault 공급자가 제공하는 메트릭을 나열합니다.

|메트릭|Description|태그|
|----|----|----|
|keyvault_request|Keyvault에서 가져오는 데 걸린 시간 배포|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|GRPC 요청에 소요 된 시간 배포|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|

### <a name="secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버

메트릭은 포트 8095에서 제공 되지만이 포트는 기본적으로 pod 외부에 노출 되지 않습니다. 다음을 사용 하 여 localhost에서 메트릭에 액세스 합니다 `kubectl port-forward` .

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

다음 표에서는 비밀 저장소 CSI 드라이버에서 제공 하는 메트릭을 보여 줍니다.

|메트릭|Description|태그|
|----|----|----|
|total_node_publish|성공한 볼륨 탑재 요청의 총 수|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|성공한 볼륨 분리 요청의 총 수|`os_type=<runtime os>`|
|total_node_publish_error|볼륨 탑재 요청에 대 한 총 오류 수|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish_error|볼륨 분리 요청이 있는 총 오류 수입니다.|`os_type=<runtime os>`|
|total_sync_k8s_secret|동기화 된 k8s 암호의 총 수|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|K8s 암호를 동기화 하는 데 걸린 시간 배포|`os_type=<runtime os>`|
|total_rotation_reconcile|총 회전 조정 수|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|오류를 사용 하 여 조정 된 총 회전 수|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|Pod에 대 한 비밀 저장소 콘텐츠를 회전 하는 데 걸린 시간 분포|`os_type=<runtime os>`|

## <a name="next-steps"></a>다음 단계
<!-- Add a context sentence for the following links -->
AKS 클러스터에서 비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자를 사용 하는 방법을 학습 한 후에는 다음 리소스를 참조 하세요.

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
[identity-access-methods]: ./csi-secrets-store-identity-access.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[kubernetes-version-support]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
