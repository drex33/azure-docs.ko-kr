---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705618"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> 애플리케이션에서 이벤트 대리자를 구현할 때 이벤트 구독이 필요한 개체에 대한 강력한 참조를 보유해야 합니다. 예를 들어 `call.addParticipant` 메서드를 호출할 때 `RemoteParticipant` 개체가 반환되고 애플리케이션에서 `RemoteParticipantDelegate`를 수신 대기하도록 대리자를 설정하는 경우 애플리케이션은 `RemoteParticipant` 개체에 대한 강력한 참조를 보유해야 합니다. 그렇지 않으면 이 개체가 수집될 경우 호출 SDK가 개체를 호출하려고 할 때 대리자가 치명적인 예외를 throw합니다.

## <a name="place-an-outgoing-call"></a>발신 전화 걸기

통화를 만들고 시작하려면 `CallAgent`에서 API 중 하나를 호출하고 Communication Services Management SDK를 사용하여 프로비저닝한 사용자의 Communication Services ID를 제공해야 합니다.

통화 만들기와 시작은 동기적입니다. 통화의 모든 이벤트를 구독할 수 있는 호출 인스턴스를 받게 됩니다.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>사용자와 1:1 통화 또는 사용자 및 PSTN과 1:n 통화

```swift
let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN과 1:n 통화
PSTN과 통화하려면 Communication Services로 얻은 전화번호를 지정해야 합니다.

```swift
let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}
```

## <a name="join-a-group-call"></a>그룹 통화 참가
통화에 참가하려면 `CallAgent`에서 API 중 하나를 호출해야 합니다.

```swift
let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
    if error == nil {
        print("Successfully joined group call")
        self.call = call
    } else {
        print("Failed to join group call")
    }
}
```

## <a name="subscribe-to-an-incoming-call"></a>수신 전화 구독
수신 전화 이벤트를 구독합니다.

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>수신 전화 받기
전화를 받으려면 `IncomingCall` 개체에서 `accept` 메서드를 호출합니다.

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
    incomingCall.accept(options: acceptCallOptions) { (call, error) in
        if error == nil {
            print("Incoming call accepted")
        } else {
            print("Failed to accept incoming call")
        }
    }
} else {
  print("No incoming call found to accept")
}
```

## <a name="perform-mid-call-operations"></a>통화 중 작업

통화 중 비디오 및 오디오와 관련된 설정을 관리하는 다양한 작업을 수행할 수 있습니다.

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 `mute` 및 `unmute` 비동기 API를 사용할 수 있습니다.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}
```

다음 코드를 사용하여 로컬 엔드포인트의 음소거를 비동기 방식으로 해제합니다.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열

```swift
call.remoteParticipants
```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화번호)를 추가하려면 `addParticipant`를 호출합니다. 이 명령을 통해 원격 참가자 인스턴스가 동기적으로 반환됩니다.

```swift
let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))
```

### <a name="remove-a-participant-from-a-call"></a>통화에서 참가자 제거
통화에서 참가자(사용자 또는 전화번호)를 제거하려면 `removeParticipant` API를 호출합니다. 이는 비동기적으로 확인됩니다.

```swift
call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}
```

### <a name="get-remote-participant-properties"></a>원격 참가자 속성 가져오기

```swift
// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]
```