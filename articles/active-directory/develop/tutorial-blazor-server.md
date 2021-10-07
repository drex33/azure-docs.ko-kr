---
title: 자습서 - 인증을 위해 Microsoft ID 플랫폼을 사용하는 Blazor Server 앱 만들기 | Azure
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 Blazor Server 앱에서 Microsoft ID 플랫폼을 사용하여 인증을 설정합니다.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: f7d62b6fa3523accbeba9a083c74330ff344e2ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786373"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>자습서: 인증을 위해 Microsoft ID 플랫폼을 사용하는 Blazor Server 앱 만들기

이 자습서에서는 Microsoft ID 플랫폼을 사용하고, Azure AD(Azure Active Directory)에 앱을 등록하여 사용자를 로그인하고, Microsoft Graph에서 데이터를 가져오는 Blazor Server 앱을 빌드합니다.

[Blazor WASM에 대한 자습서](tutorial-blazor-webassembly.md)도 있습니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 인증을 위해 Azure AD(Azure Active Directory)를 사용하도록 구성된 새 Blazor Server 앱 만들기
> * Microsoft.Identity.Web을 사용한 인증 및 권한 부여 처리
> * 보호된 웹 API, Microsoft Graph에서 데이터를 검색

## <a name="prerequisites"></a>필수 구성 요소

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- 앱을 등록할 수 있는 Azure AD 테넌트. Azure AD 테넌트에 대한 액세스 권한이 없는 경우 [Microsoft 365 개발자 프로그램](https://developer.microsoft.com/microsoft-365/dev-program)에 등록하거나 [Azure 체험 계정](https://azure.microsoft.com/free)을 만들어서 얻을 수 있습니다.

## <a name="register-the-app-in-the-azure-portal"></a>Azure Portal에서 앱 등록

인증을 위해 Azure AD(Azure Active Directory)를 사용하는 모든 앱은 Azure AD에 등록해야 합니다. 다음과 같은 추가 기능이 포함된 [애플리케이션 등록](quickstart-register-app.md)에 나온 지침을 따르세요.

- **지원되는 계정 유형** 의 경우 **이 조직 디렉터리 계정의 계정만** 을 선택합니다.
- **리디렉션 URI** 드롭다운 세트를 **웹** 으로 두고 `https://localhost:5001/signin-oidc`를 입력합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 다른 포트에서 앱을 사용할 수 있는 경우 `5001` 대신 해당 포트 번호를 지정합니다.

**관리** 에서 **인증** > **암시적 허용 및 하이브리드 흐름** 을 선택합니다. **ID 토큰** 을 선택한 다음, **저장** 을 선택합니다.

마지막으로 앱이 보호된 API(이 경우 Microsoft Graph)를 호출하기 때문에 해당 API를 호출하는 액세스 토큰을 요청할 때 해당 ID를 확인하기 위해 클라이언트 암호가 필요합니다.

1. 동일한 앱 등록 내 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
2. 만료되지 않는 **새 클라이언트 암호** 를 만듭니다.
3. 다음 단계에서 사용하기 위해 비밀의 **값** 을 기록해 둡니다. 이 창에서 다른 곳으로 이동하면 다시 액세스할 수 없습니다. 그러나 필요에 따라 다시 만들 수 있습니다.

## <a name="create-the-app-using-the-net-cli"></a>.NET CLI를 사용하여 앱 만들기

다음 명령을 실행하여 이 자습서에서 사용할 Microsoft.Identity.Web의 템플릿을 다운로드합니다.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates
```

그런 다음, 다음 명령을 실행하여 애플리케이션을 만듭니다. 명령의 자리 표시자를 앱의 개요 페이지에 있는 적절한 정보로 바꾸고 명령 셸에서 명령을 실행합니다. `-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}" --domain "{DOMAIN}"
```

| 자리표시자   | Azure Portal 이름       | 예제                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | 애플리케이션(클라이언트) ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 디렉터리(테넌트) ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |
| `{DOMAIN}`    | 기본 도메인          | `tenantname.onmicrosoft.com`           |

이제 편집기에서 새 Blazor 앱으로 이동하고 클라이언트 암호를 *appsettings.json* 파일에 추가하여 “secret-from-app-registration” 텍스트를 바꿉니다.

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>앱 테스트

이제 앱을 빌드 및 실행할 수 있습니다. 이 템플릿 앱을 실행할 때 --framework를 사용하여 실행할 프레임워크를 지정해야 합니다. 이 자습서에서는 .NET Core 3.1 SDK를 사용합니다.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

브라우저에서 `https://localhost:5001`로 이동하고 Azure AD 사용자 계정으로 로그인하여 실행 중인 앱을 확인합니다.

## <a name="retrieving-data-from-microsoft-graph"></a>Microsoft Graph에서 데이터 검색

[Microsoft Graph](/graph/overview)는 사용자의 Microsoft 365 데이터에 대한 액세스를 제공하는 다양한 API를 제공합니다. Microsoft Graph는 Microsoft ID 플랫폼에서 발급한 토큰을 직접 지원하므로, Microsoft ID 플랫폼을 앱에 대한 ID 공급자로 사용하면 이 정보에 손쉽게 액세스할 수 있습니다. 이 섹션에서는 애플리케이션의 "데이터 가져오기" 페이지에 로그인한 사용자의 이메일을 표시하는 코드를 추가합니다.

시작하기 전에 필요한 권한을 변경하고 현재 토큰은 작동하지 않으므로 앱에서 로그아웃합니다. 아직 로그아웃하지 않은 경우 다음 코드를 업데이트하기 전에 앱을 다시 실행하고 **로그아웃** 을 선택합니다.

이제 앱의 등록과 코드를 업데이트하여 사용자의 이메일을 끌어오고 앱 내에 메시지를 표시합니다. 이를 위해서는 먼저 Azure AD에서 앱 등록 권한을 확장하여 이메일 데이터에 액세스할 수 있도록 합니다. 그런 다음, 코드를 Blazor 앱에 추가하여 페이지 중 하나에서 이 데이터를 검색하여 표시합니다.

1. Azure Portal의 **앱 등록** 에서 앱을 선택합니다.
1. **관리** 아래에서 **API 권한** 을 선택합니다.
1. **권한 추가** > **Microsoft Graph** 를 선택합니다.
1. **위임된 권한** 을 선택한 다음, **Mail.Read** 사용 권한을 검색하여 선택합니다.
1. **권한 추가** 를 선택합니다.

*appsettings.json* 파일에서 올바른 권한을 사용하여 적절한 토큰을 페치하도록 코드를 업데이트합니다. “DownstreamAPI”에서 “mail.read”를 “user.read” 범위 뒤에 추가합니다. 이는 앱에서 액세스를 요청하는 범위(또는 권한)를 지정합니다.

```json
"Scopes": "user.read mail.read"
```

다음으로, *FetchData.razor* 파일의 코드를 업데이트하여 기본(임의) 날씨 정보 대신 이메일 데이터를 검색합니다. 해당 파일의 코드를 다음으로 바꿉니다.

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

        if (dataRequest.IsSuccessStatusCode)
        {
            var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
            userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
        }

        var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

        if (mailRequest.IsSuccessStatusCode)
        {
            var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
            var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

            foreach (var m in messagesArray)
            {
                var message = new MailMessage();
                message.Subject = m.GetProperty("subject").GetString();
                message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                messages.Add(message);
            }
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

앱을 시작합니다. 새로 추가된 사용 권한을 요청하는 메시지가 표시됩니다. 이를 통해 모든 것이 예상 대로 작동하고 있음을 알 수 있습니다. 이제 기본 사용자 프로필 데이터 외에도 앱이 이메일 데이터에 대한 액세스를 요청합니다.

동의를 부여한 후 “데이터 페치” 페이지로 이동하여 일부 이메일을 읽습니다.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="최종 앱 스크린샷 Hello Nicholas라는 제목이 있고, Nicholas에 속한 이메일의 목록이 표시됩니다.":::

## <a name="next-steps"></a>다음 단계

여러 부분으로 구성된 시나리오 시리즈에서 사용자를 로그인하는 웹앱 빌드를 호출하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [시나리오: 사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
