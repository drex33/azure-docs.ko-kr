---
title: Azure Active Directory B2C를 사용하여 샘플 Angular SPA에서 인증 구성
description: Azure Active Directory B2C를 사용하여 Angular SPA에 사용자를 로그인하고 가입시키는 방법을 배웁니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: acce5f3152f8e12bd93640892f9f495834f34b9f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007901"
---
# <a name="configure-authentication-in-a-sample-angular-single-page-application-by-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 샘플 Angular 단일 페이지 애플리케이션에서 인증 구성

이 문서에서는 샘플 Angular SPA(단일 페이지 애플리케이션)을 사용하여 Angular 앱에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 웹 애플리케이션에 사용자가 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0 기반의 인증 프로토콜입니다. 이 Angular 샘플에서는 [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) 및 [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)를 사용합니다. MSAL은 Angular SPA에 인증 및 권한 부여 지원을 간편하게 추가할 수 있도록 하는 Microsoft에서 제공하는 라이브러리입니다.

### <a name="sign-in-flow"></a>로그인 흐름

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자가 앱을 열고 **로그인** 을 선택합니다. 
1. 앱에서 인증 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다.
1. 사용자가 [가입 또는 로그인](add-sign-up-and-sign-in-policy.md)하거나, [암호를 초기화](add-password-reset-policy.md)하거나, [소셜 계정](add-identity-provider.md)으로 로그인합니다.
1. 로그인에 성공하면 Azure AD B2C는 앱에 인증 코드를 반환합니다. 앱은 다음 작업을 수행합니다.
   1. 인증 코드를 ID 토큰, 액세스 토큰, 새로 고침 토큰으로 교환합니다.
   1. ID 토큰 클레임을 읽습니다.
   1. 나중에 사용할 수 있도록 액세스 토큰 및 새로 고침 토큰을 메모리 내 캐시에 저장합니다. 액세스 토큰을 통해 웹 API와 같은 보호된 리소스를 호출할 수 있습니다. 새로 고침 토큰을 사용하여 새 액세스 토큰을 획득합니다.

### <a name="app-registration"></a>앱 등록

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록해야 합니다.  

- *단일 페이지 애플리케이션*(Angular) 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다. 앱을 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증하면 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 이 문서는 예제 **앱 ID: 1** 을 사용합니다.

- *웹 API* 등록을 사용하면 앱에서 보안 웹 API를 호출할 수 있습니다. 등록은 웹 API 권한(범위)을 노출합니다. 앱 등록 프로세스는 웹 API를 고유하게 식별하는 애플리케이션 ID를 생성합니다. 이 문서에서는 예제 **앱 ID: 2** 를 사용합니다. 웹 API 범위(**앱 ID: 2**)에 앱(**앱 ID: 1**) 권한을 부여합니다.  

다음 다이어그램에서는 앱 등록 및 앱 아키텍처에 대해 설명합니다.

![웹 API, 등록 및 토큰을 사용하는 단일 페이지 애플리케이션을 설명하는 다이어그램입니다.](./media/configure-authentication-sample-angular-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>웹 API 호출

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>로그아웃 흐름

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 절차를 따르기 전에 컴퓨터가 실행 중인지 확인합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Node.js 런타임](https://nodejs.org/en/download/) 및 [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* [Angular CLI](https://angular.io/cli)

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-angular-spa-and-api"></a>2단계: Angular SPA 및 API 등록

이 단계에서는 Angular SPA 및 web API 앱에 대한 등록을 만듭니다. 또한 web API의 범위를 지정 합니다.

### <a name="21-register-the-web-api-application"></a>2.1 웹 API 애플리케이션 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-angular-app"></a>2.3 Angular 앱 등록

다음 단계에 따라 Angular 앱 등록을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 에 애플리케이션의 이름을 입력합니다. 예를 들어, **MyApp** 을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **SPA(단일 페이지 애플리케이션)** 를 선택한 다음, URL 상자에 `http://localhost:4200`을 입력합니다.
1. **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. 웹 애플리케이션을 구성하는 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 값을 기록합니다.
    ![Angular 애플리케이션 ID를 얻는 방법을 보여 주는 스크린샷](./media/configure-authentication-sample-angular-spa-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-angular-sample-code"></a>3단계: Angular 샘플 코드 가져오기

이 샘플은 Angular 단일 페이지 애플리케이션에서 사용자 가입 및 로그인에 Azure AD B2C를 사용하는 방법을 보여 줍니다. 그러면 앱이 액세스 토큰을 획득하고 보호된 웹 API를 호출합니다.

  샘플의 [.zip 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/archive/refs/heads/main.zip)하거나 다음 명령을 사용하여 [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)에서 샘플을 복제합니다.

  ```
  git clone https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial.git
  ```

### <a name="31-configure-the-angular-sample"></a>3.1 Angular 샘플 구성

이제 SPA 샘플을 얻었으므로 Azure AD B2C 및 웹 API 값으로 코드를 업데이트합니다. 샘플 폴더의 *src/app* 폴더에서 *auth.config.ts* 파일을 엽니다. 키를 해당 값으로 업데이트합니다.  


|섹션  |Key  |값  |
|---------|---------|---------|
| b2cPolicies | 이름 |사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다. |
| b2cPolicies | 인증 기관 | `your-tenant-name`을 Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)으로 바꿉니다. 예를 들면 `contoso.onmicrosoft.com`를 사용합니다. 그런 다음, 정책 이름을 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 흐름 또는 사용자 지정 정책으로 바꿉니다. 예: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` |
| b2cPolicies | authorityDomain|Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. 예: `contoso.onmicrosoft.com` |
| 구성 | clientId | [2.3단계](#23-register-the-angular-app)의 Angular 애플리케이션 ID입니다. |
| protectedResources| 엔드포인트(endpoint)| 웹 API의 URL입니다(`http://localhost:5000/api/todolist`). |
| protectedResources| 범위| [2.2단계](#22-configure-scopes)에서 만든 웹 API 범위입니다. 예: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]` |

결과 *src/app/auth-config.ts* 코드는 다음 샘플과 유사해야 합니다.

```typescript
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
    // More configuration here
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
```

## <a name="step-4-get-the-web-api-sample-code"></a>4단계: 웹 API 샘플 코드 가져오기

웹 API를 등록하고 해당 범위를 정의했으므로, 이제 Azure AD B2C 테넌트에서 작동하도록 웹 API 코드를 구성합니다.

[\*.zip 보관 파일](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)을 다운로드하거나 GitHub에서 웹 API 프로젝트 샘플을 복제합니다. 다음 명령을 사용하여 GitHub에서 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 프로젝트로 직접 이동할 수도 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-configure-the-web-api"></a>4.1 웹 API 구성

샘플 폴더에서 *config.json* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 웹 API 앱은 이 정보를 사용하여 웹앱이 전달자 토큰으로 전달하는 액세스 토큰의 유효성을 검사합니다. 앱 설정의 다음 속성을 업데이트합니다.

|섹션  |Key  |값  |
|---------|---------|---------|
|자격 증명|tenantName| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다. 예: `contoso`|
|자격 증명|clientID| [2.1](#21-register-the-web-api-application)단계의 웹 API 애플리케이션 ID입니다. [앞의 다이어그램](#app-registration)에서 애플리케이션은 **앱 ID: 2** 가 있습니다.|
|자격 증명| 발급자| (선택 사항) 토큰 발급자 `iss` 클레임 값입니다. 기본적으로 Azure AD B2C `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/` 형식으로 토큰을 반환합니다. Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫번째 부분을 `<your-tenant-name>`로 바꿉니다. `<your-tenant-ID>`을 [Azure AD B2C 테넌트 ID](tenant-management.md#get-your-tenant-id)로 바꿉니다. |
|정책|policyName|사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다. 애플리케이션에서 여러 사용자 흐름 또는 사용자 지정 정책을 사용하는 경우 하나만 지정합니다. 예를 들어, 가입 또는 로그인 사용자 흐름을 사용합니다.|
| resource| scope | [2.5](#25-grant-permissions)단계에서 웹 API 애플리케이션 등록의 범위입니다. |

최종 구성 파일은 다음 JSON과 같아야 합니다.

```json
{
    "credentials": {
        "tenantName": "<your-tenant-namee>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    // More settings here
} 
```

## <a name="step-5-run-the-angular-spa-and-web-api"></a>5단계: Angular SPA 및 웹 API 실행

이제 API에 대한 Angular 범위가 지정된 액세스 권한을 테스트할 준비가 되었습니다. 이 단계에서는 로컬 머신에서 웹 API와 샘플 Angular 애플리케이션을 모두 실행합니다. 그런 다음, Angular 애플리케이션에 로그인하고 **TodoList** 단추를 선택하여 보호된 API에 대한 요청을 시작합니다.

### <a name="run-the-web-api"></a>웹 API 실행

1. 콘솔 창을 열고, 웹 API 샘플이 있는 디렉터리로 변경합니다. 예를 들면 다음과 같습니다.

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    node index.js
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-angular-application"></a>Angular 애플리케이션 실행

1. 다른 콘솔 창을 열고 Angular 샘플을 포함하는 디렉터리로 변경합니다. 예를 들면 다음과 같습니다.

    ```console
    cd ms-identity-javascript-angular-tutorial-main/3-Authorization-II/2-call-api-b2c/SPA
    ```

1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    npm start
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

    ```console
    Listening on port 4200...
    ```

1. 브라우저에서 `http://localhost:4200`으로 이동하여 애플리케이션을 확인합니다.
1. **로그인** 을 선택합니다.

    ![로그인 링크가 있는 Angular 샘플 앱을 보여 주는 스크린샷](./media/configure-authentication-sample-angular-spa-app/sample-app-sign-in.png)

1. 등록 또는 로그인 프로세스를 완료합니다.
1. 로그인에 성공하면 프로필이 표시됩니다. 메뉴에서 **ToDoList** 를 선택합니다.

    ![사용자 프로필이 있는 Angular 샘플 앱과 할 일 목록에 대한 호출을 보여 주는 스크린샷](./media/configure-authentication-sample-angular-spa-app/sample-app-result.png)

1. **추가를** 선택하여 목록에 새 항목을 추가하거나 아이콘을 사용하여 항목을 삭제하거나 편집합니다.

    ![할 일 목록에 대한 Angular 샘플 앱 호출을 보여 주는 스크린샷](./media/configure-authentication-sample-angular-spa-app/sample-app-calls-web-api.png)

## <a name="deploy-your-application"></a>애플리케이션 배포 

프로덕션 애플리케이션에서 앱 등록용 리디렉션 URI는 일반적으로 앱이 실행되는, 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://contoso.com`). 

언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다. 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.
* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [코드 샘플에 대해 자세히 알아보기](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)
* [사용자 고유의 Angular 애플리케이션에서 인증을 사용하도록 설정](enable-authentication-angular-spa-app.md)
* [Angular 애플리케이션에서 인증 옵션](enable-authentication-angular-spa-app-options.md) 구성
* [고유 웹 API에서 인증을 사용하도록 설정](enable-authentication-web-api.md)
