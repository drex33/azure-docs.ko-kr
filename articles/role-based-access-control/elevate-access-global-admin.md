---
title: 모든 Azure 구독 및 관리 그룹을 관리하는 액세스 권한 상승
description: Azure Portal 또는 REST API를 사용하여 Azure Active Directory에서 모든 구독 및 관리 그룹을 관리하는 글로벌 관리자에 대한 액세스 권한을 상승시키는 방법에 대해 설명합니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/10/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 96e2f7176f85d5571ce73c4efdd592dd3964400d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781526"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>모든 Azure 구독 및 관리 그룹을 관리하는 액세스 권한 상승

Azure AD(Azure Active Directory)의 글로벌 관리자로서 디렉터리에 있는 모든 구독 및 관리 그룹에 대한 액세스 권한을 가질 수 없습니다. 이 문서에서는 모든 구독 및 관리 그룹에 대한 액세스 권한을 상승시킬 수 있는 방법을 설명합니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>액세스 권한을 상승시켜야 하는 이유는 무엇인가요?

전역 관리자인 경우 다음 작업을 수행해야 하는 경우가 있습니다.

- 사용자가 액세스 권한을 상실할 때 Azure 구독 또는 관리 그룹에 대한 액세스 권한 다시 얻기
- 다른 사용자 또는 사용자 자신에게 Azure 구독 또는 관리 그룹에 대한 액세스 권한 부여
- 조직에서 모든 Azure 구독 또는 관리 그룹 확인
- 자동화 앱(예: 송장 또는 감사 앱)이 모든 Azure 구독 또는 관리 그룹에 액세스하도록 허용

## <a name="how-does-elevated-access-work"></a>상승된 액세스 권한은 어떻게 작동하나요?

Azure AD와 Azure 리소스는 서로 독립적으로 보호됩니다. 즉, Azure AD 역할을 할당해도 Azure 리소스에 대한 액세스가 부여되지 않고, Azure 역할을 할당해도 Azure AD에 대한 액세스가 부여되지 않습니다. 그러나 Azure AD의 [글로벌 관리자](../active-directory/roles/permissions-reference.md#global-administrator)는 디렉터리에 있는 모든 Azure 구독 및 관리 그룹에 대한 액세스 권한을 자신에게 할당할 수 있습니다. 가상 머신이나 스토리지 계정 같은 Azure 구독 리소스에 액세스할 수 없고, 글로벌 관리자 권한을 사용하여 이러한 리소스에 대한 액세스 권한을 얻고 싶으면 이 기능을 사용하세요.

액세스 권한을 높이면 Azure의 루트 범위(`/`)에서 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 역할이 할당됩니다.이를 통해 모든 리소스를 살펴보고, 디렉터리에 있는 구독 또는 관리 그룹에 대한 액세스 권한을 할당할 수 있습니다. 사용자 액세스 관리자 역할 할당은 Azure PowerShell, Azure CLI 또는 REST API를 사용하여 제거할 수 있습니다.

루트 범위에서 필요한 변경 작업을 마친 후에는 상승된 액세스 권한을 제거해야 합니다.

![액세스 권한 상승](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>전역 관리자에 대한 액세스 권한 상승

다음 단계에 따라 Azure Portal을 사용하여 전역 관리자에 대한 액세스 권한을 상승시킵니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 글로벌 관리자로 로그인합니다.

    Azure AD Privileged Identity Management를 사용하는 경우 [전역 관리자 역할 할당을 활성화](../active-directory/privileged-identity-management/pim-how-to-activate-role.md)합니다.

1. **Azure Active Directory** 를 엽니다.

1. **관리** 에서 **속성** 을 선택합니다.

   ![Azure Active Directory 속성의 속성 선택 - 스크린샷](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. **Azure 리소스에 대한 액세스 관리** 에서 토글을 **예** 로 설정합니다.

   ![Azure 리소스에 대한 액세스 관리 - 스크린샷](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   토글을 **예** 로 설정하면 루트 범위(/)에서 Azure RBAC의 사용자 액세스 관리자 역할이 할당됩니다. 그러면 이 Azure AD 디렉터리와 연결된 모든 Azure 구독 및 관리 그룹의 역할을 할당할 수 있는 권한이 부여됩니다. 이 토글은 Azure AD에서 글로벌 관리자 역할이 할당된 사용자만 사용할 수 있습니다.

   토글을 **아니요** 로 설정하면 Azure RBAC의 사용자 액세스 관리자 역할이 사용자 계정에서 제거됩니다. 그러면 이 Azure AD 디렉터리와 연결된 모든 Azure 구독 및 관리 그룹의 역할을 더 이상 할당할 수 없습니다. 액세스 권한이 부여된 Azure 구독 및 관리 그룹만 살펴보고 관리할 수 있습니다.

    > [!NOTE]
    > [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)를 사용하는 경우 역할 할당을 비활성화해도 **Azure 리소스에 대한 액세스 관리** 토글이 **아니요** 로 변경되지 않습니다. 최소 권한 있는 액세스를 유지 관리하려면 역할 할당을 비활성화하기 전에 이 토글을 **아니요** 로 설정하는 것이 좋습니다.
    
1. **Save** 를 클릭하여 설정을 저장합니다.

   이 설정은 글로벌 속성이 아니며 현재 로그인된 사용자에게만 적용됩니다. 글로벌 관리자 역할의 모든 멤버에 대한 액세스 권한을 상승시킬 수 없습니다.

1. 로그아웃하고 다시 로그인하여 액세스를 새로 고칩니다.

    이제 이 디렉터리의 모든 구독 및 관리 그룹에 대한 액세스 권한을 갖습니다. 액세스 제어(IAM) 창을 보면 루트 범위에서 사용자 액세스 관리자 역할이 할당된 것을 알 수 있습니다.

   ![루트 범위의 구독 역할 할당 - 스크린샷](./media/elevate-access-global-admin/iam-root.png)

1. 상승된 액세스 권한으로 수행해야 하는 변경을 수행합니다.

    역할 할당에 관한 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)을 참조하세요. Privileged Identity Management를 사용하는 경우 [관리할 Azure 리소스 검색](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) 또는 [Azure 리소스 역할 할당](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)을 참조하세요.

1. 다음 섹션의 단계를 수행하여 상승된 액세스 권한을 제거합니다.

### <a name="remove-elevated-access"></a>상승된 액세스 제거

루트 범위(`/`)에서 사용자 액세스 관리자 역할 할당을 제거하려면 다음 단계를 수행합니다.

1. 액세스 권한을 상승시키는 데 사용된 것과 동일한 사용자로 로그인합니다.

1. 탐색 목록에서 **Azure Active Directory** 를 클릭한 다음, **속성** 을 클릭합니다.

1. **Azure 리소스에 대한 액세스 관리** 토글을 다시 **아니요** 로 설정합니다. 사용자별 설정이므로 액세스 권한을 상승시키는 데 사용했던 동일한 사용자로 로그인해야 합니다.

    액세스 제어(IAM) 창에서 사용자 액세스 관리자 역할 할당을 제거하려고 하면 다음 메시지가 표시됩니다. 역할 할당을 제거하려면 토글을 다시 **아니요** 로 설정하거나 Azure PowerShell, Azure CLI 또는 REST API를 사용해야 합니다.

    ![루트 범위를 사용하여 역할 할당 제거](./media/elevate-access-global-admin/iam-root-remove.png)

1. 전역 관리자로 로그아웃합니다.

    Privileged Identity Management를 사용하는 경우 전역 관리자 역할 할당을 비활성화합니다.

    > [!NOTE]
    > [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)를 사용하는 경우 역할 할당을 비활성화해도 **Azure 리소스에 대한 액세스 관리** 토글이 **아니요** 로 변경되지 않습니다. 최소 권한 있는 액세스를 유지 관리하려면 역할 할당을 비활성화하기 전에 이 토글을 **아니요** 로 설정하는 것이 좋습니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>루트 범위(/)에 있는 역할 할당 나열

루트 범위(`/`)에서 사용자의 사용자 액세스 관리자 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 명령을 사용합니다.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

루트 범위(`/`)에서 자신 또는 다른 사용자의 사용자 액세스 관리자 역할 할당을 제거하려면 다음 단계를 수행합니다.

1. 상승된 액세스 권한을 제거할 수 있는 사용자로 로그인합니다. 이는 액세스 권한을 상승시키는 데 사용했던 동일한 사용자이거나 루트 범위에서 상승된 액세스 권한이 있는 다른 전역 관리자일 수 있습니다.

1. [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 명령을 사용하여 사용자 액세스 관리자 역할 할당을 제거합니다.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>전역 관리자에 대한 액세스 권한 상승

다음 기본 단계에 따라 Azure CLI를 사용하여 전역 관리자의 액세스 권한을 상승시킵니다.

1. [az rest](/cli/azure/reference-index#az_rest) 명령을 사용하여 `elevateAccess` 엔드포인트를 호출하면 루트 범위(`/`)에서 사용자 액세스 관리자 역할이 부여됩니다.

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. 상승된 액세스 권한으로 수행해야 하는 변경을 수행합니다.

    역할 할당에 관한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당](role-assignments-cli.md)을 참조하세요.

1. 이후 섹션의 단계를 수행하여 상승된 액세스 권한을 제거합니다.

### <a name="list-role-assignment-at-root-scope-"></a>루트 범위(/)에 있는 역할 할당 나열

루트 범위(`/`)에서 사용자의 사용자 액세스 관리자 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) 명령을 사용합니다.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

루트 범위(`/`)에서 자신 또는 다른 사용자의 사용자 액세스 관리자 역할 할당을 제거하려면 다음 단계를 수행합니다.

1. 상승된 액세스 권한을 제거할 수 있는 사용자로 로그인합니다. 이는 액세스 권한을 상승시키는 데 사용했던 동일한 사용자이거나 루트 범위에서 상승된 액세스 권한이 있는 다른 전역 관리자일 수 있습니다.

1. [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 명령을 사용하여 사용자 액세스 관리자 역할 할당을 제거합니다.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>전역 관리자에 대한 액세스 권한 상승

다음과 같은 기본 단계를 사용하여 REST API를 사용하는 전역 관리자에 대한 액세스 권한을 상승시킵니다.

1. REST를 사용하여 `elevateAccess`를 호출하면 루트 범위(`/`)에서 사용자 액세스 관리자 역할이 부여됩니다.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 상승된 액세스 권한으로 수행해야 하는 변경을 수행합니다.

    역할 할당에 관한 자세한 내용은 [REST API를 사용하여 Azure 역할 할당](role-assignments-rest.md)을 참조하세요.

1. 이후 섹션의 단계를 수행하여 상승된 액세스 권한을 제거합니다.

### <a name="list-role-assignments-at-root-scope-"></a>루트 범위(/)에 있는 역할 할당 나열

루트 범위(`/`)에서 사용자의 역할 할당을 모두 나열할 수 있습니다.

- `{objectIdOfUser}`가 역할 할당을 검색하려는 사용자의 개체 ID인 경우 [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope)를 호출합니다.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>루트 범위(/)에 있는 거부 할당을 나열합니다.

루트 범위(`/`)에서 사용자의 거부 할당을 모두 나열할 수 있습니다.

- `{objectIdOfUser}`가 거부 할당을 검색하려는 사용자의 개체 ID인 경우 GET denyAssignments를 호출합니다.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

`elevateAccess`를 호출하면 자신의 역할 할당을 만드는 것이므로 해당 권한을 취소하려면 루트 범위(`/`)에서 자신의 사용자 액세스 관리자 역할 할당을 제거해야 합니다.

1. `roleName`이 사용자 액세스 관리자인 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)를 호출하여 사용자 액세스 관리자 역할의 ID 이름을 확인합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` 같은 경우에 `name` 매개 변수의 ID를 저장합니다.

1. 또한 디렉터리 범위에서 디렉터리 관리자의 역할 할당을 나열해야 합니다. 액세스 권한 상승 호출을 수행한 디렉터리 관리자의 `principalId`에 대한 디렉터리 범위에 포함되는 모든 할당을 나열합니다. 그러면 objectid에 대한 디렉터리의 모든 할당이 나열됩니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >디렉터리 관리자는 할당이 많지 않아야 합니다. 이전 쿼리에서 너무 많은 할당을 반환하는 경우 디렉터리 범위 수준에서만 모든 할당을 쿼리한 다음, 결과를 필터링할 수도 있습니다. `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. 이전 호출은 역할 할당 목록을 반환합니다. 범위가 `"/"`이고 `roleDefinitionId`가 1단계에서 찾은 역할 이름 ID로 끝나는 역할 할당을 찾고 `principalId`가 디렉터리 관리자의 objectId와 일치합니다. 
    
    샘플 역할 할당:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    다시, `name` 매개 변수의 ID를 저장합니다(이 경우 11111111-1111-1111-1111-111111111111).

1. 마지막으로 역할 할당 ID를 사용하여 `elevateAccess`에 의해 추가된 할당을 제거합니다.

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="view-elevate-access-logs"></a>액세스 권한 상승 로그 보기

액세스 권한이 상승되면 로그에 항목이 추가됩니다. Azure AD의 전역 관리자는 액세스 권한이 상승된 시기와 누가 액세스했는지 확인할 수 있습니다. 액세스 권한 상승 로그 항목은 표준 활동 로그에 표시되지 않고 대신 디렉터리 활동 로그에 표시됩니다. 이 섹션에서는 액세스 권한 상승 로그를 볼 수 있는 다양한 방법을 설명합니다.

### <a name="view-elevate-access-logs-using-the-azure-portal"></a>Azure Portal 사용하여 액세스 권한 상승 로그 보기

1. 액세스 권한을 높이려면 이 문서의 앞부분에 있는 단계를 따르세요.

1. 글로벌 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모니터**  >  **활동 로그 를** 엽니다.

1. 활동 목록을 디렉터리 **작업** **으로 변경합니다.**

1. 액세스 권한 상승 작업을 나타내는 다음 작업을 검색합니다.

    `Assigns the caller to User Access Administrator role`

    ![모니터의 디렉터리 활동 로그를 보여주는 스크린샷](./media/elevate-access-global-admin/monitor-directory-activity.png)

1. 이 문서의 앞부분에 있는 단계에 따라 상승된 액세스를 제거합니다.

### <a name="view-elevate-access-logs-using-azure-cli"></a>Azure CLI 사용하여 액세스 권한 상승 로그 보기

1. 액세스 권한을 높이려면 이 문서의 앞부분에 있는 단계를 따르세요.

1. [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 전역 관리자로 로그인합니다.

1. [az rest](/cli/azure/reference-index#az_rest) 명령을 사용하여 타임스탬프 예제와 같이 날짜를 기준으로 필터링하고 로그를 저장할 파일 이름을 지정해야 하는 다음 호출을 수행합니다.

    는 `url` API를 호출하여 Microsoft.Insights 로그를 검색합니다. 출력이 파일에 저장됩니다.

    ```azurecli
    az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
    ```

1.  출력 파일에서 를 `elevateAccess` 검색합니다.

    로그는 작업이 발생한 시기와 호출한 사람의 타임스탬프를 볼 수 있는 다음과 유사합니다.

    ```json
      "submissionTimestamp": "2021-08-27T15:42:00.1527942Z",
      "subscriptionId": "",
      "tenantId": "33333333-3333-3333-3333-333333333333"
    },
    {
      "authorization": {
        "action": "Microsoft.Authorization/elevateAccess/action",
        "scope": "/providers/Microsoft.Authorization"
      },
      "caller": "user@example.com",
      "category": {
        "localizedValue": "Administrative",
        "value": "Administrative"
      },
    ```

1. 이 문서의 앞부분에 있는 단계에 따라 상승된 액세스를 제거합니다.

### <a name="delegate-access-to-a-group-to-view-elevate-access-logs-using-azure-cli"></a>Azure CLI 사용하여 액세스 권한 상승 로그를 보려면 그룹에 대한 액세스 위임

액세스 권한 상승 로그를 주기적으로 받으려면 그룹에 대한 액세스를 위임한 다음 Azure CLI 사용할 수 있습니다.

1. **Azure Active Directory**  >  **그룹을 엽니다.**

1. 새 보안 그룹을 만들고 그룹 개체 ID를 확인합니다.

1. 액세스 권한을 높이려면 이 문서의 앞부분에 있는 단계를 따르세요.

1. [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 전역 관리자로 로그인합니다.

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용하여 디렉터리 수준에서만 로그를 읽을 수 있는 그룹에 [읽기](built-in-roles.md#reader) 역할을 할당합니다. 이 그룹은 에 `Microsoft/Insights` 있습니다.

    ```azurecli
    az role assignment create --assignee "{groupId}" --role "Reader" --scope "/providers/Microsoft.Insights"
    ```

1. 이전에 만든 그룹에 로그를 읽을 사용자를 추가합니다.

1. 이 문서의 앞부분에 있는 단계에 따라 상승된 액세스를 제거합니다.

이제 그룹의 사용자가 [az rest](/cli/azure/reference-index#az_rest) 명령을 주기적으로 실행하여 액세스 권한 상승 로그를 볼 수 있습니다.

```azurecli
az rest --url "https://management.azure.com/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2021-09-10T20:00:00Z'" > output.txt
```

## <a name="next-steps"></a>다음 단계

- [여러 역할의 이해](rbac-and-directory-admin-roles.md)
- [REST API를 사용하여 Azure 역할 할당](role-assignments-rest.md)
