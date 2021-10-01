---
title: PIM에서 Azure 리소스 역할 및 Azure AD 역할의 액세스 검토 만들기 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스와 Azure AD 역할의 액세스 검토를 만드는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933b66a3a8277763112c0a1838f13fef6dd81544
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452766"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 만들기

직원이 권한 있는 Azure 리소스와 Azure AD 역할에 액세스해야 하는 필요성은 시간이 지남에 따라 변경됩니다. 오래된 역할 할당과 관련된 위험을 줄이려면 주기적으로 액세스를 검토해야 합니다. Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하여 Azure 리소스와 Azure AD 역할에 대한 권한 있는 액세스의 액세스 검토를 만들 수 있습니다. 자동으로 발생하는 정기 액세스 검토를 구성할 수도 있습니다. 이 문서에서는 하나 이상의 액세스 검토를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] PIM의 라이선스에 대한 자세한 내용은 [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

 Azure 리소스의 액세스 검토를 만들려면 Azure 리소스의 [소유자](../../role-based-access-control/built-in-roles.md#owner) 또는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할에 할당되어야 합니다. Azure AD 역할의 액세스 검토를 만들려면 [전역 관리자](../roles/permissions-reference.md#global-administrator) 또는 [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할에 할당되어야 합니다.

## <a name="create-access-reviews"></a>액세스 검토 만들기

1. 필수 조건 역할 중 하나에 할당된 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **ID 거버넌스** 를 선택합니다.
 
3. **Azure AD 역할** 의 경우 **Privileged Identity Management** 에서 **Azure AD 역할** 을 선택합니다. **Azure 리소스** 의 경우 **Privileged Identity Management** 에서 **Azure 리소스** 를 선택합니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Azure Portal 스크린샷에서 ID 거버넌스 선택" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. **Azure AD 역할** 의 경우 **관리** 에서 **Azure AD 역할** 을 다시 선택합니다. **Azure 리소스** 의 경우 관리하려는 리소스(예: 구독)를 선택합니다.


5. 관리에서 **액세스 검토** 를 선택한 다음 **새로 만들기** 를 선택하여 새 액세스 검토를 만듭니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Azure AD 역할 - 모든 검토의 상태를 보여 주는 액세스 검토 목록 스크린샷":::
 
6. 액세스 검토의 이름을 지정합니다. 필요한 경우 검토에 설명을 지정합니다. 이름 및 설명이 검토자에게 표시됩니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="액세스 검토 만들기 - 이름 및 설명 검토 스크린샷":::

7. **시작 날짜** 를 설정합니다. 기본적으로 액세스 검토는 한 번 발생하고, 만들어진 시간에 시작되고, 1개월 후에 종료됩니다. 시작 날짜와 종료 날짜를 변경하여 액세스 검토를 나중에 시작하고 원하는 기간(일 수) 동안 지속할 수 있습니다.

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="시작 날짜, 빈도, 기간, 종료, 횟수, 종료 날짜 스크린샷":::

8. 액세스 검토를 반복하려면 **빈도** 설정을 **한 번** 에서 **매주**, **매월**, **분기마다**, **매년** 또는 **반년마다** 로 변경합니다. **기간** 슬라이더 또는 텍스트 상자를 사용하여 검토자의 입력에 대해 반복 시리즈의 각 검토가 열릴 일수를 정의합니다. 예를 들어 검토가 겹치는 상황을 방지하기 위해 월별 검토에 대해 설정할 수 있는 최대 기간은 27일입니다.

9. **종료** 설정을 사용하여 되풀이 액세스 검토 시리즈를 종료하는 방법을 지정합니다. 이 시리즈는 세 가지 방법으로 종료할 수 있습니다. 무기한으로, 특정 날짜까지, 또는 정의된 되풀이 횟수가 완료된 이후에 검토를 시작하도록 연속적으로 실행됩니다. 사용자 또는 다른 전역 관리자는 해당 날짜에 종료되도록 **설정** 에서 날짜를 변경하여 생성 후 시리즈를 중지할 수 있습니다.


10. **사용자 범위** 섹션에서 검토의 범위를 선택합니다. **Azure AD 역할** 의 첫 번째 범위 옵션은 사용자 및 그룹입니다. 직접 할당된 사용자 및 [역할 할당 가능 그룹](../roles/groups-concept.md)이 선택 항목에 포함됩니다. **Azure 리소스 역할** 의 첫 번째 범위는 사용자입니다. Azure 리소스 역할에 할당된 그룹이 확장되어 이 선택 항목이 있는 검토에 전이적 사용자 할당을 표시합니다. **서비스 주체** 를 선택하여 Azure 리소스 또는 Azure AD 역할에 직접 액세스할 수 있는 머신 계정을 검토할 수도 있습니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="역할 멤버 자격을 검토할 사용자 범위 스크린샷":::

11. **역할 멤버 자격 검토** 에서 검토할 권한 있는 Azure 리소스 또는 Azure AD 역할을 선택합니다.

    > [!NOTE]
    > 둘 이상의 역할을 선택하면 여러 액세스 검토가 생성됩니다. 예를 들어 5개의 역할을 선택하면 별도의 액세스 검토가 5개 생성됩니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="역할 멤버 자격 검토 스크린샷":::

12. **할당 유형** 에서 주체가 역할에 할당된 방식에 따라 검토 범위를 지정합니다. **적격 할당만** 을 선택하여 검토를 만들 때 활성화 상태에 관계없이 적격 할당을 검토하거나 **활성 할당만** 을 선택하여 활성 할당을 검토합니다. 유형에 관계없이 모든 할당을 검토하려면 **모든 활성 및 적격 할당** 을 선택합니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="검토자 할당 유형 목록 스크린샷":::

13. **검토자** 섹션에서 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="선택한 사용자 또는 멤버(자신)의 검토자 목록":::

    - **선택한 사용자** - 검토를 완료할 특정 사용자를 지정하려면 이 옵션을 사용합니다. 이 옵션은 검토 범위에 관계없이 사용할 수 있으며, 선택한 검토자는 사용자, 그룹, 서비스 주체를 검토할 수 있습니다.
    - **멤버(자신)** - 사용자에게 자신의 역할 할당을 검토하게 하려면 이 옵션을 사용합니다. 이 옵션은 검토 범위가 **사용자 및 그룹** 또는 **사용자** 로 지정된 경우에만 사용할 수 있습니다. **Azure AD 역할** 의 경우 이 옵션을 선택하면 역할 할당 가능 그룹이 검토에 포함되지 않습니다. 
    - **관리자** - 사용자의 관리자가 사용자의 역할 할당을 검토하도록 하려면 이 옵션을 사용합니다. 이 옵션은 검토 범위가 **사용자 및 그룹** 또는 **사용자** 로 지정된 경우에만 사용할 수 있습니다. 관리자를 선택하면 대체 검토자도 지정할 수 있습니다. 사용자가 디렉터리에 관리자를 지정하지 않은 경우 대체 검토자에게 사용자를 검토하라는 메시지가 표시됩니다. **Azure AD 역할** 의 경우 대체 검토자가 선택되면 해당 검토자가 역할 할당 가능 그룹을 검토합니다. 

### <a name="upon-completion-settings"></a>완료 시 설정

1. 검토가 완료된 후 수행할 작업을 지정하려면 **완료 시 설정** 섹션을 확장합니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="설정이 완료되면 자동 적용되며 응답하지 않음을 검토해야 함 스크린샷":::

2. 거부된 사용자에 대한 액세스를 자동으로 제거하려면 **결과를 리소스에 자동 적용** 을 **사용** 으로 설정합니다. 검토가 완료될 때 결과를 수동으로 적용하려면 스위치를 **사용 안 함** 으로 설정합니다.

3. **검토자가 응답하지 않는 경우** 목록을 사용하여 검토 기간 내에 검토자가 검토하지 않은 사용자에게 어떤 일이 발생하는지 지정합니다. 이 설정은 검토자가 검토한 사용자에게 영향을 주지 않습니다.

    - **변경 없음** - 사용자의 액세스 권한을 그대로 유지
    - **액세스 권한 제거** - 사용자의 액세스 권한을 제거
    - **액세스 권한 승인** - 사용자의 액세스 권한을 승인
    - **권장 작업 수행** - 사용자의 지속적인 액세스를 거부할 것인지 아니면 승인할 것인지에 대한 시스템의 권장 사항을 수용
    
4. **거부된 게스트 사용자에 적용할 작업** 목록을 사용하여 거부된 게스트 사용자에 대해 수행할 작업을 지정합니다. 이 설정은 현재 Azure AD 및 Azure 리소스 역할 검토를 위해 편집할 수 없습니다. 모든 사용자와 마찬가지로 게스트 사용자는 거부된 경우 리소스에 대한 액세스 권한을 잃습니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="설정 완료 시 - 거부된 게스트 사용자에 적용할 작업 스크린샷":::

5. 추가 사용자 또는 그룹에 알림을 보내 검토 완료 업데이트를 받을 수 있습니다. 이 기능을 사용하면 검토 작성자 이외의 관련자를 검토 진행 상황에서 업데이트할 수 있습니다. 이 기능을 사용하려면 **사용자 또는 그룹 선택** 을 선택하고 완료 상태를 받을 사용자 또는 그룹을 추가합니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="설정 완료 시 - 알림을 받을 사용자 추가 스크린샷":::

### <a name="advanced-settings"></a>고급 설정

1. 추가 설정을 지정하려면 **고급 설정** 섹션을 확장합니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="권장 사항 표시, 승인, 메일 알림 및 미리 알림 시 이유 필요의 고급 설정 스크린샷":::

1. **권장 사항 표시** 를 **사용** 으로 설정하면 사용자의 액세스 정보에 따라 검토자에게 시스템 권장 사항이 표시됩니다.

1. **승인 시 이유 필요** 를 **사용** 으로 설정하면 검토자가 승인 이유를 입력해야 합니다.

1. **메일 알림** 을 **사용** 으로 설정하면 Azure AD는 액세스 검토가 시작될 때 검토자에게 이메일 알림을 보내고 검토가 완료될 때 관리자에게 이메일 알림을 보냅니다.

1. **미리 알림** 을 **사용** 으로 설정하면 Azure AD는 검토를 완료하지 않은 검토자에게 진행 중인 액세스 검토에 대한 미리 알림을 보냅니다.
1. 리뷰어에게 전송되는 이메일의 내용은 리뷰 이름, 리소스 이름, 기한 등과 같은 리뷰 세부 정보를 기반으로 자동 생성됩니다. 추가 지침이나 연락처 정보와 같은 추가 정보를 전달할 방법이 필요한 경우 다음을 수행할 수 있습니다. 할당된 검토자에게 발송되는 초대 및 알림 이메일에 포함될 **검토자 이메일에 대한 추가 콘텐츠** 에 이러한 세부정보를 지정합니다. 아래 강조 표시된 섹션이 이 정보가 표시되는 위치입니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="강조 표시된 검토자에게 보낸 이메일 콘텐츠"::: 

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 액세스 검토의 **개요** 페이지에서 검토를 완료하는 진행 상황을 추적할 수 있습니다. 액세스 권한은 검토가 완료될 때까지 디렉터리에서 변경되지 않습니다. 다음은 **Azure 리소스** 및 **Azure AD 역할** 액세스 검토의 개요 페이지를 보여 주는 스크린샷입니다.

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Azure AD 역할의 액세스 검토에 관한 세부 정보를 보여 주는 액세스 검토 개요 페이지 스크린샷" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

일회성 검토인 경우 액세스 검토 기간이 만료되었거나 관리자가 액세스 검토를 중지한 후에는 [Azure 리소스 및 Azure AD 역할의 액세스 검토 완료](pim-complete-azure-ad-roles-and-resource-roles-review.md)의 단계에 따라 결과를 확인하고 적용합니다.  

액세스 검토 시리즈를 관리하려면 액세스 검토로 이동하고, 예약된 검토에서 예정된 항목이 표시되면 종료 날짜를 편집하거나 적절하게 검토자를 추가/제거합니다.

**설정 완료 시** 에 대한 선택에 따라 검토의 종료 날짜 후 또는 수동으로 검토를 중지하면 자동 적용이 실행됩니다. 검토의 상태는 **완료됨** 에서 **적용 중** 과 같은 중간 상태를 거쳐 최종적으로 **적용됨** 상태로 변경됩니다. 거부된 사용자(있는 경우)가 몇 분 내에 역할에서 제거되는 것을 볼 수 있어야 합니다.

> [!IMPORTANT]
> 그룹이 **Azure 리소스 역할** 에 할당된 경우 Azure 리소스 역할의 검토자는 중첩된 그룹을 통해 할당된 액세스 권한이 있는 간접 사용자의 확장된 목록을 볼 수 있습니다. 검토자가 중첩된 그룹의 멤버를 거부하는 경우 사용자가 중첩된 그룹에서 제거되지 않으므로 해당 거부 결과가 역할에 제대로 적용되지 않습니다. **Azure AD 역할** 의 경우 그룹의 멤버를 확장하는 대신 [역할 할당 가능 그룹](../roles/groups-concept.md)이 검토에 표시되고 검토자는 전체 그룹에 대한 액세스를 승인하거나 거부합니다.

## <a name="update-the-access-review"></a>액세스 검토 업데이트

하나 이상의 액세스 검토가 시작된 후 기존 액세스 검토의 설정을 수정하거나 업데이트할 수 있습니다. 고려해야 할 몇 가지 일반적인 시나리오는 다음과 같습니다.

- **검토자 추가 및 제거** - 액세스 검토를 업데이트할 때 기본 검토자 외에 대체 검토자를 추가할 수 있습니다. 액세스 검토를 업데이트할 때 기본 검토자를 제거할 수 있습니다. 그러나 대체 검토자는 의도적으로 제거할 수 없습니다.

    > [!Note]
    > 검토자 유형이 관리자인 경우에만 대체 검토자를 추가할 수 있습니다. 검토자 유형이 선택된 사용자인 경우 기본 검토자를 추가할 수 있습니다.

- **검토자에게 미리 알림** - 액세스 검토를 업데이트할 때 고급 설정에서 미리 알림 옵션을 사용하도록 설정할 수 있습니다. 사용하도록 설정되면 사용자가 검토를 완료했는지 여부에 관계없이 검토 기간의 중간점에서 메일 알림을 받습니다. 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="액세스 검토 설정 아래 미리 알림 옵션 스크린샷":::

- **설정 업데이트** - 액세스 검토가 반복되는 경우 “현재” 및 “시리즈” 아래에 개별 설정이 있습니다. “현재” 아래 설정을 업데이트하면 현재 액세스 검토의 변경 내용만 적용되지만, “시리즈” 아래 설정을 업데이트하면 모든 이후 반복에 해당하는 설정이 업데이트됩니다.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="액세스 검토 아래 설정 페이지 스크린샷" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 수행](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 완료](pim-complete-azure-ad-roles-and-resource-roles-review.md)
