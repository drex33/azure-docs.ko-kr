---
title: '빠른 시작: Azure Queue Storage 클라이언트 라이브러리 v12 - JavaScript'
description: JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하여 큐를 만들고 메시지를 큐에 추가하는 방법을 알아봅니다. 그런 다음, 큐에서 메시지를 읽고 삭제하는 방법을 알아봅니다. 큐를 삭제하는 방법도 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: a3adb61c27610b07f17aae44c607e36c640cd07d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570933"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>빠른 시작: JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12

JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12를 시작하세요. Azure Queue Storage는 나중에 검색하고 처리할 수 있도록 대량의 메시지를 저장하는 서비스입니다. 다음 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해보십시오.

JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하여 다음을 수행합니다.

- 큐 만들기
- 큐에 메시지 추가
- 큐의 메시지 피킹(Peeking)
- 큐의 메시지 업데이트
- 큐에서 메시지 받기
- 큐에서 메시지 삭제
- 큐 삭제

추가 리소스:

- [API 참조 설명서](/javascript/api/@azure/storage-queue/)
- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [패키지(npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [샘플](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- Azure Storage 계정 - [스토리지 계정 만들기](../common/storage-account-create.md)
- 현재 운영 체제의 [Node.js](https://nodejs.org/en/download/)입니다.

## <a name="setting-up"></a>설치

이 섹션에서는 JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하는 프로젝트 준비 과정을 안내합니다.

### <a name="create-the-project"></a>프로젝트 만들기

`queues-quickstart-v12`라는 Node.js 애플리케이션 만들기

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir queues-quickstart-v12
    ```

1. 새로 만든 `queues-quickstart-v12` 디렉터리로 전환합니다.

    ```console
    cd queues-quickstart-v12
    ```

1. `package.json`라는 새 텍스트 파일을 만듭니다. 이 파일은 Node.js 프로젝트를 정의합니다. 이 파일을 `queues-quickstart-v12` 디렉터리에 저장합니다. 파일의 내용은 다음과 같습니다.

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    원할 경우 `author` 필드에 사용자 고유의 이름을 입력할 수 있습니다.

### <a name="install-the-package"></a>패키지 설치

`queues-quickstart-v12` 디렉터리에 있는 동안 `npm install` 명령을 사용하여 JavaScript용 Azure Queue Storage 클라이언트 라이브러리 패키지를 설치합니다.

```console
npm install
```

이 명령은 `package.json` 파일을 읽고 JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12 패키지와 이 패키지가 종속된 모든 라이브러리를 설치합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 다른 새 텍스트 파일 열기
1. Azure 및 Node.js 모듈을 로드하는 `require` 호출 추가
1. 매우 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

    코드는 다음과 같습니다.

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. 새 파일을 `queues-quickstart-v12` 디렉터리에 `queues-quickstart-v12.js`로 저장합니다.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Queue Storage는 대량의 메시지를 저장하기 위한 서비스입니다. 큐 메시지의 크기는 최대 64KB입니다. 큐는 스토리지 계정의 용량 제한에 도달할 때까지 수백만 개의 메시지를 포함할 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다. Queue Storage는 다음 세 가지 유형의 리소스를 제공합니다.

- 스토리지 계정
- 스토리지 계정의 큐
- 큐 내의 메시지

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Queue storage 아키텍처 다이어그램](./media/storage-queues-introduction/queue1.png)

다음 JavaScript 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient`를 사용하면 스토리지 계정의 모든 큐를 관리할 수 있습니다.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): `QueueClient` 클래스를 사용하면 개별 큐와 해당 메시지를 관리하고 조작할 수 있습니다.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` 클래스는 큐에서 [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-)를 호출할 때 반환되는 개별 개체를 나타냅니다.

## <a name="code-examples"></a>코드 예제

다음 예제 코드 조각은 JavaScript용 Azure Queue Storage 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

- [연결 문자열 가져오기](#get-the-connection-string)
- [큐 만들기](#create-a-queue)
- [큐에 메시지 추가](#add-messages-to-a-queue)
- [큐의 메시지 피킹(Peeking)](#peek-at-messages-in-a-queue)
- [큐의 메시지 업데이트](#update-a-message-in-a-queue)
- [큐에서 메시지 받기](#receive-messages-from-a-queue)
- [큐에서 메시지 삭제](#delete-messages-from-a-queue)
- [큐 삭제](#delete-a-queue)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

다음 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

다음 코드를 `main` 함수 내에 추가합니다.

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>큐 만들기

새 큐의 이름을 결정합니다. 다음 코드는 큐 이름의 고유성을 보장하기 위해 큐 이름에 UUID 값을 추가합니다.

> [!IMPORTANT]
> 큐 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 문자나 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다. 이름의 길이는 3~63자 사이여야 합니다. 자세한 내용은 [큐 및 메타데이터 명명](/rest/api/storageservices/naming-queues-and-metadata)을 참조하세요.

[`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) 메서드를 호출하여 스토리지 계정에 큐를 만듭니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>큐에 메시지 추가

다음 코드 조각은 [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 메서드를 호출하여 큐에 메시지를 추가합니다. 또한 세 번째 `sendMessage` 호출에서 반환된 [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage)를 저장합니다. 반환된 `sendMessageResponse`는 나중에 프로그램에서 메시지 콘텐츠를 업데이트하는 데 사용됩니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>큐의 메시지 피킹(Peeking)

[`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 메서드를 호출하여 큐의 메시지를 피킹합니다. 이 메서드는 큐 앞에서 하나 이상의 메시지를 검색하지만 메시지의 표시 유형을 변경하지는 않습니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>큐의 메시지 업데이트

[`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) 메서드를 호출하여 메시지 콘텐츠를 업데이트합니다. 이 메서드는 메시지의 표시 시간 제한 및 콘텐츠를 변경할 수 있습니다. 메시지 콘텐츠는 크기가 최대 64KB인 UTF-8 인코딩 문자열이어야 합니다. 신규 콘텐츠와 함께 이전에 코드에 저장된 응답에서 `messageId` 및 `popReceipt`를 전달합니다. `sendMessageResponse` 속성은 업데이트할 메시지를 식별합니다.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기

[`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 메서드를 호출하여 이전에 추가한 메시지를 다운로드합니다. `numberOfMessages` 필드에서 이 호출에 대해 받을 최대 메시지 수를 전달합니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>큐에서 메시지 삭제

메시지를 받아서 처리한 후 큐에서 메시지를 삭제합니다. 이 경우 처리는 콘솔에 메시지를 표시하는 것뿐입니다.

[`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) 메서드를 호출하여 메시지를 삭제합니다. 명시적으로 삭제되지 않은 메시지는 다시 처리될 수 있도록 큐에 다시 표시됩니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>큐 삭제

다음 코드는 [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) 메서드를 사용하여 큐를 삭제하여 앱이 만든 리소스를 정리합니다.

이 코드를 `main` 함수의 끝에 추가하고 파일을 저장합니다.

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>코드 실행

이 앱은 메시지 세 개를 만들어서 Azure 큐에 추가합니다. 이 코드는 큐의 메시지를 나열한 다음, 검색하고 삭제한 후 최종적으로 큐를 삭제합니다.

콘솔 창에서 `queues-quickstart-v12.js` 파일이 포함된 디렉터리로 이동한 후, 다음 `node` 명령을 사용하여 앱을 실행합니다.

```console
node queues-quickstart-v12.js
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

디버거에서 코드를 단계별로 실행하고 프로세스 전체에서 [Azure Portal](https://portal.azure.com)을 확인합니다. 스토리지 계정을 확인하여 큐의 메시지가 생성되고 삭제되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 JavaScript 코드를 사용하여 큐를 만들고 큐에 메시지를 추가하는 방법을 알아보았습니다. 그런 다음, 메시지를 피킹하고 검색하고 삭제하는 방법을 알아보았습니다. 마지막으로 메시지 큐를 삭제하는 방법을 알아보았습니다.

자습서, 샘플, 빠른 시작 및 기타 설명서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [JavaScript용 Azure 설명서](/azure/developer/javascript/)

- 자세한 내용은 [JavaScript용 Azure Queue Storage 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)를 참조하세요.
- 더 많은 Azure Queue Storage 샘플 앱은 [JavaScript용 Azure Queue Storage 클라이언트 라이브러리 v12 - 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)을 참조하세요.
