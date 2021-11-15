---
title: MSAL 인증 흐름 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)에서 사용하는 인증 흐름 및 권한 부여에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 67ac453bb70c6889e1a69e7c0c40a88f623813ff
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235120"
---
# <a name="authentication-flows"></a>인증 흐름

MSAL(Microsoft 인증 라이브러리)은 여러 애플리케이션 시나리오에서 사용할 수 있는 여러 인증 흐름을 지원합니다.

| 흐름 | Description | 사용 대상 |
|--|--|--|
| [인증 코드](#authorization-code) | 웹 API와 같은 보호된 리소스에 대한 액세스 권한을 얻기 위해 디바이스에 설치된 앱에서 사용됩니다. 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있도록 합니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md), [웹앱](scenario-web-app-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. | [디먼 앱](scenario-daemon-overview.md) |
| [디바이스 코드](#device-code) | 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력이 제한된 디바이스에 로그인할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token-device-code-flow.md) |
| [암시적 부여](#implicit-grant) | 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 토큰을 가져올 수 있습니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있도록 합니다. | [SPA(단일 페이지 애플리케이션)](scenario-spa-overview.md) |
| [On-behalf-of](#on-behalf-of) | 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [사용자 이름/암호](#usernamepassword) | 암호를 직접 처리하여 애플리케이션에 사용자가 로그인할 수 있도록 허용합니다. 이 흐름은 권장되지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token-username-password.md) |
| [Windows 통합 인증](#integrated-windows-authentication) | 도메인 또는 Azure AD(Azure Active Directory)에 연결된 컴퓨터의 애플리케이션에서 사용자의 UI 상호 작용 없이 자동으로 토큰을 획득할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token-integrated-windows-authentication.md) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법

클라이언트 애플리케이션을 구축하는 방법에 따라 Microsoft ID 플랫폼에서 지원되는 인증 흐름 중 하나 이상을 사용할 수 있습니다. 이러한 흐름은 여러 유형의 토큰 및 인증 코드를 생성하고 작동시키기 위해 다양한 토큰을 요청할 수 있습니다.

| 흐름                                                                               | 필수 사항            | id_token | 액세스 토큰 | 새로 고침 토큰 | 인증 코드 |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [인증 코드 흐름](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x(앱 전용) |               |                    |
| [디바이스 코드 흐름](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [암시적 흐름](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md)                                | 액세스 토큰        | x        | x            | x             |                    |
| [사용자 이름/암호](v2-oauth-ropc.md)(ROPC)                                       | 사용자 이름 및 암호 | x        | x            | x             |                    |
| [하이브리드 OIDC 흐름](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [새로 고침 토큰 상환](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | 새로 고침 토큰       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>대화형 및 비대화형 인증

이러한 흐름 중 몇 가지는 대화형 토큰 획득 및 비대화형 토큰 획득을 모두 지원합니다.

  - **대화형** 은 사용자에게 입력을 요청하는 메시지를 표시할 수 있음을 의미합니다. 예를 들어 사용자에게 로그인하라는 메시지를 표시하거나, MFA(다단계 인증)를 수행하거나, 리소스에 추가 동의를 부여합니다.
  - **비대화형** 또는 *자동* 인증은 로그인 서버에서 사용자에게 추가 정보를 *확인할 수 없는* 방식으로 토큰을 획득하려고 시도합니다.

MSAL 기반 애플리케이션은 먼저 토큰을 *자동* 으로 획득한 다음 비대화형 메서드가 실패하는 경우에만 대화형으로 시도해야 합니다. 이 패턴에 대한 자세한 내용은 [MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰 획득 및 캐싱](msal-acquire-cache-tokens.md)을 참조하세요.

## <a name="authorization-code"></a>인증 코드

[OAuth 2 인증 코드 권한 부여](v2-oauth2-auth-code-flow.md)는 디바이스에 설치된 앱에서 사용하여 웹 API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다.

사용자가 웹 애플리케이션(웹 사이트)에 로그인하면 웹 애플리케이션은 인증 코드를 수신합니다. 인증 코드는 웹 API를 호출하는 토큰을 얻는 데 사용됩니다.

![인증 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 액세스 토큰에 대해 사용되는 인증 코드를 요청합니다.
2. 액세스 토큰을 사용하여 웹 API를 호출합니다.

### <a name="considerations"></a>고려 사항

- 토큰을 사용하는 데 인증 코드는 한 번만 사용할 수 있습니다. 프로토콜 표준 사양에서 명시적으로 금지하므로 동일한 인증 코드를 사용하여 토큰을 여러 번 얻으려고 시도하지 마세요. 코드를 의도적으로 여러 번 사용하는 경우 또는 프레임워크에서도 여러 번 사용하는 것을 인식하지 못하기 때문에 다음과 같은 오류가 발생합니다.

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>클라이언트 자격 증명

[OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)을 사용하면 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 애플리케이션은 종종 디먼 또는 서비스 계정이라고 합니다.

클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스(기밀 클라이언트)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 기밀 클라이언트 흐름은 공용 클라이언트 애플리케이션만 지원하기 때문에 UWP, Xamarin.iOS 및 Xamarin.Android 등 모바일 플랫폼에서 사용할 수 없습니다. 공용 클라이언트 애플리케이션은 ID 공급자에게 애플리케이션 ID를 증명하는 방법을 모릅니다. 웹앱 또는 웹 API 백 엔드에서 인증서를 배포하여 보안 연결을 수행할 수 있습니다.

### <a name="application-secrets"></a>애플리케이션 비밀

![암호를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 애플리케이션 비밀 또는 암호 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="certificates"></a>인증서

![인증서를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 인증서 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

이러한 클라이언트 자격 증명은 다음과 같아야 합니다.

- Azure AD에 등록했습니다.
- 코드에서 기밀 클라이언트 애플리케이션 개체를 생성할 때 전달됩니다.

## <a name="device-code"></a>디바이스 코드

[OAuth 2 디바이스 코드 흐름](v2-oauth2-device-code.md)을 사용하면 사용자가 스마트 TV, IoT 디바이스 및 프린터와 같은 입력 제한 디바이스에 로그인할 수 있습니다. Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요합니다. 디바이스 또는 운영 체제에서 웹 브라우저를 제공하지 않는 경우 사용자는 디바이스 코드 흐름을 통해 컴퓨터 또는 휴대폰 같은 다른 디바이스를 사용하여 대화형으로 로그인할 수 있습니다.

애플리케이션은 디바이스 코드 흐름을 사용하여 이러한 디바이스 및 운영 체제용으로 설계된 2단계 프로세스를 통해 토큰을 가져옵니다. 이러한 애플리케이션의 예로는 IoT 디바이스 또는 CLI(명령줄 인터페이스) 도구에서 실행되는 애플리케이션이 있습니다.

![디바이스 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램에서:

1. 사용자 인증이 필요할 때마다 앱은 코드를 제공하고, 다른 디바이스(예: 인터넷에 연결된 스마트폰)를 사용하여 URL(예: `https://microsoft.com/devicelogin`)로 이동하도록 사용자에게 요청합니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시되고, 필요한 경우 동의 확인 프롬프트 및 [다단계 인증](../authentication/concept-mfa-howitworks.md)을 비롯한 일반적인 인증 환경을 진행합니다.
1. 인증에 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="constraints"></a>제약 조건

- 디바이스 코드 흐름은 공용 클라이언트 애플리케이션에서만 사용할 수 있습니다.
- 공용 클라이언트 애플리케이션을 구성할 때 전달되는 인증 기관은 다음 중 하나여야 합니다.
  - 테넌트(`https://login.microsoftonline.com/{tenant}/,` 형식, 여기서 `{tenant}`는 테넌트 ID 또는 테넌트와 연결된 도메인 이름을 나타내는 GUID)
  - 회사 및 학교 계정(`https://login.microsoftonline.com/organizations/` 형식).

## <a name="implicit-grant"></a>암시적 부여

[OAuth 2 암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하면 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 Microsoft ID 플랫폼에서 토큰을 가져올 수 있습니다. 이 흐름을 통해 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다.

![암시적 허용 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 애플리케이션은 JavaScript 또는 SPA 프레임워크(예: Angular, Vue.js, React.js 등)로 작성된 클라이언트 쪽의 SPA(단일 페이지 애플리케이션)로 빌드됩니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다. Microsoft ID 플랫폼을 사용하면 암시적 권한 부여 흐름을 사용하여 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다. 암시적 흐름을 사용하면 애플리케이션에서 ID 토큰을 가져와 인증된 사용자를 나타낼 수 있으며, 보호된 API를 호출하는 데 필요한 토큰에도 액세스할 수 있습니다.

이 인증 흐름에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요하기 때문에 Electron 또는 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다.

암시적 흐름 모드로 발급된 토큰은 URL을 통해 브라우저로 반환되었기 때문에 **길이 제한** 이 있습니다(`response_mode`가 `query` 또는 `fragment`인 경우). 어떤 브라우저는 브라우저 표시줄에 입력하는 URL 길이에 제한을 두며 너무 긴 경우 실패합니다. 따라서 이러한 암시적 흐름 토큰은 `groups` 또는 `wids` 클레임을 포함하지 않습니다.

## <a name="on-behalf-of"></a>On-behalf-of

[OAuth 2 On-Behalf-Of 인증 흐름](v2-oauth2-on-behalf-of-flow.md)은 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 하는 경우에 사용됩니다. 이 개념은 요청 체인을 통해 위임된 사용자 ID 및 권한을 전파하는 것입니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 *대신하여* Microsoft ID 플랫폼에서 액세스 토큰을 보호해야 합니다.

![On-behalf-of 흐름 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램에서:

1. 애플리케이션은 웹 API에 대한 액세스 토큰을 획득합니다.
2. 클라이언트(웹, 데스크톱, 모바일 또는 단일 페이지 애플리케이션)는 HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 추가하여 보호된 웹 API를 호출합니다. 웹 API는 사용자를 인증합니다.
3. 클라이언트에서 웹 API를 호출하면 웹 API는 사용자를 대신해 다른 토큰을 요청합니다.
4. 보호된 웹 API는 이 토큰을 사용하여 사용자 대신 다운스트림 웹 API를 호출합니다. 웹 API는 나중에 다른 다운스트림 API에 대한 토큰을 요청할 수도 있습니다(계속해서 같은 사용자를 대신함).

## <a name="usernamepassword"></a>사용자 이름/암호

[OAuth 2 ROPC(리소스 소유자 암호 자격 증명)](v2-oauth-ropc.md) 부여를 사용하여 애플리케이션에서 암호를 직접 처리하여 사용자를 로그인할 수 있습니다. 데스크톱 애플리케이션에서 사용자 이름/암호 흐름을 사용하여 토큰을 자동으로 가져올 수 있습니다. 애플리케이션을 사용하는 경우 UI가 필요하지 않습니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 사용자 이름 및 암호를 ID 공급자에게 보내 토큰을 가져옵니다.
2. 토큰을 사용하여 웹 API를 호출합니다.

> [!WARNING]
> 이 흐름은 권장되지 않습니다. 높은 수준의 신뢰 및 자격 증명 노출이 필요합니다. 보다 안전한 다른 흐름을 사용할 수 *없는 경우에만* 이 흐름을 사용해야 합니다. 자세한 내용은 [암호 문제 증가에 대한 해결책](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)을 참조하세요.

Windows 도메인 조인 머신에서 자동으로 토큰을 획득하는 기본 흐름은 [Windows 통합 인증](#integrated-windows-authentication)입니다. 다른 경우에는 [디바이스 코드 흐름](#device-code)을 사용합니다.

DevOps와 같은 일부 시나리오에서는 사용자 이름/암호 흐름이 유용할 수 있지만 사용자 고유의 UI를 제공하는 대화형 시나리오에서 사용자 이름/암호를 사용하려는 경우에는 이 방법을 사용하지 않습니다.

사용자 이름/암호를 사용하여 다음을 수행할 수 없습니다.

- 다단계 인증을 수행해야 하는 사용자는 상호 작용이 없으므로 로그인할 수 없습니다.
- 사용자는 Single Sign-On을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

[Windows 통합 인증 제약 조건](#integrated-windows-authentication) 외에도 다음과 같은 제약 조건이 적용됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행되는 경우에는 이 흐름을 사용할 수 없습니다. 이 흐름을 사용할 수 없습니다.
- ROPC는 회사 및 학교 계정에서만 적용됩니다. MSA(Microsoft 계정)에는 ROPC를 사용할 수 없습니다.
- 이 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 유니버설 Windows 플랫폼에서는 사용할 수 없습니다.
- Azure AD B2C에서 ROPC 흐름은 로컬 계정에 대해서만 작동합니다. MSAL.NET 및 Azure AD B2C의 ROPC에 대한 자세한 내용은 [Azure AD B2C에서 ROPC 사용](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)을 참조하세요.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

MSAL은 도메인에 가입되거나 Azure AD에 가입된 Windows 컴퓨터에서 실행되는 데스크톱 및 모바일 애플리케이션에 대해 IWA(Windows 통합 인증)를 지원합니다. 이러한 애플리케이션은 IWA를 사용하여 사용자의 UI 상호 작용 없이도 토큰을 자동으로 획득할 수 있습니다.

![Windows 통합 인증 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. Windows 통합 인증을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="constraints"></a>제약 조건

IWA(Windows 통합 인증)는 페더레이션 사용자(즉, Active Directory에서 만들어지고 Azure AD에서 지원하는 사용자)*만* 지원합니다. Active Directory 지원(관리되는 사용자) 없이 Azure AD에서 직접 만든 사용자는 이 인증 흐름을 사용할 수 없습니다. 이러한 제한은 [사용자 이름/암호 흐름](#usernamepassword)에 영향을 주지 않습니다.

IWA는 .NET Framework, .NET Core 및 유니버설 Windows 플랫폼 애플리케이션을 위한 것입니다.

IWA는 다단계 인증을 바이패스하지 않습니다. 다단계 인증이 구성된 경우 다단계 인증 질문이 필요하면 IWA가 실패할 수 있습니다. 다단계 인증을 사용하려면 사용자 조작이 필요합니다.

사용자는 ID 공급자가 언제 2단계 인증을 수행하도록 요청하는지 제어하지 않습니다. 테넌트 관리자가 제어합니다. 일반적으로 다른 국가/지역에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되어 있지 않은 경우 2단계 인증이 필요하며 VPN을 통해 연결된 경우에도 간혹 필요한 경우가 있습니다. Azure AD는 AI를 사용하여 2단계 인증이 필요한 경우를 계속 학습합니다. IWA가 실패하면 [대화형 사용자 프롬프트](#interactive-and-non-interactive-authentication)로 대체해야 합니다.

공용 클라이언트 애플리케이션을 구성할 때 전달되는 인증 기관은 다음 중 하나여야 합니다.

- 테넌트(`https://login.microsoftonline.com/{tenant}/,` 형식, 여기서 `{tenant}`는 테넌트 ID 또는 테넌트와 연결된 도메인 이름을 나타내는 GUID)
- 회사 및 학교 계정(`https://login.microsoftonline.com/organizations/`) MSA(Microsoft 개인 계정)는 지원되지 않습니다(`/common` 또는 `/consumers` 테넌트를 사용할 수 없음).

IWA는 자동 흐름이므로 다음 중 하나를 충족해야 합니다.

- 애플리케이션의 사용자가 사전에 애플리케이션 사용에 동의해야 합니다.
- 테넌트 관리자는 테넌트의 모든 사용자에게 사전에 애플리케이션 사용 동의를 받아야 합니다.

이는 다음 중 하나에 해당함을 의미합니다.

- 개발자가 Azure Portal에서 자신에 대해 **권한 부여** 를 선택했습니다.
- 테넌트 관리자가 Azure Portal에 있는 앱 등록의 **API 권한** 탭에서 **{테넌트 도메인}에 대한 관리자 동의 권한 부여/해지** 를 선택했습니다([웹 API 액세스 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api) 참조).
- 사용자가 애플리케이션에 동의하는 방법을 제공했습니다([개별 사용자 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent) 참조).
- 테넌트 관리자가 애플리케이션에 동의하는 방법을 제공했습니다([관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) 참조).

IWA 흐름이 .NET 데스크톱, .NET Core 및 Windows 유니버설 플랫폼 앱에 대해 사용하도록 설정됩니다.

동의에 대한 자세한 내용은 [v2.0 권한 및 동의](v2-permissions-and-consent.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 MSAL(Microsoft 인증 라이브러리)에서 지원되는 인증 흐름을 검토했으므로 이러한 흐름에서 사용되는 토큰을 획득하고 캐싱하는 방법에 대해 알아봅니다.

[MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰 획득 및 캐싱](msal-acquire-cache-tokens.md)
