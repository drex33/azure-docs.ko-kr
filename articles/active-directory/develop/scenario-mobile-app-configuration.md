---
title: 웹 API를 호출하는 모바일 앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱의 코드를 구성하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 00eeda0b831f58ed0a739521cff95133f2a24bd1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017980"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>웹 API를 호출하는 모바일 앱 구성

애플리케이션을 만든 후에 앱 등록 매개 변수를 사용하여 코드를 구성하는 방법을 알아봅니다. 모바일 애플리케이션은 생성 프레임 워크에 맞추는 것과 관련된 몇 가지 복잡성을 나타냅니다.

## <a name="microsoft-libraries-supporting-mobile-apps"></a>모바일 앱을 지원하는 Microsoft 라이브러리

다음 Microsoft 라이브러리는 모바일 앱을 지원합니다.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="instantiate-the-application"></a>애플리케이션 인스턴스화

### <a name="android"></a>Android

모바일 애플리케이션은 `PublicClientApplication` 클래스를 사용합니다. 인스턴스화하는 방법은 다음과 같습니다.

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS의 모바일 애플리케이션은 `MSALPublicClientApplication` 클래스를 인스턴스화해야 합니다. 클래스를 인스턴스화하려면 다음 코드를 사용합니다.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[추가 MSALPublicClientApplicationConfig 속성](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)은 기본 권한을 재정의하거나, 리디렉션 URI를 지정하거나, MSAL 토큰 캐싱의 동작을 변경할 수 있습니다.

### <a name="xamarin-or-uwp"></a>Xamarin 또는 UWP

이 섹션에서는 Xamarin.iOS, Xamarin Android 및 UWP 앱용 애플리케이션을 인스턴스화하는 방법을 설명합니다.

#### <a name="instantiate-the-application"></a>애플리케이션 인스턴스화

Xamarin 또는 UWP에서 애플리케이션을 인스턴스화하는 가장 간단한 방법은 다음 코드를 사용하는 것입니다. 이 코드에서 `ClientId`는 등록된 앱의 GUID입니다.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

추가 `With<Parameter>` 메서드는 부모 UI를 설정하고, 기본 권한을 재정의하고, 원격 분석에 대한 클라이언트 이름과 버전을 지정하고, 리디렉션 URI를 지정하며, 사용할 HTTP 팩토리를 지정합니다. 예를 들어 HTTP 팩터리는 프록시를 처리하고 원격 분석 및 로깅을 지정하는 데 사용될 수 있습니다.

다음 섹션에서는 애플리케이션 인스턴스화에 대한 자세한 정보를 제공합니다.

##### <a name="specify-the-parent-ui-window-or-activity"></a>부모 UI, 창 또는 활동 지정

Android에서 대화형 인증을 수행하기 전에 부모 활동을 전달합니다. iOS에서 브로커를 사용하는 경우 `ViewController`를 전달합니다. UWP에서와 동일한 방식으로 부모 창을 전달할 수 있습니다. 토큰을 획득하는 경우에 전달합니다. 그러나 앱을 만들 때 `UIParent`를 반환하는 대리자로 콜백을 지정할 수도 있습니다.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android에서는 [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)를 사용하는 것이 좋습니다. 결과 `PublicClientApplication` 작성기 코드는 다음 예제와 같습니다.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>더 많은 앱 빌드 매개 변수 찾기

`PublicClientApplicationBuilder`에서 사용할 수 있는 모든 방법의 목록은 [메서드 목록](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)을 참조하세요.

`PublicClientApplicationOptions`에 표시되는 모든 옵션에 대한 설명은 [참조 문서](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)를 참조하세요.

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS에 대한 작업

Xamarin iOS에서 MSAL.NET을 사용하는 경우 다음 작업을 수행합니다.

* [`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)에서 `OpenUrl` 함수 재정의 및 구현
* [키 집합 그룹 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [토큰 캐시 공유 사용](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [키 집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

자세한 내용은 [Xamarin iOS 고려 사항](msal-net-xamarin-ios-considerations.md)을 참조하세요.

## <a name="tasks-for-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL 작업

iOS 및 macOS용 MSAL을 사용하는 경우 다음 작업이 필요합니다.

* [`openURL` 콜백](#brokered-authentication-for-msal-for-ios-and-macos) 구현
* [키 집합 액세스 그룹 사용](howto-v2-keychain-objc.md)
* [브라우저 및 WebViews 사용자 지정](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin.Android 작업

Xamarin.Android를 사용하는 경우 다음 작업을 수행합니다.

- [인증 흐름의 대화형 부분이 종료되면 컨트롤을 MSAL로 되돌림](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Android 매니페스트 업데이트](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)
- [포함된 웹 보기 사용(선택 사항)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [필요에 따라 문제 해결](msal-net-xamarin-android-considerations.md#troubleshooting)

자세한 내용은 [Xamarin.Android 고려 사항](msal-net-xamarin-android-considerations.md)을 참조하세요.

Android의 브라우저에 대한 고려 사항은 [MSAL.NET의 Xamarin.Android 관련 고려 사항](msal-net-system-browser-android-considerations.md)을 참조하세요.

#### <a name="tasks-for-uwp"></a>UWP에 대한 작업

UWP에서는 기업 네트워크를 사용할 수 있습니다. 다음 섹션에서는 기업 시나리오에서 완료해야 하는 작업을 설명합니다.

자세한 내용은 [MSAL.NET의 UWP 관련 고려 사항](msal-net-uwp-considerations.md)을 참조하세요.

## <a name="configure-the-application-to-use-the-broker"></a>브로커를 사용하도록 애플리케이션 구성

Android 및 iOS에서 브로커를 사용하도록 설정합니다.

- **SSO(Single Sign-On)** : Azure AD(Azure Active Directory)에 등록된 디바이스에 SSO를 사용할 수 있습니다. SSO를 사용하는 경우 사용자는 각 애플리케이션에 로그인할 필요가 없습니다.
- **디바이스 식별**: 이 설정은 Azure AD 디바이스와 관련된 조건부 액세스 정책을 사용하도록 설정합니다. 인증 프로세스는 디바이스가 회사 계정에 가입했을 때 생성된 디바이스 인증서를 사용합니다.
- **애플리케이션 ID 확인**: 애플리케이션에서 브로커를 호출할 때 해당 리디렉션 URL을 전달합니다. 그러면 브로커가 URL을 확인합니다.

### <a name="enable-the-broker-on-xamarin"></a>Xamarin에서 브로커 사용

Xamarin에서 브로커를 사용하도록 설정하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용합니다. 기본적으로 `.WithBroker()`는 true로 설정됩니다.

Xamarin.iOS에 대해 조정된 인증을 사용하도록 설정하려면 이 문서에서 [Xamarin.ios 섹션](#enable-brokered-authentication-for-xamarin-ios)의 단계를 따릅니다.

### <a name="enable-the-broker-for-msal-for-android"></a>Android용 MSAL에 브로커를 사용하도록 설정

Android에서 브로커를 사용하도록 설정하는 방법에 대한 자세한 내용은 [에서 조정된 인증](msal-android-single-sign-on.md)을 참조하세요.

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에 브로커를 사용하도록 설정

조정된 인증은 iOS 및 macOS 용 MSAL에서 Azure AD 시나리오에 대해 기본적으로 사용됩니다.

다음 섹션에서는 Xamarin.iOS용 MSAL이나 iOS 및 macOS용 MSAL에 대한 브로커 인증 지원을 위해 애플리케이션을 구성하는 지침을 제공합니다. 두 지침 세트에서는 일부 단계가 다릅니다.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Xamarin iOS에 대해 조정된 인증 사용

이 섹션의 단계에 따라 Xamarin.iOS 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정합니다.

#### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 사용

브로커 지원은 기본적으로 사용되지 않습니다. 개별 `PublicClientApplication` 클래스에 대해 사용하도록 설정합니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication` 클래스를 생성하는 경우 `WithBroker()` 매개 변수를 사용합니다. `WithBroker()` 매개 변수는 기본적으로 true로 설정됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2단계: 콜백을 처리하도록 AppDelegate 업데이트

MSAL.NET이 브로커를 호출하면 브로커는 애플리케이션을 다시 호출합니다. `AppDelegate.OpenUrl` 메서드를 사용하여 콜백합니다. MSAL에서 브로커의 응답을 대기하므로 애플리케이션은 협력하여 MSAL.NET을 다시 호출해야 합니다. 다음 코드에 나와 있는 것처럼 메서드를 재정의 하도록 `AppDelegate.cs` 파일을 업데이트하여 이 동작을 설정합니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

이 메서드는 애플리케이션이 시작될 때마다 호출됩니다. 이는 브로커의 응답을 처리하고 MSAL.NET이 시작한 인증 프로세스를 완료할 수 있는 기회입니다.

#### <a name="step-3-set-a-uiviewcontroller"></a>3단계: UIViewController() 설정

Xamarin iOS의 경우 일반적으로 개체 창을 설정하지 않아도 됩니다. 그러나 이 경우에는 브로커에서 응답을 보내고 받을 수 있도록 설정해야 합니다. `AppDelegate.cs`에서 개체 창을 설정하려면 `ViewController`를 설정합니다.

개체 창을 설정하려면 다음 단계를 수행합니다.

1. `AppDelegate.cs`에서 `App.RootViewController`를 새 `UIViewController()`로 설정합니다. 이렇게 설정하면 브로커에 대한 호출에 `UIViewController`가 포함됩니다. 올바르게 설정되지 않은 경우 다음 오류가 발생할 수 있습니다.

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. `AcquireTokenInteractive` 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`를 사용합니다. 사용할 개체 창에 대한 참조를 전달합니다. 예를 들면 다음과 같습니다.

    `App.cs`의 경우
    ```csharp
       public static object RootViewController { get; set; }
    ```
    `AppDelegate.cs`의 경우
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    `AcquireToken` 호출의 경우:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>4단계: URL 구성표 등록

MSAL.NET은 URL을 사용하여 브로커를 호출한 다음 앱에 브로커 응답을 다시 반환합니다. 왕복을 완료하려면 `Info.plist` 파일에 앱의 URL 구성표를 등록합니다.

앱의 URL 구성표를 등록하려면 다음 단계를 수행합니다.

1. `CFBundleURLSchemes` 앞에 `msauth`를 붙입니다.
1. 끝에 `CFBundleURLName`을 추가합니다. 이 패턴을 따릅니다.

   `$"msauth.(BundleId)"`

   여기에서 `BundleId`는 디바이스를 고유하게 식별합니다. 예를 들어 `BundleId`가 `yourcompany.xforms`이면 URL 구성표는 `msauth.com.yourcompany.xforms`입니다.

      이 URL 구성표는 브로커의 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다.

   ```xml
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5단계: LSApplicationQueriesSchemes 섹션에 추가

MSAL은 `–canOpenURL:`을 사용하여 브로커가 디바이스에 설치되어 있는지 확인합니다. iOS 9에서 Apple은 애플리케이션에서 쿼리할 수 있는 구성표를 잠궜습니다.

다음 코드 예제와 같이 `Info.plist` 파일의 `LSApplicationQueriesSchemes` 섹션에 `msauthv2`를 추가합니다.

```xml
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>iOS 및 macOS 용 MSAL에 대한 조정된 인증

조정된 인증은 Azure AD 시나리오에 대해 기본적으로 사용됩니다.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1단계: 콜백을 처리하도록 AppDelegate 업데이트

iOS 및 macOS 용 MSAL이 브로커를 호출할 때 브로커는 `openURL` 메서드를 사용하여 애플리케이션을 다시 호출합니다. MSAL에서 브로커의 응답을 대기하므로 애플리케이션은 협력하여 MSAL을 다시 호출해야 합니다. 다음 코드 예제에 나와 있는 것처럼 메서드를 재정의 하도록 `AppDelegate.m` 파일을 업데이트하여 이 기능을 설정합니다.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

iOS 13 이상에서 `UISceneDelegate`를 사용한 경우에는 대신에 MSAL 콜백을 `UISceneDelegate`의 `scene:openURLContexts:`에 추가합니다. MSAL `handleMSALResponse:sourceApplication:`은 각 URL에 대해 한 번만 호출해야 합니다.

자세한 내용은 [Apple 설명서](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)를 참조하세요.

#### <a name="step-2-register-a-url-scheme"></a>2단계: URL 구성표 등록

iOS 및 macOS용 MSAL은 URL을 사용하여 브로커를 호출한 다음 앱에 브로커 응답을 반환합니다. 왕복을 완료하려면 `Info.plist` 파일에 앱에 대한 URL 구성표를 등록합니다.

앱에 대한 구성표를 등록하려면 다음을 수행합니다.

1. 사용자 지정 URL 구성표 앞에 `msauth`를 붙입니다.

1. 구성표 끝에 번들 식별자를 추가합니다. 이 패턴을 따릅니다.

   `$"msauth.(BundleId)"`

   여기에서 `BundleId`는 디바이스를 고유하게 식별합니다. 예를 들어 `BundleId`가 `yourcompany.xforms`이면 URL 구성표는 `msauth.com.yourcompany.xforms`입니다.

    이 URL 구성표는 브로커의 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다. `msauth.(BundleId)://auth` 형식의 리디렉션 URI가 [Azure Portal](https://portal.azure.com)의 애플리케이션에 등록되어 있는지 확인합니다.

   ```xml
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3단계: LSApplicationQueriesSchemes 추가

설치된 경우 Microsoft Authenticator 앱에 대한 호출을 허용하려면 `LSApplicationQueriesSchemes`를 추가합니다.

> [!NOTE]
> `msauthv3` 구성표는 Xcode 11 이상을 사용하여 앱을 컴파일하는 경우에 필요합니다.

다음은 `LSApplicationQueriesSchemes`를 추가하는 방법의 예제입니다.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin.Android에 대해 조정된 인증

Android에서 브로커를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Xamarin.Android에서 조정된 인증](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android)을 참조하세요.

## <a name="next-steps"></a>다음 단계

본 시나리오의 다음 문서인 [토큰 획득](scenario-mobile-acquire-token.md)으로 이동합니다.
