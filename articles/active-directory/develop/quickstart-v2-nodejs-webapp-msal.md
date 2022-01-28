---
title: '빠른 시작: MSAL 노드를 사용하여 Node.js 웹앱에 인증 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Node.js 웹앱 및 Node.js용 MSAL(Microsoft 인증 라이브러리)을 사용하여 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:js", devx-track-js, mode-api
ms.openlocfilehash: 9eb5b0e60eeddc0c4eba1f21043bf8d3c222e3b4
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803858"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-nodejs-web-app-using-the-auth-code-flow"></a>빠른 시작: 인증 코드 흐름을 사용하여 Node.js 웹앱에서 사용자 로그인 및 액세스 토큰 받기

이 빠른 시작에서는 인증 코드 흐름을 사용하여 Node.js 웹앱에서 사용자를 로그인할 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 코드 샘플은 Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오는 방법도 보여줍니다.

자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

이 빠른 시작에서는 인증 코드 흐름과 함께 Node.js용 Microsoft 인증 라이브러리(MSAL 노드)를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 [무료로 Azure 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

#### <a name="step-1-configure-the-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
이 빠른 시작의 코드 샘플이 작동하려면 클라이언트 암호를 만들고 `http://localhost:3000/redirect` 회신 URL을 추가해야 합니다.
> [!div class="nextstepaction"]
> [자동 변경]()

> [!div class="alert alert-info"]
> ![이미 구성됨](media/quickstart-v2-windows-desktop/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

Node.js를 사용하여 웹 서버에서 프로젝트를 실행합니다.

> [!div class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.

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
