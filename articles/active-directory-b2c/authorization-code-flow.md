---
title: 인증 코드 흐름 - Azure Active Directory B2C | Microsoft Docs
description: Azure AD B2C 및 OpenID Connect 인증 프로토콜을 사용하여 웹앱 빌드 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d37ad4571df0912cdb2ac8954a2292df8c8a238e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108742808"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 OAuth 2.0 인증 코드 흐름

디바이스에 설치된 앱에서 OAuth 2.0 인증 코드 권한 부여를 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. OAuth 2.0의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하면 단일 페이지, 모바일 및 데스크톱 앱에 가입, 로그인 및 기타 ID 관리 작업을 추가할 수 있습니다. 이 문서는 언어 독립적입니다. 이 문서에서는 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다. 가능하면 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다. [MSAL을 사용하는 샘플 앱](code-samples.md)을 살펴보세요.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749)에서 설명합니다. 웹 애플리케이션, 단일 페이지 애플리케이션 및 기본적으로 설치된 애플리케이션을 포함하여 대부분의 [애플리케이션 형식](application-types.md)에서 인증 및 권한 부여에 사용할 수 있습니다. OAuth 2.0 인증 코드 흐름을 사용하여 [권한 부여 서버](protocols-overview.md)를 통해 보호되는 리소스에 액세스하는 데 사용할 수 있는 애플리케이션에 대한 액세스 토큰 및 새로 고침 토큰을 안전하게 획득할 수 있습니다.  새로 고침 토큰을 사용하면 일반적으로 1시간 후 액세스 토큰이 만료되면 클라이언트가 새 액세스(및 새로 고침) 토큰을 획득할 수 있습니다.

이 문서에서는 **공용 클라이언트** OAuth 2.0 인증 코드 흐름을 중점적으로 다룹니다. 공용 클라이언트는 보안 암호의 무결성을 안전하게 유지하기 위해 신뢰할 수 없는 클라이언트 애플리케이션입니다. 여기에는 단일 페이지 애플리케이션, 모바일 앱, 데스크톱 애플리케이션 및 기본적으로 서버에서 실행되지 않는 애플리케이션이 포함됩니다.

> [!NOTE]
> Azure AD B2C를 사용하여 ID 관리를 웹앱에 추가하려면 OAuth 2.0 대신 [OpenID Connect](openid-connect.md)를 사용합니다.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OAuth 2.0의 흐름을 확장합니다. Azure AD B2C는 [사용자 흐름](user-flow-overview.md)을 도입했습니다. 사용자 흐름을 사용하면 OAuth 2.0을 통해 가입, 로그인 및 프로필 관리와 같은 사용자 환경을 애플리케이션에 추가할 수 있습니다. OAuth 2.0 프로토콜을 사용하는 ID 공급자에는 [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md) 및 [LinkedIn](identity-provider-linkedin.md)이 포함됩니다.

이 문서에서 HTTP 요청을 시도하려면 다음을 수행합니다.

1. `{tenant}`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
1. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`을 Azure AD B2C 테넌트에서 이전에 등록한 애플리케이션의 앱 ID로 바꿉니다.
1. `{policy}`를 테넌트에서 만든 정책 이름으로 대체합니다(예:`b2c_1_sign_in`).

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>단일 페이지 앱에 필요한 리디렉션 URI 설정

단일 페이지 애플리케이션에 대한 인증 코드 흐름에는 몇 가지 추가 설정이 필요합니다.  [단일 페이지 애플리케이션 만들기](tutorial-register-spa.md)에 관한 지침에 따라 리디렉션 URI를 CORS에 사용하도록 설정된 것으로 올바르게 표시합니다. CORS를 사용하도록 기존 리디렉션 URI를 업데이트하려면 **앱 등록** 의 **인증** 탭에 있는 "웹" 섹션에서 마이그레이션 프롬프트를 클릭하면 됩니다. 또는 **앱 등록 매니페스트 편집기** 를 열고 `replyUrlsWithType` 섹션에서 리디렉션 URI에 대한 `type` 필드를 `spa`로 설정할 수 있습니다.

`spa` 리디렉션 형식은 이전 버전의 암시적 흐름과 호환됩니다. 현재 암시적 흐름을 사용하여 토큰을 가져오는 앱은 이슈 없이 `spa` 리디렉션 URI 형식으로 이동하고 암시적 흐름을 계속 사용할 수 있습니다.

## <a name="1-get-an-authorization-code"></a>1. 인증 코드 가져오기
인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 엔드포인트로 보내는 것으로 시작됩니다. 사용자가 조치를 취하는 흐름의 대화형 부분입니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 `scope` 매개 변수에 나타냅니다. 다음 세 가지 예제(쉽게 읽을 수 있도록 줄 바꿈 적용)에서는 각각 다른 사용자 흐름을 사용합니다.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| 매개 변수 | 필수 여부 | Description |
| --- | --- | --- |
|{tenant}| 필수 | Azure AD B2C 테넌트의 이름|
| {policy} | 필수 | 실행할 사용자 흐름입니다. Azure AD B2C 테넌트에서 만든 사용자 흐름의 이름을 지정합니다. 예: `b2c_1_sign_in`, `b2c_1_sign_up` 또는 `b2c_1_edit_profile`. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 애플리케이션 ID입니다. |
| response_type |필수 |응답 유형입니다. 인증 코드 흐름의 경우 `code`를 포함해야 합니다. |
| redirect_uri |필수 |앱이 인증 응답을 보내고 받는 앱의 리디렉션 URI입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청되는 사용 권한을 Azure AD(Azure Active Directory)에 둘 다 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 대한 장기 액세스를 위해 앱에 새로 고침 토큰이 필요함을 나타냅니다. `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| response_mode |권장 |결과로 생성된 인증 코드를 앱에 다시 보내는 데 사용하는 방법입니다. `query`, `form_post` 또는 `fragment`일 수 있습니다. |
| state |권장 |사용하려는 콘텐츠의 문자열일 수 있는 요청에 포함된 값입니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 state(상태)는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보를 인코딩하는 데에도 사용됩니다. 예를 들어 사용자가 보고 있던 페이지 또는 실행 중이었던 사용자 흐름입니다. |
| prompt |선택 사항 |필요한 사용자 상호 작용 유형입니다. 현재 유효한 값은 `login`뿐이며, 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |
| code_challenge  | 권장/필수 | PKCE(Proof Key for Code Exchange)를 통해 권한 부여 코드를 보호하는 데 사용됩니다. `code_challenge_method`가 포함되면 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. 이제 모든 애플리케이션 유형(네이티브 앱, SPA 및 웹앱과 같은 기밀 클라이언트)에서 권장됩니다. | 
| `code_challenge_method` | 권장/필수 | `code_challenge` 매개 변수에 대한 `code_verifier`를 인코딩하는 데 사용되는 메서드입니다. 이는 `S256`*이어야* 하지만, 특정 이유로 클라이언트에서 SHA256을 지원할 수 없으면 사양에 따라 `plain`을 사용할 수 있습니다. <br/><br/>제외할 경우 `code_challenge`가 포함되면 `code_challenge`가 일반 텍스트로 간주됩니다. Microsoft ID 플랫폼은 `plain` 및 `S256`을 모두 지원합니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. [인증 코드 흐름을 사용하는 단일 페이지 앱](tutorial-register-spa.md)에 필요합니다.|
| login_hint | 예| 로그인 페이지의 로그인 이름 필드를 미리 채우는 데 사용할 수 있습니다. 자세한 내용은 [로그인 이름 미리 채우기](direct-signin.md#prepopulate-the-sign-in-name)를 참조하세요.  |
| domain_hint | 예| 로그인에 사용해야 하는 소셜 ID 공급자에 대한 힌트를 Azure AD B2C에 제공합니다. 유효한 값이 포함된 경우 사용자는 ID 공급자 로그인 페이지로 직접 이동합니다.  자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. |
| 사용자 지정 매개 변수 | 예| [사용자 지정 정책](custom-policy-overview.md)에 사용할 수 있는 사용자 지정 매개 변수입니다. 예를 들어 [동적 사용자 지정 페이지 콘텐츠 URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)또는 [키-값 클레임 해결 프로그램](claim-resolver-overview.md#oauth2-key-value-parameters)입니다. |

이 시점에서 사용자 흐름의 워크플로를 완료하도록 사용자에게 요청합니다. 이 경우 사용자 이름과 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리를 등록하거나, 다른 단계를 수행할 수도 있습니다. 사용자 작업은 사용자 흐름을 정의한 방식에 따라 다릅니다.

사용자가 사용자 흐름을 완료하면 Azure AD에서 앱에 대한 응답을 `redirect_uri`에 사용한 값으로 반환합니다. `response_mode` 매개 변수에 지정된 메서드를 사용합니다. 실행된 사용자 흐름과 관계 없이 사용자 작업 시나리오 각각에 대한 응답은 정확히 동일합니다.

`response_mode=query`를 사용하는 성공적인 응답은 다음과 같습니다.

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 매개 변수 | 설명 |
| --- | --- |
| code |앱이 요청한 권한 부여 코드입니다. 앱에서는 권한 부여 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 인증 코드는 수명이 매우 짧습니다. 일반적으로 약 10분 후에 만료됩니다. |
| state |전체 설명은 이전 섹션의 첫 번째 표를 참조하세요. `state` 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

오류 응답은 앱에서 적절히 처리할 수 있도록 리디렉션 URI에 보낼 수도 있습니다.

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | Description |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state |잎의 표에 나와 있는 전체 설명을 참조하세요. `state` 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

## <a name="2-get-an-access-token"></a>2. 액세스 토큰 가져오기
인증 코드를 받았으므로 이제 POST 요청을 `/token` 엔드포인트로 전송하여 `code`를 의도한 리소스에 대한 토큰으로 교환할 수 있습니다. Azure AD B2C에서는 요청에서 범위를 지정하는 방식으로 평소처럼 [다른 API에 대한 액세스 토큰을 요청](access-tokens.md#request-a-token)할 수 있습니다.

앱의 클라이언트 ID를 요청된 범위로 사용하는 규칙에 따라 앱의 자체 백엔드 웹 API에 대한 액세스 토큰을 요청할 수도 있습니다(이렇게 하면 해당 클라이언트 ID가 “대상”으로 지정된 액세스 토큰이 생성됩니다).

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| 매개 변수 | 필수 여부 | Description |
| --- | --- | --- |
|{tenant}| 필수 | Azure AD B2C 테넌트의 이름|
|{policy}| 필수| 권한 부여 코드를 획득하는 데 사용된 사용자 흐름입니다. 이 요청에 다른 사용자 흐름을 사용할 수 없습니다. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 애플리케이션 ID입니다.|
| client_secret | 예(웹앱) | [Azure Portal](https://portal.azure.com/)에서 생성한 애플리케이션 암호입니다. 웹앱 시나리오의 이 흐름에서는 클라이언트가 클라이언트 암호를 안전하게 저장할 수 있으므로 클라이언트 암호가 사용됩니다. 네이티브 앱(퍼블릭 클라이언트) 시나리오의 경우 클라이언트 암호를 안전하게 저장할 수 없으므로 이 호출에서 사용되지 않습니다. 클라이언트 암호를 사용하는 경우 주기적으로 변경해야 합니다. |
| grant_type |필수 |권한 부여 유형입니다. 인증 코드 흐름에서 권한 부여 유형은 `authorization_code`여야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 대한 장기 액세스를 위해 앱에 새로 고침 토큰이 필요함을 나타냅니다.  `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| code |필수 |흐름의 첫 번째 단계에서 얻은 권한 부여 코드입니다. |
| redirect_uri |필수 |인증 코드를 받은 애플리케이션의 리디렉션 URI입니다. |
| code_verifier | 권장 | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 매개 변수 | Description |
| --- | --- |
| not_before |epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT(JSON Web Token)입니다. |
| scope |토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| expires_in |토큰이 유효한 기간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 장기적으로 존재합니다. 토큰을 사용하여 오랜 시간 동안 리소스에 대한 액세스를 유지할 수 있습니다. 자세한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | Description |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="3-use-the-token"></a>3. 토큰 사용
액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 웹 API에 대한 요청에서 토큰을 사용할 수 있습니다.

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. 토큰 새로 고침
액세스 토큰 및 ID 토큰은 수명이 짧습니다. 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 POST 요청을 `/token` 엔드포인트에 제출합니다. 여기에서는 `code` 대신 `refresh_token`을 제공합니다.

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 여부 | Description |
| --- | --- | --- |
|{tenant}| 필수 | Azure AD B2C 테넌트의 이름|
|{policy} |필수 |원래의 새로 고침 토큰을 얻는 데 사용된 사용자 흐름입니다. 이 요청에 다른 사용자 흐름을 사용할 수 없습니다. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 애플리케이션 ID입니다. |
| client_secret | 예(웹앱) | [Azure Portal](https://portal.azure.com/)에서 생성한 애플리케이션 암호입니다. 웹앱 시나리오의 이 흐름에서는 클라이언트가 클라이언트 암호를 안전하게 저장할 수 있으므로 클라이언트 암호가 사용됩니다. 네이티브 앱(퍼블릭 클라이언트) 시나리오의 경우 클라이언트 암호를 안전하게 저장할 수 없으므로 이 호출에서 사용되지 않습니다. 클라이언트 암호를 사용하는 경우 주기적으로 변경해야 합니다. |
| grant_type |필수 |권한 부여 유형입니다. 이 인증 코드 흐름 레그에서 권한 부여 유형은 `refresh_token`여야 합니다. |
| scope |권장 |공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 Web API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.  `offline_access` 범위는 리소스에 장기간 액세스하기 위한 새로 고침 토큰이 앱에 필요함을 나타냅니다.  `openid` 범위를 사용하여 Azure AD B2C에서 ID 토큰을 요청할 수도 있습니다. |
| redirect_uri |선택 사항 |인증 코드를 받은 애플리케이션의 리디렉션 URI입니다. |
| refresh_token |필수 |흐름의 두 번째 단계에서 얻은 원래의 새로 고침 토큰입니다. |

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 매개 변수 | Description |
| --- | --- |
| not_before |epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type |토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| access_token |사용자가 요청한 서명된 JWT입니다. |
| scope |토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| expires_in |토큰이 유효한 기간(초)입니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | Description |
| --- | --- |
| error |발생한 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>사용자 고유의 Azure AD B2C 디렉터리 사용
이러한 요청을 직접 시도하려면 다음 단계를 완료합니다. 이 문서에서 사용한 예제 값을 사용자 고유의 값으로 바꿉니다.

1. [Azure AD B2C 디렉터리를 만듭니다](tutorial-create-tenant.md). 요청에 디렉터리의 이름을 사용합니다.
2. [애플리케이션을 만들어](tutorial-register-applications.md) 애플리케이션 ID 및 리디렉션 URI를 얻습니다. 앱에 네이티브 클라이언트를 포함합니다.
3. [사용자 흐름을 만들어](user-flow-overview.md) 사용자 흐름 이름을 가져옵니다.
