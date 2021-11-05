---
title: macOS 및 iOS에서 SSO 구성
titleSuffix: Microsoft identity platform
description: macOS 및 iOS에서 SSO(Single Sign-On)를 구성하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 02140728536e458c85e8c63a0fa1ca3c9663506b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050509"
---
# <a name="configure-sso-on-macos-and-ios"></a>macOS 및 iOS에서 SSO 구성

macOS 및 iOS용 MSAL(Microsoft 인증 라이브러리)은 macOS/iOS 앱과 브라우저 간 SSO(Single Sign-On)를 지원합니다. 이 문서에는 다음 SSO 시나리오를 다룹니다.

- [여러 앱 간의 자동 SSO](#silent-sso-between-apps)

해당 유형의 SSO는 동일한 Apple 개발자가 배포하는 여러 앱 간에 작동합니다. 키 집합에서 다른 앱에서 작성한 새로 고침 토큰을 읽고 이를 액세스 토큰에 대해 자동으로 교환하여 자동 SSO(즉, 사용자에게 자격 증명을 입력하라는 메시지가 표시되지 않음)를 제공합니다.  

- [인증 브로커를 통한 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 이러한 흐름은 macOS에서 사용할 수 없습니다.

Microsoft는 모바일 디바이스가 AAD(Azure Active Directory)에 등록되어 있는 한 다른 공급 기업의 애플리케이션 간에 SSO를 사용하도록 설정하는 브로커라는 앱을 제공합니다. 이러한 유형의 SSO를 사용하려면 사용자의 디바이스에 브로커 애플리케이션을 설치해야 합니다.

- **MSAL과 Safari 간 SSO**

SSO는 [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) 클래스를 통해 구현됩니다. 다른 앱 및 Safari 브라우저에서 기존 로그인 상태를 사용합니다. 동일한 Apple 개발자가 배포한 앱에 국한되지는 않지만 몇몇 사용자 상호 작용이 필요합니다.

앱의 기본 웹 보기를 사용하여 사용자를 로그인하게 하는 경우 MSAL 기반 애플리케이션과 Safari 간 자동 SSO를 받게 됩니다. MSAL에서 지원하는 웹 보기에 대한 자세한 내용은 [브라우저 및 웹 보기 사용자 지정](customize-webviews.md)을 참조하세요.

> [!IMPORTANT]
> 현재 해당 유형의 SSO는 macOS에서 사용할 수 없습니다. macOS의 MSAL은 Safari를 통해 SSO를 지원하지 않는 WKWebView만을 지원합니다. 

- **ADAL 및 MSAL macOS/iOS 앱 간 자동 SSO**

MSAL Objective-C는 ADAL Objective-C 기반 앱을 사용한 마이그레이션 및 SSO를 지원합니다. 동일한 Apple 개발자가 앱을 배포해야 합니다.

ADAL 및 MSAL 기반 앱 간의 앱 간 SSO에 대한 지침은 [macOS 및 iOS의 ADAL 및 MSAL 앱 간 SSO](sso-between-adal-msal-apps-macos-ios.md)를 참조하세요.

## <a name="silent-sso-between-apps"></a>앱 간 자동 SSO

MSAL은 iOS 키 집합 액세스 그룹을 통해 SSO 공유를 지원합니다.

애플리케이션에서 SSO를 사용하도록 설정하려면 아래에 자세히 설명되어 있는 다음 단계를 수행해야 합니다.

1. 모든 애플리케이션에서 동일한 클라이언트 ID 또는 애플리케이션 ID를 사용하고 있는지 확인합니다.
1. 모든 애플리케이션이 키 집합을 공유할 수 있도록 Apple에서 동일한 서명 인증서를 공유하는지 확인합니다.
1. 각 애플리케이션에 대한 동일한 키 집합 권한 부여를 요청합니다.
1. 기본 키 집합이 아닌 경우 사용하려는 공유 키 집합에 대한 MSAL SDK를 알려 주세요.

### <a name="use-the-same-client-id-and-application-id"></a>동일한 클라이언트 ID 및 애플리케이션 ID 사용

Microsoft ID 플랫폼에서 토큰을 공유할 수 있는 애플리케이션을 확인하려면 이러한 애플리케이션이 동일한 클라이언트 ID 또는 애플리케이션 ID를 공유해야 합니다. 포털에 첫 번째 애플리케이션을 등록했던 경우에 제공된 고유 식별자입니다.

Microsoft ID 플랫폼에서 동일한 애플리케이션 ID를 사용하는 앱을 구별하는 방법은 **리디렉션 URI** 를 사용하는 것입니다. 각 애플리케이션에는 등록 포털에 등록한 여러 개의 리디렉션 URI가 있을 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 예를 들면 다음과 같습니다.

App1 리디렉션 URI: `msauth.com.contoso.mytestapp1://auth`  
App2 리디렉션 URI: `msauth.com.contoso.mytestapp2://auth`  
App3 리디렉션 URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 리디렉션 URI의 형식은 MSAL이 지원하는 형식과 호환되어야 하며 이는 [MSAL 리디렉션 URI 형식 요구 사항](redirect-uris-ios.md#msal-redirect-uri-format-requirements)에 설명되어 있습니다.

### <a name="setup-keychain-sharing-between-applications"></a>애플리케이션 간 공유되는 키 집합 설정

키 집합 공유를 사용하도록 설정하려면 Apple의 [기능 추가](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) 문서를 참조하세요. 중요한 것은 키 집합을 호출하려는 항목을 결정하고 SSO와 관련된 모든 애플리케이션에 해당 기능을 추가하는 것입니다.

권한 부여를 올바르게 설정하면 프로젝트 디렉터리에 다음 예제와 같은 항목이 포함된 `entitlements.plist` 파일이 표시됩니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>새 키 집합 그룹 추가

프로젝트 **기능** 에 새 키 집합 그룹을 추가합니다. 키 집합 그룹은 다음과 같아야 합니다.
* iOS에서 `com.microsoft.adalcache` 
* macOS에서 `com.microsoft.identity.universalstorage`

![키 집합 예제](media/single-sign-on-macos-ios/keychain-example.png)

자세한 내용은 [키 집합 그룹](howto-v2-keychain-objc.md)을 참조하세요.

## <a name="configure-the-application-object"></a>애플리케이션 개체 구성

각 애플리케이션에서 키 집합 자격을 사용하도록 설정하고 SSO를 사용할 준비가 되면 다음 예제와 같이 키 집합 액세스 그룹을 사용하여 `MSALPublicClientApplication`을 구성합니다.

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> 애플리케이션에서 키 집합을 공유하는 경우 모든 애플리케이션은 사용자를 삭제하거나 애플리케이션에서 모든 토큰을 삭제할 수 있습니다.
> 이는 백그라운드 작업을 하기 위해 토큰을 사용하는 애플리케이션이 있는 경우에 특히 효과가 큽니다.
> 키 집합을 공유하면 앱이 Microsoft Identity SDK 제거 작업을 사용할 때 매우 주의해야 합니다.

정말 간단하죠. 이제 Microsoft ID SDK는 모든 애플리케이션에서 자격 증명을 공유합니다. 계정 목록도 애플리케이션 인스턴스 간에 공유됩니다.

## <a name="sso-through-authentication-broker-on-ios"></a>IOS에서 인증 브로커를 통한 SSO

MSAL은 Microsoft Authenticator를 사용하여 조정된 인증을 지원합니다. Microsoft Authenticator는 AAD 등록 디바이스에 대한 SSO를 제공하고 애플리케이션이 조건부 액세스 정책을 따르도록 도와줍니다.

다음 단계는 앱에 대한 인증 브로커를 사용하여 SSO를 사용하도록 설정하는 방법을 설명합니다.

1. 앱의 Info.plist에서 애플리케이션에 대한 브로커 호환 리디렉션 URI 형식을 등록합니다. 브로커 호환 리디렉션 URI 형식은 `msauth.<app.bundle.id>://auth`입니다. `<app.bundle.id>``를 애플리케이션의 번들 ID로 바꿉니다. 예를 들면 다음과 같습니다.

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. `LSApplicationQueriesSchemes`에서 앱의 Info.plist에 다음 체계를 추가합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. `AppDelegate.m` 파일에 다음을 추가하여 콜백을 처리합니다.

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Xcode 11을 사용하는 경우** `SceneDelegate` 파일에 MSAL 콜백을 대신 추가해야 합니다.
이전 iOS와의 호환성을 위해 UISceneDelegate 및 UIApplicationDelegate를 모두 지원하는 경우 MSAL 콜백을 두 파일에 배치해야 합니다.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
