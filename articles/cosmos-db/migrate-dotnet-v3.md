---
title: Azure Cosmos DB .NET SDK 3.0(com.azure.cosmos)을 사용하도록 애플리케이션 마이그레이션
description: 기존 .NET 애플리케이션을 v2 SDK에서 최신 .NET SDK v3 for Core (SQL) API (com.azure.cosmos 패키지)로 업그레이드하는 방법에 대해 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/19/2021
ms.openlocfilehash: c050a874d3a63847fd062ffcc1be952e9c95c315
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597492"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Azure Cosmos DB .NET SDK v3를 사용하도록 애플리케이션 마이그레이션
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB .NET SDK v3에 대해 알아보려면 [릴리스 정보](sql-api-sdk-dotnet-standard.md), [.NET GitHub 리포지토리](https://github.com/Azure/azure-cosmos-dotnet-v3), .Net SDK v3 [성능 팁](performance-tips-dotnet-sdk-v3-sql.md) 및 [문제 해결 가이드](troubleshoot-dot-net-sdk.md)를 참조하세요.
>

이 문서에서는 기존 .NET 애플리케이션을 최신 Azure Cosmos DB .NET SDK v3 for Core (SQL) API로 업그레이드하는 경우의 몇 가지 고려 사항을 중점적으로 설명합니다. Azure Cosmos DB .NET SDK v3는 Microsoft.Azure.Cosmos 네임스페이스에 해당합니다. 다음 Azure Cosmos DB .NET SDK 중 하나에서 애플리케이션을 마이그레이션하는 경우 이 문서의 정보를 활용합니다.

* Azure Cosmos DB .NET Framework SDK v2 for SQL API
* Azure Cosmos DB .NET Core SDK v2 for SQL API

이 문서의 지침을 참조하면 Azure Cosmos DB .NET SDK v3 for Core (SQL) API에 포함된 다음 외부 라이브러리도 마이그레이션할 수 있습니다.

* .NET 변경 피드 프로세서 라이브러리 2.0
* .NET 대량 실행기 라이브러리 1.1 이상

## <a name="whats-new-in-the-net-v3-sdk"></a>.NET v3 SDK의 새로운 기능

v3 SDK에는 다음과 같이 많은 유용성 및 성능 개선 사항이 포함되어 있습니다.

* 직관적인 프로그래밍 모델 명명
* .NET Standard 2.0 **
* 스트림 API 지원을 통한 성능 향상
* URI 팩터리의 필요성을 대체하는 흐름 계층 구조
* 변경 피드 프로세서 라이브러리에 대한 기본 제공 지원
* 대량 작업에 대한 기본 제공 지원
* Mockable API를 통해 간단해진 유닛 네스트
* 트랜잭션 일괄 처리 및 Blazor 지원
* 플러그형 직렬 변환기
* 분할되지 않은 자동 크기 조정 컨테이너의 크기 조정

** SDK는 기존 Azure Cosmos DB .NET Framework와 .NET Core SDK를 단일 .NET SDK로 통합하는 .NET Standard 2.0을 대상으로 합니다. .NET Framework 4.6.1+ 및 .NET Core 2.0+ 애플리케이션을 포함하여 .NET Standard 2.0을 구현하는 모든 플랫폼에서 .NET SDK를 사용할 수 있습니다.

대부분의 네트워킹, 재시도 논리 및 낮은 수준의 SDK는 거의 변경되지 않습니다.

**Azure Cosmos DB .NET SDK v3는 현재 오픈 소스입니다.** 모든 끌어오기 요청을 수용하고 [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/)에서 문제를 로깅하며 피드백을 추적할 것입니다. 고객 환경을 개선할 수 있는 모든 기능을 활용하기 위해 노력하겠습니다.

## <a name="why-migrate-to-the-net-v3-sdk"></a>.NET v3 SDK로 마이그레이션하는 이유

수많은 유용성 및 성능 개선 사항 외에도 최신 SDK에 투자된 새로운 기능을 접한다면 이전 버전을 다시 사용하지 않게 될 것입니다.

당장 [2.0 SDK에 대한 지원을 중지](sql-api-sdk-dotnet.md)하는 것은 아니지만 차후에 최신 버전으로 대체되고 2,0 버전은 유지 관리 모드로 전환될 것입니다. 최상의 개발 환경을 위해 항상 지원되는 최신 버전의 SDK로 시작하는 것이 좋습니다.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>v2 SDK에서 v3 SDK로 주요 이름 변경

다음 이름 변경은 Core (SQL) API의 API 명명 규칙에 맞추어 .NET 3.0 SDK 전반에 걸쳐 적용되었습니다.

* `DocumentClient`가 `CosmosClient`로 이름이 바뀌었습니다.
* `Collection`이 `Container`로 이름이 바뀌었습니다.
* `Document`가 `Item`으로 이름이 바뀌었습니다.

모든 리소스 개체는 명확성을 위해 리소스 이름을 포함하는 추가 속성으로 이름이 변경됩니다.

다음은 몇 가지 주요 클래스 이름 변경 내용입니다.

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>.NET v3 SDK에서 바뀌는 클래스

다음 클래스는 3.0 SDK에서 바뀌었습니다.

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents.UriFactory 클래스가 흐름 디자인으로 바뀌었습니다. 흐름 디자인은 URL을 내부적으로 빌드하고 `DocumentClient`, `DatabaseName` 및 `DocumentCollection` 대신 단일 `Container` 개체가 전달될 수 있도록 합니다.

### <a name="changes-to-item-id-generation"></a>항목 ID 생성 변경

.NET v3 SDK에서는 항목 ID가 자동으로 채워지지 않습니다. 따라서 항목 ID에는 특별히 생성된 ID가 포함되어야 합니다. 다음 예제를 봅니다.

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>연결 모드에 대한 기본 동작이 변경됨

게이트웨이 + HTTPS 연결 모드로 기본 설정되었던 이전 v2 SDK와 달리 SDK v3는 직접 + TCP 연결 모드로 기본 설정됩니다. 이 변경을 통해 성능 및 확장성이 향상되었습니다.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>FeedOptions에 대한 변경(v3.0 SDK의 QueryRequestOptions)

이제 SDK v2의 `FeedOptions` 클래스가 SDK v3 및 클래스 내에서 `QueryRequestOptions`로 이름이 변경되었습니다. 여러 속성은 완전히 제거되거나 이름 및/또는 기본값이 변경되었습니다.  

`FeedOptions.MaxDegreeOfParallelism`은 `QueryRequestOptions.MaxConcurrency`로 이름이 바뀌었으며 기본값 및 관련 동작은 동일하게 유지됩니다. 병렬 쿼리 실행 중에 클라이언트 측에서 실행되는 작업은 병렬 처리 없이 직렬로 실행됩니다.

`FeedOptions.EnableCrossPartitionQuery`는 제거되었으며 SDK 3.0의 기본 동작은 속성을 사용하도록 설정하지 않아도 파티션 간 쿼리가 실행됩니다.

`FeedOptions.PopulateQueryMetrics`는 기본적으로 사용하도록 설정되어 있으며 응답의 진단 속성에 결과가 표시됩니다.

`FeedOptions.RequestContinuation`은 쿼리 메서드로 자체 승격되었습니다.

다음 속성은 제거되었습니다.

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>클라이언트 생성

.NET SDK v3는 SDK v2 URI 팩터리의 필요성을 대체하는 흐름 `CosmosClientBuilder` 클래스를 제공합니다.

다음 예제에서는 강력한 ConsistencyLevel 및 기본 위치 목록을 사용하여 새 `CosmosClientBuilder`를 만듭니다.

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="exceptions"></a>예외

v2 SDK가 작업 중 오류를 알리기 위해 `DocumentClientException`을 사용했다면 v3 SDK는 `StatusCode`, `Diagnostics` 및 기타 응답 관련 정보를 노출하는 `CosmosClientException`을 사용합니다. 모든 완전한 정보는 `ToString()`이 사용될 때 직렬화됩니다.

```csharp
catch (CosmosClientException ex)
{
    HttpStatusCode statusCode = ex.StatusCode;
    CosmosDiagnostics diagnostics = ex.Diagnostics;
    // store diagnostics optionally with diagnostics.ToString();
    // or log the entire error details with ex.ToString();
}
```

### <a name="diagnostics"></a>진단

v2 SDK에 `ResponseDiagnosticsString` 속성을 통해 사용할 수 있는 직접 전용 진단이 있는 경우 v3 SDK는 더 풍부하고 직접 모드에 국한되지 않는 모든 응답 및 예외에서 사용할 수 있는 `Diagnostics`를 사용합니다. 여기에는 작업을 위해 SDK에 소요된 시간뿐만 아니라 작업이 연결된 지역도 포함됩니다.

```csharp
try
{
    ItemResponse<MyItem> response = await container.ReadItemAsync<MyItem>(
                    partitionKey: new PartitionKey("MyPartitionKey"),
                    id: "MyId");
    
    TimeSpan elapsedTime = response.Diagnostics.GetElapsedTime();
    if (elapsedTime > somePreDefinedThreshold)
    {
        // log response.Diagnostics.ToString();
        IReadOnlyList<(string region, Uri uri)> regions = response.Diagnostics.GetContactedRegions();
    }
}
catch (CosmosException cosmosException) {
    string diagnostics = cosmosException.Diagnostics.ToString();
    
    TimeSpan elapsedTime = cosmosException.Diagnostics.GetElapsedTime();
    
    IReadOnlyList<(string region, Uri uri)> regions = cosmosException.Diagnostics.GetContactedRegions();
    
    // log cosmosException.ToString()
}
```

### <a name="connectionpolicy"></a>ConnectionPolicy

`ConnectionPolicy`의 일부 설정은 이름이 변경되거나 대체되었습니다.

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`EnableEndpointRediscovery`|`LimitToEndpoint` - 이제 값이 반전됩니다. `EnableEndpointRediscovery`가 `true`로 설정되어 있으면 `LimitToEndpoint`는 `false`로 설정되어야 합니다. 이 설정을 사용하기 전에 [클라이언트에 미치는 영향](troubleshoot-sdk-availability.md)을 이해해야 합니다.|
|`ConnectionProtocol`|제거됩니다. 프로토콜은 게이트웨이(HTTPS) 또는 직접(TCP) 모드에 연결됩니다.|
|`MediaRequestTimeout`|제거됩니다. 첨부 파일은 더 이상 지원되지 않습니다.|

### <a name="session-token"></a>세션 토큰

v2 SDK가 세션 토큰 캡처가 필요할 때 응답의 세션 토큰을 `ResourceResponse.SessionToken`으로 노출한 경우 세션 토큰이 헤더이므로 v3 SDK는 응답의 `Headers.Session` 속성에 해당 값을 노출합니다.

### <a name="timestamp"></a>타임스탬프

v2 SDK가 `Timestamp` 속성을 통해 문서의 타임스탬프를 노출한 경우 `Document`를 더 이상 사용할 수 없으므로 사용자는 `_ts` [시스템 속성](account-databases-containers-items.md#properties-of-an-item)을 모델의 속성에 매핑할 수 있습니다.

### <a name="openasync"></a>OpenAsync

`OpenAsync()`를 사용하여 v2 SDK 클라이언트를 워밍업하는 사용 사례의 경우 `CreateAndInitializeAsync`를 사용하여 v3 SDK 클라이언트를 [만들고 워밍업](https://devblogs.microsoft.com/cosmosdb/improve-net-sdk-initialization/)할 수 있습니다.

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>v3 SDK에서 직접 변경 피드 프로세서 API 사용

v3 SDK는 동일한 SDK를 사용하여 애플리케이션을 빌드하고 변경 피드 프로세서를 구현할 수 있도록 변경 피드 프로세서 API에 대한 기본 제공을 지원합니다. 이전에는 별도의 변경 피드 프로세서 라이브러리를 사용해야 했습니다.

자세한 내용은 [변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET v3 SDK로 마이그레이션하는 방법](how-to-migrate-from-change-feed-library.md)을 참조하세요.

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>v3 SDK에서 직접 대량 실행기 라이브러리 사용

v3 SDK는 동일한 SDK를 사용하여 애플리케이션을 빌드하고 대량 작업을 수행할 수 있도록 대량 실행기 라이브러리에 대한 기본 제공을 지원합니다. 이전에는 별도의 대량 실행기 라이브러리를 사용해야 했습니다.

자세한 내용은 [대량 실행기 라이브러리에서 Azure Cosmos DB .NET V3 SDK의 대량 지원으로 마이그레이션하는 방법](how-to-migrate-from-bulk-executor-library.md)을 참조하세요.

## <a name="code-snippet-comparisons"></a>코드 조각 비교

다음 코드 조각은 .NET v2와 v3 SDK에서 리소스를 만드는 방법의 차이점을 보여 줍니다.

## <a name="database-operations"></a>데이터베이스 작업

### <a name="create-a-database"></a>데이터베이스 만들기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>ID로 데이터베이스 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>데이터베이스 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>컨테이너 작업

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>컨테이너 만들기(자동 크기 조정 + 만료 시간에 대한 TTL(Time to Live))

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>컨테이너 속성 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>컨테이너 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>항목 및 쿼리 작업

### <a name="create-an-item"></a>항목 만들기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>컨테이너의 모든 항목 읽기

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>쿼리 항목

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>항목 삭제

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>다음 단계

* v3 SDK를 사용하여 Azure Cosmos DB SQL API 데이터를 관리하는 [콘솔 앱 빌드](sql-api-get-started.md)
* [v3 SDK에서 수행할 수 있는 작업](sql-api-dotnet-v3sdk-samples.md)에 대해 자세히 알아봅니다.
