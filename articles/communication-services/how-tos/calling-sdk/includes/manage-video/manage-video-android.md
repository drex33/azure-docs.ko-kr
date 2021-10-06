---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d77857152ad381bb12e5bacc9889c10ca633948
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585282"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>디바이스 관리
을 호출 하 여 비디오를 사용 하기 시작 하려면 장치를 관리 하는 방법을 알아야 합니다. 장치를 사용 하면 통화에 대 한 오디오 및 비디오 전송 대상을 제어할 수 있습니다.

`DeviceManager`를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용하여 사용자의 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.

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
VideoDeviceInfo videoDevice = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(videoDevice, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

RenderingOptions renderingOptions = new RenderingOptions(ScalingMode.Fit);
VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);

VideoStreamRendererView uiView = previewRenderer.createView(renderingOptions);

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기
> [!WARNING]
> 현재는 발신 로컬 비디오 스트림 하나만 지원됩니다. 비디오를 통해 전화를 걸려면 `deviceManager` `getCameras` API를 사용하여 로컬 카메라를 열거해야 합니다.
원하는 카메라를 선택한 후에는 해당 카메라를 사용하여 `LocalVideoStream` 인스턴스를 생성하고 `call` 메서드에 대한 `localVideoStream` 배열의 항목으로 `videoOptions`에 전달합니다.
호출이 연결 되 면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작 합니다.

> [!NOTE]
> 개인 정보 보호 문제 때문에 비디오를 로컬로 미리 보지 않는 경우에는 비디오가 통화에서 공유되지 않습니다.
자세한 내용은 [로컬 카메라 미리 보기](#local-camera-preview)를 참조하세요.

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

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

## <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체의 `getCameraList` API를 사용하여 카메라를 열거해야 합니다. 그런 다음, 원하는 카메라를 전달하는 새 `LocalVideoStream` 인스턴스를 만들고, 이 인스턴스를 `startVideo` API에서 인수로 전달합니다.

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);

VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);

Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

비디오 전송을 성공적으로 시작하면 호출 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```java
List<LocalVideoStream> videoStreams = call.getLocalVideoStreams();
LocalVideoStream currentLocalVideoStream = videoStreams.get(0); // Please make sure there are VideoStreams in the list before calling get(0).
```

로컬 비디오를 중지하려면 `LocalVideoStream` 컬렉션에서 사용할 수 있는 `localVideoStreams` 인스턴스를 전달합니다.

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

`LocalVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
RemoteParticipant remoteParticipant = remoteParticipants.get(0); // Please make sure there are remote participants in the list before calling get(0).

List<RemoteVideoStream> remoteStreams = remoteParticipant.getVideoStreams();
RemoteVideoStream remoteParticipantStream = remoteStreams.get(0); // Please make sure there are video streams in the list before calling get(0).

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
