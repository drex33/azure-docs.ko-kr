---
title: Azure Active Directory B2C를 사용하여 웹 API를 호출하는 샘플 웹 애플리케이션에서 인증 구성
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 웹 API를 호출하는 ASP.NET 웹 애플리케이션에 사용자를 로그인하고 가입하는 방법을 소개합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: f2f0f00bfc3b5f551d60817e3ea52924c78d98d8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007812"
---
# <a name="configure-authentication-in-a-sample-web-app-that-calls-a-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 웹 API를 호출하는 샘플 웹앱에서 인증 구성

이 문서에서는 웹 API를 호출하는 샘플 ASP.NET 웹 애플리케이션을 사용하여 웹 애플리케이션에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 문서에서 참조하는 샘플 ASP.NET 웹앱은 전달자 토큰을 사용하여 웹 API를 호출하는 데 사용됩니다. 웹 API를 호출하지 않는 웹앱의 경우 [Azure AD B2C를 사용하여 샘플 웹 애플리케이션에서 인증 구성](configure-authentication-sample-web-app.md)을 참조하세요.  

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 OAuth 2.0을 기반으로 하는 인증 프로토콜입니다. OIDC를 사용하여 사용자가 애플리케이션에 안전하게 로그인할 수 있습니다. 이 웹앱 샘플에서는 [Microsoft ID 웹](https://www.nuget.org/packages/Microsoft.Identity.Web)을 사용합니다. Microsoft ID 웹은 보안 웹 API를 호출할 수 있는 웹앱에 대한 인증 및 권한 부여 지원 추가 작업을 간소화하는 ASP.NET Core 라이브러리 세트입니다. 

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자는 웹앱으로 이동하여 **로그인** 을 선택합니다.
1. 앱에서 인증 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다.
1. 사용자가 [가입 또는 로그인](add-sign-up-and-sign-in-policy.md)하고 [암호를 초기화](add-password-reset-policy.md)합니다. 또는 [소셜 계정](add-identity-provider.md)으로 로그인할 수 있습니다.
1. 사용자가 로그인하면 Azure AD B2C는 앱에 인증 코드를 반환합니다.
1. 그러면 앱에서 다음을 수행합니다.
 
   a. 인증 코드를 ID 토큰, 액세스 토큰 및 새로 고침 토큰으로 교환합니다.  
   b. ID 토큰 클레임을 읽고 애플리케이션 권한 부여 쿠키를 유지합니다.  
   다. 나중에 사용할 수 있도록 새로 고침 토큰을 메모리 내 캐시에 저장합니다.

### <a name="app-registration-overview"></a>앱 등록 개요

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록합니다.  

- *웹 애플리케이션* 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다. 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증을 완료한 후 Azure AD B2C에 의해 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 또한 앱에서 토큰을 안전하게 획득하는 데 사용하는 *클라이언트 암호* 를 만듭니다.

- *웹 API* 등록을 사용하면 앱에서 보안 웹 API를 호출할 수 있습니다. 등록에는 웹 API *범위* 가 포함됩니다. 범위는 웹 API와 같은 보호된 리소스에 대한 사용 권한을 관리하는 방법을 제공합니다. 웹 API 범위에 웹 애플리케이션 권한을 부여합니다. 액세스 토큰을 요청할 때 앱에서 요청의 범위 매개 변수에서 원하는 권한을 지정합니다.  

앱 아키텍처 및 등록은 다음 다이어그램에 설명되어 있습니다.

![웹 API 호출 등록 및 토큰이 있는 웹앱 다이어그램](./media/configure-authentication-sample-web-app-with-api/web-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>웹 API 호출

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>로그아웃

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>사전 요구 사항

다음 중 하나를 실행하는 컴퓨터: 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.8 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 **ASP.NET 및 웹 개발** 워크로드
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code용 C#(최신 버전)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-web-applications"></a>2단계: 웹 애플리케이션 등록

이 단계에서는 웹앱 및 웹 API 애플리케이션 등록을 만들고 웹 API의 범위를 지정합니다.

### <a name="step-21-register-the-web-api-app"></a>2\.1단계: 웹 API 앱 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="step-22-configure-web-api-app-scopes"></a>2\.2단계: 웹 API 앱 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="step-23-register-the-web-app"></a>2\.3단계: 웹앱 등록

웹앱 등록을 만들려면 다음을 수행합니다.

1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 아래에 애플리케이션 이름을 입력합니다(예: *webapp1*).
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음 URL 상자에 `https://localhost:5000/signin-oidc`를 입력합니다.
1. **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. 앱 등록이 완료되면 **개요** 를 선택합니다.
1. 웹 애플리케이션을 구성할 때 나중에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.

    ![웹 애플리케이션 ID를 기록하기 위한 웹앱 개요 페이지의 스크린샷](./media/configure-authentication-sample-web-app-with-api/get-azure-ad-b2c-app-id.png)  

### <a name="step-24-create-a-web-app-client-secret"></a>2\.4단계: 웹앱 클라이언트 암호 만들기

[!INCLUDE [active-directory-b2c-app-integration-client-secret](../../includes/active-directory-b2c-app-integration-client-secret.md)]


### <a name="step-25-grant-the-web-app-permissions-for-the-web-api"></a>2\.5단계: 웹 API에 대한 웹앱 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-web-app-sample"></a>3단계: 웹앱 샘플 가져오기

[zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)하거나 다음 Bash 명령을 실행하여 GitHub에서 샘플 웹 애플리케이션을 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

경로의 총 문자 길이가 260자 이하인 폴더에 샘플 파일을 추출합니다.

## <a name="step-4-configure-the-sample-web-api"></a>4단계: 샘플 웹 API 구성

샘플 폴더의 *4-WebApp-your-API/4-2-B2C/TodoListService* 폴더에서 Visual Studio 또는 Visual Studio Code로 **TodoListService.csproj** 프로젝트를 엽니다. 

프로젝트 루트 폴더에서 *appsettings.json* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 웹 API 앱은 이 정보를 사용하여 웹앱이 전달자 토큰으로 전달하는 액세스 토큰의 유효성을 검사합니다. 앱 설정의 다음 속성을 업데이트합니다.

| 섹션 | Key | 값 |
| --- | --- | --- |
|AzureAdB2C|인스턴스| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다. 예들 들어 `https://contoso.b2clogin.com`입니다.|
|AzureAdB2C|도메인| Azure AD B2C 테넌트 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. 예들 들어 `contoso.onmicrosoft.com`입니다.|
|AzureAdB2C|clientid| [2.1단계](#step-21-register-the-web-api-app)의 웹 API 애플리케이션 ID입니다.|
|AzureAdB2C|SignUpSignInPolicyId|사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다.|
| | | |

최종 구성 파일은 다음 JSON 파일과 같아야 합니다.

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

### <a name="step-41-set-the-permission-policy"></a>4\.1단계: 권한 정책 설정

웹 API는 사용자가 전달자 토큰을 사용하여 인증했고, 전달자 토큰에 구성된 허용 범위가 있음을 확인합니다. 전달자 토큰에 이러한 허용된 범위가 없는 경우 웹 API는 HTTP 상태 코드 403(사용할 수 없음)을 반환하고 토큰에 예상되는 범위를 알려주는 메시지를 응답 본문에 씁니다. 

허용되는 범위를 구성하려면 `Controller/TodoListController.cs` 클래스를 열고 전체 URI 없이 범위 이름을 설정합니다.

```csharp
[RequiredScope("tasks.read")]
```

### <a name="step-42-run-the-sample-web-api-app"></a>4\.2단계: 샘플 웹 API 앱 실행

웹앱이 웹 API 샘플을 호출하도록 허용하려면 다음을 수행하여 웹 API를 실행합니다.

1. 종속성을 복원하도록 요청되면 복원합니다.
1. 프로젝트를 빌드하고 실행합니다.
1. 프로젝트를 빌드한 후 Visual Studio 또는 Visual Studio Code는 브라우저에서 다음 주소 https://localhost:44332 로 웹 API를 시작합니다.

## <a name="step-5-configure-the-sample-web-app"></a>5단계: 샘플 웹앱 구성

샘플 폴더의 `4-WebApp-your-API/4-2-B2C/Client` 폴더에서 Visual Studio 또는 Visual Studio Code로 **TodoListClient.csproj** 프로젝트를 엽니다. 

프로젝트 루트 폴더에서 `appsettings.json` 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 웹앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃하고, 토큰을 획득하고, 유효성을 검사합니다. 앱 설정의 다음 속성을 업데이트합니다.

| 섹션 | Key | 값 |
| --- | --- | --- |
| AzureAdB2C | 인스턴스 | Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다.(예를 들어 `https://contoso.b2clogin.com`)|
|AzureAdB2C|도메인| Azure AD B2C 테넌트 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. (예를 들어 `contoso.onmicrosoft.com`)|
|AzureAdB2C|clientid| [2.3단계](#step-23-register-the-web-app)의 웹 애플리케이션 ID입니다.|
|AzureAdB2C | ClientSecret | [2.4단계](#step-24-create-a-web-app-client-secret)의 웹 애플리케이션 비밀입니다. | 
|AzureAdB2C|SignUpSignInPolicyId|사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다.|
| TodoList | TodoListScope | [2.5단계](#step-25-grant-the-web-app-permissions-for-the-web-api)에서 만든 웹 API 범위입니다.|
| TodoList | TodoListBaseAddress | 웹 API의 기본 URI(예: `https://localhost:44332`)입니다. |
| | | |

최종 구성 파일은 다음 JSON과 같아야 합니다.

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-app-application-id>",
    "ClientSecret": "<web-app-application-secret>",  
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  "TodoList": {
    "TodoListScope": "https://contoso.onmicrosoft.com/api/demo.read",
    "TodoListBaseAddress": "https://localhost:44332"
  }
}
```


## <a name="step-6-run-the-sample-web-app"></a>6단계: 샘플 웹앱 실행

1. 프로젝트를 빌드하고 실행합니다.
1. [https://www.microsoft.com]\(`https://localhost:5000`) 로 이동합니다. 
1. 가입 또는 로그인 프로세스를 완료합니다.

성공적으로 인증되면 탐색 모음에 표시 이름이 표시됩니다. Azure AD B2C 토큰이 앱에 반환하는 클레임을 보려면 **TodoList** 를 선택합니다.

![웹앱 토큰 클레임의 스크린샷](./media/configure-authentication-sample-web-app-with-api/web-api-to-do-list.png)


## <a name="deploy-your-application"></a>애플리케이션 배포

프로덕션 애플리케이션에서 앱 등록 리디렉션 URI는 일반적으로 앱이 실행되는, 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://contoso.com/signin-oidc`). 

언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다. 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.
* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 

### <a name="token-cache-for-a-web-app"></a>웹앱에 대한 토큰 캐시

웹앱 샘플은 메모리 토큰 캐시 직렬화에 사용합니다. 이 구현은 샘플에서 매우 유용합니다. 웹앱이 다시 시작될 때 토큰 캐시가 손실되어도 상관하지 않는 경우 프로덕션 애플리케이션에서도 유용합니다. 

프로덕션 환경의 경우 분산 메모리 캐시를 사용하는 것이 좋습니다. 예를 들어 Redis cache, NCache 또는 SQL Server 캐시입니다. 분산 메모리 캐시 구현에 대한 자세한 내용은 [토큰 캐시 직렬화](../active-directory/develop/msal-net-token-cache-serialization.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

* [코드 샘플에 대해](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code) 자세히 알아보기
* [Azure AD B2C를 사용하여 사용자 고유의 웹 애플리케이션에서 인증을 사용하도록 설정](enable-authentication-web-application.md)하는 방법 알아보기
* [고유 웹 API에서 인증을 사용하도록 설정](enable-authentication-web-api.md)하는 방법 알아보기
