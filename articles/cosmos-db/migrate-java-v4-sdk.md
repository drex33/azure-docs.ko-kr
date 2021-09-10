---
title: Azure Cosmos DB Java SDK v4(com.azure.cosmos)를 사용하도록 애플리케이션 마이그레이션
description: 이전 Azure Cosmos DB Java SDK를 사용하여 기존 Java 애플리케이션을 최신 Core (SQL) API용 Java SDK 4.0(com.azure.cosmos 패키지)으로 업그레이드하는 방법에 대해 알아봅니다.
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: c4f62acd9be89aee191614d28e97959d8aaf079b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033781"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4를 사용하도록 애플리케이션 마이그레이션
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> 이 SDK에 대한 자세한 내용은 Azure Cosmos DB Java SDK v4 [릴리스 정보](sql-api-sdk-java-v4.md), [Maven 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md) 및 Azure Cosmos DB Java SDK v4 [문제 해결 가이드](troubleshoot-java-sdk-v4-sql.md)를 참조하세요.
>

> [!IMPORTANT]  
> Azure Cosmos DB Java SDK v4는 최대 20% 향상된 처리량, TCP 기반 직접 모드 및 최신 백엔드 서비스 기능을 지원하므로 다음 기회에 v4로 업그레이드하는 것이 좋습니다. 자세히 알아보려면 계속 읽어보세요.
>

최신 Azure Cosmos DB Java SDK로 업데이트하여 Azure Cosmos DB가 제공해야 하는 서비스로서, 경쟁력 있는 성능, 고가용성, 일종의 리소스 거버넌스 등을 제공하는 관리형 비관계형 데이터베이스 서비스를 최대한 활용합니다. 이 문서에서는 이전 Azure Cosmos DB Java SDK를 사용하는 기존 Java 애플리케이션을 최신 Core (SQL) API용 Azure Cosmos DB Java SDK 4.0으로 업그레이드하는 방법을 설명합니다. Azure Cosmos DB Java SDK v4는 `com.azure.cosmos` 패키지에 해당합니다. 다음 Azure Cosmos DB Java SDK 중 하나에서 애플리케이션을 마이그레이션하는 경우 이 문서의 지침을 사용할 수 있습니다. 

* Sync Java SDK 2.x.x
* Async Java SDK 2.x.x
* Java SDK 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK와 패키지 매핑

다음 표에는 다양한 Azure Cosmos DB Java SDK, 패키지 이름 및 릴리스 정보가 나와 있습니다.

| Java SDK| 출시 날짜 | 번들 API   | Maven Jar  | Java 패키지 이름  |API 참조   | 릴리스 정보  | 만료 날짜 |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|--------|
| Async 2.x.x  | 2018년 6월    | Async(RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [릴리스 정보](sql-api-sdk-async-java.md) | 2024년 8월 31일 |
| Sync 2.x.x     | 2018년 9월    | 동기화   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [릴리스 정보](sql-api-sdk-java.md)  | 2024년 2월 29일 |
| 3.x.x    | 2019년 7월    | Async(Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - | 2024년 8월 31일 |
| 4.0   | 2020년 6월   | Async(Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | [API](/java/api/overview/azure/cosmosdb) | - | - |

## <a name="sdk-level-implementation-changes"></a>SDK 수준 구현 변경 내용

다음은 서로 다른 SDK의 주요 구현 차이점입니다.

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava는 Azure Cosmos DB Java SDK 버전 3.x.x 및 4.0에서 Reactor로 바뀝니다.

비동기 프로그래밍 또는 사후 프로그래밍에 익숙하지 않은 경우 비동기 프로그래밍 및 Project Reactor를 소개하는 [Reactor 패턴 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)를 참조하세요. 이 가이드는 이전의 Azure Cosmos DB Sync Java SDK 2.x.x 또는 Azure Cosmos DB Java SDK 3.x.x Sync API를 사용하고 있는 경우 유용할 수 있습니다.

Azure Cosmos DB Async Java SDK 2.x.x를 사용하고 있고 4.0 SDK로 마이그레이션할 계획인 경우 Reactor를 사용하도록 RxJava 코드를 변환하는 방법에 대한 지침은 [Reactor 및 RxJava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)를 참조하세요.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4에는 Async 및 Sync API의 직접 연결 모드가 있습니다.

Azure Cosmos DB Sync Java SDK 2.x.x를 사용하고 있는 경우 TCP(HTTP 아님) 기반 직접 연결 모드가 Async 및 Sync API를 위해 Azure Cosmos DB Java SDK 4.0에서 구현됩니다.

## <a name="api-level-changes"></a>API 수준 변경

다음은 이전 SDK(Java SDK 3.x.x, Async Java SDK 2.x.x 및 Sync Java SDK 2.x.x)와 비교하여 Azure Cosmos DB Java SDK 4.x.x에 있는 API 수준의 변경 내용입니다.

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB Java SDK 명명 규칙":::

* Azure Cosmos DB Java SDK 3.x.x 및 4.0은 클라이언트 리소스를 `Cosmos<resourceName>`으로 참조합니다. 예: `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. 버전 2.x.x에서는 Azure Cosmos DB Java SDK에 일정한 명명 체계가 없습니다.

* Azure Cosmos DB Java SDK 3.x.x 및 4.0은 Sync 및 Async API를 모두 제공합니다.

  * **Java SDK 4.0** : 클래스 이름이 `Cosmos` 뒤에 `Async`가 추가되어 있지 않으면 모든 클래스가 Sync API에 포함됩니다.

  * **Java SDK 3.x.x**: 클래스 이름이 `Cosmos` 뒤에 `Async`가 추가되어 있지 않으면 모든 클래스가 Async API에 포함됩니다.

  * **Async Java SDK 2.x.x**: 클래스 이름은 Sync Java SDK 2.x.x와 유사하지만 이름이 *Async* 로 시작합니다.

### <a name="hierarchical-api-structure"></a>계층적 API 구조

Azure Cosmos DB Java SDK 4.0 및 3.x.x에는 다음 4.0 SDK 코드 조각에 표시된 대로 클라이언트, 데이터베이스 및 컨테이너를 중첩된 방식으로 구성하는 계층적 API 구조가 도입되었습니다.

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Azure Cosmos DB Java SDK 버전 2.x.x에서 리소스 및 문서에 대한 모든 작업은 클라이언트 인스턴스를 통해 수행됩니다.

### <a name="representing-documents"></a>문서 표현

Azure Cosmos DB Java SDK 4.0에서 사용자 지정 POJO 및 `JsonNodes`는 Azure Cosmos DB에서 문서를 읽고 쓰는 두 가지 옵션입니다.

Azure Cosmos DB Java SDK 3.x.x에서 `CosmosItemProperties` 개체는 공개 API에 의해 노출되고 문서 표현으로 제공됩니다. 이 클래스는 더 이상 버전 4.0에서 공개적으로 노출되지 않습니다.

### <a name="imports"></a>가져오기

* Azure Cosmos DB Java SDK 4.0 패키지는 `com.azure.cosmos`로 시작합니다.
* Azure Cosmos DB Java SDK 3.x.x 패키지는 `com.azure.data.cosmos`로 시작합니다.
* Azure Cosmos DB Java SDK 2.x.x Sync API packages는 `com.microsoft.azure.documentdb`로 시작합니다.

* Azure Cosmos DB Java SDK 4.0은 여러 클래스를 중첩 패키지 `com.azure.cosmos.models`에 배치합니다. 이러한 패키지 중 일부는 다음과 같습니다.

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * 위의 모든 패키지에 대해 유사한 Async API
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` 등

### <a name="accessors"></a>접근자

Azure Cosmos DB Java SDK 4.0은 `get` 및 `set` 메서드를 노출하여 인스턴스 멤버에 액세스합니다. 예를 들어 `CosmosContainer` 인스턴스에는 `container.getId()` 및 `container.setId()` 메서드가 있습니다.

이는 흐름 인터페이스를 노출하는 Azure Cosmos DB Java SDK 3.x.x와 다릅니다. 예를 들어 `CosmosSyncContainer` 인스턴스에는 `id` 값을 가져오거나 설정하기 위해 오버로드된 `container.id()`가 있습니다.

## <a name="code-snippet-comparisons"></a>코드 조각 비교

### <a name="create-resources"></a>리소스 만들기

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 리소스를 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Create Async client.
// Building an async client is still a sync operation.
AsyncDocumentClient client = new Builder()
    .withServiceEndpoint("your.hostname")
    .withMasterKeyOrResourceToken("yourmasterkey")
    .withConsistencyLevel(ConsistencyLevel.Eventual)
    .build();
// Create database with specified name
Database database = new Database();
database.setId("YourDatabaseName");
client.createDatabase(database, new RequestOptions())
      .flatMap(databaseResponse -> {
          // Collection properties - name and partition key
          DocumentCollection documentCollection = new DocumentCollection();
          documentCollection.setId("YourContainerName");
          documentCollection.setPartitionKey(new PartitionKeyDefinition("/id"));
          // Create collection
          return client.createCollection(databaseResponse.getResource().getSelfLink(), documentCollection, new RequestOptions());
}).subscribe();

```

---

### <a name="item-operations"></a>항목 작업

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 항목 작업을 수행하는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Collection is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<Document> docs = generateManyDocs(number_of_docs);
// Insert many docs into collection...
Observable.from(docs)
    .flatMap(doc -> client.createDocument(createdCollection.getSelfLink(), doc, new RequestOptions(), false))
    .subscribe(); // ...Subscribing triggers stream execution.
```

---

### <a name="indexing"></a>인덱싱

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 인덱싱을 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.None
// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).subscribe();
```

---

### <a name="stored-procedures"></a>저장 프로시저

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 저장 프로시저를 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
logger.info("Creating stored procedure...\n");
String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client
    .createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
    .toBlocking()
    .single()
    .getResource();
// ...
logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));
StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null)
    .toBlocking().single();
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```

---

### <a name="change-feed"></a>변경 피드

다음 코드 조각은 4.0과 3.x.x Async API에서 변경 피드 작업을 실행하는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

* 이 기능은 Java SDK v2 sync에서 지원되지 않습니다. 

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

* 이 기능은 Java SDK v2 Async에서 지원되지 않습니다. 

---

### <a name="container-level-time-to-livettl"></a>컨테이너 수준 TTL(Time-To-Live)

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 컨테이너에서 데이터의 TTL(Time to Live)을 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
DocumentCollection collection = new DocumentCollection();
// Create a new container with TTL enabled with default expiration value
collection.setDefaultTimeToLive(90 * 60 * 60 * 24);
collection = client
    .createCollection(database.getSelfLink(), documentCollection, new RequestOptions())
    .toBlocking()
    .single()
    .getResource();
```

---

### <a name="item-level-time-to-livettl"></a>항목 수준 TTL(Time-To-Live)

다음 코드 조각은 4.0, 3.x.x Async, 2.x.x Sync, 2.x.x Async API 간에 항목의 TTL(Time to Live)을 만드는 방법의 차이점을 보여 줍니다.

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 Async API](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3.x.x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[Java SDK 3.x.x Async API](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```

# <a name="java-sdk-2xx-async-api"></a>[Java SDK 2.x.x Async API](#tab/java-v2-async)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true).toBlocking().single();
Document responseDocument = documentResourceResponse.getResource();
```

---

## <a name="next-steps"></a>다음 단계

* V4 SDK를 사용하여 Azure Cosmos DB SQL API 데이터를 관리하는 [Java 앱 빌드](create-sql-api-java.md)
* [Reactor 기반 Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)에 대해 알아보기
* [Reactor 및 RxJava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)를 사용하여 RxJava 비동기 코드를 Reactor 비동기 코드로 변환하는 방법에 대해 알아보기
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.