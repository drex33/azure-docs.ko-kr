---
title: Azure Active Directory에서 엔터프라이즈 앱을 관리하는 사용자 지정 역할 만들기
description: Azure Active Directory에서 엔터프라이즈 앱 액세스에 대한 사용자 지정 Azure AD 역할 만들기 및 할당
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf49cfd4d1e0e9b3e65354c9f6c89dd7efd71970
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528859"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱을 관리하는 사용자 지정 역할 만들기

이 문서에서는 Azure AD(Azure Active Directory)에서 사용자 및 그룹에 대한 엔터프라이즈 앱 할당을 관리할 수 있는 권한이 있는 사용자 지정 역할을 만드는 방법을 설명합니다. 역할 할당의 요소와 하위 형식, 권한 및 속성 집합과 같은 용어의 의미에 대해서는 [사용자 지정 역할 개요](custom-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="enterprise-app-role-permissions"></a>엔터프라이즈 앱 역할 권한

이 문서에서는 두 가지 엔터프라이즈 앱 권한에 대해 설명합니다. 모든 예에서는 업데이트 권한을 사용합니다.

* 범위에서 사용자 및 그룹 할당을 읽으려면 `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` 권한을 부여합니다.
* 범위에서 사용자 및 그룹 할당을 관리하려면 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` 권한을 부여합니다.

업데이트 권한을 부여하면 담당자가 엔터프라이즈 앱에 대한 사용자 및 그룹의 할당을 관리할 수 있습니다. 사용자 및/또는 그룹 할당의 범위를 부여하는 것은 단일 애플리케이션 또는 모든 애플리케이션에 대해 가능합니다. 조직 전체 수준에서 부여되는 경우 담당자는 모든 애플리케이션에 대한 할당을 관리할 수 있습니다. 애플리케이션 수준에서 부여되는 경우 담당자는 지정된 애플리케이션에 대한 할당을 관리할 수 있습니다.

업데이트 권한을 부여하는 작업은 다음과 같이 두 단계로 수행됩니다.

1. 권한이 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`인 사용자 지정 역할을 만듭니다.
1. 사용자 또는 그룹에게 엔터프라이즈 앱에 대한 사용자 및 그룹 할당을 관리할 수 있는 권한을 부여합니다. 이는 범위를 조직 전체 수준 또는 단일 애플리케이션으로 설정할 수 있는 경우입니다.

## <a name="azure-portal"></a>Azure portal

### <a name="create-a-new-custom-role"></a>새 사용자 지정 역할 만들기

>[!NOTE]
> 사용자 지정 역할은 조직 전체 수준에서 만들어지고 관리되며 조직의 개요 페이지에서만 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택한 다음, **새 사용자 지정 역할** 을 선택합니다.

    ![Azure AD의 역할 목록에서 새 사용자 지정 역할 추가](./media/custom-enterprise-apps/new-custom-role.png)

1. **기본 사항** 탭에서 역할의 이름에 "사용자 및 그룹 할당 관리"를 제공하고 역할 설명에 "사용자 및 그룹 할당을 관리하는 권한 부여"를 선택한 후 **다음** 을 선택합니다.

    ![사용자 지정 역할에 대한 이름 및 설명 제공](./media/custom-enterprise-apps/role-name-and-description.png)

1. **사용 권한** 탭에서 검색 상자에 "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"를 입력한 다음 원하는 사용 권한 옆의 확인란을 선택한 후 **다음** 을 선택합니다.

    ![사용자 지정 역할에 대한 사용 권한 추가](./media/custom-enterprise-apps/role-custom-permissions.png)

1. **검토 + 생성** 탭에서 권한을 검토하고 **생성** 를 선택합니다.

    ![이제 사용자 지정 역할을 만들 수 있습니다.](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자에게 역할 할당

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택합니다.

1. **사용자 및 그룹 할당을 관리할 수 있는 권한 부여** 역할을 선택합니다.

    ![역할 및 관리자를 열고 사용자 지정 역할 검색](./media/custom-enterprise-apps/select-custom-role.png)

1. **할당 추가** 를 선택하고 원하는 사용자를 선택한 다음 **선택** 을 클릭하여 사용자에게 역할 할당을 추가합니다.

    ![사용자에게 사용자 지정 역할에 대한 할당 추가](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>할당 팁

* 조직 전체에서 모든 엔터프라이즈 앱에 대한 사용자 및 그룹 액세스를 관리하기 위해 담당자에게 권한을 부여하려면 조직에 대한 Azure AD **개요** 페이지의 조직 전체 **역할 및 관리자** 목록에서 시작합니다.
* 특정 엔터프라이즈 앱에 대한 사용자 및 그룹 액세스를 관리하기 위해 담당자에게 권한을 부여하려면 Azure AD에서 해당 앱으로 이동하여 해당 앱에 대한 **역할 및 관리자** 목록에서 엽니다. 새 사용자 지정 역할을 선택하고 사용자 또는 그룹 할당을 완료합니다. 담당자는 특정 앱에 대한 사용자 및 그룹 액세스만 관리할 수 있습니다.
* 사용자 지정 역할 할당을 테스트하려면 담당자로 로그인하고 애플리케이션의 **사용자 및 그룹** 페이지를 열어 **사용자 추가** 옵션이 설정되어 있는지 확인합니다.

    ![사용자 권한 확인](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="powershell"></a>PowerShell

자세한 내용은 [사용자 지정 역할 만들기 및 할당](custom-create.md) 및 [PowerShell을 사용하여 리소스 범위를 통해 사용자 지정 역할 할당](custom-assign-powershell.md)을 참조하세요.

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>사용자 지정 역할 할당

이 PowerShell 스크립트를 사용하여 역할을 할당합니다.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API의 주어진 예를 사용하여 사용자 지정 역할 만들기 자세한 내용은 [사용자 지정 역할 만들기 및 할당](custom-create.md) 및 [Microsoft Graph API를 사용하여 사용자 지정 관리자 역할 할당](custom-assign-graph.md)을 참조하세요.

사용자 지정 역할을 만들기 위한 HTTP 요청.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-the-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 사용자 지정 역할 할당

역할 할당은 보안 주체 ID(사용자 또는 서비스 주체일 수 있음), 역할 정의 ID 및 Azure AD 리소스 범위를 결합합니다. 역할 할당의 요소에 대한 자세한 내용은 [사용자 지정 역할 개요](custom-overview.md)를 참조하세요.

사용자 지정 역할을 할당하기 위한 HTTP 요청.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 앱에 사용할 수 있는 사용자 지정 역할 사용 권한 살펴보기](custom-enterprise-app-permissions.md)
