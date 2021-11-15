---
title: ADAL.js에서 MSAL.js로 JavaScript 애플리케이션 마이그레이션 | Azure
titleSuffix: Microsoft identity platform
description: ADAL(Active Directory 인증 라이브러리) 대신 인증 및 권한 부여에 MSAL(Microsoft 인증 라이브러리)을 사용하도록 기존 JavaScript 애플리케이션을 업데이트하는 방법입니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: marsma
ms.custom: has-adal-ref
ms.openlocfilehash: 6614c1d06dd1e5093dfbe7e3ea3a20ff13ec74c1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501042"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>ADAL.js에서 MSAL.js로 JavaScript 앱을 마이그레이션하는 방법

[JavaScript용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-js)(MSAL.js, *msal-browser* 라고도 함) 2.x는 Microsoft ID 플랫폼에서 JavaScript 애플리케이션과 함께 사용하는 것이 좋은 인증 라이브러리입니다. 이 문서에서는 ADAL.js를 사용하여 MSAL.js 2.x를 사용하는 앱을 마이그레이션하기 위해 수행해야 하는 변경 내용을 강조 표시합니다.

> [!NOTE]
> MSAL.js 2.x는 MSAL.js 1.x를 통해 권장됩니다. 인증 코드 부여 흐름은 더 안전하며 여러 이점이 있지만 특히 Safari와 같은 브라우저가 타사 쿠키를 차단하기 위해 구현한 개인 정보 보호 조치에도 불구하고 단일 페이지 애플리케이션이 우수한 사용자 환경을 유지하도록 허용합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 앱 등록 포털에서 **플랫폼** / **회신 URL 형식** 을 **단일 페이지 애플리케이션** 으로 설정해야 합니다(**웹** 과 같이 앱 등록에 다른 플랫폼이 추가된 경우 리디렉션 URI가 겹치지 않게 해야 합니다. [리디렉션 URI에 대한 제한 사항](./reply-url.md)을 참조하세요.)
- **Internet Explorer** 에서 앱을 실행하려면 MSAL.js가 의존하는 ES6 기능(예: 프라미스)에 [폴리필](./msal-js-use-ie-browser.md)을 제공해야 합니다.
- 아직 마이그레이션하지 않은 경우 Azure AD 앱을 [v2 엔드포인트](../azuread-dev/azure-ad-endpoint-comparison.md)로 마이그레이션했는지 확인합니다.

## <a name="install-and-import-msal"></a>MSAL 설치 및 가져오기

MSAL.js 2.x 라이브러리를 설치하는 방법에는 다음 두 가지가 있습니다.

### <a name="via-npm"></a>NPM 사용:

```console
npm install @azure/msal-browser
```

그런 다음 모듈 시스템에 따라 아래와 같이 가져옵니다.

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>CDN 사용:

HTML 문서의 헤더 섹션에서 스크립트를 로드합니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

CDN 사용 시 대체 CDN 링크 및 모범 사례는 [CDN 사용](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)을 참조하세요.

## <a name="initialize-msal"></a>MSAL 초기화

ADAL.js에서 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) 클래스를 인스턴스화하면 인증을 달성하는 데 사용할 수 있는 메서드(`login`, `acquireTokenPopup` 등)가 노출됩니다. 이 개체는 권한 부여 서버 또는 ID 공급자에 대한 애플리케이션 연결을 나타내는 역할을 합니다. 초기화할 때 필수 매개 변수는 다음과 같은 **clientId** 입니다.

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

MSAL.js에서 [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) 클래스를 대신 인스턴스화합니다. ADAL.js와 같이 생성자에는 최소한 `clientId` 매개 변수를 포함하는 [구성 개체](#configure-msal)가 필요합니다. 자세한 내용은 [MSAL.js 초기화](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md)를 참조하세요.

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

ADAL.js와 MSAL.js에서 권한 URI는 지정하지 않으면 기본적으로 `https://login.microsoftonline.com/common`으로 설정됩니다.

> [!NOTE]
> v2.0에서 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 모든 Azure AD 조직 또는 MSA(개인 Microsoft 계정)를 사용하여 로그인하도록 허용할 수 있습니다. MSAL.js에서 Azure AD 계정에 대한 로그인(ADAL.js와 동일한 동작)을 제한하려면 `https://login.microsoftonline.com/organizations`를 사용해야 합니다.

## <a name="configure-msal"></a>MSAL 구성

[AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext)를 초기화할 때 사용되는 [ADAL.js의 구성 옵션](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context) 중 일부는 MSAL.js에서 더 이상 사용되지 않으며, 일부 새로운 구성 옵션이 도입되었습니다. [사용 가능한 전체 옵션 목록](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)을 참조하세요. `clientId`를 제외하고 해당 옵션 중 대부분이 토큰 획득 중에 재정의되므로 ‘요청별’ 기준으로 설정할 수 있다는 점이 중요합니다. 예를 들어 토큰을 획득할 때 초기화 중에 설정한 것과 다른 **권한 URI** 또는 **리디렉션 URI** 를 사용할 수 있습니다.

또한 더 이상 구성 옵션을 통해 로그인 경험(즉, 팝업 창 사용 또는 페이지 리디렉션 여부)을 지정할 필요가 없습니다. 대신 `MSAL.js`는 `PublicClientApplication` 인스턴스를 통해 `loginPopup` 및 `loginRedirect` 메서드를 노출합니다.

## <a name="enable-logging"></a>로깅 사용

ADAL.js에서는 코드의 어느 위치에서나 별도로 로깅을 구성합니다.

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

MSAL.js에서 로깅은 구성 옵션의 일부이며 `PublicClientApplication` 초기화 중에 생성됩니다.

```javascript
const msalConfig = {
  auth: {
      // authentication related parameters
  },
  cache: {
      // cache related parameters
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
}

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>MSAL API로 전환

ADAL.js의 퍼블릭 메서드 중 일부는 MSAL.js에 해당합니다.

| ADAL                                | MSAL                              | 참고                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 이름이 바뀌고 [계정](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) 개체가 필요합니다. |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | 이제 비동기화하고 프라미스를 반환              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | 이제 비동기화하고 프라미스를 반환              |
| `handleWindowCallback`              | `handleRedirectPromise`           | 리디렉션 환경을 사용하는 경우 필요          |
| `getCachedUser`                     | `getAllAccounts`                  | 이름을 바꾸고 이제 계정 배열을 반환합니다.|

다른 항목은 더 이상 사용되지 않지만 MSAL.js는 새로운 메서드를 제공합니다.

| ADAL                              | MSAL                            | 참고                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | 해당 없음                             | 더 이상 사용되지 않습니다. `loginPopup` 또는 `loginRedirect` 사용  |
| `logOut`                          | 해당 없음                             | 더 이상 사용되지 않습니다. `logoutPopup` 또는 `logoutRedirect` 사용|
| 해당 없음                               | `loginPopup`                    |                                                  |
| 해당 없음                               | `loginRedirect`                 |                                                  |
| 해당 없음                               | `logoutPopup`                   |                                                  |
| 해당 없음                               | `logoutRedirect`                |                                                  |
| 해당 없음                               | `getAccountByHomeId`            | 홈 ID로 계정 필터링(이전 + 테넌트 ID)    |
| 해당 없음                               | `getAccountLocalId`             | 로컬 ID로 계정 필터링(ADFS에 유용함)   |
| 해당 없음                               | `getAccountUsername`            | 사용자 이름으로 계정 필터링(있는 경우)         |

또한 MSAL.js는 ADAL.js와 달리 TypeScript로 구현되기 때문에 프로젝트에서 사용할 수 있는 다양한 형식과 인터페이스를 노출합니다. 자세한 내용은 [MSAL.js API 참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)를 확인하세요.

## <a name="use-scopes-instead-of-resources"></a>리소스 대신 범위 사용

Azure AD **v1.0** vs. **v2.0** 엔드포인트 간의 중요한 차이점은 리소스에 액세스하는 방법입니다. **v1.0** 엔드포인트에서 ADAL.js를 사용할 때 먼저 앱 등록 포털에 대한 권한을 등록한 다음 아래와 같이 리소스(예: Microsoft Graph)에 대한 액세스 토큰을 요청합니다.

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js는 **v1.0** 및 **v2.0** 엔드포인트를 모두 지원합니다. **v2.0** 엔드포인트는 *범위 중심* 모델을 사용하여 리소스에 액세스합니다. 따라서 리소스에 대한 액세스 토큰을 요청하는 경우 해당 리소스에 대한 범위도 지정해야 합니다.

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

범위 중심 모델의 이점 중 하나는 ‘동적 범위’를 사용하는 기능입니다. v1.0 엔드포인트를 사용하여 애플리케이션을 빌드하는 경우 로그인할 때 사용자가 동의하도록 애플리케이션에서 요구하는 권한의 전체 세트(‘고정적인 범위’)를 등록해야 했습니다. v2.0에서는 범위 매개 변수를 사용하여 원하는 시간에 권한을 요청할 수 있습니다(즉, ‘동적 범위’). 이를 통해 사용자는 범위에 대한 **증분 동의** 를 제공할 수 있습니다. 따라서 처음에는 사용자가 애플리케이션에 로그인하도록 하고 어떤 종류의 액세스도 필요하지 않은 경우 그렇게 할 수 있습니다. 나중에 사용자의 일정을 읽을 수 있는 기능이 필요한 경우 acquireToken 메서드에서 일정 범위를 요청하고 사용자의 동의를 받을 수 있습니다. 자세한 정보는 [리소스 및 범위](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)를 참조하세요.

## <a name="use-promises-instead-of-callbacks"></a>콜백 대신 프라미스 사용

ADAL.js에서는 인증이 성공하고 응답을 가져온 후 모든 작업에 대해 콜백을 사용합니다.

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

MSAL.js에서는 프라미스가 대신 사용됩니다.

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

ES8와 함께 제공되는 **async/await** 구문을 사용할 수도 있습니다.

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>토큰 캐시 및 검색

ADAL.js와 마찬가지로 MSAL.js는 [웹 스토리지 API](https://developer.mozilla.org/docs/Web/API/Web_Storage_API)를 사용하여 브라우저 스토리지에 토큰 및 기타 인증 아티팩트를 캐시합니다. 사용자가 획득한 토큰을 저장하는 데 더 안전하므로 `sessionStorage` 옵션(참조: [구성](#configure-msal))을 사용하는 것이 좋지만 `localStorage`에서 탭 및 사용자 세션에 걸쳐 [Single Sign On](./msal-js-sso.md)을 제공합니다.

중요한 점은 캐시에 직접 액세스할 필요가 없다는 것입니다. 대신 액세스 토큰이나 사용자 계정과 같은 인증 아티팩트를 검색하는 데 적절한 MSAL.js API를 사용해야 합니다.

## <a name="renew-tokens-with-refresh-tokens"></a>새로 고침 토큰을 사용하여 토큰 갱신

ADAL.js에서는 보안상의 이유로 새로 고침 토큰을 반환하지 않는 [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md)을 사용합니다(새로 고침 토큰은 액세스 토큰보다 수명이 길기 때문에 악의적인 행위자가 소유하는 경우 더 위험함). 따라서 ADAL.js는 숨겨진 Iframe을 사용하여 토큰 갱신을 수행하므로 사용자에게 반복적으로 인증하라는 메시지가 표시되지 않습니다.

PKCE가 지원되는 인증 코드 흐름을 통해 MSAL.js 2.x를 사용하는 앱은 갱신에 사용할 수 있는 ID 및 액세스 토큰과 함께 갱신 토큰을 획득합니다. 새로 고침 토큰의 사용은 무시되며 개발자는 해당 토큰 주위에 논리를 구축해서는 안 됩니다. 대신, MSAL은 자체적으로 새로 고침 토큰을 사용하여 토큰 갱신을 관리합니다. ADAL.js가 있는 이전 토큰 캐시는 토큰 캐시 스키마가 변경되었고 ADAL.js에서 사용된 스키마와 호환되지 않기 때문에 MSAL.js로 전송할 수 없습니다.

## <a name="handle-errors-and-exceptions"></a>오류 및 예외 처리

MSAL.js를 사용할 때 직면할 수 있는 가장 일반적인 오류 유형은 `interaction_in_progress` 오류입니다. 이 오류는 다른 대화형 API가 아직 진행 중인 동안 대화형 API(`loginPopup`, `loginRedirect`, `acquireTokenPopup`, `acquireTokenRedirect`)가 호출될 때 발생합니다. `login*` 및 `acquireToken*` API는 ‘비동기’이므로 다른 프라미스를 호출하기 전에 결과 프라미스가 해결되었는지 확인해야 합니다.

또 다른 일반적인 오류는 `interaction_required`입니다. 이 오류는 종종 대화형 토큰 획득 프롬프트를 시작하여 해결합니다. 예를 들어 액세스하려는 웹 API에 [조건부 액세스](../conditional-access/overview.md) 정책이 있으므로, 사용자가 MFA([다단계 인증](../authentication/concept-mfa-howitworks.md))를 수행해야 할 수도 있습니다. 이 경우 `acquireTokenPopup` 또는 `acquireTokenRedirect`를 트리거하여 `interaction_required` 오류를 처리하면 사용자에게 MFA를 요청하여 사용자가 MFA를 완료하도록 할 수 있습니다.

발생할 수 있는 또 다른 일반적인 오류는 `consent_required`이며, 보호된 리소스에 대한 액세스 토큰을 얻는 데 필요한 권한을 사용자가 동의하지 않을 때 발생합니다. `interaction_required`에서와 같이 `consent_required` 오류에 대한 솔루션은 종종 `acquireTokenPopup` 또는 `acquireTokenRedirect`를 사용하여 대화형 토큰 획득 프롬프트를 시작하는 것입니다.

자세한 내용은 [일반적인 MSAL.js 오류 및 처리 방법](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md)을 참조하세요.

## <a name="use-the-events-api"></a>이벤트 API 사용

MSAL.js(>=v2.4)에서는 앱에서 사용할 수 있는 이벤트 API를 도입합니다. 해당 이벤트는 인증 프로세스 및 MSAL이 현재 수행 중인 작업과 관련이 있으며 UI 업데이트, 오류 메시지 표시, 진행 중인 상호 작용 여부 확인 등에 사용할 수 있습니다. 예를 들어, 다음은 어떤 이유로든 로그인 프로세스가 실패할 때 호출되는 이벤트 콜백입니다.

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

성능을 위해 더 이상 필요하지 않은 이벤트 콜백은 등록을 취소하는 것이 중요합니다. 자세한 내용은 [MSAL.js 이벤트 API](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md)를 참조하세요.

## <a name="handle-multiple-accounts"></a>여러 계정 처리

ADAL.js에는 현재 인증된 엔터티를 나타내는 ‘사용자’ 개념이 있습니다. 사용자가 자신과 연결된 계정을 두 개 이상 가질 수 있다는 사실을 감안하여, MSAL.js에서는 ‘사용자’를 ‘계정’으로 바꿉니다.  또한 이제 여러 계정을 제어하고 작업할 적절한 계정을 선택해야 함을 의미합니다. 아래 코드 조각에서는 이 프로세스를 보여 줍니다.

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

자세한 내용은 [MSAL.js의 계정](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md)을 참조하세요.

## <a name="use-the-wrappers-libraries"></a>래퍼 라이브러리 사용

Angular와 React 프레임워크용으로 개발하는 경우 각각 [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) 및 [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)를 사용할 수 있습니다. 해당 래퍼는 인증 및 토큰 획득 프로세스를 간소화할 수 있는 프레임워크별 메서드와 구성 요소를 제공하면서 MSAL.js와 동일한 공용 API를 노출합니다.

## <a name="run-the-app"></a>앱 실행

변경이 완료되면 앱을 실행하고 인증 시나리오를 테스트합니다.

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>예제: ADAL Node 및 MSAL Node를 사용하여 웹앱 보안하기

아래 코드 조각은 Microsoft ID 플랫폼으로 사용자를 인증하고 먼저 ADAL.js를 사용한 다음 MSAL.js를 사용하여 Microsoft Graph에 대한 액세스 토큰을 가져오는 단일 페이지 애플리케이션에 필요한 최소 코드를 보여줍니다.

<table>
<tr><td> ADAL.js 사용 </td><td> MSAL.js 사용 </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com",
            null, null,
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script
    type="text/javascript"
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>다음 단계

- [MSAL.js API 참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [MSAL.js 코드 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)
