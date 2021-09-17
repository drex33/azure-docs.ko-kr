---
title: 참조 - Azure Web PubSub에 대한 CloudEvents 확장
description: 참조는 Azure Web PubSub 서비스에 대해 정의된 CloudEvents 확장을 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 6503433f164e0b8153aa8832473fd06ad3959bae
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434865"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>Azure Web PubSub에 대한 CloudEvents 확장

서비스는 [CloudEvents HTTP 프로토콜](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)을 사용하여 업스트리밍 웹후크에 클라이언트 이벤트를 전달합니다.

서비스에서 서버로 보내는 데이터는 항상 CloudEvents `binary` 형식입니다.

- [웹후크 검증](#protection)
- [웹 PubSub CloudEvents 특성 확장](#extension)
- [이벤트](#events)
    - 이벤트 차단
        - [시스템 `connect` 이벤트](#connect)
        - [사용자 이벤트](#message)
    - 이벤트 차단 해제
        - [시스템 `connected` 이벤트](#connected)
        - [시스템 `disconnected` 이벤트](#disconnected)

## <a name="webhook-validation"></a>웹후크 유효성 검사

<a name="protection"></a>

웹후크 유효성 검사는 [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)를 따릅니다. 요청은 항상 헤더에 `WebHook-Request-Origin: xxx.webpubsub.azure.com`을 포함합니다.

전달 대상이 이벤트 전달을 허용하는 경우에만 `WebHook-Allowed-Origin` 헤더를 포함하여 요청에 응답해야 합니다. 예를 들면 다음과 같습니다.

`WebHook-Allowed-Origin: *`

또는

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

현재 [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) 및 [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback)은 지원되지 않습니다.


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Web PubSub CloudEvents 특성 확장
<a name="extension"></a>

> 또한 HTTP 사양은 이제 확장 HTTP 헤더에 X- 접두사가 붙는 것을 더 이상 제안하지 않음으로써 유사한 패턴을 따르고 있다는 점에 주목했습니다.

이 확장은 생성하는 모든 이벤트에 대해 Web PubSub에서 사용하는 특성을 정의합니다.

### <a name="attributes"></a>특성

| Name | Type | Description | 예|
|--|--|--|--|
| `userId` | `string` | 연결이 인증된 사용자 | |
| `hub` | `string` | 연결이 속한 허브 | |
| `connectionId` | `string` | connectionId는 클라이언트 연결에 고유합니다. | |
| `eventName` | `string` | 접두사가 없는 이벤트 이름 | |
| `subprotocol` | `string` | 클라이언트가 사용 중인 하위 프로토콜(있는 경우) | |
| `connectionState` | `string` | 연결 상태를 정의합니다. 동일한 응답 헤더를 사용하여 상태 값을 다시 설정할 수 있습니다. 여러 `connectionState` 헤더는 허용되지 않습니다. 예를 들어 내부에 복합 문자가 포함된 경우 문자열 값을 base64로 인코딩합니다. 예를 들어 이 특성을 사용하여 복합 개체를 전달할 수 `base64(jsonString)` 있습니다.| |
| `signature` | `string` | 들어오는 요청이 예상 출처에서 온 것인지 확인하기 위한 업스트리밍 웹후크의 서명입니다. 이 서비스는 기본 액세스 키와 보조 액세스 키를 모두 HMAC 키로 사용하여 값을 계산합니다. `Hex_encoded(HMAC_SHA256(accessKey, connectionId))`. 업스트리밍에서는 요청을 처리하기 전에 요청이 유효한지 확인해야 합니다. | |

## <a name="events"></a>이벤트

두 가지 유형의 이벤트가 있습니다. 하나는 서비스가 이벤트 응답이 계속되기를 기다리는 이벤트를 *차단* 하는 것이며, 하나는 서비스가 다음 메시지를 처리하기 전에 해당 이벤트의 응답을 기다리지 않는 이벤트를 *차단 해제* 하는 것입니다.

- 이벤트 차단
    - [시스템 `connect` 이벤트](#connect)
    - [사용자 이벤트](#message)
- 이벤트 차단 해제
    - [시스템 `connected` 이벤트](#connected)
    - [시스템 `disconnected` 이벤트](#disconnected)

### <a name="system-connect-event"></a>시스템 `connect` 이벤트
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>요청 형식:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>성공 응답 형식:
* 상태 코드:
    * `204`: 성공, 콘텐츠가 없습니다.
    * `200`: 성공, 콘텐츠가 다음 속성이 허용되는 JSON 형식이어야 합니다.
* 헤더: `ce-connectionState` 이 헤더가 있는 경우 이 연결의 연결 상태가 헤더 값으로 업데이트됩니다. *차단* 이벤트만 연결 상태를 업데이트할 수 있습니다. 아래 샘플에서는 base64로 인코딩된 JSON 문자열을 사용하여 연결의 복합 상태를 저장합니다.
* 
```HTTP
HTTP/1.1 200 OK
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    `connect` 이벤트는 클라이언트에서 업스트리밍으로 하위 프로토콜 및 인증 정보를 전달합니다. Azure SignalR Service는 상태 코드를 사용하여 요청을 WebSocket 프로토콜로 업그레이드할지 여부를 결정합니다.

    요청에 `subprotocols` 속성이 포함된 경우 서버는 지원하는 하나의 하위 프로토콜을 반환해야 합니다. 서버가 하위 프로토콜을 사용하지 않으려는 경우 응답으로 `subprotocol` 속성을 보내지 **않습니다**. [빈 헤더를 보낼 수 없습니다](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols).

* `userId`: `{auth-ed user ID}`

    서비스가 익명 연결을 허용하므로 서비스에 클라이언트 연결의 사용자 ID를 알려주는 것은 `connect` 이벤트의 책임입니다. 서비스는 응답 페이로드 `userId`에서 사용자 ID를 읽습니다(있는 경우). 요청 클레임이나 `connect` 이벤트의 응답 페이로드에서 사용자 ID를 읽을 수 없으면 연결이 끊어집니다.

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    이 속성은 사용자가 이 연결을 하나 또는 여러 그룹에 추가할 수 있는 편리한 방법을 제공합니다. 이런 식으로 이 연결을 일부 그룹에 추가하기 위해 별도의 호출을 할 필요가 없습니다.

* `roles`: `{roles the client has}`
    
    속성은 업스트리밍이 클라이언트에 권한을 부여하는 방법을 제공합니다. 역할에 따라 클라이언트가 갖는 다양한 초기 권한이 정의되며, 클라이언트가 PubSub WebSocket 클라이언트일 때 유용할 수 있습니다. 권한에 대한 자세한 내용은 [클라이언트 권한](./concept-client-protocols.md#permissions)에 설명되어 있습니다.

#### <a name="error-response-format"></a>오류 응답 형식:

* `4xx`: 오류, Upstream의 응답이 클라이언트 요청에 대한 응답으로 반환됩니다.

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>시스템 `connected` 이벤트
<a name="connected"></a> 클라이언트가 WebSocket 핸드셰이크를 완료하고 성공적으로 연결되면 서비스가 Upstream을 호출합니다.

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`
* `ce-connectionState`: `eyJrZXkiOiJhIn0=`

요청 본문이 빈 JSON입니다.

#### <a name="request-format"></a>요청 형식:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{}

```

#### <a name="response-format"></a>응답 형식:

`2xx`: 성공 응답

`connected`는 비동기식 이벤트로 응답 상태 코드가 성공하지 못한 경우 서비스에서 오류를 기록합니다.

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>시스템 `disconnected` 이벤트
<a name="disconnected"></a>
`disconnected` 이벤트는 **연결** 이벤트가 `2xx` 상태 코드를 반환하는 경우 클라이언트 요청이 완료될 때 **항상** 트리거됩니다.

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>요청 형식:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason`은 클라이언트가 연결을 끊는 이유를 설명합니다.


#### <a name="response-format"></a>응답 형식:

`2xx`: 성공 응답

`disconnected`는 비동기식 이벤트로 응답 상태 코드가 성공하지 못한 경우 서비스에서 오류를 기록합니다.

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>단순 WebSocket 클라이언트에 대한 사용자 이벤트 `message`
<a name="message"></a> 서비스는 모든 WebSocket 메시지 프레임에 대해 업스트리밍 이벤트 처리기를 호출합니다.

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type`: 바이너리 프레임의 경우 `application/octet-stream`, 텍스트 프레임의 경우 `text/plain` 

UserPayload는 클라이언트가 보내는 것입니다.

#### <a name="request-format"></a>요청 형식:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message
ce-connectionState: eyJrZXkiOiJhIn0=

UserPayload

```

#### <a name="success-response-format"></a>성공 응답 형식

* 상태 코드
    * `204`: 성공, 콘텐츠가 없습니다.
    * `200`: 성공, `UserResponsePayload`의 형식은 응답의 `Content-Type`에 따라 다릅니다.
* `Content-Type`: 바이너리 프레임의 경우 `application/octet-stream`, 텍스트 프레임의 경우 `text/plain` 
* 헤더 `Content-Type` : `application/octet-stream` 이진 프레임의 경우 이고, `text/plain` 텍스트 프레임의 경우 입니다. 
* 헤더: `ce-connectionState` 이 헤더가 있는 경우 이 연결의 연결 상태가 헤더 값으로 업데이트됩니다. *차단* 이벤트만 연결 상태를 업데이트할 수 있습니다. 아래 샘플에서는 base64로 인코딩된 JSON 문자열을 사용 하 여 연결에 대 한 복합 상태를 저장 합니다.

`Content-Type`이 `application/octet-stream`이면 서비스는 `binary` WebSocket 프레임을 사용하여 클라이언트에 `UserResponsePayload`를 보냅니다. `Content-Type`이 `text/plain`이면 서비스는 `text` WebSocket 프레임을 사용하여 클라이언트에 `UserResponsePayload`를 보냅니다. 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn
ce-connectionState: eyJrZXkiOiJhIn0=

UserResponsePayload
```

#### <a name="error-response-format"></a>오류 응답 형식
상태 코드가 성공하지 않으면 오류 응답으로 간주됩니다. `message` 응답 상태 코드가 성공하지 못하면 연결이 **끊어집니다**.

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>PubSub WebSocket 클라이언트에 대한 사용자 지정 이벤트 `{custom_event}`
<a name="custom_event"></a>

서비스는 모든 유효한 사용자 지정 이벤트 메시지에 대해 이벤트 처리기 웹후크를 호출합니다.

#### <a name="case-1-send-event-with-text-data"></a>사례 1: 텍스트 데이터와 함께 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

text data

```

#### <a name="case-2-send-event-with-json-data"></a>사례 2: JSON 데이터로 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>사례 3: 이진 데이터와 함께 이벤트 보내기:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>성공 응답 형식

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* 상태 코드
    * `204`: 성공, 콘텐츠가 없습니다.
    * `200`: 성공, PubSub WebSocket 클라이언트로 보내는 데이터는 `Content-Type`에 따라 다릅니다. 
* 헤더 `ce-connectionState` :이 헤더가 있으면이 연결의 연결 상태는 헤더의 값으로 업데이트 됩니다. *차단* 이벤트만 연결 상태를 업데이트할 수 있습니다. 아래 샘플에서는 base64로 인코딩된 JSON 문자열을 사용 하 여 연결에 대 한 복합 상태를 저장 합니다.
* 헤더가 `Content-Type` 이면 `application/octet-stream` 서비스는 base64 인코딩 인코딩을 사용 하 여를 `UserResponsePayload` 사용 하 여 클라이언트로 다시 보냅니다 `dataType` `binary` . 샘플 응답:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* `Content-Type`이 `text/plain`이면 서비스는 `dataType`을 페이로드 문자열과 함께 `text`로 사용하여 `UserResponsePayload`를 클라이언트에 보냅니다.
* `Content-Type`이 `application/json`인 경우 서비스는 응답 페이로드 본문으로 `data` 값 토큰과 함께 `dataType`=`json`을 사용하여 `UserResponsePayload`를 클라이언트에 보냅니다.


#### <a name="error-response-format"></a>오류 응답 형식
상태 코드가 성공하지 않으면 오류 응답으로 간주됩니다. `{custom_event}` 응답 상태 코드가 성공하지 못하면 연결이 **끊어집니다**.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
