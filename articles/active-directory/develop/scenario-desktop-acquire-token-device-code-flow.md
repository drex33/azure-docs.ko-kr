---
title: 디바이스 코드 흐름을 사용하여 웹 API를 호출하는 토큰 획득(데스크톱 앱) | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출해 디바이스 코드 흐름을 사용하여 앱에 대한 토큰을 획득하는 데스크톱 앱을 빌드하는 방법 알아보기
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
ms.openlocfilehash: dad9c8f85c778e2b75320a8ce9605e484ae39cfa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838718"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-device-code-flow"></a>웹 API를 호출하는 데스크톱 앱: 디바이스 코드 흐름을 사용하여 토큰 획득 

웹 컨트롤이 없는 명령줄 도구를 작성하고 있는데 이전 흐름을 사용할 수 없거나 사용하지 않고 싶다면 디바이스 코드 흐름을 사용하세요.

## <a name="device-code-flow"></a>디바이스 코드 흐름

Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요합니다. 자세한 내용은 [Usage of web browsers](https://aka.ms/msal-net-uses-web-browser)(웹 브라우저의 용도)를 참조하세요. 디바이스 코드 흐름은 웹 브라우저를 제공하지 않는 디바이스나 운영 체제에서 사용자를 인증하기 위해 사용자가 컴퓨터나 휴대폰과 같은 다른 디바이스를 사용하여 대화형으로 로그인할 수 있도록 지원합니다. 애플리케이션은 디바이스 코드 흐름을 사용하여 해당 디바이스 또는 운영 체제용으로 설계된 2단계 프로세스를 통해 토큰을 가져옵니다. 이러한 애플리케이션의 예로 iOT 또는 CLI(명령줄) 도구에서 실행되는 애플리케이션을 들 수 있습니다. 기본 개념은 다음과 같습니다.

1. 사용자 인증이 필요할 때마다 앱이 사용자에게 코드를 제공합니다. 사용자는 다른 디바이스(예: 인터넷에 연결된 스마트폰)를 사용하여 URL(예: `https://microsoft.com/devicelogin`)로 이동해야 합니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시됩니다. 사용자가 코드를 입력하면 웹 페이지는 동의 프롬프트, 다단계 인증(필요한 경우) 등을 포함하는 일반적인 인증 환경으로 사용자를 안내합니다.

2. 인증에 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

## <a name="use-it"></a>사용해 보기

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`은 `AcquireTokenWithDeviceCode` 메서드를 포함합니다.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는 다음과 같은 매개 변수를 받습니다.

- 액세스 토큰을 요청할 `scopes`.
- [`DeviceCodeResult`](/dotnet/api/microsoft.identity.client.devicecoderesult)를 수신하는 콜백.

다음 샘플 코드에서는 가장 최근 사례의 개요와 함께 발생할 수 있는 예외와 완화에 대한 설명도 제시합니다. 완전한 기능을 갖춘 코드 샘플은 GitHub의 [active-directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2)를 참조하세요.

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

# <a name="java"></a>[Java](#tab/java)

다음 발췌본은 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다.

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

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

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
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

다음 발췌본은 [MSAL Node 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/device-code)에서 가져온 것입니다.

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

다음 발췌본은 [MSAL Python 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)에서 가져온 것입니다.

```python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [데스크톱 앱에서 웹 API를 호출](scenario-desktop-call-api.md)합니다.
