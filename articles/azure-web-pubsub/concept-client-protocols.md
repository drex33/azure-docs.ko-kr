---
title: Azure Web PubSub에 대한 WebSocket 클라이언트 프로토콜
description: 이 문서에서는 Azure 웹 PubSub의 클라이언트 프로토콜에 대 한 개요를 제공 합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 58fef1f2ada19af2ff81ef27d5ff5ab2ad4cebf7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997952"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub에 대한 WebSocket 클라이언트 프로토콜

클라이언트는 표준 [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) 프로토콜을 사용 하 여 Azure 웹 pubsub에 연결 합니다.

## <a name="service-endpoints"></a>서비스 엔드포인트
웹 PubSub 서비스는 클라이언트가 연결할 수 있는 두 가지 유형의 끝점을 제공 합니다.
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` 는 다양 한 응용 프로그램에 대 한 격리 역할을 하는 필수 매개 변수입니다. 경로 또는 쿼리에서이를 설정할 수 있습니다.

## <a name="authorization"></a>권한 부여

클라이언트는 JSON Web Token (JWT)를 사용 하 여 서비스에 연결 합니다. 토큰은 쿼리 문자열, as 또는 헤더 중 하나에 있을 수 있습니다 `/client/?hub={hub}&access_token={token}` `Authorization` `Authorization: Bearer {token}` .

일반적인 권한 부여 워크플로는 다음과 같습니다.

1. 클라이언트는 응용 프로그램 서버와 협상 합니다. 응용 프로그램 서버에는 클라이언트 요청을 처리 하 고 클라이언트에서 서비스에 연결 하기 위해 JWT에 서명 하는 권한 부여 미들웨어가 포함 되어 있습니다.
1. 응용 프로그램 서버는 JWT와 서비스 URL을 클라이언트에 반환 합니다.
1. 클라이언트는 응용 프로그램 서버에서 반환 되는 URL 및 JWT를 사용 하 여 웹 PubSub 서비스에 연결을 시도 합니다.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>단순 WebSocket 클라이언트

단순한 **websocket 클라이언트** 는 이름 지정으로, 간단한 websocket 연결입니다. 고유한 사용자 지정 하위 프로토콜을 포함할 수도 있습니다. 

예를 들어 JavaScript에서 다음 코드를 사용 하 여 간단한 WebSocket 클라이언트를 만들 수 있습니다.

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 클라이언트

**Pubsub websocket 클라이언트** 는 Azure 웹 pubsub 서비스에서 정의 된 하위 프로토콜을 사용 하는 WebSocket 클라이언트입니다.

* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

서비스 지원 하위 프로토콜을 사용 하는 경우 **Pubsub WebSocket 클라이언트** 는 [권한이](#permissions)있는 경우 그룹에 직접 메시지를 게시할 수 있습니다.

### <a name="the-jsonwebpubsubazurev1-subprotocol"></a>하위 `json.webpubsub.azure.v1` 프로토콜

[JSON 하위 프로토콜에 대 한 자세한 내용은 여기](reference-json-webpubsub-subprotocol.md)를 참조 하세요.

#### <a name="create-a-pubsub-websocket-client"></a>PubSub WebSocket 클라이언트 만들기

```js
var pubsubClient = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="join-a-group-from-the-client-directly"></a>클라이언트에서 직접 그룹 조인

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'joinGroup',
        group: 'group1',
        ackId: ++ackId
    }));
```

#### <a name="send-messages-to-a-group-from-the-client-directly"></a>클라이언트에서 직접 그룹에 메시지 보내기

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'sendToGroup',
        group: 'group1',
        ackId: ++ackId,
        dataType: "json",
        data: {
            "hello": "world"
        }
    }));
```

### <a name="the-protobufwebpubsubazurev1-subprotocol"></a>하위 `protobuf.webpubsub.azure.v1` 프로토콜

프로토콜 버퍼 (protobuf)는 이진 데이터를 쉽게 보낼 수 있도록 하는 언어 중립적인 플랫폼 중립적 이진 기반 프로토콜입니다. Protobuf는 Java, Python, 객관적인 C, c # 및 c + +와 같은 여러 언어의 클라이언트를 생성 하는 도구를 제공 합니다. [Protobuf에 대해 자세히 알아보세요](https://developers.google.com/protocol-buffers).

예를 들어 JavaScript에서 다음 코드를 사용 하 여 protobuf 하위 프로토콜을 사용 하 여 **Pubsub WebSocket 클라이언트** 를 만들 수 있습니다.

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

[Protobuf 하위 프로토콜에 대 한](reference-protobuf-webpubsub-subprotocol.md)자세한 내용을 확인 하세요.


### <a name="ackid-and-ack-response"></a>AckId 및 Ack 응답

**Pubsub WebSocket 클라이언트** 는 `ackId` , 및 메시지에 대 한 속성 `joinGroup` 을 지원 `leaveGroup` `sendToGroup` `event` 합니다. 를 사용 하는 경우 `ackId` 요청이 처리 될 때 ack 응답 메시지를 수신할 수 있습니다. `ackId`화재 및 잊은 시나리오에서 생략 하도록 선택할 수 있습니다. 이 문서에서는를 지정 하는 경우의 동작 차이점에 대해 설명 합니다 `ackId` .

#### <a name="behavior-when-no-ackid-specified"></a>지정 되지 않은 경우의 동작 `ackId`

`ackId`을 지정 하지 않은 경우에는 발생 하 고이를 잊지 마십시오. 메시지를 중개 하는 경우에도 오류가 발생 하 여 알림 메시지를 받을 수 있습니다.

#### <a name="behavior-when-ackid-specified"></a>지정 된 경우의 동작 `ackId`

##### <a name="idempotent-publish"></a>Idempotent 게시

`ackId` 는 uint64 숫자 이며 동일한 연결 id를 사용 하는 클라이언트 내에서 고유 해야 합니다. 웹 PubSub 서비스는를 기록 `ackId` 하 고 같은 메시지는 동일한 `ackId` 메시지로 처리 됩니다. 서비스는 동일한 메시지를 두 번 이상 broker를 거부 하므로 중복 된 메시지를 방지 하기 위해 다시 시도 하는 데 유용 합니다. 예를 들어 클라이언트가를 사용 하 여 메시지를 보내고에서 `ackId=5` ack 응답을 받지 못하는 경우 `ackId=5` 클라이언트는 다시 시도 하 여 동일한 메시지를 다시 보냅니다. 일부 경우에는 메시지를 이미 조정 했 고 ack 응답이 어떤 이유로 손실 된 경우 서비스에서 재시도를 거부 하 고 이유로 ack 응답을 응답 합니다 `Duplicate` .

#### <a name="ack-response"></a>Ack 응답

웹 PubSub 서비스는를 사용 하 여 각 요청에 대 한 ack 응답을 보냅니다 `ackId` .

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

* 는 `ackId` 요청을 연결 합니다.

* `success` 는 bool 이며 서비스에서 요청을 성공적으로 처리 했는지 여부를 나타냅니다. 그렇다면 클라이언트는를 `false` 확인 해야 합니다 `error` .

* `error``success`가이 `false` 고 클라이언트에 다른 논리가 있는 경우에만 존재 `name` 합니다. 나중에 더 많은 형식이 있을 수 있습니다 `name` .
    - `Forbidden`: 클라이언트에 요청에 대 한 권한이 없습니다. 클라이언트에 관련 역할을 추가 해야 합니다.
    - `InternalServerError`: 서비스에서 내부 오류가 발생 했습니다. 다시 시도 해야 합니다.
    - `Duplicate`:가 같은 메시지를 `ackId` 서비스에서 이미 처리 했습니다.

### <a name="permissions"></a>사용 권한

이전 PubSub WebSocket 클라이언트 설명에서 볼 수 있듯이 클라이언트는이에 대 한 *권한이 있는* 경우에만 다른 클라이언트에 게시할 수 있습니다. 클라이언트의 사용 권한은 연결 될 때 또는 연결 수명 동안 부여할 수 있습니다.

| 역할 | 사용 권한 |
|---|---|
| 지정되지 않음 | 클라이언트는 이벤트 요청을 보낼 수 있습니다. |
| `webpubsub.joinLeaveGroup` | 클라이언트는 모든 그룹에 참가 하거나 탈퇴할 수 있습니다. |
| `webpubsub.sendToGroup` | 클라이언트는 모든 그룹에 메시지를 게시할 수 있습니다. |
| `webpubsub.joinLeaveGroup.<group>` | 클라이언트는 그룹에 참가 하거나 그룹을 벗어날 수 있습니다 `<group>` . |
| `webpubsub.sendToGroup.<group>` | 클라이언트는 `<group>` 그룹에 메시지를 게시할 수 있습니다. |
| | |

클라이언트의 권한은 다음과 같은 여러 가지 방법으로 부여할 수 있습니다.

#### <a name="1-assign-the-role-to-the-client-when-generating-the-access-token"></a>1. 액세스 토큰을 생성할 때 클라이언트에 역할을 할당 합니다.

클라이언트는 JWT 토큰을 사용 하 여 서비스에 연결할 수 있습니다. 그러면 토큰 페이로드가 클라이언트의와 같은 정보를 전달할 수 있습니다 `role` . JWT 토큰을 클라이언트에 서명 하는 경우 클라이언트에 특정 역할을 부여 하 여 클라이언트에 권한을 부여할 수 있습니다.

예를 들어 및로 메시지를 보낼 수 있는 권한이 있는 JWT 토큰에 서명 하겠습니다 `group1` `group2` . 

```js
let token = await serviceClient.getClientAccessToken({
    roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
});
```

#### <a name="2-assign-the-role-to-the-client-with-the-connect-event-handler"></a>2. 이벤트 처리기를 사용 하 여 클라이언트에 역할을 할당 합니다. `connect`

`connect`이벤트 처리기가 등록 되 고 업스트림 이벤트 처리기가 `roles` 이벤트를 처리할 때 클라이언트의를 웹 pubsub 서비스에 반환할 수 있는 경우에도 클라이언트의 역할을 설정할 수 있습니다 `connect` .

예를 들어 JavaScript에서 이벤트를 구성 하 여 `handleConnect` 다음과 같은 작업을 수행할 수 있습니다.

```js
let handler = new WebPubSubEventHandler("hub1", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
    });
  },
});
```

#### <a name="3-assign-the-role-to-the-client-through-rest-apis-or-server-sdks-during-runtime"></a>3. 런타임 중 REST Api 또는 서버 Sdk를 통해 클라이언트에 역할을 할당 합니다.

```js
let service = new WebPubSubServiceClient("<your_connection_string>", "test-hub");
await service.grantPermission("<connection_id>", "joinLeaveGroup", { targetName: "group1" });
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
