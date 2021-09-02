---
title: Azure Cosmos DB에서 SQL 쿼리에 대한 RU(요청 단위) 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행되는 SQL 쿼리에 대한 RU(요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, .NET, Java, Python 및 Node.js 언어를 사용하여 RU 요금을 찾을 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0bfaee110f0d19672abc36ae96f95083735c14d1
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117026"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서 실행되는 작업에 대한 요청 단위 요금 찾기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. 요청 요금은 모든 데이터베이스 작업에서 사용되는 요청 단위입니다. RU는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화로 생각할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다. 자세한 내용은 [요청 단위 및 고려 사항](../request-units.md) 문서를 참조하세요.

이 문서에서는 Azure Cosmos DB SQL API의 컨테이너에 대해 실행한 작업의 [RU(요청 단위)](../request-units.md) 사용량을 알아보는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 초당 RU 요금은 [MongoDB용 API](../mongodb/find-request-unit-charge-mongodb.md), [Cassandra API](../cassandra/find-request-unit-charge-cassandra.md), [Gremlin API](../graph/find-request-unit-charge-gremlin.md), [Table API](../table/find-request-unit-charge.md) 문서를 참조하세요.

현재는 Azure Portal을 사용하거나 SDK 중 하나를 통해 Azure Cosmos DB로 다시 전송된 응답을 검사하는 방법으로만 이 사용량을 측정할 수 있습니다. SQL API를 사용하는 경우 Azure Cosmos 컨테이너에 대해 실행한 작업의 RU 사용량을 알아보는 여러 옵션이 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-sql-api-dotnet.md#create-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 Azure Cosmos 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컨테이너를 선택합니다.

1. **새 SQL 쿼리** 를 선택합니다.

1. 유효한 쿼리를 입력한 다음, **쿼리 실행** 을 선택합니다.

1. **쿼리 통계** 를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다.

:::image type="content" source="../media/find-request-unit-charge/portal-sql-query.png" alt-text="Azure Portal의 SQL 쿼리 요청 요금 스크린샷":::

## <a name="use-the-net-sdk"></a>.NET SDK 사용

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)에서 반환되는 개체는 다음과 같이 `RequestCharge` 속성을 표시합니다.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)에서 반환되는 개체는 다음과 같이 `RequestCharge` 속성을 표시합니다.

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

자세한 내용은 [빠른 시작: Azure Cosmos DB에서 SQL API 계정을 사용하여 .NET 웹앱 빌드](create-sql-api-dotnet.md)를 참조하세요.

---

## <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)에서 반환되는 개체는 다음과 같이 `getRequestCharge()` 메서드를 표시합니다.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Java 애플리케이션 빌드](create-sql-api-java.md)를 참조하세요.

## <a name="use-the-nodejs-sdk"></a>Node.js SDK 사용

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)에서 반환되는 개체는 기본 HTTP API가 반환한 모든 헤더를 매핑하는 `headers` 하위 개체를 표시합니다. 요청 요금은 다음과 같이 `x-ms-request-charge` 키 아래에서 확인할 수 있습니다.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Node.js 앱 빌드](create-sql-api-nodejs.md)를 참조하세요. 

## <a name="use-the-python-sdk"></a>Python SDK 사용

[Python SDK](https://pypi.org/project/azure-cosmos/)의 `CosmosClient` 개체는 마지막으로 실행된 작업의 기본 HTTP API에서 반환된 모든 헤더를 매핑하는 `last_response_headers` 사전을 표시합니다. 요청 요금은 다음과 같이 `x-ms-request-charge` 키 아래에서 확인할 수 있습니다.

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

자세한 내용은 [빠른 시작: Azure Cosmos DB SQL API 계정을 사용하여 Python 앱 빌드](create-sql-api-python.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](../optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](../optimize-cost-reads-writes.md)
* [프로비저닝된 처리량을 전역적으로 크기 조정](../request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md)
* [컨테이너의 처리량 프로비전](how-to-provision-container-throughput.md)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](../use-metrics.md)
