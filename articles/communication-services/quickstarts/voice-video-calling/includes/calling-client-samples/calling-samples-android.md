---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 86ab1bc1b9612c663076e5ed55ae5531317906f2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078248"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../../access-tokens.md)에 대한 자세한 정보
- 선택 사항: [애플리케이션에 통화 추가 시작](../../getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-package"></a>패키지 설치

> [!NOTE]
> 이 문서에서는 Calling SDK의 버전 1.0.0을 사용합니다.

프로젝트 수준 build.gradle을 찾고, `mavenCentral()`을 `buildscript` 및 `allprojects` 아래의 리포지토리 목록에 추가해야 합니다.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
그런 다음, 모듈 수준 build.gradle에서 dependencies 섹션에 다음 줄을 추가합니다.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}

```

## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient는 Calling SDK의 주 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.|
| CommunicationIdentifier | CommunicationIdentifier는 통화에 포함될 수 있는 다른 유형의 참가자로 사용됩니다.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>CallClient 초기화, CallAgent 만들기 및 DeviceManager 액세스

`CallAgent` 인스턴스를 만들려면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출해야 합니다. 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.
`createCallAgent` 메서드는 `CommunicationUserCredential`을 인수로 사용하여 [액세스 토큰](../../../access-tokens.md)을 캡슐화합니다.
`DeviceManager`에 액세스하려면 먼저 callAgent 인스턴스를 만든 다음, `CallClient.getDeviceManager` 메서드를 사용하여 DeviceManager를 가져오면 됩니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
호출자의 표시 이름을 설정하려면 다음과 같은 대체 방법을 사용합니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>발신 전화를 걸고 그룹 통화에 참가

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

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기
> [!WARNING]
> 현재는 발신 로컬 비디오 스트림 하나만 지원됩니다. 비디오를 통해 전화를 걸려면 `deviceManager` `getCameras` API를 사용하여 로컬 카메라를 열거해야 합니다.
원하는 카메라를 선택한 후에는 해당 카메라를 사용하여 `LocalVideoStream` 인스턴스를 생성하고 `call` 메서드에 대한 `localVideoStream` 배열의 항목으로 `videoOptions`에 전달합니다.
전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다.

> [!NOTE]
> 개인 정보 보호 문제 때문에 비디오를 로컬로 미리 보지 않는 경우에는 비디오가 통화에서 공유되지 않습니다.
자세한 내용은 [로컬 카메라 미리 보기](#local-camera-preview)를 참조하세요.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager(appContext).get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>그룹 통화 참가
새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 'join' 메서드를 호출하고 `groupId` 속성을 통해 개체를 전달해야 합니다. 값은 GUID여야 합니다.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>통화 수락
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


## <a name="push-notifications"></a>푸시 알림

### <a name="overview"></a>개요
모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화에서는 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 푸시 알림을 등록하고 푸시 알림을 처리한 다음, 푸시 알림을 등록 취소하겠습니다.

### <a name="prerequisites"></a>사전 요구 사항

Cloud Messaging(FCM)을 사용하도록 설정되었으며 Firebase Cloud Messaging 서비스가 Azure Notification Hub 인스턴스에 연결된 Firebase 계정 자세한 내용은 [Communication Services 알림](../../../../concepts/notifications.md)을 참조하세요.
또한 이 자습서에서는 Android Studio 버전 3.6 이상을 사용하여 애플리케이션을 빌드하는 것으로 가정합니다.

Firebase Cloud Messaging 서비스에서 알림 메시지를 받으려면 Android 애플리케이션에 대한 권한 세트가 필요합니다. `AndroidManifest.xml` 파일에서 *<manifest ...>* 바로 뒤에 또는 *</application>* 태그 바로 아래에 다음 권한 세트를 추가합니다.

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록하려면 애플리케이션이 디바이스 등록 토큰을 사용하여 *CallAgent* 인스턴스에서 `registerPushNotification()`을 호출해야 합니다.

디바이스 등록 토큰을 가져오려면 `dependencies` 섹션에서 다음 줄을 추가하여(아직 없는 경우) Firebase SDK를 애플리케이션 모듈의 *build.gradle* 파일에 추가합니다.

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

프로젝트 수준의 *build.gradle* 파일에서 다음을 `dependencies` 섹션에 추가합니다(아직 없는 경우).

```
    classpath 'com.google.gms:google-services:4.3.3'
```

다음 플러그 인이 아직 없는 경우 파일 첫 부분에 추가합니다.

```
apply plugin: 'com.google.gms.google-services'
```

도구 모음에서 *지금 동기화* 를 선택합니다. 다음 코드 조각을 추가하여 클라이언트 애플리케이션 인스턴스에 대한 Firebase Cloud Messaging SDK에서 생성한 디바이스 등록 토큰을 가져옵니다. 인스턴스에 대한 주 활동의 헤더에 아래 가져오기를 추가해야 합니다. 코드 조각이 토큰을 검색하는 데 필요합니다.

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

다음 코드 조각을 추가하여 토큰을 검색합니다.

```java
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
수신 통화 푸시 알림에 대한 Calling Services SDK에 디바이스 등록 토큰을 등록합니다.

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>푸시 알림 처리

수신 통화 푸시 알림을 받으려면 페이로드가 있는 *CallAgent* 인스턴스에서 *handlePushNotification()* 을 호출합니다.

Firebase Cloud Messaging에서 페이로드를 가져오려면 *FirebaseMessagingService* Firebase SDK 클래스를 확장하는 새 서비스를 만들고(파일 > 새로 만들기 > 서비스 > 서비스) `onMessageReceived` 메서드를 재정의합니다. 이 메서드는 Firebase Cloud Messaging에서 애플리케이션에 푸시 알림을 전달할 때 호출되는 이벤트 처리기입니다.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
`AndroidManifest.xml` 파일의 <application> 태그 내부에 다음 서비스 정의를 추가합니다.

```XML
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- 페이로드를 검색한 후에는 *CallAgent* 인스턴스에서 *handlePushNotification* 메서드를 호출하여 처리되는 내부 *IncomingCallInformation* 개체로 구문 분석될 *Communication Services* SDK에 전달할 수 있습니다. `CallAgent` 인스턴스는 `CallClient` 클래스에서 `createCallAgent(...)` 메서드를 호출하여 만듭니다.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

푸시 알림 메시지가 성공적으로 처리되고 모든 이벤트 처리기가 제대로 등록되면 애플리케이션이 전화를 겁니다.

### <a name="unregister-push-notifications"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. 등록을 취소하려면 callAgent에서 `unregisterPushNotification()` 메서드를 호출합니다.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>통화 관리
통화 중에 통화 속성에 액세스하여 비디오 및 오디오와 관련된 설정을 관리하는 다양한 작업을 수행할 수 있습니다.

### <a name="call-properties"></a>통화 속성

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

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 다음과 같이 `mute` 및 `unmute` 비동기 API를 사용합니다.

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
### <a name="change-the-volume-of-the-call"></a>통화 볼륨 변경
    
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

    
### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체의 `getCameraList` API를 사용하여 카메라를 열거해야 합니다. 그런 다음, 원하는 카메라를 전달하는 새 `LocalVideoStream` 인스턴스를 만들고, 이 인스턴스를 `startVideo` API에서 인수로 전달합니다.

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

비디오 전송을 성공적으로 시작하면 호출 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

로컬 비디오를 중지하려면 `LocalVideoStream` 컬렉션에서 사용할 수 있는 `localVideoStreams` 인스턴스를 전달합니다.

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

`LocalVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열
`remoteParticipants` 컬렉션은 특정 통화의 원격 참가자 목록을 반환합니다.
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
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

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링
원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
원격 참가자의 `RemoteVideoStream`을 렌더링하려면 `OnVideoStreamsUpdated` 이벤트를 구독해야 합니다.

이 이벤트 내에서 `isAvailable` 속성이 true로 변경되면 원격 참가자가 현재 스트림을 보내고 있다는 뜻입니다. 이 경우 새 `Renderer` 인스턴스를 만든 다음, 비동기 `createView` API를 사용하여 새 `RendererView`를 만들고 애플리케이션의 UI 내 아무 위치에나 `view.target`을 연결합니다.

원격 스트림의 가용성이 변경될 때마다 전체 렌더러, 즉, 특정 `RendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성
원격 비디오 스트림에는 몇 가지 속성이 있습니다.

* `Id` - 원격 비디오 스트림의 ID
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` - 'Video' 또는 'ScreenSharing' 중 하나
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable` - 원격 참가자 엔드포인트가 스트림을 능동적으로 전송하고 있는지 여부를 표시
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>렌더러 메서드 및 속성
API를 따르는 렌더러 개체

* 나중에 애플리케이션 UI에서 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `VideoStreamRendererView` 인스턴스를 만듭니다.
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* 렌더러 및 이 렌더러와 연결된 모든 `VideoStreamRendererView`를 삭제합니다. UI에서 연결된 모든 보기를 제거할 때 호출됩니다.
```java
VideoStreamRenderer.dispose()
```

* `StreamSize` - 원격 비디오 스트림의 크기(너비/높이)
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>RendererView 메서드 및 속성
`VideoStreamRendererView`를 만들 때 이 뷰에 적용될 `ScalingMode` 및 `mirrored` 속성을 지정할 수 있습니다. 스케일링 모드는 ‘CROP’ | ‘FIT’ 중 하나로 설정할 수 있습니다.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

이렇게 만든 RendererView는 다음 코드 조각을 사용하여 애플리케이션 UI에 연결할 수 있습니다.
```java
layout.addView(rendererView);
```

나중에 RendererView 개체에서 ScalingMode.CROP | ScalingMode.FIT 중 하나를 인수로 사용하고 `updateScalingMode` API를 호출하여 스케일링 모드를 업데이트할 수 있습니다.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```


## <a name="device-management"></a>디바이스 관리

`DeviceManager`를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용하여 사용자의 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.
> [!WARNING]
> 현재는 DeviceManager에 대한 액세스 권한을 얻으려면 먼저 `callAgent` 개체를 인스턴스화해야 합니다.

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>로컬 디바이스 열거

로컬 디바이스에 액세스하려면 디바이스 관리자에서 열거 메서드를 사용합니다. 열거는 비동기 작업입니다.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`DeviceManager` 및 `Renderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
videoOptions = new VideoOptions(localVideoStreams);

VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
VideoStreamRendererView uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="record-calls"></a>통화 녹음
> [!WARNING]
> ACS 호출 Android SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isRecordingActive` 및 `addOnIsRecordingActiveChangedListener`가 `Call` 개체의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.

> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 Android SDK의 '베타' 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```java
RecordingFeature callRecordingFeature = call.api(RecordingFeature.class);
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingFeature`의 `isRecordingActive` 속성을 검사합니다. `boolean`를 반환합니다.

```java
boolean isRecordingActive = callRecordingFeature.isRecordingActive();
```

레코딩 변화를 구독할 수도 있습니다.

```java
private void handleCallOnIsRecordingChanged(PropertyChangedEvent args) {
    boolean isRecordingActive = callRecordingFeature.isRecordingActive();
}

callRecordingFeature.addOnIsRecordingActiveChangedListener(handleCallOnIsRecordingChanged);

```

애플리케이션에서 녹음/녹화를 시작하려면 먼저 [통화 녹음/녹화 개요](../../../../concepts/voice-video-calling/call-recording.md)에 따라 통화 녹음/녹화를 설정하는 단계를 따르세요.

서버에 통화 녹음/녹화를 설정하고 나면 Android 애플리케이션에서 통화에서 `ServerCallId` 값을 얻은 다음 이를 서버로 보내 녹음/녹화 프로세스를 시작해야 합니다. `ServerCallId` 값은 `getInfo()`를 사용하여 클래스 개체에서 찾을 수 있는 `CallInfo` 클래스의 `getServerCallId()`를 사용하여 찾을 수 있습니다.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to start the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

서버에서 녹음/녹화가 시작되면 `handleCallOnIsRecordingChanged` 이벤트가 트리거되고 `callRecordingFeature.isRecordingActive()`의 값은 `true`가 됩니다.

통화 녹음/녹화를 시작하는 것과 마찬가지로 통화 녹음/녹화를 중지하려면 `ServerCallId`를 가져와 녹음/녹화 서버로 보내 통화 녹음/녹화를 중지해야 합니다.

```java
try {
    String serverCallId = call.getInfo().getServerCallId().get();
    // Send serverCallId to your recording server to stop the call recording.
} catch (ExecutionException | InterruptedException e) {
} catch (UnsupportedOperationException unsupportedOperationException) {
}
```

서버에서 녹음/녹화가 중지되면 `handleCallOnIsRecordingChanged` 이벤트가 트리거되고 `callRecordingFeature.isRecordingActive()` 값은 `false`가 됩니다.


## <a name="call-transcription"></a>통화 대화 내용 기록
> [!WARNING]
> ACS 호출 Android SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isTranscriptionActive` 및 `addOnIsTranscriptionActiveChangedListener`가 `Call` 개체의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.
    
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 Android SDK의 '베타' 릴리스를 사용하세요.

통화 대화 내용 기록은 핵심 `Call` API의 확장 기능입니다. 먼저 대화 내용 기록 기능 API 개체를 가져와야 합니다.

```java
TranscriptionFeature callTranscriptionFeature = call.api(TranscriptionFeature.class);
```

그런 다음, 통화 대화 내용이 기록되고 있는지 확인하려면 `callTranscriptionFeature`의 `isTranscriptionActive` 속성을 검사합니다. `boolean`를 반환합니다.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

또한 대화 내용 기록의 변경 사항을 구독할 수 있습니다.

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);

```    

## <a name="eventing-model"></a>이벤트 모델
값이 변경될 때 알림을 받도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
`property changed` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
