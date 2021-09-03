---
title: Azure RBAC의 범위 이해
description: Azure RBAC(역할 기반 액세스 제어)의 범위와 리소스에 대한 범위를 결정하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/09/2021
ms.author: rolyon
ms.openlocfilehash: 3fc3d614ef26235325e0b9a9e8fee68d2bf919a5
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529540"
---
# <a name="understand-scope-for-azure-rbac"></a>Azure RBAC의 범위 이해

*범위* 는 액세스가 적용되는 리소스의 집합입니다. 역할을 할당할 때 보안 주체에게 필요한 액세스만 부여할 수 있도록 범위를 이해하는 것이 중요합니다. 범위를 제한함으로써 보안 주체가 손상된 경우 리소스를 위험으로부터 제한할 수 있습니다.

## <a name="scope-levels"></a>범위 수준

Azure에서는 [관리 그룹](../governance/management-groups/overview.md), 구독, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups), 리소스 등 4개 수준에서 범위를 지정할 수 있습니다. 범위는 부모-자식 관계로 구조화되어 있습니다. 계층 구조의 각 구조 수준은 범위를 보다 구체적으로 나타냅니다. 이러한 범위 수준에서 역할을 할당할 수 있습니다. 선택한 수준은 역할이 적용되는 범위를 결정합니다. 하위 수준은 상위 수준의 역할 권한을 상속합니다. 

![역할 할당 범위](./media/scope-overview/rbac-scope-no-label.png)

관리 그룹은 구독의 범위 수준이지만 관리 그룹은 더 복잡한 계층을 지원합니다. 다음 다이어그램은 사용자가 정의할 수 있는 관리 그룹 및 구독의 계층 구조 예제를 보여 줍니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹이란?](../governance/management-groups/overview.md)을 참조하세요.

![관리 그룹 및 구독 계층 구조](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>범위 형식

명령줄을 사용하여 역할을 할당하는 경우 범위를 지정해야 합니다. 명령줄 도구의 경우 범위는 역할 할당의 정확한 범위를 식별하는 잠재적으로 긴 문자열입니다. Azure Portal에서 이 범위는 일반적으로 *리소스 ID* 로 나열됩니다.

범위는 슬래시(/) 문자로 구분된 일련의 식별자로 구성됩니다. 이 문자열을 다음 계층 구조의 표현으로 간주할 수 있으며, 여기서 자리 표시자(`{}`) 없는 텍스트는 고정 식별자입니다.

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}`는 사용할 구독의 ID(GUID)입니다.
- `{resourcesGroupName}`은 포함하는 리소스 그룹의 이름입니다.
- `{providerName}`은 리소스를 처리하는 [리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)의 이름이고, `{resourceType}` 및 `{resourceSubType*}`은 리소스 공급자 내의 추가 수준을 식별합니다.
- `{resourceName}`은 특정 리소스를 식별하는 문자열의 마지막 부분입니다.

관리 그룹은 구독 상위 수준이며 가장 광범위한(구체적이지 않음) 범위를 포함합니다. 이 수준의 역할 할당은 관리 그룹 내의 구독에 적용됩니다. 관리 그룹의 범위는 다음과 같은 형식입니다.

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>범위 예제

> [!div class="mx-tableFixed"]
> | 범위 | 예제 |
> | --- | --- |
> | 관리 그룹 | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | 구독 | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | 리소스 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>리소스에 대한 범위를 결정하는 방법

관리 그룹, 구독 또는 리소스 그룹에 대한 범위를 결정하는 것은 매우 간단합니다. 이름 및 구독 ID를 알고 있어야 합니다. 그러나 리소스에 대한 범위를 결정하는 작업은 좀 더 많은 작업을 수행합니다. 리소스의 범위를 결정할 수 있는 몇 가지 방법은 다음과 같습니다.

- Azure Portal에서 리소스를 연 다음, 속성을 확인합니다. 리소스는 범위를 결정할 수 있는 **리소스 ID** 를 나열해야 합니다. 예를 들어 스토리지 계정의 리소스 ID는 다음과 같습니다.

    ![Azure Portal에서 스토리지 계정에 대한 리소스 ID](./media/scope-overview/scope-resource-id.png)

- 또 다른 방법은 Azure Portal을 사용하여 리소스 범위에서 일시적으로 역할을 할당한 다음, [Azure PowerShell](role-assignments-list-powershell.md) 또는 [Azure CLI](role-assignments-list-cli.md)를 사용하여 역할 할당을 나열하는 것입니다. 출력에서 범위는 속성으로 나열됩니다.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="scope-and-arm-templates"></a>범위 및 ARM 템플릿

역할 할당은 ‘확장 리소스’라고 하는 Azure Resource Manager의 특수한 유형입니다. 확장 리소스는 다른 리소스의 기능에 추가되는 리소스입니다. 항상 또 다른 리소스의 확장(자식과 같음)으로서 존재합니다. 예를 들어 구독 범위의 역할 할당은 구독의 확장 리소스입니다. 역할 할당의 이름은 항상 확장 중인 리소스의 이름과 `/Microsoft.Authorization/roleAssignments/{roleAssignmentId}`입니다. ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 역할을 할당하는 경우 일반적으로 범위를 제공할 필요가 없습니다. 그 이유는 범위 필드가 항상 확장 중인 리소스의 ID가 되기 때문입니다. 범위는 역할 할당 자체의 ID로부터 확인할 수 있습니다. 다음 표는 역할 할당 ID 및 해당 범위의 예를 보여 줍니다.

> [!div class="mx-tableFixed"]
> | 역할 할당 ID | 범위 |
> | --- | --- |
> | `/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}` |
> | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg` |

범위 및 ARM 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](role-assignments-template.md)을 참조하세요. 확장 리소스 종류의 전체 목록은 [다른 리소스의 기능을 확장하는 리소스 종류](../azure-resource-manager/management/extension-resource-types.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 단계](role-assignments-steps.md)
- [Azure 서비스의 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 관리 그룹이란?](../governance/management-groups/overview.md)
