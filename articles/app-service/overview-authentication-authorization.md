---
title: 인증 및 권한 부여
description: Azure App Service 및 Azure Functions에서 기본 제공되는 인증 및 권한 부여 지원과, 무단 액세스로부터 앱을 보호하는 방법을 알아봅니다.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/21/2021
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1cd95e97b38d0cebd4ae9fab7de3d32ec34154a4
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956826"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure App Service 및 Azure Functions의 인증 및 권한 부여

Azure App Service는 내장된 인증 및 권한 부여 기능(“간편한 인증”이라고 하기도 함)을 제공하므로 웹앱, RESTful API, 모바일 백 엔드에 코드를 최소한으로 작성하거나 코드를 작성하지 않고 사용자를 로그인시켜 데이터에 액세스할 수 있으며 [Azure Functions](../azure-functions/functions-overview.md)도 사용할 수 있습니다. 이 문서는 App Service가 앱의 인증 및 권한 부여를 단순화하는 방법에 대해 설명합니다.

## <a name="why-use-the-built-in-authentication"></a>기본 제공 인증을 사용하는 이유

인증 및 권한 부여를 위해 이 기능을 사용할 필요가 없습니다. 선택한 웹 프레임워크에서 번들로 제공되는 보안 기능을 사용하거나 사용자 고유의 유틸리티를 작성할 수 있습니다. 그러나 최신 보안, 프로토콜, 브라우저 업데이트를 사용하여 솔루션이 최신 상태를 유지하는지 확인해야 합니다.

인증(로그인 사용자) 및 권한 부여(보안 데이터에 대한 액세스 제공)용 보안 솔루션을 구현하려면 상당한 노력이 필요할 수 있습니다. 업계 모범 사례와 표준을 따르고 구현을 최신 상태로 유지해야 합니다. App Service 및 Azure Functions을 위한 기본 제공 인증 기능은 페더레이션 ID 공급자를 통해 기본 인증을 제공하여 시간과 노력을 절감할 수 있으므로 애플리케이션의 나머지 부분에 집중할 수 있습니다.

- Azure App Service를 사용하여 다양한 인증 기능을 직접 구현하지 않고도 웹앱 또는 API에 통합할 수 있습니다.
- 플랫폼에 직접 빌드되고 특정 언어, SDK, 보안 기술 또는 코드를 사용할 필요가 없습니다.
- 여러 로그인 공급자와 통합할 수 있습니다. 예: Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>ID 공급자

App Service는 [페더레이션 ID](https://en.wikipedia.org/wiki/Federated_identity)를 사용하며 여기서 타사 ID 공급자는 사용자 ID와 인증 흐름을 관리합니다. 기본적으로 다음 5가지 ID 공급자를 사용할 수 있습니다.

| 공급자 | 로그인 엔드포인트 | 방법 가이드 |
| - | - | - |
| [Microsoft ID 플랫폼](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [App Service Microsoft ID 플랫폼 로그인](configure-authentication-provider-aad.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [App Service Facebook 로그인](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [App Service Google 로그인](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service Twitter 로그인](configure-authentication-provider-twitter.md) |
| [OpenID 커넥트](https://openid.net/connect/) 공급자 | `/.auth/login/<providerName>` | [App Service OpenID Connect 로그인](configure-authentication-provider-openid-connect.md) |

이러한 공급자중 하나를 사용하여 인증 및 권한 부여를 활성화하면 사용자 인증과 공급자의 인증 토큰 유효성 검사에 로그인 엔드포인트를 사용할 수 있습니다. 사용자에게 여러 로그인 옵션을 제공할 수 있습니다.

## <a name="considerations-for-using-built-in-authentication"></a>기본 제공 인증 사용 관련 고려 사항

이 기능을 사용하도록 설정하면 HTTPS를 적용하는 App Service 구성 설정에 관계없이 애플리케이션 관련 요청이 모두 HTTPS로 자동 리디렉션됩니다. V2 구성의  `requireHttps` 설정으로 해당 기능을 사용하지 않게 설정할 수 있습니다. 그러나 HTTPS를 사용하는 것이 좋지만 보안되지 않은 HTTP 연결을 통해 전송되는 보안 토큰이 없어야 합니다.

사이트 콘텐츠 및 API에 대한 액세스를 제한하거나 제한하지 않고 인증을 위해 App Service를 사용할 수 있습니다. 앱 액세스를 인증된 사용자로만 제한하려면 **요청이 인증되지 않은 경우 수행할 작업** 을 구성된 ID 공급자 중 하나로 로그인하도록 설정합니다. 액세스를 인증하지만 제한하지 않으려면 **요청이 인증되지 않은 경우 수행할 작업** 을 "익명 요청 허용(작업 없음)"으로 설정합니다.

> [!NOTE]
> 각 앱 등록에 고유한 권한 및 동의를 제공해야 합니다. 환경 간에 권한을 공유하는 일이 없도록 배포 슬롯마다 별도의 앱 등록을 사용합니다. 이렇게 하면 새 코드를 테스트할 때 문제가 프로덕션 앱에 영향을 주는 것을 방지할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

[기능 아키텍처](#feature-architecture)

[인증 흐름](#authentication-flow)

[권한 부여 동작](#authorization-behavior)

[토큰 저장소](#token-store)

[로깅 및 추적](#logging-and-tracing)

### <a name="feature-architecture"></a>기능 아키텍처

인증 및 권한 부여 미들웨어 구성 요소는 애플리케이션과 동일한 VM에서 실행되는 플랫폼의 기능입니다. 이 기능이 활성화되면 애플리케이션에서 처리되기 전에 들어오는 모든 HTTP 요청이 여기를 통과합니다.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="배포된 사이트를 향한 트래픽을 허용하기 전에 ID 공급자와 상호 작용하는 사이트 샌드박스에서 프로세스로 가로채는 요청을 보여 주는 아키텍처 다이어그램" lightbox="media/app-service-authentication-overview/architecture.png":::

플랫폼 미들웨어는 다음과 같은 앱의 여러 작업을 처리합니다.

- 지정된 ID 공급자를 사용하여 사용자와 클라이언트를 인증합니다.
- 구성된 ID 공급자가 발급한 OAuth 토큰의 유효성을 검사하고, 저장하고, 새로 고칩니다.
- 인증된 세션 관리
- HTTP 요청 헤더에 ID 정보 삽입

모듈은 애플리케이션 코드와 별도로 실행되며 Azure Resource Manager 설정 또는 [구성 파일](configure-authentication-file-based.md)을 사용하여 구성할 수 있습니다. SDK, 특정 프로그래밍 언어 또는 애플리케이션 코드의 변경이 필요하지 않습니다. 

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Windows의 기능 아키텍처(컨테이너가 아닌 배포)

인증 및 권한 부여 모듈은 애플리케이션 코드와 동일한 샌드박스에서 네이티브 [IIS 모듈 ](/iis/get-started/introduction-to-iis/iis-modules-overview)로 실행됩니다. 이 기능이 활성화되면 애플리케이션에서 처리되기 전에 들어오는 모든 HTTP 요청이 여기를 통과합니다.

#### <a name="feature-architecture-on-linux-and-containers"></a>Linux 및 컨테이너의 기능 아키텍처

인증 및 권한 부여 모듈은 애플리케이션 코드와 분리된 별도의 컨테이너에서 실행됩니다. [특사 패턴](/azure/architecture/patterns/ambassador)으로 알려진 기능을 사용하면 들어오는 트래픽과 상호 작용하여 Windows에서와 비슷한 기능을 수행할 수 있습니다. In Process로 실행되지 않으므로 특정 언어 프레임워크와의 직접 통합이 가능하지 않습니다. 그러나 앱에 필요한 관련 정보는 아래에 설명된 대로 요청 헤더를 사용하여 전달됩니다.

### <a name="authentication-flow"></a>인증 흐름

인증 흐름은 모든 공급자에 대해 동일하지만 공급자의 SDK로 로그인하는지 여부에 따라 다릅니다.

- 공급자 SDK가 없는 경우: 애플리케이션은 페드레이션 로그인을 App Service에 위임합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 있는 브라우저 앱을 사용하는 경우입니다. 서버 코드는 로그인 프로세스를 관리하므로 _서버 방향 흐름_ 또는 _서버 흐름_ 이라고도 합니다. 이 경우는 브라우저 앱에 적용됩니다. 또한 SDK가 App Service 인증을 사용하여 사용자를 로그인시키기 위해 웹 보기를 열기 때문에 Mobile Apps 클라이언트 SDK를 사용하여 사용자를 로그인시키는 네이티브 앱에도 적용됩니다.
- 공급자 SDK 사용: 애플리케이션은 사용자를 수동으로 공급자에 로그인시킨 다음, 유효성 검사를 위해 인증 토큰을 App Service에 제출합니다. 이는 일반적으로 공급자의 로그인 페이지를 사용자에게 제공할 수 없는 브라우저리스 앱을 사용하는 경우입니다. 애플리케이션 코드는 로그인 프로세스를 관리하므로 _클라이언트 방향 흐름_ 또는 _클라이언트 흐름_ 이라고도 합니다. 이러한 경우는 REST API, [Azure Functions](../azure-functions/functions-overview.md) 및 JavaScript 브라우저 클라이언트뿐만 아니라 로그인 프로세스에서 더 많은 유연성이 필요한 브라우저 앱에도 적용됩니다. 공급자의 SDK를 사용하여 사용자를 로그인시키는 네이티브 모바일 앱에도 적용됩니다.

App Service의 신뢰할 수 있는 브라우저 앱에서 App Service의 또 다른 REST API 또는 [Azure Functions](../azure-functions/functions-overview.md)를 호출하면 서버 방향 흐름을 사용하여 인증할 수 있습니다. 자세한 내용은 [로그인 및 로그아웃 사용자 지정](configure-authentication-customize-sign-in-out.md)을 참조하세요.

아래 표는 인증 흐름 단계를 보여줍니다.

| 단계 | SDK 공급자가 없는 경우 | SDK 공급자가 있는 경우 |
| - | - | - |
| 1. 사용자 로그인 | 클라이언트를 `/.auth/login/<provider>`로 리디렉션합니다. | 클라이언트 코드는 공급자의 SDK를 사용하여 사용자를 직접 로그인시키고 인증 토큰을 받습니다. 자세한 내용은 공급자 설명서를 참조하세요. |
| 2. 사후 인증 | 공급자가 클라이언트를 `/.auth/login/<provider>/callback`으로 리디렉션합니다. | 클라이언트 코드는 유효성 검사를 위해 `/.auth/login/<provider>`에 [공급자의 토큰을 게시](configure-authentication-customize-sign-in-out.md#client-directed-sign-in)합니다. |
| 3. 인증된 세션 설정 | App Service는 인증된 쿠키를 응답에 추가합니다. | App Service는 자체 인증 토큰을 클라이언트 코드로 반환합니다. |
| 4. 인증된 콘텐츠 제공 | 클라이언트는 후속 요청에 인증 쿠키를 포함합니다(브라우저에 의해 자동 처리됨). | 클라이언트 코드는 `X-ZUMO-AUTH` 헤더에 인증 토큰을 제공합니다(Mobile Apps 클라이언트 SDK에 의해 자동 처리됨). |

클라이언트 브라우저의 경우 App Service는 인증되지 않은 모든 사용자를 자동으로 `/.auth/login/<provider>`로 보냅니다. 사용자 자신이 선택한 제공자를 사용하여 앱에 로그인할 수 있도록 하나 이상의 `/.auth/login/<provider>` 링크를 사용자에게 할 수도 있습니다.

<a name="authorization"></a>

### <a name="authorization-behavior"></a>권한 부여 동작

[Azure Portal](https://portal.azure.com)에서 들어오는 요청이 인증되지 않았을 때 여러 동작을 통해 App Service 인증을 구성할 수 있습니다. 다음 제목은 옵션을 설명합니다.

**인증되지 않은 요청 허용**

이 옵션은 애플리케이션 코드에 관한 인증되지 않은 트래픽의 권한 부여를 연기합니다. 인증된 요청의 경우 App Service는 HTTP 헤더의 인증 정보도 전달합니다.

이 옵션은 익명 요청을 보다 유연하게 처리할 수 있습니다. 예를 들어 [여러 로그인 공급자](configure-authentication-customize-sign-in-out.md#use-multiple-sign-in-providers)를 사용자에게 제공할 수 있습니다. 그러나 코드를 작성해야 합니다.

**인증 필요**

이 옵션은 애플리케이션에 관한 인증되지 않은 트래픽을 거부합니다. 이 거부는 구성된 ID 공급자 중 하나에 관한 리디렉션 동작일 수 있습니다. 이 경우 브라우저 클라이언트는 선택한 공급자를 위해 `/.auth/login/<provider>`로 리디렉션됩니다. 익명의 요청이 네이티브 모바일 앱에서 오는 경우 반환된 응답은 `HTTP 401 Unauthorized`입니다. 모든 요청에 관해 `HTTP 401 Unauthorized` 또는 `HTTP 403 Forbidden`으로 거부를 구성할 수도 있습니다.

이 옵션을 사용하면 앱에서 인증 코드를 작성할 필요가 없습니다. 역할별 권한 부여와 같이 보다 정교한 권한 부여는 사용자의 클레임을 검사하여 처리할 수 있습니다([사용자 클레임 액세스](configure-authentication-user-identities.md) 참조).

> [!CAUTION]
> 이러한 방식으로 액세스를 제한하면 모든 앱 호출에 제한이 적용되며, 여러 단일 페이지 애플리케이션이 그렇듯이 공개적으로 사용 가능한 홈페이지를 계획하는 앱에는 이 방법이 바람직하지 않을 수 있습니다.

> [!NOTE]
> 기본적으로 Azure AD 테넌트의 모든 사용자는 Azure AD에서 애플리케이션에 관한 토큰을 요청할 수 있습니다. 앱에 대한 액세스를 정의된 사용자 집합으로 제한하려는 경우 [Azure AD에서 애플리케이션을 구성할](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) 수 있습니다.

### <a name="token-store"></a>토큰 저장소

App Service는 웹앱, API 또는 기본 모바일 앱의 사용자와 연결된 토큰 리포지토리인 내장 토큰 저장소를 제공합니다. 공급자와 인증을 사용하도록 설정하면 이 토큰 저장소를 앱에서 즉시 사용할 수 있습니다. 다음과 같이 애플리케이션 코드가 사용자를 대신하여 이러한 공급자의 데이터에 액세스해야 하는 경우,

- 인증된 사용자의 Facebook 타임라인에 게시
- Microsoft Graph API를 사용하여 사용자의 회사 데이터 읽기

일반적으로 애플리케이션에서 이러한 토큰을 수집, 저장 및 새로 고치는 코드를 작성해야 합니다. 토큰 저장소를 사용하면 토큰이 필요할 때 [토큰을 가져오고](configure-authentication-oauth-tokens.md#retrieve-tokens-in-app-code) 토큰이 무효화되면 [App Service에 알려 이를 새로 고치도록](configure-authentication-oauth-tokens.md#refresh-auth-tokens) 해야 합니다. 

인증된 세션에 캐시된 ID 토큰, 액세스 토큰, 새로 고침 토큰은 연결된 사용자만 액세스할 수 있습니다.  

앱에서 토큰을 사용할 필요가 없는 경우 앱의 **인증/권한 부여** 페이지에서 토큰 스토리지를 사용하지 않도록 설정할 수 있습니다.

### <a name="logging-and-tracing"></a>로깅 및 추적

[애플리케이션 로깅을 사용하도록 설정](troubleshoot-diagnostic-logs.md)하면 로그 파일에서 인증 및 권한 추적을 바로 볼 수 있습니다. 예상치 못한 인증 오류가 표시되면 기존 애플리케이션 로그를 보고 모든 세부 정보를 편리하게 찾을 수 있습니다. [실패한 요청 추적](troubleshoot-diagnostic-logs.md)을 사용하면 실패한 요청에서 인증 및 권한 부여 모듈이 수행한 역할을 정확히 볼 수 있습니다. 추적 로그에서 `EasyAuthModule_32/64`라는 모듈에 대한 참조를 찾습니다.

## <a name="more-resources"></a>추가 리소스

- [사용 방법: Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성](configure-authentication-provider-aad.md)
- [로그인 및 로그아웃 사용자 지정](configure-authentication-customize-sign-in-out.md)
<!-- - [Authenticate native client apps](configure-authentication-client-apps.md) -->
- [OAuth 토큰 및 세션 작업](configure-authentication-oauth-tokens.md)
- [사용자 및 애플리케이션 클레임에 액세스](configure-authentication-user-identities.md)
- [파일 기반 구성](configure-authentication-file-based.md)

샘플:
- [자습서: Azure App Service에서 실행되는 웹앱에 인증 추가](scenario-secure-app-authentication-app-service.md)
- [자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여(Windows 또는 Linux)](tutorial-auth-aad.md)
- [Azure AppService EasyAuth의 .NET Core 통합(타사)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [.NET Core를 사용하여 Azure App Service 인증 가져오기(타사)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)