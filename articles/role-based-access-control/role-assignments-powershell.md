---
title: Azure PowerShell를 사용하여 Azure 역할 할당 - Azure RBAC
description: Azure PowerShell 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID에 대해 Azure 리소스 액세스 권한을 부여하는 방법 알아보기.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: rolyon
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0138a36c976a9c7c273d61fdd19a50ffbe31354
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305950"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]이 문서에서는 Azure PowerShell을 사용하여 역할을 할당하는 방법을 설명합니다.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 위해 필요한 사항:

- [User Access Administrator](built-in-roles.md#user-access-administrator), [Owner](built-in-roles.md#owner) 등의 `Microsoft.Authorization/roleAssignments/write`사용 권한
- [Azure Cloud Shell의 PowerShell](../cloud-shell/overview.md) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)
- PowerShell 명령을 실행하기 위해 사용하는 계정에는 Azure Active Directory Graph `Directory.Read.All` 및 Microsoft Graph `Directory.Read.All` 권한이 있어야 합니다.

## <a name="steps-to-assign-an-azure-role"></a>Azure 역할 할당 단계

역할 할당은 보안 주체, 역할 정의, 범위 등 세 가지 요소로 구성됩니다.

### <a name="step-1-determine-who-needs-access"></a>1단계: 액세스가 필요한 사용자 확인

사용자, 그룹, 서비스 주체 또는 관리 ID에 역할을 할당합니다. 역할을 할당하기 위해 개체의 고유 ID를 지정해야 할 수도 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal, Azure PowerShell을 사용하여 ID를 가져올 수 있습니다.

**사용자**

Azure AD 사용자의 경우 *patlong\@contoso.com* 또는 사용자 개체 ID와 같은 사용자 계정 이름을 가져옵니다. 개체 ID를 가져오기 위해 [AzADUser 가져오기](/powershell/module/az.resources/get-azaduser)를 사용할 수 있습니다.

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**그룹**

Azure AD 그룹의 경우 그룹 개체 ID가 필요합니다. 개체 ID를 가져오기 위해 [AzADGroup 가져오기](/powershell/module/az.resources/get-azadgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**서비스 주체**

Azure AD 서비스 주체(애플리케이션에서 사용하는 ID)의 경우 서비스 주체 개체 ID가 필요합니다. 개체 ID를 가져오기 위해 [AzADServicePrincipal 가져오기](/powershell/module/az.resources/get-azadserviceprincipal)를 사용할 수 있습니다. 서비스 주체의 경우 애플리케이션 ID가 **아니라** 개체 ID를 사용합니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**관리 ID**

시스템 할당 또는 사용자 할당 관리 ID의 경우 개체 ID가 필요합니다. 개체 ID를 가져오기 위해 [AzADServicePrincipal 가져오기](/powershell/module/az.resources/get-azadserviceprincipal)를 사용할 수 있습니다.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>2단계: 적절한 역할 선택

사용 권한은 역할에 함께 그룹화됩니다. [Azure 기본 제공 역할](built-in-roles.md) 목록에서 선택하거나 고유한 사용자 지정 역할을 사용할 수 있습니다. 필요한 최소한의 권한을 사용하여 액세스 권한을 부여하는 것이 가장 좋으므로, 광범위한 역할을 할당하지 마세요.

역할을 나열하고 고유한 역할 ID를 가져오려면 [AzRoleDefinition 가져오기](/powershell/module/az.resources/get-azroledefinition)를 사용하면 됩니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

특정 역할에 대한 세부 정보를 나열하는 방법은 다음과 같습니다.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

자세한 내용은 [Azure 역할 정의 나열하기](role-definitions-list.md#azure-powershell)를 참조하세요.
 
### <a name="step-3-identify-the-needed-scope"></a>3단계: 필요한 범위 확인

Azure는 리소스, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups), 구독, [관리 그룹](../governance/management-groups/overview.md) 등 네 가지 범위 수준을 제공합니다. 필요한 최소한의 권한을 사용하여 액세스 권한을 부여하는 것이 가장 좋으므로, 역할을 광범위하게 할당하지 마세요. 범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

**리소스 범위**

리소스 범위의 경우, 해당 리소스에 대한 리소스 ID가 필요합니다. Azure Portal 리소스의 속성에서 리소스 ID를 확인할 수 있습니다. 리소스 ID 형식은 다음과 같습니다.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**리소스 그룹 범위**

리소스 그룹 범위에는 리소스 그룹의 이름이 필요합니다. Azure Portal의 **리소스 그룹** 페이지에서 이름을 찾거나 [AzResourceGroup 가져오기](/powershell/module/az.resources/get-azresourcegroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzResourceGroup
```

**구독 범위** 

구독 범위에는 구독 ID가 필요합니다. Azure Portal의 **구독** 페이지에서 ID를 찾거나 [AzSubscription](/powershell/module/az.accounts/get-azsubscription)을 사용할 수 있습니다.

```azurepowershell
Get-AzSubscription
```

**관리 그룹 범위** 

관리 그룹 범위에는 관리 그룹 이름이 필요합니다. Azure Portal의 **관리 그룹** 페이지에서 이름을 찾거나 [AzManagementGroup 가져오기](/powershell/module/az.resources/get-azmanagementgroup)를 사용할 수 있습니다.

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>4단계: 역할 할당

역할을 할당하려면 [새로운 AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 명령을 사용합니다. 명령은 범위에 따라 일반적으로 다음 형식 중 하나를 가집니다.

**리소스 범위**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**리소스 그룹 범위**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**구독 범위** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**관리 그룹 범위** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>역할 할당 예제

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>스토리지 계정 리소스 범위에서 모든 Blob 컨테이너에 대해 역할 할당하기

*storage12345* 라는 스토리지 계정에 대한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 주체에게 [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>특정 Blob 컨테이너 리소스 범위에 대해 역할 할당하기

*blob-container-01* 이라는 Blob 컨테이너에 대한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 주체에게 [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>특정 가상 네트워크 리소스 범위에서 그룹에 대해 역할 할당하기

*pharma-sales-project-network* 라는 가상 네트워크에 대한 리소스 범위에서 ID가 aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa인 *Pharma Sales Admins* 그룹에 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대해 역할 할당하기

*pharma-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

또는 `-Scope` 매개 변수를 사용하여 정규화된 리소스 그룹을 지정할 수 있습니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>리소스 그룹 범위에서 고유한 역할 ID를 사용하여 사용자에 대해 역할 할당하기

다음은 역할 이름이 변경될 수 있는 경우의 예입니다.

- 고유한 사용자 지정 역할을 사용하고 있으며 역할 이름을 변경하기로 결정했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용하고 있습니다. 역할이 릴리스되면 역할의 이름이 변경됩니다.

역할의 이름이 변경된 경우에도 역할 ID는 변경되지 않습니다. 스크립트 또는 자동화를 사용하여 역할을 할당하는 경우, 역할 이름 대신 고유한 역할 ID를 사용하는 것이 좋습니다. 역할 이름을 변경할 경우 스크립트가 작동할 가능성이 높습니다.

다음 예제에서는 *pharma-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>리소스 그룹 범위에서 애플리케이션에 대해 역할 할당하기

다음 예제에서는 *pharma-sales* 리소스 그룹 범위에서 서비스 주체 개체 ID 77777777-7777-7777-7777-777777777777의 애플리케이션에 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>구독 범위에서 사용자에 대해 역할 할당하기

구독 범위에서 *annm\@example.com* 사용자에게 [Reader](built-in-roles.md#reader) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대해 역할 할당하기

관리 그룹 범위에서 *alain\@example.com* 사용자에게 [Billing Reader](built-in-roles.md#billing-reader) 역할을 할당합니다.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 Azure 역할 할당 나열하기](role-assignments-list-powershell.md)
- [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 그룹 액세스 권한 부여](tutorial-role-assignments-group-powershell.md)
- [Azure PowerShell을 사용하여 리소스 관리](../azure-resource-manager/management/manage-resources-powershell.md)
