---
title: '빠른 시작: 사용자를 로그인하고 Microsoft Graph를 호출하는 ASP.NET Core 웹앱 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 앱이 Microsoft.Identity.Web을 활용하여 OpenID Connect를 통해 ASP.NET Core 웹앱에서 Microsoft 로그인을 구현하고 Microsoft Graph를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/17/2021
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 244736501dbd9a996ddb89159f0a7289244e51b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466108"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>빠른 시작: 사용자를 로그인하고 사용자를 대신하여 Microsoft Graph를 호출하는 ASP.NET Core 웹앱

이 빠른 시작에서는 ASP.NET Core 웹앱이 모든 Azure AD(Azure Active Directory) 조직에서 사용자를 로그인하고 Microsoft Graph를 호출하는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다.

자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>필수 구성 요소
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 또는 [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
>
> ## <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `AspNetCoreWebAppCallsGraph-Quickstart`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
> 1. `https://localhost:44321/signin-oidc`의 **리디렉션 URI** 를 입력합니다.
> 1. **등록** 을 선택합니다.
> 1. **관리** 에서 **인증** 을 선택합니다.
> 1. `https://localhost:44321/signout-oidc`의 **프런트 채널 로그 아웃 URL** 을 입력합니다.
> 1. **저장** 을 선택합니다.
> 1. **관리** 아래에서 **인증서 및 비밀** > **클라이언트 암호** > **새 클라이언트 암호** 를 선택합니다.
> 1. **설명** 을 입력합니다(예: `clientsecret1`).
> 1. 비밀 만료에 대해 **1년 후** 를 선택합니다.
> 1. **추가** 를 선택하고 이후 단계에서 사용할 비밀의 **값** 을 즉시 기록합니다. 비밀 값은 *다시 표시되지 않으며*, 어떤 방법으로도 검색할 수 없습니다. 암호와 마찬가지로 안전한 위치에 기록해 둡니다.

> [!div class="sxs-lookup" renderon="portal"]
> ## <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
>
> 이 빠른 시작의 코드 샘플이 작동하려면 앱 등록에 `https://localhost:44321/signin-oidc`의 **리디렉션 URI** 와 `https://localhost:44321/signout-oidc`의 **프런트 채널 로그아웃 URL** 을 추가합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

## <a name="step-2-download-the-aspnet-core-project"></a>2단계: ASP.NET Core 프로젝트 다운로드

> [!div renderon="docs"]
> [ASP.NET Core 솔루션 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 프로젝트를 실행합니다.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> ## <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
>
> 앱 속성 값을 사용하여 프로젝트를 구성했고 실행할 준비가 되었습니다.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> ## <a name="step-3-configure-your-aspnet-core-project"></a>3단계: ASP.NET Core 프로젝트 구성
> 1. 드라이브 루트 근처의 로컬 폴더로 .zip 보관 파일을 추출합니다. 예를 들어 *C:\Azure-Samples* 입니다.
> 1. Visual Studio 2019에서 솔루션을 엽니다.
> 1. *appsettings.json* 파일을 열고 다음을 수정합니다.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - `Enter_the_Application_Id_here`를 Azure Portal에 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 로 바꿉니다. 앱의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 찾을 수 있습니다.
>    - `common`을 다음 중 하나로 바꿉니다.
>       - 애플리케이션이 **이 조직 디렉터리의 계정만** 을 지원하는 경우 이 값을 **디렉터리(테넌트) ID**(GUID) 또는 **테넌트 이름**(예: `contoso.onmicrosoft.com`)으로 바꿉니다. 앱의 **개요** 페이지에서 **디렉터리(테넌트) ID** 를 찾을 수 있습니다.
>       - 애플리케이션이 **모든 조직 디렉터리의 계정** 을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
>       - 애플리케이션이 **모든 Microsoft 계정 사용자** 를 지원하는 경우 이 값을 `common`으로 둡니다.
>    - `Enter_the_Client_Secret_Here`를 이전 단계에서 만들고 기록한 **클라이언트 암호** 로 바꿉니다.
>
> 이 빠른 시작에서는 *appsettings.json* 파일에서 다른 값을 변경하지 마세요.
>
> ## <a name="step-4-build-and-run-the-application"></a>4단계: 애플리케이션 빌드 및 실행
>
> **디버그** 메뉴 > **디버깅 시작** 을 선택하거나 `F5` 키를 눌러 Visual Studio에서 앱을 빌드하고 실행합니다.
>
> 자격 증명을 입력하라는 메시지가 표시되고 앱에 필요한 권한에 동의하라는 메시지가 표시됩니다. 동의 프롬프트에서 **수락** 을 선택합니다.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="앱이 > 사용자로부터 요청하는 권한을 보여주는 동의 대화 상자":::
>
> 요청된 권한에 동의하면 앱에 Azure Active Directory 자격 증명을 사용하여 성공적으로 로그인했음을 표시하고 페이지의 "Api 결과" 섹션에 이메일 주소를 표시합니다. 이는 Microsoft Graph를 사용하여 추출되었습니다.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="실행 중인 웹앱과 로그인한 사용자를 표시하는 웹 브라우저":::

## <a name="about-the-code"></a>코드 정보

이 섹션에서는 사용자를 로그인하고 사용자를 대신하여 Microsoft Graph API를 호출하는 데 필요한 코드의 개요를 제공합니다. 이 개요는 코드가 작동하는 방식과 주요 인수를 이해하려는 경우뿐만 아니라 기존 ASP.NET Core 애플리케이션에 로그인을 추가하고 Microsoft Graph를 호출하려는 경우에도 유용할 수 있습니다. [MSAL.NET](msal-overview.md)의 래퍼인 [Microsoft.Identity.Web](microsoft-identity-web.md)을 사용합니다.

### <a name="how-the-sample-works"></a>샘플 작동 방법

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>시작 클래스

*Microsoft.AspNetCore.Authentication* 미들웨어는 호스팅 프로세스가 초기화될 때 실행되는 `Startup` 클래스를 사용합니다.

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()` 메서드는 브라우저 시나리오에 사용되는 쿠키 기반 인증을 추가하고 챌린지를 OpenID Connect로 설정하도록 서비스를 구성합니다.

`.AddMicrosoftIdentityWebApp`가 포함된 줄은 애플리케이션에 Microsoft ID 플랫폼 인증을 추가합니다. 이는 [Microsoft.Identity.Web](microsoft-identity-web.md)에서 제공합니다. 그런 다음, *appsettings.json* 구성 파일의 `AzureAD` 섹션에 있는 정보를 기반으로 Microsoft ID 플랫폼을 사용하여 로그인하도록 구성됩니다.

| *appsettings.json* 키 | 설명                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure Portal에 등록된 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.                                                                                       |
| `Instance`             | 사용자가 인증하는 STS(보안 토큰 서비스) 엔드포인트입니다. 이 값은 일반적으로 `https://login.microsoftonline.com/`이며, Azure 퍼블릭 클라우드를 나타냅니다. |
| `TenantId`             | 회사 또는 학교 계정이나 Microsoft 개인 계정을 사용하여 사용자를 로그인하는 테넌트의 이름, 해당 테넌트 ID(GUID) 또는 *공용* 입니다.                             |

`EnableTokenAcquisitionToCallDownstreamApi` 메서드를 사용하면 애플리케이션에서 보호된 웹 API를 호출하는 토큰을 가져올 수 있습니다. `AddMicrosoftGraph`를 사용하면 컨트롤러 또는 Razor 페이지에서 종속성 주입을 통해 `GraphServiceClient`를 직접 활용하고 `AddInMemoryTokenCaches` 메서드를 사용하면 앱에서 토큰 캐시의 이점을 누릴 수 있습니다.

`Configure()` 메서드에는 명명된 기능을 사용할 수 있도록 설정하는 두 가지 중요한 메서드인 `app.UseAuthentication()` 및 `app.UseAuthorization()`이 포함되어 있습니다. 또한 `Configure()` 메서드에서는 `endpoints.MapControllerRoute()`에 대한 호출 또는 `endpoints.MapControllers()`에 대한 호출을 하나 이상 사용하여 Microsoft Identity Web의 경로를 등록해야 합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>컨트롤러 또는 컨트롤러 메서드 보호

컨트롤러의 클래스 또는 하나 이상의 메서드에 `[Authorize]` 특성을 적용하여 컨트롤러 또는 해당 메서드를 보호할 수 있습니다. 이 `[Authorize]` 특성은 인증된 사용자만 허용하여 액세스를 제한합니다. 사용자가 아직 인증되지 않은 경우 컨트롤러에 액세스하기 위해 인증 챌린지를 시작할 수 있습니다. 이 빠른 시작에서는 구성 파일에서 범위를 읽습니다.

```csharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 참조하는 ASP.NET Core 코드 샘플이 포함된 GitHub 리포지토리에는 다음 방법을 보여주는 지침과 추가 코드 샘플이 포함되어 있습니다.

- 새 ASP.NET Core 웹 애플리케이션에 인증 추가
- Microsoft Graph, 기타 Microsoft API 또는 사용자 고유의 웹 API 호출
- 권한 부여 추가
- 국가별 클라우드 또는 소셜 ID로 사용자 로그인

> [!div class="nextstepaction"]
> [GitHub의 ASP.NET Core 웹앱 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
