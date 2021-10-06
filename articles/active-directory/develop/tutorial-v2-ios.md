---
title: '자습서: 인증을 위해 Microsoft ID 플랫폼을 사용하는 iOS 또는 macOS 웹앱 만들기 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 Microsoft ID 플랫폼을 사용하여 사용자를 로그인하고 사용자를 대신하여 Microsoft Graph API를 호출하는 액세스 토큰을 가져오는 iOS 또는 macOS 앱을 빌드합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 58cc1a94307be535f5d0c8d496d2394863738a6a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619707"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>자습서: iOS 또는 macOS 앱에서 사용자를 로그인하고 Microsoft Graph 호출

이 자습서에서는 Microsoft ID 플랫폼과 통합되어 사용자를 서명하고 Microsoft Graph API를 호출하는 액세스 토큰을 가져오는 iOS 또는 macOS 앱을 빌드합니다.

이 가이드를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다. 이 자습서는 iOS 및 macOS 앱 모두에 적용됩니다. 두 플랫폼의 단계가 약간 다릅니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * *Xcode* 에서 iOS 또는 macOS 앱 프로젝트 만들기
> * Azure Portal에 앱 등록
> * 사용자 로그인 및 로그아웃을 지원하는 코드 추가
> * Microsoft Graph API를 호출하는 코드 추가
> * 앱 테스트

## <a name="prerequisites"></a>필수 구성 요소

- [Xcode 11.x 이상](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>자습서 앱의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

이 자습서의 앱에서는 사용자로 로그인하고 사용자를 대신하여 Microsoft Graph에서 데이터를 가져올 수 있습니다. 이 데이터는 권한 부여가 필요하고 Microsoft ID 플랫폼으로 보호되는 API(여기서는 Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱이 브라우저 또는 Microsoft Authenticator를 통해 사용자를 로그인합니다.
* 최종 사용자는 애플리케이션에서 요청한 권한을 수락합니다.
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 인증을 구현합니다. MSAL은 자동으로 토큰을 갱신하고, 디바이스의 다른 앱 간에 SSO(Single Sign-On)를 제공하고, 계정을 관리합니다.

이 자습서에서 빌드한 앱의 완성본 버전을 다운로드하려는 경우 GitHub에서 두 버전을 모두 찾을 수 있습니다.

- [iOS 코드 샘플](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/)(GitHub)
- [macOS 코드 샘플](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/)(GitHub)

## <a name="create-a-new-project"></a>새 프로젝트 만들기

1. Xcode를 열고 **Create a new Xcode project** 를 선택합니다.
2. iOS 앱의 경우 **iOS** > **단일 보기 앱** 을 선택하고 **다음** 을 선택합니다.
3. macOS 앱의 경우 **macOS** > **Cocoa App** 을 선택하고 **다음** 을 선택합니다.
4. 프로젝트 이름을 제공합니다.
5. **언어** 를 **Swift** 로 설정하고 **다음** 을 선택합니다.
6. 앱을 만들 폴더를 선택하고 **만들기** 를 선택합니다.

## <a name="register-your-application"></a>애플리케이션 등록

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **관리** 에서 **인증** > **플랫폼 추가** > **iOS/macOS** 를 선택합니다.
1. 프로젝트의 번들 ID를 입력합니다. 코드를 다운로드한 경우 `com.microsoft.identitysample.MSALiOS`입니다. 사용자 고유의 프로젝트를 만드는 경우 Xcode에서 프로젝트를 선택하고 **일반** 탭을 엽니다. 번들 식별자가 **ID** 섹션에 나타납니다.
1. **구성** 을 선택하고 **MSAL 구성** 페이지에 나타나는 **MSAL 구성** 을 저장합니다. 그러면 나중에 앱을 구성할 때 이 구성을 입력할 수 있습니다. 
1. **완료** 를 선택합니다.

## <a name="add-msal"></a>MSAL 추가

앱에 MSAL 라이브러리를 설치하려면 다음 방법 중 하나를 선택합니다.

### <a name="cocoapods"></a>CocoaPods

1. [CocoaPods](https://cocoapods.org/)를 사용하는 경우 먼저 프로젝트의 `.xcodeproj` 파일과 동일한 폴더에 `podfile`이라는 빈 파일을 만들어 `MSAL`을 설치합니다. 다음 줄을 `podfile`에 추가합니다.

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>`를 프로젝트의 이름으로 바꿉니다.
3. 터미널 창에서 사용자가 만든 `podfile`을 포함하는 폴더로 이동하고 `pod install`을 실행하여 MSAL 라이브러리를 설치합니다.
4. Xcode를 닫고 `<your project name>.xcworkspace`를 열어 Xcode에서 프로젝트를 다시 로드합니다.

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage)를 사용하는 경우 `Cartfile`에 `MSAL`을 추가하여 설치합니다.

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

업데이트된 `Cartfile`과 동일한 디렉터리에 있는 터미널 창에서 다음 명령을 실행하여 Carthage가 프로젝트의 종속성을 업데이트하도록 합니다.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>수동

Git 하위 모듈을 사용하거나, 최신 릴리스를 확인하여 애플리케이션에 프레임워크로 사용할 수도 있습니다.

## <a name="add-your-app-registration"></a>앱 등록 추가

다음으로, 코드에 앱 등록을 추가합니다.

먼저, `AppDelegate.swift` 또는 `SceneDelegate.swift` 파일뿐만 아니라 `ViewController.swift`의 맨 위에 다음 import 문을 추가합니다.

```swift
import MSAL
```

그런 다음, 다음 코드를 `ViewController.swift`에서 `viewDidLoad()` 앞에 추가합니다.

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

위에서 수정한 값은 `kClientID`[애플리케이션 ID](./developer-glossary.md#application-id-client-id)로 할당된 값뿐입니다. 이 값은 이 자습서의 시작 부분에서 애플리케이션을 Azure Portal에 등록하는 단계에서 저장한 MSAL 구성 데이터의 일부입니다.

## <a name="configure-xcode-project-settings"></a>Xcode 프로젝트 설정 구성

프로젝트 **서명 및 기능** 에 새 키 집합 그룹을 추가합니다. 키 집합 그룹은 iOS에서는 `com.microsoft.adalcache`, macOS에서는 `com.microsoft.identity.universalstorage`여야 합니다.

![키 집합 그룹을 설정하는 방법을 표시하는 Xcode UI](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>iOS의 경우에만 URL 스키마 구성

이 단계에서는 로그인한 후 사용자를 앱으로 다시 리디렉션할 수 있도록 `CFBundleURLSchemes`를 등록합니다. 그런데 `LSApplicationQueriesSchemes`를 사용하면 앱에서 Microsoft Authenticator를 사용할 수도 있습니다.

Xcode에서 `Info.plist`를 소스 코드 파일로 열고 `<dict>` 섹션 내에 다음을 추가합니다. `[BUNDLE_ID]`를 Azure Portal에서 사용한 값으로 바꿉니다. 코드를 다운로드한 경우 번들 식별자는 `com.microsoft.identitysample.MSALiOS`입니다. 사용자 고유의 프로젝트를 만드는 경우 Xcode에서 프로젝트를 선택하고 **일반** 탭을 엽니다. 번들 식별자가 **ID** 섹션에 나타납니다.

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
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>macOS의 경우에만 App Sandbox 구성

1. Xcode 프로젝트 설정 > **기능 탭** > **App Sandbox** 로 이동
2. **나가는 연결(클라이언트)** 확인란을 선택합니다.

## <a name="create-your-apps-ui"></a>앱 UI 만들기

이제 `ViewController` 클래스에 다음 코드를 추가하여 Microsoft Graph API를 호출하는 단추, 로그아웃하기 위한 단추, 일부 출력을 보기 위한 텍스트 보기를 포함하는 UI를 만듭니다.

### <a name="ios-ui"></a>iOS UI

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

그런 다음, `ViewController` 클래스 내에서 `viewDidLoad()` 메서드를 다음으로 바꿉니다.

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL 사용

### <a name="initialize-msal"></a>MSAL 초기화

`ViewController` 클래스에 다음 `initMSAL` 메서드를 추가합니다.

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

`initMSAL` 메서드 뒤에 다음을 `ViewController` 클래스에 추가합니다.

### <a name="ios-code"></a>iOS 코드:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS 코드:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>iOS의 경우에만 로그인 콜백 처리

`AppDelegate.swift` 파일을 엽니다. 로그인 후 콜백을 처리하려면 다음과 같이 `appDelegate` 클래스에 `MSALPublicClientApplication.handleMSALResponse`를 추가합니다.

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Xcode 11을 사용하는 경우**`SceneDelegate.swift`에 MSAL 콜백을 대신 추가해야 합니다.
이전 iOS와의 호환성을 위해 UISceneDelegate 및 UIApplicationDelegate를 모두 지원하는 경우 MSAL 콜백을 두 파일에 배치해야 합니다.

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

#### <a name="acquire-tokens"></a>토큰 획득

이제 MSAL을 통해 대화형으로 애플리케이션의 UI 처리 논리를 구현하고 토큰을 가져올 수 있습니다.

MSAL은 토큰을 가져오기 위한 두 가지 기본 메서드 `acquireTokenSilently()` 및 `acquireTokenInteractively()`를 공개합니다.

- `acquireTokenSilently()`는 계정이 있는 한 사용자 개입 없이 사용자를 로그인하고 토큰을 가져오려고 시도합니다. `acquireTokenSilently()`는 MSAL 계정 열거 API 중 하나를 사용하여 검색할 수 있는 유효한 `MSALAccount`를 제공해야 합니다. 이 샘플에서는 `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})`을 사용하여 현재 계정을 검색합니다.

- `acquireTokenInteractively()`는 사용자를 로그인하려고 시도할 때 UI를 표시합니다. 대화형 SSO 환경을 제공하기 위해 브라우저에서 세션 쿠키를 사용하거나 Microsoft Authenticator에서 계정을 사용할 수 있습니다.

`ViewController` 클래스에 다음 코드를 추가합니다.

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>대화형으로 토큰 가져오기

다음 코드 조각은 `MSALInteractiveTokenParameters` 개체를 만들고 `acquireToken`을 호출하여 처음으로 토큰을 가져옵니다. 그런 다음, 아래 코드를 추가합니다.

1. 범위를 사용하여 `MSALInteractiveTokenParameters`를 만듭니다.
2. 만든 매개 변수를 사용하여 `acquireToken()`을 호출합니다.
3. 오류를 처리합니다. 자세한 내용은 [iOS 및 macOS용 MSAL 오류 처리 가이드](msal-error-handling-ios.md)를 참조하세요.
4. 성공 사례를 처리합니다.

`ViewController` 클래스에 다음 코드를 추가합니다.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

`MSALInteractiveTokenParameters`의 `promptType` 속성은 인증 및 동의 프롬프트 동작을 구성합니다. 지원되는 값은 다음과 같습니다.

- `.promptIfNecessary`(기본값) - 필요한 경우에만 사용자에게 메시지를 표시합니다. SSO 환경은 웹 보기의 쿠키 유무 및 계정 유형에 의해 결정됩니다. 여러 사용자가 로그인한 경우 계정 선택 환경이 표시됩니다. *기본 동작입니다*.
- `.selectAccount` - 사용자가 지정되지 않은 경우 인증 웹 보기는 사용자가 선택할 수 있도록 현재 로그인된 계정 목록을 표시합니다.
- `.login` - 사용자가 웹 보기에서 인증을 받아야 합니다. 이 값을 지정하는 경우 한 번에 하나의 계정만 로그인될 수 있습니다.
- `.consent` - 사용자는 요청에 대한 현재 범위 세트에 동의해야 합니다.

#### <a name="get-a-token-silently"></a>자동으로 토큰 가져오기

업데이트된 토큰을 자동으로 가져오려면 `ViewController` 클래스에 다음 코드를 추가합니다. `MSALSilentTokenParameters` 개체를 만들고 `acquireTokenSilent()`을 호출합니다.

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출

토큰을 획득한 후에는 앱에서 다음과 같이 이 토큰을 HTTP 헤더에 사용하여 Microsoft Graph에 대한 권한 있는 요청을 만들 수 있습니다.

| 헤더 키    | 값                 |
| ------------- | --------------------- |
| 권한 부여 | 전달자 \<access-token> |

`ViewController` 클래스에 다음 코드를 추가합니다.

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Microsoft Graph API에 대해 자세히 알아보려면 [Microsoft Graph API](https://graph.microsoft.com)를 참조하세요.

### <a name="use-msal-for-sign-out"></a>로그아웃에 MSAL 사용

다음으로, 로그아웃에 대한 지원을 추가합니다.

> [!Important]
> MSAL을 사용하여 로그아웃하면 사용자에 대해 알려진 모든 정보가 애플리케이션에서 제거되고 디바이스 구성에서 허용되는 경우 해당 디바이스의 활성 세션이 제거됩니다. 필요에 따라 브라우저에서 사용자를 로그 아웃할 수도 있습니다.

로그아웃 기능을 추가하려면 `ViewController` 클래스 내부에 다음 코드를 추가합니다.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>토큰 캐싱 사용

기본적으로 MSAL은 앱의 토큰을 iOS 또는 macOS 키 집합에 캐시합니다.

토큰 캐싱을 사용하려면 다음을 수행합니다.

1. 애플리케이션이 제대로 서명되었는지 확인
1. Xcode 프로젝트 설정 > **기능 탭** > **키 집합 공유 사용** 으로 이동합니다.
1. **+** 를 선택하고 다음 **키 집합 그룹** 중 하나를 입력합니다.
    - iOS: `com.microsoft.adalcache`
    - macOS: `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>도우미 메서드 추가
샘플을 완료하려면 `ViewController` 클래스에 다음과 같은 도우미 메서드를 추가합니다.

### <a name="ios-ui"></a>iOS UI:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS UI:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>iOS 전용인 경우 추가 디바이스 정보 가져오기

디바이스가 공유로 구성되었는지 여부를 포함하여 현재 디바이스 구성을 읽으려면 다음 코드를 사용합니다.

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>다중 계정 애플리케이션

이 앱은 단일 계정 시나리오를 위해 빌드됩니다. MSAL은 다중 계정 시나리오도 지원하지만, 그러려면 앱에서 몇 가지 추가 작업을 수행해야 합니다. 사용자가 토큰이 필요한 각 작업에 어떤 계정을 사용할 것인지 선택할 수 있는 UI를 만들어야 합니다. 또는 앱이 MSAL에서 모든 계정을 쿼리하여 사용할 계정을 선택하는 경험적 접근을 구현할 수 있습니다. 예를 들어 `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)를 참조하세요.

## <a name="test-your-app"></a>앱 테스트

앱을 빌드하여 테스트용 디바이스 또는 시뮬레이터에 배포합니다. 로그인하여 Azure AD 계정 또는 Microsoft 개인 계정의 토큰을 가져올 수 있어야 합니다.

사용자가 앱에 처음으로 로그인하면 Microsoft ID는 사용자에게 요청된 권한에 동의할 것을 요구합니다. 대부분의 사용자가 동의할 수 있지만, 사용자 동의가 해제된 일부 Azure AD 테넌트에서는 관리자가 모든 사용자를 대신하여 동의해야 합니다. 이 시나리오를 지원하려면 Azure Portal에서 앱의 범위를 등록해야 합니다.

로그인하면 이 앱은 Microsoft Graph `/me` 엔드포인트에서 반환된 데이터를 표시합니다.

## <a name="next-steps"></a>다음 단계

여러 부분으로 구성된 시나리오 시리즈에서 보호된 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [시나리오: 웹 API를 호출하는 모바일 애플리케이션](scenario-mobile-overview.md)
