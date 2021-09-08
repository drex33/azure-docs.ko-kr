---
title: MongoDB용 Azure Cosmos DB API를 사용하는 경우 쿼리 문제 해결
description: MongoDB용 Azure Cosmos DB의 API 쿼리 문제를 식별, 진단 및 해결하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.reviewer: sngun
ms.openlocfilehash: 90641a3ecbf7d09e9ecdc1101d97cedbe64b893e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039504"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>MongoDB용 Azure Cosmos DB API를 사용하는 경우 쿼리 문제 해결
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 문서에서는 Azure Cosmos DB의 쿼리 문제를 해결하기 위한 일반적인 권장 방법을 안내합니다. 이 문서에 설명된 단계가 잠재적 쿼리 문제에 대한 완전한 방어로 간주할 수는 없지만 여기에는 가장 일반적인 성능 팁이 포함되어 있습니다. 이 문서를 사용하여 MongoDB용 Azure Cosmos DB의 API에서 속도가 느리거나 비용이 많이 드는 쿼리 문제를 해결을 시작할 수 있습니다. Azure Cosmos DB core(SQL) API를 사용하는 경우 [SQL API 쿼리 문제 해결 가이드](troubleshoot-query-performance.md) 문서를 참조하세요.

Azure Cosmos DB의 쿼리 최적화는 광범위하게 다음과 같이 분류됩니다.

- 쿼리의 RU(요청 단위) 비용을 낮추는 최적화
- 단지 대기 시간을 단축하는 최적화

쿼리의 RU 비용을 낮출 경우 일반적으로 대기 시간도 단축됩니다.

이 문서에서는 [영양 데이터 세트](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)를 사용하여 다시 만들 수 있는 예제를 제공합니다.

> [!NOTE] 
> 이 문서에서는 버전 3.6 이상인 MongoDB용 Azure Cosmos DB의 API 계정을 사용하고 있다고 가정합니다. 버전 3.2에서 성능이 저하되는 일부 쿼리는 버전 3.6 이상에서 크게 개선되었습니다. [지원 요청](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출하여 버전 3.6으로 업그레이드합니다.

## <a name="use-explain-command-to-get-metrics"></a>$explain 명령을 사용하여 메트릭 가져오기

Azure Cosmos DB에서 쿼리를 최적화하는 경우 첫 번째 단계는 항상 쿼리에 대한 [RU 비용을 가져오는](find-request-unit-charge-mongodb.md) 것입니다. 대략적인 지침은 50 RU 이상의 요금이 부과된 쿼리에 대해 RU 비용을 낮추는 방법을 살펴보는 것입니다. 

RU 비용을 가져오는 것 외에도 `$explain` 명령을 사용하여 쿼리 및 인덱스 사용 현황 메트릭을 가져와야 합니다. 다음은 쿼리를 실행하고 `$explain` 명령을 사용하여 쿼리 및 인덱스 사용 현황 메트릭을 표시하는 예입니다.

**$explain 명령:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**출력:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain` 명령 출력은 길고 쿼리 실행에 대한 자세한 정보를 포함합니다. 하지만 일반적으로 쿼리 성능을 최적화할 때 중점을 두어야 하는 몇 가지 섹션이 있습니다.

| 메트릭 | Description | 
| ------ | ----------- |
| `timeInclusiveMS` | 백 엔드 쿼리 대기 시간 |
| `pathsIndexed` | 쿼리에서 사용한 인덱스 표시 | 
| `pathsNotIndexed` | 쿼리에서 사용할 수 있는 인덱스 표시(사용 가능한 경우) | 
| `shardInformation` | 특정 [물리적 파티션](../partitioning-overview.md#physical-partitions)의 쿼리 성능 요약 | 
| `retrievedDocumentCount` | 쿼리 엔진에서 로드한 문서 수 | 
| `outputDocumentCount` | 쿼리 결과로 반환된 문서 수 | 
| `estimatedDelayFromRateLimitingInMilliseconds` | 요금 제한으로 인한 예상 추가 쿼리 대기 시간 | 

쿼리 메트릭을 가져온 후 쿼리의 `retrievedDocumentCount`를 `outputDocumentCount`와 비교합니다. 이 비교를 사용하여 이 문서에서 검토할 관련 섹션을 식별할 수 있습니다. `retrievedDocumentCount`는 쿼리 엔진이 로드해야 하는 문서 수입니다. `outputDocumentCount`는 쿼리 결과에 필요한 문서 수입니다. `retrievedDocumentCount`가 `outputDocumentCount`보다 훨씬 큰 경우 한 부분 이상의 쿼리가 검색을 수행하는 데 필요한 인덱스를 사용할 수 없었던 것입니다.

시나리오와 관련된 쿼리 최적화를 이해하려면 다음 섹션을 참조하십시오.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 RU 요금이 너무 높음

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>검색된 문서 수가 출력 문서 수보다 크게 많음

- [필요한 인덱스를 포함합니다.](#include-necessary-indexes)

- [인덱스를 사용하는 집계 작업을 파악합니다.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색된 문서 수와 출력 문서 수가 거의 동일함

- [파티션 간 쿼리를 최소화합니다.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 RU 요금은 감수할만한 수준이지만 대기 시간이 너무 높음

- [근접성을 개선합니다.](#improve-proximity)

- [프로비전된 처리량을 늘립니다.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색된 문서 수가 출력 문서 수를 초과하는 쿼리

 `retrievedDocumentCount`는 쿼리 엔진이 로드해야 한 문서 수입니다. `outputDocumentCount`은 쿼리에서 반환된 문서 수입니다. `retrievedDocumentCount`가 `outputDocumentCount`보다 훨씬 큰 경우 적어도 쿼리의 일부가 검색을 수행하는 데 필요한 인덱스를 사용할 수 없었던 것입니다.

다음은 인덱스에 의해 완전히 처리되지 않은 검색 쿼리의 예입니다.

**$explain 명령:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**출력:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount`(8618)는 `outputDocumentCount`(1)보다 상당히 높으므로 이 쿼리에 문서 검색이 필요함을 나타냅니다. 

### <a name="include-necessary-indexes"></a>필요한 인덱스 포함

`pathsNotIndexed` 배열을 확인하고 이러한 인덱스를 추가해야 합니다. 이 예제에서는 및 경로 `foodGroup` 및 `description`을 인덱싱해야 합니다.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

MongoDB용 Azure Cosmos DB의 API의 인덱싱 모범 사례는 MongoDB와 다릅니다. MongoDB용 Azure Cosmos DB의 API에서 복합 인덱스는 여러 속성을 효율적으로 정렬해야 하는 쿼리에만 사용됩니다. 여러 속성에 대한 필터에 쿼리가 있는 경우 이러한 속성 각각에 대해 단일 필드 인덱스를 생성해야 합니다. 쿼리 조건자는 여러 개의 단일 필드 인덱스를 사용할 수 있습니다.

[와일드카드 인덱스](mongodb-indexing.md#wildcard-indexes)는 인덱싱을 단순화할 수 있습니다. MongoDB와 달리 와일드카드 인덱스는 쿼리 조건자의 여러 필드를 지원할 수 있습니다. 각 속성에 대한 별도의 인덱스를 만드는 대신 하나의 와일드카드 인덱스를 사용해도 쿼리 성능에는 차이가 없습니다. 모든 속성에 대해 와일드카드 인덱스를 추가하는 것이 모든 쿼리를 최적화하는 가장 쉬운 방법입니다.

쓰기 또는 읽기 가용성에 영향을 주지 않고 언제든지 새 인덱스를 추가할 수 있습니다. [인덱스 변환 진행률을 추적](../how-to-manage-indexing-policy.md#dotnet-sdk)할 수 있습니다.

### <a name="understand-which-aggregation-operations-use-the-index"></a>인덱스를 사용하는 집계 작업을 파악합니다.

대부분의 경우 MongoDB용 Azure Cosmos DB의 API 집계 작업은 인덱스를 부분적으로 사용합니다. 일반적으로 쿼리 엔진은 같음 및 범위 필터를 먼저 적용하고 인덱스를 사용합니다. 쿼리 엔진은 이러한 필터를 적용한 후 추가 필터를 평가하고 필요한 경우 집계를 계산하기 위해 나머지 문서를 로드할 수 있습니다. 

예를 들면 다음과 같습니다.

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

이 경우 인덱스는 `$match` 단계를 최적화할 수 있습니다. `foodGroup`에 대한 인덱스를 추가하면 쿼리 성능이 크게 향상됩니다. MongoDB와 마찬가지로 `$match`를 초기에 집계 파이프라인에 배치하여 인덱스 사용을 극대화해야 합니다.

MongoDB용 Azure Cosmos DB의 API에서는 인덱스가 실제 집계에 사용되지 않으며 이 경우 `$max`입니다. `version`에 인덱스를 추가하면 쿼리 성능이 향상되지 않습니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색된 문서 수와 출력 문서 수가 동일한 쿼리

`retrievedDocumentCount`와 `outputDocumentCount`가 거의 같으면 쿼리 엔진은 불필요하게 많은 문서를 검색할 필요가 없습니다.

### <a name="minimize-cross-partition-queries"></a>파티션 간 쿼리를 최소화

Azure Cosmos DB는 요청 단위 및 데이터 스토리지 증가가 필요한 경우 [분할](../partitioning-overview.md)을 사용하여 개별 컨테이너를 확장합니다. 각 실제 파티션에는 별도의 독립된 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인하면 됩니다. 이러한 최적화를 통해 쿼리에 필요한 총 RU 수가 줄어듭니다. [파티션 내 쿼리와 교차 파티션 쿼리 간의 차이점에 대해 자세히 알아봅니다](../how-to-query-container.md).

프로비전된 RU(30,000개 초과)가 많거나 대용량의 데이터가 저장되어 있는 경우(약 100GB 초과) 컨테이너 크기가 충분히 크다면 쿼리 RU 비용을 크게 줄일 수 있습니다. 

`shardInformation` 배열을 확인하여 각 개별 실제 파티션에 대한 쿼리 메트릭을 이해할 수 있습니다. 고유 `shardKeyRangeId` 값 수는 쿼리를 실행하는 데 필요한 실제 파티션 수입니다. 이 예에서는 4개의 실제 파티션에서 쿼리가 실행되었습니다. 실행은 인덱스 사용량과 완전히 독립적이라고 이해하는 것이 중요합니다. 즉, 파티션 간 쿼리는 인덱스를 계속 사용할 수 있습니다.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>쿼리 대기 시간을 줄이는 최적화

많은 경우에 RU 요금은 감수할만한 수준이지만 쿼리 대기 시간은 매우 높을 수 있습니다. 다음 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁을 개략적으로 설명합니다. 동일한 쿼리를 동일한 데이터 세트에서 여러 번 실행하는 경우 매번 RU 요금은 일반적으로 동일합니다. 하지만 쿼리 대기 시간은 쿼리 실행마다 다를 수 있습니다.

### <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정이 아닌 다른 지역에서 실행되는 쿼리는 동일한 지역 내에서 실행된 경우 보다 대기 시간이 더 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내의 가상 머신에서 나온 것보다 수십 또는 수백 밀리초(또는 그 이상) 높은 대기 시간을 예상해야 합니다. 데이터를 앱에 더 가깝게 가져올 수 있도록 [Azure Cosmos DB 데이터를 전 세계적으로 배포](tutorial-global-distribution-mongodb.md)하는 것은 간단합니다.

### <a name="increase-provisioned-throughput"></a>프로비전된 처리량 증가

Azure Cosmos DB에서 프로비전된 처리량은 RU(요청 단위)로 측정됩니다. 5RU의 처리량을 사용하는 쿼리가 있다고 가정합니다. 예를 들어 1,000RU를 프로비전하는 경우 해당 쿼리를 초당 200번 실행할 수 있습니다. 사용 가능한 처리량이 충분하지 않을 때 쿼리를 실행하려고 하면 Azure Cosmos DB가 요청 등급을 제한합니다. MongoDB용 Azure Cosmos DB의 API는 잠시 기다린 후 이 쿼리를 자동으로 다시 시도합니다. 제한된 요청은 시간이 오래 걸리므로 프로비전된 처리량을 늘리면 쿼리 대기 시간이 향상될 수 있습니다.

`estimatedDelayFromRateLimitingInMilliseconds` 값은 처리량을 늘리는 경우 잠재적 대기 시간 혜택을 의미합니다.

## <a name="next-steps"></a>다음 단계

* [쿼리 성능 문제 해결(SQL API)](troubleshoot-query-performance.md)
* [Azure Cosmos DB의 API for MongoDB에서 인덱싱 관리](mongodb-indexing.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
