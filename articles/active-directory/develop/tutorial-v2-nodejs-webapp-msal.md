---
title: '자습서: Node.js 및 Express 웹앱에서 사용자 로그인 | Azure'
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 웹앱에서 사용자를 로그인시키기 위한 지원 기능을 추가합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: marsma
ms.openlocfilehash: 1a61eb908221d4fe2eea9d74d4fbc02c7660ba85
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507931"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>자습서: Node.js 및 Express 웹앱에서 사용자 로그인

이 자습서에서는 사용자를 로그인시키는 웹앱을 빌드합니다. 빌드한 웹앱은 [Node용 MSAL(Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)을 사용합니다.

다음을 수행하려면 이 자습서의 단계를 따릅니다.

> [!div class="checklist"]
> - Azure Portal에 애플리케이션 등록
> - Express 웹앱 프로젝트 만들기
> - 인증 라이브러리 패키지 설치
> - 앱 등록 세부 정보 추가
> - 사용자 로그인 코드 추가
> - 앱 테스트

## <a name="prerequisites"></a>사전 요구 사항

- [Node.JS](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

## <a name="register-the-application"></a>애플리케이션 등록

먼저 [Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)의 단계를 완료하여 앱을 등록합니다.

앱 등록 시 다음 설정을 사용합니다.

- 이름: `ExpressWebApp`(권장)
- 지원되는 계정 유형: **모든 조직 디렉터리(모든 Azure AD 디렉터리 - 다중 테넌트)의 계정 및 개인 Microsoft 계정(예: Skype, Xbox)**
- 플랫폼 유형: **Web**
- 리디렉션 URI: `http://localhost:3000/redirect`
- 클라이언트 암호: `*********`(이 값은 한 번만 표시되므로 이후 단계에서 사용할 수 있게 기록해 두세요.)

## <a name="create-the-project"></a>프로젝트 만들기

애플리케이션을 호스팅할 폴더를 만듭니다(예: *ExpressWebApp*).

1. 먼저, 터미널에서 프로젝트 디렉터리로 변경하고 다음 `npm` 명령을 실행합니다.

```console
    npm init -y
    npm install --save express
```

2. 다음으로, *index.js* 라는 파일을 만들고 다음 코드를 추가합니다.

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

이제 포트 3000에서 실행되는 간단한 웹 서버를 만들었습니다. 프로젝트의 파일 및 폴더 구조는 다음과 같습니다.

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>인증 라이브러리 설치

터미널에서 프로젝트 디렉터리의 루트를 찾고 NPM을 통해 MSAL Node 패키지를 설치합니다.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>앱 등록 세부 정보 추가

이전에 만든 *index.js* 파일에서 다음 코드를 추가합니다.

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

Azure 앱 등록 포털에서 가져온 값으로 이러한 세부 정보를 채웁니다.

- `Enter_the_Tenant_Id_here`는 다음 중 하나여야 합니다.
  - 애플리케이션에서 *이 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름** 으로 바꿉니다. 예들 들어 `contoso.microsoft.com`입니다.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정* 을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
  - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정* 을 지원하는 경우 이 값을 `common`으로 바꿉니다.
  - *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 `consumers`로 바꿉니다.
- `Enter_the_Application_Id_Here`: 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.
- `Enter_the_Cloud_Instance_Id_Here`: 애플리케이션이 등록된 Azure 클라우드 인스턴스입니다.
  - 기본(또는 *글로벌*) Azure 클라우드의 경우 `https://login.microsoftonline.com`을 입력합니다.
  - **국가별** 클라우드(예: 중국)의 경우 [국가별 클라우드](authentication-national-cloud.md)에서 적절한 값을 찾을 수 있습니다.
- `Enter_the_Client_secret`: 이 값을 이전에 만든 클라이언트 암호로 바꿉니다. 새 키를 생성하려면 Azure Portal의 앱 등록 설정에서 **인증서 및 비밀** 을 사용합니다.

> [!WARNING]
> 소스 코드의 일반 텍스트 비밀은 보안 위험을 가중시킵니다. 이 문서에서는 간단한 설명을 위해 일반 텍스트 클라이언트 암호를 사용합니다. 기밀 클라이언트 애플리케이션(특히 프로덕션 환경에 배포하려는 앱)에는 클라이언트 암호 대신 [인증서 자격 증명](active-directory-certificate-credentials.md)을 사용합니다.

## <a name="add-code-for-user-login"></a>사용자 로그인 코드 추가

이전에 만든 *index.js* 파일에서 다음 코드를 추가합니다.

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>로그인 테스트

애플리케이션 만들기가 완료되었으며 이제 앱의 기능을 테스트할 준비가 되었습니다.

1. 프로젝트 폴더의 루트 내에서 다음 명령을 실행하여 Node.js 콘솔 앱을 시작합니다.

```console
   node index.js
```

2. 브라우저 창을 열고 `http://localhost:3000`으로 이동합니다. 로그인 화면이 표시됩니다.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Azure AD 로그인 화면 표시":::

3. 자격 증명을 입력하면 앱 사용 권한을 승인하라는 동의 화면이 표시됩니다.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Azure AD 동의 화면 표시":::

## <a name="how-the-application-works"></a>애플리케이션 작동 방식

이 자습서에서는 Azure Portal의 Azure AD 앱 등록에서 가져온 매개 변수를 포함하는 구성 개체(*msalConfig*)를 전달하여 MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) 개체를 초기화했습니다. 생성된 웹앱은 [OAuth 2.0 권한 부여 코드 부여 흐름](./v2-oauth2-auth-code-flow.md)을 사용하여 사용자를 로그인시키고 ID 및 액세스 토큰을 가져옵니다.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼에서 Node.js 및 Express 웹 애플리케이션 개발에 대해 자세히 알아보려면 여러 부분으로 구성된 시나리오 시리즈를 참조하세요.

> [!div class="nextstepaction"]
> [시나리오: 사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
