---
title: 암시적 허용에서 권한 부여 코드 흐름으로 JavaScript 단일 페이지 앱 마이그레이션 | Azure
titleSuffix: Microsoft identity platform
description: MSAL.js 1.x와 암시적 허용 흐름을 사용하는 JavaScript SPA를 MSAL.js 2.x와 PKCE 및 CORS를 지원하는 권한 부여 코드 흐름으로 업데이트하는 방법
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 5a38063e8b5311422ece4bafc9c22355262d631d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636214"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>암시적 허용에서 권한 부여 코드 흐름으로 JavaScript 단일 페이지 앱 마이그레이션

JavaScript용 Microsoft 인증 라이브러리(MSAL.js) v2.0은 Microsoft ID 플랫폼의 단일 페이지 애플리케이션에 PKCE 및 CORS를 사용하는 권한 부여 코드 흐름 지원을 제공합니다. 암시적 허용을 사용하는 MSAL.js 1.x 애플리케이션을 MSAL.js 2.0 이상("*2.x*")과 권한 부여 코드 흐름으로 마이그레이션하려면 아래 섹션의 단계를 따르세요.

MSAL.js 1.x에서 개선된 MSAL.js 2.x는 암시적 허용 흐름 대신 브라우저의 권한 부여 코드 흐름을 지원합니다. MSAL.js 2.x는 암시적 흐름을 지원하지 **않습니다**.

## <a name="migration-steps"></a>마이그레이션 단계

애플리케이션을 MSAL.js 2.x 및 권한 부여 코드 흐름으로 업데이트하려면 세 가지 기본 단계를 수행해야 합니다.

1. **웹** 플랫폼에서 **단일 페이지 애플리케이션** 플랫폼으로 [앱 등록](#switch-redirect-uris-to-spa-platform) 리디렉션 URI를 전환합니다.
1. [코드](#switch-redirect-uris-to-spa-platform)를 MSAL.js 1.x에서 **2.x** 로 업데이트합니다.
1. 등록을 공유하는 모든 애플리케이션을 MSAL.js 2.x 및 권한 부여 코드 흐름으로 업데이트했으면 앱 등록에서 [암시적 허용](#disable-implicit-grant-settings)을 사용하지 않도록 설정합니다.

다음 섹션에서는 각 단계에 대해 자세히 설명합니다.

## <a name="switch-redirect-uris-to-spa-platform"></a>리디렉션 URI를 SPA 플랫폼으로 전환

애플리케이션에 대한 기존 앱 등록을 계속 사용하려는 경우 Azure Portal을 사용하여 등록의 리디렉션 URI를 SPA 플랫폼으로 업데이트합니다. 이렇게 하면 해당 등록을 사용하는 앱에 PKCE 및 CORS가 지원되는 권한 부여 코드 흐름을 사용할 수 있습니다(애플리케이션의 코드는 MSAL.js v2.x로 업데이트해야 함).

현재 **웹** 플랫폼 리디렉션 URI를 사용하여 구성된 앱 등록에 대해 다음 단계를 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인하고 **Azure Active Directory** 테넌트를 선택합니다.
1. **앱 등록** 에서 애플리케이션, **인증** 을 차례로 선택합니다.
1. **웹** 플랫폼 타일의 **리디렉션 URI** 에서 URI를 마이그레이션해야 함을 나타내는 경고 배너를 선택합니다.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Azure Portal의 웹앱 타일에 대한 암시적 흐름 경고 배너":::
1. MSAL.js 2.x를 사용할 애플리케이션의 리디렉션 *URI만* 선택한 후 **구성** 을 선택합니다.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Azure Portal의 SPA 창에서 리디렉션 URI 창 선택":::

이러한 리디렉션 URI는 이제 **단일 페이지 애플리케이션** 플랫폼 타일에 표시되며, 이는 권한 부여 코드 흐름에 CORS가 지원되고 이러한 URI에 PKCE가 설정되어 있음을 보여 줍니다.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Azure Portal에서 앱 등록의 단일 페이지 애플리케이션 타일":::

기존 등록에서 리디렉션 URI를 업데이트하는 대신 [새 앱 등록을 만들 수도 있습니다](scenario-spa-app-registration.md).

## <a name="update-your-code-to-msaljs-2x"></a>코드를 MSAL.js 2.x로 업데이트

MSAL 1.x에서 다음과 같이 [UserAgentApplication][msal-js-useragentapplication]을 초기화하여 애플리케이션 인스턴스를 만들었습니다.

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

MSAL 2.x에서는 [PublicClientApplication][msal-js-publicclientapplication]을 대신 초기화합니다.

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

MSAL 2.x를 애플리케이션에 추가하는 전체 연습을 보려면 [자습서: 권한 부여 코드 흐름을 사용하여 사용자 로그인 및 JavaScript SPA(단일 페이지 앱)에서 Microsoft Graph API 호출](tutorial-v2-javascript-auth-code.md)을 참조하세요.

코드를 변경해야 할 수 있는 추가 변경 내용은 GitHub의 [마이그레이션 가이드](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md)를 참조하세요.

## <a name="disable-implicit-grant-settings"></a>암시적 허용 설정 사용 안 함

이 앱 등록 및 해당 클라이언트 ID를 사용하는 모든 프로덕션 애플리케이션을 MSAL 2.x로 업데이트하고 권한 부여 코드 흐름을 업데이트한 후에는 앱 등록에서 암시적 허용 설정을 선택 취소해야 합니다.

앱 등록에서 암시적 허용 설정을 선택 취소하면 등록 및 해당 클라이언트 ID를 사용하는 모든 애플리케이션에 암시적 흐름이 사용하지 않도록 설정됩니다.

모든 애플리케이션을 MSAL.js 2.x 및 [PublicClientApplication][msal-js-publicclientapplication]으로 업데이트하기 전에 암시적 허용 흐름을 해제하지 **마세요**.

## <a name="next-steps"></a>다음 단계

암시적 코드 흐름과 권한 부여 코드 흐름 간의 차이점을 포함하여 권한 부여 코드 흐름에 대해 자세히 알아보려면 [Microsoft ID 플랫폼 및 OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 참조하세요.

Microsoft ID 플랫폼에서 JavaScript 단일 페이지 애플리케이션을 개발하는 방법에 대해 자세히 알아보려면 여러 부분으로 구성된 [시나리오: 단일 페이지 애플리케이션](scenario-spa-overview.md) 시리즈 문서가 시작하는 데 도움이 될 수 있습니다.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
