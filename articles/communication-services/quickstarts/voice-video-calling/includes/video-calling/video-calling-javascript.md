---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e8a00161e9619a27b371ed80a61d7b3ea5d3e9c6
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112536178"
---
Communication Services Calling SDK를 사용하여 앱에 1:1 영상 통화를 추가하여 Azure Communication Services를 시작하세요. JavaScript용 Azure Communication Services Calling SDK를 사용하여 영상 통화를 시작하고 응답하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

끝으로 건너뛰려면 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)에서 이 빠른 시작을 샘플로 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
- 활성 구독이 있는 Azure 계정을 얻습니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1)
- 활성 Communication Services 리소스를 만듭니다. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰을 만듭니다. [사용자 액세스 토큰 만들기 및 관리 방법에 대해 알아봅니다.](../../../access-tokens.md?pivots=programming-language-csharp)

## <a name="setting-up"></a>설치
### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기
터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>패키지 설치
`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services Calling SDK를 설치합니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 Azure Communication Services Calling SDK 버전 `1.1.0-beta.1`을 사용합니다. 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling@1.1.0-beta.1 --save
```
### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

이 빠른 시작에서는 webpack을 사용하여 애플리케이션 자산을 번들로 묶습니다. 다음 명령을 실행하여 `webpack`, `webpack-cli` 및 `webpack-dev-server` npm 패키지를 설치하고 `package.json`에 개발 종속성으로 나열합니다.

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
프로젝트의 루트 디렉터리에 `index.html` 파일을 만듭니다. 이 파일을 사용하여 사용자가 1:1 영상 통화를 걸 수 있도록 하는 기본 레이아웃을 구성합니다.

코드는 다음과 같습니다.
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

이 빠른 시작에 대한 애플리케이션 로직을 포함하기 위해 `client.js`라는 프로젝트의 루트 디렉터리에 파일을 만듭니다. 다음 코드를 추가하여 통화 클라이언트를 가져오고 DOM 요소에 대한 참조를 가져옵니다.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름      | 설명 | 
| :---        |    :----   |
| CallClient  | CallClient는 Calling SDK의 주 진입점입니다.      |
| CallAgent  | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다.        |
| DeviceManager | DeviceManager는 미디어 디바이스를 관리하는 데 사용됩니다.    |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 클래스는 CallAgent를 인스턴스화하는 데 사용되는 CommunicationTokenCredential 인터페이스를 구현합니다.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>클라이언트 인증 및 DeviceManager 액세스

리소스의 경우 **<USER_ACCESS_TOKEN>을 유효한 사용자 액세스 토큰으로 바꿔야 합니다**. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰 설명서](../../../access-tokens.md)를 참조하세요. 

`CallClient`를 사용하여 호출을 수행하고 받을 수 있도록 하는 `CommunicationUserCredential`을 사용하여 `CallAgent` 인스턴스를 초기화합니다. 

`DeviceManager`에 액세스하려면 먼저 callAgent 인스턴스를 만들어야 합니다. 그런 다음, `CallClient` 인스턴스에서 `getDeviceManager` 메서드를 사용하여 `DeviceManager`를 가져올 수 있습니다.

다음 코드를 `client.js`에 추가합니다.

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    // Receive an incoming call
    // To handle incoming calls you need to listen to the `incomingCall` event of `callAgent`. Once there is an incoming call, you need to enumerate local cameras and construct 
    // a `LocalVideoStream` instance to send a video stream to the other participant. You also need to subscribe to `remoteParticipants` to handle remote video streams. You can 
    // accept or reject the call through the `incomingCall` instance. 
    callAgent.on('incomingCall', async e => {
        const videoDevices = await deviceManager.getCameras();
        const videoDeviceInfo = videoDevices[0];
        localVideoStream = new LocalVideoStream(videoDeviceInfo);
        localVideoView();

        stopVideoButton.disabled = false;
        callButton.disabled = true;
        hangUpButton.disabled = false;

        const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
        call = addedCall;

        subscribeToRemoteParticipantInCall(addedCall);  
    });
    
    // Subscribe to call updates
    // You need to subscribe to the event when the remote participant ends the call to dispose of video renderers and toggle button states. 
    callAgent.on('callsUpdated', e => {
        e.removed.forEach(removedCall => {
            // dispose of video renders
            rendererLocal.dispose();
            rendererRemote.dispose();
            // toggle button states
            hangUpButton.disabled = true;
            callButton.disabled = false;
            stopVideoButton.disabled = true;
        })
    })

    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}

init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>사용자에게 1:1 발신 영상 통화 걸기

`callButton`을 클릭할 때 통화를 시작하는 이벤트 수신기를 추가합니다.

먼저 deviceManager `getCameraList` API를 사용하여 로컬 카메라를 열거해야 합니다. 이 빠른 시작에서 컬렉션의 첫 번째 카메라를 사용합니다. 원하는 카메라가 선택되면 LocalVideoStream 인스턴스가 생성되고 `videoOptions` 내에서 localVideoStream 배열 내의 항목으로 호출 메서드에 전달됩니다. 통화가 연결되면 자동으로 다른 참가자에게 비디오 스트림을 보내기 시작합니다. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
`LocalVideoStream`을 렌더링하려면 `VideoStreamRenderer`의 새 인스턴스를 만든 다음, 비동기 `createView` 메서드를 사용하여 새 `VideoStreamRendererView` 인스턴스를 만들어야 합니다. 그러면 모든 UI 요소에 `view.target`을 연결할 수 있습니다. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
모든 원격 참가자는 통화 인스턴스의 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다. 새 원격 참가자가 통화에 추가될 때 알림을 받으려면 이벤트 `remoteParticipantsUpdated`를 수신해야 합니다. 또한 `remoteParticipants` 컬렉션을 반복하여 각 컬렉션에 구독해야 비디오 스트림을 구독할 수 있습니다. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
원격 참가자의 추가 비디오 스트림을 처리하려면 `videoStreamsUpdated` 이벤트를 구독해야 합니다. 현재 통화의 `remoteParticipants` 컬렉션을 진행하는 동안 `videoStreams` 컬렉션을 검사하여 각 참가자의 스트림을 나열할 수 있습니다.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
`remoteVideoStream`을 렌더링하려면 `isAvailableChanged` 이벤트를 구독해야 합니다. `isAvailable` 속성이 `true`로 변경되면 원격 참가자가 스트림을 보내는 것입니다. 원격 스트림의 가용성이 변경될 때마다 전체 `Renderer`, 즉, 특정 `RendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
`RemoteVideoStream`을 렌더링하려면 `VideoStreamRenderer`의 새 인스턴스를 만든 다음, 비동기 `createView` 메서드를 사용하여 새 `VideoStreamRendererView` 인스턴스를 만들어야 합니다. 그러면 모든 UI 요소에 `view.target`을 연결할 수 있습니다. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```

## <a name="end-the-current-call"></a>현재 통화 종료
`hangUpButton`을 클릭하면 현재 호출을 종료하는 이벤트 수신기를 추가합니다.
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```

## <a name="start-and-end-video-during-the-call"></a>통화 중 비디오 시작 및 종료
현재 통화 중에 이벤트 수신기를 비디오 중지 단추에 추가하여 `localVideoStream`의 렌더러를 삭제하여 비디오를 중지할 수 있습니다. 
 ```JavaScript
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
비디오 시작 단추에 이벤트 수신기를 추가하여 현재 통화 중에 비디오가 중지되었을 때 비디오를 다시 켤 수 있습니다. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>코드 실행
`webpack-dev-server`를 사용하여 앱을 빌드하고 실행합니다. 다음 명령을 실행하여 로컬 웹 서버에서 애플리케이션 호스트를 번들로 묶습니다.

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
브라우저를 열고 http://localhost:8080/로 이동합니다. 다음이 표시되어야 합니다.

:::image type="content" source="../../media/javascript/1-on-1-video-calling.png" alt-text="1대1 영상 통화 페이지":::

텍스트 필드에서 사용자 ID를 제공하고 호출 시작 단추를 클릭하여 1:1 발신 영상 통화를 수행할 수 있습니다.
