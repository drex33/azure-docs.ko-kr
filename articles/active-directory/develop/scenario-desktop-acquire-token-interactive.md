---
title: 웹 API를 호출하기 위한 토큰을 대화형으로 획득(데스크톱 앱) | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 데스크톱 앱을 빌드하여 앱의 토큰을 대화형으로 획득하는 방법을 알아봅니다.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838711"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>웹 API를 호출하는 데스크톱 앱: 대화형으로 토큰 획득

다음 예에서는 Microsoft Graph로 사용자 프로필을 읽기 위해 대화형으로 토큰을 가져오는 최소한의 코드를 보여 줍니다.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET에서

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>필수 매개 변수

`AcquireTokenInteractive`에는 오직 하나의 필수 매개 변수 ``scopes``가 있습니다. 이 매개 변수에는 토큰이 필요한 범위를 정의하는 문자열 열거형이 포함되어 있습니다. 토큰이 Microsoft Graph용인 경우, 필요한 범위는 각 Microsoft Graph API의 API 참조의 “권한” 섹션에서 확인할 수 있습니다. 예를 들어, [사용자 연락처를 나열하려면](/graph/api/user-list-contacts) “User.Read”, “Contacts.Read” 범위를 사용해야 합니다. 자세한 내용은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference)를 참조하세요.

Android에서는 예에서 볼 수 있듯이 `.WithParentActivityOrWindow`를 사용하여 부모 작업도 지정해야 합니다. 이렇게 해야 토큰이 이 상호 작용 후에 부모 작업으로 돌아갑니다. 부모 작업을 지정하지 않으면 `.ExecuteAsync()`를 호출할 때 예외가 발생합니다.

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI는 대화형이기 때문에 중요합니다. `AcquireTokenInteractive`에는 부모 UI를 지원하는 플랫폼에서 부모 UI를 지정할 수 있는 선택적 매개 변수가 하나 있습니다. 데스크톱 애플리케이션에서 사용할 경우 `.WithParentActivityOrWindow`는 플랫폼에 따라 다른 형식을 갖습니다. 또는 화면에 로그인 대화 상자가 표시되는 위치를 컨트롤하지 않으려는 경우 선택적 부모 창 매개 변수를 생략하여 창을 만들 수 있습니다. 이는 다른 백 엔드 서비스에 대한 호출을 전달하는 데 사용되는 명령줄 기반 애플리케이션에 적용되며, 사용자 조작을 위한 창이 필요하지 않습니다.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

설명:

- .NET Standard에서 필요한 `object`는 Android의 경우 `Activity`, iOS의 경우`UIViewController`, Mac의 경우 `NSWindow`, Windows의 경우 `IWin32Window` 또는 `IntPr`입니다.
- Windows에서는 임베디드 브라우저가 올바른 UI 동기화 컨텍스트를 가져올 수 있도록 UI 스레드에서 `AcquireTokenInteractive`를 호출해야 합니다. UI 스레드에서 호출하지 않으면 메시지가 올바르게 펌프되지 않고 UI가 교착 상태에 빠질 수 있습니다. 이미 UI 스레드에 있지 않은 경우 UI 스레드에서 MSAL(Microsoft 인증 라이브러리)을 호출하는 한 가지 방법은 WPF에서 `Dispatcher`를 호출하는 것입니다.
- WPF를 사용하는 경우, WPF 컨트롤에서 창을 가져오려면 `WindowInteropHelper.Handle` 클래스를 사용할 수 있습니다. 그러면 호출이 WPF 컨트롤(`this`)에서 이루어집니다.

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`는 프롬프트를 지정하여 사용자와의 대화형 작업을 제어하는 데 사용됩니다. 정확한 동작은 [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt) 구조체를 사용하여 제어할 수 있습니다.

이 구조체는 다음 상수를 정의합니다.

- `SelectAccount`는 사용자가 세션을 보유한 계정을 포함하는 계정 선택 대화 상자를 제시하도록 STS를 강제합니다. 이 옵션은 애플리케이션 개발자가 여러 ID 중에서 사용자가 선택할 수 있도록 하려는 경우에 유용합니다. 이 옵션은 MSAL이 ID 공급자에게 `prompt=select_account`를 보내도록 유도합니다. 이 옵션이 기본값입니다. 이 옵션은 계정, 사용자의 세션 유무와 같은 사용 가능한 정보를 기반으로 최상의 환경을 제공합니다. 이 옵션은 반드시 변경해야 할 이유가 있지 않은 한 변경하지 마세요.
- `Consent`는 애플리케이션 개발자가 이전에 동의가 부여된 경우에도 사용자에게 동의를 묻는 프롬프트를 강제로 표시할 수 있도록 지원합니다. 이 경우 MSAL이 ID 공급자에게 `prompt=consent`를 보냅니다. 이 옵션은 조직 거버넌스에서 애플리케이션이 사용될 때마다 사용자에게 동의 대화 상자를 표시하도록 요구하는 일부 보안 우선 애플리케이션에서 사용할 수 있습니다.
- `ForceLogin`은 애플리케이션 개발자가 사용자 프롬프트가 필요하지 않은 경우에도 서비스가 사용자에게 자격 증명 프롬프트를 표시하도록 설정하는 것을 지원합니다. 이 옵션은 토큰 획득에 실패한 경우 사용자가 다시 로그인하도록 할 때 유용합니다. 이 경우 MSAL이 ID 공급자에게 `prompt=login`을 보냅니다. 이 옵션은 조직 거버넌스에서 사용자가 애플리케이션의 특정 부분에 액세스할 때마다 다시 로그인하도록 요구하는 보안 우선 애플리케이션에서 사용되기도 합니다.
- `Create`는 ID 공급자에게 `prompt=create`를 전송하여 External Identities에 사용되는 등록 환경을 활성화합니다. 이 프롬프트는 Azure AD B2C 앱 관련하여 전송되어서는 안 됩니다. 자세한 내용은 [앱에 셀프 서비스 등록 사용자 흐름 추가](../external-identities/self-service-sign-up-user-flow.md)를 참조하세요.
- `Never`(.NET 4.5 및 WinRT만 해당)는 사용자에게 프롬프트를 표시하지 않으며 그 대신 숨겨진 임베디드 웹 보기에 저장된 쿠키를 사용하려고 합니다. 자세한 내용은 MSAL.NET의 웹 보기를 참조하세요. 이 옵션을 사용하는 것은 실패할 수 있습니다. 실패하는 경우 `AcquireTokenInteractive`가 UI 상호 작용이 필요함을 알리기 위해 예외를 발생시킵니다. 이 경우 다른 `Prompt` 매개 변수를 사용해야 합니다.
- `NoPrompt`는 ID 공급자에게 프롬프트를 보내지 않습니다. 따라서 ID 공급자는 사용자에게 최상의 로그인 환경(Single Sign-On 또는 특정 계정)을 제공하기로 결정하게 됩니다. 이 옵션은 Azure AD(Azure Active Directory) B2C 프로필 편집 정책에서도 필수입니다. 자세한 내용은 [Azure AD B2C specifics](https://aka.ms/msal-net-b2c-specificities)(Azure AD B2C 관련 사항)를 참조하세요.

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

이 메서드를 사용하면 포함된 웹 보기 또는 시스템 웹 보기(사용 가능한 경우)를 강제로 사용하도록 지정할 수 있습니다. 자세한 내용은 [Usage of web browsers](msal-net-web-browsers.md)(웹 브라우저의 용도)를 참조하세요.

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 사용자가 처음에 몇 가지 리소스에 미리 동의하도록 하고자 하며 MSAL.NET/Microsoft ID 플랫폼에서 통상적으로 사용되는 증분 동의를 사용하지 않으려는 고급 시나리오에서 사용됩니다. 자세한 내용은 [처음에 여러 리소스에 대한 사용자 동의 받기](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)를 참조하세요.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

웹 UI는 브라우저를 호출하는 메커니즘입니다. 이 메커니즘은 전용 UI WebBrowser 컨트롤일 수도 있고 브라우저 열기를 위임하는 방법일 수도 있습니다.
MSAL은 대부분의 플랫폼에 대한 웹 UI 구현을 제공하지만, 다음과 같이 브라우저를 직접 호스트해야 하는 경우가 있습니다.

- MSAL에서 명시적으로 지원하지 않는 플랫폼(예: 데스크톱용 Mono, Blazor, Unity).
- 애플리케이션의 UI를 테스트할 때 Selenium과 함께 사용할 수 있는 자동화된 브라우저를 사용하려는 경우.
- MSAL을 실행하는 앱과 브라우저가 서로 다른 프로세스에 있는 경우.

##### <a name="at-a-glance"></a>개요

이를 위해서는 MSAL에 `start Url`을 제공해야 합니다. Start Url은 최종 사용자가 사용자 이름과 같은 항목을 입력할 수 있도록 브라우저에 표시되어야 합니다.
인증이 완료되면 앱에서 MSAL로 Azure AD에서 제공한 코드를 포함하는 `end Url`을 전달해야 합니다.
`end Url`의 호스트는 항상 `redirectUri`입니다. `end Url`을 가로채려면 다음 중 하나를 수행하세요.

- `redirect Url`이 적중될 때까지 브라우저 리디렉션을 모니터링합니다.
- 개발자가 모니터링하는 URL로 브라우저가 리디렉션되도록 합니다.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi는 확장성 지점임

`WithCustomWebUi`는 공용 클라이언트 애플리케이션에서 자체 UI를 제공하는 데 사용할 수 있는 확장성 지점입니다. 사용자가ID 공급자의 /Authorize 엔드포인트를 통과하여 로그인 및 동의하도록 할 수도 있습니다. 이렇게 하면 MSAL.NET이 인증 코드를 사용하고 토큰을 가져올 수 있습니다. 예를 들어, WithCustomWebUi는 Visual Studio에서 Electrons 애플리케이션(예: Visual Studio Feedback)으로 하여금 웹 상호 작용을 제공하되 대부분의 작업을 MSAL.NET이 처리하도록 하려는 경우에 사용됩니다. UI 자동화를 제공하려는 경우에도 사용할 수 있습니다. 공용 클라이언트 애플리케이션에서 MSAL.NET은 PKCE(Proof Key for Code Exchange) 표준을 사용하여 보안이 준수되도록 합니다. 오직 MSAL.NET만 코드를 사용할 수 있습니다. 자세한 내용은 [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)(RFC 7636 - OAuth 공용 클라이언트에 의한 코드 교환을 위한 증명 키)를 참조하세요.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi 사용

`.WithCustomWebUI`를 사용하려면 다음 단계를 수행합니다.

  1. `ICustomWebUi` 인터페이스를 구현합니다. 자세한 내용은 [이 웹 사이트](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)를 참조하세요. `AcquireAuthorizationCodeAsync` 메서드 하나를 구현하고 MSAL.NET에 의해 계산된 인증 코드 URL을 적용합니다. 그런 다음 사용자가 ID 공급자와의 상호 작용을 통과하도록 한 후, ID 공급자가 구현을 호출하는 데 사용했을 URL과 인증 코드를 함께 반환하도록 합니다. 문제가 발생하면 MSAL과의 매끄러운 상호 작용을 위해 구현에서 `MsalExtensionException` 예외를 발생시켜야 합니다.
  2. `AcquireTokenInteractive` 호출에서, 사용자 지정 웹 UI 인스턴스를 전달하는 `.WithCustomUI()` 한정자를 사용합니다.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>테스트 자동화에서 사용되는 ICustomWebUi의 구현 예 SeleniumWebUI

MSAL.NET 팀은 UI 테스트가 이 확장성 메커니즘을 사용할 수 있도록 수정했습니다. 관심이 있다면 MSAL.NET 소스 코드에서 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 클래스를 살펴보세요.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions를 사용하여 우수한 환경 제공하기

MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions)에서 다음을 지정할 수 있습니다.

- 시스템 웹 브라우저에서 로그인 또는 동의 오류가 발생한 경우 이동할 URI(`BrowserRedirectError`) 또는 표시할 HTML 조각(`HtmlMessageError`).
- 성공적인 로그인 또는 동의가 이루어진 경우 이동할 URI(`BrowserRedirectSuccess`) 또는 표시할 HTML 조각(`HtmlMessageSuccess`).
- 시스템 브라우저를 시작하기 위해 실행할 작업. `OpenBrowserAsync` 대리자를 설정하여 자체 구현을 제공할 수 있습니다. 이 클래스는 두 개의 브라우저를 위한 기본 구현도 제공합니다. Microsoft Edge의 경우 `OpenWithEdgeBrowserAsync`이고, [Chromium의 Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium)의 경우 `OpenWithChromeEdgeBrowserAsync`입니다.

이 구조체를 사용하려면 다음 예와 같은 코드를 작성합니다.

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>그 밖의 선택적 매개 변수

`AcquireTokenInteractive`의 다른 모든 선택적 매개 변수에 대해 자세히 알아보려면 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

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

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
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

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

MSAL Node에서 PKCE(Proof Key for Code Exchange)를 사용하여 인증 코드 흐름을 통해 토큰을 획득합니다. 프로세스에는 두 단계가 있습니다. 우선 애플리케이션에서 인증 코드를 생성하는 데 사용할 수 있는 URL을 가져옵니다. 이 URL은 선택한 브라우저에서 열 수 있습니다. 여기서 사용자는 자신의 자격 증명을 입력하고 인증 코드를 사용하여 `redirectUri`(앱 등록 시 등록됨)로 다시 리디렉션됩니다. 두 번째로, 애플리케이션은 수신된 인증 코드를 액세스 토큰에 대해 교환하는 `acquireTokenByCode()` 메서드로 전달합니다.

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };

    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 1.7 이상은 대화형 토큰 획득 방법을 제공합니다.

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [데스크톱 앱에서 웹 API를 호출](scenario-desktop-call-api.md)합니다.
