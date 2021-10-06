---
title: '빠른 시작: MSAL 노드를 사용하여 Node.js 웹앱에 인증 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Node.js 웹앱 및 Node.js용 MSAL(Microsoft 인증 라이브러리)을 사용하여 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 3421c519c4ab059cda1d413db3906b3f1ed76ca0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594691"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>빠른 시작: 인증 코드 흐름을 사용하여 사용자를 로그인하고 노드 웹앱에서 액세스 토큰을 가져옵니다.

이 빠른 시작에서는 인증 코드 흐름을 사용하여 Node.js 웹앱에서 사용자를 로그인할 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 코드 샘플은 Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오는 방법도 보여줍니다.

자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

이 빠른 시작에서는 인증 코드 흐름과 함께 Node.js용 Microsoft 인증 라이브러리(MSAL 노드)를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 [무료로 Azure 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>빠른 시작 애플리케이션 등록 및 다운로드
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. 애플리케이션의 **이름** 을 입력합니다. 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** 을 선택합니다.
> 1. **리디렉션 URI** 값을 `http://localhost:3000/redirect`으로 설정합니다.
> 1. **등록** 을 선택합니다.
> 1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. **관리** 아래에서 **인증서 및 비밀** > **새 클라이언트 암호** 를 선택합니다.  설명을 비워 두고 기본 만료를 그대로 둔 다음, **추가** 를 선택합니다.
> 1. 나중에 사용할 수 있도록 **클라이언트 암호** 의 값을 기록해 둡니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 클라이언트 암호를 만들고 `http://localhost:3000/redirect` 회신 URL을 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-windows-desktop/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

> [!div renderon="docs"]
> Node.js를 사용하여 웹 서버로 프로젝트를 실행하려면 [주요 프로젝트 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)합니다.

> [!div renderon="portal" class="sxs-lookup"]
> Node.js를 사용하여 웹 서버에서 프로젝트를 실행합니다.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>3단계: 노드 앱 구성
>
> 프로젝트를 추출하고 *ms-identity-node-main* 폴더를 연 다음, *index.js* 파일을 엽니다.
>
> 애플리케이션(클라이언트) ID로 `clientID` 값을 설정한 다음, 클라이언트 암호로 `clientSecret` 값을 설정합니다.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> `config` 섹션에서 값을 수정합니다.
>
> - `Enter_the_Application_Id_Here`는 등록한 애플리케이션의 애플리케이션(클라이언트) ID입니다.
>
>    애플리케이션(클라이언트) ID 값을 찾으려면 Azure Portal에서 앱 등록의 **개요** 페이지로 이동합니다.
> - `Enter_the_Client_Secret_Here`는 등록한 애플리케이션의 클라이언트 암호입니다.
>
>    새 클라이언트 암호를 검색하거나 생성하려면 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
>
> 기본 `authority` 값은 기본(글로벌) Azure 클라우드를 나타냅니다.
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

Node.js를 사용하여 프로젝트를 실행합니다.

1. 서버를 시작하려면 프로젝트의 디렉터리 내에서 다음 명령을 실행합니다.

    ```console
    npm install
    npm start
    ```

1. [https://editor.swagger.io](`http://localhost:3000/`) 로 이동합니다.

1. **로그인** 을 선택하여 로그인 프로세스를 시작합니다.

    사용자가 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인되면 명령줄에 로그 메시지가 표시됩니다.

## <a name="more-information"></a>자세한 정보

### <a name="how-the-sample-works"></a>샘플 작동 방법

이 샘플은 localhost, 포트 3000에서 웹 서버를 호스팅합니다. 웹 브라우저가 이 사이트에 액세스하면 샘플은 즉시 사용자를 Microsoft 인증 페이지로 리디렉션합니다. 이 때문에 샘플에는 HTML 또는 표시 요소가 포함되지 않습니다. 인증에 성공하면 "OK" 메시지가 표시됩니다.

### <a name="msal-node"></a>MSAL 노드

MSAL 노드 라이브러리는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청합니다. Node.js 패키지 관리자(npm)를 사용하여 최신 버전을 다운로드할 수 있습니다.

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기존 웹앱에 인증 추가 - GitHub 코드 샘플 >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
