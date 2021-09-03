---
title: 다양한 범위에서 Azure AD 역할 할당 - Azure Active Directory
description: Azure Active Directory의 다양한 범위에서 역할을 할당하는 방법을 알아봅니다.
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c9e92d09ce929b47cd9a71559eabb4e7c40858
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537282"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>다양한 범위에서 Azure AD 역할 할당

이 문서에서는 다양한 범위에서 Azure AD 역할을 할당하는 방법을 설명합니다. Azure AD의 범위 지정을 이해하려면 [Azure AD의 RBAC 개요](custom-overview.md) 문서를 참조하세요. 일반적으로 역할 할당을 제한하려는 범위 내에 있어야 합니다. 예를 들어 [관리 단위](administrative-units.md)에 대해 범위 지정된 기술 지원팀 관리자 역할을 할당하려면 **Azure AD > 관리 단위 > {관리 단위} > 역할 및 관리자** 로 이동한 후 역할 할당을 수행해야 합니다. 그러면 전체 테넌트가 아니라 관리 단위로 범위가 지정된 역할 할당이 만들어집니다.

## <a name="prerequisites"></a>필수 구성 요소

- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의.

자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="assign-roles-scoped-to-the-tenant"></a>테넌트에 범위가 지정된 역할 할당

이 섹션에서는 테넌트 범위에서 역할을 할당하는 방법을 설명합니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택하여 사용 가능한 역할 목록을 봅니다.

    ![Azure Active Directory의 역할 및 관리자 페이지.](./media/manage-roles-portal/roles-and-administrators.png)

1. 역할을 선택하면 할당을 확인할 수 있습니다. 필요한 역할을 찾는 데 도움이 되도록 **필터 추가** 를 사용하여 역할을 필터링합니다.

1. **할당 추가** 를 선택한 다음 이 역할에 할당하려는 사용자를 선택합니다.

    ![선택한 역할에 대한 할당 창을 추가합니다.](./media/manage-roles-portal/add-assignments.png)

1. **추가** 를 선택하여 역할을 할당합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure AD 역할을 할당하려면 다음 단계를 수행합니다.

1. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)를 사용하여 사용자를 가져옵니다.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 할당하려는 역할을 가져옵니다.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. 테넌트를 역할 할당 범위로 설정합니다.

    ```powershell
    $directoryScope = '/'
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment)를 사용하여 역할을 할당합니다.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 지침에 따라 [Graph 탐색기](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 역할을 할당합니다.

1. [Graph 탐색기](https://aka.ms/ge)에 로그인합니다.

1. [List user](/graph/api/user-list) API를 사용하여 사용자를 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API를 사용하여 할당하려는 역할을 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API를 사용하여 역할을 할당합니다.\

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>관리 단위에 범위가 지정된 역할 할당

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory > 관리 단위** 를 선택하면 모든 관리 단위 목록을 볼 수 있습니다.

1. 관리 단위를 선택합니다.

    ![Azure Active Directory의 관리 단위](./media/assign-roles-different-scopes/admin-units.png)

1. 왼쪽 탐색 메뉴에서 **역할 및 관리자** 를 선택하면 관리 단위를 통해 할당할 수 있는 모든 역할 목록을 볼 수 있습니다.

    ![Azure Active Directory의 관리 단위 아래에 있는 역할 및 관리자 메뉴.](./media/assign-roles-different-scopes/admin-units-roles.png)

1. 원하는 역할을 선택합니다.

1. **할당 추가** 를 선택한 다음 이 역할에 할당하려는 사용자 또는 그룹을 선택합니다.

1. **추가** 를 선택하여 모든 관리 단위 범위에 대한 역할을 할당합니다.

>[!Note] 
>Azure AD 기본 제공 또는 사용자 지정 역할의 전체 목록은 여기에 표시되지 않습니다. 예상된 동작입니다. 관리 단위 내에서 지원되는 개체와 관련된 권한이 있는 역할을 보여 줍니다. 관리 단위 내에서 지원되는 개체 목록을 보려면 [이 문서](administrative-units.md)를 참조하세요.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 관리 단위 범위에서 Azure AD 역할을 할당하려면 다음 단계를 수행합니다.

1. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)를 사용하여 사용자를 가져옵니다.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 할당하려는 역할을 가져옵니다.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit)을 사용하여 역할 할당 범위를 지정할 관리 단위를 가져옵니다.

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment)를 사용하여 역할을 할당합니다.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 지침에 따라 [Graph 탐색기](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 관리 단위 범위에서 역할을 할당합니다.

1. [Graph 탐색기](https://aka.ms/ge)에 로그인합니다.

1. [List user](/graph/api/user-list) API를 사용하여 사용자를 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API를 사용하여 할당하려는 역할을 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. [List managementUnits](/graph/api/administrativeunit-list) API를 사용하여 역할 할당 범위를 지정할 관리 단위를 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API를 사용하여 역할을 할당합니다.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>여기에서 directoryScopeId는 */foo* 대신 */administrativeUnits/foo* 로 지정됩니다. 이는 의도된 것입니다. */administrativeUnits/foo* 범위는 보안 주체가 관리 단위 자체가 아니라 자신에게 할당된 역할에 따라 관리 단위의 구성원을 관리할 수 있음을 의미합니다. */foo* 범위는 보안 주체가 해당 Azure AD 개체 자체를 관리할 수 있음을 의미합니다. 다음 섹션에서는 범위가 */foo* 인 것을 볼 수 있습니다. 앱 등록을 통해 범위가 지정된 역할이 개체 자체를 관리할 수 있는 권한을 부여하기 때문입니다.

## <a name="assign-roles-scoped-to-an-app-registration"></a>앱 등록 범위가 지정된 역할 할당

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. 모든 앱 등록 목록을 보려면 **Azure Active Directory > 앱 등록** 을 선택합니다.

1. 애플리케이션을 선택합니다. 검색창을 이용하여 원하는 앱을 찾을 수 있습니다.

    ![Azure Active Directory에서 앱 등록](./media/assign-roles-different-scopes/app-reg.png)

1. 앱 등록을 통해 할당할 수 있는 모든 역할 목록을 보려면 왼쪽 탐색 메뉴에서 **역할 및 관리자** 를 선택합니다.

    ![Azure Active Directory에서 앱 등록을 위한 역할.](./media/assign-roles-different-scopes/app-reg-roles.png)

1. 원하는 역할을 선택합니다.

1. **할당 추가** 를 선택한 다음 이 역할에 할당하려는 사용자 또는 그룹을 선택합니다.

    ![Azure Active Directory의 앱 등록 범위가 지정된 역할 할당을 추가합니다.](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. **추가** 를 선택하여 앱 등록 범위의 역할을 할당합니다. 

    ![Azure Active Directory의 앱 등록 범위에 있는 역할 할당을 성공적으로 추가했습니다.](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![Azure Active Directory의 앱 등록 범위가 지정된 사용자에게 할당된 역할입니다.](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>Azure AD 기본 제공 또는 사용자 지정 역할의 전체 목록은 여기에 표시되지 않습니다. 예상된 동작입니다. 앱 등록 관리와 관련된 권한만 있는 역할을 보여 줍니다. 


### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 애플리케이션 범위에서 Azure AD 역할을 할당하려면 다음 단계를 수행합니다.

1. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)를 사용하여 사용자를 가져옵니다.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 할당하려는 역할을 가져옵니다.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication)을 사용하여 역할 할당 범위를 지정할 앱 등록을 가져옵니다.

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment)를 사용하여 역할을 할당합니다.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 지침에 따라 [Graph 탐색기](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 애플리케이션 범위에서 역할을 할당합니다.

1. [Graph 탐색기](https://aka.ms/ge)에 로그인합니다.

1. [List user](/graph/api/user-list) API를 사용하여 사용자를 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) API를 사용하여 할당하려는 역할을 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. [List applications](/graph/api/application-list) API를 사용하여 역할 할당 범위를 지정할 관리 단위를 가져옵니다.

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) API를 사용하여 역할을 할당합니다.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>여기에서 directoryScopeId는 위의 섹션과 달리 */foo* 로 지정됩니다. 이는 의도된 것입니다. */foo* 범위는 보안 주체가 해당 Azure AD 개체를 관리할 수 있음을 의미합니다. */administrativeUnits/foo* 범위는 보안 주체가 관리 단위 자체가 아니라 자신에게 할당된 역할에 따라 관리 단위의 구성원을 관리할 수 있음을 의미합니다.


## <a name="next-steps"></a>다음 단계

* [Azure AD 역할 할당 나열](view-assignments.md)
* [사용자에게 Azure AD 역할 할당](manage-roles-portal.md)
* [그룹에 Azure AD 역할 할당](groups-assign-role.md)