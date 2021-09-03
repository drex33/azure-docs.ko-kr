---
title: Azure AD에서 Privileged Identity Management를 사용하여 그룹에 역할 할당 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하여 그룹에 Azure AD(Azure Active Directory) 역할을 할당하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b6ee8181f24601a66df7205d44256604834c10
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228638"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Privileged Identity Management를 사용하여 그룹에 역할 할당

이 문서에서는 Azure AD PIM(Privileged Identity Management)을 사용하여 그룹에 Azure AD(Azure Active Directory) 역할을 할당하는 방법에 대해 설명합니다.

> [!NOTE]
> PIM을 사용하여 Azure AD 역할에 그룹을 할당하려면 업데이트된 버전의 Privileged Identity Management를 사용해야 합니다. Azure AD 조직에서 Privileged Identity Management API를 활용하는 경우 이전 버전의 PIM을 사용하고 있을 수 있습니다. 이 경우에는 별칭 pim_preview@microsoft.com에 문의하여 조직을 이동하고 API를 업데이트하세요. [PIM에서 Azure AD 역할 및 기능](../privileged-identity-management/pim-configure.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P2 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell 사용 시 AzureADPreview 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)에 로그인합니다.

1. **Privileged Identity Management** > **Azure AD 역할** > **역할** > **할당 추가** 를 선택합니다.

1. 역할과 그룹을 차례로 선택합니다. 모든 그룹이 아니라 역할 할당(역할 할당 가능한 그룹)에 적합한 그룹만 표시됩니다.

    ![“할당 추가” 페이지에서 “역할 선택” 및 “구성원 선택” 섹션이 강조 표시된 모습을 보여 주는 스크린샷.](./media/groups-pim-eligible/select-member.png)

1. 원하는 구성원 자격 설정을 선택합니다. 활성화가 필요한 역할에 **적격** 을 선택합니다. 기본적으로 사용자는 영구적으로 적격 상태를 유지지만 사용자의 자격에 대한 시작 및 종료 시간을 설정할 수도 있습니다. 완료되면 저장 및 추가를 눌러 역할 할당을 완료합니다.

    ![역할을 할당할 사용자 선택](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="powershell"></a>PowerShell

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>그룹을 역할의 적격 구성원으로 할당

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](../privileged-identity-management/pim-resource-roles-assign-roles.md)