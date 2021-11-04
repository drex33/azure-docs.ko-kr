---
title: 앱 등록 생성에 대한 제한 제거 - Azure AD | Microsoft Docs
description: Azure AD Active Directory에서 무제한 앱 등록을 허용하는 사용자 지정 역할을 할당합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdffe02ad1f0b5bc0b1881781f35a056d9a6a2ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057156"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>빠른 시작: 무제한 앱 등록 만들기에 대한 사용 권한 부여

이 빠른 시작 가이드에서는 갯수 제한 없이 앱 등록을 만들 수 있는 권한이 있는 사용자 지정 역할을 만든 다음, 해당 역할을 사용자에게 할당합니다. 그러면 할당된 사용자는 Azure Portal, Azure AD PowerShell 또는 Microsoft Graph API를 사용하여 애플리케이션 등록을 만들 수 있습니다. 기본 제공 애플리케이션 개발자 역할과 달리 이 사용자 지정 역할은 애플리케이션 등록을 무제한으로 만들 수 있는 기능을 부여합니다. 애플리케이션 개발자 역할은 기능을 부여하지만, [디렉터리 차원의 개체 할당량](../enterprise-users/directory-service-limits-restrictions.md)을 초과하지 않도록 생성된 개체의 총 수는 250개로 제한됩니다. Azure AD 사용자 지정 역할을 만들고 할당하는 데 필요한 최소 권한 역할은 권한 있는 역할 관리자입니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure Portal

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택한 다음, **새 사용자 지정 역할** 을 선택합니다.

    ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/quickstart-app-registration-limits/new-custom-role.png)

1. **기본** 탭에서 역할의 이름에 “애플리케이션 등록 작성자”를 입력하고 역할 설명에 “애플리케이션 등록을 무제한으로 만들 수 있음”을 선택한 후, **다음** 을 선택합니다.

    ![기본 사항 탭에서 사용자 지정 역할의 이름 및 설명 제공](./media/quickstart-app-registration-limits/basics-tab.png)

1. **사용 권한** 탭에서 검색 상자에 “microsoft.directory/applications/create”를 입력한 다음, 원하는 사용 권한 옆의 확인란을 선택한 후 **다음** 을 선택합니다.

    ![권한 탭에서 사용자 지정 역할에 대한 권한 선택](./media/quickstart-app-registration-limits/permissions-tab.png)

1. **검토 + 만들기** 탭에서 권한을 검토하고 **만들기** 를 선택합니다.

### <a name="assign-the-role"></a>역할 할당

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택합니다.

1. 애플리케이션 등록 작성자 역할을 선택하고 **할당 추가** 를 선택합니다.

1. 원하는 사용자를 선택하고 **선택** 을 클릭하여 사용자를 역할에 추가합니다.

완료되었습니다. 이 빠른 시작에서는 갯수 제한 없이 앱 등록을 만들 수 있는 권한이 있는 사용자 지정 역할을 만든 다음, 해당 역할을 사용자에게 할당했습니다.

> [!TIP]
> Azure Portal을 사용하여 애플리케이션에 역할을 할당하려면 할당 페이지의 검색 상자에 애플리케이션의 이름을 입력합니다. 애플리케이션은 기본적으로 목록에 표시되지 않지만 검색 결과에 반환됩니다.

### <a name="app-registration-permissions"></a>앱 등록 권한

애플리케이션 등록을 만들 수 있는 기능을 부여하는 데 사용할 수 있는 두 가지 권한은 각기 다른 동작입니다.

- microsoft.directory/applications/createAsOwner: 이 권한을 할당하면 생성된 앱 등록의 첫 번째 소유자로 작성자가 추가되고 생성된 앱 등록은 작성자의 250개 생성된 개체 할당량에 대해 계산됩니다.
- microsoft.directory/applications/create: 이 권한을 할당하면 생성된 앱 등록의 첫 번째 소유자로 작성자가 추가되지 않으며 생성된 앱 등록이 작성자의 250개 생성된 개체 할당량에 대해 계산되지 않습니다. 디렉터리 수준 할당량에 도달할 때까지는 담당자가 앱 등록 생성을 막을 수 없으므로 이 권한을 신중하게 사용하세요. 두 권한이 모두 할당된 경우 이 권한이 우선적으로 적용됩니다.

## <a name="powershell"></a>PowerShell

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role"></a>역할 할당

다음 PowerShell 스크립트를 사용하여 역할을 할당합니다.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들기 위한 HTTP 요청.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

본문

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role"></a>역할 할당

역할 할당은 보안 주체 ID(사용자 또는 서비스 주체일 수 있음), 역할 정의(역할) ID 및 Azure AD 리소스 범위를 결합합니다.

사용자 지정 역할을 할당하기 위한 HTTP 요청.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

본문

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>다음 단계

- 언제든지 [Azure AD 관리 역할 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 경험을 공유하세요.
- Azure AD 역할에 대한 자세한 내용은 [Azure AD 기본 제공 역할](permissions-reference.md)을 참조하세요.
- 기본 사용자 권한에 대한 자세한 내용은 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
