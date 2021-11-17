---
title: PIM에서 Azure AD 역할에 대한 요청 승인 또는 거부 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할에 대한 요청을 승인하거나 거부하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 80965c0a1ff0ba14586ac99f08833f3a02a3a608
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667234"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 요청 승인 또는 거부

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 활성화에 대한 승인이 필요한 역할을 구성하고 하나 이상의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure AD 역할 요청이 승인 보류 중인 경우 위임된 승인자가 메일 알림을 받게 됩니다. Privileged Identity Management에서 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **요청 승인** 을 선택합니다.

    ![요청 승인 - Azure AD 역할 검토 요청을 표시하는 페이지](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="view-pending-requests-using-graph-api"></a>Graph API를 사용하여 보류 중인 요청 보기

### <a name="http-request"></a>HTTP 요청

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests/filterByCurrentUser(on='approver')?$filter=status eq 'PendingApproval' 
````

### <a name="http-response"></a>HTTP 응답

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleAssignmentScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleAssignmentScheduleRequest", 
            "id": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "status": "PendingApproval", 
            "createdDateTime": "2021-07-15T19:57:17.76Z", 
            "completedDateTime": "2021-07-15T19:57:17.537Z", 
            "approvalId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "customData": null, 
            "action": "SelfActivate", 
            "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
            "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "9f2b5ddb-a50e-44a1-a6f4-f616322262ea", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "d96ea738-3b95-4ae7-9e19-78a083066d5b" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": null, 
                "recurrence": null, 
                "expiration": { 
                    "type": "afterDuration", 
                    "endDateTime": null, 
                    "duration": "PT5H30M" 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        } 
    ] 
} 
````

## <a name="approve-requests"></a>요청 승인

1. 승인하려는 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 표시됩니다.

    ![“요청 승인 - Azure AD 역할” 페이지를 보여 주는 스크린샷](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. **타당성** 상자에 비즈니스 타당성을 입력합니다.

1. **승인** 을 선택합니다. 승인에 대한 Azure 알림을 받게 됩니다.

    ![요청이 승인되었음을 보여 주는 승인 알림](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

## <a name="approve-pending-requests-using-graph-api"></a>Graph API를 사용하여 보류 중인 요청 승인

### <a name="get-ids-for-the-steps-that-require-approval"></a>승인이 필요한 단계에 대한 ID를 가져옵니다.

특정 활성화 요청의 경우 이 명령은 승인이 필요한 모든 승인 단계를 가져옵니다. 다단계 승인은 현재 지원되지 않습니다.

#### <a name="http-request"></a>HTTP 요청

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID> 
````

#### <a name="http-response"></a>HTTP 응답

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals/$entity", 
    "id": "<request-ID-GUID>",
    "steps@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentApprovals('<request-ID-GUID>')/steps", 
    "steps": [ 
        { 
            "id": "<approval-step-ID-GUID>", 
            "displayName": null, 
            "reviewedDateTime": null, 
            "reviewResult": "NotReviewed", 
            "status": "InProgress", 
            "assignedToMe": true, 
            "justification": "", 
            "reviewedBy": null 
        } 
    ] 
} 
````

### <a name="approve-the-activation-request-step"></a>활성화 요청 단계 승인

#### <a name="http-request"></a>HTTP 요청

````HTTP
PATCH 
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentApprovals/<request-ID-GUID>/steps/<approval-step-ID-GUID> 
{ 
    "reviewResult": "Approve", 
    "justification": "abcdefg" 
} 
 ````

#### <a name="http-response"></a>HTTP 응답

PATCH 호출이 성공적이면 빈 응답이 생성됩니다.

## <a name="deny-requests"></a>요청 거부

1. 거부하려는 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 표시됩니다.

    ![요청 승인 - 세부 정보 및 타당성 상자가 있는 승인 또는 거부 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **타당성** 상자에 비즈니스 타당성을 입력합니다.

1. **거부** 를 선택합니다. 거부가 포함된 알림이 표시됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 역할에 대한 요청이 해당 검토를 보류하는 경우 승인자는 메일을 통해 알림을 받습니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인하거나 거부하는 첫 번째 승인자가 해결합니다.
- 승인자가 요청에 응답하면 모든 승인자에게 해당 작업에 대한 알림이 표시됩니다.
- 승인된 사용자가 해당 역할에서 활성화되면 전역 관리자 및 권한 있는 역할 관리자에게 알림이 표시됩니다.

>[!NOTE]
>승인된 사용자가 활성화되지 않아야 한다고 생각하는 전역 관리자 또는 권한 있는 역할 관리자는 Privileged Identity Management에서 활성 역할 할당을 제거할 수 있습니다. 관리자가 승인자 목록의 멤버가 아닌 경우 보류 중인 요청의 알림을 받지 않지만 Privileged Identity Management에서 보류 중인 요청을 검토하여 모든 사용자의 보류 중인 요청을 확인하고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management의 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
