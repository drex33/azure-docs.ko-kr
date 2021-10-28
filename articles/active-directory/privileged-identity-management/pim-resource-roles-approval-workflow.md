---
title: PIM에서 Azure 리소스 역할에 대한 요청 승인 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 요청을 승인하거나 거부하는 방법을 알아봅니다.
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
ms.openlocfilehash: 42940c078424f86ed8e3bba43232edd180c6a2c8
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669381"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대한 요청 승인 또는 거부

Azure AD(Azure Active Directory)에서 PIM(Privileged Identity Management)을 사용하여 활성화에 대한 승인이 필요하도록 역할을 구성하고 Azure AD 조직에서 위임된 승인자로 사용자 또는 그룹을 선택할 수 있습니다. 권한 있는 역할 관리자에 대한 작업을 줄이려면 각 역할에 대해 둘 이상의 승인자를 선택하는 것이 좋습니다. 위임된 승인자는 24시간 동안 요청을 승인할 수 있습니다. 24시간 내에 요청이 승인되지 않으면 적격 사용자는 새 요청을 다시 제출해야 합니다. 24시간 승인 시간 범위는 구성이 불가능합니다.

이 문서의 단계에 따라 Azure 리소스 역할에 대한 요청을 승인하거나 거부합니다.

## <a name="view-pending-requests"></a>보류 중인 요청 보기

Azure 리소스 역할 요청이 승인 보류 중일 때는 위임된 승인자가 이메일 알림을 받게 됩니다. Privileged Identity Management에서 이러한 보류 중인 요청을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **요청 승인** 을 선택합니다.

    ![요청 승인 - 검토할 요청이 표시되는 Azure 리소스 페이지](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **역할 활성화 요청** 섹션에서는 사용자의 승인이 보류 중인 요청 목록이 표시됩니다.

## <a name="approve-requests"></a>요청 승인

1. 승인하려는 요청을 찾아 선택합니다. 승인 또는 거부 페이지가 표시됩니다.

    ![요청 승인 - 세부 정보 및 타당성 상자가 있는 승인 또는 거부 창](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **타당성** 상자에 비즈니스 타당성을 입력합니다.

1. **승인** 을 선택합니다. 승인에 대한 Azure 알림을 받게 됩니다.

    ![요청이 승인되었음을 보여 주는 승인 알림](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

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
- 승인된 사용자가 해당 역할에서 활성화될 때 리소스 관리자에게 알림이 표시됩니다.

>[!Note]
>승인된 멤버를 활성화하지 않아야 한다고 판단하는 경우 리소스 관리자는 Privileged Identity Management의 활성 역할 할당을 제거할 수 있습니다. 리소스 관리자가 승인자가 아니면 보류 중인 요청에 대한 알림이 표시되지 않지만 Privileged Identity Management에서 보류 중인 요청을 확인하고 모든 사용자에 대해 보류 중인 요청을 확인하고 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management의 메일 알림](pim-email-notifications.md)
- [Privileged Identity Management에서 Azure AD 역할에 대한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
