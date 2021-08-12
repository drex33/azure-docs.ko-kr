---
title: Azure PowerShell을 사용하여 Azure 역할 할당 나열 - Azure RBAC
description: Azure PowerShell 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 액세스할 수 있는 사용자, 그룹, 서비스 주체 또는 관리 ID를 확인하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c46a8306004fc052cd33cd13d7026934c24e3325
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110691006"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] 이 문서에서는 Azure PowerShell을 사용하여 역할 할당을 나열하는 방법에 관해 설명합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> 조직에서 [Azure Lighthouse](../lighthouse/overview.md)를 사용하는 서비스 공급자에 대해 아웃소싱된 관리 기능을 사용하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Cloud Shell의 PowerShell](../cloud-shell/overview.md) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>현재 구독에 대한 역할 할당 나열

현재 구독에서 모든 역할 할당(루트 및 관리 그룹에서 상속된 역할 할당 포함)의 목록을 가져오는 가장 쉬운 방법은 매개 변수 없이 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용하는 것입니다.

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>구독의 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 확인하거나 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)을 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

지정된 사용자에 할당된 모든 역할을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

지정된 사용자에게 할당된 역할 및 사용자가 속한 그룹에 할당된 역할을 모두 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹 범위에서 모든 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>관리 그룹의 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 확인하거나 [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)을 사용할 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>리소스에 대한 역할 할당 나열

특정 리소스에 대한 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 및 `-Scope` 매개 변수를 사용합니다. 범위는 리소스에 따라 달라집니다. 범위를 가져오려면 매개 변수 없이 `Get-AzRoleAssignment`를 실행하여 모든 역할 할당을 나열한 다음 나열하려는 범위를 찾을 수 있습니다.

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

다음 예제에서는 스토리지 계정에 대한 역할 할당을 나열하는 방법을 보여 줍니다. 이 명령은 리소스 그룹 및 구독과 같이 이 스토리지 계정에 적용되는 더 상위 범위의 역할 할당도 나열합니다.

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

리소스에 직접 할당된 역할 할당만 나열하려는 경우에는 [Where-Object](/powershell/module/microsoft.powershell.core/where-object) 명령을 사용하여 목록을 필터링할 수 있습니다.

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>클래식 서비스 관리자 및 공동 관리자에 대한 역할 할당 나열

클래식 구독 관리자 및 공동 관리자에 대한 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 ID에 대한 역할 할당 나열

1. 시스템 할당 또는 사용자 할당 관리 ID의 개체 ID를 가져옵니다. 

    사용자 할당 관리 ID의 개체 ID를 가져오려면 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)을 사용할 수 있습니다.

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. 역할 할당을 나열하려면 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)를 사용합니다.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 Azure 역할 할당](role-assignments-powershell.md)
