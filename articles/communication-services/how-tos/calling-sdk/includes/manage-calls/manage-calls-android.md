---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d2a6415a2cc03513606183c290443c453a82577
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130143765"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>전화 걸기

통화를 만들고 시작하려면 `CallAgent.startCall()` 메서드를 호출하고 호출 수신자의 `Identifier`를 제공해야 합니다.
그룹 통화에 참가하려면 `CallAgent.join()` 메서드를 호출하고 groupId를 제공해야 합니다. 그룹 ID는 GUID 또는 UUID 형식이어야 합니다.

통화 만들기와 시작은 동기적입니다. 통화 인스턴스를 사용하여 통화의 모든 이벤트를 구독할 수 있습니다.

### <a name="place-a-11-call-to-a-user"></a>사용자에게 1:1 전화 걸기
다른 Communication Services 사용자에게 전화를 걸려면 `callAgent`에서 `call` 메서드를 호출하고 `communicationUserId` 키를 사용하여 개체를 전달합니다.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN과 1:n 전화 걸기
> [!WARNING]
> 현재는 PSTN 통화를 사용할 수 없습니다.

사용자 및 PSTN 번호로 1:n 전화를 걸려면 호출 수신자의 전화 번호를 지정해야 합니다.
PSTN 통화를 허용하도록 Communication Services 리소스를 구성해야 합니다.
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>통화 수락
통화를 수락하려면 통화 개체에서 'accept' 메서드를 호출합니다.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

비디오 카메라를 켠 상태에서 통화를 수락하려면 다음을 수행합니다.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

수신 통화는 `callAgent` 개체에서 `onIncomingCall` 이벤트를 구독하여 가져올 수 있습니다.

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>그룹 통화 참가
새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 'join' 메서드를 호출하고 `groupId` 속성을 통해 개체를 전달해야 합니다. 값은 GUID여야 합니다.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>통화 속성

다음과 같이 이 통화의 고유 ID를 가져옵니다.

```java
String callId = call.getId();
```

통화의 다른 참가자에 대해 알아보려면 `call` 인스턴스의 `remoteParticipant` 컬렉션을 검사합니다.

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

수신 전화가 걸려 올 때의 호출자 ID입니다.

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

다음과 같이 통화의 상태를 가져옵니다. 

```java
CallState callState = call.getState();
```

다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.
* ‘NONE’ - 초기 통화 상태입니다.
* ‘EARLY_MEDIA’ - 전화가 연결되기 전에 알림이 재생되는 상태를 나타냅니다.
* ‘CONNECTING’ - 전화를 걸었거나 수락되었을 때의 초기 전환 상태입니다.
* ‘RINGING’ - 발신 통화의 경우 원격 참가자의 전화벨이 울리고 있다는 뜻입니다.
* ‘CONNECTED’ - 전화가 연결되었습니다.
* ‘LOCAL_HOLD’ - 로컬 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* ‘REMOTE_HOLD’ - 원격 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* ‘DISCONNECTING’ - 통화가 ‘Disconnected’ 상태로 전환되기 전의 상태입니다.
* ‘DISCONNECTED’ - 최종 통화 상태입니다.
* ‘IN_LOBBY’ - Teams 모임의 상호 운용성을 위해 로비에 있습니다.

통화가 종료된 이유를 알아보려면 `callEndReason` 속성을 검사합니다. 이 속성에는 다음과 같은 코드/하위 코드가 포함되어 있습니다. 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

현재 통화가 수신 통화인지 아니면 발신 통화인지 알아보려면 `callDirection` 속성을 검사합니다.

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

현재 마이크가 음소거되었는지 확인하려면 `muted` 속성을 검사합니다.

```java
boolean muted = call.isMuted();
```

활성 비디오 스트림을 검사하려면 `localVideoStreams` 컬렉션을 검사합니다.

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 다음과 같이 `mute` 및 `unmute` 비동기 API를 사용합니다.

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>통화 볼륨 변경

통화 중일 때 전화기의 하드웨어 볼륨 키를 사용하여 사용자가 통화 볼륨을 변경할 수 있어야 합니다.
이는 통화가 이루어지는 작업에서 스트리밍 유형이 `AudioManager.STREAM_VOICE_CALL`인 `setVolumeControlStream` 메서드를 사용하여 수행됩니다.
이렇게 하면 하드웨어 볼륨 키가 통화 볼륨(전화 아이콘 또는 볼륨 슬라이더에 이와 유사한 것으로 표시됨)을 변경할 수 있어 경보, 미디어 또는 시스템 전체 볼륨과 같은 다른 사운드 프로필의 볼륨을 변경할 수 없습니다. 자세한 내용은 [오디오 출력의 변경 사항 처리 | Android Developers](https://developer.android.com/guide/topics/media-apps/volume-and-earphones)를 확인할 수 있습니다.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열
`remoteParticipants` 컬렉션은 특정 통화의 원격 참가자 목록을 반환합니다.
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화 번호)를 추가하려면 `addParticipant`를 호출합니다. 그러면 원격 참가자 인스턴스가 동기적으로 반환됩니다.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>통화에서 참가자 제거
통화에서 참가자(사용자 또는 전화 번호)를 제거하려면 `removeParticipant`를 호출합니다.
그러면 통화에서 참가자가 제거될 때 이를 비동기적으로 확인합니다.
참가자도 `remoteParticipants` 컬렉션에서 제거됩니다.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>원격 참가자 속성
특정 원격 참가자와 연결된 여러 가지 속성 및 컬렉션이 있습니다.

* 이 원격 참가자의 식별자를 가져옵니다.
ID는 'Identifier' 형식 중 하나입니다.
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* 이 원격 참가자의 상태를 가져옵니다.
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
상태는 다음 중 하나입니다.
* ‘IDLE’ - 초기 상태입니다.
* ‘EARLY_MEDIA’ - 참가자가 통화에 연결되기 전에 알림이 재생됩니다.
* ‘RINGING’ - 참가자의 전화벨이 울리고 있습니다.
* ‘CONNECTING’ - 참가자가 통화에 연결하는 동안의 전환 상태입니다.
* ‘CONNECTED’ - 참가자가 전화에 연결되었습니다.
* ‘HOLD’ - 참가자가 보류 중입니다.
* ‘IN_LOBBY’ - 참가자가 로비에서 참석 대기 중입니다. 현재는 Teams interop 시나리오에만 사용됩니다.
* ‘DISCONNECTED’ - 최종 상태입니다. 참가자의 통화 연결이 끊겼습니다.

* 참가자가 통화를 종료한 이유를 알아보려면 `callEndReason` 속성을 검사합니다.
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* 이 원격 참가자가 음소거되었는지 확인하려면 `isMuted` 속성을 검사합니다.
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* 이 원격 참가자가 말하는 중인지 확인하려면 `isSpeaking` 속성을 검사합니다.
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* 특정 참가자가 이 통화에서 보내는 모든 비디오 스트림을 검사하려면 `videoStreams` 컬렉션을 확인합니다.
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```
## <a name="using-foreground-services"></a>포그라운드 서비스 사용

애플리케이션이 백그라운드에 있는 경우에도 사용자 표시 작업을 실행하려는 경우 [Foreground Services](https://developer.android.com/guide/components/foreground-services)를 사용할 수 있습니다.

예를 들어 Foreground Services를 사용하면 애플리케이션에 활성 호출이 있을 때 사용자에게 표시되는 알림을 유지합니다. 이러한 방식으로 사용자가 홈 화면으로 가거나 [최근 화면에서](https://developer.android.com/guide/components/activities/recents)애플리케이션을 제거하더라도 호출은 계속 활성화됩니다.

호출하는 동안 포그라운드 서비스를 사용하지 않는 경우 홈 화면으로 이동하면 호출을 활성 상태로 유지할 수 있지만 Android OS가 애플리케이션의 프로세스를 중지하면 최근 화면에서 애플리케이션을 제거하면 호출이 중지됩니다.

호출을 시작/조인할 때 Foreground 서비스를 시작해야 합니다. 예를 들면 다음과 같습니다.

```java
call = callAgent.startCall(context, participants, options);
startService(yourForegroundServiceIntent);
```

호출을 중단하거나 호출 상태가 연결 끊김인 경우 포그라운드 서비스를 중지합니다. 예를 들면 다음과 같습니다.

```java
call.hangUp(new HangUpOptions()).get();
stopService(yourForegroundServiceIntent);
```

### <a name="notes-on-using-foreground-services"></a>포그라운드 서비스 사용에 대한 참고 사항

앱이 최근 목록에서 제거될 때 이미 실행 중인 Foreground Service를 중지하는 것과 같은 시나리오는 사용자 표시 알림을 제거하고 Android OS는 일정 기간 동안 애플리케이션 프로세스를 활성 상태로 유지할 수 있습니다. 즉, 이 기간 동안 호출이 계속 활성화될 수 있습니다.

예를 들어 애플리케이션이 서비스 메서드에서 포그라운드 서비스를 중지하는 경우 `onTaskRemoved` 애플리케이션은 메서드 재정의로 활동이 소멸된 경우 오디오 및 비디오 중지와 같은 [작업 수명 주기에](https://developer.android.com/guide/components/activities/activity-lifecycle) 따라 오디오 및 비디오를 시작/중지할 수 `onDestroy` 있습니다.
