---
title: Azure Service Bus 프리미엄 및 표준 계층
description: 이 문서에서는 Azure Service Bus의 표준 및 프리미엄 계층에 대해 설명합니다. 이러한 계층을 비교하고 기술적 차이점을 제공합니다.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f0cc6b6d7b9026d9be23e36a587b7ce667ba1652
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811265"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus 프리미엄 및 표준 메시징 계층

큐 및 토픽과 같은 엔터티를 포함하는 Service Bus 메시징은 클라우드 규모로 엔터프라이즈 메시징 기능을 풍부한 게시-구독 의미 체계와 결합합니다. Service Bus 메시징은 정교한 여러 클라우드 솔루션의 통신 백본으로 사용됩니다.

Service Bus 메시징의 *프리미엄* 계층은 중요 업무용 애플리케이션에 대한 확장성, 성능 및 가용성에 관한 일반적인 고객의 요청을 해결합니다. 프리미엄 계층은 프로덕션 시나리오에 사용하는 것이 좋습니다. 기능 집합은 거의 동일하지만 이러한 Service Bus 메시징의 두 계층은 다른 용도로 사용하도록 고안되었습니다.

다음 테이블에는 차이가 자세히 설명되어 있습니다.

| Premium | Standard |
| --- | --- |
| 높은 처리량 |가변 처리량 |
| 예측 가능한 성능 |가변 대기 시간 |
| 고정된 가격 책정 |종량제 가변 가격 |
| 작업을 확장 및 축소하는 기능 |해당 없음 |
| 최대 1MB의 메시지 크기 이 한도는 향후 상향될 수 있습니다. 서비스에 대한 최신 중요 업데이트는 [Azure의 메시징 블로그](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog)를 참조하세요. |최대 256KB의 메시지 크기 |

**Service Bus 프리미엄 메시지** 는 각 고객의 워크로드가 따로 실행되도록 CPU 및 메모리 수준에서 리소스 격리를 제공합니다. 이 리소스 컨테이너를 *메시징 단위* 라고 합니다. 각 프리미엄 네임스페이스에는 하나 이상의 메시징 단위가 할당됩니다. 각 Service Bus 프리미엄 네임스페이스에 대해 1, 2, 4, 8 또는 16 메시징 단위를 구입할 수 있습니다. 단일 워크로드 또는 엔터티는 여러 메시징 단위에 걸쳐 있을 수 있으며 메시징 단위 수는 마음대로 변경할 수 있습니다. 그 결과, Service Bus 기반 솔루션에 대해 예측 가능하고 반복 가능한 성능이 구현됩니다.

이로 인해 예측 가능성 및 가용성도 높아질 뿐 아니라 속도도 더 빨라집니다. 프리미엄 메시징을 사용할 경우 표준 계층을 사용하는 것보다 최고 성능이 훨씬 더 빠릅니다.

> [!NOTE]
> 프리미엄 메시징의 일괄 처리 크기 제한은 1MB입니다.

## <a name="premium-messaging-technical-differences"></a>프리미엄 메시징 기술 차이

다음 섹션에서는 프리미엄 및 표준 메시지 계층 간의 몇 가지 차이점을 설명합니다.

### <a name="partitioned-queues-and-topics"></a>분할 큐 및 항목

분할 큐 및 토픽은 프리미엄 메시징에서 지원되지 않습니다. 분할에 대한 자세한 내용은 [분할 큐 및 항목](service-bus-partitioning.md)을 참조하세요.

### <a name="express-entities"></a>Express 엔터티

프리미엄 메시지가 격리된 런타임 환경에서 실행되므로 Express 엔터티는 프리미엄 네임스페이스에서 지원되지 않습니다. Express 엔터티는 메시지를 영구 저장소에 쓰기 전에 일시적으로 메모리에 보관합니다. 표준 메시징에서 실행되는 코드가 있고 이를 프리미엄 계층으로 이식하려는 경우 Express 엔터티 기능을 사용하지 않도록 설정해야 합니다.

## <a name="premium-messaging-resource-usage"></a>프리미엄 메시징 리소스 사용량
일반적으로 엔터티에 대한 모든 작업으로 인해 CPU 및 메모리 사용량이 발생할 수 있습니다. 다음은 그 중 몇 가지 작업입니다. 

- 큐, 토픽 및 구독에 대한 CRUD(만들기, 검색, 업데이트 및 삭제) 작업과 같은 관리 작업
- 런타임 작업(메시지 보내기 및 받기)
- 작업 및 경고 모니터링

추가 CPU 및 메모리 사용량은 추가로 가격이 책정되지 않습니다. 프리미엄 메시징 계층에는 메시지 단위에 대한 단일 가격이 있습니다.

다음과 같은 이유로 CPU 및 메모리 사용량이 추적되고 표시됩니다. 

- 시스템 내부에 투명성 제공
- 구매한 리소스의 용량 파악
- 스케일 업/다운을 결정하는 데 도움이 되는 용량 계획

## <a name="messaging-unit---how-many-are-needed"></a>메시징 단위 - 몇 개가 필요한가요?

Azure Service Bus 프리미엄 네임스페이스를 프로비저닝할 때 할당된 메시징 단위 수를 지정해야 합니다. 이러한 메시징 단위는 네임스페이스에 할당된 전용 리소스입니다.

Service Bus 프리미엄 네임스페이스에 할당된 메시징 단위 수는 워크로드의 변경(증가 또는 감소)을 고려하여 **동적으로 조정** 할 수 있습니다.

아키텍처의 메시징 단위 수를 결정할 때 고려해야 할 여러 요인이 있습니다.

- 네임스페이스에 할당된 ***1개 또는 2개의 메시징 단위*** 로 시작합니다.
- 네임스페이스에 대한 [리소스 사용 현황 메트릭](monitor-service-bus-reference.md#resource-usage-metrics) 내에서 CPU 사용 현황 메트릭을 연구합니다.
    - CPU 사용량이 ***20% 미만** _인 경우 네임스페이스에 할당된 메시징 단위 수를 _ *_스케일 다운_* 할* 수 있습니다.
    - CPU 사용량이 ***70% 이상** _인 경우 네임스페이스에 할당된 메시징 단위 수를 _ *_스케일 업_* 할* 수 있습니다.

자동으로 크기를 조정(메시징 단위 증가 또는 감소)하도록 Service Bus 네임스페이스를 구성하는 방법을 알아보려면 [메시징 단위 자동 업데이트](automate-update-messaging-units.md)를 참조하세요.

> [!NOTE]
> 네임스페이스에 할당된 리소스의 **크기 조정** 은 선점형이거나 반응형일 수 있습니다.
>
>  * **선점형**: 추가 워크로드가 예상되는 경우(계절성 또는 추세로 인해) 워크로드가 발생하기 전에 네임스페이스에 더 많은 메시징 단위를 할당할 수 있습니다.
>
>  * **반응형**: 리소스 사용 현황 메트릭을 연구하여 추가 워크로드를 식별하면 증가하는 수요를 반영하기 위해 네임스페이스에 추가 리소스를 할당할 수 있습니다.
>
> Service Bus의 청구 측정 단위는 시간당입니다. 스케일 업의 경우 사용된 시간에 대한 추가 리소스에 대해서만 비용을 지불합니다.
>

## <a name="get-started-with-premium-messaging"></a>프리미엄 메시징 시작

프리미엄 메시징 시작은 간단하며 프로세스는 표준 메시징의 프로세스와 비슷합니다. 먼저 [Azure Portal](https://portal.azure.com)에서 [네임스페이스를 만듭니다](service-bus-create-namespace-portal.md). **가격 책정 계층** 에서 **프리미엄** 을 선택했는지 확인합니다. **전체 가격 책정 세부 정보 보기** 를 클릭하여 각 계층에 대해 자세히 알아봅니다.

![create-premium-namespace][create-premium-namespace]

[Azure Resource Manager 템플릿을 사용하여 프리미엄 네임스페이스](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/)를 만들 수도 있습니다.


## <a name="next-steps"></a>다음 단계

Service Bus 메시지에 대해 자세히 알아보려면 다음 링크를 참조하세요.

- [자동으로 메시징 단위 업데이트](automate-update-messaging-units.md)
- [Azure Service Bus 프리미엄 메시징 소개(블로그 게시물)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure Service Bus 프리미엄 메시징 소개(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
