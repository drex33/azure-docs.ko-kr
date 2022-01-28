---
title: '빠른 시작: Node.js 콘솔 앱에서 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Node.js 콘솔 애플리케이션이 앱의 자체 ID를 사용하여 액세스 토큰을 가져오고 Microsoft ID 플랫폼 엔드포인트로 보호되는 API를 호출하는 방법을 보여주는 샘플을 다운로드하고 실행합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: mode-api
ms.openlocfilehash: 86e119de86f9fd9fc6b8efa536de3c627fc4c686
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803896"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>빠른 시작: 앱의 ID를 사용하여 Node.js 콘솔 앱에서 토큰 가져오기 및 Microsoft Graph API 호출

이 빠른 시작에서는 Node.js 콘솔 애플리케이션이 앱의 ID를 사용하여 액세스 토큰을 가져와 Microsoft Graph API를 호출하고 디렉터리에 [사용자 목록](/graph/api/user-list)을 표시하는 방법을 보여 주는 코드 샘플을 다운로드하고 실행합니다. 코드 샘플에서는 사용자의 ID 대신 애플리케이션 ID를 사용하여 무인 작업 또는 Windows 서비스를 실행할 수 있는 방법을 보여줍니다.

이 빠른 시작에서는 [클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md)와 함께 [Node.js용 Microsoft 인증 라이브러리(MSAL 노드)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기


### <a name="download-and-configure-the-sample-app"></a>샘플 앱 다운로드 및 구성

#### <a name="step-1-configure-the-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
이 빠른 시작에 대한 코드 샘플을 작동시키려면 클라이언트 비밀을 만들고, Graph API의 **User.Read.All** 애플리케이션 권한에 추가해야 합니다.
> [!div class="nextstepaction"]
> [이러한 변경 내용 적용]()

> [!div class="alert alert-info"]
> ![이미 구성됨](media/quickstart-v2-netcore-daemon/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-nodejs-sample-project"></a>2단계: Node.js 샘플 프로젝트 다운로드

> [!div class="sxs-lookup nextstepaction"]
> [코드 샘플 다운로드](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>3단계: 관리자 동의

이 시점에서 애플리케이션을 실행하려고 시도하면 *HTTP 403 - 사용할 수 없음* 오류: `Insufficient privileges to complete the operation` 메시지가 표시됩니다. 모든 *앱 전용 권한* 에는 **관리자 동의** 가 필요하기 때문에 이 오류가 발생합니다. 디렉터리의 전역 관리자가 애플리케이션에 동의해야 합니다. 역할에 따라 아래 옵션 중 하나를 선택합니다.

##### <a name="global-tenant-administrator"></a>글로벌 테넌트 관리자

글로벌 관리자인 경우 **API 사용 권한** 페이지로 이동하고 **Enter_the_Tenant_Name_Here에 대한 관리자 동의 부여** 를 선택합니다.
> > [!div id="apipermissionspage"]
> > [API 사용 권한 페이지로 이동]()

##### <a name="standard-user"></a>표준 사용자

테넌트의 표준 사용자인 경우 전역 관리자에게 애플리케이션에 대한 **관리자 동의** 부여를 요청해야 합니다. 이렇게 하려면 관리자에게 다음 URL을 제공합니다.

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

#### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

명령 프롬프트 또는 콘솔에서 `package.json`이 있는 샘플의 루트 폴더를 찾습니다. 이 샘플의 종속성을 한 번 설치해야 합니다.

```console
npm install
```

그런 다음, 명령 프롬프트 또는 콘솔을 통해 애플리케이션을 실행합니다.

```console
node . --op getUsers
```

콘솔에서 Azure AD 디렉터리의 사용자 목록을 나타내는 일부 JSON 조각을 출력하는 것을 볼 수 있습니다.

## <a name="about-the-code"></a>코드 정보

아래에서는 애플리케이션 예제의 몇 가지 중요한 측면에 대해 설명합니다.

### <a name="msal-node"></a>MSAL 노드

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)는 사용자를 로그인시키고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 라이브러리입니다. 설명한 것과 같이 이 빠른 시작은 위임된 권한 대신 애플리케이션 권한(애플리케이션 소유 ID 사용)으로 토큰을 요청합니다. 이 경우에 사용되는 인증 흐름을 [OAuth 2.0 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)이라고 합니다. 디먼 앱에서 MSAL Node를 사용하는 방법에 대한 자세한 내용은 [시나리오: 디먼 애플리케이션](scenario-daemon-overview.md)을 참조하세요.

 다음 npm 명령을 실행하여 MSAL Node를 설치할 수 있습니다.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```javascript
const msal = require('@azure/msal-node');
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | 위치: |Description |
> |---------|---------|
> | `clientId` | Azure Portal에 등록된 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다. 이 값은 Azure Portal에서 앱의 **개요** 페이지에 있습니다. |
> | `authority`    | 사용자가 인증하는 STS 엔드포인트 일반적으로 퍼블릭 클라우드에 대한 `https://login.microsoftonline.com/{tenant}`입니다. 여기서 {tenant}는 테넌트의 이름 또는 테넌트 ID입니다.|
> | `clientSecret` | Azure Portal에서 애플리케이션에 대한 클라이언트 비밀이 생성됩니다. |

자세한 내용은 [`ConfidentialClientApplication`에 대한 참조 설명서](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)를 참조하세요.

### <a name="requesting-tokens"></a>토큰 요청

앱의 ID를 사용하여 토큰을 요청하려면 `acquireTokenByClientCredential` 메서드를 사용합니다.

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |위치:| Description |
> |---------|---------|
> | `tokenRequest` | 요청된 범위를 포함합니다. 비밀 클라이언트의 경우 요청되는 범위가 Azure Portal에서 설정된 앱 개체에서 정적으로 정의된 것임을 나타내기 위해 `{Application ID URI}/.default`와 유사한 양식을 사용해야 합니다(Microsoft Graph의 경우 `{Application ID URI}`는 `https://graph.microsoft.com`을 가리킴). 사용자 지정 웹 API의 경우 `{Application ID URI}`는 Azure Portal의 애플리케이션 등록에서 **API 노출** 섹션 아래에 정의됩니다. |
> | `tokenResponse` | 응답에는 요청된 범위에 대한 액세스 토큰이 포함되어 있습니다. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

MSAL Node를 사용한 디먼/콘솔 앱 개발에 대해 자세히 알아보려면 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 디먼 애플리케이션](tutorial-v2-nodejs-console.md)
