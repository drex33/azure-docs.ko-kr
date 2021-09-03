---
title: Microsoft ID 플랫폼 및 OpenID Connect 프로토콜 | Azure
titleSuffix: Microsoft identity platform
description: OpenID Connect 인증 프로토콜에 대한 Microsoft ID 플랫폼 구현을 사용하여 웹 애플리케이션을 빌드합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a23fd4f764773dfa29e1abd20f4952cc86049c42
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464081"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft ID 플랫폼 및 OpenID Connect 프로토콜

OIDC(OpenID Connect)는 웹 애플리케이션에 사용자가 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0 기반의 인증 프로토콜입니다. Microsoft ID 플랫폼의 OpenID Connect 구현을 사용하는 경우 로그인 및 API 액세스를 앱에 추가할 수 있습니다. 이 문서는 언어 독립적으로 수행하는 방법을 보여주고 [Microsoft 오픈 소스 라이브러리](reference-v2-libraries.md)를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)는 OAuth를 사용하여 Single Sign-On을 수행할 수 있도록 OAuth 2.0 *권한 부여* 프로토콜을 확장하여 *인증* 프로토콜로 사용합니다. OpenID Connect는 클라이언트가 사용자 ID를 확인할 수 있게 하는 보안 토큰인 *ID 토큰* 의 개념을 소개합니다. ID 토큰은 사용자에 대한 기본 프로필 정보도 가져옵니다. 또한 사용자에 대한 정보를 반환하는 API인 [UserInfo 엔드포인트](userinfo.md)를 소개합니다. 

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram-sign-in"></a>프로토콜 다이어그램: 로그인

가장 기본적인 로그인 흐름은 다음 다이어그램에 표시된 단계를 포함합니다. 각 단계는 이 문서에서는 자세히 설명되어 있습니다.

![OpenID Connect 프로토콜: 로그인](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>OpenID Connect 메타데이터 문서를 가져옵니다.

OpenID Connect는 앱이 로그인하는 데 필요한 대부분의 정보를 포함하는 메타데이터 문서[(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html)를 설명합니다. 여기에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. 검색 문서 경로를 기관 URL에 추가하여 이 문서를 찾을 수 있습니다.

검색 문서 경로: `/.well-known/openid-configuration`

권한: `https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}`는 4개의 값 중 하나를 가질 수 있습니다.

| 값 | Description |
| --- | --- |
| `common` |개인 Microsoft 계정과 Azure AD의 회사 또는 학교 계정이 둘 다 있는 사용자가 애플리케이션에 로그인할 수 있습니다. |
| `organizations` |Azure AD의 회사 또는 학교 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. |
| `consumers` |개인 Microsoft 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 또는 `contoso.onmicrosoft.com` | 특정 Azure AD 테넌트(회사 또는 학교 계정의 디렉터리에 속한 구성원 또는 개인 Microsoft 계정의 디렉터리의 게스트)에 속한 사용자만 애플리케이션에 로그인할 수 있습니다. Azure AD 테넌트의 친숙한 도메인 이름 또는 테넌트의 GUID 식별자를 사용할 수 있습니다. `consumers` 테넌트 대신 소비자 테넌트 `9188040d-6c67-4c5b-b112-36a304b66dad`를 사용할 수도 있습니다.  |

이 기관은 전 세계의 클라우드(예: `https://login.microsoftonline.de`은 AZURE AD 독일 인스턴스에 해당)에서 다릅니다. 퍼블릭 클라우드를 사용하지 않는 경우 [국가 클라우드 엔드포인트](authentication-national-cloud.md#azure-ad-authentication-endpoints)를 검토하여 적절한 항목을 찾으십시오. 엔드포인트의 v2.0 버전을 사용할 수 있도록 테넌트 및 `/v2.0/`이 요청에 존재하는지 확인합니다.

> [!TIP]
> 사용해 보세요. [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)을 클릭하여 `common` 구성을 확인합니다.

### <a name="sample-request"></a>샘플 요청

퍼블릭 클라우드의 공용 기관에 대한 userinfo 엔드포인트를 호출하려면 다음을 사용합니다.

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>샘플 응답

메타데이터는 간단한 JSON(JavaScript Object Notation) 문서입니다. 예제를 보려면 다음 코드 조각을 참조하세요. 해당 내용은 [OpenID Connect 사양](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)에 자세히 설명되어 있습니다.

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

[claims-mapping](active-directory-claims-mapping.md) 기능을 사용한 결과 앱에 사용자 지정 서명 키가 적용된 경우, 앱 ID를 포함하는 `appid` 쿼리 매개 변수를 추가해야 앱의 서명 키 정보를 가리키는 `jwks_uri`를 얻을 수 있습니다. 예를 들어 `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e`는 `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`의 `jwks_uri`를 포함합니다.

일반적으로 이 메타데이터 문서를 사용하여 OpenID Connect 라이브러리 또는 SDK를 구성하고 라이브러리는 작업을 수행하기 위해 메타데이터를 사용합니다. 그러나 미리 빌드된 OpenID Connect 라이브러리를 사용하지 않는 경우 이 문서의 나머지 부분에 나와 있는 단계에 따라 Microsoft ID 플랫폼을 사용하여 웹앱에서 로그인할 수 있습니다.

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기

웹앱이 사용자를 인증해야 하는 경우 사용자를 `/authorize` 엔드포인트로 보낼 수 있습니다. 이 요청은 다음과 같은 중요한 차이점이 있지만 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)의 첫 번째 레그와 유사합니다.

* 요청이 `openid` 범위를 `scope` 매개 변수에 포함해야 합니다.
* `response_type` 매개 변수는 `id_token`을(를) 포함해야 합니다.
* 요청은 `nonce` 매개 변수를 포함해야 합니다.

> [!IMPORTANT]
> /authorization 엔드포인트에서 ID 토큰을 성공적으로 요청하려면 [등록 포털](https://portal.azure.com)의 앱 등록에 인증 탭에서 사용하도록 설정된 id_tokens가 암시적으로 부여되어야 합니다(따라서 [애플리케이션 매니페스트](reference-app-manifest.md)의 `oauth2AllowIdTokenImplicitFlow` 플래그가 `true`로 설정됩니다). 사용하도록 설정되어 있지 않으면 `unsupported_response` 오류가 반환됩니다. “입력 매개 변수 ‘response_type’에 대해 제공된 값은 이 클라이언트에 대해 허용되지 않습니다. 필요한 값은 'code'입니다."가 반환됩니다.

다음은 그 예입니다.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본 사항](active-directory-v2-protocols.md#endpoints)을 참조하세요. 한 테넌트의 사용자를 다른 테넌트로 로그인하는 게스트 시나리오의 경우, 리소스 테넌트로 올바르게 로그인하려면 테넌트 식별자를 *반드시* 제공해야 합니다.|
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID** 입니다. |
| `response_type` | 필수 | OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. `code`와 같은 다른 `response_type` 값을 포함할 수도 있습니다. |
| `redirect_uri` | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. 없는 경우, 엔드포인트가 사용자를 돌려보낼 하나의 등록된 redirect_uri를 임의로 선택합니다. |
| `scope` | 필수 | 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인" 권한으로 해석되는 `openid`범위가 포함되어야 합니다. 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| `nonce` | 필수 | 앱에서 생성한 요청에 포함되는 값이며, 결과 id_token 값에 클레임으로 포함됩니다. 앱은 이 값을 확인하여 토큰 재생 공격을 완화할 수 있습니다. 이 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| `response_mode` | 권장 | 결과 권한 부여 코드를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. `form_post` 또는 `fragment`일 수 있습니다. 웹 애플리케이션의 경우 애플리케이션에 대한 가장 안전한 토큰 전송을 보장하기 위해 `response_mode=form_post`를 사용하는 것이 좋습니다. |
| `state` | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| `prompt` | 옵션 | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 `login`, `none`, `consent`, `select_account`뿐입니다. `prompt=login` 클레임은 사용자가 해당 요청에 자격 증명을 입력하도록 하여 Single-Sign On을 무효화합니다. `prompt=none` 매개 변수는 반대이며 로그인해야 하는 사용자를 나타내기 위해 `login_hint`와 쌍을 이루어야 합니다. 이러한 매개 변수는 사용자에게 어떤 대화형 메시지도 표시하지 않습니다. Single Sign-On을 통해 요청을 자동으로 완료할 수 없는 경우(사용자가 로그인하지 않았거나, 힌트가 지정된 사용자가 로그인하지 않았거나, 로그인한 사용자가 여러 명 있거나, 제공된 힌트가 없음) Microsoft ID 플랫폼에서 오류를 반환합니다. `prompt=consent` 클레임은 사용자가 로그인 한 후 OAuth 동의 대화 상자를 트리거합니다. 이 대화 상자에서는 앱에 권한을 부여하도록 사용자에게 요청합니다. 마지막으로 `select_account`는 사용자에게 계정 선택기를 표시하고 자동 SSO를 무효화하지만 사용자가 자격 증명을 입력하지 않고도 로그인할 계정을 선택할 수 있도록 합니다. `login_hint` 및 `select_account`는 함께 사용할 수 없습니다.|
| `login_hint` | 옵션 | 사용자 이름을 미리 알고 있는 경우 이 매개 변수를 사용하여 사용자를 위해 로그인 페이지의 사용자 이름 및 전자 메일 주소 필드를 미리 채울 수 있습니다. 종종 앱은 이전 로그인에서 `login_hint` [선택적 클레임](active-directory-optional-claims.md)을 이미 추출한 후 재인증 과정에서 이 매개 변수를 사용합니다. |
| `domain_hint` | 옵션 | 페더레이션된 디렉터리에 있는 사용자의 영역입니다.  사용자 경험을 보다 간소화하기 위해 로그인 페이지에서 사용자가 거치는 메일 기반 검색 프로세스를 건너뜁니다. AD FS와 같은 온-프레미스 디렉터리를 통해 페더레이션된 테넌트의 경우, 기존 로그인 세션으로 인해 원활한 로그인이 이루어지는 경우가 많습니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. Microsoft ID 플랫폼은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지 확인합니다. 사용자가 이러한 사용 권한에 동의하지 않은 경우 Microsoft ID 플랫폼은 필요한 사용 권한에 동의하라는 메시지를 표시합니다. [권한, 동의 및 다중 테넌트 앱](v2-permissions-and-consent.md)에 대해 자세히 알아볼 수 있습니다.

사용자가 인증하고 동의하면 Microsoft ID 플랫폼은 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 리디렉션 URI에서 해당 앱에 응답을 반환합니다.

### <a name="successful-response"></a>성공적인 응답

`response_mode=form_post`를 사용할 때의 성공적인 응답은 다음과 같습니다.

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 매개 변수 | Description |
| --- | --- |
| `id_token` | 앱이 요청한 ID 토큰입니다. `id_token` 매개 변수를 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대한 자세한 내용은 [`id_tokens` 참조](id-tokens.md)를 참조하세요. |
| `state` | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

### <a name="error-response"></a>오류 응답

앱이 처리할 수 있도록 리디렉션 URI에 오류 응답을 보낼 수도 있습니다. 오류 응답은 다음과 같습니다.

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description |
| --- | --- |
| `error` | 발생한 오류 유형을 분류하고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 엔드포인트 오류에 대한 오류 코드

다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 오류 코드를 설명합니다.

| 오류 코드 | Description | 클라이언트 작업 |
| --- | --- | --- |
| `invalid_request` | 프로토콜 오류(예: 필수 매개 변수 누락)입니다. |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다. |
| `unauthorized_client` | 클라이언트 애플리케이션이 인증 코드를 요청할 수 없습니다. |이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `access_denied` | 리소스 소유자가 동의를 거부했습니다. |클라이언트 애플리케이션에서 사용자 동의를 받아야만 진행할 수 있다고 알릴 수 있습니다. |
| `unsupported_response_type` |권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다. |
| `server_error` | 서버에 예기치 않은 오류가 발생했습니다. |요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 애플리케이션이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `temporarily_unavailable` | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. |요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `invalid_resource` | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. |리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사

id_token을 받는 것만으로는 사용자를 인증하는 데 항상 충분하지 않습니다. id_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 또한 확인해야 합니다. 모든 OIDC 플랫폼과 같이 Microsoft ID 플랫폼은 [JWT(JSON 웹 토큰)](https://tools.ietf.org/html/rfc7519) 및 공개 키 암호화를 사용하여 ID 토큰에 서명하고 토큰이 유효한지 확인합니다.

모든 앱에서 ID 토큰-네이티브 앱 및 단일 페이지 앱을 확인하는 것이 좋은 것은 아닙니다. 예를 들어 ID 토큰의 유효성을 검사하여 얻는 혜택은 거의 없습니다.  디바이스(또는 브라우저)에 물리적으로 액세스하는 사용자는 디바이스에 대한 웹 트래픽을 편집하여 애플리케이션을 디버깅하는 가짜 토큰 및 키를 제공하여 유효성 검사 논리를 건너뛰도록 하는 등의 다양한 방법으로 유효성 검사를 우회할 수 있습니다.  반면에 ID 토큰을 사용하는 웹 앱 및 API는 데이터에 대한 액세스를 제한하므로 ID 토큰의 유효성을 신중하게 확인해야 합니다.

id_token의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다. [토큰 유효성 검사](id-tokens.md#validating-an-id-token) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-signing-key-rollover.md)를 포함한 자세한 내용은 [`id_token` 참조](id-tokens.md)를 확인하세요. 대부분의 언어 및 플랫폼에서 사용할 수 있는 하나 이상의 토큰의 구문 분석 및 유효성 검사에 대한 라이브러리를 사용하는 것이 좋습니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록했는지 확인
* 사용자에게 적절한 권한 부여/권한이 있는지 확인
* [다단계 인증](../authentication/concept-mfa-howitworks.md)과 같은 특정 강도의 인증이 발생했는지 확인

id_token의 유효성을 검사한 후 사용자와 세션을 시작하고 id_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 개인 설정 등에 사용될 수 있습니다.

## <a name="protocol-diagram-access-token-acquisition"></a>프로토콜 다이어그램: 액세스 토큰 획득

대부분의 웹앱은 사용자를 로그인할 뿐만 아니라 OAuth를 사용하여 해당 사용자 대신 웹 서비스에 액세스해야 합니다. 이 시나리오에서는 OAuth 권한 부여 코드 흐름을 사용하여 액세스 토큰을 가져오는 데 사용할 수 있는 권한 부여 코드를 획득하는 동시에 사용자 인증에 OpenID Connect를 사용합니다.

전체 OpenID Connect 로그인 및 토큰 획득 흐름은 다음 다이어그램과 유사합니다. 각 단계는 문서의 다음 섹션에서 자세히 설명합니다.

![OpenID Connect 프로토콜: 토큰 획득](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>UserInfo를 호출하는 액세스 토큰 가져오기

OIDC UserInfo 엔드포인트에 대한 토큰을 가져오려면 로그인 요청을 수정합니다.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

앱에 대한 토큰을 가져오는 대신 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md), [디바이스 코드 흐름](v2-oauth2-device-code.md)또는 [새로 고침 토큰](v2-oauth2-auth-code-flow.md#refresh-the-access-token)을 사용할 수도 있습니다`response_type=token`.

> [!TIP]
> 이 요청을 실행하려면 다음 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 ID 토큰 및 토큰과 함께 `https://localhost/myapp/`으로 리디렉션됩니다. 이 요청은 가능한 경우 추가적인 보안을 위해 `response_mode=fragment`사용을 권고하는 웹앱을 위한 데모용으로만`form_post` 사용함을 유의하십시오. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>토큰 응답 성공

`response_mode=form_post`를 사용할 때의 성공적인 응답은 다음과 같습니다.

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

응답 매개 변수는 해당 매개 변수를 획득하는 데 사용되는 흐름과 상관없이 동일한 작업을 의미합니다.

| 매개 변수 | 설명 |
| --- | --- |
| `access_token` | UserInfo 엔드포인트를 호출하는 데 사용되는 토큰입니다.|
| `token_type` | 항상 “전달자”입니다. |
| `expires_in`| 액세스 토큰이 만료될 때까지의 시간(초)입니다. |
| `scope` | 액세스 토큰에 부여되는 권한입니다.  UserInfo 엔드포인트는 MS Graph에서 호스트되므로 앱에 이전에 부여된 경우 여기에 나열된 추가 그래프 범위(예: user.읽기)가 있을 수 있음을 유의하세요.  지정된 리소스에 대한 토큰에는 항상 현재 클라이언트에 부여된 모든 사용 권한이 포함되기 때문입니다.  |
| `id_token` | 앱이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대한 자세한 내용은 [`id_tokens` 참조](id-tokens.md)에서 확인할 수 있습니다. |
| `state` | state 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>오류 응답

앱이 적절히 처리할 수 있도록 리디렉션 URI에 오류 응답을 보낼 수도 있습니다. 오류 응답은 다음과 같습니다.

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description |
| --- | --- |
| `error` | 발생한 오류 유형을 분류하고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

가능한 오류 코드 및 권장되는 클라이언트 응답에 대한 설명은 [권한 부여 엔드포인트 오류에 대한 오류 코드](#error-codes-for-authorization-endpoint-errors)를 참조하세요.

권한 부여 코드와 ID 토큰이 있는 경우 사용자로 로그인하여 액세스 토큰을 대신 가져올 수 있습니다. 사용자로 로그인하려면 [정확히 위에 설명된 대로](id-tokens.md#validating-an-id-token) ID 토큰의 유효성을 검사해야 합니다. 액세스 토큰을 가져오려면 [OAuth 코드 흐름 설명서](v2-oauth2-auth-code-flow.md#redeem-a-code-for-an-access-token)에 설명된 단계를 따릅니다.

### <a name="calling-the-userinfo-endpoint"></a>UserInfo 엔드포인트 호출

[Userinfo 설명서](userinfo.md#calling-the-api)를 검토하여 이 토큰을 사용해 userinfo 엔드포인트를 호출하는 방법을 확인합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

앱에서 사용자를 로그아웃시키려는 경우 앱의 쿠키를 삭제하거나 그렇지 않은 경우 사용자의 세션을 지우는 것은 충분하지 않습니다. 또한 로그아웃할 사용자를 Microsoft ID 플랫폼으로 리디렉션해야 합니다. 이렇게 하지 않으면 사용자는 Microsoft ID 플랫폼을 사용하는 유효한 단일 로그인 세션이 있기 때문에 자격 증명을 다시 입력하지 않고 앱에 다시 인증할 수 있습니다.

OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint`에 사용자를 리디렉션할 수 있습니다.

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| 매개 변수 | 조건 | Description |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | 권장 | 성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 매개 변수를 포함하지 않을 경우 사용자에게는 Microsoft ID 플랫폼에 의해 생성된 일반 메시지가 표시됩니다. URL은 앱 등록 포털에서 애플리케이션에 등록한 리디렉션 URI 중 하나와 일치해야 합니다. |

## <a name="single-sign-out"></a>Single Sign-Out

사용자를 `end_session_endpoint`에 리디렉션하는 경우 Microsoft ID 플랫폼은 브라우저에서 사용자의 세션을 지웁니다. 하지만 사용자는 인증을 위해 Microsoft 계정을 사용하는 다른 애플리케이션에 여전히 로그인되어 있을 수 있습니다. 사용자를 동시에 로그아웃하도록 해당 애플리케이션을 활성화하려면 Microsoft ID 플랫폼은 현재 사용자가 로그인된 모든 애플리케이션의 등록된 `LogoutUrl`에 HTTP GET 요청을 보냅니다. 애플리케이션은 사용자를 식별하는 모든 세션을 지우고 `200` 요청을 반환하여 이 요청에 응답해야 합니다. 애플리케이션에서 단일 로그아웃을 지원하려는 경우 애플리케이션 코드에서 해당 `LogoutUrl`을 구현해야 합니다. 앱 등록 포털에서 `LogoutUrl`을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [UserInfo 설명서](userinfo.md) 검토
* 온-프레미스 시스템의 데이터로 [토큰의 값을 사용자 지정](active-directory-claims-mapping.md)하는 방법에 대해 알아봅니다. 
* [토큰에 추가 표준 클레임을 포함](active-directory-optional-claims.md)하는 방법을 알아봅니다.  
