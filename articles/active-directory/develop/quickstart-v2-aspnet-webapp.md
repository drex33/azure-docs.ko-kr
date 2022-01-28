---
title: '빠른 시작: 사용자를 로그인하는 ASP.NET 웹앱'
titleSuffix: Microsoft identity platform
description: ASP.NET 웹앱에서 Azure AD 사용자가 로그인하는 방법을 보여 주는 코드 샘플을 다운로드하고 실행합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", contperf-fy21q1, mode-other
ms.openlocfilehash: b784950dcd1a5b8f1a55d45719a79a64f1fbdfba
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137805055"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>빠른 시작: Azure AD 사용자가 로그인하는 ASP.NET 웹앱

이 빠른 시작에서는 Azure AD(Azure Active Directory) 계정이 있는 사용자가 로그인할 수 있는 ASP.NET 웹 애플리케이션을 보여 주는 코드 샘플을 다운로드하고 실행합니다.

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
이 빠른 시작의 코드 샘플이 작동하려면 **리디렉션 URI** 에 대해 **https://localhost:44368/** 을 추가합니다.

> [!div class="nextstepaction"]
> [자동 변경]()

> [!div class="alert alert-info"]
> ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

Visual Studio 2019를 사용하여 프로젝트를 실행합니다.
> [!div class="sxs-lookup nextstepaction"]
> [코드 샘플 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
앱의 속성 값을 사용하여 프로젝트를 구성했습니다.

1. .zip 보관 파일의 압축을 루트 폴더에 가까운 로컬 폴더에 풉니다. 예를 들어 파일의 압축을 *C:\Azure-Samples* 에 풉니다.
   
   Windows의 경로 길이 제한으로 인해 발생하는 오류를 방지하려면 보관 파일의 압축을 드라이브 루트에 가까운 디렉터리에 푸는 것이 좋습니다.
2. Visual Studio에서 솔루션(*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*)을 엽니다.
3. Visual Studio 버전에 따라 마우스 오른쪽 단추로 **AppModelv2-WebApp-OpenIDConnect-DotNet** 프로젝트를 클릭한 다음, **NuGet 패키지 복원** 을 선택해야 할 수도 있습니다.
4. **보기** > **다른 창** > **패키지 관리자 콘솔** 을 차례로 선택하여 패키지 관리자 콘솔을 엽니다. 그런 다음, `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`을 실행합니다.

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>자세한 정보

이 섹션에서는 로그인 사용자에 필요한 코드에 대한 개요를 제공합니다. 이 개요는 코드의 작동 방식, 기본 인수의 개념 및 로그인을 기존 ASP.NET 애플리케이션에 추가하는 방법을 이해하는 데 유용할 수 있습니다.


### <a name="how-the-sample-works"></a>샘플 작동 방법

![샘플 앱에서 웹 브라우저, 웹앱 및 Microsoft ID 플랫폼 간의 상호 작용에 대한 다이어그램](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN 미들웨어 NuGet 패키지

OWIN 미들웨어 패키지가 있는 ASP.NET에서 OpenID Connect를 사용하여 쿠키 기반 인증이 포함된 인증 파이프라인을 설정할 수 있습니다. 이러한 패키지는 Visual Studio의 패키지 관리자 콘솔에서 다음 명령을 실행하여 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN 시작 클래스

OWIN 미들웨어는 호스팅 프로세스가 시작될 때 실행되는 *시작 클래스* 를 사용합니다. 이 빠른 시작에서 *startup.cs* 파일은 루트 폴더에 있습니다. 다음 코드는 이 빠른 시작에서 사용하는 매개 변수를 보여 줍니다.

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | Description |
> |---------|---------|
> | `ClientId`     | Azure Portal에 등록된 애플리케이션의 애플리케이션 ID입니다. |
> | `Authority`    | 사용자가 인증하는 STS(보안 토큰 서비스) 엔드포인트입니다. 퍼블릭 클라우드의 경우 일반적으로 `https://login.microsoftonline.com/{tenant}/v2.0`입니다. 이 URL에서 *{tenant}* 는 테넌트 이름, 테넌트 ID 또는 공통 엔드포인트에 대한 참조의 `common`입니다. (공통 엔드포인트는 다중 테넌트 애플리케이션에 사용됩니다.) |
> | `RedirectUri`  | Microsoft ID 플랫폼에 대해 인증한 후 사용자가 전송되는 URL입니다. |
> | `PostLogoutRedirectUri`     | 로그오프한 후 사용자가 전송되는 URL입니다. |
> | `Scope`     | 공백으로 구분된 요청되는 범위 목록입니다. |
> | `ResponseType`     | 인증의 응답에 권한 부여 코드 및 ID 토큰이 포함되는 요청입니다. |
> | `TokenValidationParameters`     | 토큰 유효성 검사에 대한 매개 변수 목록입니다. 이 경우 개인, 회사 또는 학교 계정 유형의 로그인을 수락할 수 있음을 나타내기 위해 `ValidateIssuer`가 `false`로 설정됩니다. |
> | `Notifications`     | `OpenIdConnect` 메시지에서 실행할 수 있는 대리자의 목록입니다. |


> [!NOTE]
> 이 빠른 시작을 간단하게 수행할 수 있도록 `ValidateIssuer = false`로 설정했습니다. 실제 애플리케이션에서 발급자의 유효성을 검사합니다. 자세한 방법은 샘플을 참조하세요.

### <a name="authentication-challenge"></a>인증 질문

컨트롤러에서 다른 사용자에게 인증 질문을 요청하여 로그인하게 할 수 있습니다.

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> 이 메서드를 사용하여 인증 질문을 요청하는 것은 선택 사항입니다. 인증된 사용자와 인증되지 않은 사용자 모두에서 보기에 액세스할 수 있도록 하려면 일반적으로 이 옵션을 사용합니다. 또는 다음 섹션에 설명된 메서드를 사용하여 컨트롤러를 보호할 수 있습니다.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>컨트롤러 또는 컨트롤러 작업을 보호하기 위한 특성

컨트롤러 또는 컨트롤러 작업은 `[Authorize]` 특성을 사용하여 보호할 수 있습니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러의 작업에 액세스할 수 있도록 허용하여 컨트롤러 또는 작업에 대한 액세스를 제한합니다. 그러면 인증되지 않은 사용자가 `[Authorize]` 특성으로 데코레이팅된 작업 또는 컨트롤러 중 하나에 액세스하려고 할 때 인증 질문이 자동으로 수행됩니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작의 전체 설명을 포함하여 애플리케이션 및 새로운 기능 빌드에 대한 전체 단계별 가이드는 ASP.NET 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [ASP.NET 웹앱에 로그인 추가](tutorial-v2-asp-webapp.md)
