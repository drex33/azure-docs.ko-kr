---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 4c95736394cd112daa48f3bfa6a47ae4ba0f147d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705612"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="manage-devices"></a>디바이스 관리
통화로 비디오 사용을 시작하려면 디바이스를 관리하는 방법을 알아야 합니다. 디바이스를 사용하면 오디오 및 비디오를 통화로 전송하는 내용을 제어할 수 있습니다.

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

## <a name="place-a-11-call-with-video"></a>비디오로 1:1 통화
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