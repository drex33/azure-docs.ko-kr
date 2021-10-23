---
title: '빠른 시작: Node.js 데스크톱 앱에서 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Node.js Electron 데스크톱 애플리케이션이 사용자를 로그인시키고 액세스 토큰을 가져와서 Microsoft ID 플랫폼 엔드포인트로 보호되는 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: KarenH444
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: karenhoran
ms.openlocfilehash: 362fdcddc9a8d58da30f269be44fc195870949e1
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163760"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>빠른 시작: Electron 데스크톱 앱에서 액세스 토큰을 얻고 Microsoft Graph API 호출

이 빠른 시작에서는 Electron 데스크톱 애플리케이션이 사용자를 로그인시키고 Microsoft Graph API를 호출할 액세스 토큰을 얻는 방법을 보여 주는 코드 샘플을 다운로드하고 실행합니다.

이 빠른 시작에서는 [PKCE를 사용한 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)과 함께 [Node.js용 Microsoft 인증 라이브러리(MSAL 노드)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>샘플 애플리케이션 등록 및 다운로드
>
> 시작하려면 아래의 단계를 따르세요.
>
> #### <a name="step-1-register-the-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `msal-node-desktop`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
> 1. **등록** 을 선택하여 애플리케이션을 만듭니다.
> 1. **관리** 에서 **인증** 을 선택합니다.
> 1. **플랫폼 추가** > **모바일 및 데스크톱 애플리케이션** 을 차례로 선택합니다.
> 1. **리디렉션 URI** 섹션에서 `msal://redirect`를 입력합니다.
> 1. **구성** 을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 회신 URL을 **msal://redirect** 로 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-windows-desktop/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-electron-sample-project"></a>2단계: Electron 샘플 프로젝트 다운로드

> [!div renderon="docs"]
> [코드 샘플 다운로드](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [코드 샘플 다운로드](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>3단계: Electron 샘플 프로젝트 구성
>
> 1. zip 파일을 디스크 루트에 가까운 로컬 폴더(예: *C:/Azure-Samples*)로 추출합니다.
> 1. *.env* 를 편집하고 `TENANT_ID` 및 `CLIENT_ID` 필드의 값을 다음 코드 조각으로 바꿉니다.
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    위치:
>    - `Enter_the_Application_Id_Here` - 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.
>    - `Enter_the_Tenant_Id_Here` - 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: contoso.microsoft.com)으로 바꿉니다.
>
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 의 값을 찾아보려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

이 샘플의 종속성을 한 번 설치해야 합니다.

```console
npm install
```

그런 다음, 명령 프롬프트 또는 콘솔을 통해 애플리케이션을 실행합니다.

```console
npm start
```

애플리케이션의 UI에 **로그인** 단추가 표시됩니다.

## <a name="about-the-code"></a>코드 정보

아래에서는 애플리케이션 예제의 몇 가지 중요한 측면에 대해 설명합니다.

### <a name="msal-node"></a>MSAL 노드

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)는 사용자를 로그인시키고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 라이브러리입니다. 데스크톱 앱에서 MSAL Node를 사용하는 방법에 대한 자세한 내용은 [이 문서](scenario-desktop-overview.md)를 참조하세요.

다음 npm 명령을 실행하여 MSAL Node를 설치할 수 있습니다.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL Node에 대한 참조를 추가할 수 있습니다.

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | 위치: |Description |
> |---------|---------|
> | `clientId` | Azure Portal에 등록된 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다. 이 값은 Azure Portal에서 앱의 **개요** 페이지에 있습니다. |
> | `authority`    | 사용자가 인증하는 STS 엔드포인트 일반적으로 퍼블릭 클라우드에 대한 `https://login.microsoftonline.com/{tenant}`입니다. 여기서 {tenant}는 테넌트의 이름 또는 테넌트 ID입니다.|

### <a name="requesting-tokens"></a>토큰 요청

PKCE를 사용한 권한 부여 코드 흐름의 첫 번째 레그에서 적절한 매개 변수를 사용하여 권한 부여 코드 요청을 준비하고 보냅니다. 그런 다음, 흐름의 두 번째 레그에서 권한 부여 코드 응답을 수신합니다. 코드를 가져온 후에는 코드를 교환하여 토큰을 가져옵니다.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

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
```

> |위치:| Description |
> |---------|---------|
> | `authWindow` | 현재 Electron 창이 처리 중입니다. |
> | `tokenRequest` | 요청된 범위(예: Microsoft Graph의 경우 `"User.Read"`, 사용자 지정 웹 API의 경우 `"api://<Application ID>/access_as_user"`)를 포함합니다. |

## <a name="next-steps"></a>다음 단계

MSAL Node를 사용한 Electron 데스크톱 앱 개발에 대해 자세히 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Electron 데스크톱 앱에서 사용자 로그인 및 Microsoft Graph API 호출](tutorial-v2-nodejs-desktop.md)