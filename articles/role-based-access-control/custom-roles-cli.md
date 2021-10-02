---
title: Azure CLI를 사용하여 사용자 지정 역할 만들기 또는 업데이트 - Azure RBAC
description: Azure CLI 및 Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure 사용자 지정 역할을 나열하고, 만들고, 업데이트하고, 삭제하는 방법 알아보기.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0a090f7a4ba00c9a7d55f06acc74556b604d6b7e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363336"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Azure CLI를 사용하여 사용자 지정 역할 만들기 또는 업데이트

> [!IMPORTANT]
> `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [사용자 지정 역할](built-in-roles.md)을 만들면 됩니다. 이 문서에서는 Azure CLI를 사용하여 사용자 지정 역할을 나열하고, 만들고, 업데이트하고, 삭제하는 방법을 설명합니다.

사용자 지정 역할을 만드는 방법에 대한 단계별 자습서는 [자습서: Azure CLI를 사용하여 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)를 참조하세요.

## <a name="prerequisites"></a>필수 요건

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))
- [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

할당에 사용할 수 있는 사용자 지정 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용합니다. 다음 예제에서는 현재 구독의 모든 사용자 지정 역할을 나열합니다.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>사용자 지정 역할 정의 나열하기

사용자 지정 역할 정의를 나열하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용합니다. 이 명령은 기본 제공 역할에 사용하는 것과 동일한 명령입니다.

```azurecli
az role definition list --name {roleName}
```

다음 예제에서는 *Virtual Machine Operator* 역할 정의를 나열합니다.

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
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

다음 예제에서는 *Virtual Machine Operator* 역할의 작업만 나열합니다.

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
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
  ]
]
```

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [az role definition create](/cli/azure/role/definition#az_role_definition_create)를 사용합니다. 역할 정의는 JSON 설명이거나, JSON 설명이 포함된 파일에 대한 경로가 될 수 있습니다.

```azurecli
az role definition create --role-definition {roleDefinition}
```

다음 예제에서는 *Virtual Machine Operator* 라는 사용자 지정 역할을 만듭니다. 이 사용자 지정 역할은 *Microsoft.Compute, Microsoft.Storage* 및 *Microsoft.Network*  리소스 공급자의 모든 읽기 작업에 대한 액세스를 할당하고 가상 머신을 시작, 다시 시작 및 모니터링할 수 있는 액세스 권한을 할당합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다. 이 예제에서는 입력으로 JSON 파일을 사용합니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 먼저 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 [az role definition update](/cli/azure/role/definition#az_role_definition_update)를 사용하여 업데이트된 역할 정의를 저장합니다.

```azurecli
az role definition update --role-definition {roleDefinition}
```

다음 예제에서는 *Microsoft.Insights/diagnosticSettings/* 작업을 에 `Actions` 추가하고 Virtual Machine Operator 사용자 지정 역할에 대한 관리 그룹을 에 `AssignableScopes` 추가합니다.  `AssignableScopes`에 관리 그룹을 추가하는 것은 현재 미리 보기로 제공됩니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [az role definition delete](/cli/azure/role/definition#az_role_definition_delete)를 사용합니다. 삭제할 역할을 지정하려면 역할 이름이나 역할 ID를 사용합니다. 역할 ID를 결정하려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용합니다.

```azurecli
az role definition delete --name {roleNameOrId}
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 삭제합니다.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure CLI를 사용하여 Azure 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure 리소스 공급자 작업](resource-provider-operations.md)