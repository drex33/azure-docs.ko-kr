---
title: SPA(단일 페이지 애플리케이션) 등록 | Azure
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드하는 방법 알아보기(앱 등록)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2021
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 6b7c54dcac287574559a135b664e19f1150b80bc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987084"
---
# <a name="single-page-application-app-registration"></a>단일 페이지 애플리케이션: 앱 등록

Microsoft ID 플랫폼에서 SPA(단일 페이지 애플리케이션)를 등록하려면 다음 단계를 완료합니다. 암시적 허용 흐름을 지원하는 MSAL.js 1.0 및 PKCE를 사용하는 인증 코드 흐름을 지원하는 MSAL.js 2.0의 등록 단계는 서로 다릅니다.

## <a name="create-the-app-registration"></a>앱 등록 만들기

MSAL.js 1.0 및 2.0 기반 애플리케이션의 경우 둘 다 먼저 다음 단계를 완료하여 초기 앱 등록을 만듭니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
1. 애플리케이션에 **지원되는 계정 형식** 을 선택합니다. **리디렉션 URI** 를 입력하면 **안 됩니다**. 다른 계정 형식에 대한 설명은 [애플리케이션 등록](quickstart-register-app.md)을 참조하세요.
1. **등록** 을 선택하여 앱 등록을 만듭니다.

그런 다음, **리디렉션 URI** 로 앱 등록을 구성하여 Microsoft ID 플랫폼이 보안 토큰과 함께 클라이언트를 리디렉션해야 하는 위치를 지정합니다. 애플리케이션에서 사용 중인 MSAL.js 버전에 해당하는 단계를 사용합니다.

- [인증 코드 흐름을 사용하는 MSAL.js 2.0](#redirect-uri-msaljs-20-with-auth-code-flow)(권장)
- [암시적 흐름을 사용하는 MSAL.js 1.0](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>리디렉트 URI: [인증 코드 흐름을 사용하는 MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

MSAL.js 2.0 이상을 사용하는 앱의 리디렉션 URI를 추가하려면 다음 단계를 따르세요. MSAL.js 2.0보다 높은 버전에서는 [브라우저의 타사 쿠키 제한](reference-third-party-cookies-spas.md)에 대한 대응으로 PKCE 및 CORS를 사용하여 인증 코드 흐름을 지원합니다. 암시적 허용 흐름은 MSAL.js 2.0보다 높은 버전에서 지원되지 않습니다.

1. Azure Portal에서 이전에 [앱 등록 만들기](#create-the-app-registration)에서 만든 앱 등록을 선택합니다.
1. **관리** 에서 **인증** > **플랫폼 추가** 를 선택합니다.
1. **웹 애플리케이션** 에서 **단일 페이지 애플리케이션** 타일을 선택합니다.
1. **리디렉션 URI** 에서 [리디렉션 URI](reply-url.md)를 입력합니다. **암시적 허용 및 하이브리드 흐름** 에서 확인란을 선택하면 **안됩니다**.
1. **구성** 을 선택하여 리디렉션 URI 추가를 마칩니다.

이제 SPA(단일 페이지 애플리케이션)의 등록을 완료하고 클라이언트가 리디렉션되고 보안 토큰이 전송될 리디렉션 URI를 구성했습니다. **플랫폼 추가** 창에서 **단일 페이지 애플리케이션** 타일을 사용하여 리디렉션 URI를 구성하면 애플리케이션 등록은 PKCE 및 CORS를 사용하여 인증 코드 흐름을 지원하도록 구성됩니다.

추가 지침을 보려면 [자습서](tutorial-v2-javascript-auth-code.md)를 따르세요.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>리디렉션 URI: [암시적 흐름을 사용하는 MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

MSAL.js 1.3 이하 및 암시적 허용 흐름을 사용하는 단일 페이지 앱에 대한 리디렉션 URI를 추가하려면 다음 단계를 수행합니다. MSAL.js 1.3 이전 버전을 사용하는 애플리케이션은 인증 코드 흐름을 지원하지 않습니다.

1. Azure Portal에서 이전에 [앱 등록 만들기](#create-the-app-registration)에서 만든 앱 등록을 선택합니다.
1. **관리** 에서 **인증** > **플랫폼 추가** 를 선택합니다.
1. **웹 애플리케이션** 에서 **단일 페이지 애플리케이션** 타일을 선택합니다.
1. **리디렉션 URI** 에서 [리디렉션 URI](reply-url.md)를 입력합니다.
1. **암시적 허용 및 하이브리드 흐름** 사용:
    - 애플리케이션에서 사용자를 로그인하는 경우 **ID 토큰** 을 선택합니다.
    - 애플리케이션에서 보호된 웹 API를 호출해야 하는 경우에는 **액세스 토큰** 을 선택합니다. 이러한 토큰 형식에 대한 자세한 내용은 [ID 토큰](id-tokens.md) 및 [액세스 토큰](access-tokens.md)을 참조하세요.
1. **구성** 을 선택하여 리디렉션 URI 추가를 마칩니다.

이제 SPA(단일 페이지 애플리케이션)의 등록을 완료하고 클라이언트가 리디렉션되고 보안 토큰이 전송될 리디렉션 URI를 구성했습니다. **ID 토큰** 및 **액세스 토큰** 중 하나 또는 둘 다를 선택하여 암시적 허용 흐름을 사용하도록 설정했습니다.

## <a name="note-about-authorization-flows"></a>권한 부여 흐름에 대한 참고 사항

기본적으로 단일 페이지 애플리케이션 플랫폼 구성을 사용하여 만든 앱 등록은 인증 코드 흐름을 사용하도록 설정합니다. 이 흐름을 활용하려면 애플리케이션에서 MSAL.js 2.0 이상을 사용해야 합니다.

앞에서 설명한 것처럼 MSAL.js 1.3을 사용하는 단일 페이지 애플리케이션은 암시적 허용 흐름으로 제한됩니다. 현재 [OAuth 2.0 모범 사례](v2-oauth2-auth-code-flow.md)에서는 SPA에 대한 암시적 흐름이 아닌 인증 코드 흐름을 사용하도록 권장합니다. 또한 수명이 제한된 새로 고침 토큰을 사용하면 애플리케이션이 Safari ITP와 같은 [최신 브라우저 쿠키 프라이버시 제한](reference-third-party-cookies-spas.md)에 맞게 조정됩니다.

앱 등록으로 표시되는 모든 프로덕션 단일 페이지 애플리케이션이 MSAL.js 2.0 및 인증 코드 흐름을 사용하는 경우 Azure Portal의 앱 등록 **인증** 창에서 암시적 허용 설정을 선택 취소합니다. 그러나 암시적 흐름이 사용하도록 설정(선택)된 상태로 두면 MSAL.js 1.x 및 암시적 흐름을 사용하는 애플리케이션은 계속 작동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이전 단계에서 생성한 앱 등록을 사용하도록 앱 코드를 구성합니다. [앱의 코드 구성](scenario-spa-app-configuration.md).
