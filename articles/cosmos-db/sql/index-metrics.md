---
title: Azure Cosmos DB 인덱싱 메트릭
description: Azure Cosmos DB에서 인덱싱 메트릭을 얻고 해석 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/24/2021
ms.author: tisande
ms.openlocfilehash: b9eea7016da1e96e51e17d513ccbc3ca240c6e3d
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236962"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 메트릭 인덱싱
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 사용 되는 인덱싱된 경로 및 권장 되는 인덱싱된 경로를 모두 표시 하는 인덱싱 메트릭을 제공 합니다. 인덱싱 메트릭을 사용 하 여 쿼리 성능을 최적화할 수 있습니다. 특히 [인덱싱 정책을](../index-policy.md)수정 하는 방법을 모르는 경우에 해당 합니다.

> [!NOTE]
> 인덱싱 메트릭은 .NET SDK (버전 3.21.0 이상) 및 Java SDK (버전 4.19.0 이상) 에서만 지원 됩니다.

## <a name="enable-indexing-metrics"></a>인덱싱 메트릭 사용

속성을로 설정 하 여 쿼리에 대 한 인덱싱 메트릭을 사용 하도록 설정할 수 있습니다 `PopulateIndexMetrics` `true` . 지정 하지 않으면 기본적으로 `PopulateIndexMetrics` 로 설정 `false` 됩니다. 인덱스 메트릭을 사용 하 여 쿼리 성능 문제를 해결 하는 것이 좋습니다. 쿼리 및 인덱싱 정책이 동일 하 게 유지 되는 한 인덱스 메트릭이 변경 될 가능성이 적습니다. 대신 진단 로그를 사용 하 여 쿼리 및 대기 시간을 모니터링 하 여 비용이 많이 드는 쿼리를 식별 하는 것이 좋습니다.

### <a name="net-sdk-example"></a>.NET SDK 예제

```csharp
    string sqlQuery = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    List<Result> results = new List<Result>(); //Individual Benchmark results

    QueryDefinition query = new QueryDefinition(sqlQuery);

    FeedIterator<Item> resultSetIterator = exampleApp.container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    double requestCharge = 0;
    tring indexMetrics = "";

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
            response = await resultSetIterator.ReadNextAsync();
            requestCharge = requestCharge + response.RequestCharge;

            if (indexMetrics != "")
                {
                    indexMetrics = response.IndexMetrics;
                }
        }

    Console.WriteLine(response.IndexMetrics);
    Console.WriteLine($"RU charge: " + response.RequestCharge);
```

### <a name="example-output"></a>예제 출력

이 예제 쿼리에서는 사용 된 경로 `/Item/?` 와 `/Price/?` 잠재적 복합 인덱스를 관찰 합니다 `(/Item ASC, /Price ASC)` .

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>사용한 인덱싱된 경로

사용 된 단일 인덱스 및 사용 된 복합 인덱스는 각각 쿼리에 사용 되는 포함 된 경로 및 복합 인덱스를 표시 합니다. 쿼리에서는 여러 개의 인덱싱된 경로 뿐만 아니라 포함 된 경로와 복합 인덱스를 함께 사용할 수 있습니다. 인덱싱된 경로가 사용 되는 것으로 표시 되지 않으면 인덱싱된 경로를 제거 해도 쿼리 성능에 영향을 주지 않습니다.

사용 되는 인덱싱된 경로 목록을 쿼리에서 해당 경로를 사용 하는 증거로 간주 합니다. 새 인덱싱된 경로를 사용 하 여 쿼리 성능을 향상 시킬 수 있는 경우 새 인덱싱된 경로를 추가 하 고 쿼리에서 해당 경로를 사용 하는지 확인 해야 합니다.

## <a name="potential-indexed-paths"></a>잠재적으로 인덱싱된 경로

잠재적 단일 인덱스 및 사용 되는 복합 인덱스는 추가 될 경우 쿼리를 활용할 수 있는 포함 된 경로와 복합 인덱스를 각각 표시 합니다. 잠재적으로 인덱싱된 경로가 표시 되는 경우 인덱싱 정책에 추가 하는 것이 좋습니다 .이를 통해 쿼리 성능이 향상 되는지 확인 해야 합니다.

쿼리에서 특정 인덱싱된 경로를 사용 하는 결정적인 증거 대신 권장 사항으로 가능한 인덱싱된 경로 목록을 고려 합니다. 잠재적으로 인덱싱된 경로는 쿼리에서 사용할 수 있는 인덱싱된 경로의 완전 한 목록이 아닙니다. 또한 일부 잠재적인 인덱싱된 경로가 쿼리 성능에 영향을 주지 않을 수 있습니다. [권장 되는 인덱싱된 경로를 추가](how-to-manage-indexing-policy.md) 하 고 쿼리 성능이 개선 되는지 확인 합니다.

## <a name="index-impact-score"></a>인덱스 영향 점수

인덱스 영향 점수는 쿼리 셰이프를 기반으로 하는 인덱싱된 경로가 쿼리 성능에 상당한 영향을 줄 가능성이 있습니다. 즉, 인덱스 영향 점수는 특정 한 인덱싱된 경로 없이 쿼리 및 비용이 크게 증가 하는 확률입니다. 

인덱스 영향 점수는 **높음** 및 **낮음** 의 두 가지로 가능 합니다. 인덱스 경로가 여러 개 있는 경우 **큰 영향 점수를 사용** 하 여 인덱싱된 경로에 집중 하는 것이 좋습니다.

인덱스 영향 점수에 사용 되는 유일한 기준은 쿼리 모양입니다. 예를 들어 아래 쿼리에서 인덱싱된 경로에는 `/name/?` **높은** 인덱스 영향 점수가 할당 됩니다.

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

데이터의 특성에 따라 실제 영향을 받습니다. 필터와 일치 하는 항목이 몇 개만 있는 경우 `/name` 인덱싱된 경로를 통해 쿼리를 크게 향상 시킬 수 있습니다. 그러나 대부분의 항목이 필터와 일치 하는 경우에는 `/name` 인덱싱된 경로를 통해 쿼리 성능이 향상 되지 않을 수 있습니다. 이러한 각 경우에 인덱싱된 경로에는 `/name/?` 쿼리 셰이프를 기반으로 하 여 쿼리 성능이 향상 될 가능성이 높기 때문에 인덱스에 **높은** 인덱스 영향 점수가 할당 됩니다.

## <a name="additional-examples"></a>추가 예

### <a name="example-query"></a>예제 쿼리

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>인덱스 메트릭

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
이러한 인덱스 메트릭은 쿼리가 인덱싱된 경로,, 및를 사용 하는 것을 보여 줍니다 `/name/?` `/age/?` `/town/?` `/timestamp/?` . 인덱스 메트릭은 복합 인덱스 (ASC)를 추가 하 여 `/name` `(/town ASC, /age ASC)` 성능을 향상 시킬 가능성이 높다는 것을 나타내기도 합니다 `(/name ASC, /town ASC, /timestamp ASC)` .

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](../index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
