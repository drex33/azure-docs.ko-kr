---
title: Azure Service Bus 토픽에 메시지를 보내고 구독에서 토픽으로 메시지 받기(JavaScript)
description: '@azure/service-bus 패키지의 최신 버전을 사용하여 메시지를 Service Bus 토픽으로 보내고 구독에서 토픽으로 메시지를 받는 JavaScript 프로그램을 작성하는 방법에 대해 알아봅니다.'
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 9534781c653302514e3146cbb58f322e31d0d602
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043542"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-javascript"></a>Azure Service Bus 토픽에 메시지를 보내고 구독에서 토픽으로 메시지 받기(JavaScript)
이 자습서에서는 JavaScript 프로그램에서 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지를 사용하여 Service Bus 토픽으로 메시지를 보내고 Service Bus 구독에서 해당 토픽으로 메시지를 받는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md)의 단계를 따릅니다. 연결 문자열, 항목 이름 및 구독 이름을 기록해 둡니다. 이 빠른 시작에는 하나의 구독만 사용합니다. 

> [!NOTE]
> - 이 자습서에서는 [Nodejs](https://nodejs.org/)를 사용하여 복사하고 실행할 수 있는 샘플을 사용합니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포](../app-service/quickstart-nodejs.md) 또는 Windows PowerShell을 사용하는 [Node.js 클라우드 서비스](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)를 참조하세요.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치
Service Bus에 대한 npm 패키지를 설치하려면 경로에 `npm`이 있는 명령 프롬프트를 열고, 샘플을 저장하고 이 명령을 실행할 폴더로 디렉터리를 변경합니다.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
다음 샘플 코드에서는 Service Bus 토픽으로 메시지 일괄 처리를 보내는 방법을 보여줍니다. 자세한 내용은 코드 주석을 참조하세요. 

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. `sendtotopic.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다. 이 코드는 토픽에 메시지를 보냅니다.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
1. `<TOPIC NAME>`을 토픽 이름으로 바꿉니다. 
1. 그런 다음, 명령 프롬프트에서 명령을 실행하여 이 파일을 실행합니다.

    ```console
    node sendtotopic.js 
    ```
1. 다음 출력이 표시되어야 합니다.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. **receivefromsubscription.js** 라는 파일을 만들고, 그 안에 다음 코드를 붙여넣습니다. 자세한 내용은 코드 주석을 참조하세요. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`을 네임스페이스에 대한 연결 문자열로 바꿉니다. 
1. `<TOPIC NAME>`을 토픽 이름으로 바꿉니다. 
1. `<SUBSCRIPTION NAME>`을 토픽에 대한 구독 이름으로 바꿉니다. 
1. 그런 다음, 명령 프롬프트에서 명령을 실행하여 이 파일을 실행합니다.

    ```console
    node receivefromsubscription.js
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

Azure Portal에서 Service Bus 네임스페이스로 이동하고 아래쪽 창에서 토픽을 선택하여 토픽에 대한 **Service Bus 토픽** 페이지를 확인합니다. 이 페이지에는 **메시지** 차트에 세 개의 들어오고 나가는 메시지가 표시됩니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="수신 및 발신 메시지":::

다음에 보내기 앱만 실행하는 경우 **Service Bus 토픽** 페이지에서 6개의 들어오는 메시지(새 메시지 3개)가 표시되지만 나가는 메시지는 3개가 표시됩니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="업데이트된 항목 페이지":::

이 페이지에서 구독을 선택하면 **Service Bus 구독** 페이지로 이동합니다. 이 페이지에서는 활성 메시지 수, 전달 못한 메시지 수 등을 볼 수 있습니다. 이 예제에는 수신자가 아직 받지 않은 3개의 활성 메시지가 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="활성 메시지 수":::

## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요. 

- [JavaScript용 Azure Service Bus 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API 참조 설명서](/javascript/api/overview/azure/service-bus)
