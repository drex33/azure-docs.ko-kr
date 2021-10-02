---
title: Azure 사용자 지정 역할 - Azure RBAC
description: Azure 리소스에 대한 세분화된 액세스 관리를 위해 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할을 만드는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2021
ms.author: rolyon
ms.openlocfilehash: 31da46a7eb6a021a14641d13f9c76ab7406f9b94
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363414"
---
# <a name="azure-custom-roles"></a>Azure 사용자 지정 역할

> [!IMPORTANT]
> `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](built-in-roles.md)을 만들면 됩니다. 기본 제공 역할과 마찬가지로 관리 그룹(미리 보기에만 해당), 구독 및 리소스 그룹 범위에서 사용자 지정 역할을 사용자, 그룹 및 서비스 주체에 할당할 수 있습니다.

동일한 Azure AD 디렉터리를 신뢰하는 구독 간에 사용자 지정 역할을 공유할 수 있습니다. 사용자 지정 역할은 디렉터리당 **5,000** 개로 제한됩니다. (Azure 독일 및 Azure 중국 21Vianet의 경우 제한은 사용자 지정 역할 2,000개입니다.) Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 사용자 지정 역할을 만들 수 있습니다.

## <a name="steps-to-create-a-custom-role"></a>사용자 지정 역할을 만드는 단계

사용자 지정 역할을 만드는 기본적인 단계는 다음과 같습니다.

1. 필요한 권한을 결정합니다.

    사용자 지정 역할을 만들 때 사용 권한을 정의 하는 데 사용할 수 있는 작업을 알고 있어야 합니다. 일반적으로 기존 기본 제공 역할로 시작한 다음, 필요에 따라 수정합니다. `Actions` `NotActions` [역할 정의](role-definitions.md)의 또는 속성에 동작을 추가 합니다. 데이터 작업을 수행 하는 경우 또는 속성에 해당 작업을 추가 합니다 `DataActions` `NotDataActions` .

    자세한 내용은 다음 섹션에서 [필요한 권한을 결정하는 방법](#how-to-determine-the-permissions-you-need)을 참조하세요.

1. 사용자 지정 역할을 만들 방법을 결정합니다.

    [Azure Portal](custom-roles-portal.md), [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md) 또는 [REST API](custom-roles-rest.md)를 사용하여 사용자 지정 역할을 만들 수 있습니다.

1. 사용자 지정 역할을 만듭니다.

    가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. Azure Portal을 사용하여 사용자 지정 역할을 만드는 단계별 방법은 [Azure Portal로 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-portal.md)를 참조하세요.

1. 사용자 지정 역할을 테스트합니다.

    사용자 지정 역할이 있으면 해당 역할을 테스트하여 예상대로 작동하는지 확인해야 합니다. 나중에 조정해야 하는 경우 사용자 지정 역할을 업데이트할 수 있습니다.

## <a name="how-to-determine-the-permissions-you-need"></a>필요한 권한 결정 방법

Azure에는 사용자 지정 역할에 잠재적으로 포함할 수 있는 수천 개의 권한이 있습니다. 사용자 지정 역할에 추가할 권한을 결정하는 데 도움이 되는 몇 가지 방법은 다음과 같습니다.

- 기존 [기본 제공 역할](built-in-roles.md)을 확인합니다.

    기존 역할을 수정하거나 여러 역할에 사용되는 사용 권한을 조합할 수 있습니다.

- 액세스 권한을 부여할 Azure 서비스를 나열합니다.

- [Azure 서비스에 매핑되는 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)를 결정합니다.

    Azure 서비스는 [리소스 공급자](../azure-resource-manager/management/overview.md)를 통해 해당 기능 및 사용 권한을 노출합니다. 예를 들어, Microsoft. Compute 리소스 공급자는 가상 머신 리소스를 제공하고 Microsoft 청구 리소스 공급자는 구독 및 청구 리소스를 제공합니다. 리소스 공급자를 알면 사용자 지정 역할에 필요한 권한을 제한하고 결정하는 데 도움이 될 수 있습니다.

    Azure Portal을 사용하여 사용자 지정 역할을 만드는 경우 키워드를 검색하여 리소스 공급자를 결정할 수도 있습니다. 이 검색 기능은 [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-portal.md#step-4-permissions)에 설명되어 있습니다.

    ![리소스 공급자를 사용하여 권한 창 추가](./media/shared/add-permissions-provider.png)

- [사용 가능한 권한](resource-provider-operations.md)을 검색하여 포함하려는 사용 권한을 찾습니다.

    Azure Portal을 사용하여 사용자 지정 역할을 만드는 경우 키워드별로 사용 권한을 검색할 수 있습니다. 예를 들어 가상 컴퓨터 또는 청구 권한을 검색할 수 있습니다. 모든 사용 권한을 CSV 파일로 다운로드한 다음 이 파일에서 검색할 수도 있습니다. 이 검색 기능은 [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-portal.md#step-4-permissions)에 설명되어 있습니다.

    ![권한 목록 추가](./media/shared/add-permissions-list.png)

## <a name="custom-role-example"></a>사용자 지정 역할 예제

다음은 Azure PowerShell을 사용하여 JSON 형식으로 표시된 대로 사용자 지정 역할을 보여줍니다. 이 사용자 지정 역할은 가상 머신의 모니터링 및 재시작에 사용할 수 있습니다.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

다음은 Azure CLI로 표시된 동일한 사용자 지정 역할을 보여줍니다.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="custom-role-properties"></a>사용자 지정 역할 속성

다음 테이블은 사용자 지정 역할 속성의 의미에 대해 설명합니다.

| 속성 | 필수 | Type | Description |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | 예 | String | 사용자 지정 역할의 표시 이름입니다. 역할 정의는 관리 그룹 또는 구독 수준 리소스이지만 역할 정의는 동일한 Azure AD 디렉터리를 공유하는 여러 구독에서 사용할 수 있습니다. 표시 이름은 Azure AD 디렉터리의 범위에서 고유해야 합니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 128자입니다. |
| `Id`</br>`name` | 예 | String | 사용자 지정 역할의 고유 ID입니다. Azure PowerShell 및 Azure CLI의 경우 이 ID는 새 역할을 만들 때 자동으로 생성됩니다. |
| `IsCustom`</br>`roleType` | 예 | String | 사용자 지정 역할인지 여부를 나타냅니다. 사용자 지정 역할인 경우 `true` 또는 `CustomRole`로 설정합니다. 기본 제공 역할인 경우 `false` 또는 `BuiltInRole`으로 설정합니다. |
| `Description`</br>`description` | 예 | String | 사용자 지정 역할에 대한 설명입니다. 문자, 숫자, 공백 및 특수 문자를 포함할 수 있습니다. 최대 문자 수는 1,024자입니다. |
| `Actions`</br>`actions` | 예 | String[] | 역할에서 수행할 수 있는 제어 평면 작업을 지정 하는 문자열의 배열입니다. 자세한 내용은 [Actions](role-definitions.md#actions)를 참조하세요. |
| `NotActions`</br>`notActions` | No | String[] | 허용 된에서 제외 되는 제어 평면 작업을 지정 하는 문자열 배열입니다 `Actions` . 자세한 내용은 [NotActions](role-definitions.md#notactions)를 참조하세요. |
| `DataActions`</br>`dataActions` | No | String[] | 해당 개체 내의 데이터에 대해 역할이 수행할 수 있도록 허용 하는 데이터 평면 작업을 지정 하는 문자열의 배열입니다. `DataActions`를 사용하여 사용자 지정 역할을 만드는 경우 해당 역할은 관리 그룹 범위에서 할당할 수 없습니다. 자세한 내용은 [DataActions](role-definitions.md#dataactions)를 참조하세요. |
| `NotDataActions`</br>`notDataActions` | No | String[] | 허용 된에서 제외 되는 데이터 평면 작업을 지정 하는 문자열 배열입니다 `DataActions` . 자세한 내용은 [NotDataActions](role-definitions.md#notdataactions)를 참조하세요. |
| `AssignableScopes`</br>`assignableScopes` | 예 | String[] | 할당에 사용할 수 있는 사용자 지정 역할에 대한 범위를 지정하는 문자열 배열입니다. 사용자 지정 역할의 `AssignableScopes`에서 하나의 관리 그룹만 정의할 수 있습니다. `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다. 자세한 내용은 [AssignableScopes](role-definitions.md#assignablescopes)를 참조하세요. |

사용 권한 문자열은 대/소문자를 구분하지 않습니다. 사용자 지정 역할을 만들 때 규칙은 [Azure 리소스 공급자 작업](resource-provider-operations.md)의 사용 권한에 대해 표시되는 사례와 일치시키는 것입니다.

## <a name="wildcard-permissions"></a>와일드카드 사용 권한

`Actions`, `NotActions`, `DataActions`, `NotDataActions`는 사용 권한을 정의하는 와일드 카드(`*`)를 지원합니다. 와일드 카드(`*`)는 사용자가 제공하는 동작 문자열과 일치하는 모든 항목으로 사용 권한을 확장합니다. 예를 들어 Azure Cost Management 및 내보내기와 관련된 모든 권한을 추가하려고 한다고 가정해 보겠습니다. 이러한 모든 동작 문자열을 추가할 수도 있겠지만,

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

대신 간단히 와일드카드 문자열을 추가할 수도 있습니다. 예를 들어 다음 와일드카드 문자열은 이전의 5개 문자열과 같습니다. 여기에는 추가될 수 있는 이후의 내보내기 권한도 포함됩니다.

```
Microsoft.CostManagement/exports/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>사용자 지정 역할을 생성, 삭제, 업데이트 또는 볼 수 있는 사용자

기본 제공 역할과 마찬가지로, `AssignableScopes` 속성은 할당에 사용할 수 있는 역할에 대한 범위를 지정합니다. 또한 사용자 지정 역할에 대한 `AssignableScopes` 속성은 사용자 지정 역할을 생성, 삭제, 업데이트 또는 볼 수 있는 사용자도 제어합니다.

| 작업 | 작업 | Description |
| --- | --- | --- |
| 사용자 지정 역할 만들기/삭제 | `Microsoft.Authorization/ roleDefinitions/write` | 사용자 지정 역할의 모든에 대해이 작업이 부여 된 사용자는 `AssignableScopes` 해당 범위에서 사용할 사용자 지정 역할을 만들거나 삭제할 수 있습니다. 예를 들어 관리 그룹, 구독, 리소스 그룹의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 업데이트 | `Microsoft.Authorization/ roleDefinitions/write` | 사용자 지정 역할의 모든에 대해이 작업을 수행 하는 사용자는 `AssignableScopes` 해당 범위에서 사용자 지정 역할을 업데이트할 수 있습니다. 예를 들어 관리 그룹, 구독, 리소스 그룹의 [소유자](built-in-roles.md#owner) 및 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)가 있습니다. |
| 사용자 지정 역할 보기 | `Microsoft.Authorization/ roleDefinitions/read` | 범위에서이 작업이 부여 된 사용자는 해당 범위에서 할당에 사용할 수 있는 사용자 지정 역할을 볼 수 있습니다. 모든 기본 제공 역할은 사용자 지정 역할을 할당에 사용할 수 있도록 허용합니다. |

## <a name="custom-role-limits"></a>사용자 지정 역할 제한

다음 목록은 사용자 지정 역할에 대한 제한을 설명합니다.

- 각 디렉터리는 최대 **5000** 개의 사용자 지정 역할을 가질 수 있습니다.
- Azure 독일 및 Azure 중국 21Vianet의 경우 디렉터리별로 최대 2000개의 사용자 지정 역할을 가질 수 있습니다.
- `AssignableScopes`를 루트 범위(`"/"`)로 설정할 수 없습니다.
- `AssignableScopes`에서는 와일드카드(`*`)를 사용할 수 없습니다. 이 와일드카드 제한은 사용자가 역할 정의를 업데이트하여 범위에 대한 액세스 권한을 얻을 수 없도록 하는 데 도움이 됩니다.
- 사용자 지정 역할의 `AssignableScopes`에서 하나의 관리 그룹만 정의할 수 있습니다. `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.
- 작업 문자열에는 와일드카드를 하나만 사용할 수 있습니다.
- `DataActions`의 사용자 지정 역할은 관리 그룹 범위에서 할당할 수 없습니다.
- Azure Resource Manager는 관리 그룹이 역할 정의의 할당 가능한 범위에 있는지 확인하지 않습니다.

사용자 지정 역할 및 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹이란?](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)을 참조하세요.

## <a name="input-and-output-formats"></a>입력 및 출력 양식

명령줄을 사용하여 사용자 지정 역할을 만드는 경우 일반적으로 JSON을 사용하여 사용자 지정 역할에 대해 원하는 속성을 지정합니다. 사용하는 도구에 따라 입력 및 출력 형식이 약간 다르게 표시됩니다. 이 섹션에는 도구에 따른 입력 및 출력 형식이 나열됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell을 사용하여 사용자 지정 역할을 만들려면 다음 입력을 제공해야 합니다.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Azure PowerShell을 사용하여 사용자 지정 역할을 업데이트하려면 다음 입력을 제공해야 합니다. `Id` 속성이 추가되었습니다. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

다음은 Azure PowerShell 및 [ConvertTo-Json](/powershell/module/microsoft.powershell.utility/convertto-json) 명령을 사용하여 사용자 지정 역할을 나열하는 경우의 출력 예시입니다. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 사용자 지정 역할을 만들거나 업데이트하려면 다음 입력을 제공해야 합니다. 이 형식은 Azure PowerShell을 사용하여 사용자 지정 역할을 만들 때와 동일한 형식입니다.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

다음은 Azure CLI를 사용하여 사용자 지정 역할을 나열하는 경우의 출력 예시입니다.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

REST API를 사용하여 사용자 지정 역할을 만들거나 업데이트하려면 다음 입력을 제공해야 합니다. 이 형식은 Azure Portal을 사용하여 사용자 지정 역할을 만들 때 생성된 것과 동일한 형식입니다.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

다음은 REST API를 사용하여 사용자 지정 역할을 나열하는 경우의 출력 예시입니다.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기](tutorial-custom-role-powershell.md)
- [자습서: Azure CLI를 사용하여 Azure 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)
- [Azure 역할 정의 이해](role-definitions.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
