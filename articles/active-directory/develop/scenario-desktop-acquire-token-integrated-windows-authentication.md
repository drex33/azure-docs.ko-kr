---
title: Windows 통합 인증을 사용하여 웹 API를 호출하기 위한 토큰 획득(데스크톱 앱) | Azure
titleSuffix: Microsoft identity platform
description: Windows 통합 인증을 사용하여 앱에 대한 토큰을 획득하기 위해 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f589603fc5a7bb1b9e3a62d997cf9e64efebf5af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017942"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>웹 API를 호출하는 데스크톱 앱: Windows 통합 인증을 사용하여 토큰 획득

도메인 또는 Azure AD 조인된 컴퓨터에서 도메인 사용자를 로그인하려면 IWA(Windows 통합 인증)를 사용합니다.

## <a name="constraints"></a>제약 조건

- Windows 통합 인증은 *페더레이션+* 사용자(즉, Active Directory에서 만들어지고 Azure AD에서 지원하는 사용자)만 지원합니다. Active Directory 지원 없이 Azure AD에서 직접 만들어진 사용자(‘관리형’ 사용자)는 이 인증 흐름을 사용할 수 없습니다. 이러한 제한은 사용자 이름 및 암호 흐름에 영향을 주지 않습니다.
- IWA는 [MFA(다단계 인증)](../authentication/concept-mfa-howitworks.md)를 우회하지 않습니다. MFA가 구성된 경우, MFA 질문이 필요하면 IWA가 실패할 수 있습니다. MFA에는 사용자 상호 작용이 필요하기 때문입니다.

    IWA는 비대화형이지만 MFA에는 사용자 상호 작용이 필요합니다. ID 공급자가 MFA를 언제 요구할지는 개발자가 아니라 테넌트 관리자가 제어합니다. 일반적으로 다른 국가/지역에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되어 있지 않을 때, 그리고 간혹 VPN을 통해 연결되어 있을 때도 MFA가 필요합니다. 결정적 규칙 세트가 적용되지 않습니다. Azure AD는 AI를 사용하여 MFA가 필요한지 여부를 계속해서 학습합니다. IWA가 실패할 경우 대화형 인증이나 디바이스 코드 흐름과 같은 사용자 프롬프트를 사용하세요.

- `PublicClientApplicationBuilder`에 포함되어 전달되는 인증 기관은 다음이어야 합니다.
  - 테넌트(`https://login.microsoftonline.com/{tenant}/` 형식, 여기서 `tenant`는 테넌트 ID 또는 테넌트와 연결된 도메인을 나타내는 GUID).
  - 회사 및 학교 계정의 경우: `https://login.microsoftonline.com/organizations/`.
  - Microsoft 개인 계정은 지원되지 않습니다. /common 테넌트나 /consumers 테넌트는 사용할 수 없습니다.

- Windows 통합 인증은 자동 흐름이기 때문에,
  - 애플리케이션의 사용자가 사전에 애플리케이션 사용에 동의했어야 합니다.
  - 또는 테넌트 관리자자 사전에 테넌트의 모든 사용자에 대해 애플리케이션 사용을 동의했어야 합니다.
  - 다시 말하면,
    - 개발자가 직접 Azure Portal에서 **권한 부여** 단추를 선택했어야 합니다.
    - 또는 테넌트 관리자가 애플리케이션 등록의 **API 권한** 탭에서 **{테넌트 도메인}에 대한 관리자 동의 권한 부여/해지** 를 선택했어야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)를 참조하세요.
    - 또는 사용자가 애플리케이션에 동의할 방법을 제공했어야 합니다. 자세한 내용은 [개별 사용자의 동의 요청](./v2-permissions-and-consent.md#requesting-individual-user-consent)을 참조하세요.
    - 또는 테넌트 관리자가 애플리케이션에 동의할 방법을 제공했어야 합니다. 자세한 내용은 [관리자 동의](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)를 참조하세요.

- 이 흐름은 .NET 데스크톱, .NET Core 및 UWP 앱에 대해 사용하도록 설정됩니다.

동의에 관한 자세한 내용은 [Microsoft ID 플랫폼 엔드포인트의 권한 및 동의](./v2-permissions-and-consent.md)를 참조하세요.

## <a name="learn-how-to-use-it"></a>사용 방법 알아보기

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET에서 다음을 사용합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

통상적으로는 하나의 매개 변수만 필요합니다(`scopes`). Windows 관리자가 정책을 어떻게 설정했는지에 따라, Windows 컴퓨터의 애플리케이션이 로그인한 사용자를 조회하는 것이 허용되지 않을 수 있습니다. 이 경우 두 번째 방법인 `.WithUsername()`을 사용하여 로그인한 사용자의 사용자 이름을 UPN 형식으로 전달합니다(예: `joe@contoso.com`).

다음 샘플에서는 가장 최근 사례를 보여 줍니다. 이때 발생할 수 있는 예외와 그 대응 방법에 대한 설명도 제시합니다.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication의 가능한 한정자 목록은 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

다음 발췌본은 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다.

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

이 흐름은 macOS에 적용되지 않습니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 흐름은 MSAL Node에서 아직 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

이 흐름은 MSAL Python에서 아직 지원되지 않습니다.

---
## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [데스크톱 앱에서 웹 API를 호출](scenario-desktop-call-api.md)합니다.
