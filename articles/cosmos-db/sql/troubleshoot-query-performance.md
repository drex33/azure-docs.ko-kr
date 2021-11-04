---
title: Azure Cosmos DB 사용 시 문제 해결
description: Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 52b5e16bbe04ca90668410980fc6fff36d9d6b3f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011506"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB 사용 시 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB의 쿼리 문제를 해결하기 위한 일반적인 권장 방법을 안내합니다. 이 문서에 설명된 단계가 잠재적 쿼리 문제에 대한 완전한 방어로 간주할 수는 없지만 여기에는 가장 일반적인 성능 팁이 포함되어 있습니다. 이 문서를 사용하여 Azure Cosmos DB Core(SQL) API에서 속도가 느리거나 비용이 많이 드는 쿼리 문제를 해결할 수 있습니다. 또한 [진단 로그](../cosmosdb-monitor-resource-logs.md)를 사용하여 속도가 느리거나 상당한 처리량을 사용하는 쿼리를 식별할 수도 있습니다. Azure Cosmos DB의 API for MongoDB를 사용하는 경우 [Azure Cosmos DB의 API for MongoDB 쿼리 문제 해결 가이드](../mongodb/troubleshoot-query-performance.md)를 사용해야 합니다.

Azure Cosmos DB의 쿼리 최적화는 다음과 같이 광범위하게 분류됩니다.

- 쿼리의 RU(요청 단위) 비용을 낮추는 최적화
- 단지 대기 시간을 단축하는 최적화

쿼리의 RU 비용을 낮출 경우 일반적으로 대기 시간도 단축됩니다.

이 문서에서는 [영양 데이터 세트](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)를 사용하여 다시 만들 수 있는 예제를 제공합니다.

## <a name="common-sdk-issues"></a>일반적인 SDK 문제

이 가이드를 읽기 전에 쿼리 엔진과 관련이 없는 일반적인 SDK 문제를 고려하는 것이 좋습니다.

- 이러한 [SDK 성능 팁](performance-tips.md)을 따르세요.
    - [.NET SDK 문제 해결 가이드](troubleshoot-dot-net-sdk.md)
    - [Java SDK 문제 해결 가이드](troubleshoot-java-sdk-v4-sql.md)
- SDK를 사용하여 쿼리에 대한 `MaxItemCount`를 설정할 수 있지만 최소 항목 수를 지정할 수는 없습니다.
    - 코드는 0부터 `MaxItemCount`까지 모든 페이지 크기를 처리해야 합니다.
- 이후 페이지에 결과가 있더라도 간혹 쿼리에 빈 페이지가 있을 수 있습니다. 그 이유는 다음과 같습니다.
    - SDK에서 여러 네트워크 호출을 수행할 수 있습니다.
    - 쿼리가 문서를 검색하는 데 시간이 오래 걸릴 수 있습니다.
- 모든 쿼리에는 쿼리가 지속되도록 허용하는 연속 토큰이 있습니다. 쿼리를 완전히 드레이닝해야 합니다. [여러 페이지의 결과 처리](sql-query-pagination.md#handling-multiple-pages-of-results)에 대한 자세한 정보

## <a name="get-query-metrics"></a>쿼리 메트릭 가져오기

Azure Cosmos DB에서 쿼리를 최적화하는 경우 첫 번째 단계는 항상 쿼리에 대한 [쿼리 메트릭을 가져오는](profile-sql-api-query.md) 것입니다. 이러한 메트릭은 Azure Portal에서도 사용할 수 있습니다. 데이터 탐색기에서 쿼리를 실행하면 **결과** 탭 옆에 쿼리 메트릭이 표시됩니다.

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="쿼리 메트릭 가져오기" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

쿼리 메트릭을 가져온 후 쿼리의 **검색된 문서 수** 를 **출력 문서 수** 와 비교합니다. 이 비교를 사용하여 이 문서에서 검토할 관련 섹션을 식별할 수 있습니다.

**검색된 문서 수** 는 쿼리 엔진이 로드해야 한 문서 수입니다. **출력 문서 수** 는 쿼리 결과에 필요한 문서 수입니다. **검색된 문서 수** 가 **출력 문서 수** 보다 훨씬 큰 경우 적어도 쿼리의 일부가 검색을 수행하는 데 필요한 인덱스를 사용할 수 없었던 것입니다.

시나리오와 관련된 쿼리 최적화를 이해하려면 다음 섹션을 참조하십시오.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 RU 요금이 너무 높음

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>검색된 문서 수가 출력 문서 수보다 크게 많음

- [인덱싱 정책에 필요한 경로를 포함합니다.](#include-necessary-paths-in-the-indexing-policy)

- [인덱스를 사용하는 시스템 함수를 파악합니다.](#understand-which-system-functions-use-the-index)

- [문자열 시스템 함수 실행을 개선합니다.](#improve-string-system-function-execution)

- [인덱스를 사용하는 집계 쿼리를 파악합니다.](#understand-which-aggregate-queries-use-the-index)

- [필터와 ORDER BY 절을 모두 포함하는 쿼리를 최적화합니다.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [하위 쿼리를 사용하여 JOIN 식을 최적화합니다.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색된 문서 수와 출력 문서 수가 거의 동일함

- [파티션 간 쿼리를 최소화합니다.](#minimize-cross-partition-queries)

- [여러 속성에 대한 필터가 있는 쿼리를 최적화합니다.](#optimize-queries-that-have-filters-on-multiple-properties)

- [필터와 ORDER BY 절을 모두 포함하는 쿼리를 최적화합니다.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 RU 요금은 감수할만한 수준이지만 대기 시간이 너무 높음

- [근접성을 개선합니다.](#improve-proximity)

- [프로비전된 처리량을 늘립니다.](#increase-provisioned-throughput)

- [MaxConcurrency를 늘립니다.](#increase-maxconcurrency)

- [MaxBufferedItemCount를 늘립니다.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색된 문서 수가 출력 문서 수를 초과하는 쿼리

 **검색된 문서 수** 는 쿼리 엔진이 로드해야 한 문서 수입니다. **출력 문서 수** 은 쿼리에서 반환된 문서 수입니다. **검색된 문서 수** 가 **출력 문서 수** 보다 훨씬 큰 경우 적어도 쿼리의 일부가 검색을 수행하는 데 필요한 인덱스를 사용할 수 없었던 것입니다.

다음은 인덱스에 의해 완전히 처리되지 않은 검색 쿼리의 예입니다.

쿼리:

```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

쿼리 메트릭:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**검색된 문서 수**(60,951)가 **출력 문서 수**(7)보다 훨씬 크며 이는 쿼리 결과가 문서 검색임을 의미합니다. 이 경우 시스템 함수 [UPPER()](sql-query-upper.md)는 인덱스를 사용하지 않습니다.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>인덱싱 정책에 필요한 경로를 포함

인덱싱 정책은 `WHERE` 절, `ORDER BY` 절, `JOIN` 및 대부분의 시스템 함수에 포함된 모든 속성을 커버해야 합니다. 인덱스 정책에 지정된 원하는 경로는 JSON 문서의 속성과 일치해야 합니다.

> [!NOTE]
> Azure Cosmos DB 인덱싱 정책의 속성은 대/소문자를 구분합니다.

[영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 세트에 대해 다음과 같은 간단한 쿼리를 실행하는 경우 `WHERE` 절에서 속성이 인덱싱될 때 훨씬 더 낮은 RU 요금이 발생합니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 요금:** 409.51RU

#### <a name="optimized"></a>최적화됨

업데이트된 인덱싱 정책:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 요금:** 2.98RU

쓰기 또는 읽기 가능성에 영향을 주지 않고 언제든지 인덱싱 정책에 속성을 추가할 수 있습니다. [인덱스 변환 진행률을 추적](./how-to-manage-indexing-policy.md#dotnet-sdk)할 수 있습니다.

### <a name="understand-which-system-functions-use-the-index"></a>인덱스를 사용하는 시스템 함수를 파악

대부분의 시스템 함수는 인덱스를 사용합니다. 인덱스를 사용하는 몇 가지 일반적인 문자열 함수 목록은 다음과 같습니다.

- StartsWith
- 포함
- RegexMatch
- 왼쪽
- Substring - 첫 번째 num_expr이 0인 경우에만 해당

`WHERE` 절에 사용될 때 인덱스를 사용하지 않고 각 문서를 로드해야 하는 몇 가지 일반적인 시스템 함수는 다음과 같습니다.

| **시스템 함수**                     | **최적화 아이디어**             |
| --------------------------------------- |------------------------------------------------------------ |
| 상/하                         | 시스템 함수를 사용하여 비교를 위해 데이터를 정규화하는 대신 삽입 시 대/소문자를 정규화합니다. ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```과 같은 쿼리는 ```SELECT * FROM c WHERE c.name = 'BOB'```이 됩니다. |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | 쿼리 실행 전 현재 시간을 계산하고 `WHERE` 절에서 해당 문자열 값을 사용합니다. |
| 수학 함수(비집계) | 쿼리에서 값을 자주 계산해야 하는 경우 JSON 문서에 속성으로 값을 저장하는 것이 좋습니다. |

이러한 시스템 함수는 집계가 있는 쿼리에 사용되는 경우를 제외하고 인덱스를 사용할 수 있습니다.

| **시스템 함수**                     | **최적화 아이디어**             |
| --------------------------------------- |------------------------------------------------------------ |
| 공간 시스템 함수                        | 실시간 구체화된 뷰에 쿼리 결과 저장 |

`SELECT` 절에서 사용되는 경우 비효율적인 시스템 함수는 쿼리에서 인덱스를 사용하는 방법에 영향을 주지 않습니다.

### <a name="improve-string-system-function-execution"></a>문자열 시스템 함수 실행 개선

인덱스를 사용하는 일부 시스템 함수의 경우 쿼리에 `ORDER BY` 절을 추가하여 쿼리 실행을 개선할 수 있습니다. 

더 구체적으로, 속성의 카디널리티에 따라 RU 요금이 증가하는 시스템 함수는 쿼리에 `ORDER BY`를 포함하는 것이 유리할 수 있습니다. 이러한 쿼리는 인덱스 검색을 수행하므로 쿼리 결과를 정렬하여 쿼리의 효율성을 높일 수 있습니다.

이러한 최적화는 다음과 같은 시스템 함수의 실행을 향상시킬 수 있습니다.

- StartsWith(대/소문자 구분 안 함 = true)
- StringEquals(대/소문자 구분 안 함 = true)
- 포함
- RegexMatch
- EndsWith

예를 들어 `CONTAINS`를 사용하는 아래 쿼리를 고려하세요. `CONTAINS`는 인덱스를 사용하지만 경우에 따라 관련 인덱스를 추가한 후에도 아래 쿼리를 실행할 때 여전히 매우 높은 RU 요금이 발생할 수 있습니다.

원래 쿼리:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

`ORDER BY`를 추가하여 쿼리 실행을 향상시킬 수 있습니다.

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

추가 필터가 있는 쿼리에서는 동일한 최적화를 사용할 수 있습니다. 이 경우 같음 필터가 있는 속성도 `ORDER BY` 절에 추가하는 것이 좋습니다.

원래 쿼리:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

(c.name, c.town)에 대한 `ORDER BY` 및 [복합 인덱스](../index-policy.md#composite-indexes)를 추가하여 쿼리 실행을 개선할 수 있습니다.

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>인덱스를 사용하는 집계 쿼리를 파악

대부분의 경우 Azure Cosmos DB 집계 시스템 함수는 인덱스를 사용합니다. 그러나 집계 쿼리의 필터 또는 추가 절에 따라 쿼리 엔진이 많은 수의 문서를 로드해야 할 수 있습니다. 일반적으로 쿼리 엔진은 같음 및 범위 필터를 먼저 적용합니다. 쿼리 엔진은 이러한 필터를 적용한 후 추가 필터를 평가하고 필요한 경우 집계를 계산하기 위해 나머지 문서를 로드할 수 있습니다.

예를 들어 다음 두 개의 샘플 쿼리가 있는 경우 같음 및 `CONTAINS` 시스템 함수 필터를 모두 포함하는 쿼리는 일반적으로 `CONTAINS` 시스템 함수 필터만 포함하는 쿼리보다 효율적입니다. 이는 비용이 더 많이 드는 `CONTAINS` 필터를 위해 문서를 로드하기 전에 같음 필터가 먼저 적용되고 인덱스를 사용하기 때문입니다.

`CONTAINS` 필터만 포함하는 쿼리 - 더 높은 RU 요금:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

같음 필터와 `CONTAINS` 필터를 모두 포함하는 쿼리 - 더 낮은 RU 요금:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

인덱스를 완전하게 사용하지 않을 집계 쿼리의 추가 예는 다음과 같습니다.

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>인덱스를 사용하지 않는 시스템 함수를 포함하는 쿼리

관련 [시스템 함수의 페이지](sql-query-system-functions.md)를 참조하여 인덱스를 사용하는지 확인해야 합니다.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>UDF(사용자 정의 함수)를 포함하는 집계 쿼리

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>GROUP BY를 포함하는 쿼리

`GROUP BY`를 포함하는 쿼리의 RU 비용은 `GROUP BY` 절에 있는 속성의 카디널리티가 증가함에 따라 커집니다. 예를 들어 아래 쿼리에서는 고유 설명 수가 증가함에 따라 쿼리의 RU 요금이 비싸집니다.

`GROUP BY` 절을 포함하는 집계 함수의 RU 비용은 집계 함수만의 RU 비용보다 큽니다. 다음 예제에서 쿼리 엔진은 `c.foodGroup = "Sausages and Luncheon Meats"` 필터와 일치하는 모든 문서를 로드해야 합니다. 그러므로 높은 RU 비용이 예상됩니다.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

동일한 집계 쿼리를 자주 실행하려는 경우 개별 쿼리를 실행하는 것보다 [Azure Cosmos DB 변경 피드](../change-feed.md)를 사용하여 실시간 구체화된 뷰를 작성하는 것이 더 효율적일 수 있습니다.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>필터와 ORDER BY 절을 모두 포함하는 쿼리를 최적화

필터와 `ORDER BY` 절이 있는 쿼리는 일반적으로 범위 인덱스를 사용하지만 복합 인덱스를 사용할 수 있다면 더 효율적입니다. 인덱싱 정책을 수정하는 것 외에도 복합 인덱스의 모든 속성을 `ORDER BY` 절에 추가해야 합니다. 이렇게 쿼리를 변경하면 쿼리가 복합 인덱스를 사용하게 됩니다.  [영양](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 데이터 세트에 쿼리를 실행하여 영향을 관찰할 수 있습니다.

#### <a name="original"></a>Original

쿼리:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

인덱싱 정책:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 요금:** 44.28RU

#### <a name="optimized"></a>최적화됨

업데이트된 쿼리(`ORDER BY` 절에 두 속성을 포함):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

업데이트된 인덱싱 정책:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 요금:** 8.86RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>하위 쿼리를 사용하여 JOIN 식을 최적화

다중 값 하위 쿼리는 `WHERE` 절의 모든 크로스 조인 뒤가 아니라 각 select-many 식 뒤에 조건자를 푸시하여 `JOIN` 식을 최적화할 수 있습니다.

다음 쿼리를 살펴보십시오.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 요금:** 167.62RU

이 쿼리의 경우 인덱스는 이름 `infant formula`를 포함하는 태그가 있고, `nutritionValue`가 0보다 크고, `amount`가 1보다 큰 모든 문서를 일치시킵니다. 여기에서 `JOIN` 식은 필터를 적용하기 전에 일치하는 각 문서에 대해 태그, 영양소 및 1회 용량 배열의 모든 항목에 대한 교차곱을 수행합니다. 그런 다음 `WHERE` 절은 각 `<c, t, n, s>` 튜플에서 필터 조건자를 적용합니다.

예를 들어, 일치하는 문서가 3개의 배열에 각각 10개의 항목이 있는 경우 이는 1 x 10 x 10 x 10(즉, 1,000) 튜플로 확장됩니다. 여기서 하위 쿼리를 사용하여 다음 식으로 조인하기 전에 조인된 배열 항목을 필터링할 수 있습니다.

다음 쿼리는 위의 쿼리와 동일하지만 하위 쿼리를 사용합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 요금:** 22.17RU

태그 배열의 한 항목만 필터와 일치하고 영양소 및 1회 용량 배열 모두 5개 항목이 있다고 가정합니다. `JOIN` 식은 첫 번째 쿼리의 1,000개 항목과 달리 1 x 1 x 5 x 5 = 25개 항목으로 확장됩니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색된 문서 수와 출력 문서 수가 동일한 쿼리

**검색된 문서 수** 와 **출력 문서 수** 가 거의 같으면 쿼리 엔진은 불필요하게 많은 문서를 검색할 필요가 없습니다. `TOP` 키워드를 사용하는 쿼리를 비롯한 많은 쿼리에서 **검색된 문서 수** 는 **출력 문서 수** 보다 1이 클 수 있습니다. 이에 대해서는 신경 쓰지 않아도 됩니다.

### <a name="minimize-cross-partition-queries"></a>파티션 간 쿼리를 최소화

Azure Cosmos DB는 요청 단위 및 데이터 스토리지 증가가 필요한 경우 [분할](../partitioning-overview.md)을 사용하여 개별 컨테이너를 확장합니다. 각 실제 파티션에는 별도의 독립된 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인하면 됩니다. 이러한 최적화를 통해 쿼리에 필요한 총 RU 수가 줄어듭니다.

프로비전된 RU(30,000개 초과)가 많거나 대용량의 데이터가 저장되어 있는 경우(약 100GB 초과) 컨테이너 크기가 충분히 크다면 쿼리 RU 비용을 크게 줄일 수 있습니다.

예를 들어 파티션 키 foodGroup으로 컨테이너를 만드는 경우 다음 쿼리는 실제 파티션만 확인하면 됩니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

파티션 키를 사용하는 `IN` 필터가 있는 쿼리는 관련 실제 파티션만 확인하고 "팬아웃"하지 않습니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

파티션 키에 대해 범위 필터를 포함하거나 파티션 키에 필터가 없는 쿼리는 "팬아웃"하고 모든 실제 파티션의 인덱스에서 결과를 확인해야 합니다.

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>여러 속성에 대한 필터가 있는 쿼리를 최적화

여러 속성에 대한 필터가 있는 쿼리는 일반적으로 범위 인덱스를 사용하지만 복합 인덱스를 사용할 수 있으면 더 효율적입니다. 소량의 데이터에서는 이러한 최적화가 별 영향을 주지 않습니다. 그러나 대량의 데이터에서는 유용할 수 있습니다. 복합 인덱스당 하나의 같지 않음 필터만 최적화할 수 있습니다. 쿼리에 같지 않음 필터가 여러 개 있는 경우 복합 인덱스를 사용할 필터를 하나 선택합니다. 나머지는 범위 인덱스를 계속 사용합니다. 같지 않음 필터는 복합 인덱스에서 마지막으로 정의되어야 합니다. [복합 인덱스에 대해 자세히 알아보세요](../index-policy.md#composite-indexes).

다음은 복합 인덱스를 사용하여 최적화할 수 있는 쿼리의 몇 가지 예입니다.

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

다음은 관련 복합 인덱스입니다.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>쿼리 대기 시간을 줄이는 최적화

많은 경우에 RU 요금은 감수할만한 수준이지만 쿼리 대기 시간은 매우 높을 수 있습니다. 다음 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁을 개략적으로 설명합니다. 동일한 쿼리를 동일한 데이터 세트에서 여러 번 실행하는 경우 매번 RU 요금은 일반적으로 동일합니다. 하지만 쿼리 대기 시간은 쿼리 실행마다 다를 수 있습니다.

### <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정이 아닌 다른 지역에서 실행되는 쿼리는 동일한 지역 내에서 실행된 경우 보다 대기 시간이 더 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내의 가상 머신에서 나온 것보다 수십 또는 수백 밀리초(또는 그 이상) 높은 대기 시간을 예상해야 합니다. 데이터를 앱에 더 가깝게 가져올 수 있도록 [Azure Cosmos DB 데이터를 전 세계적으로 배포](../distribute-data-globally.md)하는 것은 간단합니다.

### <a name="increase-provisioned-throughput"></a>프로비전된 처리량 증가

Azure Cosmos DB에서 프로비전된 처리량은 RU(요청 단위)로 측정됩니다. 5RU의 처리량을 사용하는 쿼리가 있다고 가정합니다. 예를 들어 1,000RU를 프로비전하는 경우 해당 쿼리를 초당 200번 실행할 수 있습니다. 사용 가능한 처리량이 충분하지 않은 경우 쿼리를 실행하려고 하면 Azure Cosmos DB가 HTTP 429 오류를 반환합니다. 모든 최신 Core(SQL) API SDK는 잠시 기다린 후 이 쿼리를 자동으로 다시 시도합니다. 제한된 요청은 시간이 오래 걸리므로 프로비전된 처리량을 늘리면 쿼리 대기 시간이 향상될 수 있습니다. Azure Portal의 **메트릭** 블레이드에서 [총 제한 요청 수](../use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)를 확인할 수 있습니다.

### <a name="increase-maxconcurrency"></a>MaxConcurrency 증가

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 순차적으로 가져옵니다. 따라서 MaxConcurrency를 파티션 수로 설정하면 다른 모든 시스템 조건을 동일하게 유지하는 경우 가장 성능이 뛰어난 쿼리를 달성할 수 있습니다. 파티션 수를 모르는 경우 MaxConcurrency(또는 이전 SDK 버전의 MaxDegreesOfParallelism)를 높게 설정할 수 있습니다. 시스템은 최대 병렬 처리 수준으로 최소값(파티션 수, 사용자 제공 입력)을 선택합니다.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount 증가

결과의 현재 배치가 클라이언트에서 처리되는 반면 쿼리는 결과를 프리페치하도록 설계되었습니다. 프리페치를 사용하면 쿼리의 전체 대기 시간을 향상시킬 수 있습니다. MaxBufferedItemCount를 설정하면 프리페치된 결과의 수가 제한됩니다. 이 값을 예상되는 반환 결과 수(또는 더 높은 수)로 설정하면 쿼리는 프리페치를 통해 가장 많은 이점을 얻을 수 있습니다. 이 값을 -1로 설정하면 시스템이 버퍼링할 항목 수를 자동으로 결정합니다.

## <a name="next-steps"></a>다음 단계
쿼리당 RU를 측정하고 쿼리를 튜닝하기 위한 실행 통계를 가져오는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [.NET SDK를 사용하여 SQL 쿼리 실행 메트릭 가져오기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](./sql-api-query-metrics.md)
* [.NET SDK용 성능 팁](performance-tips.md)
* [Java v4 SDK용 성능 팁](performance-tips-java-sdk-v4-sql.md)