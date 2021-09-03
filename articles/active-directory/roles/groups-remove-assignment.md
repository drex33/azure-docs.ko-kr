---
title: Azure Active Directory에서 그룹의 역할 할당 제거
description: Azure Portal, PowerShell 또는 Microsoft Graph API를 사용하여 Azure Active Directory의 그룹에서 역할 할당을 제거합니다.
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
ms.openlocfilehash: add87a653c977c1378feeaa6d204a7bce0184431
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566550"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹의 역할 할당 제거

이 문서에서는 IT 관리자가 그룹에 할당된 Azure AD 역할을 제거하는 방법을 설명합니다. Azure Portal에서 사용자에 대한 직접 및 간접 역할 할당을 모두 제거할 수 있습니다. 사용자에게 그룹 구성원 자격에 의해 역할이 할당된 경우 그룹에서 사용자를 제거하여 역할 할당을 제거합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** > *역할 이름* 을 선택합니다.

1. 역할 할당을 제거할 그룹을 선택하고 **할당 제거** 를 선택합니다.

   ![선택한 그룹에서 역할 할당을 제거합니다.](./media/groups-remove-assignment/remove-assignment.png)

1. 제거를 확인하는 메시지가 표시되면 **예** 를 선택합니다.

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당 가능한 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>그룹을 할당하려는 역할 정의 가져오기

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>역할 할당 만들기

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>역할 할당 제거

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Azure AD 역할 할당이 가능한 그룹 만들기

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>역할 정의 가져오기

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>역할 할당 만들기

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>역할 할당 삭제

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
