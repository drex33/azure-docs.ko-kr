---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705615"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>디바이스 관리
을 호출 하 여 비디오를 사용 하기 시작 하려면 장치를 관리 하는 방법을 알아야 합니다. 장치를 사용 하면 통화에 대 한 오디오 및 비디오 전송 대상을 제어할 수 있습니다.

`deviceManager`의 통화에서 오디오 및 비디오 스트리밍을 전송할 수 있는 로컬 디바이스를 열거할 수 있습니다. 또한 이를 사용하여 로컬 디바이스의 마이크 및 카메라에 대한 액세스 권한을 요청할 수 있습니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>로컬 디바이스 가져오기

로컬 디바이스에 액세스하려면 `deviceManager`에서 열거 메서드를 사용합니다. 열거는 비동기 작업입니다.

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>기본 마이크 및 스피커 설정

`deviceManager`에서 통화를 시작하는 데 사용할 기본 디바이스를 설정할 수 있습니다. 클라이언트 기본값이 설정되지 않은 경우 Communication Services는 운영 체제 기본값을 사용합니다.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`deviceManager` 및 `VideoStreamRenderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);
```

### <a name="request-permission-to-camera-and-microphone"></a>카메라 및 마이크 사용 권한 요청

사용자에게 카메라 및/또는 마이크 사용 권한을 부여하라는 메시지를 표시합니다.

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

이는 `audio` 및 `video` 사용 권한이 부여되었는지 여부를 나타내는 개체를 사용하여 확인됩니다.

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>참고
- 'videoDevicesUpdated' 이벤트는 비디오 디바이스가 연결/연결 해제될 때 발생합니다.
- 'audioDevicesUpdated' 이벤트는 오디오 디바이스가 연결될 때 발생합니다.
- DeviceManager가 만들어지면 처음에는 사용 권한이 아직 부여되지 않은 경우 디바이스에 대한 정보를 알 수 없으므로 초기에는 디바이스 목록이 비어 있습니다. 그런 다음, DeviceManager.askPermission() API를 호출하면 사용자에게 디바이스 액세스를 요청하는 메시지가 표시되고, 사용자가 '허용'을 클릭하여 액세스 권한을 부여하는 경우 디바이스 관리자는 시스템의 디바이스에 대해 알아보고 디바이스 목록을 업데이트한 후 'audioDevicesUpdated' 및 'videoDevicesUpdated' 이벤트를 내보냅니다. 페이지를 새로 고치고 디바이스 관리자를 만드는 경우를 가정해보겠습니다. 사용자가 이전에 액세스 권한을 이미 부여했기 때문에 디바이스 관리자는 디바이스에 대해 알아볼 수 있습니다. 따라서 처음에는 디바이스 목록이 채워지고 'audioDevicesUpdated' 또는 'videoDevicesUpdated' 이벤트가 내보내지지 않습니다.
- Android Chrome, iOS Safari 또는 macOS Safari에서는 스피커 열거/선택이 지원 되지 않습니다.

## <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체의 `getCameras` 메서드를 사용하여 카메라를 열거해야 합니다. 그런 다음 원하는 카메라를 사용 하 여의 새 인스턴스를 만든 `LocalVideoStream` 다음 `LocalVideoStream` 개체를 `startVideo` 기존 호출 개체의 메서드에 전달 합니다.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

비디오 전송을 성공적으로 시작하면 통화 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```js
call.localVideoStreams[0] === localVideoStream;
```

로컬 비디오를 중지하려면 `localVideoStreams` 컬렉션에서 사용할 수 있는 `localVideoStream` 인스턴스를 전달합니다.

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```

,,, `localVideoStream` 및 호출에서 인스턴스를 전달 하 여 비디오를 시작할 수 있는 4 가지 메서드가 있습니다 `callAgent.startCall()` `callAgent.join()` `call.accept()` `call.startVideo()` . 를 사용 하려면 `call.stopVideo()` `localVideoStream` 비디오를 시작 하는 데 사용 되는 원래 메서드에 전달 된 것과 동일한 인스턴스를 전달 해야 합니다.

`localVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

지정된 비디오 디바이스가 다른 프로세스에서 사용되고 있거나 시스템에서 사용하지 않도록 설정되어 있는 경우:
- 호출 하는 동안 비디오가 꺼져 있는 경우를 사용 하 여 비디오를 시작 하면 `call.startVideo()` 이 메서드는을 throw 하 `SourceUnavailableError` 고가 `cameraStartFiled` true로 설정 됩니다.
- 메서드를 호출 하면 `localVideoStream.switchSource()` 이 `cameraStartFailed` true로 설정 됩니다.
호출 진단 가이드에서는 호출 관련 문제를 진단 하는 방법에 대 한 추가 정보를 제공 합니다.

## <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기

> [!IMPORTANT]
> 현재는 나가는 로컬 비디오 스트림이 하나만 지원 됩니다.

화상 통화를 하려면 `deviceManager`에서 `getCameras()` 메서드를 사용하여 로컬 카메라를 열거해야 합니다.

카메라를 선택한 후에는 이를 사용하여 `LocalVideoStream` 인스턴스를 생성합니다. 이를 `videoOptions` 내에서 `localVideoStream` 배열 내의 한 항목으로 `startCall` 메서드에 전달합니다.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- 전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다. 이는 `Call.Accept()` 비디오 옵션과 `CallAgent.join()` 비디오 옵션에도 적용됩니다.

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

`RemoteVideoStream`을 렌더링하려면 `isAvailableChanged` 이벤트를 구독해야 합니다. `isAvailable` 속성이 `true`로 변경되면 원격 참가자가 스트림을 보내는 것입니다. 이 경우 `VideoStreamRenderer`의 새 인스턴스를 만든 다음, 비동기 `createView` 메서드를 사용하여 새 `VideoStreamRendererView` 인스턴스를 만듭니다.  그러면 모든 UI 요소에 `view.target`을 연결할 수 있습니다.

원격 스트림의 가용성이 변경될 때마다 전체 `VideoStreamRenderer`, 즉, 특정 `VideoStreamRendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성

원격 비디오 스트림에는 다음과 같은 속성이 있습니다.

- `id`: 원격 비디오 스트림의 ID입니다.

```js
const id: number = remoteVideoStream.id;
```

- `mediaStreamType`: `Video` 또는 `ScreenSharing`일 수 있습니다.

```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```

- `isAvailable`: 원격 참가자 엔드포인트가 스트림을 능동적으로 전송하고 있는지 여부입니다.

```js
const type: boolean = remoteVideoStream.isAvailable;
```

## <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 메서드 및 속성
애플리케이션 UI에 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `VideoStreamRendererView` 인스턴스를 만들고, 비동기 `createView()` 메서드를 사용합니다. 이 메서드는 스트림을 렌더링할 준비가 되었을 때를 확인하고 DOM 트리의 아무 곳에나 추가될 수 있는 `video` 요소를 나타내는 `target` 속성이 있는 개체를 반환합니다.

```js
await videoStreamRenderer.createView();
```

`videoStreamRenderer` 및 모든 연결된 `VideoStreamRendererView` 인스턴스를 삭제합니다.
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 메서드 및 속성

`VideoStreamRendererView`를 만들 때 `scalingMode` 및 `isMirrored` 속성을 지정할 수 있습니다. `scalingMode`는 `Stretch`, `Crop` 또는 `Fit`일 수 있습니다. `isMirrored`가 지정된 경우 렌더링된 스트림은 세로로 대칭 이동됩니다.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```
모든 `VideoStreamRendererView` 인스턴스에는 렌더링 화면을 나타내는 `target` 속성이 있습니다. 애플리케이션 UI에서 이 속성을 연결합니다.

```js
htmlElement.appendChild(view.target);
```

나중에 `updateScalingMode` 메서드를 호출하여 `scalingMode`를 업데이트할 수 있습니다.

```js
view.updateScalingMode('Crop');
```