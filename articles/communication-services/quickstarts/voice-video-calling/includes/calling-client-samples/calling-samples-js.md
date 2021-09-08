---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 1bba08e751afa5e3cd5c51401ca92e3b5bd9def1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454120"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../../access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작을 완료하여 [애플리케이션에 음성 통화를 추가](../../getting-started-with-calling.md)합니다.

## <a name="install-the-sdk"></a>SDK 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 통화 SDK 및 일반 SDK를 설치합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
Communication Services 웹 호출 SDK는 `https`를 통해 사용해야 합니다. 로컬 개발의 경우 `localhost` 또는 로컬 'file:'을 사용합니다.

## <a name="documentation-support"></a>설명서 지원
- [릴리스 정보](https://github.com/Azure/Communication/blob/master/releasenotes/acs-javascript-calling-library-release-notes.md)
- [Github에서 문제/버그 제출](https://github.com/Azure/Communication/issues)
- [샘플 애플리케이션](../../../../samples/overview.md)
- [API 참조](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>모델
### <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                                | Description                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | 통화 SDK의 주 진입점입니다.                                                                                                 |
| `AzureCommunicationTokenCredential` | `callAgent`를 인스턴스화하는 데 사용되는 `CommunicationTokenCredential` 인터페이스를 구현합니다.                                       |
| `CallAgent`                         | 통화를 시작하고 관리하는 데 사용됩니다.                                                                                                          |
| `DeviceManager`                     | 미디어 디바이스를 관리하는 데 사용됩니다.                                                                                                            |
| `Call`                              | 호출을 나타내는 데 사용됩니다.                                                                                                              |
| `LocalVideoStream`                  | 로컬 시스템의 카메라 디바이스에 대한 로컬 비디오 스트림을 만드는 데 사용됩니다.                                                          |
| `RemoteParticipant`                 | 호출에서 원격 참가자를 나타내는 데 사용됩니다.                                                                                   |
| `RemoteVideoStream`                 | 원격 참가자로부터 원격 비디오 스트림을 나타내는 데 사용됩니다.                                                                  |

> [!NOTE]
> 호출 SDK 개체 인스턴스는 일반 JavaScript 개체로 간주하면 안 됩니다. 다양한 클래스의 실제 인스턴스이므로 직렬화할 수 없습니다.

### <a name="events-model"></a>이벤트 모델
호출 SDK의 각 개체에는 개체의 수명 동안 변경되는 속성 및 컬렉션 값이 있습니다.
`on()` 메서드를 사용하여 개체의 이벤트를 구독하고 `off()` 메서드를 사용하여 개체의 이벤트에서 구독 취소합니다.

#### <a name="properties"></a>속성
- 초기 값을 검사하고 후속 값 업데이트를 위해 `'<property>Changed'` 이벤트를 구독해야 합니다.

#### <a name="collections"></a>컬렉션
- 초기 값을 검사하고 후속 값 업데이트를 위해 `'<collection>Updated'` 이벤트를 구독해야 합니다.
- `'<collection>Updated'` 이벤트의 페이로드에는 컬렉션에 추가된 값을 포함하는 `added` 배열이 있습니다.
- `'<collection>Updated'` 이벤트의 페이로드에는 컬렉션에서 제거된 값을 포함하는 `removed` 배열이 있습니다.

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
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

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

위의 JavaScript 예제(`client.js`)에서 생성된 번들을 사용할 수 있는 HTML 예제 코드입니다.
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
호출을 시작하거나, 호출을 조인하거나, 호출을 수락하면 callAgent의 'callsUpdated' 이벤트를 사용하여 새 Call 개체에 대해 알림을 받고 구독을 시작할 수 있습니다.
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient 인스턴스를 초기화하고, CallAgent 인스턴스를 만들고, deviceManager에 액세스합니다.

새 `CallClient` 인스턴스를 만듭니다. 로거 인스턴스와 같은 사용자 지정 옵션을 사용하여 구성할 수 있습니다.

`CallClient` 인스턴스가 있으면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출하여 `CallAgent` 인스턴스를 만들 수 있습니다. 이 메서드는 `CallAgent` 인스턴스 개체를 비동기적으로 반환됩니다.

`createCallAgent` 메서드는 `CommunicationTokenCredential`을 인수로 사용합니다. [사용자 액세스 토큰](../../../access-tokens.md)이 허용됩니다.

`CallClient` 인스턴스에서 `getDeviceManager` 메서드를 사용하여 `deviceManager`에 액세스할 수 있습니다.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the logger's log level
setLogLevel('verbose');
// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};
const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>전화 걸기

통화를 만들고 시작하려면 `callAgent`에서 API 중 하나를 사용하여 Communication Services ID SDK를 통해 만든 사용자를 제공합니다.

통화 만들기와 시작은 동기적입니다. `call` 인스턴스를 사용하여 통화 이벤트를 구독할 수 있습니다.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>사용자 또는 PSTN에게 1:n 전화 걸기

다른 Communication Services 사용자에게 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 사용하고, [Communication Services 관리 라이브러리를 사용하여 만든](../../../access-tokens.md) 수신자의 `CommunicationUserIdentifier`를 전달합니다.

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
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
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

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기

> [!IMPORTANT]
> 현재 발신 로컬 비디오 스트림은 하나만 가능합니다.

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


### <a name="join-a-group-call"></a>그룹 통화 참가

> [!NOTE]
> `groupId` 매개 변수는 시스템 메타데이터로 간주되며 Microsoft가 시스템을 실행하는 데 필요한 작업에 사용할 수 있습니다. `groupId` 값에 개인 데이터를 포함하지 마세요. Microsoft는 이 매개 변수를 개인 데이터로 간주하지 않으며 해당 내용이 Microsoft 직원에게 표시되거나 장기적으로 저장될 수 있습니다.
>
> `groupId` 매개 변수는 데이터를 GUID 형식으로 지정해야 합니다. 시스템에서 개인 데이터로 간주되지 않는 임의로 생성된 GUID를 사용하는 것이 좋습니다.
>

새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 `join` 메서드를 사용하고 `groupId` 속성을 통해 개체를 전달합니다. `groupId` 값은 GUID여야 합니다.

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Teams 회의 참가
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

Teams 회의에 참가하려면 `join` 메서드를 사용하고 회의 링크 또는 회의 좌표를 전달합니다.

모임 링크를 사용하여 참가:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

모임 좌표를 사용하여 조인:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>수신 전화 받기

`callAgent` 인스턴스는 로그인된 ID가 전화를 받을 때 `incomingCall` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음 옵션 중 하나를 사용하여 구독합니다.

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
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
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` 이벤트에는 허용 또는 거부할 수 있는 `incomingCall` 인스턴스가 포함됩니다.

비디오를 켜고 통화를 시작/참여/수락하는 경우 지정된 비디오 카메라 디바이스가 다른 프로세스에서 사용되고 있거나 시스템에서 사용하지 않도록 설정된 경우 통화는 비디오가 꺼진 상태로 시작되고 cameraStartFailed: true 통화 진단이 발생합니다.

통화 진단 섹션을 참조하여 이 통화 진단을 처리하는 방법을 확인하세요.

## <a name="manage-calls"></a>통화 관리

통화 도중 통화 속성에 액세스하고 비디오 및 오디오 설정을 관리할 수 있습니다.

### <a name="check-call-properties"></a>통화 속성 확인

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




### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 `mute` 및 `unmute` 비동기 API를 사용할 수 있습니다.

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체의 `getCameras` 메서드를 사용하여 카메라를 열거해야 합니다. 그런 다음 원하는 카메라로 `LocalVideoStream`의 새 인스턴스를 만든 다음 `LocalVideoStream` 개체를 `startVideo` 메서드에 전달합니다.

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
localVideoStream 인스턴스를 전달하여 통화 시 비디오를 시작할 수 있는 4가지 api인 callAgent.startCall() api, callAgent.join() api, call.accept() api 및 call.startVideo() api가 있습니다. call.stopVideo() api에 이러한 4개 api에 전달한 것과 동일한 localVideoStream 인스턴스를 전달해야 합니다.

`localVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

지정된 비디오 디바이스가 다른 프로세스에서 사용되고 있거나 시스템에서 사용하지 않도록 설정되어 있는 경우:
- 통화하는 동안 비디오가 꺼져 있고 call.startVideo() api를 사용하여 비디오를 시작하는 경우 이 API는 SourceUnavailableError를 throw하고 cameraStartFiled 통화 진단이 발생합니다.
- localVideoStream.switchSource() api를 호출하면 cameraStartFailed: true 통화 진단이 발생합니다.
통화 진단을 처리하는 방법을 보려면 통화 진단 섹션을 참조하세요.

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-the-participants-in-a-call"></a>통화 참가자 나열

`remoteParticipants` 컬렉션은 통화의 원격 참가자 목록을 반환합니다.

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
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

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 메서드 및 속성
애플리케이션 UI에 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `VideoStreamRendererView` 인스턴스를 만들고, 비동기 `createView()` 메서드를 사용합니다. 이 메서드는 스트림을 렌더링할 준비가 되었을 때를 확인하고 DOM 트리의 아무 곳에나 추가될 수 있는 `video` 요소를 나타내는 `target` 속성이 있는 개체를 반환합니다.

  ```js
  await videoStreamRenderer.createView()
  ```

`videoStreamRenderer` 및 모든 연결된 `VideoStreamRendererView` 인스턴스를 삭제합니다.

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 메서드 및 속성

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
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>디바이스 관리

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
- Android Cheome, iOS Safari 및 macOS Safari에서는 스피커 열거/선택이 지원되지 않습니다.

## <a name="call-feature-extensions"></a>통화 기능 확장

### <a name="record-calls"></a>통화 녹음
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```js
const callRecordingApi = call.api(Features.Recording);
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingApi`의 `isRecordingActive` 속성을 검사합니다. `Boolean`를 반환합니다.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

레코딩 변화를 구독할 수도 있습니다.

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

### <a name="transfer-calls"></a>통화 전송
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 전송은 핵심 `Call` API의 확장 기능입니다. 먼저 전송 기능 API 개체를 가져와야 합니다.

```js
const callTransferApi = call.api(Features.Transfer);
```

통화 전환에는 세 명의 당사자가 포함됩니다.

- '전송자': 전송 요청을 시작한 사람입니다.
- '피전송자': 이 사람의 통화가 전송됩니다.
- '전송 대상': 통화가 이 사람에게 전송됩니다.

전송은 다음 단계를 따릅니다.

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

### <a name="dominant-speakers-of-a-call"></a>통화의 주요 스피커
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화의 주요 스피커는 핵심 `Call` API의 확장된 기능으로, 통화에서 활성 스피커의 목록을 가져올 수 있도록 합니다. 

이 목록은 순위가 매겨진 목록으로, 목록의 첫 번째 요소가 통화의 마지막 활성 스피커를 나타냅니다.

통화의 주요 스피커를 알려면 먼저 통화 주요 스피커 기능 API 개체를 가져와야 합니다.

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

그런 다음, `dominantSpeakers`를 호출하여 주요 스피커 목록을 가져옵니다. 이 목록은 `DominantSpeakersInfo` 형식을 가지며 다음 구성원을 포함합니다.

- `speakersList`에는 통화의 순위가 매겨진 주요 스피커 목록을 포함합니다. 이러한 항목은 참가자 ID로 표시됩니다.
- `timestamp`는 통화에 있는 주요 스피커의 최신 업데이트 시간입니다.

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
또한 `dominantSpeakersChanged` 이벤트를 구독하여 주요 스피커 목록이 변경된 시간을 알 수 있습니다.

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>주요 스피커의 비디오 스트림 처리

애플리케이션은 `DominantSpeakers` 기능을 사용하여 하나 이상의 주요 스피커 비디오 스트림을 렌더링하고, 주요 스피커 목록이 업데이트될 때마다 UI를 계속 업데이트할 수 있습니다. 다음 코드 예제로 이 작업을 수행할 수 있습니다.

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>통화 진단
통화 진단은 핵심 `Call` API의 확장된 기능으로, 활성 호출을 진단할 수 있도록 합니다.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

다음과 같은 사용자 연결 진단을 사용할 수 있습니다.

| Type    | Name                           | Description                                                     | 가능한 값                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | 사용 사례                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| 네트워크 | noNetwork                      | 사용 가능한 업데이트가 없습니다.                                  | - 사용 가능한 네트워크가 없어서 호출을 시작하지 못한 경우 `True`로 설정됩니다. <br/> - ICE 후보가 있는 경우 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                                                                                      | 디바이스가 네트워크에 연결되지 않았습니다.                                           |
| 네트워크 | networkRelaysNotReachable      | 네트워크에 문제가 있습니다.                                        | - 네트워크에 ACS 릴레이에 연결하도록 허용하지 않는 일부 제약 조건이 있는 경우 `True`로 설정됩니다. <br/> - 새 호출 시 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                                                                                    | 호출하는 동안 WiFi 신호가 켜지고 꺼질 때                             |  |
| 네트워크 | networkReconnect               | 연결이 끊어졌으며 네트워크에 다시 연결하고 있습니다. | - 미디어 전송 연결이 끊어지면 `Poor`로 설정됩니다.                                                                                                                                 <br/> - 네트워크 연결이 끊어지면 `Bad`로 설정됩니다. <br/> - 새 세션이 연결되면 `Good`으로 설정됩니다.                                                                                                                                                                                           | 낮은 대역폭, 인터넷 없음                                                      |
| 네트워크 | networkReceiveQuality          | 들어오는 스트림 품질에 대한 표시기입니다.                 | - 스트림을 받는 데 심각한 문제가 있는 경우 `Bad`로 설정됩니다. 품질                                                                                                                           <br/> - 스트림을 받는 데 경미한 문제가 있는 경우 `Poor`로 설정됩니다. 품질                                                                                                                           <br/> - 스트림을 받는 데 문제가 없는 경우 `Good`으로 설정됩니다. | 낮은 대역폭                                                                   |
| 미디어   | noSpeakerDevicesEnumerated     | 사용자 시스템에 오디오 출력 디바이스(스피커)가 없습니다. | - 시스템에 스피커 디바이스가 없고 스피커 선택이 지원되는 경우 `True`로 설정됩니다. <br/> - 시스템에 스피커 디바이스가 1개 이상 없고 스피커 선택이 지원되는 경우 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                | 모든 스피커가 플러그가 뽑혀 있습니다.                                                      |
| 미디어   | speakingWhileMicrophoneIsMuted | 음소거 상태에서 말합니다.                                   | - 로컬 마이크가 음소거되고 로컬 사용자가 말하는 경우 `True`로 설정됩니다. <br/> - 로컬 사용자가 말하기를 중지하거나 마이크를 음소거 해제할 때 `False`로 설정됩니다. <br/> * 참고: 현재 오디오 수준 샘플은 webrtc 통계에서 가져온 것이므로 safari에서는 아직 지원되지 않습니다.                                                                                                                                                                                                     | 통화하는 동안 마이크를 음소거하고 마이크에 대고 말합니다.                          |
| 미디어   | noMicrophoneDevicesEnumerated  | 사용자 시스템에 오디오 캡처 디바이스(마이크)가 없습니다.      | - 시스템에 마이크 디바이스가 없는 경우 `True`로 설정됩니다. <br/> - 시스템에 마이크 디바이스가 1개 이상 있는 경우 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                                                                                 | 모든 마이크는 통화 중에 연결이 끊어집니다.                                  |
| 미디어   | cameraFreeze                   | 카메라가 5초 넘게 프레임 생성을 중지합니다.          | - 로컬 비디오 스트림이 중지된 경우 `True`로 설정합니다. 즉, 원격 쪽 사용자가 화면에서 중지된 비디오를 보거나 원격 참가자의 화면에서 비디오가 렌더링되지 않음을 의미합니다. <br/> - 중지가 끝나고 사용자가 정상적으로 비디오를 볼 수 있는 경우 `False`로 설정됩니다.                                                                                                                                                                                         | 통화 중에 카메라가 연결이 끊어지거나 네트워크에 문제가 생겨 카메라가 중지되었습니다. |
| 미디어   | cameraStartFailed              | 일반 카메라 오류입니다.                                         | - 카메라 디바이스가 시스템에서 사용하지 않도록 설정되었거나 다른 프로세스에서 사용되고 있기 때문에 로컬 비디오 전송을 시작하지 못한 경우 `True`로 설정됩니다. <br/> - 선택한 카메라 디바이스가 로컬 비디오를 성공적으로 보낼 때 `False`로 설정됩니다. 를 다시 클릭합니다.                                                                                                                                                                                                                                           | 카메라 오류                                                                 |
| 미디어   | cameraStartTimedOut            | 카메라가 잘못된 상태인 일반적인 시나리오입니다.                   | - 카메라 디바이스가 비디오 스트림 전송을 시작하는 동안 시간이 초과될 경우 `True`로 설정됩니다. <br/> - 선택한 카메라 디바이스가 로컬 비디오를 다시 보낼 경우 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                                                                 | 카메라 오류                                                                 |
| 미디어   | microphoneNotFunctioning       | 마이크가 작동하지 않습니다.                                  | - 마이크 디바이스가 시스템에서 사용하지 않도록 설정되었거나 다른 프로세스에서 사용되고 있기 때문에 로컬 오디오 스트림 전송을 시작하지 못한 경우 `True`로 설정됩니다. 이 UFD가 발생하는 데 약 10초가 걸립니다. <br/> - 마이크가 오디오 스트림을 다시 성공적으로 보내기 시작하면 `False`로 설정됩니다.                                                                                                                                                                                    | 마이크를 사용할 수 없습니다. 시스템에서 마이크 액세스가 사용하지 않도록 설정되었습니다.                |
| 미디어   | microphoneMuteUnexpectedly     | 마이크가 음소거되었습니다.                                             | - 마이크가 예기치 않게 음소거 상태가 되면 `True`로 설정됩니다. <br/> - 마이크가 오디오 스트림을 성공적으로 보내기 시작하면 `False`로 설정됩니다.                                                                                                                                                                                                                                                                                                                                                     | 마이크가 시스템에서 음소거되었습니다.                                            |
| 미디어   | screenshareRecordingDisabled   | 설정의 기본 설정에 의해 시스템 화면 공유가 거부되었습니다.     | - 시스템 설정(공유)에서 화면 공유 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br/> 참고: 이 진단은 macOS.Chrome에서만 작동합니다.                                                                                                                                                                                                                                                                                               | 설정에서 화면 녹화가 사용하지 않도록 설정되었습니다.                                       |
| 미디어   | microphonePermissionDenied     | 디바이스의 볼륨이 낮거나 macOS에서 거의 무음입니다. | - 시스템 설정(오디오)에서 오디오 사용 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br/> 참고: 이 진단은 macOS에서만 작동합니다.                                                                                                                                                                                                                                                                                                                | 설정에서 마이크 사용 권한이 사용하지 않도록 설정되었습니다.                            |
| 미디어   | cameraPermissionDenied         | 설정에서 카메라 사용 권한이 거부되었습니다.                     | - 시스템 설정(비디오)에서 카메라 사용 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br> 참고: 이 진단은 macOS Chrome에서만 작동합니다.                                                                                                                                                                                                                                                                                                          | 설정에서 카메라 사용 권한이 사용하지 않도록 설정되었습니다.                                |

- 통화 진단이 변경될 때를 모니터링하기 위해 `diagnosticChanged` 이벤트를 구독합니다.
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- 발생한 최신 통화 진단 값을 가져옵니다. 진단이 정의되지 않은 경우 절대 발생하기 않기 때문입니다.
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>리소스 해제
1. 통화가 완료될 때 리소스를 적절히 해제하는 방법:
    - 통화가 완료되면 SDK는 신호 및 미디어 세션을 종료하여 통화 인스턴스가 마지막 상태를 유지하도록 합니다. callEndReason을 계속 확인할 수 있습니다. 앱이 통화 인스턴스에 대한 참조를 유지하지 않는 경우 JavaScript 가비지 수집기는 모든 것을 정리하므로 메모리 사용량 측면에서 앱이 통화 이전의 초기 상태로 돌아가게 됩니다.
2. 장기(앱 수명) 리소스 종류 및 단기(통화 수명) 리소스 종류:
    - 다음은 "장기" 리소스로 간주됩니다. 즉, 리소스를 만들고 오랫동안 참조된 상태로 유지할 수 있습니다. 또한 리소스(메모리) 사용량이 적으므로 성능에 영향을 미치지 않습니다.
        - CallClient
        - CallAgent
        - DeviceManager
    - 다음은 "단기" 리소스로 간주되며, 통화 자체에서 역할을 수행하거나, 애플리케이션에 이벤트를 내보내거나, 로컬 미디어 디바이스와 상호 작용하는 리소스입니다. 이러한 리소스는 더 많은 CPU 및 메모리를 사용하지만 일단 통화가 끝나면 SDK는 모든 상태 정리하고 리소스를 해제합니다.
        - 통화 - 통화의 실제 상태(신호 및 미디어 모두)를 유지합니다.
        - RemoteParticipants - 통화의 원격 참가자를 나타냅니다.
        - VideoStreamRenderer와 VideoStreamRendererViews - 비디오 렌더링 처리
