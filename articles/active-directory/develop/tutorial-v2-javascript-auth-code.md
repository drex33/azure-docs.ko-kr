---
title: '자습서: 인증 코드 흐름을 사용하는 JavaScript 단일 페이지 앱 만들기 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 사용자를 로그인하고, 인증 코드 흐름을 사용하여 Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, Microsoft Graph API를 호출할 수 있는 JavaScript SPA를 만듭니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 10/12/2021
ms.author: marsma
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: ffd447d4fcacad1185d8ad95778e0a9d22cb16ce
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066018"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>자습서: 권한 부여 코드 흐름을 사용하여 사용자 로그인 및 JavaScript SPA(단일 페이지 앱)에서 Microsoft Graph API 호출

이 자습서에서는 PKCE와 함께 권한 부여 코드 흐름을 사용하여 사용자를 로그인하고 Microsoft Graph를 호출하는 JavaScript SPA(단일 페이지 애플리케이션)를 빌드합니다. 빌드된 SPA는 JavaScript v2.0용 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

이 자습서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * PKCE를 사용하여 OAuth 2.0 권한 부여 코드 흐름 수행
> * 회사 및 학교 계정과 개인 Microsoft 계정에 로그인
> * 액세스 토큰 획득
> * Microsoft ID 플랫폼에서 가져온 액세스 토큰이 필요한 Microsoft Graph 또는 사용자 고유의 API 호출

MSAL.js 2.0은 암시적 권한 부여 흐름 대신 브라우저의 권한 부여 코드 흐름을 지원하여 MSAL.js 1.0에서 향상되었습니다. MSAL.js 2.0은 암시적 흐름을 **지원하지 않습니다**.

## <a name="prerequisites"></a>필수 구성 요소

* [Node.js](https://nodejs.org/en/download/)(로컬 웹 서버 실행용)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

## <a name="how-the-tutorial-app-works"></a>자습서 앱의 작동 방식

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="단일 페이지 애플리케이션의 권한 부여 코드 흐름을 보여 주는 다이어그램":::

이 자습서에서 만든 애플리케이션을 사용하면 JavaScript SPA에서 Microsoft ID 플랫폼으로부터 보안 토큰을 획득하여 Microsoft Graph API를 쿼리할 수 있습니다. 이 시나리오에서는 사용자가 로그인한 후에 액세스 토큰이 요청되고 권한 부여 헤더의 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 JavaScript용 Microsoft 인증 라이브러리(MSAL.js)에서 처리됩니다.

이 자습서에서는 다음 라이브러리를 사용합니다.

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) JavaScript용 Microsoft 인증 라이브러리 v2.0 브라우저 패키지

## <a name="get-the-completed-code-sample"></a>완성된 코드 샘플 가져오기

대신 이 자습서의 완성된 샘플 프로젝트를 다운로드하는 것이 좋습니다. [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) 리포지토리를 복제합니다. 

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

로컬 개발 환경에서 다운로드한 프로젝트를 실행하려면 [프로젝트 만들기](#create-your-project)의 1단계에 설명된 대로 애플리케이션에 대한 localhost 서버를 만들어 시작합니다. 완료되면 [구성 단계](#register-your-application)로 건너뛰어 코드 샘플을 구성할 수 있습니다.

자습서를 계속 진행하여 애플리케이션을 직접 빌드하려면 다음 섹션인 [프로젝트 만들기](#create-your-project)로 이동합니다.

## <a name="create-your-project"></a>프로젝트 만들기

[Node.js](https://nodejs.org/en/download/)가 설치되었으면 애플리케이션을 호스팅할 폴더를 만듭니다(예: *msal-spa-tutorial*).

다음으로, *index.html* 파일을 제공하기 위해 작은 [Express](https://expressjs.com/) 웹 서버를 구현합니다.

1. 먼저, 터미널에서 프로젝트 디렉터리로 변경하고 다음 `npm` 명령을 실행합니다.
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. 다음으로, *server.js* 라는 파일을 만들고 다음 코드를 추가합니다.

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

이제 SPA를 제공하는 작은 웹 서버가 있습니다. 자습서의 나머지 부분이 완료되면 프로젝트의 파일 및 폴더 구조는 다음과 같습니다.

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>SPA UI 만들기

1. *app* 폴더를 프로젝트 디렉터리에 만들고, 이 폴더에서 JavaScript SPA에 대한 *index.html* 파일을 만듭니다. 이 파일은 **Bootstrap 4 Framework** 를 사용하여 빌드된 UI를 구현하고 구성, 인증 및 API 호출을 위한 스크립트 파일을 가져옵니다.

    *index.html* 파일에서 다음 코드를 추가합니다.

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
          </div>
        </div>
        </div>
        <br>
        <br>
          <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
          </div>
          <div class="col-md-5">
            <div class="tab-content" id="nav-tabContent">
            </div>
          </div>
        </div>
        <br>
        <br>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. 다음으로, *app* 폴더에서 *ui.js* 라는 파일도 만들고 다음 코드를 추가합니다. 이 파일은 DOM 요소에 액세스하고 업데이트합니다.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>애플리케이션 등록

[단일 페이지 애플리케이션: 앱 등록](scenario-spa-app-registration.md)의 단계에 따라 SPA에 대한 앱 등록을 만듭니다.

[리디렉션 URI: MSAL.js 2.0(권한 부여 코드 흐름 포함)](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) 단계에서 이 자습서의 애플리케이션이 실행되는 기본 위치인 `http://localhost:3000`을 입력합니다.

다른 포트를 사용하려면 `http://localhost:<port>`를 입력합니다. 여기서 `<port>`는 기본 설정 TCP 포트 번호입니다. `3000` 이외의 포트 번호를 지정하는 경우 *server.js* 도 기본 설정 포트 번호로 업데이트합니다.

### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

인증에 대한 구성 매개 변수를 포함할 *authConfig.js* 라는 파일을 *app* 폴더에 만들고, 다음 코드를 추가합니다.

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

여기에 설명한 대로 `msalConfig` 섹션에서 값을 수정합니다.

- `Enter_the_Application_Id_Here`: 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.
- `Enter_the_Cloud_Instance_Id_Here`: 애플리케이션이 등록된 Azure 클라우드 인스턴스입니다.
  - 기본(또는 *글로벌*) Azure 클라우드의 경우 `https://login.microsoftonline.com`을 입력합니다.
  - **국가별** 클라우드(예: 중국)의 경우 [국가별 클라우드](authentication-national-cloud.md)에서 적절한 값을 찾을 수 있습니다.
- `Enter_the_Tenant_info_here`는 다음 중 하나여야 합니다.
  - 애플리케이션에서 *이 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름** 으로 바꿉니다. 예들 들어 `contoso.microsoft.com`입니다.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정* 을 지원하는 경우 이 값을 `common`으로 바꿉니다.
  - *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 `consumers`로 바꿉니다.
- `Enter_the_Redirect_Uri_Here`은 `http://localhost:3000`입니다.

글로벌 Azure 클라우드를 사용하는 경우 *authConfig.js* 의 `authority` 값은 다음과 비슷해야 합니다.

```javascript
authority: "https://login.microsoftonline.com/common",
```

여전히 *app* 폴더에서 *graphConfig.js* 라는 파일을 만듭니다. 다음 코드를 추가하여 Microsoft Graph API를 호출하기 위한 구성 매개 변수를 애플리케이션에 제공합니다.

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

여기서 설명한 대로 `graphConfig` 섹션의 값을 수정합니다.

- `Enter_the_Graph_Endpoint_Here`는 애플리케이션과 통신해야 하는 Microsoft Graph API의 인스턴스입니다.
  - **글로벌** Microsoft Graph API 엔드포인트의 경우 이 문자열의 두 인스턴스를 모두 `https://graph.microsoft.com`으로 바꿉니다.
  - **국가별** 클라우드 배포의 엔드포인트는 Microsoft Graph 설명서의 [국가별 클라우드 배포](/graph/deployments)를 참조하세요.

글로벌 엔드포인트를 사용하는 경우 *graphConfig.js* 의 `graphMeEndpoint` 및 `graphMailEndpoint` 값은 다음과 비슷해야 합니다.

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>MSAL(Microsoft 인증 라이브러리)을 사용하여 사용자 로그인

### <a name="pop-up"></a>팝업

*app* 폴더에서 *authPopup.js* 라는 파일을 만들고, 로그인 팝업에 대한 다음 인증 및 토큰 획득 코드를 추가합니다.

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>리디렉션

*app* 폴더에서 *authRedirect.js* 라는 파일을 만들고, 로그인 리디렉션에 대한 다음 인증 및 토큰 획득 코드를 추가합니다.

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>코드의 작동 방식

사용자가 **로그인** 단추를 처음 선택하면 `signIn` 메서드에서 `loginPopup`을 호출하여 사용자를 로그인합니다. `loginPopup` 메서드는 *Microsoft ID 플랫폼 엔드포인트* 가 있는 팝업 창을 열어 사용자의 자격 증명을 묻고 유효성을 검사합니다. 로그인에 성공하면 *msal.js* 에서 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 시작합니다.

이 시점에서 PKCE로 보호된 권한 부여 코드가 CORS로 보호된 토큰 엔드포인트로 보내져 토큰과 교환됩니다. 애플리케이션에서 ID 토큰, 액세스 토큰 및 새로 고침 토큰을 받고, *msal.js* 에서 처리하며, 토큰에 포함된 정보를 캐시합니다.

ID 토큰에는 사용자에 대한 기본 정보(예: 표시 이름)가 포함되어 있습니다. ID 토큰에서 제공하는 데이터를 사용하려는 경우 토큰이 애플리케이션의 유효한 사용자에게 발급되었는지 확인하기 위해 백 엔드 서버에서 *유효성을 검사해야 합니다*.

액세스 토큰은 수명이 제한되어 있으며 24시간 후에 만료됩니다. 새로 고침 토큰을 사용하여 새 액세스 토큰을 자동으로 가져올 수 있습니다.

이 자습서에서 만든 SPA는 `acquireTokenSilent` 및/또는 `acquireTokenPopup`을 호출하여 사용자 프로필 정보에 대한 Microsoft Graph API를 쿼리하는 데 사용되는 *액세스 토큰* 을 가져옵니다. ID 토큰의 유효성을 검사하는 샘플이 필요하면 GitHub의 [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) 애플리케이션 샘플을 참조하세요. 이 샘플에서는 ASP.NET 웹 API를 토큰 유효성 검사에 사용합니다.

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

처음 로그인한 후에 보호된 리소스에 액세스해야 할 때마다(즉, 토큰을 요청하기 위해) 사용자에게 재인증하도록 요청하지 않아야 합니다. 이러한 재인증 요청을 방지하려면 `acquireTokenSilent`를 호출합니다. 그러나 사용자가 Microsoft ID 플랫폼과 상호 작용하도록 강제로 적용해야 하는 경우도 있습니다. 다음은 그 예입니다.

- 암호가 만료되어 사용자가 해당 자격 증명을 다시 입력해야 합니다.
- 애플리케이션에서 리소스에 액세스하도록 요청하고 있으며 사용자의 동의가 필요합니다.
- 2단계 인증이 필요합니다.

`acquireTokenPopup`을 호출하면 팝업 창이 열리거나 `acquireTokenRedirect`에서 사용자를 Microsoft ID 플랫폼으로 리디렉션합니다. 이 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 동의하거나, 2단계 인증을 수행하여 상호 작용해야 합니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginPopup`(또는 `loginRedirect`)이 처음 실행되면 `acquireTokenSilent`가 이후 호출에서 보호된 리소스에 액세스하는 데 사용되는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다. (토큰을 요청하거나 갱신하기 위한 호출은 자동으로 수행됩니다.) `acquireTokenSilent`는 경우에 따라 실패할 수 있습니다. 예를 들어 사용자의 암호가 만료되었을 수 있습니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1. 즉시 `acquireTokenPopup`을 호출하여 사용자 로그인 프롬프트를 트리거합니다. 이 패턴은 애플리케이션에 사용자가 사용할 수 있는 인증되지 않은 콘텐츠가 없는 온라인 애플리케이션에서 일반적으로 사용됩니다. 이 설정 안내에서 생성하는 예제는 이 패턴을 사용합니다.
1. 사용자가 로그인에 적절한 시간을 선택하거나 나중에 애플리케이션에서 `acquireTokenSilent`를 다시 시도할 수 있도록 대화형 로그인이 필요하다는 것을 사용자에게 시각적으로 표시합니다. 이 기술은 일반적으로 사용자가 중단 없이 애플리케이션의 다른 기능을 사용할 수 있는 경우에 사용됩니다. 예를 들어 애플리케이션에서 사용할 수 있는 인증되지 않은 콘텐츠가 있을 수 있습니다. 이 경우 사용자는 보호된 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인하려는 시기를 결정할 수 있습니다.

> [!NOTE]
> 이 자습서에서는 기본적으로 `loginPopup` 및 `acquireTokenPopup` 메서드를 사용합니다. Internet Explorer를 사용하는 경우 Internet Explorer 및 팝업 창에 대한 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)로 인해 `loginRedirect` 및 `acquireTokenRedirect` 방법을 사용하는 것이 좋습니다. 리디렉션 메서드를 사용하여 동일한 결과를 얻는 예제는 GitHub의 [*authRedirect.js*](https://github.com/Azure-Samples/ms-identity-javascript-v2/blob/master/app/authRedirect.js)를 참조하세요.

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출

*app* 폴더에서 *graph.js* 라는 파일을 만들고, Microsoft Graph API에 대한 REST 호출을 수행하는 다음 코드를 추가합니다.

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

이 자습서에서 만든 샘플 애플리케이션에서 `callMSGraph()` 메서드는 토큰을 요구하는 보호된 리소스에 대한 HTTP `GET` 요청을 수행하는 데 사용됩니다. 그러면 요청에서 콘텐츠를 호출자에 반환합니다. 이 메서드는 *HTTP 인증 헤더* 에 획득된 토큰을 추가합니다. 이 자습서에서 만든 샘플 애플리케이션에서 보호된 리소스는 로그인한 사용자의 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.

## <a name="test-your-application"></a>애플리케이션 테스트

애플리케이션 만들기가 완료되었으며 이제 Node.js 웹 서버를 시작하고 앱의 기능을 테스트할 준비가 되었습니다.

1. 프로젝트 폴더의 루트 내에서 다음 명령을 실행하여 Node.js 웹 서버를 시작합니다.

   ```console
   npm start
   ```
1. 브라우저에서 `http://localhost:3000` 또는 `http://localhost:<port>`로 이동합니다. 여기서 `<port>`는 웹 서버에서 수신 대기하는 포트입니다. *index.html* 파일과 **로그인** 단추의 내용을 확인해야 합니다.

### <a name="sign-in-to-the-application"></a>애플리케이션에 로그인합니다.

브라우저에서 *index.html* 파일이 로드되면 **로그인** 을 선택합니다. Microsoft ID 플랫폼으로 로그인하라는 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="로그인 대화 상자가 표시된 웹 브라우저":::

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음 로그인하면 프로필에 대한 액세스 권한을 부여하고 로그인하라는 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="웹 브라우저에 표시된 콘텐츠 대화 상자":::

요청한 권한에 동의하면 웹 애플리케이션에서 사용자 이름을 표시하여 성공적으로 로그인했음을 나타냅니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="웹 브라우저에서 성공적으로 로그인한 결과":::

### <a name="call-the-graph-api"></a>Graph API 호출

로그인한 후에 **프로필 보기** 를 선택하여 Microsoft Graph API에 대한 호출에서 응답으로 반환된 사용자 프로필 정보를 확인합니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="브라우저에 표시된 Microsoft Graph의 프로필 정보":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 Azure Portal에 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자의 이메일을 나열하기 위해 *Mail.Read* 범위가 필요합니다.

범위를 추가하면 추가된 범위에 대한 추가 동의를 제공하라는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요하지 않은 경우(추천되지 않음) 호출에서 `clientId`를 범위로 사용하여 토큰을 획득할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 JavaScript 단일 페이지 애플리케이션 개발에 대해 자세히 알아보려면 여러 부분으로 구성된 시나리오 시리즈를 참조하세요.

> [!div class="nextstepaction"]
> [시나리오: 단일 페이지 애플리케이션](scenario-spa-overview.md)
