---
title: 분할된 Azure Service Bus 큐 및 토픽 만들기 | Microsoft Docs
description: 여러 메시지 broker를 사용하여 Service Bus 큐 및 항목을 분할하는 방법을 설명합니다.
ms.topic: article
ms.date: 09/21/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b6e0c0473ea5bf50ed64bf7abee2c66ab5789840
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616083"
---
# <a name="partitioned-queues-and-topics"></a>분할 큐 및 항목

Azure Service Bus에서는 여러 메시지 broker가 메시지를 처리하고 여러 메시징 저장소가 메시지를 저장하도록 합니다. 일반적인 큐 또는 항목은 단일 메시지 broker에서 처리되며 하나의 메시징 저장소에 저장됩니다. Service Bus *파티션* 을 사용하면 큐 및 항목 또는 *메시징 엔터티* 가 여러 메시지 broker 및 메시징 저장소에 분할될 수 있습니다. 분할은 분할된 엔터티의 전체 처리량이 단일 메시지 broker 또는 메시징 저장소의 성능으로 제한되지 않는다는 의미입니다. 또한 메시징 저장소가 일시적으로 중단된 경우에도 분할된 큐 또는 토픽을 계속 사용할 수 있습니다. 분할된 큐 및 항목은 트랜잭션 및 세션에 대한 지원 같은 모든 고급 Service Bus 기능을 포함할 수 있습니다.

> [!NOTE]
> 분할은 기본 또는 표준 SKU의 모든 큐와 항목에서 엔터티 생성에 지원됩니다. 더 이상 프리미엄 메시지 SKU에 지원되지 않지만 프리미엄 네임스페이스에서 지원되는 이전에 분할된 기존 엔터티는 정상적으로 계속 작동합니다.
 
기존 큐나 토픽에 대한 분할 옵션을 변경할 수 없으며 엔터티를 만들 때만 이 옵션을 설정할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

분할된 큐 또는 토픽 각각은 여러 파티션으로 구성됩니다. 각 파티션은 다른 메시징 저장소에 저장되고 서로 다른 메시지 브로커에서 처리됩니다. 분할된 큐 또는 토픽으로 메시지를 보내면 Service Bus는 메시지를 파티션 중 하나에 할당합니다. 선택은 발신자가 지정할 수 있는 Service Bus 또는 파티션 키에서 임의로 수행됩니다.

클라이언트가 분할된 큐 또는 분할된 토픽의 구독에서 메시지를 받으려는 경우 Service Bus는 모든 파티션에서 메시지를 쿼리한 다음 메시징 저장소 중 하나에서 획득되는 첫 번째 메시지를 수신자에게 반환합니다. Service Bus는 더 많은 수신 요청을 받을 때 다른 메시지를 캐시하고 반환합니다. 수신하는 클라이언트는 분할을 인식하지 못합니다. 분할된 큐나 토픽의 클라이언트 연결 동작(예, 읽기, 완료, 연기, 배달 못함, 프리페치)은 일반적인 엔터티 동작과 동일합니다.

분할되지 않은 엔터티의 피킹(peeking) 작업은 항상 가장 오래된 메시지를 반환하지만 분할된 엔터티에서는 그러지 않습니다. 대신 메시지 브로커가 먼저 응답한 파티션 중 하나에서 가장 오래된 메시지를 반환합니다. 반환된 메시지가 모든 파티션에서 가장 오래된 메시지이지 않을 수도 있습니다. 

분할된 큐나 토픽에 메시지를 보내거나 메시지를 받을 때 추가 비용이 없습니다.

> [!NOTE]
> 피킹(peeking) 작업은 해당 시퀀스 번호를 기준으로 파티션에서 가장 오래된 메시지를 반환합니다. 분할된 엔터티의 경우 시퀀스 번호는 파티션에 상대적으로 발생됩니다. 자세한 내용은 [Message sequencing and timestamps](../service-bus-messaging/message-sequencing.md)(메시지 시퀀싱 및 타임스탬프)를 참조하세요.

## <a name="use-of-partition-keys"></a>파티션 키의 사용

메시지가 분할된 큐 또는 항목에 큐를 넣으면 Service Bus는 파티션 키의 존재를 확인합니다. 파티션 키를 찾으면 해당 키를 기반으로 파티션을 선택합니다. 파티션 키를 찾지 못하면 내부 알고리즘을 기반으로 파티션을 선택합니다.

### <a name="using-a-partition-key"></a>파티션 키 사용

세션 또는 트랜잭션과 같은 일부 시나리오에서는 메시지를 특정 파티션에 저장해야 합니다. 이러한 모든 시나리오에서는 파티션 키를 사용해야 합니다. 동일한 파티션 키를 사용하는 모든 메시지는 동일한 파티션에 할당됩니다. 파티션을 일시적으로 사용할 수 없는 경우 Service Bus가 오류를 반환합니다.

시나리오에 따라 다양한 메시지 속성이 파티션 키로 사용됩니다.

**SessionId**: 메시지에 세션 ID 속성 집합이 있으면 Service Bus는 이 집합을 파티션 키로 사용합니다. 이러한 방식으로 동일한 세션에 속한 모든 메시지가 동일한 메시지 broker에서 처리됩니다. 세션을 사용하면 Service Bus가 세션 상태의 일관성 뿐만 아니라 메시지 순서를 보장할 수 있습니다.

**PartitionKey**: 메시지에 파티션 키 속성이 있지만 세션 ID 속성 집합이 없으면 Service Bus는 파티션 키 속성 값을 파티션 키로 사용합니다. 메시지에 세션 ID와 파티션 키 속성 집합이 모두 있으면 두 속성 모두 동일해야 합니다. 파티션 키 속성이 세션 ID 속성이 아닌 다른 값으로 설정되면 Service Bus는 잘못된 작업 예외를 반환합니다. 보낸 사람이 세션이 아닌 인식 트랜잭션 메시지를 보내는 경우 파티션 키 속성을 사용해야 합니다. 파티션 키는 트랜잭션 내에서 전송되는 모든 메시지가 동일한 메시징 broker에서 처리되도록 합니다.

**MessageId**: [중복 검색 기능](duplicate-detection.md)을 사용하여 큐나 토픽을 만들었지만 세션 ID나 파티션 키 속성이 설정되지 않은 경우 메시지 ID 속성 값이 파티션 키 역할을 합니다. (보내는 애플리케이션에서 메시지 ID를 할당하지 않으면 Microsoft 클라이언트 라이브러리가 자동으로 메시지 ID를 할당합니다.) 이 경우에 동일한 메시지의 모든 복사본이 동일한 메시지 브로커에서 처리됩니다. 이 ID를 사용하면 Service Bus가 중복 메시지를 감지하고 제거할 수 있습니다. 중복 검색 기능을 사용할 수 없으면 Service Bus는 메시지 ID 속성을 파티션 키로 간주하지 않습니다.

### <a name="not-using-a-partition-key"></a>파티션 키 사용하지 않음

파티션 키가 없으면 Service Bus는 분할된 큐 또는 토픽의 모든 파티션에 라운드 로빈 방식으로 메시지를 분산합니다. 선택한 파티션을 사용할 수 없으면 Service Bus는 다른 파티션에 메시지를 할당합니다. 이러한 방식으로 메시징 저장소를 일시적으로 사용할 수 없음에도 불구하고 보내기 작업은 성공합니다. 그러나 파티션 키가 제공하는 순서는 보장되지 않습니다.

가용성(파티션 키 없음) 및 일관성(파티션 키 사용) 간 상쇄 관계에 대한 자세한 내용은 [Event Hubs에서 가용성 및 일관성](../event-hubs/event-hubs-availability-and-consistency.md)을 참조하세요. 사용자에게 노출되지 않는 파티션 ID를 제외하고 이 정보는 분할된 Service Bus 엔터티에 동일하게 적용됩니다.

메시지를 다른 파티션으로 큐에 넣도록 Service Bus에 충분한 시간을 주려면 메시지를 보내는 클라이언트에서 지정한 시간 초과 값이 15초보다 길어야 합니다. 기본값(60초)을 사용하는 것이 좋습니다.

파티션 키는 특정 파티션에 메시지를 “고정”합니다. 이 파티션이 보관된 메시징 저장소를 사용할 수 없으면 Service Bus는 오류를 반환합니다. 파티션 키가 없으면 Service Bus는 다른 파티션을 선택할 수 있고 작업은 성공합니다. 그러므로 필요한 경우가 아니면 파티션 키를 제공하지 않는 것이 좋습니다.

## <a name="advanced-topics"></a>고급 항목

### <a name="use-transactions-with-partitioned-entities"></a>분할된 엔터티가 포함된 트랜잭션 사용

트랜잭션의 일부로 전송되는 메시지는 파티션 키를 지정해야 합니다. 키는 세션 ID, 파티션 키 또는 메시지 ID 등의 속성 중 하나일 수 있습니다. 동일한 트랜잭션의 일부로 전송되는 모든 메시지는 동일한 파티션 키를 지정해야 합니다. 트랜잭션 내에서 파티션 키 없이 메시지를 보내려면 Service Bus는 잘못된 작업 예외를 반환합니다. 다른 파티션 키를 가진 동일한 트랜잭션 내에서 여러 메시지를 보내려면 Service Bus는 잘못된 작업 예외를 반환합니다. 예를 들면 다음과 같습니다.

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

파티션 키로 제공한 속성을 설정하면 Service Bus는 특정 파티션에 메시지를 고정합니다. 이 동작은 트랜잭션이 사용되는지 여부와 상관 없이 발생합니다. 필요하지 않은 경우 파티션 키를 지정하지 않는 것이 좋습니다.

### <a name="use-transactions-in-sessions-with-partitioned-entities"></a>분할된 엔터티가 포함된 세션에서 트랜잭션 사용

세션 인식 토픽이나 큐에 트랜잭션 메시지를 보내려면 메시지에 세션 ID 속성 집합이 있어야 합니다. 파티션 키 속성도 지정할 경우 세션 ID 속성과 동일해야 합니다. 다른 경우 Service Bus가 잘못된 작업 예외를 반환합니다.

일반(분할되지 않은) 큐나 토픽과 달리 단일 트랜잭션을 사용하여 다른 세션에 메시지 여러 개를 보낼 수 없습니다. 시도할 경우 Service Bus가 잘못된 작업 예외를 반환합니다. 예를 들면 다음과 같습니다.

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    ServiceBusMessage msg = new ServiceBusMessage("This is a message");
    msg.SessionId = "mySession";
    await sender.SendMessageAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

### <a name="automatic-message-forwarding-with-partitioned-entities"></a>분할된 엔터티로 자동 메시지 전달

Service Bus는 분할된 엔터티 간에 자동 메시지 전달을 지원합니다. 큐와 구독을 만들거나 업데이트할 때 이 기능을 사용하도록 설정할 수 있습니다. 자세한 내용은 [메시지 전달 사용](enable-auto-forward.md)을 참조하세요. 메시지가 파티션 키(세션 ID, 파티션 키 또는 메시지 ID)를 지정하는 경우 해당 파티션 키를 대상 엔터티에 사용합니다.

## <a name="considerations-and-guidelines"></a>고려 사항 및 지침
* **높은 일관성 기능**: 엔터티가 세션, 중복 검색, 분할 키의 명시적 제어 등과 같은 기능을 사용하면 메시징 작업이 항상 특정 파티션으로 라우팅됩니다. 어떤 파티션에서든 트래픽이 높아지거나 기본 저장소가 손상되면 해당 작업은 실패하고 가용성은 줄어듭니다. 결과적으로 분할되지 않은 엔터티보다 일관성은 훨씬 높아집니다. 모든 트래픽이 아닌 트래픽 일부에서만 문제가 나타나기 때문입니다. 자세한 내용은 이 [가용성 및 일관성 논의](../event-hubs/event-hubs-availability-and-consistency.md)를 참조하세요.
* **관리**: 생성, 업데이트 및 삭제와 같은 작업은 엔터티의 모든 파티션에서 수행되어야 합니다. 어떤 파티션이라도 비정상이면 이러한 작업이 실패할 수 있습니다. 가져오기 작업의 경우 메시지 수와 같은 정보를 모든 파티션에서 집계해야 합니다. 파티션이 비정상이면 엔터티 가용성 상태가 제한됨으로 보고됩니다.
* **저용량 메시지 시나리오**: 이런 시나리오에서, 특히 HTTP 프로토콜을 사용하는 경우 모든 메시지를 가져오기 위해 여러 수신 작업을 수행해야 할 수 있습니다. 수신 요청의 경우 프런트 엔드는 모든 파티션에서 수신을 수행하고 수신된 모든 응답을 캐시합니다. 동일한 연결에 대한 후속 수신 요청은 이러한 캐싱 덕분에 수신 대기 시간이 감소합니다. 그러나 연결이 여러 개 있거나 HTTP를 사용하는 경우 각 요청에 대해 새 연결이 설정됩니다. 따라서 동일한 노드에 요청이 들어온다고 보장할 수 없습니다. 모든 기존 메시지가 잠기고 다른 프런트 엔드에 캐시될 경우 수신 작업은 **null** 을 반환합니다. 결과적으로 메시지가 만료되고 다시 받을 수 있습니다. HTTP 연결 유지를 사용하는 것이 좋습니다. 볼륨이 낮은 시나리오에서 분할을 사용하는 경우 수신 작업이 예상보다 오래 걸릴 수 있습니다. 따라서 해당 시나리오에서는 분할을 사용하지 않는 것이 좋습니다. 기존 분할된 엔터티를 삭제하고 분할을 사용하지 않도록 설정하여 엔터티를 다시 만들어 성능을 향상할 수 있습니다.
* **메시지 탐색/피킹**: 피킹 작업이 항상 요청된 메시지 수를 반환하지는 않습니다. 이 동작에는 일반적으로 다음 두 가지 이유가 있습니다. 하나는 메시지 컬렉션의 집계 크기가 최대 크기인 256KB를 초과하기 때문입니다. 또 다른 이유는 분할된 큐나 토픽에서 파티션에 요청된 메시지 수를 반환하는 데 충분한 메시지가 없을 수 있다는 점입니다. 일반적으로 애플리케이션에서 특정 개수의 메시지를 피킹/탐색하려는 경우 해당 메시지 수에 도달할 때까지 또는 피킹할 수 있는 추가 메시지가 없게 될 때까지 피킹 작업을 반복적으로 호출해야 합니다. 코드 샘플을 비롯한 자세한 내용은 [메시지 탐색](message-browsing.md)을 참조하세요.

## <a name="partitioned-entities-limitations"></a>분할된 엔터티 제한 사항
현재 Service Bus에서는 분할된 큐 및 항목에 다음과 같은 제한 사항이 적용됩니다.

* 프리미엄 메시지 계층에서 분할된 큐와 토픽은 지원되지 않습니다. 세션은 SessionId를 사용하는 방식을 통해 프리미어 계층에서 지원됩니다. 
* 분할된 큐와 토픽은 단일 트랜잭션에서 다른 세션에 속한 메시지를 보내지 않습니다.
* Service Bus는 현재 네임스페이스당 최대 100개의 분할된 큐 또는 항목을 허용합니다. 각 분할된 큐나 토픽은 네임스페이스당 엔터티 10,000개를 할당량으로 계산합니다(프리미엄 계층에는 적용되지 않음).


## <a name="next-steps"></a>다음 단계
Azure Portal, PowerShell, CLI, Resource Manager 템플릿, .NET, Java, Python, JavaScript 등을 사용하여 분할을 사용하도록 설정할 수 있습니다. 자세한 내용은 [Enable partitioning](enable-partitions.md)(분할 사용)을 참조하세요. 

[AMQP 1.0 프로토콜 가이드](service-bus-amqp-protocol-guide.md)에서 AMQP 1.0 메시징 사양의 핵심 개념을 읽어보세요.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
