---
title: Azure Cosmos DB 계정에 대한 고객 관리형 키 구성
description: Azure Key Vault를 사용하여 Azure Cosmos DB 계정에 대한 고객 관리형 키를 구성하는 방법 알아보기
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d22b299cca557774ed47ecf6d8309f780f32fee8
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133066"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure Key Vault를 사용하여 Azure Cosmos 계정에 대한 고객 관리형 키 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos 계정에 저장된 데이터는 Microsoft 에서 관리하는 키(**서비스 관리형 키**)를 통해 원활하게 자동 암호화됩니다. 필요에 따라 관리하는 **키(고객 관리형** 키 또는 CMK)를 사용하여 두 번째 암호화 계층을 추가하도록 선택할 수 있습니다.

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="고객 데이터 주위의 암호화 계층":::

고객 관리형 키를 [Azure Key Vault](../key-vault/general/overview.md)에 저장하고 고객 관리형 키를 사용하도록 설정된 각 Azure Cosmos 계정에 키를 제공해야 합니다. 이 키는 해당 계정에 저장된 모든 데이터를 암호화하는 데 사용됩니다.

> [!NOTE]
> 현재 고객 관리형 키는 새 Azure Cosmos 계정에만 사용할 수 있으며, 계정을 만드는 동안 구성해야 합니다.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Azure 구독에 대한 Azure Cosmos DB 리소스 공급자 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure 구독으로 이동하여 **설정** 탭에서 **리소스 공급자** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="왼쪽 메뉴의 리소스 공급자 항목":::

1. **Microsoft.DocumentDB** 리소스 공급자를 검색합니다. 리소스 공급자가 이미 등록된 것으로 표시되어 있는지 확인합니다. 그렇지 않으면 리소스 공급자를 선택하고 **등록** 을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Microsoft.DocumentDB 리소스 공급자 등록":::

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 구성

Azure Cosmos DB에 고객 관리형 키를 사용하려면 암호화 키를 호스팅하는 데 사용할 Azure Key Vault 인스턴스에 **일시 삭제** 및 **제거 보호** 라는 두 가지 속성을 설정해야 합니다.

새 Azure Key Vault 인스턴스를 만드는 경우 생성 중에 이러한 속성을 사용하도록 설정합니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="새 Azure Key Vault 인스턴스에 대해 일시 삭제 및 제거 보호를 사용하도록 설정":::

기존 Azure Key Vault 인스턴스를 사용하는 경우 Azure Portal에서 **속성** 섹션을 보면 이러한 속성을 사용하도록 설정되어 있는지 확인할 수 있습니다. 이러한 속성 중 하나라도 사용하도록 설정되어 있지 않으면 다음 문서 중 하나에서 "일시 삭제를 사용하도록 설정" 및 "제거 보호 활성화" 섹션을 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../key-vault/general/key-vault-recovery.md)
- [Azure CLI에서 일시 삭제를 사용하는 방법](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a><a id="add-access-policy"></a>Azure Key Vault 인스턴스에 액세스 정책 추가

1. Azure Portal에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 왼쪽 메뉴에서 **액세스 정책** 을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="왼쪽 메뉴의 액세스 정책":::

1. **+ 액세스 정책 추가** 를 선택합니다.

1. **키 권한** 드롭다운 메뉴에서 **가져오기**, **키 래핑 해제**, **키 래핑** 권한을 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="올바른 권한 선택":::

1. **주체 선택** 에서 **선택된 항목 없음** 을 선택합니다.

1. **Azure Cosmos DB** 보안 주체를 검색하여 선택합니다.(쉽게 찾을 수 있도록 보안 주체 ID가 `57506a73-e302-42a9-b869-6f12d9ec29e9`인 Azure Government 지역을 제외한 모든 Azure 지역에 대해 보안 주체 ID: `a232010e-820c-4083-83bb-3ace5fc29d0b`로 검색할 수도 있습니다.) **Azure Cosmos DB** 보안 주체가 목록에 없으면, 이 문서에서 [리소스 공급자 등록](#register-resource-provider) 섹션의 설명을 참고하여 **Microsoft.DocumentDB** 리소스 공급자를 다시 등록해야 할 수도 있습니다.

   > [!NOTE]
   > 그러면 Azure Key Vault 액세스 정책에 Azure Cosmos DB 자사 ID가 등록됩니다. 이 자사 ID를 Azure Cosmos DB 계정 관리 ID로 바꾸려면 [Azure Key Vault 액세스 정책에서 관리 ID 사용](#using-managed-identity)을 참조하세요.

1. 아래쪽에서 **선택** 을 선택합니다. 

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Azure Cosmos DB 보안 주체 선택":::

1. **추가** 를 선택하여 새로운 액세스 정책을 추가합니다.

1. Key Vault 인스턴스에서 **저장** 을 선택하여 모든 변경 내용을 저장합니다.

## <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault에서 새 키 생성

1. Azure Portal에서 암호화 키를 호스트하는 데 사용할 Azure Key Vault 인스턴스로 이동합니다. 그런 다음, 왼쪽 메뉴에서 **키** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="왼쪽 메뉴의 키 항목":::

1. **생성/가져오기** 를 선택하고 새 키의 이름을 제공하고 RSA 키 크기를 선택합니다. 최상의 보안을 위해 3072 이상을 사용하는 것이 좋습니다. **만들기** 를 선택합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="새 키 만들기":::

1. 키가 생성되면 새로 생성된 키를 선택한 다음, 현재 버전을 선택합니다.

1. 마지막 슬래시 뒷 부분을 제외하고 키의 **키 식별자** 를 복사합니다.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="키의 키 식별자 복사":::

## <a name="create-a-new-azure-cosmos-account"></a>새 Azure Cosmos 계정 만들기

### <a name="using-the-azure-portal"></a>Azure Portal 사용

Azure Portal에서 새 Azure Cosmos DB 계정을 생성할 때 **암호화** 단계에서 **고객 관리형 키** 를 선택합니다. **키 URI** 필드에 이전 단계에서 복사한 Azure Key Vault 키의 URI/키 식별자를 붙여넣습니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Azure Portal에서 CMK 매개 변수 설정":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell 사용

PowerShell을 사용하여 새 Azure Cosmos DB 계정을 만드는 경우:

- 앞서 **PropertyObject** 의 **keyVaultKeyUri** 속성 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

- **2019-12-12** 이상의 API 버전을 사용합니다.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 계정을 만들려면 `locations` 속성을 명시적으로 설정해야 합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

계정이 생성되면 Azure Key Vault 키의 URI를 가져와서 고객 관리형 키를 사용하도록 설정되었는지 확인할 수 있습니다.

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 통해 새 Azure Cosmos 계정을 만드는 경우:

- 앞서 **properties** 개체의 **keyVaultKeyUri** 속성 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

- **2019-12-12** 이상의 API 버전을 사용합니다.

> [!IMPORTANT]
> 고객 관리형 키를 사용하여 계정을 만들려면 `locations` 속성을 명시적으로 설정해야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

다음 PowerShell 스크립트를 사용하여 템플릿을 배포합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Azure CLI 사용

Azure CLI를 통해 새 Azure Cosmos 계정을 만드는 경우 앞서 `--key-uri` 매개 변수 아래에서 복사한 Azure Key Vault 키의 URI를 전달합니다.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

계정이 생성되면 Azure Key Vault 키의 URI를 가져와서 고객 관리형 키를 사용하도록 설정되었는지 확인할 수 있습니다.

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="using-a-managed-identity-in-the-azure-key-vault-access-policy"></a><a id="using-managed-identity"></a> Azure Key Vault 액세스 정책에서 관리 ID 사용

이 액세스 정책을 사용하면 Azure Cosmos DB 계정에서 암호화 키에 액세스할 수 있습니다. 이 작업은 특정 AD(Azure Active Directory) ID에 대한 액세스 권한을 부여하여 수행됩니다. 지원되는 두 가지 유형의 ID는 다음과 같습니다.

- Azure Cosmos DB의 자사 ID를 사용하여 Azure Cosmos DB 서비스에 대한 액세스 권한을 부여할 수 있습니다.
- 계정에 대한 액세스 권한을 부여하는 데에는 Azure Cosmos DB 계정의 [관리 ID](how-to-setup-managed-identity.md)를 사용할 수 있습니다.

### <a name="to-use-a-system-assigned-managed-identity"></a>시스템 할당 관리 ID를 사용하려면

시스템 할당 관리 ID는 계정을 만든 후에만 검색할 수 있으므로 [위에](#add-access-policy)설명된 대로 먼저 자사 ID를 사용하여 계정을 만들어야 합니다. 그렇다면

1.  계정을 만드는 동안 이 작업을 수행하지 않은 경우 사용자 계정에서 [시스템 할당 관리 ID를 사용하도록 설정](./how-to-setup-managed-identity.md#add-a-system-assigned-identity)하고 할당된 `principalId`를 복사합니다.

1.  [위에서](#add-access-policy)설명한 대로 Azure Key Vault 계정에 새 액세스 정책을 추가하지만 `principalId` Azure Cosmos DB의 첫 번째 ID 대신 이전 단계에서 복사한 를 사용하세요.

1.  Azure Key Vault의 암호화 키에 액세스할 때 시스템 할당 관리 ID를 사용하도록 지정하려면 Azure Cosmos DB 계정을 업데이트합니다. 다음과 같이 수행할 수 있습니다.

    - 계정의 Azure Resource Manager 템플릿에서 이 속성을 지정하여

    ```json
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "properties": {
            "defaultIdentity": "SystemAssignedIdentity",
            // ...
        },
        // ...
    }
    ```

    - Azure CLI로 계정을 업데이트하여

    ```azurecli
        resourceGroupName='myResourceGroup'
        accountName='mycosmosaccount'

        az cosmosdb update --resource-group $resourceGroupName --name $accountName --default-identity "SystemAssignedIdentity"
    ```
  
1.  필요에 따라 Azure Key Vault 액세스 정책에서 Azure Cosmos DB 자사 ID를 제거할 수 있습니다.

### <a name="to-use-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID를 사용하려면

1.  [위에서](#add-access-policy)설명한 대로 Azure Key Vault 계정에서 새 액세스 정책을 만들 때 `Object ID` Azure Cosmos DB의 타사 ID 대신 사용하려는 관리 ID의 를 사용합니다.

1.  Azure Cosmos DB 계정을 만들 때 사용자 할당 관리 ID를 사용하도록 설정하고 Azure Key Vault 암호화 키에 액세스할 때 이 ID를 사용하도록 지정해야 합니다. 다음과 같이 수행할 수 있습니다.

    - Azure Resource Manager 템플릿에서 다음을 수행합니다.

    ```json
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
        "properties": {
            "defaultIdentity": "UserAssignedIdentity=<identity-resource-id>"
            "keyVaultKeyUri": "<key-vault-key-uri>"
            // ...
        }
    }
    ```

    - Azure CLI:

    ```azurecli
    resourceGroupName='myResourceGroup'
    accountName='mycosmosaccount'
    keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

    az cosmosdb create \
        -n $accountName \
        -g $resourceGroupName \
        --key-uri $keyVaultKeyUri
        --assign-identity <identity-resource-id>
        --default-identity "UserAssignedIdentity=<identity-resource-id>"  
    ```

## <a name="key-rotation"></a>키 회전

Azure Cosmos 계정에서 사용하는 고객 관리형 키 회전은 두 가지 방법으로 수행할 수 있습니다.

- Azure Key Vault에서 현재 사용되는 키의 새 버전을 만듭니다.

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="새로운 키 버전 만들기":::

- 계정에 대한 키 URI를 업데이트하여 현재 사용되는 키를 완전히 다른 키로 바꿉니다. Azure Portal에서 Azure Cosmos 계정으로 이동하고 왼쪽 메뉴에서 **데이터 암호화** 를 선택합니다.

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="데이터 암호화 메뉴 항목":::

    그런 다음, **키 URI** 를 사용하려는 새 키로 바꾸고 **저장** 을 선택합니다.

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="키 URI 업데이트":::

    PowerShell에서 동일한 결과를 얻기 위해 수행하는 방법은 다음과 같습니다.

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

이전 키 또는 키 버전은 해당 키 또는 키 버전의 Azure Cosmos DB 활동이 [Azure Key Vault 감사 로그](../key-vault/general/logging.md)에 더 이상 표시되지 않으면 사용하지 않도록 설정할 수 있습니다. 키 회전 24시간 후에는 이전 키 또는 키 버전에서 더 이상 활동이 진행되지 않아야 합니다.
    
## <a name="error-handling"></a>오류 처리

Azure Cosmos DB에서 고객이 관리 하는 키를 사용 하는 경우 오류가 발생 하면 응답에 HTTP 하위 상태 코드와 함께 오류 정보를 반환 Azure Cosmos DB. 이 하위 상태 코드를 사용하여 문제의 근본 원인을 디버그할 수 있습니다. 지원되는 HTTP 하위 상태 코드 목록을 가져오려면 [Azure Cosmos DB에 대한 HTTP 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 문서를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>고객 관리형 키를 사용하도록 설정하는 데 추가 요금이 있나요?

아니요,이 기능을 사용하도록 설정하는 데 청구되는 요금은 없습니다.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>고객 관리형 키는 용량 계획에 어떤 영향을 주나요?

고객 관리형 키를 사용하는 경우 데이터의 암호화 및 암호 해독을 수행하는 데 필요한 추가 처리를 반영하여 데이터베이스 작업에 소비된 [요청 단위](./request-units.md)가 증가합니다. 이로 인해 프로비저닝된 용량의 사용률이 약간 높아질 수 있습니다. 아래 표를 참조하세요.

| 작업 유형 | 요청 단위 증가 |
|---|---|
| 포인트 읽기(ID별 항목 가져오기) | 작업당 +5% |
| 모든 쓰기 작업 | 작업당 +6%<br/>인덱싱된 속성당 +0.06RU 정도 |
| 쿼리, 변경 피드 읽기 또는 충돌 피드 | 작업당 +15% |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>고객 관리형 키로 어떤 데이터가 암호화되나요?

Azure Cosmos 계정에 저장된 모든 데이터는 고객 관리형 키로 암호화되며 단, 다음 메타데이터는 제외됩니다.

- Azure Cosmos DB [계정, 데이터베이스 및 컨테이너](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)의 이름

- [저장 프로시저](./stored-procedures-triggers-udfs.md)의 이름

- [인덱싱 정책](./index-policy.md)에 선언된 속성 경로

- 컨테이너의 [파티션 키](./partitioning-overview.md) 값

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>기존 Azure Cosmos 계정에 고객 관리형 키가 지원되나요?

이 기능은 현재 새 계정에서만 사용할 수 있습니다.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>고객 관리형 키를 Azure Cosmos DB [분석 저장소](analytical-store-introduction.md)와 함께 사용할 수 있나요?

예, Azure Synapse Link는 Azure Cosmos DB 계정의 관리 ID를 사용하는 고객 관리형 키 구성만 지원합니다. 하지만 계정에서 [Azure Synapse Link를 사용하도록 설정](configure-synapse-link.md#enable-synapse-link)하기 전에 Azure Key Vault 액세스 정책에서 [Azure Cosmos DB 계정의 관리 ID를 사용](#using-managed-identity)해야 합니다.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>계정 수준 키보다 세부적인 수준을 지원할 계획이 있나요?

현재는 아니지만, 컨테이너 수준 키를 고려하고 있습니다.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Azure Cosmos 계정에서 고객 관리형 키를 사용하도록 설정되어 있는지 어떻게 알 수 있나요?

Azure Portal에서 Azure Cosmos 계정으로 이동하고 왼쪽 메뉴에서 **데이터 암호화** 항목을 확인합니다. 이 항목이 있으면 계정에서 고객 관리형 키를 사용할 수 있습니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="데이터 암호화 메뉴 항목":::

또한 Azure Cosmos 계정에 대한 세부 정보를 프로그래밍 방식으로 가져와서 `keyVaultKeyUri` 속성이 있는지 확인할 수 있습니다. [PowerShell에서](#using-powershell) 그리고 [Azure CLI를 사용하여](#using-azure-cli) 수행하는 방법은 위를 참조하십시오.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>고객 관리형 키는 백업에 어떤 영향을 주나요?

Azure Cosmos DB는 계정에 저장된 데이터를 [정기적으로 자동 백업](./online-backup-and-restore.md)합니다. 이러한 작업은 암호화된 데이터를 백업합니다. 백업을 성공적으로 복원 하려면 다음 조건을 충족 해야 합니다.
- 백업 시 사용한 암호화 키는 필수 이며 Azure Key Vault에서 사용할 수 있어야 합니다. 즉, 해지를 수행 하지 않고 백업 시 사용 된 키의 버전을 계속 사용할 수 있습니다.
- [Azure Key Vault 액세스 정책에서 관리 id를 사용](#using-managed-identity)하는 경우 원본 계정에 구성 된 id는 삭제 되지 않아야 하 고 Azure Key Vault 인스턴스의 액세스 정책에서 계속 선언 해야 합니다.

### <a name="how-do-i-revoke-an-encryption-key"></a>암호화 키를 어떻게 해지하나요?

키 해지는 최신 버전의 키를 사용하지 않도록 설정하여 수행됩니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="키 버전을 사용하지 않도록 설정":::

또는 Azure Key Vault 인스턴스에서 모든 키를 해지하려면 Azure Cosmos DB 보안 주체에 부여된 액세스 정책을 삭제하면 됩니다.

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Azure Cosmos DB 보안 주체에 대한 액세스 정책 삭제하기":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>고객 관리형 키가 해지되면 어떤 작업을 수행할 수 있나요?

암호화 키가 해지된 경우 가능한 유일한 작업은 계정 삭제입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 데이터 암호화](./database-encryption-at-rest.md)에 대해 알아봅니다.
- [Cosmos DB의 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요를 살펴봅니다.
