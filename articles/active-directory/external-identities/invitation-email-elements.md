---
title: B2B 초대 이메일의 요소 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업 초대 이메일 템플릿
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 324b37931fff91779b8d25c744ae63671bae315c
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224313"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Azure Active Directory B2B 협업 초대 이메일의 요소

초대 이메일은 온보드의 파트너를 Azure AD에서 B2B 협업 사용자로 불러오기 위한 중요한 구성 요소입니다. [다른 사용자에게 초대 이메일을 보내는 데 B2B Collaboration을 꼭 사용할 필요는 없지만](add-user-without-invite.md), 이렇게 하면 초대를 수락할지 여부를 결정하는 데 필요한 모든 정보가 사용자에게 제공됩니다. 또한 향후 리소스로 돌아가야 할 때 항상 참조할 수 있는 링크를 제공합니다.

![B2B 초대 이메일을 보여 주는 스크린샷](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>전자 메일 설명

전자 메일의 몇 가지 요소를 확인하여 이러한 기능을 최대한 활용하는 방법에 대해 살펴보겠습니다.

### <a name="subject"></a>제목

이메일의 제목은 다음 패턴을 따릅니다.

&lt;사용자 이름&gt;이 해당 조직 내에서 애플리케이션에 액세스하도록 초대했습니다.

### <a name="from-address"></a>보낸 사람 주소

보낸 사람 주소에 대해서는 LinkedIn 유사 패턴을 사용합니다. 이 패턴은 이메일이 invites@microsoft.com에서 제공되더라도 초대는 다른 조직에서 온 것이라는 것을 명확하게 해야 합니다. 형식은 Microsoft Invitations <invites@microsoft.com> 또는 &lt;tenantname&gt; <invites@microsoft.com>을 대신한 Microsoft Invitations입니다. 

> [!NOTE]
> 중국의 21Vianet에서 운영하는 Azure 서비스의 경우 발신자 주소는 Invites@oe.21vianet.com입니다.  
> Azure AD Government의 경우 발신자 주소는 invites@azuread.us입니다.

### <a name="reply-to"></a>회신

회신 전자 메일은 사용 가능할 때 초대자의 전자 메일로 설정되므로 전자 메일에 회신하면 초대자에게 전자 메일이 다시 전송됩니다.

### <a name="phishing-warning"></a>피싱 경고

이메일은 사용자에게 피싱에 대한 간단한 경고로 시작하고 이미 예상하고 있던 초대만 수락해야 한다는 것을 알립니다. 초대하고자 하는 파트너에게 초대에 대해 사전에 언급하는 것이 좋습니다.

![이메일 내 피싱 경고 이미지](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>초대자의 정보 및 초대 메시지

이메일에는 초대를 보내는 조직과 연결된 이름 및 주 도메인이 포함됩니다. 이 정보는 초대받은 사람이 초대를 수락하는 것에 대한 의사 결정을 내리는 데 도움이 됩니다. 초대자가 [게스트 사용자를 디렉터리, 그룹 또는 앱에 초대](add-users-administrator.md)하거나 [초대 API를 사용](customize-invitation-api.md)할 때 초대의 일부로 메시지를 포함하는 경우 이메일의 주 섹션에 메시지가 강조 표시됩니다. 초대자의 이름 및 프로필 이미지(설정된 경우)도 포함되어 있습니다. 메시지 자체는 텍스트 영역이므로 보안상의 이유로 HTML 태그는 처리하지 않습니다.

![이메일의 초대 메시지 이미지](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>수락 단추 및 리디렉션 URL

이메일의 다음 섹션에는 초대받은 사람이 초대를 수락한 후 어디로 가야 하는지에 대한 정보와 초대를 수락하기 위한 단추가 포함되어 있습니다.  나중에 초대받은 사람이 항상 이 링크를 사용하여 리소스로 직접 돌아갈 수 있습니다.

![이메일의 수락 단추 및 리디렉션 URL 이미지](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>바닥글 섹션

바닥글에는 전송 중인 초대에 대한 자세한 정보가 포함됩니다. 초대받은 사람이 향후 초대를 차단할 수 있는 옵션도 항상 포함되어 있습니다. 조직에서 [개인 정보 취급 방침을 설정한](../fundamentals/active-directory-properties-area.md) 경우 문에 대한 링크가 여기에 표시됩니다.  그렇지 않으면 조직이 개인 정보 취급 방침을 설정하지 않았음을 나타냅니다.

![이메일의 바닥글 섹션 이미지](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>조직 차단(구독 취소)

조직의 초대에서 바닥글에는 **향후 초대 차단** 에 대한 옵션이 포함되어 있습니다. 게스트 사용자는 이 링크를 선택하여 조직의 향후 초대를 차단할 수 있습니다. 이 작업은 또한 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage)에서 사용자의 구독 취소 목록에 조직을 추가합니다.

### <a name="viewing-organizations-youve-blocked"></a>차단된 조직 보기

게스트 사용자는 다음 단계에 따라 차단된 조직을 보거나 내보낼 수 있습니다.

1. [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage)으로 이동합니다.
2. 이메일을 입력하고 이메일 일회용 암호 인증을 위한 로그인 단계를 수행합니다.
3. 차단된 조직을 보거나 복사 및 붙여넣기를 사용하여 이름을 내보냅니다.
   > [!NOTE]
   > 차단한 조직의 초대를 다시 허용하려면 조직을 선택하고 **다음** 을 선택합니다.

## <a name="how-the-language-is-determined"></a>언어를 결정하는 방법

초대 이메일에서 게스트 사용자에게 표시되는 언어는 다음 설정에 의해 결정됩니다. 이러한 설정은 우선 순위 순서대로 나열됩니다. 설정이 구성되지 않은 경우 목록의 다음 설정이 언어를 결정합니다.

- 초대 만들기 API를 사용하는 경우 [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) 개체의 **messageLanguage** 속성
-   게스트의 [사용자 개체](/graph/api/resources/user)에 지정한 **preferredLanguage** 속성
-   게스트 사용자의 홈 테넌트의 속성에 설정된 **알림 언어**(Azure AD 테넌트만 해당)
-   리소스 테넌트의 속성에 설정된 **알림 언어**

이러한 설정을 하나도 구성하지 않은 경우 기본 언어는 영어(미국)입니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure Active Directory 관리자가 B2B 협업 사용자를 추가하는 방법은 무엇인가요?](add-users-administrator.md)
- [정보 작업자가 B2B 협업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)
- [B2B 협업 초대 상환](redemption-experience.md)
- [초대 없이 B2B 협업 사용자 추가](add-user-without-invite.md)
