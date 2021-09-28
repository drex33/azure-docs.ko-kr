---
title: Azure Service Bus 트랜잭션 처리 개요
description: 이 문서에서는 Azure Service Bus 기능을 통해 전송 및 트랜잭션 처리에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eb3bf6eef551fd13788f7659eb8becede8e250d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666215"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Service Bus 트랜잭션 처리의 개요

이 문서에서는 Microsoft Azure Service Bus의 트랜잭션 기능을 설명합니다. 대부분의 설명은 Service Bus 샘플을 [사용하는 AMQP 트랜잭션에서 설명합니다.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia) 이 문서는 트래잭션 처리에 대한 개요와 Service Bus의 *send via* 기능으로 제한되며 원자적 트랜잭선 샘플의 범위는 훨씬 광범위하고 더 복잡합니다.

> [!NOTE]
> Service Bus 기본 계층은 트랜잭션을 지원하지 않습니다. 표준 및 프리미엄 계층은 트랜잭션을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

## <a name="transactions-in-service-bus"></a>Service Bus의 트랜잭션

*트랜잭션은* 둘 이상의 작업을 실행 *범위* 로 그룹화합니다. 기본적으로 이러한 트랜잭션은 지정된 작업 그룹에 속한 모든 작업이 성공 또는 실패해야 합니다. 이러한 점에서 트랜잭션은 하나의 단위(또는 *원자성* 이라고 함)로 작용합니다.

Service Bus는 트랜잭션 메시지 broker이며 메시지 저장소에 대한 모든 내부 작업의 트랜잭션 무결성을 보장합니다. 배달하지 Service Bus [큐로](service-bus-dead-letter-queues.md) 메시지 이동 또는 엔터티 간 메시지 [자동 전달과](service-bus-auto-forwarding.md) 같은 모든 메시지 전송은 트랜잭션입니다. 따라서 Service Bus에서 메시지를 수락할 경우 이미 저장되어 시퀀스 번호가 레이블로 지정되었습니다. 이후로 Service Bus 내 메시지 전송은 엔터티 전반에서 조정된 작업으로 손실(원본 성공 및 대상 실패) 또는 메시지의 중복(원본 실패 및 대상 성공)이 발생하지 않습니다.

Service Bus는 트랜잭션 범위 내에서 단일 메시징 엔터티(큐, 토픽, 구독)에 대한 그룹화 작업을 지원합니다. 예를 들어 트랜잭션 범위 내에서 하나의 큐에 여러 메시지를 보낼 수 있으며, 트랜잭션이 성공적으로 완료되면 메시지가 큐의 로그에만 커밋됩니다.

## <a name="operations-within-a-transaction-scope"></a>트랜잭션 범위 내 작업

트랜잭션 범위 내에서 수행할 수 있는 작업은 다음과 같습니다.

- Send
- 완료
- 중단
- Deadletter
- 연기
- 잠금 갱신

수신 작업은 애플리케이션이 일부 수신 루프 내부 또는 콜백과 함께 peek-lock 모드를 사용하여 메시지를 획득하고 메시지를 처리하기 위한 트랜잭션 범위를 여는 것으로 가정하기 때문에 포함되지 않습니다.

메시지 처리(완료, 중단, 배달 못 한 편지, 지연)가 범위 내에서 그리고 전반적인 트랜잭션 결과에 다라 발생합니다.

## <a name="transfers-and-send-via"></a>전송 및 "send via"

큐 또는 토픽에서 프로세서로, 그리고 다른 큐 또는 토픽으로 데이터를 트랜잭션으로 전달하도록 설정하기 위해 Service Bus *전송을 지원합니다.* 전송 작업에서 발신자는 먼저 전송 *큐 또는 토픽* 에 메시지를 보내고 전송 큐 또는 토픽은 자동 전달 기능이 사용하는 것과 동일한 강력한 전송 구현을 사용하여 메시지를 의도한 대상 큐 또는 토픽으로 즉시 이동합니다. 메시지는 전송 큐 또는 토픽의 소비자에게 표시되는 방식으로 전송 큐 또는 토픽의 로그에 커밋되지 않습니다.

전송 큐 또는 토픽 자체가 보낸 사람의 입력 메시지 원본인 경우 이 트랜잭션 기능의 기능이 분명해집니다. 즉, Service Bus 하나의 원자성 작업으로 입력 메시지에 대해 전체(또는 지연 또는 데드 문자) 작업을 수행하는 동안 전송 큐 또는 토픽을 "통해" 대상 큐 또는 토픽으로 메시지를 전송할 수 있습니다. 

토픽 구독에서 받은 다음 동일한 트랜잭션의 큐 또는 토픽으로 보내야 하는 경우 전송 엔터티는 토픽이어야 합니다. 이 시나리오에서는 토픽에서 트랜잭션 범위를 시작하고, 트랜잭션 범위에서 를 사용하여 구독에서 수신하고, 전송 토픽을 통해 큐 또는 토픽 대상으로 보냅니다. 

### <a name="see-it-in-code"></a>실제 코드 엿보기

이러한 전송을 설정하기 위해 전송 큐를 통해 대상 큐를 목표로 하는 메시지 보낸 사람을 생성합니다. 또한 같은 큐에서 메시지를 풀링하는 수신기가 있을 수도 있습니다. 예:

그런 다음, 간단한 트랜잭션은 다음 예제와 같이 이러한 요소를 사용합니다. 전체 예제를 참조하려면 [GitHub 소스 코드를](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples/Sample06_Transactions.md#transactions-across-entities)참조하세요.

```csharp
var options = new ServiceBusClientOptions { EnableCrossEntityTransactions = true };
await using var client = new ServiceBusClient(connectionString, options);

ServiceBusReceiver receiverA = client.CreateReceiver("queueA");
ServiceBusSender senderB = client.CreateSender("queueB");

ServiceBusReceivedMessage receivedMessage = await receiverA.ReceiveMessageAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    await receiverA.CompleteMessageAsync(receivedMessage);
    await senderB.SendMessageAsync(new ServiceBusMessage());
    ts.Complete();
}
```


## <a name="timeout"></a>제한 시간
트랜잭션은 2분 후에 시간 초과됩니다. 트랜잭션 타이머는 트랜잭션의 첫 번째 작업이 시작될 때 시작됩니다. 

## <a name="next-steps"></a>다음 단계

Service Bus 큐에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Service Bus 큐를 사용하는 방법](service-bus-dotnet-get-started-with-queues.md)
* [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)
* [자동포워드 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Service Bus 샘플과 함께 원자성 트랜잭션](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure 큐와 Service Bus 큐 비교](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


