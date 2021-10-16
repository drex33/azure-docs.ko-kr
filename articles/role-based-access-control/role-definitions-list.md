---
title: Azure 역할 정의 나열 - Azure RBAC
description: Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 Azure 기본 제공 및 사용자 지정 역할을 나열하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15d8b077feadefb95d40014e257b9c300ef1aba2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069859"
---
# <a name="list-azure-role-definitions"></a>Azure 역할 정의 나열

역할 정의는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업 집합입니다. 일반적으로 단지 역할이라고 합니다. [Azure RBAC(역할 기반 액세스 제어)](overview.md)는 120개 이상의 [기본 제공 역할](built-in-roles.md)을 사용하거나 사용자 고유의 사용자 지정 역할을 만들 수 있습니다. 이 문서에서는 Azure 리소스에 대한 액세스 권한을 부여하는 데 사용할 수 있는 기본 제공 및 사용자 지정 역할을 나열하는 방법을 설명합니다.

Azure Active Directory의 관리자 역할 목록을 보려면 [Azure Active Directory의 관리자 역할 권한](../active-directory/roles/permissions-reference.md)을 참조하세요.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>모든 역할 나열

Azure Portal의 모든 역할을 나열하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭하고 임의의 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

   ![새 환경을 사용하는 역할 목록을 보여 주는 스크린샷](./media/shared/roles-list.png)
  
1. 특정 역할에 대한 권한을 보려면 **자세히** 열에서 **보기** 링크를 클릭합니다.

    권한 창이 표시됩니다.

1. **권한** 탭을 클릭하여 선택한 역할에 대한 권한을 확인하고 검색합니다.

   ![새 환경을 사용하는 역할 권한을 보여 주는 스크린샷](./media/role-definitions-list/role-permissions.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>모든 역할 나열

Azure PowerShell의 모든 역할을 나열하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>역할 정의 나열

특정 역할에 대한 세부 정보를 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>JSON 형식의 역할 정의를 나열합니다.

JSON 형식으로 역할을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>역할 정의의 권한 나열

특정 역할에 대한 권한을 나열하려면 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>모든 역할 나열

Azure CLI의 모든 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용합니다.

```azurecli
az role definition list
```

다음 예제에서는 사용 가능한 모든 역할 정의의 이름과 설명을 나열합니다.

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

다음 예제에서는 모든 기본 제공 역할 정의를 나열합니다.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>역할 정의 나열

역할에 대한 세부 정보를 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용합니다.

```azurecli
az role definition list --name {roleName}
```

다음 예제에서는 *Contributor* 역할 정의를 나열합니다.

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>역할 정의의 권한 나열

다음 예제에서는 목록에서 *참가자* 역할의 *actions* 및 *notActions* 만 나열합니다.

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

다음 예제에서는 *Virtual Machine 참가자* 역할의 작업만 나열합니다.

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>역할 정의 나열

역할 정의를 나열하려면 [역할 정의-목록](/rest/api/authorization/roledefinitions/list) REST API를 사용합니다. 결과를 구체화하려면 범위와 선택적 필터를 지정합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 역할 정의를 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | 구독 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |

    이전 예제에서 microsoft.web은 App Service 인스턴스를 참조하는 리소스 공급자입니다. 마찬가지로 다른 리소스 공급자를 사용하여 범위를 지정할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 및 지원되는 [Azure 리소스 공급자 작업](resource-provider-operations.md)을 참조하세요.  
     
1. *{filter}* 를 역할 정의 목록을 필터링하기 위해 적용하려는 조건으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | 지정된 범위 및 모든 하위 범위에 대한 역할 정의를 나열합니다. |
    > | `$filter=type+eq+'{type}'` | 지정된 형식의 역할 정의를 나열합니다. 역할의 형식은 `CustomRole` 또는 `BuiltInRole`일 수 있습니다. |

다음 요청을 사용하면 구독 범위에서 사용자 지정 역할 정의를 나열합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

다음은 출력 예제입니다.

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>역할 정의 나열

특정 역할에 대한 세부 정보를 나열하려면 [Role Definitions - Get](/rest/api/authorization/roledefinitions/get) 또는 [Role Definitions - Get By Id](/rest/api/authorization/roledefinitions/getbyid) REST API를 사용합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    디렉터리 수준 역할 정의의 경우 다음 요청을 사용할 수 있습니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 역할 정의를 나열하려는 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |
    > | `subscriptions/{subscriptionId1}` | 구독 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 리소스 |
     
1. *{roleDefinitionId}* 를 역할 정의 식별자로 바꿉니다.

다음 요청을 사용하면 [Reader](built-in-roles.md#reader) 역할 정의를 나열합니다.

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

다음은 출력 예제입니다.

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure 기본 제공 역할](built-in-roles.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure Portal을 사용하여 Azure 역할 할당을 나열](role-assignments-list-portal.md)합니다.
- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
