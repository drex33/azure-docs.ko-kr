---
title: 그룹에 Azure AD 역할 할당 - Azure Active Directory
description: Azure Portal, PowerShell 또는 Graph API의 역할 할당 가능 그룹에 Azure AD 역할을 할당합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35982d62a0e92b5f4647243cde5920529d6c2989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528135"
---
# <a name="assign-azure-ad-roles-to-groups"></a>그룹에 Azure AD 역할 할당

이 섹션에서는 IT 관리자가 Azure AD 그룹에 Azure AD (Azure Active Directory) 역할을 할당할 수 있는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

Azure AD 역할에 그룹을 할당하는 것은 역할을 할당할 수 있는 그룹만 사용할 수 있다는 점을 제외하고 사용자 및 서비스 사용자를 할당하는 것과 비슷합니다. Azure Portal에서는 역할을 할당할 수 있는 그룹만 표시됩니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** >  **역할 및 관리자** 를 선택하고 할당하려는 역할을 선택합니다.

1. ***role name** _ page에서 > _*Add assignment**를 선택합니다.

   ![새 역할 할당 추가](./media/groups-assign-role/add-assignment.png)

1. 그룹을 선택합니다. Azure AD 역할을 할당할 수 있는 그룹만 표시됩니다.

    [![할당할 수 있는 그룹만 새 역할 할당에 표시됩니다.](./media/groups-assign-role/eligible-groups.png "할당할 수 있는 그룹만 새 역할 할당에 표시됩니다.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. **추가** 를 선택합니다.

역할 권한 할당에 관한 자세한 내용은 [사용자에게 관리자 역할 및 관리자가 아닌 역할 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)을 참조하세요.

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당할 수 있는 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>할당하려는 역할 정의 가져오기

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>역할 할당 만들기

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Azure AD 역할 할당이 가능한 그룹 만들기

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [],
"mailEnabled": false,
"securityEnabled": true,
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true
}
```

### <a name="get-the-role-definition"></a>역할 정의 가져오기

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq 'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>역할 할당 만들기

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>다음 단계

- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
