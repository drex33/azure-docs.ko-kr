---
title: 기능 개요 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 기능 및 용어에 대한 정보를 제공합니다.
ms.topic: overview
ms.date: 08/27/2021
ms.openlocfilehash: b06ce04a7b2fd4fbb55cbe1b3ac8c2510305e781
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226326"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure Event Hubs의 기능 및 용어

Azure Event Hubs는 확장 가능한 처리 서비스로 대량의 이벤트 및 데이터를 수집하여 처리하며, 대기 시간이 낮고 안정성이 우수합니다. 개요는 [Event Hubs란?](./event-hubs-about.md)을 참조하세요.

이 문서는 [개요](./event-hubs-about.md) 문서의 정보를 기반으로 하며, Event Hubs 구성 요소 및 기능에 대한 기술 정보와 구현 정보를 제공합니다.

> [!TIP]
> [**Apache Kafka** 클라이언트에 대한 프로토콜 지원](event-hubs-for-kafka-ecosystem-overview.md)(버전 1.0 이상)은 클라이언트에 Apache Kafka를 사용하도록 빌드된 애플리케이션에서 Event Hubs를 사용하도록 지원하는 네트워크 엔드포인트를 제공합니다. 대부분의 기존 Kafka 애플리케이션은 Kafka 클러스터 부트스트랩 서버 대신 Event Hub 네임스페이스를 가리키도록 다시 구성할 수 있습니다. 
>
>비용, 운영 활동, 안정성의 관점에서 Azure Event Hubs는 고유한 Kafka 및 Zookeeper 클러스터 배포와 운영 및 Azure에 기본 제공되지 않는 Kafka-as-a-Service 제품 대신 사용할 수 있습니다. 
>
> Apache Kafka 브로커와 동일한 핵심 기능을 제공하는 것 외에도, [Event Hubs 캡처](event-hubs-capture-overview.md), 자동 스케일링 및 분산, 재해 복구, 비용 중립적인 가용성 영역 지원, 유연하고 안전한 네트워크 통합, 방화벽 AMQP-over-WebSockets 프로토콜을 포함한 다중 프로토콜 지원을 통해 자동 일괄 처리 및 보관과 같은 Azure Event Hub 기능에 액세스할 수 있습니다.


## <a name="namespace"></a>네임스페이스
Event Hubs 네임스페이스는 이벤트 허브(또는 Kafka 구문 분석의 토픽)에 대한 관리 컨테이너입니다. DNS 통합 네트워크 엔드포인트와, [IP 필터링](event-hubs-ip-filtering.md), [가상 네트워크 서비스 엔드포인트](event-hubs-service-endpoints.md) 및 [프라이빗 링크](private-link-service.md)와 같은 다양한 액세스 제어 및 네트워크 통합 관리 기능을 제공합니다.

:::image type="content" source="./media/event-hubs-features/namespace.png" alt-text="Event Hubs 네임스페이스를 보여주는 이미지":::

## <a name="event-publishers"></a>이벤트 게시자

이벤트 허브로 데이터를 전송하는 모든 엔터티는 *이벤트 게시자*(*이벤트 생산자* 에서 동의어처럼 사용됨)입니다. 이벤트 게시자는 HTTPS, AMQP 1.0 또는 Kafka 프로토콜을 사용하여 이벤트를 게시할 수 있습니다. 이벤트 게시자는 OAuth2에서 발급한 JWT 토큰 또는 Event Hub 관련 SAS(공유 액세스 서명) 토큰 획득 게시 액세스를 사용하여 Azure Active Directory 기반 권한 부여를 사용합니다.

### <a name="publishing-an-event"></a>이벤트 게시

AMQP 1.0, Kafka 프로토콜 또는 HTTPS를 통해 이벤트를 게시할 수 있습니다. Event Hubs 서비스는 이벤트 허브에 이벤트를 게시하기 위한 [REST API](/rest/api/eventhub/) and [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md) 및 [Go](event-hubs-go-get-started-send.md) 클라이언트 라이브러리를 제공합니다. 다른 런타임 및 플랫폼의 경우, [Apache Qpid](https://qpid.apache.org/)와 같은 모든 AMQP 1.0 클라이언트를 사용할 수 있습니다. 

AMQP 또는 HTTPS 사용 선택은 사용량 시나리오에 해당됩니다. 전송 수준 보안(TLS) 또는 SSL/TLS 외에 AMQP는 영구 양방향 소켓을 설정해야 합니다. AMQP는 세션을 초기화할 때 네트워크 비용이 더 많이 듭니다. 그러나 HTTPS를 사용하려면 모든 요청에 대한 추가 TLS 오버헤드가 필요합니다. AMQP는 자주 사용하는 게시자의 경우 성능이 훨씬 높고 비동기 게시 코드와 함께 사용하는 경우 대기 시간이 훨씬 짧아질 수 있습니다.

이벤트를 개별적으로 게시하거나 일괄처리할 수 있습니다. 단일 게시는 단일 이벤트 또는 일괄 처리인지에 관계없이 1MB로 제한됩니다. 이 임계값보다 큰 게시 이벤트는 거부됩니다. 

Event Hubs 처리량은 파티션 및 처리량 단위 할당을 사용하여 스케일링됩니다(아래 참조). 게시자는 이벤트 허브에 대해 선택한 특정 분할 모델을 인식하지 못하고 동일한 파티션에 관련 이벤트를 일관되게 할당하는 데 사용되는 *파티션 키* 만 지정하는 것이 가장 좋습니다.

![파티션 키](./media/event-hubs-features/partition_keys.png)

Event Hubs는 파티션 키 값을 공유하는 모든 이벤트가 함께 저장되고 도착하는 순서대로 배달되도록 합니다. 파티션 키가 게시자 정책과 함께 사용되는 경우 게시자 ID와 파티션 키 값이 일치해야 합니다. 그렇지 않으면 오류가 발생합니다.

### <a name="event-retention"></a>이벤트 보존

게시된 이벤트는 구성 가능한 시간 기반 보존 정책을 기반으로 하여 이벤트 허브에서 제거됩니다. 다음은 몇 가지 중요한 사항입니다.

- **기본값** 및 가능한 **가장 짧은** 보존 기간은 **1일(24시간)** 입니다.
- Event Hubs **Standard** 의 경우 최대 보존 기간은 **7일** 입니다. 
- Event Hubs  **프리미엄** 및 **전용** 의 경우 최대 보존 기간은 **90일** 입니다.
- 보존 기간을 변경하는 경우 이벤트 허브에 이미 있는 메시지를 포함하여 모든 메시지에 적용됩니다. 

Event Hubs는 모든 파티션에 적용되도록 구성된 보존 시간에 대한 이벤트를 유지합니다. 보존 기간에 도달하면 이벤트가 자동으로 제거됩니다. 보존 기간을 1일로 지정하면 해당 이벤트가 승인되고 정확히 24시간이 지나면 볼 수 없게 됩니다. 이벤트는 명시적으로 삭제할 수 없습니다. 

허용되는 보존 기간을 초과하여 이벤트를 보관해야 하는 경우 [Event Hubs 캡처](event-hubs-capture-overview.md) 기능을 켜서 Azure Storage 또는 Azure Data Lake에 이벤트를 자동으로 저장할 수 있습니다. 이러한 심층 보관을 검색하거나 분석해야 하는 경우 간편하게 해당 항목을 [Azure Synapse](store-captured-data-data-warehouse.md)또는 비슷한 저장소 및 분석 플랫폼에 가져올 수 있습니다. 

Event Hubs에서 데이터 보존 시간을 제한하는 이유는 타임스탬프로만 인덱싱되는 심층 저장소에 대량의 고객 데이터 기록이 모이지 않게 방지하고 순차적 액세스만 허용하도록 하기 위해서입니다. 여기에는 Event Hubs 또는 Kafka에서 제공하는 실시간 이벤트 인터페이스보다 풍부한 인덱싱 및 직접 액세스가 데이터 기록에 필요하다는 아키텍처 철학이 적용되었습니다. 이벤트 스트림 엔진은 이벤트 소싱을 위한 데이터 레이크 또는 장기 보관 스토리지 계층의 역할을 수행하는 데 적합하지 않습니다. 
 

> [!NOTE]
> Event Hubs는 실시간 이벤트 스트림 엔진이며, 데이터베이스 대신 사용하거나 무한히 대기 이벤트 스트림에 대한 영구 저장소로 사용하도록 설계되지 않았습니다. 
> 
> 이벤트 스트림에 대한 기록이 자세할수록 지정된 스트림의 특정 기록 조각을 찾기 위해 더 많은 보조 인덱스가 필요합니다. 이벤트 페이로드 및 인덱싱 검사는 Event Hubs(또는 Apache Kafka)의 기능 범위에 없습니다. 따라서 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md), [Azure Synapse](../synapse-analytics/overview-what-is.md) 같은 데이터베이스와 특수 분석 저장소 및 엔진은 기록 이벤트 저장에 훨씬 더 적합합니다.
>
> [Event Hubs 캡처](event-hubs-capture-overview.md)는 Azure Blob Storage 및 Azure Data Lake Storage와 직접 통합되며, 이러한 통합을 통해 [Azure Synapse로 이벤트를 직접 이동](store-captured-data-data-warehouse.md)할 수도 있습니다.
>
> 애플리케이션에 [이벤트 소싱](/azure/architecture/patterns/event-sourcing) 패턴을 사용하려면 스냅샷 전략을 Event Hubs의 보존 제한에 맞춰야 합니다. 처음부터 원시 이벤트에서 시작하여 구체화된 뷰를 다시 빌드하는 것을 목표로 하지 마세요. 애플리케이션이 잠시 동안 프로덕션 환경에서 잘 사용되고 프로젝션 빌더에서 최근 및 지속적인 변경 사항을 따라 잡으면서 수년간의 변경 이벤트를 변동해야 합니다. 


### <a name="publisher-policy"></a>게시자 정책

Event Hubs는 *게시자 정책* 을 통한 이벤트 게시자에 대한 세부적 제어를 사용합니다. 게시자 정책은 많은 수의 독립 이벤트 게시자를 지원하도록 설계된 런타임 기능입니다. 게시자 정책을 사용하여 다음 메커니즘을 사용하여 Event Hub로 이벤트를 게시하는 경우 각 게시자는 자체 고유 식별자를 사용합니다.

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

시간에 앞서 게시자 이름을 미리 만들 필요가 없지만, 독립 게시자 ID를 보장하기 위해 이벤트를 게시하는 경우 사용하는 SAS 토큰과 일치해야 합니다. 게시자 정책을 사용할 때 **PartitionKey** 값은 게시자 이름으로 설정됩니다. 제대로 작동하려면 이 값이 일치해야 합니다.

## <a name="capture"></a>캡처

[Event Hubs 캡처](event-hubs-capture-overview.md)를 사용하면 Event Hubs의 스트리밍 데이터를 자동으로 캡처하고 선택한 Blob Storage 계정 또는 Azure Data Lake Storage 계정에 저장할 수 있습니다. Azure Portal에서 캡처를 사용하도록 설정하고 캡처를 수행할 최소 크기와 시간 범위를 지정할 수 있습니다. Event Hubs 캡처를 사용하여 자신의 Azure Blob Storage 계정 및 컨테이너 또는 Azure Data Lake Storage 계정을 지정합니다. 이 중 하나는 캡처된 데이터를 저장하는 데 사용됩니다. 캡처된 데이터는 Apache Avro 형식으로 기록됩니다.

:::image type="content" source="./media/event-hubs-features/capture.png" alt-text="Azure Storage 또는 Azure Data Lake Storage로의 Event Hubs 데이터 캡처를 보여주는 이미지":::

Event Hubs 캡처에서 생성된 파일에는 다음과 같은 Avro 스키마가 있습니다.

:::image type="content" source="./media/event-hubs-capture-overview/event-hubs-capture3.png" alt-text="캡처된 데이터의 구조를 보여주는 이미지":::

## <a name="partitions"></a>파티션
[!INCLUDE [event-hubs-partitions](./includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS 토큰

Event Hubs는 네임스페이스 및 Event Hubs 수준에서 사용할 수 있는 *공유 액세스 서명* 을 사용합니다. SAS 토큰은 SAS 키에서 생성되고 특정 형식으로 인코딩된 URL의 SHA 해시입니다. 키(정책)와 토큰의 이름을 사용하는 경우 Event Hubs는 해시를 다시 생성하여 발신자를 인증할 수 있습니다. 일반적으로 이벤트 게시자에 대한 SAS 토큰은 특정 Event Hub에 대한 **전송** 권한만으로 작성됩니다. 이 SAS 토큰 URL 메커니즘은 게시자 정책에 도입된 게시자 ID에 대한 기반이 됩니다. SAS 작업에 대한 자세한 내용은 [Service Bus를 사용한 공유 액세스 서명 인증](../service-bus-messaging/service-bus-sas.md)을 참조하세요.

## <a name="event-consumers"></a>이벤트 소비자

Event Hubs에서 이벤트 데이터를 읽는 모든 엔터티는 *이벤트 소비자* 입니다. 모든 Event Hubs 소비자는 AMQP 1.0 세션을 통해 연결되며, 사용 가능한 상태가 되면 이 세션을 통해 이벤트가 전달됩니다. 클라이언트는 데이터 가용성에 대해 폴링할 필요가 없습니다.

### <a name="consumer-groups"></a>소비자 그룹

*소비자 그룹* 을 통해 이벤트 허브의 게시/구독 메커니즘을 사용하도록 설정합니다. 소비자 그룹은 전체 Event Hub의 보기(상태, 위치, 또는 오프셋)입니다. 소비자 그룹은 여러 소비 애플리케이션을 사용하여 이벤트 스트림의 별도 보기가 있으며 자신의 속도 및 자신의 오프셋으로 독립적으로 스트림을 읽을 수 있습니다.

스트림 처리 아키텍처에서 각 다운스트림 애플리케이션은 소비자 그룹에 해당합니다. 이벤트 데이터를 장기 스토리지에 기록하려는 경우, 해당 스토리지 기록기 애플리케이션은 소비자 그룹입니다. 복합 이벤트 처리는 별도의 다른 소비자 그룹에서 수행될 수 있습니다. 소비자 그룹을 통해서만 파티션을 액세스할 수 있습니다. Event Hubs에는 항상 기본 소비자 그룹이 있으며 해당 가격 책정 계층에 대해 [최대 소비자 그룹 수](event-hubs-quotas.md)를 만들 수 있습니다. 

소비자 그룹당 파티션에는 최대 5개의 동시 판독기가 있을 수 있습니다. 하지만 **소비자 그룹당 파티션에는 활성 수신기를 하나만 포함하는 것이 좋습니다.** 각 판독기는 단일 파티션 내에서 모든 메시지를 받습니다. 동일한 파티션에 여러 판독기가 있는 경우 중복 메시지를 처리합니다. 사소하지 않을 수도 있는 이 문제는 코드에서 처리해야 합니다. 그러나 일부 시나리오에서는 유효한 방법입니다.

Azure SDK에서 제공하는 일부 클라이언트는 각 파티션에 단일 판독기가 있는지와 이벤트 허브에 대한 모든 파티션을 읽을 수 있는지 확인하는 세부 정보를 자동으로 관리하는 인텔리전트 소비자 에이전트입니다. 이렇게 하면 코드에서 이벤트 허브에서 읽는 이벤트를 처리하는 데 집중할 수 있으므로 파티션의 여러 세부 정보를 무시할 수 있습니다. 자세한 내용은 [파티션에 연결](#connect-to-a-partition)을 참조하세요.

다음 예에서는 소비자 그룹 URI 규칙을 보여 줍니다.

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

다음 그림에서는 아키텍처를 처리하는 Event Hubs 스트림을 보여 줍니다.

![Event Hubs 아키텍처](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>스트림 오프셋

*오프셋* 은 파티션 내의 이벤트 위치입니다. 오프셋을 클라이언트 쪽 커서로 생각할 수 있습니다. 오프셋은 이벤트의 바이트 번호입니다. 오프셋을 사용하여 이벤트 소비자(판독기)가 이벤트를 읽기 시작할 이벤트 스트림의 위치를 지정할 수 있습니다. 타임스탬프 또는 오프셋 값으로 오프셋을 지정할 수 있습니다. 소비자는 Event Hubs 서비스 외부에 자신의 오프셋 값을 저장하는 일을 담당합니다. 파티션 내에서 각 이벤트는 오프셋을 포함합니다.

![파티션 오프셋](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>검사점 설정

*검사점* 은 판독기가 파티션 이벤트 시퀀스 내에서 위치를 표시하거나 커밋하는 프로세스입니다. 검사점은 소비자의 책임으로 소비자 그룹 내에서 파티션별로 발생합니다. 이러한 책임은 각 소비자 그룹에 대해 각 파티션 판독기는 이벤트 스트림의 현재 위치를 추적해야 하며 데이터 스트림이 완료된 것으로 간주되면 서비스를 알릴 수 있다는 것을 의미합니다.

판독기가 파티션에서 연결을 끊은 경우 다시 연결하면 해당 소비자 그룹에서 해당 파티션의 마지막 판독기에서 이전에 제출한 검사점에서 읽기 시작합니다. 판독기가 연결하면, 오프셋을 이벤트 허브로 전달하여 읽기 시작할 위치를 지정합니다. 이러한 방식으로, 서로 다른 머신에서 실행되는 판독기 간의 장애 조치(failover)가 발생하는 경우 복원력을 제공하고 다운스트림 애플리케이션에서 이벤트를 "완료"로 표시하는 데 검사점을 사용할 수 있습니다. 이 검사점 프로세스에서 더 낮은 오프셋을 지정하면 이전 데이터로 돌아갈 수 있습니다. 이 메커니즘을 통해 검사점을 지정하면 장애 조치 복원력 및 제어된 이벤트 스트림 재생 모두를 사용할 수 있습니다.

> [!IMPORTANT]
> 오프셋은 Event Hubs 서비스에서 제공합니다. 이벤트가 처리될 때 소비자는 검사점을 담당해야 합니다.

> [!NOTE]
> Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원하는 환경에서 검사점 저장소로 Azure Blob Storage를 사용하는 경우 코드를 사용하여 Storage 서비스 API 버전을 해당 환경에서 지원하는 특정 버전으로 변경해야 합니다. 예를 들어 [Azure Stack Hub 버전 2002의 Event Hubs](/azure-stack/user/event-hubs-overview)에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우 코드를 사용하여 Storage 서비스 API 버전의 대상을 2017-11-09로 해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub에 대한 다음 샘플을 참조하세요. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript) 또는 [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>일반 소비자 작업

모든 Event Hubs 소비자는 상태 인식 양방향 통신 채널인 AMQP 1.0 세션을 통해 연결됩니다. 각 파티션에는 AMQP 1.0 세션이 있어 파티션별로 분리된 이벤트를 쉽게 전송할 수 있습니다.

#### <a name="connect-to-a-partition"></a>파티션에 연결

파티션에 연결할 때 일반적으로 임대 메커니즘을 사용하여 판독기 연결을 특정 파티션으로 조정합니다. 이러한 방식으로, 소비자 그룹의 모든 파티션에 활성 판독기가 하나만 있을 수 있습니다. 인텔리전트 소비자 에이전트 역할을 하는 Event Hubs SDK 내에서 클라이언트를 사용하여 판독기의 검사점 설정, 임대 및 관리를 간소화합니다. 이러한 항목은 다음과 같습니다.

- .NET용 [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- Java용 [EventProcessorClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventProcessorClient.java)
- Python용 [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- JavaScript/TypeScript용 [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>읽기 이벤트

AMQP 1.0 세션 및 링크는 특정 파티션에 대해 열린 후, 이벤트는 이벤트 허브 서비스에서 AMQP 1.0 클라이언트로 전달됩니다. 이 배달 메커니즘은 HTTP GET과 같은 풀 기반 메커니즘보다 더 낮은 대기 시간 및 더 높은 처리량을 사용합니다. 이벤트가 클라이언트에 전송되므로, 각 이벤트 데이터 인스턴스는 이벤트 시퀀스에서 검사점을 용이하게 하는데 사용되는 오프셋 및 시퀀스 번호와 같은 중요한 메타데이터를 포함합니다.

이벤트 데이터:
* Offset
* 시퀀스 번호
* 본문
* 사용자 속성
* 시스템 속성

사용자의 책임 하에 오프셋을 관리해야 합니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

- Event Hubs 시작
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs 프로그래밍 가이드](event-hubs-programming-guide.md)
* [Event Hubs의 가용성 및 일관성](event-hubs-availability-and-consistency.md)
* [Event Hubs FAQ](event-hubs-faq.yml)
* [Event Hubs 샘플](event-hubs-samples.md)
