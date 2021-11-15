---
title: Microsoft ID 플랫폼의 애플리케이션 유형 | Azure
description: Microsoft ID 플랫폼에서 지원되는 앱 형식 및 시나리오입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: c775efa92c249904348f1ced18994c42e9ecc8eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597819"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼의 애플리케이션 유형

Microsoft ID 플랫폼은 모두 업계 표준 프로토콜 [OAuth 2.0 또는 OpenID Connect](active-directory-v2-protocols.md)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다. 이 문서에서는 기본 설정 언어 또는 플랫폼에 관계없이 Microsoft ID 플랫폼을 사용하여 빌드할 수 있는 앱 형식을 설명합니다. 이 정보는 [애플리케이션 시나리오](authentication-flows-app-scenarios.md#application-scenarios)에서 코드 작업을 시작하기 전에 개략적인 시나리오를 이해하는 데 도움이 됩니다.

## <a name="the-basics"></a>기본 사항

Microsoft ID 플랫폼을 사용하는 각 앱을 Azure Portal [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)에 등록해야 합니다. 앱 등록 프로세스는 앱에 대한 다음 값을 수집하고 할당합니다.

* 앱을 고유하게 식별하는 **애플리케이션(클라이언트) ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
* 지원되는 계정 유형과 같은 다른 몇 가지 시나리오 관련 값

자세한 내용은 [앱 등록](quickstart-register-app.md)방법을 참조하세요.

앱이 등록되면 엔드포인트에 요청을 보내 Microsoft ID 플랫폼과 통신합니다. Microsoft에서는 이러한 요청에 대한 세부 정보를 처리하는 오픈 소스 프레임워크 및 라이브러리를 제공합니다. 또한 이러한 엔드포인트에 대한 요청을 만들어 인증 논리를 직접 구현할 수 있습니다.

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>단일 페이지 앱(JavaScript)

대다수의 최신 앱에는 주로 Angular, React 또는 Vue 같은 프레임워크를 사용하여 JavaScript로 작성된 단일 페이지 앱 프런트 엔드가 있습니다. Microsoft ID 플랫폼은 인증을 위해서 [Openid Connect](v2-protocols-oidc.md) 프로토콜을 사용하고 권한 부여를 위해서는 [OAuth 2.0 암시적 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md) 또는 더 최신의 [OAuth 2.0 권한 부여 코드 + PKCE 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 이러한 앱을 지원합니다(아래 참조).

아래의 흐름 다이어그램에서는 앱이 Microsoft ID 플랫폼 `authorize` 엔드포인트에서 코드를 수신하고 사이트 간 웹 요청을 사용하여 액세스 토큰 및 새로 고침 토큰에 대한 교환 OAuth 2.0 권한 부여 코드를 부여(PKCE에 관한 세부 정보 생략)하는 것을 보여줍니다. 액세스 토큰은 24시간마다 만료되며 앱은 새로 고침 토큰을 사용하여 다른 코드를 요청해야 합니다. 액세스 토큰 외에 클라이언트 애플리케이션에 로그인한 사용자를 나타내는 `id_token`은 일반적으로 동일한 흐름 및/또는 별도의 Openid Connect 요청을 통해 요청됩니다(여기에 표시되지 않음).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="단일 페이지 앱과 보안 토큰 서비스 엔드포인트 간의 OAuth 2 인증 코드 흐름을 보여 주는 다이어그램입니다." border="false":::

이 시나리오의 작동 방식을 확인하려면 [자습서: 권한 부여 코드 흐름을 사용하여 사용자를 로그인시키고 JavaScript SPA에서 Microsoft Graph API 호출](tutorial-v2-javascript-auth-code.md)을 참조하세요.

### <a name="authorization-code-flow-vs-implicit-flow"></a>권한 부여 코드 흐름과 암시적 흐름 비교

OAuth 2.0의 경우 [암시적 흐름](v2-oauth2-implicit-grant-flow.md)은 단일 페이지 앱을 빌드하는 데 권장되는 방법입니다. Microsoft는 [타사 쿠키](reference-third-party-cookies-spas.md)를 제거하고 암시적 흐름과 관련된 보안 문제에 [더 많은 주의](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)를 기울여 단일 페이지 앱의 권한 부여 코드 흐름으로 전환했습니다.

Safari 및 기타 프라이버시에 민감한 브라우저에서 앱의 호환성을 보장하기 위해 더 이상 암시적 흐름을 사용하지 않는 것이 좋으며, 대신 권한 부여 코드 흐름을 사용하는 것이 좋습니다.

## <a name="web-apps"></a>웹 앱

사용자가 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, Node)의 경우 사용자 로그인에 [OpenID Connect](active-directory-v2-protocols.md)를 사용할 수 있습니다. OpenID Connect에서는 웹앱이 ID 토큰을 받습니다. ID 토큰은 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰입니다.

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Microsoft ID 플랫폼에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [액세스 토큰](access-tokens.md) 참조 및 [id_token 참조](id-tokens.md)를 확인하세요.

웹 서버 앱에서 로그인 인증 흐름은 다음과 같은 높은 수준의 단계를 수행합니다.

![웹앱 인증 흐름 표시](./media/v2-app-types/convergence-scenarios-webapp.svg)

Microsoft ID 플랫폼에서 받은 공개 서명 키로 ID 토큰의 유효성을 검사하여 사용자의 ID를 확인할 수 있습니다. 후속 페이지 요청에서 사용자를 식별하는 데 사용할 수 있는 세션 쿠키가 설정됩니다.

이 시나리오의 작동 방식을 확인하려면 [사용자를 로그인하는 웹앱 시나리오](scenario-web-app-sign-user-overview.md)에서 코드 샘플을 사용해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 REST API와 같은 다른 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹 서버 앱은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 결합된 OpenID Connect 및 OAuth 2.0 흐름에 참여합니다. 이 시나리오에 대한 자세한 내용은 [웹앱 및 Web API 시작](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)을 참조하세요.

## <a name="web-apis"></a>Web API

Microsoft ID 플랫폼을 사용하여 앱의 RESTful 웹 API와 같은 웹 서비스의 보안을 유지할 수 있습니다. 웹 API는 다양한 플랫폼과 언어로 구현할 수 있습니다. Azure Functions에서 HTTP 트리거를 사용하여 구현할 수도 있습니다. ID 토큰 및 세션 쿠키 대신 웹 API는 OAuth 2.0 액세스 토큰을 사용하여 데이터 보안을 유지하고 들어오는 요청을 인증합니다. 웹 API 호출자는 다음과 같이 HTTP 요청의 인증 헤더에 액세스 토큰을 추가합니다.

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

웹 API는 액세스 토큰을 사용하여 API 호출자의 ID를 확인하고 액세스 토큰에 인코딩된 클레임에서 호출자에 대한 정보를 추출합니다. Microsoft ID 플랫폼에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [액세스 토큰](access-tokens.md) 참조 및 [id_token](id-tokens.md) 참조를 확인하세요.

웹 API를 통해 사용자는 [범위](v2-permissions-and-consent.md)라고도 하는 사용 권한을 노출하여 특정 기능이나 데이터를 옵트인(opt-in)하거나 옵트아웃(opt-out)할 수 있습니다. 호출 앱이 범위에 대한 사용 권한을 얻으려면 사용자가 흐름 중 범위에 동의해야 합니다. Microsoft ID 플랫폼은 사용자에게 권한을 요청한 다음, 웹 API가 받은 모든 액세스 토큰에 이러한 권한을 기록합니다. 웹 API는 각 호출에서 받은 액세스 토큰의 유효성을 검사하고 권한 부여 검사를 수행합니다.

웹 API는 웹 서버 앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 웹 API까지 포함하여 모든 유형의 앱에서 액세스 토큰을 받을 수 있습니다. 웹 API의 개략적인 흐름은 다음과 같습니다.

![웹 API 인증 흐름 표시](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 액세스 토큰을 사용하여 웹 API 보안을 유지하는 방법을 알아보려면 [보호된 웹 API 시나리오](scenario-protected-web-api-overview.md)의 웹 API 코드 샘플을 확인하세요.

대부분의 경우 웹 API는 Microsoft ID 플랫폼으로 보호되는 다른 다운스트림 API에 대해 아웃바운드 요청도 보내야 합니다. 이를 위해 웹 API는 **On-Behalf-Of** 흐름을 활용하여 들어오는 액세스 토큰을 아웃바운드 요청에서 사용할 다른 액세스 토큰으로 바꿀 수 있습니다. 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md)을 참조하세요.

## <a name="mobile-and-native-apps"></a>모바일 및 네이티브 앱

모바일 및 데스크톱 앱과 같은 디바이스 설치 앱은 데이터를 저장하고 사용자 대신 기능을 수행하는 백 엔드 서비스 또는 웹 API에 액세스해야 하는 경우가 많습니다. 이러한 앱은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 백 엔드 서비스에 로그인 및 권한 부여를 추가할 수 있습니다.

이 흐름에서 앱은 사용자 로그인 시 Microsoft ID 플랫폼에서 권한 부여 코드를 받습니다. 권한 부여 코드는 현재 로그인한 사용자를 대신해 백 엔드 서비스를 호출할 앱의 사용 권한을 나타냅니다. 앱은 백그라운드에서 권한 부여 코드를 OAuth 2.0 액세스 토큰 및 새로 고침 토큰으로 교환할 수 있습니다. 앱은 액세스 토큰을 사용하여 HTTP 요청 시 웹 API에 인증할 수 있고 새로 고침 토큰을 사용하여 이전 액세스 토큰 만료 시 새 액세스 토큰을 가져올 수 있습니다.

![네이티브 앱 인증 흐름 표시](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> 애플리케이션에서 기본 시스템 웹 보기를 사용하는 경우 [Azure AD 인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)에서 "로그인 확인" 기능과 오류 코드 AADSTS50199에 대한 정보를 확인합니다.

## <a name="daemons-and-server-side-apps"></a>디먼 및 서버 쪽 앱

장기 실행 프로세스가 있거나 사용자와의 상호 작용 없이 작동하는 앱은 웹 API와 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다. 클라이언트 암호 또는 인증서를 사용하여 앱 ID를 증명할 수 있습니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하는 .NET Core 디먼 콘솔 애플리케이션](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)을 참조하세요.

이 흐름에서 앱은 `/token` 엔드포인트와 직접 상호 작용하여 액세스 권한을 가져옵니다.

![디먼 앱 인증 흐름 표시](./media/v2-app-types/convergence-scenarios-daemon.svg)

디먼 앱을 빌드하려면 [클라이언트 자격 증명 설명서](v2-oauth2-client-creds-grant-flow.md)를 참조하거나 [.NET 샘플 앱](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)을 사용해 보세요.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 지원되는 애플리케이션의 유형을 배웠으므로 이제 [OAuth 2.0 및 OpenID Connect](active-directory-v2-protocols.md)에 대해 자세히 알아보고 다양한 시나리오에서 사용되는 프로토콜 구성 요소를 파악하세요.
