---
title: 웹 API를 호출하는 데스크톱 앱 등록 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기(앱 등록)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d196fa6eabd0a6f9fc08b8d1b1d60e649d02ee1b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234968"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>웹 API를 호출하는 데스크톱 앱: 앱 등록

이 문서에서는 데스크톱 애플리케이션에 대한 앱 등록 세부 사항을 설명합니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

데스크톱 애플리케이션에서 지원되는 계정 유형은 강화하려는 환경에 따라 다릅니다. 이 관계로 인해 지원되는 계정 유형은 사용하려는 흐름에 따라 다릅니다.

### <a name="audience-for-interactive-token-acquisition"></a>대화형 토큰 획득의 대상

데스크톱 애플리케이션에서 대화형 인증을 사용하는 경우 모든 [계정 유형](quickstart-register-app.md)에서 사용자 로그인을 수행할 수 있습니다.

### <a name="audience-for-desktop-app-silent-flows"></a>데스크톱 앱 자동 흐름의 대상

- Windows 통합 인증 또는 사용자 이름 및 암호를 사용하려면 애플리케이션이 사용자의 자체 테넌트에서 사용자 로그인을 수행해야 합니다(예를 들어, 사용자가 LOB(기간 업무) 개발자인 경우). 또는 Azure Active Directory 조직에서는 ISV 시나리오인 경우 애플리케이션이 사용자의 자체 테넌트에서 사용자 로그인을 수행해야 합니다. 이러한 인증 흐름은 Microsoft 개인 계정에 대해 지원되지 않습니다.
- B2C(Business to Commerce) 기관 및 정책을 통과하는 소셜 ID로 사용자 로그인을 수행하면 대화형 및 사용자 이름-암호 인증만 사용할 수 있습니다.

## <a name="redirect-uris"></a>리디렉션 URI

데스크톱 애플리케이션에서 사용할 리디렉션 URI는 사용하려는 흐름에 따라 다릅니다.

Azure Portal의 **앱 등록** 에서 앱에 대한 [플랫폼 설정을 구성](quickstart-register-app.md#add-a-redirect-uri)하여 앱에 대한 리디렉션 URI를 지정합니다.

- 대화형 인증을 사용하는 앱의 경우:

  - 포함된 브라우저를 사용하는 앱: `https://login.microsoftonline.com/common/oauth2/nativeclient`(참고: 앱이 일반적으로 주소 표시줄을 포함하지 않는 창을 팝업하는 경우 "포함된 브라우저"를 사용합니다.)
  - 시스템 브라우저를 사용하는 앱: `http://localhost`(참고: 앱이 Microsoft 로그인 포털을 방문하도록 시스템의 기본 브라우저(예: Edge, Chrome, Firefox 등)를 가져오는 경우 "시스템 브라우저"를 사용합니다.)
  
  > [!IMPORTANT]
  > 보안 모범 사례에 따라 `https://login.microsoftonline.com/common/oauth2/nativeclient` 또는 `http://localhost`을(를) 리디렉션 URI로 명시적으로 설정하는 것이 좋습니다. MSAL.NET와 같은 일부 인증 라이브러리는 다른 리디렉션 URI가 지정되지 않은 경우 기본값 `urn:ietf:wg:oauth:2.0:oob`을(를) 사용하고, 이는 권장되지 않습니다. 이 기본값은 다음 주요 릴리스의 호환성이 손상되는 변경으로 업데이트됩니다.

- macOS용 네이티브 Objective-C 또는 Swift 앱을 빌드하는 경우 애플리케이션의 번들 식별자를 기준으로 하는 리디렉션 URI를  `msauth.<your.app.bundle.id>://auth` 형식으로 등록합니다. `<your.app.bundle.id>`을(를) 애플리케이션의 번들 식별자로 바꿉니다.
- Node.js Electron 앱을 빌드하는 경우, 예를 들어 `msal://redirect`과(와) 같은 권한 부여 흐름의 리디렉션 단계를 처리하기 위해 일반 웹(https://) 리디렉션 URI 대신 사용자 지정 파일 프로토콜을 사용합니다. 사용자 지정 파일 프로토콜 이름은 명확하게 추측되어서는 안 되며 [네이티브 앱에 대한 OAuth2.0 사양](https://tools.ietf.org/html/rfc8252#section-7.1)의 제안 사항을 따라야 합니다.
- 앱에서 Windows 통합 인증 또는 사용자 이름 및 암호만 사용하는 경우 애플리케이션에 대한 리디렉션 URI를 등록할 필요가 없습니다. 이러한 흐름은 Microsoft Identity 플랫폼 v2.0 엔드포인트를 대상으로 왕복을 수행합니다. 애플리케이션은 특정 URI에서 다시 호출되지 않습니다.
- [디바이스 코드 흐름](scenario-desktop-acquire-token-device-code-flow.md), [Windows 통합 인증](scenario-desktop-acquire-token-integrated-windows-authentication.md), [사용자 이름 및 암호](scenario-desktop-acquire-token-username-password.md)와 [디먼 애플리케이션](scenario-daemon-overview.md)에서 사용되는 클라이언트 자격 증명 흐름을 사용하는 기밀 클라이언트 애플리케이션을 구분하려면(모두 리디렉션 URI가 필요하지 않음) 퍼블릭 클라이언트 애플리케이션으로 구성합니다. 이 구성을 설정하려면

    1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>의 **앱 등록** 에서 앱을 선택한 다음 **인증** 을 선택합니다.
    1. **고급 설정** > **퍼블릭 클라이언트 흐름 허용** > **다음 모바일 및 데스크톱 흐름 사용** 에서 **예** 를 선택합니다.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure Portal의 인증 창에서 퍼블릭 클라이언트 설정 사용":::

## <a name="api-permissions"></a>API 사용 권한

데스크톱 애플리케이션은 로그인한 사용자에 대한 API를 호출하고, 위임된 권한을 요청해야 합니다. 데스크톱 애플리케이션은 [디먼 애플리케이션](scenario-daemon-overview.md)에서만 처리되는 애플리케이션 권한을 요청할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 코드 구성](scenario-desktop-app-configuration.md)으로 이동합니다.
