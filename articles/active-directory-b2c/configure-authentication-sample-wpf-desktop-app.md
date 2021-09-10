---
title: Azure Active Directory B2C를 사용하여 샘플 WPF 데스크톱 애플리케이션에서 인증 구성
description: Azure Active Directory B2C를 사용하여 WPF 데스크톱 애플리케이션에 로그인하고 사용자를 등록합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fb9f90e93ed13faf64703cb511cd540f9af685ba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566557"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 샘플 WPF 데스크톱 애플리케이션에서 인증 구성

이 문서에서는 샘플 [WPF 데스크톱](/visualstudio/designers/getting-started-with-wpf.md) 애플리케이션을 사용하여 데스크톱 앱에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 애플리케이션에 사용자가 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0 기반의 인증 프로토콜입니다. 이 데스크톱 앱 샘플은 OpenId Connect 인증 코드 PKCE 흐름에서 [MSAL](../active-directory/develop/msal-overview.md) 라이브러리를 사용합니다. MSAL 라이브러리는 데스크톱 앱에 인증 및 권한 부여 지원을 간편하게 추가할 수 있도록 하는 Microsoft에서 제공하는 라이브러리입니다. 

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자가 앱을 열고 **로그인** 을 선택합니다.
1. 앱에 데스크톱 디바이스의 시스템 브라우저가 열리고 Azure AD B2C에 대한 인증 요청을 시작합니다.
1. 사용자가 [등록 또는 로그인](add-sign-up-and-sign-in-policy.md)하거나, [암호를 다시 설정](add-password-reset-policy.md)하거나, [소셜 계정](add-identity-provider.md)으로 로그인합니다.
1. 로그인에 성공하면 Azure AD B2C는 앱에 인증 코드를 반환합니다.
1. 앱은 다음 작업을 수행합니다.
    1. 인증 코드를 ID 토큰, 액세스 토큰, 새로 고침 토큰으로 교환합니다.
    1. ID 토큰 클레임을 읽습니다.
    1. 나중에 사용할 수 있도록 토큰을 메모리 내 캐시에 저장합니다.

### <a name="app-registration-overview"></a>앱 등록 개요

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록합니다.  

- **데스크톱 애플리케이션** 등록을 사용하면 앱에서 Azure AD B2C에 로그인할 수 있습니다. 앱을 등록하는 동안 ‘리디렉션 URI’를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증하면 Azure AD B2C에 의해 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 데스크톱 앱을 고유하게 식별하는 ‘애플리케이션 ID’(‘클라이언트 ID’라고도 함)를 생성합니다.  예를 들어 **앱 ID: 1** 을 생성합니다.

- **웹 API** 등록을 사용하면 앱에서 보호된 웹 API를 호출할 수 있습니다. 등록하면 웹 API 권한(범위)이 노출됩니다. 앱 등록 프로세스는 웹 API를 고유하게 식별하는 ‘애플리케이션 ID’를 생성합니다.  예를 들어 **앱 ID: 2** 를 생성합니다. 웹 API 범위(앱 ID: 2)에 데스크톱 앱(앱 ID: 1) 권한을 부여합니다. 

다음 다이어그램에서는 앱 등록 및 애플리케이션 아키텍처를 설명합니다.

![웹 API, 등록, 토큰을 사용하는 데스크톱 앱을 설명하는 다이어그램](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>웹 API 호출

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>로그아웃

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>사전 요구 사항

[Visual Studio 2019](https://www.visualstudio.com/downloads/)( **.NET 데스크톱 개발** 포함)가 실행되는 컴퓨터

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>2단계: 애플리케이션 등록

이 단계에서는 데스크톱 앱 및 웹 API 애플리케이션 등록을 만들고 웹 API의 범위를 지정합니다.

### <a name="21-register-the-web-api-app"></a>2.1 웹 API 앱 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 웹 API 앱 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-desktop-app"></a>2.3 데스크톱 앱 등록

다음 단계에 따라 데스크톱 앱 등록을 만듭니다.

1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *desktop-app1* 을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택하고 `https://your-tenant-name.b2clogin.com/oauth2/nativeclient`를 입력합니다. `your-tenant-name`을 [테넌트 이름](tenant-management.md#get-your-tenant-name)을 바꿉니다. 추가 옵션은 [리디렉션 URI 구성](enable-authentication-wpf-desktop-app-options.md#configure-redirect-uri)을 참조하세요.
1. **등록** 을 선택합니다.
1. 앱 등록이 완료되면 **개요** 를 선택합니다.
1. 데스크톱 애플리케이션을 구성할 때 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.
    ![데스크톱 애플리케이션 ID를 얻는 방법을 보여 주는 스크린샷](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="24-grant-the-desktop-app-permissions-for-the-web-api"></a>2.4 웹 API에 대한 데스크톱 앱 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>3단계: 샘플 웹 API 구성

이 샘플은 데스크톱 앱이 웹 API에 사용할 수 있는 관련 범위를 사용하여 액세스 토큰을 획득합니다.  코드에서 웹 API를 호출하려면 다음 단계를 수행하세요.

1. 기존 웹 API를 사용하거나 새로 만듭니다. 자세한 내용은 [Azure AD B2C를 사용하여 사용자 고유의 웹 API에서 인증 사용](enable-authentication-web-api.md)을 참조하세요.
1. 웹 API를 구성한 후 웹 API 엔드포인트의 URI를 복사합니다. 다음 단계에서 웹 API 엔드포인트를 사용합니다.

> [!TIP]
> 웹 API가 없는 경우에도 이 샘플을 실행할 수 있습니다. 이 경우 앱은 액세스 토큰을 반환하지만 웹 API를 호출할 수 없습니다. 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>4단계: WPF 데스크톱 앱 샘플 다운로드

1. [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)하거나 [GitHub 리포지토리](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)에서 샘플 웹 애플리케이션을 복제합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. Visual Studio에서 **active-directory-b2c-wpf** 솔루션(`active-directory-b2c-wpf.sln`)을 엽니다.



## <a name="step-5-configure-the-sample-desktop-app"></a>5단계: 샘플 데스크톱 앱 구성

**active-directory-b2c-wpf** 프로젝트에서 *App.xaml.cs* 파일을 엽니다. `App.xaml.cs` 클래스 멤버에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 데스크톱 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃하고, 토큰을 획득하고, 유효성을 검사합니다. 

다음 멤버가 업데이트됩니다.

|키  |값  |
|---------|---------|
|`TenantName`|Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다. 예들 들어 `contoso.b2clogin.com`입니다.|
|`ClientId`|[2.3단계](#23-register-the-desktop-app)의 데스크톱 애플리케이션 ID입니다.|
|`PolicySignUpSignIn`| [1단계](#step-1-configure-your-user-flow)에서 만든 등록 또는 로그인 사용자 흐름이나 사용자 지정 정책입니다.|
|`PolicyEditProfile`|[1단계](#step-1-configure-your-user-flow)에서 만든 프로필 편집 사용자 흐름이나 사용자 지정 정책입니다.|
|`ApiEndpoint`| (선택 사항) [3단계](#step-3-configure-the-sample-web-api)에서 만든 웹 API 엔드포인트입니다. `https://contoso.azurewebsites.net/hello`)을 입력합니다.|
| `ApiScopes` | [2.4단계](#24-grant-the-desktop-app-permissions-for-the-web-api)에서 만든 웹 API 범위입니다.| 

최종 *App.xaml.cs* 파일은 다음 C# 코드와 같이 표시됩니다.

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>6단계: 데스크톱 앱 실행 및 테스트

1. [NuGet 패키지를 복원](/nuget/consume-packages/package-restore.md)합니다.
1. **F5** 키를 눌러 샘플을 빌드 및 실행합니다.
1. **로그인** 을 선택합니다. 그런 다음, Azure AD B2C 로컬 또는 소셜 계정으로 등록하거나 로그인합니다.

    ![로그인 흐름을 시작하는 방법을 보여 주는 스크린샷](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. 성공적으로 가입 또는 로그인한 후에는 토큰 정보가 WPF 앱의 아래쪽 창에 표시됩니다.

    ![Azure AD B2C 액세스 토큰 및 사용자 ID를 보여 주는 스크린샷](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. **API 호출** 을 선택하여 웹 API를 호출합니다.


## <a name="next-steps"></a>다음 단계

* [Azure Active Directory B2C를 사용하여 WPF 데스크톱 애플리케이션에서 인증 옵션 구성](enable-authentication-wpf-desktop-app-options.md)
