---
title: Azure SignalR Service Serverless 빠른 시작 - C#
description: C#을 사용하여 Azure SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 3a3fa958ac6ad1cb440f30b5c680ae3f9139d29a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444926"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>빠른 시작: C를 사용하여 SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱 만들기\#

Azure SignalR Service를 사용하면 애플리케이션에 실시간 기능을 쉽게 추가할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 빠른 시작에서는 C#을 사용하여 SignalR Service와 Azure Functions로 서버리스 애플리케이션을 빌드하고 클라이언트에 메시지를 브로드캐스트하는 방법을 알아봅니다.

> [!NOTE]
> 문서에 언급된 모든 코드는 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp)에서 얻을 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Visual Studio Code가 아직 설치되어 있지 않다면 무료로 다운로드하여 사용할 수 있습니다(https://code.visualstudio.com/Download).

[Azure Functions Core Tools)](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2)를 사용하여 명령줄(macOS, Windows 또는 Linux)에서 이 가이드를 실행할 수도 있습니다. 또한 [.NET Core SDK](https://dotnet.microsoft.com/download)와 선호하는 코드 편집기를 실행할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/dotnet).

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>Azure에 로그인하고 SignalR Service 인스턴스 만들기

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qscsharp).

## <a name="setup-and-run-the-azure-function-locally"></a>Azure Function을 로컬로 설정 및 실행

1. Azure Function Core Tools가 설치되어 있는지 확인합니다. 그리고 빈 디렉터리를 만들고 명령줄을 사용하여 디렉터리로 이동합니다.

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. 프로젝트를 초기화한 후. 이름이 *Function.cs* 인 새 파일을 만듭니다. *Function.cs* 에 다음 코드를 추가합니다.
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current star count of https://github.com/Azure/azure-signalr is: {result.StarCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StarCount { get; set; }
            }
        }
    }
    ```
    이 코드에는 세 가지 함수가 있습니다. `Index`는 웹사이트를 클라이언트로 가져오는 데 사용됩니다. `Negotiate`는 클라이언트가 액세스 토큰을 가져오는 데 사용됩니다. `Broadcast`는 GitHub에서 별 개수를 주기적으로 가져오고 메시지를 모든 클라이언트에 브로드캐스트합니다.

3. 이 샘플의 클라이언트 인터페이스는 웹 페이지입니다. `GetHomePage` 함수의 `content/index.html`에서 HTML 콘텐츠를 읽은 것으로 간주하고 프로젝트 루트 폴더 아래의 `content` 디렉터리에 새 파일 `index.html`을 만듭니다. 그리고 다음 내용을 복사합니다.
    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```

4. `*.csproj`를 업데이트하여 빌드 출력 폴더에 콘텐츠 페이지를 만듭니다.

    ```html
    <ItemGroup>
      <None Update="content/index.html">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </None>
    </ItemGroup>
    ```

5. 이제 거의 완료되었습니다. 마지막 단계는 Azure Function 설정에 SignalR Service의 연결 문자열을 설정하는 것입니다.

    1. Azure Portal이 열리는 브라우저에서, 포털의 맨 위에 있는 검색 상자에서 해당 이름을 검색하여 이전에 배포한 SignalR Service 인스턴스를 성공적으로 만들었는지 확인합니다. 인스턴스를 선택하여 엽니다.

        ![SignalR Service 인스턴스를 검색합니다.](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. SignalR Service 인스턴스의 연결 문자열을 보려면 **키** 를 선택합니다.
    
        ![기본 연결 문자열을 강조 표시하는 스크린샷.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. 기본 연결 문자열을 복사합니다. 그런 다음 아래의 명령을 실행합니다.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
6. 로컬에서 Azure Function 실행:

    ```bash
    func start
    ```

    로컬로 Azure Function을 실행한 후 브라우저에서 `http://localhost:7071/api/index`를 방문하면 현재 별 개수를 볼 수 있습니다. 또한 GitHub에서 별을 표시하거나 표시 해제하면 몇 초마다 별 개수가 바뀝니다.

    > [!NOTE]
    > SignalR 바인딩에는 Azure Storage가 필요하지만 함수가 로컬에서 실행 중일 때 로컬 스토리지 에뮬레이터를 사용할 수 있습니다.
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`과 같은 오류가 발생한 경우 [Storage Emulator](../storage/common/storage-use-emulator.md)를 다운로드하여 사용하도록 설정해야 합니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬에서 실시간 서버리스 애플리케이션을 빌드하고 실행했습니다. Azure Functions에 대해 SignalR Service 바인딩을 사용하는 방법을 자세히 알아봅니다.
그런 다음, SignalR Service를 사용하여 클라이언트와 Azure Function 간 양방향 통신을 수행하는 방법을 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions의 SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [서버리스의 양방향 통신](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Visual Studio를 사용하여 Azure 함수 앱에 배포](../azure-functions/functions-develop-vs.md#publish-to-azure)

