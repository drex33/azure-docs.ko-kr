---
title: PIM에서 Azure AD 역할에 대한 PowerShell -Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 PowerShell cmdlet을 사용하여 Azure AD 역할을 관리합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b25ae76e2af971c32b027245a852c29f088a145
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669229"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 PowerShell

이 문서에는 Azure AD(Azure Active Directory) PowerShell cmdlet을 사용하여 PIM(Privileged Identity Management)에서 Azure AD 역할을 관리하는 방법에 대한 지침이 포함되어 있습니다. 또한 Azure AD PowerShell 모듈을 사용하여 설정하는 방법을 설명합니다.

## <a name="installation-and-setup"></a>설치 및 설정

1. Azure AD Preview 모듈 설치

    ```powershell
    Install-module AzureADPreview
    ```

1. 계속하기 전에 필요한 역할 권한을 갖고 있는지 확인하세요. 역할 할당 또는 역할 설정 업데이트 등의 관리 작업을 수행하려면 전역 관리자 또는 권한 있는 역할 관리자 역할이 있는지 확인하세요. 본인의 할당만 활성화하려는 경우에는 기본 사용자 권한만 있으면 됩니다.

1. Azure AD에 연결합니다.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. **Azure Active Directory** > **속성** > **디렉터리 ID** 로 이동하여 Azure AD 조직의 테넌트 ID를 찾습니다. cmdlet 섹션에서 resourceId를 입력해야 할 때마다 이 ID를 사용합니다.

    ![Azure AD 조직의 속성에서 조직 ID 찾기](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 다음 섹션은 실행하는 데 도움이 되는 간단한 예제입니다. [/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management)에서 다음 cmdlet에 대한 자세한 설명서를 찾을 수 있습니다. 하지만 providerID 매개 변수에서 "azureResources"를 "aadRoles"로 바꿔야 합니다. 또한 Azure AD 조직의 테넌트 ID를 resourceId 매개 변수로 사용해야 합니다.

## <a name="retrieving-role-definitions"></a>역할 정의 검색

다음 cmdlet을 사용하여 Azure AD 조직의 모든 기본 제공 및 사용자 지정 Azure AD 역할을 가져옵니다. 이 단계는 역할 이름과 roleDefinitionId 간의 매핑을 제공하는 중요한 단계입니다. roleDefinitionId는 특정 역할을 참조하기 위해 이러한 cmdlet 전체에서 사용됩니다.

roleDefinitionId는 Azure AD 조직과 관련이 있으며 역할 관리 API에서 반환하는 roleDefinitionId와는 다릅니다.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

결과:

![Azure AD 조직의 모든 역할을 가져옵니다.](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>역할 할당 검색

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 할당을 검색합니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

다음 cmdlet을 사용하여 특정 사용자의 모든 역할 할당을 검색합니다. 이 목록을 Azure AD 포털에서는 "내 역할"이라고 합니다. 여기서 유일한 차이점은 주체 ID의 필터를 추가했다는 것입니다. 이 컨텍스트에서 주체 ID는 사용자 ID 또는 그룹 ID입니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

다음 cmdlet을 사용하여 특정 역할의 모든 역할 할당을 검색합니다. 여기서 roleDefinitionId는 이전 cmdlet에서 반환된 ID입니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

cmdlet의 결과로 아래의 역할 할당 개체 목록이 생성됩니다. 주체 ID는 역할이 할당된 사용자의 사용자 ID입니다. 할당 상태는 활성 또는 적격 중 하나입니다. 사용자가 활성 상태이고 LinkedEligibleRoleAssignmentId 필드에 ID가 있으면 역할이 현재 활성화되었다는 뜻입니다.

결과:

![Azure AD 조직의 모든 역할 할당 검색](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>역할 할당

다음 cmdlet을 사용하여 적격 할당을 만듭니다.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

할당의 시작 및 종료 시간을 정의하는 일정은 다음 예제처럼 만들 수 있는 개체입니다.

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> endDateTime 값이 null로 설정되면 영구적 할당이라는 의미입니다.

## <a name="activate-a-role-assignment"></a>역할 할당 활성화

다음 cmdlet을 사용하여 일반 사용자의 컨텍스트에서 적격 할당을 활성화합니다.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

관리자 권한으로 적격 할당을 활성화해야 하는 경우 `Type` 매개 변수에 대해 `adminAdd`를 지정합니다.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Active' -Schedule $schedule -Reason "Business Justification for the role assignment"
``` 

이 cmdlet은 역할 할당을 만드는 cmdlet과 거의 동일합니다. cmdlet 간의 주요 차이점은 –Type 매개 변수에서 활성화는 "adminAdd"가 아닌 "userAdd"라는 점입니다. 다른 차이점으로 –AssignmentState 매개 변수는 "Eligible"이 아닌 "Active"입니다.

> [!Note]
> PowerShell을 통해 역할을 활성화할 때 두 가지 제한이 있습니다.
> 1. 역할 설정에서 티켓 시스템/티켓 번호가 필요한 경우 이를 매개 변수로 제공할 수 있는 방법이 없습니다. 따라서 Azure Portal의 범위를 벗어나서 역할을 활성화할 수 없습니다. 이 기능은 향후 몇 달 동안 PowerShell에 도입될 예정입니다.
> 1. 역할 활성화에 다단계 인증이 필요한 경우 현재는 사용자가 자신의 역할을 활성화할 때 PowerShell에서 사용자를 확인할 수 있는 방법이 없습니다. 대신 사용자는 Azure AD에 연결할 때 당사의 엔지니어 중 한 명이 작성한 [이 블로그 게시물](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)에 따라 MFA를 트리거해야 합니다. PIM용 애플리케이션을 개발 중인 경우에는 사용자가 "MfaRule" 오류를 받은 후 사용자를 확인하고 사용자를 모듈에 다시 연결하는 방법을 구현할 수 있습니다.

## <a name="retrieving-and-updating-role-settings"></a>역할 설정 검색 및 업데이트

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 설정을 가져옵니다.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

설정에는 네 가지 주요 개체가 있습니다. 이러한 개체 중 세 개만 현재 PIM에서 사용됩니다. UserMemberSettings는 활성화 설정이고, AdminEligibleSettings는 적격 할당에 대한 할당 설정이고, AdminmemberSettings는 활성 할당에 대한 할당 설정입니다.

[![역할 설정을 가져오고 업데이트합니다.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

역할 설정을 업데이트하려면 특정 역할의 기존 설정 개체를 가져와서 변경해야 합니다.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

그런 다음, 아래와 같이 특정 역할의 개체 중 하나에 설정을 적용할 수 있습니다. 여기서 ID는 목록 역할 설정 cmdlet의 결과에서 검색할 수 있는 역할 설정 ID입니다.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>다음 단계

- [Azure AD의 역할 정의](../roles/permissions-reference.md)
