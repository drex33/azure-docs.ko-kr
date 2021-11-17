---
title: Xamarin iOS 고려 사항(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)에서 Xamarin iOS를 사용하는 경우 고려 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 2698d958746aaea86a7dbb290468ffaf1f8c2eec
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993725"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET에서 Xamarin iOS를 사용하기 위한 고려 사항

Xamarin iOS에서 Microsoft Authentication Library for .NET(MSAL.NET)을 사용하는 경우 다음을 수행해야 합니다.

- `AppDelegate`에서 `OpenUrl`함수를 재정의하고 구현합니다.
- 키 집합 그룹을 사용하도록 설정합니다.
- 토큰 캐시 공유를 사용하도록 설정합니다.
- 키 집합 액세스를 사용하도록 설정합니다.
- iOS 12 및 iOS 13 및 인증의 알려진 문제를 이해합니다.

## <a name="implement-openurl"></a>OpenUrl 구현

`FormsApplicationDelegate` 파생 클래스의 `OpenUrl` 메서드를 재정의하고 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`를 호출합니다. 예를 들면 다음과 같습니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

또한 다음 작업을 수행합니다.

* 리디렉션 URI 체계를 정의합니다.
* 앱에서 다른 앱을 호출하기 위해 권한이 필요합니다.
* 리디렉션 URI에 대한 특정 형식이 있어야 합니다.
* Azure Portal에서 [리디렉션 URI를 등록](quickstart-register-app.md#add-a-redirect-uri)합니다.

### <a name="enable-keychain-access"></a>키 집합 액세스를 사용하도록 설정

키 집합 액세스를 사용하도록 설정하려면 애플리케이션에 키 집합 액세스 그룹이 있는지 확인합니다. `WithIosKeychainSecurityGroup()` API를 사용하여 애플리케이션을 만들 때 키 집합 액세스 그룹을 설정할 수 있습니다.

캐시 및 SSO(Single Sign-On)를 활용하려면 모든 애플리케이션에서 키 집합 액세스 그룹을 동일한 값으로 설정합니다.

이 설치 예제에서는 MSAL 4.x를 사용합니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

또한 `Entitlements.plist` 파일에서 키 집합 액세스를 사용하도록 설정합니다. 다음 액세스 그룹 또는 사용자의 액세스 그룹을 사용합니다.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

`WithIosKeychainSecurityGroup()` API를 사용하는 경우 MSAL은 애플리케이션의 *팀 ID*(`AppIdentifierPrefix`) 끝에 보안 그룹을 자동으로 추가합니다. Xcode에서 애플리케이션을 빌드할 때 동일한 작업을 수행하기 때문에 MSAL은 보안 그룹을 추가합니다. 이러한 이유로 `Entitlements.plist` 파일의 자격에서 키 집합 액세스 그룹 앞에 `$(AppIdentifierPrefix)`를 포함해야 합니다.

자세한 내용은 [iOS 자격 설명서](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)를 참조하세요.

#### <a name="troubleshooting-keychain-access"></a>KeyChain 액세스 문제 해결

"애플리케이션이 애플리케이션 게시자의 iOS 키체인에 액세스할 수 없습니다(TeamId가 null임)"와 유사한 오류 메시지가 표시되면 MSAL이 KeyChain에 액세스할 수 없음을 의미합니다. 이는 구성 문제입니다. 문제를 해결하려면 다음과 같이 직접 KeyChain에 액세스해 보세요. 

```csharp
var queryRecord = new SecRecord(SecKind.GenericPassword)
{
    Service = "",
    Account = "SomeTeamId",
    Accessible = SecAccessible.Always
};

SecRecord match = SecKeyChain.QueryAsRecord(queryRecord, out SecStatusCode resultCode);

if (resultCode == SecStatusCode.ItemNotFound)
{
    SecKeyChain.Add(queryRecord);
    match = SecKeyChain.QueryAsRecord(queryRecord, out resultCode);
}

// Make sure that  resultCode == SecStatusCode.Success
```

### <a name="enable-token-cache-sharing-across-ios-applications"></a>iOS 애플리케이션에서 토큰 캐시 공유를 사용하도록 설정

MSAL 2.x부터 키 집합 액세스 그룹을 지정하여 여러 애플리케이션에 걸쳐 토큰 캐시를 유지할 수 있습니다. 이 설정을 사용하면 동일한 키 집합 액세스 그룹을 포함하는 여러 애플리케이션에서 토큰 캐시를 공유할 수 있습니다. [ADAL.NET](https://aka.ms/adal-net) 애플리케이션, MSAL.NET 애플리케이션 및 [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 또는 [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)에서 개발한 네이티브 iOS 애플리케이션 간에 토큰 캐시를 공유할 수 있습니다.

토큰 캐시를 공유하면 동일한 키 집합 액세스 그룹을 사용하는 모든 애플리케이션에서 SSO(Single Sign-On)를 허용할 수 있습니다.

이 캐시 공유를 사용하도록 설정하려면 `WithIosKeychainSecurityGroup()` 메서드를 사용하여 동일한 캐시를 공유하는 모든 애플리케이션에서 키 집합 액세스 그룹을 동일한 값으로 설정합니다. 이 문서의 첫 번째 코드 예제에서는 이 메서드를 사용하는 방법을 보여 줍니다.

이 문서의 앞부분에서 MSAL이 `WithIosKeychainSecurityGroup()` API를 사용할 때마다 `$(AppIdentifierPrefix)`를 추가한다는 사실을 확인했습니다. 팀 ID `AppIdentifierPrefix`는 동일한 게시자가 만든 애플리케이션만 키 집합 액세스를 공유할 수 있도록 하기 때문에 MSAL은 이 요소를 추가합니다.

> [!NOTE]
> `KeychainSecurityGroup` 속성은 더 이상 사용되지 않습니다. 대신 `iOSKeychainSecurityGroup` 속성을 사용하세요. `iOSKeychainSecurityGroup`을 사용할 때는 `TeamId` 접두사가 필요하지 않습니다.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator 사용

애플리케이션에서 Microsoft Authenticator를 broker로 사용하여 다음을 지원할 수 있습니다.

- **SSO**: SSO를 사용하도록 설정하면 사용자가 각 애플리케이션에 로그인할 필요가 없습니다.
- **디바이스 식별**: 디바이스 ID를 사용하여 디바이스 인증서에 액세스하여 인증합니다. 이 인증서는 작업 공간에 조인될 때 디바이스에 생성됩니다. 테넌트 관리자가 디바이스와 관련된 조건부 액세스를 사용하도록 설정하면 애플리케이션을 사용할 준비가 됩니다.
- **애플리케이션 ID 확인**: 애플리케이션에서 broker를 호출할 때 해당 리디렉션 URL을 전달합니다. broker가 리디렉션 URL을 확인합니다.

broker를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Xamarin iOS 및 Android 애플리케이션에서 Microsoft Authenticator 또는 Microsoft Intune 회사 포털 사용](msal-net-use-brokers-with-xamarin-apps.md)을 참조하세요.

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 및 인증의 알려진 문제

Microsoft는 iOS 12와 일부 인증 유형 간의 비호환성에 대해 [보안 권고](https://github.com/aspnet/AspNetCore/issues/4647)를 발표했습니다. 호환되지 않을 경우 소셜, WSFed 및 OIDC 로그인이 중단됩니다. 보안 권고를 통해 애플리케이션에서 ASP.NET 보안 제한을 제거하여 iOS 12와 호환되도록 하는 방법을 이해할 수 있습니다.

Xamarin iOS에서 MSAL.NET 애플리케이션을 개발할 때 iOS 12에서 웹 사이트에 로그인하려고 하면 무한 루프가 표시될 수 있습니다. 이러한 동작은 GitHub: [iOS 12 #1329에서 웹 사이트에 로그인하려고 할 때 무한 루프 발생(Infinite loop when trying to login to website from iOS 12 #1329)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)에 나오는 이 ADAL 문제와 비슷합니다.

iOS 12 Safari를 사용하는 ASP.NET Core OIDC 인증에 문제가 있는 것을 볼 수도 있습니다. 자세한 내용은 이 [WebKit 이슈](https://bugs.webkit.org/show_bug.cgi?id=188165)를 참조하세요.

## <a name="known-issues-with-ios-13-and-authentication"></a>iOS 13 및 인증의 알려진 문제

앱에 조건부 액세스 또는 인증서 인증 지원이 필요한 경우 앱이 Microsoft Authenticator broker 앱과 통신할 수 있도록 설정합니다. 그러면 MSAL이 애플리케이션과 Microsoft Authenticator 간의 요청 및 응답을 처리하는 일을 담당합니다.

iOS 13에서 Apple은 사용자 지정 URL 체계를 통해 외부 애플리케이션에서 응답을 받을 때 원본 애플리케이션을 읽는 애플리케이션의 기능을 제거하여 API를 획기적으로 변경했습니다.

[UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) 상태에 대한 Apple 설명서:

> 요청이 팀에 속한 다른 앱에서 시작된 경우 UIKit는 이 키의 값을 해당 앱의 ID로 설정합니다. 원래 앱의 팀 식별자가 현재 앱의 팀 식별자와 다른 경우 키의 값은 nil입니다.

MSAL은 MSAL과 Microsoft Authenticator 앱 간의 통신을 확인하기 위해 `UIApplication.SharedApplication.OpenUrl`에 의존하기 때문에 이러한 변경은 매우 획기적인 것입니다.

또한 iOS 13에서 개발자는 `ASWebAuthenticationSession`을 사용할 때 프레젠테이션 컨트롤러를 제공해야 합니다.

Xcode 11을 사용하여 빌드하는 중이며 iOS broker 또는 `ASWebAuthenticationSession`을 사용하는 경우 앱에 영향을 미칩니다.

이러한 경우에는 [MSAL.NET 4.4.0+](https://www.nuget.org/packages/Microsoft.Identity.Client/)를 사용하여 성공적인 인증을 사용하도록 설정합니다.

### <a name="additional-requirements"></a>추가 요구 사항

- 최신 MSAL 라이브러리를 사용하는 경우 **Microsoft Authenticator 버전 6.3.19+** 가 디바이스에 설치되어 있는지 확인합니다.
- MSAL.NET 4.4.0+로 업데이트하는 경우 *info.plist* 파일에서 `LSApplicationQueriesSchemes`를 업데이트하고 `msauthv3`를 추가합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    iOS 13을 지원하는 디바이스에서 최신 Microsoft Authenticator 앱이 있는지 검색하려면 *Info.plist* 에 `msauthv3`를 추가해야 합니다.

## <a name="report-an-issue"></a>문제 보고

질문이 있거나 MSAL.NET에서 확인된 이슈를 보고하려는 경우, GitHub의 [AzureAD/microsoft-authentication-library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) 리포지토리에서 이슈를 여세요.

## <a name="next-steps"></a>다음 단계

Xamarin iOS의 속성에 대한 내용은 다음 샘플 README.md 파일에서 [iOS 관련 고려 사항](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) 단락을 참조하세요.

샘플 | 플랫폼 | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP(유니버설 Windows 플랫폼) | MSAL을 사용하여 Azure AD 2.0 엔드포인트를 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 앱은 결과 토큰을 사용하여 Microsoft Graph에 액세스하는 방법을 보여 줍니다.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
