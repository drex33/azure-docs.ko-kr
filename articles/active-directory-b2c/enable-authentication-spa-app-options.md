---
title: Azure Active Directory B2C를 사용하여 SPA 애플리케이션 옵션 활성화
description: 이 문서에서는 SPA 애플리케이션을 활성화하는 여러 가지 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 675db9c9d0a21b805ce8b4edeaff1a3b29ae7bfd
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529632"
---
# <a name="configure-authentication-options-in-a-single-page-application-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 단일 페이지 애플리케이션에서 인증 옵션 구성

이 문서에서는 SPA(단일 페이지 애플리케이션)에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용자 지정하고 개선하는 방법을 설명합니다. 

시작하기 전에 다음 문서: [샘플 웹 애플리케이션에서 인증 구성](configure-authentication-sample-spa-app.md)을 숙지합니다.

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 사용자 지정 도메인 및 테넌트 ID를 사용하려면 [사용자 지정 도메인 사용](custom-domain.md)의 지침을 따릅니다. MSAL(Microsoft 인증 라이브러리) 구성 개체를 찾고 사용자 지정 도메인 이름 및 테넌트 ID를 사용하도록 *인증 기관* 및 *knownAuthorities* 를 변경합니다.

다음 JavaScript 코드는 변경 *전* 의 MSAL 구성 개체를 보여 줍니다. 

```Javascript
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

다음 JavaScript 코드는 변경 *후* 의 MSAL 구성 개체를 보여 줍니다. 

```Javascript
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

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우 [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. 개체를 만들어 **login_hint** 에 저장하고, 이 개체를 **MSAL loginPopup()** 메서드에 전달합니다.

    ```javascript
    let loginRequest = {
        loginHint: "bob@contoso.com"
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. 개체를 만들어 **extraQueryParameters** 에 저장하고, 이 개체를 **MSAL loginPopup()** 메서드에 전달합니다.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {domain_hint: 'facebook.com'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성합니다](language-customization.md).
1. 개체를 만들어 **extraQueryParameters** 에 저장하고, 이 개체를 **MSAL loginPopup()** 메서드에 전달합니다.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {ui_locales: 'en-us'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. 개체를 만들어 **extraQueryParameters** 에 저장하고, 이 개체를 **MSAL loginPopup()** 메서드에 전달합니다.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {campaignId: 'germany-promotion'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. 개체를 만들어 **extraQueryParameters** 에 저장하고, 이 개체를 **MSAL loginPopup()** 메서드에 전달합니다.

    ```javascript
    let loginRequest = {
         extraQueryParameters: {id_token_hint: 'id-token-hint-value'}
    }
    
    myMSALObj.loginPopup(loginRequest);
    ```

## <a name="secure-your-logout-redirect"></a>로그아웃 리디렉션 보안

로그아웃 후 사용자는 애플리케이션에 대해 지정된 회신 URL에 관계없이 `post_logout_redirect_uri` 매개 변수에 지정된 URI로 리디렉션됩니다. 그러나 유효한 `id_token_hint`가 전달되고 [로그아웃 요청에서 ID 토큰 요구](session-behavior.md#secure-your-logout-redirect)가 켜져 있는 경우 Azure AD B2C는 리디렉션을 수행하기 전 `post_logout_redirect_uri` 값이 애플리케이션의 구성된 리디렉션 URI 중 하나와 일치하는지 확인합니다. 애플리케이션에 일치하는 회신 URL이 구성되지 않은 경우 오류 메시지가 표시되고 사용자는 리디렉션되지 않습니다.

보안 로그아웃 리디렉션 URI를 지원하려면 다음 단계를 수행합니다.

1. `id_token`을 저장할 전역적으로 액세스 가능한 변수를 만듭니다.
    ```javascript
    let id_token = "";
    ```
    
1. MSAL `handleResponse`함수에서 `authenticationResult` 개체에서 `id_token` 변수로 `id_token`을 구문 분석합니다.
    ```javascript
    function handleResponse(response) {
        if (response !== null) {
            setAccount(response.account);
            id_token = response.idToken;
        } else {
            selectAccount();
        }
    }
    ```
    
1. `signOut` 함수에서 `id_token_hint` 매개 변수를 **logoutRequest** 개체에 추가합니다.
    ```javascript
    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
            //set id_token_hint to the id_token value
            idTokenHint : id_token,
            mainWindowRedirectUri: msalConfig.auth.redirectUri
        };
        myMSALObj.logoutPopup(logoutRequest);
    }
    ```

위의 예제에서 로그아웃 요청으로 전달된 **post_logout_redirect_uri** 의 형식은 `https://your-app.com/`입니다. 이 URL을 애플리케이션 등록의 회신 URL에 추가해야 합니다.

## <a name="enable-single-logout"></a>단일 로그아웃 사용

Azure AD B2C에서 단일 로그아웃은 OpenId Connect 프런트 채널 로그아웃을 사용하여 사용자가 Azure AD B2C를 통해 로그인한 모든 애플리케이션에 대한 로그아웃 요청을 만듭니다.

이러한 로그아웃 요청은 숨겨진 Iframe의 Azure AD B2C 로그아웃 페이지에서 이루어집니다. Iframe은 Azure AD B2C가 로그인된 것으로 기록한 앱에 대해 등록된 모든 프런트 채널 로그아웃 엔드포인트에 대해 HTTP 요청을 합니다. 

각 애플리케이션에 대한 로그아웃 엔드포인트는 **MSAL logout()** 메서드를 호출해야 합니다. 또한 `allowRedirectInIframe`을 `true`로 설정하여 이 시나리오의 Iframe 내에서 실행하도록 MSAL을 명시적으로 구성해야 합니다.

다음 코드 샘플은 `allowRedirectInIframe`을 `true`로 설정합니다.

```javascript
const msalConfig = {
    auth: {
        clientId: "enter_client_id_here",
        .....
    },
    cache: {
        cacheLocation: "..",
        ....
    },
    system: {
        allowRedirectInIframe: true
    };
}

async function logoutSilent(MSAL) {
   return MSAL.logout({
      onRedirectNavigate: (url) => {
         return false;
       }
```

## <a name="next-steps"></a>다음 단계

[MSAL.js 구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md)에 대해 자세히 알아봅니다.
