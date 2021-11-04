---
title: 자습서 - WebSocket API 및 Azure Web PubSub 서비스 SDK를 사용하여 메시지 게시 및 구독
description: Azure Web PubSub 서비스 및 Azure Functions를 사용하여 서버 리스 애플리케이션을 빌드하는 방법을 안내하는 자습서입니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 00ff941ccf008b84ac72191035cc9322d4d08c8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579087"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>자습서: WebSocket API 및 Azure Web PubSub 서비스 SDK를 사용하여 메시지 게시 및 구독

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. 이 자습서에서는 WebSocket API를 사용하여 서비스를 구독하는 방법과 서비스 SDK를 사용하여 메시지를 게시하는 방법을 알아봅니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Web PubSub 서비스 인스턴스 만들기
> * WebSocket 연결을 설정하기 위한 전체 URL 생성
> * 표준 WebSocket 프로토콜을 사용하여 메시지 구독
> * 서비스 SDK를 사용하여 메시지 게시

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

* [.NET Core 2.1 이상](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 이상](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>구독자 설정

클라이언트는 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 인증을 사용하여 표준 WebSocket 프로토콜을 통해 Azure Web PubSub 서비스에 연결합니다. 서비스 SDK는 토큰을 생성하는 도우미 메서드를 제공합니다. 이 자습서에서 구독자는 *ConnectionString* 에서 직접 토큰을 생성합니다. 실제 애플리케이션에서는 일반적으로 서버 쪽 애플리케이션을 사용하여 인증/권한 부여 워크플로를 처리합니다. 워크플로를 보다 정확히 이해하려면 [채팅 앱 빌드](./tutorial-build-chat.md) 자습서를 진행하세요.

# <a name="c"></a>[C#](#tab/csharp)

1. 먼저 이 프로젝트에 대한 새 폴더 `subscribe`를 만들고 필요한 종속성을 설치하겠습니다.
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) 패키지는 WebSocket 연결을 지원하는 타사 패키지입니다. WebSocket을 지원하는 API/라이브러리를 사용하여 이 작업을 수행할 수 있습니다.
    * SDK 패키지 `Azure.Messaging.WebPubSub`는 JWT 토큰을 생성하는 데 도움이 됩니다. 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. 서비스에 연결하도록 `Program.cs` 파일을 다음과 같이 업데이트합니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    using Websocket.Client;

    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];

                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                var url = serviceClient.GenerateClientAccessUri();

                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
                    client.ReconnectTimeout = null;
                    client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                    await client.Start();
                    Console.WriteLine("Connected.");
                    Console.Read();
                }
            }
        }
    }
    ```

    위의 코드는 Azure Web PubSub의 허브에 연결하기 위한 WebSocket 연결을 만듭니다. 허브는 클라이언트 그룹에 메시지를 게시할 수 있는 Azure Web PubSub의 논리적 단위입니다. [주요 개념](./key-concepts.md)에는 Azure Web PubSub에서 사용되는 용어에 대한 자세한 설명이 포함되어 있습니다.
    
    Azure Web PubSub 서비스는 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 인증을 사용하므로, 코드 샘플에서는 Web PubSub SDK에서 `WebPubSubServiceClient.GenerateClientAccessUri()`를 사용하여 유효한 액세스 토큰이 있는 전체 URL을 포함하는 서비스에 대한 URL을 생성합니다.
    
    연결이 설정되면 WebSocket 연결을 통해 메시지를 받게 됩니다. 따라서 `client.MessageReceived.Subscribe(msg => ...));`를 사용하여 들어오는 메시지를 수신 대기합니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 먼저 이 프로젝트에 대한 새 폴더 `subscriber`를 만들고 필요한 종속성을 설치하겠습니다.

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4

    ```
2. 그런 다음, WebSocket API를 사용하여 서비스에 연결합니다. 아래 코드를 사용하여 `subscribe.js` 파일을 만듭니다.

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      const hub = "pubsub";
      let serviceClient = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
      let token = await serviceClient.getClientAccessToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    위의 코드는 Azure Web PubSub의 허브에 연결하기 위한 WebSocket 연결을 만듭니다. 허브는 클라이언트 그룹에 메시지를 게시할 수 있는 Azure Web PubSub의 논리적 단위입니다. [주요 개념](./key-concepts.md)에는 Azure Web PubSub에서 사용되는 용어에 대한 자세한 설명이 포함되어 있습니다.
    
    Azure Web PubSub 서비스는 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 인증을 사용하므로, 코드 샘플에서는 Web PubSub SDK에서 `WebPubSubServiceClient.getClientAccessToken()`를 사용하여 유효한 액세스 토큰이 있는 전체 URL을 포함하는 서비스에 대한 URL을 생성합니다.
    
    연결이 설정되면 WebSocket 연결을 통해 메시지를 받게 됩니다. 따라서 `WebSocket.on('message', ...)`를 사용하여 들어오는 메시지를 수신 대기합니다.
    
3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node subscribe
    ```

# <a name="python"></a>[Python](#tab/python)

1. 먼저 이 프로젝트에 대한 새 폴더 `subscriber`를 만들고 필요한 종속성을 설치하겠습니다.

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice==1.0.0b1
    pip install websockets

    ```

2. 그런 다음, WebSocket API를 사용하여 서비스에 연결합니다. 아래 코드를 사용하여 `subscribe.py` 파일을 만듭니다.

    ```python
    import asyncio
    import sys
    import websockets
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Message received: ' + await ws.recv())

    if len(sys.argv) != 3:
        print('Usage: python subscribe.py <connection-string> <hub-name>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]

    token = build_authentication_token(connection_string, hub_name)

    try:
        asyncio.get_event_loop().run_until_complete(connect(token['url']))
    except KeyboardInterrupt:
        pass

    ```

    위의 코드는 Azure Web PubSub의 허브에 연결하기 위한 WebSocket 연결을 만듭니다. 허브는 클라이언트 그룹에 메시지를 게시할 수 있는 Azure Web PubSub의 논리적 단위입니다. [주요 개념](./key-concepts.md)에는 Azure Web PubSub에서 사용되는 용어에 대한 자세한 설명이 포함되어 있습니다.
    
    Azure Web PubSub 서비스는 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 인증을 사용하므로, 코드 샘플에서는 Web PubSub SDK에서 `build_authentication_token()`를 사용하여 유효한 액세스 토큰이 있는 전체 URL을 포함하는 서비스에 대한 URL을 생성합니다.
    
    연결이 설정되면 WebSocket 연결을 통해 메시지를 받게 됩니다. 따라서 `await ws.recv()`를 사용하여 들어오는 메시지를 수신 대기합니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. 먼저 이 자습서에 대한 새 폴더 `pubsub`를 만듭니다.
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. 그런 다음, 이 `pubsub` 폴더 안에서 Maven을 사용하여 새 콘솔 앱 `webpubsub-quickstart-subscriber`를 만들고 *webpubsub-quickstart-subscriber* 폴더로 전환합니다.

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. 다음과 같이 `pom.xml`의 `dependencies` 노드에 WebSocket 및 Azure Web PubSub SDK 종속성을 추가하겠습니다.

    * `azure-messaging-webpubsub`: Java용 Web PubSub 서비스 SDK
    * `Java-WebSocket`: Java용 WebSocket 클라이언트 SDK

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. Azure Web PubSub에서 WebSocket 연결을 통해 서비스에 연결하고 메시지를 구독할 수 있습니다. WebSocket은 완전한 전이중 통신 채널이므로 서비스에서 클라이언트에 메시지를 실시간으로 푸시할 수 있습니다. WebSocket을 지원하는 API/라이브러리를 사용하여 이 작업을 수행할 수 있습니다. 이 샘플에서는 [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket) 패키지를 사용합니다. */src/main/java/com/webpubsub/quickstart* 디렉터리로 이동하고, 편집기에서 *App.java* 파일을 열고, 코드를 아래 코드로 바꿉니다.

    ```java
    
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;

    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;

    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();

            WebPubSubAuthenticationToken token = client.getAuthenticationToken(new GetAuthenticationTokenOptions());

            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }

                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
                
            };

            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    위의 코드는 Azure Web PubSub의 허브에 연결하기 위한 WebSocket 연결을 만듭니다. 허브는 클라이언트 그룹에 메시지를 게시할 수 있는 Azure Web PubSub의 논리적 단위입니다. [주요 개념](./key-concepts.md)에는 Azure Web PubSub에서 사용되는 용어에 대한 자세한 설명이 포함되어 있습니다.
    
    Azure Web PubSub 서비스는 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 인증을 사용하므로, 코드 샘플에서는 Web PubSub SDK에서 `WebPubSubServiceClient.getAuthenticationToken(new GetAuthenticationTokenOptions())`를 사용하여 유효한 액세스 토큰이 있는 전체 URL을 포함하는 서비스에 대한 URL을 생성합니다.
    
    연결이 설정되면 WebSocket 연결을 통해 메시지를 받게 됩니다. 따라서 `onMessage(String message)`를 사용하여 들어오는 메시지를 수신 대기합니다.

4. *pom.xml* 파일이 들어 있는 디렉터리로 이동한 다음, 아래 코드를 사용하여 앱을 실행하고 `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>서비스 SDK를 사용하여 메시지 게시

이번에는 Azure Web PubSub SDK를 사용하여 연결된 클라이언트에 메시지를 게시해 보겠습니다.

# <a name="c"></a>[C#](#tab/csharp)

1. 먼저 이 프로젝트에 대한 새 폴더 `publisher`를 만들고 필요한 종속성을 설치하겠습니다.

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. `WebPubSubServiceClient` 클래스를 사용하여 클라이언트에 메시지를 보내도록 `Program.cs` 파일을 업데이트하겠습니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var serviceClient = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await serviceClient.SendToAllAsync(message);
            }
        }
    }
    ```

    `SendToAllAsync()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. 이전 구독자가 아래 메시지를 수신한 것을 볼 수 있습니다.
   
    ```
    Message received: Hello World
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 먼저 이 프로젝트에 대한 새 폴더 `publisher`를 만들고 필요한 종속성을 설치하겠습니다.

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4

    ```
2. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. 아래 코드를 사용하여 `publish.js` 파일을 만듭니다.

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const hub = "pubsub";
    let serviceClient = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    serviceClient.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    `sendToAll()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. 이전 구독자가 아래 메시지를 수신한 것을 볼 수 있습니다.
   
    ```
    Message received: Hello World
    ```

# <a name="python"></a>[Python](#tab/python)

1. 먼저 이 프로젝트에 대한 새 폴더 `publisher`를 만들고 필요한 종속성을 설치하겠습니다.
    * bash를 사용하는 경우
        ```bash
        mkdir publisher
        cd publisher
        # Create venv
        python -m venv env

        # Active venv
        ./env/Scripts/activate

        # Or call .\env\Scripts\activate when you are using CMD under windows

        pip install azure-messaging-webpubsubservice==1.0.0b1

        ```
2. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. 아래 코드를 사용하여 `publish.py` 파일을 만듭니다.

    ```python
    import sys
    from azure.messaging.webpubsubservice import (
        WebPubSubServiceClient
    )
    from azure.messaging.webpubsubservice.rest import *

    if len(sys.argv) != 4:
        print('Usage: python publish.py <connection-string> <hub-name> <message>')
        exit(1)

    connection_string = sys.argv[1]
    hub_name = sys.argv[2]
    message = sys.argv[3]

    service_client = WebPubSubServiceClient.from_connection_string(connection_string)
    res = service_client.send_request(build_send_to_all_request(hub_name, content=message, content_type='text/plain'))
    # res should be <HttpResponse: 202 Accepted>
    print(res)

    ```

    `build_send_to_all_request()`는 메시지를 빌드하고 `send_request()`는 허브에 있는 모든 연결된 클라이언트에 메시지를 전송합니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 이전 구독자가 아래 메시지를 수신한 것을 볼 수 있습니다.
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  다른 터미널을 사용하여 `pubsub` 폴더로 돌아가서 게시자 콘솔 앱 `webpubsub-quickstart-publisher`를 만들고 *webpubsub-quickstart-publisher* 폴더로 전환해 보겠습니다.
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. 다음과 같이 `pom.xml`의 `dependencies` 노드에 Azure Web PubSub SDK 종속성을 추가하겠습니다.

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. */src/main/java/com/webpubsub/quickstart* 디렉터리로 이동하고, 편집기에서 *App.java* 파일을 열고, 코드를 아래 코드로 바꿉니다.

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            client.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    `sendToAll()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

4. *pom.xml* 파일이 들어 있는 디렉터리로 이동한 다음, 아래 명령을 사용하여 프로젝트를 실행하고 `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. 이전 구독자가 아래 메시지를 수신한 것을 볼 수 있습니다.
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Web PubSub 서비스에 연결하는 방법과 연결된 클라이언트에 메시지를 게시하는 방법에 대한 기본 개념을 알아보았습니다.

다른 자습서를 확인하여 서비스 사용 방법을 자세히 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Azure Web PubSub를 사용하여 채팅방 만들기](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://aka.ms/awps/samples)
