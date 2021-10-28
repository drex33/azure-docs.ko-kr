---
title: Azure Active Directory B2C 구성 요소를 사용하여 웹앱에서 인증 사용
description: 이 문서에서는 Azure Active Directory B2C의 구성 요소를 사용하여 ASP.NET 웹앱에서 사용자를 로그인하고 등록하는 방법에 대해 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 66b37e5464d548559b8d4e08922f1970e0c8bb7f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040819"
---
# <a name="enable-authentication-in-your-own-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 사용자 자체 웹앱에서 인증을 사용하도록 설정합니다.

이 문서에서는 고유한 ASP.NET 웹 애플리케이션에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 보여 줍니다. [OpenID Connect](openid-connect.md) 프로토콜을 사용하는 ASP.NET Core 미들웨어로 ASP.NET Core 웹 애플리케이션을 만드는 방법을 알아봅니다. 

[샘플 웹앱에서 인증 구성](configure-authentication-sample-web-app.md)과 함께 이 문서를 사용하여 샘플 웹앱을 사용자 고유의 웹앱으로 바꿉니다.

## <a name="prerequisites"></a>필수 조건

필수 조건 및 통합 지침을 검토하려면 [샘플 웹 애플리케이션에서 인증 구성](configure-authentication-sample-web-app.md)을 참조하세요.

## <a name="step-1-create-a-web-app-project"></a>1단계: 웹앱 프로젝트 만들기

기존 ASP.NET MVC(Model-View-Controller) 웹앱 프로젝트를 사용하거나 새로 만들 수 있습니다. 새 프로젝트를 만들려면 명령 셸을 열고 다음 명령을 입력합니다.

```dotnetcli
dotnet new mvc -o mywebapp
```

앞의 명령은 다음을 수행합니다.

* 새 MVC 웹앱을 만듭니다.  
* `-o mywebapp` 매개 변수는 앱의 원본 파일을 사용하여 ‘mywebapp’이라는 이름의 디렉터리를 만듭니다.

## <a name="step-2-add-the-authentication-libraries"></a>2단계: 인증 라이브러리 추가

웹앱에 Azure AD B2C 인증 및 권한 부여 지원을 간편하게 추가하는 ASP.NET Core 라이브러리 집합인 Microsoft ID 웹 라이브러리를 추가합니다. Microsoft ID 웹 라이브러리는 쿠키 기반 인증을 사용하여 인증 파이프라인을 설정합니다. HTTP 인증 메시지 송수신, 토큰 유효성 검사, 클레임 추출 등을 처리합니다.

Microsoft ID 웹 라이브러리를 추가하려면 다음 명령을 실행하여 패키지를 설치합니다. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI 
```

---


## <a name="step-3-initiate-the-authentication-libraries"></a>3단계: 인증 라이브러리 시작

Microsoft ID 웹 미들웨어는 호스팅 프로세스가 시작될 때 실행되는 시작 클래스를 사용합니다. 이 단계에서는 인증 라이브러리를 시작하는 데 필요한 코드를 추가합니다.

*Startup.cs* 를 열고 클래스의 시작 부분에 다음 `using` 선언을 추가합니다.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Microsoft ID 웹은 쿠키 기반 인증을 사용하여 웹앱을 보호하므로 다음 코드는 *SameSite* 쿠키 설정을 지정합니다. 그런 다음, `AzureAdB2C` 애플리케이션 설정을 읽고 해당 보기로 미들웨어 컨트롤러를 시작합니다. 

`ConfigureServices(IServiceCollection services)` 함수를 다음 코드 조각으로 바꿉니다. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

다음 코드는 쿠키 정책을 추가하고 인증 모델을 사용합니다. `Configure` 함수를 다음 코드 조각으로 바꿉니다. 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="step-4-add-the-ui-elements"></a>4단계: UI 요소 추가

사용자 인터페이스 요소를 추가하려면 사용자가 로그인했는지 여부를 확인하는 논리가 포함된 부분 보기를 사용합니다. 사용자가 로그인하지 않은 경우 부분 보기는 로그인 단추를 렌더링합니다. 사용자가 로그인하면 사용자의 표시 이름 및 로그아웃 단추가 표시됩니다.
  
다음 코드 조각을 사용하여 */Views/Shared* 폴더 안에 *\_LoginPartial.cshtml* 이라는 새 파일을 만듭니다.

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

추가한 *_LoginPartial.cshtml* 파일을 포함하도록 */Views/Shared_Layout.cshtml* 파일을 수정합니다. *_Layout.cshtml* 파일은 사용자가 페이지 간을 이동할 때 일관된 환경을 제공하는 공통 레이아웃입니다. 일반적으로 레이아웃에는 앱 머리글, 바닥글과 같은 공통 사용자 인터페이스 요소가 포함됩니다.

> [!NOTE]
> 실행 중인 .NET Core 버전 및 기존 앱에 로그인을 추가하는지 여부에 따라 UI 요소가 다르게 보일 수 있습니다. 이 경우 페이지 레이아웃 내의 적절한 위치에 *_LoginPartial* 을 포함해야 합니다.

*/Views/Shared/_Layout.cshtml* 파일을 열고 다음 `div` 요소를 추가합니다.

```razor
<div class="navbar-collapse collapse">
...
</div>
```

이 요소를 다음 Razor 코드로 바꿉니다.

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

위의 Razor 코드에는 다음 단계에서 만들 `Claims` 작업에 대한 링크가 포함되어 있습니다.

## <a name="step-5-add-the-claims-view"></a>5단계: 클레임 보기 추가

ID 토큰 클레임을 보려면 */Views/Home* 폴더 아래에 *Claims.cshtml* 보기를 추가합니다.

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

이 단계에서는 *Claims.cshtml* 보기를 *홈* 컨트롤러에 연결하는 `Claims` 작업을 추가합니다. `Claims` 작업에 대한 액세스 권한을 인증된 사용자로 제한하는 `Authorize` 특성을 사용합니다.  

*/Controllers/HomeController.cs* 컨트롤러에서 다음 작업을 추가합니다.

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

클래스의 시작 부분에 다음 `using` 선언을 추가합니다.

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-app-settings"></a>6단계: 앱 설정 추가

Azure AD B2C ID 공급자 설정은 *appsettings.json* 파일에 저장됩니다. *appsettings.json* 을 열고 다음 설정을 추가합니다.

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

필요한 정보는 [샘플 웹앱에서 인증 구성](configure-authentication-sample-web-app.md) 문서에 설명되어 있습니다. 다음 설정을 사용합니다.

* **인스턴스**: `<your-tenant-name>`을 Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분으로 바꿉니다(예: `https://contoso.b2clogin.com`).
* **도메인**: `<your-b2c-domain>`을 Azure AD B2C 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)으로 바꿉니다(예: `contoso.onmicrosoft.com`).
* **클라이언트 ID**: `<web-app-application-id>`를 [2단계](configure-authentication-sample-web-app.md#step-2-register-a-web-application)의 애플리케이션 ID로 바꿉니다.
* **정책 이름**: `<your-sign-up-in-policy>`를 [1단계](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow)에서 만든 사용자 흐름으로 바꿉니다.

## <a name="step-7-run-your-application"></a>7단계: 애플리케이션 실행

1. 프로젝트를 빌드하고 실행합니다.
1. `https://localhost:5001`로 이동합니다. 
1. **등록/로그인** 을 선택합니다.
1. 가입 또는 로그인 프로세스를 완료합니다.

성공적으로 인증되면 탐색 모음에 표시 이름이 나타납니다. Azure AD B2C 토큰이 앱에 반환하는 클레임을 보려면 **클레임** 을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [웹앱에 대한 Azure AD B2C 인증 환경 사용자 지정 및 개선](enable-authentication-web-application-options.md) 방법을 알아봅니다.
