---
title: 웹 API를 호출하는 토큰 얻기(디먼 앱) - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 디먼 앱을 빌드하는 방법 알아보기(토큰 획득)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9e037a1ba1ba3c0820321662d4f3feffa6dd2b35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050625"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>웹 API를 호출하는 디먼 앱 - 토큰 획득

기밀 클라이언트 애플리케이션을 생성한 후에는 `AcquireTokenForClient`를 호출하고, 범위를 전달하고, 필요에 따라 토큰을 강제로 새로 고치는 방법으로 앱의 토큰을 획득할 수 있습니다.

## <a name="scopes-to-request"></a>요청할 범위

클라이언트 자격 증명 흐름에 대해 요청할 범위는 `/.default` 앞에 있는 리소스의 이름입니다. 이 표기법은 애플리케이션 등록 시 고정으로 선언된 *애플리케이션 수준 권한* 을 사용하도록 Azure AD(Azure Active Directory)에 지시합니다. 또한 이러한 API 권한은 테넌트 관리자가 부여해야 합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```JavaScript
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'], // e.g. 'https://graph.microsoft.com/.default'
};
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python에서 구성 파일은 다음 코드 조각과 같습니다.

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD(v1.0) 리소스

클라이언트 자격 증명에 사용되는 범위는 항상 리소스 ID 뒤에 `/.default`이 따라와야 합니다.

> [!IMPORTANT]
> MSAL에서 버전 1.0 액세스 토큰을 허용하는 리소스에 대한 액세스 토큰을 요청하는 경우, Azure AD는 마지막 슬래시 앞에 있는 모든 항목을 리소스 식별자로 사용하여 요청된 범위에서 원하는 대상 그룹을 구문 분석합니다.
> 따라서 Azure SQL Database(`https://database.windows.net`)와 같이 리소스가 슬래시로 끝나는 대상을 예상하는 경우(Azure SQL Database의 경우 `https://database.windows.net/`) `https://database.windows.net//.default` 범위를 요청해야 합니다. (이중 슬래시 참고) MSAL.NET 문제 [#747: `Resource url's trailing slash is omitted, which caused sql auth failure`](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)도 참조하세요.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

앱의 토큰을 얻으려면 플랫폼에 따라 `AcquireTokenForClient` 또는 그와 동등한 API를 사용합니다.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient는 애플리케이션 토큰 캐시를 사용합니다

MSAL.NET에서는 `AcquireTokenForClient`가 애플리케이션 토큰 캐시를 사용합니다. (다른 모든 AcquireToken *XX* 메서드는 사용자 토큰 캐시를 사용합니다.) `AcquireTokenForClient`를 호출하기 전에 먼저 `AcquireTokenSilent`을 호출하지 마세요. `AcquireTokenSilent`가 *사용자* 토큰 캐시를 사용하기 때문입니다. `AcquireTokenForClient`는 *애플리케이션* 토큰 캐시 자체를 확인하고 업데이트합니다.

# <a name="java"></a>[Java](#tab/java)

이 코드는 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)에서 가져왔습니다.

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

아래 코드 조각에서는 MSAL 노드의 기밀 클라이언트 애플리케이션에서 토큰을 획득하는 방법을 설명합니다.

```JavaScript
try {
    const authResponse = await cca.acquireTokenByClientCredential(tokenRequest);
    console.log(authResponse.accessToken) // display access token
} catch (error) {
    console.log(error);
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

---

### <a name="protocol"></a>프로토콜

선택한 언어에 대한 라이브러리가 아직 없는 경우 프로토콜을 직접 사용하는 것이 좋습니다.

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용하여 토큰 요청에 액세스

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>두 번째 사례: 인증서를 사용하여 토큰 요청에 액세스

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

자세한 내용은 다음 프로토콜 설명서를 참조하세요. [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>문제 해결

### <a name="did-you-use-the-resourcedefault-scope"></a>resource/.default 범위를 사용했나요?

유효하지 않은 범위를 사용했음을 나타내는 오류 메시지가 표시되면 `resource/.default` 범위를 사용하지 않았을 수 있습니다.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>관리자 동의를 제공을 잊으셨나요? 디먼 앱에는 관리자 동의가 필요합니다.

API를 호출할 때 **작업 오류를 완료하기에 불충분한 권한** 이 있는 경우 테넌트 관리자는 애플리케이션에 권한을 부여해야 합니다. 위의 클라이언트 앱 등록 6단계를 참조하세요.
일반적으로 다음과 같은 오류 페이지가 표시됩니다.

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>사용자 고유의 API를 호출하고 있나요?

사용자 고유의 웹 API를 호출하고 디먼 앱의 앱 등록에 앱 권한을 추가할 수 없는 경우, 웹 API에서 앱 역할을 노출시켰나요?

자세한 내용은 [애플리케이션 권한 노출(앱 역할)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) 및 특히 [Azure AD가 허용된 클라이언트에게만 웹 API에 대한 토큰을 발급하도록 보장](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients)을 참조하세요.

## <a name="next-steps"></a>다음 단계

# <a name="net"></a>[.NET](#tab/dotnet)

이 시나리오의 다음 문서인 [웹 API 호출](./scenario-daemon-call-api.md?tabs=dotnet)로 이동합니다.

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서인 [웹 API 호출](./scenario-daemon-call-api.md?tabs=java)로 이동합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 시나리오의 다음 문서인 [웹 API 호출](./scenario-daemon-call-api.md?tabs=nodejs)로 이동합니다.

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서인 [웹 API 호출](./scenario-daemon-call-api.md?tabs=python)로 이동합니다.

---
