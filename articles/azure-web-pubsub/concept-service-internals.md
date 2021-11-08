---
title: Azure Web PubSub 서비스 내부
description: Azure Web PubSub 서비스 내부 기능, 아키텍처, 연결 및 데이터 전송 방법에 대해 알아봅니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ec1a0bfcc883e9434c8c80264c574c3630e395b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000803"
---
#  <a name="azure-web-pubsub-service-internals"></a>Azure Web PubSub 서비스 내부

Azure Web PubSub 서비스에서는 단순 [WebSocket](https://tools.ietf.org/html/rfc6455) 연결을 사용하여 메시지를 손쉽게 게시/구독하는 방법을 제공합니다.

- WebSocket을 지원하는 모든 언어로 클라이언트를 작성할 수 있습니다.
- 하나의 연결 내에서 텍스트와 이진 메시지 모두 지원
- 클라이언트가 클라이언트 간에 직접 메시지 게시를 수행하기 위한 단순 프로토콜
- 서비스에서 WebSocket 연결을 관리함

## <a name="terms"></a>항
* **서비스**: Azure Web PubSub 서비스입니다.

[!INCLUDE [Terms](includes/terms.md)]

* **클라이언트 연결** 및 **ConnectionId**: 클라이언트가 `/client` 엔드포인트에 연결합니다. 연결되면 서비스에서 고유한 `connectionId`를 클라이언트 연결의 고유 ID로 생성합니다. 그러면 사용자는 이 `connectionId`를 사용하여 클라이언트 연결을 관리할 수 있습니다. 세부 정보는 [클라이언트 프로토콜](#client_protocol) 섹션에 설명되어 있습니다.

* **클라이언트 이벤트**: 이벤트는 클라이언트 연결의 수명 주기 동안 생성됩니다. 예를 들어 단순 WebSocket 클라이언트 연결에서는 서비스에 연결하려고 하면 `connect` 이벤트, 서비스에 성공적으로 연결되면 `connected` 이벤트, 서비스에 메시지를 보낼 때 `message` 이벤트, 서비스에서 연결이 끊어지면 `disconnected` 이벤트가 생성됩니다. *클라이언트 이벤트* 에 대한 세부 정보는 [클라이언트 프로토콜](#client_protocol) 섹션에 설명되어 있습니다.

* **이벤트 처리기**: 이벤트 처리기에는 클라이언트 이벤트를 처리하는 논리가 포함되어 있습니다. 포털이나 Azure CLI를 통해 미리 서비스에 이벤트 처리기를 등록하고 구성합니다. 세부 정보는 [이벤트 처리기](#event_handler) 섹션에 설명되어 있습니다. 이벤트 처리기 논리를 호스트하는 위치는 서버 쪽으로 간주됩니다.

* **서버**: 서버는 클라이언트 이벤트를 처리하고 클라이언트 연결을 관리하며 그룹에 메시지를 게시할 수 있습니다. 서버는 클라이언트에 비해 신뢰할 수 있습니다. **서버** 에 대한 세부 정보는 [서버 프로토콜](#server_protocol) 섹션에 설명되어 있습니다.

<a name="workflow"></a>

## <a name="workflow"></a>워크플로

![Web PubSub 서비스 워크플로를 보여 주는 다이어그램](./media/concept-service-internals/workflow.png)

위의 워크플로 그래프에서 설명한 대로 다음이 구성됩니다.
1. *클라이언트* 는 WebSocket 전송을 사용하여 서비스 `/client` 엔드포인트에 연결합니다. 서비스는 모든 WebSocket 프레임을 구성된 업스트림(서버)으로 전달합니다. WebSocket 연결은 서버가 처리할 사용자 지정 하위 프로토콜과 연결하거나 클라이언트가 직접 게시/구독을 수행할 수 있는 서비스 지원 하위 프로토콜 `json.webpubsub.azure.v1`과 연결할 수 있습니다. 세부 정보는 [클라이언트 프로토콜](#client_protocol)에 설명되어 있습니다.
2. 서비스는 다양한 클라이언트 이벤트에서 **CloudEvents HTTP 프로토콜** 을 사용하여 서버를 호출합니다. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)는 CNCF(Cloud Native Computing Foundation)에서 호스트하는 이벤트의 구조 및 메타데이터 설명에 대한 표준화된 프로토콜 독립적 정의입니다. 세부 정보는 [서버 프로토콜](#server_protocol)에 설명되어 있습니다.
3. 서버는 REST API로 서비스를 호출하여 클라이언트에 메시지를 보내거나 연결된 클라이언트를 관리할 수 있습니다. 세부 정보는 [서버 프로토콜](#server_protocol)에 설명되어 있습니다.

<a name="client_protocol"></a>

## <a name="client-protocol"></a>클라이언트 프로토콜

클라이언트 연결은 [WebSocket 프로토콜](https://tools.ietf.org/html/rfc6455)을 사용하여 서비스의 `/client` 엔드포인트에 연결합니다. WebSocket 프로토콜은 단일 TCP 연결을 통해 전이중 통신 채널을 제공하며 2011년에 IETF에서 RFC 6455로 표준화했습니다. 대부분의 언어에는 WebSocket 연결을 시작하는 기본 지원을 제공합니다. 

이 서비스는 다음 두 가지 종류의 클라이언트를 지원합니다.
- 하나는 [단순 WebSocket 클라이언트](#simple_client)라고 합니다.
- 다른 하나는 [PubSub WebSocket 클라이언트](#pubsub_client)라고 합니다.

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>단순 WebSocket 클라이언트
이름에서 알 수 있듯이 간단한 WebSocket 클라이언트는 단순 WebSocket 연결입니다. 또한 사용자 지정 하위 프로토콜을 가질 수 있습니다. 

예를 들어 JS에서 다음을 사용하여 단순 WebSocket 클라이언트를 만들 수 있습니다.
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

단순 WebSocket 클라이언트는 아래 시퀀스 다이어그램에서 볼 수 있듯이 클라이언트<->서버 아키텍처를 따릅니다. ![클라이언트 연결 시퀀스를 보여 주는 다이어그램](./media/concept-service-internals/simple-client-sequence.png)


1. 클라이언트가 WebSocket 핸드셰이크를 시작하면 서비스는 WebSocket 핸드셰이크에 대해 `connect` 이벤트 처리기(서버)를 호출하려고 합니다. 사용자는 이 처리기를 사용하여 WebSocket 핸드셰이크를 처리하고, 사용할 하위 프로토콜을 결정하며, 클라이언트를 인증하고, 클라이언트를 일부 그룹에 가입할 수 있습니다.
2. 클라이언트가 성공적으로 연결되면 서비스가 `connected` 이벤트 처리기를 호출합니다. 그러면 알림으로 작동하며 클라이언트에서 메시지를 보내는 것을 차단하지 않습니다. 사용자는 이 처리기를 사용하여 일부 데이터 스토리지를 수행하고 클라이언트에 보내는 메시지로 응답할 수 있습니다.
2. 클라이언트가 메시지를 보낼 때 서비스는 전송된 메시지를 처리하기 위해 이벤트 처리기(서버)에 대한 `message` 이벤트를 트리거합니다. 이 이벤트는 WebSocket 프레임에서 보낸 메시지를 포함하는 일반 이벤트입니다. 사용자는 이 이벤트 처리기 내에서 메시지를 직접 디스패치해야 합니다.
3. 클라이언트가 연결이 끊기면 서비스는 연결 끊김 감지 시 이벤트 처리기(서버)에 `disconnected` 이벤트를 트리거하려고 시도합니다.

이 이벤트는 다음과 같은 두 가지 범주로 나뉩니다.
* 동기 이벤트(차단) 동기 이벤트는 클라이언트 워크플로를 차단합니다. 해당 이벤트 트리거가 실패하면 서비스는 클라이언트 연결을 끊습니다.
    * `connect`
    * `message`
* 비동기 이벤트(비차단) 비동기 이벤트는 클라이언트 워크플로를 차단하지 않으며 업스트림 이벤트 처리기에 대한 알림 역할을 합니다. 해당 이벤트 트리거가 실패하면 서비스는 오류 세부 정보를 기록합니다.
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>시나리오:
해당 연결은 클라이언트가 서버에 메시지를 보내고 서버가 [이벤트 처리기](#event_handler)를 사용하여 수신 메시지를 처리하는 일반적인 클라이언트-서버 아키텍처에서 사용할 수 있습니다. 고객이 애플리케이션 로직에 기존 [하위 프로토콜](https://www.iana.org/assignments/websocket/websocket.xml)을 적용할 때도 사용할 수 있습니다.

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 클라이언트
이 서비스는 클라이언트가 업스트리밍 서버로의 왕복 대신 직접 게시/구독을 수행할 수 있도록 하는 `json.webpubsub.azure.v1`이라는 특정 하위 프로토콜도 지원합니다. `json.webpubsub.azure.v1` 하위 프로토콜을 사용하는 WebSocket 연결을 PubSub WebSocket 클라이언트라고 합니다.

예를 들어 JS에서 다음을 사용하여 PubSub WebSocket 클라이언트를 만들 수 있습니다.
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

PubSub WebSocket 클라이언트는 다음을 수행할 수 있습니다.
* 그룹에 가입합니다. 예를 들면 다음과 같습니다.
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* 그룹에서 탈퇴합니다. 예를 들면 다음과 같습니다.
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* 그룹에 메시지를 게시합니다. 예를 들면 다음과 같습니다.
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* 업스트림 서버에 사용자 지정 이벤트를 보냅니다. 예를 들면 다음과 같습니다.

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

[PubSub WebSocket 하위 프로토콜](./reference-json-webpubsub-subprotocol.md)에는 `json.webpubsub.azure.v1` 하위 프로토콜의 세부 정보가 포함되어 있습니다.

[단순 WebSocket 클라이언트](#simple_client)의 경우 *서버* 가 클라이언트의 이벤트를 처리하기 위한 필수 역할임을 알 수 있습니다. 항상 단순 WebSocket 연결은 메시지를 보낼 때 `message` 이벤트를 트리거하며 서버 측에 의존하여 메시지를 처리하고 다른 작업을 수행합니다. `json.webpubsub.azure.v1` 하위 프로토콜을 사용하면 권한 부여된 클라이언트가 그룹에 가입하고 그룹에 직접 메시지를 게시할 수 있습니다. 또한 메시지가 속한 *이벤트* 를 사용자 지정하여 다른 업스트림(이벤트 처리기)으로 메시지를 라우팅할 수 있습니다. 

#### <a name="scenarios"></a>시나리오:
이러한 클라이언트는 클라이언트가 서로 대화하려고 할 때 사용할 수 있습니다. 메시지는 `client1`에서 서비스로 전송되고, 클라이언트에 해당 권한이 있는 경우 서비스에서 `client2`에 직접 메시지를 전달합니다.

Client1:

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

Client2:

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

위의 예제에서 볼 수 있듯이 `client2`는 `client1`이 있는 `Group1`에 메시지를 게시하여 `client1`에 직접 데이터를 보냅니다.

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>클라이언트 메시지 한도
하나의 WebSocket 프레임에 허용되는 최대 메시지 크기는 **1MB** 입니다.

<a name="client_auth"></a>

### <a name="client-auth"></a>클라이언트 인증

#### <a name="auth-workflow"></a>인증 워크플로

클라이언트는 서명된 JWT 토큰을 사용하여 서비스에 연결합니다. 수신 클라이언트의 `connect` 이벤트 처리기인 경우 업스트림에서 클라이언트를 거부할 수도 있습니다. 이벤트 처리기는 웹후크 응답에서 클라이언트의 `userId` 및 `role`을 지정하여 클라이언트를 인증하거나 401을 사용하여 클라이언트를 거부합니다. [이벤트 처리기](#event_handler) 섹션에서 자세히 설명합니다.

아래 그래프는 워크플로를 설명합니다.

![클라이언트 인증 워크플로를 보여 주는 다이어그램](./media/concept-service-internals/client-connect-workflow.png)

PubSub WebSocket 클라이언트를 설명할 때 알 수 있듯이 클라이언트는 *승인* 된 경우에만 다른 클라이언트에 게시할 수 있습니다. 클라이언트의 `role`에 따라 클라이언트에 있는 *초기* 권한이 결정됩니다.

| 역할 | 사용 권한 |
|---|---|
| 지정되지 않음 | 클라이언트에서 이벤트를 보낼 수 있습니다.
| `webpubsub.joinLeaveGroup` | 클라이언트는 모든 그룹에 가입/탈퇴할 수 있습니다.
| `webpubsub.sendToGroup` | 클라이언트는 모든 그룹에 메시지를 게시할 수 있습니다.
| `webpubsub.joinLeaveGroup.<group>` | 클라이언트는 `<group>` 그룹에 가입하거나 탈퇴할 수 있습니다.
| `webpubsub.sendToGroup.<group>` | 클라이언트는 `<group>` 그룹에 메시지를 게시할 수 있습니다.

서버 측에서는 이후 섹션에서 설명하는 대로 [서버 프로토콜](#connection_manager)을 통해 동적으로 클라이언트의 권한을 부여하거나 취소할 수도 있습니다.

<a name="server_protocol"></a>

## <a name="server-protocol"></a>서버 프로토콜

서버 프로토콜에서는 서버에서 클라이언트 연결 및 그룹을 관리하는 기능을 제공합니다.

일반적으로 서버 프로토콜에는 다음 두 가지 역할이 포함되어 있습니다.
1. [이벤트 처리기](#event_handler)
2. [Connection manager](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>이벤트 처리기
이벤트 처리기는 수신 클라이언트 이벤트를 처리합니다. 이벤트 처리기는 포털 또는 Azure CLI를 통해 서비스에 미리 등록되고 구성되므로, 클라이언트 이벤트가 트리거되면 서비스에서 이벤트 처리가 예상되는지 여부를 식별할 수 있습니다. 퍼블릭 미리 보기의 경우 `PUSH` 모드를 사용하여 이벤트 처리기를 호출합니다. 이벤트 처리기는 서버 측에서 이벤트가 트리거될 때 호출할 서비스의 액세스 가능한 퍼블릭 엔드포인트를 노출합니다. **웹후크** 역할을 합니다. 

서비스는 [CloudEvents HTTP 프로토콜](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)을 사용하여 업스트리밍 웹후크에 클라이언트 이벤트를 전달합니다.

모든 이벤트에 대해 등록된 업스트리밍에 대한 HTTP POST 요청을 공식화하고 HTTP 응답을 예상합니다. 

서비스에서 서버로 보내는 데이터는 항상 CloudEvents `binary` 형식입니다.

![Web PubSub 서비스 이벤트 푸시 모드를 보여 주는 다이어그램](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>업스트림 및 유효성 검사

이벤트 처리기는 포털 또는 Azure CLI를 통해 서비스에 미리 등록되고 구성되어야 하므로, 클라이언트 이벤트가 트리거되면 서비스에서 이벤트 처리가 예상되는지 여부를 식별할 수 있습니다. 퍼블릭 미리 보기의 경우 `PUSH` 모드를 사용하여 이벤트 처리기를 호출합니다. 이벤트 처리기는 서버 측에서 이벤트가 트리거될 때 호출할 서비스의 액세스 가능한 퍼블릭 엔드포인트를 노출합니다. **웹후크** **업스트림** 역할을 합니다. 

웹후크 엔드포인트를 구성할 때 URL에서 `{event}` 매개 변수를 사용하여 URL 템플릿을 정의할 수 있습니다. 서비스는 클라이언트 요청이 들어올 때 웹후크 URL의 값을 동적으로 계산합니다. 예를 들어 허브 `chat`에 맞게 구성된 이벤트 처리기 URL 패턴 `http://host.com/api/{event}`와 함께 요청 `/client/hubs/chat`가 들어오면 클라이언트가 연결할 때 먼저 이 URL `http://host.com/api/connect`에 POST합니다. 이는 PubSub WebSocket 클라이언트가 사용자 지정 이벤트를 보낼 때 유용할 수 있습니다. 즉, 이벤트 처리기가 다른 이벤트를 다른 업스트림으로 전달하는 데 도움이 됩니다. URL 도메인 이름에는 `{event}` 매개 변수가 허용되지 않습니다.

Azure Portal 또는 CLI를 통해 이벤트 처리기 업스트림을 설정할 때 서비스는 [CloudEvents 남용 방지](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)를 따라 업스트림 웹후크의 유효성을 검사합니다. `WebHook-Request-Origin` 요청 헤더는 서비스 도메인 이름 `xxx.webpubsub.azure.com`으로 설정되고 헤더 `WebHook-Allowed-Origin`가 있는 응답에 이 도메인 이름이 포함될 것으로 예상합니다.

유효성 검사를 수행할 때 `{event}` 매개 변수는 `validate`로 확인됩니다. 예를 들어 URL을 `http://host.com/api/{event}`로 설정하려고 하면 서비스에서 `http://host.com/api/validate`에 대한 요청을 **OPTIONS** 하려고 하고, 응답이 유효한 경우에만 구성을 제대로 설정할 수 있습니다.

현재 [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) 및 [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback)은 지원되지 않습니다.

#### <a name="authentication-between-service-and-webhook"></a>서비스와 웹후크 간 인증
- 익명 모드
- 구성된 웹후크 URL을 통해 `code`가 제공되는 단순 인증입니다.
- AAD 인증. 
   - AAD의 [앱 등록]에 클라이언트 암호를 추가하고 portal/cli를 통해 Azure Web PubSub에 [클라이언트 암호]를 제공합니다.
   - portal/cli를 통해 Azure Web PubSub에 [ID](../app-service/overview-managed-identity.md?tabs=dotnet)를 제공합니다.

<a name="connection_manager"></a>

### <a name="connection-manager"></a>ODBC 대상 편집기

서버는 기본적으로 권한이 있는 사용자입니다. *이벤트 처리기 역할* 의 도움으로 서버는 클라이언트의 메타데이터(예: `connectionId` 및 `userId`)를 알고 있으므로 다음을 수행할 수 있습니다.
   - 클라이언트 연결 닫기
   - 클라이언트에 메시지 보내기
   - 동일한 사용자에게 속한 클라이언트에 메시지 보내기
   - 그룹에 클라이언트 추가
   - 동일한 사용자로 인증된 클라이언트를 그룹에 추가
   - 그룹에서 클라이언트 제거
   - 그룹에서 동일한 사용자로 인증된 클라이언트 제거
   - 그룹에 메시지 게시

PubSub 클라이언트에 대한 게시/가입 권한도 부여하거나 취소할 수 있습니다.
   - 특정 그룹 또는 모든 그룹에 가입/게시 사용 권한 부여
   - 특정 그룹 또는 모든 그룹에 대한 가입/게시 권한 취소
   - 클라이언트에 특정 그룹 또는 모든 그룹에 가입/게시할 수 있는 권한이 있는지 확인합니다.
   
퍼블릭 미리 보기의 경우 서비스에서는 서버에서 연결 관리를 수행하기 위한 REST API를 제공합니다.

![Web PubSub 서비스 연결 관리자 워크플로를 보여 주는 다이어그램입니다.](./media/concept-service-internals/manager-rest.png)

자세한 REST API 프로토콜은 [여기][rest]에 정의되어 있습니다.

### <a name="summary"></a>요약
*이벤트 처리기 역할* 은 서비스에서 서버로의 통신을 처리하고 *관리자 역할* 은 서버에서 서비스로의 통신을 처리한다는 사실을 알고 있습니다. 따라서 두 가지 역할을 결합하면 서비스와 서버 간의 데이터 흐름은 HTTP 프로토콜을 사용하여 아래와 비슷하게 보입니다.

![Web PubSub 서비스 양방향 워크플로를 보여 주는 다이어그램입니다.](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]