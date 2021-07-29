---
title: PIM에서 Azure AD 역할에 대한 액세스 검토 만들기 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할에 대한 액세스 검토를 만드는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 05/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdd4fe2f749f36fe0016e0cba71093cd6083dda8
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783788"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 액세스 검토 만들기

오래된 역할 할당과 관련된 위험을 줄이려면 주기적으로 액세스를 검토해야 합니다. Azure AD PIM(Privileged Identity Management)을 사용하여 권한 있는 Azure AD 역할에 대한 액세스 검토를 만들 수 있습니다. 자동으로 발생하는 정기 액세스 검토를 구성할 수도 있습니다.

이 문서에서는 권한 있는 Azure AD 역할에 대한 액세스 검토를 하나 이상 만드는 방법을 설명합니다.

## <a name="prerequisite-license"></a>필수 라이선스

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] PIM의 라이선스에 대한 자세한 내용은 [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

> [!Note]
> 현재 테넌트에서 활성화된 Azure Active Directory Premium P2 버전을 사용하여 Azure AD 및 Azure 리소스 역할(미리 보기)에 액세스할 수 있는 서비스 주체에 대한 액세스 검토의 범위를 지정할 수 있습니다. 서비스 주체에 대한 라이선스 모델은 이 기능의 일반 공급용으로 최종 결정되며 추가 라이선스가 필요할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

전역 관리자

## <a name="open-access-reviews"></a>액세스 검토 열기

1. 전역 관리자 역할이 할당된 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **ID 거버넌스** 를 선택합니다.
 
3. **Azure AD Privileged Identity Management** 에서 **Azure AD 역할** 을 선택합니다.
 
4. **관리** 에서 다시 **Azure AD 역할** 을 선택합니다.

5. 관리에서 **액세스 검토** 를 선택한 다음 **새로 만들기** 를 선택합니다.

    ![Azure AD 역할 - 모든 검토의 상태를 보여 주는 액세스 검토 목록](./media/pim-how-to-start-security-review/access-reviews.png)

6. 새 액세스 검토를 만들려면 **새로 만들기** 를 클릭합니다.

7. 액세스 검토의 이름을 지정합니다. 필요한 경우 검토에 설명을 지정합니다. 이름 및 설명이 검토자에게 표시됩니다.

    ![액세스 검토 만들기 - 이름 및 설명 검토](./media/pim-how-to-start-security-review/name-description.png)

8. **시작 날짜** 를 설정합니다. 기본적으로 액세스 검토는 한 번 발생하고, 만들어진 시간에 시작되고, 1개월 후에 종료됩니다. 시작 날짜와 종료 날짜를 변경하여 액세스 검토를 나중에 시작하고 원하는 기간(일 수) 동안 지속할 수 있습니다.

    ![시작 날짜, 빈도, 기간, 종료, 횟수 및 종료 날짜](./media/pim-how-to-start-security-review/start-end-dates.png)

9. 액세스 검토를 반복하려면 **빈도** 설정을 **한 번** 에서 **매주**, **매월**, **분기마다**, **매년** 또는 **반년마다** 로 변경합니다. **기간** 슬라이더 또는 텍스트 상자를 사용하여 검토자의 입력에 대해 반복 시리즈의 각 검토가 열릴 일수를 정의합니다. 예를 들어 검토가 겹치는 상황을 방지하기 위해 월별 검토에 대해 설정할 수 있는 최대 기간은 27일입니다.

10. **종료** 설정을 사용하여 되풀이 액세스 검토 시리즈를 종료하는 방법을 지정합니다. 이 시리즈는 세 가지 방법으로 종료할 수 있습니다. 무기한으로, 특정 날짜까지, 또는 정의된 되풀이 횟수가 완료된 이후에 검토를 시작하도록 연속적으로 실행됩니다. 사용자, 다른 사용자 관리자 또는 다른 전역 관리자는 해당 날짜에 종료되도록 **설정** 에서 날짜를 변경하여 생성 후 시리즈를 중지할 수 있습니다.

11. **사용자 범위** 섹션에서 검토의 범위를 선택합니다. Azure AD 역할에 대한 액세스 권한이 있는 사용자 및 그룹을 검토하려면 **사용자 및 그룹** 을 선택하거나 **(미리 보기) 서비스 주체** 를 선택하여 Azure AD 역할에 대한 액세스 권한이 있는 컴퓨터 계정을 검토합니다.

    ![역할 멤버 자격을 검토할 사용자 범위](./media/pim-how-to-start-security-review/users.png)

12. **역할 멤버 자격 검토** 에서 검토할 권한 있는 Azure AD 역할을 선택합니다. 

    > [!NOTE]
    > - 둘 이상의 역할을 선택하면 여러 액세스 검토가 생성됩니다. 예를 들어 5개의 역할을 선택하면 별도의 액세스 검토가 5개 생성됩니다.
    > - 그룹이 할당된 역할의 경우 검토 중인 역할과 연결된 각 그룹의 액세스는 액세스 검토의 일부로 검토됩니다.
    **Azure AD 역할** 의 액세스 검토를 만드는 경우 다음은 멤버 자격 검토 목록 예제를 보여 줍니다.

    > [!NOTE]
    > 둘 이상의 역할을 선택하면 여러 액세스 검토가 생성됩니다. 예를 들어 5개의 역할을 선택하면 별도의 액세스 검토가 5개 생성됩니다.

1. **할당 유형** 에서 주체가 역할에 할당된 방식에 따라 검토 범위를 지정합니다. **(미리 보기) 적격 할당만** 을 선택하여 검토 생성시 활성화 상태에 관계없이 적격 할당을 검토하거나 **(미리 보기) 활성 할당만** 을 선택하여 활성 할당을 검토합니다. 유형에 관계없이 모든 할당을 검토하려면 **모든 활성 및 적격 할당** 을 선택합니다.

     ![검토자 할당 유형 목록](./media/pim-how-to-start-security-review/assignment-type-select.png)

13. **검토자** 섹션에서 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다.

    ![선택한 사용자 또는 멤버(자신)의 검토자 목록](./media/pim-how-to-start-security-review/reviewers.png)

    - **선택한 사용자** - 검토를 완료할 특정 사용자를 지정하려면 이 옵션을 사용합니다. 이 옵션은 검토 범위에 관계없이 사용할 수 있으며, 선택한 검토자는 사용자, 그룹 및 서비스 주체를 검토할 수 있습니다. 
    - **멤버(자신)** - 사용자에게 자신의 역할 할당을 검토하게 하려면 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 역할에 할당된 그룹은 검토에 포함되지 않습니다. 이 옵션은 검토 범위가 **사용자 및 그룹** 으로 지정된 경우에만 사용할 수 있습니다.
    - **관리자** - 사용자의 관리자가 사용자의 역할 할당을 검토하도록 하려면 이 옵션을 사용합니다. 이 옵션은 검토 범위가 **사용자 및 그룹** 으로 지정된 경우에만 사용할 수 있습니다. 관리자를 선택하면 대체 검토자도 지정할 수 있습니다. 사용자가 디렉터리에 관리자를 지정하지 않은 경우 대체 검토자에게 사용자를 검토하라는 메시지가 표시됩니다. 역할에 할당된 그룹은 대체 검토자(선택된 경우)가 검토합니다. 

### <a name="upon-completion-settings"></a>완료 시 설정

1. 검토가 완료된 후 수행할 작업을 지정하려면 **완료 시 설정** 섹션을 확장합니다.

    ![자동 응답 및 검토자가 응답하지 않을 경우에 대한 완료 시 설정](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. 거부된 사용자에 대한 액세스를 자동으로 제거하려면 **결과를 리소스에 자동 적용** 을 **사용** 으로 설정합니다. 검토가 완료될 때 결과를 수동으로 적용하려면 스위치를 **사용 안 함** 으로 설정합니다.

1. **검토자가 응답하지 않는 경우** 목록을 사용하여 검토 기간 내에 검토자가 검토하지 않은 사용자를 어떻게 할 것인지 지정합니다. 이 설정은 검토자가 수동으로 검토한 사용자에게 영향을 주지 않습니다. 최종 검토자의 결정이 [거부]이면 사용자의 액세스 권한이 제거됩니다.

    - **변경 없음** - 사용자의 액세스 권한을 그대로 유지
    - **액세스 권한 제거** - 사용자의 액세스 권한을 제거
    - **액세스 권한 승인** - 사용자의 액세스 권한을 승인
    - **권장 작업 수행** - 사용자의 지속적인 액세스를 거부할 것인지 아니면 승인할 것인지에 대한 시스템의 권장 사항을 수용

1. 추가 사용자 또는 그룹(미리 보기)에 알림을 보내 검토 완료 업데이트를 받을 수 있습니다. 이 기능을 사용하면 검토 작성자 이외의 관련자를 검토 진행 상황에서 업데이트할 수 있습니다. 이 기능을 사용하려면 **사용자 또는 그룹 선택** 을 선택하고 완료 상태를 받을 사용자 또는 그룹을 추가합니다.

    ![완료 시 설정 - 알림을 받을 사용자를 추가합니다.](./media/pim-how-to-start-security-review/upon-completion-settings-additional-receivers.png) 

### <a name="advanced-settings"></a>고급 설정

1. 추가 설정을 지정하려면 **고급 설정** 섹션을 확장합니다.

    ![권장 사항 표시, 승인 이유, 메일 알림 및 미리 알림에 대한 고급 설정](./media/pim-how-to-start-security-review/advanced-settings.png)

1. **권장 사항 표시** 를 **사용** 으로 설정하면 사용자의 액세스 정보에 따라 검토자에게 시스템 권장 사항이 표시됩니다.

1. **승인 시 이유 필요** 를 **사용** 으로 설정하면 검토자가 승인 이유를 입력해야 합니다.

1. **메일 알림** 을 **사용** 으로 설정하면 Azure AD는 액세스 검토가 시작될 때 검토자에게 이메일 알림을 보내고 검토가 완료될 때 관리자에게 이메일 알림을 보냅니다.

1. **미리 알림** 을 **사용** 으로 설정하면 Azure AD는 검토를 완료하지 않은 검토자에게 진행 중인 액세스 검토에 대한 미리 알림을 보냅니다.
1. 검토자에게 보낸 이메일의 콘텐츠는 검토 이름, 리소스 이름, 기한 등 검토 세부 정보를 기준으로 자동 생성됩니다. 추가 지침이나 연락처 등의 추가 정보를 전달할 방법이 필요한 경우 **검토자 이메일에 대한 추가 콘텐츠** 에 해당 세부 정보를 지정할 수 있습니다. 이 콘텐츠는 할당된 검토자에게 전송하는 초대 및 미리 알림 이메일에 포함됩니다. 아래 강조 표시된 섹션이 이 정보가 표시되는 위치입니다.

    ![강조 표시된 검토자에게 보낸 이메일 콘텐츠](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정했으면 **시작** 을 선택합니다. 액세스 검토는 상태 지표를 사용하여 목록에 표시됩니다.

![시작된 검토의 상태를 보여 주는 액세스 검토 목록](./media/pim-how-to-start-security-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. [Azure AD 역할에 대한 액세스 검토](pim-how-to-perform-security-review.md) 방법에 대한 지침을 보여 줄 수 있습니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 액세스 검토의 **개요** 페이지에서 검토를 완료하는 진행 상황을 추적할 수 있습니다. 액세스 권한은 [검토가 완료](pim-how-to-complete-review.md)될 때까지 디렉터리에서 변경되지 않습니다.

![검토 세부 정보를 표시하는 액세스 검토 개요 페이지](./media/pim-how-to-start-security-review/access-review-overview.png)

일회성 검토인 경우 액세스 검토 기간이 만료되었거나 관리자가 액세스 검토를 중지한 후에는 [Azure AD 역할에 대한 액세스 검토 완료](pim-how-to-complete-review.md)의 단계를 따라 결과를 확인하고 적용합니다.  

액세스 검토 시리즈를 관리하려면 액세스 검토로 이동하고, 예약된 검토에서 예정된 항목이 표시되면 종료 날짜를 편집하거나 적절하게 검토자를 추가/제거합니다.

**설정 완료 시** 에 대한 선택에 따라 검토의 종료 날짜 후 또는 수동으로 검토를 중지하면 자동 적용이 실행됩니다. 검토의 상태는 **완료됨** 에서 **적용 중** 과 같은 중간 상태를 거쳐 최종적으로 **적용됨** 상태로 변경됩니다. 거부된 사용자(있는 경우)가 몇 분 내에 역할에서 제거되는 것을 볼 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 역할에 대한 액세스 검토](pim-how-to-perform-security-review.md)
- [Azure AD 역할에 대한 액세스 검토 완료](pim-how-to-complete-review.md)
- [Azure 리소스 역할에 대한 액세스 검토 만들기](pim-resource-roles-start-access-review.md)
