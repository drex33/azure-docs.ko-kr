---
title: Azure Active Directory B2C를 사용하여 Angular 애플리케이션 옵션을 사용하도록 설정합니다.
description: 여러 가지 방법으로 Angular 애플리케이션 옵션을 사용하도록 설정합니다.
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
ms.openlocfilehash: 94057e0b1d566d092089725dea96a53e907f2a71
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041104"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Angular 애플리케이션에서 인증 옵션 구성

이 문서에서는 Angular SPA(단일 페이지 애플리케이션)에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용자 지정하고 개선할 수 있는 방법을 설명합니다. 시작하기 전에 [Angular SPA에서 인증을 구성](configure-authentication-sample-angular-spa-app.md)하거나 [Angular SPA에서 인증을 사용하도록 설정](enable-authentication-angular-spa-app.md)하는 문서를 숙지합니다.


## <a name="sign-in-and-sign-out-behavior"></a>로그인 및 로그아웃 동작


다음 두 가지 방법으로 MSAL.js를 사용하여 사용자를 로그인하도록 단일 페이지 애플리케이션을 구성할 수 있습니다.

- **팝업 창** - 인증이 팝업 창에서 발생하고 애플리케이션의 상태가 유지됩니다. 인증하는 동안 사용자가 애플리케이션 페이지에서 벗어나지 않도록 하려면 이 방법을 사용합니다.  [Internet Explorer의 팝업 창에는 알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)가 있습니다.
  - 팝업 창으로 로그인하려면 `src/app/app.component.ts` 클래스에서 `loginPopup` 메서드를 사용합니다.  
  - `src/app/app.module.ts` 클래스에서 `interactionType` 특성을 `InteractionType.Popup`으로 설정합니다.
  - 팝업 창에서 로그아웃하려면 `src/app/app.component.ts` 클래스에서 `logoutPopup` 메서드를 사용합니다. 요청의 일부로 `mainWindowRedirectUri`를 전달하여 로그아웃이 완료된 후에 홈페이지 또는 로그인 페이지와 같은 다른 페이지로 주 창을 리디렉션하도록 `logoutPopup`을 구성할 수도 있습니다.
- **리디렉션**: 인증 흐름을 완료하기 위해 Azure AD B2C로 리디렉션됩니다. 사용자에게 팝업 창을 사용하지 않게 설정하는 브라우저 제약 조건이나 정책이 있으면 이 방법을 사용합니다. 
  - 리디렉션을 사용하여 로그인하려면 `src/app/app.component.ts` 클래스에서 `loginRedirect` 메서드를 사용합니다.  
  - `src/app/app.module.ts` 클래스에서 `interactionType` 특성을 `InteractionType.Redirect`으로 설정합니다.
  - 리디렉션으로 로그아웃하려면 `src/app/app.component.ts` 클래스에서 `logoutRedirect` 메서드를 사용합니다. `postLogoutRedirectUri`를 설정하여 로그아웃 후 리디렉션해야 하는 URI를 구성합니다. 애플리케이션 등록에서 이 URI를 리디렉션 URI로 등록해야 합니다.
  
다음 샘플에서는 로그인 및 로그아웃하는 방법을 보여 줍니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
//src/app/app.component.ts
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
```

---

MSAL Angular 라이브러리에는 대화형 로그인(사용자가 로그인 단추 선택), MSAL Guard 및 MSAL Interceptor의 세 가지 로그인 흐름이 있습니다. MSAL Guard 및 MSAL Interceptor 구성은 사용자가 유효한 액세스 토큰 없이 보호된 리소스에 액세스하려고 할 때 적용됩니다. 이러한 경우 MSAL 라이브러리는 사용자가 강제로 로그인하도록 합니다. 

다음 샘플에서는 팝업 창 또는 리디렉션을 사용하는 로그인을 위해 MSAL Guard 및 MSAL Interceptor를 구성하는 방법을 보여 줍니다. 

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우, [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. 기존 `PopupRequest` 또는 `RedirectRequest` MSAL 구성 개체를 만들거나 사용합니다.
1. 해당 로그인 힌트를 사용하여 `loginHint` 특성을 설정합니다. 

다음 코드 조각은 로그인 힌트 매개 변수를 전달하는 방법을 보여 줍니다. 특성 값으로 `bob@contoso.com`을 사용합니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. 기존 `PopupRequest` 또는 `RedirectRequest` MSAL 구성 개체를 만들거나 사용합니다.
1. 해당 도메인 힌트를 사용하여 `domainHint` 특성을 설정합니다.

다음 코드 조각은 도메인 힌트 매개 변수를 전달하는 방법을 보여 줍니다. 특성 값으로 `facebook.com`를 사용합니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성](language-customization.md)합니다. 
1. `extraQueryParameters` 특성이 있는 기존 `PopupRequest` 또는 `RedirectRequest` MSAL 구성 개체를 만들거나 사용합니다.
1. 해당 언어 코드가 있는 `ui_locales` 매개 변수를 `extraQueryParameters` 특성에 추가합니다.

다음 코드 조각은 도메인 힌트 매개 변수를 전달하는 방법을 보여 줍니다. 특성 값으로 `es-es`를 사용합니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. `extraQueryParameters` 특성이 있는 기존 `PopupRequest` 또는 `RedirectRequest` MSAL 구성 개체를 만들거나 사용합니다.
1. `campaignId`와 같은 사용자 지정 쿼리 문자열 매개 변수를 추가합니다. 매개 변수 값을 설정합니다. 

다음 코드 조각은 사용자 지정 쿼리 문자열 매개 변수를 전달하는 방법을 보여 줍니다. 특성 값으로 `germany-promotion`을 사용합니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 사용자 지정 정책에서 [ID 토큰 힌트의 기술 프로필](id-token-hint.md)을 정의합니다.
1. `extraQueryParameters` 특성이 있는 기존 `PopupRequest` 또는 `RedirectRequest` MSAL 구성 개체를 만들거나 사용합니다.
1. ID 토큰을 저장하는 해당 변수를 사용하여 `id_token_hint` 매개 변수를 추가합니다.

다음 코드 조각은 ID 토큰 힌트를 정의하는 방법을 보여 줍니다.

#### <a name="pop-up"></a>[팝업](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[리디렉션](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 테넌트 ID에 대한 사용자 지정 도메인을 사용하려면 [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따릅니다. `src/app/auth-config.ts` MSAL 구성 개체를 열고 `authorities` 및 `knownAuthorities`를 변경하여 사용자 지정 도메인 이름과 테넌트 ID를 사용합니다.  

다음 JavaScript는 변경 전의 MSAL 구성 개체를 보여 줍니다. 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

다음 JavaScript는 변경 후의 MSAL 구성 개체를 보여 줍니다. 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

Angular [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md)을 구성하려면 *src/app/auth-config.ts* 에서 다음 키를 구성합니다.

- `loggerCallback`는 로거 콜백 함수입니다. 
- `logLevel`에서는 로깅 수준을 지정할 수 있습니다. 가능한 값은 `Error`, `Warning`, `Info` 및 `Verbose`입니다.
- `piiLoggingEnabled`에서는 개인 데이터를 입력할 수 있습니다. 가능한 값은 `true` 또는 `false`입니다.
 
다음 코드 조각은 MSAL 로깅을 구성하는 방법을 보여 줍니다.

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>다음 단계

- 자세한 정보: [MSAL.js 구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
