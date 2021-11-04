---
title: Azure Active Directory B2C를 사용하여 샘플 Python 웹 애플리케이션에서 인증 구성
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 Python 웹 애플리케이션에 로그인하고 사용자를 등록하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: 1466e62e50cac9d24616c9662f73ad23f3f6eea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007920"
---
# <a name="configure-authentication-in-a-sample-python-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 샘플 Python 웹앱에서 인증 구성

이 문서에서는 샘플 Python 웹 애플리케이션을 사용하여 웹 애플리케이션에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 OAuth 2.0을 기반으로 하는 인증 프로토콜입니다. OIDC를 사용하여 사용자가 애플리케이션에 안전하게 로그인할 수 있습니다. 웹앱 샘플은 [Python용 MSAL(Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-python)을 사용합니다. Python용 MSAL을 사용하면 Python 웹앱에 인증 및 권한 부여 지원을 간편하게 추가할 수 있습니다. 

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자는 웹앱으로 이동하여 **로그인** 을 선택합니다. 
1. 앱에서 인증 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다.
1. 사용자가 [등록 또는 로그인](add-sign-up-and-sign-in-policy.md)하거나, [암호를 초기화](add-password-reset-policy.md)하거나, [소셜 계정](add-identity-provider.md)으로 로그인합니다.
1. 사용자가 성공적으로 로그인하면 Azure AD B2C가 앱에 ID 토큰을 반환합니다.
1. 앱은 인증 코드를 ID 토큰과 교환하고, ID 토큰의 유효성을 검사하고, 클레임을 읽은 다음, 사용자에게 보안 페이지를 반환합니다.


### <a name="sign-out"></a>로그아웃

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>사전 요구 사항

다음 중 하나를 실행하는 컴퓨터: 

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Python](https://nodejs.org/en/download/) 2.7+ 또는 3+ 

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>2단계: 웹 애플리케이션 등록

애플리케이션에서 Azure AD B2C를 사용해 로그인할 수 있도록 하려면 Azure AD B2C 디렉터리에 앱을 등록합니다. 앱을 등록하면 앱과 Azure AD B2C 간에 트러스트 관계가 설정됩니다.  

앱을 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증하면 Azure AD B2C에 의해 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 앱이 등록되면 Azure AD B2C에서 애플리케이션 ID와 리디렉션 URI를 모두 사용하여 인증 요청을 만듭니다. 

### <a name="step-21-register-the-app"></a>2\.1단계: 앱 등록

웹앱 등록을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 아래에 애플리케이션 이름을 입력합니다(예: *webapp1*).
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음 URL 상자에 `http://localhost:5000/getAToken`를 입력합니다.
1. **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. **개요** 를 선택합니다.
1. 웹 애플리케이션을 구성할 때 나중에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.

    ![웹앱 ID를 기록하기 위한 웹앱 개요 페이지의 스크린샷](./media/configure-authentication-sample-python-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-create-a-web-app-client-secret"></a>2\.2단계: 웹앱 클라이언트 비밀 만들기

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]

## <a name="step-3-get-the-web-app-sample"></a>3단계: 웹 앱 샘플 가져오기

[zip 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)하거나, GitHub에서 샘플 웹 애플리케이션을 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

경로의 총 문자 길이가 260자 이하인 폴더에 샘플 파일을 추출합니다.

## <a name="step-4-configure-the-sample-web-app"></a>4단계: 샘플 웹앱 구성

프로젝트의 루트 디렉터리에서 다음을 수행합니다.

1. *app_config.py* 파일의 이름을 *app_config.py.OLD* 로 바꿉니다.
1. *app_config_b2c.py* 파일의 이름을 *app_config.py* 로 바꿉니다. 

*app_config.py* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 다음 앱 설정 속성을 업데이트합니다. 

|키  |값  |
|---------|---------|
|`b2c_tenant`| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다.(예를 들어 `contoso`)|
|`CLIENT_ID`| [2.1단계](#step-21-register-the-app)의 웹 API 애플리케이션 ID입니다.|
|`CLIENT_SECRET`| [2.2단계](#step-22-create-a-web-app-client-secret)에서 만든 클라이언트 암호입니다. 보안 강화를 위해 설명에 권장된 대로 환경 변수에 대신 저장하는 것이 좋습니다. |
|`*_user_flow`|사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다.|
| | |

최종 구성 파일은 다음 Python 코드와 같아야 합니다.

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.
```

> [!IMPORTANT]
> 코드 조각 설명에 나와 있는 것처럼 애플리케이션 코드에 ‘비밀을 일반 텍스트로 저장하지 않는 것’이 좋습니다. 하드 코딩된 변수는 ‘편의를 위해서만’ 코드 샘플에서 사용됩니다. 환경 변수 또는 비밀 저장소(예: Azure Key Vault)를 사용하는 것이 좋습니다.


## <a name="step-5-run-the-sample-web-app"></a>5단계: 샘플 웹앱 실행

1. 콘솔 또는 터미널에서 샘플이 포함된 디렉터리로 전환합니다. 예를 들면 다음과 같습니다.

    ```console
    cd ms-identity-python-webapp
    ```
1. 다음 명령을 실행하여 PyPi에서 필요한 패키지를 설치하고 로컬 머신에서 웹앱을 실행합니다.

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    콘솔 창에 로컬에서 실행 중인 애플리케이션의 포트 번호가 표시됩니다.

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on `http://localhost:5000/` (Press CTRL+C to quit)
    ```

 
1. 로컬 머신에서 실행되는 웹 애플리케이션을 보려면 `http://localhost:5000`으로 이동합니다. 

1. **로그인** 을 선택합니다.

    ![Azure AD B2C를 사용한 로그인을 보여 주는 스크린샷](./media/configure-authentication-sample-python-web-app/web-app-sign-in.png)


1. 가입 또는 로그인 프로세스를 완료합니다.

1. 성공적으로 인증되면 다음과 같이 표시 이름이 표시됩니다. 

    ![웹앱 토큰의 표시 이름 클레임을 보여 주는 스크린샷](./media/configure-authentication-sample-python-web-app/web-app-token-claims.png)


## <a name="step-6-call-to-a-web-api"></a>6단계: 웹 API 호출

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록해야 합니다.  

- [2단계](#step-2-register-a-web-application)에서 이미 만든 **웹 애플리케이션**(Python) 등록입니다. 이 앱 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다.  앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 예를 들어 **앱 ID: 1** 을 생성합니다.

- **웹 API** 등록을 사용하면 앱에서 보안 웹 API를 호출할 수 있습니다. 등록은 웹 API 권한(범위)을 노출합니다. 앱 등록 프로세스는 웹 API를 고유하게 식별하는 ‘애플리케이션 ID’를 생성합니다(예: ‘앱 ID: 2’).  웹 API 범위 (앱 ID: 2)에 앱 (앱 ID: 1) 사용 권한을 부여 합니다.  

앱 등록 및 애플리케이션 아키텍처는 다음 다이어그램에 설명되어 있습니다.

![웹 API, 등록, 토큰을 사용하는 웹앱을 설명하는 다이어그램](./media/configure-authentication-sample-python-web-app/web-app-with-api-architecture.png) 

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="step-61-register-the-web-api-app"></a>6\.1단계: 웹 API 앱 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-62-configure-scopes"></a>6\.2단계: 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="step-63-grant-the-web-app-permissions"></a>6\.3 단계: 웹앱 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

### <a name="step-64-configure-your-web-api"></a>6\.4단계: 웹 API 구성

이 샘플은 웹앱이 웹 API에 사용할 수 있는 관련 범위를 사용하여 액세스 토큰을 획득합니다. 코드에서 웹 API를 호출하려면 기존 웹 API를 사용하거나 새 웹 API를 만듭니다. 자세한 내용은 [Azure AD B2C를 사용하여 사용자 고유의 웹 API에서 인증 사용](enable-authentication-web-api.md)을 참조하세요.

### <a name="step-65-configure-the-sample-app-with-the-web-api"></a>6\.5단계: 웹 API를 통해 샘플 앱 구성

*app_config.py* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 앱 설정의 다음 속성을 업데이트합니다. 

|키  |값  |
|---------|---------|
|`ENDPOINT`| 웹 API의 URI(예: `https://localhost:44332/hello`)입니다.|
|`SCOPE`| 만든 웹 API [범위](#step-62-configure-scopes)입니다.|
| | |

최종 구성 파일은 다음 Python 코드와 같아야 합니다.

```python
import os

b2c_tenant = "contoso"
signupsignin_user_flow = "B2C_1_signupsignin"
editprofile_user_flow = "B2C_1_profileediting"
resetpassword_user_flow = "B2C_1_passwordreset"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "xxxxxxxxxxxxxxxxxxxxxxxx" # Placeholder - for use ONLY during testing.

### More code here

# This is the API resource endpoint
ENDPOINT = 'https://localhost:44332' 


SCOPE = ["https://contoso.onmicrosoft.com/api/demo.read", "https://contoso.onmicrosoft.com/api/demo.write"] 
```

### <a name="step-66-run-the-sample-app"></a>6\.6단계: 샘플 앱 실행

1. 콘솔 또는 터미널에서 샘플이 포함된 디렉터리로 전환합니다. 
1. 앱을 중지합니다. 그런 다음, 다시 실행합니다.
1. **Microsoft Graph API 호출** 을 선택합니다.

    ![웹 API 호출 방법을 보여 주는 스크린샷](./media/configure-authentication-sample-python-web-app/call-web-api.png)

## <a name="step-7-deploy-your-application"></a>7단계: 애플리케이션 배포 

프로덕션 애플리케이션에서 앱 등록 리디렉션 URI는 일반적으로 앱이 실행되는, 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://contoso.com/getAToken`). 

언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다. 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.
* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 

## <a name="next-steps"></a>다음 단계
* [Azure AD B2C를 사용하여 Python 웹앱에서 인증 옵션을 구성](enable-authentication-python-web-app-options.md)하는 방법을 알아봅니다.
