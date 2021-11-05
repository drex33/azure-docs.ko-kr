---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 52549e92a066df3caea479b0a20b21c747cb5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461197"
---
## <a name="set-up-prerequisites"></a>필수 조건 설정

- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장)

## <a name="set-up"></a>설정

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir communication-access-tokens-quickstart && cd communication-access-tokens-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 `package.json` 파일을 만듭니다.

```console
npm init -y
```

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services ID SDK를 설치합니다.

```console

npm install @azure/communication-identity@beta --save
npm install @azure/msal-node --save
npm install express --save

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

## <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 새 텍스트 파일 열기
1. `require` 호출을 추가하여 `CommunicationIdentityClient`를 로드합니다.
1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

    ```javascript
    const { CommunicationIdentityClient } = require('@azure/communication-identity');
    const express = require("express");
    const msal = require('@azure/msal-node');
    
    const SERVER_PORT = process.env.PORT || 80;
    const REDIRECT_URI = "http://localhost"; 
    
    // Quickstart code goes here
    
    app.listen(SERVER_PORT, () => console.log(`Communication access token application started on ${SERVER_PORT}!`))
    
    ```

1. 새 파일을 `access-tokens-quickstart` 디렉터리에 `issue-communication-access-token.js`로 저장합니다.

### <a name="step-1-receive-the-azure-ad-user-token-via-the-msal-library"></a>1단계: MSAL 라이브러리를 통해 Azure AD 사용자 토큰 받기

토큰 교환 흐름의 첫 번째 단계는 [Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md)를 사용하여 Teams 사용자에 대한 토큰을 가져오는 것입니다.

```javascript
const msalConfig = {
    auth: {
        clientId: "<contoso_application_id>",
        authority: "https://login.microsoftonline.com/<contoso_tenant_id>",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const provider = new msal.CryptoProvider();

const app = express();

app.get('/', async (req, res) => {
    const {verifier, challenge} = await provider.generatePkceCodes();
    const authCodeUrlParameters = {
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
        codeChallenge: challenge, 
        codeChallengeMethod: "S256"
    };

    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

app.get('/redirect', async (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["https://auth.msft.communication.azure.com/VoIP"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("Response: ", response);
        //TODO: the following code snippets go here
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>2단계: CommunicationIdentityClient 초기화

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`then` 메서드에 다음 코드를 추가합니다.

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>3단계: Teams 액세스 토큰에 대한 Azure AD 사용자 토큰 교환

`getTokenForTeamsUser` 메서드를 사용하여 Azure Communication Services SDK와 함께 사용할 수 있는 Teams 사용자에 대한 액세스 토큰을 발급합니다.

```javascript
let accessToken = await identityClient.getTokenForTeamsUser(teamsToken);
console.log(`Token: ${accessToken}`);
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-communication-access-token.js* 파일이 포함된 디렉터리로 이동한 후, 다음 `node` 명령을 실행하여 앱을 실행합니다.

```console
node ./issue-communication-access-token.js
```
