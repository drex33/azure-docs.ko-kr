---
title: 사용자가 로그인하는 웹앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 사용자가 로그인하는 웹앱을 빌드하는 방법 알아보기(코드 구성)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 39ca13ebaea9469dbbfa21cc8ded20b36dc2ed3d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050663"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>사용자가 로그인하는 웹앱: 코드 구성

사용자가 로그인하는 웹앱의 코드를 구성하는 방법을 알아봅니다.

## <a name="microsoft-libraries-supporting-web-apps"></a>웹앱을 지원하는 Microsoft 라이브러리

<!-- This section can be in an include for web app and web APIs -->
웹앱(및 웹 API)을 보호하는 데 사용되는 Microsoft 라이브러리는 다음과 같습니다.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

관심 있는 플랫폼에 해당하는 탭을 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 문서 및 다음 항목의 코드 조각은 [ASP.NET Core 웹앱 증분 자습서의 1장](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)에서 추출되었습니다.

전체 구현에 대한 자세한 내용은 해당 자습서를 참조하는 것이 좋습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 문서 및 다음 항목의 코드 조각은 [ASP.NET 웹앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)에서 추출되었습니다.

전체 구현에 대한 자세한 내용은 해당 샘플을 참조하는 것이 좋습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서 및 다음 항목의 코드 조각은 [Microsoft Graph를 호출하는 Java 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-java-webapp)에서 추출되었습니다.

전체 구현에 대한 자세한 내용은 해당 샘플을 참조하는 것이 좋습니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 문서 및 다음 항목의 코드 조각은 MSAL 노드에서의 [사용자가 로그인하는 Node.js 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-node) 샘플에서 추출되었습니다.

전체 구현에 대한 자세한 내용은 해당 샘플을 참조하는 것이 좋습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서 및 다음 항목의 코드 조각은 MSAL Python에서의 [Microsoft Graph를 호출하는 Python 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-python-webapp) 샘플에서 추출되었습니다.

전체 구현에 대한 자세한 내용은 해당 샘플을 참조하는 것이 좋습니다.

---

## <a name="configuration-files"></a>구성 파일

Microsoft ID 플랫폼을 사용하여 사용자가 로그인하는 웹 애플리케이션은 구성 파일을 통해 구성됩니다. 구성에서 지정해야 하는 값은 다음과 같습니다.

- 국가 클라우드 등에서 앱을 실행하려는 경우 클라우드 인스턴스(`Instance`)
- 테넌트 ID(`TenantId`)의 대상
- Azure Portal에서 복사한 애플리케이션의 클라이언트 ID(`ClientId`)

`Authority`에 대한 참조도 확인할 수 있습니다. `Authority` 값은 `Instance` 및 `TenantId` 값의 연결입니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 해당 설정은 “AzureAD” 섹션의 [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) 파일에 있습니다.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.partner.microsoftonline.cn/common" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

ASP.NET Core에서 다른 파일([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7))에는 애플리케이션 및 다양한 프로필에 대한 URL(`applicationUrl`) 및 TLS/SSL 포트(`sslPort`)가 포함되어 있습니다.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure Portal에서 애플리케이션의 **인증** 페이지에 등록하는 리디렉션 URL은 해당 URL과 일치해야 합니다. 위의 두 구성 파일은 `https://localhost:44321/signin-oidc`입니다. 그 이유는 `applicationUrl`이 `http://localhost:3110`이기 때문이지만, `sslPort`는 지정됩니다(44321). `CallbackPath`는 `appsettings.json`에 정의된 대로 `/signin-oidc`입니다.

동일한 방식으로 로그아웃 URL은 `https://localhost:44321/signout-oidc`로 설정됩니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 애플리케이션은 [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)파일의 12~15번째 줄을 통해 구성됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure Portal에서 애플리케이션의 **인증** 페이지에 등록하는 응답 URI는 해당 URL과 일치해야 합니다. 즉, `https://localhost:44326/`여야 합니다.

# <a name="java"></a>[Java](#tab/java)

Java에서 구성은 `src/main/resources`에 위치한 [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties)에 있습니다.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure Portal에서 애플리케이션의 **인증** 페이지에 등록하는 응답 URI는 애플리케이션이 정의하는 `redirectUri` 인스턴스와 일치해야 합니다. 즉, `http://localhost:8080/msal4jsample/secure/aad` 및 `http://localhost:8080/msal4jsample/graph/me`여야 합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

여기에서 구성 매개 변수는 `index.js`에 있습니다.

```javascript

const REDIRECT_URI = "http://localhost:3000/redirect";

const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};
```

Azure Portal에서 애플리케이션의 인증 페이지에 등록하는 응답 URI는 애플리케이션이 정의하는 redirectUri 인스턴스와 일치해야 합니다(`http://localhost:3000/redirect`).

> [!NOTE]
> 해당 빠른 시작에서는 단순성을 위해 구성 파일에 클라이언트 암호를 저장할 것을 제안합니다. 프로덕션 앱에서 키 자격 증명 모음 또는 환경 변수와 같은 다른 방법을 사용하여 암호를 저장할 수도 있습니다.

# <a name="python"></a>[Python](#tab/python)

다음은 [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)의 Python 구성 파일입니다.

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common"
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 해당 빠른 시작에서는 단순성을 위해 구성 파일에 클라이언트 암호를 저장할 것을 제안합니다. 프로덕션 앱에서 [Flask의 설명서](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)에 설명된 대로 키 자격 증명 모음 또는 환경 변수와 같은 다른 방법을 사용하여 암호를 저장할 수도 있습니다.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>초기화 코드

초기화 코드는 플랫폼에 따라 다릅니다. ASP.NET Core 및 ASP.NET의 경우 사용자 로그인은 OpenID Connect 미들웨어에 위임됩니다. ASP.NET 또는 ASP.NET Core 템플릿은 Azure AD(Azure Active Directory) v1.0 엔드포인트에 대한 웹 애플리케이션을 생성합니다. Microsoft ID 플랫폼에 맞게 조정하려면 몇 가지 구성이 필요합니다. Java의 경우 애플리케이션의 협업을 통해 Spring에서 처리합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 웹앱(및 웹 API)의 경우 컨트롤러 또는 컨트롤러 작업에 `[Authorize]` 특성이 있으므로 애플리케이션이 보호됩니다. 이 특성은 사용자가 인증되었는지 확인합니다. 애플리케이션을 초기화하는 코드는 *Startup.cs* 파일에 있습니다.

Microsoft ID 플랫폼(이전의 Azure AD v2.0)으로 인증을 추가하려면 다음 코드를 추가해야 합니다. 코드의 설명은 쉽게 이해할 수 있어야 합니다.

> [!NOTE]
> Microsoft.Identity.Web을 활용하는 Microsoft ID 플랫폼용 새 ASP.NET Core 템플릿을 사용하여 직접 시작하려는 경우에는 .NET Core 3.1 및 .NET 5.0용 프로젝트 템플릿이 포함된 미리 보기 NuGet 패키지를 다운로드할 수 있습니다. 설치가 완료되면 ASP.NET Core 웹 애플리케이션(MVC 또는 Blazor)을 직접 인스턴스화할 수 있습니다. 자세한 내용은 [Microsoft.Identity.Web 웹앱 프로젝트 템플릿](https://aka.ms/ms-id-web/webapp-project-templates)을 참조하세요. 이 방법은 아래의 모든 단계를 수행하는 가장 간단한 방법입니다.
>
> Visual Studio 내에서 현재의 기본 ASP.NET Core 웹 프로젝트를 사용하거나 `dotnet new mvc --auth SingleOrg` 또는 `dotnet new webapp --auth SingleOrg`를 사용하여 프로젝트를 시작하려는 경우 다음과 같은 코드가 표시됩니다.
>
> ```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> 해당 코드는 Azure AD v1.0 애플리케이션을 만드는 데 사용되는 레거시 **Microsoft.AspNetCore.Authentication.AzureAD.UI** NuGet 패키지를 사용합니다. 이 문서에서는 해당 코드를 대체하는 Microsoft ID 플랫폼(Azure AD v2.0) 애플리케이션을 만드는 방법을 설명합니다.
>

1. 프로젝트에 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) 및 [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) NuGet 패키지를 추가합니다. Microsoft.AspNetCore.Authentication.AzureAD.UI NuGet 패키지를 제거합니다(있는 경우).

2. `AddMicrosoftIdentityWebAppAuthentication` 및 `AddMicrosoftIdentityUI` 메서드를 사용하도록 `ConfigureServices`의 코드를 업데이트합니다.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. *Startup.cs* 의 `Configure` 메서드에서 `app.UseAuthentication();`을 호출하여 인증을 사용하도록 설정합니다.

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

위의 코드에서 다음을 수행합니다.
- `AddMicrosoftIdentityWebAppAuthentication` 확장 메서드는 **Microsoft.Identity.Web** 에 정의되어 있습니다. 해당 항목은 다음을 수행합니다.
  - 인증 서비스를 추가합니다.
  - 옵션을 구성하여 구성 파일을 읽습니다(이 경우 “AzureAD” 섹션에서).
  - Microsoft ID 플랫폼에 권한이 부여되도록 OpenID Connect 옵션을 구성합니다.
  - 토큰 발급자의 유효성을 검사합니다.
  - 이름에 해당하는 클레임이 ID 토큰의 `preferred_username` 클레임에서 매핑되었는지 확인합니다.

- 구성 개체 외에도 `AddMicrosoftIdentityWebAppAuthentication`을 호출할 때 구성 섹션의 이름을 지정할 수 있습니다. 기본적으로 `AzureAd`입니다.

- `AddMicrosoftIdentityWebAppAuthentication`에는 고급 시나리오에 대한 다른 매개 변수가 있습니다. 예를 들어 OpenID Connect 미들웨어 이벤트를 추적하면 인증이 작동하지 않을 때 웹 애플리케이션의 문제 해결을 지원할 수 있습니다. 선택적 매개 변수 `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents`를 `true`로 설정하면 ASP.NET Core 미들웨어 집합이 HTTP 응답으로부터 `HttpContext.User`의 사용자 ID로 진행할 때 정보를 처리하는 방법을 보여줍니다.

- `AddMicrosoftIdentityUI` 확장 메서드는 **Microsoft.Identity.Web.UI** 에 정의되어 있습니다. 로그인 및 로그아웃을 처리하는 기본 컨트롤러를 제공합니다.

Microsoft.Identity.Web이 웹앱 생성을 지원하는 방법에 대한 자세한 내용은 <https://aka.ms/ms-id-web/webapp>을 참조하세요.

> [!WARNING]
> 현재는 Azure AD를 외부 로그인 공급자로 사용하는 경우 Microsoft.Identity.Web에서 **개별 사용자 계정** 의 시나리오(앱 내 사용자 계정 저장)를 지원하지 않습니다. 자세한 내용은 다음 문서를 참조하세요. [AzureAD/microsoft-identity-web#133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 웹앱 및 웹 API의 인증과 관련된 코드는 [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) 파일에 있습니다.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java 샘플에서는 Spring 프레임워크를 사용합니다. 각 HTTP 응답을 가로채는 필터를 구현하기 때문에 애플리케이션이 보호됩니다. Java 웹앱의 빠른 시작에서 이 필터는 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`의 `AuthFilter`입니다.

이 필터는 OAuth 2.0 인증 코드 흐름을 처리하고 사용자가 인증되었는지 확인합니다(`isAuthenticated()` 메서드). 사용자가 인증되지 않은 경우 Azure AD 권한 부여 엔드포인트의 URL을 계산하고 브라우저를 해당 URI로 리디렉션합니다.

인증 코드를 포함하는 응답이 도착하면 MSAL Java를 사용하여 토큰을 획득합니다. 마지막으로 리디렉션 URI에서 토큰 엔드포인트로부터 토큰을 수신하면 사용자가 로그인됩니다.

자세한 내용은 [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)의 `doFilter()` 메서드를 참조하세요.

> [!NOTE]
> `doFilter()`의 코드는 약간 다른 순서로 작성되지만 흐름은 설명된 것과 동일합니다.

해당 메서드가 트리거하는 인증 코드 흐름에 대한 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 참조하세요.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
const msal = require('@azure/msal-node');

// Create msal application object
const cca = new msal.ConfidentialClientApplication(config);
```

# <a name="python"></a>[Python](#tab/python)

Python 샘플은 Flask를 사용합니다. Flask 및 MSAL Python의 초기화는 [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)에서 수행됩니다.

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config

app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>다음 단계

다음 문서에서는 로그인 및 로그아웃을 트리거하는 방법을 알아봅니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 시나리오의 다음 문서인 [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)으로 이동합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 시나리오의 다음 문서인 [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)으로 이동합니다.

# <a name="java"></a>[Java](#tab/java)

이 시나리오의 다음 문서인 [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=java)으로 이동합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 시나리오의 다음 문서인 [로그인](./scenario-web-app-sign-user-sign-in.md?tabs=nodejs)으로 이동합니다.

# <a name="python"></a>[Python](#tab/python)

이 시나리오의 다음 문서인 [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=python)으로 이동합니다.

---
