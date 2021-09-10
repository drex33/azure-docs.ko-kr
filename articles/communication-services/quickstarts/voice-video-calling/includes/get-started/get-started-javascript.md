---
title: 빠른 시작 - Azure Communication Services를 사용하여 웹앱에 VOIP 통화 추가
description: 이 자습서에서는 JavaScript용 Azure Communication Services Calling SDK를 사용하는 방법을 알아봅니다.
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1f027dd3dfb812e9a700810972d2b019810dc41c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251508"
---
이 빠른 시작에서는 JavaScript용 Azure Communication Services Calling SDK를 사용하여 통화를 시작하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>예제 코드

샘플 앱은 [Github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장)
- 활성 Communication Services 리소스 [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰입니다. [사용자 액세스 토큰 만들기 및 관리](../../../access-tokens.md) 방법에 대해 알아봅니다.

[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

코드는 다음과 같습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

이 빠른 시작에 대한 애플리케이션 로직을 포함하기 위해 **client.js** 라는 프로젝트의 루트 디렉터리에 파일을 만듭니다. 다음 코드를 추가하여 통화 클라이언트를 가져오고, 비즈니스 논리를 연결할 수 있도록 DOM 요소에 대한 참조를 가져옵니다. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let tokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                             | 설명                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient는 Calling SDK의 주 진입점입니다.                                                                       |
| CallAgent                        | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다.                                                                                            |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 클래스는 CallAgent를 인스턴스화하는 데 사용되는 CommunicationTokenCredential 인터페이스를 구현합니다. |

## <a name="authenticate-the-client"></a>클라이언트 인증

텍스트 필드에 리소스에 대한 유효한 사용자 액세스 토큰을 입력하고 '제출'을 클릭해야 합니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../../access-tokens.md) 설명서를 참조하세요. `CallClient`를 사용하여 호출을 수행하고 받을 수 있도록 하는 `CommunicationTokenCredential`을 사용하여 `CallAgent` 인스턴스를 초기화합니다. 

**client.js** 에 다음 코드를 추가합니다.

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>호출 시작

`callButton`을 클릭하면 호출을 시작하는 이벤트 처리기를 추가합니다.

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    // To call an ACS communication user, use {communicationUserId: 'ACS_USER_ID'}.
    // To call echobot, use {id: '8:echo123'}.
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>호출 종료

`hangUpButton`을 클릭하면 현재 호출을 종료하는 이벤트 수신기를 추가합니다.

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

`forEveryone` 속성은 모든 호출 참가자에 대한 호출을 종료합니다.

## <a name="run-the-code"></a>코드 실행

`webpack-dev-server`를 사용하여 앱을 빌드하고 실행합니다. 다음 명령을 실행하여 로컬 웹 서버에 애플리케이션 호스트를 번들로 묶습니다.

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

브라우저를 열고 http://localhost:8080/로 이동합니다. 다음이 표시되어야 합니다.

:::image type="content" source="../../media/javascript/calling-javascript-app-2.png" alt-text="완성된 JavaScript 애플리케이션의 스크린샷":::

해당 텍스트 필드에 유효한 사용자 액세스 토큰과 사용자 ID를 제공하고 **호출 시작** 단추를 클릭하여 아웃바운드 VOIP 호출을 수행할 수 있습니다.

`8:echo123`에 전화를 걸면 에코 봇과 연결됩니다. 이렇게 하면 통화를 시작하고 오디오 디바이스가 작동하는지 확인하는 데 유용합니다. `{id: '8:echo123'}`을 CallAgent.startCall() API에 전달하여 echobot을 호출합니다.
ACS 통신 사용자를 호출하려면 `{communicationUserId: 'ACS_USER_ID'}`를 `CallAgent.startCall()` API에 전달합니다.
