---
title: 참조 - Azure Web PubSub용 JavaScript SDK
description: 이 참조에서는 Azure Web PubSub 서비스에 대한 JavaScript SDK를 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 899f9845721412ceef9171c334383e596fd798e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493069"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>Azure Web PubSub용 JavaScript SDK

[Azure Web PubSub 서비스는](https://aka.ms/awps/doc) 개발자가 실시간 기능 및 게시-구독 패턴으로 웹 애플리케이션을 쉽게 빌드할 수 있도록 도와주는 Azure 관리형 서비스입니다. 서버와 클라이언트 간 또는 클라이언트 간에 실시간 게시-구독 메시징이 필요한 시나리오는 Azure Web PubSub 서비스를 사용할 수 있습니다. 서버에서 폴링하거나 HTTP 요청을 제출해야 하는 기존의 실시간 기능에서도 Azure Web PubSub 서비스를 사용할 수 있습니다.

JavaScript용으로 제공되는 두 가지 라이브러리는 서비스 클라이언트 라이브러리와 Express 미들웨어입니다. 다음 섹션에는 이러한 라이브러리에 대한 자세한 정보가 포함되어 있습니다.

- [서비스 클라이언트 라이브러리](#service-client-library)
- [Express 미들웨어](#express)

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript용 Azure Web PubSub 서비스 클라이언트 라이브러리

아래 다이어그램과 같이 앱 서버 쪽에서 이 라이브러리를 사용하여 WebSocket 클라이언트 연결을 관리할 수 있습니다.

![오버플로 다이어그램은 서비스 클라이언트 라이브러리를 사용하는 의 오버플로를 보여줍니다.](media/sdk-reference/service-client-overflow.png)

- 허브 및 그룹에 메시지를 보냅니다.
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여, 철회 및 확인

여기에 사용된 용어에 대한 자세한 내용은 [주요 개념](#key-concepts) 섹션에 설명되어 있습니다.

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API 참조 설명서](https://aka.ms/awps/sdk/js) |
[제품 설명서](https://aka.ms/awps/doc) |
[샘플][samples_ref]

### <a name="getting-started"></a>시작

#### <a name="currently-supported-environments"></a>현재 지원되는 환경

- [Node.js의 LTS 버전](https://nodejs.org/about/releases/)

#### <a name="prerequisites"></a>사전 요구 사항

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. `@azure/web-pubsub` 패키지 설치

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. WebPubSubServiceClient 만들기 및 인증

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

엔드포인트와 `AzureKeyCredential`을 사용하여 `WebPubSubServiceClient`를 인증할 수도 있습니다.

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

또는 `WebPubSubServiceClient` [Azure Active Directory][aad_doc]를 사용하여 를 인증합니다.

1. `@azure/identity`종속성 설치

```batch
npm install @azure/identity
```

2. 를 사용하도록 소스 코드를 업데이트합니다. `DefaultAzureCredential`

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new DefaultAzureCredential();
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>주요 개념

#### <a name="connection"></a>연결

클라이언트 또는 클라이언트 연결이라고도 하는 연결은 Web PubSub 서비스에 연결된 개별 WebSocket 연결을 나타냅니다. 성공적으로 연결되면 Web PubSub 서비스에서 고유한 연결 ID를 이 연결에 할당합니다.

#### <a name="hub"></a>허브

허브는 클라이언트 연결 집합에 대한 논리적 개념입니다. 일반적으로 하나의 목적에 대해 하나의 허브를 사용합니다(예: 채팅 허브 또는 알림 허브). 클라이언트 연결이 만들어지면 허브에 연결되고 수명 동안 해당 허브에 속합니다. 애플리케이션마다 서로 다른 허브 이름을 사용하여 하나의 Azure Web PubSub 서비스를 공유할 수 있습니다.

#### <a name="group"></a>그룹

그룹은 허브에 대한 연결의 하위 집합입니다. 그룹에 클라이언트 연결을 추가하거나 원하는 경우 그룹에서 클라이언트 연결을 제거할 수 있습니다. 예를 들어 클라이언트가 채팅방에 참가하거나 채팅방에서 나가면 이 채팅방이 그룹일 수 있습니다. 클라이언트는 여러 그룹에 참가할 수 있으며, 한 그룹에 여러 클라이언트가 포함될 수 있습니다.

#### <a name="user"></a>사용자

Web PubSub에 대한 연결은 한 명의 사용자에게 속할 수 있습니다. 단일 사용자가 여러 디바이스 또는 여러 브라우저 탭에서 연결된 경우와 같이 사용자에게 여러 연결이 있을 수 있습니다.

#### <a name="message"></a>메시지

클라이언트가 연결되면 업스트림 애플리케이션으로 메시지를 보내거나 WebSocket 연결을 통해 업스트림 애플리케이션에서 메시지를 받을 수 있습니다.

### <a name="examples"></a>예

#### <a name="get-the-access-token-for-a-client-to-start-the-websocket-connection"></a>클라이언트가 WebSocket 연결을 시작하기 위한 액세스 토큰을 얻습니다.

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Get the access token for the WebSocket client connection to use
let token = await serviceClient.getClientAccessToken();

// Or get the access token and assign the client a userId
token = await serviceClient.getClientAccessToken({ userId: "user1" });

// return the token to the WebSocket client
```

#### <a name="broadcast-messages-to-all-connections-in-a-hub"></a>허브의 모든 연결에 메시지 브로드캐스트

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-in-a-group"></a>그룹의 모든 연결에 메시지 보내기

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// Add user to the group
await groupClient.addUser("user1");

// Send a JSON message
await groupClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await groupClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await groupClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-for-a-user"></a>사용자에 대한 모든 연결에 메시지 보내기

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToUser("user1", { message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToUser("user1", "Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToUser("user1", payload.buffer);
```

#### <a name="check-if-the-group-has-any-connection"></a>그룹에 연결이 있는지 확인합니다.

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");
const WebSocket = require("ws");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// close all the connections in the group
await groupClient.closeAllConnections({ reason: "<closeReason>" });

// check if the group has any connections
const hasConnections = await serviceClient.groupExists("<groupName>");
```

#### <a name="access-the-raw-http-response-for-an-operation"></a>작업에 대한 원시 HTTP 응답에 액세스

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

function onResponse(rawResponse: FullOperationResponse): void {
  console.log(rawResponse);
}
const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" }, { onResponse });
```

### <a name="troubleshooting"></a>문제 해결

#### <a name="enable-logs"></a>로그 사용 설정

다음 환경 변수를 설정하여 이 라이브러리를 사용할 때 디버그 로그를 볼 수 있습니다.

- SignalR 클라이언트 라이브러리에서 디버그 로그 가져오기

```bash
export AZURE_LOG_LEVEL=verbose
```

로그를 사용하는 방법에 대한 자세한 내용은 [@azure/logger 패키지 문서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger)를 참조하세요.

#### <a name="live-trace"></a>라이브 추적

Web PubSub 서비스 포털에서 **라이브 추적** 을 사용하여 실시간 트래픽을 확인합니다.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express용 Azure Web PubSub CloudEvents 처리기

WebSocket 연결이 연결되면 Web PubSub 서비스는 연결 수명 주기 및 메시지를 [CloudEvents 형식의 이벤트로 변환합니다.](concept-service-internals.md#workflow) 이 라이브러리는 아래 다이어그램과 같이 WebSocket 연결의 수명 주기 및 메시지를 나타내는 이벤트를 처리하는 Express 미들웨어를 제공합니다.

![오버플로 다이어그램은 이벤트 처리기 미들웨어를 사용하는 의 오버플로를 보여줍니다.](media/sdk-reference/event-handler-middleware.png)

여기에 사용된 용어에 대한 자세한 내용은 [주요 개념](#key-concepts) 섹션에 설명되어 있습니다.

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub-express) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API 참조 설명서](https://aka.ms/awps/sdk/js) |
[제품 설명서](https://aka.ms/awps/doc) |
[샘플][samples_ref]

### <a name="getting-started"></a>시작

#### <a name="currently-supported-environments"></a>현재 지원되는 환경

- [Node.js의 LTS 버전](https://nodejs.org/about/releases/)
- [Express](https://expressjs.com/) 버전 4.x.x 이상

#### <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 엔드포인트.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. `@azure/web-pubsub-express` 패키지 설치

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. 만들기 `WebPubSubEventHandler`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat");

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>주요 개념

#### <a name="connection"></a>연결

클라이언트 또는 클라이언트 연결이라고도 하는 연결은 Web PubSub 서비스에 연결된 개별 WebSocket 연결을 나타냅니다. 성공적으로 연결되면 Web PubSub 서비스에서 고유한 연결 ID를 이 연결에 할당합니다.

#### <a name="hub"></a>허브

허브는 클라이언트 연결 집합에 대한 논리적 개념입니다. 일반적으로 하나의 목적에 대해 하나의 허브를 사용합니다(예: 채팅 허브 또는 알림 허브). 클라이언트 연결이 만들어지면 허브에 연결되고 수명 동안 해당 허브에 속합니다. 애플리케이션마다 서로 다른 허브 이름을 사용하여 하나의 Azure Web PubSub 서비스를 공유할 수 있습니다.

#### <a name="group"></a>그룹

그룹은 허브에 대한 연결의 하위 집합입니다. 그룹에 클라이언트 연결을 추가하거나 원하는 경우 그룹에서 클라이언트 연결을 제거할 수 있습니다. 예를 들어 클라이언트가 채팅방에 참가하거나 채팅방에서 나가면 이 채팅방이 그룹일 수 있습니다. 클라이언트는 여러 그룹에 참가할 수 있으며, 한 그룹에 여러 클라이언트가 포함될 수 있습니다.

#### <a name="user"></a>사용자

Web PubSub에 대한 연결은 한 명의 사용자에게 속할 수 있습니다. 단일 사용자가 여러 디바이스 또는 여러 브라우저 탭에서 연결된 경우와 같이 사용자에게 여러 연결이 있을 수 있습니다.

#### <a name="client-events"></a>클라이언트 이벤트

이벤트는 클라이언트 연결의 수명 주기 동안 생성됩니다. 예를 들어 단순 WebSocket 클라이언트 연결에서는 서비스에 연결하려고 하면 `connect` 이벤트, 서비스에 성공적으로 연결되면 `connected` 이벤트, 서비스에 메시지를 보낼 때 `message` 이벤트, 서비스에서 연결이 끊어지면 `disconnected` 이벤트가 생성됩니다.

#### <a name="event-handler"></a>이벤트 처리기

이벤트 처리기에는 클라이언트 이벤트를 처리하는 논리가 포함되어 있습니다. 이벤트 처리기는 미리 포털 또는 Azure CLI를 통해 서비스에 등록 및 구성해야 합니다. 이벤트 처리기 논리를 호스팅하는 위치는 일반적으로 서버 쪽으로 간주됩니다.

### <a name="examples"></a>예

#### <a name="handle-the-connect-request-and-assign-userid"></a>요청을 처리 `connect` 하 고 할당 `<userId>`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      userId: "<userId>"
    });
  },
  allowedEndpoints: ["https://<yourAllowedService>.webpubsub.azure.com"]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="only-allow-specified-endpoints"></a>지정 된 끝점만 허용

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  allowedEndpoints: [
    "https://<yourAllowedService1>.webpubsub.azure.com",
    "https://<yourAllowedService2>.webpubsub.azure.com"
  ]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-custom-event-handler-path"></a>사용자 지정 이벤트 처리기 경로 설정

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  path: "customPath1"
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  // Azure WebPubSub Upstream ready at http://localhost:3000/customPath1
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-and-read-connection-state"></a>연결 상태 설정 및 읽기

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");

const handler = new WebPubSubEventHandler("chat", {
  handleConnect(req, res) {
    // You can set the state for the connection, it lasts throughout the lifetime of the connection
    res.setState("calledTime", 1);
    res.success();
  },
  handleUserEvent(req, res) {
    var calledTime = req.context.states.calledTime++;
    console.log(calledTime);
    // You can also set the state here
    res.setState("calledTime", calledTime);
    res.success();
  }
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="troubleshooting"></a>문제 해결

#### <a name="enable-logs"></a>로그 사용 설정

다음 환경 변수를 설정하여 이 라이브러리를 사용할 때 디버그 로그를 볼 수 있습니다.

- SignalR 클라이언트 라이브러리에서 디버그 로그 가져오기

```bash
export AZURE_LOG_LEVEL=verbose
```

로그를 사용하는 방법에 대한 자세한 내용은 [@azure/logger 패키지 문서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger)를 참조하세요.

#### <a name="live-trace"></a>라이브 추적

Web PubSub 서비스 포털에서 **라이브 추적** 을 사용하여 실시간 트래픽을 확인합니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]