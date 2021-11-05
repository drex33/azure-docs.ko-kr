---
title: 공용 클라이언트 애플리케이션을 MSAL.NET으로 마이그레이션
titleSuffix: Microsoft identity platform
description: .NET용 Azure Active Directory 인증 라이브러리에서 .NET용 Microsoft 인증 라이브러리로 공용 클라이언트 애플리케이션을 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 0bb1dc80d9bebf0488bfa2a272f5f7fa5303b31b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018018"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>ADAL.NET에서 MSAL.NET으로 공용 클라이언트 애플리케이션 마이그레이션

이 문서에서는 .NET용 Azure Active Directory 인증 라이브러리(ADAL.NET)에서 .NET용 Microsoft 인증 라이브러리(MSAL.NET)로 공용 클라이언트 애플리케이션을 마이그레이션하는 방법을 설명합니다. 공용 클라이언트 애플리케이션은 사용자를 대신하여 다른 서비스를 호출하는 Win32, WPF 및 UWP 앱과 모바일 앱을 비롯한 데스크톱 앱입니다. 공개 클라이언트 애플리케이션에 대한 자세한 내용은 [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)를 참조하세요.

## <a name="migration-steps"></a>마이그레이션 단계

1. 앱에서 ADAL.NET을 사용하여 코드를 찾습니다.

   공용 클라이언트 애플리케이션에서 ADAL을 사용하는 코드는 `AuthenticationContext`를 인스턴스화하고 다음 매개 변수를 사용하여 `AcquireTokenAsync` 재정의를 호출합니다.

   - `resourceId` 문자열입니다. 이 변수는 호출하려는 웹 API의 앱 ID URI입니다.
   - 앱 ID라고도 하는 애플리케이션의 식별자인 `clientId`.

2. ADAL.NET을 사용하는 앱이 있음을 확인한 후 MSAL.NET NuGet 패키지 [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)를 설치하고 프로젝트 라이브러리 참조를 업데이트합니다. 자세한 내용은 [NuGet 패키지 설치](https://www.bing.com/search?q=install+nuget+package)를 참조하세요.

3. 공용 클라이언트 애플리케이션 시나리오에 따라 코드를 업데이트합니다. 일부 단계는 공통이며 모든 공용 클라이언트 시나리오에 적용됩니다. 다른 단계는 시나리오마다 고유합니다. 

   공개 클라이언트 시나리오는 다음과 같습니다.

   - [웹 인증 관리자](scenario-desktop-acquire-token-wam.md)는 Windows에서 선호하는 브로커 기반 인증.
   - 로그인 프로세스를 완료하기 위해 사용자에게 웹 기반 인터페이스가 표시되는 [대화형 인증](scenario-desktop-acquire-token-interactive.md).
   - 사용자가 Windows 도메인에 로그인하는 데 사용한 것과 동일한 ID를 사용하여 서명하는 [Windows 통합 인증](scenario-desktop-acquire-token-integrated-windows-authentication.md)(도메인 조인 또는 Azure AD 조인 컴퓨터인 경우).
   - 사용자 이름/암호 자격 증명을 제공하여 로그인하는 [사용자 이름/암호](scenario-desktop-acquire-token-username-password.md).
   - 제한된 UX의 디바이스가 대체 디바이스에서 인증 흐름을 완료하기 위한 디바이스 코드를 표시하는 [디바이스 코드 흐름](scenario-desktop-acquire-token-device-code-flow.md).


## <a name="interactive"></a>[대화형](#tab/interactive)

대화형 시나리오에서 공개 클라이언트 애플리케이션은 브라우저에서 호스팅되는 로그인 사용자 인터페이스를 표시하며 사용자는 대화식으로 로그인해야 합니다.

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>코드에서 대화형 시나리오를 사용하는지 확인

대화형 인증을 사용하는 공개 클라이언트 애플리케이션의 앱에 대한 ADAL 코드는 `AuthenticationContext`를 인스턴스화하고 다음 매개 변수를 사용하여 `AcquireTokenAsync`에 대한 호출을 포함합니다.
 - 애플리케이션 등록을 나타내는 GUID인 `clientId`
 - 토큰을 요청하는 리소스를 나타내는 `resourceUrl`
 - 응답 URL인 URI
 - `PlatformParameters` 개체입니다. 

 #### <a name="update-the-code-for-interactive-scenarios"></a>대화형 시나리오에 대한 코드 업데이트

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IPublicClientApplication.AcquireTokenInteractive` 호출로 바꿉니다.

다음은 대화형 시나리오에 대한 ADAL.NET 및 MSAL.NET 코드를 비교한 것입니다.

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

위에 표시된 MSAL 코드는 권장되는 접근 방식인 WAM(웹 인증 관리자)을 사용합니다. WAM 없이 대화형 인증을 사용하려면 [대화형 인증](scenario-desktop-acquire-token-interactive.md)을 참조하세요.

## <a name="integrated-windows-authentication"></a>[Windows 통합 인증](#tab/iwa)

Windows 통합 인증에서 공용 클라이언트 애플리케이션이 Windows 도메인에 로그인하는 데 사용한 것과 동일한 ID를 사용하여 로그인합니다(도메인 조인 또는 Azure AD 조인 컴퓨터의 경우).

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>코드에서 Windows 통합 인증을 사용하는지 확인

앱의 ADAL 코드는 다음 매개 변수와 함께 `AuthenticationContextIntegratedAuthExtensions` 클래스의 확장 메서드로 사용할 수 있는 `AcquireTokenAsync`에 대한 호출이 포함된 경우 Windows 통합 인증 시나리오를 사용합니다.

- 토큰을 요청하는 리소스를 나타내는 `resource`
- 애플리케이션 등록을 나타내는 GUID인 `clientId`
- 토큰을 요청하려는 사용자를 나타내는 `UserCredential` 개체

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>Windows 통합 인증 시나리오에 대한 코드 업데이트

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth` 호출로 바꿉니다.

다음은 Windows 통합 인증 시나리오에 대한 ADAL.NET 및 MSAL.NET 코드를 비교한 것입니다.

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
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
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[Username Password](#tab/up)

사용자 이름 암호 인증은 사용자 이름/암호 자격 증명을 제공하여 로그인하는 곳입니다.
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>코드에서 사용자 이름 암호 인증을 사용하는지 확인

앱의 ADAL 코드는 다음 매개 변수와 함께 `AuthenticationContextIntegratedAuthExtensions` 클래스의 확장 메서드로 사용할 수 있는 `AcquireTokenAsync`에 대한 호출이 포함된 경우 사용자 이름 암호 인증 시나리오를 사용합니다.

- 토큰을 요청하는 리소스를 나타내는 `resource`
- 애플리케이션 등록을 나타내는 GUID인 `clientId`
- 토큰을 요청하려는 사용자의 사용자 이름과 암호가 포함된 `UserPasswordCredential` 개체.

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>사용자 이름 암호 인증 시나리오에 대한 코드 업데이트

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IPublicClientApplication.AcquireTokenByUsernamePassword` 호출로 바꿉니다.

다음은 사용자 이름 암호 시나리오에 대한 ADAL.NET 및 MSAL.NET 코드를 비교한 것입니다.

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

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
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[디바이스 코드](#tab/devicecode)

디바이스 코드 흐름 인증에서 제한된 UX의 디바이스가 대체 디바이스에서 인증 흐름을 완료하기 위한 디바이스 코드를 표시합니다.

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>코드에서 디바이스 코드 흐름 인증을 사용하는지 확인

앱의 ADAL 코드는 다음 매개 변수를 사용하여 `AuthenticationContext.AcquireTokenByDeviceCodeAsync`에 대한 호출을 포함하는 경우 디바이스 코드 흐름 시나리오를 사용합니다.
- 토큰을 요청하는 리소스의 `resourceID` 및 애플리케이션을 나타내는 GUID인 `clientId`로 인스턴스화되는 `DeviceCodeResult` 개체 인스턴스.

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>디바이스 코드 흐름 시나리오에 대한 코드 업데이트

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

이 경우 `AuthenticationContext.AcquireTokenAsync` 호출을 `IPublicClientApplication.AcquireTokenWithDeviceCode` 호출로 바꿉니다.

다음은 디바이스 코드 흐름 시나리오에 대한 ADAL.NET 및 MSAL.NET 코드를 비교한 것입니다.

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
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>MSAL 혜택

앱에 대한 MSAL.NET 주요 혜택은 다음과 같습니다.

- **복원력**. MSAL.NET을 사용하면 다음을 통해 앱의 복원력을 향상할 수 있습니다.

   - Azure AD CCS(캐시된 자격 증명 서비스)의 혜택 CCS는 Azure AD 백업으로 작동합니다.
   - 호출하는 API가 [지속적 액세스 평가](app-resilience-continuous-access-evaluation.md)를 통해 수명이 긴 토큰을 사용으로 설정하는 경우 토큰을 사전에 갱신합니다.

### <a name="troubleshooting"></a>문제 해결

다음 문제 해결 정보에서는 두 가지를 가정합니다. 

- ADAL.NET 코드가 작동했습니다.
- 동일한 클라이언트 ID를 유지하여 MSAL로 마이그레이션했습니다.

다음 메시지 중 하나와 함께 예외가 발생하면 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

다음 단계를 사용하여 예외 문제를 해결할 수 있습니다.

1. 최신 버전의 MSAL.NET을 사용하고 있는지 확인합니다.
1. 기밀 클라이언트 애플리케이션을 빌드할 때 설정한 기관 호스트와 ADAL과 함께 사용한 기관 호스트가 비슷한지 확인합니다. 특히 동일한 [클라우드](msal-national-cloud.md)(Azure Government, Azure 중국 21Vianet 또는 Azure 독일)인가요?

## <a name="next-steps"></a>다음 단계

[ADAL.NET과 MSAL.NET 앱의 차이점](msal-net-differences-adal-net.md)에 관해 자세히 알아봅니다.
[MSAL.NET의 토큰 캐시 직렬화](msal-net-token-cache-serialization.md)에 대해 자세히 알아보기
