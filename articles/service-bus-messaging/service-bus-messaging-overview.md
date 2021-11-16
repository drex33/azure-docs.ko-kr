---
title: Azure Service Bus 메시지 개요 | Microsoft Docs
description: 이 문서에서는 완전 관리형 엔터프라이즈 통합 메시지 브로커인 Azure Service Bus에 대한 간략한 개요를 제공합니다.
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: 8aa59315dcb196cf7f5c5b107e8cf575a5eb4f86
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373122"
---
# <a name="what-is-azure-service-bus"></a>Azure Service Bus란?
Azure Service Bus는 메시지 큐와 게시-구독 토픽이 있는 완전 관리형 엔터프라이즈 메시지 브로커입니다. Service Bus는 애플리케이션과 서비스를 서로 분리하는 데 사용되며, 다음과 같은 이점을 제공합니다.

- 경쟁하는 작업자 간에 작업 부하 분산
- 서비스 및 애플리케이션 경계에서 데이터 및 제어를 안전하게 라우팅하고 전송
- 높은 수준의 안정성이 필요한 트랜잭션 작업 조정 

> [!NOTE]
> Azure 메시징 서비스를 비교하려면 [Azure 메시징 서비스(Event Grid, Event Hubs 및 Service Bus) 중에서 선택](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)을 참조하세요. 

## <a name="overview"></a>개요
데이터는 **메시지** 를 사용하여 서로 다른 애플리케이션 및 서비스간에 전송됩니다. 메시지는 메타데이터로 데코레이팅된 컨테이너이며 데이터를 포함합니다. 다음과 같은 일반적인 형식으로 인코딩된 정형 데이터를 포함하여 모든 종류의 정보는 데이터가 될 수 있습니다: JSON, XML, Apache Avro, 일반 텍스트 등.

몇 가지 일반적인 메시징 시나리오는 다음과 같습니다.

* **메시징** 주문, 저널 판매 및 구입 또는 인벤토리 이동과 같은 비즈니스 데이터를 전송합니다.
* **애플리케이션 분리**. 애플리케이션 및 서비스의 안정성과 확장성을 개선합니다. 생산자와 소비자가 반드시 온라인 상태이거나 동시에 자리에 있지 않아도 됩니다. [부하가 평준화](/azure/architecture/patterns/queue-based-load-leveling)되므로 트래픽 급증으로 인해 서비스 요금이 과도하게 부과되는 일이 없습니다. 
* **부하 분산**. 여러 [경쟁하는 소비자](/azure/architecture/patterns/competing-consumers)가 한 큐에서 동시에 읽을 수 있으며, 각 소비자는 특정 메시지에 대한 배타적 소유권을 안전하게 획득합니다. 
* **토픽 및 구독**. 사용 1: [게시자와 구독자](/azure/architecture/patterns/publisher-subscriber) 간에 *n* 관계가 있으므로, 구독자는 게시된 메시지 스트림에서 특정 메시지를 선택할 수 있습니다.
* **트랜잭션**. 원자성 트랜잭션의 범위에서 여러 작업을 수행할 수 있습니다. 예를 들어 트랜잭션 범위에서 다음 작업을 수행할 수 있습니다.  

    1. 한 큐에서 메시지를 가져옵니다.
    2. 처리 결과를 하나 이상의 서로 다른 큐에 게시합니다.
    3. 원래 큐의 입력 메시지를 이동합니다. 
    
    입력 메시지가 성공적으로 확인되는 경우를 포함하여 성공할 때만 다운스트림 소비자에게 결과가 표시되므로, 일회성 처리 의미 체계를 허용합니다. 이 트랜잭션 모델은 더 큰 솔루션 컨텍스트에서 [보정 트랜잭션](/azure/architecture/patterns/compensating-transaction) 패턴을 구현하기 위한 강력한 기반입니다. 
* **메시지 세션**. 엄격한 메시지 정렬 또는 메시지 지연이 필요한 워크플로 및 멀티플렉싱 전송의 대규모 조정을 구현합니다.

Apache ActiveMQ와 같은 다른 메시지 broker에 익숙한 분들에게는 Service Bus의 개념이 알고 있는 지식과 비슷할 것입니다. Service Bus는 PaaS(platform-as-a-service) 제품이므로 다음 작업에 대해 신경 쓸 필요가 없다는 점이 결정적인 차이점입니다. Azure가 이러한 작업을 알아서 처리합니다. 

- 하드웨어 오류 해결 
- 운영 체제 또는 제품 패치 유지
- 로그 배치 및 디스크 공간 관리
- 백업 처리
- 예약 머신으로 장애 조치(failover)

## <a name="concepts"></a>개념
이 섹션에서는 Service Bus의 기본 개념에 대해 설명합니다.

### <a name="queues"></a>큐
메시지는 **큐** 간에서 전송 및 수신됩니다. 큐는 수신 애플리케이션이 수신 및 처리할 수 있을 때까지 메시지를 저장합니다.

![큐](./media/service-bus-messaging-overview/about-service-bus-queue.png)

큐의 메시지는 도착 시 순서가 지정되고 타임스탬프가 지정됩니다. broker의 승인을 받은 메시지는 항상 삼중 중복 스토리지에 안전하게 보관되며, 네임스페이스가 영역을 사용하도록 설정되면 가용성 영역으로 확장됩니다. Service Bus는 클라이언트에 수락된 것으로 보고된 메시지를 절대로 메모리 또는 휘발성 스토리지에 남겨 두지 않습니다.

메시지는 요청 시에만 메시지를 전송하는 **끌어오기** 모드에서 전송됩니다. 다른 클라우드 큐의 사용 중-폴링 모델과 달리, 끌어오기 작업은 오래 걸릴 수 있으며 메시지를 사용할 수 있게 되면 완료됩니다. 

> [!NOTE]
> Storage 큐와 Service Bus 큐를 비교하려면 [Storage 큐 및 Service Bus 큐 - 비교 및 대조](service-bus-azure-and-service-bus-queues-compared-contrasted.md)를 참조하세요.

### <a name="topics"></a>토픽

**토픽** 을 사용하여 메시지를 보내고 받을 수도 있습니다. 큐는 종종 지점 간 통신에 사용되지만 토픽은 게시/구독 시나리오에서 유용합니다.

![항목](./media/service-bus-messaging-overview/about-service-bus-topic.png)

토픽에는 여러 개의 독립 구독이 있을 수 있습니다. 이러한 독립 구독은 토픽에 연결되며, 그렇지 않으면 받는 사람 쪽의 큐와 정확히 같은 방식으로 작동합니다. 토픽에 대한 구독자는 해당 토픽으로 전송된 각 메시지의 복사본을 수신할 수 있습니다. 구독은 명명된 엔터티입니다. 구독은 기본적으로 지속되지만, 만료되면 자동으로 삭제되도록 구성할 수 있습니다. JMS(Java Message Service) API를 통해 Service Bus Premium을 사용하면 연결 기간 동안 존재하는 휘발성 구독을 만들 수 있습니다.

구독에 대한 규칙을 정의할 수 있습니다. 구독 규칙에는 구독으로 복사되는 메시지의 조건을 정의하는 **필터** 와 메시지 메타데이터를 수정할 수 있는 선택적 **작업** 이 포함되어 있습니다. 자세한 내용은 [토픽 필터 및 작업](topic-filters.md)을 참조하세요. 이 기능은 다음과 같은 시나리오에서 유용합니다.

- 토픽에 전송되는 메시지 중 일부는 구독에서 수신하지 않으려 합니다.
- 메시지가 구독을 통과할 때 추가 메타데이터로 메시지를 표시하려 합니다.

> [!NOTE]
> 큐 및 토픽에 대한 자세한 내용은 [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)을 참조하세요.


### <a name="namespaces"></a>네임스페이스
네임스페이스는 모든 메시징 구성 요소(큐 및 토픽)의 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스에 있을 수 있으며 네임스페이스는 종종 애플리케이션 컨테이너로 사용됩니다. 

네임스페이스는 다른 broker 용어로 서버와 비교할 수 있지만 그 개념은 직접적으로 일치하지 않습니다. Service Bus 네임스페이스는 수십 개의 전체 활성 가상 머신으로 구성된 대용량 클러스터의 고유한 용량 조각입니다. 필요에 따라 세 개의 [Azure 가용성 영역](../availability-zones/az-overview.md)으로 확장할 수 있습니다. 따라서 메시지 broker를 대규모로 실행할 때 얻게 되는 가용성 및 견고함이라는 이점을 모두 누릴 수 있습니다. 그리고 기본 복잡성에 대해 걱정할 필요가 없습니다. Service Bus는 서버리스 메시징입니다.

## <a name="advanced-features"></a>고급 기능

또한 Service Bus에는 보다 복잡한 메시징 문제를 해결할 수 있는 고급 기능이 있습니다. 다음 섹션에서는 이러한 주요 기능을 설명합니다.

### <a name="message-sessions"></a>메시지 세션

Service Bus에서 FIFO(first-in, first-out) 보장을 실현하려면 세션을 사용합니다. [메시지 세션](message-sessions.md)을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. 

### <a name="auto-forwarding"></a>자동 전달

[자동 전달](service-bus-auto-forwarding.md) 기능을 통해 동일한 네임스페이스의 일부인 다른 큐 또는 토픽에 큐 또는 구독을 연결할 수 있습니다. 자동 전달을 사용하도록 설정하면 Service Bus가 자동으로 첫 번째 큐 또는 구독(원본)에 있는 메시지를 제거하고 두 번째 큐 또는 토픽(대상)에 넣습니다.

### <a name="dead-lettering"></a>배달 못 한 편지

Service Bus는 수신자에게 배달할 수 없는 메시지 또는 처리하지 못한 메시지를 보관하도록 [DLQ](service-bus-dead-letter-queues.md)(배달 못 한 편지 큐)를 지원합니다. 그런 다음, DLQ에서 메시지를 제거하고 검사할 수 있습니다.

### <a name="scheduled-delivery"></a>예약 배달

[지연된 처리를 위해](message-sequencing.md#scheduled-messages) 큐 또는 토픽에 메시지를 제출할 수 있습니다. 예를 들어 시스템에서 특정 시간에 처리하는 데 사용할 수 있도록 작업을 예약합니다.

### <a name="message-deferral"></a>메시지 지연

큐 또는 구독 클라이언트에서 처리하려고 하지만 애플리케이션 내의 특수한 상황으로 인해 현재 처리가 가능하지 않은 메시지를 받는 경우 엔터티에서 [메시지의 검색을 나중의 시점으로 연기](message-deferral.md)할 수 있습니다. 메시지는 큐나 구독에 남아 있지만 따로 분리됩니다.

### <a name="batching"></a>일괄 처리

[클라이언트 쪽 일괄 처리](service-bus-performance-improvements.md#client-side-batching)를 통해 큐 또는 토픽 클라이언트가 일정 시간 동안 메시지 전송을 연기할 수 있습니다. 클라이언트에서 이 기간 동안 더 많은 메시지를 보내면 메시지를 단일 배치로 전송합니다. 

### <a name="transactions"></a>의

[트랜잭션](service-bus-transactions.md) 그룹은 두 개 이상의 작업을 실행 범위로 그룹화합니다. Service Bus는 트랜잭션 범위 내에서 단일 메시징 엔터티(큐, 토픽, 구독)에 대한 그룹화 작업을 지원합니다.

### <a name="filtering-and-actions"></a>필터링 및 작업

구독자는 토픽에서 수신할 메시지를 정의할 수 있습니다. 이러한 메시지는 하나 이상의 [명명된 구독 규칙](topic-filters.md)의 형태로 지정됩니다. 일치하는 각 규칙 조건에 대해 구독은 메시지의 복사본을 생성하며 일치하는 규칙마다 다르게 주석을 달 수 있습니다.

### <a name="auto-delete-on-idle"></a>유휴 상태에서 자동 삭제

[유휴 상태에서 자동 삭제](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle)를 통해 큐가 자동으로 삭제된 후 유휴 간격을 지정할 수 있습니다. 최소 기간은 5분입니다.

### <a name="duplicate-detection"></a>중복 검색

클라이언트가 전송 작업의 결과에 대한 의문을 갖게 되는 오류가 발생하는 경우 [중복 검색](duplicate-detection.md)은 보낸 사람이 동일한 메시지를 다시 보낼 수 있도록 하고 큐 또는 토픽에서 중복된 복사본을 삭제하여 이러한 상황에 대한 의문을 해소합니다.

### <a name="shared-access-signature-sas-role-based-access-control-and-managed-identities"></a>SAS(공유 액세스 서명), 역할 기반 액세스 제어 및 관리 ID

Service Bus는 [SAS(공유 액세스 서명)](service-bus-sas.md), [RBAC(역할 기반 액세스 제어)](service-bus-role-based-access-control.md) 및 [Azure 리소스에 대한 관리 ID](service-bus-managed-service-identity.md) 같은 보안 프로토콜을 지원합니다.

### <a name="geo-disaster-recovery"></a>지리적 재해 복구

Azure 지역 또는 데이터 센터에서 가동 중단이 발생하는 경우 [지리적 재해 복구](service-bus-geo-dr.md)를 통해 데이터 처리는 다른 지역 또는 데이터 센터에서 작업을 계속할 수 있습니다.

### <a name="security"></a>보안

Service Bus는 표준 [AMQP(고급 메시지 큐 프로토콜) 1.0](service-bus-amqp-overview.md) 및 [HTTP/REST](/rest/api/servicebus/) 프로토콜을 지원합니다.

> [!NOTE]
> 이러한 기능에 대한 자세한 내용은 [Azure Service Bus의 고급 기능](advanced-features-overview.md)을 참조하세요.

## <a name="compliance-with-standards-and-protocols"></a>표준 및 프로토콜 규정 준수

Service Bus의 기본 유선 프로토콜은 오픈 ISO/IEC 표준인 [AMQP(Advanced Messaging Queueing Protocol) 1.0](service-bus-amqp-overview.md)입니다. 따라서 고객은 Service Bus와 ActiveMQ 또는 RabbitMQ 같은 온-프레미스 broker에 대해 작동하는 애플리케이션을 작성할 수 있습니다. [AMQP 프로토콜 가이드](service-bus-amqp-protocol-guide.md)는 이러한 추상화를 구현하려 할 때 도움이 되는 자세한 정보를 제공합니다.

[Service Bus Premium](service-bus-premium-messaging.md)은 Java/Jakarta EE [JMS(Java Message Service) 2.0](how-to-use-java-message-service-20.md) API와 완벽하게 호환됩니다. 그리고 Service Bus Standard는 큐에 중점을 둔 JMS 1.1 하위 세트를 지원합니다. JMS는 메시지 broker에 사용되는 일반적인 추상화이며 인기 있는 Spring 프레임워크를 포함하여 여러 애플리케이션 및 프레임워크와 통합됩니다. 다른 broker에서 Azure Service Bus로 전환하려면 큐 및 토픽의 토폴로지를 다시 만들고 클라이언트 공급자 종속성 및 구성을 변경하기만 하면 됩니다. 예제는 [ActiveMQ 마이그레이션 가이드](migrate-jms-activemq-to-servicebus.md)를 참조하세요.

## <a name="client-libraries"></a>클라이언트 라이브러리

완전히 지원되는 Service Bus 클라이언트 라이브러리는 Azure SDK를 통해 사용할 수 있습니다.

- [.NET용 Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java용 Azure Service Bus 라이브러리](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0용 Azure Service Bus 공급자](how-to-use-java-message-service-20.md)
- [JavaScript 및 TypeScript용 Azure Service Bus 모듈](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python용 Azure Service Bus 라이브러리](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus의 기본 프로토콜은 AMQP 1.0](service-bus-amqp-overview.md)이며 모든 AMQP 1.0 규격 프로토콜 클라이언트에서 사용할 수 있습니다. 여러 오픈 소스 AMQP 클라이언트에서 Service Bus 상호 운용성을 명시적으로 보여주는 샘플을 제공합니다. [AMQP 1.0 프로토콜 가이드](service-bus-amqp-protocol-guide.md)를 검토하여 AMQP 1.0 클라이언트에서 직접 Service Bus 기능을 사용하는 방법을 알아보세요.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>통합

Service Bus는 다음과 같은 여러 Microsoft 및 Azure 서비스와 완벽하게 통합됩니다.

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>다음 단계

Service Bus 메시징 사용을 시작하려면 다음 문서를 참조하세요.

- [Azure 메시징 서비스(Event Grid, Event Hubs 및 Service Bus) 중에서 선택](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
- [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- 빠른 시작: [.NET,](service-bus-dotnet-get-started-with-queues.md)[Java](service-bus-java-how-to-use-queues.md) 또는 [JMS](service-bus-java-how-to-use-jms-api-amqp.md)
- [Service Bus 가격](https://azure.microsoft.com/pricing/details/service-bus/) 
- [프리미엄 메시징](service-bus-premium-messaging.md)