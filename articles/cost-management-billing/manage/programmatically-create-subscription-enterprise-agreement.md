---
title: 최신 API를 사용하여 프로그래밍 방식으로 Azure 기업계약 구독 만들기
description: 최신 버전의 REST API, Azure CLI, Azure PowerShell 및 Azure Resource Manager 템플릿을 사용하여 프로그래밍 방식으로 Azure 기업계약 구독을 만드는 방법을 알아봅니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 06/22/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b30856b5fe84f8c66e4029714e4bf39fca0470a9
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541305"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>최신 API를 사용하여 프로그래밍 방식으로 Azure 기업계약 구독 만들기

이 문서는 최신 API 버전을 사용하여 EA 청구 계정에 대한 Azure EA(기업계약) 구독을 프로그래밍 방식으로 만드는 데 도움이 됩니다. 이전 미리 보기 버전을 여전히 사용 중인 경우 [레거시 API를 사용하여 프로그래밍 방식으로 Azure 구독 만들기](programmatically-create-subscription-preview.md)를 참조하세요. 

이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

프로그래밍 방식으로 Azure 구독을 만든 경우 해당 구독은 Microsoft 또는 공인된 재판매인으로부터 Azure 서비스를 구입한 계약이 적용됩니다. 자세한 내용은 [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

사용자는 구독을 만들려면 등록 계정에 대한 소유자 역할이 있어야 합니다. 역할을 얻는 방법은 두 가지입니다.

* 등록의 엔터프라이즈 관리자는 귀하를 [계정 소유자로 만들 수](https://ea.azure.com/helpdocs/addNewAccount) 있고(로그인 필요) 그러면 귀하가 등록 계정의 소유자가 됩니다.
* 등록 계정의 기존 소유자가 [액세스 권한을 부여](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)할 수 있습니다. 

SPN(서비스 주체)을 사용하여 EA 구독을 만들려면 등록 계정의 소유자가 [해당 서비스 주체에게 구독을 만들 수 있는 권한을 부여](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)해야 합니다. SPN을 사용하여 구독을 만드는 경우 [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true ) 또는 [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list)를 사용하여 Azure AD 애플리케이션 등록의 ObjectId를 서비스 사용자 ObjectId로 사용합니다. EA 역할 할당 API 요청에 대한 자세한 내용은 [Azure 기업계약 서비스 주체 이름에 역할 할당](assign-roles-azure-service-principals.md)을 참조하세요. 이 문서에는 SPN에 할당할 수 있는 역할(및 역할 정의 ID) 목록이 포함되어 있습니다.

  > [!NOTE]
  > - 올바른 API 버전을 사용하여 등록 계정 소유자 권한을 부여했는지 확인합니다. 이 문서 및 여기에서 설명하는 API에는 [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) API를 사용합니다. 
  > - 최신 API를 사용하기 위해 마이그레이션하는 경우 [2015-07-01 버전](grant-access-to-create-subscription.md)으로 만든 이전 구성은 최신 API에서 사용하도록 자동으로 변환되지 않습니다.

## <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자와 연결된 등록 계정에 귀하가 추가되면 Azure는 계정-등록 관계를 사용하여 구독 요금을 청구할 위치를 결정합니다. 계정에서 만든 모든 구독은 계정이 있는 EA 등록에 청구됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다.

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리* 에 로그인해야 합니다.

### <a name="rest"></a>[REST (영문)](#tab/rest)

액세스 권한이 있는 모든 등록 계정을 나열하도록 요청합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

액세스 권한이 있는 모든 등록 계정이 API 응답에 나열됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

청구 범위와 `id`의 값은 동일합니다. 등록 계정의 `id`는 구독 요청이 시작되는 청구 범위입니다. 구독을 만들기 위해 문서의 뒷부분에서 사용하는 필수 매개 변수이므로 ID를 알고 있어야 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 값을 가져오려면 Azure CLI 또는 REST API를 사용하십시오.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

액세스 권한이 있는 모든 등록 계정을 나열하도록 요청합니다.

```azurecli-interactive
> az billing account list
```

액세스 권한이 있는 모든 등록 계정이 응답에 나열됩니다.

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

청구 범위와 `id`의 값은 동일합니다. 등록 계정의 `id`는 구독 요청이 시작되는 청구 범위입니다. 구독을 만들기 위해 문서의 뒷부분에서 사용하는 필수 매개 변수이므로 ID를 알고 있어야 합니다.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예에서는 이전 단계에서 선택한 등록 계정에 *Dev Team Subscription* 이라는 구독을 만듭니다. 

### <a name="rest"></a>[REST (영문)](#tab/rest)

PUT API를 호출하여 구독 만들기 요청/별칭을 만듭니다.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

요청 본문에서, `enrollmentAccounts` 중 하나의 `billingScope` 및 `id`를 입력합니다.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload`에 허용되는 값은 `Production` 및 `DevTest`입니다.

#### <a name="response"></a>응답

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscriptionAlias` cmdlet을 포함하는 최신 버전의 모듈을 설치하려면 `Install-Module Az.Subscription`을 실행합니다. 최신 버전의 PowerShellGet을 설치하려면 [PowerShellGet 모듈 가져오기](/powershell/scripting/gallery/installing-psget)를 참조하세요.

청구 범위 `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`를 사용하여 다음 [New-AzSubscriptionAlias](/powershell/module/az.subscription/get-azsubscriptionalias) 명령을 실행합니다. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 `az extension add --name account` 및 `az extension add --name alias`를 실행하여 확장을 설치합니다.

다음 [az account alias create](/cli/azure/account/alias#az_account_alias_create) 명령을 실행하고 `enrollmentAccounts` 중 하나의 `billing-scope` 및 `id`를 입력합니다. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

명령의 응답에 subscriptionId가 포함됩니다.

```azurecli
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

---

## <a name="use-arm-template"></a>ARM 템플릿 사용

이전 섹션에서는 PowerShell, CLI 또는 REST API를 사용하여 구독을 만드는 방법을 살펴보았습니다. 자동으로 구독을 만들어야 하는 경우 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하는 것이 좋습니다.

다음 템플릿에서 구독을 만듭니다. `billingScope`에 등록 계정 ID를 제공합니다. 구독은 루트 관리 그룹에서 만들어집니다. 구독을 만든 후 다른 관리 그룹으로 이동할 수 있습니다.

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

[관리 그룹 수준](../../azure-resource-manager/templates/deploy-to-management-group.md)에서 템플릿을 배포합니다.

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
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321'
```

---

구독을 새 관리 그룹으로 이동하려면 다음 템플릿을 사용합니다.

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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 오직 Azure 엔터프라이즈 구독만이 이 API를 사용하여 생성됩니다.
- 등록 계정당 구독 수는 2000개로 제한됩니다. 그 후에는 Azure Portal에서만 계정에 대해 더 많은 구독을 만들 수 있습니다. API를 통해 더 많은 구독을 만들려면 또 다른 등록 계정을 만드세요. 취소, 삭제 및 전송된 구독은 2000개 제한에 포함됩니다.
- 계정 소유자가 아니지만 Azure RBAC를 통해 등록 계정에 추가된 사용자는 Azure Portal에서 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 여러 구독을 관리하는 방법에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
* 구독의 관리 그룹을 변경하려면 [구독 이동](../../governance/management-groups/manage.md#move-subscriptions)을 참조하세요.
