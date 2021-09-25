---
title: Azure Service Bus 제한 개요 | Microsoft Docs
description: 표준 계층 및 프리미엄 계층의 Service Bus 제한에 대한 개요입니다.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0607cd96b68de960829d80f00765586213a55d2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609178"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure Service Bus의 작업 제한

클라우드 네이티브 솔루션에서는 워크로드에 따라 스케일링할 수 있는 무제한 리소스라는 개념을 제공합니다. 이 개념은 온-프레미스 시스템보다 클라우드에 더 잘 맞지만 클라우드에서도 여전히 제한은 존재합니다.

이 문서에 설명된 대로 이러한 제한으로 인해 표준 계층 및 프리미엄 계층 둘 다에서 클라이언트 애플리케이션 요청이 제한될 수 있습니다. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure Service Bus 표준 계층의 제한

Azure Service Bus 표준 계층은 종량제 가격 책정 모델을 사용하는 다중 테넌트 설정으로 작동합니다. 여기서는 동일한 클러스터의 여러 네임스페이스가 할당된 리소스를 공유합니다. 표준 계층은 처리량이 낮은 프로덕션 시스템을 사용하는 개발자, 테스트, QA 환경용으로 선택하는 것이 좋습니다.

이전의 Azure Service Bus 제한은 전적으로 리소스 사용률을 기반으로 하는 정교하지 않은 제한 방식이었습니다. 이제는 제한 논리를 구체화하여 리소스를 공유하는 모든 네임스페이스에 예측 가능한 제한 동작을 제공할 수 있습니다.

동일한 리소스를 사용하는 모든 Azure Service Bus 표준 네임스페이스의 리소스를 공평하게 사용하고 배포하기 위해 제한 논리가 크레딧에 기반하도록 수정되었습니다.

> [!NOTE]
> 제한이 Azure Service Bus 또는 클라우드 네이티브 서비스에 ***새로운 기능이 아니라는*** 점에 유의해야 합니다.
>
> 크레딧 기반 제한에서는 간단히 다중 테넌트 표준 계층 환경에서 다양한 네임스페이스의 리소스 공유 방식을 구체화하여 리소스를 공유하는 모든 네임스페이스의 공평한 사용을 지원합니다.

### <a name="what-is-credit-based-throttling"></a>크레딧 기반 제한이란 무엇인가요?

크레딧 기반 제한에서는 특정 기간에 지정된 네임스페이스에서 수행할 수 있는 작업의 수를 제한합니다. 

크레딧 기반 제한 워크플로는 다음과 같습니다. 

  * 각 기간이 시작될 때 각 네임스페이스에 특정 개수의 크레딧을 제공합니다.
  * 송신기 또는 수신기 클라이언트 애플리케이션에서 수행하는 모든 작업에 이 크레딧이 적용됩니다(사용 가능한 크레딧에서 차감됨).
  * 크레딧을 모두 사용하면 다음 기간이 시작될 때까지 후속 작업이 제한됩니다.
  * 다음 기간이 시작될 때 크레딧이 보충됩니다.

### <a name="what-are-the-credit-limits"></a>크레딧 한도란 무엇인가요?

크레딧 한도는 현재 네임스페이스 기준으로 초당 ‘1000’크레딧으로 설정됩니다.

모든 작업이 동일한 비용으로 만들어지는 것은 아닙니다. 각 작업의 크레딧 비용은 다음과 같습니다. 

| 작업 | 크레딧 비용|
|-----------|-----------|
| 데이터 작업(Send, SendAsync, Receive, ReceiveAsync, Peek) |메시지당 1크레딧 |
| 관리 작업(Create, Read, Update, Delete on Queues, Topics, Subscriptions, Filters) | 10크레딧 |

> [!NOTE]
> 메시지를 토픽으로 송신하는 경우 구독에서 사용 가능하게 설정하기 전에 각 메시지를 필터에 대해 평가합니다.
> 각 필터 평가도 크레딧 한도가 적용됩니다(즉, 필터 평가당 1크레딧).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>작업이 제한되는지 알 수 있는 방법은 무엇인가요?

클라이언트 애플리케이션 요청이 제한되는 경우 아래 서버 응답이 애플리케이션에서 수신되고 기록됩니다.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>작업이 제한되지 않도록 하는 방법은 무엇인가요?

공유 리소스를 사용하는 경우 해당 리소스를 공유하는 다양한 Service Bus 네임스페이스 간에 리소스를 공정하게 사용하도록 적용하는 것이 중요합니다. 제한을 사용하면 단일 작업에서 스파이크가 발생해도 동일한 리소스의 다른 워크로드가 제한되지 않습니다.

이 문서의 뒷부분에 설명된 대로 클라이언트 SDK(및 다른 Azure PaaS 제공 사항)에는 기본 재시도 정책이 빌드되어 있으므로 제한될 위험이 없습니다. 제한된 요청은 지수 백오프를 사용하여 다시 시도되며 크레딧이 보충되면 결국 진행됩니다.

당연히 일부 애플리케이션은 제한에 민감할 수 있습니다. 이런 경우 [현재 Service Bus 표준 네임스페이스를 프리미엄으로 마이그레이션](service-bus-migrate-standard-premium.md)하는 것이 좋습니다. 

마이그레이션하면 Service Bus 네임스페이스에 전용 리소스를 할당하고 워크로드에 스파이크가 발생하는 경우 리소스를 적절하게 스케일 업하여 제한될 가능성을 줄일 수 있습니다. 워크로드가 정상 수준으로 감소하면 네임스페이스에 할당된 리소스를 스케일 다운할 수도 있습니다.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure Service Bus 프리미엄 계층의 제한

[Azure Service Bus 프리미엄](service-bus-premium-messaging.md) 계층은 메시징 단위를 기준으로 고객의 각 네임스페이스 설정에 전용 리소스를 할당합니다. 전용 리소스는 예측 가능한 처리량과 대기 시간을 제공하므로 처리량이 높거나 민감한 프로덕션 등급 시스템에 사용하는 것이 좋습니다.

또한 프리미엄 계층을 사용하면 워크로드에 스파이크가 발생하는 경우 고객이 처리량 용량을 스케일 업할 수 있습니다.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Service Bus 프리미엄에서 제한은 어떻게 작동하나요?

Service Bus 프리미엄의 전용 리소스 할당을 사용하면 제한은 전적으로 네임스페이스에 할당된 리소스 제한 사항에 따라 작동합니다.

요청 수가 현재 리소스에서 서비스를 지원할 수 있는 요청 수보다 많으면 요청이 제한됩니다.

### <a name="how-will-i-know-that-im-being-throttled"></a>작업이 제한되는지 알 수 있는 방법은 무엇인가요?

Azure Service Bus 프리미엄에서 제한을 확인하는 방법은 여러 가지가 있습니다. 
  * [Azure Monitor 요청 메트릭](monitor-service-bus-reference.md#request-metrics)에 **제한된 요청** 이 표시되므로 제한된 요청 수를 확인합니다.
  * 높은 **CPU 사용** 은 현재 리소스 할당이 높으며 현재 워크로드가 감소하지 않으면 요청이 제한될 수 있음을 나타냅니다.
  * 높은 **메모리 사용** 은 현재 리소스 할당이 높으며 현재 워크로드가 감소하지 않으면 요청이 제한될 수 있음을 나타냅니다.

### <a name="how-can-i-avoid-being-throttled"></a>작업이 제한되지 않도록 하는 방법은 무엇인가요?

Service Bus 프리미엄 네임스페이스에는 이미 전용 리소스가 있으므로 워크로드에서 스파이크가 발생하는(또는 예상되는) 경우 네임스페이스에 할당된 메시징 단위 수를 스케일 업하여 제한될 가능성을 줄일 수 있습니다.

위의 메트릭을 변경하여 트리거할 수 있는 [Runbook](../automation/automation-create-alert-triggered-runbook.md)을 만들면 스케일 업/다운할 수 있습니다.

## <a name="faqs"></a>FAQ

### <a name="how-does-throttling-affect-my-application"></a>제한은 애플리케이션에 어떤 영향을 주나요?

요청이 제한된 경우 리소스에서 허용하는 것보다 요청 수가 더 많으므로 서비스가 사용 중인 상태임을 의미합니다. 동일한 작업을 잠시 후 다시 시도하면 서비스가 현재 워크로드 처리를 완료한 경우 요청에 대응할 수 있습니다.

제한은 모든 클라우드 네이티브 서비스의 예상되는 동작이므로 Azure Service Bus SDK 자체에 재시도 논리가 빌드되어 있습니다. 기본값은 매번 동일한 요청이 제한되지 않도록 지수 백오프를 사용하는 자동 재시도로 설정됩니다.

기본 재시도 논리는 모든 작업에 적용됩니다.

### <a name="does-throttling-result-in-data-loss"></a>제한으로 인해 데이터 손실이 발생하나요?

Azure Service Bus는 지속성을 지원하도록 최적화되어 있으므로 서비스에서 요청 성공을 승인하기 전에 Service Bus로 송신된 모든 데이터가 스토리지에 커밋됩니다.

Service Bus에서 요청이 성공적으로 ‘ACK’(승인)되면 Service Bus가 요청을 성공적으로 처리했음을 나타냅니다. Service Bus가 ‘NACK’(실패)를 반환하면 Service Bus가 요청을 처리하지 못했으며 클라이언트 애플리케이션에서 요청을 다시 시도해야 함을 나타냅니다.

하지만 요청이 제한된 경우에는 서비스가 리소스 제한으로 인해 현재 요청을 수락하고 처리할 수 없음을 나타냅니다. Service Bus가 단지 요청을 확인하지 않았다고 해서 데이터 손실을 나타내는 것은 **아닙니다**. 이 경우 Service Bus SDK의 기본 재시도 정책을 따르면 요청이 결국 처리되도록 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [빠른 시작: Azure Portal 및 .NET을 사용하여 메시지 보내기 및 받기](service-bus-quickstart-portal.md)
* [자습서: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트](service-bus-tutorial-topics-subscriptions-portal.md)

