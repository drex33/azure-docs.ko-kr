---
title: 웹 계정 관리자(데스크톱 앱)를 사용하여 웹 API를 호출하는 토큰 획득 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하여 웹 계정 관리자를 사용하는 앱에 대한 토큰을 획득하는 데스크톱 앱을 빌드하는 방법을 알아봅니다.
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
ms.openlocfilehash: b06c771d14d0257ac69db9d3a9a40d3eadeba3e7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436263"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-wam"></a>웹 API를 호출하는 데스크톱 앱: WAM을 사용하여 토큰 획득

MSAL은 OS와 함께 Windows 10 구성 요소인 웹 계정 관리자를 호출할 수 있습니다. 이 구성 요소는 인증 브로커 역할을 하며 앱 사용자는 Windows 세션에서 로그인한 계정과 같이 Windows에서 알려진 계정과의 통합을 활용할 수 있습니다.

## <a name="availability"></a>가용성

MSAL 4.25 이상은 UWP, .NET Classic, .NET Core 3.x 및 .NET 5에서 WAM을 지원합니다.

.NET Classic 및 .NET Core 3.x의 경우 WAM 기능이 완전히 지원되지만 MSAL과 함께 [Microsoft.Identity.Client.Desktop](https://www.nuget.org/packages/Microsoft.Identity.Client.Desktop/) 패키지에 대한 참조를 추가해야 하며 `WithBroker()` 대신 `.WithWindowsBroker()`를 호출합니다.

.NET 5의 경우 `net5.0`뿐만 아니라 대상 `net5.0-windows10.0.17763.0`(또는 이상)에만 사용할 수 있습니다. csproj에 `<SupportedOSPlatformVersion>7</SupportedOSPlatformVersion>`을 추가하면 앱이 이전 버전의 Windows에서 계속 실행됩니다. WAM을 사용할 수 없는 경우 MSAL은 브라우저를 사용합니다.

## <a name="wam-value-proposition"></a>WAM 가치 제안

WAM과 같은 인증 브로커를 사용하면 많은 이점이 있습니다.

- 보안 강화(앱에서 강력한 새로 고침 토큰을 관리할 필요가 없음)
- Windows Hello, 조건부 액세스 및 FIDO 키에 대한 향상된 지원
- Windows "이메일 및 계정" 보기와 통합
- 더 나은 Single Sing-On(사용자가 암호를 다시 입력할 필요가 없음)
- 대부분의 버그 수정 및 향상된 기능이 Windows와 함께 제공될 예정입니다.

## <a name="wam-limitations"></a>WAM 제한 사항

- B2C 기관은 지원되지 않습니다.
- Win10, Win Server 2016, Win Server 2019에서 사용할 수 있습니다. Mac, Linux 및 이전 Windows에서 MSAL은 브라우저로 대체됩니다.

## <a name="wam-calling-pattern"></a>WAM 호출 패턴

다음 패턴을 사용하여 WAM을 사용할 수 있습니다.

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

`.WithBroker(true)`을 호출합니다. 브로커가 없는 경우(예: Win8.1, Mac 또는 Linux) MSAL이 브라우저로 대체됩니다. 리디렉션 URI 규칙은 브라우저에 적용됩니다.

## <a name="redirect-uri"></a>리디렉션 URI

WAM 리디렉션 URI는 MSAL에서 구성할 필요가 없지만 앱 등록에서 구성해야 합니다.

### <a name="win32-net-framework--net-5"></a>Win32(.NET 프레임워크/.NET 5)

```
ms-appx-web://microsoft.aad.brokerplugin/{client_id}
```

### <a name="uwp"></a>UWP
```csharp
            // returns smth like S-1-15-2-2601115387-131721061-1180486061-1362788748-631273777-3164314714-2766189824
            string sid = WebAuthenticationBroker.GetCurrentApplicationCallbackUri().Host.ToUpper();

            // the redirect uri you need to register
            string redirectUri = $"ms-appx-web://microsoft.aad.brokerplugin/{sid}";
```

## <a name="token-cache-persistence"></a>토큰 캐시 지속성

MSAL은 내부 WAM 계정 ID를 저장해야 하므로 MSAL의 토큰 캐시를 유지하는 것이 중요합니다. 그렇지 않으면 앱을 다시 시작하면 `GetAccounts` API에서 일부 계정이 누락됩니다. UWP에서 MSAL은 토큰 캐시를 저장할 위치를 알고 있습니다.

## <a name="getaccounts"></a>GetAccounts

`GetAccounts`는 이전에 앱에 대화형으로 로그인한 사용자의 계정을 반환합니다.

이 외에도 WAM은 Windows에서 구성된 OS 차원의 회사 및 학교 계정을 나열할 수 있습니다(Win32 앱의 경우, UWP 앱은 나열하지 않음). 이 기능을 옵트인하려면 `WindowsBrokerOptions`에서 `ListWindowsWorkAndSchoolAccounts`를 **true** 로 설정합니다. 아래와 같이 사용하도록 설정할 수 있습니다.

```csharp
.WithWindowsBrokerOptions(new WindowsBrokerOptions()
{
    // GetAccounts will return Work and School accounts from Windows
    ListWindowsWorkAndSchoolAccounts = true,

    // Legacy support for 1st party apps only
    MsaPassthrough = true
})
```

>[!NOTE]
> 개인 정보 보호를 위해 Microsoft(즉, outlook.com 등) 계정은 Win32 또는 UWP에 나열되지 않습니다.

애플리케이션은 Windows에서 계정을 제거할 수 없습니다. 

## <a name="removeasync"></a>RemoveAsync

- MSAL의 토큰 캐시에서 모든 계정 정보를 제거합니다(여기에는 MSA(개인 계정) 계정 정보 및 MSAL에서 캐시로 복사한 기타 계정 정보가 포함됨).
- 앱 전용(OS 차원이 아닌) 계정을 제거합니다.

>[!NOTE]
> 앱은 OS 계정을 제거할 수 없습니다. 사용자만 이 작업을 수행할 수 있습니다. OS 계정이 `RemoveAsync`로 전달된 다음, `GetAccounts`는 `ListWindowsWorkAndSchoolAccounts`를 사용하여 호출되는 경우에도 동일한 OS 계정이 반환됩니다.

## <a name="other-considerations"></a>기타 고려 사항

- WAM의 대화형 작업은 UI 스레드에 있어야 합니다. MSAL은 UI 스레드에 없는 경우 의미 있는 예외를 throw합니다. 콘솔 앱에는 적용되지 않습니다.
- `WithAccount`는 MSAL 계정이 원래 WAM을 통해 가져온 경우 또는 WAM이 Windows에서 회사 및 학교 계정을 찾을 수 있는 경우 가속화된 인증 환경을 제공합니다.
- WAM은 Windows에서 동일한 사용자 이름을 가진 회사 및 학교 계정을 찾을 수 없으면 사용자 이름 필드를 로그인 힌트로 미리 채울 수 없습니다.
- WAM이 가속 인증 환경을 제공할 수 없는 경우 계정 선택기가 표시됩니다. 사용자는 새 계정을 추가할 수 있습니다.

!["WAM 계정 선택기"](media/scenario-desktop-acquire-token-wam/wam-account-picker.png)

- 새 계정은 Windows에서 자동으로 기억됩니다. 회사 및 학교에서는 조직의 디렉터리에 조인하거나 완전히 옵트아웃할 수 있습니다. 이 경우 계정이 "이메일 및 계정" 아래에 표시되지 않습니다. Microsoft 계정이 Windows에 자동으로 추가됩니다. 앱은 이러한 계정을 프로그래밍 방식으로 나열할 수 없습니다(계정 선택기를 통해서만 가능).

## <a name="troubleshooting"></a>문제 해결

MSAL을 사용하는 앱이 관리자 권한 프로세스로 실행되는 경우 WAM 내에서 이러한 호출 중 일부는 다른 프로세스 보안 수준으로 인해 실패할 수 있습니다. 내부적으로 MSAL.NET은 네이티브 Windows 메서드([COM](/windows/win32/com/the-component-object-model))를 사용하여 WAM과 통합합니다. 버전 4.32.0부터 MSAL은 앱 프로세스가 상승되고 WAM이 계정을 반환하지 않은 것을 감지하면 설명 오류 메시지를 표시합니다.

한 가지 해결 방법은 가능한 경우 앱을 관리자 권한으로 실행하지 않는 것입니다. 또 다른 잠재적 해결 방법은 앱이 시작될 때 `WindowsNativeUtils.InitializeProcessSecurity` 메서드를 호출하는 것입니다. 이렇게 하면 WAM에서 사용하는 프로세스의 보안이 동일한 수준으로 설정됩니다. 예제는 [이 샘플 앱](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/devapps/WAM/NetCoreWinFormsWam/Program.cs#L18-L21)을 참조하세요. 그러나 이 해결 방법은 기본 CLR 동작과 같은 외부 요인으로 인해 성공하도록 보장되지 않습니다. 이 경우 `MsalClientException`이 throw됩니다. 추가 정보는 문제 [#2560](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/2560)을 참조하세요.

## <a name="sample"></a>샘플

[WAM을 사용하는 WPF 샘플](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2)

[Xamarin과 함께 WAM을 사용하는 UWP 샘플](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/2-With-broker)

---
## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서로 이동하여 [데스크톱 앱에서 웹 API를 호출](scenario-desktop-call-api.md)합니다.
