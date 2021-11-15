---
title: Safari에서 ITP(Intelligent Tracking Protection)를 처리하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: 타사 쿠키가 더 이상 허용되지 않는 경우, SPA(단일 페이지 앱) 인증을 사용합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 13df7323c1b0e0c7a18ffa722454bf1b9ce147d8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993685"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>타사 쿠키를 차단하는 Safari 및 다른 브라우저에서 ITP 처리

현재 대부분의 브라우저에서는 타사 쿠키 즉, 브라우저 표시줄에 표시되는 것과 동일하지 않은 도메인에 대해 요청되는 쿠키를 차단합니다. 이렇게 하면 암시적 흐름이 중단되며, 사용자 로그인인 성공하려면 새 인증 패턴이 필요합니다. Microsoft ID 플랫폼에서는 PKCE(Proof Key for Code Exchange) 및 새로 고침 토큰과 함께 권한 부여 흐름을 사용하여 타사 쿠키가 차단될 때 사용자 로그인 상태를 유지합니다.

## <a name="what-is-intelligent-tracking-protection-itp"></a>ITP(Intelligent Tracking Protection)의 정의

Apple Safari에는 [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/)또는 _ITP_ 라는 개인 정보 보호 기능이 기본적으로 설정되어 있습니다. ITP는 "타사" 쿠키 즉, 여러 도메인을 교차하는 요청에 대한 쿠키를 차단합니다.

일반적인 형태의 사용자 추적은 백그라운드에서 타사 사이트에 iframe을 로드하고 쿠키를 사용하여 인터넷을 통해 사용자를 상호 연결함으로써 실행됩니다. 아쉽게도 이 패턴은 단일 페이지 앱(SPA)에서 [암시적 흐름](v2-oauth2-implicit-grant-flow.md)을 구현하는 표준 방법이기도 합니다. 브라우저에서 타사 쿠키를 차단하여 사용자 추적을 방지하는 경우에도 SPA는 손상됩니다.

Safari 외에도 많은 브라우저에서는 사용자 개인정보 보호를 강화하기 위해 타사 쿠키를 차단하고 있습니다. Brave는 기본적으로 타사 쿠키를 차단하고 있으며, Chromium(Google Chrome 및 Microsoft Edge 배후의 플랫폼)은 향후 타사 쿠키에 대한 지원도 중단할 것이라고 발표했습니다.

이 문서에서 간략히 설명한 솔루션은 이러한 모든 브라우저에서 작동하며 또는 타사 쿠키가 차단되는 모든 브라우저에서 작동합니다.

## <a name="overview-of-the-solution"></a>솔루션 개요

SPA에서 사용자 인증을 계속 진행하려면 앱 개발자가 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용해야 합니다. 인증 코드 흐름에서 ID 공급자는 코드를 발급하고 SPA는 액세스 토큰 및 새로 고침 토큰에 대한 코드를 사용합니다. 앱에 추가 토큰이 필요한 경우, [새로 고침 토큰 흐름](v2-oauth2-auth-code-flow.md#refresh-the-access-token)을 사용하여 새 토큰을 가져올 수 있습니다. JavaScript v2.0용 MSAL(Microsoft 인증 라이브러리)은 SPA에 대한 인증 코드 흐름을 구현하며 소규모 업데이트를 통해 MSAL.js 1.x를 즉시 대체합니다.

Microsoft ID 플랫폼의 경우, SPA 및 네이티브 클라이언트는 다음과 같이 비슷한 프로토콜 지침을 따릅니다.

- [PKCE 코드 챌린지](https://tools.ietf.org/html/rfc7636)의 사용
  - PKCE는 Microsoft ID 플랫폼에서 SPA에 필요합니다. PKCE는 네이티브 및 기밀 클라이언트에 권장됩니다.
- 클라이언트 암호 사용 안 함

SPA에는 다음의 두 가지 추가 제한 사항이 있습니다.

- [로그인 엔드포인트에서 CORS를 사용하려면 리디렉션 URI를 `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) 형식으로 표시해야 합니다.
- `spa` 리디렉션 URI에 대한 인증 코드 흐름을 통해 발급된 새로 고침 토큰의 수명은 90일이 아닌 24시간입니다.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="단일 페이지 앱과 보안 토큰 서비스 엔드포인트 간의 OAuth 2 인증 코드 흐름을 보여 주는 다이어그램입니다." border="false":::

## <a name="performance-and-ux-implications"></a>성능 및 UX 영향

암시적 흐름을 사용하는 일부 애플리케이션은 `prompt=none`을 사용해 로그인 iframe을 열어 리디렉션하지 않고 로그인을 시도합니다. 대부분의 브라우저에서 이 요청은 현재 로그인된 사용자에 대한 토큰을 사용하여 응답합니다(동의가 이미 부여된 것으로 가정). 이 패턴은 애플리케이션에서 사용자가 로그인하기 위해 전체 페이지 리디렉션을 할 필요가 없으며, 성능 및 사용자 환경이 향상됨을 의미합니다(사용자가 웹 페이지를 방문하여 이미 로그인한 상태). 타사 쿠키가 차단될 때 iframe의 `prompt=none`는 더 이상 옵션이 아니기 때문에 애플리케이션에서 인증 코드를 발급하려면 최상위 프레임의 로그인 페이지를 방문해야 합니다.

로그인을 수행하는 방법은 다음의 두 가지가 있습니다.

- **전체 페이지 리디렉션**
  - SPA를 맨 처음 로드할 때 세션이 없거나 만료된 경우 사용자를 로그인 페이지로 리디렉션합니다. 사용자의 브라우저는 로그인 페이지를 방문하여 사용자 세션이 포함된 쿠키를 표시한 다음, 조각 내 코드와 토큰을 사용하여 애플리케이션으로 다시 리디렉션합니다.
  - 이 리디렉션이 발생하면 SPA가 두 번 로드됩니다. 앱이 완전히 두 번 다운로드되지 않도록 SPA 캐싱에 대한 모범 사례를 따릅니다.
  - 앱이 완전히 압축을 풀고 JavaScript 페이로드를 실행하기 전에 로그인 세션을 확인하고 로그인 페이지로 리디렉션하는 미리 로드 시퀀스를 앱에 포함시키는 것이 좋습니다.
- **팝업**
  - 전체 페이지 리디렉션의 UX(사용자 환경)가 애플리케이션에 대해 작동하지 않는 경우, 팝업을 사용하여 인증을 처리하는 것이 좋습니다.
  - 인증 후 팝업이 애플리케이션에 대한 리디렉션을 완료하면 리디렉션 처리기의 코드는 사용할 애플리케이션에 대한 코드와 토큰을 로컬 스토리지에 저장합니다. MSAL.js는 대부분의 라이브러리와 마찬가지로 인증을 위한 팝업을 지원합니다.
  - 브라우저는 팝업에 대한 지원을 줄이고 있기 때문에 가장 안정적인 옵션이 아닐 수도 있습니다. 브라우저 요구 사항을 충족하려면 팝업을 만들기 전에 SPA와 사용자 상호 작용이 필요할 수 있습니다.

> [!NOTE]
> Apple은 타사 쿠키에 대한 액세스 권한을 원래의 창에 제공하는 임시 호환성 수정으로서 [팝업 방법을 설명](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)합니다. Apple은 나중에 이러한 권한 이전을 제거할 수 있지만 본 지침에는 영향을 미치지 않습니다. 여기서는 세션이 검색되고 인증 코드를 제공할 수 있도록 로그인 페이지에 대한 첫 번째 파티 탐색으로 팝업이 사용됩니다. 이러한 작업은 앞으로도 계속 진행되어야 합니다.

### <a name="a-note-on-iframe-apps"></a>iframe 앱에 대한 참고

웹 앱의 일반적인 패턴은 iframe을 사용하여 하나의 앱을 다른 앱 내부에 포함시키는 것입니다. 최상위 프레임은 사용자 인증을 처리하며 iframe에 호스트된 애플리케이션은 사용자가 로그인되었음을 신뢰하면서 암시적 흐름을 사용하여 토큰을 자동으로 가져올 수 있습니다. 타사 쿠키를 차단하는 경우 자동 토큰 획득이 더 이상 작동하지 않음 - iframe에 포함된 애플리케이션은 로그인 페이지로 이동할 수 없으므로 팝업을 사용하여 사용자의 세션에 액세스하도록 전환해야 합니다.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>브라우저에서 새로 고침 토큰의 보안 관련 사항

브라우저에 새로 고침 토큰을 발급하는 것은 보안 문제로 간주됩니다. 사이트 간 스크립팅(XSS) 공격 또는 손상된 JS 패키지는 새로 고침 토큰을 도용할 수 있으며 만료되거나 해지될 때까지 이 토큰을 원격으로 사용할 수 있습니다. 도용된 새로 고침 토큰의 위험을 최소화하기 위해 24시간 동안만 유효한 토큰이 SPA에 발급됩니다. 24시간 후 앱은 로그인 페이지에 대한 최상위 프레임 방문을 통해 새 인증 코드를 획득해야 합니다.

수명이 제한된 이 새로 고침 토큰 패턴은 보안과 성능이 저하된 UX 간의 균형으로 선택되었습니다. 새로 고침 토큰 또는 타사 쿠키가 없다면 새 토큰 또는 추가 토큰이 필요할 때 ([OAuth 보안 모범 사례 초안](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)에서 권장하는) 인증 코드 흐름은 번거로워집니다. 토큰이 만료될 때마다(Microsoft ID 플랫폼 토큰의 경우에는 대체로 1시간마다) 모든 단일 토큰에 대해 전체 페이지 리디렉션 또는 팝업이 필요합니다.

## <a name="next-steps"></a>다음 단계

인증 코드 흐름 및 JavaScript v2.0용 MSAL(Microsoft 인증 라이브러리)에 대한 자세한 내용은 다음을 참조하세요.

- [인증 코드 흐름](v2-oauth2-auth-code-flow.md).
- [MSAL.js 2.0 빠른 시작](quickstart-v2-javascript-auth-code.md).
