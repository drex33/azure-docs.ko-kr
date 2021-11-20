---
title: Azure Kubernetes 서비스 비밀에 대해 암호 저장소 CSI 드라이버에 Azure Key Vault 공급자 사용
description: Azure Kubernetes Service (AKS)와 비밀 저장소를 통합 하는 데 Azure Key Vault Provider for 비밀 Store CSI 드라이버를 사용 하는 방법에 대해 알아봅니다.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: b2afc62720df3a1bfae3c766de82f59d5de93603
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132867928"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-aks-cluster"></a>AKS 클러스터에서 비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자 사용

비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자는 [CSI 볼륨][kube-csi]을 통해 azure Key VAULT를 AKS (Azure Kubernetes Service) 클러스터와 함께 비밀 저장소로 통합할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 시작 하기 전에 Azure CLI 버전이 2.30.0 이상 인지 확인 합니다. 이전 버전인 경우 [최신 버전을 설치](/cli/azure/install-azure-cli)합니다.

### <a name="supported-kubernetes-versions"></a>지원되는 Kubernetes 버전

최소 권장 Kubernetes 버전은 [롤링 Kubernetes 버전 지원 창을][kubernetes-version-support]기반으로 합니다. N-2 이상 버전을 실행 하 고 있는지 확인 합니다.

## <a name="features"></a>기능

- CSI 볼륨을 사용 하 여 pod에 비밀, 키 및 인증서를 탑재 합니다.
- CSI 인라인 볼륨 지원
- 단일 볼륨으로 여러 비밀 저장소 개체 탑재 지원
- CRD를 사용 하 여 pod 이식성 지원 `SecretProviderClass`
- Windows 컨테이너 지원
- Kubernetes 암호와 동기화
- 탑재 된 콘텐츠의 자동 회전 및 동기화 된 Kubernetes 암호를 지원 합니다.

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>비밀 저장소 CSI 드라이버 지원에 대 한 Azure Key Vault 공급자를 사용 하 여 AKS 클러스터 만들기

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

비밀 저장소 CSI 드라이버 기능에 대 한 Azure Key Vault 공급자를 사용 하 여 AKS 클러스터를 만들려면 추가 기능과 함께 [az AKS create][az-aks-create] 명령을 사용 합니다 `azure-keyvault-secrets-provider` .

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

이라는 사용자 할당 관리 id는 `azurekeyvaultsecretsprovider-*` Azure 리소스에 액세스 하기 위해 추가 기능에 의해 만들어집니다. 다음 예제에서는이 id를 사용 하 여 비밀이 저장 될 Azure key vault에 연결 하지만 다른 [id 액세스 방법도][identity-access-methods]사용할 수 있습니다. 출력에 있는 ID의 `clientId`를 적어 둡니다.

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

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>비밀 저장소 CSI 드라이버 지원에 대 한 Azure Key Vault 공급자로 기존 AKS 클러스터 업그레이드

Azure Key Vault 공급자를 사용 하 여 암호 저장소 CSI 드라이버 기능으로 기존 AKS 클러스터를 업그레이드 하려면 추가 기능과 함께 [az AKS enable-addons][az-aks-enable-addons] 명령을 사용 합니다 `azure-keyvault-secrets-provider` .

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

이전 섹션에서 설명한 것 처럼 추가 기능을 통해 Azure key vault에 인증 하는 데 사용할 수 있는 사용자 할당 관리 id가 만들어집니다.

## <a name="verify-the-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>비밀 저장소 CSI 드라이버 설치에 대 한 Azure Key Vault 공급자 확인

위의 명령은 암호 저장소 CSI 드라이버와 Azure Key Vault 공급자를 노드에 설치 합니다. Kube 네임 스페이스에 및 레이블이 있는 모든 pod을 나열 하 여 설치가 완료 되었는지 확인 하 `secrets-store-csi-driver` `secrets-store-provider-azure` 고 출력이 여기에 표시 된 출력과 비슷한지 확인 합니다.

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

클러스터의 노드 풀에 있는 각 노드에서 암호 저장소 CSI 드라이버 pod와 Azure Key Vault 공급자 pod가 실행 되 고 있어야 합니다.

## <a name="create-or-use-an-existing-azure-key-vault"></a>기존 Azure 주요 자격 증명 모음 만들기 또는 사용

AKS 클러스터 외에도 비밀 콘텐츠를 저장 하는 Azure key vault 리소스가 필요 합니다. 키 자격 증명 모음 이름은 전역적으로 고유 해야 합니다.

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure key vault는 키, 암호 및 인증서를 저장할 수 있습니다. 이 예제에서는 라는 일반 텍스트 암호를 설정 합니다 `ExampleSecret` .

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

다음 섹션에서 사용하기 위해 다음 속성을 기록해 둡니다.

- 키 자격 증명 모음에 있는 비밀 개체의 이름입니다.
- 개체 유형 (secret, key 또는 certificate)
- Azure 키 자격 증명 모음 리소스의 이름입니다.
- 구독이 속한 Azure 테 넌 트 ID

## <a name="provide-an-identity-to-access-the-azure-key-vault"></a>Azure key vault에 액세스 하기 위한 id 제공

비밀 저장소 CSI 드라이버는 다음과 같은 방법으로 Azure key vault에 액세스할 수 있습니다.
- [Azure Active Directory pod id][aad-pod-identity]
- 사용자 할당 또는 시스템 할당 관리 id

선택한 방법에 대 한 [Azure Key Vault 공급자의 암호 저장소 CSI 드라이버에 액세스 하려면 Id 제공][identity-access-methods] 의 지침을 따르세요.

## <a name="validate-the-secrets"></a>비밀 유효성 검사

Pod가 시작 된 후에는 배포 YAML에서 지정한 볼륨 경로에 탑재 된 콘텐츠를 사용할 수 있습니다.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtain-certificates-and-keys"></a>인증서 및 키 가져오기

Azure Key Vault 디자인은 키, 암호 및 인증서를 선명 하 게 구분 합니다. Key Vault 서비스의 인증서 기능은 해당 키 및 비밀 기능을 사용할 수 있도록 설계 되었습니다. 키 자격 증명 모음 인증서를 만들면 동일한 이름으로 주소 지정 가능한 키와 비밀이 생성 됩니다. 키를 사용 하 여 키 작업을 허용 하 고 비밀으로 인증서 값을 검색할 수 있습니다. 

키 자격 증명 모음 인증서에는 공용 x509 인증서 메타 데이터도 포함 됩니다. 키 자격 증명 모음은 인증서의 공개 및 개인 구성 요소를 비밀에 모두 저장 합니다. 에서를 지정 하 여 각 개별 구성 요소를 가져올 수 있습니다 `objectType` `SecretProviderClass` . 다음 표에서는 인증서와 연결 된 다양 한 리소스에 매핑되는 개체를 보여 줍니다.

| Object | 반환 값 | 전체 인증서 체인을 반환 합니다. |
|---|---|---|
|`key`|Privacy Enhanced Mail (PEM) 형식의 공개 키|N/A|
|`cert`|인증서 (PEM 형식)|아니요|
|`secret`|개인 키 및 인증서 (PEM 형식)|예|
| | |

## <a name="disable-the-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>기존 AKS 클러스터에서 비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자 사용 안 함

> [!NOTE]
> 추가 기능을 사용 하지 않도록 설정 하기 전에 `SecretProviderClass` 이 사용 중인지 확인 합니다. 존재 하는 동안 추가 기능을 사용 하지 않도록 설정 하려고 `SecretProviderClass` 하면 오류가 발생 합니다.

기존 클러스터에서 비밀 저장소 CSI 드라이버 기능에 대해 Azure Key Vault 공급자를 사용 하지 않도록 설정 하려면 [az aks addons][az-aks-disable-addons] 명령을 플래그와 함께 사용 합니다 `azure-keyvault-secrets-provider` .

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> 추가 기능을 사용 하지 않도록 설정 하면 기존 작업에 문제가 발생 하지 않으며 탑재 된 암호에 업데이트가 표시 되지 않습니다. Pod를 다시 시작 하거나 확장 이벤트의 일부로 새 pod를 만든 경우 드라이버가 더 이상 실행 되지 않으므로 pod가 시작 되지 않습니다.

## <a name="additional-configuration-options"></a>추가 구성 옵션

### <a name="enable-and-disable-autorotation"></a>Autorotation 사용 및 사용 안 함

> [!NOTE]
> Secret Store CSI Driver에 대 한 Azure Key Vault 공급자를 사용 하도록 설정 하면의 필드에 정의 된 pod 탑재 및 Kubernetes 암호를 업데이트 합니다 `secretObjects` `SecretProviderClass` . 이렇게 하려면 정의한 회전 폴링 간격에 따라 변경 내용을 주기적으로 폴링합니다. 기본 회전 폴링 간격은 2 분입니다.

비밀 autorotation을 사용 하도록 설정 하려면 `enable-secret-rotation` 클러스터를 만들 때 플래그를 사용 합니다.

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

또는 추가 기능을 사용 하도록 설정 된 기존 클러스터를 업데이트 합니다.

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

사용자 지정 회전 간격을 지정 하려면 플래그를 사용 합니다 `rotation-poll-interval` .

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

Autorotation를 사용 하지 않도록 설정 하려면 플래그를 사용 합니다 `disable-secret-rotation` .

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>Kubernetes 암호를 사용 하 여 탑재 된 콘텐츠 동기화

경우에 따라 Kubernetes 암호를 만들어 탑재 된 콘텐츠를 미러링할 수 있습니다.

를 만들 때 `SecretProviderClass` `secretObjects` 다음 예제와 같이 필드를 사용 하 여 Kubernetes 암호의 원하는 상태를 정의 합니다.

> [!NOTE]
> 여기에 나와 있는 예제는 완전 하지 않습니다. 선택한 키 자격 증명 모음 id 액세스 방법을 지원 하도록 수정 해야 합니다.

비밀은 pod를 시작 하 여 탑재 한 후에만 동기화 됩니다. Kubernetes 암호 기능과의 동기화만 사용 하면 작동 하지 않습니다. 비밀을 사용 하는 모든 pod 삭제 되 면 Kubernetes 비밀도 삭제 됩니다.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObjects defines the desired state of synced Kubernetes secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync; this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes secret object
    type: Opaque                              # type of Kubernetes secret object (for example, Opaque, kubernetes.io/tls)
```

> [!NOTE]
> `objectName`필드의가 `secretObjects` 탑재 된 콘텐츠의 파일 이름과 일치 하는지 확인 합니다. `objectAlias`대신를 사용 하는 경우 개체 별칭과 일치 해야 합니다.

#### <a name="set-an-environment-variable-to-reference-kubernetes-secrets"></a>Kubernetes 비밀을 참조 하도록 환경 변수 설정

Kubernetes 암호를 만든 후에는 다음 예제 코드와 같이 pod에서 환경 변수를 설정 하 여 참조할 수 있습니다.

> [!NOTE]
> 여기에 나와 있는 예제는 완전 하지 않습니다. 선택한 Azure key vault id 액세스를 지원 하도록 수정 해야 합니다.

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

### <a name="the-azure-key-vault-provider"></a>Azure Key Vault 공급자

메트릭은 포트 8898의 프로메테우스를 통해 제공 되지만이 포트는 기본적으로 pod 외부에 노출 되지 않습니다. 다음을 사용 하 여 localhost에서 메트릭에 액세스 합니다 `kubectl port-forward` .

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

다음 표에서는 Azure Key Vault Provider for 비밀 저장소 CSI 드라이버에서 제공 하는 메트릭을 보여 줍니다.

|메트릭|설명|태그들|
|----|----|----|
|keyvault_request|키 자격 증명 모음에서 가져오는 데 걸린 시간 분포|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|GRPC 요청에 소요 된 시간 분포|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|
| | |

### <a name="the-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버

메트릭은 포트 8095에서 제공 되지만이 포트는 기본적으로 pod 외부에 노출 되지 않습니다. 다음을 사용 하 여 localhost에서 메트릭에 액세스 합니다 `kubectl port-forward` .

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

다음 표에서는 비밀 저장소 CSI 드라이버에서 제공 하는 메트릭을 보여 줍니다.

|메트릭|설명|태그들|
|----|----|----|
|total_node_publish|성공한 볼륨 탑재 요청의 총 수|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|성공한 볼륨 분리 요청의 총 수|`os_type=<runtime os>`|
|total_node_publish_error|볼륨 탑재 요청이 있는 총 오류 수입니다.|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish_error|볼륨 분리 요청이 있는 총 오류 수입니다.|`os_type=<runtime os>`|
|total_sync_k8s_secret|동기화 된 Kubernetes 암호의 총 수|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|Kubernetes 암호를 동기화 하는 데 걸린 시간 분포|`os_type=<runtime os>`|
|total_rotation_reconcile|회전 조정의 총 수입니다.|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|오류로 인해 조정 된 총 회전 수입니다.|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|Pod에 대 한 비밀 저장소 콘텐츠를 회전 하는 데 걸린 시간 분포|`os_type=<runtime os>`|

## <a name="next-steps"></a>다음 단계

AKS 클러스터와 함께 비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자를 사용 하는 방법을 알아보았습니다. [Azure 디스크에 대 한 CSI 드라이버 사용 및 AKS에서 Azure Files][csi-storage-drivers]을 참조 하세요.

<!-- LINKS INTERNAL -->
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

<!-- LINKS EXTERNAL -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
