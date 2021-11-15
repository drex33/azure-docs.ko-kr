---
title: 기밀 클라이언트 애플리케이션을 MSAL.NET으로 마이그레이션
titleSuffix: Microsoft identity platform
description: 기밀 클라이언트 애플리케이션을 .NET용 Azure Active Directory 인증 라이브러리에서 .NET용 Microsoft 인증 라이브러리로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 4e362812224f8e538d7a36dfa5378e23f6438d6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462825"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>기밀 클라이언트 애플리케이션을 ADAL.NET에서 MSAL.NET으로 마이그레이션

이 문서에서는 기밀 클라이언트 애플리케이션을 .NET용 Azure Active Directory 인증 라이브러리(ADAL.NET)에서 .NET용 Microsoft 인증 라이브러리(MSAL.NET)로 마이그레이션하는 방법을 알아봅니다. 기밀 클라이언트 애플리케이션은 대신 다른 서비스를 호출하는 웹앱, 웹 API 및 디먼 애플리케이션입니다. 기밀 애플리케이션에 관한 자세한 내용은 [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)를 참조하세요. 앱이 ASP.NET Core를 기반으로 하는 경우 [Microsoft.Identity.Web](microsoft-identity-web.md)을 사용합니다.

앱 등록의 경우:

- 새 앱 등록을 만들 필요가 없습니다. (동일한 클라이언트 ID를 유지합니다.)
- 사전 인증(관리자가 동의한 API 권한)을 변경할 필요가 없습니다.

## <a name="migration-steps"></a>마이그레이션 단계

1. 앱에서 ADAL.NET을 사용하여 코드를 찾습니다.

   기밀 클라이언트 애플리케이션에서 ADAL을 사용하는 코드는 `AuthenticationContext`를 인스턴스화하고 다음 매개 변수를 사용하여 `AcquireTokenByAuthorizationCode` 또는 `AcquireTokenAsync`의 재정의를 하나 호출합니다.

   - `resourceId` 문자열입니다. 이 변수는 호출하려는 웹 API의 앱 ID URI입니다.
   - `IClientAssertionCertificate`의 인스턴스이거나 `ClientAssertion`입니다. 이 인스턴스는 앱의 ID를 증명하기 위해 앱의 클라이언트 자격 증명을 제공합니다.

1. ADAL.NET을 사용하는 앱이 있음을 확인한 후 MSAL.NET NuGet 패키지 [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)를 설치하고 프로젝트 라이브러리 참조를 업데이트합니다. 자세한 내용은 [NuGet 패키지 설치](https://www.bing.com/search?q=install+nuget+package)를 참조하세요. 토큰 캐시 직렬 변환기를 사용하려는 경우 [Microsoft.Identity.Web.TokenCache](https://www.nuget.org/packages/Microsoft.Identity.Web.TokenCache)도 설치합니다.

1. 기밀 클라이언트 시나리오에 따라 코드를 업데이트합니다. 일부 단계는 공통이며 모든 기밀 클라이언트 시나리오에 적용됩니다. 다른 단계는 시나리오마다 고유합니다. 

   기밀 클라이언트 시나리오는 다음과 같습니다.

   - 웹앱, 웹 API 및 데몬 콘솔 애플리케이션에서 지원하는 [디먼 시나리오](?tabs=daemon#migrate-daemon-apps).
   - 사용자를 대신하여 다운스트림 웹 API를 호출하는 웹 API에서 지원하는 [다운스트림 웹 API를 호출하는 웹 API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis).
   - 사용자를 로그인하고 다운스트림 웹 API를 호출하는 웹앱에서 지원하는 [웹 API 호출 웹앱](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis).

인증서 및 캐싱을 처리하기 위해 ADAL.NET에 래퍼를 제공했을 수 있습니다. 이 문서에서는 동일한 방법을 사용하여 ADAL.NET에서 MSAL.NET으로 마이그레이션하는 프로세스를 보여 줍니다. 그러나 이 코드는 데모용으로만 사용됩니다. 해당 래퍼를 복사하여 붙여넣거나 코드에 그대로 통합하지 마세요.

## <a name="daemon"></a>[디먼](#tab/daemon)

### <a name="migrate-daemon-apps"></a>디먼 앱 마이그레이션

디먼 시나리오에서는 OAuth2.0 [클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)을 사용합니다. 서비스 간 호출이라고도 합니다. 앱에서는 사용자 대신 토큰을 획득하지 않고 자체적으로 토큰을 획득합니다.

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>코드에서 디먼 시나리오를 사용하는지 확인

앱의 ADAL 코드는 다음 매개 변수를 사용하여 `AuthenticationContext.AcquireTokenAsync`에 대한 호출을 포함하는 경우 디먼 시나리오를 사용합니다.

- 첫 번째 매개 변수인 리소스(앱 ID URI)
- 두 번째 매개 변수인 `IClientAssertionCertificate` 또는 `ClientAssertion`

`AuthenticationContext.AcquireTokenAsync`에 `UserAssertion` 형식의 매개 변수가 없습니다. 그렇다면 앱이 웹 API이고 [다운스트림 웹 API를 호출하는 웹 API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) 시나리오를 사용하고 있는 것입니다.

#### <a name="update-the-code-of-daemon-scenarios"></a>디먼 시나리오 코드 업데이트

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IConfidentialClientApplication.AcquireTokenClient` 호출로 바꿉니다.

다음은 디먼 시나리오의 ADAL.NET 및 MSAL.NET 코드를 비교한 것입니다.

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              // .WithTenantId(specificTenant)
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>토큰 캐싱의 이점

메모리 내 캐시를 활용하려면 `IConfidentialClientApplication` 인스턴스를 멤버 변수에 보관해야 합니다. 토큰을 요청할 때마다 기밀 클라이언트 애플리케이션을 다시 만들면 토큰 캐시의 이점을 얻을 수 없습니다.

기본 메모리 내 앱 토큰 캐시를 사용하지 않도록 선택하는 경우 `AppTokenCache`를 직렬화해야 합니다. 마찬가지로 분산 토큰 캐시를 구현하려면 `AppTokenCache`를 직렬화해야 합니다. 자세한 내용은 [웹앱 또는 웹 API(기밀 클라이언트 애플리케이션)용 토큰 캐시](msal-net-token-cache-serialization.md?tabs=aspnet) 및 샘플 [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)를 참조하세요.

[디먼 시나리오에 대해 자세히 알아보고](scenario-daemon-overview.md) 새 애플리케이션에서 MSAL.NET 또는 Microsoft.Identity.Web으로 구현하는 방법을 알아보세요.

## <a name="web-api-calling-downstream-web-apis"></a>[다운스트림 웹 API를 호출하는 웹 API](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>다운스트림 웹 API를 호출하는 웹 API 마이그레이션

다운스트림 웹 API를 호출하는 웹 API는 OAuth2.0 [OBO(대체)](v2-oauth2-on-behalf-of-flow.md) 흐름을 사용합니다. 웹 API는 HTTP **권한 있는** 헤더에서 검색된 액세스 토큰을 사용하고 이 토큰의 유효성을 검사합니다. 그런 다음, 이 토큰은 다운스트림 웹 API를 호출하기 위해 토큰과 교환됩니다. 이 토큰은 ADAL.NET과 MSAL.NET 모두에서 `UserAssertion` 인스턴스로 사용됩니다.

#### <a name="find-out-if-your-code-uses-obo"></a>코드에서 OBO를 사용하는지 확인

앱의 ADAL 코드는 다음 매개 변수를 사용하여 `AuthenticationContext.AcquireTokenAsync`에 대한 호출을 포함하는 경우 OBO를 사용합니다.

- 첫 번째 매개 변수인 리소스(앱 ID URI)
- 두 번째 매개 변수인 `IClientAssertionCertificate` 또는 `ClientAssertion`
- `UserAssertion` 형식의 매개 변수

#### <a name="update-the-code-by-using-obo"></a>OBO를 사용하여 코드 업데이트

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IConfidentialClientApplication.AcquireTokenOnBehalfOf` 호출로 바꿉니다.

ADAL.NET 및 MSAL.NET의 샘플 OBO 코드를 비교한 내용은 다음과 같습니다.

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
:::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              // .WithTenantId(specificTenant) 
              // See https://aka.ms/msal.net/withTenantId
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>토큰 캐싱의 이점

OBO의 토큰 캐싱에서는 분산 토큰 캐시를 사용해야 합니다. 자세한 내용은 [웹앱 또는 웹 API(기밀 클라이언트 애플리케이션)용 토큰 캐시](msal-net-token-cache-serialization.md?tabs=aspnet)를 참조하고 [샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)를 읽어 보세요.

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[다운스트림 웹 API를 호출하는 웹 API에 대해 자세히 알아보고](scenario-web-api-call-api-overview.md) 새 애플리케이션에서 MSAL.NET 또는 Microsoft.Identity.Web으로 구현하는 방법을 알아보세요.

## <a name="web-app-calling-web-apis"></a>[웹 API를 호출하는 웹앱](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>웹 API를 호출하는 웹앱 마이그레이션

앱에서 ASP.NET Core를 사용하는 경우 모든 사항을 처리하는 Microsoft.Identity.Web으로 업데이트하는 것이 좋습니다. 빠른 프레젠테이션은 [Microsoft.Identity.Web 일반 공급 발표](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0)를 참조하세요. 웹앱에서 사용하는 방법에 관한 자세한 내용은 [웹앱에서 Microsoft.Identity.Web을 사용하는 이유](https://aka.ms/ms-id-web/webapp)를 참조하세요.

사용자를 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹앱은 OAuth2.0 [인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용합니다. 일반적으로 다음과 같습니다.

1. 웹앱은 인증 코드 흐름의 첫 번째 구간을 실행하여 사용자를 로그인합니다. Microsoft ID 플랫폼 권한 부여 엔드포인트로 이동하여 이를 수행합니다. 사용자가 로그인하고 필요한 경우 다단계 인증을 수행합니다. 이 작업의 결과로 앱은 인증 코드를 받습니다. 이 단계에서는 인증 라이브러리가 사용되지 않습니다.
1. 앱은 인증 코드 흐름의 두 번째 구간을 실행합니다. 인증 코드를 사용하여 액세스 토큰, ID 토큰, 새로 고침 토큰을 가져옵니다. 애플리케이션은 Microsoft ID 플랫폼 엔드포인트에서 보안 토큰을 제공하는 URI인 `redirectUri` 값을 제공해야 합니다. 앱에서 해당 URI를 수신한 후 일반적으로 ADAL 또는 MSAL의 `AcquireTokenByAuthorizationCode`를 호출하여 코드를 사용하고 토큰 캐시에 저장할 토큰을 가져옵니다.
1. 앱은 ADAL 또는 MSAL을 사용하여 `AcquireTokenSilent`를 호출하여 필요한 웹 API를 호출하기 위한 토큰을 가져올 수 있습니다. 이 작업은 웹앱 컨트롤러에서 수행됩니다.

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>코드에서 인증 코드 흐름을 사용하는지 확인

앱의 ADAL 코드는 `AuthenticationContext.AcquireTokenByAuthorizationCodeAsync`에 대한 호출을 포함하는 경우 인증 코드 흐름을 사용합니다.

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>인증 코드 흐름을 사용하여 코드 업데이트

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IConfidentialClientApplication.AcquireTokenByAuthorizationCode` 호출로 바꿉니다.

ADAL.NET 및 MSAL.NET의 샘플 권한 부여 코드 흐름을 비교한 내용은 다음과 같습니다.

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
using Microsoft.Identity.Client;
using Microsoft.Identity.Web;
using System;
using System.Security.Claims;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority
    = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 public IConfidentialClientApplication CreateApplication()
 {
  IConfidentialClientApplication app;

  app = ConfidentialClientApplicationBuilder.Create(ClientId)
               .WithCertificate(certificate)
               .WithAuthority(authority)
               .WithRedirectUri(redirectUri.ToString())
               .WithLegacyCacheCompatibility(false)
               .Build();

  // Add a token cache. For details about other serialization
  // see https://aka.ms/msal-net-cca-token-cache-serialization
  app.AddInMemoryTokenCache();

  return app;
 }

 // Called from 'code received event'.
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId,
      string authorizationCode)
 {
  IConfidentialClientApplication app = CreateApplication();

  var authResult = await app.AcquireTokenByAuthorizationCode(
                  new[] { $"{resourceId}/.default" },
                  authorizationCode)
                  .ExecuteAsync()
                  .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

`AcquireTokenByAuthorizationCode`를 호출하면 인증 코드가 수신될 때 토큰 캐시에 토큰이 추가됩니다. 다른 리소스 또는 테넌트의 추가 토큰을 획득하려면 컨트롤러에서 `AcquireTokenSilent`를 사용합니다.

```csharp
public partial class AuthWrapper
{
 // Called from controllers
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId2,
      string authority)
 {
  IConfidentialClientApplication app = CreateApplication();
  AuthenticationResult authResult;

  var scopes = new[] { $"{resourceId2}/.default" };
  var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());

  try
  {
   // try to get an already cached token
   authResult = await app.AcquireTokenSilent(
               scopes,
               account)
                // .WithTenantId(specificTenantId) 
                // See https://aka.ms/msal.net/withTenantId
                .ExecuteAsync().ConfigureAwait(false);
  }
  catch (MsalUiRequiredException)
  {
   // The controller will need to challenge the user
   // including asking for claims={ex.Claims}
   throw;
  }
  return authResult;
 }
}
```

#### <a name="benefit-from-token-caching"></a>토큰 캐싱의 이점

웹앱에서 `AcquireTokenByAuthorizationCode`를 사용하기 때문에 앱은 토큰 캐싱에 분산 토큰 캐시를 사용해야 합니다. 자세한 내용은 [웹앱 또는 웹 API용 토큰 캐시](msal-net-token-cache-serialization.md?tabs=aspnet)를 참조하고 [샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)를 읽어 보세요.


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

#### <a name="handling-msaluirequiredexception"></a>MsalUiRequiredException 처리

컨트롤러가 다른 범위/리소스에 대한 토큰을 자동으로 획득하려고 하면 MSAL.NET은 `MsalUiRequiredException`을 throw할 수 있습니다. 예를 들어 사용자가 다시 로그인해야 하거나 리소스에 대한 액세스에 더 많은 클레임이 필요한 경우(예를 들어 조건부 액세스 정책으로 인해) 이 작업이 필요합니다. 완화에 대한 자세한 내용은 [MSAL.NET에서 오류 및 예외를 처리](msal-error-handling-dotnet.md)하는 방법을 참조하세요.

[웹 API를 호출하는 웹앱에 대해 자세히 알아보고](scenario-web-app-call-api-overview.md) 새 애플리케이션에서 MSAL.NET 또는 Microsoft.Identity.Web으로 구현하는 방법을 알아보세요.

---

## <a name="msal-benefits"></a>MSAL 혜택

앱에 대한 MSAL.NET 주요 혜택은 다음과 같습니다.

- **복원력**. MSAL.NET을 사용하면 다음을 통해 앱의 복원력을 향상할 수 있습니다.

   - Azure AD CCS(캐시된 자격 증명 서비스)의 혜택 CCS는 Azure AD 백업으로 작동합니다.
   - 호출하는 API가 [지속적 액세스 평가](app-resilience-continuous-access-evaluation.md)를 통해 수명이 긴 토큰을 사용으로 설정하는 경우 토큰을 사전에 갱신합니다.

- **보안**. 호출하려는 웹 API에 필요한 경우 PoP(소유 증명) 토큰을 획득할 수 있습니다. 자세한 내용은 [MSAL.NET의 소유 증명 토큰](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)을 참조하세요.

- **성능 및 확장성**. 캐시를 ADAL.NET과 공유할 필요가 없으면 기밀 클라이언트 애플리케이션(`.WithLegacyCacheCompatibility(false)`)을 만들 때 레거시 캐시 호환성을 사용하지 않게 설정합니다. 그러면 성능이 크게 향상됩니다.
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>문제 해결

### <a name="msalserviceexception"></a>MsalServiceException

다음 문제 해결 정보에서는 두 가지를 가정합니다. 

- ADAL.NET 코드가 작동했습니다.
- 동일한 클라이언트 ID를 유지하여 MSAL로 마이그레이션했습니다.

다음 메시지 중 하나와 함께 예외가 발생하면 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

다음 단계를 사용하여 예외 문제를 해결할 수 있습니다.

1. 최신 버전의 [MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)을 사용하고 있는지 확인합니다.
1. 기밀 클라이언트 애플리케이션을 빌드할 때 설정한 기관 호스트와 ADAL과 함께 사용한 기관 호스트가 비슷한지 확인합니다. 특히 동일한 [클라우드](msal-national-cloud.md)(Azure Government, Azure 중국 21Vianet 또는 Azure 독일)인가요?

### <a name="msalclientexception"></a>MsalClientException

다중 테넌트 애플리케이션에서는 애플리케이션을 빌드할 때 공동 인증 기관을 지정하지만 웹 API를 호출할 때 특정 테넌트(예: 사용자의 테넌트)를 대상으로 지정하려는 시나리오가 있을 수 있습니다. MSAL.NET 4.37.0이므로 애플리케이션 생성 시 `.WithAzureRegion`을 지정할 때 토큰 요청 중에 `.WithAuthority`를 사용하여 기관을 더 이상 지정할 수 없습니다. 이 경우 이전 버전의 MSAL.NET에서 업데이트할 때 다음 오류가 발생합니다.

  `MsalClientException - "You configured WithAuthority at the request level, and also WithAzureRegion. This is not supported when the environment changes from application to request. Use WithTenantId at the request level instead."`

이 문제를 해결하려면 AcquireTokenXXX 식에서 `.WithAuthority`를 `.WithTenantId`로 대체합니다. GUID 또는 도메인 이름을 사용하여 테넌트를 지정합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
- [ADAL.NET과 MSAL.NET 앱 간의 차이점](msal-net-differences-adal-net.md)
- [MSAL.NET에서 토큰 캐시 직렬화](msal-net-token-cache-serialization.md)