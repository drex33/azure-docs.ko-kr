---
title: Azure AD 역할 기반 액세스 제어에서 사용자 지정 역할 만들기 | Microsoft Docs
description: Azure Active Directory 리소스에서 리소스 범위를 사용하여 사용자 지정 Azure AD 역할을 만들고 할당합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 10/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8425c85f74e5540094be4dab12eb3e3e7c931d1b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057327"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 역할 만들기 및 할당

이 문서에서는 Azure AD(Azure Active Directory)에서 새 사용자 지정 역할을 만드는 방법을 설명합니다. 사용자 지정 역할의 기본 사항은 [사용자 지정 역할 개요](custom-overview.md)를 참조하세요. 역할은 디렉터리 수준 범위 또는 앱 등록 리소스 범위에서만 할당할 수 있습니다.

사용자 지정 역할은 Azure AD 개요 페이지의 [역할 및 관리자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) 탭에서 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph Explorer를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="create-a-role-in-the-azure-portal"></a>Azure Portal에서 역할 만들기

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>앱 등록 관리에 대한 액세스 권한을 부여하는 새 사용자 지정 역할 만들기

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** > **새 사용자 지정 역할** 을 선택합니다.

   ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/custom-create/new-custom-role.png)

1. **기본** 탭에서 역할의 이름과 설명을 입력한 후, **다음** 을 클릭합니다.

   ![기본 사항 탭에서 사용자 지정 역할의 이름 및 설명 제공](./media/custom-create/basics-tab.png)

1. **권한** 탭에서 앱 등록의 기본 속성 및 자격 증명 속성을 관리하는 데 필요한 권한을 선택합니다. 각 권한에 대한 자세한 설명은 [Azure Active Directory의 애플리케이션 등록 하위 유형 및 권한](custom-available-permissions.md)을 참조하세요.
   1. 먼저 검색 창에 "자격 증명"을 입력하고 `microsoft.directory/applications/credentials/update` 권한을 선택합니다.

      ![권한 탭에서 사용자 지정 역할에 대한 권한 선택](./media/custom-create/permissions-tab.png)

   1. 그런 다음, 검색 창에 "기본"을 입력하고 `microsoft.directory/applications/basic/update` 권한을 선택한 후, **다음** 을 클릭합니다.
1. **검토 + 만들기** 탭에서 권한을 검토하고 **만들기** 를 선택합니다.

사용자 지정 역할이 할당할 수 있는 역할 목록에 표시됩니다.

## <a name="create-a-role-using-powershell"></a>PowerShell을 사용하여 역할 만들기

### <a name="connect-to-azure"></a>Azure에 연결

Azure Active Directory에 연결하려면 다음 명령을 사용합니다.

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-powershell"></a>PowerShell을 사용하여 사용자 지정 역할 할당

아래 PowerShell 스크립트를 사용하여 역할을 할당합니다.

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-the-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 역할 만들기

1. 역할 정의를 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    본문

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"`는 요구 사항에 따라 본문에 전송되는 선택적 매개 변수입니다. 공통 매개 변수를 사용하여 서로 다른 사용자 지정 역할을 여러 개 만들어야 하는 경우 템플릿을 만들고 `templateId` 값을 정의하는 것이 가장 좋습니다. PowerShell cmdlet `(New-Guid).Guid`를 사용하여 미리 `templateId` 값을 생성할 수 있습니다. 

1. 역할 할당을 만듭니다.

    사용자 지정 역할 정의를 만들기 위한 HTTP 요청.

    POST

    ```http
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    본문

    ```http
    {
        "principalId":"<GUID OF USER>",
        "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
        "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
    }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>리소스에 범위가 지정된 사용자 지정 역할 할당

기본 제공 역할과 마찬가지로 사용자 지정 역할은 기본적으로 조직 전체의 기본 범위에서 할당되어 조직의 모든 앱 등록에 대한 액세스 권한을 부여합니다. 또한 Azure AD 리소스의 유형에 따라 사용자 지정 역할 및 일부 관련 기본 제공 역할은 단일 Azure AD 리소스의 범위에서 할당될 수도 있습니다. 이를 통해 두 번째 사용자 지정 역할을 만들지 않고도 단일 앱의 자격 증명 및 기본 속성을 업데이트할 수 있는 권한을 사용자에게 부여할 수 있습니다.

1. 애플리케이션 개발자 권한으로 [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **앱 등록** 을 선택합니다.

1. 관리 권한을 부여하는 앱 등록을 선택합니다. Azure AD 조직에서 앱 등록의 전체 목록을 보려면 **모든 애플리케이션** 을 선택해야 할 수도 있습니다.

    ![역할 할당에 대한 리소스 범위로 앱 등록을 선택합니다.](./media/custom-create/appreg-all-apps.png)

1. 앱 등록에서 **역할 및 관리자** 를 선택합니다. 아직 만들지 않은 경우 지침은 [이전 절차](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)에 있습니다.

1. 역할을 선택하여 **할당** 페이지를 엽니다.

1. **할당 추가** 를 선택하여 사용자를 추가합니다. 사용자에게는 선택한 앱 등록에 대해서만 권한이 부여됩니다.

## <a name="next-steps"></a>다음 단계

- 언제든지 [Azure AD 관리 역할 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 경험을 공유하세요.
- 역할 권한에 대한 자세한 내용은 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context)를 참조하세요.
