---
title: 양방향 SMS는 더 이상 지원되지 않습니다. - Azure Active Directory
description: 여전히 양방향 SMS를 사용하는 사용자들에게 다른 방법을 사용할 수 있도록 설정하는 방법을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/19/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92095449132b790c80cf6396a3608f802d7e9a7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458507"
---
# <a name="two-way-sms-unsupported"></a>양방향 SMS가 지원되지 않음

Azure AD MFA(Multi-Factor Authentication) 서버용 양방향 SMS는 원래 2018년에 사용이 중단되었으며, 2021년 8월 2일까지 지원 확장을 받은 조직을 제외하고 2021년 2월 24일 이후에는 더 이상 지원되지 않습니다. 관리자는 아직 양방향 SMS를 사용하는 사용자들이 다른 방법을 사용할 수 있도록 하여야 합니다.

대상 관리자에게 2020년 12월 8일과 2021년 1월 28일에 이메일 알람과 Azure Portal Service Health 알림(포털 알림)이 발송되었습니다. 이 경고는 해당 구독에 연결된 소유자, 공동 소유자, 관리자 및 서비스 관리자 RBAC 역할에 전달되었습니다. 이미 다음 단계를 완료하였다면 아무 조치도 필요 없습니다.

## <a name="required-actions"></a>필요한 작업

1. 아직 완료 전인 경우, 사용자용 모바일 앱을 사용하도록 설정합니다. 자세한 내용은 [MFA 서버를 통한 모바일 앱 인증 사용](howto-mfaserver-deploy-mobileapp.md)을 확인하세요.
1. 최종 사용자에게 내 MFA 서버의 [사용자 포털](howto-mfaserver-deploy-userportal.md)을 방문하여 모바일 앱을 활성화하도록 알립니다. [Microsoft Authenticator 앱](https://www.microsoft.com/en-us/account/authenticator)은 양방향 SMS보다 안전하다는 점에서 추천하는 확인 옵션입니다. 자세한 내용은 [전화기를 통한 인증은 이제 그만](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)을 확인하세요.
1. 양방향 텍스트 메시지에서 모바일 앱으로 사용자 설정의 기본 메서드를 변경합니다.

## <a name="faq"></a>FAQ

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>기본 메서드를 양방향 SMS에서 모바일 앱으로 변경하지 않으면 어떻게 되나요?
양방향 SMS는 2021년 2월 24일 이후 실패합니다. 사용자가 로그인을 시도하고 MFA를 통과하려고 하면 오류가 발생합니다.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>사용자 설정을 양방향 텍스트 메시지에서 모바일 앱으로 변경하는 방법은 무엇인가요?

다음 단계를 따라 사용자 설정을 변경하여야 합니다.

1. MFA 서버에서 양방향 텍스트 메시지에 대한 사용자 목록을 필터링합니다.
1. 모든 사용자를 선택합니다.
1. 사용자 편집 대화를 엽니다.
1. 사용자를 텍스트 메시지에서 모바일 앱으로 변경합니다.

   ![최종 사용자의 스크린샷](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>사용자가 취하여야 할 작업이 있나요? 그렇다면 어떻게 하나요?
예. 최종 사용자는 특정 MFA 서버 사용자 포털을 방문하여 모바일 앱을 활성화하지 않은 경우 이를 수행하여야 합니다. 3단계를 완료하고 나면 사용자 포털을 방문하여 모바일 앱을 설정하지 않은 사용자는 사용자 포털에 방문하여 재등록하기 전에는 로그인에 실패하게 됩니다.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>사용자가 모바일 앱을 설치할 수 없으면 어떻게 하나요? 다른 옵션이 있나요?
양방향 SMS 또는 모바일 앱에 대한 대안으로 전화 통화를 이용할 수 있습니다. 그러나 Microsoft Authenticator 앱을 확인 메서드로 사용하는 것이 좋습니다.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>단방향 SMS도 사용되지 않나요?
아니요. 양방향 SMS만 사용되지 않습니다. MFA 서버의 경우에는 다음 시나리오의 하위 집합에 대하여 단방향 SMS가 작동합니다.

- AD FS 어댑터
- IIS 인증(사용자 포털 및 구성이 필요)
- RADIUS(RADIUS 클라이언트가 액세스 변경을 지원하고 PAP 프로토콜이 사용되어야 함)

모바일 앱은 확인 코드 메시지 표시가 필요 없기 때문에, 단방향 SMS를 사용하여 모바일 앱을 더 나은 대안으로 활용할 수 있는 시기에 대하여서는 제한 사항이 있습니다.
여전히 어떤 시나리오에서는 단방향 SMS를 사용하고자 하는 경우에는 해당 항목을 선택된 상태로 남겨둘 수 있지만, **회사 설정** 섹션, **일반** 탭 **사용자 기본값 텍스트 메시지** 를 **양방향** 대신 **단방향** 으로 변경합니다. 마지막으로 SMS를 기본값으로 하는 디렉터리 동기화를 사용하는 경우, 이를 양방향이 아니라 단방향으로 변경하여야 합니다.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>아직 양방향 SMS를 사용하는 사용자가 누구인지 어떻게 확인하나요?
해당되는 사용자를 목록으로 표시하려면 **MFA 서버** 를 시작하여 **사용자** 섹션을 선택하고 **사용자 목록 필터링** 을 클릭한 다음, **텍스트 메시지 양방향** 을 필터링합니다.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>나중에라도 사용자들이 선택하지 않도록 양방향 SMS를 MFA 포털의 옵션에서 감추려면 어떻게 하나요?
MFA 서버 사용자 포털에서 **설정** 을 클릭하면 **텍스트 메시지** 를 삭제하여 사용할 수 없도록 할 수 있습니다. 사용자 등록에 AD FS를 사용한다면 마찬가지로 **AD FS** 섹션에 적용할 수 있습니다.

