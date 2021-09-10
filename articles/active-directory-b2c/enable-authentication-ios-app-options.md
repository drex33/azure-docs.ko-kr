---
title: Azure Active Directory B2C를 사용하여 iOS Swift 모바일 애플리케이션 옵션을 사용하도록 설정
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 iOS Swift 모바일 애플리케이션 옵션을 사용하도록 설정하는 여러 가지 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 128c5000e6614e769100a92303007317eadaed96
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220697"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 iOS Swift 앱에서 인증 옵션을 사용하도록 설정 

이 문서에서는 iOS Swift 애플리케이션에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용, 사용자 지정 및 개선할 수 있는 방법을 설명합니다. 

시작하기 전에 다음 문서를 숙지합니다. 
* [Azure AD B2C를 사용하여 샘플 iOS Swift 앱에서 인증 구성](configure-authentication-sample-ios-app.md)
* [Azure AD B2C를 사용하여 자체 iOS Swift 앱에서 인증을 사용하도록 설정](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 사용자 지정 도메인 및 테넌트 ID를 사용하려면 다음을 수행합니다. 

1. [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따릅니다.
1. 사용자 지정 도메인을 사용하여 `kAuthorityHostName` 클래스 멤버를 업데이트합니다.
1. [테넌트 ID](tenant-management.md#get-your-tenant-id)를 사용하여 `kTenantName` 클래스 멤버를 업데이트합니다.

다음 Swift 코드는 변경 전의 앱 설정을 보여 줍니다.

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

다음 Swift 코드는 변경 후의 앱 설정을 보여 줍니다.

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우 [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. MSAL(Microsoft 인증 라이브러리) 구성 개체를 찾은 다음, 로그인 힌트와 함께 `withLoginHint()` 메서드를 추가합니다.

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 도메인 이름이 포함된 `domain_hint` 매개 변수를 목록에 추가합니다(예: `facebook.com`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `extraQueryParameters` 특성에 전달합니다.

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성합니다](language-customization.md).
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. 해당 언어 코드가 포함된 `ui_locales` 매개 변수를 목록에 추가합니다(예: `en-us`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `extraQueryParameters` 특성에 전달합니다.

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. `campaignId`와 같은 사용자 지정 쿼리 문자열 매개 변수를 추가합니다. 매개 변수 값을 설정합니다(예: `germany-promotion`).
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `extraQueryParameters` 특성에 전달합니다.

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. 코드에서 ID 토큰을 생성하거나 획득한 다음 토큰을 변수로 설정합니다(예: `idToken`). 
1. 기존 목록 개체를 만들거나 사용하여 추가 쿼리 매개 변수를 저장합니다.
1. ID 토큰을 저장하는 해당 변수를 사용하여 `id_token_hint` 매개 변수를 추가합니다.
1. 추가 쿼리 매개 변수 목록을 MSAL 구성 개체의 `extraQueryParameters` 특성에 전달합니다.

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


MSAL 로거는 MSAL 요청이 이루어지기 전에 앱 시작 시퀀스에서 최대한 빨리 설정해야 합니다. *AppDelegate.swift* `application` 메서드에서 MSAL [로깅](../active-directory/develop/msal-logging-ios.md)을 구성합니다.

다음 코드 조각은 MSAL 로깅을 구성하는 방법을 보여 줍니다.

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>포함된 웹 보기 환경

대화형 인증에는 웹 브라우저가 필요합니다. 기본적으로 MSAL 라이브러리는 시스템 웹 보기를 사용합니다. 로그인하는 동안 MSAL 라이브러리는 Azure AD B2C 사용자 인터페이스를 사용하여 iOS 시스템 웹 보기를 표시합니다.  

자세한 내용은 [iOS/macOS용 브라우저 및 웹 보기 사용자 지정](../active-directory/develop/customize-webviews.md) 문서를 참조하세요.

요구 사항에 따라 포함된 웹 보기를 사용할 수 있습니다. 포함된 웹 보기와 MSAL의 시스템 웹 보기 사이에는 시각적 개체 및 Single Sign-On 동작 차이가 있습니다.

![시스템 웹 보기 환경과 포함된 웹 보기 환경의 차이점을 보여 주는 스크린샷](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> 플랫폼 기본값을 사용하는 것이 좋으며, 보통은 시스템 브라우저가 기본값입니다. 시스템 브라우저는 이전에 로그인한 사용자를 기억하는 데 더 뛰어납니다. Google과 같은 일부 ID 공급자는 포함된 보기 환경을 지원하지 않습니다.

이 동작을 변경하려면 `MSALWebviewParameters`의 `webviewType` 특성을 `wkWebView`로 변경합니다. 다음 예제는 웹 보기 유형을 포함된 보기로 변경하는 방법을 보여 줍니다. 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [iOS Swift용 MSAL 구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)을 참조하세요.
