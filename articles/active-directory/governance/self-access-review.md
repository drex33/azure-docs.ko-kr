---
title: 액세스 검토에서 리소스에 대한 액세스 검토 - Azure AD
description: Azure Active Directory 액세스 검토에서 리소스에 대한 자신의 액세스 권한을 검토하는 방법을 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/27/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7c9d0e24eefb1c615f45f0aa33f5a1fe0af760
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102694"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리의 액세스 패키지 및 리소스에 대한 자체 검토

Azure AD 권한 관리는 기업의 그룹, 애플리케이션, SharePoint 사이트에 대한 액세스 관리 방법을 간소화합니다. 이 문서에서는 사용자가 할당된 액세스 패키지 자체 검토를 수행하는 방법을 설명합니다.

## <a name="review-your-own-access-using-my-access"></a>내 액세스를 사용하여 내 액세스 권한 검토

아래에서 설명하는 두 가지 방법으로 그룹, 애플리케이션 또는 액세스 패키지에 대한 자신의 액세스를 검토할 수 있습니다.

### <a name="email"></a>메일

>[!IMPORTANT]
> 이메일을 받을 때 지연될 수 있으며, 경우에 따라 최대 24시간이 걸릴 수 있습니다. 수신 허용 목록에 추가azure-noreply@microsoft.com하여 모든 메일을 수신하고 있는지 확인합니다.

1. Microsoft에서 액세스를 검토하도록 요청하는 메일을 찾습니다. 아래와 같은 이메일 메시지의 예를 볼 수 있습니다.

 ![그룹에 대한 액세스를 검토하도록 Microsoft에서 보낸 이메일의 예](./media/self-access-review/access-review-email-preview.png)

2. **액세스 검토** 링크를 클릭하여 액세스 검토를 엽니다.

3. **액세스 검토 수행** 섹션에서 계속 진행합니다.

### <a name="directly-at-my-access"></a>내 액세스에서 직접

브라우저를 사용하여 내 액세스를 열어 보류 중인 액세스 검토를 볼 수도 있습니다.

1. https://myaccess.microsoft.com/ 에서 내 액세스에 로그인합니다.

2. 왼쪽 사이드바의 메뉴에서 **액세스 검토** 를 선택하여 사용자에게 할당된 보류 중인 액세스 검토 목록을 표시합니다.

   ![메뉴의 액세스 검토](./media/self-access-review/access-review-menu.png)

## <a name="perform-the-access-review"></a>액세스 검토 수행

1. 그룹 및 앱 아래에서 다음을 볼 수 있습니다.
    
    - **이름** 액세스 검토의 이름입니다.
    - **기한** 검토 기한입니다. 이 날짜 이후에는 검토 중인 그룹 또는 앱에서 거부된 사용자가 제거될 수 있습니다.
    - **리소스** 검토 중인 리소스의 이름입니다.
    - **진행률** 이 액세스 검토의 총 사용자 수에 대해 검토된 사용자 수입니다.
    
2. 시작하려면 액세스 검토 이름을 클릭합니다.

   ![앱 및 그룹에 대한 보류 중인 액세스 검토 목록](./media/self-access-review/access-reviews-list-preview.png)

3. 액세스 권한을 검토하고 여전히 액세스 권한이 필요한지 여부를 결정합니다.

    다른 사람의 액세스 권한을 검토하라는 요청이면 페이지가 다르게 표시됩니다. 자세한 내용은 [그룹 또는 애플리케이션에 대한 액세스 검토](perform-access-review.md)를 참조하세요.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 액세스 검토 열기](./media/self-access-review/review-access-preview.png)

1. 액세스를 유지하려면 **예** 를 선택하고 액세스를 제거하려면 **아니요** 를 선택합니다.

1. **예** 를 클릭하는 경우 **이유** 상자에 근거를 지정해야 할 수 있습니다.

    ![그룹에 대한 액세스가 필요한지 여부를 묻는 액세스 검토 완료](./media/self-access-review/review-access-yes-preview.png)

1. **제출** 을 클릭합니다.

    선택 항목이 제출되고 내 액세스 페이지로 돌아갑니다.

    응답을 변경하려면 액세스 검토 페이지를 다시 열고 응답을 업데이트합니다. 액세스 검토가 끝날 때까지 언제든지 응답을 변경할 수 있습니다.

    > [!NOTE]
    > 더 이상 액세스할 필요가 없다고 표시한 경우, 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 제거됩니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)


