---
title: Azure Cosmos DB 인덱싱 메트릭
description: Azure Cosmos DB에서 인덱싱 메트릭을 가져오고 해석하는 방법 알아보기
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: ed60ce6586947f59d9a6c32b08f1c50082db5077
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056711"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 메트릭 인덱싱
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 활용된 인덱싱된 경로와 권장 인덱싱된 경로를 모두 표시하는 인덱싱 메트릭을 제공합니다. 인덱싱 메트릭을 사용하여 특히 인덱싱 [정책을](../index-policy.md)수정하는 방법을 잘 모르는 경우 쿼리 성능을 최적화할 수 있습니다.

> [!NOTE]
> 인덱싱 메트릭은 .NET SDK(버전 3.21.0 이상) 및 Java SDK(버전 4.19.0 이상)에서만 지원됩니다.

## <a name="enable-indexing-metrics"></a>인덱싱 메트릭 사용

속성을 로 설정하여 쿼리에 대한 인덱싱 메트릭을 사용하도록 설정할 수 `PopulateIndexMetrics` `true` 있습니다. 지정하지 않은 경우 `PopulateIndexMetrics` 기본값은 `false` 입니다. 쿼리 성능 문제 해결을 위해 인덱스 메트릭만 사용하도록 설정하는 것이 좋습니다. 쿼리 및 인덱싱 정책이 동일하게 유지되는 한 인덱스 메트릭은 변경되지 않을 수 있습니다. 대신 진단 로그를 사용하여 쿼리 RU 요금 및 대기 시간을 모니터링하여 비용이 많이 드는 쿼리를 식별하는 것이 좋습니다.

### <a name="net-sdk-example"></a>.NET SDK 예제

```csharp
    string sqlQueryText = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    QueryDefinition query = new QueryDefinition(sqlQueryText);

    FeedIterator<Item> resultSetIterator = container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
          response = await resultSetIterator.ReadNextAsync();
          Console.WriteLine(response.IndexMetrics);
        }
```

### <a name="example-output"></a>예제 출력

이 예제 쿼리에서는 사용 경로 `/Item/?` 및 `/Price/?` 잠재적 복합 인덱스 를 `(/Item ASC, /Price ASC)` 관찰합니다.

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

## <a name="utilized-indexed-paths"></a>활용된 인덱싱된 경로

사용된 단일 인덱스와 사용된 복합 인덱스는 각각 쿼리에서 사용한 포함된 경로 및 복합 인덱스를 표시합니다. 쿼리는 포함된 경로와 복합 인덱스의 혼합뿐만 아니라 여러 인덱싱된 경로를 사용할 수 있습니다. 인덱싱된 경로가 활용된 것으로 나열되지 않으면 인덱싱된 경로를 제거해도 쿼리 성능에 영향을 주지 않습니다.

사용된 인덱싱된 경로 목록을 쿼리에서 해당 경로를 사용했다는 증거로 간주합니다. 새 인덱싱된 경로로 쿼리 성능이 향상되는지 확실하지 않은 경우 새 인덱싱된 경로를 추가하고 쿼리에서 이를 사용하는지 확인해야 합니다.

## <a name="potential-indexed-paths"></a>잠재적 인덱싱된 경로

잠재적인 단일 인덱스와 활용된 복합 인덱스는 각각 쿼리가 활용할 수 있는 포함된 경로 및 복합 인덱스를 표시합니다. 잠재적인 인덱싱된 경로가 표시되면 인덱싱 정책에 해당 경로를 추가하고 쿼리 성능이 향상되는지 확인해야 합니다.

잠재적인 인덱싱된 경로 목록을 쿼리가 특정 인덱싱된 경로를 사용한다는 결정적인 증거 대신 권장 사항으로 간주합니다. 잠재적 인덱싱된 경로는 쿼리에서 사용할 수 있는 인덱싱된 경로의 전체 목록이 아닙니다. 또한 일부 잠재적인 인덱싱된 경로는 쿼리 성능에 영향을 주지 않을 수 있습니다. [권장되는 인덱싱된 경로를 추가하고](how-to-manage-indexing-policy.md) 쿼리 성능을 향상시키는지 확인합니다.

> [!NOTE]
> 인덱싱 메트릭에 대한 피드백이 있나요? 많은 의견 부탁드립니다. 언제든지 자유롭게 Azure Cosmos DB 엔지니어링 팀(cosmosdbindexing@microsoft.com)과 피드백을 직접 공유해 주세요.

## <a name="index-impact-score"></a>인덱스 영향 점수

인덱스 영향 점수는 쿼리 셰이프를 기반으로 하는 인덱싱된 경로가 쿼리 성능에 상당한 영향을 미칠 가능성입니다. 즉, 인덱스 영향 점수는 특정 인덱싱된 경로가 없으면 쿼리 RU 요금이 상당히 높을 확률입니다. 

인덱스 영향 점수에는 **높음** 및 **낮음의** 두 가지가 있습니다. 잠재적인 인덱싱된 경로가 여러 가지 있는 경우 영향 점수가 **높은** 인덱싱된 경로에 집중하는 것이 좋습니다.

인덱스 영향 점수에 사용되는 유일한 조건은 쿼리 셰이프입니다. 예를 들어 아래 쿼리에서 인덱싱된 `/name/?` 경로에는 **높은** 인덱스 영향 점수가 할당됩니다.

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

데이터의 특성에 따른 실제 영향입니다. 필터와 일치하는 항목이 몇 가지뿐인 경우 `/name` 인덱싱된 경로는 쿼리 RU 요금을 크게 향상시킵니다. 그러나 대부분의 항목이 필터와 일치하게 되면 `/name` 인덱싱된 경로로 쿼리 성능이 향상되지 않을 수 있습니다. 이러한 각 경우 인덱싱된 `/name/?` 경로에는 **높은** 인덱스 영향 점수가 할당됩니다. 쿼리 셰이프에 따라 인덱싱된 경로가 쿼리 성능을 개선할 가능성이 높기 때문입니다.

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
이러한 인덱스 메트릭은 쿼리가 인덱싱된 경로 , , 및 을 사용한다는 것을 보여 주는 `/name/?` `/age/?` `/town/?` `/timestamp/?` 것입니다. 또한 인덱스 메트릭은 복합 인덱스( `/name` ASC)를 `(/town ASC, /age ASC)` 추가하면 성능이 더욱 향상될 가능성이 높음을 `(/name ASC, /town ASC, /timestamp ASC)` 나타냅니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](../index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
