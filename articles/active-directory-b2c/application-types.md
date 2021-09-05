---
title: Azure AD B2C에서 지원하는 애플리케이션 형식
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에 사용할 수 있는 애플리케이션 형식을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6e4f54f736a47cc6b351ff02b8fb7f8bad1c5c56
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552697"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Active Directory B2C에서 사용할 수 있는 애플리케이션 형식
 
Azure AD B2C(Azure Active Directory B2C)는 다양한 최신 애플리케이션 아키텍처의 인증을 지원합니다. 모두 업계 표준 프로토콜인 [OAuth 2.0](protocols-overview.md) 또는 [OpenID Connect](protocols-overview.md)를 기반으로 합니다. 이 문서에서는 선호하는 언어 또는 플랫폼에 독립적으로 빌드할 수 있는 애플리케이션 형식을 설명합니다. 또한 애플리케이션 빌드를 시작하기 전에 대략적인 시나리오에 대한 이해를 돕습니다.

Azure AD B2C를 사용하는 모든 애플리케이션은 [Azure Portal](https://portal.azure.com/)을 사용하여 [Azure AD B2C 테넌트](tutorial-create-tenant.md)에 등록해야 합니다. 애플리케이션 등록 프로세스는 다음과 같은 값을 수집하고 할당합니다.

* 애플리케이션을 고유하게 식별하는 **애플리케이션 ID**
* 응답을 다시 애플리케이션으로 보내는 데 사용할 수 있는 **회신 URL**

Azure AD B2C에 전송되는 각 요청은 Azure AD B2C의 동작을 제어하는 **사용자 흐름**(기본 제공 정책) 또는 **사용자 지정 정책** 을 지정합니다. 두 정책 형식 모두 사용자 환경의 사용자 지정할 수 있는 집합을 만들 수 있습니다.

모든 애플리케이션의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 애플리케이션이 사용자를 v2.0 엔드포인트로 보내어 [정책](user-flow-overview.md)을 실행합니다.
2. 사용자는 정책 정의에 따라 정책을 완료합니다.
3. 애플리케이션은 v2.0 엔드포인트에서 보안 토큰을 수신합니다.
4. 애플리케이션이 보안 토큰을 사용하여 보호된 정보 또는 보호된 리소스에 액세스합니다.
5. 리소스 서버가 보안 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
6. 애플리케이션은 주기적으로 보안 토큰을 새로 고칩니다.

이러한 단계는 빌드 중인 애플리케이션의 유형에 따라 약간씩 다를 수 있습니다.

## <a name="web-applications"></a>웹 애플리케이션

서버에서 호스트되며 브라우저를 통해 액세스하는 웹 애플리케이션(.NET, PHP, Java, Ruby, Python, Node.js 등)의 경우, Azure AD B2C는 모든 사용자 환경에 [OpenID Connect](protocols-overview.md)를 지원합니다. OpenID Connect의 Azure AD B2C 구현에서 웹 애플리케이션은 Azure AD로 인증 요청을 발급하여 사용자 환경을 시작합니다. 요청의 결과는 `id_token`입니다. 이 보안 토큰은 사용자의 ID를 나타냅니다. 또한 클레임 형태로 사용자에 대한 정보를 제공합니다.

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

[Azure AD B2C 토큰 참조](tokens-overview.md)에서 애플리케이션이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

웹 애플리케이션에서 [정책](user-flow-overview.md)의 각 실행에서는 이러한 수준 높은 단계를 수행합니다.

1. 사용자가 웹 애플리케이션으로 이동합니다.
2. 웹 애플리케이션에서 사용자를 Azure AD B2C로 리디렉션하여 실행할 정책을 나타냅니다.
3. 사용자가 정책을 완성합니다.
4. Azure AD B2C에서 `id_token`을 브라우저에 반환합니다.
5. `id_token`이 리디렉션 URI에 게시됩니다.
6. `id_token`의 유효성이 검사되고 세션 쿠키가 설정됩니다.
7. 보안 페이지가 사용자에게 반환됩니다.

Azure AD에서 수신한 공개 서명 키를 사용하여 `id_token` 의 유효성을 확인하는 것으로 사용자의 ID를 충분히 확인할 수 있습니다. 이 프로세스는 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키도 설정합니다.

이 시나리오의 작동 방식을 확인하려면 [시작 섹션](overview.md)의 웹 애플리케이션 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 애플리케이션은 백 엔드 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우, 웹 애플리케이션은 약간 다른 [OpenID Connect 흐름](openid-connect.md)을 수행하고 권한 부여 코드를 사용하여 토큰을 획득하며 토큰을 새로 고칠 수 있습니다. 이 시나리오는 다음 [Web API 섹션](#web-apis)에서 설명합니다.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션
많은 최신 웹 애플리케이션이 클라이언트 쪽 "SPA"(단일 페이지 애플리케이션)로 빌드됩니다. 개발자는 JavaScript 또는 Angular, Vue, React 같은 SPA 프레임워크를 사용하여 단일 페이지 애플리케이션을 작성합니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다.

Azure AD B2C는 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스하는 다음과 같은 **두 가지** 옵션을 제공합니다.

### <a name="authorization-code-flow-with-pkce"></a>권한 부여 코드 흐름(PKCE 사용)
- [OAuth 2.0 인증 코드 흐름(PKCE 사용)](./authorization-code-flow.md). 권한 부여 코드 흐름을 사용하면 인증된 사용자를 나타내는 **ID** 토큰과 보호된 API를 호출하는 데 필요한 **액세스** 토큰의 권한 부여 코드를 애플리케이션에서 교환할 수 있습니다. 뿐만 아니라 애플리케이션은 해당 사용자와의 상호 작용을 요구하지 않고 사용자 대신 리소스에 대한 장기 액세스 권한을 제공하는 **새로 고침** 토큰을 반환합니다. 

이 방법을 사용하는 것이 **좋습니다**. 수명이 제한된 새로 고침 토큰을 사용하면 애플리케이션이 Safari ITP와 같은 [최신 브라우저 쿠키 프라이버시 제한](../active-directory/develop/reference-third-party-cookies-spas.md)에 맞게 조정됩니다.

이 흐름을 활용하기 위해, 애플리케이션에서 이를 지원하는 인증 라이브러리(예: [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))를 사용할 수 있습니다.

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![단일 페이지 애플리케이션 인증](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>암시적 권한 부여 흐름
- [OAuth 2.0 암시적 흐름](implicit-flow-single-page-application.md). [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) 같은 일부 프레임워크는 암시적 권한 부여 흐름만 지원합니다. 암시적 권한 부여 흐름을 사용하면 애플리케이션에서 **ID** 및 **액세스** 토큰을 얻을 수 있습니다. 권한 부여 코드 흐름과 달리, 암시적 허용 흐름은 **새로 고침 토큰** 을 반환하지 않습니다. 

이 인증 흐름에는 Electron 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다. 이러한 시나리오에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요합니다.

## <a name="web-apis"></a>Web API

Azure AD B2C를 사용하여 애플리케이션의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. Web API는 토큰을 사용하는 들어오는 HTTP 요청을 인증하여 해당 데이터를 보호하는 데 OAuth 2.0을 사용할 수 있습니다. Web API 호출자는 HTTP 요청의 권한 부여 헤더에 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

그러면 Web API는 토큰을 사용하여 API 호출자의 ID를 확인하고 토큰에 인코드된 클레임에서 호출자에 대한 정보를 추출할 수 있습니다. [Azure AD B2C 토큰 참조](tokens-overview.md)에서 앱이 사용할 수 있는 토큰 및 클레임 유형에 대해 알아볼 수 있습니다.

Web API는 웹 애플리케이션, 데스크톱 및 모바일 애플리케이션, 단일 페이지 애플리케이션, 서버 쪽 데몬 및 다른 Web API까지 포함하여 많은 유형의 클라이언트에서 토큰을 받을 수 있습니다. 다음은 Web API를 호출하는 웹 애플리케이션에 대한 전체 흐름을 보여주는 예입니다.

1. 웹 애플리케이션에서 정책을 실행하고, 사용자는 사용자 환경을 완료합니다.
2. Azure AD B2C에서 (OpenID Connect) `id_token`과 인증 코드를 브라우저에 반환합니다.
3. 브라우저에서 `id_token` 및 권한 부여 코드를 리디렉션 URI에 게시합니다.
4. 웹 서버에서 `id_token`의 유효성을 검사하고 세션 쿠키를 설정합니다.
5. 웹 서버는 인증 코드, 애플리케이션 클라이언트 ID 및 클라이언트 자격 증명을 제공하여 Azure AD B2C에 `access_token`을 요청합니다.
6. `access_token` 및 `refresh_token`이 웹 서버에 반환됩니다.
7. 웹 API가 권한 부여 헤더에서 `access_token`을 사용하여 호출됩니다.
8. 웹 API에서 토큰의 유효성을 검사합니다.
9. 보안 데이터가 웹 애플리케이션에 반환됩니다.

권한 부여 코드, 새로 고침 토큰 및 토큰을 가져오는 단계에 대한 자세한 내용은 [OAuth 2.0 프로토콜](authorization-code-flow.md)을 참조하세요.

Azure AD B2C를 사용하여 Web API를 보호하는 방법을 알아보려면 [시작 섹션](overview.md)에서 Web API 자습서를 확인하세요.

## <a name="mobile-and-native-applications"></a>모바일 및 네이티브 애플리케이션

모바일 및 데스크톱 애플리케이션과 같은 디바이스에 설치된 애플리케이션은 사용자 대신 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 네이티브 애플리케이션에 사용자 지정된 ID 관리 환경을 추가하고 Azure AD B2C 및 [OAuth 2.0 권한 부여 코드 흐름](authorization-code-flow.md)을 사용하여 안전하게 백 엔드 서비스를 호출할 수 있습니다.

이 흐름에서 애플리케이션은 [정책](user-flow-overview.md)을 실행하고 사용자가 정책을 완료하면 Azure AD에서 `authorization_code`를 수신합니다. `authorization_code`는 현재 로그인한 사용자를 대신하여 백 엔드 서비스를 호출할 애플리케이션의 사용 권한을 나타냅니다. 그러면 애플리케이션은 백그라운드에서 `access_token` 및 `refresh_token`에 대한 `authorization_code`를 교환할 수 있습니다.  애플리케이션은 HTTP 요청에서 백 엔드 Web API를 인증하는 데 `access_token`을 사용할 수 있습니다. 또한 이전 항목이 만료된 경우 `refresh_token`을 사용하여 새 `access_token`을 가져올 수도 있습니다.

## <a name="current-limitations"></a>현재 제한 사항

### <a name="unsupported-application-types"></a>지원되지 않는 애플리케이션 유형

#### <a name="daemonsserver-side-applications"></a>디먼/서버 쪽 애플리케이션

장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 애플리케이션은 Web API와 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 애플리케이션은 사용자의 위임된 ID 대신 애플리케이션의 ID를 사용하거나 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 인증하고 토큰을 가져올 수 있습니다. 클라이언트 자격 증명 흐름은 대리 흐름과 동일하지 않으며 대리 흐름은 서버 간 인증에 사용하지 않아야 합니다.

OAuth 2.0 클라이언트 자격 증명 권한 부여 흐름은 현재 Azure AD B2C 인증 서비스를 통해 직접 지원되지 않지만, Azure AD B2C 테넌트에서 애플리케이션에 대해 Azure AD 및 Microsoft ID 플랫폼/토큰 https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token) 엔드포인트를 사용하여 클라이언트 자격 증명 흐름을 설정할 수 있습니다. Azure AD B2C 테넌트는 Azure AD Enterprise 테넌트와 일부 기능을 공유합니다.

클라이언트 자격 증명 흐름을 설정하려면 [Azure Active Directory v2.0 및 OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 참조하세요. 인증에 성공하면 [Azure AD 토큰 참조](../active-directory/develop/id-tokens.md)에 설명된 대로 Azure AD에서 사용할 수 있도록 형식이 지정된 토큰을 수신합니다.

관리 애플리케이션 등록 지침은 [Microsoft Graph를 사용하여 Azure AD B2C 관리](microsoft-graph-get-started.md)를 참조하세요.

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web API 체인(On-Behalf-Of 흐름)

많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 웹 API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적으로 수행되는 이 시나리오에서는 Microsoft Graph API 등의 Microsoft 온라인 서비스를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다.  그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C에서 사용자 흐름](user-flow-overview.md)을 통해 제공되는 기본 제공 정책에 대한 자세한 내용을 알아봅니다.
