---
title: 참조 - Azure Web PubSub 지원 Protobuf WebSocket 하위 프로토콜 `protobuf.webpubsub.azure.v1`
description: 참조에서는 Azure Web PubSub 지원 WebSocket 하위 프로토콜 `protobuf.webpubsub.azure.v1`을 설명합니다.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 9b54f219f52c9fa19134841488fcdf7f2bbc844d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441325"
---
#  <a name="azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Azure Web PubSub 지원 Protobuf WebSocket 하위 프로토콜
     
이 문서에서는 하위 프로토콜 `protobuf.webpubsub.azure.v1`을 설명합니다.

클라이언트에서 이 하위 프로토콜을 사용하는 경우 나가는 데이터 프레임과 들어오는 데이터 프레임은 모두 **protobuf** 페이로드여야 합니다.

## <a name="overview"></a>개요

하위 프로토콜 `protobuf.webpubsub.azure.v1`을 사용하면 클라이언트가 왕복하는 것이 아니라 업스트림 서버로 직접 게시/구독을 수행할 수 있습니다. `protobuf.webpubsub.azure.v1` 하위 프로토콜을 사용하는 WebSocket 연결을 PubSub WebSocket 클라이언트라고 합니다.

예를 들어 JS에서 Protobuf 하위프로토콜이 있는 PubSub WebSocket 클라이언트는 다음을 사용하여 만들 수 있습니다.
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```
단순 WebSocket 클라이언트의 경우 *서버* 는 클라이언트의 이벤트를 처리하기 위해 반드시 있어야 하는 역할입니다. 항상 단순 WebSocket 연결은 메시지를 보낼 때 `message` 이벤트를 트리거하며 서버 측에 의존하여 메시지를 처리하고 다른 작업을 수행합니다. `protobuf.webpubsub.azure.v1` 하위 프로토콜을 사용하면 권한 부여된 클라이언트가 [가입 요청](#join-groups)을 사용하여 그룹에 가입하고 [게시 요청](#publish-messages)을 사용하여 직접 메시지를 그룹에 게시할 수 있습니다. 또한 [이벤트 요청](#send-custom-events)을 사용하여 메시지가 속한 *이벤트* 를 사용자 지정하여 다른 업스트림(이벤트 처리기)으로 메시지를 라우팅할 수 있습니다.

> [!NOTE]
> 현재 WebPubSub 서비스는 [proto3](https://developers.google.com/protocol-buffers/docs/proto3)만 지원합니다.

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

모든 요청 메시지는 다음 protobuf 형식을 따릅니다.

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
        optional int32 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
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

* `ackId`는 선택 사항이며 이 명령 메시지의 증분 정수입니다. `ackId`가 지정되면 서비스에서 명령이 실행될 때 클라이언트에 [ack 응답 메시지](#ack-response)를 다시 보냅니다.

### <a name="leave-groups"></a>그룹 탈퇴

형식:

`leave_group_message.group`그룹 이름으로 설정합니다.

* `ackId`는 선택 사항이며 이 명령 메시지의 증분 정수입니다. `ackId`가 지정되면 서비스에서 명령이 실행될 때 클라이언트에 [ack 응답 메시지](#ack-response)를 다시 보냅니다.

### <a name="publish-messages"></a>메시지 게시

형식:

* `ackId`는 선택 사항이며 이 명령 메시지의 증분 정수입니다. `ackId`가 지정되면 서비스에서 명령이 실행될 때 클라이언트에 [ack 응답 메시지](#ack-response)를 다시 보냅니다.

설정한 의 를 기반으로 , 또는 중 하나일 수 있는 암시적 `dataType` `protobuf` 가 `text` `binary` `data` `MessageData` 있습니다. 수신기 클라이언트는 를 활용하여 콘텐츠를 올바르게 처리할 수 `dataType` 있습니다.

* `protobuf`: 를 설정하는 경우 `send_to_group_message.data.protobuf_data` 암시적 는 `protobuf` 입니다. `protobuf_data`[는 모든](https://developers.google.com/protocol-buffers/docs/proto3#any) 형식에 있을 수 있습니다. 다른 모든 클라이언트는 protobuf SDK로 deserialized할 수 있는 protobuf 인코딩된 이진 파일을 받게 됩니다. 텍스트 기반 콘텐츠(예: )만 지원하는 클라이언트에서는 `json.webpubsub.azure.v1` base64로 인코딩된 이진을 받습니다.

* `text`: 를 설정하는 경우 `send_to_group_message.data.text_data` 암시적 는 `text` 입니다. `text_data` 는 문자열이어야 합니다. 다른 프로토콜을 사용하는 모든 클라이언트는 UTF-8로 인코딩된 문자열을 받습니다.

* `binary`: 를 설정하는 경우 `send_to_group_message.data.binary_data` 암시적 는 `binary` 입니다. `binary_data` 는 바이트 배열이어야 합니다. 다른 프로토콜을 가진 모든 클라이언트는 protobuf 인코딩 없이 원시 이진을 수신합니다. 텍스트 기반 콘텐츠(예: )만 지원하는 클라이언트에서는 `json.webpubsub.azure.v1` base64로 인코딩된 이진을 받습니다.

#### <a name="case-1-publish-text-data"></a>사례 1: 텍스트 데이터 게시:

`send_to_group_message.group`을 `group`로 설정하고 `send_to_group_message.data.text_data`를 `"text data"`로 설정합니다.

* 이 그룹의 protobuf 하위 프로토콜 클라이언트가 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있는 대상입니다.

* 이 그룹의 json 하위 프로토콜 클라이언트가 `group` 받는 내용:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "text",
    "data" : "text data"
}
```

* 이 그룹 `group`의 원시 클라이언트가 수신하는 것은 문자열 데이터 `text data`입니다.

#### <a name="case-2-publish-protobuf-data"></a>사례 2: protobuf 데이터 게시:

고객 메시지가 있다고 가정합니다.

```
message MyMessage {
    int32 value = 1;
}
```

`send_to_group_message.group`를 로 설정하고 `group` `send_to_group_message.data.protobuf_data` 을 `Any.pack(MyMessage)` 로 설정합니다.`value = 1`

* 이 그룹의 protobuf 하위 프로토콜 클라이언트가 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있는 대상입니다.

* 이 그룹 `group`의 하위 프로토콜 클라이언트는 다음을 수신합니다.

```json
{
    "type": "message",
    "from": "group",
    "group": "G",
    "dataType" : "protobuf",
    "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64 encoded bytes
}
```

데이터는 base64로 인코딩된 deserialize 가능 protobuf 이진 파일입니다. 다음 protobuf 정의를 사용하고 를 사용하여 `Any.unpack()` deserialize할 수 있습니다.

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* 이 그룹의 원시 `group` 클라이언트가 받는 것은 이진 프레임입니다.

```
# Show in Hex
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

#### <a name="case-3-publish-binary-data"></a>사례 3: 이진 데이터 게시:

`send_to_group_message.group`을 `group`로 설정하고 `send_to_group_message.data.binary_data`를 `[1, 2, 3]`로 설정합니다.

* 이 그룹의 protobuf 하위 프로토콜 클라이언트가 `group` 이진 프레임을 수신하고 [DownstreamMessage를](#responses) 사용하여 역직렬화할 수 있는 대상입니다.

* 이 그룹의 json 하위 프로토콜 클라이언트가 `group` 받는 내용:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "binary",
    "data" : "AQID", // Base64 encoded [1,2,3]
}
```

json 하위 프로토콜 클라이언트는 텍스트 기반 메시지만 지원하기 때문에 이진 파일은 항상 base64로 인코딩됩니다.

* 이 그룹 `group`의 원시 클라이언트가 수신하는 것은 이진 프레임의 **이진** 데이터입니다.

```
# Show in Hex
01 02 03
```

### <a name="send-custom-events"></a>사용자 지정 이벤트 보내기

`dataType` `protobuf` `text` 설정한 dataType을 기반으로 , 또는 중 하나일 수 있는 암시적 가 `binary` 있습니다. 수신기 클라이언트는 를 활용하여 콘텐츠를 올바르게 처리할 수 `dataType` 있습니다.

* `protobuf`: 를 설정하는 경우 `event_message.data.protobuf_data` 암시적 는 `protobuf` 입니다. `protobuf_data` 는 지원되는 모든 protobuf 형식일 수 있습니다. 이벤트 처리기는 protobuf SDK에서 deserialized할 수 있는 protobuf 인코딩된 이진 파일을 받습니다.

* `text`: 를 설정하는 경우 `event_message.data.text_data` 암시적 는 `text` 입니다. `text_data` 는 문자열이어야 합니다. 이벤트 처리기는 UTF-8로 인코딩된 문자열을 받습니다.

* `binary`: 를 설정하는 경우 `event_message.data.binary_data` 암시적 는 `binary` 입니다. `binary_data` 는 바이트 배열이어야 합니다. 이벤트 처리기는 원시 이진 프레임을 받습니다.

#### <a name="case-1-send-event-with-text-data"></a>사례 1: 텍스트 데이터와 함께 이벤트 보내기:

`event_message.data.text_data`를 `"text data"`로 설정합니다.

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

#### <a name="case-2-send-event-with-protobuf-data"></a>사례 2: protobuf 데이터를 통해 이벤트 보내기:

고객 메시지가 있다고 가정합니다.

```
message MyMessage {
    int32 value = 1;
}
```

`event_message.data.protobuf_data`다음을 사용하여 로 설정 `any.pack(MyMessage)``value = 1`

업스트림 이벤트 처리기가 수신하는 내용은 아래와 같이 `Content-Type` CloudEvents HTTP 요청에 대한 `application/x-protobuf` 입니다. `dataType`=`protobuf`

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

// Just show in hex, you need to read as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

데이터는 유효한 protobuf 이진 파일입니다. 다음 및 를 사용하여 `proto` `any.unpack()` 이를 deserialize할 수 있습니다.

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-event-with-binary-data"></a>사례 3: 이진 데이터와 함께 이벤트 보내기:

`send_to_group_message.binary_data`를 `[1, 2, 3]`로 설정합니다.

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

// Just show in hex, you need to read as binary
01 02 03 
```

WebSocket 프레임은 텍스트 메시지 프레임의 경우 `text` 형식이거나 `binary` 메시지 프레임의 경우 UTF8로 인코딩된 이진일 수 있습니다.

메시지가 설명된 형식과 일치하지 않으면 서비스에서 클라이언트를 거부합니다.

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
        int32 ack_id = 1;
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

클라이언트가 수신한 메시지는 `ack`, `message`, `system`과 같은 여러 유형이 될 수 있습니다. 

### <a name="ack-response"></a>ack 응답

요청에 `ackId`가 포함된 경우 서비스는 이 요청에 대한 ack 응답을 반환합니다. 클라이언트 구현은 `async` `await` 작업에 대한 ack 응답에 대기하고 특정 기간 동안 ack 응답이 수신되지 않을 때 시간 초과를 확인하는 등, 이 ack 메커니즘을 처리해야 합니다.

클라이언트 구현에서는 먼저 `success`가 `true`인지 아니면 `false`인지 항상 확인해야 합니다. `success`가 `false`이면 클라이언트가 `error`에서 읽습니다.

### <a name="message-response"></a>메시지 응답

클라이언트는 클라이언트가 가입한 한 그룹 또는 서버가 특정 클라이언트나 특정 사용자에게 메시지를 보내는 서버 관리 역할에서 게시된 메시지를 받을 수 있습니다.

항상 다음을 얻게 됩니다. `DownstreamMessage.DataMessage`

- 메시지가 그룹에서 온 경우 는 `from` `group` 입니다. 메시지가 서버에서 온 경우 `from` 는 입니다. `server`

- 메시지가 그룹에서 온 경우 는 `group` 그룹 이름이 됩니다.

- 보낸 사람의 경우 `dateType` 메시지 중 하나가 설정됩니다. `dateType`가 `text` 이면 를 사용해야 `message_response_message.data.text_data` 합니다. `dateType`가 `binary` 이면 를 사용해야 `message_response_message.data.binary_data` 합니다. `dateType`가 `protobuf` 이면 를 사용해야 `message_response_message.data.protobuf_data` 합니다. `dateType`가 `json` 이면 를 사용해야 `message_response_message.data.text_data` 하며 콘텐츠가 직렬화된 json 문자열입니다.

### <a name="system-response"></a>시스템 응답

Web PubSub 서비스에서는 시스템 관련 응답을 클라이언트에 보낼 수도 있습니다. 

#### <a name="connected"></a>연결됨

연결을 서비스에 연결하는 경우 다음을 받게 됩니다. `DownstreamMessage.SystemMessage.ConnectedMessage`

#### <a name="disconnected"></a>연결 끊김

서버에서 연결을 닫거나 서비스가 클라이언트를 거부하는 경우 다음을 받게 됩니다. `DownstreamMessage.SystemMessage.DisconnectedMessage`

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]