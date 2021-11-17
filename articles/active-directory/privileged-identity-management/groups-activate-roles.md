---
title: PIM에서 권한 있는 액세스 그룹 역할 활성화 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365f480af81289a9f48dd6ce5a1102be7a7ed6a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444158"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 역할 활성화

PIM(Privileged Identity Management)을 사용하여 권한 있는 액세스 그룹의 적격 역할 구성원이 지정된 날짜와 시간에 역할 활성화를 예약하도록 할 수 있습니다. 또한 관리자가 구성한 최대 기간까지 활성화 기간을 선택할 수 있습니다.

이 문서는 Privileged Identity Management에서 권한 있는 액세스 그룹 역할을 활성화하려는 적격 구성원용으로 작성되었습니다.

## <a name="activate-a-role"></a>역할 활성화

권한 있는 액세스 그룹 역할을 담당해야 하는 경우 Privileged Identity Management에서 **내 역할** 탐색 옵션을 사용하여 활성화를 요청할 수 있습니다.

1. 전역 관리자 또는 그룹 소유자 권한을 사용하여 [Azure AD 포털에 로그인](https://aad.portal.azure.com)합니다.

1. [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)를 엽니다.

1. **권한 있는 액세스 그룹(미리 보기)** 을 선택하고 **역할 활성화** 를 선택하여 권한 있는 액세스 그룹에 대한 **내 역할** 페이지를 엽니다.

    ![PIM의 권한 있는 액세스 역할 페이지](./media/groups-activate-roles/groups-select-group.png)

1. **내 역할** 페이지에서 활성화하려는 적격 할당의 행에 있는 **활성화** 를 선택합니다.

    ![적격 역할 할당 행의 활성화 링크](./media/groups-activate-roles/groups-activate-link.png)

1. 다단계 인증이 필요한 역할은 **진행하기 전에 ID 확인** 을 클릭합니다. 세션당 한 번만 인증해야 합니다.

    ![역할을 활성화하기 전에 MFA를 사용하여 ID 확인](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. **ID 확인** 을 선택하고 지침에 따라 추가 보안 확인을 제공합니다.

    ![PIN 코드와 같은 보안 확인을 제공하는 화면](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. 필요한 경우 사용자 지정 활성화 시작 시간을 지정합니다. 멤버 또는 소유자는 선택한 시간 이후에만 활성화됩니다.

1. **이유** 상자에 활성화 요청의 이유를 입력합니다.

    ![기간 및 타당성이 표시된 활성화 페이지](./media/groups-activate-roles/groups-activate-page.png)

1. **활성화** 를 선택합니다.

[역할을 활성화하는 데 승인이 필요](pim-resource-roles-approval-workflow.md)한 경우 브라우저의 오른쪽 위 모서리에 요청이 승인 보류 중임을 알려 주는 Azure 알림이 표시됩니다.

## <a name="view-the-status-of-your-requests"></a>요청 상태 보기

보류 중인 요청의 상태를 보고 활성화할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청** 을 선택하여 Azure AD 역할 및 권한 있는 액세스 그룹 역할 요청 목록을 확인합니다.

1. 필요한 경우 오른쪽으로 스크롤하여 **요청 상태** 열을 확인합니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

승인이 필요한 역할을 활성화할 필요가 없으면 언제든지 보류 중인 요청을 취소할 수 있습니다.

1. Azure AD Privileged Identity Management를 엽니다.

1. **내 요청** 을 선택합니다.

1. 취소하려는 역할에 대해 **취소** 링크를 선택합니다.

    **취소** 를 선택하면 해당 요청이 취소됩니다. 역할을 다시 활성화하려면 활성화 요청을 새로 제출해야 합니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="permissions-are-not-granted-after-activating-a-role"></a>역할을 활성화한 후 권한이 부여되지 않음

Privileged Identity Management에서 역할을 활성화하는 경우 해당 활성화는 권한 있는 역할을 필요로 하는 모든 포털에 즉시 전파되지 않을 수 있습니다. 경우에 따라 변경 내용이 전파되더라도 포털의 웹 캐싱 때문에 변경 내용이 즉시 적용되지 않을 수 있습니다. 활성화가 지연되는 경우 다음 작업을 수행해야 합니다.

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.
1. Privileged Identity Management에서 역할의 멤버로 나열되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 권한 있는 액세스 그룹 역할 연장 또는 갱신](groups-renew-extend.md)
- [Privileged Identity Management에서 권한 있는 액세스 그룹 역할 할당](groups-assign-member-owner.md)
