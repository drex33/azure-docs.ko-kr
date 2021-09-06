---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 검토 만들기
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 관리 액세스 패키지에 대한 액세스 검토 정책을 만드는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f2497e47078f91c1649db107dd105227e219ae2
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113565735"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 검토 만들기

부실 액세스의 위험을 줄이려면 Azure AD 권한 관리에서 액세스 패키지에 대한 활성 할당이 있는 사용자를 정기적으로 검토할 수 있어야 합니다. 새 액세스 패키지를 만들거나 기존 액세스 패키지를 편집하는 경우 검토를 사용하도록 설정할 수 있습니다. 이 문서에서는 액세스 패키지 액세스 검토를 사용하도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

액세스 패키지에 대한 검토를 사용하도록 설정하려면 액세스 패키지 만들기에 대한 필수 조건을 충족해야 합니다.
- Azure AD Premium P2
- 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.


## <a name="create-an-access-review-of-an-access-package"></a>액세스 패키지에 대한 액세스 검토 만들기

[새 액세스 패키지를 만들거나](entitlement-management-access-package-create.md) [기존 액세스 패키지 정책을 편집](entitlement-management-access-package-lifecycle-policy.md)하는 경우 액세스 검토를 사용하도록 설정할 수 있습니다. 액세스 패키지에 대한 액세스 검토를 사용하도록 설정하려면 다음 단계를 따르세요.

1. 액세스 패키지의 **수명 주기** 탭을 열고 **액세스 검토** 로 스크롤합니다.

1. **액세스 검토 필요** 토글을 **예** 로 이동합니다.

    ![액세스 검토 추가](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. **시작 날짜** 옆에 검토가 시작될 날짜를 지정합니다.

1. 그런 다음 **검토 빈도** 를 **매년**, **6개월마다**, **매분기** 또는 **매월** 로 설정합니다.
이 설정은 액세스 검토를 수행하는 빈도를 결정합니다.

1. 검토자의 입력과 관련하여 일련의 되풀이 작업에 대한 각 검토가 열려 있는 일수를 정의하는 **기간** 을 설정합니다. 예를 들어 1월 1일에 시작하고 30일 동안 검토가 열려 있는 연간 검토를 예약하면 검토자는 해당 월이 종료될 때까지 응답할 수 있게 됩니다.

1. **검토자** 옆에서는 사용자가 자체 액세스 검토를 수행하도록 하려면 **자체 검토** 를 선택하고 검토자를 지정하려면 **특정 검토자** 를 선택합니다. 검토자의 관리자를 검토자로 지정하려는 경우 **관리자** 를 선택할 수도 있습니다. 이 옵션을 선택하는 경우 시스템에서 관리자를 찾을 수 없다면 검토를 전달할 **대체** 인력을 추가해야 합니다.

1. **특정 검토자** 를 선택한 경우 액세스 검토를 수행할 사용자를 지정합니다. ![검토자 추가를 선택합니다](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png).

    1. **검토자 추가** 를 선택합니다.
    1. **검토자 선택** 창에서 검토자로 지정할 사용자를 검색하여 선택합니다.
    1. 검토자를 선택한 경우 **선택** 단추를 클릭합니다.

    ![검토자 지정](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1.  **관리자** 를 선택한 경우 대체 검토자를 지정합니다. 
    1.  **대체 검토자 추가** 를 선택합니다.
    1. 대체 검토자 선택 창에서 검토자의 관리자에 대한 대체 검토자가 될 사용자를 검색하여 선택합니다.
    1. 대체 검토자를 선택한 경우  **선택** 단추를 클릭합니다. 

    ![대체 검토자 추가](./media/entitlement-management-access-reviews/access-reviews-select-manager.png)

1. 페이지 맨 아래에서는 새 액세스 패키지를 만드는 경우 **검토 + 만들기** 를 클릭하고 액세스 패키지를 편집하는 경우 **업데이트** 를 클릭합니다.

## <a name="view-the-status-of-the-access-review"></a>액세스 검토 상태 보기

시작 날짜 이후 **액세스 검토** 섹션에 액세스 검토가 나열됩니다. 액세스 검토 상태를 보려면 다음 단계를 수행합니다.

1. **ID 거버넌스** 에서 **액세스 패키지** 를 클릭한 다음 확인할 액세스 검토 상태의 액세스 패키지를 선택합니다.   

1. 액세스 패키지 개요에 있는 경우 왼쪽 메뉴에서 **액세스 검토** 를 클릭합니다.
    
    ![액세스 검토 선택](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 액세스 검토와 연결된 모든 정책이 포함되어 있는 목록이 표시됩니다. 검토를 클릭하여 해당 보고서를 봅니다.

    ![액세스 검토 목록](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 보고서를 보면 검토한 사용자 수와 검토자가 수행한 작업이 표시되어 있습니다.

    ![검토 상태 보기](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>액세스 검토 메일 알림
검토자를 지정하거나 사용자가 자체적으로 액세스를 검토할 수 있습니다. Azure AD는 기본적으로 검토가 시작되면 곧바로 검토자 또는 자체 검토자에게 메일을 보냅니다.

메일에는 액세스 패키지에 대한 액세스를 검토하는 방법 지침이 포함됩니다. 검토에서 사용자가 자체 액세스를 검토하는 경우 액세스 패키지에 대한 자체 검토 수행 방법 지침을 보여 줍니다.
  
게스트 사용자를 검토자로 할당했으나 Azure AD 게스트 초대를 수락하지 않은 경우 Azure AD 액세스 검토에서 메일을 받지 못합니다. 먼저 초대를 수락하고 Azure AD를 사용하여 계정을 만들어야 메일을 받을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 액세스 검토](entitlement-management-access-reviews-review-access.md)
- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)
