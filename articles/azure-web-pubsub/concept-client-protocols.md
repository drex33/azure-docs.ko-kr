---
title: Azure Web PubSub에 대한 WebSocket 클라이언트 프로토콜
description: Azure Web PubSub에 대한 클라이언트 프로토콜 개요
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 06f4b3050b90f27d1f30ba59b26eeed4d76c6bc8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539366"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub에 대한 WebSocket 클라이언트 프로토콜

클라이언트는 표준 [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) 프로토콜을 사용하여 Azure Web PubSub에 연결합니다.

## <a name="service-endpoint"></a>서비스 엔드포인트
이 서비스는 클라이언트가 연결할 두 가지 유형의 엔드포인트를 제공합니다.
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}`은 다른 애플리케이션에 대한 격리 역할을 하는 필수 매개 변수입니다. 경로 또는 쿼리에서 설정할 수 있습니다.

## <a name="auth"></a>인증
1. 클라이언트는 JWT 토큰으로 서비스에 연결합니다.

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. 클라이언트는 JWT 토큰을 사용하여 연결합니다.

JWT 토큰은 쿼리 문자열 `/client/?hub={hub}&access_token={token}` 또는 `Authorization` 헤더(`Authorization: Bearer {token}`)에 있을 수 있습니다.

일반적인 워크플로는 다음과 같습니다.
1. 클라이언트는 애플리케이션 서버와 협상합니다. 애플리케이션 서버에는 클라이언트 요청을 처리하고 클라이언트가 서비스에 연결할 수 있도록 JWT 토큰에 서명하는 인증 미들웨어가 있습니다.
2. 애플리케이션 서버는 JWT 토큰과 서비스 URL을 클라이언트에 반환합니다.
3. 클라이언트는 애플리케이션 서버에서 반환된 URL 및 JWT 토큰을 사용하여 Web PubSub 서비스에 연결을 시도합니다.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>간단한 WebSocket 클라이언트
이름에서 알 수 있듯이 간단한 WebSocket 클라이언트는 간단한 WebSocket 연결입니다. 또한 사용자 지정 하위 프로토콜을 가질 수 있습니다. 

예를 들어 JS에서 다음을 사용하여 간단한 WebSocket 클라이언트를 만들 수 있습니다.
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket 클라이언트

PubSub WebSocket 클라이언트는 하위 프로토콜 `json.webpubsub.azure.v1`로 연결합니다.

예를 들어 JS에서 다음을 사용하여 PubSub WebSocket 클라이언트를 만들 수 있습니다.
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

클라이언트가 이 하위 프로토콜을 사용할 때 나가는 데이터 프레임과 들어오는 데이터 프레임 모두 JSON 페이로드가 될 것으로 예상됩니다. 인증된 클라이언트는 Azure Web PubSub 서비스를 통해 직접 다른 클라이언트에 메시지를 게시할 수 있습니다.

### <a name="permissions"></a>사용 권한

PubSub WebSocket 클라이언트를 설명할 때 알 수 있듯이 클라이언트는 *승인* 된 경우에만 다른 클라이언트에 게시할 수 있습니다. 클라이언트의 권한은 연결될 때 또는 연결 수명 동안 부여될 수 있습니다.

| 역할 | 사용 권한 |
|---|---|
| 지정되지 않음 | 클라이언트는 이벤트 요청을 보낼 수 있습니다.
| `webpubsub.joinLeaveGroup` | 클라이언트는 모든 그룹에 가입/탈퇴할 수 있습니다.
| `webpubsub.sendToGroup` | 클라이언트는 모든 그룹에 메시지를 게시할 수 있습니다.
| `webpubsub.joinLeaveGroup.<group>` | 클라이언트는 `<group>` 그룹에 가입하거나 탈퇴할 수 있습니다.
| `webpubsub.sendToGroup.<group>` | 클라이언트는 `<group>` 그룹에 메시지를 게시할 수 있습니다.

하위 프로토콜에 대한 자세한 내용은 [JSON 하위 프로토콜](./reference-json-webpubsub-subprotocol.md)에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
