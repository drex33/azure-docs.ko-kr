---
title: Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 구현된 OAuth 2.0 인증 프로토콜을 사용하여 웹 애플리케이션을 빌드합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9f7cb3e5869070e8120e39b21c2708887505043c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050264"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름

OAuth 2.0 인증 코드 권한은 디바이스에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. Microsoft ID 플랫폼에서 구현된 OAuth 2.0과 OIDC(Open ID Connect)를 사용하여, 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다.

이 문서에서는 임의 언어를 사용하여 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하면 [토큰을 획득하고 보안 Web API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749)에서 설명합니다. OIDC와 함께 이것은 [단일 페이지 앱](v2-app-types.md#single-page-apps-javascript), [웹앱](v2-app-types.md#web-apps) 및 [기본적으로 설치된 앱](v2-app-types.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다. 이 흐름을 사용하면 앱이 Microsoft ID 플랫폼에서 보호되는 리소스에 액세스하는 데 사용할 수 있는 access_token뿐만 아니라 로그인한 사용자에 대한 추가 access_token 및 ID 토큰을 가져오기 위한 새로 고침 토큰도 안전하게 획득할 수 있습니다.

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>프로토콜 다이어그램

높은 수준에서 애플리케이션에 대한 전체 인증 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>단일 페이지 앱에 필요한 리디렉션 URI 설정

단일 페이지 애플리케이션에 대한 인증 코드 흐름에는 몇 가지 추가 설정이 필요합니다.  [단일 페이지 애플리케이션 만들기](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)에 관한 지침에 따라 리디렉션 URI를 CORS에 사용하도록 설정된 것으로 올바르게 표시합니다. CORS를 사용하도록 기존 리디렉션 URI를 업데이트하려면 매니페스트 편집기를 열고 `replyUrlsWithType` 섹션에서 리디렉션 URI의 `type` 필드를 `spa`로 설정합니다. 인증 탭의 “웹” 섹션에서 리디렉션 URI를 클릭하고 권한 부여 코드 흐름을 사용하여 마이그레이션하려는 URI를 선택할 수도 있습니다.

`spa` 리디렉션 형식은 이전 버전의 암시적 흐름과 호환됩니다. 현재 암시적 흐름을 사용하여 토큰을 가져오는 앱은 이슈 없이 `spa` 리디렉션 URI 형식으로 이동하고 암시적 흐름을 계속 사용할 수 있습니다.

인증 코드 흐름을 사용하려고 하면 다음 오류가 표시됩니다.

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

그런 다음, 앱 등록을 방문하고 앱의 리디렉션 URI를 `spa` 형식으로 업데이트합니다.

애플리케이션은 비 SPA 흐름(예: 원시 애플리케이션 또는 클라이언트 자격 증명 흐름)에서 `spa` 리디렉션 URI를 사용할 수 없습니다. `Origin` 헤더 없이 `spa` 리디렉션 URI를 사용하려고 하면 보안 및 모범 사례를 보장하기 위해 Microsoft ID 플랫폼에서 오류가 반환됩니다. 마찬가지로, Microsoft ID 플랫폼은 `Origin` 헤더가 있을 경우 OBO 흐름, 클라이언트 자격 증명 흐름, 인증 코드 흐름에서 클라이언트 자격 증명 사용을 차단하여 브라우저 내에서 비밀이 사용되지 않도록 합니다. 

## <a name="request-an-authorization-code"></a>인증 코드 요청

인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 엔드포인트로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 `openid`, `offline_access` 및 `https://graph.microsoft.com/mail.read ` 권한을 요청합니다.  예를 들어, `Directory.ReadWrite.All`을 사용하여 조직의 디렉터리에 데이터를 쓰는 것과 같은 일부 권한은 관리자가 제한합니다. 애플리케이션이 조직 사용자에게 이러한 사용 권한 중 하나에 대한 액세스를 요청하는 경우 사용자에게는 앱의 사용 권한에 동의할 권한이 부여되지 않음을 나타내는 오류 메시지가 표시됩니다. 관리자 제한 범위에 대한 액세스를 요청하려면 글로벌 관리자에게 직접 요청해야 합니다.  자세한 내용은 [관리자 제한 권한](v2-permissions-and-consent.md#admin-restricted-permissions)을 참조하세요.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `code` 과 함께 `http://localhost/myapp/` 으로 리디렉션됩니다.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 매개 변수    | 필수/선택 | Description |
|--------------|-------------|--------------|
| `tenant`    | required    | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. 한 테넌트의 사용자를 다른 테넌트로 로그인하는 게스트 시나리오의 경우, 리소스 테넌트로 올바르게 로그인하려면 테넌트 식별자를 ‘제공해야 합니다’.|
| `client_id`   | required    | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID** 입니다.  |
| `response_type` | required    | 인증 코드 흐름에 대한 `code`를 포함해야 합니다. [하이브리드 흐름](#request-an-id-token-as-well-hybrid-flow)을 사용하는 경우 `id_token`이나 `token`도 포함할 수 있습니다. |
| `redirect_uri` | 필수 | 앱에서 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. 원시 앱과 모바일 앱의 경우 권장되는 값 `https://login.microsoftonline.com/common/oauth2/nativeclient`(포함된 브라우저를 사용하는 앱의 경우)나 `http://localhost`(시스템 브라우저를 사용하는 앱의 경우) 중 하나를 사용해야 합니다. |
| `scope`  | required    | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  요청의 `/authorize` 레그에서는 여러 리소스를 포함할 수 있으므로 앱은 사용자가 호출하려는 여러 웹 API에 대한 동의를 받을 수 있습니다. |
| `response_mode`   | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 다음 중 하나일 수 있습니다.<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`는 리디렉션 URI에 코드를 쿼리 문자열 매개 변수로 제공합니다. 암시적 흐름을 사용하여 ID 토큰을 요청하는 경우 `query`는 [OpenID 사양](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)에서 명시한 대로 사용할 수 없습니다. 코드만 요청하는 경우 `query`, `fragment` 또는 `form_post`를 사용할 수 있습니다. `form_post`는 리디렉션 URI에 대한 코드가 포함된 POST를 실행합니다. |
| `state`                 | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 이 값은 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
| `prompt`  | 선택 사항    | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 `login`, `none`, `consent`, `select_account`뿐입니다.<br/><br/>- `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다.<br/>- `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 Microsoft ID 플랫폼은 `interaction_required` 오류를 반환합니다.<br/>- `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다.<br/>- `prompt=select_account`는 세션의 모든 계정 또는 저장된 계정을 나열하는 계정 선택 환경이나 다른 계정을 모두 사용하도록 선택하는 옵션을 제공하면서 Single Sign-On을 중단시킵니다.<br/> |
| `login_hint` | 옵션 | 사용자 이름을 미리 알고 있는 경우 이 매개 변수를 사용하여 사용자를 위해 로그인 페이지의 사용자 이름 및 전자 메일 주소 필드를 미리 채울 수 있습니다. 대개 앱은 이전 로그인에서 `login_hint` [선택적 클레임](active-directory-optional-claims.md)을 이미 추출한 후 재인증 과정에서 이 매개 변수를 사용합니다. |
| `domain_hint`  | 선택 사항    | 포함된 경우, 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 이메일 기반 검색 프로세스를 건너뜁니다(예: 페더레이션 ID 공급자로 보내기). 앱이 이전 로그인 작업에서 `tid` 를 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다.  |
| `code_challenge`  | 권장/필수 | PKCE(Proof Key for Code Exchange)를 통해 권한 부여 코드를 보호하는 데 사용됩니다. `code_challenge_method`가 포함되면 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. 이제 모든 애플리케이션 형식(퍼블릭 및 기밀 클라이언트 모두)에 권장되며 [권한 부여 코드 흐름을 사용하는 단일 페이지 앱](reference-third-party-cookies-spas.md)의 Microsoft ID 플랫폼에 필요합니다. |
| `code_challenge_method` | 권장/필수 | `code_challenge` 매개 변수에 대한 `code_verifier`를 인코딩하는 데 사용되는 메서드입니다. 이는 `S256`*이어야* 하지만, 특정 이유로 클라이언트에서 SHA256을 지원할 수 없으면 사양에 따라 `plain`을 사용할 수 있습니다. <br/><br/>제외할 경우 `code_challenge`가 포함되면 `code_challenge`가 일반 텍스트로 간주됩니다. Microsoft ID 플랫폼은 `plain` 및 `S256`을 모두 지원합니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. [인증 코드 흐름을 사용하는 단일 페이지 앱](reference-third-party-cookies-spas.md)에 필요합니다.|


이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. Microsoft ID 플랫폼은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](v2-permissions-and-consent.md).

사용자가 인증하고 동의하면 Microsoft ID 플랫폼이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=query` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 매개 변수 | Description  |
|-----------|--------------|
| `code` | 앱이 요청한 authorization_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization_code는 수명이 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| `state` | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 state 값이 동일한지 확인해야 합니다. |

ID 토큰을 요청하고 애플리케이션 등록에서 암시적 부여를 사용하도록 설정한 경우 해당 ID 토큰도 받을 수 있습니다.  이것을 경우에 따라 [“하이브리드 흐름”](#request-an-id-token-as-well-hybrid-flow)이라고도 하며 ASP.NET과 같은 프레임워크에서 사용됩니다.

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description  |
|----------|------------------|
| `error`  | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 엔드포인트 오류에 대한 오류 코드

다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드  | Description    | 클라이언트 작업   |
|-------------|----------------|-----------------|
| `invalid_request` | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다. |
| `unauthorized_client` | 클라이언트 애플리케이션이 인증 코드를 요청할 수 없습니다. | 이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `access_denied`  | 리소스 소유자가 동의 거부  | 클라이언트 애플리케이션이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| `unsupported_response_type` | 권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다. [하이브리드 흐름](#request-an-id-token-as-well-hybrid-flow)에서 볼 때 클라이언트 앱 등록에서 ID 토큰 암시적 허용 설정을 사용하도록 설정해야 한다는 신호를 보냅니다. |
| `server_error`  | 서버에 예기치 않은 오류가 발생했습니다.| 요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 애플리케이션이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `temporarily_unavailable`   | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `invalid_resource`  | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 이 오류는 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `login_required` | 사용자가 너무 많거나 없습니다. | 클라이언트에서 자동 인증(`prompt=none`)을 요청했지만 단일 사용자를 찾을 수 없습니다. 이는 세션에서 여러 사용자가 활성 상태이거나 사용자가 없음을 의미할 수 있습니다. 이 경우 선택한 테넌트를 고려합니다. 예를 들어 두 개의 Azure AD 계정이 활성 상태이고 하나의 Microsoft 계정이 있고 `consumers`가 선택되면 자동 인증이 작동합니다. |
| `interaction_required` | 요청을 위해 사용자 상호 작용이 필요합니다. | 추가 인증 단계 또는 동의가 필요합니다. `prompt=none`을 사용하지 않고 요청을 다시 시도하세요. |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>ID 토큰도 요청(하이브리드 흐름)

권한 부여 코드를 사용하기 전에 사용자가 누구인지 알아보려면 일반적으로 애플리케이션이 권한 부여 코드를 요청할 때 ID 토큰도 요청합니다. 암시적 허용을 권한 부여 코드 흐름과 혼합하므로, *하이브리드 흐름* 이라고 합니다. 하이브리드 흐름은 코드 사용을 차단하지 않고 사용자를 위해 페이지를 렌더링하려는 웹앱, 특히 [ASP.NET](quickstart-v2-aspnet-core-webapp.md)에서 일반적으로 사용됩니다. 단일 페이지 앱과 기존 웹앱 모두 이 모델에서 대기 시간이 감소하는 혜택을 얻습니다.

하이브리드 흐름은 앞에서 설명한 권한 부여 코드 흐름과 같지만, 새 범위, 새 response_type, 새 `nonce` 쿼리 매개 변수의 세 가지 항목이 추가되며, 이 모두는 ID 토큰을 요청하는 데 필요합니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| 업데이트된 매개 변수 | 필수/선택 | 설명 |
|---------------|-------------|--------------|
|`response_type`| 필수 | `id_token`을 추가하면 애플리케이션이 `/authorize` 엔드포인트의 응답에서 ID 토큰을 원한다는 것을 서버에 나타냅니다.  |
|`scope`| 필수 | ID 토큰의 경우 ID 토큰 범위(`openid`와 선택적으로 `profile`과 `email`)를 포함하도록 업데이트해야 합니다. |
|`nonce`| 필수|     앱에서 생성하여 요청에 포함된 값이며, 결과 id_token에 클레임으로 포함됩니다. 그러면 앱에서 이 값을 확인하여 토큰 재생 공격을 완화할 수 있습니다. 이 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
|`response_mode`| 권장 | 결과 토큰을 앱으로 다시 보내는 데 사용해야 하는 메서드를 지정합니다. 권한 부여 코드의 경우 기본값은 `query`이지만, 요청에 id_token `response_type`이 포함되어 있으면 `fragment`입니다.  그러나 앱은 특히 `http://localhost`를 리디렉션 URI로 사용할 때 `form_post`를 사용하는 것이 좋습니다. |

`fragment`를 응답 모드로 사용하면 브라우저가 조각을 웹 서버로 전달하지 않기 때문에 리디렉션에서 코드를 읽는 웹앱에 이슈가 발생합니다.  이 상황에서 앱은 모든 데이터가 서버로 전송되도록 `form_post` 응답 모드를 사용해야 합니다. 

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| 매개 변수 | 설명  |
|-----------|--------------|
| `code` | 앱이 요청한 권한 부여 코드입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 권한 부여 코드는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| `id_token` | *암시적 허용* 을 통해 발급된 사용자의 ID 토큰입니다. 같은 요청에 있는 `code`의 해시인 특수 `c_hash` 클레임을 포함합니다. |
| `state` | state 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

## <a name="redeem-a-code-for-an-access-token"></a>액세스 토큰에 대한 코드 사용

모든 기밀 클라이언트는 클라이언트 비밀(Microsoft ID 플랫폼에서 생성한 대칭 공유 비밀) 및 [인증서 자격 증명](active-directory-certificate-credentials.md)(개발자가 업로드한 비대칭 키)을 사용할 수 있습니다.  최상의 보안을 위해 인증서 자격 증명을 사용하는 것이 좋습니다. 공용 클라이언트(네이티브 애플리케이션 및 단일 페이지 앱)는 인증 코드를 사용할 때 비밀 또는 인증서를 사용해서는 안 됩니다. 리디렉션 URI가 애플리케이션 유형을 올바르게 나타내고 [고유](reply-url.md#localhost-exceptions)한지 항상 확인합니다. 

### <a name="request-an-access-token-with-a-client_secret"></a>client_secret를 사용하여 액세스 토큰 요청

authorization_code를 획득하고 사용자가 사용 권한을 부여했으므로 `code`를 원하는 리소스에 대한 `access_token`으로 교환할 수 있습니다. 이렇게 하려면 `/token` 엔드포인트에 `POST` 요청을 보내면 됩니다.

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

| 매개 변수  | 필수/선택 | Description     |
|------------|-------------------|----------------|
| `tenant`   | required   | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id` | required  | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `scope`      | 선택적   | 공백으로 구분된 범위 목록입니다. 이 범위는 OIDC 범위(`profile`, `openid`, `email`)와 마찬가지로 모두 단일 리소스에 속해야 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. 권한 부여 코드 흐름의 Microsoft 확장이며, 토큰 사용 중에 앱에서 토큰을 원하는 리소스를 선언하는 데 사용합니다.|
| `code`          | required  | 흐름의 첫 번째 레그에서 얻은 authorization_code입니다. |
| `redirect_uri`  | required  | authorization_code를 획득하는 데 사용된 값과 동일한 redirect_uri 값입니다. |
| `grant_type` | required   | 인증 코드 흐름에 대한 `authorization_code` 여야 합니다.   |
| `code_verifier` | 권장  | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |
| `client_secret` | 기밀 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. 디바이스 또는 웹 페이지에 client_secrets를 안정적으로 저장할 수 없기 때문에 네이티브 앱 또는 단일 페이지 앱에서 애플리케이션 암호를 사용하면 안 됩니다. 서버 쪽에서 client_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다.  여기에 설명된 모든 매개 변수와 마찬가지로 클라이언트 암호는 전송되기 전에 URL로 인코딩되어야 하며, 이는 일반적으로 SDK에서 수행하는 단계입니다. URI 인코딩에 대한 자세한 내용은 [URI 일반 구문 사양](https://tools.ietf.org/html/rfc3986#page-12)을 참조하세요.  대신 [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-2.3.1)에 따라 권한 부여 헤더에 자격 증명을 제공하는 기본 인증 패턴도 지원됩니다. |

### <a name="request-an-access-token-with-a-certificate-credential"></a>인증서 자격 증명을 사용하여 액세스 토큰 요청

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
```

| 매개 변수  | 필수/선택 | Description     |
|------------|-------------------|----------------|
| `tenant`   | required   | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id` | required  | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `scope`      | 선택적   | 공백으로 구분된 범위 목록입니다. 이 범위는 OIDC 범위(`profile`, `openid`, `email`)와 마찬가지로 모두 단일 리소스에 속해야 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. 권한 부여 코드 흐름의 Microsoft 확장이며, 토큰 사용 중에 앱에서 토큰을 원하는 리소스를 선언하는 데 사용합니다.|
| `code`          | required  | 흐름의 첫 번째 레그에서 얻은 authorization_code입니다. |
| `redirect_uri`  | required  | authorization_code를 획득하는 데 사용된 값과 동일한 redirect_uri 값입니다. |
| `grant_type` | required   | 인증 코드 흐름에 대한 `authorization_code` 여야 합니다.   |
| `code_verifier` | 권장  | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |
| `client_assertion_type` | 기밀 웹앱에 필요 | 인증서 자격 증명을 사용하려면 값을 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`로 설정해야 합니다. |
| `client_assertion` | 기밀 웹앱에 필요  | 애플리케이션의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요.|

`client_secret` 매개 변수가 두 개의 매개 변수 `client_assertion_type` 및 `client_assertion`으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 동일합니다.  

### <a name="successful-response"></a>성공적인 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 매개 변수     | Description   |
|---------------|------------------------------|
| `access_token`  | 요청된 액세스 토큰입니다. 앱에서 이 토큰을 사용하여 보안 리소스(예: 웹 API)를 인증할 수 있습니다. |
| `token_type`    | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| `expires_in`    | 액세스 토큰이 유효한 기간(초)입니다. |
| `scope`         | access_token이 유효한 범위입니다. 선택 사항 - 표준이 아니며, 생략된 경우 토큰은 흐름의 초기 구간에서 요청된 범위에 사용됩니다. |
| `refresh_token` | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 액세스 토큰 새로 고침에 대한 자세한 내용은 [아래 섹션](#refresh-the-access-token)을 참조하세요. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다. |
| `id_token`      | JWT(JSON Web Token) 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있으며 기밀 클라이언트는 권한 부여에 사용할 수 있습니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |

### <a name="error-response"></a>오류 응답

오류 응답은 다음과 같습니다.

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수         | Description    |
|-------------------|----------------|
| `error`       | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| `error_codes` | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다.  |
| `timestamp`   | 오류가 발생한 시간입니다. |
| `trace_id`    | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `correlation_id` | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

### <a name="error-codes-for-token-endpoint-errors"></a>토큰 엔드포인트 오류에 대한 오류 코드

| 오류 코드         | Description        | 클라이언트 작업    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청 또는 앱 등록을 수정하고 요청을 다시 제출합니다.   |
| `invalid_grant`    | 권한 부여 코드 또는 PKCE 코드 확인자가 잘못되었거나 만료되었습니다. | `/authorize` 엔드포인트에 대한 새 요청을 시도하고 code_verifier 매개 변수가 잘못되었는지 확인합니다.  |
| `unauthorized_client` | 인증된 클라이언트가 이 권한 부여 유형을 사용할 권한이 없습니다. | 이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `invalid_client` | 클라이언트 인증에 실패했습니다.  | 클라이언트 자격 증명이 잘못되었습니다. 해결하려면 애플리케이션 관리자가 자격 증명을 업데이트합니다.   |
| `unsupported_grant_type` | 권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. | 요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| `invalid_resource` | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다.  |
| `interaction_required` | OIDC 사양이 `/authorize` 엔드포인트에서만 요청하므로 비표준입니다. 요청을 위해 사용자 상호 작용이 필요합니다. 예를 들어 추가 인증 단계가 필요합니다. | 같은 범위를 사용하여 `/authorize` 요청을 다시 시도합니다. |
| `temporarily_unavailable` | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 약간 연기된 후 요청을 다시 시도합니다. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
|`consent_required` | 요청하려면 사용자가 동의해야 합니다. 이 오류는 일반적으로 OIDC 사양에 따라 `/authorize` 엔드포인트에서만 반환되므로 비표준입니다. 클라이언트 앱에 요청 권한이 없는 코드 사용 흐름에서 `scope` 매개 변수를 사용하면 반환됩니다.  | 클라이언트는 동의를 트리거하기 위해 올바른 범위를 사용하여 사용자를 `/authorize` 엔드포인트로 다시 보내야 합니다. |
|`invalid_scope` | 앱에서 요청한 범위가 잘못되었습니다.  | 인증 요청에서 범위 매개 변수 값을 유효한 값으로 업데이트합니다. |

> [!NOTE]
> 단일 페이지 앱에는 '단일 페이지 애플리케이션' 클라이언트 유형에 대해서만 원본 간 토큰 상환이 허용됨을 나타내는 `invalid_request` 오류가 표시될 수 있습니다.  이것은 토큰을 요청하는 데 사용되는 리디렉션 URI가 `spa` 리디렉션 URI로 표시되지 않았음을 나타냅니다.  이 흐름을 사용하도록 설정하는 방법에 대해서는 [애플리케이션 등록 단계](#redirect-uri-setup-required-for-single-page-apps)를 검토합니다.

## <a name="use-the-access-token"></a>액세스 토큰 사용

`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>액세스 토큰 새로 고침

access_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 엔드포인트에 제출해야 하며, 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.  새로 고침 토큰은 클라이언트가 이미 동의를 받은 모든 권한에 유효합니다. 따라서 `scope=mail.read`에 대한 요청에서 발행된 새로 고침 토큰을 사용하여 `scope=api://contoso.com/api/UseResource`에 대한 새 액세스 토큰을 요청할 수 있습니다.

웹앱 및 네이티브 앱에 대한 새로 고침 토큰에는 지정된 수명이 없습니다. 일반적으로 새로 고침 토큰의 수명은 비교적 깁니다. 그러나 새로 고침 토큰이 만료되거나 해지되거나 원하는 작업을 위한 충분한 권한이 없는 경우가 있습니다. 애플리케이션은 [토큰 발급 엔드포인트에서 반환하는 오류](#error-codes-for-token-endpoint-errors)를 예상하고 정확히 처리해야 합니다. 그러나 단일 페이지 앱은 수명이 24시간인 토큰을 가져와서 매일 새 인증을 요구합니다.  타사 쿠키를 사용하도록 설정하면 iframe에서 이 작업을 자동으로 수행할 수 있지만 Safari와 같이 타사 쿠키를 사용하지 않는 브라우저에서는 최상위 수준 프레임(전체 페이지 탐색 또는 팝업 창)에서 수행해야 합니다.

새로 고침 토큰은 새 액세스 토큰을 획득하는 데 사용될 경우 해지되지 않지만 이전 새로 고침 토큰은 삭제해야 합니다. [OAuth 2.0 사양](https://tools.ietf.org/html/rfc6749#section-6)에는 다음과 같이 명시되어 있습니다. "권한 부여 서버는 새 새로 고침 토큰을 발급할 수 있지만 이 경우 클라이언트는 이전 새로 고침 토큰을 삭제하고 새 새로 고침 토큰으로 바꾸어야 합니다. 권한 부여 서버는 클라이언트에 새 새로 고침 토큰을 발급한 후 이전 새로 고침 토큰을 해지할 수 있습니다."

>[!IMPORTANT]
> `spa`로 등록된 리디렉션 URI로 전송되는 새로 고침 토큰의 경우 24시간 후에 만료됩니다. 초기 새로 고침 토큰을 사용하여 획득한 추가 새로 고침 토큰은 해당 만료 시간까지 계속 사용되므로 앱은 대화형 인증을 사용하여 인증 코드 흐름을 다시 실행함으로써 24시간마다 새 새로 고침 토큰을 가져올 수 있도록 준비해야 합니다. 사용자는 자격 증명을 입력할 필요가 없고 일반적으로 UX도 표시되지 않으며 애플리케이션이 다시 로드되기만 합니다. 그렇지만 브라우저에서 로그인 세션을 보려면 최상위 프레임의 로그인 페이지를 방문해야 합니다.  이것은 [타사 쿠키를 차단하는 브라우저의 개인 정보 보호 기능](reference-third-party-cookies-spas.md) 때문입니다.

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=sampleCredentia1s    // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

| 매개 변수     | Type           | Description        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.   |
| `client_id`     | required    | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID** 입니다. |
| `grant_type`    | required    | 이 인증 코드 흐름 범례에 대한 `refresh_token` 이어야 합니다. |
| `scope`         | 선택적    | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 원래 authorization_code 요청 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 Microsoft ID 플랫폼은 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. |
| `refresh_token` | required    | 흐름의 두 번째 레그에서 얻은 refresh_token입니다. |
| `client_secret` | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. 디바이스에 client_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. 이 암호는 URL로 인코딩해야 합니다. 자세한 내용은 [URI 일반 구문 사양](https://tools.ietf.org/html/rfc3986#page-12)을 참조하세요. |

#### <a name="successful-response"></a>성공적인 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 매개 변수     | Description         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다. |
| `token_type`    | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| `expires_in`    | 액세스 토큰이 유효한 기간(초)입니다.   |
| `scope`         | access_token이 유효한 범위입니다.    |
| `refresh_token` | 새 OAuth 2.0 새로 고침 토큰입니다. 이전 새로 고침 토큰을 새로 얻은 새로 고침 토큰으로 대체하여 새로 고침 토큰을 최대한 오랫동안 유효한 상태로 유지해야 합니다. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다.|
| `id_token`      | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

#### <a name="error-response"></a>오류 응답

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수         | Description                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다.           |
| `error_codes` |진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| `timestamp` | 오류가 발생한 시간입니다. |
| `trace_id` | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `correlation_id` | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

오류 코드 및 권장되는 클라이언트 작업에 대한 설명은 [토큰 엔드포인트 오류에 대한 오류 코드](#error-codes-for-token-endpoint-errors)를 참조하세요.
