---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705617"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>전화 걸기

통화를 만들고 시작하려면 `callAgent`에서 API 중 하나를 사용하여 Communication Services ID SDK를 통해 만든 사용자를 제공합니다.

통화 만들기와 시작은 동기적입니다. `call` 인스턴스를 사용하여 통화 이벤트를 구독할 수 있습니다.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>사용자 또는 PSTN에게 1:n 전화 걸기

다른 Communication Services 사용자에게 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 사용하고, [Communication Services 관리 라이브러리를 사용하여 만든](../../../../quickstarts/access-tokens.md) 수신자의 `CommunicationUserIdentifier`를 전달합니다.

사용자에 대한 1:1 통화의 경우 다음 코드를 사용합니다.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

PSTN(공중 교환 전화망)으로 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 사용하여 수신자의 `PhoneNumberIdentifier`를 전달합니다. PSTN 호출을 허용하도록 Communication Services 리소스를 구성해야 합니다.

PSTN 번호로 전화를 거는 경우 대체 발신자 ID를 지정합니다. 대체 발신자 ID는 PSTN 통화의 발신자를 식별하는 전화 번호(E.164 표준 기반)입니다. 통화 수신자가 수신 통화에 대해 확인하는 전화번호입니다.

> [!NOTE]
> PSTN 통화는 현재 프라이빗 미리 보기로 제공됩니다. 액세스하려면 [얼리어답터 프로그램을 신청](https://aka.ms/ACS-EarlyAdopter)하세요.

PSTN 번호에 대한 1:1 통화의 경우 다음 코드를 사용합니다.
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

사용자 및 PSTN 번호에 대한 1:n 통화의 경우 다음 코드를 사용합니다.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>그룹 통화 참가

> [!NOTE]
> `groupId` 매개 변수는 시스템 메타데이터로 간주되며 Microsoft가 시스템을 실행하는 데 필요한 작업에 사용할 수 있습니다. `groupId` 값에 개인 데이터를 포함하지 마세요. Microsoft는 이 매개 변수를 개인 데이터로 간주하지 않으며 해당 내용이 Microsoft 직원에게 표시되거나 장기적으로 저장될 수 있습니다.
>
> `groupId` 매개 변수는 데이터를 GUID 형식으로 지정해야 합니다. 시스템에서 개인 데이터로 간주되지 않는 임의로 생성된 GUID를 사용하는 것이 좋습니다.
>

새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 `join` 메서드를 사용하고 `groupId` 속성을 통해 개체를 전달합니다. `groupId` 값은 GUID여야 합니다.

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>수신 전화 받기

`callAgent` 인스턴스는 로그인된 ID가 전화를 받을 때 `incomingCall` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음 옵션 중 하나를 사용하여 구독합니다.

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHandler);
```

`incomingCall` 이벤트에는 허용 또는 거부할 수 있는 `incomingCall` 인스턴스가 포함됩니다.

비디오를 켜고 통화를 시작/참여/수락하는 경우 지정된 비디오 카메라 디바이스가 다른 프로세스에서 사용되고 있거나 시스템에서 사용하지 않도록 설정된 경우 통화는 비디오가 꺼진 상태로 시작되고 cameraStartFailed: true 통화 진단이 발생합니다.

## <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 `mute` 및 `unmute` 비동기 API를 사용할 수 있습니다.

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-the-participants-in-a-call"></a>통화 참가자 나열

`remoteParticipants` 컬렉션은 통화의 원격 참가자 목록을 반환합니다.

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화번호)를 추가하려면 `addParticipant`를 사용할 수 있습니다. `Identifier` 유형 중 하나를 제공합니다. `remoteParticipant` 인스턴스를 동기적으로 반환합니다. 참가자가 호출에 성공적으로 추가되면 호출의 `remoteParticipantsUpdated` 이벤트가 발생합니다.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>통화에서 참가자 제거

통화에서 참가자(사용자 또는 전화 번호)를 제거하려면 `removeParticipant`를 호출할 수 있습니다. `Identifier` 유형 중 하나를 전달해야 합니다. 이 메서드는 통화에서 참가자가 제거된 후 이를 비동기적으로 해결합니다. 참가자는 `remoteParticipants` 컬렉션에서도 제거됩니다.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

### <a name="access-remote-participant-properties"></a>원격 참가자 속성에 액세스

원격 참가자에게는 연결된 속성 및 컬렉션 집합이 있습니다.

- `CommunicationIdentifier`: 원격 참가자의 식별자를 가져옵니다. ID는 `CommunicationIdentifier` 유형 중 하나입니다.

    ```js
    const identifier = remoteParticipant.identifier;
    ```

다음 `CommunicationIdentifier` 유형 중 하나일 수 있습니다.

- `{ communicationUserId: '<ACS_USER_ID'> }`: ACS 사용자를 나타내는 개체입니다.
- `{ phoneNumber: '<E.164>' }`: E.164 형식의 전화 번호를 나타내는 개체입니다.
- `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Teams 사용자를 나타내는 개체입니다.
- `{ id: string }`: 다른 유형에 해당하지 않는 식별자를 나타내는 개체입니다.

- `state`: 원격 참가자의 상태를 가져옵니다.

    ```js
    const state = remoteParticipant.state;
    ```

상태는 다음과 같을 수 있습니다.

- `Idle`: 초기 상태입니다.
- `Connecting`: 참가자가 통화에 연결되는 동안의 전환 상태입니다.
- `Ringing`: 참가자가 전화를 거는 중입니다.
- `Connected`: 참가자가 통화에 연결되었습니다.
- `Hold`: 참가자가 대기 중입니다.
- `EarlyMedia`: 참가자가 통화에 연결되기 전에 재생되는 알림입니다.
- `InLobby`: 원격 참가자가 로비에 있음을 나타냅니다.
- `Disconnected`: 최종 상태입니다. 참가자의 통화 연결이 끊겼습니다. 원격 참가자의 네트워크 연결이 끊어지면 2분 후 상태가 `Disconnected`로 변경됩니다.

- `callEndReason`: 참가자가 통화를 종료한 이유를 알아보려면 `callEndReason` 속성을 확인합니다.

    ```js
    const callEndReason = remoteParticipant.callEndReason;
    const callEndReasonCode = callEndReason.code // (number) code associated with the reason
    const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
    ```

- `isMuted` 상태: 원격 참가자가 음소거되어 있는지 확인하려면 `isMuted` 속성을 확인합니다. `Boolean`를 반환합니다.

    ```js
    const isMuted = remoteParticipant.isMuted;
    ```

- `isSpeaking` 상태: 원격 참가자가 말하는 중인지 확인하려면 `isSpeaking` 속성을 확인합니다. `Boolean`를 반환합니다.

    ```js
    const isSpeaking = remoteParticipant.isSpeaking;
    ```

- `videoStreams`: 특정 참가자가 이 통화에서 보내는 모든 비디오 스트림을 검사하려면 `videoStreams` 컬렉션을 확인합니다. `RemoteVideoStream` 개체가 포함됩니다.

    ```js
    const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
    ```
- `displayName`: 이 원격 참가자의 표시 이름을 가져오려면 `displayName` 속성을 검사합니다. 문자열을 반환합니다. 

    ```js
    const displayName = remoteParticipant.displayName;
    ```

## <a name="check-call-properties"></a>통화 속성 확인

통화의 고유 ID(문자열)를 가져옵니다.

```js
const callId: string = call.id;
```
통화에 대한 정보 얻기:
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.
```js
const callInfo = call.info;
```

통화의 다른 참가자에 대해 알아보려면 '통화' 인스턴스의 `remoteParticipants` 컬렉션을 검사합니다.

```js
const remoteParticipants = call.remoteParticipants;
```

수신 통화의 발신자를 식별합니다.
```js
const callerIdentity = call.callerInfo.identifier;
```

`identifier`는 `CommunicationIdentifier` 유형 중 하나입니다.

통화의 상태를 가져옵니다.

```js
const callState = call.state;
```

다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.

- `None`: 초기 통화 상태입니다.
- `Connecting`: 전화를 걸었거나 수락되었을 때의 초기 전환 상태입니다.
- `Ringing`: 발신 통화의 경우 원격 참가자의 전화 벨이 울리고 있다는 뜻입니다. 해당 쪽에서는 `Incoming` 상태입니다.
- `EarlyMedia`: 통화가 연결되기 전에 알림이 재생되는 상태를 나타냅니다.
- `Connected`: 통화가 연결되었음을 나타냅니다.
- `LocalHold`: 로컬 참가자가 통화를 대기 상태로 전환했음을 나타냅니다. 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
- `RemoteHold`: 원격 참가자가 통화를 대기 상태로 전환했음을 나타냅니다. 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
- `InLobby`: 사용자가 로비에 있음을 나타냅니다.
- `Disconnecting`: 통화가 `Disconnected` 상태로 전환되기 전의 전환 상태입니다.
- `Disconnected`: 최종 통화 상태입니다. 네트워크 연결이 끊어지면 2분 후 상태가 `Disconnected`로 변경됩니다.

`callEndReason` 속성을 검사하여 통화가 종료된 이유를 확인합니다.

```js
const callEndReason = call.callEndReason;
const callEndReasonCode = callEndReason.code // (number) code associated with the reason
const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
```

`direction` 속성을 검사하여 현재 통화가 수신 또는 발신인지 확인합니다. `CallDirection`를 반환합니다.

```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

현재 마이크가 음소거되었는지 확인합니다. `Boolean`를 반환합니다.

```js
const muted = call.isMuted;
```

`isScreenSharingOn` 속성을 검사하여 지정된 엔드포인트에서 화면 공유 스트림을 보내고 있는지 확인합니다. `Boolean`를 반환합니다.

```js
const isScreenSharingOn = call.isScreenSharingOn;
```

`localVideoStreams` 컬렉션을 확인하여 활성 비디오 스트림을 검사합니다. `LocalVideoStream` 개체를 반환합니다.

```js
const localVideoStreams = call.localVideoStreams;
```