---
title: SQL 쿼리 성능 및 실행 메트릭 가져오기
description: SQL 쿼리 실행 메트릭을 검색하고 Azure Cosmos DB 요청에 대한 SQL 쿼리 성능을 프로파일링하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 674488daa883ec480f785de6ab2378954501a5bd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116549"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL 쿼리 실행 메트릭 가져오기 및 .NET SDK을 사용하여 쿼리 성능 분석하기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB에서 SQL 쿼리 성능을 프로파일링하는 방법을 보여줍니다. 해당 프로파일링은 .NET SDK 검색 결과 `QueryMetrics`을 사용하여 완료할 수 있으며 여기에서 자세히 설명하고 있습니다. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics)은 백 엔드 쿼리 실행에 대한 정보를 포함한 강력한 형식의 개체입니다. 해당 메트릭은 [쿼리 성능 조정](./sql-api-query-metrics.md) 문서에서 보다 자세히 알아볼 수 있습니다.

## <a name="set-the-feedoptions-parameter"></a>FeedOptions 매개 변수 설정하기

[DocumentClient.CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery)의 모든 오버로드는 선택적 [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) 매개 변수를 사용합니다. 해당 옵션을 사용하면 쿼리 실행을 조정하고 매개 변수화 할 수 있습니다. 

SQL 쿼리 실행 메트릭을 수집하려면 [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions)에서 `true`로 [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) 매개 변수를 설정해야 합니다. `PopulateQueryMetrics`을 True로 설정하면 `FeedResponse`가 관련 `QueryMetrics`을 포함하게 됩니다. 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery()을 사용하여 쿼리 메트릭 가져오기
아래의 코드 샘플은 [AsDocumentQuery()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) 메서드를 사용할 때 메트릭을 검색하는 방법을 보여줍니다:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics 집계

이전 섹션에서 [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) 메서드에 대한 여러 번의 호출이 있음을 확인했습니다. 각 호출은 `QueryMetrics` 사전이 있는 `FeedResponse` 개체를 반환했습니다; 해당 개체는 쿼리가 계속할 때마다 하나씩 반환됩니다. 다음 예제에서는 LINQ를 사용하여 `QueryMetrics`을 집계하는 방법을 보여줍니다:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>파티션 ID로 쿼리 메트릭 그룹화

파티션 ID로 `QueryMetrics`을 그룹화할 수 있습니다. 파티션 ID로 그룹화하면 다른 파티션과 비교했을 때 특정 파티션에서 성능 문제가 발생하는지 여부를 확인할 수 있습니다. 다음 예제에서는 LINQ를 사용하여 `QueryMetrics`을 그룹화하는 방법을 보여줍니다:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>DocumentQuery의 LINQ

`AsDocumentQuery()` 메서드를 사용하면 LINQ 쿼리에서 `FeedResponse`을 가져올 수 있습니다:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>비용이 높은 쿼리

각 쿼리에서 사용하는 요청 단위를 캡처하여 비용이 높은 쿼리나 높은 처리량을 사용하는 쿼리를 조사할 수 있습니다. `FeedResponse`에서 [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge)을 사용하여 요청 요금을 가져올 수 있습니다. Azure Portal과 기타 SDK를 사용하여 요청 요금을 가져오는 방법에 대한 자세한 내용은 [요청 단위 요금 찾기](find-request-unit-charge.md) 문서를 참조하세요.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>쿼리 실행 시간 가져오기

클라이언트 측 쿼리를 실행하는데 필요한 시간을 계산하려면 `ExecuteNextAsync` 메서드를 호출하는 시간만 포함하고 코드 베이스의 다른 부분은 포함하지 않았는지 확인합니다. 해당 호출은 다음 예제에서 볼 수 있듯 쿼리 실행에 걸린 시간 계산을 지원합니다:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>검색 쿼리(일반적으로 느리고 비용이 높음)

검색 쿼리는 인덱스에서 제공하지 않은 쿼리를 참조하며 이는 결과 집합을 반환하기 전 많은 문서를 로드하기 때문입니다.

다음은 검색 쿼리의 예시입니다:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

해당 쿼리 필터는 인덱스에서 제공하지 않는 UPPER 시스템 함수를 사용합니다. 대규모 컬렉션에 대해 이 쿼리를 실행하면 첫번째 연속에 대한 다음의 쿼리 메트릭이 생성됩니다:

```
QueryMetrics

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

쿼리 메트릭 출력에서 다음 값을 확인합니다:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

이 쿼리는 총 399,998,938바이트의 60,951개 문서를 로드했습니다. 이렇듯 많은 양의 바이트를 로드하면 높은 비용 또는 요청 단위 요금이 발생합니다. 또한 총 시간 사용 속성에서 명확히 확인할 수 있듯 쿼리 실행에 더 오랜 시간이 걸립니다:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

즉, 쿼리가 실행되는데 (단 하나의 연속에만) 4.5초 정도가 걸렸습니다.

이 예제 쿼리를 최적화하려면 필터에서 UPPER 사용을 피하십시오. 대신 문서를 만들거나 업데이트할 때 `c.description` 값을 모두 대문자로 입력합니다. 그러면 쿼리가 다음과 같이 됩니다: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

그러면 이제 이 쿼리를 인덱스에서 제공할 수 있습니다.

쿼리 성능 조정에 대한 자세한 내용은 [쿼리 성능 조정](./sql-api-query-metrics.md) 문서를 참조하세요.

## <a name="references"></a><a id="References"></a>참조

- [Azure Cosmos DB SQL 사양](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>다음 단계

- [쿼리 성능 조정](sql-api-query-metrics.md)
- [인덱싱 개요](../index-overview.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)