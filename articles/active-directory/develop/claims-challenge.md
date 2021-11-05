---
title: 클레임 챌린지, 클레임 요청 및 클라이언트 기능
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼의 클레임 챌린지, 클레임 요청 및 클라이언트 기능에 대한 설명
services: active-directory
author: knicholasa
manager: martinco
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: nichola
ms.reviewer: kkrishna, kylemar
ms.openlocfilehash: 6d1998673b3295129c494b4568e1ba6313984898
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067256"
---
# <a name="claims-challenges-claims-requests-and-client-capabilities"></a>클레임 챌린지, 클레임 요청 및 클라이언트 기능

*클레임 챌린지* 는 API에서 전송된 응답으로 클라이언트 응용 프로그램에서 보낸 액세스 토큰에 충분한 클레임이 없음을 나타냅니다. 이는 토큰이 해당 API에 설정된 조건부 액세스 정책을 충족하지 않거나 액세스 토큰이 철회되었기 때문일 수 있습니다.

*클레임 요청* 은 충족되지 않은 추가 요구 사항을 충족하는 클레임이 있는 새 토큰을 검색하기 위해 사용자를 다시 ID 공급자로 리디렉션하도록 클라이언트 응용 프로그램에 의해 수행됩니다.

[CAE(연속 액세스 평가)](../conditional-access/concept-continuous-access-evaluation.md) 및 [조건부 액세스 인증 컨텍스트와](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775) 같은 향상된 보안 기능을 사용하는 애플리케이션은 클레임 챌린지를 처리하도록 대비해야 합니다.

애플리케이션이 [Microsoft Graph](/graph/overview) 같은 자주 사용되는 서비스에서 클레임 챌린지를 수신하는 일은 서비스 호출에서 [클라이언트 기능](#client-capabilities)을 선언하는 경우에만 발생합니다.

## <a name="claims-challenge-header-format"></a>클레임 챌린지 헤더 형식

클레임 챌린지는 API에 제공된 [액세스 토큰](access-tokens.md)에 권한이 부여되지 않고, 새로운 액세스 토큰이 필요한 경우 API에 의해 반환되는 `www-authenticate` 헤더로서의 지시문입니다. 클레임 챌린지는 여러 부분으로 구성됩니다. 응답의 HTTP 상태 코드와 `www-authenticate` 헤더는 그 자체로 여러 부분을 포함하며 클레임 지시문을 포함해야 합니다.

예를 들면 다음과 같습니다.

```https
HTTP 401; Unauthorized

www-authenticate =Bearer realm="", authorization_uri="https://login.microsoftonline.com/common/oauth2/authorize", error="insufficient_claims", claims="eyJhY2Nlc3NfdG9rZW4iOnsiYWNycyI6eyJlc3NlbnRpYWwiOnRydWUsInZhbHVlIjoiYzEifX19"
```

 **HTTP 상태 코드**: **401 인증되지 않아야** 합니다.

**www-인증 응답 헤더** 는 다음을 포함합니다:

| 매개 변수    | 필수/선택 | Description |
|--------------|-------------|--------------|
| 인증 유형 | 필수 | 반드시 **전달자** 여야 합니다.|
| Realm | 옵션 | 액세스되는 테넌트 ID 또는 테넌트 도메인 이름(예: microsoft.com)입니다. 인증이 [공통 엔드포인트](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)를 통과 하는 경우에는 빈 문자열이어야 합니다. |
| `authorization_uri` | 필수 | 필요한 경우 대화형 인증을 수행할 수 있는 권한 부여 엔드포인트의 URI입니다. 보안영역에 지정된 경우 테넌트 정보는 authorization_uri에 포함되어야 합니다. 보안영역이 빈 문자열인 경우 authorization_uri는 [공통 엔드포인트](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)에 반대여야 합니다. |
| `error` | 필수 | 클레임 챌린지를 생성해야 하는 경우 "insufficient_claims"이어야 합니다. | 
| `claims` | 오류가 "insufficient_claims"일 때 필요합니다. | Base 64로 인코딩된 [클레임 요청](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter)이 포함된 따옴표가 있는 문자열입니다. 클레임 요청은 JSON 개체의 최상위 수준에서 "access_token"에 대한 클레임을 요청해야 합니다. 값(요청된 클레임)은 컨텍스트에 따라 달라지고 이 문서의 뒷부분에서 지정됩니다. 크기 문제로 인해 신뢰 당사자 애플리케이션은 Base 64 인코딩 전에 JSON을 축소해야 합니다. 위 예제의 원시 JSON은 {"access_token":{"acrs":{"essential":true,"value":"cp1"}}}입니다. |

**401** 응답은 `www-authenticate` 헤더를 두 개 이상 포함할 수 있습니다. 앞의 테이블에 있는 모든 필드는 동일한 `www-authenticate` 헤더 내에 포함되어야 합니다. 클레임 챌린지를 포함하는 `www-authenticate` 헤더에는 기타 필드가 포함될 *수도 있습니다*. 헤더의 필드는 순서가 지정되지 않았습니다. RFC 7235에 따르면 각 매개 변수 이름은 인증 체계 챌린지당 하나여야 합니다.

## <a name="claims-request"></a>클레임 요청

애플리케이션이 클레임 챌린지를 받으면 이전 액세스 토큰이 더 이상 유효한 것으로 간주되지 않음을 나타냅니다. 이 시나리오에서 애플리케이션은 로컬 캐시 또는 사용자 세션에서 토큰을 지워야 합니다. 그 후 [OAuth 2.0 인증 코드 플로우](v2-oauth2-auth-code-flow.md)를 충족되지 않은 추가 요구 사항을 충족하는 *클레임* 매개 변수와 함께 사용하여 새 토큰을 검색하도록, 로그인한 사용자를 다시 Azure AD(Azure Active Directory)로 리디렉션해야 합니다.

예를 들면 다음과 같습니다.

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
claims=%7B%22access_token%22%3A%7B%22acrs%22%3A%7B%22essential%22%3Atrue%2C%22value%22%3A%22c1%22%7D%7D%7D
```

클레임 챌린지는 토큰이 성공적으로 검색될 때까지 Azure AD의 [/authorize](v2-oauth2-auth-code-flow.md#request-an-authorization-code) 엔드포인트에 대한 모든 호출의 일부로 전달되어야 하며, 그 후에는 더 이상 필요하지 않습니다.

클레임 매개 변수를 채우려면 개발자는 다음을 수행해야 합니다.

1. 이전에 받은 base64 문자열을 디코딩합니다.
2. URL-문자열을 인코딩하고 **클레임** 매개 변수에 다시 추가합니다.

이 흐름이 완료되면 애플리케이션은 사용자가 필요한 조건을 충족했음을 증명하는 추가 클레임이 있는 액세스 토큰을 수신하게 됩니다.

## <a name="client-capabilities"></a>클라이언트 기능

클라이언트 기능을 통해 웹 API와 같은 리소스 공급자는 호출하는 클라이언트 응용 프로그램이 클레임 챌린지를 해석하고 그에 따라 응답을 사용자 지정할 수 있는지 여부를 감지합니다. 이 기능은 모든 API 클라이언트가 클레임 문제를 처리할 수는 없는 경우 유용할 수 있으며, 일부 이전 버전은 여전히 다른 응답이 필요합니다.

[Microsoft Graph](/graph/overview)와 같은 널리 사용되는 애플리케이션은 호출 클라이언트 앱이 *클라이언트 기능* 을 사용하여 처리할 수 있다고 선언하는 경우에만 클레임 챌린지를 전송합니다.

사용자 환경에 대한 추가 트래픽 또는 영향을 방지하기 위해 Azure AD는 명시적으로 옵트인하지 않는 한 앱이 클레임 챌린지를 처리할 수 있다고 가정하지 않습니다. 애플리케이션은 클레임 챌린지를 수신하지 않으며, "cp1" 기능을 사용하여 처리할 준비가 된 경우가 아니면 CAE 토큰과 같은 관련 기능을 사용할 수 없습니다.

### <a name="how-to-communicate-client-capabilities-to-azure-ad"></a>Azure AD에 클라이언트 기능을 전달하는 방법

다음 예시 클레임 매개 변수는 클라이언트 응용 프로그램이 [OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)에서 Azure AD에 해당 기능을 전달 하는 방법을 알려줍니다.

```json
Claims: {"access_token":{"xms_cc":{"values":["cp1"]}}}
```

MSAL 라이브러리를 사용하는 경우에는 다음 코드를 사용합니다.

```c#
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
 .WithDefaultRedirectUri()
 .WithAuthority(authority)
 .WithClientCapabilities(new [] {"cp1"})
 .Build();*
```

Microsoft.Identity.Web을 사용하는 이러한 작업은 구성 파일에 다음 코드를 추가할 수 있습니다.

```c#
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    // the remaining settings
    // ... 
    "ClientCapabilities": [ "cp1" ]
},
```

Azure AD에 요청이 표시되는 방법의 예는 다음과 같습니다.

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
&claims=%7B%22access_token%22%3A%7B%22xms_cc%22%3A%7B%22values%22%3A%5B%22cp1%22%5D%7D%7D%7D
```

클레임 매개 변수에 대한 기존 페이로드가 이미 있는 경우 기존 집합에 이를 추가합니다.

예를 들어 조건 액세스 인증 컨텍스트 작업에서 다음 응답이 이미 있는 경우

```json
{"access_token":{"acrs":{"essential":true,"value":"c25"}}}
```

기존 **클레임** 페이로드에 클라이언트 기능을 추가합니다.

```json
{"access_token":{"xms_cc":{"values":["cp1"]},"acrs":{"essential":true,"value":"c25"}}}
```

## <a name="receiving-xms_cc-claim-in-an-access-token"></a>액세스 토큰에서 xms_cc 클레임 수신

클라이언트 응용 프로그램이 클레임 챌린지를 처리할 수 있는지 여부에 대한 정보를 수신하려면 API 구현자가 **xms_cc** 애플리케이션 매니페스트에서 선택적 클레임으로 요청해야 합니다.

액세스 토큰에서 값이 "cp1"인 **xms_cc** 클레임은 클라이언트 응용 프로그램이 클레임 챌린지를 처리할 수 있는지 식별하는 신뢰할 수 있는 방법입니다. **xms_cc** 는 클라이언트가 "xms_cc"을 사용하여 클레임 요청을 보내는 경우에도 액세스 토큰에서 항상 발급되지는 않는 선택적 클레임입니다. 액세스 토큰이 **xms_cc** 클레임을 포함하려면 리소스 애플리케이션(즉, API 구현자)이 애플리케이션 매니페스트에서 [선택적 클레임](active-directory-optional-claims.md)으로 xms_cc를 요청해야 합니다. 선택적 클레임으로 요청된 경우 클라이언트 응용 프로그램이 클레임 요청에서 **xms_cc** 를 보내는 경우에만 **xms_cc** 가 액세스 토큰에 추가됩니다. **xms_cc** 클레임 요청 값은 액세스 토큰의 **xms_cc** 클레임 값(알려진 값인 경우)으로 포함됩니다. 현재 알려진 유일한 값은 **cp1** 입니다.

이 값은 대/소문자를 구분하지 않으며 순서가 지정되지 않았습니다. 두 개 이상의 값이 **xms_cc** 클레임 요청에 지정된 경우 해당 값은 **xms_cc** 클레임의 값으로 다중값 컬렉션이 됩니다.

요청 대상:

```json
{ "access_token": { "xms_cc":{"values":["cp1","foo", "bar"] } }}
```

만약

```json
"xms_cc": ["cp1", "foo", "bar"]
```

**cp1**, **foo** 및 **bar** 가 잘 알려진 기능이라면 액세스 토큰 클레임이 발생하게 됩니다.

다음은 **xms_cc**  [선택적 클레임이](active-directory-optional-claims.md) 요청된 후 앱 매니페스트의 모습입니다

```c#
"optionalClaims":
{
    "accessToken": [
    {
        "additionalProperties": [],
        "essential": false,
        "name": "xms_cc",
        "source": null
    }],
    "idToken": [],
    "saml2Token": []
}
```

이제 API는 클라이언트가 클레임 챌린지를 처리할 수 있는지 여부에 따라 응답을 사용자 지정할 수 있습니다.

C#의 예제

```c#
Claim ccClaim = context.User.FindAll(clientCapabilitiesClaim).FirstOrDefault(x => x.Type == "xms_cc");
if (ccClaim != null && ccClaim.Value == "cp1")
{
    // Return formatted claims challenge as this client understands this
}
else
{
    // Throw generic exception
    throw new UnauthorizedAccessException("The caller does not meet the authentication bar to carry our this operation. The service cannot allow this operation");
}
```

## <a name="next-steps"></a>다음 단계

- [Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- [애플리케이션에서 지속적인 액세스 권한 평가를 사용하도록 설정된 API를 사용하는 방법](app-resilience-continuous-access-evaluation.md)
- [중요한 데이터 및 작업에 대한 세분화된 조건부 액세스](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
