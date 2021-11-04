---
title: 자습서 - Azure Web PubSub 서비스 및 Azure Functions를 사용하여 서버리스 알림 앱 만들기
description: Azure Web PubSub 서비스 및 Azure Functions를 사용하여 서버리스 알림 애플리케이션을 빌드하는 방법을 안내하는 자습서입니다.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 8e565d31de0943b592db0bafff3e9a55e15c0fee
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578671"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>자습서: Azure Functions 및 Azure Web PubSub 서비스를 사용하여 서버리스 알림 앱 만들기

Azure Web PubSub 서비스를 사용하면 WebSocket을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 자습서에서는 Azure Web PubSub 서비스 및 Azure Functions를 사용하여 알림 시나리오의 실시간 메시징과 함께 서버리스 애플리케이션을 빌드하는 방법을 알아봅니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 서버리스 알림 앱 빌드
> * Web PubSub 함수 입력 및 출력 바인딩 작업
> * 로컬로 샘플 함수 실행
> * Azure Function App에 함수 배포

## <a name="prerequisites"></a>사전 요구 사항

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기

* [Node.js](https://nodejs.org/en/download/) 버전 10.x
   > [!NOTE]
   > 지원되는 Node.js 버전에 대한 자세한 내용은 [Azure Functions 런타임 버전 설명서](../azure-functions/functions-versions.md#languages)를 참조하세요.

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(v3 이상 추천) - Azure Function 앱을 로컬로 실행하고 Azure에 배포합니다.

* Azure 리소스 관리를 위한 [Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)

# <a name="c"></a>[C#](#tab/csharp)

* 코드 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(v3 이상 추천) - Azure Function 앱을 로컬로 실행하고 Azure에 배포합니다.

* Azure 리소스 관리를 위한 [Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>로컬로 함수 만들기 및 실행

1. [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)가 설치되었는지 확인합니다. 그런 다음, 프로젝트에 대한 빈 디렉터리를 만듭니다. 이 작업 디렉터리 아래에서 명령을 실행합니다.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

1. `Microsoft.Azure.WebJobs.Extensions.WebPubSub` 함수 확장 패키지를 명시적으로 설치합니다.

   1. 다음 단계에서 특정 확장 패키지를 설치할 수 있도록 `host.json`에서 `extensionBundle` 섹션을 제거합니다. 또는 호스트 json을 아래와 같이 간단히 지정합니다.

      ```json
      {
        "version": "2.0"
      }
      ```

   1. 특정 함수 확장 패키지를 설치하는 명령을 실행합니다.

      ```bash
      func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
      ```

1. 클라이언트에 대한 정적 웹 페이지를 읽고 호스팅하는 `index` 함수를 만듭니다.

   ```bash
   func new -n index -t HttpTrigger
   ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `index/function.json`을 업데이트하고 다음 json 코드를 복사합니다.
     ```json
     {
         "bindings": [
             {
                 "authLevel": "anonymous",
                 "type": "httpTrigger",
                 "direction": "in",
                 "name": "req",
                 "methods": [
                   "get",
                   "post"
                 ]
             },
             {
                 "type": "http",
                 "direction": "out",
                 "name": "res"
             }
         ]
     }
     ```
   - `index/index.js`를 업데이트하고 다음 코드를 복사합니다.
     ```js
     var fs = require('fs');
     module.exports = function (context, req) {
         fs.readFile('index.html', 'utf8', function (err, data) {
             if (err) {
                 console.log(err);
                 context.done(err);
             }
             context.res = {
                 status: 200,
                 headers: {
                     'Content-Type': 'text/html'
                 },
                 body: data
             };
             context.done();
         });
     }
     ```

   # <a name="c"></a>[C#](#tab/csharp)
   - `index.cs`를 업데이트하고 `Run` 함수를 다음 코드로 바꿉니다.
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            return new ContentResult
            {
                Content = File.ReadAllText("index.html"),
                ContentType = "text/html",
            };
        }
        ```

2. 클라이언트가 액세스 토큰과 함께 서비스 연결 URL을 얻을 수 있도록 `negotiate` 함수를 만듭니다.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `negotiate/function.json`을 업데이트하고 다음 json 코드를 복사합니다.
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req"
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                },
                {
                    "type": "webPubSubConnection",
                    "name": "connection",
                    "hub": "notification",
                    "direction": "in"
                }
            ]
        }
        ```
   - `negotiate/index.js`를 업데이트하고 다음 코드를 복사합니다.
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - `negotiate.cs`를 업데이트하고 `Run` 함수를 다음 코드로 바꿉니다.
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

3. `TimerTrigger`를 사용하여 알림을 생성하도록 `notification` 함수를 만듭니다.
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `notification/function.json`을 업데이트하고 다음 json 코드를 복사합니다.
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "webPubSubOperation",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - `notification/index.js`를 업데이트하고 다음 코드를 복사합니다.
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.webPubSubOperation = {
                "operationKind": "sendToAll",
                "message": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - `notification.cs`를 업데이트하고 `Run` 함수를 다음 코드로 바꿉니다.
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = MessageDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

4. 프로젝트 루트 폴더에서 클라이언트 단일 페이지(`index.html`)를 추가하고 아래와 같이 콘텐츠를 복사합니다.
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
            })();
        </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    C# 프로젝트가 파일을 다른 출력 폴더로 컴파일하기 때문에 콘텐츠 파일에 사용되도록 `*.csproj`를 업데이트해야 합니다.
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

5. Azure 함수 앱을 구성하고 실행합니다.

    - 브라우저에서 **Azure Portal** 을 열고 이전에 배포한 Web PubSub 서비스 인스턴스가 성공적으로 만들어졌는지 확인합니다. 인스턴스로 이동합니다.
    - **키** 를 선택하고 연결 문자열을 복사합니다.

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Web PubSub 연결 문자열을 복사하는 스크린샷.":::

    함수 폴더에서 아래 명령을 실행하여 서비스 연결 문자열을 설정합니다. 필요에 따라 `<connection-string`>을 해당 값으로 바꿉니다.

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > 샘플에 사용된 `TimerTrigger`에 Azure Storage에 대한 종속성이 있지만 함수가 로컬로 실행될 때 로컬 스토리지 에뮬레이터를 사용할 수 있습니다. `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`와 같은 오류가 표시되면 [스토리지 에뮬레이터](../storage/common/storage-use-emulator.md)를 다운로드하고 사용하도록 설정해야 합니다.

    이제 아래 명령으로 로컬 함수를 실행할 수 있습니다.

    ```bash
    func start
    ```

    실행 중인 로그를 확인하고 `https://localhost:7071/api/index`를 방문하여 로컬 호스트 정적 페이지를 방문할 수 있습니다.

## <a name="deploy-function-app-to-azure"></a>Azure에 함수 앱 배포

함수 코드를 Azure에 배포하기 전에 다음 세 가지 리소스를 만들어야 합니다.
* 리소스 그룹 - 관련 리소스에 대한 논리 컨테이너입니다.
* 스토리지 계정 - 함수에 대한 상태 및 기타 정보를 유지 관리합니다.
* 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱은 로컬 함수 프로젝트에 매핑되며, 함수를 논리적 단위로 그룹화하여 리소스를 더 쉽게 관리, 배포 및 공유할 수 있습니다.

다음 명령을 사용하여 이러한 항목을 만듭니다. 

1. 아직 로그인하지 않은 경우 Azure에 로그인합니다.

    ```bash
    az login
    ```

1. 리소스 그룹을 만들거나 Azure Web PubSub 서비스 중 하나를 다시 사용하여 건너뛸 수 있습니다.

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. 범용 스토리지 계정을 리소스 그룹 및 지역에 만듭니다.

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. Azure에서 함수 앱을 만듭니다.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. 함수 프로젝트를 Azure에 배포합니다.

    Azure에서 함수 앱을 성공적으로 만들었으면 이제 [func azure functionapp publish](../azure-functions/functions-run-local.md) 명령을 사용하여 로컬 함수 프로젝트를 배포할 준비가 되었습니다.

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > 여기서는 로컬 설정(`local.settings.json`)을 명령 매개 변수(`--publish-local-settings`)와 함께 배포합니다. Microsoft Azure Storage 에뮬레이터를 사용하는 경우 프롬프트 메시지(`App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`)에 따라 Azure에서 이 값의 덮어쓰기를 건너뛰려면 `no`를 입력할 수 있습니다. 뿐만 아니라 **Azure Portal** -> **설정** -> **구성** 에서 함수 앱 설정을 업데이트할 수 있습니다.

1. 이제 `https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index` URL로 이동하여 Azure Function App에서 사이트를 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 다음 단계에 따라 이 문서에서 만든 리소스를 모두 삭제하세요. 요금은 발생되지 않습니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹** 을 선택한 다음, 만든 리소스 그룹을 선택합니다. 대신 검색 상자를 사용하여 이름으로 리소스 그룹을 찾을 수 있습니다.

1. 열린 창에서 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 서버리스 채팅 애플리케이션을 실행하는 방법을 알아보았습니다. 이제 자체 애플리케이션을 빌드할 수 있습니다. 

> [!div class="nextstepaction"]
> [자습서: Azure Web PubSub를 사용하여 간단한 채팅방 만들기](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Functions에 대한 Azure Web PubSub 바인딩](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://github.com/Azure/azure-webpubsub/tree/main/samples)