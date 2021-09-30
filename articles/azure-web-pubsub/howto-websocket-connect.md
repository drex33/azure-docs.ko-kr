---
title: Azure 웹 PubSub에 대 한 WebSocket 연결을 시작 하는 방법
description: 다른 언어로 Azure 웹 PubSub 서비스에 대 한 WebSocket 연결을 시작 하는 방법을 알아봅니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 02e7afa9b8c454b3520db933d76149973b50c699
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275167"
---
#  <a name="start-a-websocket-connection-to-azure-web-pubsub"></a>Azure 웹 PubSub에 대 한 WebSocket 연결 시작

클라이언트는 표준 [WebSocket](https://tools.ietf.org/html/rfc6455) 프로토콜을 사용 하 여 Azure 웹 pubsub 서비스에 연결 합니다. WebSocket 클라이언트를 지 원하는 언어를 사용 하 여 서비스에 대 한 클라이언트를 작성할 수 있습니다. 이 문서에서는 다양 한 언어의 WebSocket 클라이언트 샘플을 여러 개 볼 수 있습니다.

## <a name="authorization"></a>권한 부여

웹 PubSub는 [JWT (JSON Web Token)](https://tools.ietf.org/html/rfc7519.html) 를 사용 하 여 클라이언트의 유효성을 검사 하 고 권한을 부여 합니다. 클라이언트는 `access_token` 쿼리 매개 변수에 토큰을 추가 하거나 `Authorization` 서비스에 연결할 때 헤더에 넣을 수 있습니다.

일반적으로 클라이언트는 먼저 해당 앱 서버와 통신 하 여 서비스와 토큰의 URL을 가져옵니다. 그런 다음 클라이언트가 수신 하는 URL 및 토큰을 사용 하 여 서비스에 대 한 WebSocket 연결을 엽니다.

또한 포털에서는 토큰을 사용 하 여 클라이언트 URL을 동적으로 생성 하는 도구를 제공 합니다. 이 도구는 빠른 테스트를 수행 하는 데 유용할 수 있습니다.

:::image type="content" source="./media/howto-websocket-connect/generate-client-url.png" alt-text="클라이언트 URL 생성기를 찾을 수 있는 위치를 보여 주는 스크린샷":::

> [!NOTE]
> 토큰을 생성할 때 필요한 역할만 포함 해야 합니다.
>

다음 섹션에서는 샘플 워크플로를 간소화 하기 위해 클라이언트에서 연결 하기 위해 포털에서 임시로 생성 된 URL을 사용 합니다. 을 사용 `<Client_URL_From_Portal>` 하 여 값을 나타냅니다. 생성 된 토큰은 기본적으로 60 분 후에 만료 되므로 토큰이 만료 되 면 다시 생성 해야 합니다.

서비스는 두 가지 유형의 WebSocket 클라이언트를 지원 합니다. 하나는 단순 WebSocket 클라이언트이 고 다른 하나는 PubSub WebSocket 클라이언트입니다. 여기에서는 이 두 종류의 클라이언트가 서비스에 연결하는 방법을 보여 줍니다. 이러한 클라이언트에 대 한 자세한 내용은 [Azure 웹 PubSub의 WebSocket 클라이언트 프로토콜](./concept-client-protocols.md)을 참조 하세요.

## <a name="dependency"></a>종속성

# <a name="in-browser"></a>[브라우저에서](#tab/browser)
대부분의 최신 브라우저에서는 `WebSocket` API가 기본적으로 지원됩니다. 

# <a name="nodejs"></a>[Node.JS](#tab/javascript)

* [Node.js 12. x 이상](https://nodejs.org)
* `npm install ws`

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)
* `pip install websockets`

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 이상](https://dotnet.microsoft.com/download)
* `dotnet add package Websocket.Client`
    * [Websocket. 클라이언트](https://github.com/Marfusios/websocket-client) 는 기본 제공 재연결 및 오류 처리를 포함 하는 타사 Websocket 클라이언트입니다.

# <a name="java"></a>[Java](#tab/java)
- [JDK (Java Development Kit)](/java/azure/jdk/) 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

## <a name="simple-websocket-client"></a>단순 WebSocket 클라이언트

# <a name="in-browser"></a>[브라우저에서](#tab/browser)

`script`HTML 페이지 블록 내에서:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>");
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.JS](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def hello():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri) as ws:
        while True:
            await ws.send('hello')
            greeting = await ws.recv()
            print(greeting)

asyncio.get_event_loop().run_until_complete(hello())
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Websocket.Client;

namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>")))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A simple WebSocket Client.
 *
 */
public final class SimpleClient {
    private SimpleClient() {
    }

    /**
     * Starts a simple WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder()
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();
        System.in.read();
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
}

```


---

## <a name="pubsub-websocket-client"></a>PubSub WebSocket 클라이언트

# <a name="in-browser"></a>[브라우저에서](#tab/browser)

`script`HTML 페이지 블록 내에서:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>", 'json.webpubsub.azure.v1');
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.JS](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>", "json.webpubsub.azure.v1");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def join_group():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri, subprotocols=['json.webpubsub.azure.v1']) as ws:
        await ws.send('{"type":"joinGroup","ackId":1,"group":"group1"}')
        return await ws.recv()

print(asyncio.get_event_loop().run_until_complete(join_group()))
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading.Tasks;
namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>"), () =>
            {
                var inner = new ClientWebSocket();
                inner.Options.AddSubProtocol("json.webpubsub.azure.v1");
                return inner;
            }))
            {
                // Disable the auto disconnect and reconnect because the sample would like the client to stay online even if no data comes in
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A PubSub WebSocket Client.
 *
 */
public final class SubprotocolClient {
    private SubprotocolClient() {
    }

    /**
     * Starts a PubSub WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();

        ws.sendText("{\"type\":\"joinGroup\",\"ackId\":1,\"group\":\"group1\"}", true);
        System.in.read();
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
}
```

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 포털에서 생성 된 URL을 사용 하 여 서비스에 연결 하는 방법을 알아보았습니다. 클라이언트가 앱 서버와 통신 하 여 실제 응용 프로그램에서 URL을 가져오는 방법을 확인 하려면 이러한 자습서를 읽고 샘플을 확인 하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Web PubSub를 사용하여 채팅방 만들기](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [자습서: 서비스 지원 하위 프로토콜을 사용한 클라이언트 스트리밍](./tutorial-subprotocol.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://aka.ms/awps/samples)

