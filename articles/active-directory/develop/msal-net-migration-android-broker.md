---
title: 브로커를 사용하여 MSAL.NET으로 Xamarin Android 앱 마이그레이션
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator 또는 Intune 회사 포털을 사용하는 Xamarin Android 앱을 ADAL.NET에서 MSAL.NET으로 마이그레이션하는 방법에 대해 알아봅니다.
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 8573666d5b3f590ca17779bfe03a035968c801ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615038"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>ADAL.NET에서 MSAL.NET로 브로커를 사용하는 Android 애플리케이션 마이그레이션

현재 .NET용 Azure Active Directory 인증 라이브러리(ADAL.NET) 및 [인증 브로커](msal-android-single-sign-on.md)를 사용하는 Xamarin Android 앱이 있는 경우 [.NET용 Microsoft 인증 라이브러리](msal-overview.md)(MSAL.NET)로 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Xamarin Android 앱은 이미 브로커([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) 및 MSAL.NET으로 마이그레이션해야 하는 ADAL.NET과 통합되어 있습니다.

## <a name="step-1-enable-the-broker"></a>1단계: 브로커를 사용하도록 설정

<table>
<tr><td>현재 ADAL 코드:</td><td>대응하는 MSAL:</td></tr>
<tr><td>
ADAL.NET에서 브로커 지원은 인증별 컨텍스트를 기준으로 사용하도록 설정되었습니다.

브로커를 호출하려면 `PlatformParameters` 생성자에서 `useBroker`을(를) *true* 로 설정해야 했습니다.

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

Android용 플랫폼 관련 페이지 렌더러 코드에서 `useBroker` 플래그를 true로 설정합니다.

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

그런 다음 토큰 가져오기 호출에 매개 변수를 포함합니다.

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
MSAL.NET에서 브로커 지원은 PublicClientApplication 기준으로 사용하도록 설정됩니다.

`WithBroker()` 매개 변수(true로 설정됨)를 사용하여 브로커를 호출합니다.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

그런 다음 AcquireToken 호출에서 다음을 수행합니다.

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>2단계: 활동 설정

ADAL.NET에서 [1단계: 브로커를 사용하도록 설정](#step-1-enable-the-broker)에 표시된 대로 활동(일반적으로 MainActivity)을 PlatformParameters의 일부로 전달했습니다.

또한 MSAL.NET은 활동을 사용하지만 브로커 없이 일반 Android를 사용하는 경우에는 필요하지 않습니다. 브로커를 사용하려면 브로커에서 응답을 보내고 받도록 활동을 설정합니다.

<table>
<tr><td>현재 ADAL 코드:</td><td>대응하는 MSAL:</td></tr>
<tr><td>
활동은 Android 관련 플랫폼의 PlatformParameters로 전달됩니다.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

MSAL.NET에서 다음 두 가지 작업을 수행하여 Android에 대한 활동을 설정합니다.

1. `MainActivity.cs`에서 `App.RootViewController`을(를) `MainActivity`(으)로 설정하여 브로커에 대한 호출을 포함하는 활동이 있는지 확인합니다.

    올바르게 설정되지 않으면 다음 오류가 발생할 수 있습니다. `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. AcquireTokenInteractive 호출에서 `.WithParentActivityOrWindow(App.RootViewController)`을(를) 사용하고 사용할 활동에 대한 참조를 전달합니다. 이 예에서는 MainActivity를 사용합니다.

**예를 들면 다음과 같습니다.**

*App.cs* 에서:

```CSharp
   public static object RootViewController { get; set; }
```

*MainActivity.cs* 에서:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

AcquireToken 호출에서 다음을 수행 합니다.

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>다음 단계

Xamarin과 함께 MSAL.NET을 사용할 때의 Android 관련 고려 사항에 대한 자세한 내용은 [Xamarin Android with MSAL.NET에 대한 구성 요구 사항 및 문제 해결 팁](msal-net-xamarin-android-considerations.md)을 참조하세요.