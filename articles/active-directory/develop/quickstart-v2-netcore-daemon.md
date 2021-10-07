---
title: '빠른 시작: 콘솔 앱에서 토큰 가져오기 및 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 .NET Core 샘플 앱이 클라이언트 자격 증명 흐름을 사용하여 토큰을 가져오고 Microsoft Graph를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: d5b463156116b675c2220d1c1b1278d7552eadb0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619309"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>빠른 시작: 콘솔 앱의 ID를 사용하여 토큰 가져오기 및 Microsoft Graph API 호출

이 빠른 시작에서는 .NET Core 콘솔 애플리케이션이 Microsoft Graph API를 호출하고 디렉터리에 [사용자 목록](/graph/api/user-list)을 표시하는 액세스 토큰을 가져올 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 또한 코드 샘플에서는 사용자의 ID 대신 애플리케이션 ID를 사용하여 작업 또는 Windows 서비스를 실행할 수 있는 방법을 보여줍니다. 이 빠른 시작의 샘플 콘솔 애플리케이션은 디먼 애플리케이션이기도 하므로 기밀 클라이언트 애플리케이션입니다.

> [!div renderon="docs"]
> 다음 다이어그램에서는 샘플 앱의 작동 방식을 보여 줍니다.
>
> ![이 빠른 시작에서 생성된 샘플 앱의 작동 방식을 보여 주는 다이어그램](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)가 필요하지만 .NET 5.0 SDK에서도 작동합니다.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>앱 등록 및 다운로드

> [!div renderon="docs" class="sxs-lookup"]
>
> 애플리케이션 빌드를 시작할 수 있는 두 가지 옵션, 즉 자동 구성 또는 수동 구성이 있습니다.
>
> ### <a name="automatic-configuration"></a>자동 구성
>
> 앱을 자동으로 등록하고 구성한 다음, 코드 샘플을 다운로드하려면 다음 단계를 수행합니다.
>
> 1. <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">앱 등록을 위한 Azure Portal 페이지</a>로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록** 을 선택합니다.
> 1. 지침에 따라 한 번의 클릭으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="manual-configuration"></a>수동 구성
>
> 애플리케이션 및 코드 샘플을 수동으로 구성하려면 다음 절차를 사용합니다.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</span></a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. **이름** 에 대해 애플리케이션 이름을 입력합니다. 예를 들어 **Daemon-console** 을 입력합니다. 이 이름은 앱의 사용자에게 표시되며 나중에 변경할 수 있습니다.
> 1. **등록** 을 선택하여 애플리케이션을 만듭니다.
> 1. **관리** 에서 **인증서 및 암호** 를 선택합니다.
> 1. **클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택하고 이름을 입력한 다음, **추가** 를 선택합니다. 이후 단계에서 사용할 수 있도록 안전한 위치에 비밀 값을 기록합니다.
> 1. **관리** 에서 **API 권한** > **권한 추가** 를 선택합니다. **Microsoft Graph** 를 선택합니다.
> 1. **애플리케이션 권한** 을 선택합니다.
> 1. **사용자** 노드 아래에서 **User.Read.All** 을 선택한 다음, **권한 추가** 를 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>빠른 시작 앱 다운로드 및 구성
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 클라이언트 암호를 만들고 Graph API의 **User.Read.All** 애플리케이션 권한을 추가합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-netcore-daemon/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-your-visual-studio-project"></a>2단계: Visual Studio 프로젝트 다운로드

> [!div renderon="docs"]
> [Visual Studio 프로젝트 다운로드](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Visual Studio 또는 Mac용 Visual Studio에서 제공된 프로젝트를 실행할 수 있습니다.


> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019를 사용하여 프로젝트를 실행합니다.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3단계: Visual Studio 프로젝트 구성
>
> 1. 디스크의 루트와 가까운 로컬 폴더에 .zip 파일의 압축을 풉니다. 예를 들어 파일의 압축을 *C:\Azure-Samples* 에 풉니다.
>
>    Windows의 경로 길이 제한으로 인해 발생하는 오류를 방지하려면 보관 파일의 압축을 드라이브 루트에 가까운 디렉터리에 푸는 것이 좋습니다.
>
> 1. Visual Studio: *1-Call-MSGraph\daemon-console.sln* 에서 솔루션을 엽니다(선택 사항).
> 1. *appsettings.json* 에서 `Tenant`, `ClientId` 및 `ClientSecret`의 값을 바꿉니다.
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    이 코드의 경우 다음과 같습니다.
>    - `Enter_the_Application_Id_Here`는 등록한 애플리케이션의 애플리케이션(클라이언트) ID입니다.
        애플리케이션(클라이언트) ID 및 디렉터리(테넌트) ID 값을 찾으려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.
>    - `Enter_the_Tenant_Id_Here`를 테넌트 ID 또는 테넌트 이름으로 바꿉니다(예: `contoso.microsoft.com`).
>    - `Enter_the_Client_Secret_Here`를 1단계에서 만든 클라이언트 암호로 바꿉니다.
    새 키를 생성하려면 **인증서 및 비밀** 페이지로 이동합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3단계: 관리자 동의

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4단계: 관리자 동의

이 시점에서 애플리케이션을 실행하려고 시도하면 *HTTP 403 - 사용할 수 없음* 오류: "권한이 부족하여 작업을 완료할 수 없습니다"라는 메시지가 표시됩니다. 모든 앱 전용 권한은 디렉터리 전역 관리자가 애플리케이션에 동의해야 하므로 이 오류가 발생합니다. 역할에 따라 다음 옵션 중 하나를 선택합니다.

##### <a name="global-tenant-administrator"></a>글로벌 테넌트 관리자

> [!div renderon="docs"]
> 글로벌 테넌트 관리자인 경우 Azure Portal에서 **엔터프라이즈 애플리케이션** 으로 이동합니다. 앱 등록을 선택하고, 왼쪽 창의 **보안** 섹션에서 **사용 권한** 을 선택합니다. **{테넌트 이름}에 대한 관리자 동의 부여** 레이블이 지정된 큰 단추를 선택합니다(여기서 **{테넌트 이름}** 은 디렉터리 이름).

> [!div renderon="portal" class="sxs-lookup"]
> 전역 관리자인 경우 **API 사용 권한** 페이지로 이동하여 **Enter_the_Tenant_Name_Here에 대한 관리자 동의 부여** 를 선택합니다.
> > [!div id="apipermissionspage"]
> > [API 사용 권한 페이지로 이동]()

##### <a name="standard-user"></a>표준 사용자

테넌트의 표준 사용자인 경우 전역 관리자에게 애플리케이션에 대한 관리자 동의를 부여하도록 요청합니다. 이렇게 하려면 관리자에게 다음 URL을 제공합니다.

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> 해당 URL에서 다음을 수행합니다.
> * `Enter_the_Tenant_Id_Here`를 테넌트 ID 또는 테넌트 이름으로 바꿉니다(예: `contoso.microsoft.com`).
> * `Enter_the_Application_Id_Here`는 등록한 애플리케이션의 애플리케이션(클라이언트) ID입니다.

위의 URL을 사용하여 앱에 동의한 후 "AADSTS50011: 애플리케이션에 대해 등록된 회신 주소가 없습니다" 오류가 발생할 수 있습니다. 이 오류는 애플리케이션과 URL의 리디렉션 URI가 없기 때문에 발생합니다. 무시해도 됩니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5단계: 애플리케이션 실행

Visual Studio 또는 Mac용 Visual Studio를 사용하는 경우 **F5** 키를 눌러 애플리케이션을 시작합니다. 그렇지 않으면 명령 프롬프트, 콘솔 또는 터미널을 통해 애플리케이션을 실행합니다.

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
이 코드의 경우 다음과 같습니다.
* `{ProjectFolder}`는 .zip 파일의 압축을 푼 폴더입니다. 예제는 `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`입니다.

그 결과로 Azure Active Directory에 사용자 목록이 표시됩니다.

이 빠른 시작 애플리케이션에서는 클라이언트 암호를 사용하여 자체를 기밀 클라이언트로 식별합니다. 클라이언트 암호는 프로젝트 파일에 일반 텍스트 파일로 추가됩니다. 보안을 위해 애플리케이션을 프로덕션 애플리케이션으로 사용하는 방안을 고려하기 전에 클라이언트 암호 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하는 방법에 대한 자세한 내용은 이 샘플에 대한 GitHub 리포지토리의 [지침](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates)을 참조하세요.

## <a name="more-information"></a>자세한 정보
이 섹션에서는 로그인 사용자에 필요한 코드에 대한 개요를 제공합니다. 이 개요는 코드의 작동 방식, 기본 인수의 개념 및 로그인을 기존 .NET Core 애플리케이션에 추가하는 방법을 이해하는 데 도움이 됩니다.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>샘플 작동 방법
>
> ![이 빠른 시작에서 생성된 샘플 앱의 작동 방식을 보여 주는 다이어그램](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft 인증 라이브러리([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) 패키지의 MSAL)는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API에 액세스하기 위한 토큰을 요청하는 데 사용되는 라이브러리입니다. 이 빠른 시작에서는 위임된 권한 대신 애플리케이션 소유의 ID를 사용하여 토큰을 요청합니다. 이 예제의 인증 흐름을 [클라이언트 자격 증명 OAuth 흐름](v2-oauth2-client-creds-grant-flow.md)이라고 합니다. 클라이언트 자격 증명 흐름과 함께 MSAL.NET을 사용하는 방법에 대한 자세한 내용은 [이 문서](https://aka.ms/msal-net-client-credentials)를 참조하세요.

 Visual Studio 패키지 관리자 콘솔에서 다음 명령을 실행하여 MSAL.NET을 설치할 수 있습니다.

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL 초기화

다음 코드를 추가하여 MSAL에 대한 참조를 추가할 수 있습니다.

```csharp
using Microsoft.Identity.Client;
```

그런 다음, 아래 코드를 사용하여 MSAL을 초기화합니다.

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | 요소 | 설명 |
 |---------|---------|
 | `config.ClientSecret` | Azure Portal에서 애플리케이션에 대해 생성된 클라이언트 암호입니다. |
 | `config.ClientId` | Azure Portal에 등록된 애플리케이션의 애플리케이션(클라이언트) ID입니다. 이 값은 Azure Portal에 있는 앱의 **개요** 페이지에서 찾을 수 있습니다. |
 | `config.Authority`    | (선택 사항) 사용자가 인증하는 STS(보안 토큰 서비스) 엔드포인트입니다. 일반적으로 퍼블릭 클라우드의 `https://login.microsoftonline.com/{tenant}`이며, 여기서 `{tenant}`는 테넌트 이름 또는 테넌트 ID입니다.|

자세한 내용은 [`ConfidentialClientApplication` 참조 설명서](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)에서 확인할 수 있습니다.

### <a name="requesting-tokens"></a>토큰 요청

앱의 ID를 사용하여 토큰을 요청하려면 `AcquireTokenForClient` 메서드를 사용합니다.

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|요소| 설명 |
|---------|---------|
| `scopes` | 요청된 범위를 포함합니다. 기밀 클라이언트의 경우 이 값은 `{Application ID URI}/.default`와 비슷한 형식을 사용해야 합니다. 이 형식은 요청된 범위가 Azure Portal에서 설정한 앱 개체에서 정적으로 정의된 범위임을 나타냅니다. Microsoft Graph에서 `{Application ID URI}`는 `https://graph.microsoft.com`을 가리킵니다. 사용자 지정 웹 API의 경우 `{Application ID URI}`는 Azure Portal의 **애플리케이션 등록(미리 보기)**  > **API 노출** 에서 정의합니다. |

자세한 내용은 [`AcquireTokenForClient` 참조 설명서](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)에서 확인할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

디먼 애플리케이션에 대해 자세히 알아보려면 시나리오 개요를 참조하세요.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)
