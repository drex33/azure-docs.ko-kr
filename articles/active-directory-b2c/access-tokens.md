---
title: 액세스 토큰 요청 - Azure Active Directory B2C
description: Azure Active Directory B2C에서 액세스 토큰을 요청하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 2d6aeddaaf2efc039150d7060f76f46045bd0efe
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008316"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 액세스 토큰 요청

*액세스 토큰* 에는 API에 부여된 권한을 식별하기 위해 Azure AD B2C(Azure Active Directory B2C)에서 사용할 수 있는 클레임이 포함되어 있습니다. 리소스 서버를 호출할 때 액세스 토큰은 HTTP 요청에 있어야 합니다. 액세스 토큰은 Azure AD B2C의 응답에서 **access_token** 으로 표시됩니다.

이 문서에서는 웹 애플리케이션 및 Web API에 대한 액세스 토큰을 요청하는 방법을 보여 줍니다. Azure AD B2C의 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](tokens-overview.md)를 참조하세요.

> [!NOTE]
> **Azure AD B2C에서는 Web API 체인(On-Behalf-Of)을 지원하지 않습니다.** - 많은 아키텍처에는 다른 다운스트림 웹 API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 다른 서비스를 호출하는 Web API 백 엔드가 있는 클라이언트에서 일반적입니다. On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 웹 API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다. On-Behalf-Of는 Azure AD에 등록된 애플리케이션에서 작동하지만 토큰을 발급하는 테넌트(Azure AD 또는 Azure AD B2C)에 관계없이 Azure AD B2C 등록된 애플리케이션에는 작동하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).
- 아직 수행하지 않았다면 [Azure Active Directory B2C 테넌트에 Web API 애플리케이션을 추가](add-web-api-application.md)합니다.

## <a name="scopes"></a>범위

범위는 보호된 리소스에 대한 사용 권한을 관리하는 방법을 제공합니다. 액세스 토큰을 요청할 때 클라이언트 애플리케이션에서 요청의 **범위** 매개 변수에서 원하는 권한을 지정해야 합니다. 예를 들어 `https://contoso.onmicrosoft.com/api`의 **앱 ID URI** 가 있는 API에 대한 **범위 값** `read`를 지정하기 위해 범위는 `https://contoso.onmicrosoft.com/api/read`가 됩니다.

범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 같은 요청에 여러 권한을 얻기 위해 요청의 단일 **범위** 매개 변수에 공백으로 구분된 여러 항목을 추가할 수 있습니다.

다음 예제에서는 URL에서 디코딩된 범위를 보여 줍니다.

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

다음 예제에서는 URL에서 인코딩된 범위를 보여 줍니다.

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

클라이언트 애플리케이션에 부여된 것보다 더 많은 범위가 필요한 경우 하나 이상의 사용 권한이 부여되면 호출이 성공합니다. 결과 액세스 토큰의 **scp** 클레임은 성공적으로 부여된 권한으로만 채워집니다. 

### <a name="openid-connect-scopes"></a>OpenID Connect 범위

OpenID Connect 표준은 몇 가지 특별한 범위 값을 지정합니다. 다음과 같은 범위는 사용자 프로필에 액세스하기 위한 권한을 나타냅니다.

- **openid** - ID 토큰을 요청합니다.
- **offline_access** - [인증 코드 흐름](authorization-code-flow.md)을 사용하여 새로 고침 토큰을 요청합니다.
- **00000000-0000-0000-0000-000000000000** - 클라이언트 ID를 범위로 사용할 경우 동일한 클라이언트 ID가 나타내는 사용자 고유의 서비스 또는 웹 API에 대해 사용할 수 있는 액세스 토큰이 앱에 필요합니다.

`/authorize` 요청의 **response_type** 매개 변수가 `token`을 포함하는 경우 **scope** 매개 변수는 권한이 부여될 하나 이상의 리소스 범위(`openid` 및 `offline_access` 이외)를 포함해야 합니다. 그렇지 않으면 `/authorize` 요청이 실패합니다.

## <a name="request-a-token"></a>토큰 요청

액세스 토큰을 요청하려면 인증 코드가 필요합니다. 다음은 `/authorize` 엔드포인트에 대한 인증 코드 요청 예제입니다. 사용자 지정 도메인은 액세스 토큰과 함께 사용하도록 지원되지 않습니다. 요청 URL에서 tenant-name.onmicrosoft.com 도메인을 사용합니다.

다음 예에서는 쿼리 문자열에서 이러한 값을 바꿉니다.

- `<tenant-name>` - Azure AD B2C 테넌트의 이름.
- `<policy-name>` - 사용자 지정 정책 또는 사용자 흐름의 이름.
- `<application-ID>` - 사용자 흐름을 지원하기 위해 등록한 웹 애플리케이션의 애플리케이션 식별자
- `<application-ID-URI>` - 클라이언트 애플리케이션의 **API 노출**  블레이드에서 설정한 애플리케이션 식별자 URI입니다.
- `<scope-name>` - 클라이언트 애플리케이션의 **API 노출** 아래에 추가한 범위의 이름입니다.
- `<redirect-uri>` - 클라이언트 애플리케이션을 등록할 때 입력한 **리디렉션 URI**.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=<application-ID-URI>/<scope-name>
&response_type=code
```

인증 코드에 대한 응답은 다음 예제와 유사해야 합니다.

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

인증 코드를 성공적으로 받은 후에는 이 코드를 사용하여 다음과 같이 액세스 토큰을 요청할 수 있습니다. 매개 변수는 HTTP POST 요청의 본문에 있습니다.

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=<application-ID-URI>/<scope-name>
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```
 
다음 응답과 비슷한 내용이 표시됩니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

https://jwt.ms 를 사용하여 반환된 액세스 토큰을 검사하는 경우 다음 예제와 유사한 내용이 표시되어야 합니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 토큰을 구성](configure-tokens.md)하는 방법을 알아봅니다.
