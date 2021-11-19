---
title: 자습서 - Azure Web PubSub에서 하위 프로토콜을 사용하여 WebSocket 클라이언트 간에 메시지 게시 및 구독
description: Azure Web PubSub 서비스 및 지원되는 WebSocket 하위 프로토콜을 사용하여 클라이언트 간에 동기화하는 방법을 안내하는 자습서입니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 8a181f48bcdf7ec186aac1b05d3aaf135fa35c09
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550498"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>자습서: 하위 프로토콜을 사용하여 WebSocket 클라이언트 간에 메시지 게시 및 구독

[채팅 앱 빌드 자습서](./tutorial-build-chat.md)에서는 WebSocket API를 사용하여 Azure Web PubSub와 데이터를 보내고 받는 방법을 알아보았습니다. 클라이언트가 서비스와 통신할 때는 프로토콜이 필요하지 않습니다. 예를 들어 `WebSocket.send()`를 사용하여 데이터를 보낼 수 있으며 서버는 데이터를 있는 그대로 받습니다. 이 방법은 사용하기 쉽지만 기능이 제한적입니다. 예를 들어 서버에 이벤트를 보낼 때 이벤트 이름을 지정하거나, 서버에 메시지를 전송하는 대신 다른 클라이언트에 메시지를 게시할 수 없습니다. 이 자습서에서는 하위 프로토콜을 사용하여 클라이언트의 기능을 확장하는 방법을 배웁니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Web PubSub 서비스 인스턴스 만들기
> * WebSocket 연결을 설정하기 위한 전체 URL 생성
> * 하위 프로토콜을 사용하여 WebSocket 클라이언트 간에 메시지 게시

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이렇게 설정하려면 버전 2.22.0 이상의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-an-azure-web-pubsub-instance"></a>Azure Web PubSub 인스턴스 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Web PubSub 인스턴스 만들기

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>나중에 사용할 수 있도록 ConnectionString 가져오기

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

가져온 **ConnectionString** 을 복사합니다. 이 자습서의 뒷부분에서 `<connection_string>` 값으로 사용됩니다.

## <a name="set-up-the-project"></a>프로젝트 설정

### <a name="prerequisites"></a>사전 요구 사항

# <a name="c"></a>[C#](#tab/csharp)

* [ASP.NET Core 3.1 이상](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 이상](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="using-a-subprotocol"></a>하위 프로토콜 사용

클라이언트는 특정 [하위 프로토콜](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9)을 사용하여 WebSocket 연결을 시작할 수 있습니다. Azure Web PubSub 서비스는 클라이언트가 업스트리밍 서버에 왕복하는 대신 직접 게시/구독할 수 있도록 `json.webpubsub.azure.v1`이라는 하위 프로토콜을 지원합니다. 하위 프로토콜에 대한 자세한 내용은 [Azure Web PubSub 지원 JSON WebSocket 하위 프로토콜](./reference-json-webpubsub-subprotocol.md)을 참조하세요.

> 다른 프로토콜 이름을 사용하는 경우 해당 이름은 서비스에서 무시되고 연결 이벤트 처리기의 서버에 전달되므로 사용자 고유의 프로토콜을 만들 수 있습니다.

이제 `json.webpubsub.azure.v1` 하위 프로토콜을 사용하여 웹 애플리케이션을 만들어 보겠습니다.

1.  종속성 설치

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    [Javalin](https://javalin.io/) 웹 프레임워크를 사용하여 웹 페이지를 호스트합니다.
    
    1. 먼저 Maven을 사용하여 새 `logstream-webserver` 앱을 만들고 *logstream-webserver* 폴더로 전환해 보겠습니다.
    
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=logstream-webserver --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-webserver
        ```
    
    2. Azure Web PubSub SDK 및 `javalin` 웹 프레임워크 종속성을 `pom.xml`의 `dependencies` 노드에 추가해 보겠습니다.
    
        * `javalin`: 간단한 Java용 간단한 웹 프레임워크
        * `slf4j-simple`: Java용 로거
        * `azure-messaging-webpubsub`: Azure Web PubSub를 사용하는 서비스 클라이언트 SDK

        ```xml
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-webpubsub</artifactId>
            <version>1.0.0-beta.6</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
        <dependency>
            <groupId>io.javalin</groupId>
            <artifactId>javalin</artifactId>
            <version>3.13.6</version>
        </dependency>
    
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.30</version>
        </dependency>
        ```
    ---
    
2.  `/negotiate` API 및 웹 페이지를 호스트하는 서버 쪽을 만듭니다.

    # <a name="c"></a>[C#](#tab/csharp)

    `Startup.cs`를 다음 코드로 업데이트합니다.
    - 서비스 클라이언트를 추가하고 구성에서 연결 문자열을 읽도록 `ConfigureServices` 메서드를 업데이트합니다. 
    - 정적 파일을 지원할 수 있도록 `Configure` 메서드를 업데이트하여 `app.UseRouting();` 앞에 `app.UseStaticFiles();`를 추가합니다. 
    - `/negotiate` 요청을 통해 클라이언트 액세스 토큰을 생성하도록 `app.UseEndpoints`를 업데이트합니다.

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
    
                app.UseStaticFiles();
    
                app.UseRouting();
    
                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var service = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = service.GetClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    `server.js`를 만들고 아래 코드를 추가합니다.

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    `/negotiate` API 및 웹 페이지를 호스트하는 `server.py`를 만듭니다.

    ```python
    import json
    import sys
    
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    service = WebPubSubServiceClient.from_connection_string(sys.argv[1], hub='stream')
    
    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                roles = ['webpubsub.sendToGroup.stream',
                         'webpubsub.joinLeaveGroup.stream']
                token = service.get_client_access_token(roles=roles)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())
    
    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)
    
        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    
    ```
    
    # <a name="java"></a>[Java](#tab/java)

    다음과 같이 편집기에서 */src/main/java/com/webpubsub/tutorial* 디렉터리를 찾아서 *App.java* 파일을 열고, `Javalin.create`를 사용하여 고정 파일을 제공합니다.

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;
    import com.azure.messaging.webpubsub.models.GetClientAccessTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubClientAccessToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
                option.addRole("webpubsub.sendToGroup.stream");
                option.addRole("webpubsub.joinLeaveGroup.stream");
                WebPubSubClientAccessToken token = service.getClientAccessToken(option);
    
                // return JSON string
                ctx.result("{\"url\":\"" + token.getUrl() + "\"}");
                return;
            });
        }
    }
    ```

    설정에 따라 언어 수준을 Java 8로 명시적으로 설정해야 할 수도 있습니다. 이 작업은 pom.xml에서 수행할 수 있습니다. 다음 코드 조각을 추가합니다.
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```
    ---
    
3.  웹 페이지 만들기

    # <a name="c"></a>[C#](#tab/csharp)
    아래 내용으로 HTML 페이지를 만들고 `wwwroot/index.html`로 저장합니다.
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    아래 내용으로 HTML 페이지를 만들고 `public/index.html`로 저장합니다.

    # <a name="python"></a>[Python](#tab/python)

    아래 내용으로 HTML 페이지를 만들고 `public/index.html`로 저장합니다.
    
    # <a name="java"></a>[Java](#tab/java)

    <a name="create-an-html-page-with-below-content-and-save-it-to-srcmainresourcespublicindexhtml"></a>아래 콘텐츠를 사용하여 HTML 페이지를 만들고 */src/main/resources/public/index.html* 에 저장합니다.
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    이 페이지는 서비스에 연결하고 페이지에 수신된 메시지를 인쇄하기만 합니다. 주요 변경 내용은 WebSocket 연결을 만들 때 하위 프로토콜을 지정하는 것입니다.

4. 서버 실행
    # <a name="c"></a>[C#](#tab/csharp)
    .NET Core용 [Secret Manager](/aspnet/core/security/app-secrets#secret-manager) 도구를 사용하여 연결 문자열을 설정합니다. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 연결 문자열로 바꾸고, 브라우저에서 http://localhost:5000/index.html 을 엽니다.

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    이제 아래 명령을 실행하고, `<connection-string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꾸고, 브라우저에서 http://localhost:8080 을 엽니다.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    이제 아래 명령을 실행하고, `<connection-string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꾸고, 브라우저에서 http://localhost:8080 을 엽니다.

    ```bash
    python server.py "<connection-string>"
    ```

    # <a name="java"></a>[Java](#tab/java)

    이제 아래 명령을 실행하고, `<connection-string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꾸고, 브라우저에서 http://localhost:8080 을 엽니다.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```
    ---

    Chrome을 사용하는 경우 F12 키를 누르거나 마우스 오른쪽 단추 -> **검사** -> **개발자 도구** 를 클릭하고 **네트워크** 탭을 선택하면 됩니다. 웹 페이지를 로드하면 WebSocket 연결이 설정된 것을 볼 수 있습니다. WebSocket 연결을 검사하기 위해 클릭하면 아래 `connected` 이벤트 메시지가 클라이언트에 수신되는 것을 확인할 수 있습니다. 이 클라이언트에 대해 생성된 `connectionId`를 가져올 수 있습니다.
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

하위 프로토콜의 도움을 받으면 연결이 `connected`일 때 연결의 일부 메타데이터를 가져올 수 있습니다.

또한 이제 클라이언트는 일반 텍스트 대신 메시지 유형 및 메시지 출처와 같은 추가 정보가 포함된 JSON 메시지를 수신합니다. 따라서 이 정보를 사용하여 메시지에 대한 추가 처리를 수행할 수 있습니다. 예를 들어 다른 원본에서 온 메시지인 경우 다른 스타일로 표시할 수 있습니다. 이에 대한 내용은 이후 섹션에서 찾을 수 있습니다.

## <a name="publish-messages-from-client"></a>클라이언트에서 메시지 게시

[채팅 앱 빌드](./tutorial-build-chat.md) 자습서에서는 클라이언트가 WebSocket 연결을 통해 메시지를 보낼 때 서버 쪽에서 사용자 이벤트를 트리거합니다. 하위 프로토콜을 사용하면 클라이언트가 JSON 메시지를 전송하여 더 많은 기능을 갖게 됩니다. 예를 들어 클라이언트에서 다른 클라이언트로 직접 메시지를 게시할 수 있습니다.

이 기능은 대량의 데이터를 다른 클라이언트에 실시간으로 스트리밍하려는 경우에 유용합니다. 이 기능을 사용하여 콘솔 로그를 실시간으로 브라우저에 스트리밍할 수 있는 로그 스트리밍 애플리케이션을 빌드해 보겠습니다.

1. 스트리밍 프로그램 만들기
 
    # <a name="c"></a>[C#](#tab/csharp)
    다음과 같이 `stream` 프로그램을 만듭니다.
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    `Program.cs`를 다음 내용으로 업데이트합니다.
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    다음 내용으로 `stream.js`를 만듭니다.
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      let ackId = 0;
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            ackId: ++ackId,
            dataType: 'text',
            data: data.toString()
          }));
        });
      });
      ws.on('message', data => console.log("Received: %s", data));
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    위의 코드는 서비스에 대한 WebSocket 연결을 만든 다음, 데이터를 수신할 때마다 `ws.send()`를 사용하여 데이터를 게시합니다. 다른 클라이언트에 게시하려면 `type`을 `sendToGroup`으로 설정하고 메시지의 그룹 이름을 지정하기만 하면 됩니다.

    # <a name="python"></a>[Python](#tab/python)

    `stream` 프로그램에 대한 또 다른 bash 창을 열고 `websockets` 종속성을 설치합니다.

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install websockets
    ```

    다음 내용으로 `stream.py`를 만듭니다.

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json
    
    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()
    
    if __name__ == '__main__':
        res = requests.get('http://localhost:8080/negotiate').json()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(res['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    위의 코드는 서비스에 대한 WebSocket 연결을 만든 다음, 데이터를 수신할 때마다 `ws.send()`를 사용하여 데이터를 게시합니다. 다른 클라이언트에 게시하려면 `type`을 `sendToGroup`으로 설정하고 메시지의 그룹 이름을 지정하기만 하면 됩니다.
    
    # <a name="java"></a>[Java](#tab/java)

    1.  다른 터미널을 사용하여 루트 폴더로 돌아가서 스트리밍 콘솔 앱 `logstream-streaming`을 만들고 *logstream-streaming* 폴더로 전환해 보겠습니다.
        ```console
        mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=logstream-streaming --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
        cd logstream-streaming
        ```
    
    2. HttpClient 종속성을 `pom.xml`의 `dependencies` 노드에 추가해 보겠습니다.
    
        ```xml
        <!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.13</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.8.9</version>
        </dependency>
        ```
    
    3. 이제 WebSocket을 사용하여 서비스에 연결해 보겠습니다. */src/main/java/com/webpubsub/quickstart* 디렉터리로 이동하고, 편집기에서 *App.java* 파일을 열고, 코드를 아래 코드로 바꿉니다.
    ```java
    package com.webpubsub.quickstart;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.WebSocket;
    import java.util.concurrent.CompletionStage;
    
    import com.google.gson.Gson;
    
    public class App 
    {
        public static void main( String[] args ) throws IOException, InterruptedException
        {
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("http://localhost:8080/negotiate"))
                .build();
    
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            
            Gson gson = new Gson();
    
            String url = gson.fromJson(response.body(), Entity.class).url;
    
            WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                    .buildAsync(URI.create(url), new WebSocketClient()).join();
            int id = 0;
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            String streaming = reader.readLine();
            App app = new App();
            while (streaming != null && !streaming.isEmpty()){
                String frame = gson.toJson(app.new GroupMessage(streaming + "\n", ++id));
                System.out.println("Sending: " + frame);
                ws.sendText(frame, true);
                streaming = reader.readLine();
            }
        }
    
        private class GroupMessage{
            public String data;
            public int ackId;
            public final String type = "sendToGroup";
            public final String group = "stream";
            
            GroupMessage(String data, int ackId){
                this.data = data;
                this.ackId = ackId;
            }
        }
    
        private static final class WebSocketClient implements WebSocket.Listener {
            private WebSocketClient() {
            }
    
            @Override
            public void onOpen(WebSocket webSocket) {
                System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
                WebSocket.Listener.super.onOpen(webSocket);
            }
    
            @Override
            public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
                System.out.println("onText received " + data);
                return WebSocket.Listener.super.onText(webSocket, data, last);
            }
    
            @Override
            public void onError(WebSocket webSocket, Throwable error) {
                System.out.println("Bad day! " + webSocket.toString());
                WebSocket.Listener.super.onError(webSocket, error);
            }
        }
    
        private static final class Entity {
            public String url;
        }
    }
    
    ```

    4. *pom.xml* 파일이 포함된 디렉터리로 이동해 아래 명령을 사용하여 프로젝트를 실행합니다.
    
      ```console
      mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
      ```
    
    ---
    
    여기서는 "그룹"이라는 새로운 개념을 볼 수 있습니다. 그룹은 연결 그룹에 메시지를 게시할 수 있는 허브의 논리적 개념입니다. 허브에 여러 그룹을 포함할 수 있으며 한 클라이언트가 동시에 여러 그룹을 구독할 수 있습니다. 하위 프로토콜을 사용하는 경우 전체 허브에 브로드캐스트하는 대신 한 그룹에만 게시할 수 있습니다. 용어에 대한 자세한 내용은 [기본 개념](./key-concepts.md)을 참조하세요.

2.  여기서는 그룹을 사용하기 때문에 `index.html` 콜백 내에서 WebSocket 연결이 설정되면 그룹에 조인하도록 `ws.onopen` 웹 페이지를 업데이트해야 합니다.
    
    ```javascript
    let ackId = 0;
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream',
        ackId: ++ackId
      }));
    };
    ```

    클라이언트가 메시지를 `joinGroup` 형식으로 전송하여 그룹에 조인하는 것을 볼 수 있습니다.

3.  또한 JSON 응답을 구문 분석하고 `ws.onmessage` 그룹의 메시지만 인쇄하도록 `stream` 콜백 논리를 살짝 업데이트합니다. 그러면 라이브 스트림 프린터로 작동합니다.

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  보안상의 이유로 클라이언트는 기본적으로 그룹 자체를 게시하거나 구독할 수 없습니다. 이러한 이유로 토큰을 생성할 때 `roles`를 클라이언트로 설정했습니다.

    # <a name="c"></a>[C#](#tab/csharp)
    `Startup.cs`의 `GenerateClientAccessUri`가 아래와 같으면 `roles`를 설정합니다.
    ```csharp
    service.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    `server.js`의 `getClientAccessToken`이 아래와 같으면 `roles`를 추가합니다.

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await service.getClientAccessToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    액세스 토큰을 생성할 때 `server.py`에서 올바른 역할을 클라이언트에 설정합니다.

    ```python
    roles = ['webpubsub.sendToGroup.stream',
              'webpubsub.joinLeaveGroup.stream']
    token = service.get_client_access_token(roles=roles)
    ```
    
    # <a name="java"></a>[Java](#tab/java)
    
    액세스 토큰을 생성할 때 `App.java`에서 올바른 역할을 클라이언트에 설정합니다.

    ```java
    GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
    option.addRole("webpubsub.sendToGroup.stream");
    option.addRole("webpubsub.joinLeaveGroup.stream");
    WebPubSubClientAccessToken token = service.getClientAccessToken(option);

    ```
    ---

5.  마지막으로, 보기 좋게 표시되도록 `index.html`에 스타일을 적용합니다.

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

이제 아래 코드를 실행하고 텍스트를 입력하면 실시간으로 브라우저에 표시됩니다.

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

또는 데이터가 실시간으로 브라우저에 스트리밍되는 것을 볼 수 있도록 속도를 낮출 수도 있습니다.

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

이 자습서의 전체 코드 샘플은 [여기][code-csharp]서 찾을 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

또는 이 앱을 사용하여 다른 콘솔 앱의 출력을 파이핑하고 브라우저로 스트리밍할 수도 있습니다. 예를 들면 다음과 같습니다.

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

또는 데이터가 실시간으로 브라우저에 스트리밍되는 것을 볼 수 있도록 속도를 낮출 수도 있습니다.

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

이 자습서의 전체 코드 샘플은 [여기][code-js]서 찾을 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

이제 `python stream.py`를 실행하고 텍스트를 입력하면 실시간으로 브라우저에 표시됩니다.

또는 이 앱을 사용하여 다른 콘솔 앱의 출력을 파이핑하고 브라우저로 스트리밍할 수도 있습니다. 예를 들면 다음과 같습니다.

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

또는 데이터가 실시간으로 브라우저에 스트리밍되는 것을 볼 수 있도록 속도를 낮출 수도 있습니다.

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

이 자습서의 전체 코드 샘플은 [여기][code-python]서 찾을 수 있습니다.


# <a name="java"></a>[Java](#tab/java)

이제 아래 코드를 실행하고 텍스트를 입력하면 실시간으로 브라우저에 표시됩니다.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

이 자습서의 전체 코드 샘플은 [여기][code-java]서 찾을 수 있습니다.

---


## <a name="next-steps"></a>다음 단계

이 자습서에서는 하위 프로토콜을 허용하여 Web PubSub 서비스에 연결하는 방법과 연결된 클라이언트에 메시지를 게시하는 방법에 대한 기본 개념을 알아보았습니다.

다른 자습서를 확인하여 서비스 사용 방법을 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://aka.ms/awps/samples)

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/

[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/logstream/
