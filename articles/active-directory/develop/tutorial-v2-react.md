---
title: '자습서: 인증 코드 흐름을 사용하는 React 단일 페이지 앱 만들기 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 사용자를 로그인하고, 인증 코드 흐름을 사용하여 Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, Microsoft Graph API를 호출할 수 있는 React SPA를 만듭니다.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/16/2021
ms.author: jamesmantu
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: e3481ee896e6caed33bdf7e59c64307a88136fde
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993656"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-react-single-page-app-spa-using-auth-code-flow"></a>자습서: 권한 부여 코드 흐름을 사용하여 사용자 로그인 및 React SPA(단일 페이지 앱)에서 Microsoft Graph API 호출

이 자습서에서는 PKCE와 함께 인증 코드 흐름을 사용하여 사용자를 로그인하고 Microsoft Graph를 호출하는 React SPA(단일 페이지 앱)를 빌드합니다. 빌드된 SPA는 React용 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

이 자습서에서:
> [!div class="checklist"]
> * `npm`를 사용하여 React 프로젝트 만들기
> * Azure Portal에 애플리케이션 등록
> * 사용자 로그인 및 로그아웃을 지원하는 코드 추가
> * Microsoft Graph API를 호출하는 코드 추가
> * 앱 테스트

MSAL React는 암시적 허용 흐름 대신 브라우저의 인증 코드 흐름을 지원합니다. MSAL React는 암시적 흐름을 지원하지 **않습니다**.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.js](https://nodejs.org/en/download/)(로컬 웹 서버 실행용)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

## <a name="how-the-tutorial-app-works"></a>자습서 앱의 작동 방식

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="단일 페이지 애플리케이션의 권한 부여 코드 흐름을 보여 주는 다이어그램":::

이 자습서에서 만든 애플리케이션을 사용하면 React SPA에서 Microsoft ID 플랫폼으로부터 보안 토큰을 획득하여 Microsoft Graph API를 쿼리할 수 있습니다. 애플리케이션은 MSAL.js v2 라이브러리의 래퍼인 React에 MSAL(Microsoft 인증 라이브러리)을 사용합니다. MSAL React를 사용하면 React 16 이상 애플리케이션에서 Azure AD(Azure Active Directory)를 사용하여 엔터프라이즈 사용자를 인증하고 Microsoft 계정 사용자 및 소셜 ID(Facebook, Google, LinkedIn 등)를 사용하여 사용자를 인증할 수 있습니다. 또한 이 라이브러리를 사용하면 애플리케이션에서 Microsoft 클라우드 서비스 및 Microsoft Graph에 액세스할 수 있습니다.

이 시나리오에서는 사용자가 로그인한 후에 액세스 토큰이 요청되고 권한 부여 헤더의 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 React용 Microsoft 인증 라이브러리(MSAL React)에서 처리됩니다.

### <a name="libraries"></a>라이브러리

이 자습서에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)|JavaScript React 래퍼용 Microsoft 인증 라이브러리|
|[MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|JavaScript v2 브라우저 패키지용 Microsoft 인증 라이브러리|

## <a name="get-the-completed-code-sample"></a>완성된 코드 샘플 가져오기

대신 이 자습서의 완성된 샘플 프로젝트를 다운로드하는 것이 좋습니다. Node.js와 같은 로컬 웹 서버를 사용하여 프로젝트를 실행하려면 [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) 리포지토리를 복제합니다.

`git clone https://github.com/Azure-Samples/ms-identity-javascript-react-spa`

그런 다음, 코드 샘플을 실행하기 전에 구성하려면 [구성 단계](#register-your-application)로 건너뜁니다.

자습서를 계속 진행하여 애플리케이션을 직접 빌드하려면 다음 [사전 요구 사항](#prerequisites) 섹션으로 이동합니다.

## <a name="create-your-project"></a>프로젝트 만들기

[Node.js](https://nodejs.org/en/download/)가 설치되면 터미널 창을 열고 다음 명령을 실행합니다.

```console
npx create-react-app msal-react-tutorial # Create a new React app
cd msal-react-tutorial # Change to the app directory
npm install @azure/msal-browser @azure/msal-react # Install the MSAL packages
npm install react-bootstrap bootstrap # Install Bootstrap for styling
```

이제 [React 앱 만들기](https://create-react-app.dev/docs/getting-started)를 사용하여 작은 React 프로젝트를 부트스트랩했습니다. 이 자습서의 나머지 부분이 빌드될 시작점이 됩니다. 이 자습서를 진행하는 동안 앱의 변경 내용을 확인하려면 다음 명령을 실행합니다. 

```console
npm start
```

앱에 브라우저 창이 자동으로 열립니다. 그렇지 않을 경우 브라우저를 열고 http://localhost:3000 로 이동합니다. 업데이트된 코드가 있는 파일을 저장할 때마다 변경 내용을 반영하도록 페이지가 다시 로드됩니다.

## <a name="register-your-application"></a>애플리케이션 등록

[단일 페이지 애플리케이션: 앱 등록](./scenario-spa-app-registration.md)의 단계에 따라 Azure portal을 사용하여 SPA에 대한 앱 등록을 만듭니다.

[리디렉션 URI: MSAL.js 2.0(권한 부여 코드 흐름 포함)](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) 단계에서 create-react-app이 애플리케이션을 제공하는 기본 위치인 `http://localhost:3000`을 입력합니다.

### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1. 인증에 대한 구성 매개 변수를 포함할 *authConfig.js* 라는 파일을 *src* 폴더에 만들고, 다음 코드를 추가합니다.

    ```javascript
    export const msalConfig = {
      auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here", // This is a URL (e.g. https://login.microsoftonline.com/{your tenant ID})
        redirectUri: "Enter_the_Redirect_Uri_Here",
      },
      cache: {
        cacheLocation: "sessionStorage", // This configures where your cache will be stored
        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      }
    };
    
    // Add scopes here for ID token to be used at Microsoft identity platform endpoints.
    export const loginRequest = {
     scopes: ["User.Read"]
    };
    
    // Add the endpoints here for Microsoft Graph API services you'd like to use.
    export const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me"
    };
    ```

1. 여기에 설명한 대로 `msalConfig` 섹션에서 값을 수정합니다.
    
    |값 이름| 정보|
    |----------|------|
    |`Enter_the_Application_Id_Here`| 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.|
    |`Enter_the_Cloud_Instance_Id_Here`| 애플리케이션이 등록된 Azure 클라우드 인스턴스입니다. 기본(또는 *글로벌*) Azure 클라우드의 경우 `https://login.microsoftonline.com`을 입력합니다. **국가별** 클라우드(예: 중국)의 경우 [국가별 클라우드](authentication-national-cloud.md)에서 적절한 값을 찾을 수 있습니다.
    |`Enter_the_Tenant_Info_Here`| 다음 옵션 중 하나로 설정합니다. 애플리케이션이 *이 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 디렉터리(테넌트) ID 또는 테넌트 이름(예: **contoso.microsoft.com**)으로 바꿉니다. 애플리케이션이 *모든 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 **organizations** 으로 바꿉니다. 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정* 을 지원하는 경우 이 값을 **common** 으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers** 로 바꿉니다. |
    |`Enter_the_Redirect_Uri_Here`|**http://localhost:3000** 으로 바꿉니다.|
    |`Enter_the_Graph_Endpoint_Here`| 애플리케이션과 통신해야 하는 Microsoft Graph API의 인스턴스입니다. **글로벌** Microsoft Graph API 엔드포인트의 경우 이 문자열의 두 인스턴스를 모두 `https://graph.microsoft.com`으로 바꿉니다. **국가별** 클라우드 배포의 엔드포인트는 Microsoft Graph 설명서의 [국가별 클라우드 배포](/graph/deployments)를 참조하세요.|
    
    사용할 수 있는 구성 가능한 옵션에 대한 자세한 내용은 [클라이언트 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

1. *src/index.js* 파일을 열고 다음 가져오기를 추가합니다.

    ```javascript
    import "bootstrap/dist/css/bootstrap.min.css";
    import { PublicClientApplication } from "@azure/msal-browser";
    import { MsalProvider } from "@azure/msal-react";
    import { msalConfig } from "./authConfig";
    ```

1. *src/index.js* 가져오기에서 1단계의 구성을 사용하여 `PublicClientApplication` 인스턴스를 만듭니다.

    ```javascript
    const msalInstance = new PublicClientApplication(msalConfig);
    ``` 

1. *src/index.js* 에서 `<App />` 구성 요소를 찾아 `MsalProvider` 구성 요소에 래핑합니다. 렌더링 함수는 다음과 같아야 합니다.

    ```jsx
    ReactDOM.render(
        <React.StrictMode>
            <MsalProvider instance={msalInstance}>
                <App />
            </MsalProvider>
        </React.StrictMode>,
        document.getElementById("root")
    );
    ``` 


## <a name="sign-in-users"></a>사용자 로그인

*src에* *components* 라는 폴더를 만들고 폴더 내에 *SignInButton.jsx* 라는 파일을 만듭니다. 팝업 창 또는 전체 프레임 리디렉션을 사용하여 로그인을 호출하려면 다음 섹션 중 하나의 코드를 추가합니다.

### <a name="sign-in-using-popups"></a>팝업을 사용하여 로그인

*src/components/SignInButton.jsx* 에 다음 코드를 추가하여 선택 시 팝업 로그인을 호출하는 단추 구성 요소를 만듭니다.

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginPopup(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for login
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Popup</Button>
    );
}
```

### <a name="sign-in-using-redirects"></a>리디렉션을 사용하여 로그인

*src/components/SignInButton.jsx* 에 다음 코드를 추가하여 선택 시 리디렉션 로그인을 호출하는 단추 구성 요소를 만듭니다.

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import { loginRequest } from "../authConfig";
import Button from "react-bootstrap/Button";

function handleLogin(instance) {
    instance.loginRedirect(loginRequest).catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the login prompt
 */
export const SignInButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogin(instance)}>Sign in using Redirect</Button>
    );
}
```

### <a name="add-the-sign-in-button"></a>로그인 단추 추가

1. *구성 요소* 폴더에 *PageLayout.jsx* 라는 또 다른 파일을 만들고 다음 코드를 추가하여 방금 만든 로그인 단추를 포함하는 navbar 구성 요소를 만듭니다.

    ```jsx
    import React from "react";
    import Navbar from "react-bootstrap/Navbar";
    import { useIsAuthenticated } from "@azure/msal-react";
    import { SignInButton } from "./SignInButton";
    
    /**
     * Renders the navbar component with a sign-in button if a user is not authenticated
     */
    export const PageLayout = (props) => {
        const isAuthenticated = useIsAuthenticated();
    
        return (
            <>
                <Navbar bg="primary" variant="dark">
                    <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                    { isAuthenticated ? <span>Signed In</span> : <SignInButton /> }
                </Navbar>
                <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
                <br />
                <br />
                {props.children}
            </>
        );
    };
    ```

2. 이제 *src/App.js* 를 열고 기존 콘텐츠를 다음 코드로 대체합니다. 

    ```jsx
    import React from "react";
    import { PageLayout } from "./components/PageLayout";
    
    function App() {
      return (
          <PageLayout>
              <p>This is the main app content!</p>
          </PageLayout>
      );
    }
    
    export default App;
    ```

이제 앱에서 인증되지 않은 사용자에게만 표시되는 로그인 단추가 생겼습니다.

사용자가 최초 로그인 시 **팝업을 사용하여 로그인** 또는 **리디렉션 단추를 사용하여 로그인** 을 선택하는 경우 `onClick` 처리기는 `loginPopup`(또는 `loginRedirect`)를 호출하여 사용자를 로그인합니다. `loginPopup` 메서드는 *Microsoft ID 플랫폼 엔드포인트* 가 있는 팝업 창을 열어 사용자의 자격 증명을 묻고 유효성을 검사합니다. 로그인에 성공하면 *msal.js* 에서 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)을 시작합니다.

이 시점에서 PKCE로 보호된 권한 부여 코드가 CORS로 보호된 토큰 엔드포인트로 보내져 토큰과 교환됩니다. 애플리케이션에서 ID 토큰, 액세스 토큰 및 새로 고침 토큰을 받고, *msal.js* 에서 처리하며, 토큰에 포함된 정보를 캐시합니다.

## <a name="sign-users-out"></a>사용자 로그아웃

*src/components* 에서 *SignOutButton.jsx* 라는 파일을 만듭니다. 팝업 창 또는 전체 프레임 리디렉션을 사용하여 로그아웃을 호출하려면 다음 섹션 중 하나의 코드를 추가합니다.

### <a name="sign-out-using-popups"></a>팝업을 사용하여 로그아웃

*src/components/SignInButton.jsx* 에 다음 코드를 추가하여 선택 시 팝업 로그아웃을 호출하는 단추 구성 요소를 만듭니다.

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutPopup().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will open a popup for logout
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Popup</Button>
    );
}
```

### <a name="sign-out-using-redirects"></a>리디렉션을 사용하여 로그아웃

*src/components/SignInButton.jsx* 에 다음 코드를 추가하여 선택 시 리디렉션 로그아웃을 호출하는 단추 구성 요소를 만듭니다.

```jsx
import React from "react";
import { useMsal } from "@azure/msal-react";
import Button from "react-bootstrap/Button";

function handleLogout(instance) {
    instance.logoutRedirect().catch(e => {
        console.error(e);
    });
}

/**
 * Renders a button which, when selected, will redirect the page to the logout prompt
 */
export const SignOutButton = () => {
    const { instance } = useMsal();

    return (
        <Button variant="secondary" className="ml-auto" onClick={() => handleLogout(instance)}>Sign out using Redirect</Button>
    );
}
```

### <a name="add-the-sign-out-button"></a>로그아웃 단추 추가

*src/components/PageLayout.jsx* 에서 `PageLayout`구성 요소를 업데이트하여 인증된 사용자에 대한 `SignOutButton`새 구성 요소를 렌더링합니다. 코드는 다음과 비슷합니다.

```jsx
import React from "react";
import Navbar from "react-bootstrap/Navbar";
import { useIsAuthenticated } from "@azure/msal-react";
import { SignInButton } from "./SignInButton";
import { SignOutButton } from "./SignOutButton";

/**
 * Renders the navbar component with a sign-in button if a user is not authenticated
 */
export const PageLayout = (props) => {
    const isAuthenticated = useIsAuthenticated();

    return (
        <>
            <Navbar bg="primary" variant="dark">
                <a className="navbar-brand" href="/">MSAL React Tutorial</a>
                { isAuthenticated ? <SignOutButton /> : <SignInButton /> }
            </Navbar>
            <h5><center>Welcome to the Microsoft Authentication Library For React Tutorial</center></h5>
            <br />
            <br />
            {props.children}
        </>
    );
};
```

## <a name="conditionally-render-components"></a>조건부로 구성 요소 렌더링

인증되거나 인증되지 않은 사용자에 대해서만 특정 구성 요소를 렌더링하려면 아래와 같이 `AuthenticateTemplate` 및/또는 `UnauthenticatedTemplate`를 사용합니다.

1. 다음 가져오기를 *src/App.js* 에 추가합니다.

    ```javascript
    import { AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";
    ```
    
1. 인증된 사용자에 대해서만 특정 구성 요소를 렌더링하려면 다음 코드로 *src/App.js* 의 `App` 함수를 업데이트합니다. 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

1. 로그인 제안과 같이 인증되지 않은 사용자에 대해서만 특정 구성 요소를 렌더링하려면 다음 코드로 *src/App.js* 의 `App` 함수를 업데이트합니다. 

    ```jsx
    function App() {
        return (
            <PageLayout>
                <AuthenticatedTemplate>
                    <p>You are signed in!</p>
                </AuthenticatedTemplate>
                <UnauthenticatedTemplate>
                    <p>You are not signed in! Please sign in.</p>
                </UnauthenticatedTemplate>
            </PageLayout>
        );
    }
    ```

## <a name="acquire-a-token"></a>토큰 획득

1. Microsoft Graph 같은 API를 호출하기 전에 액세스 토큰을 획득해야 합니다. 다음 코드를 사용하여 호출된 `ProfileContent`의 *src/App.js* 에 새 구성 요소를 추가합니다.

    ```jsx
    function ProfileContent() {
        const { instance, accounts, inProgress } = useMsal();
        const [accessToken, setAccessToken] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestAccessToken() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                setAccessToken(response.accessToken);
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    setAccessToken(response.accessToken);
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {accessToken ? 
                    <p>Access Token Acquired!</p>
                    :
                    <Button variant="secondary" onClick={RequestAccessToken}>Request Access Token</Button>
                }
            </>
        );
    };
    ```

1. *src/App.js* 에서 가져오기를 다음과 일치하도록 업데이트합니다.

    ```js
    import React, { useState } from "react";
    import { PageLayout } from "./components/PageLayout";
    import { AuthenticatedTemplate, UnauthenticatedTemplate, useMsal } from "@azure/msal-react";
    import { loginRequest } from "./authConfig";
    import Button from "react-bootstrap/Button";
    ```

1. 마지막으로 *src/App.js* 의 `App`구성 요소에서 새 `ProfileContent`구성 요소를 `AuthenticatedTemplate`의 자식으로 추가합니다. `App` 구성 요소는 다음과 같습니다.

    ```javascript
    function App() {
      return (
          <PageLayout>
              <AuthenticatedTemplate>
                  <ProfileContent />
              </AuthenticatedTemplate>
              <UnauthenticatedTemplate>
                  <p>You are not signed in! Please sign in.</p>
              </UnauthenticatedTemplate>
          </PageLayout>
      );
    }
    ```

위의 코드는 로그인한 사용자에 대한 단추를 렌더링하여 단추를 선택할 때 Microsoft Graph에 대한 액세스 토큰을 요청할 수 있도록 합니다.

사용자가 로그인한 후에 보호된 리소스에 액세스해야 할 때마다(즉, 토큰을 요청하기 위해) 사용자에게 재인증하도록 요청하지 않아야 합니다. 이러한 재인증 요청을 방지하려면 먼저 캐시되고 만료되지 않은 액세스 토큰을 찾는 `acquireTokenSilent`를 호출한 다음 필요한 경우 새로 고침 토큰을 사용하여 새 액세스 토큰을 얻습니다. 그러나 사용자가 Microsoft ID 플랫폼과 상호 작용하도록 강제로 적용해야 하는 경우도 있습니다. 다음은 그 예입니다.

- 세션이 만료되어 사용자가 해당 자격 증명을 다시 입력해야 합니다.
- 새로 고침 토큰이 만료되었습니다.
- 애플리케이션에서 리소스에 액세스하도록 요청하고 있으며 사용자의 동의가 필요합니다.
- 2단계 인증이 필요합니다.

`acquireTokenPopup`을 호출하면 팝업 창이 열리거나 `acquireTokenRedirect`에서 사용자를 Microsoft ID 플랫폼으로 리디렉션합니다. 이 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 동의하거나, 2단계 인증을 수행하여 상호 작용해야 합니다.

> [!NOTE]
> Internet Explorer를 사용하는 경우 Internet Explorer 및 팝업 창에 대한 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)로 인해 `loginRedirect` 및 `acquireTokenRedirect` 방법을 사용하는 것이 좋습니다.

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출

1. *src* 폴더에서 *graph.js* 라는 파일을 만들고, Microsoft Graph API에 대한 REST 호출을 수행하는 다음 코드를 추가합니다.

    ```javascript
    import { graphConfig } from "./authConfig";
    
    /**
     * Attaches a given access token to a Microsoft Graph API call. Returns information about the user
     */
    export async function callMsGraph(accessToken) {
        const headers = new Headers();
        const bearer = `Bearer ${accessToken}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        return fetch(graphConfig.graphMeEndpoint, options)
            .then(response => response.json())
            .catch(error => console.log(error));
    }
    ```

1. 다음으로 *src/components* 에서 *ProfileData.jsx* 라는 파일을 만들고 다음 코드를 추가합니다.

    ```javascript
    import React from "react";
    
    /**
     * Renders information about the user obtained from Microsoft Graph
     */
    export const ProfileData = (props) => {
        return (
            <div id="profile-div">
                <p><strong>First Name: </strong> {props.graphData.givenName}</p>
                <p><strong>Last Name: </strong> {props.graphData.surname}</p>
                <p><strong>Email: </strong> {props.graphData.userPrincipalName}</p>
                <p><strong>Id: </strong> {props.graphData.id}</p>
            </div>
        );
    };
    ```

1. 다음으로 *src/App.js* 를 열고 가져오기에 추가합니다.
    
    ```javascript
    import { ProfileData } from "./components/ProfileData";
    import { callMsGraph } from "./graph";
    ```

1. 마지막으로 *src/App.js* 의 `ProfileContent` 구성 요소를 업데이트하여 Microsoft Graph를 호출하고 토큰을 획득한 후 프로필 데이터를 표시합니다. `ProfileContent` 구성 요소는 다음과 같습니다.

    ```javascript
    function ProfileContent() {
        const { instance, accounts } = useMsal();
        const [graphData, setGraphData] = useState(null);
    
        const name = accounts[0] && accounts[0].name;
    
        function RequestProfileData() {
            const request = {
                ...loginRequest,
                account: accounts[0]
            };
    
            // Silently acquires an access token which is then attached to a request for Microsoft Graph data
            instance.acquireTokenSilent(request).then((response) => {
                callMsGraph(response.accessToken).then(response => setGraphData(response));
            }).catch((e) => {
                instance.acquireTokenPopup(request).then((response) => {
                    callMsGraph(response.accessToken).then(response => setGraphData(response));
                });
            });
        }
    
        return (
            <>
                <h5 className="card-title">Welcome {name}</h5>
                {graphData ? 
                    <ProfileData graphData={graphData} />
                    :
                    <Button variant="secondary" onClick={RequestProfileData}>Request Profile Information</Button>
                }
            </>
        );
    };
    ```

위의 변경된 내용에서 `callMSGraph()` 메서드는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행하는 데 사용됩니다. 그러면 요청에서 콘텐츠를 호출자에 반환합니다. 이 메서드는 *HTTP 인증 헤더* 에 획득된 토큰을 추가합니다. 이 자습서에서 만든 샘플 애플리케이션에서 보호된 리소스는 로그인한 사용자의 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.

## <a name="test-your-application"></a>애플리케이션 테스트

애플리케이션 만들기가 완료되었으며 이제 웹 서버를 시작하고 앱의 기능을 테스트할 준비가 되었습니다.

1. 프로젝트 폴더의 루트 내에서 다음 명령을 실행하여 앱을 시작합니다.

   ```console
   npm start
   ```
1. 앱에 브라우저 창이 자동으로 열립니다. 그렇지 않을 경우 브라우저를 열고 `http://localhost:3000`로 이동합니다. 아래와 같은 페이지가 표시됩니다.

    :::image type="content" source="media/tutorial-v2-react/react-01-unauthenticated.png" alt-text="로그인 대화 상자가 표시된 웹 브라우저":::

1. 로그인 단추를 선택하여 로그인합니다.

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음 로그인하면 프로필에 대한 액세스 권한을 부여하고 로그인하라는 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="웹 브라우저에 표시된 콘텐츠 대화 상자":::

요청된 권한에 동의하면 웹 애플리케이션에서 사용자 이름을 표시하여 성공적으로 로그인했음을 나타냅니다.

:::image type="content" source="media/tutorial-v2-react/react-02-authenticated.png" alt-text="웹 브라우저에서 성공적으로 로그인한 결과":::

### <a name="call-the-graph-api"></a>Graph API 호출

로그인한 후에 **프로필 보기** 를 선택하여 Microsoft Graph API에 대한 호출에서 응답으로 반환된 사용자 프로필 정보를 확인합니다.

:::image type="content" source="media/tutorial-v2-react/react-03-graph-data.png" alt-text="브라우저에 표시된 Microsoft Graph의 프로필 정보":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 Azure Portal에 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자의 이메일을 나열하기 위해 *Mail.Read* 범위가 필요합니다.

범위를 추가하면 추가된 범위에 대한 추가 동의를 제공하라는 메시지가 표시될 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 JavaScript 단일 페이지 애플리케이션 개발에 대해 자세히 알아보려면 여러 부분으로 구성된 시나리오 시리즈를 참조하세요.

> [!div class="nextstepaction"]
> [시나리오: 단일 페이지 애플리케이션](scenario-spa-overview.md)
