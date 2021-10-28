---
title: MSAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: Android용 MSAL(Microsoft 인증 라이브러리)을 사용하여 모든 애플리케이션에서 Single Sign-On을 사용하도록 설정하는 방법입니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: marsma
ms.reviewer: marsma
ms.openlocfilehash: 877ebb2ab58af978896e4cb61989e12bb5731a7c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628917"
---
# <a name="enable-cross-app-sso-on-android-using-msal"></a>MSAL을 사용하여 Android에서 앱 간 SSO 사용

SSO(Single Sign-On)으로 사용자가 자격 증명을 한 번 입력하면 해당 자격 증명이 모든 애플리케이션에서 자동으로 작동하도록 할 수 있습니다.

[Microsoft ID 플랫폼](./index.yml)과 MSAL(Microsoft 인증 라이브러리)을 사용하면 자체 앱 제품군에서 SSO를 사용하도록 설정할 수 있습니다. 브로커 기능과 Authenticator 애플리케이션을 사용하여 전체 디바이스로 SSO를 확장할 수 있습니다.

이 방법에서는 고객에게 SSO를 제공하도록 애플리케이션에서 사용하는 SDK를 구성하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법에서는 다음 작업을 수행하는 방법을 알고 있다고 가정합니다.

- Azure Portal를 사용하여 앱 프로비전. 이 항목에 대한 자세한 내용은 [Android 자습서](./tutorial-v2-android.md#create-a-project)의 앱 만들기 지침을 참조하세요.
- [Android용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-android)와 애플리케이션 통합

## <a name="methods-for-single-sign-on"></a>Single Sign-On 구현 방법

Android용 MSAL을 사용하는 애플리케이션은 다음 두 가지 방법으로 SSO를 구현할 수 있습니다.

* [브로커 애플리케이션](#sso-through-brokered-authentication)을 통해
* [시스템 브라우저](#sso-through-system-browser)를 통해


   디바이스 전체 SSO, 계정 관리, 조건부 액세스 등의 이점을 위해 브로커 애플리케이션을 사용하는 것이 좋습니다. 그러나 사용자는 추가 애플리케이션을 다운로드해야 합니다.

## <a name="sso-through-brokered-authentication"></a>조정된 인증을 통한 SSO

Microsoft의 인증 브로커 중 하나를 사용하여 디바이스 전체 SSO(Single Sign-On)에 참여하고 조직의 조건부 액세스 정책을 충족하는 것이 좋습니다. 브로커와 통합하면 다음과 같은 이점이 있습니다.

- 디바이스 Single Sign-On
- 다음을 위한 조건부 액세스:
  - Intune 앱 보호
  - 디바이스 등록(Workplace Join)
  - Mobile Device Management
- 디바이스 전체 계정 관리
  -  Android AccountManager 및 계정 설정을 통해
  - "회사 계정" - 사용자 지정 계정 형식

Android에서 Microsoft Authentication Broker는 [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 및 [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) 앱에 포함된 구성 요소입니다.

다음 다이어그램에서는 앱, MSAL(Microsoft 인증 라이브러리) 및 Microsoft의 인증 브로커 간의 관계를 보여줍니다.

![애플리케이션과 MSAL, 브로커 앱 및 Android 계정 관리자의 관계를 보여주는 다이어그램](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>브로커를 호스트하는 앱 설치

브로커 호스팅 앱은 언제든지 디바이스 소유자가 앱 스토어(일반적으로 Google Play 스토어)에서 설치할 수 있습니다. 그러나 일부 API(리소스)는 디바이스에 다음을 요구하는 조건부 액세스 정책에 의해 보호됩니다.

- 등록됨(작업 공간에 연결됨) 및/또는
- 디바이스 관리에 등록됨
- Intune 앱 보호에 등록됨

디바이스에 브로커 앱이 아직 설치되어 있지 않은 경우 MSAL은 앱이 대화형으로 토큰을 가져오려고 하는 즉시 사용자에게 브로커 앱을 설치하도록 지시합니다. 그런 다음, 앱이 디바이스가 필수 정책을 준수하도록 하는 단계를 사용자에게 안내해야 합니다.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>브로커 설치 및 제거의 효과

#### <a name="when-a-broker-is-installed"></a>브로커 설치 시

브로커가 디바이스에 설치되면 이후의 모든 대화형 토큰 요청(`acquireToken()`에 대한 호출)이 MSAL에서 로컬로 처리되지 않고 브로커에서 처리됩니다. 이전에 MSAL에서 사용할 수 있었던 SSO 상태는 브로커에서 사용할 수 없습니다. 따라서 사용자는 다시 인증하거나 디바이스에 알려진 기존 계정 목록에서 계정을 선택해야 합니다.

브로커를 설치할 때 사용자가 다시 로그인할 필요는 없습니다. 사용자가 `MsalUiRequiredException`을 해결해야 하는 경우에만 다음 요청이 브로커로 이동합니다. `MsalUiRequiredException`은 여러 가지 이유로 발생할 수 있으며 대화식으로 해결해야 합니다. 예를 들면 다음과 같습니다.

- 사용자가 자신의 계정과 연결된 암호를 변경했습니다.
- 사용자의 계정이 더 이상 조건부 액세스 정책을 충족하지 않습니다.
- 사용자가 앱과 계정 연결에 대한 동의를 취소했습니다.

**복수 브로커** - 디바이스에 여러 브로커가 설치된 경우 먼저 설치된 브로커가 항상 활성 브로커입니다. 디바이스에서 하나의 브로커만 활성 상태일 수 있습니다.

#### <a name="when-a-broker-is-uninstalled"></a>브로커 제거 시

유일하게 설치된 브로커 호스팅 앱이 제거되면 사용자는 다시 로그인해야 합니다. 활성 브로커를 제거하면 디바이스에서 계정과 관련 토큰이 제거됩니다.

Intune 회사 포털이 설치되어 활성 브로커로 작동 중이고 Microsoft Authenticator도 설치되어 있는 경우 Intune 회사 포털(활성 브로커)이 제거되면 사용자는 다시 로그인해야 합니다. 다시 로그인하면 Microsoft Authenticator 앱이 활성 브로커가 됩니다.

### <a name="integrating-with-a-broker"></a>브로커와 통합

#### <a name="generate-a-redirect-uri-for-a-broker"></a>브로커에 대한 리디렉션 URI 생성

브로커와 호환되는 리디렉션 URI를 등록해야 합니다. 브로커의 리디렉션 URI에는 앱의 패키지 이름과 앱 서명의 Base64로 인코딩된 표현이 포함되어야 합니다.

리디렉션 URI의 형식은 `msauth://<yourpackagename>/<base64urlencodedsignature>`입니다.

[keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html)을 사용하여 앱의 서명 키로 Base64로 인코딩된 서명 해시를 생성한 다음, Azure Portal을 사용하여 해당 해시로 리디렉션 URI를 생성할 수 있습니다.

Linux 및 macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

*keytool* 을 사용하여 서명 해시를 생성했으면 Azure Portal을 사용하여 리디렉션 URI를 생성합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인하고 **앱 등록** 에서 Android 앱을 선택 합니다.
1. **인증** > **플랫폼 추가** > **Android** 를 선택합니다.
1. **Android 앱 구성** 창이 열리면 앞서 생성한 **서명 해시** 와 **패키지 이름** 을 입력합니다.
1. **구성** 단추를 클릭합니다.

Azure Portal이 리디렉션 URI를 생성하여 **Android 구성** 창의 **리디렉션 URI** 필드에 표시합니다.

앱에 서명하는 방법에 대한 자세한 내용은 Android Studio 사용자 가이드의 [앱에 서명](https://developer.android.com/studio/publish/app-signing)을 참조하세요.

> [!IMPORTANT]
> 앱의 프로덕션 버전에는 프로덕션 서명 키를 사용합니다.

#### <a name="configure-msal-to-use-a-broker"></a>broker를 사용하도록 MSAL 구성

앱에서 브로커를 사용하려면 브로커 리디렉션을 구성했음을 인증해야 합니다. 예를 들어 MSAL 구성 파일에 다음 설정을 포함하여 브로커 사용 리디렉션 URI를 모두 포함하고 등록했음을 표시합니다.

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>브로커 관련 예외

MSAL은 다음 두 가지 방법으로 브로커와 통신합니다.

- 브로커에 바인딩된 서비스
- Android AccountManager

이 서비스를 호출하는 데 Android 권한이 필요하지 않으므로 MSAL은 먼저 브로커에 바인딩 서비스를 사용합니다. 바인딩된 서비스에 바인딩이 실패하면 MSAL은 Android AccountManager API를 사용합니다. 단, 앱에 `"READ_CONTACTS"` 권한이 이미 부여된 경우에 한합니다.

오류 코드 `"BROKER_BIND_FAILURE"`와 함께 `MsalClientException`이 표시될 경우 두 가지 옵션이 있습니다.

- 사용자에게 Microsoft Authenticator 앱과 Intune 회사 포털에 전원 최적화를 사용하지 않도록 설정할 것을 요청합니다.
- 사용자에게 `"READ_CONTACTS"` 권한 부여를 요청합니다.

### <a name="verify-broker-integration"></a>브로커 통합 확인

브로커 통합 작동 여부가 분명하지 않을 경우 다음 단계를 사용하여 확인할 수 있습니다.

1. Android 디바이스에서 브로커를 사용하여 요청을 완료합니다.
1. Android 디바이스의 설정에서 인증한 계정에 해당하는 새로 만든 계정을 찾습니다. 계정은 *회사 계정* 형식이어야 합니다.

테스트를 반복하려는 경우 설정에서 계정을 제거할 수 있습니다.

## <a name="sso-through-system-browser"></a>시스템 브라우저를 통한 SSO

Android 애플리케이션에는 인증 사용자 경험을 위해 WebView, 시스템 브라우저 또는 Chrome Custom Tabs를 사용하는 옵션이 있습니다. 애플리케이션이 조정된 인증을 사용하지 않는 경우 SSO를 구현하려면 네이티브 웹 보기가 아닌 시스템 브라우저를 사용해야 합니다.

### <a name="authorization-agents"></a>권한 부여 에이전트

권한 부여 에이전트에 대한 특정 전략(선택 사항)을 선택하면 앱이 사용자 지정할 수 있는 추가 기능을 나타냅니다. 대부분의 앱은 MSAL 기본값을 사용합니다(다양한 기본값을 보려면 [Android MSAL 구성 파일 이해](msal-configuration.md) 참조).

MSAL은 `WebView` 또는 시스템 브라우저를 사용한 권한 부여를 지원합니다.  아래 이미지는 `WebView` 또는 CustomTabs가 있거나 CustomTabs가 없는 시스템 브라우저를 사용하는 모습을 보여줍니다.

![MSAL 로그인 예](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Single Sign-On 의미

기본적으로 MSAL과 통합된 애플리케이션은 시스템 브라우저의 Custom Tabs를 사용하여 인증합니다. WebView와 달리 Custom Tabs는 기본 시스템 브라우저와 쿠키 보관함을 공유하여 Custom Tabs와 통합된 웹 또는 기타 네이티브 앱 로그인을 줄일 수 있습니다.

애플리케이션이 Microsoft Authenticator 또는 회사 포털 지원을 앱에 통합하지 않고 `WebView` 전략을 사용하는 경우 사용자는 디바이스 전체 또는 네이티브 앱과 웹앱 간에 Single Sign-On을 이용할 수 없습니다.

애플리케이션이 Microsoft Authenticator 또는 Intune 회사 포털과 같은 브로커와 함께 MSAL을 사용하는 경우 사용자는 앱 중 하나에 활성 로그인이 있으면 모든 애플리케이션에서 SSO를 이용할 수 있습니다.

### <a name="webview"></a>WebView

앱 내 WebView를 사용하려면 MSAL에 전달되는 앱 구성 JSON에 다음 줄을 입력합니다.

```json
"authorization_user_agent" : "WEBVIEW"
```

앱 내 `WebView`를 사용하면 사용자가 앱에 직접 로그인합니다. 토큰은 앱의 샌드박스 안에 보관되며 앱의 쿠키 보관함 밖에서는 사용할 수 없습니다. 따라서 앱이 Authenticator 또는 회사 포털과 통합되지 않은 경우 사용자는 애플리케이션 간에 SSO를 이용할 수 없습니다.

그러나 `WebView`에서는 로그인 UI의 모양과 느낌을 사용자 지정할 수 있는 기능을 제공합니다. 이 사용자 지정 방법에 대한 자세한 내용은 [Android WebView](https://developer.android.com/reference/android/webkit/WebView)를 참조하세요.

### <a name="default-browser-plus-custom-tabs"></a>기본 브라우저 및 사용자 지정 탭

기본적으로 MSAL은 브라우저와 [사용자 지정 탭](https://developer.chrome.com/multidevice/android/customtabs) 전략을 사용합니다. 사용자 정의 구성 파일에서 다음 JSON 구성을 사용하여 `DEFAULT`에 대한 향후 릴리스의 변경을 방지하기 위해 이 전략을 명시적으로 나타낼 수 있습니다.

```json
"authorization_user_agent" : "BROWSER"
```

이 방법을 사용하여 디바이스의 브라우저를 통해 SSO 경험을 제공합니다. MSAL은 다른 네이티브 앱 또는 웹앱이 MSAL에서 설정한 지속 세션 쿠키를 사용하여 디바이스에서 SSO를 구현할 수 있게 하는 공유 쿠키 보관함을 사용합니다.

### <a name="browser-selection-heuristic"></a>브라우저 선택 추론

다양한 Android 휴대폰에서 사용할 정확한 브라우저 패키지를 지정할 수 없으므로 MSAL은 최상의 디바이스 간 SSO 제공을 시도하는 브라우저 선택 추론을 구현합니다.

MSAL은 주로 패키지 관리자에서 기본 브라우저를 검색하고 테스트를 거친 안전한 브라우저 목록에 있는지 확인합니다. 없으면 MSAL은 안전 목록에서 기본이 아닌 다른 브라우저를 시작하지 않고 Webview를 사용합니다. 기본 브라우저는 사용자 지정 탭을 지원하는지 여부에 관계없이 선택됩니다. 브라우저에서 Custom Tabs를 지원하는 경우 MSAL은 Custom Tabs를 시작합니다. Custom Tabs는 앱 내 `WebView`와 모양 및 느낌이 비슷하며 기본 UI 사용자 지정을 허용합니다. 자세한 내용은 [Android의 Custom Tabs](https://developer.chrome.com/multidevice/android/customtabs)를 참조하세요.

디바이스에 브라우저 패키지가 없는 경우 MSAL은 앱 내 `WebView`를 사용합니다. 디바이스 기본 설정이 변경되지 않은 경우 SSO 경험 보장을 위해 로그인할 때마다 동일한 브라우저가 시작되어야 합니다.

#### <a name="tested-browsers"></a>테스트된 브라우저

다음 브라우저는 구성 파일에 지정된 `"redirect_uri"`로 올바르게 리디렉션되는지 확인하는 테스트를 거쳤습니다.

| 디바이스 | 기본 제공 브라우저 | Chrome | Opera  | Microsoft Edge | UC Browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4(API 17) | pass | pass |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |적용할 수 없음 |
| Samsung S7(API 25) | pass<sup>1</sup> | pass | pass | pass | fail |pass |
| Huawei(API 26) |pass<sup>2</sup> | pass | fail | pass | pass |pass |
| Vivo(API 26) |pass|pass|pass|pass|pass|fail|
| Pixel 2(API 26) |pass | pass | pass | pass | fail |pass |
| Oppo | pass | 적용할 수 없음<sup>3</sup>|적용할 수 없음  |적용할 수 없음 |적용할 수 없음 | 적용할 수 없음|
| OnePlus(API 25) |pass | pass | pass | pass | fail |pass |
| Nexus(API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

<sup>1</sup>Samsung의 기본 제공 브라우저는 Samsung 인터넷입니다.<br/>
<sup>2</sup>Huawei의 기본 제공 브라우저는 Huawei Browser입니다.<br/>
<sup>3</sup>Oppo 디바이스 설정 내에서 기본 브라우저를 변경할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Android 디바이스용 공유 디바이스 모드](msal-android-shared-devices.md)를 사용하면 여러 직원이 쉽게 공유할 수 있도록 Android 디바이스를 구성할 수 있습니다.