---
title: Xamarin iOS 및 Android와 함께 브로커 사용 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator와 .NET용 Microsoft 인증 라이브러리(MSAL.NET)를 사용할 수 있는 Xamarin iOS 애플리케이션을 설정하는 방법에 대해 알아봅니다. .NET용 Azure AD 인증 라이브러리(ADAL.NET)에서 .NET용 Microsoft 인증 라이브러리(MSAL.NET)로 마이그레이션하는 방법에 대해서도 알아봅니다.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 823c01dc3c6f6856bfc8cac2249da6bc1ddc411f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054327"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin 애플리케이션에서 Microsoft Authenticator 또는 Intune 회사 포털 사용

Android와 iOS에서 Microsoft Authenticator 및 Android 관련 Microsoft Intune 회사 포털과 같은 브로커는 다음을 지원합니다.

- **SSO(Single Sign-On)** : 사용자가 각 애플리케이션에 로그인할 필요가 없습니다.
- **디바이스 식별**: 브로커가 디바이스 인증서에 액세스합니다. 이 인증서는 작업 공간에 조인될 때 디바이스에 생성됩니다.
- **애플리케이션 ID 확인**: 애플리케이션에서 브로커를 호출할 때 해당 리디렉션 URL을 전달합니다. 브로커가 URL을 확인합니다.

이러한 기능 중 하나를 사용하려면 `PublicClientApplicationBuilder.CreateApplication` 메서드를 호출할 때 `WithBroker()` 매개 변수를 사용합니다. `.WithBroker()` 매개 변수는 true로 기본 설정됩니다.

.NET용 Microsoft 인증 라이브러리(MSAL.NET)에서 조정된 인증 설정은 플랫폼에 따라 다릅니다.

* [iOS 애플리케이션](#brokered-authentication-for-ios)
* [Android 애플리케이션](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>iOS에 대한 조정된 인증

다음 단계에 따라 Xamarin.iOS 앱이 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 앱과 통신할 수 있도록 설정합니다. iOS 13을 대상으로 하는 경우 [Apple의 호환성이 손상되는 API 변경](./msal-net-xamarin-ios-considerations.md)에 대해 읽어보세요.

### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 사용

`PublicClientApplication`의 개별 인스턴스에 대해 브로커 지원을 사용하도록 설정해야 합니다. 지원은 기본적으로 사용되지 않습니다. `PublicClientApplicationBuilder`를 통해 `PublicClientApplication`을 만들 때 다음 예와 같이 `WithBroker()` 매개 변수를 사용합니다. `WithBroker()` 매개 변수는 true로 기본 설정됩니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2단계: 키 집합 액세스 사용

키 집합 액세스를 사용하도록 설정하려면 애플리케이션에 대한 키 집합 액세스 그룹이 있어야 합니다. `WithIosKeychainSecurityGroup()` API를 사용하여 애플리케이션을 만들 때 키 집합 액세스 그룹을 설정할 수 있습니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

자세한 내용은 [키 집합 액세스 사용](msal-net-xamarin-ios-considerations.md#enable-keychain-access)을 참조하세요.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3단계: 콜백을 처리하도록 AppDelegate 업데이트

MSAL.NET에서 브로커를 호출하면 브로커가 `AppDelegate` 클래스의 `OpenUrl` 메서드를 통해 애플리케이션을 다시 호출합니다. MSAL에서 브로커의 응답을 대기하므로 애플리케이션은 협력하여 MSAL.NET을 다시 호출해야 합니다. 이 협력을 사용하도록 설정하려면 *AppDelegate.cs* 파일을 업데이트하여 다음 메서드를 재정의합니다.

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

이 메서드는 애플리케이션이 시작될 때마다 호출됩니다. 이는 브로커의 응답을 처리하고 MSAL.NET이 시작한 인증 프로세스를 완료하는 기회로 사용됩니다.

### <a name="step-4-set-uiviewcontroller"></a>4단계: UIViewController() 설정

*AppDelegate.cs* 파일에서 개체 창을 설정합니다. 일반적으로 Xamarin iOS에 대한 개체 창을 설정하지 않아도 되지만 브로커의 응답을 보내고 받으려면 개체 창이 필요합니다.

개체 창을 설정하려면 다음을 수행합니다.

1. *AppDelegate.cs* 파일에서 `App.RootViewController`를 새 `UIViewController()`로 설정합니다. 이렇게 할당하면 브로커에 대한 호출에 `UIViewController`가 포함됩니다. 이 설정이 잘못 할당되면 다음과 같은 오류가 발생할 수 있습니다.

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive` 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`를 사용하고 사용할 개체 창에 대한 참조를 전달합니다.

    *App.cs* 에서

    ```csharp
       public static object RootViewController { get; set; }
    ```

    *AppDelegate.cs* 에서

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    `AcquireToken` 호출에서

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>5단계: URL 구성표 등록

MSAL.NET은 URL을 사용하여 브로커를 호출한 다음, 앱에 브로커 응답을 반환합니다. 왕복을 완료하려면 *info.plist* 파일에 앱에 대한 URL 구성표를 등록합니다.

`CFBundleURLSchemes` 이름에는 `msauth.`가 접두사로 포함되어야 합니다. 접두사 뒤에 `CFBundleURLName`을 붙입니다.

URL 구성표에서는 `BundleId`로 `$"msauth.(BundleId)"` 앱이 고유하게 식별됩니다. 따라서 `BundleId`가 `com.yourcompany.xforms`인 경우 URL 구성표는 `msauth.com.yourcompany.xforms`입니다.

> [!NOTE]
> 이 URL 구성표는 브로커의 응답을 받을 때 앱을 고유하게 식별하는 리디렉션 URI의 일부가 됩니다.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6단계: LSApplicationQueriesSchemes 섹션에 브로커 식별자 추가

MSAL은 `–canOpenURL:`을 사용하여 브로커가 디바이스에 설치되어 있는지 확인합니다. iOS 9에서 Apple은 애플리케이션에서 쿼리할 수 있는 구성표를 잠궜습니다.

다음 예와 같이 *info.plist* 파일의 `LSApplicationQueriesSchemes` 섹션에 `msauthv2`를 추가합니다.

```xml
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>7단계: 앱 등록에 리디렉션 URI 추가

브로커를 사용하는 경우 리디렉션 URI에는 추가 요구 사항이 있습니다. 리디렉션 URI의 형식은 다음과 _같아야 합니다_.

```csharp
$"msauth.{BundleId}://auth"
```

예를 들면 다음과 같습니다.

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

리디렉션 URI는 *info.plist* 파일에 포함한 `CFBundleURLSchemes` 이름과 일치합니다.

[Azure Portal](https://portal.azure.com)의 앱 등록에 리디렉션 URI를 추가합니다. 올바른 형식의 리디렉션 URI를 생성하려면 Azure Portal에서 **앱 등록** 을 사용하여 번들 ID에서 조정된 리디렉션 URI를 생성합니다.

**리디렉션 URI를 생성하려면**

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. **Azure Active Directory** > **앱 등록** > 등록된 앱을 선택합니다.
1. **인증** > **플랫폼 추가** > **iOS/macOS** 를 선택합니다.
1. 번들 ID를 입력하고 **구성** 을 선택합니다.

    코드에 포함할 **리디렉션 URI** 텍스트 상자에 표시되는 생성된 리디렉션 URI를 복사합니다.

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Azure Portal에서 생성된 리디렉션 URI가 있는 iOS 플랫폼 설정":::
1. **완료** 를 선택하여 리디렉션 URI 생성을 완료합니다.

## <a name="brokered-authentication-for-android"></a>Android에 대한 조정된 인증

### <a name="step-1-enable-broker-support"></a>1단계: 브로커 지원 사용

브로커 지원은 `PublicClientApplication`별로 사용하도록 설정됩니다. 기본적으로 사용하지 않도록 설정되어 있습니다. `PublicClientApplicationBuilder`를 통해 `IPublicClientApplication`를 만들 때 `WithBroker()` 매개 변수(기본적으로 true로 설정됨)를 사용합니다.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2단계: 콜백을 처리하도록 AppDelegate 업데이트

MSAL.NET에서 브로커를 호출하면 브로커는 `OnActivityResult()` 메서드로 애플리케이션을 다시 호출합니다. MSAL에서 브로커의 응답을 기다리므로 애플리케이션에서 결과를 MSAL.NET으로 라우팅해야 합니다.

다음과 같이 `OnActivityResult()` 메서드를 재정의하여 결과를 `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` 메서드로 라우팅합니다.

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

이 메서드는 브로커 애플리케이션이 시작될 때마다 호출되며 브로커의 응답을 처리하고 MSAL.NET에서 시작한 인증 프로세스를 완료할 수 있는 기회로 사용됩니다.

### <a name="step-3-set-an-activity"></a>3단계: 작업 설정

조정된 인증을 사용하도록 설정하려면 MSAL에서 브로커와 응답을 보내고 받을 수 있도록 작업을 설정합니다. 이렇게 하려면 작업(일반적으로 `MainActivity`)을 부모 개체인 `WithParentActivityOrWindow(object parent)`에 제공합니다.

예를 들어, `AcquireTokenInteractive()` 호출에서 다음을 수행합니다.

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>4단계: 앱 등록에 리디렉션 URI 추가

MSAL은 URL을 사용하여 브로커를 호출한 다음, 앱에 반환합니다. 이 왕복을 완료하려면 [Azure Portal](https://portal.azure.com)을 사용하여 앱에 대한 **리디렉션 URI** 를 등록합니다.

애플리케이션에 대한 리디렉션 URI의 형식은 APK 서명에 사용되는 인증서에 따라 다릅니다. 예:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI의 마지막 부분인 `hgbUYHVBYUTvuvT&Y6tr554365466=`는 APK 서명에 사용되는 Base64로 인코딩된 버전의 서명입니다. Visual Studio에서 앱을 개발하는 동안 특정 인증서로 APK에 서명하지 않고 코드를 디버깅하는 경우 Visual Studio는 디버깅을 위해 APK에 서명합니다. 이러한 방식으로 Visual Studio에서 APK에 서명하면 기반 컴퓨터에 대한 고유한 서명이 제공됩니다. 따라서 다른 컴퓨터에서 앱을 빌드할 때마다 MSAL로 인증하기 위해 애플리케이션 코드의 리디렉션 URI와 Azure Portal의 애플리케이션 등록을 업데이트해야 합니다.

디버깅하는 동안 제공된 리디렉션 URI가 잘못되었음을 나타내는 MSAL 예외(또는 로그 메시지)가 발생할 수 있습니다. **예외 또는 로그 메시지** 는 디버깅 중인 현재 컴퓨터에서 사용해야 하는 리디렉션 URI도 나타냅니다. 코드에서 리디렉션 URI를 업데이트하고 제공된 리디렉션 URI를 Azure Portal의 앱 등록에 추가하기만 하면 제공된 리디렉션 URI를 사용하여 앱을 계속 개발할 수 있습니다.

코드를 완료할 준비가 되면 코드에서 리디렉션 URI를 업데이트하고 Azure Portal에서 애플리케이션 등록을 업데이트하여 APK 서명에 사용하는 인증서의 서명을 사용합니다.

실제로 이는 개발 팀의 각 구성원에 대한 리디렉션 URI *와* 프로덕션 서명된 버전의 APK에 대한 리디렉션 URI를 추가하는 것을 고려해야 함을 의미합니다.

MSAL에서 이를 수행하는 방식과 비슷하게 서명을 직접 계산할 수 있습니다.

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

다음 명령으로 **keytool** 을 사용하여 패키지에 대한 서명을 가져오는 옵션도 있습니다.

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>5단계(선택 사항): 시스템 브라우저로 변경

MSAL이 브로커를 사용하도록 구성되었지만 브로커가 설치되어 있지 않은 경우 MSAL은 대신 웹 보기(브라우저)를 사용합니다. MSAL은 디바이스에서 기본 시스템 브라우저를 사용하여 인증을 시도하지만 리디렉션 URI가 브로커에 대해 구성되어 있고 시스템 브라우저는 이를 사용하여 MSAL로 다시 이동하는 방법을 모르기 때문에 실패합니다. 실패를 방지하려면 4단계에서 사용한 브로커 리디렉션 URI로 *의도 필터* 를 구성하면 됩니다.

애플리케이션의 매니페스트를 수정하여 의도 필터를 추가합니다.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

예를 들어 `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=`의 리디렉션 URI가 있는 경우 매니페스트는 다음 XML 조각과 유사해야 합니다.

`android:path` 값의 서명 앞에 슬래시(`/`)가 **필요** 합니다.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

시스템 브라우저 및 Android 11 지원을 위한 애플리케이션 구성에 대한 자세한 내용은 [시스템 브라우저 지원을 위한 Android 매니페스트 업데이트](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)를 참조하세요.

또는 리디렉션 URI를 사용하지 않는 포함된 브라우저로 변경되도록 MSAL을 구성할 수 있습니다.

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android 조정된 인증에 대한 문제 해결 팁

다음은 Android에서 조정된 인증을 구현할 때 발생하는 문제를 방지하기 위한 몇 가지 팁입니다.

- **리디렉션 URI** - [Azure Portal](https://portal.azure.com/)의 애플리케이션 등록에 리디렉션 URI를 추가합니다. 누락되거나 잘못된 리디렉션 URI는 개발자에게 발생하는 일반적인 문제입니다.
- **브로커 버전** - 필요한 최소 버전의 브로커 앱을 설치합니다. Android에서 조정된 인증에 이러한 두 앱 중 하나를 사용할 수 있습니다.
  - [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)(버전 5.0.4689.0 이상)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator)(버전 6.2001.0140 이상).
- **브로커 우선 순위** - MSAL은 여러 브로커가 설치된 경우 디바이스에 *설치된 첫 번째 브로커* 와 통신합니다.

    예: 먼저 Microsoft Authenticator를 설치하고 Intune 회사 포털을 설치하는 경우 조정된 인증은 Microsoft Authenticator에서 *만* 수행됩니다.
- **로그** -조정된 인증에 문제가 발생하는 경우 브로커의 로그를 보면 원인을 진단하는 데 도움이 될 수 있습니다.
  - Microsoft Authenticator 로그 가져오기:

    1. 앱의 오른쪽 위 모퉁이에 있는 메뉴 단추를 선택합니다.
    1. **사용자 의견 보내기** > **문제가 있나요?** 를 선택합니다.
    1. **수행하려는 작업이 무엇인가요?** 아래에서 옵션을 선택하고 설명을 추가합니다.
    1. 로그를 보내려면 앱의 오른쪽 위 모퉁이에 있는 화살표를 선택합니다.

    로그를 보낸 후 대화 상자에 인시던트 ID가 표시됩니다. 인시던트 ID를 기록하고 지원을 요청할 때 포함합니다.

  - Intune 회사 포털 로그 가져오기:

    1. 앱의 왼쪽 위 모퉁이에 있는 메뉴 단추를 선택합니다.
    1. **도움말** > **이메일 지원** 을 선택합니다.
    1. 로그를 보내려면 **로그만 업로드** 를 선택합니다.

    로그를 보낸 후 대화 상자에 인시던트 ID가 표시됩니다. 인시던트 ID를 기록하고 지원을 요청할 때 포함합니다.

## <a name="next-steps"></a>다음 단계

[MSAL.NET과 함께 유니버설 Windows 플랫폼을 사용하기 위한 고려 사항](msal-net-uwp-considerations.md)에 대해 알아봅니다.
