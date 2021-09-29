---
title: '자습서: 단일 페이지 애플리케이션 등록'
titleSuffix: Azure AD B2C
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C에서 SPA(단일 페이지 애플리케이션)를 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 82454e41ea9895c8006ac179a7c731a57a0c5c3b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581649"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 SPA(단일 페이지 애플리케이션) 등록

[애플리케이션](application-types.md)이 Azure AD B2C(Azure Active Directory B2C)와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 "SPA"(단일 페이지 애플리케이션)를 등록하는 방법을 보여줍니다.

## <a name="overview-of-authentication-options"></a>인증 옵션 개요

많은 최신 웹 애플리케이션이 클라이언트 쪽 "SPA"(단일 페이지 애플리케이션)로 빌드됩니다. 개발자는 JavaScript 또는 Angular, Vue, React 같은 SPA 프레임워크를 사용하여 단일 페이지 애플리케이션을 작성합니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다.

Azure AD B2C는 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스하는 다음과 같은 **두 가지** 옵션을 제공합니다.

### <a name="authorization-code-flow-with-pkce"></a>권한 부여 코드 흐름(PKCE 사용)
- [OAuth 2.0 인증 코드 흐름(PKCE 사용)](./authorization-code-flow.md). 권한 부여 코드 흐름을 사용하면 인증된 사용자를 나타내는 **ID** 토큰과 보호된 API를 호출하는 데 필요한 **액세스** 토큰의 권한 부여 코드를 애플리케이션에서 교환할 수 있습니다. 뿐만 아니라 애플리케이션은 해당 사용자와의 상호 작용을 요구하지 않고 사용자 대신 리소스에 대한 장기 액세스 권한을 제공하는 **새로 고침** 토큰을 반환합니다. 

이 방법을 사용하는 것이 **좋습니다**. 수명이 제한된 새로 고침 토큰을 사용하면 애플리케이션이 Safari ITP와 같은 [최신 브라우저 쿠키 프라이버시 제한](../active-directory/develop/reference-third-party-cookies-spas.md)에 맞게 조정됩니다.

이 흐름을 활용하려면 애플리케이션에서 이를 지원하는 인증 라이브러리(예: [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))를 사용하면 됩니다. 

![단일 페이지 애플리케이션 인증](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>암시적 권한 부여 흐름
- [OAuth 2.0 암시적 흐름](implicit-flow-single-page-application.md). [MSAL.js 1.X](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)와 같은 일부 프레임워크에서는 암시적 권한 부여 흐름만 지원합니다. 암시적 권한 부여 흐름을 사용하면 애플리케이션에서 **ID** 및 **액세스** 토큰을 얻을 수 있습니다. 권한 부여 코드 흐름과 달리, 암시적 허용 흐름은 **새로 고침 토큰** 을 반환하지 않습니다. 

![단일 페이지 애플리케이션 암시적](./media/tutorial-single-page-app/spa-app.svg)

이 인증 흐름에는 Electron 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다. 이러한 시나리오에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-the-spa-application"></a>SPA 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *spaapp1* 을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다.
1. **리디렉션 URI** 에서 **SPA(단일 페이지 애플리케이션)** 를 선택한 다음, URL 텍스트 상자에 `https://jwt.ms`를 입력합니다.

    리디렉션 URI는 사용자와의 상호 작용을 완료한 후 권한 부여 서버(이 경우에는 Azure AD B2C)에서 사용자를 보내고, 성공적인 권한 부여 시 액세스 토큰 또는 권한 부여 코드를 전송하는 엔드포인트입니다. 프로덕션 애플리케이션에서는 일반적으로 `https://contoso.com/auth-response`와 같이 앱이 실행되는 공개적으로 액세스할 수 있는 엔드포인트입니다. 이 자습서와 같은 테스트 목적으로 토큰을 디코딩된 토큰 콘텐츠를 표시하는 Microsoft 소유의 웹 애플리케이션 `https://jwt.ms`로 설정할 수 있습니다(토큰의 콘텐츠가 브라우저에서 벗어나면 안 됨). 앱을 개발하는 동안 애플리케이션이 로컬에서 수신 대기하는 엔드포인트(예: `http://localhost:5000`)를 추가할 수 있습니다. 언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다.

    리디렉션 URI에는 다음 제한 사항이 적용됩니다.

    * `localhost`를 사용하지 않는 이상, 회신 URL은 `https` 스키마로 시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분하여 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록** 을 선택합니다.


## <a name="enable-the-implicit-flow"></a>암시적 흐름 사용
암시적 흐름을 사용하는 경우 앱 등록에서 암시적 권한 부여 흐름을 사용하도록 설정해야 합니다.

1. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다.
1. **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장** 을 선택합니다.

## <a name="migrate-from-the-implicit-flow"></a>암시적 흐름에서 마이그레이션

암시적 흐름을 사용하는 기존 애플리케이션이 있는 경우 해당 애플리케이션을 지원하는 프레임워크(예: [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))를 사용하여 권한 부여 코드 흐름을 사용하도록 마이그레이션하는 것이 좋습니다.

앱 등록을 통해 나타낸 모든 프로덕션 단일 페이지 애플리케이션에서 권한 부여 코드 흐름을 사용하는 경우 암시적 권한 부여 흐름 설정을 해제합니다. 

1. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다.
1. **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 확인란을 모두 선택 취소합니다.
1. **저장** 을 선택합니다.

암시적 흐름을 사용하도록(선택한 상태로) 두면 암시적 흐름을 사용하는 애플리케이션이 계속 작동할 수 있습니다.

* * *

## <a name="next-steps"></a>다음 단계

다음으로 사용자가 자신의 프로필을 등록, 로그인 및 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
