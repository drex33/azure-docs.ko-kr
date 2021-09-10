---
title: Azure AD B2C를 사용하여 iOS Swift 앱에서 인증 사용
description: 이 문서에서는 Azure Active Directory B2C 구성 요소를 사용하여 iOS Swift 애플리케이션에서 인증을 사용하도록 설정하는 방법에 대해 설명합니다. Azure AD B2C를 사용하여 iOS Swift 애플리케이션에 사용자를 로그인하고 가입하는 방법을 알아봅니다.
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
ms.openlocfilehash: bb590ee57cc78c27c0e6cec7dc54cc15a9fd7648
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186104"
---
# <a name="enable-authentication-in-your-own-ios-swift-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 자체 iOS Swift 앱에서 인증 사용

이 문서에서는 iOS Swift 모바일 애플리케이션에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 보여줍니다. iOS Swift 애플리케이션을 [iOS용 MSAL(Microsoft Authentication Library)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)과 통합하는 방법을 알아봅니다. 

이 문서를 [샘플 iOS Swift 애플리케이션에서 인증 구성](./configure-authentication-sample-ios-app.md) 문서와 함께 사용하고, 샘플 iOS Swift 앱을 해당하는 iOS Swift 앱으로 바꿉니다. 이 문서의 지침을 완료하면 애플리케이션에서 Azure AD B2C를 통한 로그인을 수락합니다.

## <a name="prerequisites"></a>필수 조건

[Azure AD B2C를 사용하여 샘플 iOS Swift 앱에서 인증 구성](configure-authentication-sample-ios-app.md)에서 필수 조건 및 통합 지침을 검토합니다.

## <a name="create-an-ios-swift-app-project"></a>iOS Swift 앱 프로젝트 만들기

iOS Swift 애플리케이션이 아직 없는 경우 다음을 수행하여 새 프로젝트를 설정합니다.

1. [Xcode](https://developer.apple.com/xcode/)를 연 다음 **파일** > **새로 만들기** > **프로젝트** 를 선택합니다.
1. iOS 앱의 경우 **iOS** > **앱** 을 선택한 후 **다음** 을 선택합니다.
1. **새 프로젝트의 옵션 선택** 에서 다음 정보를 입력합니다.
    1. **제품 이름**(예: `MSALiOS`)
    1. **조직 식별자**(예: `contoso.com`)
    1. **인터페이스** 에서 **Storyboard** 를 선택합니다.
    1. **수명 주기** 에서 **UIKit 앱 대리자** 를 선택합니다.
    1. **언어** 에서 **Swift** 를 선택합니다. 
1. **다음** 을 선택합니다.
1. 앱을 만들 폴더를 선택한 다음 **만들기** 를 선택합니다.


## <a name="step-1-install-the-msal-library"></a>1단계: MSAL 라이브러리 설치

1. [CocoaPods](https://cocoapods.org/)를 사용하여 MSAL 라이브러리를 설치합니다.   프로젝트의 *.xcodeproj* 파일과 동일한 폴더에 *podfile* 파일이 없으면 *podfile* 이라는 빈 파일을 만듭니다. 다음 코드를 *podfile* 파일에 추가합니다.

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. `<your-target-here>`을 프로젝트 이름으로 바꿉니다(예: `MSALiOS`). 자세한 내용은 [Podfile 구문 참조](https://guides.cocoapods.org/syntax/podfile.html#podfile)를 확인하세요.
1. 터미널 창에서 *podfile* 파일이 포함된 폴더로 이동한 다음 *pod install* 을 실행하여 MSAL 라이브러리를 설치합니다.
1. `pod install` 명령을 실행하면 *\<your project name>.xcworkspace* 파일이 만들어집니다. Xcode에서 프로젝트를 다시 로드하려면 Xcode를 닫은 다음 *\<your project name>.xcworkspace* 파일을 엽니다.

## <a name="step-2-set-the-app-url-scheme"></a>2단계: 앱 URL 구성표 설정

사용자가 권한을 부여하면 Azure AD B2C는 Azure AD B2C 애플리케이션 등록에 구성된 리디렉션 URI를 사용하여 앱에 권한 부여 코드를 보냅니다. 

MSAL 기본 리디렉션 URI 형식은 `msauth.[Your_Bundle_Id]://auth`입니다. `msauth.com.microsoft.identitysample.MSALiOS://auth`는 `msauth.com.microsoft.identitysample.MSALiOS`가 URL 구성표인 예입니다.

이 단계에서는 `CFBundleURLSchemes` 배열을 사용하여 URL 체계를 등록합니다. 애플리케이션은 URL 체계에서 Azure AD B2C의 콜백을 수신 대기합니다. 

Xcode에서 [*Info.plist* 파일](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html)을 소스 코드 파일로 엽니다. `<dict>` 섹션에서 다음 XML 코드 조각을 추가합니다. 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="step-3-add-the-authentication-code"></a>3단계: 인증 코드 추가

이 [샘플 코드](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample)는 `UIViewController` 클래스로 구성됩니다. 이 클래스는 다음을 수행합니다.

- 사용자 인터페이스의 구조를 정의합니다.
- Azure AD B2C ID 공급자에 대한 정보를 포함합니다. 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정합니다. 
- 사용자를 인증하고 토큰을 획득하고 유효성을 검사하기 위한 인증 코드가 포함되어 있습니다.

사용자가 인증할 `UIViewController`를 선택합니다.  `UIViewController`에서 코드를 [GitHub에서 제공되는 코드](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift)와 병합합니다. 

## <a name="step-4-configure-your-ios-swift-app"></a>4단계: iOS Swift 앱 구성

[인증 코드를 추가](#step-3-add-the-authentication-code)한 후에는 Azure AD B2C 설정을 통해 iOS Swift 앱을 구성합니다. Azure AD B2C ID 공급자 설정은 이전 섹션에서 선택한 `UIViewController` 클래스에서 구성합니다. 

iOS Swift 앱을 구성하는 방법을 알아보려면 [Azure AD B2C를 사용하여 샘플 iOS Swift 앱에서 인증 구성](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)을 참조하세요.

## <a name="step-5-run-and-test-the-mobile-app"></a>5단계: 모바일 앱 실행 및 테스트

1. [연결된 iOS 디바이스의 시뮬레이터](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)를 사용하여 프로젝트를 빌드하고 실행합니다.
1. **로그인** 을 선택한 다음, Azure AD B2C 로컬 또는 소셜 계정으로 가입하거나 로그인합니다.
1. 성공적으로 인증되면 탐색 모음에 표시 이름이 나타납니다.

## <a name="step-6-customize-your-code-building-blocks"></a>6단계: 코드 구성 요소 사용자 지정

이 섹션에서는 iOS Swift 앱에 대한 인증을 사용하도록 설정하는 코드 구성 요소에 대해 설명합니다. UIViewController의 메서드를 나열하고 코드를 사용자 지정하는 방법을 설명합니다. 

### <a name="step-61-instantiate-a-public-client-application"></a>6\.1단계: 공용 클라이언트 애플리케이션 인스턴스화

공용 클라이언트 애플리케이션은 애플리케이션 비밀을 안전하게 유지하도록 신뢰할 수 없으며 클라이언트 암호가 없습니다. [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload)에서 퍼블릭 클라이언트 애플리케이션 개체를 사용하여 MSAL을 인스턴스화합니다.

다음 Swift 코드 조각은 `MSALPublicClientApplicationConfig` 구성 개체를 사용하여 MSAL을 초기화하는 방법을 보여 줍니다. 

구성 개체는 Azure AD B2C 환경에 대한 정보를 제공합니다. 예를 들어 클라이언트 ID, 리디렉션 URI 및 Azure AD B2C에 대한 인증 요청을 작성할 수 있는 권한을 제공합니다. 구성 개체에 대한 자세한 내용은 [샘플 모바일 앱 구성](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)을 참조하세요.

```swift
do {

    let signinPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: signinPolicyAuthority)
    pcaConfig.knownAuthorities = [signinPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

`initWebViewParams` 메서드는 [대화형 인증](../active-directory/develop/customize-webviews.md) 환경을 구성합니다. 

다음 Swift 코드 조각은 시스템 웹 보기를 통해 `webViewParameters` 클래스 멤버를 초기화합니다. 자세한 내용은 [iOS/macOS용 브라우저 및 웹 보기 사용자 지정](../active-directory/develop/customize-webviews.md)을 참조하세요.

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParameters?.webviewType = .default
}
```

### <a name="step-62-start-an-interactive-authorization-request"></a>6\.2단계: 대화형 권한 부여 요청 시작

대화형 권한 부여 요청은 시스템 웹 보기를 사용하여 사용자에게 등록 또는 로그인하라는 메시지가 표시되는 흐름입니다. 사용자가 **로그인** 단추를 선택하면 `authorizationButton` 메서드가 호출됩니다.

`authorizationButton` 메서드는 권한 부여 요청과 관련된 데이터를 사용하여 `MSALInteractiveTokenParameters` 개체를 준비합니다. `acquireToken` 메서드는 `MSALInteractiveTokenParameters`를 사용하여 시스템 웹 보기를 통해 사용자를 인증합니다.

다음 코드 조각은 대화형 권한 부여 요청을 시작하는 방법을 보여 줍니다. 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error information" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
사용자가 권한 부여 흐름을 완료(성공 또는 실패)한 후 그 결과가 `acquireToken` 메서드의 [클로저](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)에 반환됩니다. 

`acquireToken` 메서드는 `result` 및 `error` 개체를 반환합니다. 이 클로저를 사용하여 다음을 수행합니다.

- 모바일 앱 UI를 인증이 완료된 후의 정보로 업데이트합니다.
- 액세스 토큰을 사용하여 웹 API 서비스를 호출합니다.
- 인증 오류를 처리합니다(사용자가 로그인 흐름을 취소하는 경우).
 
### <a name="step-63-call-a-web-api"></a>6\.3단계: 웹 API 호출

[토큰 기반 권한 부여 웹 API](enable-authentication-web-api.md)를 호출하려면 앱에 유효한 액세스 토큰이 필요합니다. 앱은 다음 작업을 수행합니다.

1. 웹 API 엔드포인트에 필요한 권한(범위)이 있는 액세스 토큰을 획득합니다.
1. 다음 형식을 사용하여 액세스 토큰을 HTTP 요청의 권한 부여 헤더에 전달자 토큰으로 전달합니다.

```http
Authorization: Bearer <access-token>
```

사용자가 [대화형으로 인증](#step-62-start-an-interactive-authorization-request)하면 앱이 `acquireToken` 클로저에서 액세스 토큰을 가져옵니다. 후속 웹 API 호출에서는 이 섹션에 설명된 대로 토큰 자동 획득(`acquireTokenSilent`) 메서드를 사용합니다. 

`acquireTokenSilent` 메서드는 다음 작업을 수행합니다.

1. 토큰 캐시에서 요청된 범위의 액세스 토큰을 가져오려고 시도합니다. 토큰이 있고 만료되지 않은 경우 토큰이 반환됩니다. 
1. 토큰이 토큰 캐시에 없거나 만료된 경우 MSAL 라이브러리는 새로 고침 토큰을 사용하여 새 액세스 토큰을 얻으려고 합니다. 
1. 새로 고침 토큰이 없거나 만료된 경우 예외가 반환됩니다. 이 경우 사용자에게 [대화형으로 로그인](#step-62-start-an-interactive-authorization-request)하라는 메시지를 표시해야 합니다.  

다음 코드 조각은 액세스 토큰을 획득하는 방법을 보여줍니다.

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

`callGraphAPI` 메서드는 액세스 토큰을 검색하고 다음과 같이 웹 API를 호출합니다.

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error information" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="step-64-sign-out-users"></a>6\.4단계: 사용자 로그아웃

MSAL을 사용하여 로그아웃하면 애플리케이션에서 사용자에 대한 알려진 모든 정보가 제거됩니다. 로그아웃 메서드를 사용하여 사용자를 로그아웃하고 UI를 업데이트하세요. 예를 들어 보호된 UI 요소를 숨기거나, 로그아웃 단추를 숨기거나, 로그인 단추를 표시할 수 있습니다.

다음 코드 조각은 사용자를 로그아웃하는 방법을 보여 줍니다.

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [Azure AD B2C를 사용하여 iOS Swift 앱에서 인증 옵션 구성](enable-authentication-ios-app-options.md)
* [Azure AD B2C를 사용하여 사용자 자체 웹 API에서 인증 사용](enable-authentication-web-api.md)
