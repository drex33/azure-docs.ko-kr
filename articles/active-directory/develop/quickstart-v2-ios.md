---
title: '빠른 시작: iOS 또는 macOS 앱에 Microsoft로 로그인 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 iOS 또는 macOS 앱이 사용자를 로그인하고, Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, Microsoft Graph API를 호출하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/14/2022
ROBOTS: NOINDEX
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, "scenarios:getting-started", "languages:iOS", mode-api
ms.openlocfilehash: 9278c0fa86af5a95413e55d9321f170bc6551def
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138036454"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>빠른 시작: iOS 또는 macOS 앱에서 사용자 로그인 및 Microsoft Graph API 호출

> [!div renderon="docs"]
> 환영합니다! 아마도 기대했던 페이지는 아닐 것입니다. 수정 작업을 진행하는 동안 이 링크를 통해 올바른 문서로 이동해야 합니다.
>
> > [빠른 시작: 사용자를 로그인하고 웹 API를 호출하는 iOS 또는 macOS 앱](mobile-app-quickstart.md?pivots=devlang-ios)
> 
> 이 문제를 해결하는 동안 불편을 끼쳐 드려 죄송하며 양해해 주셔서 감사합니다.

> [!div renderon="portal" class="sxs-lookup"]
> 이 빠른 시작에서는 네이티브 iOS 또는 macOS 애플리케이션이 사용자를 로그인하고 Microsoft Graph API를 호출할 액세스 토큰을 가져오는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다.
> 
> 빠른 시작은 iOS 및 macOS 앱 모두에 적용됩니다. 일부 단계는 iOS 앱에만 필요하며, 이러한 단계는 다음과 같이 표시됩니다.
> 
> ## <a name="prerequisites"></a>사전 요구 사항
> 
> * 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * XCode 10 이상
> * iOS 10 이상
> * macOS 10.12 이상
> 
> ## <a name="how-the-sample-works"></a>샘플 작동 방법
> 
> ![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-ios/ios-intro.svg)
> 
> #### <a name="step-1-configure-your-application"></a>1단계: 애플리케이션 구성
> 이 빠른 시작에 대한 코드 샘플이 작동하려면 인증 broker와 호환되는 **리디렉션 URI** 를 추가합니다.
> > [!div class="nextstepaction"]
> > [자동 변경]()
> 
> > [!div class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-ios/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.
> 
> #### <a name="step-2-download-the-sample-project"></a>2단계: 샘플 프로젝트 다운로드
> > [!div class="nextstepaction"]
> > [iOS용 코드 샘플 다운로드]()
> 
> > [!div class="nextstepaction"]
> > [macOS용 코드 샘플 다운로드]()
> 
> #### <a name="step-3-install-dependencies"></a>3단계: 종속성 설치
> 
> 1. zip 파일의 압축을 풉니다.
> 2. 터미널 창에서 다운로드한 코드 샘플이 있는 폴더로 이동하고 `pod install`을 실행하여 최신 MSAL 라이브러리를 설치합니다.
> 
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>4단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱 속성 값을 사용하여 프로젝트를 구성했고 실행할 준비가 되었습니다.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> 
> 1. [Azure AD 국가별 클라우드](/graph/deployments#app-registration-and-token-service-root-endpoints)에 대한 앱을 빌드하는 경우 'let kGraphEndpoint' 및 'let kAuthority'로 시작하는 줄을 올바른 엔드포인트로 바꿉니다. 글로벌 액세스의 경우 기본값을 사용합니다.
> 
>    ```swift
>    let kGraphEndpoint = "https://graph.microsoft.com/"
>    let kAuthority = "https://login.microsoftonline.com/common"
>    ```
> 
> 1. 다른 엔드포인트는 [여기에](/graph/deployments#app-registration-and-token-service-root-endpoints) 설명되어 있습니다. 예를 들어 Azure AD 독일에서 빠른 시작을 실행하려면 다음을 사용합니다.
> 
>    ```swift
>    let kGraphEndpoint = "https://graph.microsoft.de/"
>    let kAuthority = "https://login.microsoftonline.de/common"
>    ```
> 
> 3. 프로젝트 설정을 엽니다. **ID** 섹션에서 포털에 입력한 **번들 식별자** 를 입력합니다.
> 4. 마우스 오른쪽 단추로 **Info.plist** 를 클릭하고, **파일 열기 형식** > **소스 코드** 를 차례로 선택합니다.
> 5. dict 루트 노드 아래에서 `Enter_the_bundle_Id_Here`를 포털에서 사용한 ***번들 ID*** 로 바꿉니다. 문자열의 `msauth.` 접두사를 확인합니다.
> 
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 
> 6. 앱을 빌드하고 실행하세요!
> 
> ## <a name="more-information"></a>추가 정보
> 
> 이 빠른 시작에 대한 자세한 내용은 다음 섹션을 참조하세요.
> 
> ### <a name="get-msal"></a>MSAL 가져오기
> 
> MSAL([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))은 사용자를 로그인하고 Microsoft ID 플랫폼에서 보호되는 API 액세스에 사용되는 토큰을 요청하는 데 사용되는 라이브러리입니다. 다음 프로세스를 사용하여 애플리케이션에 MSAL을 추가할 수 있습니다.
> 
> ```
> $ vi Podfile
> ```
> 
> 다음을 이 podfile에 추가합니다(프로젝트의 대상 포함).
> 
> ```
> use_frameworks!
> 
> target 'MSALiOS' do
>    pod 'MSAL'
> end
> ```
> 
> CocoaPods 설치 명령을 실행합니다.
> 
> `pod install`
> 
> ### <a name="initialize-msal"></a>MSAL 초기화
> 
> 다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.
> 
> ```swift
> import MSAL
> ```
> 
> 그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.
> 
> ```swift
> let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
> 
> let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
> self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
> ```
> 
> > |위치: | Description |
> > |---------|---------|
> > | `clientId` | *portal.azure.com* 에 등록된 애플리케이션의 애플리케이션 ID |
> > | `authority` | Microsoft ID 플랫폼. 대부분의 경우 `https://login.microsoftonline.com/common` >과 같습니다. |
> > | `redirectUri` | 애플리케이션의 리디렉션 URI입니다. 'nil'을 전달하여 기본값을 사용하거나 사용자 지정 리디렉션 URI를 사용할 수 있습니다. |
> 
> ### <a name="for-ios-only-additional-app-requirements"></a>iOS 전용, 추가 앱 요구 사항
> 
> 앱의 `AppDelegate`에도 다음이 있어야 합니다. 이렇게 하면 인증을 수행할 때 MSAL SDK에서 인증 broker 앱의 토큰 응답을 처리할 수 있습니다.
> 
> ```swift
> func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
> 
>     return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
> }
> ```
> 
> > [!NOTE]
> > iOS 13 이상에서 `UIApplicationDelegate` 대신 `UISceneDelegate`를 채택하는 경우 이 코드를 `scene:openURLContexts:` 콜백에 대신 배치합니다([Apple 설명서](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc) 참조).
> > 이전 iOS와의 호환성을 위해 UISceneDelegate 및 UIApplicationDelegate를 모두 지원하는 경우 MSAL 콜백을 두 곳 모두에 배치해야 합니다.
> 
> ```swift
> func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
> 
>    guard let urlContext = URLContexts.first else {
>       return
>    }
> 
>    let url = urlContext.url
>    let sourceApp = urlContext.options.sourceApplication
> 
>    MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
> }
> ```
> 
> 마지막으로 앱의 ***Info.plist*** 에 `CFBundleURLTypes`와 함께 `LSApplicationQueriesSchemes` 항목이 있어야 합니다. 샘플에는 이 항목이 포함되어 있습니다.
> 
>    ```xml
>    <key>LSApplicationQueriesSchemes</key>
>    <array>
>       <string>msauthv2</string>
>       <string>msauthv3</string>
>    </array>
>    ```
> 
> ### <a name="sign-in-users--request-tokens"></a>사용자 로그인 및 토큰 요청
> 
> MSAL에는 토큰 가져오기에 사용되는 두 가지 메서드인 `acquireToken` 및 `acquireTokenSilent`가 있습니다.
> 
> #### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: 대화형으로 토큰 가져오기
> 
> 상황에 따라 사용자가 Microsoft ID 플랫폼과 상호 작용해야 합니다. 이러한 경우 최종 사용자는 해당 계정을 선택하거나, 해당 자격 증명을 입력하거나, 앱의 권한에 동의해야 할 수 있습니다. 예를 들면 다음과 같습니다.
> 
> * 처음으로 사용자가 애플리케이션에 로그인한 경우
> * 사용자가 자신의 암호를 다시 설정하는 경우 해당 자격 증명을 입력해야 합니다.
> * 애플리케이션에서 처음으로 리소스에 대한 액세스를 요청하는 경우
> * MFA 또는 기타 조건부 액세스 정책이 필요한 경우
> 
> ```swift
> let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
> self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
> ```
> 
> > |위치:| Description |
> > |---------|---------|
> > | `scopes` | 요청된 범위(즉 Microsoft Graph의 경우 `[ "user.read" ]`, 사용자 지정 웹 API(`api://<Application ID>/access_as_user`)의 경우 `[ "<Application ID URL>/scope" ]`)가 포함됩니다. |
> 
> #### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: 자동으로 액세스 토큰 가져오기
> 
> 사용자가 토큰을 요청할 때마다 앱에서 사용자가 로그인하도록 요구하지 않아야 합니다. 사용자가 이미 로그인한 경우 다음 메서드를 사용하면 앱에서 토큰을 자동으로 요청할 수 있습니다.
> 
> ```swift
> self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in
> 
>    guard let account = currentAccount else {
>       return
>    }
> 
>    let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
>    self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
> }
> ```
> 
> > |위치: | Description |
> > |---------|---------|
> > | `scopes` | 요청된 범위(즉 Microsoft Graph의 경우 `[ "user.read" ]`, 사용자 지정 웹 API(`api://<Application ID>/access_as_user`)의 경우 `[ "<Application ID URL>/scope" ]`)가 포함됩니다. |
> > | `account` | 토큰을 요청하는 계정입니다. 이 빠른 시작은 단일 계정 애플리케이션에 대한 것입니다. 다중 계정 앱을 빌드하려면 `accountsFromDeviceForParameters:completionBlock:`를 사용하여 토큰 요청에 사용할 계정을 식별하고 올바른 `accountIdentifier`를 전달하는 논리를 정의해야 합니다. |
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>다음 단계
> 
> Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, 이를 사용하여 Microsoft Graph API를 호출하는 iOS 또는 macOS 앱을 빌드하는 단계별 자습서로 이동합니다.
> 
> > [!div class="nextstepaction"]
> > [자습서: iOS 또는 macOS 앱에서 사용자를 로그인하고 Microsoft Graph 호출](tutorial-v2-ios.md)
