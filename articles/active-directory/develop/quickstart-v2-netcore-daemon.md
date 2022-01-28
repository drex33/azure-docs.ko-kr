---
title: '빠른 시작: 콘솔 앱에서 토큰 가져오기 및 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 .NET Core 샘플 앱이 클라이언트 자격 증명 흐름을 사용하여 토큰을 가져오고 Microsoft Graph를 호출하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: 417fb1f2c11822b8c475324c15c27a24a1ff49f0
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803934"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>빠른 시작: 콘솔 앱의 ID를 사용하여 토큰 가져오기 및 Microsoft Graph API 호출

이 빠른 시작에서는 .NET Core 콘솔 애플리케이션이 Microsoft Graph API를 호출하고 디렉터리에 [사용자 목록](/graph/api/user-list)을 표시하는 액세스 토큰을 가져올 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 또한 코드 샘플에서는 사용자의 ID 대신 애플리케이션 ID를 사용하여 작업 또는 Windows 서비스를 실행할 수 있는 방법을 보여줍니다. 이 빠른 시작의 샘플 콘솔 애플리케이션은 디먼 애플리케이션이기도 하므로 기밀 클라이언트 애플리케이션입니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)가 필요하지만 .NET 5.0 SDK에서도 작동합니다.

> [!div class="sxs-lookup"]
### <a name="download-and-configure-your-quickstart-app"></a>빠른 시작 앱 다운로드 및 구성

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
이 빠른 시작의 코드 샘플이 작동하려면 클라이언트 암호를 만들고 Graph API의 **User.Read.All** 애플리케이션 권한을 추가합니다.
> [!div class="nextstepaction"]
> [이러한 변경 내용 적용]()

> [!div class="alert alert-info"]
> ![이미 구성됨](media/quickstart-v2-netcore-daemon/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-your-visual-studio-project"></a>2단계: Visual Studio 프로젝트 다운로드

> [!div class="sxs-lookup"]
> Visual Studio 2019를 사용하여 프로젝트를 실행합니다.
> [!div class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>3단계: 관리자 동의

이 시점에서 애플리케이션을 실행하려고 시도하면 *HTTP 403 - 사용할 수 없음* 오류: "권한이 부족하여 작업을 완료할 수 없습니다"라는 메시지가 표시됩니다. 모든 앱 전용 권한은 디렉터리 전역 관리자가 애플리케이션에 동의해야 하므로 이 오류가 발생합니다. 역할에 따라 다음 옵션 중 하나를 선택합니다.

##### <a name="global-tenant-administrator"></a>글로벌 테넌트 관리자

전역 관리자인 경우 **API 사용 권한** 페이지로 이동하여 **Enter_the_Tenant_Name_Here에 대한 관리자 동의 부여** 를 선택합니다.
> [!div id="apipermissionspage"]
> [API 사용 권한 페이지로 이동]()

##### <a name="standard-user"></a>표준 사용자

테넌트의 표준 사용자인 경우 전역 관리자에게 애플리케이션에 대한 관리자 동의를 부여하도록 요청합니다. 이렇게 하려면 관리자에게 다음 URL을 제공합니다.

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

위의 URL을 사용하여 앱에 동의한 후 "AADSTS50011: 애플리케이션에 대해 등록된 회신 주소가 없습니다" 오류가 발생할 수 있습니다. 이 오류는 애플리케이션과 URL의 리디렉션 URI가 없기 때문에 발생합니다. 무시해도 됩니다.

#### <a name="step-4-run-the-application"></a>4단계: 애플리케이션 실행

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

> [!div class="sxs-lookup"]
### <a name="how-the-sample-works"></a>샘플 작동 방법

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식을 보여 주는 다이어그램](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

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

 | 요소 | Description |
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

|요소| Description |
|---------|---------|
| `scopes` | 요청된 범위를 포함합니다. 기밀 클라이언트의 경우 이 값은 `{Application ID URI}/.default`와 비슷한 형식을 사용해야 합니다. 이 형식은 요청된 범위가 Azure Portal에서 설정한 앱 개체에서 정적으로 정의된 범위임을 나타냅니다. Microsoft Graph에서 `{Application ID URI}`는 `https://graph.microsoft.com`을 가리킵니다. 사용자 지정 웹 API의 경우 `{Application ID URI}`는 Azure Portal의 **애플리케이션 등록(미리 보기)**  > **API 노출** 에서 정의합니다. |

자세한 내용은 [`AcquireTokenForClient` 참조 설명서](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)에서 확인할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

디먼 애플리케이션에 대해 자세히 알아보려면 시나리오 개요를 참조하세요.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 디먼 애플리케이션](scenario-daemon-overview.md)
