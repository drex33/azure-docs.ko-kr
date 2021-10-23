---
title: '자습서: Electron 데스크톱 앱에서 사용자 로그인 및 Microsoft Graph API 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 사용자를 로그인하고, 인증 코드 흐름을 사용하여 Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, Microsoft Graph API를 호출할 수 있는 Electron 데스크톱 앱을 빌드합니다.
services: active-directory
author: KarenH444
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: karenhoran
ms.openlocfilehash: 2036a0317aef3d383d065cdd2ad6143342ea1d91
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161859"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>자습서: Electron 데스크톱 앱에서 사용자 로그인 및 Microsoft Graph API 호출

이 자습서에서는 PKCE와 함께 권한 부여 코드 흐름을 사용하여 사용자를 로그인하고 Microsoft Graph를 호출하는 Electron 데스크톱 애플리케이션을 빌드합니다. 빌드한 데스크톱 앱은 [Node.js용 MSAL(Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)을 사용합니다.

다음을 수행하려면 이 자습서의 단계를 따릅니다.

> [!div class="checklist"]
> - Azure Portal에 애플리케이션 등록
> - Electron 데스크톱 앱 프로젝트 만들기
> - 앱에 인증 논리 추가
> - 메서드를 추가하여 웹 API 호출
> - 앱 등록 정보 추가
> - 앱 테스트

## <a name="prerequisites"></a>사전 요구 사항

- [Node.JS](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

## <a name="register-the-application"></a>애플리케이션 등록

먼저 [Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)의 단계를 완료하여 앱을 등록합니다.

앱 등록 시 다음 설정을 사용합니다.

- 이름: `ElectronDesktopApp`(권장)
- 지원되는 계정 유형: **모든 조직 디렉터리(모든 Azure AD 디렉터리 - 다중 테넌트)의 계정 및 개인 Microsoft 계정(예: Skype, Xbox)**
- 플랫폼 유형: **모바일 및 데스크톱 애플리케이션**
- 리디렉션 URI: `msal://redirect`

## <a name="create-the-project"></a>프로젝트 만들기

애플리케이션을 호스팅할 폴더를 만듭니다(예: *ElectronDesktopApp*).

1. 먼저, 터미널에서 프로젝트 디렉터리로 변경하고 다음 `npm` 명령을 실행합니다.

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. 그런 다음, *App* 이라는 폴더를 만듭니다. 이 폴더 내에 UI 역할을 할 *index.html* 이라는 파일을 만듭니다. 거기에 다음 코드를 추가합니다.

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
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

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. 다음으로, *main.js* 라는 파일을 만들고 다음 코드를 추가합니다.

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

위의 코드 조각에서 Electron 기본 창 개체를 초기화하고 Electron 창과의 상호 작용을 위한 이벤트 처리기를 몇 개 만듭니다. 또한 구성 매개 변수를 가져오고 로그인, 로그아웃 및 토큰 획득을 처리하기 위해 *authProvider* 클래스를 인스턴스화하고 Microsoft Graph API를 호출합니다.

4. 동일한 폴더(*App*)에서 *renderer.js* 라는 또 다른 파일을 만들고 다음 코드를 추가합니다.

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. 마지막으로 애플리케이션 **이벤트** 를 설명하는 문자열 상수를 저장할 *constants.js* 라는 파일을 만듭니다.

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

이제 Electron 앱에 대한 간단한 GUI 및 상호 작용이 생겼습니다. 자습서의 나머지 부분이 완료되면 프로젝트의 파일 및 폴더 구조는 다음과 같습니다.

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>앱에 인증 논리 추가

*App* 폴더에서 *AuthProvider.js* 라는 파일을 만듭니다. 여기에는 MSAL 노드를 사용하여 로그인, 로그아웃, 토큰 획득, 계정 선택 및 관련 인증 작업을 처리하는 인증 공급자 클래스가 포함됩니다. 거기에 다음 코드를 추가합니다.

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

위의 코드 조각에서 먼저 구성 개체(`msalConfig`)를 전달하여 MSAL 노드 `PublicClientApplication`을 초기화했습니다. 그런 다음, 기본 모듈(*main.js*)이 호출할 `login`, `logout` 및 `getToken` 메서드를 노출했습니다. `login` 및 `getToken`에서는 먼저 인증 코드를 요청한 다음, MSAL 노드 `acquireTokenByCode` 공용 API를 사용하여 이를 토큰과 교환하여 각각 ID와 액세스 토큰을 획득합니다.

## <a name="add-a-method-to-call-a-web-api"></a>메서드를 추가하여 웹 API 호출

*fetch.js* 라는 또 다른 파일을 만듭니다. 이 파일에는 Microsoft Graph API에 대한 REST 호출을 수행하기 위한 Axios HTTP 클라이언트가 포함됩니다.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>앱 등록 정보 추가

마지막으로, 토큰을 가져올 때 사용될 앱 등록 세부 정보를 저장할 환경 파일을 만듭니다. 이렇게 하려면 샘플(*ElectronDesktopApp*)의 루트 폴더 안에 *.env* 라는 파일을 만들고 다음 코드를 추가합니다.

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Azure 앱 등록 포털에서 얻은 값으로 이러한 세부 정보를 입력합니다.

- `Enter_the_Tenant_Id_here`는 다음 중 하나여야 합니다.
  - 애플리케이션에서 *이 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름** 으로 바꿉니다. 예들 들어 `contoso.microsoft.com`입니다.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정* 을 지원하는 경우 이 값을 `common`으로 바꿉니다.
  - *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 `consumers`로 바꿉니다.
- `Enter_the_Application_Id_Here`: 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.
- `Enter_the_Cloud_Instance_Id_Here`: 애플리케이션이 등록된 Azure 클라우드 인스턴스입니다.
  - 기본(또는 *글로벌*) Azure 클라우드의 경우 `https://login.microsoftonline.com/`을 입력합니다.
  - **국가별** 클라우드(예: 중국)의 경우 [국가별 클라우드](authentication-national-cloud.md)에서 적절한 값을 찾을 수 있습니다.
- `Enter_the_Graph_Endpoint_Here`는 애플리케이션과 통신해야 하는 Microsoft Graph API의 인스턴스입니다.
  - **글로벌** Microsoft Graph API 엔드포인트의 경우 이 문자열의 두 인스턴스를 모두 `https://graph.microsoft.com/`으로 바꿉니다.
  - **국가별** 클라우드 배포의 엔드포인트는 Microsoft Graph 설명서의 [국가별 클라우드 배포](/graph/deployments)를 참조하세요.

## <a name="test-the-app"></a>앱 테스트

애플리케이션 만들기가 완료되었으며 이제 Electron 데스크톱 앱을 시작하고 앱의 기능을 테스트할 준비가 되었습니다.

1. 프로젝트 폴더의 루트 내에서 다음 명령을 실행하여 앱을 시작합니다.

```console
electron App/main.js
```

2. 애플리케이션 기본 창에서 *index.html* 파일과 **로그인** 단추의 콘텐츠를 확인해야 합니다.

## <a name="test-sign-in-and-sign-out"></a>로그인 및 로그아웃 테스트

*index.html* 파일을 로드한 후 **로그인** 을 선택합니다. Microsoft ID 플랫폼으로 로그인하라는 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="로그인 프롬프트":::

요청한 권한에 동의하면 웹 애플리케이션에서 사용자 이름을 표시하여 성공적으로 로그인했음을 나타냅니다.

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="로그인 완료":::

## <a name="test-web-api-call"></a>웹 API 호출 테스트

로그인한 후에 **프로필 보기** 를 선택하여 Microsoft Graph API에 대한 호출에서 응답으로 반환된 사용자 프로필 정보를 확인합니다.

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Microsoft Graph의 프로필 정보":::

사용자 계정의 메시지를 보려면 **메일 읽기** 를 선택합니다. 동의 화면이 표시됩니다.

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="read.mail 권한에 대한 동의 화면":::

동의하면 Microsoft Graph API에 대한 호출의 응답으로 반환되는 메시지를 볼 수 있습니다.

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Microsoft Graph의 메일 정보":::

## <a name="how-the-application-works"></a>애플리케이션 작동 방식

사용자가 처음으로 **로그인** 단추를 선택하면 *AuthProvider.js* 의 get `getTokenInteractive` 메서드가 호출됩니다. 이 메서드는 사용자를 *Microsoft ID 플랫폼 엔드포인트* 로 로그인하도록 리디렉션하고 사용자의 자격 증명을 검사한 다음, **인증 코드** 를 얻습니다. 그런 다음, 이 코드는 MSAL 노드의 `acquireTokenByCode` 공용 API를 사용하여 액세스 토큰으로 교환됩니다.

이 시점에서 PKCE로 보호된 권한 부여 코드가 CORS로 보호된 토큰 엔드포인트로 보내져 토큰과 교환됩니다. 애플리케이션에서 ID 토큰, 액세스 토큰 및 새로 고침 토큰을 받고, MSAL 노드에서 처리하며, 토큰에 포함된 정보를 캐시합니다.

ID 토큰에는 사용자에 대한 기본 정보(예: 표시 이름)가 포함되어 있습니다. 액세스 토큰은 수명이 제한되어 있으며 24시간 후에 만료됩니다. 이러한 토큰을 보호된 리소스에 액세스하는 데 사용하려는 경우 백엔드 서버는 토큰이 애플리케이션의 유효한 사용자에게 발급되었음을 보장하기 위해 해당 토큰의 유효성을 *검사해야* 합니다.

이 자습서에서 만든 데스크톱 앱은 요청 헤더([RFC 6750](https://tools.ietf.org/html/rfc6750))의 전달자 토큰으로 액세스 토큰을 사용하여 Microsoft Graph API에 대한 REST 호출을 수행합니다.

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 Azure Portal에 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자의 이메일을 나열하기 위해 *Mail.Read* 범위가 필요합니다.

범위를 추가하면 추가된 범위에 대한 추가 동의를 제공하라는 메시지가 표시될 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 Node.js 및 Electron 데스크톱 애플리케이션 개발에 대해 자세히 알아보려면 여러 부분으로 구성된 시나리오 시리즈를 참조하세요.

> [!div class="nextstepaction"]
> [시나리오: Web API를 호출하는 데스크톱 앱](scenario-desktop-overview.md)
