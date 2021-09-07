---
title: OpenID Connect로 웹 로그인 - Azure Active Directory B2C
description: Azure Active Directory B2C에서 OpenID Connect 인증 프로토콜을 사용하여 웹 애플리케이션을 빌드합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 32154904a78062f5e3afdb6217351f39151b36b8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528408"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 OpenID Connect로 웹 로그인

OpenID Connect는 웹 애플리케이션에 사용자를 안전하게 로그인하는 데 사용할 수 있도록 OAuth 2.0을 기반으로 하여 빌드된 인증 프로토콜입니다. OpenID Connect의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하여 웹 애플리케이션의 등록, 로그인 및 기타 ID 관리 환경을 Azure AD( Azure Active Directory)로 아웃소싱할 수 있습니다. 이 가이드에서는 언어에 관계 없이 이 작업을 수행하는 방법을 보여 줍니다. 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

> [!NOTE]
> 대부분의 오픈 소스 인증 라이브러리는 애플리케이션에 대한 JWT 토큰을 가져와 유효성을 검사합니다. 사용자 고유의 코드를 구현하는 대신 이러한 옵션을 탐색하는 것이 좋습니다. 자세한 내용은 [Microsoft 인증 라이브러리(MSAL) 개요](../active-directory/develop/msal-overview.md) 및 [Microsoft Identity Web 인증 라이브러리](../active-directory/develop/microsoft-identity-web.md)를 참조하세요.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장합니다. 이 인증 프로토콜을 통해 Single Sign-On을 수행할 수 있습니다. 클라이언트에서 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있게 하는 ‘ID 토큰’의 개념을 소개합니다.

또한 OAuth 2.0을 확장하므로 애플리케이션에서 ‘액세스 토큰’을 안전하게 얻을 수 있습니다. 액세스 토큰을 사용하여 [권한 부여 서버](protocols-overview.md)로 보안이 유지되는 리소스에 액세스할 수 있습니다. OpenID Connect는 서버에서 호스트되고 브라우저를 통해 액세스되는 웹 애플리케이션을 빌드하는 경우에 권장됩니다. 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](tokens-overview.md)를 참조하세요.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OpenID Connect 프로토콜을 확장합니다. OpenID Connect를 사용하여 등록, 로그인, 프로필 관리와 같은 사용자 환경을 애플리케이션에 추가할 수 있는 [사용자 흐름 매개 변수](user-flow-overview.md)를 도입합니다.

## <a name="send-authentication-requests"></a>인증 요청 보내기

웹 애플리케이션이 사용자를 인증하고 사용자 흐름을 실행해야 하는 경우 사용자를 `/authorize` 엔드포인트로 보낼 수 있습니다. 사용자가 사용자 흐름에 따라 동작을 수행합니다.

이 요청에서 클라이언트는 `scope` 매개 변수에서 사용자로부터 가져와야 할 사용 권한을 나타내며, 실행할 사용자 흐름을 지정합니다. 요청의 작동 방식에 대해 이해하려면 요청을 브라우저에 붙여넣고 실행합니다. `{tenant}`을 테넌트 이름으로 바꿉니다. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`을 테넌트에 등록해 두었던 애플리케이션의 앱 ID로 바꿉니다. 또한 정책 이름(`{policy}`)을 테넌트에 있는 정책 이름(예: `b2c_1_sign_in`)으로 변경합니다.

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| {tenant} | 예 | Azure AD B2C 테넌트의 이름 |
| {policy} | 예 | 실행할 사용자 흐름입니다. Azure AD B2C 테넌트에서 만든 사용자 흐름의 이름을 지정합니다. 예: `b2c_1_sign_in`, `b2c_1_sign_up` 또는 `b2c_1_edit_profile`. |
| client_id | 예 | [Azure Portal](https://portal.azure.com/)이 애플리케이션에 할당한 애플리케이션 ID입니다. |
| nonce | 예 | 애플리케이션에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다. 그러면 애플리케이션에서 이 값을 확인하여 토큰 재생 공격을 완화할 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response_type | 예 | OpenID Connect의 ID 토큰을 포함해야 합니다. 웹 API를 호출하기 위한 토큰이 웹 애플리케이션에 필요한 경우 `code+id_token`을 사용할 수 있습니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. `offline_access` 범위는 웹 애플리케이션에 대한 선택 사항입니다. 리소스에 확장된 시간 동안 액세스하기 위한 ‘새로 고침 토큰’이 애플리케이션에 필요함을 나타냅니다. `https://{tenant-name}/{app-id-uri}/{scope}`는 웹 API와 같은 보호된 리소스에 대한 권한을 나타냅니다. 자세한 내용은 [액세스 토큰 요청](access-tokens.md#scopes)을 참조하세요. |
| prompt | 예 | 필요한 사용자 상호 작용의 형식입니다. 현재 유효한 값은 `login`뿐이며, 이는 사용자가 해당 요청에 대한 자격 증명을 입력하도록 합니다. |
| redirect_uri | 예 | 애플리케이션에서 인증 응답을 보내고 받을 수 있는 애플리케이션의 `redirect_uri` 매개 변수입니다. URL로 인코딩되어야 한다는 점을 제외하고는 Azure Portal에서 등록한 `redirect_uri` 매개 변수 중 하나와 정확히 일치해야 합니다. |
| response_mode | 예 | 결과 권한 부여 코드를 애플리케이션에 다시 보내는 데 사용되는 메서드입니다. `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다.  최상의 보안을 위해 `form_post` 응답 모드를 사용하는 것이 좋습니다. |
| state | 예 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 애플리케이션의 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코드하는 데에도 사용됩니다. |
| login_hint | 예| 로그인 페이지의 로그인 이름 필드를 미리 채우는 데 사용할 수 있습니다. 자세한 내용은 [로그인 이름 미리 채우기](direct-signin.md#prepopulate-the-sign-in-name)를 참조하세요.  |
| domain_hint | 예| 로그인에 사용해야 하는 소셜 ID 공급자에 대한 힌트를 Azure AD B2C에 제공합니다. 유효한 값이 포함된 경우 사용자는 ID 공급자 로그인 페이지로 직접 이동합니다.  자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. |
| 사용자 지정 매개 변수 | 예| [사용자 지정 정책](custom-policy-overview.md)에 사용할 수 있는 사용자 지정 매개 변수입니다. 예를 들어 [동적 사용자 지정 페이지 콘텐츠 URI](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)또는 [키-값 클레임 해결 프로그램](claim-resolver-overview.md#oauth2-key-value-parameters)입니다. |

이 시점에서 워크플로를 완료하도록 사용자에게 요청합니다. 사용자는 사용자 이름 및 암호를 입력하거나, 소셜 ID로 로그인하거나, 디렉터리에 가입해야 할 수 있습니다. 사용자 흐름이 정의된 방법에 따라 따라야 하는 단계의 수가 다를 수 있습니다.

사용자가 사용자 흐름을 완료하면 `response_mode` 매개 변수에 지정된 메서드를 사용하여 표시된 `redirect_uri` 매개 변수에서 애플리케이션에 대한 응답이 반환됩니다. 응답은 사용자 흐름과 관계없이 앞의 경우 각각에 대해 동일합니다.

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| id_token | 애플리케이션이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| code | `response_type=code+id_token`을 사용한 경우 애플리케이션에서 요청한 권한 부여 코드입니다. 애플리케이션은 권한 부여 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 권한 부여 코드는 일반적으로 약 10분 후에 만료됩니다. |
| state | `state` 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 애플리케이션은 요청의 상태 값과 응답의 `state` 값이 같은지 확인해야 합니다. |

애플리케이션에서 적절하게 처리할 수 있도록 `redirect_uri` 매개 변수에 오류 응답을 보낼 수도 있습니다.

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state | `state` 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 애플리케이션은 요청의 상태 값과 응답의 `state` 값이 같은지 확인해야 합니다. |

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사

ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. ID 토큰의 서명에 대한 유효성을 검사하고 애플리케이션의 요구 사항에 따라 토큰의 클레임을 확인합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다. 

> [!NOTE]
> 대부분의 오픈 소스 인증 라이브러리는 애플리케이션에 대한 JWT 토큰의 유효성을 검사합니다. 고유한 유효성 검사 논리를 구현하는 것보다 이러한 옵션을 탐색하는 것이 좋습니다. 자세한 내용은 [Microsoft 인증 라이브러리(MSAL) 개요](../active-directory/develop/msal-overview.md) 및 [Microsoft Identity Web 인증 라이브러리](../active-directory/develop/microsoft-identity-web.md)를 참조하세요.

Azure AD B2C에는 애플리케이션이 런타임에 Azure AD B2C에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 엔드포인트가 있습니다. 이 정보에는 엔드포인트, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 사용자 흐름에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 사용자 흐름에 대한 메타데이터 문서는 다음 위치에 있습니다.

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

이 구성 문서의 속성 중 하나가 `jwks_uri`이며, 동일한 사용자 흐름에 대한 값은 다음과 같습니다.

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

ID 토큰에 서명하는 데 사용된 사용자 흐름(및 메타데이터를 가져올 위치)을 결정하려면 두 가지 옵션이 있습니다. 먼저 사용자 흐름 이름이 ID 토큰의 `acr` 클레임에 포함됩니다. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 사용자 흐름을 인코딩한 다음 이를 디코딩하여 어떤 사용자 흐름을 사용할지 결정하는 것입니다. 두 방법 중 하나는 유효합니다.

OpenID Connect 메타데이터 엔드포인트에서 메타데이터 문서를 가져오면 RSA-256 공개 키를 사용하여 ID 토큰의 서명에 대한 유효성을 검사할 수 있습니다. 해당 엔드포인트에 나열된 키가 여러 개 있을 수 있으며, 각 키는 `kid` 클레임으로 식별됩니다. ID 토큰의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다.

Azure AD B2C에서 토큰을 확인하려면 지수(e) 및 모듈러스(n)를 사용하여 공개 키를 생성해야 합니다. 이에 따라 각각의 프로그래밍 언어에서 이를 수행하는 방법을 결정해야 합니다. RSA 프로토콜을 사용한 공개 키 생성에 대한 공식 문서는 https://tools.ietf.org/html/rfc3447#section-3.1 페이지에서 확인할 수 있습니다.

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예를 들면 다음과 같습니다.

- `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
- `aud` 클레임의 유효성을 검사하여 애플리케이션에 대한 ID 토큰이 발급되었는지 확인합니다. 해당 값이 애플리케이션의 애플리케이션 ID여야 합니다.
- `iat` 및 `exp` 클레임의 유효성을 검사하여 ID 토큰이 만료되지 않았는지 확인합니다.

또한 수행해야 하는 몇 가지 추가 유효성 검사가 있습니다. 유효성 검사는 [OpenID Connect Core 사양](https://openid.net/specs/openid-connect-core-1_0.html)에 자세히 설명되어 있습니다. 시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 애플리케이션에 등록했는지 확인
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- Azure AD Multi-Factor Authentication과 같은 특정 강도의 인증이 발생했는지 확인

ID 토큰의 유효성을 검사한 후에는 사용자가 포함된 세션을 시작할 수 있습니다. 애플리케이션에서 ID 토큰의 클레임을 사용하여 사용자 관련 정보를 가져올 수 있으며, 이 정보의 용도로 표시, 레코드 및 권한 부여가 있습니다.

## <a name="get-a-token"></a>토큰 가져오기

웹 애플리케이션에서 사용자 흐름만 실행해야 하는 경우 다음 몇 가지 섹션을 건너뛸 수 있습니다. 이러한 섹션은 Web API에 대해 인증된 호출을 수행해야 하며 Azure AD B2C로도 보호되는 웹 애플리케이션에만 적용할 수 있습니다.

`response_type=code+id_token`을 사용하여 `POST` 요청을 `/token` 엔드포인트로 보내 원하는 리소스에 대한 토큰에 대해 얻은 권한 부여 코드를 사용할 수 있습니다. Azure AD B2C에서는 요청에서 범위를 지정하는 방식으로 평소처럼 [다른 API에 대한 액세스 토큰을 요청](access-tokens.md#request-a-token)할 수 있습니다.

앱의 클라이언트 ID를 요청된 범위로 사용하는 규칙에 따라 앱의 자체 백엔드 웹 API에 대한 액세스 토큰을 요청할 수도 있습니다(이렇게 하면 해당 클라이언트 ID가 “대상”으로 지정된 액세스 토큰이 생성됩니다).

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| {tenant} | 예 | Azure AD B2C 테넌트의 이름 |
| {policy} | 예 | 권한 부여 코드를 획득하는 데 사용된 사용자 흐름입니다. 이 요청에 다른 사용자 흐름을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 쿼리 문자열에 추가합니다. |
| client_id | 예 | [Azure Portal](https://portal.azure.com/)이 애플리케이션에 할당한 애플리케이션 ID입니다. |
| client_secret | 예(웹앱) | [Azure Portal](https://portal.azure.com/)에서 생성한 애플리케이션 암호입니다. 웹앱 시나리오의 이 흐름에서는 클라이언트가 클라이언트 암호를 안전하게 저장할 수 있으므로 클라이언트 암호가 사용됩니다. 네이티브 앱(공용 클라이언트) 시나리오의 경우 클라이언트 암호를 안전하게 저장할 수 없으므로 이 흐름에서 사용되지 않습니다. 클라이언트 암호를 사용하는 경우 주기적으로 변경해야 합니다. |
| code | 예 | 사용자 흐름의 시작 부분에서 얻은 권한 부여 코드입니다. |
| grant_type | 예 | 권한 유형입니다. 인증 코드 흐름의 경우 `authorization_code`이어야 합니다. |
| redirect_uri | 예 | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 id_token 매개 변수의 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 이는 클라이언트와 동일한 애플리케이션 ID로 표시되는 애플리케이션 자체의 백 엔드 웹 API에 토큰을 가져오는 데 사용할 수 있습니다. `offline_access` 범위는 확장된 시간 동안 리소스에 액세스하기 위한 새로 고침 토큰이 애플리케이션에 필요함을 나타냅니다. |

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
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. 유일하게 지원되는 유형은 `Bearer`입니다. |
| access_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위입니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 애플리케이션은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 새로 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 `offline_access` 범위를 사용해야 합니다. |

오류 응답은 다음과 같습니다.

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 메시지입니다. |

## <a name="use-the-token"></a>토큰 사용

액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 웹 API에 대한 요청에서 토큰을 사용할 수 있습니다.

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>토큰 새로 고침

ID 토큰은 짧은 시간 후에 만료됩니다. 리소스에 계속 액세스하려면 만료된 후 토큰을 새로 고쳐야 합니다. 토큰을 새로 고치려면 다른 `POST` 요청을 `/token` 엔드포인트에 제출하면 됩니다. 이번에는 `code` 매개 변수 대신 `refresh_token` 매개 변수를 제공합니다.

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| {tenant} | 예 | Azure AD B2C 테넌트의 이름 |
| {policy} | 예 | 원래의 새로 고침 토큰을 얻는 데 사용된 사용자 흐름입니다. 이 요청에 다른 사용자 흐름을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 쿼리 문자열에 추가합니다. |
| client_id | 예 | [Azure Portal](https://portal.azure.com/)이 애플리케이션에 할당한 애플리케이션 ID입니다. |
| client_secret | 예(웹앱) | [Azure Portal](https://portal.azure.com/)에서 생성한 애플리케이션 암호입니다. 웹앱 시나리오의 이 흐름에서는 클라이언트가 클라이언트 암호를 안전하게 저장할 수 있으므로 클라이언트 암호가 사용됩니다. 네이티브 앱(공용 클라이언트) 시나리오의 경우 클라이언트 암호를 안전하게 저장할 수 없으므로 이 호출에서 사용되지 않습니다. 클라이언트 암호를 사용하는 경우 주기적으로 변경해야 합니다. |
| grant_type | 예 | 권한 유형입니다. 이 인증 코드 흐름 부분의 경우 `refresh_token`이어야 합니다. |
| refresh_token | 예 | 흐름의 두 번째 부분에서 얻은 원래의 새로 고침 토큰입니다. 새로 고침 토큰을 받으려면 권한 부여 및 토큰 요청 모두에서 `offline_access` 범위를 사용해야 합니다. |
| redirect_uri | 예 | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 클라이언트와 동일한 애플리케이션 ID로 표시되는 애플리케이션 자체의 백 엔드 웹 API에 토큰을 보내는 데 사용할 수 있습니다. `offline_access` 범위는 확장된 시간 동안 리소스에 액세스하기 위한 새로 고침 토큰이 애플리케이션에 필요함을 나타냅니다. |

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
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. 유일하게 지원되는 유형은 `Bearer`입니다. |
| access_token | 요청된 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위입니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 애플리케이션은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. 새로 고침 토큰은 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. |

오류 응답은 다음과 같습니다.

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| --------- | ----------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 메시지입니다. |

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

애플리케이션에서 사용자를 로그아웃시키려는 경우 애플리케이션의 쿠키를 삭제하거나 그렇지 않은 경우 사용자로 세션을 지우는 것은 충분하지 않습니다. Azure AD B2C로 사용자를 리디렉션하여 로그아웃하도록 해야 합니다. 그러지 않으면 사용자가 자격 증명을 다시 입력하지 않고 애플리케이션을 다시 인증할 수 있습니다. 자세한 내용은 [Azure AD B2C 세션](session-behavior.md)을 참조하세요.

사용자를 로그아웃하도록 하려면 앞부분에서 설명한 OpenID Connect 메타데이터 문서에 나열된 `end_session` 엔드포인트로 사용자를 리디렉션합니다.

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| {tenant} | 예 | Azure AD B2C 테넌트의 이름 |
| {policy} | 예 | 애플리케이션에서 사용자를 로그아웃하는 데 사용하려는 사용자 흐름입니다. |
| id_token_hint| 예 | 이전에 발급된 ID 토큰으로 최종 사용자의 클라이언트와의 현재 인증된 세션에 대한 힌트로서 로그아웃 엔드포인트로 전달됩니다. `id_token_hint`는 Azure AD B2C 애플리케이션 설정에서 `post_logout_redirect_uri`가 등록된 회신 URL인지 확인합니다. 자세한 내용은 [로그아웃 리디렉션 보안](#secure-your-logout-redirect)을 참조하세요. |
| client_id | 아니요* | [Azure Portal](https://portal.azure.com/)이 애플리케이션에 할당한 애플리케이션 ID입니다.<br><br>\*`Application` 격리 SSO 구성 및 로그아웃 요청이 `No`로 설정된 경우 ‘ID 토큰 필요’를 사용할 때 필요합니다. |
| post_logout_redirect_uri | 예 | 성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 해당 URL이 포함되어 있지 않은 경우 Azure AD B2C는 사용자에게 일반 메시지를 표시합니다. `id_token_hint`를 제공하지 않는 한 Azure AD B2C 애플리케이션 설정에서 이 URL을 회신 URL로 등록해서는 안 됩니다. |
| state | 예 | `state` 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 애플리케이션은 요청의 상태 값과 응답의 `state` 값이 같은지 확인해야 합니다. |

### <a name="secure-your-logout-redirect"></a>로그아웃 리디렉션 보안

로그아웃 후 사용자는 애플리케이션에 대해 지정된 회신 URL에 관계없이 `post_logout_redirect_uri` 매개 변수에 지정된 URI로 리디렉션됩니다. 그러나 유효한 `id_token_hint`가 전달되고 **로그아웃 요청에서 ID 토큰 요구** 가 켜져 있는 경우 Azure AD B2C는 리디렉션을 수행하기 전 `post_logout_redirect_uri` 값이 애플리케이션의 구성된 리디렉션 URI 중 하나와 일치하는지 확인합니다. 애플리케이션에 일치하는 회신 URL이 구성되지 않은 경우 오류 메시지가 표시되고 사용자는 리디렉션되지 않습니다.

로그아웃 요청에 필요한 ID 토큰을 설정하려면 [Azure Active Directory B2C에서 세션 동작 구성](session-behavior.md#secure-your-logout-redirect)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-behavior.md)에 대해 자세히 알아보세요.
