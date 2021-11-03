---
title: JavaScript에서 azure/service-bus 큐를 사용하는 방법
description: '@azure/service-bus 패키지의 최신 버전을 사용하여 메시지를 Service Bus 큐에서 메시지를 보내고 받는 JavaScript 프로그램을 작성하는 방법에 대해 알아봅니다.'
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom: devx-track-js, mode-api
ms.openlocfilehash: 176ed204227d1bedcb0977d35149f36c792e10ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009076"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Azure Service Bus 큐에서 메시지 보내기 및 받기(JavaScript)
이 자습서에서는 JavaScript 프로그램에서 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지를 사용하여 Service Bus 큐와 메시지를 주고 받는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화하거나 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 등록할 수 있습니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 네임스페이스에 대한 **연결 문자열** 및 사용자가 만든 **큐** 의 이름을 적어둡니다.

> [!NOTE]
> - 이 자습서에서는 [Nodejs](https://nodejs.org/)를 사용하여 복사하고 실행할 수 있는 샘플을 사용합니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포](../app-service/quickstart-nodejs.md) 또는 Windows PowerShell을 사용하는 [Node.js 클라우드 서비스](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)를 참조하세요.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치
Service Bus에 대한 npm 패키지를 설치하려면 경로에 `npm`이 있는 명령 프롬프트를 열고, 샘플을 저장하고 이 명령을 실행할 폴더로 디렉터리를 변경합니다.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
다음 샘플 코드는 큐에 메시지를 보내는 방법을 보여줍니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. `send.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다. 이 코드는 과학자의 이름을 메시지로 큐에 보냅니다.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`를 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
1. `<QUEUE NAME>`을 큐 이름으로 바꿉니다. 
1. 그런 다음, 명령 프롬프트에서 명령을 실행하여 이 파일을 실행합니다.

    ```console
    node send.js 
    ```
1. 다음 출력이 표시되어야 합니다.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. `receive.js`라는 파일을 만들고, 그 안에 다음 코드를 붙여넣습니다.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(20000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`를 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
1. `<QUEUE NAME>`을 큐 이름으로 바꿉니다. 
1. 그런 다음, 명령 프롬프트에서 명령을 실행하여 이 파일을 실행합니다.

    ```console
    node receive.js
    ```
1. 다음 출력이 표시되어야 합니다.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Azure Portal의 Service Bus 네임스페이스에 대한 **개요** 페이지에서 **수신** 및 **발신** 메시지 수를 확인할 수 있습니다. 1분 정도 기다렸다가 페이지를 새로 고쳐 최신 값을 확인해야 할 수 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="수신 및 발신 메시지 수":::

이 **개요** 페이지에서 큐를 선택하여 **Service Bus 큐** 페이지로 이동합니다. 이 페이지에도 **수신** 및 **발신** 메시지 수가 표시됩니다. 큐의 **현재 크기**, **최대 크기**, **활성 메시지 수** 등과 같은 기타 정보도 볼 수 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="큐 세부 정보":::
## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요. 

- [JavaScript용 Azure Service Bus 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/v7/javascript)
- [TypeScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/v7/typescript)
- [API 참조 설명서](/javascript/api/overview/azure/service-bus)
