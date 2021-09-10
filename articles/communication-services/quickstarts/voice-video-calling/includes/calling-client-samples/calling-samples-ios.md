---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: aaf99113e3d7fd1190266976e9475e33272d338c
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078284"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Azure Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. [사용자 액세스 토큰을 가져옵니다](../../../access-tokens.md).
- 선택 사항: [앱에 음성 통화를 추가](../../getting-started-with-calling.md) 빠른 시작을 완료합니다.

## <a name="set-up-your-system"></a>시스템 설정

### <a name="create-the-xcode-project"></a>Xcode 프로젝트 만들기

Xcode에서 새 iOS 프로젝트를 만들고 **단일 보기 앱** 템플릿을 선택합니다. 이 빠른 시작에서는 [SwiftUI 프레임워크](https://developer.apple.com/xcode/swiftui/)를 사용하므로 **언어** 를 **Swift** 로 설정하고 **사용자 인터페이스** 를 **SwiftUI** 로 설정해야 합니다. 

이 빠른 시작 중에는 단위 테스트 또는 UI 테스트를 만들지 않습니다. **단위 테스트 포함** 및 **UI 테스트 포함** 텍스트 상자 선택을 취소할 수 있습니다.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Xcode 내에서 프로젝트를 만드는 창을 보여주는 스크린샷.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치

1. 다음과 같이 애플리케이션에 대한 Podfile을 만듭니다.

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

2. `pod install`을 실행합니다.
3. Xcode에서 `.xcworkspace`를 엽니다.

### <a name="request-access-to-the-microphone"></a>마이크에 대한 액세스 요청

디바이스의 마이크에 액세스하려면 앱의 정보 속성 목록을 `NSMicrophoneUsageDescription`으로 업데이트해야 합니다. 연결된 값을 시스템이 사용자의 액세스를 요청하는 데 사용하는 대화 상자에 포함될 `string`으로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기** > **소스 코드** 를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트의 *ContentView.swift* 파일을 열고 파일 맨 위에 `import` 선언을 추가하여 `AzureCommunicationCalling` 라이브러리를 가져옵니다. 추가로 `AVFoundation`을 가져옵니다. 코드에서 오디오 권한 요청에 필요합니다.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>개체 모델 학습

iOS용 Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

> [!NOTE]
> 이 빠른 시작에서는 Calling SDK의 버전 1.0.0-beta.8을 사용합니다.


| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient`는 Calling SDK의 주 진입점입니다.|
| `CallAgent` | `CallAgent`는 통화를 시작하고 관리하는 데 사용됩니다. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential`은 `CallAgent`를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.| 
| `CommunicationIdentifier` | `CommunicationIdentifier`는 사용자의 ID를 나타내는 데 사용됩니다. ID는 `CommunicationUserIdentifier`, `PhoneNumberIdentifier` 또는 `CallingApplication`일 수 있습니다. |

> [!NOTE]
> 애플리케이션에서 이벤트 대리자를 구현할 때 이벤트 구독이 필요한 개체에 대한 강력한 참조를 보유해야 합니다. 예를 들어 `call.addParticipant` 메서드를 호출할 때 `RemoteParticipant` 개체가 반환되고 애플리케이션에서 `RemoteParticipantDelegate`를 수신 대기하도록 대리자를 설정하는 경우 애플리케이션은 `RemoteParticipant` 개체에 대한 강력한 참조를 보유해야 합니다. 그렇지 않으면 이 개체가 수집될 경우 호출 SDK가 개체를 호출하려고 할 때 대리자가 치명적인 예외를 throw합니다.

## <a name="initialize-callagent"></a>CallAgent 초기화

`CallAgent`에서 `CallClient` 인스턴스를 만들려면 초기화된 후 비동기적으로 `callClient.createCallAgent` 개체를 반환하는 `CallAgent` 메서드를 사용해야 합니다.

통화 클라이언트를 만들려면 `CommunicationTokenCredential` 개체를 전달해야 합니다.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

만든 `CommunicationTokenCredential` 개체를 `CallClient`에 전달하고 표시 이름을 설정합니다.

```swift

self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

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

### <a name="place-a-11-call-with-video"></a>비디오로 1:1 통화
디바이스 관리자 인스턴스를 가져오려면 [디바이스 관리](#manage-devices)에 대한 섹션을 참조하세요.

```swift

let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
     if error == nil {
         print("Successfully started outgoing video call")
         self.call = call
     } else {
         print("Failed to start outgoing video call")
     }
}

```

### <a name="join-a-group-call"></a>그룹 통화 참가
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

### <a name="subscribe-to-an-incoming-call"></a>수신 전화 구독
수신 전화 이벤트를 구독합니다.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingcall: IncomingCall) {
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

## <a name="set-up-push-notifications"></a>푸시 알림 설정

모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화에서는 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 

다음 섹션에서는 푸시 알림을 등록, 처리 및 등록 취소하는 방법을 설명합니다. 이러한 작업을 시작하기 전에 다음 필수 조건을 완료해야 합니다.

1. Xcode에서 **서명 및 기능** 으로 이동합니다. **+ 기능** 을 선택하여 기능을 추가한 다음, **푸시 알림** 을 선택합니다.
2. **+ 기능** 을 선택하여 다른 기능을 추가한 다음, **백그라운드 모드** 를 선택합니다.
3. **백그라운드 모드** 에서 **Voice over IP** 및 **원격 알림** 확인란을 선택합니다.

:::image type="content" source="../../media/ios/xcode-push-notification.png" alt-text="Xcode에서 기능을 추가하는 방법을 보여주는 스크린샷." lightbox="../../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록하려면 디바이스 등록 토큰을 사용하여 `CallAgent` 인스턴스에서 `registerPushNotification()`을 호출해야 합니다.

성공적으로 초기화한 이후 푸시 알림에 대한 등록이 이루어져야 합니다. `callAgent` 개체가 destroy되면 푸시 알림을 자동으로 등록 취소하는 `logout`이 호출됩니다.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>푸시 알림 처리
수신 전화에 대한 푸시 알림을 받으려면 사전 페이로드를 사용하여 `CallAgent` 인스턴스에서 `handlePushNotification()`을 호출합니다.

```swift

let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}

```
### <a name="unregister-push-notifications"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. `CallAgent`에서 `unregisterPushNotification` 메서드를 호출하기만 하면 됩니다.

> [!NOTE]
> 로그아웃 시 애플리케이션이 푸시 알림에서 자동으로 등록 취소되지 않습니다.

```swift

callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
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

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

통화 중 다른 참가자에게 로컬 비디오 보내기를 시작하려면 `startVideo` API를 사용하고 `camera`로 `localVideoStream`을 전달합니다.

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
let localVideoStream = LocalVideoStream(camera: firstCamera!)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

비디오 보내기를 시작한 이후 `LocalVideoStream` 인스턴스가 호출 인스턴스의 `localVideoStreams` 컬렉션에 추가됩니다.

```swift

call.localVideoStreams

```

로컬 비디오를 중지하려면 `call.startVideo` 호출에서 반환된 `localVideoStream` 인스턴스를 전달합니다. 비동기 작업입니다.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열

```swift

call.remoteParticipants

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

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자는 전화 통화 중에 비디오 또는 화면 공유를 시작할 수 있습니다.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>원격 참가자의 비디오 공유 또는 화면 공유 스트림 처리

원격 참가자의 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>원격 비디오 스트림 속성 가져오기

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>원격 참가자 스트림 렌더링

원격 참가자 스트림 렌더링을 시작하려면 다음 코드를 사용합니다.

```swift

let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>원격 비디오 렌더러 메서드 및 속성 가져오기

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>디바이스 관리

`DeviceManager`를 사용하여 오디오 또는 비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 사용자에게 마이크 또는 카메라 액세스 권한을 요청할 수 있습니다. `callClient` 개체에서 `deviceManager`에 액세스할 수 있습니다.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>로컬 디바이스 열거

로컬 디바이스에 액세스하려면 디바이스 관리자에서 열거 메서드를 사용합니다. 열거는 비동기 작업입니다.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]

``` 

### <a name="get-a-local-camera-preview"></a>로컬 카메라 미리 보기 가져오기

`Renderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()

```

### <a name="get-local-camera-preview-properties"></a>로컬 카메라 미리 보기 속성 가져오기

렌더러에는 렌더링을 제어할 수 있는 속성 및 메서드 집합이 있습니다.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="record-calls"></a>통화 녹음
> [!WARNING]
> ACS 통화 iOS SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isRecordingActive`가 `Call` 개체의 일부이고 `didChangeRecordingState`는 `CallDelegate` 대리자의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 iOS SDK의 ‘베타’ 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```swift
let callRecordingFeature = call.api(RecordingFeature.self)
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingFeature`의 `isRecordingActive` 속성을 검사합니다. `Bool`를 반환합니다.

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

이벤트 `didChangeRecordingState`를 사용하여 클래스에 `RecordingFeatureDelegate` 대리자를 구현하여 변경 내용 기록을 구독할 수도 있습니다.

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingFeatureDelegate
public func recordingFeature(_ recordingFeature: RecordingFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

애플리케이션에서 녹음/녹화를 시작하려면 먼저 [통화 녹음/녹화 개요](../../../../concepts/voice-video-calling/call-recording.md)에 따라 통화 녹음/녹화를 설정하는 단계를 따르세요.

서버에 통화 녹음/녹화를 설정하고 나면 iOS 애플리케이션에서 통화에서 `ServerCallId` 값을 얻은 다음, 이를 서버로 보내 녹음/녹화 프로세스를 시작해야 합니다. `ServerCallId` 값은 `getInfo()`를 사용하여 클래스 개체에서 찾을 수 있는 `CallInfo` 클래스의 `getServerCallId()`를 사용하여 찾을 수 있습니다.

```swift
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
// Send serverCallId to your recording server to start the call recording.
```

서버에서 녹음/녹화가 시작되면 `didChangeRecordingState` 이벤트가 트리거되고 `recordingFeature.isRecordingActive`의 값은 `true`가 됩니다.

통화 녹음/녹화를 시작하는 것과 마찬가지로 통화 녹음/녹화를 중지하려면 `ServerCallId`를 가져와 녹음/녹화 서버로 보내 통화 녹음/녹화를 중지해야 합니다.

```swift
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
// Send serverCallId to your recording server to stop the call recording.
```

서버에서 녹음/녹화가 중지되면 `didChangeRecordingState` 이벤트가 트리거되고 `recordingFeature.isRecordingActive` 값은 `false`가 됩니다.

## <a name="call-transcription"></a>통화 대화 내용 기록
> [!WARNING]
> ACS 통화 iOS SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isTranscriptionActive`가 `Call` 개체의 일부이고 `didChangeTranscriptionState`는 `CallDelegate` 대리자의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 iOS SDK의 ‘베타’ 릴리스를 사용하세요.

통화 대화 내용 기록은 핵심 `Call` API의 확장 기능입니다. 먼저 대화 내용 기록 기능 API 개체를 가져와야 합니다.

```swift
let callTranscriptionFeature = call.api(TranscriptionFeature.self)
```

그런 다음, 통화 대화 내용이 기록되고 있는지 확인하기 위해 `callTranscriptionFeature`의 `isTranscriptionActive` 속성을 검사합니다. `Bool`를 반환합니다.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

이벤트 `didChangeTranscriptionState`를 사용하여 클래스에 `TranscriptionFeatureDelegate` 대리자를 구현하여 전사 변경 내용을 구독할 수도 있습니다.

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionFeatureDelegate
public func transcriptionFeature(_ transcriptionFeature: TranscriptionFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = transcriptionFeature.isTranscriptionActive
}
```

## <a name="subscribe-to-notifications"></a>알림 구독

값이 변경될 때 알림을 받도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
`property changed` 이벤트를 구독하려면 다음 코드를 사용합니다.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하려면 다음 코드를 사용합니다.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func call(_ call: Call, didUpdateLocalVideoStreams args: LocalVideoStreamsUpdatedEventArgs) {
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
