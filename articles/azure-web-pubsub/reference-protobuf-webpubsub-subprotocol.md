---
title: 참조 - Azure Web PubSub 지원 protobuf WebSocket 하위 프로토콜 `protobuf.webpubsub.azure.v1`
description: 이 참조는 Azure Web PubSub 지원 WebSocket 하위 프로토콜 에 대해 `protobuf.webpubsub.azure.v1` 설명합니다.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 8e801391e5ac2ebe2a4dee276f525885ff84f449
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997802"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Azure Web PubSub 지원 protobuf WebSocket 하위 프로토콜
     
이 문서에서는 하위 프로토콜 `protobuf.webpubsub.azure.v1`을 설명합니다.

클라이언트가 이 하위 프로토콜을 사용하는 경우 나가는 데이터 프레임과 들어오는 데이터 프레임은 모두 프로토콜 버퍼(protobuf) 페이로드여야 합니다.

## <a name="overview"></a>개요

Subprotocol을 `protobuf.webpubsub.azure.v1` 사용하면 클라이언트가 업스트림 서버로 왕복하는 대신 게시-구독(PubSub)을 직접 수행할 수 있습니다. 하위 프로토콜과의 WebSocket 연결을 `protobuf.webpubsub.azure.v1` PubSub WebSocket 클라이언트라고 합니다.

예를 들어 JavaScript에서는 다음을 사용하여 protobuf 하위 프로토콜을 사용하여 PubSub WebSocket 클라이언트를 만들 수 있습니다.

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

간단한 WebSocket 클라이언트의 경우 서버는 클라이언트에서 이벤트를 처리하는 *데 필요한* 역할을 합니다. 간단한 WebSocket 연결은 메시지를 보낼 때 항상 `message` 이벤트를 트리거하며, 항상 서버 쪽을 사용하여 메시지를 처리하고 다른 작업을 수행합니다. 하위 프로토콜의 도움을 받아 권한 있는 `protobuf.webpubsub.azure.v1` 클라이언트는 [조인 요청을](#join-groups) 사용하여 그룹에 조인하고 게시 요청을 직접 사용하여 그룹에 메시지를 게시할 수 [있습니다.](#publish-messages) 클라이언트는 이벤트 [요청을](#send-custom-events) 사용하여 메시지가 속한 이벤트를 사용자 지정하여 메시지를 다양한 업스트림 *이벤트* 처리기로 라우팅할 수도 있습니다.

> [!NOTE]
> 현재 Web PubSub 서비스는 [proto3](https://developers.google.com/protocol-buffers/docs/proto3)만 지원합니다.

## <a name="permissions"></a>사용 권한

PubSub WebSocket 클라이언트에 대한 이전 설명에서 클라이언트는 권한이 *있는* 경우에만 다른 클라이언트에 게시할 수 있음을 알 수 있습니다. 클라이언트의 역할은 다음 표에 나열된 대로 *초기* 사용 권한을 결정합니다.

| 역할 | 사용 권한 |
|---|---|
| 지정되지 않음 | 클라이언트는 이벤트 요청을 보낼 수 있습니다. |
| `webpubsub.joinLeaveGroup` | 클라이언트는 모든 그룹에 조인하거나 나갈 수 있습니다. |
| `webpubsub.sendToGroup` | 클라이언트는 모든 그룹에 메시지를 게시할 수 있습니다. |
| `webpubsub.joinLeaveGroup.<group>` | 클라이언트는 그룹을 조인하거나 나갈 수 `<group>` 있습니다. |
| `webpubsub.sendToGroup.<group>` | 클라이언트는 `<group>` 그룹에 메시지를 게시할 수 있습니다. |
| | |

또한 서버 쪽에서는 REST API 또는 서버 SDK를 통해 클라이언트의 권한을 동적으로 부여하거나 취소할 수 있습니다.

## <a name="requests"></a>요청

모든 요청 메시지는 다음 protobuf 형식을 준수합니다.

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
        optional uint64 ack_id = 3;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>그룹 가입

형식:

`join_group_message.group`그룹 이름으로 설정합니다.

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

### <a name="leave-groups"></a>그룹 탈퇴

형식:

`leave_group_message.group`그룹 이름으로 설정합니다.

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

### <a name="publish-messages"></a>메시지 게시

형식:

* `ackId` 는 각 요청의 ID이며 고유해야 합니다. 서비스는 요청의 프로세스 결과를 알리기 위해 [ack 응답 메시지를](#ack-response) 보냅니다. 자세한 내용은 [AckId 및 Ack 응답에서](./concept-client-protocols.md#ackid-and-ack-response) 찾을 수 있습니다.

설정한 의 에 따라 , 또는 일 수 있는 암시적 가 `dataType` `protobuf` `text` `binary` `data` `MessageData` 있습니다. 수신기 클라이언트는 를 사용하여 콘텐츠를 올바르게 처리할 수 `dataType` 있습니다.

* `protobuf`: 를 설정하는 경우 `send_to_group_message.data.protobuf_data` 암시적 `dataType` 는 `protobuf` 입니다. `protobuf_data` 는 [모든](https://developers.google.com/protocol-buffers/docs/proto3#any) 메시지 유형일 수 있습니다. 다른 모든 클라이언트는 protobuf로 인코딩된 이진 파일을 수신하며 protobuf SDK를 통해 deserialized할 수 있습니다. 텍스트 기반 콘텐츠(예: )만 지원하는 클라이언트는 `json.webpubsub.azure.v1` Base64로 인코딩된 이진을 받습니다.

* `text`: 를 설정하는 경우 `send_to_group_message.data.text_data` 암시적 `dataType` 는 `text` 입니다. `text_data` 는 문자열이어야 합니다. 다른 프로토콜을 사용하는 모든 클라이언트는 UTF-8로 인코딩된 문자열을 받습니다.

* `binary`: 를 설정하는 경우 `send_to_group_message.data.binary_data` 암시적 `dataType` 는 `binary` 입니다. `binary_data` 는 바이트 배열이어야 합니다. 다른 프로토콜을 가진 모든 클라이언트는 protobuf 인코딩 없이 원시 이진을 수신합니다. 텍스트 기반 콘텐츠(예: )만 지원하는 클라이언트는 `json.webpubsub.azure.v1` Base64로 인코딩된 이진을 받습니다.

#### <a name="case-1-publish-text-data"></a>사례 1: 텍스트 데이터 게시

`send_to_group_message.group`를 `group` 로 설정하고 를 `send_to_group_message.data.text_data` 로 `"text data"` 설정합니다.

* 그룹의 protobuf 하위 프로토콜 클라이언트는 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있습니다.

* 그룹의 JSON 하위 프로토콜 클라이언트는 `group` 다음을 수신합니다.

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* 그룹의 원시 클라이언트는 `group` 문자열 을 받습니다. `text data`

#### <a name="case-2-publish-protobuf-data"></a>사례 2: protobuf 데이터 게시

고객 메시지가 있다고 가정해 보겠습니다.

```
message MyMessage {
    int32 value = 1;
}
```

를 로 설정하고 `send_to_group_message.group` `group` 를 로 `send_to_group_message.data.protobuf_data` `Any.pack(MyMessage)` `value = 1` 설정합니다.

* 그룹의 protobuf 하위 프로토콜 클라이언트는 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있습니다.

* 그룹의 하위 프로토콜 클라이언트는 `group` 다음을 받습니다.

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > 데이터는 Base64로 인코딩되고 deserialize 가능한 protobuf 이진 파일입니다. 

다음 protobuf 정의를 사용하고 를 사용하여 `Any.unpack()` deserialize할 수 있습니다.

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* 그룹의 원시 클라이언트는 `group` 이진 프레임을 받습니다.

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>사례 3: 이진 데이터 게시

`send_to_group_message.group`를 `group` 로 설정하고 를 `send_to_group_message.data.binary_data` 로 `[1, 2, 3]` 설정합니다.

* 그룹의 protobuf 하위 프로토콜 클라이언트는 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있습니다.

* 그룹의 JSON 하위 프로토콜 클라이언트는 `group` 다음을 수신합니다.

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    JSON 하위 프로토콜 클라이언트는 텍스트 기반 메시징만 지원하므로 이진 파일은 항상 Base64로 인코딩됩니다.

* 그룹의 원시 클라이언트는 `group` 이진 프레임의 이진 데이터를 받습니다.

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>사용자 지정 이벤트 보내기

설정한 에 따라 , 또는 일 수 있는 암시적 `dataType` `protobuf` 가 `text` `binary` `dataType` 있습니다. 수신기 클라이언트는 를 사용하여 콘텐츠를 올바르게 처리할 수 `dataType` 있습니다.

* `protobuf`: 를 설정하는 경우 `event_message.data.protobuf_data` 암시적 `dataType` 는 `protobuf` 입니다. `protobuf_data` 는 지원되는 모든 protobuf 형식일 수 있습니다. 이벤트 처리기는 protobuf로 인코딩된 이진 파일을 수신하며, 모든 protobuf SDK에서 deserialized할 수 있습니다.

* `text`: 를 설정하는 경우 `event_message.data.text_data` 암시적 는 `text` 입니다. `text_data` 는 문자열이어야 합니다. 이벤트 처리기는 UTF-8로 인코딩된 문자열을 받습니다.

* `binary`: 를 설정하는 경우 `event_message.data.binary_data` 암시적 는 `binary` 입니다. `binary_data` 는 바이트 배열이어야 합니다. 이벤트 처리기는 원시 이진 프레임을 받습니다.

#### <a name="case-1-send-an-event-with-text-data"></a>사례 1: 텍스트 데이터가 있는 이벤트 보내기

`event_message.data.text_data`를 `"text data"`로 설정합니다.

업스트림 이벤트 처리기는 다음과 유사한 요청을 받습니다. `Content-Type`CloudEvents HTTP 요청의 경우 는 `text/plain` 입니다. 여기서 `dataType` = `text` 입니다.

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

#### <a name="case-2-send-an-event-with-protobuf-data"></a>사례 2: protobuf 데이터를 통해 이벤트 보내기

다음 고객 메시지를 수신했다고 가정합니다.

```
message MyMessage {
    int32 value = 1;
}
```

`event_message.data.protobuf_data`다음을 사용하여 로 설정 `any.pack(MyMessage)``value = 1`

업스트림 이벤트 처리기는 다음과 유사한 요청을 받습니다. `Content-Type`CloudEvents HTTP 요청에 대한 는 `application/x-protobuf` 입니다. 여기서 `dataType` = `protobuf` 입니다.

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

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

데이터는 유효한 protobuf 이진 파일입니다. 다음 및 를 사용하여 `proto` `any.unpack()` 이를 deserialize할 수 있습니다.

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>사례 3: 이진 데이터를 사용 하 여 이벤트 보내기

`send_to_group_message.binary_data`를 `[1, 2, 3]`로 설정합니다.

업스트림 이벤트 처리기는 다음과 유사한 요청을 받습니다. 의 경우 `dataType` = `binary` `Content-Type` CloudEvents HTTP 요청에 대 한는 `application/octet-stream` 입니다. 

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

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

WebSocket 프레임은 `text` 메시지 프레임에 대 한 문자 메시지 프레임 또는 u t f-8로 인코딩된 이진 파일의 형식일 수 있습니다 `binary` .

메시지가 지정 된 형식과 일치 하지 않으면 서비스에서 클라이언트를 거부 합니다.

## <a name="responses"></a>응답

모든 응답 메시지는 다음 protobuf 형식을 따릅니다.

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        uint64 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

클라이언트에서 받은 메시지는 `ack` , 또는의 세 가지 유형 중 하나일 수 `message` `system` 있습니다. 

### <a name="ack-response"></a>ack 응답

요청에가 포함 되어 있으면 `ackId` 서비스는이 요청에 대 한 ack 응답을 반환 합니다. 클라이언트 구현에서는 다음을 포함 하 여이 ack 메커니즘을 처리 해야 합니다.
* 작업에 대 한 ack 응답을 기다리는 중 `async` `await` 입니다. 
* 특정 기간 동안 ack 응답이 수신 되지 않으면 시간 제한이 있습니다.

클라이언트 구현은 항상 먼저를 확인 하 여 상태가 또는 인지 확인 해야 합니다 `success` `true` `false` . `success`상태가 이면 `false` 클라이언트는 속성에서 오류 정보를 읽을 수 있습니다 `error` .

### <a name="message-response"></a>메시지 응답

클라이언트는 연결 된 그룹에서 게시 된 메시지를 받을 수 있습니다. 또는 서버에서 특정 클라이언트 또는 특정 사용자에 게 메시지를 보낼 때 서버 관리 역할에서 메시지를 받을 수 있습니다.

`DownstreamMessage.DataMessage`다음 시나리오에서 항상 메시지를 받게 됩니다.

- 메시지를 그룹에서 가져온 경우 `from` 은 `group` 입니다. 서버에서 보낸 메시지는입니다 `from` `server` .
- 메시지를 그룹에서 가져온 경우 `group` 는 그룹 이름입니다.

보낸 사람의는 `dataType` 다음 메시지 중 하나를 전송 합니다. 
* 가 이면를 `dataType` `text` 사용 `message_response_message.data.text_data` 합니다. 
* 가 이면를 `dataType` `binary` 사용 `message_response_message.data.binary_data` 합니다. 
* 가 이면를 `dataType` `protobuf` 사용 `message_response_message.data.protobuf_data` 합니다. 
* `dataType`가 이면 `json` 를 사용 `message_response_message.data.text_data` 하 고 콘텐츠는 serialize 된 JSON 문자열입니다.

### <a name="system-response"></a>시스템 응답

Web PubSub 서비스에서는 시스템 관련 응답을 클라이언트에 보낼 수도 있습니다. 

#### <a name="connected"></a>연결됨

클라이언트는 서비스에 연결할 때 메시지를 받게 `DownstreamMessage.SystemMessage.ConnectedMessage` 됩니다.

#### <a name="disconnected"></a>연결 끊김

서버에서 연결을 닫거나 서비스가 클라이언트를 거부 하는 경우 메시지를 받게 `DownstreamMessage.SystemMessage.DisconnectedMessage` 됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
