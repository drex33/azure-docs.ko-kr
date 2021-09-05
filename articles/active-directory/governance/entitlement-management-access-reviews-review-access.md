---
title: Azure AD 권한 관리에서 액세스 패키지의 액세스 검토
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 관리 액세스 패키지의 액세스 검토를 완료하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: amsliu
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/22/2021
ms.author: amsliu
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d034d3e64e6c74b076ba636822bbd6e04ad1f5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440226"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지의 액세스 검토

Azure AD 권한 관리는 기업의 그룹, 애플리케이션, SharePoint 사이트에 대한 액세스 관리 방법을 간소화합니다. 이 문서에서는 지정된 검토자로 액세스 패키지에 할당된 다른 사용자에 대해 액세스 검토를 수행하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

사용자의 활성 액세스 패키지 할당을 검토하려면 검토 작성자가 다음 필수 구성 요소를 충족해야 합니다.
- Azure AD Premium P2
- 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.

>[!NOTE]
>검토자는 검토 작성자가 선택한 모든 사람(그룹 소유자, 사용자 관리자, 사용자 자신 또는 선택한 사용자 또는 그룹)일 수 있습니다.


## <a name="open-the-access-review"></a>액세스 검토 열기

다음 단계에 따라 액세스 검토를 찾아서 엽니다.

1. Microsoft로부터 액세스 검토를 요청하는 이메일을 받을 수도 있습니다. 이메일을 찾아 액세스 검토를 엽니다. 다음은 액세스 검토를 위한 이메일의 예입니다.
    
    ![액세스 검토 검토자 이메일](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. **사용자 액세스 검토** 링크를 클릭하여 액세스 검토를 엽니다. 

1. 이메일이 없는 경우 https://myaccess.microsoft.com 으로 직접 이동하여 보류 중인 액세스 검토를 찾을 수 있습니다.  (미국 정부의 경우 `https://myaccess.microsoft.us`를 사용합니다.)

1. 왼쪽 탐색 모음에서 **액세스 검토** 를 클릭하여 본인에게 할당된 보류 중인 액세스 검토 목록을 표시합니다.
    
    ![내 액세스에서 액세스 검토 선택](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 시작할 검토를 클릭합니다.
    
    ![액세스 검토 선택](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토해야 하는 사용자의 이름이 표시됩니다. 액세스를 승인하거나 거부할 방법은 두 가지가 있습니다.
- 한 명 이상의 사용자에 대한 액세스를 수동으로 승인하거나 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>한 명 이상의 사용자에 대한 액세스를 수동으로 승인 또는 거부
1. 사용자 목록을 검토하고 계속 액세스해야 하는 사용자를 결정합니다.

    ![검토할 사용자 목록](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 액세스를 승인하거나 거부하려면 사용자 이름 왼쪽에 있는 라디오 단추를 선택합니다.

1. 사용자 이름 위의 표시줄에서 **승인** 또는 **거부** 를 선택합니다.

    ![사용자 선택](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 확실하지 않은 경우 **잘 모름** 단추를 클릭합니다.

    이 항목을 선택하면 사용자가 액세스를 유지 관리하며 선택 내용이 감사 로그에 기록됩니다. 로그에는 검토를 완료한 다른 검토자가 표시됩니다.

1. 결정에 대한 이유를 제공해야 할 수도 있습니다. 이유를 입력하고 **제출** 을 클릭합니다.

    ![액세스 승인 또는 거부](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 검토가 끝나기 전에 언제든지 결정을 변경할 수 있습니다. 이렇게 하려면 목록에서 사용자를 선택하고 결정을 변경합니다. 예를 들어 이전에 거부한 사용자에 대한 액세스를 승인할 수 있습니다.

검토자가 여러 명인 경우 마지막에 제출된 응답이 기록됩니다. 관리자가 Alice와 Bob이라는 두 검토자를 지정하는 예를 살펴보겠습니다. Alice가 검토를 먼저 열고 액세스를 승인합니다. 검토가 끝나기 전에 Bob이 검토를 열고 액세스를 거부합니다. 이 경우 마지막 거부 액세스 결정이 기록됩니다.

>[!NOTE]
>사용자의 액세스가 거부된 경우 액세스 패키지에서 사용자가 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 종료하면 사용자가 액세스 패키지에서 제거됩니다. 그러나 사용자에게 액세스 권한이 승인되면 검토 기간이 계속 열려 있는 경우에도 승인이 즉시 허용됩니다.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>시스템 생성 권장 사항을 사용하여 액세스 승인 또는 거부

여러 사용자에 대한 액세스를 더 빠르게 검토하기 위해 시스템 생성 권장 사항을 사용하여 한 번의 클릭으로 권장 사항에 동의할 수 있습니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성됩니다.

1.  페이지 상단의 표시줄에서 **권장 사항 동의** 를 클릭합니다.
    
    ![권장 사항 동의 선택](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    권장되는 작업 요약이 표시됩니다.

1.  **제출** 을 클릭하여 권장 사항에 동의합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)