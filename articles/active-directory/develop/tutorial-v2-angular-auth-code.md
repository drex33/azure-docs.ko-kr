---
title: '자습서: 인증 코드 흐름을 사용하여 인증할 수 있도록 Microsoft ID 플랫폼을 사용하는 Angular 앱 만들기 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 Microsoft ID 플랫폼을 사용하여 사용자를 로그인하고 사용자를 대신해 Microsoft Graph API를 호출하도록 액세스 토큰을 가져오면서 인증 코드 흐름을 사용하는 Angular SPA(단일 페이지 앱)를 빌드합니다.
services: active-directory
author: jo-arroyo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/14/2021
ms.author: joarroyo
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 398117120e560db00be97a344feed09351a26258
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050340"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa-using-auth-code-flow"></a>자습서: 인증 코드 흐름을 사용하여 Angular SPA(단일 페이지 애플리케이션)에서 사용자 로그인 및 Microsoft Graph API 호출

이 자습서에서는 PKCE와 함께 권한 부여 코드 흐름을 사용하여 사용자를 로그인하고 Microsoft Graph API를 호출하는 Angular SPA(단일 페이지 애플리케이션)를 빌드합니다. 빌드된 SPA에서는 Angular v2용 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * `npm`을 사용하여 Angular 프로젝트 만들기
> * Azure Portal에 애플리케이션 등록
> * 사용자 로그인 및 로그아웃을 지원하는 코드 추가
> * Microsoft Graph API를 호출하는 코드 추가
> * 앱 테스트

MSAL Angular v1에서 향상된 MSAL Angular v2는 브라우저에서 암시적 허용 흐름 대신 권한 부여 코드 흐름을 지원합니다. MSAL Angular v2에서는 암시적 흐름을 지원하지 **않습니다**.

## <a name="prerequisites"></a>필수 구성 요소

* 로컬 앱 서버 실행을 위한 [Node.js](https://nodejs.org/en/download/).
* 프로젝트 파일을 수정하기 위한 [Visual Studio Code](https://code.visualstudio.com/download) 또는 기타 편집기

## <a name="how-the-sample-app-works"></a>샘플 앱의 작동 방식

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="단일 페이지 애플리케이션의 권한 부여 코드 흐름을 보여 주는 다이어그램":::

이 자습서에서 만드는 샘플 애플리케이션을 사용하면 Angular SPA에서 Microsoft ID 플랫폼에서 발급한 토큰을 수락하는 Microsoft Graph API 또는 웹 API를 쿼리할 수 있습니다. MSAL.js v2 라이브러리의 래퍼인 Angular v2용 MSAL(Microsoft 인증 라이브러리)을 사용합니다. MSAL Angular를 사용하면 Angular 9 이상 애플리케이션에서 Azure AD(Azure Active Directory)를 사용하여 엔터프라이즈 사용자를 인증하고 Microsoft 계정 사용자 및 소셜 ID(Facebook, Google, LinkedIn 등)를 사용하여 사용자를 인증할 수 있습니다. 또한 이 라이브러리를 사용하면 애플리케이션에서 Microsoft 클라우드 서비스 및 Microsoft Graph에 액세스할 수 있습니다.

이 시나리오에서는 사용자가 로그인하면 권한 부여 헤더를 통해 액세스 토큰이 요청되고 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL에서 처리합니다.

### <a name="libraries"></a>라이브러리

이 자습서에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|JavaScript Angular 래퍼용 Microsoft 인증 라이브러리|
|[MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|JavaScript v2 브라우저 패키지용 Microsoft 인증 라이브러리 |

GitHub의 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 리포지토리에서 모든 MSAL.js 라이브러리의 소스 코드를 찾을 수 있습니다.

## <a name="create-your-project"></a>프로젝트 만들기

[Node.js](https://nodejs.org/en/download/)가 설치되면 터미널 창을 연 다음, 다음 명령을 실행하여 새 Angular 애플리케이션을 생성합니다.

```bash
npm install -g @angular/cli                         # Install the Angular CLI
ng new msal-angular-tutorial --routing=true --style=css --strict=false    # Generate a new Angular app
cd msal-angular-tutorial                            # Change to the app directory
npm install @angular/material @angular/cdk          # Install the Angular Material component library (optional, for UI)
npm install @azure/msal-browser @azure/msal-angular # Install MSAL Browser and MSAL Angular in your application
ng generate component home                          # To add a home page
ng generate component profile                       # To add a profile page
```

## <a name="register-your-application"></a>애플리케이션 등록

지침에 따라 Azure Portal에서 [단일 페이지 애플리케이션을 등록](./scenario-spa-app-registration.md)합니다.

나중에 사용할 수 있도록 등록의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 적어 둡니다.

**리디렉션 URI** 값을 **http://localhost:4200/** 으로 등록하고 ‘SPA’를 입력합니다.

## <a name="configure-the-application"></a>애플리케이션 구성

1. *src/app* 폴더에서 *app.module.ts* 를 편집하고 `MsalModule` 및 `MsalInterceptor`를 `imports` 및 `isIE` 상수에 추가합니다. 코드는 다음과 비슷합니다.

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

    다음 값을 바꿉니다.

    |값 이름|정보|
    |---------|---------|
    |Enter_the_Application_Id_Here|애플리케이션 등록의 **개요** 페이지에서 이는 **애플리케이션(클라이언트) ID** 값입니다. |
    |Enter_the_Cloud_Instance_Id_Here|Azure 클라우드의 인스턴스입니다. 기본 또는 글로벌 Azure 클라우드의 경우 **https://login.microsoftonline.com** 을 입력합니다. 국가별 클라우드(예: 중국)의 경우 [국가별 클라우드](./authentication-national-cloud.md)를 참조하세요.|
    |Enter_the_Tenant_Info_Here| 다음 옵션 중 하나로 설정합니다. 애플리케이션이 *이 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 디렉터리(테넌트) ID 또는 테넌트 이름(예: **contoso.microsoft.com**)으로 바꿉니다. 애플리케이션이 *모든 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 **organizations** 으로 바꿉니다. 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정* 을 지원하는 경우 이 값을 **common** 으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers** 로 바꿉니다. |
    |Enter_the_Redirect_Uri_Here|**http://localhost:4200** 으로 바꿉니다.|

    사용할 수 있는 구성 가능한 옵션에 대한 자세한 내용은 [클라이언트 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

2. *src/app/app-routing.module.ts* 에서 경로를 홈과 프로필 구성 요소에 추가합니다. 코드는 다음과 같습니다.

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

## <a name="replace-base-ui"></a>기본 UI 바꾸기

1. *src/app/app.component.html* 에서 자리 표시자 코드를 다음으로 교체합니다.

    ```HTML
    <mat-toolbar color="primary">
      <a class="title" href="/">{{ title }}</a>

      <div class="toolbar-spacer"></div>

      <a mat-button [routerLink]="['profile']">Profile</a>

      <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>

    </mat-toolbar>
    <div class="container">
      <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
      <router-outlet *ngIf="!isIframe"></router-outlet>
    </div>
    ```

2. 재료 모듈을 *src/app/app.module.ts* 에 추가합니다. `AppModule`은 다음과 같습니다.

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, 
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

3. (선택 사항) CSS를 *src/style.css* 에 추가합니다.

    ```css
    @import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

    html, body { height: 100%; }
    body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
    .container { margin: 1%; }
    ```

4. (선택 사항) CSS를 *src/app/app.component.css* 에 추가합니다.

    ```css
    .toolbar-spacer {
        flex: 1 1 auto;
      }
      
      a.title {
        color: white;
      }
    ```

## <a name="sign-in-a-user"></a>사용자 로그인

팝업 창이나 전체 프레임 리디렉션을 사용하여 로그인을 호출하려면 다음 섹션에서 코드를 추가합니다. 

### <a name="sign-in-using-pop-ups"></a>팝업을 사용하여 로그인

1. *src/app/app.component.ts* 에서 코드를 다음으로 변경하여 팝업 창을 사용해 사용자를 로그인합니다.

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginPopup()
          .subscribe({
            next: (result) => {
              console.log(result);
              this.setLoginDisplay();
            },
            error: (error) => console.log(error)
          });
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

> [!NOTE]
> Internet Explorer에서 팝업 창 처리와 관련된 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md)로 인해 이 자습서의 나머지 부분에서는 Microsoft Internet Explorer에서 `loginRedirect` 메서드를 사용합니다.

### <a name="sign-in-using-redirects"></a>리디렉션을 사용하여 로그인

1. *src/app/app.module.ts* 를 업데이트하여 `MsalRedirectComponent`를 부트스트랩합니다. 리디렉션을 처리하는 전용 리디렉션 구성 요소입니다. 이제 코드가 다음과 비슷할 것입니다.

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular'; // Updated import
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent, MsalRedirectComponent] // MsalRedirectComponent bootstrapped here
    })
    export class AppModule { }
    ```

2. `<app-redirect>` 선택기를 *src/index.html* 에 추가합니다. 이 선택기는 `MsalRedirectComponent`에서 사용됩니다. *src/index.html* 은 다음과 같습니다.

    ```HTML
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>msal-angular-tutorial</title>
      <base href="/">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link rel="icon" type="image/x-icon" href="favicon.ico">
    </head>
    <body>
      <app-root></app-root>
      <app-redirect></app-redirect>
    </body>
    </html>
    ```

3. *src/app/app.component.ts* 에서 코드를 다음으로 바꿔 전체 프레임 리디렉션을 사용해 사용자를 로그인합니다.

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

4. *src/app/home/home.component.ts* 에서 기존 코드를 바꿔 `LOGIN_SUCCESS` 이벤트를 구독합니다. 이렇게 하면 리디렉션을 사용하여 성공적인 로그인 결과에 액세스할 수 있습니다. 코드는 다음과 비슷합니다.

    ```javascript
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
      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
      }
    }
    ```

## <a name="conditional-rendering"></a>조건부 렌더링

인증된 사용자 전용 특정 UI를 렌더링하려면 구성 요소에서 사용자가 로그인했고 상호 작용이 완료되었는지 확인하도록 `MsalBroadcastService`를 구독해야 합니다.

1. `MsalBroadcastService`를 *src/app/app.component.ts* 에 추가하고 식별 가능한 `inProgress$`를 구독하여 UI를 렌더링하기 전에 상호 작용이 완료되고 계정이 로그인되었는지 확인합니다. 이제 코드가 다음과 비슷할 것입니다.

    ```javascript
    import { Component, OnInit, OnDestroy } from '@angular/core';
    import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
    import { InteractionStatus } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

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
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

2. 또한 *src/app/home/home.component.ts* 에서 코드를 업데이트하여 UI를 업데이트하기 전에 완료할 상호 작용을 확인합니다. 이제 코드가 다음과 비슷할 것입니다.

    ```javascript
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

3. *src/app/home/home.component.html* 에서 코드를 다음 조건부 표시로 바꿉니다.

    ```HTML
    <div *ngIf="!loginDisplay">
        <p>Please sign-in to see your profile information.</p>
    </div>

    <div *ngIf="loginDisplay">
        <p>Login successful!</p>
        <p>Request your profile information by clicking Profile above.</p>
    </div>
    ```

## <a name="guarding-routes"></a>경로 보호

### <a name="angular-guard"></a>Angular Guard

MSAL Angular는 경로를 보호하는 데 사용할 수 있고 보호된 경로에 액세스하기 전에 인증이 필요한 클래스인 `MsalGuard`를 제공합니다. 다음 단계에서는 `MsalGuard`를 `Profile` 경로에 추가합니다. `Profile` 경로를 사용하면 사용자가 `Login` 단추를 사용하여 로그인하지 않은 경우나 `Profile` 경로에 액세스하려거나 `Profile` 단추를 클릭하려는 경우에도 `MsalGuard`에 `Profile` 페이지를 표시하기 전에 팝업이나 리디렉션을 통해 사용자를 인증하라는 메시지가 표시됩니다.

`MsalGuard`는 사용자 경험을 향상시키는 데 사용할 수 있는 편리한 클래스이지만 보안에 사용해서는 안 됩니다. 공격자는 잠재적으로 클라이언트 쪽 가드를 돌아다닐 수 있으므로 서버에서 사용자가 액세스해서는 안 되는 데이터를 반환하지 않는지 확인해야 합니다.

1. `MsalGuard` 클래스를 *src/app/app.module.ts* 의 애플리케이션에 있는 공급자로 추가하고 `MsalGuard`의 구성을 추가합니다. 나중에 토큰을 획득하는 데 필요한 범위를 `authRequest`에서 제공할 수 있으며 Guard의 상호 작용 형식을 `Redirect` 또는 `Popup`으로 설정할 수 있습니다. 코드는 다음과 같습니다.

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard } from '@azure/msal-angular'; // MsalGuard added to imports
    import { PublicClientApplication, InteractionType } from '@azure/msal-browser'; // InteractionType added to imports

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
            interactionType: InteractionType.Redirect, // MSAL Guard Configuration
            authRequest: {
              scopes: ['user.read']
            }
        }, null)
      ],
      providers: [
        MsalGuard // MsalGuard added as provider here
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }
    ```

2. *src/app/app-routing.module.ts* 에서 보호하려는 경로에 `MsalGuard`를 설정합니다.

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';
    import { MsalGuard } from '@azure/msal-angular';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [MsalGuard]
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

3. *src/app/app.component.ts* 에서 로그인 호출을 조정하여 가드 구성에 있는 `authRequest` 세트를 고려합니다. 지금 코드는 다음과 같습니다.

    ```javascript
    import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
    import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
    import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

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

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

## <a name="acquire-a-token"></a>토큰 획득

### <a name="angular-interceptor"></a>Angular 인터셉터

MSAL Angular는 알려진 보호된 리소스에 Angular `http` 클라이언트를 사용하는 발신 요청에 대한 토큰을 자동으로 획득하는 `Interceptor` 클래스를 제공합니다.

1. `Interceptor` 클래스를 구성과 함께 *src/app/app.module.ts* 의 애플리케이션에 대한 공급자로 추가합니다. 이제 코드가 다음과 비슷할 것입니다.

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';
    import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http"; // Import 

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard, MsalInterceptor } from '@azure/msal-angular'; // Import MsalInterceptor
    import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        HttpClientModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_Here',
            authority: 'Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here',
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
          interactionType: InteractionType.Redirect,
          authRequest: {
            scopes: ['user.read']
            }
        }, {
          interactionType: InteractionType.Redirect, // MSAL Interceptor Configuration
          protectedResourceMap: new Map([ 
              ['Enter_the_Graph_Endpoint_Here/v1.0/me', ['user.read']]
          ])
        })
      ],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: MsalInterceptor,
          multi: true
        },
        MsalGuard
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }

    ```

    보호된 리소스는 `protectedResourceMap`으로 제공됩니다. `protectedResourceMap` 컬렉션에서 제공하는 URL은 대/소문자를 구분합니다. 리소스마다 액세스 토큰에서 반환되도록 요청되는 범위를 추가합니다.

    다음은 그 예입니다.

    * Microsoft Graph의 `["user.read"]`
    * 사용자 지정 웹 API의 `["<Application ID URL>/scope"]`(즉, `api://<Application ID>/access_as_user`)
    
    다음 설명대로 `protectedResourceMap`에서 값을 수정합니다.

    |값 이름| 정보|
    |----------|------|
    |`Enter_the_Graph_Endpoint_Here`| 애플리케이션과 통신해야 하는 Microsoft Graph API의 인스턴스입니다. **글로벌** Microsoft Graph API 엔드포인트의 경우 이 문자열의 두 인스턴스를 모두 `https://graph.microsoft.com`으로 바꿉니다. **국가별** 클라우드 배포의 엔드포인트는 Microsoft Graph 설명서의 [국가별 클라우드 배포](/graph/deployments)를 참조하세요.|

2. *src/app/profile/profile.component.ts* 에서 코드를 바꿔 HTTP 요청이 있는 사용자 프로필을 검색합니다.

    ```JavaScript
    import { Component, OnInit } from '@angular/core';
    import { HttpClient } from '@angular/common/http';

    const GRAPH_ENDPOINT = 'Enter_the_Graph_Endpoint_Here/v1.0/me';

    type ProfileType = {
      givenName?: string,
      surname?: string,
      userPrincipalName?: string,
      id?: string
    };

    @Component({
      selector: 'app-profile',
      templateUrl: './profile.component.html',
      styleUrls: ['./profile.component.css']
    })
    export class ProfileComponent implements OnInit {
      profile!: ProfileType;

      constructor(
        private http: HttpClient
      ) { }

      ngOnInit() {
        this.getProfile();
      }

      getProfile() {
        this.http.get(GRAPH_ENDPOINT)
          .subscribe(profile => {
            this.profile = profile;
          });
      }
    }
    ```

3. *src/app/profile/profile.component.html* 에서 UI를 바꿔 프로필 정보를 표시합니다. 

    ```HTML
    <div>
        <p><strong>First Name: </strong> {{profile?.givenName}}</p>
        <p><strong>Last Name: </strong> {{profile?.surname}}</p>
        <p><strong>Email: </strong> {{profile?.userPrincipalName}}</p>
        <p><strong>Id: </strong> {{profile?.id}}</p>
    </div>
    ```

## <a name="sign-out"></a>로그아웃

*src/app/app.component.html* 에서 코드를 업데이트하여 `Logout` 단추를 조건부로 표시합니다.

```HTML
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
  <router-outlet *ngIf="!isIframe"></router-outlet>
</div>
```

### <a name="sign-out-using-redirects"></a>리디렉션을 사용하여 로그아웃

*src/app/app.component.ts* 에서 코드를 업데이트하여 리디렉션을 사용해 사용자를 로그아웃합니다.

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

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

  logout() { // Add log out function here
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
}
```

### <a name="sign-out-using-pop-ups"></a>팝업을 사용하여 로그아웃

*src/app/app.component.ts* 에서 코드를 업데이트하여 팝업을 사용해 사용자를 로그아웃합니다.

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, PopupRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

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
      this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest)
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    } else {
      this.authService.loginPopup()
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    }
  }

  logout() { // Add log out function here
    this.authService.logoutPopup({
      mainWindowRedirectUri: "/"
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

## <a name="test-your-code"></a>코드 테스트

1.  애플리케이션 폴더의 명령줄 프롬프트에서 다음 명령을 실행하여 포트를 수신 대기하도록 웹 서버를 시작합니다.

    ```bash
    npm install
    npm start
    ```
1. 브라우저에서 **http://localhost:4200** 또는 **http://localhost:{port}** 를 입력합니다. 여기서 *port* 는 웹 서버에서 수신 대기하는 포트입니다. 아래와 같은 페이지가 표시됩니다.

    :::image type="content" source="media/tutorial-v2-angular-auth-code/angular-01-not-signed-in.png" alt-text="로그인 대화 상자가 표시된 웹 브라우저":::


### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 프로필에 대한 액세스 권한을 부여하고 사용자를 로그인할 수 있도록 허용하라는 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="웹 브라우저에 표시된 콘텐츠 대화 상자":::

요청된 권한에 동의하면 웹 애플리케이션에 성공적으로 로그인 페이지가 표시됩니다.

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-02-signed-in.png" alt-text="웹 브라우저에서 성공적으로 로그인한 결과":::

### <a name="call-the-graph-api"></a>Graph API 호출

로그인한 후에 **프로필** 을 선택하여 Microsoft Graph API에 대한 호출에서 응답으로 반환된 사용자 프로필 정보를 확인합니다.

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-03-profile-data.png" alt-text="브라우저에 표시된 Microsoft Graph의 프로필 정보":::

## <a name="add-scopes-and-delegated-permissions"></a>범위 및 위임된 권한 추가

Microsoft Graph API에서 사용자 프로필을 읽으려면 _User.Read_ 범위가 필요합니다. _User.Read_ 범위는 Azure Portal에서 만든 모든 앱 등록에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자의 이메일을 나열하기 위해 _Mail.Read_ 범위가 필요합니다.

범위를 추가하면 추가된 범위에 대한 추가 동의를 제공하라는 메시지가 표시될 수 있습니다.

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

여러 부분으로 구성된 문서 시리즈에서 Microsoft ID 플랫폼의 SPA(단일 페이지 애플리케이션) 개발에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [시나리오: 단일 페이지 애플리케이션](scenario-spa-overview.md)
