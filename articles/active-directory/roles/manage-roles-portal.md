---
title: 사용자에게 Azure AD 역할 할당 - Azure Active Directory
description: Azure AD 역할을 할당하여 Azure Active Directory에서 사용자에게 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/15/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 311478f52de547a99354a6effad376145166704b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535583"
---
# <a name="assign-azure-ad-roles-to-users"></a>사용자에게 Azure AD 역할 할당

Azure AD 역할을 할당하여 Azure Active Directory(Azure AD)에서 사용자에게 액세스 권한을 부여합니다. 역할은 사용 권한 컬렉션입니다. 이 문서에서는 Azure portal 및 PowerShell를 사용하여 Azure AD 역할을 할당하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 권한 있는 역할 관리자 또는 전역 관리자
- PIM(Privileged Identity Management)을 사용하는 경우 Azure AD Premium P2 라이선스
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 Azure AD 역할 할당 단계를 따릅니다. [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 사용하도록 설정했는지 여부에 따라 환경이 달라집니다.

### <a name="assign-a-role"></a>역할 할당

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택하여 사용 가능한 역할 목록을 봅니다.

    ![Azure Active Directory의 역할 및 관리자 페이지.](./media/manage-roles-portal/roles-and-administrators.png)

1. 역할을 선택하면 할당을 확인할 수 있습니다.

    필요한 역할을 찾는 데 도움이 되도록 **필터 추가** 를 사용하여 역할을 필터링합니다.

1. **할당 추가** 를 선택한 다음 이 역할에 할당하려는 사용자를 선택합니다.

    다음 그림과 다르게 표시되면, PIM을 사용하도록 설정했을 수 있습니다. 다음 섹션을 참조하세요.

    ![선택한 역할에 대한 할당 창을 추가합니다.](./media/manage-roles-portal/add-assignments.png)

1. **추가** 를 선택하여 역할을 할당합니다.

### <a name="assign-a-role-using-pim"></a>PIM을 사용하여 역할 할당

[Azure AD Privileged Identity Management(PIM)](../privileged-identity-management/pim-configure.md)을 사용하도록 설정한 경우, 추가 역할 할당 기능이 있습니다. 예를 들어, 사용자를 역할에 적격으로 설정하거나 기간을 설정할 수 있습니다. PIM을 사용하는 경우, Azure Portal 사용하여 역할을 할당할 수 있는 두 가지 방법이 있습니다. 역할 및 관리자 페이지 또는 PIM 환경을 사용할 수 있습니다. 어떤 방식으로든 동일한 PIM 서비스를 사용합니다.

[역할 및 관리자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) 페이지를 사용하여 역할을 할당하려면 다음 단계를 수행합니다. [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 페이지를 사용하여 역할을 할당하려면 [Privileged Identity Management에서 Azure AD 역할 할당](../privileged-identity-management/pim-how-to-add-role-to-user.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택하여 사용 가능한 역할 목록을 봅니다.

    ![PIM을 사용하는 경우, Azure Active Directory 역할 및 관리자 페이지입니다.](./media/manage-roles-portal/roles-and-administrators.png)

1. 역할을 선택하여 적격, 활성, 만료된 역할 할당을 확인합니다.

    필요한 역할을 찾는 데 도움이 되도록 **필터 추가** 를 사용하여 역할을 필터링합니다.

1. **할당 추가** 를 선택합니다.

1. **구성원 미선택** 을 선택한 다음 이 역할에 할당하려는 사용자를 선택합니다.

    ![할당 추가 페이지 및 PIM이 활성화된 멤버 선택 창.](./media/manage-roles-portal/add-assignments-pim.png)

1. **다음** 을 선택합니다.

1. **설정** 탭에서 이 역할 할당을 **적격** 또는 **활성** 으로 만들지 여부를 선택합니다.

    적격 역할 할당은 사용자가 역할을 사용하려면 하나 이상의 작업을 수행해야 함을 의미합니다. 활성 역할 할당은 사용자가 역할을 사용하기 위해 어떤 작업도 수행할 필요가 없다는 것을 의미합니다. 이러한 설정의 의미에 대한 자세한 내용은 [PIM 용어](../privileged-identity-management/pim-configure.md#terminology)를 참조하세요.

    ![PIM을 사용하도록 설정된 할당 페이지 및 설정 탭을 추가합니다.](./media/manage-roles-portal/add-assignments-pim-setting.png)

1. 나머지 옵션을 사용하여 할당 기간을 설정합니다.

1. **할당** 을 선택하여 역할을 할당합니다.

## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure AD 역할을 할당하려면 다음 단계를 수행합니다.

### <a name="setup"></a>설정

1. PowerShell 창을 열고 [Import-Module](/powershell/module/microsoft.powershell.core/import-module)을 사용하여 AzureADPreview 모듈을 가져옵니다. 자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. PowerShell 창에서 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)를 사용하여 테넌트에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

1. [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)를 사용하여 역할을 할당할 사용자를 가져옵니다.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'user@contoso.com'"
    ```

### <a name="assign-a-role"></a>역할 할당

1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 할당하려는 역할을 가져옵니다.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment)를 사용하여 역할을 할당합니다.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="assign-a-role-as-eligible-using-pim"></a>PIM을 사용하여 적격 역할 할당

PIM을 사용하는 경우, 사용자를 역할 할당에 적격으로 설정하거나 역할 할당의 시작 및 종료 시간을 정의하는 등의 추가 기능이 있습니다. 이러한 기능은 다른 PowerShell 명령 집합을 사용합니다. PowerShell 및 PIM 사용에 대한 자세한 내용은 [Privileged Identity Management의 Azure AD 역할용 PowerShell](../privileged-identity-management/powershell-for-azure-ad-roles.md)을 참조하세요.


1. [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition)을 사용하여 할당하려는 역할을 가져옵니다.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. [Get-AzureADMSPrivilegedResource](/powershell/module/azuread/get-azureadmsprivilegedresource)를 사용하여 권한 있는 리소스를 가져옵니다. 이 경우는 사용자의 테넌트입니다.

    ```powershell
    $aadTenant = Get-AzureADMSPrivilegedResource -ProviderId aadRoles
    ```

1. [New-Object](/powershell/module/microsoft.powershell.utility/new-object)를 사용하여 역할 할당의 시작 및 종료 시간을 정의하는 새 `AzureADMSPrivilegedSchedule` 개체를 생성합니다.

    ```powershell
    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.EndDateTime = "2021-07-25T20:00:00.000Z"
    ```

1. [Open-AzureADMSPrivilegedRoleAssignmentRequest](/powershell/module/azuread/open-azureadmsprivilegedroleassignmentrequest)를 사용하여 역할을 적격으로 할당합니다.

    ```powershell
    $roleAssignmentEligible = Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId $aadTenant.Id -RoleDefinitionId $roleDefinition.Id -SubjectId $user.objectId -Type 'AdminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "Review billing info"
    ```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 지침에 따라 [Graph 탐색기](https://aka.ms/ge)에서 Microsoft Graph API를 사용하여 역할을 할당합니다.

### <a name="assign-a-role"></a>역할 할당

이 예제에서는 objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d`이 있는 보안 주체에 테넌트 범위에서 청구 관리자 역할(역할 정의 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`)이 할당됩니다. 모든 기본 제공 역할의 변경 불가능한 역할 템플릿 ID 목록을 보려면 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.

1. [Graph 탐색기](https://aka.ms/ge)에 로그인합니다.
2. 드롭다운에서 HTTP 메서드로 **POST** 를 선택합니다. 
3. API 버전을 **베타** 로 선택합니다.
4. [roleAssignments](/graph/api/rbacapplication-post-roleassignments) API를 사용하여 역할을 할당합니다. URL 및 요청 본문에 다음 세부 정보를 추가하고 **쿼리 실행** 을 선택합니다.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json

{ 
    "@odata.type": "#microsoft.graph.unifiedRoleAssignment",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "directoryScopeId": "/"
}
```

### <a name="assign-a-role-using-pim"></a>PIM을 사용하여 역할 할당

이 예제에서는 objectID `f8ca5a85-489a-49a0-b555-0a6d81e56f0d`이 있는 보안 주체에게 180일 동안 청구 관리자(역할 정의 ID `b0f54661-2d74-4c50-afa3-1ec803f12efe`)에 시간 제한 적격 역할이 할당됩니다.

1. [Graph 탐색기](https://aka.ms/ge)에 로그인합니다.
2. 드롭다운에서 HTTP 메서드로 **POST** 를 선택합니다. 
3. API 버전을 **베타** 로 선택합니다.
4. URL 및 요청 본문에 다음 세부 정보를 추가하고 **쿼리 실행** 을 선택합니다.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "AfterDuration",
            "duration": "PT180D"
        }
    }
}

```

다음 예제에서는 보안 주체에게 청구 관리자에게 영구 적격 역할이 할당됩니다.

```HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests

Content-type: application/json

{
    "action": "AdminAssign",
    "justification": "for managing admin tasks",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "scheduleInfo": {
        "startDateTime": "2021-07-15T19:15:08.941Z",
        "expiration": {
            "type": "NoExpiration"
        }
    }
}

```

역할 할당을 활성화하려면, 다음 API를 사용합니다.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests

Content-type: application/json

{
    "action": "SelfActivate",
    "justification": "activating role assignment for admin privileges",
    "roleDefinitionId": "b0f54661-2d74-4c50-afa3-1ec803f12efe",
    "directoryScopeId": "/",
    "principalId": "f8ca5a85-489a-49a0-b555-0a6d81e56f0d"
}

```

## <a name="next-steps"></a>다음 단계

- [Azure AD 역할 할당 나열](view-assignments.md)
- [PowerShell을 사용하여 리소스 범위가 있는 사용자 지정 역할 할당](custom-assign-powershell.md)
- [Azure AD 기본 제공 역할](permissions-reference.md)
