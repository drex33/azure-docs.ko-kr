---
title: 고객 관리형 키를 사용하여 레지스트리 암호화
description: Azure 컨테이너 레지스트리의 저장 데이터 암호화 및 Azure Key Vault에 저장된 고객 관리형 키를 사용하여 프리미엄 레지스트리를 암호화하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: a5fd3140474b1d6d7c36d686c14f2dc4e6a9ef73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561606"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 암호화

이미지 및 기타 아티팩트를 Azure 컨테이너 레지스트리에 저장하면 Azure에서 [서비스 관리형 키](../security/fundamentals/encryption-models.md)를 사용하여 저장된 레지스트리 콘텐츠를 자동으로 암호화합니다. Azure Key Vault에서 만들고 관리하는 키(고객 관리형 키)를 사용하여 추가 암호화 계층을 통해 기본 암호화를 보완할 수 있습니다. 이 문서에서는 Azure CLI, Azure Portal 또는 Resource Manager 템플릿을 사용하는 단계를 안내합니다.

고객 관리형 키를 사용하는 서버 쪽 암호화는 [Azure Key Vault](../key-vault/general/overview.md)와의 통합을 통해 지원됩니다. 

* 사용자 고유의 암호화 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault의 API를 사용하여 키를 생성할 수 있습니다. 
* Azure Key Vault를 사용하면 키 사용을 감사할 수도 있습니다.
* Azure Key Vault에서 새 키 버전을 사용할 수 있는 경우 Azure Container Registry는 레지스트리 암호화 키의 자동 회전을 지원합니다. 레지스트리 암호화 키를 수동으로 회전할 수도 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.


## <a name="things-to-know"></a>알아야 할 사항

* 고객 관리형 키는 현재 레지스트리를 만들 때만 사용하도록 설정할 수 있습니다. 키를 사용할 때 키 자격 증명 모음에 액세스하기 위해 *사용자가 할당한* 관리 ID를 구성합니다. 나중에 필요한 경우 키 자격 증명 모음 액세스를 위해 레지스트리의 시스템 관리 ID를 사용하도록 설정할 수 있습니다.
* 레지스트리에서 고객 관리형 키를 사용하여 암호화를 설정한 후에는 암호화를 사용하지 않도록 설정할 수 없습니다.  
* Azure Container Registry는 RSA 또는 RSA-HSM 키만 지원합니다. 타원 곡선 키는 현재 지원되지 않습니다.
* [콘텐츠 신뢰](container-registry-content-trust.md)는 현재 고객 관리형 키로 암호화된 레지스트리에서 지원되지 않습니다.
* 고객 관리형 키로 암호화된 레지스트리에서 [ACR 작업](container-registry-tasks-overview.md)에 대한 실행 로그는 현재 24시간 동안만 유지됩니다. 로그를 더 오랜 기간 동안 유지해야 하는 경우 [작업 실행 로그 내보내기 및 저장](container-registry-tasks-logs.md#alternative-log-storage)에 대한 지침을 참조하세요.

## <a name="automatic-or-manual-update-of-key-versions"></a>키 버전의 자동 또는 수동 업데이트

고객 관리형 키로 암호화된 레지스트리 보안에 대한 중요한 고려 사항은 암호화 키를 업데이트(회전)하는 빈도입니다. 조직에는 고객 관리형 키로 사용될 때 Azure Key Vault에 저장된 키 [버전](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)을 정기적으로 업데이트해야 하는 규정 준수 정책이 있을 수 있습니다. 

고객 관리형 키를 사용하여 레지스트리 암호화를 구성할 때 암호화에 사용되는 키 버전을 업데이트하는 두 가지 옵션이 있습니다.

* **키 버전 자동 업데이트** - Azure Key Vault에서 새 버전을 사용할 수 있을 때 고객 관리형 키를 자동으로 업데이트하려면 고객 관리형 키로 레지스트리 암호화를 사용할 때 키 버전을 생략합니다. 버전이 지정되지 않은 키로 레지스트리가 암호화되면 Azure Container Registry는 키 볼트에 새 키 버전이 있는지 정기적으로 확인하고 1시간 이내에 고객 관리형 키를 업데이트합니다. Azure Container Registry는 자동으로 최신 버전의 키를 사용합니다.

* **수동으로 키 버전 업데이트** - 레지스트리 암호화에 특정 버전의 키를 사용하려면 고객 관리형 키를 사용하여 레지스트리 암호화를 사용할 때 해당 키 버전을 지정합니다. 레지스트리가 특정 키 버전으로 암호화되면 Azure Container Registry는 고객 관리형 키를 수동으로 회전할 때까지 해당 버전을 암호화에 사용합니다.

자세한 내용은 이 도움말 뒷부분에 있는 [키 버전이 있거나 없는 키 ID 선택](#choose-key-id-with-or-without-key-version) 및 [키 버전 업데이트](#update-key-version)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.2.0 이상 또는 Azure Cloud Shell이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-customer-managed-key---cli"></a>고객 관리형 키 사용 - CLI

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create][az-group-create] 명령을 실행하여 키 자격 증명 모음, 컨테이너 레지스트리 및 기타 필요한 리소스를 만들기 위한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

[az identity create][az-identity-create] 명령을 사용하여 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 이 ID는 레지스트리에서 Key Vault 서비스에 액세스하는 데 사용됩니다.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

명령 출력에서 `id` 및 `principalId` 값을 적어 둡니다. 이러한 값은 이후 단계에서 키 자격 증명 모음에 대한 레지스트리 액세스를 구성하는 데 필요합니다.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

편의를 위해 이러한 값을 환경 변수에 저장합니다.

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[az keyvault create][az-keyvault-create]를 사용하여 키 자격 증명 모음을 만들어 레지스트리 암호화를 위한 고객 관리형 키를 저장합니다. 

기본적으로 **일시 삭제** 설정은 새 키 자격 증명 모음에서 자동으로 활성화됩니다. 실수로 키 또는 키 자격 증명 모음 삭제로 인한 데이터 손실을 방지하려면 **보호 제거** 설정도 사용합니다.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

이후 단계에서 사용하려면 Key Vault의 리소스 ID를 가져옵니다.

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access-by-trusted-services"></a>신뢰할 수 있는 서비스를 통한 키 자격 증명 모음 액세스 사용

키 자격 증명 모음을 방화벽 또는 가상 네트워크(프라이빗 엔드포인트)로 보호하는 경우 [신뢰할 수 있는 Azure 서비스](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)의 액세스를 허용하도록 네트워크 설정을 활성화합니다. 

자세한 내용은 [Azure Key Vault 네트워킹 설정 구성](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-cli)을 참조하세요.


### <a name="enable-key-vault-access-by-managed-identity"></a>관리 ID를 통한 키 자격 증명 모음 액세스 사용

#### <a name="enable-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 사용

한 가지 옵션은 ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성하는 것입니다. 다음 [az keyvault set-policy][az-keyvault-set-policy] 명령에서는 이전에 만들어 환경 변수에 저장한 관리 ID의 보안 주체 ID를 전달합니다. 키 권한을 **get**, **unwrapKey** 및 **wrapKey** 로 설정합니다.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey

```
#### <a name="assign-rbac-role"></a>RBAC 역할 할당

또는 [Azure RBAC for Key Vault](../key-vault/general/rbac-guide.md)를 사용하여 키 자격 증명 모음에 액세스 할 수 있는 권한을 ID에 할당합니다. 예를 들어 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용하여 Key Vault Crypto Service 암호화 역할을 ID에 할당합니다.

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>키 만들기 및 키 ID 가져오기

[az keyvault key create][az-keyvault-key-create] 명령을 실행하여 키를 키 자격 증명 모음에 만듭니다.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

명령 출력에서 키의 ID(`kid`)를 적어 둡니다. 이 ID는 다음 단계에서 사용합니다.

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>키 버전이 있거나 없는 키 ID 선택

편의를 위해 $keyID 환경 변수에 키 ID로 선택한 형식을 저장합니다. 버전이 있거나 버전이 없는 키 ID를 사용할 수 있습니다.

#### <a name="manual-key-rotation---key-id-with-version"></a>수동 키 회전 - 버전이 있는 키 ID

고객 관리형 키로 레지스트리를 암호화하는 데 사용되는 경우 이 키는 Azure Container Registry에서 수동 키 회전만 허용합니다.

이 예에서는 키의 `kid` 속성을 저장합니다.

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>자동 키 회전 - 버전이 생략된 키 ID 

고객 관리형 키로 레지스트리를 암호화하는 데 사용되는 경우 이 키는 Azure Key Vault에서 새 키 버전이 검색될 때 자동 키 회전을 사용합니다.

이 예에서는 키의 `kid` 속성에서 버전을 제거합니다.

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 만들기

[az acr create][az-acr-create] 명령을 실행하여 레지스트리를 프리미엄 서비스 계층에 만들고 고객 관리형 키를 사용하도록 설정합니다. 이전에 환경 변수에 저장된 관리 ID 및 키 ID를 전달합니다.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>암호화 상태 표시

고객 관리형 키를 사용하는 레지스트리 암호화를 사용하도록 설정되었는지 여부를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

레지스트리 암호화에 사용된 키에 따라 출력은 다음과 유사합니다.

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>고객 관리형 키 사용 - 포털

### <a name="create-a-managed-identity"></a>관리 ID 만들기

Azure Portal에서 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 단계는 [사용자 할당 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조하세요.

ID 이름은 이후 단계에서 사용합니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Azure Portal에서 사용자가 할당한 ID 만들기":::

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

키 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

고객 관리형 키에 대한 키 자격 증명 모음을 만들 때 **기본 사항** 탭에서 **제거 보호** 설정을 사용합니다. 이 설정을 사용하면 실수로 키 또는 키 자격 증명 모음을 삭제하여 발생하는 데이터 손실을 방지할 수 있습니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Azure Portal에서 키 자격 증명 모음 만들기":::

### <a name="enable-key-vault-access-by-trusted-services"></a>신뢰할 수 있는 서비스를 통한 키 자격 증명 모음 액세스 사용

키 자격 증명 모음을 방화벽 또는 가상 네트워크(프라이빗 엔드포인트)로 보호하는 경우 [신뢰할 수 있는 Azure 서비스](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)의 액세스를 허용하도록 네트워크 설정을 활성화합니다. 

자세한 내용은 [Azure Key Vault 네트워킹 설정 구성](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-portal)을 참조하세요.

### <a name="enable-key-vault-access-by-managed-identity"></a>관리 ID를 통한 키 자격 증명 모음 액세스 사용

#### <a name="enable-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 사용

한 가지 옵션은 ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성하는 것입니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가** 를 차례로 선택합니다.
1. **키 권한** 을 선택하고, **가져오기**, **키 래핑 해제** 및 **키 래핑** 을 선택합니다.
1. **보안 주체 선택** 에서 사용자가 할당한 관리 ID의 리소스 이름을 선택합니다.  
1. **추가**, **저장** 을 차례로 선택합니다.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="키 자격 증명 모음 액세스 정책 만들기":::

#### <a name="assign-rbac-role"></a>RBAC 역할 할당    

또는 키 자격 증명 모음 범위에서 사용자가 할당한 관리 ID에 키 자격 증명 모음 암호화 서비스 암호화 사용자 역할을 할당합니다.

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

### <a name="create-key-optional"></a>키 만들기(선택 사항)

선택적으로 레지스트리를 암호화하는 데 사용할 키 저장소에 키를 만듭니다. 특정 키 버전을 고객 관리형 키로 선택하려면 다음 단계를 따릅니다. 키 자격 증명 모음 액세스가 프라이빗 엔드포인트 또는 선택한 네트워크로 제한된 경우 레지스트리를 만들기 전에 키를 만들어야 할 수도 있습니다. 

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **키** 를 차례로 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 키에 대한 고유한 이름을 입력합니다.
1. 나머지 기본값을 그대로 적용하고 **만들기** 를 선택합니다.
1. 만들어지면 키를 선택하고 현재 버전을 선택합니다. 키 버전의 **키 식별자** 를 복사합니다.

### <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택하거나 만들고, 레지스트리 이름을 입력합니다. **SKU** 에서 **프리미엄** 을 선택합니다.
1. **암호화** 탭의 **고객 관리형 키** 에서 **사용** 을 선택합니다.
1. **ID** 에서 사용자가 만든 관리 ID를 선택합니다.
1. **암호화** 에서 다음 중 하나를 선택합니다.
    * **Key Vault에서 선택** 을 선택하고 기존 키 자격 증명 모음 및 키 또는 **새로 만들기** 를 선택합니다. 선택한 키는 버전이 없으며 자동 키 회전을 사용합니다.
    * **키 URI 입력** 을 선택하고 기존 키의 식별자를 제공합니다. 버전이 지정된 키 URI(수동으로 회전해야 하는 키의 경우) 또는 버전이 지정되지 않은 키 URI(자동 키 회전을 사용하는 경우)를 제공할 수 있습니다. 키를 만드는 단계는 이전 섹션을 참조하세요.
1. **암호화** 탭에서 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택하여 레지스트리 인스턴스를 배포합니다.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Azure Portal에서 암호화된 레지스트리 만들기":::

포털에서 레지스트리의 암호화 상태를 확인하려면 레지스트리로 이동합니다. **설정** 아래에서 **암호화** 를 선택합니다.

## <a name="enable-customer-managed-key---template"></a>고객 관리형 키 사용 - 템플릿

Resource Manager 템플릿을 사용하여 레지스트리를 만들고 고객 관리형 키를 사용하는 암호화를 사용하도록 설정할 수도 있습니다.

다음 템플릿은 새 컨테이너 레지스트리 및 사용자가 할당한 관리 ID를 만듭니다. 다음 내용을 새 파일에 복사하고 `CMKtemplate.json`과 같은 파일 이름을 사용하여 저장합니다.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

이전 섹션의 단계에 따라 다음 리소스를 만듭니다.

* 키 자격 증명 모음(이름으로 식별됨)
* 키 자격 증명 모음 키(키 ID로 식별됨)

다음 [az deployment group create][az-deployment-group-create] 명령을 실행하여 이전 템플릿 파일을 통해 레지스트리를 만듭니다. 표시되면 새 레지스트리 이름 및 관리 ID 이름과 사용자가 만든 키 자격 증명 모음 이름 및 키 ID를 제공합니다.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>암호화 상태 표시

레지스트리 암호화 상태를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>레지스트리 사용

레지스트리에서 고객 관리형 키를 사용하도록 설정되면 고객 관리형 키로 암호화되지 않은 레지스트리에서 수행하는 것과 동일한 레지스트리 작업을 수행할 수 있습니다. 예를 들어 레지스트리로 인증하고 Docker 이미지를 푸시할 수 있습니다. [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)의 명령 예제를 참조하세요.

## <a name="rotate-key"></a>키 회전

Azure Key Vault에서 키 버전을 업데이트하거나 새 키를 만든 다음, 키를 사용하여 레지스트리를 업데이트하여 데이터를 암호화합니다. 이러한 단계는 Azure CLI 또는 포털을 사용하여 수행할 수 있습니다.

키를 회전시키는 경우 일반적으로 레지스트리를 만들 때 사용되는 것과 동일한 ID를 지정합니다. 필요에 따라 키 액세스를 위해 사용자가 할당한 새 ID를 구성하거나 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하고 지정합니다.

> [!NOTE]
> * 포털에서 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하려면 **설정** > **ID** 를 선택하고 시스템이 할당한 ID의 상태를 **켜기** 로 설정합니다.
> * 필요한 [키 자격 증명 모음 액세스](#enable-key-vault-access-by-managed-identity)가 키 액세스를 위해 구성하는 ID에 설정되어 있는지 확인하세요.

### <a name="update-key-version"></a>키 버전 업데이트

일반적인 시나리오는 고객 관리형 키로 사용되는 키 버전을 업데이트하는 것입니다. 레지스트리 암호화가 구성된 방식에 따라 Azure Container Registry의 고객 관리형 키가 자동으로 업데이트되거나 수동으로 업데이트해야 합니다.

### <a name="azure-cli"></a>Azure CLI

[az keyvault key][az-keyvault-key] 명령을 사용하여 키 자격 증명 모음 키를 만들거나 관리합니다. 새 키 버전을 생성하려면 [az keyvault key create][az-keyvault-key-create] 명령을 실행합니다.

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

다음 단계는 레지스트리 암호화가 구성된 방법에 따라 다릅니다.

* 레지스트리가 키 버전 업데이트를 감지하도록 구성된 경우 고객 관리형 키는 1시간 이내에 자동으로 업데이트됩니다.

* 새 키 버전에 대한 수동 업데이트가 필요하도록 레지스트리가 구성된 경우 [az acr encryption rotate-key][az-acr-encryption-rotate-key] 명령을 실행하여 새 키 ID와 구성할 ID를 전달합니다.

고객 관리형 키 버전을 수동으로 업데이트하려면 다음을 수행합니다.

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> `az acr encryption rotate-key`를 실행할 때 버전이 지정된 키 ID 또는 버전이 지정되지 않은 키 ID를 전달할 수 있습니다. 버전이 지정되지 않은 키 ID를 사용하는 경우 레지스트리는 나중에 키 버전 업데이트를 자동으로 검색하도록 구성됩니다.

### <a name="portal"></a>포털

레지스트리의 **암호화** 설정을 사용하여 고객 관리형 키에 사용되는 키 자격 증명 모음, 키 또는 ID 설정을 업데이트합니다.

예를 들어 새 키를 구성하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** 아래에서 **암호화** > **키 변경** 을 차례로 선택합니다.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Azure Portal에서 키 회전":::
1. **암호화** 에서 다음 중 하나를 선택합니다.
    * **Key Vault에서 선택** 을 선택하고 기존 키 자격 증명 모음 및 키 또는 **새로 만들기** 를 선택합니다. 선택한 키는 버전이 없으며 자동 키 회전을 사용합니다.
    * **키 URI 입력** 을 선택하고 키 식별자를 직접 제공합니다. 버전이 지정된 키 URI(수동으로 회전해야 하는 키의 경우) 또는 버전이 지정되지 않은 키 URI(자동 키 회전을 사용하는 경우)를 제공할 수 있습니다.
1. 키 선택을 완료하고, **저장** 을 선택합니다.

## <a name="revoke-key"></a>키 철회

키 자격 증명 모음의 액세스 정책 또는 권한을 변경하거나 키를 삭제하여 고객 관리형 암호화 키를 철회합니다. 예를 들어 [az keyvault delete-policy][az-keyvault-delete-policy] 명령을 사용하여 레지스트리에서 사용하는 관리 ID의 액세스 정책을 변경합니다.

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

레지스트리는 암호화 키에 액세스할 수 없으므로 키를 철회하면 모든 레지스트리 데이터에 대한 액세스가 효과적으로 차단됩니다. 키에 대한 액세스를 사용하도록 설정되거나 삭제된 키가 복원되는 경우 레지스트리에서 암호화된 레지스트리 데이터에 다시 액세스할 수 있도록 키를 선택합니다. 

## <a name="troubleshoot"></a>문제 해결

### <a name="removing-managed-identity"></a>관리 ID 제거


암호화를 구성하는 데 사용되는 레지스트리에서 사용자가 할당한 또는 시스템이 할당한 관리 ID를 제거하려고 하면 다음과 유사한 오류 메시지가 표시될 수 있습니다.
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
또한 암호화 키를 변경(회전)할 수 없습니다. 해결 단계는 암호화에 사용되는 ID 유형에 따라 다릅니다.

**사용자가 할당한 ID**

사용자가 할당한 ID에서 이 문제가 발생하면 먼저 [az acr identity assign](/cli/azure/acr/identity/#az_acr_identity_assign) 명령을 사용하여 ID를 재할당합니다. Id의 리소스 ID를 전달 하거나, 레지스트리와 동일한 리소스 그룹에 있는 id의 이름을 사용 합니다. 예:

```azurecli
az acr identity assign -n myRegistry \
    --identities "/subscriptions/mysubscription/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentity"
```
        
그런 다음, 키를 변경하고 다른 ID를 할당한 후 원래 사용자가 할당한 ID를 제거할 수 있습니다.

**시스템이 할당한 ID**

시스템이 할당한 ID에서 이 문제가 발생하는 경우 ID 복원에 대한 지원을 받으려면 [Azure 지원 티켓을 만드세요](https://azure.microsoft.com/support/create-ticket/).

### <a name="enabling-key-vault-firewall"></a>키 자격 증명 모음 방화벽 사용

암호화된 레지스트리를 만든 후 키 자격 증명 모음 방화벽 또는 가상 네트워크를 사용하도록 설정하면 HTTP 403 또는 이미지 가져오기 또는 자동화된 키 순환과 관련된 기타 오류가 표시될 수 있습니다. 이 문제를 해결하려면 처음에 암호화를 위해 사용한 관리 ID 및 키를 다시 구성합니다. [Rotate key](#rotate-key)(키 순환)의 단계를 참조하세요. 

문제가 지속되면 Azure 지원에 문의하세요.

### <a name="accidental-deletion-of-key-vault-or-key"></a>실수로 키 자격 증명 모음 또는 키를 삭제했습니다.

고객 관리형 키로 레지스트리를 암호화하는 데 사용되는 키 자격 증명 모음 또는 키를 삭제하면 레지스트리의 콘텐츠에 액세스할 수 없게 됩니다. 키 자격 증명 모음(기본 옵션)에서 [일시 삭제를](../key-vault/general/soft-delete-overview.md) 사용하도록 설정한 경우 삭제된 자격 증명 모음 또는 키 자격 증명 모음 개체를 복구하고 레지스트리 작업을 다시 시작할 수 있습니다.

키 자격 증명 모음 삭제 및 복구 시나리오는 [일시 삭제 및 제거 보호를 Azure Key Vault 복구 관리를](../key-vault/general/key-vault-recovery.md)참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 저장 데이터 암호화](../security/fundamentals/encryption-atrest.md)에 대해 자세히 알아봅니다.
* 액세스 정책 및 [키 자격 증명 모음에 대한 액세스를 보호하는 방법](../key-vault/general/security-features.md)에 대해 자세히 알아봅니다.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
