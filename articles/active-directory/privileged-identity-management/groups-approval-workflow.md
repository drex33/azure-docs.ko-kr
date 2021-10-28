---
title: Privileged Identity Management에서 그룹 구성원 및 소유자에 대한 활성화 요청 승인 - Azure AD
description: Azure AD PIM(Privileged Identity Management)에서 역할 할당 가능 그룹에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c07c654e5a4baf0f817cefa8c48be9c49cebae4
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668222"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>권한 있는 액세스 그룹 구성원 및 소유자에 대한 활성화 요청 승인(미리 보기)

Azure AD(Azure Active Directory)에서 PIM(Privileged Identity Management)을 사용하여 활성화 승인이 필요하도록 권한 있는 액세스 그룹 구성원 및 소유자를 구성하고 Azure AD 조직의 사용자 또는 그룹을 위임된 승인자로 선택할 수 있습니다. 권한 있는 역할 관리자의 워크로드를 줄이기 위해 각 그룹에 대해 둘 이상의 승인자를 선택하는 것이 좋습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. Privileged Identity Management에서 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **요청 승인** 을 선택합니다.

    ![요청 승인 - 검토할 요청이 표시되는 Azure 리소스 페이지](./media/groups-approval-workflow/groups-select-request.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인할 요청을 찾아서 선택한 다음 **승인** 을 선택합니다.

    ![“승인” 및 “확인” 단추가 강조 표시된 “요청 승인” 페이지 스크린샷](./media/groups-approval-workflow/groups-confirm-approval.png)

1. **사유** 상자에 비즈니스 타당성을 입력합니다.

1. **확인** 을 선택합니다. 승인하면 Azure 알림이 생성됩니다.

## <a name="deny-requests"></a>요청 거부

1. 거부할 요청을 찾아서 선택한 다음 **거부** 를 선택합니다.

    ![요청 승인 - 세부 정보 및 사유 상자가 포함된 승인 또는 거부 창](./media/groups-approval-workflow/groups-confirm-denial.png)

1. **사유** 상자에 비즈니스 타당성을 입력합니다.

1. **확인** 을 선택합니다. 거부하면 Azure 알림이 생성됩니다.

## <a name="workflow-notifications"></a>워크플로 알림

워크플로 알림에 대한 일부 정보는 다음과 같습니다.

- 그룹 할당 요청이 검토 보류 중이면 승인자는 메일 알림을 받게 됩니다. 메일 알림에는 승인자가 승인 또는 거부할 수 있는 요청에 대한 직접 링크가 포함되어 있습니다.
- 요청은 승인하거나 거부하는 첫 번째 승인자가 해결합니다.
- 한 승인자가 요청에 응답하면 모든 승인자는 해당 작업에 대한 알림을 받게 됩니다.

>[!Note]
>승인된 사용자가 활성 상태가 아니어야 한다고 생각하는 관리자는 Privileged Identity Management에서 활성 그룹 할당을 제거할 수 있습니다. 승인자가 아닐 경우 리소스 관리자는 보류 중인 요청에 대한 알림을 받지 못하지만 Privileged Identity Management에서 보류 중인 요청을 확인하여 모든 사용자의 보류 중인 요청을 확인하고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 그룹 할당 연장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management의 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 그룹 할당 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
