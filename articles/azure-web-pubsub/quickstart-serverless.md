---
title: 자습서 - 클라이언트 인증으로 서버리스 실시간 채팅 앱 빌드
description: Azure Web PubSub 서비스 및 Azure Functions를 사용하여 클라이언트 인증을 통해 서버리스 채팅 앱을 빌드하는 방법을 안내하는 자습서입니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: e4dd54ef01cf93ffa0bb47d4bbdccb1d14695934
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434954"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>자습서: Azure Functions 및 Azure Web PubSub 서비스를 사용하여 서버리스 실시간 채팅 앱 만들기

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 자습서에서는 Azure Web PubSub 서비스 및 Azure Functions를 사용하여 실시간 메시징과 게시-구독 패턴으로 서버리스 애플리케이션을 빌드하는 방법에 대해 알아봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 서버리스 실시간 채팅 앱 빌드
> * Web PubSub 함수 트리거 바인딩 및 출력 바인딩 작업
> * Azure Function 앱에 함수 배포
> * Azure 인증 구성
> * 애플리케이션에 이벤트 및 메시지를 라우팅하도록 Web PubSub 이벤트 처리기 구성

## <a name="prerequisites"></a>사전 요구 사항

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기

* [Node.js](https://nodejs.org/en/download/) 버전 10.x
   > [!NOTE]
   > 지원되는 Node.js 버전에 대한 자세한 내용은 [Azure Functions 런타임 버전 설명서](../azure-functions/functions-versions.md#languages)를 참조하세요.
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(v3 이상 추천) - Azure Function 앱을 로컬로 실행하고 Azure에 배포합니다.

* [Azure CLI(Azure 명령줄 인터페이스)](/cli/azure) - Azure 리소스를 관리합니다.

* (선택 사항) [ngrok](https://ngrok.com/download) - 로컬 함수를 Web PubSub 서비스에 대한 이벤트 처리기로 공개합니다. 함수 앱을 로컬로 실행하는 경우에만 선택 사항입니다.

# <a name="c"></a>[C#](#tab/csharp)

* 코드 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))

* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(v3 이상 추천) - Azure Function 앱을 로컬로 실행하고 Azure에 배포합니다.

* [Azure CLI(Azure 명령줄 인터페이스)](/cli/azure) - Azure 리소스를 관리합니다.

* (선택 사항) [ngrok](https://ngrok.com/download) - 로컬 함수를 Web PubSub 서비스에 대한 이벤트 처리기로 공개합니다. 함수 앱을 로컬로 실행하는 경우에만 선택 사항입니다.

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>함수 만들기

1. [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)가 설치되어 있는지 확인합니다. 그런 다음, 프로젝트에 대한 빈 디렉터리를 만듭니다. 이 작업 디렉터리 아래에서 명령을 실행합니다.

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

1. `Microsoft.Azure.WebJobs.Extensions.WebPubSub` 함수 확장 패키지를 명시적으로 설치합니다.

   a. 다음 단계에서 특정 확장 패키지를 설치할 수 있도록 `host.json`에서 `extensionBundle` 섹션을 제거합니다. 또는 호스트 json을 아래와 같이 간단히 지정합니다.
    ```json
    {
        "version": "2.0"
    }
    ```
   b. 특정 함수 확장 패키지를 설치하는 명령을 실행합니다.
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

1. 클라이언트에서 액세스 토큰을 사용하여 서비스 연결 URL을 가져오는 데 도움이 되는 `negotiate` 함수를 만듭니다.
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > 이 샘플에서는 [AAD](/azure/app-service/configure-authentication-user-identities) 사용자 ID 헤더(`x-ms-client-principal-name`)를 사용하여 `userId`를 검색합니다. 그리고 이는 로컬 함수에서 작동하지 않습니다. 비워두거나, 로컬로 재생할 때 `userId`를 가져오거나 생성하는 다른 방법으로 변경할 수 있습니다. 예를 들어 클라이언트에서 사용자 이름을 입력하고, `negotiate` 함수를 호출하여 서비스 연결 URL을 가져올 때 `?user={$username}`과 같은 쿼리에 해당 사용자 이름을 전달할 수 있습니다. 그리고 `negotiate` 함수에서 `userId`를 `{query.user}` 값으로 설정합니다.
    
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
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
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
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

2. 서비스를 통해 클라이언트 메시지를 브로드캐스트하는 `message` 함수를 만듭니다.
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > 이 함수는 실제로 `WebPubSubTrigger`를 사용합니다. 그러나 서비스가 아직 미리 보기 상태이므로 `WebPubSubTrigger`는 함수의 템플릿에 통합되지 않습니다. `HttpTrigger`를 사용하여 함수 템플릿을 초기화하고 코드에서 트리거 형식을 변경합니다.

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `message/function.json`을 업데이트하고 다음 json 코드를 복사합니다.
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "message",
                    "dataType": "binary",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "webPubSubEvent",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - `message/index.js`를 업데이트하고 다음 코드를 복사합니다.
        ```js
        module.exports = async function (context, message) {
            context.bindings.webPubSubEvent = {
                "operationKind": "sendToAll",
                "message": `[${context.bindingData.connectionContext.userId}] ${message}`,
                "dataType": context.bindingData.dataType
            };
            // MessageResponse directly return to caller
            var response = { 
                "message": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - `message.cs`를 업데이트하고 `Run` 함수를 다음 코드로 바꿉니다.
        ```c#
        [FunctionName("message")]
        public static async Task<MessageResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] ConnectionContext context,
            BinaryData message,
            MessageDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubOperation> operations)
        {
            await operations.AddAsync(new SendToAll
            {
                Message = BinaryData.FromString($"[{context.UserId}] {message.ToString()}"),
                DataType = dataType
            });
            return new MessageResponse
            {
                Message = BinaryData.FromString("[SYSTEM] ack"),
                DataType = MessageDataType.Text
            };
        }
        ```

3. 프로젝트 루트 폴더에서 클라이언트 단일 페이지(`index.html`)를 추가하고 아래와 같이 콘텐츠를 복사합니다.
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
                        ws.onmessage = event => {
                            let m = document.createElement('p');
                            m.innerText = event.data;
                            messages.appendChild(m);
                        };
                        let message = document.querySelector('#message');
                        message.addEventListener('keypress', e => {
                            if (e.charCode !== 13) return;
                            ws.send(message.value);
                            message.value = '';
                        });
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    C# 프로젝트는 파일을 다른 출력 폴더로 컴파일하므로 콘텐츠 페이지가 포함되도록 `*.csproj`를 업데이트해야 합니다.
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. 리소스 그룹을 만들거나, Azure Web PubSub 서비스 중 하나를 다시 사용하여 건너뛸 수 있습니다.

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

    Azure에서 함수 앱을 성공적으로 만들었으면 이제 [func azure functionapp publish](/azure-functions/functions-run-local) 명령을 사용하여 로컬 함수 프로젝트를 배포할 준비가 되었습니다.

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > 여기서는 로컬 설정(`local.settings.json`)을 명령 매개 변수(`--publish-local-settings`)와 함께 배포합니다. Microsoft Azure Storage 에뮬레이터를 사용하는 경우 프롬프트 메시지(`App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]`)에 따라 Azure에서 이 값의 덮어쓰기를 건너뛰려면 `no`를 입력할 수 있습니다. 또한 **Azure Portal** -> **설정** -> **구성** 에서 함수 앱 설정을 업데이트할 수 있습니다.

## <a name="configure-the-web-pubsub-service-event-handler"></a>`Event Handler` Web PubSub 서비스 구성

이 샘플에서는 `WebPubSubTrigger`를 사용하여 서비스 업스트림 메시지 요청을 수신 대기합니다. 따라서 Web PubSub는 대상 클라이언트 요청을 보내기 위해 함수의 엔드포인트 정보를 인식하고 있어야 합니다. 그리고 Azure Function 앱에는 확장 관련 웹후크 방법에 대한 보안을 위한 시스템 키가 필요합니다. 이전 단계에서 `message` 함수를 사용하여 함수 앱을 배포하면 시스템 키를 얻을 수 있습니다.

**Azure Portal** -> 함수 앱 리소스 찾기 -> **앱 키** -> **시스템 키** ->  **`webpubsub_extension`** 로 차례로 이동합니다. 값을 `<APP_KEY>`로 복사합니다.

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="함수 시스템 키 가져오기의 스크린샷":::

Azure Web PubSub 서비스에 `Event Handler`를 설정합니다. **Azure Portal** -> Web PubSub 리소스 찾기 -> **설정** 으로 이동합니다. 아래와 같이 사용 중인 하나의 함수에 새 허브 설정 매핑을 추가합니다. `<FUNCTIONAPP_NAME>` 및 `<APP_KEY>`를 사용자 고유의 값으로 바꿉니다.

   - 허브 이름: `simplechat`
   - URL 템플릿: **https://<FUNCTIONAPP_NAME>.azurewebsites.net/runtime/webhooks/webpubsub?code=<APP_KEY>**
   - 사용자 이벤트 패턴: *
   - 시스템 이벤트: -(이 샘플에서는 구성할 필요가 없음)

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="이벤트 처리기를 설정하는 스크린샷.":::

> [!NOTE]
> 함수를 로컬로 실행하는 경우 함수가 시작되면 [ngrok](https://ngrok.com/download)에서 `ngrok http 7071` 명령을 사용하여 함수 URL을 공개할 수 있습니다. 그리고 `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub` URL을 사용하여 `Event Handler` Web PubSub 서비스를 구성합니다. 

## <a name="configure-to-enable-client-authentication"></a>클라이언트 인증을 사용하도록 구성

**Azure Portal** -> 함수 앱 리소스 찾기 -> **인증** 으로 차례로 이동합니다. 클릭 **`Add identity provider`** 합니다. **App Service 인증 설정** 을 **인증되지 않은 액세스 허용** 으로 설정합니다. 그러면 익명 사용자가 인증을 위해 리디렉션하기 전에 클라이언트 인덱스 페이지를 방문할 수 있습니다. 그런 다음, **저장** 합니다.

여기서는 `negotiate` 함수에서 `x-ms-client-principal-name`을 `userId`로 사용할 ID 공급자로 `Microsoft`를 선택합니다. 또한 아래 링크에 따라 다른 ID 공급자를 구성할 수 있으며, 이에 따라 `negotiate` 함수에서 `userId` 값을 업데이트해야 합니다.

* [Microsoft(Azure AD)](/azure/app-service/configure-authentication-provider-aad)
* [Facebook](/azure/app-service/configure-authentication-provider-facebook)
* [Google](/azure/app-service/configure-authentication-provider-google)
* [Twitter](/azure/app-service/configure-authentication-provider-twitter)

## <a name="try-the-application"></a>애플리케이션 사용해 보기

이제 함수 앱(`https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index`)에서 페이지를 테스트할 수 있습니다. 아래 스냅샷을 참조하세요.
1. `login`을 클릭하여 자신을 인증합니다.
2. 입력 상자에서 채팅 메시지를 입력합니다.

메시지 함수에서 호출자의 메시지를 모든 클라이언트에 브로드캐스트하고 `[SYSTEM] ack` 메시지와 함께 호출자를 반환합니다. 따라서 아래 샘플 채팅 스냅샷에서 처음 4개 메시지는 현재 클라이언트에서 제공되었으며, 마지막 2개 메시지는 다른 클라이언트에서 제공되었습니다.

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="채팅 샘플의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

이 앱을 계속 사용하지 않으려면 다음 단계에 따라 이 문서에서 만든 리소스를 모두 삭제하세요. 요금은 발생되지 않습니다.

1. Azure Portal에서 맨 왼쪽에 있는 **리소스 그룹** 을 선택한 다음, 만든 리소스 그룹을 선택합니다. 대신 검색 상자를 사용하여 이름으로 리소스 그룹을 찾을 수 있습니다.

1. 열린 창에서 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

1. 새 창에서 삭제할 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 서버리스 채팅 애플리케이션을 실행하는 방법을 알아보았습니다. 이제 자체 애플리케이션을 빌드할 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Functions에 대한 Azure Web PubSub 바인딩](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [빠른 시작: Azure Web PubSub를 사용하여 간단한 채팅방 만들기](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://github.com/Azure/azure-webpubsub/tree/main/samples)
