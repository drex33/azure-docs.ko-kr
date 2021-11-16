---
title: 웹 API를 호출하는 모바일 앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법 알아보기(앱 등록)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/18/2021
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: dba6dcf0dded3db4c1cf1ddc26071e9803b55cd6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232920"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>웹 API를 호출하는 모바일 앱 등록

이 문서에는 만들고 있는 모바일 애플리케이션을 등록하는 데 도움이 되는 명령이 포함되어 있습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

모바일 애플리케이션에서 지원하는 계정 형식은 사용하려는 환경 및 사용하려는 흐름에 따라 달라집니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득의 대상

대부분의 모바일 애플리케이션은 대화형 인증을 사용합니다. 앱이 이 형식의 인증을 사용하는 경우 모든 [계정 유형](quickstart-register-app.md)에서 사용자에게 로그인할 수 있습니다.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Windows 통합 인증, 사용자 이름-암호 및 B2C에 대한 대상

UWP(유니버설 Windows 플랫폼) 앱이 있는 경우 IWA(Windows 통합 인증)를 사용하여 사용자를 로그인시킬 수 있습니다. IWA 또는 사용자 이름-암호 인증을 사용하려면 애플리케이션이 LOB(기간 업무) 개발자 테넌트에 사용자를 로그인시켜야 합니다. 독립 소프트웨어 공급 업체(ISV) 시나리오에서 애플리케이션은 Azure Active Directory 조직에서 사용자에게 로그인할 수 있습니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대해 지원되지 않습니다.

B2C 인증 및 정책을 통과하는 소셜 ID를 사용하여 사용자가 로그인할 수도 있습니다. 이 방법을 사용하려면 대화형 인증 및 사용자 이름-암호 인증만 사용할 수 있습니다. 사용자 이름-암호 인증은 현재 Xamarin.iOS, Xamarin.Android 및 UWP에서만 지원됩니다.

자세한 내용은 [시나리오 및 지원되는 인증 흐름](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) 및 [시나리오, 지원되는 플랫폼 및 언어](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)를 참조하세요.

## <a name="platform-configuration-and-redirect-uris"></a>플랫폼 구성 및 리디렉션 URI

### <a name="interactive-authentication"></a>대화형 인증

대화형 인증을 사용하는 모바일 앱을 빌드할 때 가장 중요한 등록 단계는 리디렉션 URI입니다. [**인증** 블레이드에서 플랫폼 구성](https://aka.ms/MobileAppReg)을 통해 대화형 인증을 설정할 수 있습니다.

이러한 환경을 통해 앱은 Microsoft Authenticator (및 Android의 Intune 회사 포털)를 통해 Single Sign-On (SSO)를 가져올 수 있습니다. 또한 장치 관리 정책도 지원합니다.

앱 등록 포털은 iOS 및 Android 애플리케이션에 대한 조정된 회신 URI를 계산하는 데 도움이 되는 프리뷰 환경을 제공합니다.

1. 앱 등록 포털에서 **인증** > **을 선택하여 새 환경을 사용해보세요**.

   ![새 환경을 선택하는 인증 블레이드](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **플랫폼 추가** 를 선택합니다.

   ![플랫폼 추가](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. 플랫폼 목록이 지원되는 경우 **iOS** 를 선택합니다.

   ![모바일 애플리케이션 선택](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 번들 ID를 입력하고 **등록** 을 선택합니다.

   ![번들 ID 입력](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

단계를 완료하면 다음 이미지와 같이 리디렉션 URI가 사용자에 대해 계산됩니다.

![결과 리디렉션 URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

리디렉션 URI를 수동으로 구성하려는 경우 애플리케이션 매니페스트를 통해 수행할 수 있습니다. 다음은 매니페스트에 대해 권장되는 형식입니다.

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - 예를 들어 다음과 같이 입력합니다. `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - KeyTool 명령을 통해 릴리스 키 또는 디버그 키를 사용하여 Android 서명 해시를 생성할 수 있습니다.

### <a name="username-password-authentication"></a>사용자 이름 - 암호 인증

앱에서 사용자 이름-암호 인증만 사용하는 경우 애플리케이션에 대한 리디렉션 URI를 등록할 필요가 없습니다. 이 흐름은 Microsoft ID 플랫폼에 대해 왕복을 수행합니다. 애플리케이션은 특정 URI에서 다시 호출되지 않습니다.

그러나 애플리케이션을 공용 클라이언트 애플리케이션으로 식별합니다. 이를 수행하려면:

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>의 **앱 등록** 에서 앱을 선택한 다음 **인증** 을 선택합니다.
1. **고급 설정** > **공용 클라이언트 흐름 허용** > **다음 모바일 및 데스크톱 흐름 사용** 에서 **예** 를 선택합니다.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure Portal의 인증 창에서 퍼블릭 클라이언트 설정 사용":::

## <a name="api-permissions"></a>API 사용 권한

모바일 애플리케이션은 로그인한 사용자에 대한 API를 호출합니다. 앱에서 위임된 권한을 요청해야 합니다. 이러한 권한을 범위라고도 합니다. 원하는 환경에 따라 Azure Portal을 통해 위임된 사용 권한을 정적으로 요청할 수 있습니다. 또는 런타임에 동적으로 요청할 수 있습니다.

권한을 정적으로 등록하면 관리자가 앱을 쉽게 승인할 수 있습니다. 정적 등록을 권장합니다.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 코드 구성](scenario-mobile-app-configuration.md)으로 이동합니다.
