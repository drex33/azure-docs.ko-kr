---
title: Azure Active Directory B2C를 사용하여 웹앱 인증 옵션을 사용하도록 설정
description: 이 문서에서는 웹앱 인증 옵션을 사용하도록 설정하는 여러 가지 방법을 설명합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 50cdb5f171614c138427b358f2418b8b81751457
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220323"
---
# <a name="enable-authentication-options-in-a-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 웹앱에서 인증 옵션을 사용하도록 설정 

이 문서에서는 웹 애플리케이션에 대한 Azure AD B2C(Azure Active Directory B2C) 인증 환경을 사용하도록 설정, 사용자 지정 및 개선할 수 있는 방법을 설명합니다. 

시작하기 전에 다음 문서를 숙지하는 것이 중요합니다. 
* [샘플 웹앱에서 인증 구성](configure-authentication-sample-web-app.md)
* [자체 웹앱에서 인증을 사용하도록 설정](enable-authentication-web-application.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

인증 URL에서 사용자 지정 도메인과 테넌트 ID를 사용하려면 [사용자 지정 도메인을 사용하는 것으로 설정](custom-domain.md)의 참고 자료를 따릅니다. 프로젝트 루트 폴더에서 *appsettings.json* 파일을 엽니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다.

*appsettings.json* 파일에서 다음을 수행합니다.

- 사용자 지정 도메인을 사용하여 `Instance` 항목을 업데이트합니다.
- [테넌트 ID](tenant-management.md#get-your-tenant-id)를 사용하여 `Domain` 항목을 업데이트합니다. 자세한 내용은 [테넌트 ID 사용](custom-domain.md#optional-use-tenant-id)을 참조하세요.

다음 JSON은 변경 전의 앱 설정을 보여 줍니다. 

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

다음 JSON은 변경 후의 앱 설정을 보여 줍니다. 

```JSON
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>고급 시나리오 지원

개발자는 Microsoft ID 플랫폼 API의 `AddMicrosoftIdentityWebAppAuthentication` 메서드를 사용하여 고급 인증 시나리오에 대한 코드를 추가하거나 OpenIdConnect 이벤트를 구독할 수 있습니다. 예를 들어, 앱이 Azure AD B2C로 보내는 인증 요청을 사용자 지정할 수 있는 OnRedirectToIdentityProvider를 구독할 수 있습니다.

고급 시나리오를 지원하려면 *Startup.cs* 파일을 열고 `ConfigureServices` 함수에서 `AddMicrosoftIdentityWebAppAuthentication`을 다음 코드 스니펫으로 바꿉니다. 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

위의 코드는 `OnRedirectToIdentityProviderFunc` 메서드에 대한 참조와 함께 OnRedirectToIdentityProvider 이벤트를 추가합니다. `Startup.cs` 클래스에 다음 코드 조각을 추가합니다.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

컨텍스트 매개 변수를 사용하여 컨트롤러와 `OnRedirectToIdentityProvider` 함수 간에 매개 변수를 전달할 수 있습니다. 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 사용자 지정 정책을 사용하는 경우 [직접 로그인 설정](direct-signin.md#prepopulate-the-sign-in-name)에 설명된 대로 필요한 입력 클레임을 추가합니다. 
1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. `OnRedirectToIdentityProvider` 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 외부 ID 공급자의 도메인 이름을 확인합니다. 자세한 내용은 [소셜 공급자로 로그인 리디렉션](direct-signin.md#redirect-sign-in-to-a-social-provider)을 참조하세요. 
1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. `OnRedirectToIdentityProviderFunc` 함수에서 `OnRedirectToIdentityProvider` 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [언어 사용자 지정을 구성](language-customization.md)합니다.
1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. `OnRedirectToIdentityProvider` 함수에 다음 코드 줄을 추가합니다.

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 요소를 구성합니다.
1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. `OnRedirectToIdentityProvider` 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. [고급 시나리오 지원](#support-advanced-scenarios) 프로시저를 완료합니다.
1. 사용자 지정 정책에서 [ID 토큰 힌트 기술 프로필](id-token-hint.md)을 정의합니다.
1. `OnRedirectToIdentityProvider` 함수에 다음 코드 줄을 추가합니다.
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>계정 컨트롤러

*SignIn*, *SignUp* 또는 *SignOut* 작업을 사용자 지정하려면 자체 컨트롤러를 만드는 것이 좋습니다. 사용자 고유의 컨트롤러를 사용하면 컨트롤러와 인증 라이브러리 간에 매개 변수를 전달할 수 있습니다. `AccountController`는 로그인 및 로그아웃 작업을 처리하는 `Microsoft.Identity.Web.UI` NuGet 패키지의 일부입니다. [Microsoft ID 웹 라이브러리](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs)에서 해당 기능이 구현된 것을 확인하실 수 있습니다. 

### <a name="add-the-account-controller"></a>계정 컨트롤러 추가

Visual Studio 프로젝트에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 새 **컨트롤러** 를 추가합니다. **MVC - 빈 컨트롤러** 를 선택하고 **MyAccountController.cs** 라는 이름을 제공합니다.

다음 코드 조각에서는 *SignIn* 작업을 사용하는 사용자 지정 `MyAccountController`을 보여 줍니다.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            return Challenge(properties, scheme);
        }

    }
}
```

*_LoginPartial.cshtml* 보기에서 컨트롤러에 대한 로그인 링크를 변경합니다.

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>Azure AD B2C 정책 ID 전달

다음 코드 조각에서는 **SignIn** 및 **SignUp** 작업을 사용하는 사용자 지정 `MyAccountController`를 보여 줍니다. 해당 작업은 `policy`이라는 매개 변수를 인증 라이브러리에 전달합니다. 이렇게 하면 특정 작업에 대해 올바른 Azure AD B2C 정책 ID를 제공할 수 있습니다.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

*_LoginPartial.cshtml* 보기에서 등록 또는 프로필 편집과 같은 다른 모든 인증 링크에 대해 `asp-controller` 값을 `MyAccountController`로 변경합니다.

### <a name="pass-custom-parameters"></a>사용자 지정 매개 변수 전달

다음 코드 조각에서는 **SignIn** 작업을 사용하는 사용자 지정 `MyAccountController`을 보여 줍니다. 해당 작업은 `campaign_id`이라는 매개 변수를 인증 라이브러리에 전달합니다.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

[고급 시나리오 지원](#support-advanced-scenarios) 절차를 완료한 다음 `OnRedirectToIdentityProvider` 메서드에서 사용자 지정 매개 변수를 읽습니다.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>로그아웃 리디렉션 보안

로그아웃 후 사용자는 애플리케이션에 대해 지정된 회신 URL에 관계없이 `post_logout_redirect_uri` 매개 변수에 지정된 URI로 리디렉션됩니다. 그러나 유효한 `id_token_hint`가 전달되고 [로그아웃 요청에서 ID 토큰 요구](session-behavior.md#secure-your-logout-redirect)가 켜져 있는 경우 Azure AD B2C는 리디렉션을 수행하기 전 `post_logout_redirect_uri` 값이 애플리케이션의 구성된 리디렉션 URI 중 하나와 일치하는지 확인합니다. 애플리케이션에 일치하는 회신 URL이 구성되지 않은 경우 오류 메시지가 표시되고 사용자는 리디렉션되지 않습니다.

애플리케이션에서 보안 로그아웃 리디렉션을 지원하려면 먼저 [계정 컨트롤러](enable-authentication-web-application-options.md#add-the-account-controller) 및 [고급 시나리오 지원](#support-advanced-scenarios) 섹션의 단계를 따릅니다. 그런 다음 아래 단계를 따릅니다.

1. `MyAccountController.cs` 컨트롤러에서 다음 코드 조각을 사용하여 **SignOut** 작업을 추가합니다.

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. **Startup.cs** 클래스에서 `id_token_hint` 값을 구문 분석하고 값을 인증 요청에 추가합니다. 다음 코드 조각은 `id_token_hint` 값을 인증 요청에 전달하는 방법을 보여 줍니다.

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. `ConfigureServices` 함수에서 **Controllers** 가 `id_token` 값에 액세스할 수 있도록 `SaveTokens` 옵션을 추가합니다. 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. **appsettings.json** 구성 파일에서 로그아웃 리디렉션 URI 경로를 `SignedOutCallbackPath` 키에 추가합니다.

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

위의 예제에서 로그아웃 요청으로 전달된 **post_logout_redirect_uri** 의 형식은 `https://your-app.com/signout/<your-sign-up-in-policy>`입니다. 이 URL을 애플리케이션 등록의 회신 URL에 추가해야 합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[ASP.NET Core의 권한 부여](/aspnet/core/security/authorization/introduction)를 사용하면 다음 방법 중 하나를 사용하여 사용자가 보호된 리소스에 액세스할 수 있는 권한이 있는지 확인할 수 있습니다. 
* [역할 기반 권한 부여](/aspnet/core/security/authorization/roles) 
* [클레임 기반 권한 부여](/aspnet/core/security/authorization/claims) 
* [정책 기반 권한 부여](/aspnet/core/security/authorization/policies)

`ConfigureServices` 메서드에서 권한 부여 모델을 추가하는 `AddAuthorization` 메서드를 추가합니다. 다음 예시에서는 `EmployeeOnly`이라는 정책을 생성합니다. 이 정책은 클레임 `EmployeeNumber`가 존재하는지 여부를 확인합니다. 클레임 값은 1, 2, 3, 4, 5와 같은 ID 중 하나여야 합니다.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

ASP.NET Core의 권한 부여는 [AuthorizeAttribute](/aspnet/core/security/authorization/simple) 및 다양한 매개 변수를 사용하여 제어됩니다. 가장 기본적인 형태에서 컨트롤러, 작업, Razor 페이지에 `Authorize` 특성을 적용하면 해당 구성 요소의 인증된 사용자에 대한 액세스를 제한합니다.

정책 이름과 함께 `Authorize` 특성을 사용하여 정책을 컨트롤러에 적용합니다. 다음 코드는 `EmployeeOnly` 정책에 따라 권한이 부여된 사용자의 `Claims` 작업에 대한 액세스를 제한합니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>다음 단계

- 권한 부여에 대한 자세한 내용은 [ASP.NET Core의 권한 부여 소개](/aspnet/core/security/authorization/introduction)를 참조하세요.
