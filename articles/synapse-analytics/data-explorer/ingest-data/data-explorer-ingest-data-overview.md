---
title: Azure Synapse Data Explorer 데이터 수집 개요(미리 보기)
description: Azure Synapse Data Explorer에서 데이터를 수집(로드)할 수 있는 다양한 방법을 알아봅니다.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aa240292a3263c6ad7ab922654cc4cab5df1d88c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720318"
---
# <a name="azure-synapse-data-explorer-data-ingestion-overview-preview"></a>Azure Synapse Data Explorer 데이터 수집 개요(미리 보기)

데이터 수집은 하나 이상의 원본에서 데이터 레코드를 로드하여 Azure Synapse Data Explorer 풀의 테이블로 데이터를 가져오는 데 사용되는 프로세스입니다. 수집한 후에는 데이터를 쿼리에 사용할 수 있게 됩니다.

데이터 수집을 담당하는 Azure Synapse Data Explorer 데이터 관리 서비스는 다음과 같은 프로세스를 구현합니다.

- 일괄 처리 또는 외부 원본에서 스트리밍으로 데이터를 끌어오고 보류 중인 Azure 큐에서 요청을 읽습니다.
- 동일한 데이터베이스와 테이블로 흐르는 일괄 처리 데이터는 수집 처리량에 최적화됩니다.
- 초기 데이터의 유효성이 검사되고 필요한 경우 형식이 변환됩니다.
- 그 외에도 스키마 매칭, 데이터 구성, 데이터 인덱싱, 데이터 인코딩, 데이터 압축 등의 데이터 조작이 더 있습니다.
- 데이터는 설정된 보존 정책에 따라 스토리지에 보존됩니다.
- 수집된 데이터는 쿼리에 사용할 수 있는 엔진에 커밋됩니다.

## <a name="supported-data-formats-properties-and-permissions"></a>지원되는 데이터 형식, 속성 및 권한

* **[지원되는 데이터 형식](data-explorer-ingest-data-supported-formats.md)**

* **[수집 속성](data-explorer-ingest-data-properties.md)** : 데이터 수집 방법에 영향을 주는 속성입니다(예: 태그 지정, 매핑, 생성 시간).

* **권한** 데이터를 수집하려면 프로세스에서 [데이터베이스 수집기 수준 권한](/azure/data-explorer/kusto/management/access-control/role-based-authorization?context=/azure/synapse-analytics/context/context)이 필요합니다. 쿼리와 같은 다른 작업에는 데이터베이스 관리자, 데이터베이스 사용자 또는 테이블 관리자 권한이 필요할 수 있습니다.

## <a name="batching-vs-streaming-ingestions"></a>일괄 처리 수집과 스트리밍 수집의 차이

* 일괄 처리 수집은 데이터 일괄 처리를 수행하며 대량 수집에 최적화되었습니다. 선호도가 높고 가장 성능이 뛰어난 수집 방법입니다. 수집 속성에 따라 데이터가 일괄 처리됩니다. 작은 데이터 일괄 처리는 병합되고, 빠른 쿼리 결과를 위해 최적화됩니다. 데이터베이스 또는 테이블에서 [수집 일괄 처리](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) 정책을 설정할 수 있습니다. 기본적으로 최대 일괄 처리 값은 5분, 1000개 항목 또는 전체 크기 1GB입니다.  일괄 수집 명령의 데이터 크기 제한은 4GB입니다.

* [스트리밍 수집](data-explorer-ingest-data-streaming.md)은 스트리밍 원본에서 지속적으로 데이터를 수집하는 것입니다. 스트리밍 수집은 테이블마다 소량의 데이터 세트에 대해 거의 실시간 대기 시간을 허용합니다. 데이터는 처음에 행 저장소에 수집되고, 이후에 열 저장소 범위로 이동됩니다.

## <a name="ingestion-methods-and-tools"></a>수집 방법 및 도구

Azure Synapse Data Explorer는 여러 가지 수집 방법을 지원하며, 방법마다 고유의 시나리오가 있습니다. 이러한 방법에는 수집 도구, 다양한 서비스에 대한 커넥터 및 플러그 인, 관리형 파이프라인, SDK를 사용하는 프로그래밍 방식 수집, 그리고 직접 액세스 수집이 있습니다.

### <a name="ingestion-using-managed-pipelines"></a>관리형 파이프라인을 사용하여 수집

관리(제한, 다시 시도, 모니터링 및 경고)를 외부 서비스에 맡기려는 조직의 경우 커넥터를 사용하는 것이 가장 적합한 솔루션일 가능성이 높습니다. 큐에 대기된 수집은 큰 데이터 볼륨에 적합합니다. Azure Synapse Data Explorer는 다음과 같은 Azure Pipelines를 지원합니다.

<!-- * **[Event Grid](https://azure.microsoft.com/services/event-grid/)**: A pipeline that listens to Azure storage, and updates Azure Synapse Data Explorer to pull information when subscribed events occur. For more information, see [Ingest Azure Blobs into Azure Synapse Data Explorer](ingest-data-event-grid.md). -->

* **[Event Hub](https://azure.microsoft.com/services/event-hubs/)** : 서비스에서 Azure Synapse Data Explorer로 이벤트를 전송하는 파이프라인입니다. 자세한 내용은 [이벤트 허브에서 Azure Synapse Data Explorer로 데이터 수집](data-explorer-ingest-event-hub-overview.md)을 참조하세요.

<!-- * **[IoT Hub](https://azure.microsoft.com/services/iot-hub/)**: A pipeline that is used for the transfer of data from supported IoT devices to Azure Synapse Data Explorer. For more information, see [Ingest from IoT Hub](ingest-data-iot-hub.md). -->

* **Synapse 파이프라인**: [Synapse 파이프라인](../../../data-factory/copy-activity-overview.md?context=%2fazure%2fsynapse-analytics%2fcontext%2fcontext&tabs=synapse-analytics)의 분석 워크로드에 대한 완전 관리형 데이터 통합 서비스는 90개 이상의 지원되는 원본과 연결하여 효율적이고 복원력 있는 데이터 전송을 제공합니다. Synapse 파이프라인은 데이터를 준비, 변환 및 강화하여 다양한 방법으로 모니터링할 수 있는 인사이트를 제공합니다. 이 서비스는 일회성 솔루션으로, 주기적으로 또는 특정 이벤트 발생 시 트리거되도록 사용할 수 있습니다.

<!-- ### Ingestion using connectors and plugins

* **Logstash plugin**, see [Ingest data from Logstash to Azure Synapse Data Explorer](ingest-data-logstash.md).

* **Kafka connector**, see [Ingest data from Kafka into Azure Synapse Data Explorer](ingest-data-kafka.md).

* **[:::no-loc text="Power Automate":::](https://flow.microsoft.com/)**: An automated workflow pipeline to Azure Synapse Data Explorer. :::no-loc text="Power Automate"::: can be used to execute a query and do preset actions using the query results as a trigger. See [Azure Synapse Data Explorer connector to :::no-loc text="Power Automate"::: (Preview)](flow.md).

* **Apache Spark connector**: An open-source project that can run on any Spark cluster. It implements data source and data sink for moving data across Azure Synapse Data Explorer and Spark clusters. You can build fast and scalable applications targeting data-driven scenarios. See [Azure Synapse Data Explorer Connector for Apache Spark](spark-connector.md). -->

### <a name="programmatic-ingestion-using-sdks"></a>SDK를 사용하는 프로그래밍 방식 수집

Azure Synapse Data Explorer는 쿼리 및 데이터 수집에 사용할 수 있는 SDK를 제공합니다. 프로그래밍 방식 수집은 수집 프로세스 중 및 후에 스토리지 트랜잭션을 최소화하여 수집 비용(COG)을 줄이도록 최적화됩니다.

시작하기 전에 다음 단계를 사용하여 프로그래밍 방식의 Data Explorer 풀 엔드포인트를 구성합니다.

[!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

**사용 가능한 SDK 및 오픈 소스 프로젝트**

* [Python SDK](/azure/data-explorer/kusto/api/python/kusto-python-client-library?context=/azure/synapse-analytics/context/context)

* [.NET SDK](/azure/data-explorer/kusto/api/netfx/about-the-sdk?context=/azure/synapse-analytics/context/context)

* [Java SDK](/azure/data-explorer/kusto/api/java/kusto-java-client-library?context=/azure/synapse-analytics/context/context)

* [Node SDK](/azure/data-explorer/kusto/api/node/kusto-node-client-library?context=/azure/synapse-analytics/context/context)

* [REST API](/azure/data-explorer/kusto/api/netfx/kusto-ingest-client-rest?context=/azure/synapse-analytics/context/context)

* [GO SDK](/azure/data-explorer/kusto/api/golang/kusto-golang-client-library?context=/azure/synapse-analytics/context/context)

### <a name="tools"></a>도구

* **[원클릭 수집](data-explorer-ingest-data-one-click.md)** : 다양한 형식의 원본에서 테이블을 만들고 조정하여 신속하게 데이터를 수집할 수 있습니다. 원클릭 수집은 Azure Synapse Data Explorer의 데이터 원본을 기반으로 테이블 및 매핑 구조를 자동으로 제안합니다. 원클릭 수집은 일회성 수집에 사용하거나, 데이터가 수집된 컨테이너의 Event Grid를 통해 지속적인 수집을 정의하는 데 사용할 수 있습니다.

<!-- * **[LightIngest](lightingest.md)**: A command-line utility for ad-hoc data ingestion into Azure Synapse Data Explorer. The utility can pull source data from a local folder or from an Azure blob storage container. -->

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto 쿼리 언어 수집 제어 명령

KQL(Kusto 쿼리 언어) 명령을 통해 데이터를 엔진에 직접 수집할 수 있는 여러 가지 방법이 있습니다. 이 방법은 데이터 관리 서비스를 우회하므로 탐색 및 프로토타입 작성에만 적합합니다. 프로덕션 또는 대량 시나리오에는 이 방법을 사용하지 마세요.

  * **인라인 수집**:  제어 명령 [.ingest inline](/azure/data-explorer/kusto/management/data-ingestion/ingest-inline?context=/azure/synapse-analytics/context/context)은 엔진으로 전송되고, 수집할 데이터는 명령 텍스트 자체에 포함됩니다. 이 방법은 임시 테스트 용도로 사용됩니다.

  * **쿼리에서 수집**: 제어 명령 [.set, .append, .set-or-append 또는 .set-or-replace](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context)는 엔진으로 전송되고, 데이터는 쿼리 또는 명령의 결과로 간접적으로 지정됩니다.

  * **스토리지에서 수집(끌어오기)** : 제어 명령 [.ingest into](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-storage?context=/azure/synapse-analytics/context/context)는 엔진으로 전송되고, 데이터는 엔진에서 액세스할 수 있고 명령이 가리키는 외부 스토리지(예: Azure Blob Storage)에 저장됩니다.

수집 제어 명령을 사용하는 예제는 [Data Explorer를 사용하여 분석](../../get-started-analyze-data-explorer.md)을 참조하세요.

<!-- ## Comparing ingestion methods and tools

| Ingestion name | Data type | Maximum file size | Streaming, batching, direct | Most common scenarios | Considerations |
| --- | --- | --- | --- | --- | --- |
| [**One click ingestion**](ingest-data-one-click.md) | *sv, JSON | 1 GB uncompressed (see note)| Batching to container, local file and blob in direct ingestion | One-off, create table schema, definition of continuous ingestion with event grid, bulk ingestion with container (up to 10,000 blobs) | 10,000 blobs are randomly selected from container|
| [**LightIngest**](lightingest.md) | All formats supported | 1 GB uncompressed (see note) | Batching via DM or direct ingestion to engine |  Data migration, historical data with adjusted ingestion timestamps, bulk ingestion (no size restriction)| Case-sensitive, space-sensitive |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX to Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory (ADF)**](./data-factory-integration.md) | [Supported data formats](../../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats) | unlimited *(per ADF restrictions) | Batching or per ADF trigger | Supports formats that are usually unsupported, large files, can copy from over 90 sources, from on perm to cloud | This method takes relatively more time until data is ingested. ADF uploads all data to memory and then begins ingestion. |
|[ **Power Automate**](./flow.md) | | | | Ingestion commands as part of flow| Must have high-performing response time |
| [**IoT Hub**](ingest-data-iot-hub-overview.md) | [Supported data formats](ingest-data-iot-hub-overview.md#data-format)  | N/A | Batching, streaming | IoT messages, IoT events, IoT properties | |
| [**Event Hub**](ingest-data-event-hub-overview.md) | [Supported data formats](ingest-data-event-hub-overview.md#data-format) | N/A | Batching, streaming | Messages, events | |
| [**Event Grid**](ingest-data-event-grid-overview.md) | [Supported data formats](ingest-data-event-grid-overview.md#data-format) | 1 GB uncompressed | Batching | Continuous ingestion from Azure storage, external data in Azure storage | Ingestion can be triggered by blob renaming or blob creation actions. |
| [**.NET SDK**](./net-sdk-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Python**](python-ingest-data.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Node.js**](node-ingest-data.md) | All formats supported | 1 GB uncompressed (see note | Batching, streaming, direct | Write your own code according to organizational needs |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct| Write your own code according to organizational needs |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | All formats supported | 1 GB uncompressed (see note) | Batching, streaming, direct | Write your own code according to organizational needs |

> [!Note]
> When referenced in the above table, ingestion supports a maximum file size of 4 GB. The recommendation is to ingest files between 100 MB and 1 GB. -->

## <a name="ingestion-process"></a>수집 프로세스

요구 사항에 가장 적합한 수집 방법을 선택한 후에는 다음 단계를 수행합니다.

1. **보존 정책 설정**

    Azure Synapse Data Explorer의 테이블에 수집되는 데이터에는 테이블의 유효 보존 정책이 적용됩니다. 테이블에 명시적으로 설정되지 않은 경우 유효 보존 정책은 데이터베이스의 보존 정책에서 파생됩니다. 핫 보존은 클러스터 크기 및 보존 정책에 대한 함수입니다. 사용 가능한 공간보다 더 많은 데이터를 수집하면 첫 번째 데이터가 콜드 보존으로 강제 적용됩니다.

    데이터베이스의 보존 정책이 요구 사항에 적합해야 합니다. 그렇지 않으면 테이블 수준에서 명시적으로 재정의합니다. 자세한 내용은 [보존 정책](/azure/data-explorer/kusto/management/retentionpolicy?context=/azure/synapse-analytics/context/context)을 참조하세요.

1. **테이블 만들기**

    데이터를 수집하려면 먼저 테이블을 만들어야 합니다. 다음 옵션 중 하나를 사용합니다.

    * 명령을 사용하여 테이블을 만듭니다. 테이블 만들기 명령을 사용하는 예제는 [Data Explorer를 사용하여 분석](../../get-started-analyze-data-explorer.md)을 참조하세요.

    * [원클릭 수집](data-explorer-ingest-data-one-click.md)을 사용하여 테이블을 만듭니다.

    > [!Note]
    > 레코드가 불완전하거나 필수 데이터 형식으로 필드를 구문 분석할 수 없는 경우, 해당 테이블 열에는 Null 값이 채워집니다.

1. **스키마 매핑 만들기**

    [스키마 매핑](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context)은 원본 데이터 필드를 대상 테이블 열에 바인딩하는 데 도움이 됩니다. 매핑을 사용하면 정의된 특성에 따라 여러 원본의 데이터를 동일한 테이블로 가져올 수 있습니다. 행 기반(CSV, JSON 및 AVRO) 및 열 기반(Parquet)의 여러 가지 매핑 형식이 지원됩니다. 대부분의 방법에서 매핑을 [테이블에 미리 만들고](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) 수집 명령 매개 변수에서 참조할 수도 있습니다.

1. **업데이트 정책 설정**(선택 사항)

   일부 데이터 형식 매핑(Parquet, JSON 및 Avro)은 간단하면서도 유용한 수집 시간 변환을 지원합니다. 데이터를 수집할 때 좀 더 복잡한 처리가 필요한 시나리오인 경우 Kusto 쿼리 언어 명령을 사용하여 간단하게 처리할 수 있는 업데이트 정책을 사용합니다. 업데이트 정책은 원래 테이블에 수집된 데이터에 대해 추출 및 변환을 자동으로 실행하고, 그 결과로 얻은 데이터를 하나 이상의 대상 테이블에 수집합니다. [업데이트 정책](/azure/data-explorer/kusto/management/update-policy?context=/azure/synapse-analytics/context/context)을 설정하세요.



## <a name="next-steps"></a>다음 단계

- [지원되는 데이터 형식](data-explorer-ingest-data-supported-formats.md)
- [지원되는 수집 속성](data-explorer-ingest-data-properties.md)