---
title: 최신 API를 사용하여 프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독 만들기
description: 최신 버전의 REST API, Azure CLI, Azure PowerShell 및 Azure Resource Manager 템플릿을 사용하여 프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독을 만드는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/01/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 985f649d864e0fad250a5b2342b8cb96c049c0ec
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478359"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>최신 API를 사용하여 프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독 만들기

이 문서는 최신 API 버전을 사용하여 프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독을 만드는 데 도움이 됩니다. 이전 미리 보기 버전을 여전히 사용 중인 경우 [레거시 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기](programmatically-create-subscription-preview.md)를 참조하세요. 

이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

프로그래밍 방식으로 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 재판매인으로부터 Azure 서비스를 구입한 계약이 적용됩니다. 자세한 내용은 [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

청구 계정에 대한 구독을 만들려면 조직의 클라우드 솔루션 공급자 계정에 전역 관리자 또는 관리 에이전트 역할이 있어야 합니다. 자세한 내용은 [파트너 센터 - 사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조하세요.

Microsoft 파트너 계약 계정에 대한 액세스 권한이 있는지 여부를 모르는 경우 [Microsoft 파트너 계약에 대한 액세스 권한 확인](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement)을 참조하세요.

다음 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

다음 요청을 통해 액세스 권한이 있는 모든 청구 계정을 나열합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

청구 계정이 API 응답에 나열됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

`displayName` 속성을 사용하여 구독을 생성하려는 청구 계정을 식별합니다. 계정의 agreementType이 *MicrosoftPartnerAgreement* 인지 확인합니다. 계정의 `name`을 복사합니다. 예를 들어 청구 계정 `Contoso`에 대한 구독을 만들려면 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 값을 가져오려면 Azure CLI 또는 REST API를 사용하십시오.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
액세스 권한이 있는 모든 청구 계정 목록이 다시 표시됩니다.

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

displayName 속성을 사용하여 구독을 만들 청구 계정을 식별합니다. 계정의 agreementType이 MicrosoftPartnerAgreement인지 확인합니다. 계정의 이름을 복사합니다. 예를 들어 Contoso 청구 계정에 대한 구독을 만들려면 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

---

## <a name="find-customers-that-have-azure-plans"></a>Azure 플랜이 있는 고객 찾기

Azure 구독을 만들 수 있는 청구 계정의 모든 고객을 나열하도록, 첫 번째 단계에서 복사한 `name`(```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)을 사용하여 다음 요청을 수행합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

Azure 플랜이 있는 청구 계정의 고객이 API 응답에 나열됩니다. 이러한 고객에 대한 구독을 만들 수 있습니다.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

`displayName` 속성을 사용하여 구독을 만들 고객을 식별할 수 있습니다. 고객의 `id`를 검색합니다. 예를 들어 `Fabrikam toys`에 대한 구독을 만들려면 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`를 복사합니다. 이후 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 값을 가져오려면 Azure CLI 또는 REST API를 사용하십시오.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

Azure 플랜이 있는 청구 계정의 고객이 API 응답에 나열됩니다. 이러한 고객에 대한 구독을 만들 수 있습니다.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

`displayName` 속성을 사용하여 구독을 만들 고객을 식별할 수 있습니다. 고객의 `id`를 검색합니다. 예를 들어 `Fabrikam toys`에 대한 구독을 만들려면 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`를 복사합니다. 이후 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

---

## <a name="get-the-resellers-for-a-customer"></a>고객을 위한 재판매인 확보

이 섹션은 간접 공급자에 대해서만 선택 사항입니다.

CSP 2계층 모델의 간접 공급자인 경우, 고객에 대한 구독을 만드는 동안 재판매인을 지정할 수 있습니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

고객이 사용할 수 있는 재판매인을 모두 나열하도록, 두 번째 단계에서 복사한 `id`(```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)를 사용하여 다음 요청을 수행합니다.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
고객을 위한 재판매인이 API 응답에 나열됩니다.

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

`description` 속성을 사용하여 구독과 연결된 재판매인을 식별합니다. 재판매인의 `resellerId`를 검색합니다. 예를 들어 `Wingtip`를 연결하려면 `3xxxxx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 값을 가져오려면 Azure CLI 또는 REST API를 사용하십시오.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

첫 번째 단계(```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`과 이전 단계에서 복사한(```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) 고객 `name`을 사용하여 다음 요청을 수행합니다.

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

고객을 위한 재판매인이 API 응답에 나열됩니다.

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

`description` 속성을 사용하여 구독과 연결된 재판매인을 식별합니다. 재판매인의 `resellerId`를 검색합니다. 예를 들어 `Wingtip`를 연결하려면 `3xxxxx`를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여넣습니다.

---

## <a name="create-a-subscription-for-a-customer"></a>고객을 위한 구독 만들기

다음 예에서는 *Fabrikam toys* 에 대한 *Dev Team subscription* 이라는 구독을 만들고 *Wingtip* 재판매인을 구독에 연결합니다. 이전 단계에서 복사한 청구 범위 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`를 사용합니다. 

### <a name="rest"></a>[REST (영문)](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>요청 본문

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>응답

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

동일한 URL에 대해 GET을 수행하여 요청 상태를 가져올 수 있습니다.

### <a name="request"></a>요청

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>응답

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

진행 중 상태는 `provisioningState` 아래에 `Accepted` 상태로 반환됩니다. 

API 요청 본문의 두 번째 단계에서 복사한 선택적 *resellerId* 를 전달합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscriptionAlias` cmdlet을 포함하는 최신 버전의 모듈을 설치하려면 `Install-Module Az.Subscription`을 실행합니다. 최신 버전의 PowerShellGet을 설치하려면 [PowerShellGet 모듈 가져오기](/powershell/scripting/gallery/installing-psget)를 참조하세요.

청구 범위 `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`를 사용하여 다음 New-AzSubscriptionAlias 명령을 실행합니다. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

`New-AzSubscriptionAlias` 호출의 두 번째 단계에서 복사한 선택적 *resellerId* 를 전달합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 `az extension add --name account` 및 `az extension add --name alias`를 실행하여 확장을 설치합니다.

다음 [az account alias create](/cli/azure/account/alias#az_account_alias_create) 명령을 실행합니다. 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

`az account alias create` 호출의 두 번째 단계에서 복사한 선택적 *resellerId* 를 전달합니다.

---

## <a name="use-arm-template-or-bicep"></a>ARM 템플릿 또는 Bicep 사용

이전 섹션에서는 PowerShell, CLI 또는 REST API를 사용하여 구독을 만드는 방법을 살펴보았습니다. 구독 만들기를 자동화 해야 하는 경우 Azure Resource Manager 템플릿 (ARM 템플릿) 또는 [Bicep 파일](../../azure-resource-manager/bicep/overview.md)을 사용 하는 것이 좋습니다.

다음 ARM 템플릿은 구독을 만듭니다. `billingScope`에 고객 ID를 제공합니다. 구독은 루트 관리 그룹에서 만들어집니다. 구독을 만든 후 다른 관리 그룹으로 이동할 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]"
            }
        }
    ],
    "outputs": {}
}
```

또는 Bicep 파일을 사용 하 여 구독을 만듭니다.

```bicep
targetScope = 'managementGroup'

@description('Provide a name for the alias. This name will also be the display name of the subscription.')
param subscriptionAliasName string

@description('Provide the full resource ID of billing scope to use for subscription creation.')
param billingScope string

resource subscriptionAlias 'Microsoft.Subscription/aliases@2020-09-01' = {
  scope: tenant()
  name: subscriptionAliasName
  properties: {
    workload: 'Production'
    displayName: subscriptionAliasName
    billingScope: billingScope
  }
}
```

[관리 그룹 수준](../../azure-resource-manager/templates/deploy-to-management-group.md)에서 템플릿을 배포합니다. 다음 예제에서는 JSON ARM 템플릿 배포를 보여 주지만 대신 Bicep 파일을 배포할 수 있습니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

다음 요청 본문을 사용합니다.

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

---

새 관리 그룹으로 구독을 이동 하려면 다음 ARM 템플릿을 사용 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

또는 다음 Bicep 파일입니다.

```bicep
targetScope = 'managementGroup'

@description('Provide the ID of the management group that you want to move the subscription to.')
param targetMgId string

@description('Provide the ID of the existing subscription to move.')
param subscriptionId string

resource subToMG 'Microsoft.Management/managementGroups/subscriptions@2020-05-01' = {
  scope: tenant()
  name: '${targetMgId}/${subscriptionId}'
}
```

## <a name="next-steps"></a>다음 단계

* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 여러 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
