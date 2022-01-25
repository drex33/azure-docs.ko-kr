---
title: '빠른 시작: Python 디먼에서 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Python 프로세스가 앱의 자체 ID를 사용하여 액세스 토큰을 가져오고 Microsoft ID 플랫폼으로 보호된 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: 56da3c2e7b52ffc9cfc0f4bdb2e4c83bb031965f
ms.sourcegitcommit: b55c580fe2bb9fbf275ddf414d547ddde8d71d8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2022
ms.locfileid: "136832026"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>빠른 시작: 앱의 ID를 사용하여 Python 콘솔 앱에서 토큰 가져오기 및 Microsoft Graph API 호출

이 빠른 시작에서는 Python 애플리케이션이 앱의 ID를 사용하여 액세스 토큰을 가져와 Microsoft Graph API를 호출하고 디렉터리에 [사용자 목록](/graph/api/user-list)을 표시하는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 코드 샘플에서는 사용자의 ID 대신 애플리케이션 ID를 사용하여 무인 작업 또는 Windows 서비스를 실행할 수 있는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 샘플을 실행하려면 다음이 필요합니다.

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) 또는 [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div class="sxs-lookup"]
### <a name="download-and-configure-the-quickstart-app"></a>빠른 시작 앱 다운로드 및 구성

#### <a name="step-1-configure-your-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
이 빠른 시작의 코드 샘플이 작동하려면 클라이언트 암호를 만들고 Graph API의 **User.Read.All** 애플리케이션 권한을 추가합니다.
> [!div class="nextstepaction"]
> [이러한 변경 내용 적용]()

> [!div class="alert alert-info"]
> ![이미 구성됨](media/quickstart-v2-netcore-daemon/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-python-project"></a>2단계: Python 프로젝트 다운로드

> [!div class="sxs-lookup nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>3단계: 관리자 동의

이 시점에서 애플리케이션을 실행하려고 시도하면 *HTTP 403 - 사용할 수 없음* 오류: `Insufficient privileges to complete the operation` 메시지가 표시됩니다. 모든 *앱 전용 권한* 에는 관리자 동의가 필요하기 때문에 이 오류가 발생합니다. 디렉터리의 글로벌 관리자가 애플리케이션에 동의해야 합니다. 역할에 따라 아래 옵션 중 하나를 선택합니다.

##### <a name="global-tenant-administrator"></a>글로벌 테넌트 관리자

전역 관리자인 경우 **API 사용 권한** 페이지로 이동하여 **Enter_the_Tenant_Name_Here에 대한 관리자 동의 부여** 를 선택합니다.
> [!div id="apipermissionspage"]
> [API 사용 권한 페이지로 이동]()

##### <a name="standard-user"></a>표준 사용자

테넌트의 표준 사용자인 경우 전역 관리자에게 애플리케이션에 대한 관리자 동의를 부여하도록 요청합니다. 이렇게 하려면 관리자에게 다음 URL을 제공합니다.

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```


#### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

이 샘플의 종속성을 한 번 설치해야 합니다.

```console
pip install -r requirements.txt
```

그런 다음, 명령 프롬프트 또는 콘솔을 통해 애플리케이션을 실행합니다.

```console
python confidential_client_secret_sample.py parameters.json
```

콘솔에서 Azure AD 디렉터리의 사용자 목록을 나타내는 일부 Json 조각을 출력하는 것을 볼 수 있습니다.

> [!IMPORTANT]
> 이 빠른 시작 애플리케이션에서는 클라이언트 비밀을 사용하여 자체를 기밀 클라이언트로 식별합니다. 클라이언트 비밀은 보안상의 이유로 프로젝트 파일에 일반 텍스트로 추가되므로, 이 애플리케이션을 프로덕션 애플리케이션으로 사용하는 방안을 고려하기 전에 클라이언트 비밀 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하는 방법에 대한 자세한 내용은 이 샘플에 대한 동일한 GitHub 리포지토리의 두 번째 폴더 **2-Call-MsGraph-WithCertificate** 에 있는 [다음 지침](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md)을 참조하세요.

## <a name="more-information"></a>자세한 정보

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)은 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 라이브러리입니다. 설명한 것과 같이 이 빠른 시작은 위임된 권한 대신 애플리케이션 소유 ID를 사용하여 토큰을 요청합니다. 이 경우에 사용되는 인증 흐름을 *[클라이언트 자격 증명 oauth 흐름](v2-oauth2-client-creds-grant-flow.md)* 이라고 합니다. MSAL Python을 디먼 앱과 함께 사용하는 방법에 대한 자세한 내용은 [이 문서](scenario-daemon-overview.md)를 참조하세요.

 다음 pip 명령을 실행하여 MSAL Python을 설치할 수 있습니다.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```Python
import msal
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | 위치: |Description |
> |---------|---------|
> | `config["secret"]` | Azure Portal에서 애플리케이션에 대한 클라이언트 암호가 생성됩니다. |
> | `config["client_id"]` | Azure Portal에 등록된 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다. 이 값은 Azure Portal에서 앱의 **개요** 페이지에 있습니다. |
> | `config["authority"]`    | 사용자가 인증하는 STS 엔드포인트 일반적으로 퍼블릭 클라우드에 대한 `https://login.microsoftonline.com/{tenant}`입니다. 여기서 {tenant}는 테넌트의 이름 또는 테넌트 ID입니다.|

자세한 내용은 [`ConfidentialClientApplication`에 대한 참조 설명서](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)를 참조하세요.

### <a name="requesting-tokens"></a>토큰 요청

앱의 ID를 사용하여 토큰을 요청하려면 `AcquireTokenForClient` 메서드를 사용합니다.

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |위치:| Description |
> |---------|---------|
> | `config["scope"]` | 요청된 범위를 포함합니다. 기밀 클라이언트의 경우 요청되는 범위가 Azure Portal에서 설정된 앱 개체에서 정적으로 정의된 것임을 나타내기 위해 `{Application ID URI}/.default`와 유사한 양식을 사용해야 합니다(Microsoft Graph의 경우 `{Application ID URI}`는 `https://graph.microsoft.com`을 가리킴). 사용자 지정 웹 API의 경우 `{Application ID URI}`는 Azure Portal의 **앱 등록** 에서 **API 노출** 섹션 아래에 정의됩니다.|

자세한 내용은 [`AcquireTokenForClient`에 대한 참조 설명서](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)를 참조하세요.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

디먼 애플리케이션에 대해 자세히 알아보려면 시나리오 방문 페이지를 참조하세요.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)
