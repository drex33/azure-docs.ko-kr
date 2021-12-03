---
title: 분석 저장소 데이터를 분할 하도록 사용자 지정 분할 구성 (미리 보기)
description: Azure Cosmos DB에 대 한 azure Synapse 링크를 사용 하 여 azure Synapse Spark 노트북에서 사용자 지정 분할을 트리거하는 방법을 알아봅니다. 구성 옵션을 설명 합니다.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: b564743c01656a5adc9b131b2fc3ca89c316667f
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517265"
---
# <a name="configure-custom-partitioning-to-partition-analytical-store-data-preview"></a>분석 저장소 데이터를 분할 하도록 사용자 지정 분할 구성 (미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

사용자 지정 분할을 사용 하면 분석 쿼리에서 자주 사용 되는 필드에 분석 저장소 데이터를 분할할 수 있으므로 쿼리 성능이 향상 됩니다.
사용자 지정 분할에 대 한 자세한 내용은 [사용자 지정 분할 이란?](custom-partitioning-analytical-store.md) 문서를 참조 하세요.

사용자 지정 분할을 사용 하려면 Azure Cosmos DB 계정에서 Azure Synapse 링크를 사용 하도록 설정 해야 합니다. 자세히 알아보려면 [Azure Synapse를 구성 하는 방법 링크](configure-synapse-link.md)를 참조 하세요. Azure Cosmos DB에 대 한 azure Synapse 링크를 사용 하 여 azure Synapse Spark 노트북에서 사용자 지정 분할 실행을 트리거할 수 있습니다.

> [!IMPORTANT]
> 사용자 지정 분할 기능은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> Azure Cosmos DB 계정은 사용자 지정 분할을 활용 하기 위해 Azure Synapse Link를 사용 하도록 설정 해야 합니다. 사용자 지정 분할은 현재 Azure Synapse Spark 2.0에만 지원 됩니다.

## <a name="trigger-a-custom-partitioning-job"></a>사용자 지정 분할 작업 트리거

Azure Synapse 링크를 사용 하 여 Azure Synapse Spark 노트북에서 분할을 트리거할 수 있습니다. 백그라운드 작업으로 실행 되도록 예약 하거나 하루에 한 번 또는 두 번 실행할 수 있습니다. 또는 필요한 경우 자주 실행 될 수 있습니다.  데이터 집합에서 하나 이상의 필드를 분석 저장소 파티션 키로 선택할 수도 있습니다.

다음은 사용자 지정 분할 실행을 트리거하는 데 필요한 필수 구성 옵션입니다.

* `spark.cosmos.asns.execute.partitioning` -사용자 지정 분할 실행을 트리거하는 부울 값입니다. 기본값은 false입니다.

* `spark.cosmos.asns.partition.keys` -DDL 형식 문자열을 사용 하 여 키/s를 분할 합니다. 예: *Readdate 문자열*.

* `spark.cosmos.asns.basePath` -Synapse 기본 저장소 계정의 분할 된 저장소에 대 한 기본 경로입니다.

> [!NOTE]
> 여러 파티션 키를 선택 하는 경우 키를 나타내는 basePath를 사용 하 여 동일한 분할 된 저장소에서 이러한 레코드에 액세스할 수 있습니다.

다음은 사용자 지정 분할 실행을 트리거할 때 사용할 수 있는 선택적 구성 옵션입니다.

* `spark.cosmos.asns.merge.partitioned.files` -실행 당 파티션 값 마다 단일 파일을 만들 수 있도록 하는 부울 값입니다. 기본값은 false입니다.

* `spark.cosmos.asns.partitioning.maxRecordsPerFile` -분할 된 저장소에서 단일 분할 된 파일의 최대 레코드 수입니다. 이 구성 및가 지정 된 경우 `spark.cosmos.asns.merge.partitioned.files` 레코드 수가 maxrecords Perfile 값을 초과 하면 새 파일이 생성 됩니다. 이 구성은 일반적으로 더 큰 컬렉션의 초기 분할에만 필요 합니다. 기본값은 100만입니다.

  Maxrecords Perfile을 설정 하 고 구성 하지 않으면 `spark.cosmos.asns.merge.partitioned.files` maxrecords Perfile에 도달 하기 전에 레코드를 파일 간에 분할할 수 있습니다. 파일 분할도 풀에서 사용 가능한 병렬 처리에 따라 달라 집니다.

* `spark.cosmos.asns.partitioning.shuffle.partitions` -분할 된 저장소에 분할 된 쓰기를 수행 하는 동안 병렬 처리를 제어 합니다. 이 구성은 대규모 컬렉션에 대 한 초기 분할에만 필요 합니다. Spark 풀에서 사용 가능한 코어 수로 설정 됩니다. 기본값은 200입니다. 풀이 다른 작업에 사용 되지 않는 경우 값이 낮을수록 리소스가 낭비 될 수 있습니다. 값이 높을수록 일반적으로 문제가 발생 하지 않습니다. 일부 작업은 일찍 완료 되 고 느리게 실행 되는 동안 더 많은 작업을 시작할 수 있기 때문입니다. 분할 작업을 더 빠르게 완료 하려면 풀 크기를 늘리는 것이 좋습니다.

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

## <a name="query-execution-with-partitioned-store"></a>분할 된 저장소를 사용 하 여 쿼리 실행

다음 두 configs는 분할 된 저장소를 지 원하는 쿼리를 실행 하는 데 필요 합니다.

* `spark.cosmos.asns.partition.keys`
* `spark.cosmos.asns.basePath`

다음 예에서는 이러한 configs를 사용 하 여 위의 분할 된 저장소를 쿼리 하는 방법과 파티션 키를 사용 하 여 필터링 하 여 파티션 정리를 사용 하는 방법을 보여 줍니다. 이 분할 된 저장소는 "ReadDate" 필드를 사용 하 여 분할 됩니다.

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

위의 *readdate = ' 2021-11-01 '* 필터는 실행 하는 동안 검색에서 *2021-11-01* 이 아닌 readdate 값에 해당 하는 데이터를 제거 합니다.

> [!NOTE]
> 분할 된 저장소를 사용 하 여 쿼리를 개선 하는 작업은 다음에 대해 쿼리를 실행할 때 적용할 수 있습니다.
>
> * Azure Cosmos DB 분석 저장소 컨테이너에서 만든 Spark 데이터 프레임
> * Azure Cosmos DB 분석 저장소 컨테이너를 가리키는 Spark 테이블입니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* Azure Cosmos DB에 대 한 Azure Synapse 링크의 [사용자 지정 분할](custom-partitioning-analytical-store.md) 이란?
* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)
* [Microsoft Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)
