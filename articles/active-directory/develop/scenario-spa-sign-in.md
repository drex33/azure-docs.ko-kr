---
title: 단일 페이지 앱 로그인 및 로그아웃
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드하는 방법 알아보기(로그인)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 2fa29b8cfd15ccee133ef416d729c1807aa0a400
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017923"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>단일 페이지 애플리케이션: 로그인 및 로그아웃

단일 페이지 애플리케이션의 코드에 로그인을 추가하는 방법을 알아봅니다.

애플리케이션의 API에 액세스하기 위한 토큰을 얻으려면 인증된 사용자 컨텍스트가 필요합니다. 다음 두 가지 방법으로 MSAL.js의 애플리케이션에 사용자를 로그인할 수 있습니다.

* [팝업 창](#sign-in-with-a-pop-up-window), `loginPopup` 메서드 사용
* [리디렉션](#sign-in-with-redirect), `loginRedirect` 메서드 사용

로그인 시 사용자의 동의가 필요한 API 범위를 선택적으로 전달할 수도 있습니다.

> [!NOTE]
> 애플리케이션에 이미 인증된 사용자 컨텍스트 또는 ID 토큰에 대한 액세스 권한이 있으면 로그인 단계를 건너뛰고 토큰을 직접 획득할 수 있습니다. 자세한 내용은 [MSAL.js 로그인을 사용하지 않는 SSO](msal-js-sso.md#sso-without-msaljs-login)를 참조하세요.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중에서 선택

팝업이나 리디렉션 환경 중 선택은 애플리케이션 흐름에 따라 다릅니다.

* 인증 중에 사용자가 기본 애플리케이션 페이지에서 이동하는 것을 원하지 않는 경우 팝업 방법을 권장합니다. 인증 리디렉션은 팝업 창에서 발생하기 때문에 기본 애플리케이션의 상태가 유지됩니다.

* 사용자에게 팝업 창을 사용하지 않게 설정하는 브라우저 제약 조건이나 정책이 있으면 리디렉션 메서드를 사용할 수 있습니다. [Internet Explorer의 팝업 창에 알려진 이슈](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)가 있으므로 Internet Explorer 브라우저에서 리디렉션 메서드를 사용합니다.

## <a name="sign-in-with-a-pop-up-window"></a>팝업 창을 사용하여 로그인

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        accountId = loginResponse.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

MSAL Angular 래퍼를 사용하면 `MsalGuard`를 경로 정의에 추가하여 애플리케이션의 특정 경로를 보호할 수 있습니다. 이 가드는 해당 경로에 액세스할 때 로그인하는 메서드를 호출합니다.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
    {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [
            MsalGuard
        ]
    },
    {
        path: '',
        component: HomeComponent
    }
];

@NgModule({
    imports: [RouterModule.forRoot(routes, { useHash: false })],
    exports: [RouterModule]
})
export class AppRoutingModule { }
```

팝업 창 환경의 경우 가드 구성에서 `interactionType` 구성을 `InteractionType.Popup`으로 설정합니다. 다음과 같이 동의가 필요한 범위도 전달할 수 있습니다.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Popup, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)
MSAL Angular 래퍼를 사용하면 `MsalGuard`를 경로 정의에 추가하여 애플리케이션의 특정 경로를 보호할 수 있습니다. 이 가드는 해당 경로에 액세스할 때 로그인하는 메서드를 호출합니다.
```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

팝업 창 환경을 사용하려면 `popUp` 구성 옵션을 사용으로 설정합니다. 다음과 같이 동의가 필요한 범위도 전달할 수 있습니다.

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```

# <a name="react"></a>[React](#tab/react)

MSAL React 래퍼를 사용하면 `MsalAuthenticationTemplate` 구성 요소에 특정 구성 요소를 래핑하여 해당 구성 요소를 보호할 수 있습니다. 사용자가 아직 로그인하지 않은 경우 이 구성 요소는 로그인을 호출하고, 그렇지 않으면 자식 구성 요소를 렌더링합니다.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Popup}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

`@azure/msal-browser` API를 직접 사용하여 `AuthenticatedTemplate` 및/또는 `UnauthenticatedTemplate` 구성 요소와 연결된 로그인을 호출하면 각각 로그인 또는 로그아웃된 사용자에게 특정 콘텐츠를 렌더링할 수도 있습니다. 단추 클릭과 같은 사용자 조작의 결과로 로그인을 호출해야 하는 경우에 권장되는 방법입니다.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginPopup();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-in-with-redirect"></a>리디렉션을 사용하여 로그인

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let accountId = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    if (response !== null) {
        accountId = response.account.homeAccountId;
        // Display signed-in user content, call API, etc.
    } else {
        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();

        if (currentAccounts.length === 0) {
            // no accounts signed-in, attempt to sign a user in
            myMsal.loginRedirect(loginRequest);
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            accountId = currentAccounts[0].homeAccountId;
        }
    }
}

myMsal.handleRedirectPromise().then(handleResponse);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

리디렉션 메서드는 기본 앱에서 이동해 나가기 때문에 프라미스를 반환하지 않습니다. 반환된 토큰을 처리하고 액세스하려면 리디렉션 메서드를 호출하기 전에 성공 및 오류 콜백을 등록합니다.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

여기에서 코드는 팝업 창을 사용한 로그인에 관한 섹션에서 앞서 설명한 것과 같습니다. 단, MsalGuard 구성에서 `interactionType`는 `InteractionType.Redirect`로 설정되고 `MsalRedirectComponent`는 리디렉션을 처리하도록 부트스트랩됩니다.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect, // Msal Guard Configuration
            authRequest: {
                scopes: ['user.read']
            }
        }, null)
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)

여기에서 코드는 팝업 창을 사용한 로그인에 관한 섹션에서 앞서 설명한 것과 같습니다. 기본 흐름은 리디렉션입니다.

# <a name="react"></a>[React](#tab/react)

MSAL React 래퍼를 사용하면 `MsalAuthenticationTemplate` 구성 요소에 특정 구성 요소를 래핑하여 해당 구성 요소를 보호할 수 있습니다. 사용자가 아직 로그인하지 않은 경우 이 구성 요소는 로그인을 호출하고, 그렇지 않으면 자식 구성 요소를 렌더링합니다.

```javascript
import { InteractionType } from "@azure/msal-browser";
import { MsalAuthenticationTemplate, useMsal } from "@azure/msal-react";

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <MsalAuthenticationTemplate interactionType={InteractionType.Redirect}>
            <p>This will only render if a user is signed-in.</p>
            <WelcomeUser />
        </MsalAuthenticationTemplate>
      )
};
```

`@azure/msal-browser` API를 직접 사용하여 `AuthenticatedTemplate` 및/또는 `UnauthenticatedTemplate` 구성 요소와 연결된 로그인을 호출하면 각각 로그인 또는 로그아웃된 사용자에게 특정 콘텐츠를 렌더링할 수도 있습니다. 단추 클릭과 같은 사용자 조작의 결과로 로그인을 호출해야 하는 경우에 권장되는 방법입니다.

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signInClickHandler(instance) {
    instance.loginRedirect();
}

// SignInButton Component returns a button that invokes a popup login when clicked
function SignInButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signInClickHandler(instance)}>Sign In</button>
};

function WelcomeUser() {
    const { accounts } = useMsal();
    const username = accounts[0].username;

    return <p>Welcome, {username}</p>
}

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <WelcomeUser />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
                <SignInButton />
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-pop-up-window"></a>팝업 창으로 로그아웃

MSAL.js v2는 브라우저 스토리지의 캐시를 지우고 Azure AD(Azure Active Directory) 로그아웃 페이지로 팝업 창을 여는 `logoutPopup` 메서드를 제공합니다. 로그아웃 후에 Azure AD는 팝업을 애플리케이션으로 다시 리디렉션하고, MSAL.js눈 팝업을 닫습니다.

`postLogoutRedirectUri`를 설정하여 Azure AD가 로그아웃 후에 리디렉션해야 하는 URI를 구성할 수 있습니다. 애플리케이션 등록에서 이 URI를 리디렉션 URI로 등록해야 합니다.

요청의 일부로 `mainWindowRedirectUri`를 전달하여 로그아웃이 완료된 후에 홈페이지 또는 로그인 페이지와 같은 다른 페이지로 주 창을 리디렉션하도록 `logoutPopup`을 구성할 수도 있습니다.

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", // defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId),
    mainWindowRedirectUri: "your_app_main_window_redirect_uri"
}

await myMsal.logoutPopup(logoutRequest);
```
# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

팝업 창을 통한 로그아웃은 MSAL.js v1에서 지원되지 않습니다.

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutPopup({
        mainWindowRedirectUri: "/"
    });
}
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)

팝업 창을 통한 로그아웃은 MSAL Angular v1에서 지원되지 않습니다.

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        mainWindowRedirectUri: "your_app_main_window_redirect_uri",
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutPopup(logoutRequest);
}

// SignOutButton Component returns a button that invokes a popup logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="sign-out-with-a-redirect"></a>리디렉션을 사용한 로그아웃

MSAL.js는 브라우저 스토리지의 캐시를 지우고 창을 Azure AD(Azure Active Directory) 로그아웃 페이지로 리디렉션하는 `logout` 메서드(v1)와 `logoutRedirect` 메서드(v2)를 제공합니다. 로그아웃 후에 Azure AD는 기본적으로 로그아웃을 호출한 페이지로 다시 리디렉션됩니다.

`postLogoutRedirectUri`를 설정하여 로그아웃 후 리디렉션해야 하는 URI를 구성할 수 있습니다. 애플리케이션 등록에서 이 URI를 리디렉션 URI로 등록해야 합니다.

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByHomeId(homeAccountId)
}

myMsal.logoutRedirect(logoutRequest);
```

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: 'your_app_logout_redirect_uri'
            }
        }), null, null)
    ]
})

// In app.component.ts
logout() {
    this.authService.logoutRedirect();
}
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})
// In app.component.ts
this.authService.logout();
```

# <a name="react"></a>[React](#tab/react)

```javascript
import { useMsal, AuthenticatedTemplate, UnauthenticatedTemplate } from "@azure/msal-react";

function signOutClickHandler(instance) {
    const logoutRequest = {
        account: instance.getAccountByHomeId(homeAccountId),
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
    instance.logoutRedirect(logoutRequest);
}

// SignOutButton Component returns a button that invokes a redirect logout when clicked
function SignOutButton() {
    // useMsal hook will return the PublicClientApplication instance you provided to MsalProvider
    const { instance } = useMsal();

    return <button onClick={() => signOutClickHandler(instance)}>Sign Out</button>
};

// Remember that MsalProvider must be rendered somewhere higher up in the component tree
function App() {
    return (
        <>
            <AuthenticatedTemplate>
                <p>This will only render if a user is signed-in.</p>
                <SignOutButton />
            </AuthenticatedTemplate>
            <UnauthenticatedTemplate>
                <p>This will only render if a user is not signed-in.</p>
            </UnauthenticatedTemplate>
        </>
    )
}
```

---

## <a name="next-steps"></a>다음 단계

본 시나리오의 다음 문서인 [앱용 토큰 획득](scenario-spa-acquire-token.md)으로 이동합니다.
