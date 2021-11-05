---
title: MSAL.js 클라이언트 앱 초기화 | Azure
titleSuffix: Microsoft identity platform
description: MSAL.js(JavaScript용 Microsoft 인증 라이브러리)를 사용하여 클라이언트 애플리케이션을 초기화하는 방법에 관해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 2680f78f7bcd1caaa3d6d1280c51c81b97a52048
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050644"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js를 사용하여 클라이언트 애플리케이션 초기화

이 문서에서는 사용자 에이전트 애플리케이션의 인스턴스를 사용하여 MSAL.js(JavaScript용 Microsoft 인증 라이브러리)를 초기화하는 방법을 설명합니다.

사용자 에이전트 애플리케이션은 클라이언트 코드가 웹 브라우저와 같은 사용자 에이전트에서 실행되는 퍼블릭 클라이언트 애플리케이션의 형태입니다. 이러한 클라이언트는 브라우저 컨텍스트에 공개적으로 액세스할 수 있기 때문에 비밀을 저장하지 않습니다.

클라이언트 애플리케이션 유형 및 애플리케이션 구성 옵션에 관한 자세한 내용은 [MSAL의 퍼블릭 및 기밀 클라이언트 앱](msal-client-applications.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

애플리케이션을 초기화하기 전에 먼저 [Azure Portal에 등록](scenario-spa-app-registration.md)하여 애플리케이션과 Microsoft ID 플랫폼 간의 신뢰 관계를 설정해야 합니다.

앱을 등록한 후 Azure Portal에서 찾을 수 있는 다음 값의 일부 또는 모두가 필요합니다.

| 값                   | 필수 | 설명                                                                                                                                                                |
| :---------------------- | :------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 애플리케이션(클라이언트) ID | 필수 | Microsoft ID 플랫폼 내에서 애플리케이션을 고유하게 식별하는 GUID입니다.                                                                                   |
| Authority               | Optional | 애플리케이션에 대한 ID 공급자 URL(‘인스턴스’) 및 ‘로그인 대상 그룹’입니다.  인스턴스와 로그인 대상 그룹이 연결되면 ‘인증 기관’을 구성합니다. |
| 디렉터리(테넌트) ID   | Optional | _단일 테넌트 애플리케이션_ 이라고도 하는 조직 전용 LOB(기간 업무) 애플리케이션을 빌드하는 경우 디렉터리(테넌트) ID를 지정합니다.          |
| 리디렉션 URI            | Optional | 웹앱을 빌드하는 경우 `redirectUri`는 ID 공급자(Microsoft ID 플랫폼)가 발급한 보안 토큰을 반환할 위치를 지정합니다.   |

## <a name="initialize-msaljs-2x-apps"></a>MSAL.js 2.x 앱 초기화

[구성][msal-js-configuration] 개체로 [PublicClientApplication][msal-js-publicclientapplication]을 인스턴스화하여 MSAL.js 인증 컨텍스트를 초기화합니다. 필요한 최소 구성 속성은 Azure Portal의 앱 등록 **개요** 페이지에 **애플리케이션(클라이언트) ID** 로 표시되는 애플리케이션의 `clientID`입니다.

다음은 `PublicClientApplication`의 구성 개체 및 인스턴스화에 대한 예제입니다.

```javascript
const msalConfig = {
  auth: {
    clientId: "11111111-1111-1111-111111111111",
    authority: "https://login.microsoftonline.com/common",
    knownAuthorities: [],
    redirectUri: "https://localhost:3001",
    postLogoutRedirectUri: "https://localhost:3001/logout",
    navigateToLoginRequestUrl: true,
  },
  cache: {
    cacheLocation: "sessionStorage",
    storeAuthStateInCookie: false,
  },
  system: {
    loggerOptions: {
      loggerCallback: (
        level: LogLevel,
        message: string,
        containsPii: boolean
      ): void => {
        if (containsPii) {
          return;
        }
        switch (level) {
          case LogLevel.Error:
            console.error(message);
            return;
          case LogLevel.Info:
            console.info(message);
            return;
          case LogLevel.Verbose:
            console.debug(message);
            return;
          case LogLevel.Warning:
            console.warn(message);
            return;
        }
      },
      piiLoggingEnabled: false,
    },
    windowHashTimeout: 60000,
    iframeHashTimeout: 6000,
    loadFrameTimeout: 0,
  },
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance
  .handleRedirectPromise()
  .then((tokenResponse) => {
    // Handle redirect response
  })
  .catch((error) => {
    // Handle redirect error
  });
```

### `handleRedirectPromise`

애플리케이션에서 리디렉션 흐름을 사용할 때 [handleRedirectPromise][msal-js-handleredirectpromise]를 호출합니다. 리디렉션 흐름을 사용할 때는 페이지 로드마다 `handleRedirectPromise`를 실행해야 합니다.

이 프라미스에서 가능한 세 가지 결과는 다음과 같습니다.

- `.then`이 호출되고 `tokenResponse`가 true인 경우: 애플리케이션이 성공한 리디렉션 작업에서 반환됩니다.
- `.then`이 호출되고 `tokenResponse`가 false인 경우(`null`): 애플리케이션이 리디렉션 작업에서 반환되지 않습니다.
- `.catch`가 호출된 경우: 애플리케이션이 리디렉션 작업에서 반환되고 오류가 발생했습니다.

## <a name="initialize-msaljs-1x-apps"></a>MSAL.js 1.x 앱 초기화

구성 개체로 [UserAgentApplication][msal-js-useragentapplication]을 인스턴스화하여 MSAL 1.x 인증 컨텍스트를 초기화합니다. 필요한 최소 구성 속성은 Azure Portal의 앱 등록 **개요** 페이지에 **애플리케이션(클라이언트) ID** 로 표시되는 애플리케이션의 `clientID`입니다.

MSAL.js 1.2.x 또는 그 이전 버전에서 리디렉션 흐름([loginRedirect][msal-js-loginredirect] 및 [acquireTokenRedirect][msal-js-acquiretokenredirect])을 사용하는 인증 방법의 경우 `handleRedirectCallback()` 메서드를 통해 성공 또는 오류에 대한 콜백을 명시적으로 등록해야 합니다. 리디렉션 흐름은 팝업 항목 환경을 사용하는 메서드처럼 프라미스를 반환하지 않기 때문에 명시적으로 MSAL.js 1.2.x 또는 그 이전 버전에서 콜백을 등록해야 합니다. MSAL.js 버전 1.3.x 이상에서는 콜백을 ‘선택적으로’ 등록할 수 있습니다.

```javascript
// Configuration object constructed
const msalConfig = {
  auth: {
    clientId: "11111111-1111-1111-111111111111",
  },
};

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
  // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>단일 인스턴스 및 구성

MSAL.js 1.x 및 2.x는 각각 단일 인스턴스 및 `UserAgentApplication` 또는 `PublicClientApplication` 구성을 갖도록 설계되어 단일 인증 컨텍스트를 나타냅니다.

`UserAgentApplication` 또는 `PublicClientApplication`의 여러 인스턴스는 브라우저에서 충돌하는 캐시 항목 및 동작을 유발하므로 권장하지는 않습니다.

## <a name="next-steps"></a>다음 단계

GitHub의 이 MSAL.js 2.x 코드 샘플에서는 [구성][msal-js-configuration] 개체로 [PublicClientApplication][msal-js-publicclientapplication]의 인스턴스화를 보여 줍니다.

[Azure-Samples/ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->

[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
