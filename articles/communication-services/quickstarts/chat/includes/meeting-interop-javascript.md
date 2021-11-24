---
title: 빠른 시작 - Teams 모임 참가
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: mode-other
ms.openlocfilehash: 8005e9a3c8fb87d94b321fe33f8088e111156c77
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133046449"
---
이 빠른 시작에서는 JavaScript용 Azure Communication Services 채팅 SDK를 사용하여 Teams 모임에서 채팅하는 방법을 알아봅니다.

## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항 

*  [Teams 배포](/deployoffice/teams-install). 
* 작동하는 [채팅 앱](../get-started.md). 

## <a name="joining-the-meeting-chat"></a>모임 채팅 참가 

Communication Services 사용자는 호출 SDK를 사용하여 익명 사용자로 Teams 모임에 참가할 수 있습니다. 모임에 참가하면 모임 채팅에도 참가자로 추가됩니다. 그러면 모임의 다른 사용자와 메시지를 보내고 받을 수 있습니다. 사용자는 모임에 참가하기 전에 전송된 채팅 메시지에 액세스할 수 없으며 모임이 끝난 후에는 메시지를 보내거나 받을 수 없습니다. 모임에 참가하고 채팅을 시작하려면 다음 단계를 수행하면 됩니다.

## <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir chat-interop-quickstart && cd chat-interop-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

## <a name="install-the-chat-packages"></a>채팅 패키지 설치

`npm install` 명령을 사용하여 필수 JavaScript용 Communication Services SDK를 설치합니다.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

## <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

이 빠른 시작에서는 webpack을 사용하여 애플리케이션 자산을 번들로 묶습니다. 다음 명령을 실행하여 webpack, webpack-cli 및 webpack-dev-server npm 패키지를 설치하고 **package.json** 에서 개발 종속성으로 나열합니다.

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

프로젝트의 루트 디렉터리에 **index.html** 파일을 만듭니다. 이 파일을 사용하여 사용자가 회의에 참석하고 채팅을 시작할 수 있는 기본 레이아웃을 구성합니다.

## <a name="add-the-teams-ui-controls"></a>Teams UI 컨트롤 추가

index.html의 코드를 다음 코드 조각으로 바꿉니다.
페이지 맨 위에 있는 텍스트 상자는 Teams 모임 컨텍스트 및 모임 스레드 ID를 입력하는 데 사용됩니다. 'Teams 모임 참가' 단추는 지정된 모임에 참가하는 데 사용됩니다.
페이지 아래쪽에 채팅 팝업이 표시됩니다. 이를 사용하여 모임 스레드에서 메시지를 보낼 수 있으며 Communication Services 사용자가 구성원인 동안 스레드에서 보낸 메시지를 실시간으로 표시합니다.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams UI 컨트롤 사용

client.js 파일의 내용을 다음 코드 조각으로 바꿉니다.

코드 조각 내에서 다음과 같이 바꿉니다. 
- `SECRET_CONNECTION_STRING`을 Communication Service의 연결 문자열로 
- `ENDPOINT_URL`을 Communication Service의 엔드포인트 URL로

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var lastMessage = '';
var previousMessage = '';

async function init() {
  const connectionString = "<SECRET_CONNECTION_STRING>";
  const endpointUrl = "<ENDPOINT_URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);

  let tokenResponse = await identityClient.getToken(identityResponse, [
    "voip",
    "chat",
  ]);

  const { token, expiresOn } = tokenResponse;
  console.log(`\nIssued an access token that expires at: ${expiresOn}`);
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationTokenCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationTokenCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});

  call.on('stateChanged', () => {
      callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = "";

  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");

      // check whether the notification is intended for the current thread
    if (threadIdInput.value != e.threadId) {
      return;
    }

    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.message);
    }
    else {
       renderSentMessage(e.message);
    }
  });

  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container lighter">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

async function renderSentMessage(message) {
  previousMessage = lastMessage;
  lastMessage = '<div class="container darker">' + message + '</div>';
  messagesContainer.innerHTML = previousMessage + lastMessage;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
    let message = messagebox.value;

    let sendMessageRequest = { content: message };
    let sendMessageOptions = { senderDisplayName : 'Jack' };
    let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
    let messageId = sendChatMessageResult.id;

    messagebox.value = '';
    console.log(`Message sent!, message id:${messageId}`);
  });
```

채팅 스레드 참가자의 표시 이름은 Teams 클라이언트에서 설정하지 않습니다. 이름이 참가자 나열을 위한 API, `participantsAdded` 이벤트 및 `participantsRemoved` 이벤트에서 null로 반환됩니다. 채팅 참가자의 표시 이름은 `call` 개체의 `remoteParticipants` 필드에서 검색할 수 있습니다. 명단 변경에 대한 알림을 받으면 이 코드를 사용하여 추가되거나 제거된 사용자의 이름을 검색할 수 있습니다.

```
var displayName = call.remoteParticipants.find(p => p.identifier.communicationUserId == '<REMOTE_USER_ID>').displayName;
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services 사용자를 위한 Teams 미팅 채팅 스레드 가져오기

Teams 모임 링크 및 채팅은 [그래프 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에 자세히 설명된 Graph API를 사용하여 검색할 수 있습니다. Communication Services Calling SDK는 전체 Teams 모임 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다.

[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)를 사용하면 `threadID`도 가져올 수 있습니다. 응답에는 `threadID`를 포함하는 `chatInfo` 개체가 포함됩니다. 

또한 Teams 모임 초대 자체의 **모임 참가** URL에서 필요한 모임 정보 및 스레드 ID를 가져올 수 있습니다.
Teams 미팅 링크는 `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`와 같습니다. `threadId`는 링크의 `meeting_chat_thread_id` 위치에 있습니다. 사용하기 전에 `meeting_chat_thread_id`가 이스케이프되지 않았는지 확인합니다. `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` 형식이어야 합니다.


## <a name="run-the-code"></a>코드 실행

webpack 사용자는 `webpack-dev-server`를 사용하여 앱을 빌드하고 실행할 수 있습니다. 다음 명령을 실행하여 로컬 웹 서버에서 애플리케이션 호스트를 번들로 묶습니다.

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

브라우저를 열고 http://localhost:8080/로 이동합니다. 다음이 표시되어야 합니다.

:::image type="content" source="../join-teams-meeting-chat-quickstart.png" alt-text="완성된 JavaScript 애플리케이션의 스크린샷":::

텍스트 상자에 Teams 모임 링크와 스레드 ID를 삽입합니다. Teams 모임에 참가하려면 *팀 모임 참가* 를 누릅니다. Communication Services 사용자가 모임에 입장한 후 Communication Services 애플리케이션에서 채팅할 수 있습니다. 페이지 맨 아래에 있는 상자로 이동하여 채팅을 시작합니다. 간단히 하기 위해 애플리케이션은 채팅의 마지막 두 메시지만 표시합니다.

> [!NOTE] 
> 현재는 Teams와의 상호 운용성 시나리오에 대해서만 메시지 전송, 수신, 편집 및 타이핑 알림 보내기가 지원됩니다. 읽음 확인 및 Communication Services 사용자와 같은 다른 기능에서는 Teams 미팅에서 다른 사용자를 추가하거나 제거할 수 없습니다.
