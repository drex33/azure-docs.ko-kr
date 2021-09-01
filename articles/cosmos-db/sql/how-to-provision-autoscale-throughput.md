---
title: Azure Cosmos DB SQL API에서 자동 크기 조정 처리량 프로비전
description: Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용하여 Azure Cosmos DB SQL API의 컨테이너 및 데이터베이스 수준에서 자동 크기 조정 처리량을 프로비전하는 방법에 대해 알아보세요.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/18/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 777175af8acc6656400329c5882e6ac8551d1e9f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116964"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Azure Cosmos DB - SQL API에서 데이터베이스 또는 컨테이너의 자동 크기 조정 처리량 프로비전
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB SQL API에서 데이터베이스 또는 컨테이너(컬렉션, 그래프 또는 테이블)에 자동 크기 조정 처리량을 프로비전하는 방법을 설명합니다. 단일 컨테이너에서 자동 크기 조정을 사용하도록 설정하거나 데이터베이스에서 자동 크기 조정 처리량을 프로비전하고 데이터베이스의 모든 컨테이너 간에 공유할 수 있습니다.

다른 API를 사용하는 경우 처리량을 프로비전하려면 [API for MongoDB](../mongodb/how-to-provision-throughput-mongodb.md), [Cassandra API](../cassandra/how-to-provision-throughput-cassandra.md), [Gremlin API](../graph/how-to-provision-throughput-gremlin.md) 문서를 참조하세요.

## <a name="azure-portal"></a>Azure portal

### <a name="create-new-database-or-container-with-autoscale"></a>자동 크기 조정을 사용하여 새 데이터베이스 또는 컨테이너 만들기

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. **새 컨테이너** 를 선택합니다. 데이터베이스, 컨테이너 및 파티션 키의 이름을 입력합니다. 데이터베이스 또는 컨테이너 처리량을 고려하여 **자동 크기 조정** 옵션을 선택하고 데이터베이스나 컨테이너를 확장하고 싶은 만큼 [최대 처리량(RU/s)](../provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works)을 설정합니다.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="컨테이너 만들기 및 자동 크기 조정 프로비전된 처리량 구성":::

1. **확인** 을 선택합니다.

공유 처리량 데이터베이스에 자동 크기 조정을 프로비전하려면 새 데이터베이스를 만들 때 **데이터베이스 처리량 프로비전** 옵션을 선택합니다. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>기존 데이터베이스 또는 컨테이너에서 자동 크기 조정 사용

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. 컨테이너에 대한 **크기 조정 및 설정** 또는 데이터베이스에 대한 **크기 조정** 을 선택합니다.

1. **크기 조정** 에서 **자동 크기 조정** 옵션, **저장** 을 선택합니다.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="기존 컨테이너에서 자동 크기 조정 사용":::

> [!NOTE]
> 기존 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용하도록 설정하면 현재의 수동 프로비전된 처리량 설정 및 스토리지에 따라 최대 RU/s의 시작 값이 시스템에 의해 결정됩니다. 작업이 완료된 후 필요하면 최대 RU/s를 변경할 수 있습니다. [자세한 정보](../autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET V3 SDK

SQL API용 Azure Cosmos DB .NET SDK의 [버전 3.9 이상](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)을 사용하여 자동 크기 조정 리소스를 관리합니다. 

> [!IMPORTANT]
> .NET SDK를 사용하여 새 자동 크기 조정 리소스를 만들 수 있습니다. 이 SDK는 자동 크기 조정 처리량과 표준(수동) 처리량 간의 마이그레이션을 지원하지 않습니다. 마이그레이션 시나리오는 현재 [Azure Portal](#enable-autoscale-on-existing-database-or-container), [CLI](#azure-cli), [PowerShell](#azure-powershell)에서만 지원됩니다.

### <a name="create-database-with-shared-throughput"></a>공유 처리량을 사용하여 데이터베이스 만들기

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>전용 처리량을 사용하여 컨테이너 만들기

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>현재 처리량(RU/s) 읽기

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>자동 크기 조정 최대 처리량(RU/s) 변경

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java V4 SDK

SQL API용 Azure Cosmos DB Java SDK의 [버전 4.0 이상](https://mvnrepository.com/artifact/com.azure/azure-cosmos)을 사용하여 자동 크기 조정 리소스를 관리할 수 있습니다.

> [!IMPORTANT]
> Java SDK를 사용하여 새 자동 크기 조정 리소스를 만들 수 있습니다. 이 SDK는 자동 크기 조정 처리량과 표준(수동) 처리량 간의 마이그레이션을 지원하지 않습니다. 마이그레이션 시나리오는 현재 [Azure Portal](#enable-autoscale-on-existing-database-or-container), [CLI](#azure-cli), [PowerShell](#azure-powershell)에서만 지원됩니다.
### <a name="create-database-with-shared-throughput"></a>공유 처리량을 사용하여 데이터베이스 만들기

# <a name="async"></a>[비동기](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

# <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>전용 처리량을 사용하여 컨테이너 만들기

# <a name="async"></a>[비동기](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

# <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>현재 처리량(RU/s) 읽기

# <a name="async"></a>[비동기](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

# <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>자동 크기 조정 최대 처리량(RU/s) 변경

# <a name="async"></a>[비동기](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

# <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager 템플릿은 모든 Azure Cosmos DB API의 새로운 데이터베이스 또는 컨테이너 수준 리소스에서 자동 크기 조정 처리량을 프로비저닝하는 데 사용될 수 있습니다. [Azure Cosmos DB용 Azure Resource Manager 템플릿](./templates-samples-sql.md)에서 샘플을 확인하십시오. 디자인적으로, Azure Resource Manager 템플릿은 기존 리소스에서 프로비저닝된 처리량과 자동 크기 조정 처리량 사이에서 마이그레이션하는 데 사용할 수 없습니다. 

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 새로운 데이터베이스 또는 모든 Azure Cosmos DB API용 컨테이너 수준 리소스에서 자동 크기 조정 처리량을 프로비전하거나 기존 리소스에서 자동 크기 조정 하기 위해 사용할 수 있습니다. [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 샘플을 확인하십시오.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 새로운 데이터베이스 또는 모든 Azure Cosmos DB API용 컨테이너 수준 리소스에서 자동 크기 조정 처리량을 프로비전하거나 기존 리소스에서 자동 크기 조정 하기 위해 사용할 수 있습니다. [Azure Cosmos DB에 대한 Azure PowerShell 샘플](powershell-samples.md)에서 샘플을 확인하십시오.

## <a name="next-steps"></a>다음 단계

* [자동 크기 조정을 사용하는 프로비전된 처리량의 이점](../provision-throughput-autoscale.md#benefits-of-autoscale)에 대해 알아봅니다.
* [수동 및 자동 크기 조정 처리량 중 선택하는 방법](../how-to-choose-offer.md)을 알아봅니다.
* [자동 크기 조정 FAQ](../autoscale-faq.yml)를 검토합니다.
