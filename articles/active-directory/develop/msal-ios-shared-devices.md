---
title: iOS 디바이스의 공유 디바이스 모드
titleSuffix: Microsoft identity platform | Azure
description: 일선 직원이 iOS 디바이스를 공유할 수 있도록 공유 디바이스 모드를 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 92d52bcdd990d255a41c151b2312cc2b6494bd45
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787190"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS 디바이스의 공유 디바이스 모드

>[!IMPORTANT]
> 이 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

소매 연결, 비행 승무원 및 현장 서비스 작업자 같은 일선 직원은 공유 모바일 디바이스를 사용해 업무를 수행하는 경우가 많습니다. 사용자가 자신의 암호나 PIN을 공유 디바이스에서 고객 및 비즈니스 데이터에 액세스하기 위해 의도적으로 또는 비의도적으로 공유하는 경우 이러한 공유 디바이스는 보안 위험을 초래할 수 있습니다.

공유 디바이스 모드를 사용하면 iOS 13 이상 디바이스를 직원들이 보다 쉽고 안전하게 공유할 수 있도록 구성할 수 있습니다. 직원은 로그인하여 고객 정보에 빠르게 액세스할 수 있습니다. 직원이 교대 근무 또는 작업을 마치면 공유 디바이스에서 로그아웃하여 다음 직원이 이 디바이스를 즉시 사용할 수 있도록 준비됩니다.

또한 공유 디바이스 모드는 디바이스의 Microsoft ID 지원 관리 기능을 제공 합니다.

이 기능은 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 사용하여 디바이스의 사용자를 관리하고 [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인](apple-sso-plugin.md)을 배포합니다.

## <a name="create-a-shared-device-mode-app"></a>공유 디바이스 모드 앱 만들기

공유 디바이스 모드 앱을 만들기 위해 개발자와 클라우드 디바이스 관리자는 다음과 같이 협업합니다.

1. **애플리케이션 개발자** 는 단일 계정 앱(여러 계정 앱은 공유 디바이스 모드에서 지원되지 않음)을 작성하고 공유 디바이스 로그아웃 등의 작업을 처리하는 코드를 작성합니다.

1. **디바이스 관리자** 는 조직의 디바이스를 관리하는 Microsoft Intune과 같은 MDM(모바일 디바이스 관리) 공급자를 사용하여 디바이스를 공유할 준비를 합니다. MDM은 Microsoft Authenticator 앱을 디바이스에 푸시하고 디바이스에 대한 프로필 업데이트를 통해 각 디바이스에 대해 "공유 모드"를 설정합니다. 이 공유 모드 설정은 디바이스에서 지원되는 앱의 동작을 변경합니다. MDM 공급자의 이 구성은 디바이스에 대한 공유 디바이스 모드를 설정하고 공유 디바이스 모드에 필요한 [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인](apple-sso-plugin.md)을 사용하도록 설정합니다.

1. [**공개 미리 보기 중에만 필요**] [클라우드 디바이스 관리자](../roles/permissions-reference.md#cloud-device-administrator) 역할이 있는 사용자는 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 시작하고 조직에 디바이스를 연결해야 합니다.

    Azure Portal에서 조직 역할의 멤버 자격 구성하기: **Azure Active Directory** > **역할 및 관리자** > **클라우드 디바이스 관리자**

다음 섹션에서는 공유 디바이스 모드를 지원하도록 애플리케이션을 업데이트하는 방법을 설명합니다.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune을 사용하여 공유 디바이스 모드 및 SSO 확장을 사용하도록 설정합니다.

> [!NOTE]
> 다음 단계는 공개 미리 보기로 제공되는 중에만 필요합니다.

공유 디바이스 모드를 지원하도록 디바이스를 구성해야 합니다. iOS 13 이상이 설치되어 있고 MDM에 등록되어 있어야 합니다. 또한 MDM 구성은 [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인](apple-sso-plugin.md)을 사용하도록 설정해야 합니다. SSO 확장에 대해 자세히 알아보려면 [Apple 비디오](https://developer.apple.com/videos/play/tech-talks/301/)를 참조하세요.

1. Intune 구성 포털에서 다음 구성을 사용하여 [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인](apple-sso-plugin.md)을 사용하도록 디바이스에 지시합니다.

    - **유형**: 리디렉션
    - **확장 ID**: com.microsoft.azureauthenticator.ssoextension
    - **팀 ID**: (이 필드는 iOS에 필요하지 않음)
    - **URL**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **구성할 추가 데이터**:
      - 키: sharedDeviceMode
      - 형식: Boolean
      - 값: true

    Intune을 사용하여 구성하는 방법에 대한 자세한 내용은 [Intune 구성 설명서](/intune/configuration/ios-device-features-settings)를 참조하세요.

1. 다음으로 MDM 프로필을 통해 디바이스에 Microsoft Authenticator 앱을 푸시하도록 MDM을 구성합니다.

    공유 디바이스 모드를 설정하려면 다음 구성 옵션을 설정합니다.

    - 구성 1:
      - 키: sharedDeviceMode
      - 형식: Boolean
      - 값: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>공유 디바이스 모드를 지원하도록 iOS 애플리케이션 수정

한 사용자의 데이터가 다른 사용자에게 유출되지 않도록 하는 것은 개발자인 여러분에게 달렸습니다. 다음 섹션에서는 변경이 발생하여 처리해야 함을 애플리케이션에 알리는 데 유용한 신호를 제공합니다.

앱이 사용될 때마다 디바이스에서 사용자의 상태를 확인한 다음, 이전 사용자의 데이터를 지워야 합니다. 이는 멀티태스킹 중 백그라운드에서 다시 로드되는 경우를 포함합니다.

사용자가 바뀌는 경우 이전 사용자의 데이터를 모두 지우고 애플리케이션에 표시되는 캐시된 데이터가 모두 제거되었는지 확인해야 합니다. 공유 디바이스 모드를 지원하도록 앱을 업데이트한 후 사용자와 회사에서 보안 검토 프로세스를 수행하는 것이 좋습니다.

### <a name="detect-shared-device-mode"></a>공유 디바이스 모드 검색

공유 디바이스 모드를 검색하는 것은 애플리케이션에 중요합니다. 애플리케이션을 공유 디바이스에서 사용하는 경우 많은 애플리케이션에서 UX(사용자 환경)을 변경해야 합니다. 예를 들어 애플리케이션에 "등록" 기능이 있을 수 있습니다. 이 기능은 일선 작업자에게 이미 계정이 있을 가능성이 있으므로 적합하지 않습니다. 또한 공유 디바이스 모드에 있는 경우 애플리케이션의 데이터 처리에 추가 보안을 추가할 수 있습니다.

`MSALPublicClientApplication`에서 `getDeviceInformationWithParameters:completionBlock:` API를 사용하여 앱이 공유 디바이스 모드의 디바이스에서 실행되고 있는지 확인합니다.

다음 코드 조각에서는 `getDeviceInformationWithParameters:completionBlock:` API를 사용하는 예제를 보여 줍니다.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>로그인한 사용자를 가져와서 사용자가 디바이스에서 변경되었는지 확인

공유 디바이스 모드 지원의 또 다른 중요한 부분은 디바이스의 사용자 상태를 확인하고 사용자가 변경되었거나 디바이스에 사용자가 없는 경우 애플리케이션 데이터를 지우는 것입니다. 다른 사용자가 데이터를 유출하지 않도록 할 책임이 있습니다.

`getCurrentAccountWithParameters:completionBlock:` API를 사용하여 디바이스에서 현재 로그인된 계정을 쿼리할 수 있습니다.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>전역적으로 사용자 로그인

디바이스가 공유 디바이스로 구성된 경우 애플리케이션은 `acquireTokenWithParameters:completionBlock:` API를 호출하여 계정에 로그인할 수 있습니다. 계정에서 첫 번째 앱 로그인 후 디바이스에서 모든 적격 앱에 대해 전역적으로 계정을 사용할 수 있습니다.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>전역적으로 사용자 로그아웃

다음 코드는 로그인한 계정을 제거하고, 앱뿐만 아니라 공유 디바이스 모드에 있는 디바이스에서도 캐시된 토큰을 지웁니다. 그러나 애플리케이션에서 *데이터* 를 지워야 하는 것은 아닙니다. 애플리케이션에서 데이터를 지우고 애플리케이션이 사용자에게 표시할 수 있는 캐시된 데이터를 지워야 합니다.

#### <a name="clear-browser-state"></a>브라우저 상태 지우기

> [!NOTE]
> 다음 단계는 공개 미리 보기로 제공되는 중에만 필요합니다.

이 공개 미리 보기 버전에서 [Apple 디바이스용 Microsoft Enterprise SSO 플러그 인](apple-sso-plugin.md)은 애플리케이션에 대해서만 상태를 지웁니다. Safari 브라우저에서 상태를 지우지 않습니다. 사용자 상태의 추적이 남지 않도록 하려면 브라우저 세션을 수동으로 삭제하는 것이 좋습니다. 아래 표시된 선택적 `signoutFromBrowser` 속성을 사용하여 쿠키를 지울 수 있습니다. 이로 인해 디바이스에서 브라우저가 잠시 시작됩니다.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>다음 단계

작동 중인 공유 디바이스 모드를 확인하기 위해 GitHub의 다음 코드 샘플에는 공유 디바이스 모드의 iOS 디바이스에서 일선 작업자 앱을 실행하는 예제가 포함되어 있습니다.

[MSAL iOS Swift Microsoft Graph API 샘플](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)