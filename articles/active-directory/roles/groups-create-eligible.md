---
title: Azure Active Directory에서 역할을 할당할 그룹 만들기 | Microsoft Docs
description: Azure AD에서 역할 할당 가능 그룹을 만드는 방법에 대해 알아봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리합니다.
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
ms.openlocfilehash: c14580790891190f40dd2866aaac25ad2c286137
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536423"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Azure Active Directory에서 역할 할당 가능 그룹 만들기

'isAssignableToRole' 속성을 True로 설정하여 만든 그룹에만 역할을 할당할 수 있으며, **Azure AD 역할이 그룹** 에 설정된 Azure Portal에서만 할당할 수 있습니다. 이 그룹 특성은 Azure Active Directory (Azure AD)에서 역할에 할당될 수 있는 그룹을 만듭니다. 이 문서에서는 이러한 특수한 종류의 그룹을 만드는 방법을 설명합니다. **참고:** isAssignableToRole 속성이 true로 설정된 그룹은 동적 멤버 자격 형식일 수 없습니다. 자세한 내용은 [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P1 또는 P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **그룹** > **모든 그룹** > **새 그룹** 을 선택합니다.

    [![Azure Active Directory에 액세스하여 새 그룹 만들기.](./media/groups-create-eligible/new-group.png "Azure Active Directory를 열고 새 그룹 만들기.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. **새 그룹** 탭에서 그룹 형식, 이름 및 설명을 입력합니다.

1. **Azure AD 역할을 그룹에 할당하기** 를 켭니다. 이 스위치는 스위치를 설정할 수 있는 두 개의 역할만 있으므로 권한 있는 역할 관리자 및 전역 관리자에게만 표시됩니다.

    [![새 그룹을 역할 할당에 적합하도록 설정](./media/groups-create-eligible/eligible-switch.png "새 그룹을 역할 할당에 적합하도록 설정")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. 그룹의 멤버 및 소유자를 선택합니다. 그룹에 역할을 할당하는 옵션도 있지만, 여기에서는 역할을 할당할 필요가 없습니다.

    [![역할 할당 가능 그룹에 멤버를 추가하고 역할을 할당합니다.](./media/groups-create-eligible/specify-members.png "역할 할당 가능 그룹에 멤버를 추가하고 역할을 할당합니다.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. 멤버 및 소유자를 지정한 후 **만들기** 를 선택합니다.

    [![만들기 버튼은 페이지의 맨 아래에 있습니다.](./media/groups-create-eligible/create-button.png "만들기 버튼은 페이지의 맨 아래에 있습니다.")](./media/groups-create-eligible/create-button.png#<lightbox>)

사용자가 할당했을 수 있는 모든 역할로 그룹이 만들어집니다.

## <a name="powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>역할에 할당 가능한 그룹 만들기

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

이 형식의 그룹에 대해는 `isPublic`이 항상 false가 되며 `isSecurityEnabled`는 항상 true입니다.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>한 그룹의 사용자 및 서비스 사용자를 역할 할당 가능 그룹에 복사합니다.

```powershell
#Basic set up
Install-Module -Name AzureAD
Import-Module -Name AzureAD
Get-Module -Name AzureAD

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Azure AD에서 역할 할당 가능 그룹을 만드는 방법에 대해 알아봅니다.

```http
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

이 형식의 그룹에 대해는 `isPublic`이 항상 false가 되며 `isSecurityEnabled`는 항상 true입니다.

## <a name="next-steps"></a>다음 단계

- [그룹에 Azure AD 역할 할당](groups-assign-role.md)
- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
