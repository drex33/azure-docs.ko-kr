---
title: 몰입형 리더 C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Applied AI Services
description: 이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 API 기능을 추가합니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1865a8b4dd4ac6c263ad80d726c331060bda0e54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786676"
---
[몰입형 리더](https://www.onenote.com/learningtools)는 새 리더, 언어 학습자 및 학습 차이(예: 난독증)가 있는 사람들을 위해 독해력을 향상시키기 위해 입증된 기술을 구현하는 포괄적으로 설계된 도구입니다. 애플리케이션에서 몰입형 리더를 사용하여 텍스트를 분리하여 포커스를 개선하고, 자주 사용되는 단어에 대한 그림을 표시하고, 품사를 강조 표시하고, 선택한 텍스트를 소리내어 읽고, 단어와 구문을 실시간으로 번역하는 등의 작업을 수행할 수 있습니다.

이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 클라이언트 라이브러리를 사용하여 몰입형 리더를 통합합니다. 이 빠른 시작의 전체 작동 샘플은 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](../../how-to-create-immersive-reader.md)에 따라 설정하세요. 샘플 프로젝트 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.

## <a name="create-a-web-app-project"></a>웹앱 프로젝트 만들기

기본 제공되는 Model-View-Controller 및 ASP.NET Core 2.1과 함께 ASP.NET Core 웹 애플리케이션 템플릿을 사용하여 Visual Studio에서 새 프로젝트를 만듭니다. 프로젝트 이름을 “QuickstartSampleWebApp”으로 지정합니다.

![새 프로젝트 - C#](../../media/quickstart-csharp/1-createproject.png)

![새 프로젝트 구성 - C#](../../media/quickstart-csharp/2-configureproject.png)

![새 ASP.NET Core 웹 애플리케이션 - C#](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>인증 설정

### <a name="configure-authentication-values"></a>인증 값 구성

_Solution Explorer_ 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 비밀 관리** 를 선택합니다. 그러면 _secrets.json_ 이라는 파일이 열립니다. 이 파일은 소스 제어에 체크 인되지 않습니다. [여기](/aspnet/core/security/app-secrets?tabs=windows)를 참조하세요. 몰입형 판독기 리소스를 만들 때 지정된 값을 제공하여 _secrets.json_ 의 콘텐츠를 다음으로 바꿉니다.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Active Directory NuGet 패키지 설치

다음 코드는 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 패키지의 개체를 사용하므로, 프로젝트에서 해당 패키지에 대한 참조를 추가해야 합니다.

**도구 -> NuGet 패키지 관리자 - > 패키지 관리자 콘솔** 에서 NuGet 패키지 관리자 콘솔을 열고 다음 명령을 실행합니다.

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>토큰을 획득하도록 컨트롤러 업데이트 

_Controllers\HomeController.cs_ 를 열고 파일 맨 위에 있는 _using_ 문을 뒤에 다음 코드를 추가합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

이제 _secrets.json_ 에서 Azure AD 값을 가져오도록 컨트롤러를 구성합니다. _HomeController_ 클래스의 맨 위에서 ```public class HomeController : Controller {``` 뒤에 다음 코드를 추가합니다.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>샘플 콘텐츠 추가
먼저 _Views\Shared\Layout.cshtml_ 을 엽니다. ```</head>``` 줄 앞에 다음 코드를 추가합니다.

```html
@RenderSection("Styles", required: false)
```

이제 이 웹앱에 샘플 콘텐츠를 추가합니다. _Views\Home\Index.cshtml_ 을 열고 자동으로 생성된 모든 코드를 다음 샘플로 바꿉니다.

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

모든 텍스트에는 텍스트의 언어를 설명하는 **lang** 특성이 있습니다. 이 특성은 몰입형 판독기가 관련 언어와 문법 기능을 제공하는 데 도움이 됩니다.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>몰입형 리더 시작을 처리하는 JavaScript 추가

몰입형 판독기 라이브러리는 몰입형 판독기를 시작하고 몰입형 판독기 단추를 렌더링하는 등의 기능을 제공합니다. [여기](../../reference.md)를 참조하세요.

_Views\Home\Index.cshtml_ 의 맨 아래에 다음 코드를 추가합니다.

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

메뉴 모음에서 **디버그 > 디버깅 시작** 을 선택하거나 **F5** 키를 눌러 애플리케이션을 시작합니다.

브라우저에서 다음이 표시됩니다.

![샘플 앱 - C#](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>몰입형 판독기 시작

“몰입형 판독기” 단추를 클릭하면 페이지의 콘텐츠와 함께 몰입형 판독기가 시작됩니다.

![몰입형 리더 - C#](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [리소스 만들기 및 AAD 구성](../../how-to-create-immersive-reader.md)