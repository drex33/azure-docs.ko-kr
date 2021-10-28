---
title: Chrome 브라우저에서 SameSite 쿠키 변경 내용을 처리하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: Chrome 브라우저에서 SameSite 쿠키 변경 내용을 처리하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 01fcd0f9450f55acce3b01fb409b28f136f52a4b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734691"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리

## <a name="what-is-samesite"></a>SameSite란?

`SameSite`는 웹 애플리케이션에서 CSRF(교차 사이트 요청 위조) 공격을 방지하기 위해 HTTP 쿠키에서 설정할 수 있는 속성입니다.

- `SameSite`가 **Lax** 로 설정된 경우 쿠키는 동일한 사이트 내의 요청 및 다른 사이트의 GET 요청으로 전송됩니다. 도메인 간 GET 요청으로는 전송되지 않습니다.
- **Strict** 값은 쿠키가 동일한 사이트 내의 요청으로만 전송되도록 합니다.

기본적으로 `SameSite` 값은 브라우저에서 설정되지 않습니다. 이때문에 요청으로 전송되는 쿠키에 대한 제한이 없습니다. 애플리케이션은 요구 사항에 따라 **Lax** 또는 **Strict** 를 설정하여 CSRF 보호를 옵트인해야 합니다.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 변경 내용 및 인증에 미치는 영향

최신 [SameSite 표준 업데이트](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)는 값이 Lax로 설정되지 않은 경우 `SameSite`의 기본 동작을 만들어 앱 보호를 제안합니다. 이러한 완화는 다른 사이트에서 수행한 GET을 제외한 HTTP 요청에서 쿠키가 제한됨을 의미합니다. 또한 전송되는 쿠키에 대한 제한을 제거하기 위해 **None** 값이 도입되었습니다. 이러한 업데이트는 곧 Chrome 브라우저의 향후 버전에서 릴리스될 예정입니다.

웹앱이 응답 모드 "form_post"를 사용하여 Microsoft ID 플랫폼으로 인증하는 경우 로그인 서버는 HTTP POST를 사용하여 애플리케이션에 응답하여 토큰 또는 인증 코드를 보냅니다. 이 요청은 도메인 간 요청(예: `login.microsoftonline.com`에서 `https://contoso.com/auth`와 같은 도메인으로의 요청)이므로 앱에서 설정한 쿠키는 이제 Chrome의 새 규칙을 따릅니다. 사이트 간 시나리오에서 사용해야 하는 쿠키는 로그인 요청에서도 전송되는 *상태* 및 *nonce* 값을 보유하는 쿠키입니다. 세션을 유지하기 위해 Azure AD에서 삭제한 다른 쿠키가 있습니다.

웹앱을 업데이트하지 않으면 이 새로운 동작으로 인해 인증이 실패합니다.

## <a name="mitigation-and-samples"></a>완화 및 샘플

인증 오류를 해결하기 위해 Microsoft ID 플랫폼을 사용하여 인증하는 웹앱은 Chrome 브라우저에서 실행 될 때 도메인 간 시나리오에서 사용되는 쿠키에 대해 `SameSite` 속성을 `None`으로 설정할 수 있습니다.
다른 브라우저(전체 목록은 [여기](https://www.chromium.org/updates/same-site/incompatible-clients) 참조)는 `SameSite`의 이전 동작을 따르고 `SameSite=None`가 설정된 경우 쿠키를 포함하지 않습니다.
이때문에 여러 브라우저에서 인증을 지원하기 위해 웹앱은 Chrome에서만 `SameSite` 값을 `None`으로 설정하고 다른 브라우저에서는 이 값을 비워 둡니다.

이 방법은 아래의 코드 샘플에 나와 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

아래 표에서는 ASP.NET 및 ASP.NET Core 샘플에서 SameSite 변경 내용과 관련해서 발생한 끌어오기 요청을 보여 줍니다.

| 샘플 | 끌어오기 요청 |
| ------ | ------------ |
|  [ASP.NET Core 웹앱 증분 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [동일한 사이트 쿠키 수정 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC 웹앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect):  |  [동일한 사이트 쿠키 수정 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [동일한 사이트 쿠키 수정 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

ASP.NET 및 ASP.NET Core에서 SameSite 쿠키를 처리하는 방법에 대한 자세한 내용은 다음도 참조하세요.

- [ASP.NET Core에서 SameSite 쿠키 사용](/aspnet/core/security/samesite)
- [SameSite 문제에 대한 ASP.NET 블로그](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 샘플 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 샘플 | 끌어오기 요청 |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [동일한 사이트 쿠키 수정 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [동일한 사이트 쿠키 수정 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>다음 단계

SameSite 및 웹앱 시나리오에 대해 자세히 알아보세요.

- [SameSite에 대한 Google Chrome FAQ](https://www.chromium.org/updates/same-site/faq)

- [Chromium SameSite 페이지](https://www.chromium.org/updates/same-site)

- [시나리오: 사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)