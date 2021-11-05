---
title: Xamarin Android 코드 구성 및 문제 해결(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)에서 Xamarin Android를 사용하는 경우 고려 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 04194dc73b7c91bd1e59140a032232246b72420e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052711"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>MSAL.NET를 사용하는 Xamarin Android의 구성 요구 사항 및 문제 해결 팁

MSAL.NET(Microsoft Authentication Library for .NET)에서 Xamarin Android를 사용하는 경우 코드에 몇 가지 구성 변경이 필요합니다. 다음 섹션에서는 가장 일반적인 몇 가지 문제를 방지하기 위해 필요한 수정 사항에 대해 설명하고 [문제 해결](#troubleshooting) 섹션을 설명합니다.

## <a name="set-the-parent-activity"></a>부모 작업 설정

Xamarin Android에서 상호 작용 후 토큰이 반환되도록 부모 작업을 설정합니다.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

MSAL.NET 4.2 이상에서는 [Publicclientapplication][PublicClientApplication] 수준에서 이 기능을 설정할 수도 있습니다. 이렇게 하려면 콜백을 사용합니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

[Currentactivityplugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)를 사용하는 경우 [`PublicClientApplication`][PublicClientApplication] 작성기 코드는 다음 코드 조각과 유사하게 표시됩니다.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>컨트롤이 MSAL로 반환되는지 확인

인증 흐름의 대화형 부분이 종료되면 [`Activity`][Activity]를 재정의하여 MSAL로 제어를 반환합니다.[`OnActivityResult()`][OnActivityResult] 메서드를 재정의합니다.

재정의에서 MSAL.NET의 `AuthenticationContinuationHelper`를 호출합니다.`SetAuthenticationContinuationEventArgs()` 인증 흐름의 대화형 부분 끝에서 MSAL에 대한 제어를 반환하는 메서드입니다.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>System WebView 지원에 대한 Android 매니페스트 업데이트 

System WebView를 지원하려면 *AndroidManifest.xml* 파일에 다음 값을 포함해야 합니다.

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

`android:scheme` 값은 애플리케이션 포털에서 구성된 리디렉션 URI에서 생성됩니다. 예를 들어 리디렉션 URI가 `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth`인 경우 매니페스트의 `android:scheme` 항목은 다음 예제와 같이 표시됩니다.

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

또는 *AndroidManifest.xml* 을 수동으로 편집하는 대신 [코드에서 작업을 생성](/xamarin/android/platform/android-manifest#the-basics)합니다. 코드에서 작업을 만들려면 먼저 `Activity` 특성 및 `IntentFilter` 특성을 포함하는 클래스를 만듭니다.

XML 파일의 값을 나타내는 클래스의 예제는 다음과 같습니다.

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>조정된 인증에서 System WebView 사용

조정된 인증을 사용하도록 애플리케이션을 구성하고 디바이스에 Broker가 설치되어 있지 않은 경우, 대화형 인증을 위한 대체 요소로 System WebView를 사용하려면 MSAL이 브로커의 리디렉션 URI를 사용하여 인증 응답을 캡처하도록 설정합니다. MSAL은 Broker를 사용할 수 없음을 감지하면 디바이스에서 기본 System WebView를 사용하여 인증을 시도합니다. 이 기본값을 사용하면 리디렉션 URI가 Broker를 사용하도록 구성되어 있고 System WebView가 이를 사용하여 MSAL로 돌아가는 방법을 알 수 없기 때문에 실패합니다. 이 문제를 해결하려면 이전에 구성한 Broker 리디렉션 URI를 사용하여 _의도 필터_ 를 만듭니다. 다음 예제와 같이 애플리케이션의 매니페스트를 수정하여 의도 필터를 추가합니다.

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

`android:host=` 값을 Azure Portal에서 등록한 패키지 이름으로 바꿉니다. `android:path=` 값을 Azure Portal에서 등록한 키 해시로 바꿉니다. 서명 해시는 URL로 인코딩되면 **안 됩니다**. 선행 슬래시(`/`)가 서명 해시의 시작 부분에 표시되는지 확인합니다.

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.x 매니페스트

Xamarin.Forms 4.3.x는 *AndroidManifest.xml* 에서 `package` 특성을 `com.companyname.{appName}`로 설정하는 코드를 생성합니다. `DataScheme`를 `msal{client_id}`로 사용하는 경우 `MainActivity.cs` 네임스페이스의 값과 일치하도록 값을 변경할 수 있습니다.

## <a name="android-11-support"></a>Android 11 지원

Android 11에서 시스템 브라우저 및 조정된 인증을 사용하려면 먼저 이러한 패키지를 선언해야 앱에 표시됩니다. Android 10(API 29) 및 이전 버전을 대상으로 하는 앱은 지정된 시간에 디바이스에서 사용 가능한 패키지 목록을 OS에 쿼리할 수 있습니다. 개인 정보 및 보안을 지원하기 위해 Android 11은 패키지 표시 유형을 앱의 *AndroidManifest.xml* 파일에 지정된 패키지 및 OS 패키지의 기본 목록으로 줄입니다. 

시스템 브라우저와 Broker를 모두 사용하여 애플리케이션을 인증할 수 있도록 하려면 *AndroidManifest.xml* 에 다음 섹션을 추가합니다.

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

`{Package Name}`을 애플리케이션 패키지 이름으로 대체합니다. 

이제 시스템 브라우저 및 조정된 인증에 대한 지원이 포함된 업데이트된 매니페스트는 다음 예제와 비슷해야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>포함된 웹 보기 사용(선택 사항)

기본적으로 MSAL.NET은 시스템 웹 브라우저를 사용합니다. 이 브라우저를 사용하면 웹 애플리케이션 및 다른 앱을 사용하여 SSO(Single Sign-On)를 가져올 수 있습니다. 드문 경우지만 시스템에서 포함된 웹 보기를 사용하도록 할 수 있습니다.

이 코드 예제에서는 포함된 웹 보기를 설정하는 방법을 보여줍니다.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

자세한 내용은 [MSAL.NET 용 웹 브라우저 사용](msal-net-web-browsers.md) 및 [Xamarin Android 시스템 브라우저 고려 사항](msal-net-system-browser-android-considerations.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="general-tips"></a>일반 팁

- 기존 MSAL.NET NuGet 패키지를 [최신 버전의 MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)으로 업데이트합니다.
- Xamarin.Forms가 최신 버전인지 확인합니다.
- Xamarin.Android.Support.v4가 최신 버전인지 확인합니다.
- 모든 Xamarin.Android.Support 패키지가 최신 버전을 대상으로 하는지 확인합니다.
- 애플리케이션을 정리하거나 다시 빌드합니다.
- Visual Studio에서 최대 병렬 프로젝트 빌드 수를 **1** 로 설정해보세요. 이렇게 하려면 **옵션** > **프로젝트 및 솔루션** > **빌드 및 실행**  > **최대 병렬 프로젝트 빌드 수** 를 선택합니다.
- 명령 줄에서 빌드 중이고 명령에서 `/m`을 사용하는 경우 명령에서 이 요소를 제거해보세요.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>오류: 현재 컨텍스트에 AuthenticationContinuationHelper가 없습니다.

`AuthenticationContinuationHelper`가 현재 컨텍스트에 존재하지 않는다는 오류가 표시되면 Visual Studio가 *Android.csproj\** 파일을 잘못 업데이트했을 수 있습니다. 경우에 따라 `<HintPath>` 요소의 파일 경로에 `monoandroid90` 대신 `netstandard13`이 잘못 포함되어 있습니다.

이 예제에는 올바른 파일 경로가 포함되어 있습니다.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Microsoft ID 플랫폼을 사용하는 Xamarin 모바일 애플리케이션](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) 샘플을 참조하세요. 다음 표에서는 추가 정보 파일의 관련 정보를 요약하여 보여줍니다.

| 샘플 | 플랫폼 | 설명 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | MSAL을 사용하여 Azure AD 2.0 엔드포인트를 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 이 앱은 Microsoft Graph에 액세스하는 방법과 결과 토큰을 보여줍니다. <br>![인증 흐름 다이어그램](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
