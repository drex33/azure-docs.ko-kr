---
title: 분할 분석 저장소 데이터에 대한 사용자 지정 분할 구성(미리 보기)
description: Azure Cosmos DB에 대한 Azure Synapse 링크를 사용하여 Azure Synapse Spark Notebook에서 사용자 지정 분할을 트리거하는 방법을 알아봅니다. 구성 옵션에 대해 설명합니다.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7848a9b578c6054ef1d3deb29bf4c2bf92b9f68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053113"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>분할 분석 저장소 데이터에 대한 사용자 지정 분할 구성(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

사용자 지정 분할을 사용하려면 Azure Cosmos DB 계정에서 Azure Synapse Link를 사용하도록 설정해야 합니다. 자세한 내용은 [Azure Synapse 링크를 구성하는 방법을](configure-synapse-link.md)참조하세요. Azure Cosmos DB에 대한 Azure Synapse 링크를 사용하여 Azure Synapse Spark Notebook에서 사용자 지정 분할 실행을 트리거할 수 있습니다.

> [!IMPORTANT]
> 사용자 지정 분할 기능은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> Azure Cosmos DB 계정은 사용자 지정 분할을 활용하기 위해 Azure Synapse Link를 사용하도록 설정해야 합니다. 사용자 지정 분할은 현재 Azure Synapse Spark 2.0에서만 지원됩니다.

다음은 사용자 지정 분할 실행을 트리거하는 데 필요한 필수 구성 옵션입니다.

* `spark.cosmos.asns.execute.partitioning` - 사용자 지정 분할 실행을 트리거하는 부울 값입니다. 기본값은 false입니다.

* `spark.cosmos.asns.partition.keys` - DDL 형식 문자열을 사용하여 키/s를 분할합니다. 예를 들어 *ReadDate String 입니다.*

* `spark.cosmos.asns.basePath` - Synapse 기본 스토리지 계정의 분할된 저장소에 대한 기본 경로입니다.

> [!NOTE]
> 여러 파티션 키를 선택하는 경우 키를 나타내는 basePath를 사용하여 동일한 분할된 저장소에서 이러한 레코드에 액세스할 수 있습니다.

다음은 사용자 지정 분할 실행을 트리거할 때 사용할 수 있는 선택적 구성 옵션입니다.

* `spark.cosmos.asns.merge.partitioned.files` - 가 실행당 파티션 값당 단일 파일을 만들 수 있도록 하는 부울 값입니다. 기본값은 false입니다.

* `spark.cosmos.asns.partitioning.maxRecordsPerFile` - 분할된 저장소의 단일 분할된 파일에 있는 최대 레코드 수입니다. 이 구성 및 `spark.cosmos.asns.merge.partitioned.files` 를 지정하면 레코드 수가 maxRecordsPerFile 값을 초과하면 새 파일이 만들어집니다. 이 구성은 일반적으로 더 큰 컬렉션에 대한 초기 분할에만 필요합니다. 기본값은 1,000,000입니다.

  maxRecordsPerFile을 설정했지만 를 구성하지 않으면 `spark.cosmos.asns.merge.partitioned.files` 레코드가 maxRecordsPerFile에 도달하기 전에 파일 간에 분할할 수 있습니다. 또한 파일 분할은 풀에서 사용 가능한 병렬에 따라 달라집니다.

* `spark.cosmos.asns.partitioning.shuffle.partitions` - 분할된 저장소에 분할된 쓰기를 하는 동안 병렬을 제어합니다. 이 구성은 더 큰 컬렉션에 대한 초기 분할에만 필요합니다. Spark 풀에서 사용 가능한 코어 수로 설정됩니다. 기본값은 200입니다. 풀이 다른 워크로드에 사용되지 않는 경우 값이 낮으면 리소스가 낭비될 수 있습니다. 값이 높을수록 일반적으로 문제가 발생하지 않습니다. 일부 작업은 초기에 완료되고 느린 작업이 실행되는 동안 더 많은 작업을 시작할 수 있기 때문입니다. 분할 작업을 더 빠르게 완료하려면 풀 크기를 늘리는 것이 좋습니다.

# <a name="python"></a>[Python](#tab/python)

```python
spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.execute.partitioning", "true") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .option("spark.cosmos.asns.merge.partitioned.files", "true") \
    .option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000") \
    .option("spark.cosmos.asns.partitioning.shuffle.partitions", "400") \
    .load()
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
spark.read.
    format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.asns.execute.partitioning", "true").
    option("spark.cosmos.asns.partition.keys", "readDate String").
    option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
    option("spark.cosmos.asns.merge.partitioned.files", "true").
    option("spark.cosmos.asns.partitioning.maxRecordsPerFile", "2000000").
    option("spark.cosmos.asns.partitioning.shuffle.partitions", "400").
    load()
```
---

## <a name="query-execution-with-partitioned-store"></a>분할된 저장소를 사용하여 쿼리 실행

분할된 저장소 지원을 사용하여 쿼리를 실행하려면 다음 두 가지 구성이 필요합니다.

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

다음 예제에서는 이러한 구성을 사용하여 위의 분할된 저장소를 쿼리하는 방법과 파티션 키를 사용하여 필터링하면 파티션 정리를 사용하는 방법을 보여 있습니다. 이 분할된 저장소는 "ReadDate" 필드를 사용하여 분할됩니다.

# <a name="python"></a>[Python](#tab/python)

```python
df = spark.read\
    .format("cosmos.olap") \
    .option("spark.synapse.linkedService", "<enter linked service name>") \
    .option("spark.cosmos.container", "<enter container name>") \
    .option("spark.cosmos.asns.partition.keys", "readDate String") \
    .option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/") \
    .load()

df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```

# <a name="scala"></a>[Scala](#tab/scala)

```scala
val df = spark.read.
            format("cosmos.olap").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.asns.partition.keys", "readDate String").
            option("spark.cosmos.asns.basePath", "/mnt/CosmosDBPartitionedStore/").
            load()
val df_filtered = df.filter("readDate='2020-11-27 00:00:00.000'")
display(df_filtered.limit(10))
```
---

위의 *ReadDate = '2021-11-01'* 필터는 실행 중에 검사에서 *2021-11-01* 이외의 ReadDate 값에 해당하는 데이터를 제거합니다.

> [!NOTE]
> 분할된 저장소를 사용하는 쿼리 개선 사항은 다음에 대해 쿼리를 실행할 때 적용할 수 있습니다.
>
> * Azure Cosmos DB 분석 저장소 컨테이너에서 만든 Spark 데이터 프레임 및
> * Azure Cosmos DB 분석 저장소 컨테이너를 가리키는 Spark 테이블입니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* Azure Cosmos DB용 Azure Synapse Link의 [사용자 지정 분할이란?](custom-partitioning-analytical-store.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)
* [Microsoft Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)
