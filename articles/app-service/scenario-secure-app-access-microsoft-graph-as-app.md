---
title: 자습서 - 웹앱이 Microsoft Graph에 앱으로 액세스| Azure
description: 이 자습서에서는 관리 ID를 사용하여 Microsoft Graph의 데이터에 액세스하는 방법을 알아봅니다.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 777355c3bff17c2d9a156eb6b330f15e2551943d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471006"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>자습서: 보안 앱에서 앱으로 Microsoft Graph에 액세스

Azure App Service에서 실행되는 웹앱에서 Microsoft Graph에 액세스하는 방법에 대해 알아봅니다.

:::image type="content" alt-text="Microsoft Graph 액세스를 보여주는 다이어그램." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

웹앱용 Microsoft Graph를 호출하려고 합니다. 웹앱에 데이터 액세스 권한을 부여하는 안전한 방법은 [시스템이 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하는 것입니다. Azure Active Directory의 관리 ID를 사용하면 앱 자격 증명 없이 App Service가 RBAC(역할 기반 액세스 제어)를 통해 리소스에 액세스할 수 있습니다. 웹앱에 관리 ID를 할당한 후에는 인증서를 만들고 배포하는 작업을 Azure가 처리합니다. 사용자는 비밀 또는 앱 자격 증명 관리에 대해 신경 쓸 필요가 없습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 웹앱에서 시스템이 할당한 관리 ID를 만듭니다.
> * 관리 ID에 Microsoft Graph API 권한을 추가합니다.
> * 관리 ID를 사용하여 웹앱에서 Microsoft Graph를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [App Service 인증/권한 부여 모듈을 사용하도록 설정된](scenario-secure-app-authentication-app-service.md) Azure App Service에서 실행되는 웹 애플리케이션

## <a name="enable-managed-identity-on-app"></a>앱에서 관리 ID 사용

Visual Studio를 통해 웹앱을 만들고 게시하는 경우 앱에서 관리 ID를 사용하도록 자동으로 설정되었습니다. 앱 서비스의 왼쪽 창에서 **ID** 를 선택한 다음, **시스템 할당** 을 선택합니다. **상태** 가 **켜기** 로 설정되어 있는지 확인합니다. 설정되지 않았으면 **저장** 을 선택한 다음, **예** 를 선택하여 시스템이 할당한 관리 ID를 사용하도록 설정합니다. 관리 ID를 사용하도록 설정하면 상태가 **켜기** 로 설정되고 개체 ID를 사용할 수 있습니다.

다음 단계에서 필요한 **개체 ID** 값을 기록해 둡니다.

:::image type="content" alt-text="시스템 할당 ID를 보여주는 스크린샷." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Microsoft Graph에 액세스

Microsoft Graph에 액세스할 때 수행하려는 작업에 대한 적절한 권한이 관리 ID에 있어야 합니다. 현재는 Azure Portal을 통해 이러한 권한을 할당할 수 있는 옵션은 없습니다. 다음 스크립트는 관리 ID 서비스 주체 개체에 요청된 Microsoft Graph API 권한을 추가합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

스크립트를 실행한 후에는 [Azure Portal](https://portal.azure.com)에서 요청된 API 권한이 관리 ID에 할당되었는지 확인할 수 있습니다.

**Azure Active Directory** 로 이동한 다음, **엔터프라이즈 애플리케이션** 을 선택합니다. 이 창에는 테넌트의 모든 서비스 주체가 표시됩니다. **모든 애플리케이션** 에서 관리 ID의 서비스 주체를 선택합니다. 

이 자습서를 수행하는 경우 동일한 표시 이름(예: SecureWebApp2020094113531)을 가진 두 개의 서비스 주체가 있습니다. **홈페이지 URL** 이 있는 서비스 주체는 테넌트의 웹앱을 나타냅니다. **홈페이지 URL** 이 없는 서비스 주체는 웹앱의 시스템이 할당한 관리 ID를 나타냅니다. 관리 ID의 **개체 ID** 값은 이전에 만든 관리 ID의 개체 ID와 일치합니다.

관리 ID의 서비스 주체를 선택합니다.

:::image type="content" alt-text="모든 애플리케이션 옵션을 보여주는 스크린샷." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

**개요** 에서 **권한** 을 선택하면 Microsoft Graph에 대한 추가 권한이 표시됩니다.

:::image type="content" alt-text="권한 창을 보여주는 스크린샷." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph"></a>Microsoft Graph 호출

# <a name="c"></a>[C#](#tab/programming-language-csharp)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스는 Microsoft Graph에 대한 요청에 권한을 부여하기 위해 코드의 토큰 자격 증명을 가져오는 데 사용됩니다. 관리 ID를 사용하여 토큰을 가져오고 서비스 클라이언트에 연결하는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 다음 코드 예제에서는 인증된 토큰 자격 증명을 가져와서 서비스 클라이언트 개체를 만드는 데 사용합니다. 이 개체는 그룹의 사용자를 가져옵니다.

이 코드를 샘플 애플리케이션의 일부로 보려면 [GitHub의 샘플](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)을 참조하세요.

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Microsoft.Identity.Web.MicrosoftGraph 클라이언트 라이브러리 패키지 설치

.NET Core 명령줄 인터페이스 또는 Visual Studio의 패키지 관리자 콘솔을 사용하여 프로젝트에 [Microsoft.Identity.Web.MicrosoftGraph NuGet 패키지](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph)를 설치합니다.

#### <a name="net-core-command-line"></a>.NET Core 명령줄

명령줄을 열고 프로젝트 파일이 포함된 디렉터리로 전환합니다.

설치 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

#### <a name="package-manager-console"></a>패키지 관리자 콘솔

Visual Studio에서 프로젝트/솔루션을 열고, **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 명령을 사용하여 콘솔을 엽니다.

설치 명령을 실행합니다.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

### <a name="example"></a>예

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

# <a name="nodejs"></a>[Node.JS](#tab/programming-language-nodejs)

[@azure/identity](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md) 패키지의 `DefaultAzureCredential` 클래스는 Azure Storage에 대한 요청에 권한을 부여하기 위해 코드의 토큰 자격 증명을 가져오는 데 사용됩니다. 관리 ID를 사용하여 토큰을 가져오고 서비스 클라이언트에 연결하는 `DefaultAzureCredential` 클래스의 인스턴스를 만듭니다. 다음 코드 예제에서는 인증된 토큰 자격 증명을 가져와서 서비스 클라이언트 개체를 만드는 데 사용합니다. 이 개체는 그룹의 사용자를 가져옵니다.

이 코드를 샘플 애플리케이션의 일부로 보려면 [GitHub의 샘플](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)을 참조하세요.

### <a name="example"></a>예제

```nodejs
const graphHelper = require('../utils/graphHelper');
const { DefaultAzureCredential } = require("@azure/identity");

exports.getUsersPage = async(req, res, next) => {

    const defaultAzureCredential = new DefaultAzureCredential();
    
    try {
        const tokenResponse = await defaultAzureCredential.getToken("https://graph.microsoft.com/.default");

        const graphClient = graphHelper.getAuthenticatedClient(tokenResponse.token);

        const users = await graphClient
            .api('/users')
            .get();

        res.render('users', { user: req.session.user, users: users });   
    } catch (error) {
        next(error);
    }
}
```

Microsoft Graph를 쿼리하기 위해 샘플에서는 [Microsoft Graph JavaScript SDK](https://github.com/microsoftgraph/msgraph-sdk-javascript)를 사용합니다. 이에 대한 코드는 전체 샘플의 [utils/graphHelper.js](https://github.com/Azure-Samples/ms-identity-easyauth-nodejs-storage-graphapi/blob/main/3-WebApp-graphapi-managed-identity/controllers/graphController.js)에 있습니다.

```nodejs
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
> * 웹앱에서 시스템이 할당한 관리 ID를 만듭니다.
> * 관리 ID에 Microsoft Graph API 권한을 추가합니다.
> * 관리 ID를 사용하여 웹앱에서 Microsoft Graph를 호출합니다.

[.NET Core 앱](tutorial-dotnetcore-sqldb-app.md), [Python 앱](tutorial-python-postgresql-app.md), [Java 앱](tutorial-java-spring-cosmosdb.md) 또는 [Node.js 앱](tutorial-nodejs-mongodb-app.md)을 데이터베이스에 연결하는 방법을 알아봅니다.
