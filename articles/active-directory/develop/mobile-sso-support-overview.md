---
title: 개발하는 모바일 앱의 Single Sign-On 및 앱 보호 정책 지원 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼을 사용하고 Azure Active Directory와 통합하여 Single Sign-On 및 앱 보호 정책을 지원하는 모바일 애플리케이션 빌드에 대한 설명 및 개요입니다.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.custom: has-adal-ref
ms.openlocfilehash: 43f66b7f44e7d30816da5ac59a9692807f257a2b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787266"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>개발하는 모바일 앱의 Single Sign-On 및 앱 보호 정책 지원

SSO(Single sign-on)는 Microsoft ID 플랫폼 및 Azure Active Directory의 핵심 제품으로 앱 사용자에게 쉽고 안전한 로그인을 제공합니다. 또한 APP(앱 보호 정책)을 사용하면 사용자의 데이터를 안전하게 유지하는 키 보안 정책을 지원할 수 있습니다. 이러한 기능을 함께 사용하면 안전한 사용자 로그인 및 앱 데이터 관리가 가능합니다.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

이 문서에서는 SSO 및 APP가 중요한 이유를 설명하고 이러한 기능을 지원하는 모바일 애플리케이션을 빌드하기 위한 개략적인 지침을 제공합니다. 이는 휴대폰 및 태블릿 앱 모두에 적용됩니다. 조직의 Azure Active Directory 테넌트에서 SSO를 배포하려는 IT 관리자는 [SSO(Single Sign-On) 배포 계획에 대한 지침](../manage-apps/plan-sso-deployment.md)을 확인하세요.

## <a name="about-single-sign-on-and-app-protection-policies"></a>Single Sign-On 및 앱 보호 정책 정보

[SSO(Single Sign-On)](../manage-apps/plan-sso-deployment.md)를 사용하면 사용자가 한 번 로그인한 후 자격 증명을 다시 입력하지 않고 다른 애플리케이션에 액세스할 수 있습니다. 이렇게 하면 앱에 더 쉽게 액세스할 수 있고 사용자가 긴 사용자 이름 및 암호 목록을 기억할 필요가 없습니다. 앱에서 구현하면 앱에 더 쉽게 액세스하고 사용할 수 있습니다.

또한 앱에서 Single Sign-On을 사용하도록 설정하면 [암호 없는 로그인](../authentication/concept-authentication-passwordless.md)과 같은 최신 인증과 함께 제공되는 새로운 인증 메커니즘의 잠금이 해제됩니다. 사용자 이름 및 암호는 애플리케이션에 대해 가장 널리 사용되는 공격 벡터 중 하나이며 SSO를 사용하도록 설정하면 보안을 강화하거나 보다 안전한 인증 메커니즘을 사용하는 조건부 액세스 또는 암호 없는 로그인을 적용하여 이 위험을 완화할 수 있습니다. 마지막으로 Single Sign-On을 사용하도록 설정하면 [Single Sign-Out](v2-protocols-oidc.md#single-sign-out)도 사용할 수 있습니다. 이는 공유 디바이스에서 사용되는 작업 애플리케이션과 같은 상황에서 유용합니다.

[앱 보호 정책(APP)](/mem/intune/apps/app-protection-policy)은 조직의 데이터가 안전하게 보호되도록 합니다. 이를 통해 회사는 앱 내에서 데이터를 관리하고 보호할 수 있으며 앱 및 해당 데이터에 액세스할 수 있는 사용자를 제어할 수 있습니다. 앱 보호 정책을 구현하면 앱이 조건부 액세스 정책에 의해 보호되는 리소스에 사용자를 연결하고 보호된 다른 앱 간에 데이터를 안전하게 전송할 수 있습니다. 앱 보호 정책에 의해 잠금 해제된 시나리오에는 앱을 열 때 PIN을 요구하고, 앱 간의 데이터 공유를 제어하고, 회사 앱 데이터가 개인 스토리지 위치에 저장되지 않도록 하는 작업이 포함됩니다.

## <a name="implementing-single-sign-on"></a>Single Sign-On 구현

앱이 Single Sign-On를 활용할 수 있도록 하려면 다음을 권장합니다.

### <a name="use-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리) 사용

애플리케이션에서 Single Sign-On을 구현하는 가장 좋은 방법은 [Microsoft 인증 라이브러리(MSAL)](msal-overview.md)를 사용하는 것입니다. MSAL을 사용하면 최소한의 코드와 API 호출로 앱에 인증을 추가하고 [Microsoft ID 플랫폼](./index.yml)의 전체 기능을 사용하며 Microsoft에서 보안 인증 솔루션의 유지 관리를 처리하도록 할 수 있습니다. 기본적으로 MSAL은 애플리케이션에 대한 SSO 지원을 추가합니다. 또한 앱 보호 정책을 구현하려는 경우에도 MSAL을 사용해야 합니다.

> [!NOTE]
> MSAL을 구성하여 포함된 웹 보기를 사용할 수 있습니다. 그러면 Single Sign-On이 작동되지 않습니다. 기본 동작(즉, 시스템 웹 브라우저)을 사용하여 SSO가 작동하는지 확인합니다.

현재 애플리케이션에서 [ADAL 라이브러리](../azuread-dev/active-directory-authentication-libraries.md)를 사용 중인 경우 [ADAL이 지원 중단](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)되므로 [MSAL로 마이그레이션](msal-migration.md)하는 것이 좋습니다.

iOS 애플리케이션의 경우 MSAL을 사용하여 로그인을 설정하는 방법을 보여 주는 [빠른 시작](quickstart-v2-ios.md)과 [다양한 SSO 시나리오에 대한 MSAL 구성 지침](single-sign-on-macos-ios.md)이 있습니다.

Android 애플리케이션의 경우 MSAL을 사용하여 로그인을 설정하는 방법을 보여 주는 [빠른 시작](quickstart-v2-android.md)과 [MSAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법](msal-android-single-sign-on.md)에 대한 지침이 있습니다.

### <a name="use-the-system-web-browser"></a>시스템 웹 브라우저 사용

대화형 인증에는 웹 브라우저가 필요합니다. MSAL 이외의 최신 인증 라이브러리(즉, 다른 Openid Connect 또는 SAML 라이브러리)를 사용하는 모바일 앱의 경우 또는 자체 인증 코드를 구현하는 경우 시스템 브라우저를 인증 화면으로 사용하여 SSO를 사용하도록 설정해야 합니다.

Google은 Android 애플리케이션에서 이 작업을 수행하기 위한 지침을 제공합니다. [Chrome 사용자 지정 탭 - Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple에는 iOS 애플리케이션에서 이 작업을 수행하기 위한 지침이 있습니다. [웹 서비스를 통해 사용자 인증 | Apple 개발자 설명서](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> [Apple 디바이스용 SSO 플러그 인](apple-sso-plugin.md)을 사용하면 Intune을 사용하여 관리 디바이스에서 포함된 웹 보기를 사용하는 iOS 앱용 SSO를 사용할 수 있습니다. 모든 사용자에 대해 SSO를 사용하도록 설정하는 앱을 개발하는 데 가장 적합한 옵션인 MSAL 및 시스템 브라우저를 사용하는 것이 좋지만, 그렇지 않은 일부 시나리오에서는 SSO를 사용할 수 있습니다.

## <a name="enable-app-protection-policies"></a>앱 보호 정책 사용

앱 보호 정책을 사용하도록 설정하려면 [Microsoft 인증 라이브러리(MSAL)](msal-overview.md)를 사용합니다. MSAL은 Microsoft ID 플랫폼의 인증 및 권한 부여 라이브러리이며 Intune SDK는 함께 작동하도록 개발되었습니다.

또한 인증을 위해 broker 앱을 사용해야 합니다. broker를 사용하려면 앱이 앱을 준수하도록 애플리케이션 및 디바이스 정보를 제공해야 합니다. iOS 사용자는 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)을 사용하고 Android 사용자는 [조정된 인증](./msal-android-single-sign-on.md)을 위해 Microsoft Authenticator 앱 또는 [회사 포털 앱](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)을 사용합니다. 기본적으로 MSAL은 인증 요청을 수행하기 위한 첫 번째 선택으로 broker를 사용하므로 MSAL을 사용할 때 인증에 broker를 사용하면 앱에 자동으로 사용하도록 설정됩니다.

마지막으로 앱에 [Intune SDK를 추가](/mem/intune/developer/app-sdk-get-started)하여 앱 보호 정책을 사용합니다. 대부분의 파트에 대한 SDK는 가로채기 모델을 따르며 앱 보호 정책을 자동으로 적용하여 앱에서 수행하는 작업이 허용되는지 여부를 확인합니다. 특정 작업에 대한 제한이 있는 경우 앱에 알리기 위해 수동으로 호출할 수 있는 API도 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory Single Sign-On 배포 계획](../manage-apps/plan-sso-deployment.md)
- [방법: macOS 및 iOS에서 SSO 구성](single-sign-on-macos-ios.md)
- [Apple 디바이스를 위한 Microsoft Enterprise SSO 플러그 인(미리 보기)](apple-sso-plugin.md)
- [Android의 조정된 인증](./msal-android-single-sign-on.md)
- [권한 부여 에이전트 및 사용 설정 방법](./msal-android-single-sign-on.md)
- [Microsoft Intune 앱 SDK 시작](/mem/intune/developer/app-sdk-get-started)
- [Intune 앱 SDK에 대한 설정 구성](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Microsoft Intune 보호 앱](/mem/intune/apps/apps-supported-intune-apps)