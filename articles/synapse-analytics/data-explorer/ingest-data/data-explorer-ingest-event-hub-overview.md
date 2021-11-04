---
title: Azure Synapse 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 (미리 보기)
description: 이 문서에서는 이벤트 허브에서 Azure Synapse 데이터 탐색기로 데이터를 수집 (로드) 하는 방법에 대 한 개요를 제공 합니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aaada2bd1a778fd6e5bed4ea6a42e847b3e9d983
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482929"
---
# <a name="event-hub-data-connection-preview"></a>이벤트 허브 데이터 연결 (미리 보기)

[Azure Event Hubs](/azure/event-hubs/event-hubs-about)는 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. Azure Synapse 데이터 탐색기는 고객이 관리 하는 Event Hubs에서 지속적인 수집을 제공 합니다.

이벤트 허브 수집 파이프라인은 여러 단계로 이벤트를 Azure Synapse 데이터 탐색기으로 전송 합니다. 먼저 Azure Portal에서 Event Hub를 만듭니다. 그런 다음 지정 된 수집 [속성](#ingestion-properties)을 사용 하 여 [특정 형식의 데이터가](#data-format)수집 되는 Azure Synapse 데이터 탐색기에서 대상 테이블을 만듭니다. Event Hub 연결에서 [이벤트 라우팅](#events-routing)을 알아야 합니다. 데이터는 [이벤트 시스템 속성 매핑](#event-system-properties-mapping)에 따라 선택한 속성에 포함됩니다. Event Hub에 대한 [연결을 생성](#event-hub-connection)하여 [Event Hub를 생성](#create-an-event-hub)하고 [이벤트로 보냅니다](#send-events). 이 프로세스는 [Azure Portal](data-explorer-ingest-event-hub-portal.md)을 통해 [C#](data-explorer-ingest-event-hub-csharp.md) 또는 [Python](data-explorer-ingest-event-hub-python.md)을 사용하여 프로그래밍 방식으로 관리하거나 [Azure Resource Manager 템플릿](data-explorer-ingest-event-hub-resource-manager.md)을 사용하여 관리할 수 있습니다.

Azure Synapse 데이터 탐색기에서 데이터를 수집 하는 방법에 대 한 일반적인 내용은 [Azure Synapse 데이터 탐색기 데이터 수집 개요](data-explorer-ingest-data-overview.md)를 참조 하세요.

## <a name="data-format"></a>데이터 형식

* 데이터는 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체 형식으로 Event Hub에서 읽습니다.
* [지원되는 형식](data-explorer-ingest-data-supported-formats.md)을 참조하세요.
    > [!NOTE]
    > Event Hub는 .raw 형식을 지원하지 않습니다.

* `GZip` 압축 알고리즘을 사용하여 데이터를 압축할 수 있습니다. [수집 속성](#ingestion-properties)에서 `Compression`을 지정합니다.
   * 압축 형식(Avro, Parquet, ORC)에 대해서는 데이터 압축이 지원되지 않습니다.
   * 사용자 지정 인코딩 및 포함된 [시스템 속성](#event-system-properties-mapping)은 압축된 데이터에서 지원되지 않습니다.
  
## <a name="ingestion-properties"></a>수집 속성

수집 프로세스는 수집 속성을 통해 데이터를 라우팅할 위치와 처리 방법을 알게 됩니다. [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용하여 이벤트 수집의 [수집 속성](data-explorer-ingest-data-properties.md)을 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |설명|
|---|---|
| 테이블 | 기존 대상 테이블의 이름(대/소문자 구분)입니다. `Data Connection` 창에 설정된 `Table`을 재정의합니다. |
| 서식 | 데이터 형식 `Data Connection` 창에 설정된 `Data format`을 재정의합니다. |
| IngestionMappingReference | 사용할 기존 [수집 매핑](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context)의 이름입니다. `Data Connection` 창에 설정된 `Column mapping`을 재정의합니다.|
| 압축 | 데이터 압축, `None`(기본값) 또는 `GZip` 압축.|
| Encoding | 데이터 인코딩, 기본값은 UTF8입니다. [.NET 지원 인코딩](/dotnet/api/system.text.encoding#remarks) 중 하나일 수 있습니다. |
| 태그 | 수집된 데이터와 연결할 JSON 배열 문자열 형식의 [태그](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) 목록입니다. 태그를 사용할 때 [성능에 영향](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags)이 있습니다. |

> [!NOTE]
> 데이터 연결을 만든 후에 큐에 넣은 이벤트만 수집됩니다.

## <a name="events-routing"></a>이벤트 라우팅

Azure Synapse 데이터 탐색기 클러스터에 대 한 이벤트 허브 연결을 설정 하는 경우 대상 테이블 속성 (테이블 이름, 데이터 형식, 압축 및 매핑)을 지정 합니다. 데이터의 기본 라우팅을 `static routing`이라고도 합니다.
이벤트 속성을 사용하여 각 이벤트에 대한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 이벤트의 정적 속성을 재정의하여 [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties)에 지정된 대로 데이터를 동적으로 라우팅합니다.

다음 예제에서는 이벤트 허브 세부 정보를 설정 하 고 날씨 메트릭 데이터를 테이블에 보냅니다 `WeatherMetrics` .
데이터는 `json` 형식입니다. `mapping1`은 테이블 `WeatherMetrics`에 미리 정의되어 있습니다.

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

시스템 속성은 이벤트를 큐에 넣을 때 Event Hub 서비스에서 설정한 속성을 저장합니다. Azure Synapse 데이터 탐색기 이벤트 허브 연결에서는 선택한 속성을 테이블의 데이터에 포함 합니다.

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

### <a name="system-properties"></a>시스템 속성

Event Hub는 다음 시스템 속성을 노출합니다.

|속성 |데이터 형식 |Description|
|---|---|---|
| x-opt-enqueued-time |Datetime | 이벤트를 큐에 넣은 UTC 시간 |
| x-opt-sequence-number |long | Event Hub의 파티션 스트림 내에 있는 이벤트의 논리적 시퀀스 번호
| x-opt-offset |문자열 | Event Hub 파티션 스트림의 데이터 오프셋입니다. 오프셋 식별자는 Event Hub 스트림의 파티션 내에서 고유합니다. |
| x-opt-publisher |문자열 | 게시자 엔드포인트에 메시지를 보낸 경우 게시자 이름 |
| x-opt-partition-key |문자열 |이벤트를 저장한 해당 파티션의 파티션 키 |

<!-- When you work with [IoT Central](https://azure.microsoft.com/services/iot-central/) event hubs, you can also embed IoT Hub system properties in the payload. For the complete list, see [IoT Hub system properties](ingest-data-iot-hub-overview.md#event-system-properties-mapping). -->

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 속성을 포함해야 합니다.

[!INCLUDE [data-explorer-container-system-properties](../includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>이벤트 허브 연결

> [!Note]
> 최상의 성능을 위해 Azure Synapse 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="create-an-event-hub"></a>Event Hub 만들기

아직 없는 경우 [이벤트 허브를 만듭니다](/azure/event-hubs/event-hubs-create). Event Hub에 대한 연결은 [Azure Portal](data-explorer-ingest-event-hub-portal.md)을 통해 [C#](data-explorer-ingest-event-hub-csharp.md) 또는 [Python](data-explorer-ingest-event-hub-python.md)을 사용하여 프로그래밍 방식으로 관리하거나 [Azure Resource Manager 템플릿](data-explorer-ingest-event-hub-resource-manager.md)을 사용하여 관리할 수 있습니다.

> [!Note]
> * 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다.
> * 소비자 그룹은 소비자별로 *고유해야* 합니다. Azure Synapse 데이터 탐색기 연결 전용 소비자 그룹을 만듭니다.

## <a name="send-events"></a>이벤트 보내기

데이터를 생성 하 고 이벤트 허브로 전송 하는 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 을 참조 하세요.

샘플 데이터를 생성 하는 방법에 대 한 예제는 [이벤트 허브에서 Azure Synapse로 데이터 수집](data-explorer-ingest-event-hub-portal.md#generate-sample-data) 을 참조 하세요 데이터 탐색기

## <a name="set-up-geo-disaster-recovery-solution"></a>지리적 재해 복구 솔루션 설정

Event Hub는 [지리적 재해 복구](/azure/event-hubs/event-hubs-geo-dr) 솔루션을 제공합니다. Azure Synapse 데이터 탐색기는 `Alias` Event Hub 네임 스페이스를 지원 하지 않습니다. 솔루션에서 지리적 재해 복구를 구현하려면 두 개의 Event Hub 데이터 연결을 만듭니다. 하나는 기본 네임스페이스용이고 다른 하나는 보조 네임스페이스용입니다. Azure Synapse 데이터 탐색기는 두 이벤트 허브 연결을 모두 수신 대기 합니다.

> [!NOTE]
> 기본 네임스페이스에서 보조 네임스페이스로 장애 조치(failover)를 구현하는 것은 사용자의 책임입니다.

## <a name="next-steps"></a>다음 단계

- [이벤트 허브에서 Azure Synapse 데이터 탐색기로 데이터 수집](data-explorer-ingest-event-hub-portal.md)
- [C를 사용 하 여 Azure Synapse 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기 #](data-explorer-ingest-event-hub-csharp.md)
- [Python을 사용 하 여 Azure Synapse 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기](data-explorer-ingest-event-hub-python.md)
- [Azure Resource Manager 템플릿을 사용 하 여 Azure Synapse 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기](data-explorer-ingest-event-hub-resource-manager.md)
