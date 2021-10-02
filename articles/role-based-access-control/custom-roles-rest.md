---
title: REST API를 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트하기-Azure RBAC
description: REST API 및 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할을 나열하고, 만들고, 업데이트하고, 삭제하는 방법 알아보기.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5c7e816f15400a28e8b10f4aea7a2315c89048be
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352920"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>REST API를 사용하여 Azure 사용자 지정 역할을 만들거나 업데이트하기

> [!IMPORTANT]
> `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](built-in-roles.md)을 만들면 됩니다. 이 문서에서는 REST API를 사용하여 사용자 지정 역할을 나열하고, 만들고, 업데이트하고, 삭제하는 방법을 설명합니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

디렉터리의 모든 사용자 지정 역할을 나열하려면 [역할 정의-목록](/rest/api/authorization/roledefinitions/list) REST API를 사용합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. *{Filter}* 를 역할 유형으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | CustomRole 유형을 기반으로 필터링하기 |

## <a name="list-custom-roles-at-a-scope"></a>범위에서 사용자 지정 역할 나열하기

범위에서 사용자 지정 역할을 나열하려면 [역할 정의-목록](/rest/api/authorization/roledefinitions/list) REST API를 사용합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 리소스 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{Filter}* 를 역할 유형으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | CustomRole 유형을 기반으로 필터링하기 |

## <a name="list-a-custom-role-definition-by-name"></a>이름별로 사용자 지정 역할 정의 나열하기

표시 이름으로 해당 역할에 대한 정보를 가져오려면 [역할 정의-가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용합니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 리소스 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{Filter}* 를 역할의 표시 이름으로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | Assert | 설명 |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | 역할의 정확한 표시 이름에 대한 URL 인코딩 형식을 사용합니다. 예: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>ID 별로 사용자 지정 역할 정의 나열하기

고유 식별자를 사용하여 해당 역할에 대한 정보를 가져오려면 [역할 정의-가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용합니다.

1. [역할 정의 - 나열](/rest/api/authorization/roledefinitions/list) REST API를 사용하여 역할에 대한 GUID 식별자를 가져옵니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 리소스 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{roleDefinitionId}* 를 역할 정의의 GUID 식별자로 바꿉니다.

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [역할 정의 - 만들기 또는 업데이트](/rest/api/authorization/roledefinitions/createorupdate) REST API를 사용합니다. 해당 API를 호출하려면 모든 `assignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/write`권한이 있는 역할이 할당된 사용자로 로그인해야 합니다. 기본 제공 역할의 경우 [Owner](built-in-roles.md#owner) 및 [User Access Administrator](built-in-roles.md#user-access-administrator)만 해당 권한을 가집니다.

1. 사용자 지정 역할에 대한 권한을 만드는 데 사용할 수 있는 [리소스 공급자 작업](resource-provider-operations.md) 목록을 검토합니다.

1. GUID 도구를 사용하여 사용자 지정 역할 식별자에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `00000000-0000-0000-0000-000000000000`입니다.

1. 다음 요청 및 본문으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. URI 내에서 *{scope}* 를 사용자 지정 역할의 첫 번째 `assignableScopes`로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

1. 요청 본문 내에서 *{roleDefinitionId}* 를 GUID 식별자로 바꿉니다.

1. `assignableScopes`이 구독 또는 리소스 그룹인 경우 *{subscriptionId}* 또는 *{resourceGroup}* 인스턴스를 사용자의 식별자로 바꿉니다.

1. `assignableScopes`이 관리 그룹인 경우 *{groupId}* 인스턴스를 관리 그룹 식별자로 바꿉니다. `assignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.

1. `actions`속성에서 역할이 수행할 수 있는 작업을 추가합니다.

1. `notActions`속성에서 허용되는 에서 제외되는 작업을 추가합니다. `actions`

1. `roleName` 및 `description` 속성에서 고유한 역할 이름 및 설명을 지정합니다. 속성에 대한 자세한 내용은 [Azure 사용자 지정 역할](custom-roles.md)을 참조하세요.

    요청 본문의 예제는 다음과 같습니다.

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 [역할 정의 - 만들기 또는 업데이트](/rest/api/authorization/roledefinitions/createorupdate) REST API를 사용합니다. 해당 API를 호출하려면 모든 `assignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/write`권한이 있는 역할이 할당된 사용자로 로그인해야 합니다. 기본 제공 역할의 경우 [Owner](built-in-roles.md#owner) 및 [User Access Administrator](built-in-roles.md#user-access-administrator)만 해당 권한을 가집니다.

1. [역할 정의 - 나열 ](/rest/api/authorization/roledefinitions/list) 또는 [ 역할 정의 - 가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용하여 사용자 지정 역할에 대한 정보를 가져옵니다. 자세한 내용은 앞에서 설명한 [사용자 지정 역할 나열](#list-custom-roles) 섹션을 참조하세요.

1. 다음 요청으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 사용자 지정 역할의 첫 번째 `assignableScopes`로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

1. 사용자 지정 역할에 대한 정보에 따라 다음 형식의 요청 본문을 만듭니다.

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. 요청 본문을 사용자 지정 역할에 적용하려는 변경 내용으로 업데이트합니다.

    새 진단 설정 작업이 추가된 요청 본문의 예제는 다음과 같습니다.

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [역할 정의 - 삭제](/rest/api/authorization/roledefinitions/delete) REST API를 사용합니다. 해당 API를 호출하려면 모든 `assignableScopes`에 대한 `Microsoft.Authorization/roleDefinitions/delete`권한이 있는 역할이 할당된 사용자로 로그인해야 합니다. 기본 제공 역할의 경우 [Owner](built-in-roles.md#owner) 및 [User Access Administrator](built-in-roles.md#user-access-administrator)만 해당 권한을 가집니다.

1. [역할 정의 - 나열 ](/rest/api/authorization/roledefinitions/list) 또는 [역할 정의 - 가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용하여 사용자 지정 역할의 GUID 식별자를 가져옵니다. 자세한 내용은 앞에서 설명한 [사용자 지정 역할 나열](#list-custom-roles) 섹션을 참조하세요.

1. 다음 요청으로 시작합니다.

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 삭제하려는 사용자 지정 역할에 대한 범위로 바꿉니다.

    > [!div class="mx-tableFixed"]
    > | 범위 | 유형 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 관리 그룹 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 역할](custom-roles.md)
- [REST API를 사용하여 Azure 역할 할당](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)
