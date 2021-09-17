---
title: Azure Active Directory B2C를 사용하여 샘플 웹 애플리케이션에서 인증 구성
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 ASP.NET 웹 애플리케이션에 로그인하고 사용자를 등록하는 방법에 대해 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/23/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b05aa59f465eafd65fa111fff811d2f0909f601d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471861"
---
# <a name="configure-authentication-in-a-sample-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 샘플 웹앱에서 인증 구성

이 문서에서는 샘플 ASP.NET 웹 애플리케이션을 사용하여 웹 애플리케이션에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 문서에서 참조하는 샘플 ASP.NET 웹앱은 ID 토큰을 반환하지만 액세스 토큰을 반환하지는 않으므로 REST API 호출하는 데 사용할 수 없습니다. REST API를 호출할 수 있는 웹앱의 경우 [Azure AD B2C를 사용하여 ASP.NET Core로 빌드된 Web API 보호](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)를 참조하세요.  

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 OAuth 2.0을 기반으로 하는 인증 프로토콜입니다. OIDC를 사용하여 사용자가 애플리케이션에 안전하게 로그인할 수 있습니다. 이 웹앱 샘플에서는 [Microsoft ID 웹](https://www.nuget.org/packages/Microsoft.Identity.Web)을 사용합니다. Microsoft ID Web은 웹앱에 대한 인증 및 권한 부여 지원 추가 작업을 간소화하는 ASP.NET Core 라이브러리 세트입니다. 

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자는 웹앱으로 이동하여 **로그인** 을 선택합니다. 
1. 앱에서 인증 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다.
1. 사용자가 [가입 또는 로그인](add-sign-up-and-sign-in-policy.md)하고 [암호를 초기화](add-password-reset-policy.md)합니다. 또는 [소셜 계정](add-identity-provider.md)으로 로그인할 수 있습니다.
1. 사용자가 성공적으로 로그인하면 Azure AD B2C가 앱에 ID 토큰을 반환합니다.
1. 앱은 ID 토큰의 유효성을 검사하며 클레임을 읽고 사용자에게 보안 페이지를 반환합니다.

ID 토큰이 만료되거나 앱 세션이 무효화되면, 앱에서 새 인증 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다. Azure AD B2C [SSO 세션](session-behavior.md)이 활성화된 경우 Azure AD B2C에서 사용자에게 다시 로그인하라는 메시지를 표시하지 않고 액세스 토큰을 발급합니다. Azure AD B2C 세션이 만료되거나 무효화되면 사용자에게 다시 로그인하라는 메시지가 표시됩니다.

### <a name="sign-out"></a>로그아웃

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>사전 요구 사항

다음 중 하나를 실행하는 컴퓨터가 있어야 합니다. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16.8 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)과 ASP.NET 및 웹 개발 워크로드
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code용 C#(최신 버전)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>2단계: 웹 애플리케이션 등록

애플리케이션에서 Azure AD B2C를 사용해 로그인할 수 있도록 하려면 Azure AD B2C 디렉터리에 앱을 등록합니다. 앱을 등록하면 앱과 Azure AD B2C 간에 트러스트 관계가 설정됩니다.  

앱을 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증하면 Azure AD B2C에 의해 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다. 앱이 등록되면 Azure AD B2C에서 애플리케이션 ID와 리디렉션 URI를 모두 사용하여 인증 요청을 만듭니다. 

### <a name="step-21-register-the-app"></a>2\.1단계: 앱 등록

웹앱 등록을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 아래에 애플리케이션 이름을 입력합니다(예: *webapp1*).
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음 URL 상자에 `https://localhost:5001/signin-oidc`를 입력합니다.
1. **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. **개요** 를 선택합니다.
1. 웹 애플리케이션을 구성할 때 나중에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.

    ![웹 애플리케이션 ID를 기록하기 위한 웹앱 개요 페이지의 스크린샷.](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-enable-id-tokens"></a>2\.2단계: ID 토큰 사용

Azure AD B2C에서 직접 ID 토큰을 요청하는 웹 앱의 경우 앱 등록에서 암시적 허용 흐름을 사용합니다.

1. 왼쪽 창의 **관리** 아래에서 **인증** 을 선택합니다.
1. **암시적 허용** 에서 **ID 토큰(암시적 및 하이브리드 흐름에 사용)** 및 **액세스 토큰(암시적 흐름에 사용)** 확인란을 선택합니다.
1. **저장** 을 선택합니다.

## <a name="step-3-get-the-web-app-sample"></a>3단계: 웹 앱 샘플 가져오기

[zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)하거나, GitHub에서 샘플 웹 애플리케이션을 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

경로의 총 문자 길이가 260자 이하인 폴더에 샘플 파일을 추출합니다.

## <a name="step-4-configure-the-sample-web-app"></a>4단계: 샘플 웹앱 구성

샘플 폴더의 *1-WebApp-OIDC/1-5-B2C/* 폴더에서 Visual Studio 또는 Visual Studio Code로 **WebApp-OpenIDConnect-DotNet.csproj** 프로젝트를 엽니다. 

프로젝트 루트 폴더에서 *appsettings.json* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 다음 앱 설정 속성을 업데이트합니다. 

|섹션  |Key  |값  |
|---------|---------|---------|
|AzureAdB2C|인스턴스| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다.(예를 들어 `https://contoso.b2clogin.com`)|
|AzureAdB2C|도메인| Azure AD B2C 테넌트 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. (예를 들어 `contoso.onmicrosoft.com`)|
|AzureAdB2C|clientid| [2단계](#step-2-register-a-web-application)의 웹 API 애플리케이션 ID입니다.|
|AzureAdB2C|SignUpSignInPolicyId|사용자 흐름 또는 [1단계](#step-1-configure-your-user-flow)에서 만든 사용자 지정 정책입니다.|

최종 구성 파일은 다음 JSON과 같아야 합니다.

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-web-app"></a>5단계: 샘플 웹앱 실행

1. 프로젝트를 빌드하고 실행합니다.
1. `https://localhost:5001`로 이동합니다. 
1. **등록/로그인** 을 선택합니다.

    ![프로젝트 시작 페이지의 "등록/로그인" 단추 스크린샷.](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. 가입 또는 로그인 프로세스를 완료합니다.

성공적으로 인증되면 탐색 모음에 표시 이름이 표시됩니다. Azure AD B2C 토큰이 앱에 반환하는 클레임을 보려면 **클레임** 을 선택합니다.

![웹앱 토큰 클레임의 스크린샷.](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>애플리케이션 배포 

프로덕션 애플리케이션에서 앱 등록 리디렉션 URI는 일반적으로 앱이 실행되는, 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://contoso.com/signin-oidc`). 

언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다. 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.
* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [코드 샘플](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)에 대해 자세히 알아보기
* [Azure AD B2C를 사용하여 사용자 고유의 웹앱에서 인증을 사용하도록 설정](enable-authentication-web-application.md)하는 방법 알아보기
