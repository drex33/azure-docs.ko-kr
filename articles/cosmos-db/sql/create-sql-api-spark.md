---
title: 빠른 시작 - SQL API용 Azure Cosmos DB Spark 3 OLTP Connector를 사용하여 데이터 관리
description: 이 빠른 시작에서는 Azure Cosmos DB 계정에서 데이터에 연결하고 데이터를 쿼리하는 데 사용할 수 있는 SQL API용 Azure Cosmos DB Spark 3 OLTP Connector의 코드 샘플을 제공합니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 11/23/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java, mode-other
ms.openlocfilehash: 730d3ed62ce57c48376e4d926127a80c9ea90b4c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133059096"
---
# <a name="quickstart-manage-data-with-azure-cosmos-db-spark-3-oltp-connector-for-sql-api"></a>빠른 시작: SQL API용 Azure Cosmos DB Spark 3 OLTP Connector를 사용하여 데이터 관리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark 3 OLTP 커넥터](create-sql-api-spark.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)

이 자습서는 Cosmos DB Spark 커넥터를 사용하여 Cosmos DB에서 읽거나 쓰는 방법을 보여 주는 빠른 시작 가이드입니다. Cosmos DB Spark 커넥터는 Spark 3.1.x를 기반으로 합니다.

이 빠른 자습서에서는 [Azure Databricks Runtime 8.0(Spark 3.1.1 포함)](/databricks/release-notes/runtime/8.0) 및 Jupyter Notebook을 사용하여 Cosmos DB Spark 커넥터를 사용하는 방법을 보여 줍니다.

다른 Spark 3.1.1 제품도 사용할 수 있습니다. 또한 Spark에서 지원하는 모든 언어(PySpark, Scala, Java 등) 또는 익숙한 모든 Spark 인터페이스(Jupyter Notebook, Livy 등)를 사용할 수 있어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cosmosdb/)에 등록할 수 있습니다. 또는 개발 및 테스트를 위해 [Azure Cosmos DB 에뮬레이터](../local-emulator.md)를 사용할 수 있습니다.

* [Azure Databricks](/databricks/release-notes/runtime/8.0) Runtime 8.0(Spark 3.1.1 포함)

* (선택 사항) [SLF4J 바인딩](https://www.slf4j.org/manual.html)은 특정 로깅 프레임워크를 SLF4J와 연결하는 데 사용됩니다.

SLF4J는 로깅을 사용하려는 경우에만 필요합니다. SLF4J API를 선택한 로깅 구현과 연결하는 SLF4J 바인딩도 다운로드합니다. 자세한 내용은 [SLF4J 사용자 설명서](https://www.slf4j.org/manual.html)를 참조하세요.

Cosmos DB Spark 커넥터([azure-cosmos-spark_3-1_2-12-4.3.1.jar](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.3.1/jar))를 Spark 클러스터에 설치합니다.

이 시작 가이드는 PySpark를 기반으로 하지만 동일한 Scala 버전도 사용할 수 있으며, Azure Databricks PySpark Notebook에서 다음 코드 조각을 실행할 수 있습니다.

## <a name="create-databases-and-containers"></a>데이터베이스 및 컨테이너 만들기

먼저 Cosmos DB 계정 자격 증명과 Cosmos DB 데이터베이스 이름 및 컨테이너 이름을 설정합니다.

```python
cosmosEndpoint = "https://REPLACEME.documents.azure.com:443/"
cosmosMasterKey = "REPLACEME"
cosmosDatabaseName = "sampleDB"
cosmosContainerName = "sampleContainer"

cfg = {
  "spark.cosmos.accountEndpoint" : cosmosEndpoint,
  "spark.cosmos.accountKey" : cosmosMasterKey,
  "spark.cosmos.database" : cosmosDatabaseName,
  "spark.cosmos.container" : cosmosContainerName,
}
```

다음으로 새 카탈로그 API를 사용하여 Spark를 통해 Cosmos DB 데이터베이스 및 컨테이너를 만들 수 있습니다.

```python
# Configure Catalog Api to be used
spark.conf.set("spark.sql.catalog.cosmosCatalog", "com.azure.cosmos.spark.CosmosCatalog")
spark.conf.set("spark.sql.catalog.cosmosCatalog.spark.cosmos.accountEndpoint", cosmosEndpoint)
spark.conf.set("spark.sql.catalog.cosmosCatalog.spark.cosmos.accountKey", cosmosMasterKey)

# create a cosmos database using catalog api
spark.sql("CREATE DATABASE IF NOT EXISTS cosmosCatalog.{};".format(cosmosDatabaseName))

# create a cosmos container using catalog api
spark.sql("CREATE TABLE IF NOT EXISTS cosmosCatalog.{}.{} using cosmos.oltp TBLPROPERTIES(partitionKeyPath = '/id', manualThroughput = '1100')".format(cosmosDatabaseName, cosmosContainerName))
```

카탈로그 API를 사용하여 컨테이너를 만들 때 만들 컨테이너의 처리량 및 [파티션 키 경로](../partitioning-overview.md#choose-partitionkey)를 설정할 수 있습니다.

자세한 내용은 전체 [카탈로그 API](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md) 설명서를 참조하세요.

## <a name="ingest-data"></a>데이터 수집

데이터 원본의 이름은 `cosmos.oltp`이며, 다음 예제에서는 두 항목으로 구성된 메모리 데이터 프레임을 Cosmos DB에 쓰는 방법을 보여 줍니다.

```python
spark.createDataFrame((("cat-alive", "Schrodinger cat", 2, True), ("cat-dead", "Schrodinger cat", 2, False)))\
  .toDF("id","name","age","isAlive") \
   .write\
   .format("cosmos.oltp")\
   .options(**cfg)\
   .mode("APPEND")\
   .save()
```

`id`는 Cosmos DB에 대한 필수 필드입니다.

데이터 수집과 관련된 자세한 내용은 전체 [쓰기 구성](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md#write-config) 설명서를 참조하세요.

## <a name="query-data"></a>쿼리 데이터

동일한 `cosmos.oltp` 데이터 원본을 사용하여 데이터를 쿼리하고, `filter`를 사용하여 필터를 푸시다운할 수 있습니다.

```python
from pyspark.sql.functions import col

df = spark.read.format("cosmos.oltp").options(**cfg)\
 .option("spark.cosmos.read.inferSchema.enabled", "true")\
 .load()

df.filter(col("isAlive") == True)\
 .show()
```

데이터 쿼리와 관련된 자세한 내용은 전체 [쿼리 구성](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md#query-config) 설명서를 참조하세요.

## <a name="schema-inference"></a>스키마 유추

데이터를 쿼리할 때 Spark 커넥터는 `spark.cosmos.read.inferSchema.enabled`를 `true`로 설정하여 기존 항목 샘플링을 기반으로 하여 스키마를 유추할 수 있습니다.

```python
df = spark.read.format("cosmos.oltp").options(**cfg)\
 .option("spark.cosmos.read.inferSchema.enabled", "true")\
 .load()
 
df.printSchema()
```

또는 데이터를 읽는 데 사용하려는 사용자 지정 스키마를 전달할 수 있습니다.

```python
customSchema = StructType([
      StructField("id", StringType()),
      StructField("name", StringType()),
      StructField("type", StringType()),
      StructField("age", IntegerType()),
      StructField("isAlive", BooleanType())
    ])

df = spark.read.schema(schema).format("cosmos.oltp").options(**cfg)\
 .load()
 
df.printSchema()
```

사용자 지정 스키마가 지정되지 않고 스키마 유추가 사용하지 않도록 설정되면 결과 데이터에서 항목의 원시 Json 콘텐츠를 반환합니다.

```python
df = spark.read.format("cosmos.oltp").options(**cfg)\
 .load()
 
df.printSchema()
```

스키마 유추와 관련된 자세한 내용은 전체 [스키마 유추 구성](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md#schema-inference-config) 설명서를 참조하세요.

## <a name="configuration-reference"></a>구성 참조

### <a name="generic-configuration"></a>일반 구성
| 구성 속성 이름      | 기본값 | 설명 |
| :---        |    :----   |         :--- | 
| `spark.cosmos.accountEndpoint`      | 없음   | Cosmos DB 계정 엔드포인트 URI |
| `spark.cosmos.accountKey`      | 없음    | Cosmos DB 계정 키  |
| `spark.cosmos.database`      | 없음    | Cosmos DB 데이터베이스 이름  |
| `spark.cosmos.container`      | 없음    | Cosmos DB 컨테이너 이름  |

### <a name="extra-tuning"></a>추가 튜닝
| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.useGatewayMode`      | `false`    | 게이트웨이 모드를 클라이언트 작업에 사용합니다.  |
| `spark.cosmos.read.forceEventualConsistency`  | `true`    | 클라이언트에서 기본 계정 수준 일관성을 사용하는 대신 읽기 작업에 대한 최종 일관성을 사용하도록 설정합니다. |
| `spark.cosmos.applicationName`      | 없음    | 애플리케이션 이름  |
| `spark.cosmos.preferredRegionsList`      | 없음    | 다중 지역 Cosmos DB 계정에 사용할 기본 설정 지역 목록입니다. 쉼표로 구분된 값(예: `[East US, West US]` 또는 `East US, West US`)이며, 기본 설정 지역이 힌트로 사용됩니다. Cosmos DB 계정과 함께 배치된 Spark 클러스터를 사용하고 Spark 클러스터 지역을 기본 설정 지역으로 전달해야 합니다. Azure 지역 목록은 [여기](/dotnet/api/microsoft.azure.documents.locationnames?view=azure-dotnet&preserve-view=true)서 참조하세요. 또한 별칭으로 `spark.cosmos.preferredRegions`를 사용할 수 있습니다. |
| `spark.cosmos.diagnostics`      | 없음    | 자세한 진단을 사용하도록 설정하는 데 사용할 수 있습니다. 현재 지원되는 유일한 옵션은 이 속성을 `simple`로 설정하는 것입니다. 그러면 추가 로그가 드라이버 및 실행기 로그에서 `INFO` 로그로 내보내집니다.|

### <a name="write-config"></a>쓰기 구성
| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.write.strategy`      | `ItemOverwrite`    | Cosmos DB 항목 쓰기 전략: `ItemOverwrite`(upsert 사용), `ItemAppend`(만들기 사용, 기존 항목인 Conflicts 무시), `ItemDelete`(모든 문서 삭제), `ItemDeleteIfNotModified`(etag가 변경되지 않은 모든 문서 삭제)  |
| `spark.cosmos.write.maxRetryCount`      | `10`    | 재시도 가능한 오류(예: 연결 오류)에 대한 Cosmos DB 쓰기 최대 다시 시도 횟수   |
| `spark.cosmos.write.point.maxConcurrency`   | 없음   | Cosmos DB 항목 쓰기 최대 동시성. 지정하지 않으면 Spark 실행기 VM 크기에 따라 결정됩니다. |
| `spark.cosmos.write.bulk.maxPendingOperations`   | 없음   | Cosmos DB 항목 쓰기 대량 모드 최대 보류 작업 수. 동시에 처리되는 대량 작업에 대한 제한을 정의합니다. 지정하지 않으면 Spark 실행기 VM 크기에 따라 결정됩니다. 대상 컨테이너의 프로비전된 처리량에 대한 데이터 볼륨이 큰 경우 이 설정은 `1000 x Cores`의 예측에 따라 조정할 수 있습니다. |
| `spark.cosmos.write.bulk.enabled`      | `true`   | Cosmos DB 항목 쓰기 대량 사용 |

### <a name="query-config"></a>쿼리 구성
| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.read.customQuery`      | 없음   | 제공되면 조건자 푸시다운을 통해 쿼리를 동적으로 생성하는 대신 사용자 지정 쿼리가 Cosmos 엔드포인트에 대해 처리됩니다. 일반적으로 쿼리 계획에 따라 가장 효율적인 필터 집합을 만들 수 있으므로 Spark의 조건자 푸시다운을 사용하는 것이 좋습니다. 그러나 아직 푸시다운할 수 없는 집계(count, group by, avg, sum 등)와 같은 몇 가지 조건자가 있습니다(적어도 Spark 3.1에서). 따라서 사용자 지정 쿼리는 Cosmos에 보낸 쿼리에 푸시할 수 있도록 하는 대체(fallback)입니다. 지정되면 스키마 유추를 사용하도록 설정하는 경우 사용자 지정 쿼리도 스키마를 유추하는 데 사용됩니다. |
| `spark.cosmos.read.maxItemCount`  | `1000`    | 단일 쿼리 또는 변경 피드 요청에 대해 반환할 수 있는 최대 문서 수를 재정의합니다. 기본값은 `1000`이며, 1KB보다 작은 평균 문서 크기에 대해서만 또는 프로젝션이 쿼리에서 선택한 속성의 수를 크게 줄이는 경우(예: 문서의 "ID"만 선택하는 경우 등) 이 값을 늘리는 것이 좋습니다.  |

### <a name="schema-inference-config"></a>스키마 유추 구성
읽기 작업을 수행하는 경우 사용자는 사용자 지정 스키마를 지정하거나 커넥터에서 이를 유추하도록 허용할 수 있습니다. 스키마 유추는 기본적으로 사용하도록 설정됩니다.

| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.read.inferSchema.enabled`     | `true`    | 스키마 유추를 사용하지 않도록 설정하고 사용자가 스키마를 제공하지 않는 경우 원시 json이 반환됩니다. |
| `spark.cosmos.read.inferSchema.query`      | `SELECT * FROM r`    | 스키마 유추를 사용하도록 설정하는 경우 이 구성 속성은 유추를 위한 사용자 지정 쿼리로 사용됩니다. 예를 들어 스키마가 다른 여러 엔터티를 컨테이너 내에 저장하고 유추에서 특정 문서 형식만 확인하거나 특정 열만 프로젝션하려는 경우입니다. |
| `spark.cosmos.read.inferSchema.samplingSize`      | `1000`    | 스키마를 유추하고 쿼리를 사용하지 않는 경우 사용할 샘플링 크기입니다. |
| `spark.cosmos.read.inferSchema.includeSystemProperties`     | `false`    | 스키마 유추를 사용하도록 설정하는 경우 모든 [Cosmos DB 시스템 속성](../account-databases-containers-items.md#properties-of-an-item)이 결과 스키마에 포함될지 여부입니다. |
| `spark.cosmos.read.inferSchema.includeTimestamp`     | `false`    | 스키마 유추를 사용하도록 설정하는 경우 문서 타임스탬프(`_ts`)가 결과 스키마에 포함될지 여부입니다. `spark.cosmos.read.inferSchema.includeSystemProperties`이 사용하도록 설정된 경우 이미 모든 시스템 속성을 포함하므로 필요하지 않습니다. |
| `spark.cosmos.read.inferSchema.forceNullableProperties`     | `true`    | 스키마 유추가 사용하도록 설정된 경우 결과 스키마가 모든 열을 nullable로 만들지 여부입니다. null이 아닌 값이 샘플 집합 내의 모든 행에 있는 경우에도 기본적으로 모든 열(cosmos 시스템 속성 제외)은 null 허용으로 처리됩니다. 사용하지 않도록 설정하는 경우 샘플 집합에 있는 레코드의 열 내에 null 값이 있는지 여부에 따라 유추된 열이 null 허용 여부로 처리됩니다.  |

### <a name="serialization-config"></a>직렬화 구성
json 직렬화/역직렬화 동작에 영향을 주는 데 사용됩니다.

| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.serialization.inclusionMode`     | `Always`    | null/기본값을 json으로 직렬화할지 또는 null/기본값이 있는 속성을 건너뛸지 여부를 결정합니다. 동작은 [Jackson의 JsonInclude.Include](https://github.com/FasterXML/jackson-annotations/blob/d0820002721c76adad2cc87fcd88bf60f56b64de/src/main/java/com/fasterxml/jackson/annotation/JsonInclude.java#L98-L227)와 동일한 아이디어를 따릅니다. `Always`는 null 및 기본값에 대해서도 json 속성이 만들어진다는 것을 의미합니다. `NonNull`은 명시적 null 값에 대해 json 속성이 만들어지지 않는 다는 것을 의미합니다. `NonEmpty`는 빈 문자열 값 또는 빈 배열/mpa에 대해 json 속성이 만들어지지 않는 다는 것을 의미합니다. `NonDefault`는 null/빈 값뿐만 아니라 값이 숫자 속성의 기본값인 `0`과 동일한 경우에도 json 속성을 건너뛴다는 것을 의미합니다. |

### <a name="change-feed-only-for-spark-streaming-using-cosmosoltpchangefeed-data-source-which-is-read-only-configuration"></a>변경 피드(읽기 전용인 `cosmos.oltp.changeFeed` 데이터 원본을 사용하는 Spark-Streaming의 경우에만) 구성
| 구성 속성 이름                            | 기본값       | 설명 |
| :---                                            | :----         | :---        | 
| spark.cosmos.changeFeed.startFrom               | `Beginning`   | ChangeFeed는 `Now`, `Beginning` 또는 특정 시점(UTC, 예: `2020-02-10T14:15:03`) 설정에서 시작하며, 기본값은 `Beginning`입니다. `checkpointLocation`이 쓰기 구성에 포함되고 검사점이 있는 경우 스트림은 항상 `spark.cosmos.changeFeed.startFrom` 설정과 독립적으로 계속됩니다. 의도한 경우 스트림을 다시 시작하려면 `checkpointLocation`을 변경하거나 검사점을 삭제해야 합니다. | 
| spark.cosmos.changeFeed.mode                    | `Incremental` | ChangeFeed 모드(`Incremental` 또는 `FullFidelity`)입니다. 참고: `FullFidelity`는 현재 상태에 있습니다. 구독/계정이 프라이빗 미리 보기에 대해 사용하도록 설정되어 있고 `FullFidelity`(반환된 문서의 스키마)에 대해 발생하는 알려진 호환성이 손상되는 변경이 있어야 합니다. 이 시점에서 `FullFidelity`는 비 프로덕션 시나리오에만 사용하는 것이 좋습니다. | 
| spark.cosmos.changeFeed.itemCountPerTriggerHint | 없음          | 각 마이크로 일괄 처리/트리거에 대한 변경 피드에서 읽은 대략적인 최대 항목 수입니다. | 

### <a name="json-conversion-configuration"></a>Json 변환 구성
| 구성 속성 이름      | 기본값 | 설명 |
| :---        |    :----   |         :--- | 
| `spark.cosmos.read.schemaConversionMode`     | `Relaxed`    | 스키마 변환 동작(`Relaxed`, `Strict`)입니다. json 문서를 읽을 때 스키마 유형에 매핑되지 않는 특성이 문서에 포함된 경우 사용자는 `null` 값(Relaxed)을 사용할지 아니면 예외(Strict)를 사용할지 여부를 결정할 수 있습니다. |

### <a name="partitioning-strategy-config"></a>분할 전략 구성
| 구성 속성 이름      | 기본값 | 설명 |
| :---        |    :----   |         :--- | 
| `spark.cosmos.read.partitioning.strategy`     | `Default`    | 사용된 분할 전략(Default, Custom, Restrictive 또는 Aggressive)입니다. |
| `spark.cosmos.partitioning.targetedCount`      | 없음    | 대상 파티션 수입니다. 이 매개 변수는 선택 사항이며, strategy==Custom이 사용되는 경우를 제외하고는 무시됩니다. 이 경우 Spark 커넥터에서 파티션 수를 동적으로 계산하지 않고 이 값을 유지합니다.  |

### <a name="throughput-control-config"></a>처리량 제어 구성
| 구성 속성 이름      | 기본값 | Description |
| :---        |    :----   |         :--- | 
| `spark.cosmos.throughputControl.enabled`      | `false`    | 처리량 제어 사용 여부  |
| `spark.cosmos.throughputControl.name`      | 없음    | 처리량 제어 그룹 이름   |
| `spark.cosmos.throughputControl.targetThroughput`      | 없음   | 처리량 제어 그룹 목표 처리량  |
| `spark.cosmos.throughputControl.targetThroughputThreshold`      | 없음    | 처리량 제어 그룹 대상 처리량 임계값  |
| `spark.cosmos.throughputControl.globalControl.database`      | 없음    | 처리량 전역 제어에 사용되는 데이터베이스  |
| `spark.cosmos.throughputControl.globalControl.container`      | 없음   | 처리량 전역 제어에 사용되는 컨테이너  |
| `spark.cosmos.throughputControl.globalControl.renewIntervalInMS`      | `5s`    | 클라이언트에서 자체 처리량 사용량을 업데이트하는 빈도  |
| `spark.cosmos.throughputControl.globalControl.expireIntervalInMS`      | `11s`   | 오프라인 클라이언트가 검색되는 속도 |

## <a name="next-steps"></a>다음 단계

* Core(SQL) API용 Azure Cosmos DB Apache Spark 3 OLTP 커넥터: [릴리스 정보 및 리소스](sql-api-sdk-java-spark-v3.md)
* [Apache Spark](https://spark.apache.org/)에 대해 자세히 알아봅니다.