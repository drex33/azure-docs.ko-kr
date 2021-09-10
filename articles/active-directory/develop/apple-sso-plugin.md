---
title: Apple 디바이스용 Microsoft Enterprise SSO 플러그 인
titleSuffix: Microsoft identity platform | Azure
description: iOS, iPadOS 및 macOS 디바이스용 Azure Active Directory SSO 플러그 인에 대해 알아봅니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 5b490ff71253739779089da92c87532f7abbdbcc
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568067"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple 디바이스용 Microsoft Enterprise SSO 플러그 인(미리 보기)

>[!IMPORTANT]
> 이 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Apple 디바이스용 Microsoft Enterprise SSO 플러그 인* 은 Apple의 [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 기능을 지원하는 모든 애플리케이션에서 macOS, iOS 및 iPadOS의 Azure AD(Azure Active Directory) 계정에 대한 SSO(Single Sign-On) 계정을 제공합니다. 이 플러그 인은 비즈니스에서 의존할 수 있지만 아직 최신 ID 라이브러리 또는 프로토콜을 지원하지 않는 이전 애플리케이션에 대한 SSO를 제공합니다. Microsoft는 가능한 최상의 보호를 제공하면서 애플리케이션의 유용성을 높일 수 있는 이 플러그 인을 개발하기 위해 Apple과 긴밀하게 협력했습니다.

Enterprise SSO 플러그 인은 현재 다음 앱의 기본 제공 기능입니다.

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [회사 포털](/mem/intune/apps/apps-company-portal-macos): macOS

## <a name="features"></a>기능

Apple 디바이스용 Microsoft Enterprise SSO 플러그 인은 다음과 같은 이점을 제공합니다.

- Apple Enterprise SSO 기능을 지원하는 모든 애플리케이션에서 Azure AD 계정에 대한 SSO를 제공 합니다.
- 모든 MDM(모바일 디바이스 관리) 솔루션에서 사용하도록 설정할 수 있습니다.
- Microsoft ID 플랫폼 라이브러리를 아직 사용하지 않는 애플리케이션으로 SSO를 확장합니다.
- OAuth 2, OpenID Connect 및 SAML을 사용하는 애플리케이션으로 SSO를 확장합니다.

## <a name="requirements"></a>요구 사항

Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 사용하려면

- 디바이스는 Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 *지원* 하고 이 플러그 인이 있는 앱이 설치되어야 합니다.
  - iOS 13.0 이상: [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 이상: [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 이상: [Intune 회사 포털 앱](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- 디바이스를 *MDM에 등록* 해야 합니다(예: Microsoft Intune을 통해).
- Enterprise SSO 플러그 인을 사용하도록 설정하려면 구성을 *디바이스에 푸시* 해야 합니다. Apple에는 이 보안 제약 조건이 필요합니다.

### <a name="ios-requirements"></a>iOS 요구 사항:
- iOS 13.0 이상이 디바이스에 설치되어 있어야 합니다.
- Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 제공하는 Microsoft 애플리케이션을 디바이스에 설치해야 합니다. 퍼블릭 미리 보기의 경우 이러한 애플리케이션은 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)입니다.


### <a name="macos-requirements"></a>macOS 요구 사항:
- macOS 10.15 이상이 디바이스에 설치되어 있어야 합니다. 
- Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 제공하는 Microsoft 애플리케이션을 디바이스에 설치해야 합니다. 퍼블릭 미리 보기의 경우 이러한 애플리케이션에는 [Intune 회사 포털 앱](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)이 포함됩니다.

## <a name="enable-the-sso-plug-in"></a>SSO 플러그 인 사용

MDM을 사용하여 SSO 플러그 인을 사용하도록 설정하려면 다음 정보를 사용합니다.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune 구성

Microsoft Intune을 MDM 서비스로 사용하는 경우 기본 제공 구성 프로필 설정을 사용하여 Microsoft Enterprise SSO 플러그 인을 사용하도록 설정할 수 있습니다.

1. 구성 프로필의 [SSO 앱 확장](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) 설정을 구성합니다. 
1. 프로필이 아직 할당되지 않은 경우 [사용자 또는 디바이스 그룹에 프로필을 할당](/mem/intune/configuration/device-profile-assign)합니다.

SSO 플러그 인을 사용하도록 설정하는 프로필 설정은 다음에 각 디바이스가 Intune을 사용하여 체크 인할 때 그룹의 디바이스에 자동으로 적용됩니다.

### <a name="manual-configuration-for-other-mdm-services"></a>기타 MDM 서비스의 수동 구성

MDM에 Intune을 사용하지 않는 경우 Apple 디바이스에 대해 Extensible Single Sign On 프로필 페이로드를 구성할 수 있습니다. 다음 매개 변수를 사용하여 Microsoft Enterprise SSO 플러그인 및 해당 구성 옵션을 구성합니다.

iOS 설정:

- **확장 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **팀 ID**: 이 필드는 iOS에 필요하지 않습니다.

macOS 설정:

- **확장 ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **팀 ID**: `UBF8T346G9`

일반 설정:

- **유형**: 리디렉션
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>추가 구성 옵션
다른 구성 옵션을 추가하여 SSO 기능을 다른 앱으로 확장할 수 있습니다.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft ID 플랫폼 라이브러리를 사용하지 않는 앱에 SSO 사용

SSO 플러그 인은 MSAL(Microsoft 인증 라이브러리)과 같은 Microsoft SDK를 사용하여 개발되지 않은 경우에도 모든 애플리케이션을 SSO에 참여할 수 있도록 허용합니다.

SSO 플러그 인은 다음과 같은 디바이스에서 자동으로 설치됩니다.
* iOS 또는 iPadOS에서 Authenticator 앱을 다운로드하거나 macOS에서 Intune 회사 포털 앱을 다운로드했습니다.
* 해당 디바이스를 사용자 조직에 등록했습니다. 

조직에서는 MFA(다단계 인증), 암호 없는 인증 및 조건부 액세스와 같은 시나리오에 Authenticator 앱을 사용하는 경우가 많습니다. MDM 공급자를 사용하여 애플리케이션에 대한 SSO 플러그 인을 켤 수 있습니다. Microsoft는 Intune의 Microsoft Endpoint Manager 내에서 플러그 인을 쉽게 구성할 수 있도록 했습니다. SSO 플러그 인을 사용하도록 이러한 애플리케이션을 구성하는 데 허용 목록을 사용합니다.

>[!IMPORTANT]
> Microsoft Enterprise SSO 플러그 인은 네이티브 Apple 네트워크 기술 또는 웹 보기를 사용하는 앱만 지원합니다. 자체 네트워크 계층 구현을 전달하는 애플리케이션은 지원하지 않습니다.  

Microsoft ID 플랫폼 라이브러리를 사용하지 않는 앱에 대해 Microsoft Enterprise SSO 플러그 인을 구성하려면 다음 매개 변수를 사용합니다.

#### <a name="enable-sso-for-all-managed-apps"></a>모든 관리형 앱에 SSO 사용

- **키**: `Enable_SSO_On_All_ManagedApps`
- **형식**: `Integer`
- **값**: 1 또는 0.

이 플래그가 설정된 경우(해당 값이 `1`로 설정), `AppBlockList`에 없는 모든 MDM 관리형 앱이 SSO에 참여할 수 있습니다.

#### <a name="enable-sso-for-specific-apps"></a>특정 앱에 SSO 사용

- **키**: `AppAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 애플리케이션에 대한 쉼표로 구분된 애플리케이션 번들 ID 목록입니다.
- **예**: `com.contoso.workapp, com.contoso.travelapp`

>[!NOTE]
> Safari 및 Safari View Service는 기본적으로 SSO에 참여할 수 있습니다. AppBlockList에서 Safari 및 Safari View Service의 번들 ID를 추가하여 SSO에 참여하지 않도록 구성할 수 있습니다. iOS 번들 ID: [com.apple.mobilesafari, com.apple.SafariViewService] , macOS BundleID : com.apple.Safari

#### <a name="enable-sso-for-all-apps-with-a-specific-bundle-id-prefix"></a>특정 번들 ID 접두사가 있는 모든 앱에 SSO 사용
- **키**: `AppPrefixAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 애플리케이션에 대한 쉼표로 구분된 애플리케이션 번들 ID 접두사 목록입니다. 이 매개 변수는 특정 접두사로 시작하는 모든 앱이 SSO에 참여할 수 있도록 허용합니다.
- **예**: `com.contoso., com.fabrikam.`

#### <a name="disable-sso-for-specific-apps"></a>특정 앱에 대해 SSO 사용 안 함

- **키**: `AppBlockList`
- **형식**: `String`
- **값**: SSO에 참여할 수 없는 애플리케이션에 대한 쉼표로 구분된 애플리케이션 번들 ID 목록입니다.
- **예**: `com.contoso.studyapp, com.contoso.travelapp`

Safari 또는 Safari View Service에 대해 SSO를 *사용하지 않도록 설정* 하려면 번들 ID를 `AppBlockList`에 추가하여 명시적으로 이 작업을 수행해야 합니다. 

- iOS: `com.apple.mobilesafari`, `com.apple.SafariViewService`
- macOS: `com.apple.Safari`

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>특정 애플리케이션에 대해 쿠키를 통해 SSO를 사용하도록 설정

고급 네트워크 설정이 있는 일부 앱은 SSO에 대해 사용하도록 설정할 경우 예기치 않은 문제가 발생할 수 있습니다. 예를 들어, 네트워크 요청이 취소되었거나 중단되었음을 나타내는 오류가 표시될 수 있습니다.

다른 설정을 통해 사용하도록 설정한 후에도 애플리케이션에 로그인하는 데 문제가 있는 경우 `AppCookieSSOAllowList`에 추가하여 문제를 해결하세요.

- **키**: `AppCookieSSOAllowList`
- **형식**: `String`
- **값**: SSO에 참여할 수 있는 애플리케이션에 대한 쉼표로 구분된 애플리케이션 번들 ID 접두사 목록입니다. 나열된 접두사로 시작하는 모든 앱은 SSO에 참여할 수 있습니다.
- **예**: `com.contoso.myapp1, com.fabrikam.myapp2`

**기타 요구 사항**: `AppCookieSSOAllowList`를 사용하여 애플리케이션에 SSO를 사용하도록 설정하려면 번들 ID 접두사 `AppPrefixAllowList`도 추가해야 합니다.

예기치 않은 로그인 실패가 발생한 애플리케이션에 대해서만 이 구성을 시도하세요. 

#### <a name="summary-of-keys"></a>키 요약

| 키 | 형식 | 값 |
|--|--|--|
| `Enable_SSO_On_All_ManagedApps` | 정수 | 모든 관리형 앱에 대해 SSO를 사용하도록 설정하려면 `1`, 모든 관리형 앱에 대해 SSO를 사용하지 않도록 설정하려면 `0` |
| `AppAllowList` | String<br/>*(쉼표로 구분된 목록)* | SSO에 참여할 수 있도록 허용된 애플리케이션의 번들 ID입니다. |
| `AppBlockList` | String<br/>*(쉼표로 구분된 목록)* | SSO에 참여할 수 있도록 허용되지 않는 애플리케이션의 번들 ID입니다. |
| `AppPrefixAllowList` | String<br/>*(쉼표로 구분된 목록)* | SSO에 참여할 수 있도록 허용된 애플리케이션의 번들 ID 접두사입니다. |
| `AppCookieSSOAllowList` | String<br/>*(쉼표로 구분된 목록)* | SSO에 참여할 수 있도록 허용되지만 특수 네트워크 설정을 사용하고 다른 설정을 사용하는 SSO에서 문제가 발생하는 애플리케이션의 번들 ID 접두사입니다. `AppCookieSSOAllowList`에 추가하는 앱을 `AppPrefixAllowList`에도 추가해야 합니다. |

#### <a name="settings-for-common-scenarios"></a>일반적인 시나리오에 대한 설정

- *시나리오*: 전체는 아니지만 대부분의 관리형 애플리케이션에 대해 SSO를 사용하도록 설정하려고 합니다.

    | 키 | 값 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | SSO에 참여하지 못하게 하려는 앱의 번들 ID(쉼표로 구분된 목록)입니다. |

- *시나리오* 기본적으로 사용하도록 설정되어 있는 Safari용 SSO를 사용하지 않도록 설정하고 모든 관리형 앱에 대해 SSO를 사용하도록 설정하려고 합니다.

    | 키 | 값 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | SSO에 참여하지 못하게 하려는 Safari 앱의 번들 ID(쉼표로 구분된 목록)입니다.<br/><li>iOS의 경우: `com.apple.mobilesafari`, `com.apple.SafariViewService`<br/><li>macOS의 경우: `com.apple.Safari` |

- *시나리오*: 모든 관리형 앱과 비관리형 앱에서 SSO를 사용하도록 설정하고 몇 가지 다른 앱에 SSO를 사용하지 않도록 설정하려고 합니다.

    | 키 | 값 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppAllowList` | SSO에 참여할 수 있도록 설정하려는 앱의 번들 ID(쉼표로 구분된 목록)입니다. |
    | `AppBlockList` | SSO에 참여하지 못하게 하려는 앱의 번들 ID(쉼표로 구분된 목록)입니다. |


##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>iOS 디바이스에서 앱 번들 식별자 찾기

Apple은 App Store에서 번들 ID를 가져오는 편리한 방법을 제공하지 않습니다. SSO에 사용하려는 앱의 번들 ID를 가져오는 가장 쉬운 방법은 공급업체 또는 앱 개발자에게 요청하는 것입니다. 해당 옵션을 사용할 수 없는 경우 MDM 구성을 사용하여 번들 ID를 찾을 수 있습니다. 

1. MDM 구성에서 일시적으로 다음 플래그를 사용하도록 설정합니다.

    - **키**: `admin_debug_mode_enabled`
    - **형식**: `Integer`
    - **값**: 1 또는 0
1. 이 플래그가 설정되면 번들 ID를 알고 싶은 디바이스에서 iOS 앱으로 로그인합니다. 
1. Authenticator 앱에서 **도움말**  > **로그 보내기** > **로그 보기** 를 선택합니다. 
1. 로그 파일에서 `[ADMIN MODE] SSO extension has captured following app bundle identifiers` 줄을 찾습니다. 이 줄은 SSO 확장에 표시되는 모든 애플리케이션 번들 ID를 캡처합니다. 

번들 ID를 사용하여 앱에 대한 SSO를 구성합니다. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>사용자가 알 수 없는 애플리케이션 및 Safari 브라우저에서 로그인할 수 있도록 허용

기본적으로 Microsoft Enterprise SSO 플러그 인은 MSAL 또는 ADAL(Azure Active Directory 인증 라이브러리)과 같은 Microsoft ID 플랫폼 라이브러리를 사용하는 앱에서 로그인한 경우에만 권한 있는 앱에 대한 SSO를 제공합니다. Microsoft Enterprise SSO 플러그 인은 새로운 토큰 획득 동안 Microsoft ID 플랫폼 라이브러리를 사용하는 다른 앱에서 호출되는 경우 공유 자격 증명을 획득할 수도 있습니다.

`browser_sso_interaction_enabled` 플래그를 사용하는 경우 Microsoft ID 플랫폼 라이브러리를 사용하지 않는 앱은 초기 부트스트랩을 수행하고 공유 자격 증명을 가져올 수 있습니다. Safari 브라우저는 초기 부트스트랩을 수행하고 공유 자격 증명을 가져올 수도 있습니다. 

Microsoft Enterprise SSO 플러그 인에 공유 자격 증명이 아직 없는 경우 Safari 브라우저, ASWebAuthenticationSession, SafariViewController 또는 허용된 다른 네이티브 애플리케이션 내의 Azure AD URL에서 로그인이 요청될 때마다 공유 자격 증명을 가져오려고 시도합니다. 

다음 매개 변수를 사용하여 플래그를 사용하도록 설정합니다. 

- **키**: `browser_sso_interaction_enabled`
- **형식**: `Integer`
- **값**: 1 또는 0

macOS는 모든 앱에서 일관된 환경을 제공하기 위해 이 설정이 필요합니다. 대부분의 앱은 로그인을 위해 Authenticator 애플리케이션을 사용하므로 iOS 및 iPadOS에는 이 설정이 필요하지 않습니다. 하지만 일부 애플리케이션이 iOS 또는 iPadOS에서 Authenticator 앱을 사용하지 않는 경우 이 플래그를 사용하면 환경이 개선되므로 이 설정을 사용하도록 설정하는 것이 좋습니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>초기 부트스트랩 중에 MFA 요청을 사용하지 않도록 설정

기본적으로 Microsoft Enterprise SSO 플러그 인은 초기 부트스트랩 동안과 공유 자격 증명을 가져오는 동안 사용자에게 MFA를 묻는 메시지를 항상 표시합니다. 사용자가 연 애플리케이션에 필요하지 않은 경우에도 사용자에게 MFA를 입력하라는 메시지가 표시됩니다. 이 동작은 나중에 MFA가 필요한 경우 사용자에게 메시지를 표시하지 않고도 다른 모든 애플리케이션에서 공유 자격 증명을 쉽게 사용할 수 있도록 합니다. 이 설정을 사용하면 사용자에게 표시되는 확인 메시지 수가 더 줄어들므로 좋은 결정입니다.

`browser_sso_disable_mfa`를 사용하도록 설정하면 초기 부트스트랩 동안과 공유 자격 증명을 가져오는 동안 MFA가 꺼집니다. 이 경우 애플리케이션 또는 리소스에 MFA가 필요한 경우에만 사용자에게 메시지가 표시됩니다. 

이 플래그를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

- **키**: `browser_sso_disable_mfa`
- **형식**: `Integer`
- **값**: 1 또는 0

사용자에게 로그인하라는 메시지가 표시되는 횟수가 줄어들므로 이 플래그를 사용하지 않도록 설정하는 것이 좋습니다. 조직에서 MFA를 거의 사용하지 않는 경우 이 플래그를 사용하도록 설정할 수 있습니다. 하지만 대신 MFA를 더 자주 사용하는 것이 좋습니다. 이러한 이유로 이 플래그는 기본적으로 사용하지 않도록 설정됩니다.

#### <a name="disable-oauth-2-application-prompts"></a>OAuth 2 애플리케이션 프롬프트 사용 안 함

Microsoft Enterprise SSO 플러그 인은 허용된 애플리케이션에서 시작되는 네트워크 요청에 공유 자격 증명을 추가하여 SSO를 제공합니다. 그러나 일부 OAuth 2 애플리케이션은 프로토콜 계층에서 최종 사용자 프롬프트를 잘못 적용할 수 있습니다. 이 문제가 표시되는 경우 해당 앱에 대해 공유 자격 증명이 무시되는 것을 알 수 있습니다. Microsoft Enterprise SSO 플러그 인이 다른 애플리케이션에 대해 작동하는 경우에도 사용자에게 로그인하라는 메시지가 표시됩니다.  

`disable_explicit_app_prompt` 플래그를 사용하도록 설정하면 네이티브 애플리케이션 및 웹 애플리케이션에서 프로토콜 계층에 대한 최종 사용자 프롬프트를 강제로 실행하고 SSO를 우회하는 기능이 제한됩니다. 이 플래그를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

- **키**: `disable_explicit_app_prompt`
- **형식**: `Integer`
- **값**: 1 또는 0

모든 앱에서 일관된 환경을 제공하기 위해 이 플래그를 사용하도록 설정하는 것이 좋습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 

#### <a name="use-intune-for-simplified-configuration"></a>간소화된 구성에 Intune 사용

Intune을 MDM 서비스로 사용하여 Microsoft Enterprise SSO 플러그 인을 쉽게 구성할 수 있습니다. 예를 들어, Intune을 사용하여 플러그 인을 사용하도록 설정하고 허용 목록에 이전 앱을 추가하여 SSO를 가져오도록 할 수 있습니다. 

자세한 내용은 [Intune 구성 설명서](/intune/configuration/ios-device-features-settings)를 참조하세요.

## <a name="use-the-sso-plug-in-in-your-application"></a>애플리케이션에서 SSO 플러그 인 사용

[Apple 디바이스용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 버전 1.1.0 이상은 Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 지원합니다. 이것은 Microsoft Enterprise SSO 플러그 인에 대한 지원을 추가하기 위한 권장되는 방법입니다. 이를 통해 Microsoft ID 플랫폼의 전체 기능을 사용할 수 있습니다.

최전방 작업자 시나리오용 애플리케이션을 빌드하는 경우 설치 정보는 [iOS 디바이스에 대한 공유 디바이스 모드](msal-ios-shared-devices.md)를 참조하세요.

## <a name="understand-how-the-sso-plug-in-works"></a>SSO 플러그 인 작동 방식 이해

Microsoft Enterprise SSO 플러그 인은 [Apple Enterprise SSO 프레임워크](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)에 의존합니다. 프레임워크를 조인하는 ID 공급자는 해당 도메인에 대한 네트워크 트래픽을 가로채서 해당 요청이 처리되는 방식을 개선하거나 변경할 수 있습니다. 예를 들어, SSO 플러그 인은 최종 사용자 자격 증명을 안전하게 수집하거나, MFA를 요구하거나, 애플리케이션에 토큰을 자동으로 제공하는 추가 UI를 표시할 수 있습니다.

또한 네이티브 애플리케이션은 사용자 지정 작업을 구현하고 SSO 플러그 인과 직접 통신할 수 있습니다. 자세한 내용은 [Apple의 2019년 전 세계 개발자 회의 비디오](https://developer.apple.com/videos/play/tech-talks/301/)를 참조하세요.

### <a name="applications-that-use-msal"></a>MSAL을 사용하는 애플리케이션

[Apple 디바이스용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 버전 1.1.0 이상은 기본적으로 회사 및 학교 계정을 위한 Apple 디바이스용 Microsoft Enterprise SSO 플러그 인을 지원합니다. 

[모든 권장 단계](./quickstart-v2-ios.md)를 따르고 기본 [리디렉션 URI 형식](./redirect-uris-ios.md)을 사용한 경우 특별한 구성이 필요하지 않습니다. SSO 플러그 인이 있는 디바이스에서 MSAL은 모든 대화형 및 자동 토큰 요청에 대해 이 플러그 인을 자동으로 호출합니다. 또한 계정 열거 및 계정 제거 작업에 대해서도 호출합니다. MSAL은 사용자 지정 작업에 의존하는 네이티브 SSO 플러그 인 프로토콜을 구현하기 때문에 이 설치 프로그램은 최종 사용자에게 가장 원활한 네이티브 환경을 제공합니다. 

SSO 플러그 인이 MDM에서 사용하도록 설정되어 있지 않지만 Microsoft Authenticator 앱이 디바이스에 있는 경우 MSAL은 모든 대화형 토큰 요청에 Authenticator 앱을 대신 사용합니다. SSO 플러그 인은 Authenticator 앱과 SSO를 공유합니다.

### <a name="applications-that-dont-use-msal"></a>MSAL을 사용하지 않는 애플리케이션

MSAL과 같은 Microsoft ID 플랫폼 라이브러리를 사용하지 않는 애플리케이션은 관리자가 이러한 애플리케이션을 허용 목록에 추가하는 경우에도 SSO를 가져올 수 있습니다. 

다음 조건이 충족되기만 하면 해당 앱의 코드를 변경할 필요가 없습니다.

- 애플리케이션은 Apple 프레임워크를 사용하여 네트워크 요청을 실행합니다. 예를 들어 이러한 프레임워크는 [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 및 [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)을 포함합니다. 
- 애플리케이션은 표준 프로토콜을 사용하여 Azure AD와 통신합니다. 예를 들어, 이러한 프로토콜에는 OAuth 2, SAML, WS-FEDERATION 등이 있습니다.
- 애플리케이션은 네이티브 UI에서 일반 텍스트 사용자 이름 및 암호를 수집하지 않습니다.

이 경우 애플리케이션에서 네트워크 요청을 만들고 사용자를 로그인할 웹 브라우저를 열면 SSO가 제공됩니다. 사용자가 Azure AD 로그인 URL로 리디렉션되는 경우 SSO 플러그 인은 URL의 유효성을 검사하고 해당 URL에 대한 SSO 자격 증명을 확인합니다. SSO 플러그 인은 찾은 자격 증명을 Azure AD에 전달합니다. 그러면 사용자에게 자격 증명을 입력하도록 요구하지 않으면서 애플리케이션에서 네트워크 요청을 완료할 수 있는 권한을 부여합니다. 또한 디바이스가 Azure AD에 알려진 경우 SSO 플러그 인은 디바이스 기반 조건부 액세스 검사를 충족하기 위해 디바이스 인증서를 전달합니다. 

MSAL이 아닌 앱에 대해 SSO를 지원하기 위해 SSO 플러그 인은 [기본 새로 고침 토큰이란?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)에 설명된 Windows 브라우저 플러그 인과 비슷한 프로토콜을 구현합니다. 

MSAL 기반 앱에 비해 SSO 플러그 인은 MSAL이 아닌 앱에 대해 더 투명하게 작동합니다. 앱이 제공하는 기존 브라우저 로그인 환경과 통합됩니다. 

최종 사용자에게 친숙한 환경이 표시되며 각 애플리케이션에서 다시 로그인할 필요가 없습니다. 예를 들어, 네이티브 계정 선택을 표시하는 대신, SSO 플러그 인은 웹 기반 계정 선택 환경에 SSO 세션을 추가합니다. 

## <a name="next-steps"></a>다음 단계

[iOS 디바이스의 공유 디바이스 모드](msal-ios-shared-devices.md)에 대해 자세히 알아봅니다.
