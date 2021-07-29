---
title: Service Bus 배달 못 한 편지 큐 | Microsoft Docs
description: Azure Service Bus의 배달 못한 편지 큐에 대해 설명합니다. Service Bus 큐 및 토픽 구독은 배달 못한 편지 큐라고 하는 보조 하위 큐를 제공합니다.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 9a7eaf3cee7af4ba0f0049ee8b7a8dd0b271a94a
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108804469"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Service Bus 배달 못 한 편지 큐의 개요

Azure Service Bus 큐 및 토픽 구독은 *DLQ(배달 못한 편지 큐)* 라고 하는 보조 하위 큐를 제공합니다. 배달 못 한 편지 큐는 명시적으로 만들 필요가 없으며 주 엔터티와 독립적으로 삭제하거나 관리할 수 없습니다.

이 문서에서는 Service Bus에서 배달하지 못한 편지 큐에 대해 설명합니다. 대부분의 토론은 GitHub의 [배달하지 못한 편지 큐 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue)에서 설명합니다.
 
## <a name="the-dead-letter-queue"></a>배달하지 못한 편지 큐

배달 못한 편지 큐의 목적은 수신기에 배달할 수 없는 메시지 또는 처리할 수 없는 메시지를 보관하는 것입니다. 그런 다음 DLQ에서 메시지를 제거하여 검사할 수 있습니다. 애플리케이션에서 운영자의 도움을 받아 문제를 수정한 후 메시지를 다시 전송하고, 오류가 발생했다는 사실을 기록하고, 수정 작업을 수행할 수 있습니다. 

API 및 프로토콜의 관점에서, 부모 엔터티의 배달하지 못한 편지 작업을 통해 메시지를 제출할 수 있다는 점만 제외하면 DLQ는 다른 큐와 거의 비슷합니다. 또한 TTL(Time to Live)이 확인되지 않으며, DLQ에서 메시지를 배달 못한 편지로 처리할 수 없습니다. 배달 못 한 편지 큐는 배달 보기-잠금 및 트랜잭션 작업을 완벽하게 지원합니다.

DLQ는 자동으로 정리되지 않습니다. 사용자가 DLQ에서 명시적으로 메시지를 검색하고 배달 못한 메시지를 완료할 때까지 메시지는 DLQ에 남아 있습니다.


## <a name="dlq-message-count"></a>DLQ 메시지 수
토픽 수준에서 배달 못한 편지 큐의 메시지 수를 가져올 수 없습니다. 이는 Service Bus에서 내부 오류를 throw하지 않는 한 메시지가 토픽 수준에 있지 않기 때문입니다. 대신 발신기에서 토픽에 메시지를 보낼 때 메시지는 수 밀리초 내에 해당 토픽에 대한 구독에 전달되므로 더 이상 토픽 수준에 있지 않습니다. 따라서 DLQ에서 해당 토픽의 구독과 연결된 메시지를 볼 수 있습니다. 다음 예에서는 **Service Bus Explorer** 에서 "test1" 구독에 대한 62개의 메시지가 현재 DLQ에 있음을 보여 줍니다. 

![DLQ 메시지 수](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

또한 [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) Azure CLI 명령을 사용하여 DLQ 메시지 수를 가져올 수도 있습니다. 

## <a name="moving-messages-to-the-dlq"></a>DLQ로 메시지 이동
Service Bus의 여러 활동에 의해 메시징 엔진 자체 내에서 DLQ로 메시지가 푸시됩니다. 또한 애플리케이션이 명시적으로 메시지를 DLQ로 이동시킬 수 있습니다. 다음 두 가지 속성(배달 못한 편지 이유 및 배달 못한 편지 설명)이 배달 못한 편지 메시지에 추가됩니다. 애플리케이션에서 배달 못한 편지 이유 속성에 대한 고유의 코드를 정의할 수는 있지만, 시스템에서 다음 값을 설정합니다.

| 배달 못한 편지 이유 | 배달 못한 편지 오류 설명 |
| --- | --- |
|HeaderSizeExceeded |이 스트림에 할당된 크기를 초과합니다. |
|TTLExpiredException |메시지가 만료되어 배달 못 한 편지로 처리되었습니다. 자세한 내용은 [Time to Live](#time-to-live) 섹션을 참조하세요. |
|세션 ID가 null입니다. |세션이 활성화된 엔터티가 세션 식별자가 null인 메시지를 허용하지 않습니다. |
|MaxTransferHopCountExceeded | 큐 간에 전달할 때 허용되는 최대 홉 수입니다. 값은 4로 설정됩니다. |
| MaxDeliveryCountExceededExceptionMessage | 최대 배달 시도 후에는 메시지를 사용할 수 없습니다. 자세한 내용은 [최대 배달 횟수](#maximum-delivery-count) 섹션을 참조하세요. |

## <a name="maximum-delivery-count"></a>최대 배달 횟수
Service Bus 큐 및 구독에 대한 메시지 배달 시도 횟수에는 제한이 있습니다. 기본값은 10입니다. 메시지가 피킹 잠금 상태로 전달되었지만 명시적으로 중단되었거나 잠금이 만료되면 메시지의 배달 수가 증가합니다. 배달 수가 제한을 초과하면 메시지가 DLQ로 이동됩니다. DLQ의 메시지에서 배달 못한 이유는 MaxDeliveryCountExceeded로 설정됩니다. 이 동작을 사용하지 않도록 설정할 수 없지만 최대 배달 횟수를 큰 값으로 설정할 수 있습니다.

## <a name="time-to-live"></a>TTL(Time to live)
큐 또는 구독에서 배달 못한 편지 설정을 사용할 경우 모든 만료된 메시지가 DLQ로 이동합니다. 배달 못한 편지의 이유 코드는 TTLExpiredException으로 설정됩니다.

지연된 메시지는 만료 후 배달 못한 편지 큐로 자동으로 이동되지 않습니다. 이 동작은 의도된 것입니다.

## <a name="errors-while-processing-subscription-rules"></a>구독 규칙을 처리하는 동안 오류 발생
필터 평가 예외에서 배달 못한 편지 설정을 사용할 경우 구독의 SQL 필터 규칙이 실행되는 동안 발생하는 모든 오류가 잘못된 메시지와 함께 DLQ에 캡처됩니다. 일부 메시지 유형에 구독자가 없는 프로덕션 환경에서는 이 옵션을 사용하지 마세요.

## <a name="application-level-dead-lettering"></a>애플리케이션 수준에서 배달 못 한 편지 처리
시스템에서 제공하는 배달 못 한 편지 처리 기능 외에도 애플리케이션에서 DLQ를 사용하여 허용할 수 없는 메시지를 명시적으로 거부할 수 있습니다. 여기에는 모든 종류의 시스템 문제로 인해 제대로 처리할 수 없는 메시지, 잘못된 페이로드가 포함된 메시지 또는 메시지 수준 보안 체계가 사용될 때 인증에 실패한 메시지가 포함될 수 있습니다.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo 또는 SendVia 시나리오의 배달 못한 편지
다음과 같은 상황에서는 배달하지 못한 편지 큐에 메시지가 전달됩니다.

- 메시지는 [모두 체인으로 연결된](service-bus-auto-forwarding.md) 4개를 초과하는 큐 또는 토픽을 통과합니다.
- 대상 큐 또는 항목은 사용되지 않도록 설정되거나 삭제됩니다.
- 대상 큐 또는 항목이 최대 엔터티 크기를 초과합니다.

## <a name="path-to-the-dead-letter-queue"></a>배달 못한 편지 큐의 경로
다음 구문을 사용하여 배달 못한 편지 큐에 액세스할 수 있습니다.

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>다음 단계
[큐 또는 구독에 배달 못한 편지 사용](enable-dead-letter.md)에서 **메시지 만료 시 배달 못한 편지 설정** 의 다양한 구성 방법에 대해 알아보세요.
