---
title: 참조 - Azure Web PubSub 지원 JSON WebSocket 하위 프로토콜 `json.webpubsub.azure.v1`
description: 참조에서는 Azure Web PubSub 지원 WebSocket 하위 프로토콜 `json.webpubsub.azure.v1`을 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: 2208c41ea49bae4ad3791a7e26f694e4cf4fbbd3
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997859"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Azure Web PubSub 지원 JSON WebSocket 하위 프로토콜
     
이 문서에서는 하위 프로토콜 `json.webpubsub.azure.v1`을 설명합니다.

클라이언트가 이 하위 프로토콜을 사용할 때 나가는 데이터 프레임과 들어오는 데이터 프레임 모두 **JSON** 페이로드가 될 것으로 예상됩니다.

## <a name="overview"></a>개요

하위 프로토콜 `json.webpubsub.azure.v1`을 사용하면 클라이언트가 왕복하는 것이 아니라 업스트림 서버로 직접 게시/구독을 수행할 수 있습니다. `json.webpubsub.azure.v1` 하위 프로토콜을 사용하는 WebSocket 연결을 PubSub WebSocket 클라이언트라고 합니다.

예를 들어 JS에서 다음을 사용하여 PubSub WebSocket 클라이언트를 만들 수 있습니다.
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
단순 WebSocket 클라이언트의 경우 *서버* 는 클라이언트의 이벤트를 처리하기 위해 반드시 있어야 하는 역할입니다. 항상 단순 WebSocket 연결은 메시지를 보낼 때 `message` 이벤트를 트리거하며 서버 측에 의존하여 메시지를 처리하고 다른 작업을 수행합니다. `json.webpubsub.azure.v1` 하위 프로토콜을 사용하면 권한 부여된 클라이언트가 [가입 요청](#join-groups)을 사용하여 그룹에 가입하고 [게시 요청](#publish-messages)을 사용하여 직접 메시지를 그룹에 게시할 수 있습니다. 또한 [이벤트 요청](#send-custom-events)을 사용하여 메시지가 속한 *이벤트* 를 사용자 지정하여 다른 업스트림(이벤트 처리기)으로 메시지를 라우팅할 수 있습니다.

## <a name="permissions"></a>사용 권한

PubSub WebSocket 클라이언트를 설명할 때 알 수 있듯이 클라이언트는 *승인* 된 경우에만 다른 클라이언트에 게시할 수 있습니다. 클라이언트의 `role`에 따라 클라이언트에 있는 *초기* 권한이 결정됩니다.

| 역할 | 사용 권한 |
|---|---|
| 지정되지 않음 | 클라이언트는 이벤트 요청을 보낼 수 있습니다.
| `webpubsub.joinLeaveGroup` | 클라이언트는 모든 그룹에 가입/탈퇴할 수 있습니다.
| `webpubsub.sendToGroup` | 클라이언트는 모든 그룹에 메시지를 게시할 수 있습니다.
| `webpubsub.joinLeaveGroup.<group>` | 클라이언트는 `<group>` 그룹에 가입하거나 탈퇴할 수 있습니다.
| `webpubsub.sendToGroup.<group>` | 클라이언트는 `<group>` 그룹에 메시지를 게시할 수 있습니다.

서버 측에서는 REST API 또는 서버 SDK를 통해 동적으로 클라이언트의 권한을 부여하거나 취소할 수도 있습니다.

## <a name="requests"></a>요청

### <a name="join-groups"></a>그룹 가입

형식:

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

### <a name="leave-groups"></a>그룹 탈퇴

형식:

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

### <a name="publish-messages"></a>메시지 게시

형식:

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1,
    "noEcho": true|false,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.
* `noEcho`는 선택 사항입니다. true로 설정하면 이 메시지가 동일한 연결로 다시 에코되지 않습니다. 설정하지 않으면 기본값은 false입니다.
* `dataType`은 `json`, `text`, `binary` 중 하나일 수 있습니다.
     * `json`: `data`는 JSON이 지원하는 모든 유형이 될 수 있으며 그대로 게시됩니다. `dataType`을 지정하지 않으면 기본값은 `json`입니다.
     * `text`: `data`는 문자열 형식이어야 하며 문자열 데이터가 게시됩니다.
     * `binary`: `data`는 base64 형식이어야 하며 이진 데이터가 게시됩니다.

#### <a name="case-1-publish-text-data"></a>사례 1: 텍스트 데이터 게시:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data",
    "ackId": 1
}
```

* 이 그룹 `<group_name>`의 하위 프로토콜 클라이언트는 다음을 수신합니다.
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* 이 그룹 `<group_name>`의 원시 클라이언트가 수신하는 것은 문자열 데이터 `text data`입니다.

#### <a name="case-2-publish-json-data"></a>사례 2: JSON 데이터 게시:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* 이 그룹 `<group_name>`의 하위 프로토콜 클라이언트는 다음을 수신합니다.
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* 이 그룹 `<group_name>`의 원시 클라이언트가 수신하는 것은 직렬화된 문자열 데이터 `{"hello": "world"}`입니다.


#### <a name="case-3-publish-binary-data"></a>사례 3: 이진 데이터 게시:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>",
    "ackId": 1
}
```

* 이 그룹 `<group_name>`의 하위 프로토콜 클라이언트는 다음을 수신합니다.
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* 이 그룹 `<group_name>`의 원시 클라이언트가 수신하는 것은 이진 프레임의 **이진** 데이터입니다.

### <a name="send-custom-events"></a>사용자 지정 이벤트 보내기

형식:

```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

`dataType`은 `text`, `binary`, `json` 중 하나일 수 있습니다.
* `json`: 데이터는 JSON이 지원하는 모든 유형이 될 수 있으며 있는 그대로 게시됩니다. `dataType`을 지정하지 않으면 기본값은 `json`입니다.
* `text`: 데이터는 문자열 형식이어야 하며 문자열 데이터가 게시됩니다.
* `binary`: 데이터는 base64 형식이어야 하며 이진 데이터가 게시됩니다.

#### <a name="case-1-send-event-with-text-data"></a>사례 1: 텍스트 데이터와 함께 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "text",
    "data": "text data", 
}
```

업스트리밍 이벤트 처리기가 아래와 같이 수신하는 경우 CloudEvents HTTP 요청에 대한 `Content-Type`은 `dataType`=`text`인 `text/plain`입니다.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-event-with-json-data"></a>사례 2: JSON 데이터로 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

업스트리밍 이벤트 처리기가 아래와 같이 수신하는 경우 CloudEvents HTTP 요청에 대한 `Content-Type`은 `dataType`=`json`인 `application/json`입니다.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>사례 3: 이진 데이터와 함께 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

업스트리밍 이벤트 처리기가 아래와 같이 수신하는 경우 CloudEvents HTTP 요청에 대한 `Content-Type`은 `dataType`=`binary`인 `application/octet-stream`입니다.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

binary

```

WebSocket 프레임은 텍스트 메시지 프레임의 경우 `text` 형식이거나 `binary` 메시지 프레임의 경우 UTF8로 인코딩된 이진일 수 있습니다.

메시지가 설명된 형식과 일치하지 않으면 서비스에서 클라이언트를 거부합니다.

## <a name="responses"></a>응답

클라이언트가 수신한 메시지는 `ack`, `message`, `system`과 같은 여러 유형이 될 수 있습니다. 

### <a name="ack-response"></a>ack 응답

요청에 `ackId`가 포함된 경우 서비스는 이 요청에 대한 ack 응답을 반환합니다. 클라이언트 구현은 `async` `await` 작업에 대한 ack 응답에 대기하고 특정 기간 동안 ack 응답이 수신되지 않을 때 시간 초과를 확인하는 등, 이 ack 메커니즘을 처리해야 합니다.

형식:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "Forbidden|InternalServerError|Duplicate",
        "message": "<error_detail>"
    }
}
```

클라이언트 구현에서는 먼저 `success`가 `true`인지 아니면 `false`인지 항상 확인해야 합니다. `success`가 `false`이면 클라이언트가 `error`에서 읽습니다.

### <a name="message-response"></a>메시지 응답

클라이언트는 클라이언트가 가입한 한 그룹 또는 서버가 특정 클라이언트나 특정 사용자에게 메시지를 보내는 서버 관리 역할에서 게시된 메시지를 받을 수 있습니다.

1. 메시지가 그룹에서 오는 경우

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
        "fromUserId": "abc"
    }
    ```

1. 메시지가 서버에서 오는 경우

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>사례 1: `Content-Type`=`text/plain`을 사용하여 REST API를 통해 연결에 데이터 `Hello World` 보내기 
* 단순 WebSocket 클라이언트는 데이터(`Hello World`)가 있는 텍스트 WebSocket 프레임을 받습니다.
* PubSub WebSocket 클라이언트는 다음을 받습니다.
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>사례 2: `Content-Type`=`application/json`을 사용하여 REST API를 통해 연결에 데이터 `{ "Hello" : "World"}` 보내기
* 단순 WebSocket 클라이언트는 문자열 변환 데이터(`{ "Hello" : "World"}`)가 있는 텍스트 WebSocket 프레임을 받습니다.
* PubSub WebSocket 클라이언트는 다음을 받습니다.
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

REST API가 `application/json` 콘텐츠 유형을 사용하여 `Hello World` 문자열을 보내는 경우 단순 WebSocket 클라이언트가 수신하는 것은 `"`로 문자열을 래핑하는 `"Hello World"`인 JSON 문자열입니다.

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>사례 3: `Content-Type`=`application/octet-stream`을 사용하여 REST API를 통해 연결에 이진 데이터 보내기
* 단순 WebSocket 클라이언트는 이진 데이터가 있는 이진 WebSocket 프레임을 받습니다.
* PubSub WebSocket 클라이언트는 다음을 받습니다.
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>시스템 응답

Web PubSub 서비스에서는 시스템 관련 응답을 클라이언트에 보낼 수도 있습니다. 

#### <a name="connected"></a>연결됨

연결을 서비스에 연결하는 경우

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>연결 끊김

서버에서 연결을 닫거나 서비스가 클라이언트를 거부하는 경우

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
