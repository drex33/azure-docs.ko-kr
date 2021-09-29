---
title: 다중 사이트 및 다중 지역 페더레이션 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs를 사용하여 다중 사이트 및 다중 지역 페더레이션에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/28/2021
ms.author: spelluru
ms.openlocfilehash: 7311854b95250f94aa51bdc954b53743fbfde3a1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217867"
---
# <a name="multi-site-and-multi-region-federation"></a>다중 사이트 및 다중 지역 페더레이션

많은 정교한 솔루션은 동일한 이벤트 스트림을 여러 위치에서 사용할 수 있도록 하거나 이벤트 스트림을 여러 위치에서 수집한 특정 위치로 통합하여 사용해야 합니다. 또한 단일 지역 및 솔루션 내에서 이벤트 스트림을 보강 또는 절감하거나 이벤트 형식 변환을 수행해야 하는 경우가 종종 있습니다.

실제로 이는 솔루션이 여러 지역 및 Event Hubs 네임스페이스에 여러 Event Hubs를 유지 관리한 다음 서로 간에 이벤트를 복제한다는 것을 의미합니다. [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md) 또는 [Apache Kafka](https://kafka.apache.org)와 같은 소스 및 대상과 이벤트를 교환할 수도 있습니다. 

서로 다른 지역에 여러 활성 Event Hubs를 유지하면 콘텐츠가 병합되는 경우 클라이언트가 이들 사이에서 선택하고 전환할 수 있으므로 전체 시스템이 지역 가용성 문제에 대해 더 탄력적입니다.

이 "페더레이션" 챕터에서는 페더레이션 패턴과 서버리스 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 또는 [Azure Functions][1] 런타임을 사용하여 이벤트 흐름 경로에서 이러한 패턴을 실현하는 방법에 대해 설명합니다. 

## <a name="federation-patterns"></a>페더레이션 패턴

서로 다른 Event Hubs 또는 다른 원본과 대상 간에 이벤트를 이동하려는 이유에 대한 많은 잠재적 동기가 있으며 이 섹션에서 가장 중요한 패턴을 열거하고 각 패턴에 대한 자세한 지침에 대한 링크도 제공합니다. 

- [지역 가용성 이벤트에 대한 복원력](#resiliency-against-regional-availability-events)
- [대기 시간 최적화](#latency-optimization)
- [유효성 검사, 감소 및 보강](#validation-reduction-and-enrichment)
- [분석 서비스와의 통합](#integration-with-analytics-services)
- [이벤트 스트림의 통합 및 정규화](#consolidation-and-normalization-of-event-streams)
- [이벤트 스트림 분할 및 라우팅](#splitting-and-routing-of-event-streams)
- [로그 프로젝션](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>지역 가용성 이벤트에 대한 복원력 

![국가별 가용성](media/event-hubs-federation-overview/regional-availability.jpg)

최대 가용성과 안정성이 Event Hubs의 최우선 운영 우선 순위이지만 그럼에도 불구하고 생산자 또는 소비자가 네트워킹 또는 이름 확인 문제로 인해 할당된 "기본" Event Hub와 통신하지 못하도록 방지할 수 있는 방법이 많이 있습니다. 또는 여기서 허브가 실제로 일시적으로 응답하지 않거나 오류를 반환할 수 있습니다. 

이러한 조건은 [재해 복구](event-hubs-geo-dr.md) 상황에서 수행할 수 있는 것처럼 지역 배포를 완전히 포기해야 하는 "재앙"은 아니지만 일부 애플리케이션의 비즈니스 시나리오는 몇 분 또는 몇 초 이하로 지속되는 가용성 이벤트의 영향을 받을 수 있습니다. 

이러한 시나리오를 해결하기 위한 두 가지 기본적인 패턴이 있습니다.

- [복제][4] 패턴은 기본 Event Hub의 콘텐츠를 보조 Event Hubs로 복제하는 것입니다. 따라서 기본 Event Hub는 일반적으로 애플리케이션에서 이벤트를 생성하고 소비하기 위해 사용하고 보조는 기본 Event Hub를 사용할 수 없게 되는 경우 대체 옵션으로 사용됩니다. 복제는 기본에서 보조로 단방향이므로 사용할 수 없는 기본에서 보조로 생산자와 소비자가 모두 전환되면 이전 기본이 새 이벤트를 수신하지 않으므로 더 이상 최신 이벤트가 아닙니다.
  따라서 순수 복제는 단방향 장애 조치(failover) 시나리오에만 적합합니다. 장애 조치(failover)를 수행하면 과거의 기본이 중단되고 다른 대상 지역에 새 보조 Event Hub가 만들어져야 합니다.
- [병합][5] 패턴은 둘 이상의 Event Hubs 콘텐츠를 지속적으로 병합하여 복제 패턴을 확장합니다. 구성표에 포함된 Event Hubs 중 하나로 원래 생성된 각 이벤트는 다른 Event Hubs에 복제됩니다. 이벤트가 복제될 때 복제 대상의 복제 프로세스에서 이후에 무시되도록 주석이 추가됩니다. 병합 패턴을 사용한 결과는 궁극적으로 일관된 방식으로 동일한 이벤트 집합을 포함하는 둘 이상의 Event Hubs입니다. 
  
두 경우 모두 Event Hubs의 내용이 동일하지 않습니다. 하나의 생산자에서 발생한 이벤트와 동일한 파티션 키로 그룹화된 이벤트는 원래 제출된 것과 동일하게 상대적인 순서로 나타나지만 이벤트의 절대 순서는 다를 수 있습니다. 이는 특히 원본 및 대상 Event Hubs의 파티션 수가 다른 시나리오에 해당되며 여기에 설명된 여러 확장 패턴에 적합합니다. [분할기 또는 라우터](#splitting-and-routing-of-event-streams)는 수백 개의 파티션이 있는 훨씬 더 큰 Event Hub의 조각을 가져와 소수의 파티션이 있는 더 작은 Event Hubs로 보낼 수 있으며 제한된 처리 리소스로 하위 집합을 처리하는 데 더 적합합니다. 반대로 [통합](#consolidation-and-normalization-of-event-streams)은 통합된 처리량 및 처리 요구 사항을 대처하기 위해 더 많은 파티션이 있는 하나의 더 큰 Event Hubs로 여러 작은 Event Hubs의 데이터를 보낼 수 있습니다.

이벤트를 함께 유지하기 위한 기준은 파티션 키이며 원래 파티션 ID가 아닙니다. 상대적 순서 및 동일한 범위의 스트림 오프셋에 의존하지 않고 한 Event Hub에서 다음 Event Hub로 장애 조치(failover)하는 방법에 대한 추가 고려 사항은 [복제][4] 패턴 설명에 나와 있습니다.


지침: 
- [복제 패턴][4]
- [패턴 병합][5]

### <a name="latency-optimization"></a>대기 시간 최적화 

![대기 시간 최적화](media/event-hubs-federation-overview/latency-optimization.jpg)  

이벤트 스트림은 생산자에 의해 한 번만 기록되지만 이벤트 소비자가 여러 번 읽을 수 있습니다. 한 지역의 이벤트 스트림을 여러 소비자가 공유하고 다른 지역에 상주하는 분석 처리 중에 반복적으로 액세스해야 하는 시나리오 또는 동시 소비자를 고갈시키는 전체 수요가 있는 시나리오의 경우 왕복 대기 시간을 줄일 수 있도록 분석 프로세서 근처의 이벤트 스트림의 복사본을 배치하는 것이 좋습니다. 

여러 지역에서 원격으로 이벤트를 소비하는 것보다 복제를 선호해야 하는 경우에 대한 좋은 예는 특히 지역이 극도로 멀리 떨어져 있는 곳입니다. 예를 들어 유럽과 호주는 거의 정반대이고 지리적으로 네트워크 지연은 왕복하는 데 250ms를 쉽게 초과할 수 있습니다.
빛 속도는 가속화할 수 없지만 데이터와 상호 작용하기 위해 대기 시간이 긴 왕복 횟수를 줄일 수 있습니다.

지침: 
- [복제 패턴][4]

### <a name="validation-reduction-and-enrichment"></a>유효성 검사, 감소 및 보강

![유효성 검사, 감소, 보강](media/event-hubs-federation-overview/validation-enrichment.jpg)  

사용자 고유의 솔루션 외부에 있는 클라이언트는 이벤트 스트림을 Event Hub에 제출할 수 있습니다. 이러한 이벤트 스트림은 지정된 스키마와의 호환성을 위해 외부에서 전송된 이벤트를 검사하고 비규격 이벤트를 삭제해야 할 수 있습니다. 

측정된 대역폭을 사용하는 많은 "사물 인터넷" 시나리오의 경우처럼 클라이언트의 대역폭이 극도로 제한되는 시나리오 또는 이벤트가 패킷 크기가 제한된 비 IP 네트워크를 통해 원래 전송되는 경우 다운스트림 이벤트 프로세서에서 사용할 수 있도록 컨텍스트를 추가하기 위해 이벤트를 참조 데이터로 보강해야 할 수 있습니다.

다른 경우에 특히 스트림을 통합하는 경우에는 일부 세부 정보를 생략하여 이벤트 데이터를 복잡성 또는 단순한 크기로 줄여야 할 수 있습니다.

이러한 작업은 복제, 통합 또는 병합 흐름의 일부로 발생할 수 있습니다. 

지침: 
- [패턴 편집기][6]

### <a name="integration-with-analytics-services"></a>분석 서비스와 통합

![분석 서비스와 통합](media/event-hubs-federation-overview/integration.jpg)

Azure Stream Analytics 또는 Azure Synapse와 같은 Azure의 클라우드 네이티브 분석 서비스 중 여러 서비스는 Azure Event Hubs에서 제공되는 스트리밍되거나 사전에 일괄 처리된 데이터에서 가장 잘 작동하며 Azure Event Hubs는 Apache Samza, Apache Flink, Apache Spark 및 Apache Storm과 같은 여러 오픈 소스 분석 패키지와 통합할 수도 있습니다. 

솔루션에서 주로 Service Bus 또는 Event Grid를 사용하는 경우 이벤트가 이러한 분석 시스템에 쉽게 액세스할 수 있도록 하고 Event Hub로 보내는 경우 Event Hubs 캡처를 사용하여 보관합니다. Event Grid는 기본적으로 [Event Hubs 통합](../event-grid/handler-event-hubs.md)을 통해 이를 수행할 수 있습니다. Service Bus의 경우 [Service Bus 복제 지침](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)을 따릅니다.

Azure Stream Analytics는 [Event Hubs와 직접 통합](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)됩니다.

지침: 
- [복제 패턴][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>이벤트 스트림의 통합 및 정규화

![이벤트 스트림의 통합 및 정규화](media/event-hubs-federation-overview/consolidation.jpg)

글로벌 솔루션은 자체 분석 기능을 포함하여 대체로 독립적인 지역 공간으로 구성되는 경우가 많지만 초 지역 및 글로벌 분석 관점에는 통합 관점이 필요하므로 동일한 이벤트 스트림의 중앙 통합이 각 지역 공간에서 로컬 관점으로 평가됩니다. 

정규화는 두 개 이상의 수신 이벤트 스트림이 동일한 종류의 이벤트를 전달하지만 구조나 인코딩이 다르고 이벤트가 대부분 사용되기 전에 먼저 트랜스코딩되거나 변환되는 통합 시나리오 버전입니다. 

정규화에는 엔드투엔드 암호화된 페이로드를 해독하고 다운스트림 소비자 대상을 위해 다른 키와 알고리즘으로 다시 암호화하는 것과 같은 암호화 작업도 포함될 수 있습니다. 

지침: 
- [패턴 병합][5]
- [패턴 편집기][6]

### <a name="splitting-and-routing-of-event-streams"></a>이벤트 스트림 분할 및 라우팅

![이벤트 스트림 분할 및 라우팅](media/event-hubs-federation-overview/splitting.jpg)

Azure Event Hubs는 수집된 이벤트의 수신 토렌트가 Azure Service Bus 또는 Azure Event Grid의 용량을 훨씬 초과하는 "게시-구독" 스타일 시나리오에서 가끔 사용되며 기본 발행-구독 필터링 및 배포 기능이 있는 두 솔루션 모두 이 패턴을 위해 자주 사용됩니다. 

진정한 "게시-구독" 기능은 구독자가 원하는 이벤트를 선택하도록 남겨두지만 분할 패턴은 미리 결정된 배포 모델에 따라 생산자가 이벤트를 파티션에 매핑한 다음 지정된 소비자가 "자신의" 파티션에서 독점적으로 가져옵니다. Event Hub가 전체 트래픽을 버퍼링하면 원래 처리량 볼륨의 일부를 나타내는 특정 파티션의 콘텐츠가 신뢰할 수 있는 트랜잭션 경쟁 소비자의 소비를 위해 큐로 복제될 수 있습니다.

Event Hubs가 주로 지역 내의 애플리케이션 내에서 이벤트를 이동하는 데 사용되는 여러 시나리오에는 단일 파티션에서 선택된 이벤트가 다른 곳에서 사용할 수 있도록 설정해야 하는 경우도 있습니다. 이 시나리오는 분할 시나리오와 유사하지만 Event Hub에 도착하는 모든 메시지를 고려하고 후속 라우팅을 위해 몇 개만 선택하고 이벤트 메타데이터 또는 콘텐츠별로 라우팅 대상을 구분하는 확장 가능한 라우터를 사용할 수 있습니다. 

지침:
- [라우팅 패턴][7]

### <a name="log-projections"></a>로그 프로젝션 

![로그 프로젝션](media/event-hubs-federation-overview/log-projection.jpg)

일부 시나리오에서는 이벤트의 하위 스트림용으로 전송된 최신 값에 액세스하고 일반적으로 파티션 키로 구분하는 것이 좋습니다. Apache Kafka에서 이는 종종 특정 키로 레이블이 지정된 최신 이벤트를 제외한 모든 항목을 삭제하는 "로그 압축"을 사용하여 수행됩니다. 로그 압축 방식에는 세 가지 복합적인 단점이 있습니다. 

- 압축을 사용하려면 로그를 지속적으로 다시 구성해야 하며 이는 추가 전용 워크로드에 최적화된 브로커에 대해 과도하게 부담이 큰 작업입니다. 
- 압축은 파괴적이며 동일한 스트림에 대해 압축 및 비압축 관점을 허용하지 않습니다.
- 압축된 스트림에는 여전히 순차적인 액세스 모델이 있습니다. 즉 로그에서 원하는 값을 찾으려면 최악의 경우 전체 로그를 읽어야 합니다. 이는 일반적으로 여기에 제시된 정확한 패턴을 구현하는 최적화로 이어집니다. 로그 내용을 데이터베이스 또는 캐시에 프로젝션을 만듭니다. 

궁극적으로 압축된 로그는 키-값 저장소이므로 이러한 저장소에 대해 가능한 최악의 구현 옵션입니다. 조회 및 쿼리를 위해 올바른 키-값 저장소 또는 다른 데이터베이스에 대한 로그의 영구 프로젝션을 만들고 사용하는 것이 훨씬 더 효율적입니다. 

이벤트는 변경 불가능하고 순서는 항상 로그에 보존되기 때문에 키-값 저장소에 대한 로그의 모든 프로젝션은 동일한 이벤트 범위에 대해 항상 동일합니다. 즉, 업데이트된 프로젝션은 항상 신뢰할 수 있는 보기를 제공하며 빌드된 후에 로그 콘텐츠에서 다시 빌드해야 하는 이유가 없습니다. 

지침:
- [로그 프로젝션][8]

## <a name="replication-application-technologies"></a>복제 애플리케이션 기술

위의 패턴을 구현하려면 구성 및 실행하려는 복제 작업을 위한 확장 가능하고 안정적인 실행 환경이 필요합니다. Azure에서 이러한 작업에 가장 적합한 런타임 환경은 상태 비저장 작업인 경우 상태 저장 스트림 복제 작업을 위한 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)와 상태 비저장 복제 작업을 위한 [Azure Functions](../azure-functions/functions-overview.md)입니다.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Azure Stream Analytics의 상태 저장 복제 애플리케이션

이벤트 간의 관계를 고려하고, 복합 이벤트를 만들고, 이벤트를 강화하거나 이벤트를 줄이고, 데이터 집계를 만들고, 이벤트 페이로드를 변환해야 하는 상태 저장 복제 애플리케이션의 경우 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)가 최상의 구현 옵션입니다.

Azure Stream Analytics에서는 [입력](../stream-analytics/stream-analytics-add-inputs.md) 및 [출력](../stream-analytics/stream-analytics-define-outputs.md)을 통합하는 [작업을 만들고](../stream-analytics/stream-analytics-quick-create-portal.md) 결과를 산출하는 [쿼리](/stream-analytics-query/stream-analytics-query-language-reference)를 통해 입력 데이터를 통합합니다. 그런 다음 출력에서 ​​사용할 수 있습니다.

[SQL 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference)를 기반으로 하는 쿼리는 일정 기간 동안 스트리밍 데이터를 쉽게 필터링, 정렬, 집계 및 조인하는 데 사용될 수 있습니다. [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) 및 [C# UDF(사용자 정의 기능)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md)를 사용하여 이 SQL 언어를 확장할 수도 있습니다. 간단한 언어 구성 및/또는 구성을 통해 집계 작업을 수행할 때 이벤트 순서 지정 옵션 및 시간 범위 기간을 손쉽게 조정할 수 있습니다.

각 작업에는 변환된 데이터에 대한 하나 또는 여러 개의 출력이 있으며, 분석한 정보에 대한 응답으로 수행되는 작업을 제어 할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- Azure Functions, Service Bus 항목 또는 큐와 같은 서비스에 데이터를 보내 통신을 트리거하거나 워크플로 다운스트림을 사용자 지정합니다.
- 실시간 대시보드를 위해 Power BI 대시보드로 데이터를 전송합니다.
- 다른 Azure Storage 서비스(예: Azure Data Lake, Azure Synapse Analytics 등)에 데이터를 저장하여 일괄 분석을 수행하거나 인덱싱된 매우 큰 기록 데이터 풀을 기반으로 기계 학습 모델을 학습합니다.
- 데이터베이스([SQL 데이터베이스](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md))에 프로젝션("구체화된 뷰"라고도 함)을 저장합니다.

### <a name="stateless-replication-applications-in-azure-functions"></a>Azure Functions의 상태 비저장 복제 애플리케이션

페이로드를 고려하지 않고 이벤트를 전달하거나 이벤트의 관계(상대 순서 제외)를 고려하지 않고 단독으로 처리하려는 상태 비저장 복제 작업에는 엄청난 유연성을 제공하는 Azure Functions를 사용할 수 있습니다.

Azure Functions에는 [Azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md), [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md)에 대한 미리 빌드되고 확장 가능한 트리거 및 출력 바인딩이 있을 뿐만 아니라 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 및 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)용 사용자 지정 확장이 있습니다. 대부분의 트리거는 문서화된 메트릭을 기반으로 동시에 실행되는 인스턴스 수를 늘리거나 줄임으로써 처리량 요구 사항에 동적으로 적응합니다. 

로그 프로젝션을 빌드하기 위해 Azure Functions는 [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) 및 [Azure Table Storage](../azure-functions/functions-bindings-storage-table-output.md)에 대한 출력 바인딩을 지원합니다.

Azure Functions는 [Azure 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에서 실행될 수 있으며 이를 통해 [Azure Key Vault](../key-vault/general/overview.md) 내에서 엄격하게 액세스가 제어되는 스토리지에 자격 증명의 구성 값을 저장할 수 있습니다.

또한 Azure Functions를 사용하면 복제 작업이 모든 Azure 메시징 서비스에 대해 Azure 가상 네트워크 및 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)와 직접 통합될 수 있으며 [Azure Monitor](../azure-monitor/overview.md)와 쉽게 통합됩니다.

Azure Functions 사용 계획을 통해 복제에 사용할 수 있는 메시지가 없는 동안에도 미리 빌드된 트리거를 0으로 축소할 수 있습니다. 즉, 구성을 다시 확장할 준비를 유지하는 데 비용이 들지 않습니다. 사용 계획의 주요 단점은 이 상태에서 "깨어난" 복제 작업에 대한 대기 시간이 인프라가 계속 실행되는 호스팅 계획보다 훨씬 길다는 것입니다.  

이 모든 것과 달리 Apache Kafka의 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker)와 같은 메시징 및 이벤트를 위해 가장 일반적인 복제 엔진은 호스팅 환경을 제공하고 복제 엔진을 직접 확장해야 합니다. 여기에는 보안 및 네트워킹 기능을 구성 및 통합하고 데이터를 모니터링하는 흐름을 촉진하는 것이 포함되지만 사용자 지정 복제 작업을 흐름에 삽입할 기회가 여전히 없습니다. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Azure Functions 및 Azure Stream Analytics 중에서 선택

ASA(Azure Stream Analytics)는 이벤트를 복제하는 동안 이벤트 페이로드를 처리해야 할 때마다 가장 좋은 옵션입니다. ASA는 이벤트를 하나씩 복사하거나 이벤트 스트림의 정보를 전달하기 전에 압축하는 집계를 만들 수 있습니다. 이러한 데이터를 스트림으로 가져올 필요 없이 Azure Blob Storage 또는 Azure SQL Database에 보관된 [참조 데이터를 보완](../stream-analytics/stream-analytics-use-reference-data.md)할 수 있습니다.

ASA를 통해 대규모로 확장된 데이터베이스에서 영구적이고 구체화된 스트림 뷰를 손쉽게 만들 수 있습니다. 이는 Apache Kafka의 투박한 "로그 압축" 모델과 Kafka Streams의 불안정한 클라이언트 측 테이블 프로젝션에 대한 훨씬 우수한 접근 방식입니다. 

ASA는 [CSV, JSON 및 Apache Avro 형식](../stream-analytics/stream-analytics-parsing-json.md)으로 인코딩된 페이로드가 있는 이벤트를 즉시 처리할 수 ​​있으며 다른 형식에 대해서는 [사용자 지정 역직렬화기](../stream-analytics/custom-deserializer.md)를 플러그 인할 수 있습니다.

모든 복제 작업에서 페이로드를 건드리지 않고 이벤트 스트림을 "있는 그대로" 복사하거나 라우터를 구현해야 하는 경우 암호화 작업을 수행하고 페이로드의 인코딩을 변경합니다. 그렇지 않고 데이터 스트림 콘텐츠에 대한 완전한 제어가 필요한 경우에는 Azure Functions가 최상의 옵션입니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 다양한 페더레이션 패턴을 탐색하고 Azure에서 이벤트 및 메시징 복제 런타임으로서 Azure Functions의 역할을 알아보았습니다.

다음으로 Azure Stream Analytics 또는 Azure Functions를 사용하여 복제기 애플리케이션을 설정하는 방법과 Event Hubs와 다양한 기타 이벤트 및 메시징 시스템 간에 이벤트 흐름을 복제하는 방법을 확인할 수 있습니다.

- [이벤트 복제 작업 패턴][10]
- [Azure Stream Analytics를 사용하여 데이터 처리][9]
- [Azure Functions의 이벤트 복제기 애플리케이션][1]
- [Event Hubs 간 이벤트 복제][2]
- [Azure Service Bus에 이벤트 복제][3]
- [Event Hubs를 사용하는 Apache Kafka MirrorMaker 사용][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md