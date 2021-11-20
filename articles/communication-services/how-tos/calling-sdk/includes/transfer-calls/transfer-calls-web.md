---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 11/04/2021
ms.author: rifox
ms.openlocfilehash: 85ac61458c4a400dc294daa1a981cb60c6ea4650
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132876074"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 전송은 핵심 `Call` API의 확장 기능입니다. 먼저 전송 기능 API 개체를 가져와야 합니다.

```js
const callTransferApi = call.feature(Features.Transfer);
```

통화 전환에는 세 명의 당사자가 포함됩니다.

- '전송자': 전송 요청을 시작한 사람입니다.
- '피전송자': 이 사람의 통화가 전송됩니다.
- '전송 대상': 통화가 이 사람에게 전송됩니다.

### <a name="transfer-to-participant"></a>참가자로 전송:

1. '전송자'와 '피전송자' 사이에 이미 연결된 통화가 있습니다. '전송자'가 '피전송자'와의 통화를 '전송 대상'으로 전송하기로 결정합니다.
1. '전송자'가 `transfer` API를 호출합니다.
1. '피전송자'는 `transferRequested` 이벤트를 사용하여 '전송 대상'으로의 전송 요청을 `accept`할지 아니면 `reject`할지 결정합니다.
1. '전송 대상'은 '피전송자'가 전송 요청을 수락한 경우에만 수신 통화를 받습니다.

현재 통화를 전송하려면 `transfer` API를 사용하면 됩니다. `transfer`는 사용자가 `disableForwardingAndUnanswered` 플래그를 설정할 수 있는 `transferCallOptions` 옵션을 허용합니다.

- `disableForwardingAndUnanswered = false`: '전송 대상'이 전송 통화에 응답하지 않으면 전송은 '전송 대상' 착신 전환 및 응답 없음 설정을 따릅니다.
- `disableForwardingAndUnanswered = true`: '전송 대상'이 전송 통화에 응답하지 않으면 전송 시도가 종료됩니다.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

### <a name="transfer-to-call"></a>호출로 전송:

1. '전송자'와 '피전송자' 사이에 이미 연결된 통화가 있습니다. 
2. *전송자 또는* *전송 대상* 간에 이미 연결 된 호출이 있습니다.
3. 전송 *또는* *transferee* 호출을 *전송 대상을* 사용 하는 호출로 전송 하기로 결정 합니다.
4. '전송자'가 `transfer` API를 호출합니다.
5. '피전송자'는 `transferRequested` 이벤트를 사용하여 '전송 대상'으로의 전송 요청을 `accept`할지 아니면 `reject`할지 결정합니다.
6. '전송 대상'은 '피전송자'가 전송 요청을 수락한 경우에만 수신 통화를 받습니다.

현재 통화를 전송하려면 `transfer` API를 사용하면 됩니다.

```js
// transfer to the target call specifying the call id
const id = { targetCallId: <CALL_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({ targetCallId: <CALL_ID> });
```

`transfer` API를 사용하여 `transferStateChanged` 및 `transferRequested` 이벤트를 구독할 수 있습니다. `transferRequested` 이벤트는 `call` 인스턴스에서 발생하고, `transferStateChanged` 이벤트와 전송 `state` 및 `error`는 `transfer` 인스턴스에서 발생합니다.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

'피전송자'는 `transferRequested` 이벤트에서 `transferRequestedEventArgs`의 `accept()` 또는 `reject()`를 사용하여 '전송자'가 시작한 전송 요청을 수락하거나 거부할 수 있습니다. `transferRequestedEventArgs`에서 `targetParticipant` 정보와 `accept` 또는 `reject` 메서드에 액세스할 수 있습니다.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```

*전송자 또는* 는 전송 상태 변경에 대 한 이벤트를 구독할 수 있습니다. *Transferee* 에 대 한 호출이 *전송 대상* 에 성공적으로 연결 된 경우에는 *transferee* 를 사용 하 여 원래 *호출을 중지할 수 있습니다* .

```js
transfer.on('stateChanged', () => {
   if (transfer.state === 'Transferred') {
       call.hangUp();
   }
});
```

