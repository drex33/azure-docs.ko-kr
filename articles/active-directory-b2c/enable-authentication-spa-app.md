---
title: Azure Active Directory B2C 구성 요소를 사용하여 SPA 애플리케이션에서 인증을 사용하도록 설정
description: 이 문서에서는 SPA 애플리케이션에서 사용자를 로그인하고 가입하는 Azure Active Directory B2C 구성 요소에 대해 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 535cecf04857810d002843159d7ef3ce968e169b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041047"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 고객의 단일 페이지 애플리케이션에서 인증을 사용하도록 설정

이 문서에서는 고객의 SPA(단일 페이지 애플리케이션)에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 보여줍니다. [MSAL.js(JavaScript용 Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-js)를 사용하여 SPA 애플리케이션을 만드는 방법을 알아봅니다. 

이 문서를 [샘플 SPA 애플리케이션에서 인증 구성](./configure-authentication-sample-spa-app.md) 문서와 함께 사용하고, 샘플 SPA 앱을 고객의 SPA 앱으로 바꿉니다.

## <a name="overview"></a>개요

이 문서에서는 Node.js 및 [Express](https://expressjs.com/)를 사용하여 기본 Node.js 웹앱을 만듭니다. Express는 웹 및 모바일 애플리케이션을 위한 기능 세트를 제공하는 최소 규모의 유연한 Node.js 웹앱 프레임워크입니다.

[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 인증 라이브러리는 SPA 앱에 인증 및 권한 부여 지원을 간편하게 추가할 수 있도록 Microsoft에서 제공하는 라이브러리입니다.

> [!TIP]
> 전체 MSAL.js 코드는 클라이언트 쪽에서 실행됩니다. Node.js 및 Express 서버 쪽 코드를 .NET Core, Java 및 하이퍼텍스트 전처리기(PHP) 스크립팅 언어와 같은 다른 솔루션으로 대체할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

필수 구성 요소 및 통합 지침을 검토하려면 [샘플 SPA 애플리케이션에서 인증 구성](configure-authentication-sample-spa-app.md)을 참조하세요.

## <a name="step-1-create-an-spa-app-project"></a>1단계: SPA 앱 프로젝트 만들기

기존 SPA 앱 프로젝트를 사용해도 되고 새로 만들어도 됩니다. 새 프로젝트를 만들려면 다음을 수행합니다.

1. 명령 셸을 열고 새 디렉터리(예: *myApp*)를 만듭니다. 이 디렉터리에는 앱 코드, 사용자 인터페이스 및 구성 파일이 포함될 것입니다.

1. 만든 디렉터리를 입력합니다.

1. `npm init` 명령을 사용하여 앱의 `package.json` 파일을 만듭니다. 이 명령은 앱에 대한 정보(예: 앱의 이름 및 버전, 초기 진입점의 이름, *index.js* 파일)를 묻는 메시지를 표시합니다. 다음 명령을 실행하고 기본값을 적용합니다.

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>2단계: 종속 요소 설치

Express 패키지를 설치하려면 명령 셸에서 다음 명령을 실행합니다.

```
npm install express
```

앱의 정적 파일을 찾기 위해 서버 쪽 코드는 [Path](https://www.npmjs.com/package/path) 패키지를 사용합니다. 

Path 패키지를 설치하려면 명령 셸에서 다음 명령을 실행합니다.

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>3단계: 웹 서버 구성

*myApp* 폴더에 *index.js* 라는 파일을 만듭니다. 이 파일에는 다음 코드가 포함되어 있습니다.

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>4단계: SPA 사용자 인터페이스 만들기

SAP 앱 `index.html` 파일을 추가합니다. 이 파일은 부트스트랩 프레임워크를 사용하여 빌드된 사용자 인터페이스를 구현하며 구성, 인증 및 웹 API 호출에 사용되는 스크립트 파일을 가져옵니다.

*index.html* 파일에서 참조하는 리소스는 다음 표에 자세히 설명되어 있습니다. 

|참조 |정의|
|---|---|
|MSAL.js 라이브러리| MSAL.js 인증 JavaScript 라이브러리 [CDN 경로](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md).|
|[부트스트랩 스타일시트](https://getbootstrap.com/) | 더 빠르고 쉬운 웹 개발을 위한 무료 프런트 엔드 프레임워크입니다. 이 프레임워크에는 HTML 기반 및 CSS 기반 디자인 템플릿이 포함되어 있습니다. |
|[policies.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js) | Azure AD B2C 사용자 지정 정책 및 사용자 흐름이 포함되어 있습니다. |
|[authConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js) | 인증 구성 매개 변수가 포함되어 있습니다.|
|[authRedirect.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | 인증 논리가 포함되어 있습니다. |
|[apiConfig.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | 웹 API 범위와 API 엔드포인트 위치가 포함되어 있습니다. |
|[api.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | API를 호출하고 응답을 처리하는 데 사용할 메서드를 정의합니다.|
|[ui.js](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | UI 요소를 제어합니다. |
| | |

SPA 인덱스 파일을 렌더링하려면 *myApp* 폴더에 *index.html* 이라는 파일을 만듭니다. 이 파일에는 다음 HTML 코드 조각이 포함되어 있습니다.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>5단계: 인증 라이브러리 구성

MSAL.js 라이브러리가 Azure AD B2C와 통합되는 방법을 구성합니다. MSAL.js 라이브러리는 공통 구성 개체를 사용하여 Azure AD B2C 테넌트의 인증 엔드포인트에 연결합니다.

인증 라이브러리를 구성하려면 다음을 수행합니다.

1. *myApp* 폴더에 *App* 이라는 새 폴더를 만듭니다. 
1. *App* 폴더 안에 *authConfig.js* 라는 새 파일을 만듭니다.
1. 다음 JavaScript 코드를 *authConfig.js* 파일에 추가합니다.

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. `<Application-ID>`를 해당하는 앱 등록 애플리케이션 ID로 바꿉니다. 자세한 내용은 [샘플 SPA 애플리케이션에서 인증 구성](./configure-authentication-sample-spa-app.md#step-23-register-the-spa)을 참조하세요.

> [!TIP]
> MSAL 개체 구성 옵션에 대한 자세한 내용은 [인증 옵션](./enable-authentication-spa-app-options.md) 문서를 참조하세요.

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>6단계: Azure AD B2C 사용자 흐름 지정

Azure AD B2C 환경에 대한 정보를 제공하는 *policies.js* 파일을 만듭니다. MSAL.js 라이브러리는 이 정보를 사용하여 Azure AD B2C에 대한 인증 요청을 만듭니다.

Azure AD B2C 사용자 흐름을 지정하려면 다음을 수행합니다.

1. *App* 폴더 안에 *policies.js* 라는 새 파일을 만듭니다.
1. 다음 코드를 *policies.js* 파일에 추가합니다.

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. `B2C_1_SUSI`를 해당하는 로그인 Azure AD B2C 정책 이름으로 바꿉니다.
1. `B2C_1_EditProfile`을 해당하는 프로필 편집 Azure AD B2C 정책 이름으로 바꿉니다.
1. `contoso`의 모든 인스턴스를 해당하는 [Azure AD B2C 테넌트 이름](./tenant-management.md#get-your-tenant-name)으로 바꿉니다.

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>7단계: MSAL을 사용하여 사용자 로그인

이 단계에서는 로그인 흐름, API 액세스 토큰 획득 및 로그아웃 메서드를 초기화하는 메서드를 구현합니다. 

자세한 내용은 [MSAL PublicClientApplication 클래스 참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) 및 [사용자 로그인에 MSAL(Microsoft 인증 라이브러리) 사용](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user) 문서를 참조하세요.

사용자를 로그인하려면 다음을 수행합니다.

1. *App* 폴더 안에 *authRedirect.js* 라는 새 파일을 만듭니다.
1. 다음 코드를 복사하여 *authRedirect.json* 파일에 붙여넣습니다.

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>8단계: 웹 API 위치 및 범위 구성

SPA 앱이 웹 API를 호출할 수 있도록 허용하려면 웹 API에 대한 액세스 권한을 부여하는 데 사용할 웹 API 엔드포인트 위치 및 [범위](./configure-authentication-sample-spa-app.md#app-registration-overview)를 제공합니다.

웹 API 위치 및 범위를 구성하려면 다음을 수행합니다.

1. *App* 폴더 안에 *apiConfig.js* 라는 새 파일을 만듭니다.
1. 다음 코드를 복사하여 *apiConfig.json* 파일에 붙여넣습니다.

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. `contoso`를 해당하는 테넌트 이름으로 바꿉니다. 요청된 범위 이름은 [범위 구성](./configure-authentication-sample-spa-app.md#step-22-configure-scopes) 문서에 설명된 방법으로 찾을 수 있습니다.
1. `webApi` 값을 해당하는 웹 API 엔드포인트 위치로 바꿉니다.

## <a name="step-9-call-your-web-api"></a>9단계: 웹 API 호출

API 엔드포인트에 대한 HTTP 요청을 정의합니다. HTTP 요청은 *MSAL.js* 를 사용하여 획득한 액세스 토큰을 요청의 `Authorization` HTTP 헤더로 전달하도록 구성됩니다.

다음 코드는 API 엔드포인트에 대한 HTTP `GET` 요청을 정의하고, `Authorization` HTTP 헤더 내에서 액세스 토큰을 전달합니다. API 위치는 *apiConfig.js* 의 `webApi` 키로 정의됩니다. 

획득한 토큰을 사용하여 웹 API를 호출하려면 다음을 수행합니다.

1. *App* 폴더 안에 *api.js* 라는 새 파일을 만듭니다.
1. 다음 코드를 *api.js* 파일에 추가합니다.

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>10단계: UI 요소 참조 추가

SPA 앱은 JavaScript를 사용하여 UI 요소를 제어합니다. 예를 들어 로그인 및 로그아웃 단추를 표시하고, 사용자 ID 토큰 클레임을 화면에 렌더링합니다.

UI 요소 참조를 추가하려면 다음을 수행합니다.

1. *App* 폴더 안에 *ui.js* 라는 파일을 만듭니다.
1. 다음 코드를 *ui.js* 파일에 추가합니다.

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>11단계: SPA 애플리케이션 실행

명령 셸에서 다음 명령을 실행합니다.

``` powershell
npm install  
npm ./index.js
```

1. https://localhost:6420로 이동합니다. 
1. **로그인** 을 선택합니다.
1. 가입 또는 로그인 프로세스를 완료합니다.

성공적으로 인증되면 구문 분석된 ID 토큰이 화면에 표시됩니다. `Call API`를 선택하여 API 엔드포인트를 호출합니다.

## <a name="next-steps"></a>다음 단계

* [코드 샘플](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)에 대해 자세히 알아봅니다.
* [Azure AD B2C를 사용하여 SPA 애플리케이션에서 인증 옵션](enable-authentication-spa-app-options.md)을 구성합니다.
* [고유 웹 API에서 인증을 사용하도록 설정](enable-authentication-web-api.md)합니다.
