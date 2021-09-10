---
title: Azure Synapse Link에서 Apache Spark 2를 사용하여 Azure Cosmos DB와 상호 작용
description: Azure Synapse Link에서 Apache Spark를 사용하여 Azure Cosmos DB와 상호 작용하는 방법
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 9ea9d5d34d69b950e3fa80c350c43f12615660ba
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112559"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-2-in-azure-synapse-link"></a>Azure Synapse Link에서 Apache Spark 2를 사용하여 Azure Cosmos DB와 상호 작용

> [!NOTE]
> Spark 3을 사용하는 Cosmos DB용 Synapse Link는 [Spark 3의 Azure Cosmos DB용 Azure Synapse Link](how-to-query-analytical-store-spark-3.md) 문서를 참조하세요.

이 문서에서는 Synapse Apache Spark 2를 사용하여 Azure Cosmos DB와 상호 작용하는 방법을 알아봅니다. Scala, Python, SparkSQL 및 C#을 완전히 지원하는 Synapse Apache Spark는 [Azure Synapse Link for Azure Cosmos DB](../../cosmos-db/synapse-link.md)에서 분석, 데이터 엔지니어링, 데이터 과학 및 데이터 탐색 시나리오의 중심입니다.

Azure Cosmos DB와 상호 작용하는 동안 다음 기능이 지원됩니다.
* Synapse Apache Spark를 사용하면 트랜잭션 워크로드의 성능에 영향을 주지 않고 Azure Synapse Link를 통해 설정된 Azure Cosmos DB 컨테이너의 데이터를 거의 실시간으로 분석할 수 있습니다. 다음 두 옵션을 사용하여 Spark에서 Azure Cosmos DB [분석 저장소](../../cosmos-db/analytical-store-introduction.md)를 쿼리할 수 있습니다.
    + Spark 데이터 프레임에 로드
    + Spark 테이블 만들기
* Synapse Apache Spark를 사용하면 Azure Cosmos DB로 데이터를 수집할 수도 있습니다. 데이터는 항상 트랜잭션 저장소를 통해 Azure Cosmos DB 컨테이너로 수집된다는 점을 기억해야 합니다. Synapse Link를 사용하도록 설정하면 새로운 삽입, 업데이트 및 삭제 작업이 자동으로 분석 저장소에 동기화됩니다.
* Synapse Apache Spark는 Azure Cosmos DB를 사용하는 Spark 구조적 스트리밍을 소스이자 싱크로 지원합니다. 

다음 섹션에서는 위 기능의 구문을 안내합니다. [Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Cosmos DB를 쿼리](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/)하는 방법에 대한 학습 모듈을 확인할 수도 있습니다. Azure Synapse Analytics 작업 영역의 제스처는 즉시 시작할 수 있는 간편한 환경을 제공하도록 설계되었습니다. Synapse 작업 영역의 **데이터** 탭에서 Azure Cosmos DB 컨테이너를 마우스 오른쪽 단추로 클릭하면 제스처가 표시됩니다. 제스처를 사용하면 코드를 빠르게 생성하고 필요에 맞게 조정할 수 있습니다. 제스처는 한 번의 클릭으로 데이터를 검색하는 데에도 적합합니다.

> [!IMPORTANT]
> 데이터 로드 작업에서 예기치 않은 동작이 발생할 수 있는 분석 스키마의 몇 가지 제약 조건을 알고 있어야 합니다.
> 예를 들어, 트랜잭션 스카마의 처음 1000개의 속성만 분석 스카마에서 사용할 수 있으며 공백이 있는 속성은 사용할 수 없습니다. 예기치 않은 결과가 발생하는 경우 자세한 내용은 [분석 저정소 스키마 제약 조건](../../cosmos-db/analytical-store-introduction.md#schema-constraints)을 확인하세요.

## <a name="query-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소 쿼리

Azure Cosmos DB 분석 저장소를 쿼리하는 두 가지 옵션(Spark 데이터 프레임으로 로드 및 Spark 테이블 만들기)에 대해 알아보기 전에, 본인의 요구 사항에 맞는 옵션을 선택할 수 있도록 환경의 차이점을 살펴볼 필요가 있습니다.

환경의 차이점은 Azure Cosmos DB 컨테이너의 기본 데이터 변경 내용이 Spark에서 수행되는 분석에 자동으로 반영되는지 여부와 관련이 있습니다. Spark 데이터 프레임이 등록되거나 컨테이너의 분석 저장소에 대해 Spark 테이블이 생성되면 후속 분석의 효율적인 푸시다운을 위해 분석 저장소에 있는 데이터의 현재 스냅샷에 대한 메타데이터가 Spark로 페치됩니다. Spark는 지연 계산 정책을 따르기 때문에 작업이 Spark 데이터 프레임에서 호출되거나 SparkSQL 쿼리가 Spark 테이블에 대해 실행되지 않는 이상, 실제 데이터는 기본 컨테이너의 분석 저장소에서 페치되지 않습니다.

**Spark 데이터 프레임 로드** 를 선택하는 경우 페치된 메타데이터는 Spark 세션의 수명 추기 동안 캐시되므로 데이터 프레임에서 호출된 후속 작업은 데이터 프레임 생성 시 분석 저장소의 스냅샷에 대해 평가됩니다.

반면 **Spark 테이블 만들기** 를 선택하는 경우 분석 저장소 상태의 메타데이터가 Spark에 캐시되지 않고 Spark 테이블에 대한 SparkSQL 쿼리가 실행될 때마다 다시 로드됩니다.

따라서 분석 저장소의 고정된 스냅샷과 분석 저장소의 최신 스냅샷 중 무엇에 대해 Spark 분석을 평가할 것인지에 따라 Spark 데이터 프레임에 로드하는 방법 또는 Spark 테이블을 만드는 방법 중에 선택할 수 있습니다.

> [!NOTE]
> Mongo DB 계정의 Azure Cosmos DB API를 쿼리하려면 분석 저장소의 [전체 충실도 스키마 표현](../../cosmos-db/analytical-store-introduction.md#analytical-schema) 및 사용할 확장 속성 이름에 대해 자세히 알아보세요.

> [!NOTE]
> 아래 명령의 모든 `options`는 대/소문자를 구분합니다. 예를 들어, `Gateway`를 사용해야 하지만 `gateway`는 오류를 반환합니다.

### <a name="load-to-spark-dataframe"></a>Spark 데이터 프레임에 로드

이 예제에서는 Azure Cosmos DB 분석 저장소를 가리키는 Spark 데이터 프레임을 만듭니다. 그런 다음, 데이터 프레임에 대해 Spark 작업을 호출하여 추가 분석을 수행할 수 있습니다. 이 작업은 트랜잭션 저장소에 영향을 주지 않습니다.

**Python** 의 구문은 다음과 같습니다.
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

**Scala** 의 해당 구문은 다음과 같습니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark 테이블 만들기

이 예제에서는 Azure Cosmos DB 분석 저장소를 가리키는 Spark 테이블을 만듭니다. 그런 다음, 테이블에 대해 SparkSQL 쿼리를 호출하여 추가 분석을 수행할 수 있습니다. 이 작업은 트랜잭션 저장소에 영향을 주거나 데이터 이동을 발생시키지 않습니다. 이 Spark 테이블을 삭제하기로 결정하는 경우 기본 Azure Cosmos DB 컨테이너 및 해당하는 분석 저장소는 영향을 받지 않습니다. 

이 시나리오는 타사 도구를 통해 Spark 테이블을 다시 사용하고 런타임에 대한 기본 데이터에 대한 액세스를 제공하는 데 편리합니다.

Spark 테이블을 만드는 구문은 다음과 같습니다.
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> 기본 Azure Cosmos DB 컨테이너의 스키마가 시간에 따라 변경되는 시나리오가 있으며 업데이트된 스키마가 Spark 테이블에 대한 쿼리에 자동으로 반영되기를 원하는 경우 Spark 테이블 옵션에서 `spark.cosmos.autoSchemaMerge` 옵션을 `true`로 설정하여 목표를 달성할 수 있습니다.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Azure Cosmos DB 컨테이너에 Spark 데이터 프레임 쓰기

이 예제에서는 Azure Cosmos DB 컨테이너에 Spark 데이터 프레임을 씁니다. 이 작업은 트랜잭션 워크로드의 성능에 영향을 미치며 Azure Cosmos DB 컨테이너 또는 공유 데이터베이스에 프로비저닝된 요청 단위를 소모합니다.

**Python** 의 구문은 다음과 같습니다.
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** 의 해당 구문은 다음과 같습니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>컨테이너에서 스트리밍 DataFrame 로드
이 제스처에서는 Spark Streaming 기능을 사용하여 컨테이너에서 데이터 프레임으로 데이터를 로드합니다. 데이터는 작업 영역에 연결된 기본 데이터 레이크 계정(및 파일 시스템)에 저장됩니다. 
> [!NOTE]
> Synapse Apache Spark에서 외부 라이브러리를 참조하려면 [여기](../spark/apache-spark-azure-portal-add-libraries.md)를 참조하세요. 예를 들어 Cosmos DB API for Mongo DB의 컨테이너로 Spark 데이터 프레임을 수집하려는 경우 [여기](https://docs.mongodb.com/spark-connector/master/)서 Spark용 Mongo DB를 활용할 수 있습니다.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Azure Cosmos DB 컨테이너에서 스트리밍 데이터 프레임 로드
이 예제에서는 Spark의 구조적 스트리밍 기능을 사용하여 Azure Cosmos DB의 변경 피드 기능을 통해 Azure Cosmos DB 컨테이너의 데이터를 Spark 스트리밍 데이터 프레임으로 로드합니다. Spark에서 사용하는 검사점 데이터는 작업 영역에 연결된 기본 데이터 레이크 계정(및 파일 시스템)에 저장됩니다.

아래 예제의 */localReadCheckpointFolder* 폴더가 없으면 자동으로 생성됩니다. 이 작업은 트랜잭션 워크로드의 성능에 영향을 미치며 Azure Cosmos DB 컨테이너 또는 공유 데이터베이스에 프로비저닝된 요청 단위를 소모합니다.

**Python** 의 구문은 다음과 같습니다.
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** 의 해당 구문은 다음과 같습니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Azure Cosmos DB 컨테이너에 스트리밍 데이터 프레임 쓰기
이 예제에서는 Azure Cosmos DB 컨테이너에 스트리밍 데이터 프레임을 씁니다. 이 작업은 트랜잭션 워크로드의 성능에 영향을 미치며 Azure Cosmos DB 컨테이너 또는 공유 데이터베이스에 프로비저닝된 요청 단위를 소모합니다. 아래 예제의 */localWriteCheckpointFolder* 폴더가 없으면 자동으로 생성됩니다. 

**Python** 의 구문은 다음과 같습니다.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "Gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** 의 해당 구문은 다음과 같습니다.
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "Gateway").
            start()

query.awaitTermination()
```


## <a name="next-steps"></a>다음 단계

* [GitHub에서 Azure Synapse Link를 시작하는 샘플](https://aka.ms/cosmosdb-synapselink-samples)
* [Azure Cosmos DB용 Azure Synapse Link에서 지원되는 기능 알아보기](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB용 Synapse Link에 연결](../quickstart-connect-synapse-link-cosmos-db.md)
* [Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Cosmos DB 쿼리](/learn/modules/query-azure-cosmos-db-with-apache-spark-for-azure-synapse-analytics/) 방법에 대한 학습 모듈을 확인하세요.
