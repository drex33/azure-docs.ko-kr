---
title: '빠른 시작: ASP.NET Core 웹앱에 Microsoft ID로 로그인 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 앱에서 OpenID Connect를 사용하여 ASP.NET Core 웹앱에서 Microsoft 로그인을 구현하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 534e922436ef279c849ccd80eb32387222c9be15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588412"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가

이 빠른 시작에서는 ASP.NET Core 웹앱이 모든 Azure AD(Azure Active Directory) 조직에서 사용자를 로그인하는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다.  

> [!div renderon="docs"]
> 다음 다이어그램에서는 샘플 앱의 작동 방식을 보여 줍니다.
>
> ![샘플 앱에서 웹 브라우저, 웹앱 및 Microsoft ID 플랫폼 간의 상호 작용에 대한 다이어그램](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>필수 구성 요소
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 또는 [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>앱 등록 및 다운로드
> 애플리케이션 빌드를 시작할 수 있는 두 가지 옵션, 즉 자동 구성 또는 수동 구성이 있습니다.
>
> ### <a name="automatic-configuration"></a>자동 구성
> 앱을 자동으로 구성한 다음, 코드 샘플을 다운로드하려면 다음 단계를 수행합니다.
>
> 1. <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">앱 등록을 위한 Azure Portal 페이지</a>로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록** 을 선택합니다.
> 1. 지침에 따라 한 번의 클릭으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="manual-configuration"></a>수동 구성
> 애플리케이션 및 코드 샘플을 수동으로 구성하려면 다음 절차를 사용합니다.
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. **이름** 에 대해 애플리케이션 이름을 입력합니다. 예를 들어 **AspNetCore-Quickstart** 를 입력합니다. 이 이름은 앱의 사용자에게 표시되며 나중에 변경할 수 있습니다.
> 1. **리디렉션 URI** 에 대해 **https://localhost:44321/signin-oidc** 를 입력합니다.
> 1. **등록** 을 선택합니다.
> 1. **관리** 에서 **인증** 을 선택합니다.
> 1. **프런트 채널 로그아웃 URL** 에 대해 **https://localhost:44321/signout-oidc** 를 입력합니다.
> 1. **암시적 권한 부여 및 하이브리드 흐름** 에서 **ID 토큰** 을 선택합니다.
> 1. **저장** 을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 다음을 수행합니다.
> - **리디렉션 URI** 에 대해 **https://localhost:44321/** 및 **https://localhost:44321/signin-oidc** 를 입력합니다.
> - **프런트 채널 로그아웃 URL** 에 대해 **https://localhost:44321/signout-oidc** 를 입력합니다. 
>
> 권한 부여 엔드포인트에서 요청 ID 토큰을 발급합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-aspnet-core-project"></a>2단계: ASP.NET Core 프로젝트 다운로드

> [!div renderon="docs"]
> [ASP.NET Core 솔루션 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 프로젝트를 실행합니다.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱의 속성 값을 사용하여 프로젝트를 구성했으며 실행할 준비가 되었습니다.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>3단계: ASP.NET Core 프로젝트 구성
> 1. 드라이브 루트 근처의 로컬 폴더로 .zip 보관 파일을 추출합니다. 예를 들어 파일의 압축을 *C:\Azure-Samples* 에 풉니다.
> 
>    Windows의 경로 길이 제한으로 인해 발생하는 오류를 방지하려면 보관 파일의 압축을 드라이브 루트에 가까운 디렉터리에 푸는 것이 좋습니다.
> 1. Visual Studio 2019에서 솔루션을 엽니다.
> 1. *appsettings.json* 파일을 열고, 다음 코드를 수정합니다.
>
>    ```json
>    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - `Enter_the_Application_Id_here`를 Azure Portal에 등록한 애플리케이션의 애플리케이션(클라이언트) ID로 바꿉니다. 앱의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 를 확인할 수 있습니다.
>    - `common`을 다음 중 하나로 바꿉니다.
>       - 애플리케이션에서 **이 조직 디렉터리의 계정만** 을 지원하는 경우 이 값을 디렉터리(테넌트) ID(GUID) 또는 테넌트 이름(예: `contoso.onmicrosoft.com`)으로 바꿉니다. 앱의 **개요** 페이지에서 **디렉터리(테넌트) ID** 값을 확인할 수 있습니다.
>       - 애플리케이션에서 **모든 조직 디렉터리의 계정** 을 지원하는 경우 이 값을 `organizations`로 바꿉니다.
>       - 애플리케이션에서 **모든 Microsoft 계정 사용자** 를 지원하는 경우 이 값을 `common`으로 둡니다.
>
> 이 빠른 시작에서는 *appsettings.json* 파일에서 다른 값을 변경하지 마세요.
>
> #### <a name="step-4-build-and-run-the-application"></a>4단계: 애플리케이션 빌드 및 실행
>
> **디버그** 메뉴 > **디버깅 시작** 을 차례로 선택하거나 F5 키를 눌러 Visual Studio에서 앱을 빌드하고 실행합니다.
>
> 자격 증명을 입력하라는 메시지가 표시된 다음, 앱에 필요한 권한에 동의하라는 메시지가 표시됩니다. 동의 프롬프트에서 **수락** 을 선택합니다.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="앱이 사용자에게 요청하는 권한을 보여 주는 동의 대화 상자의 스크린샷":::
>
> 요청된 권한에 동의하면 Azure Active Directory 자격 증명을 사용하여 성공적으로 로그인했다고 앱에 표시됩니다.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="실행 중인 웹앱 및 로그인한 사용자를 보여 주는 웹 브라우저의 스크린샷":::

## <a name="more-information"></a>자세한 정보

이 섹션에서는 로그인 사용자에 필요한 코드에 대한 개요를 제공합니다. 이 개요는 코드의 작동 방식, 기본 인수의 개념 및 로그인을 기존 ASP.NET Core 애플리케이션에 추가하는 방법을 이해하는 데 유용할 수 있습니다.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>샘플 작동 방법
>
> ![샘플 앱에서 웹 브라우저, 웹앱 및 Microsoft ID 플랫폼 간의 상호 작용에 대한 다이어그램](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>시작 클래스

*Microsoft.AspNetCore.Authentication* 미들웨어는 호스팅 프로세스가 시작될 때 실행되는 `Startup` 클래스를 사용합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    });
   services.AddRazorPages()
        .AddMicrosoftIdentityUI();
}
```

`AddAuthentication()` 메서드는 쿠키 기반 인증을 추가하도록 서비스를 구성합니다. 이 인증은 브라우저 시나리오에서 OpenID Connect에 대한 질문을 설정하는 데 사용됩니다.

`.AddMicrosoftIdentityWebApp`이 포함된 줄은 Microsoft ID 플랫폼 인증을 애플리케이션에 추가합니다. 그런 다음, 애플리케이션이 *appsettings.json* 구성 파일의 `AzureAD` 섹션에 있는 정보를 기반으로 하여 사용자를 로그인하도록 구성됩니다.

| *appsettings.json* 키 | 설명                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure Portal에 등록된 애플리케이션의 애플리케이션(클라이언트) ID입니다.                                                                                       |
| `Instance`             | 사용자가 인증하는 STS(보안 토큰 서비스) 엔드포인트입니다. 이 값은 일반적으로 `https://login.microsoftonline.com/`이며, Azure 퍼블릭 클라우드를 나타냅니다. |
| `TenantId`             | 회사 계정, 학교 계정 또는 Microsoft 개인 계정을 사용하여 사용자를 로그인하는 테넌트의 이름 또는 테넌트 ID(GUID) 또는 `common`입니다.                             |

`Configure()` 메서드에는 명명된 기능을 사용할 수 있도록 설정하는 두 가지 중요한 메서드인 `app.UseAuthentication()` 및 `app.UseAuthorization()`이 포함되어 있습니다. 또한 `Configure()` 메서드에서 `endpoints.MapControllerRoute()` 또는 `endpoints.MapControllers()`에 대한 호출을 하나 이상 사용하여 Microsoft ID 웹의 경로를 등록해야 합니다.

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
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>컨트롤러 또는 메서드를 보호하기 위한 특성

컨트롤러 또는 컨트롤러 메서드는 `[Authorize]` 특성을 사용하여 보호할 수 있습니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러 또는 메서드에 대한 액세스를 제한합니다. 사용자가 인증되지 않은 경우 컨트롤러에 액세스하기 위해 인증 질문을 시작할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

이 ASP.NET Core 자습서를 포함하는 GitHub 리포지토리에는 다음 방법을 보여주는 지침과 추가 코드 샘플이 포함되어 있습니다.

- 새 ASP.NET Core 웹 애플리케이션에 인증 추가
- Microsoft Graph, 기타 Microsoft API 또는 사용자 고유의 웹 API 호출
- 권한 부여 추가
- 국가별 클라우드 또는 소셜 ID를 사용하여 사용자 로그인

> [!div class="nextstepaction"]
> [GitHub의 ASP.NET Core 웹앱 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
