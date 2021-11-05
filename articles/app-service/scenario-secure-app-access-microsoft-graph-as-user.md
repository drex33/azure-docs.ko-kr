---
title: 자습서 - 웹앱이 사용자로 Microsoft Graph에 액세스| Azure
description: 이 자습서에서는 로그인한 사용자를 위해 Microsoft Graph의 데이터에 액세스하는 방법을 알아봅니다.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: b20ae4e6cec7ad3ce710c6e9670ff580b30f2638
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462313"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>자습서: 보안 앱에서 사용자로 Microsoft Graph에 액세스

Azure App Service에서 실행되는 웹앱에서 Microsoft Graph에 액세스하는 방법에 대해 알아봅니다.

:::image type="content" alt-text="Microsoft Graph 액세스를 보여주는 다이어그램." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

웹앱에서 Microsoft Graph에 대한 액세스 권한을 추가하고 로그인한 사용자로 작업을 수행하려고 합니다. 이 섹션에서는 위임된 권한을 웹앱에 부여하고 Azure AD(Azure Active Directory)에서 로그인한 사용자의 프로필 정보를 가져오는 방법을 설명합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 위임된 권한을 웹앱에 부여합니다.
> * 로그인한 사용자를 위해 웹앱에서 Microsoft Graph를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [App Service 인증/권한 부여 모듈을 사용하도록 설정된](scenario-secure-app-authentication-app-service.md) Azure App Service에서 실행되는 웹 애플리케이션

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>프런트 엔드 액세스 권한을 부여하여 Microsoft Graph 호출

웹앱에서 인증 및 권한 부여를 사용하도록 설정했으므로, 웹앱이 Microsoft ID 플랫폼에 등록되고 Azure AD 애플리케이션에서 지원됩니다. 이 단계에서는 사용자를 위해 Microsoft Graph에 액세스할 수 있는 권한을 웹앱에 부여합니다. (기술적으로는, 웹앱의 Azure AD 애플리케이션에 사용자를 위해 Microsoft Graph Azure AD 애플리케이션에 액세스할 수 있는 권한을 부여합니다.)

[Azure Portal](https://portal.azure.com) 메뉴에서 **Azure Active Directory** 를 선택하거나 아무 페이지에서 **Azure Active Directory** 를 검색한 후 선택합니다.

**앱 등록** > **소유 애플리케이션** > **이 디렉터리의 모든 애플리케이션 보기** 를 선택합니다. 웹앱 이름을 선택한 다음, **API 사용 권한** 을 선택합니다.

**사용 권한 추가** 를 선택한 다음, Microsoft API 및 Microsoft Graph를 선택합니다.

**위임된 권한** 을 선택한 다음, 목록에서 **User.Read** 를 선택합니다. **권한 추가** 를 선택합니다.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>사용 가능한 액세스 토큰을 반환하도록 App Service 구성

이제 웹앱에 로그인한 사용자로 Microsoft Graph에 액세스하는 데 필요한 권한이 있습니다. 이 단계에서는 Microsoft Graph에 액세스하는 데 사용할 수 있는 액세스 토큰을 제공하도록 App Service 인증 및 권한 부여를 구성합니다. 이 단계에서는 다운스트림 서비스(Microsoft Graph)에 대한 User.Read 범위를 추가해야 합니다. `https://graph.microsoft.com/User.Read`.

> [!IMPORTANT]
> 사용 가능한 액세스 토큰을 반환하도록 App Service를 구성하지 않으면 코드에서 Microsoft Graph API를 호출할 때 ```CompactToken parsing failed with error code: 80049217``` 오류가 발생합니다.

# <a name="azure-resource-explorer"></a>[Azure Resource Explorer](#tab/azure-resource-explorer)
[Azure Resource Explorer](https://resources.azure.com/)로 이동하고 리소스 트리를 사용하여 웹앱을 찾습니다. 리소스 URL은 `https://resources.azure.com/subscriptions/subscriptionId/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`와 유사해야 합니다.

이제 Azure Resource Explorer가 리소스 트리에서 웹앱이 선택된 상태로 열립니다. 페이지의 위쪽에서 **읽기/쓰기** 를 선택하여 Azure 리소스 편집이 가능하도록 설정합니다.

왼쪽 브라우저에서 **구성** > **authsettingsV2** 로 드릴다운합니다.

**authsettingsV2** 보기에서 **편집** 을 선택합니다. **identityProviders** -> **azureActiveDirectory** 의 **login** 섹션을 찾아 다음 **loginParameters** 설정을 추가합니다. `"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

**PUT** 을 선택하여 설정을 저장합니다. 이 설정이 적용되려면 몇 분 정도 걸릴 수 있습니다. 이제 웹앱이 적절한 액세스 토큰을 사용하여 Microsoft Graph에 액세스하도록 구성되었습니다. 그렇지 않으면 Microsoft Graph에서 압축 토큰의 형식이 올바르지 않다는 오류를 반환합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 App Service Web App REST API를 호출하여 웹앱이 Microsoft Graph를 호출할 수 있도록 인증 구성 설정을 [가져오고](/rest/api/appservice/web-apps/get-auth-settings) [업데이트](/rest/api/appservice/web-apps/update-auth-settings)합니다. 명령 창을 열고 Azure CLI에 로그인합니다.

```azurecli
az login
```

기존 'config/authsettingsv2' 설정을 가져와서 로컬 *authsettings.json* 파일에 저장합니다.

```azurecli
az rest --method GET --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2/list?api-version=2020-06-01' > authsettings.json
```

원하는 텍스트 편집기를 사용하여 authsettings.json 파일을 엽니다. **identityProviders** -> **azureActiveDirectory** 의 **login** 섹션을 찾아 다음 **loginParameters** 설정을 추가합니다. `"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

변경 사항을 *authsettings.json* 파일에 저장하고 로컬 설정을 웹앱에 업로드합니다.

```azurecli
az rest --method PUT --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2?api-version=2020-06-01' --body @./authsettings.json
```
---

## <a name="call-microsoft-graph"></a>Microsoft Graph 호출

이제 웹앱에 필요한 권한이 있으며 Microsoft Graph의 클라이언트 ID도 로그인 매개 변수에 추가됩니다.

# <a name="c"></a>[C#](#tab/programming-language-csharp)
[Microsoft.Identity.Web 라이브러리](https://github.com/AzureAD/microsoft-identity-web/)를 사용하여 웹앱은 Microsoft Graph 인증을 위한 액세스 토큰을 얻습니다. 버전 1.2.0 이상에서 Microsoft.Identity.Web 라이브러리는 App Service 인증/권한 부여 모듈과 통합되며 함께 실행할 수 있습니다. Microsoft.Identity.Web은 웹앱이 App Service에서 호스트되는 것을 감지하고 App Service 인증/권한 부여 모듈에서 액세스 토큰을 가져옵니다. 그런 다음, 액세스 토큰은 Microsoft Graph API를 사용하여 인증된 요청에 전달됩니다.

이 코드를 샘플 애플리케이션의 일부로 보려면 [GitHub의 샘플](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf)을 참조하세요.

> [!NOTE]
> Microsoft.Identity.Web 라이브러리는 기본 인증/권한 부여를 위해 또는 Microsoft Graph를 사용하여 요청을 인증하기 위해 웹앱에 필요하지 않습니다. App Service 인증/권한 부여 모듈만 사용하도록 설정된 상태에서 [다운스트림 API를 안전하게 호출](tutorial-auth-aad.md#call-api-securely-from-server-code)하는 것이 가능합니다.
> 
> 하지만 App Service 인증/권한 부여는 보다 기본적인 인증 시나리오를 위해 설계되었습니다. 더 복잡한 시나리오(예: 사용자 지정 클레임 처리)의 경우 Microsoft.Identity.Web 라이브러리 또는 [Microsoft 인증 라이브러리](../active-directory/develop/msal-overview.md)가 필요합니다. 처음에는 설정 및 구성 작업이 약간 더 많지만 Microsoft.Identity.Web 라이브러리는 App Service 인증/권한 부여 모듈과 함께 실행할 수 있습니다. 나중에 웹앱에서 더 복잡한 시나리오를 처리해야 하는 경우 App Service 인증/권한 부여 모듈을 사용하지 않도록 설정할 수 있으며 Microsoft.Identity.Web은 이미 앱의 일부입니다.

### <a name="install-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

.NET Core 명령줄 인터페이스 또는 Visual Studio의 패키지 관리자 콘솔을 사용하여 프로젝트에 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 및 [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 패키지를 설치합니다.

#### <a name="net-core-command-line"></a>.NET Core 명령줄

명령줄을 열고 프로젝트 파일이 포함된 디렉터리로 전환합니다.

설치 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

#### <a name="package-manager-console"></a>패키지 관리자 콘솔

Visual Studio에서 프로젝트/솔루션을 열고, **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 명령을 사용하여 콘솔을 엽니다.

설치 명령을 실행합니다.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

### <a name="startupcs"></a>Startup.cs

*Startup.cs* 파일에서 ```AddMicrosoftIdentityWebApp``` 메서드는 웹앱에 Microsoft.Identity.Web을 추가합니다. ```AddMicrosoftGraph``` 메서드는 Microsoft Graph 지원을 추가합니다.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* 는 Microsoft.Identity.Web 라이브러리에 대한 구성을 지정합니다. [Azure Portal](https://portal.azure.com)의 포털 메뉴에서 **Azure Active Directory** 를 선택한 다음, **앱 등록** 을 선택합니다. App Service 인증/권한 부여 모듈을 활성화할 때 생성된 앱 등록을 선택합니다. (앱 등록은 웹앱과 이름이 같아야 합니다.) 테넌트 ID와 클라이언트 ID는 앱 등록 개요 페이지에서 찾을 수 있습니다. 도메인 이름은 테넌트의 Azure AD 개요 페이지에서 찾을 수 있습니다.

*Graph* 는 앱에 필요한 Microsoft Graph 엔드포인트와 초기 범위를 지정합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

다음 예제는 로그인한 사용자로 Microsoft Graph를 호출하고 일부 사용자 정보를 가져오는 방법을 보여줍니다. ```GraphServiceClient``` 개체가 컨트롤러에 삽입되고 인증이 Microsoft.Identity.Web 라이브러리에 의해 구성되었습니다.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/programming-language-nodejs)

웹앱은 들어오는 요청 헤더에서 사용자의 액세스 토큰을 가져옵니다. 그러면 Microsoft Graph 클라이언트로 전달되어 `/me` 엔드포인트에 인증된 요청을 만듭니다.

이 코드를 샘플 애플리케이션의 일부로 보려면 [GitHub의 샘플](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf)에서 *graphController.js* 를 참조하세요.

```nodejs
const graphHelper = require('../utils/graphHelper');

// Some code omitted for brevity.

exports.getProfilePage = async(req, res, next) => {

    try {
        const graphClient = graphHelper.getAuthenticatedClient(req.session.protectedResources["graphAPI"].accessToken);

        const profile = await graphClient
            .api('/me')
            .get();

        res.render('profile', { isAuthenticated: req.session.isAuthenticated, profile: profile, appServiceName: appServiceName });   
    } catch (error) {
        next(error);
    }
}
```

Microsoft Graph를 쿼리하려면 [Microsoft Graph JavaScript SDK](https://github.com/microsoftgraph/msgraph-sdk-javascript)를 사용합니다. 이에 대한 코드는 [utils/graphHelper.js](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/blob/main/2-WebApp-graphapi-on-behalf/utils/graphHelper.js)에 있습니다.

```nodejs
const graph = require('@microsoft/microsoft-graph-client');

// Some code omitted for brevity.

getAuthenticatedClient = (accessToken) => {
    // Initialize Graph client
    const client = graph.Client.init({
        // Use the provided access token to authenticate requests
        authProvider: (done) => {
            done(null, accessToken);
        }
    });

    return client;
}
```
---

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하고 웹앱 또는 관련 리소스가 더 이상 필요하지 않으면 [만든 리소스를 정리](scenario-secure-app-clean-up-resources.md)합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 위임된 권한을 웹앱에 부여합니다.
> * 로그인한 사용자를 위해 웹앱에서 Microsoft Graph를 호출합니다.

> [!div class="nextstepaction"]
> [앱 서비스가 앱으로 Microsoft Graph에 액세스](scenario-secure-app-access-microsoft-graph-as-app.md)
