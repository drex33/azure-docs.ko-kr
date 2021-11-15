---
title: Privileged Identity Management에서 만료된 그룹 소유자 또는 구성원 할당 갱신 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 역할 할당이 가능한 그룹 할당을 연장 또는 갱신하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.reviewer: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 791da880607de6e2e3c7ad9a9e94e41c50e5a4be
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667671"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 할당 연장 또는 갱신(미리 보기)

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)은 권한 있는 액세스 그룹의 액세스 및 할당 수명 주기를 관리하는 컨트롤을 제공합니다. 관리자는 시작 및 종료 날짜-시간 속성을 사용하여 역할을 할당할 수 있습니다. 할당 종료에 도달하면 Privileged Identity Management에서 영향을 받는 사용자 또는 그룹에 메일 알림을 보냅니다. 또한 리소스 관리자에게도 적절한 액세스가 유지되도록 메일 알림을 보냅니다. 액세스가 연장되지 않더라도 할당이 갱신되어 만료된 상태에서 최대 30일 동안 표시됩니다.

## <a name="who-can-extend-and-renew"></a>연장 및 갱신할 수 있는 사람

리소스 관리자만 권한 있는 액세스 그룹 할당을 연장하거나 갱신할 수 있습니다. 영향을 받는 사용자 또는 그룹은 만료될 예정인 할당을 연장하도록 요청할 수 있고 이미 만료된 할당을 갱신하도록 요청할 수 있습니다.

## <a name="when-notifications-are-sent"></a>알림을 보내는 경우

Privileged Identity Management는 만료될 예정인 권한 있는 액세스 그룹 할당의 영향을 받는 사용자 및 관리자에게 메일 알림을 보냅니다.

- 만료 전 14일 이내
- 만료 전 1일
- 할당이 만료되는 경우

만료될 예정이거나 만료된 할당을 연장하거나 갱신하도록 사용자 또는 그룹이 요청하면 관리자가 알림을 받게 됩니다. 관리자가 요청을 해결하면 모든 관리자 및 요청한 사용자에게 승인 또는 거부 알림이 제공됩니다.

## <a name="extend-group-assignments"></a>그룹 할당 연장

다음 단계에서는 그룹 할당 연장 또는 갱신을 요청하고, 해결하고, 관리하는 프로세스를 간략하게 설명합니다.

### <a name="self-extend-expiring-assignments"></a>만료될 예정인 할당 자체 연장

권한 있는 액세스 그룹에 할당된 사용자는 해당 그룹에 대한 **할당** 페이지의 **적격** 또는 **활성** 탭에서 만료될 예정인 그룹 할당을 직접 연장할 수 있습니다. 사용자 또는 그룹은 앞으로 14일 안에 만료될 적격 할당 및 활성 할당을 연장하도록 요청할 수 있습니다.

![작업 열이 있는 적격 할당이 표시된 내 역할 페이지](media/groups-renew-extend/self-extend-group-assignment.png)

할당 종료 날짜-시간이 14일 이내이면 **연장** 명령을 사용할 수 있습니다. 그룹 할당 연장을 요청하려면 **연장** 을 선택하여 요청 양식을 엽니다.

![이유 상자 및 세부 정보가 포함된 그룹 할당 연장 창](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>연장이 필요한 이유와 부여되어야 하는 연장 기간(이 정보가 있는 경우)에 대한 세부 정보를 포함시키는 것이 좋습니다.

잠시 후 관리자는 연장 요청을 검토하도록 요청하는 메일 알림을 받게 됩니다. 연장 요청이 이미 제출된 경우 Azure 알림이 포털에 표시됩니다.

요청 상태를 보거나 취소하려면 그룹 할당에 대한 **보류 중인 요청** 페이지를 엽니다.

![취소 링크가 표시된 권한 있는 액세스 그룹 할당 - 보류 중인 요청 페이지](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>관리자 승인된 연장

사용자 또는 그룹이 그룹 할당 연장 요청을 제출하면 관리자는 원래 할당에 대한 세부 정보와 요청 이유가 포함된 메일 알림을 받게 됩니다. 알림에는 관리자가 요청을 승인하거나 거부할 수 있는 직접 링크가 포함됩니다.

관리자는 메일의 링크를 사용하는 것 외에도 Privileged Identity Management 관리 포털로 이동한 후 왼쪽 창에서 **요청 승인** 을 선택하여 요청을 승인하거나 거부할 수 있습니다.

![요청 및 승인/거부 링크가 표시된 권한 있는 액세스 그룹 할당 - 요청 승인 페이지](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

관리자가 **승인** 또는 **거부** 를 선택하면 해당 요청 세부 정보와 감사 로그용 비즈니스 타당성을 제공하는 필드가 표시됩니다.

![요청자 이유, 할당 유형, 시작 시간, 종료 시간, 이유가 포함된 그룹 할당 요청 승인](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

그룹 할당 연장 요청을 승인하는 경우 리소스 관리자는 새로운 시작 날짜, 종료 날짜, 할당 유형을 선택할 수 있습니다. 관리자가 특정 작업을 완료하도록 제한된 액세스를 제공하려는 경우 할당 유형 변경이 필요할 수 있습니다(예: 하루). 이 예제에서는 관리자가 **적격** 에서 **활성** 으로 할당을 변경할 수 있습니다. 즉, 활성화를 요구하지 않고도 요청자에게 액세스 권한을 제공할 수 있습니다.

### <a name="admin-initiated-extension"></a>관리자가 시작한 연장

그룹에 할당된 사용자가 그룹 할당 연장을 요청하지 않는 경우 관리자는 사용자를 대신하여 할당을 연장할 수 있습니다. 그룹 할당을 관리자가 연장하는 경우 승인이 필요하지 않으나 할당이 연장되면 다른 모든 관리자에게 알림이 전송됩니다.

그룹 할당을 연장하려면 Privileged Identity Management에서 할당 보기로 이동합니다. 연장해야 하는 할당을 찾습니다. 그런 다음 작업 열에서 **연장** 을 선택합니다.

![연장 링크가 있는 적격 그룹 할당이 표시된 할당 페이지](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>그룹 할당 갱신

만료된 그룹 할당을 갱신하는 프로세스는 연장 요청 프로세스와 개념적으로는 유사하지만 서로 다릅니다. 관리자는 필요한 경우 다음 단계를 수행하여 만료된 할당에 대한 액세스를 갱신할 수 있습니다.

### <a name="self-renew"></a>자체 갱신

리소스에 더 이상 액세스할 수 없는 사용자는 최대 30일까지 만료된 할당 기록에 액세스할 수 있습니다. 할당 기록에 액세스하려면 왼쪽 창에서 **내 역할** 로 이동하여 **만료된 할당** 탭을 선택합니다.

![내 역할 페이지 - 만료된 할당 탭](media/groups-renew-extend/groups-renew-from-my-roles.png)

표시되는 할당 목록은 기본적으로 **적격 할당** 으로 설정되어 있습니다. 드롭다운 메뉴를 사용하여 적격 할당 및 활성 할당 간에 전환합니다.

목록에 있는 그룹 할당 중 하나에 대한 갱신을 요청하려면 **갱신** 작업을 선택합니다. 그런 다음 요청 이유를 제공합니다. 리소스 관리자가 승인이나 거부를 결정하는 데 도움이 되도록 추가 컨텍스트 또는 비즈니스 타당성뿐만 아니라 기간을 제공하는 것이 좋습니다.

![이유 상자가 표시된 그룹 할당 갱신 창](media/groups-renew-extend/groups-renew-request-form.png)

요청이 제출되면 리소스 관리자에게 보류 중인 그룹 할당 갱신 요청에 대한 알림이 제공됩니다.

### <a name="admin-approves"></a>관리자 승인

리소스 관리자는 메일 알림의 링크를 사용하거나 Azure Portal에서 Privileged Identity Management에 액세스한 후 왼쪽 창에서 **요청 승인** 을 선택하여 갱신 요청에 액세스할 수 있습니다.

관리자가 **승인** 또는 **거부** 를 선택하면 해당 요청 세부 정보와 감사 로그용 비즈니스 타당성을 제공하는 필드가 표시됩니다.

그룹 할당 갱신 요청을 승인하는 경우 리소스 관리자는 새로운 시작 날짜, 종료 날짜, 할당 유형을 입력해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 할당 요청 승인 또는 거부](groups-approval-workflow.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 설정 구성](groups-role-settings.md)
