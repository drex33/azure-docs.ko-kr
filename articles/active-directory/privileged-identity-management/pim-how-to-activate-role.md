---
title: PIM에서 Azure AD 디렉터리 역할 활성화 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae2bc007d6172e6f7ef2bfc7e22af89acf2c8afb
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669438"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>PIM에서 내 Azure AD 역할 활성화

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Microsoft 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 *적격* 이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 역할 할당을 *활성화* 해야 합니다. 예를 들어 경우에 따라 Microsoft 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 Privileged Identity Management에서 해당 Azure AD 역할을 활성화해야 하는 관리자를 위해 작성되었습니다.

## <a name="activate-a-role"></a>역할 활성화

Azure AD 역할을 가정해야 하는 경우 Privileged Identity Management에서 **내 역할** 을 열어 활성화를 요청할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다. 대시보드에 Privileged Identity Management 타일을 추가하는 방법에 대한 내용은 [Privileged Identity Management 사용 시작](pim-getting-started.md)을 참조하세요.

1. **내 역할** 을 선택하고 **Azure AD 역할** 을 선택하여 적합한 Azure AD 역할 목록을 표시합니다.

    ![활성화할 수 있는 역할을 보여 주는 내 역할 페이지](./media/pim-how-to-activate-role/my-roles.png)

1. **Azure AD 역할** 목록에서 활성화하려는 역할을 찾습니다.

    ![Azure AD 역할 - 내 적격 역할 목록](./media/pim-how-to-activate-role/activate-link.png)

1. **활성화** 를 선택하여 활성화 창을 엽니다.

    ![Azure AD 역할 - 활성화 페이지에 기간 및 범위가 포함됨](./media/pim-how-to-activate-role/activate-page.png)

1. **추가 확인 필요** 를 선택하고 지침에 따라 추가 보안 확인을 제공합니다. 세션당 한 번만 인증해야 합니다.

    ![PIN 코드와 같은 보안 확인을 제공하는 화면](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Multi-Factor Authentication 후에 **계속하기 전에 활성화** 를 선택합니다.

    ![역할을 활성화하기 전에 MFA를 사용하여 ID 확인](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. 축소된 범위를 지정하려는 경우 **범위** 를 선택하여 필터 창을 엽니다. 필터 창에서 액세스해야 하는 Azure AD 리소스를 지정할 수 있습니다. 필요한 최소 리소스에 대해서만 액세스를 요청하는 것이 좋습니다.

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. 선택한 시간 후에 Azure AD 역할이 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

1. **활성화** 를 선택합니다.

    [역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려주는 알림이 표시됩니다.

    ![활성화 요청이 승인 보류 중입니다.](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="activate-a-role-using-graph-api"></a>Graph API를 사용하여 역할 활성화

### <a name="get-all-eligible-roles-that-you-can-activate"></a>활성화할 수 있는 모든 적격 역할 얻기

사용자가 그룹 구성원 자격을 통해 역할 자격을 얻는 경우 이 Graph 요청은 해당 자격을 반환하지 않습니다.

#### <a name="http-request"></a>HTTP 요청

````HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests/filterByCurrentUser(on='principal')  
````

#### <a name="http-response"></a>HTTP 응답

여기서는 공간을 절약하기 위해 하나의 역할에 대한 응답만 표시하지만 실제로 활성화할 수 있는 모든 적격 역할 할당이 나열됩니다.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(unifiedRoleEligibilityScheduleRequest)", 
    "value": [ 
        { 
            "@odata.type": "#microsoft.graph.unifiedRoleEligibilityScheduleRequest", 
            "id": "<request-ID-GUID>", 
            "status": "Provisioned", 
            "createdDateTime": "2021-07-15T19:39:53.33Z", 
            "completedDateTime": "2021-07-15T19:39:53.383Z", 
            "approvalId": null, 
            "customData": null, 
            "action": "AdminAssign", 
            "principalId": "<principal-ID-GUID>", 
            "roleDefinitionId": "<definition-ID-GUID>", 
            "directoryScopeId": "/", 
            "appScopeId": null, 
            "isValidationOnly": false, 
            "targetScheduleId": "<schedule-ID-GUID>", 
            "justification": "test", 
            "createdBy": { 
                "application": null, 
                "device": null, 
                "user": { 
                    "displayName": null, 
                    "id": "<user-ID-GUID>" 
                } 
            }, 
            "scheduleInfo": { 
                "startDateTime": "2021-07-15T19:39:53.3846704Z", 
                "recurrence": null, 
                "expiration": { 
                    "type": "noExpiration", 
                    "endDateTime": null, 
                    "duration": null 
                } 
            }, 
            "ticketInfo": { 
                "ticketNumber": null, 
                "ticketSystem": null 
            } 
        },
} 
````

### <a name="activate-a-role-assignment-with-justification"></a>근거에 따라 역할 할당 활성화

#### <a name="http-request"></a>HTTP 요청

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "SelfActivate", 
    "justification": "adssadasasd", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>" 
} 
````

#### <a name="http-response"></a>HTTP 응답

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "f1ccef03-8750-40e0-b488-5aa2f02e2e55", 
    "status": "PendingApprovalProvisioning", 
    "createdDateTime": "2021-07-15T19:51:07.1870599Z", 
    "completedDateTime": "2021-07-15T19:51:17.3903028Z", 
    "approvalId": "<approval-ID-GUID>", 
    "customData": null, 
    "action": "SelfActivate", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": "<schedule-ID-GUID>", 
    "justification": "test", 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "<user-ID-GUID>" 
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
````

## <a name="view-the-status-of-activation-requests"></a>활성화 요청의 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청** 을 선택하여 Azure AD 역할 및 Azure 리소스 역할 요청 목록을 확인합니다.

    ![내 요청 - 보류 중인 요청을 표시하는 Azure AD 페이지](./media/pim-how-to-activate-role/my-requests-page.png)

1. 오른쪽으로 스크롤하여 **요청 상태** 열을 봅니다.

## <a name="cancel-a-pending-request-for-new-version"></a>새 버전에 대한 보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청** 을 선택합니다.

1. 취소하려는 역할에 대해 **취소** 링크를 선택합니다.

    취소를 선택하면 해당 요청이 취소됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

   ![취소 작업이 강조 표시된 내 요청 목록](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-portal-delay"></a>포털 지연 문제 해결

### <a name="permissions-arent-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

Privileged Identity Management에서 역할을 활성화하는 경우 해당 활성화는 권한 있는 역할을 필요로 하는 모든 포털에 즉시 전파되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 활성화가 지연되면 작업을 수행하려는 포털에서 로그아웃되었다가 다시 로그인됩니다. Azure Portal에서 PIM은 자동으로 로그아웃되었다가 다시 로그인됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 역할에 대한 감사 기록 보기](pim-how-to-use-audit-log.md)
