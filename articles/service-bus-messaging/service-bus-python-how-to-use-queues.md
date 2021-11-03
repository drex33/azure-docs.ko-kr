---
title: Python azure-servicebus 패키지 버전 7.0.0에서 Azure Service Bus 큐 사용
description: 이 문서에서는 Python을 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 방법을 설명합니다.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom: seo-python-october2019, devx-track-python, mode-api
ms.openlocfilehash: 333fb5149293a0ae1e6135511f29eafa6e1bd18b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004039"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Azure Service Bus 큐에서 메시지 보내기 및 받기(Python)
이 문서에서는 Python을 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 네임스페이스에 대한 **연결 문자열** 및 사용자가 만든 **큐** 의 이름을 적어둡니다.
- [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) 패키지가 설치된 Python 2.7 이상. 자세한 내용은 [Python 설치 가이드](/azure/developer/python/azure-sdk-install)를 참조하세요. 

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기

1. 다음 import 문을 추가합니다. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 다음 상수를 추가합니다. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스의 연결 문자열로 바꿉니다.
    > - `<QUEUE NAME>`을 큐 이름으로 바꿉니다. 
3. 단일 메시지를 보내는 메서드를 추가합니다.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    보낸 사람은 만든 큐에 대한 클라이언트 역할을 하는 개체입니다. 나중에 만들어 이 함수에 인수로 보냅니다. 
4. 메시지 목록을 보내는 메서드를 추가합니다.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. 메시지 일괄 처리를 보내는 메서드를 추가합니다.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Service Bus 클라이언트를 만든 다음, 메시지를 보낼 큐 발신자 개체를 만듭니다.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
print 문 뒤에 다음 코드를 추가합니다. 이 코드는 5(`max_wait_time`)초 동안 새 메시지를 수신하지 않을 때까지 계속해서 새 메시지를 받습니다. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>전체 코드

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>앱 실행
애플리케이션을 실행할 때 다음 출력이 표시되어야 합니다. 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Azure Portal에서 Service Bus 네임스페이스로 이동합니다. **개요** 페이지에서 **수신** 및 **발신** 메시지 수가 16개인지 확인합니다. 개수가 표시되지 않으면 몇 분 정도 기다린 후 페이지를 새로 고칩니다. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="수신 및 발신 메시지 수":::

이 **개요** 페이지에서 큐를 선택하여 **Service Bus 큐** 페이지로 이동합니다. 이 페이지에서 **수신** 및 **발신** 메시지 수를 확인할 수도 있습니다. 큐의 **현재 크기** 및 **활성 메시지 수** 와 같은 기타 정보도 볼 수 있습니다. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="큐 세부 정보":::


## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요. 

- [Python용 Azure Service Bus 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - **sync_samples** 폴더에는 동기 방식으로 Service Bus와 상호 작용하는 방법을 보여주는 샘플이 있습니다. 이 빠른 시작에서는 이 메서드를 사용했습니다. 
    - **async_samples** 폴더에는 비동기 방식으로 Service Bus와 상호 작용하는 방법을 보여주는 샘플이 있습니다. 
- [azure-servicebus 참조 설명서](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
