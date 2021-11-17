---
title: PIM에서 Azure AD 디렉터리 역할 할당 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할을 할당하는 방법을 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3ea66bbd5708e5964bb2c258feaa1fc44fcecff1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444196"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 할당

전역 관리자는 Azure AD(Azure Active Directory)를 사용하여 **영구** Azure AD 관리자 역할 할당을 만들 수 있습니다. 이러한 역할은 [Azure Portal](../roles/permissions-reference.md) 또는 [PowerShell 명령](/powershell/module/azuread#directory_roles)을 사용하여 할당할 수 있습니다.

Azure AD PIM(Privileged Identity Management) 서비스를 사용하여 권한 있는 역할 관리자는 영구 디렉터리 역할을 할당할 수도 있습니다. 또한 권한이 있는 역할 관리자는 사용자의 Azure AD 관리자 역할을 **적격** 으로 만들 수 있습니다. 적격인 관리자는 필요할 때 역할을 활성화할 수 있으며 작업을 완료하고 나면 권한이 만료됩니다.

Privileged Identity Management는 기본 제공 및 사용자 지정 Azure AD 역할을 모두 지원합니다. Azure AD 사용자 지정 역할에 대한 자세한 내용은 [Azure Active Directory의 역할 기반 액세스 제어](../roles/custom-overview.md)를 참조하세요.

## <a name="assign-a-role"></a>역할 할당

사용자를 Azure AD 관리자 역할에 대해 적격 사용자로 지정하려면 다음 단계를 따릅니다.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택하여 Azure AD 권한에 대한 역할 목록을 확인합니다.

    !["할당 추가" 작업이 선택된 "역할" 페이지 스크린샷](./media/pim-how-to-add-role-to-user/roles-list.png)

1. **할당 추가** 를 선택하여 **할당 추가** 페이지를 엽니다.

1. **역할 선택** 을 선택하여 **역할 선택** 페이지를 엽니다.

    ![새 할당 창](./media/pim-how-to-add-role-to-user/select-role.png)

1. 할당할 역할을 선택하고 역할에 할당할 구성원을 선택한 후 **다음** 을 선택합니다.

1. **멤버 자격 설정** 창의 **할당 유형** 목록에서 **적격** 또는 **활성** 을 선택합니다.

    - **적격** 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 구성원이 필요합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 특정 할당 기간을 지정하려면 시작 및 종료 날짜와 시간 상자를 추가합니다. 완료되면 **할당** 을 선택하여 새 역할 할당을 만듭니다.

    - **영구** 할당에는 만료 날짜가 없습니다. 역할 권한이 자주 필요한 영구 작업자에 대해 이 옵션을 사용합니다.

    - **시간 범위** 할당은 지정된 기간이 끝날 때 만료됩니다. 프로젝트 종료 날짜와 시간을 알고 있는 경우와 같이 임시 또는 계약 작업자와 함께 이 옵션을 사용합니다.

    ![멤버 자격 설정 - 날짜 및 시간](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 역할이 할당된 후에는 할당 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>제한된 범위의 역할 할당

특정 역할의 경우 부여된 사용 권한의 범위는 단일 관리 단위, 서비스 주체 또는 애플리케이션으로 제한될 수 있습니다. 이 절차는 관리 단위의 범위를 포함하는 역할을 할당하는 경우의 예입니다. 관리 단위를 통해 범위를 지원하는 역할 목록은 [관리 단위에 범위가 지정된 역할 할당](../roles/admin-units-assign-roles.md)을 참조하세요. 이 기능은 현재 Azure AD 조직에 롤아웃되고 있습니다.

1. 권한 있는 역할 관리자 권한으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **역할 및 관리자** 를 선택합니다.

1. **사용자 관리자** 를 선택합니다.

    ![Portal에서 역할을 열 때 할당 추가 명령을 사용할 수 있습니다.](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. **할당 추가** 를 선택합니다.

    ![역할에서 범위를 지원하는 경우 범위를 선택할 수 있습니다.](./media/pim-how-to-add-role-to-user/add-scope.png)

1. **할당 추가** 페이지에서 다음을 수행할 수 있습니다.

   - 역할에 할당할 사용자 또는 그룹 선택
   - 역할 범위(이 경우에는 관리 단위)를 선택합니다.
   - 범위에 대한 관리 단위를 선택

관리 단위를 만드는 방법에 대한 자세한 내용은 [관리 단위 추가 및 제거](../roles/admin-units-manage.md)를 참조하세요.

## <a name="assign-a-role-using-graph-api"></a>Graph API를 사용하여 역할 할당

PIM API를 사용하는 데 필요한 권한은 [Privileged Identity Management API 이해](pim-apis.md)를 참조하세요. 

### <a name="eligible-with-no-end-date"></a>종료 날짜 없음

다음은 종료 날짜가 없는 적격 할당을 만들기 위한 샘플 HTTP 요청입니다. C# 및 JavaScript와 같은 샘플을 비롯한 API 명령에 대한 자세한 내용은 [unifiedRoleEligibilityScheduleRequest 만들기](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true)를 참조하세요.

#### <a name="http-request"></a>HTTP 요청

````HTTP
POST https://graph.microsoft.com/beta/rolemanagement/directory/roleEligibilityScheduleRequests 

    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "NoExpiration"        } 
    } 
{ 
} 
````

#### <a name="http-response"></a>HTTP 응답

다음은 응답의 예제입니다. 여기에 표시된 응답 개체는 가독성을 높이기 위해 줄어들 수 있습니다.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:47:41.0939004Z", 
    "completedDateTime": "2021-07-15T19:47:42.4376681Z", 
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
        "startDateTime": "2021-07-15T19:47:42.4376681Z", 
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
}   
````

### <a name="active-and-time-bound"></a>활성 및 시간 바인딩

다음은 시간 범위 내 활성 할당을 만들기 위한 샘플 HTTP 요청입니다. C# 및 JavaScript와 같은 샘플을 비롯한 API 명령에 대한 자세한 내용은 [unifiedRoleEligibilityScheduleRequest 만들기](/graph/api/unifiedroleeligibilityschedulerequest-post-unifiedroleeligibilityschedulerequests?view=graph-rest-beta&tabs=http&preserve-view=true)를 참조하세요.

#### <a name="http-request"></a>HTTP 요청

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignmentScheduleRequests 

{ 
    "action": "AdminAssign", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "<principal-ID-GUID>", 
    "roleDefinitionId": "<definition-ID-GUID>", 
    "scheduleInfo": { 
        "startDateTime": "2021-07-15T19:15:08.941Z", 
        "expiration": { 
            "type": "AfterDuration", 
            "duration": "PT3H" 
        } 
    } 
} 
````

#### <a name="http-response"></a>HTTP 응답

다음은 응답의 예제입니다. 여기에 표시된 응답 개체는 가독성을 높이기 위해 줄어들 수 있습니다.

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleAssignmentScheduleRequests/$entity", 
    "id": "<schedule-ID-GUID>", 
    "status": "Provisioned", 
    "createdDateTime": "2021-07-15T19:15:09.7093491Z", 
    "completedDateTime": "2021-07-15T19:15:11.4437343Z", 
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
        "startDateTime": "2021-07-15T19:15:11.4437343Z", 
        "recurrence": null, 
        "expiration": { 
            "type": "afterDuration", 
            "endDateTime": null, 
            "duration": "PT3H" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다. Azure AD P2 사용이 허가된 고객만 해당: Azure AD 및 PIM(Privileged Identity Management)을 통해 그룹을 활성으로 역할에 할당하지 않습니다. 자세한 설명은 [알려진 문제](../roles/groups-concept.md#known-issues)를 참조하세요.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택하여 Azure AD에 대한 역할 목록을 확인합니다.

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. **업데이트** 또는 **제거** 를 선택하여 역할 할당을 업데이트하거나 제거합니다.

## <a name="remove-eligible-assignment-via-api"></a>API를 통해 적격 할당 제거

### <a name="request"></a>요청

````HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleEligibilityScheduleRequests 

 

{ 
    "action": "AdminRemove", 
    "justification": "abcde", 
    "directoryScopeId": "/", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b" 
} 
````

### <a name="response"></a>응답

````HTTP
{ 
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#roleManagement/directory/roleEligibilityScheduleRequests/$entity", 
    "id": "fc7bb2ca-b505-4ca7-ad2a-576d152633de", 
    "status": "Revoked", 
    "createdDateTime": "2021-07-15T20:23:23.85453Z", 
    "completedDateTime": null, 
    "approvalId": null, 
    "customData": null, 
    "action": "AdminRemove", 
    "principalId": "d96ea738-3b95-4ae7-9e19-78a083066d5b", 
    "roleDefinitionId": "88d8e3e3-8f55-4a1e-953a-9b9898b8876b", 
    "directoryScopeId": "/", 
    "appScopeId": null, 
    "isValidationOnly": false, 
    "targetScheduleId": null, 
    "justification": "test", 
    "scheduleInfo": null, 
    "createdBy": { 
        "application": null, 
        "device": null, 
        "user": { 
            "displayName": null, 
            "id": "5d851eeb-b593-4d43-a78d-c8bd2f5144d2" 
        } 
    }, 
    "ticketInfo": { 
        "ticketNumber": null, 
        "ticketSystem": null 
    } 
} 
````

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
