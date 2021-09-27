---
title: 위임에 대한 액세스 권한 제거
description: Azure Lighthouse의 서비스 공급자에게 위임된 리소스에 대한 액세스 권한을 제거하는 방법을 알아봅니다.
ms.date: 09/08/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 16d92c58e08e06832781ff7d5095039cedbb344f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736589"
---
# <a name="remove-access-to-a-delegation"></a>위임에 대한 액세스 권한 제거

고객의 구독 또는 리소스 그룹이 [Azure Lighthouse](../overview.md)의 서비스 공급자에게 위임된 후 필요한 경우 위임이 제거될 수 있습니다. 위임이 제거되면 서비스 공급자 테넌트의 사용자에게 이전에 부여되었던 [Azure 위임된 리소스 관리](../concepts/architecture.md) 액세스 권한이 더 이상 적용되지 않습니다.

사용자에게 적절한 권한이 있는 경우 고객 테넌트 또는 서비스 공급자 테넌트의 사용자가 위임 제거를 수행할 수 있습니다.

> [!TIP]
> 이 항목에서는 서비스 공급자 및 고객만 언급하지만 [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에서도 동일한 프로세스를 사용할 수 있습니다.

## <a name="customers"></a>고객

[소유자](../../role-based-access-control/built-in-roles.md#owner)와 같이 `Microsoft.Authorization/roleAssignments/write` 권한이 포함된 역할이 있는 고객 테넌트의 사용자는 해당 구독(또는 해당 구독의 리소스 그룹)에 대한 서비스 공급자 액세스 권한을 제거할 수 있습니다. 이렇게 하기 위해 사용자는 Azure Portal의 [서비스 공급자 페이지](view-manage-service-providers.md#remove-service-provider-offers)로 이동하여 **서비스 공급자 제품** 화면에서 제품을 찾고 해당 제품의 행에서 휴지통 아이콘을 선택할 수 있습니다.

삭제를 확인한 후에는 서비스 공급자 테넌트에 있는 사용자가 이전에 위임된 리소스에 액세스할 수 없게 됩니다.

## <a name="service-providers"></a>서비스 공급자

관리 테넌트의 사용자는 고객의 리소스에 대한 [관리되는 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)이 부여된 경우 위임된 리소스에 대한 액세스 권한을 제거할 수 있습니다. 서비스 공급자 사용자에게 이 역할이 할당되지 않은 경우 고객 테넌트의 사용자만이 위임을 제거할 수 있습니다.

아래 예제는 [온보딩 프로세스](onboard-customer.md) 중에 매개 변수 파일에 포함될 수 있는 **관리되는 서비스 등록 할당 삭제 역할** 을 부여하는 할당을 보여 줍니다.

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

이 역할은 Azure Marketplace에 게시할 [관리되는 서비스 제품을 만들](../../marketplace/plan-managed-service-offer.md) 때 **권한 부여** 에서 선택할 수도 있습니다.

이 권한이 있는 사용자는 다음 방법 중 하나를 사용하여 위임을 제거할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [내 고객 페이지](view-manage-customers.md)로 이동합니다.
2. **위임** 을 선택합니다.
3. 제거하려는 위임을 찾은 다음 해당 행에 표시 되는 휴지통 아이콘을 선택합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>다음 단계

- [Azure Lighthouse 아키텍처](../concepts/architecture.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객** 으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
- [이전 위임을 업데이트](update-delegation.md)하는 방법을 알아봅니다.
