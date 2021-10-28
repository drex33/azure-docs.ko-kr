---
title: Azure Active Directory B2C 구성 요소를 사용하여 Angular 애플리케이션에서 인증을 사용하도록 설정
description: Azure Active Directory B2C의 구성 요소를 사용하여 Angular 애플리케이션에 로그인하고 사용자를 등록합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 35f48f23fba0bed51d9068e3402b673b36614d0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041446"
---
# <a name="enable-authentication-in-your-own-angular-application-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 자체 Angular 애플리케이션에서 인증을 사용하도록 설정

이 문서에서는 고객의 Angular SPA(단일 페이지 애플리케이션)에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 보여줍니다. Angular 애플리케이션을 [Angular용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) 인증 라이브러리와 통합하는 방법을 알아봅니다. 

[샘플 Angular 단일 페이지 애플리케이션에서 인증 구성](./configure-authentication-sample-angular-spa-app.md)이라는 제목의 관련 문서와 함께 이 문서를 사용하세요. 샘플 Angular 앱을 자신의 Angular 앱으로 대체합니다. 이 문서의 단계를 완료하면 애플리케이션이 Azure AD B2C를 통해 로그인을 수락합니다.

## <a name="prerequisites"></a>필수 조건

[샘플 Angular 단일 페이지 애플리케이션에서 인증 구성](configure-authentication-sample-angular-spa-app.md) 문서의 필수 구성 요소와 통합 단계를 검토하세요.

## <a name="create-an-angular-app-project"></a>Angular 앱 프로젝트 만들기

기존 Angular 앱 프로젝트를 사용해도 되고 새로 만들어도 됩니다. 새 프로젝트를 만들려면 다음 명령을 실행합니다.

명령:

1. npm 패키지 관리자를 사용하여 [Angular CLI](https://angular.io/cli)를 설치합니다.
1. 라우팅 모듈을 사용하여 [Angular 작업 영역을 만듭니다](https://angular.io/cli/new). 앱 이름은 `msal-angular-tutorial`입니다. `contoso-car-service`와 같은 유효한 Angular 앱 이름으로 변경할 수 있습니다.
1. 앱 디렉터리 폴더로 변경합니다.

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>종속성 설치

[MSAL 브라우저](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser) 및 [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) 라이브러리를 애플리케이션에 설치하려면 명령 셸에서 다음 명령을 실행합니다.

```
npm install @azure/msal-browser @azure/msal-angular
```

[Angular Material 구성 요소 라이브러리](https://material.angular.io/)(선택 사항, UI용)를 설치합니다.

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>인증 구성 요소 추가

샘플 코드는 다음 구성 요소로 구성됩니다. 

|구성 요소  |형식  |설명  |
|---------|---------|---------|
| auth-config.ts| 상수 | 이 구성 파일에는 Azure AD B2C ID 공급자와 웹 API 서비스에 대한 정보가 들어 있습니다. Angular 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃하고, 토큰을 획득하고, 토큰의 유효성을 검사합니다. |
| app.module.ts| [Angular 모듈](https://angular.io/guide/architecture-modules)| 이 구성 요소는 애플리케이션 파트가 어떻게 결합되는지 설명합니다. 애플리케이션을 부트스트랩하고 여는 데 사용되는 루트 모듈입니다. 이 연습에서는 *app.module.ts* 모듈에 일부 구성 요소를 추가하고 MSAL 구성 개체를 사용하여 MSAL 라이브러리를 시작합니다.  |
| app-routing.module.ts | [Angular 라우팅 모듈](https://angular.io/tutorial/toh-pt5) | 이 구성 요소는 브라우저 URL을 해석하고 해당 구성 요소를 로드하여 탐색을 지원합니다. 이 연습에서는 라우팅 모듈에 일부 구성 요소를 추가하고, [MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md)를 사용하여 구성 요소를 보호합니다. 오직 권한 있는 사용자만이 보호된 구성 요소에 액세스할 수 있습니다.   |
| app.component.* | [Angular 구성 요소](https://angular.io/guide/architecture-components) | `ng new` 명령은 루트 구성 요소를 사용하여 Angular 프로젝트를 만들었습니다. 이 연습에서는 *app* 구성 요소를 변경하여 위쪽 탐색 모음을 호스트합니다. 탐색 모음에는 로그인 및 로그아웃 단추를 비롯한 다양한 단추가 들어 있습니다. `app.component.ts` 클래스는 로그인 및 로그아웃 이벤트를 처리합니다.  |
| home.component.* | [Angular 구성 요소](https://angular.io/guide/architecture-components)|이 연습에서는 *home* 구성 요소를 추가하여 익명 액세스에 대해 홈페이지를 렌더링합니다. 이 구성 요소는 사용자가 로그인했는지 여부를 확인하는 방법을 보여줍니다.  |
| profile.component.* | [Angular 구성 요소](https://angular.io/guide/architecture-components) | 이 연습에서는 *profile* 구성 요소를 추가하여 ID 토큰 클레임을 읽는 방법을 알아봅니다. |
| webapi.component.* | [Angular 구성 요소](https://angular.io/guide/architecture-components)| 이 연습에서는 *webapi* 구성 요소를 추가하여 웹 API를 호출하는 방법을 알아봅니다. |

앱에 다음 구성 요소를 추가하려면 Angular CLI 명령 `generate component`를 실행합니다. 이 명령은 다음 작업을 수행합니다.

1. 각 구성 요소에 대한 폴더를 만듭니다. 이 폴더에는 TypeScript, HTML, CSS 및 테스트 파일이 포함됩니다. 
1. `app.module.ts` 및 `app-routing.module.ts` 파일을 새 구성 요소에 대한 참조로 업데이트합니다. 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>앱 설정 추가

Azure AD B2C ID 공급자와 웹 API에 대한 설정은 *auth-config.ts* 파일에 저장됩니다. *src/app* 폴더에 *auth-config.ts* 라는 파일을 만듭니다. 이 파일에는 다음 코드가 포함되어 있습니다. 그런 다음, [3.1 Angular 샘플 구성](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample)에 설명된 대로 설정을 변경합니다.

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn,
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-name.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="start-the-authentication-libraries"></a>인증 라이브러리 시작

퍼블릭 클라이언트 애플리케이션은 애플리케이션 비밀을 안전하게 유지한다고 신뢰할 수 없으므로 클라이언트 비밀이 없습니다. *src/app* 폴더에서 *app.module.ts* 를 열고 다음과 같이 변경합니다.

1. MSAL Angular 및 MSAL Browser 라이브러리를 가져옵니다.
1. Azure AD B2C 구성 모듈을 가져옵니다.
1. `HttpClientModule`을 가져옵니다. HTTP 클라이언트는 웹 API를 호출하는 데 사용됩니다.
1. Angular HTTP 인터셉터를 가져옵니다. MSAL은 인터셉터를 사용하여 전달자 토큰을 HTTP 권한 부여 헤더에 삽입합니다.
1. 필수 Angular 재질을 추가합니다.
1. 다중 계정 퍼블릭 클라이언트 애플리케이션 개체를 사용하여 MSAL을 인스턴스화합니다. MSAL을 초기화할 때 다음 항목이 전달됩니다.
    1. *auth-config.ts* 의 구성 개체
    1. 라우팅 가드의 구성 개체
    1. MSAL 인터셉터의 구성 개체 인터셉터 클래스는 알려진 보호된 리소스에 Angular [HttpClient](https://angular.io/api/common/http/HttpClient) 클래스를 사용하는 발신 요청에 대한 토큰을 자동으로 획득합니다.
1. `HTTP_INTERCEPTORS` 및 `MsalGuard` [Angular 공급자](https://angular.io/guide/providers)를 구성합니다.  
1. `MsalRedirectComponent`를 [Angular 부트스트랩](https://angular.io/guide/bootstrapping)에 추가합니다.

*src/app* 폴더에서 *app.module.ts* 를 편집하여 다음 코드 조각과 같이 수정합니다. 변경 내용에는 “Changes start here” 및 “Changes end here” 플래그가 지정됩니다. 

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL configuration object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>경로 구성

이 섹션에서는 Angular 애플리케이션에 대한 경로를 구성합니다. 사용자가 단일 페이지 애플리케이션 내에서 이동할 페이지의 링크를 선택하거나 주소 표시줄에 URL을 입력하면 해당 경로는 URL을 Angular 구성 요소에 매핑합니다. Angular 라우팅 [canActivate](https://angular.io/api/router/CanActivate) 인터페이스는 MSAL Guard를 사용하여 사용자가 로그인했는지 확인합니다. 사용자가 로그인하지 않은 경우 MSAL은 사용자를 Azure AD B2C로 보내 인증합니다.

*src/app* 폴더에서 *app-routing.module.ts* 를 편집하여 다음 코드 조각과 같이 수정합니다. 변경 내용에는 “Changes start here” 및 “Changes end here” 플래그가 지정됩니다. 

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL Guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>로그인 및 로그아웃 단추 추가

이 섹션에서는 *app* 구성 요소에 로그인 및 로그아웃 단추를 추가합니다. *src/app* 폴더에서 *app.component.ts* 파일을 열고 다음과 같이 변경합니다.

1. 필수 구성 요소를 가져옵니다.
1. [OnInit 메서드](https://angular.io/api/core/OnInit)를 구현하도록 클래스를 변경합니다. `OnInit` 메서드는 [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$` 관찰 가능 이벤트를 구독합니다. 사용자 상호 작용의 상태, 특히 상호 작용이 완료되었는지 확인하려면 이 이벤트를 사용합니다. 

   MSAL 계정 개체와 상호 작용 전에 `InteractionStatus` 속성이 `InteractionStatus.None`을 반환하는지 확인합니다. `subscribe` 이벤트는 `setLoginDisplay` 메서드를 호출하여 사용자가 인증되었는지 확인합니다.
1. 클래스 변수를 추가합니다.
1. 권한 부여 흐름을 시작하는 `login` 메서드를 추가합니다.
1. 사용자를 로그아웃하는 `logout` 메서드를 추가합니다. 
1. 사용자가 인증되었는지 확인하는 `setLoginDisplay` 메서드를 추가합니다.
1. `inProgress$` 구독 이벤트를 정리하는 [ngOnDestroy](https://angular.io/api/core/OnDestroy) 메서드를 추가합니다.

변경을 마친 코드는 다음 코드 조각과 비슷합니다.

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { 
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
  /* Changes end here. */
}
```

*src/app* 폴더에서 *app.component.html* 을 편집하여 다음과 같이 변경합니다.

1. 프로필 및 웹 API 구성 요소에 대한 링크를 추가합니다.
1. 클릭 이벤트 특성이 `login()` 메서드로 설정된 로그인 단추를 추가합니다. 이 단추는 `loginDisplay` 클래스 변수가 `false`인 경우에만 나타납니다.
1. 클릭 이벤트 특성이 `logout()` 메서드로 설정된 로그아웃 단추를 추가합니다. 이 단추는 `loginDisplay` 클래스 변수가 `true`인 경우에만 나타납니다.
1. [router-outlet](https://angular.io/api/router/RouterOutlet) 요소를 추가합니다. 

변경을 마친 코드는 다음 코드 조각과 비슷합니다.

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

필요에 따라 *app.component.css* 파일을 다음 CSS 코드 조각으로 업데이트합니다. 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>앱 리디렉션 처리 

MSAL에서 리디렉션을 사용하는 경우 [app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) 지시문을 *index.html* 에 추가해야 합니다. *src* 폴더에서 다음 코드 조각에 표시된 것처럼 *index.html* 을 편집합니다.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>앱 CSS 설정(선택 사항)

*/src* 폴더에서 *styles.css* 파일을 다음 CSS 코드 조각으로 업데이트합니다. 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> 이제 앱을 실행하고 로그인 환경을 테스트할 수 있습니다. 앱을 실행하려면 [Angular 애플리케이션 실행](#run-the-angular-application) 섹션을 참조하세요.

## <a name="check-if-a-user-is-authenticated"></a>사용자가 인증되었는지 확인

*home.component* 파일은 사용자가 인증되었는지 확인하는 방법을 보여줍니다. *src/app/home* 폴더에서 *home.component.ts* 를 다음 코드 조각으로 업데이트합니다. 

코드:

1. [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `msalSubject$` 및 `inProgress$` 관찰 가능 이벤트를 구독합니다. 
1. `msalSubject$` 이벤트가 인증 결과를 브라우저 콘솔에 쓰는지 확인합니다.
1. `inProgress$` 이벤트가 사용자 인증 여부를 확인하는지 확인합니다. `getAllAccounts()` 메서드는 하나 이상의 개체를 반환합니다.


```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  loginDisplay = false;

  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }
}
```

*src/app/home* 폴더에서 *home.component.html* 을 다음 HTML 코드 조각으로 업데이트합니다. [*ngIf](https://angular.io/api/common/NgIf) 지시문은 `loginDisplay` 클래스 변수를 확인하여 시작 메시지를 표시하거나 숨깁니다.

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>ID 토큰 클레임 읽기

*profile.component* 파일은 사용자의 ID 토큰 클레임에 액세스하는 방법을 보여줍니다. *src/app/profile* 폴더에서 *profile.component.ts* 를 다음 코드 조각으로 업데이트합니다. 

코드:

1. 필수 구성 요소를 가져옵니다.
1. [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$` 관찰 가능 이벤트를 구독합니다. 이 이벤트는 계정을 로드하고 ID 토큰 클레임을 읽습니다.
1. `checkAndSetActiveAccount` 메서드가 활성 계정을 확인하고 설정하는지 확인합니다. 이 작업은 앱이 여러 Azure AD B2C 사용자 흐름 또는 사용자 지정 정책과 상호 작용하는 경우에 일반적으로 수행되는 작업입니다.
1. `getClaims` 메서드가 활성 MSAL 계정 개체에서 ID 토큰 클레임을 가져오는지 확인합니다. 그런 다음, 이 메서드는 `dataSource` 배열에 클레임을 추가합니다. 이 배열은 구성 요소의 템플릿 바인딩을 사용하여 사용자에게 렌더링됩니다.

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

*src/app/profile* 폴더에서 *profile.component.html* 을 다음 HTML 코드 조각으로 업데이트합니다. 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>웹 API 호출

[토큰 기반 권한 부여 웹 API](enable-authentication-web-api.md)를 호출하려면 앱에 유효한 액세스 토큰이 필요합니다. [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md)는 알려진 보호된 리소스에 Angular [HttpClient](https://angular.io/api/common/http/HttpClient) 클래스를 사용하는 발신 요청에 대한 토큰을 자동으로 획득합니다.

> [!IMPORTANT]
> `app.module.ts` 클래스의 MSAL 초기화 메서드는 `protectedResourceMap` 개체를 사용하여 웹 API처럼 보호된 리소스를 필요한 앱 범위와 매핑합니다. 코드에서 다른 웹 API를 호출해야 하는 경우 웹 API URI, 즉 해당 범위가 있는 웹 API HTTP 메서드를 `protectedResourceMap` 개체에 추가합니다. 자세한 내용은 [Protected Resource Map](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map)(보호된 리소스 맵)을 참조하세요.


[HttpClient](https://angular.io/api/common/http/HttpClient) 개체가 웹 API를 호출하면 [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) 공급자는 다음 단계를 수행합니다.

1. 웹 API 엔드포인트에 필요한 권한(범위)이 있는 액세스 토큰을 획득합니다. 
1. 다음 형식을 사용하여 액세스 토큰을 HTTP 요청의 권한 부여 헤더에 전달자 토큰으로 전달합니다.

   ```http
   Authorization: Bearer <access-token>
   ```

*webapi.component* 파일은 웹 API를 호출하는 방법을 보여줍니다. *src/app/webapi* 폴더에서 *webapi.component.ts* 를 다음 코드 조각으로 업데이트합니다.  

코드:

1. Angular [HttpClient](https://angular.io/guide/http) 클래스를 사용하여 웹 API를 호출합니다.
1. `auth-config` 클래스의 `protectedResources.todoListApi.endpoint` 요소를 읽습니다. 이 요소는 웹 API URI를 지정합니다. 웹 API URI에 따라 MSAL 인터셉터는 해당 범위가 있는 액세스 토큰을 획득합니다. 
1. 웹 API에서 프로필을 가져오고 `profile` 클래스 변수를 설정합니다.

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

*src/app/webapi* 폴더에서 *webapi.component.html* 을 다음 HTML 코드 조각으로 업데이트합니다. 이 구성 요소의 템플릿은 웹 API에서 반환하는 이름을 렌더링합니다. 페이지 하단을 보면 템플릿이 웹 API 주소를 렌더링합니다.

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

필요에 따라 *webapi.component.css* 파일을 다음 CSS 코드 조각으로 업데이트합니다. 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>Angular 애플리케이션 실행


다음 명령을 실행합니다.

```console
npm start
```

콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

```console
Listening on port 4200...
```

> [!TIP]
> 또는 `npm start` 명령을 실행하려면 [Visual Studio Code 디버거](https://code.visualstudio.com/docs/editor/debugging)를 사용합니다. 디버거를 사용하면 편집, 컴파일 및 디버그 루프를 빠르게 수행할 수 있습니다.

브라우저에서 `http://localhost:4200`으로 이동하여 애플리케이션을 확인합니다.


## <a name="next-steps"></a>다음 단계

* [Azure AD B2C를 사용하여 Angular 애플리케이션에서 인증 옵션 구성](enable-authentication-angular-spa-app-options.md)
* [고유 웹 API에서 인증을 사용하도록 설정](enable-authentication-web-api.md)
