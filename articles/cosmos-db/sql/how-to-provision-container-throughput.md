---
title: Azure Cosmos DB SQL API에서 컨테이너 처리량 프로비전
description: Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용하여 Azure Cosmos DB SQL API의 컨테이너 수준에서 처리량을 프로비저닝하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 1a1cebb310b98d76ecb53f1f1bbd5db4de71dc36
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117005"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Azure Cosmos 컨테이너에서 표준(수동) 처리량 프로비전 - SQL API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB SQL API의 컨테이너에서 표준(수동) 처리량을 프로비전하는 방법을 설명합니다. 단일 컨테이너 또는 [데이터베이스의 처리량을 프로비저닝](how-to-provision-database-throughput.md)하고 데이터베이스 내부의 컨테이너와 처리량을 공유할 수 있습니다. Azure Portal, Azure CLI 또는 Azure Cosmos DB SDK를 사용하여 컨테이너의 처리량을 프로비저닝할 수 있습니다.

다른 API를 사용하는 경우 처리량을 프로비전하려면 [API for MongoDB](../mongodb/how-to-provision-throughput-mongodb.md), [Cassandra API](../cassandra/how-to-provision-throughput-cassandra.md), [Gremlin API](../graph/how-to-provision-throughput-gremlin.md) 문서를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열어 **새 컨테이너** 를 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * **컨테이너 ID** 를 입력합니다.
   * **파티션 키** 값을 입력합니다(예: `/ItemID`).
   * **자동 크기 조정** 또는 **수동** 처리량을 선택하고 필요한 **컨테이너 처리량**(예: 1000RU/s)을 입력합니다. 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인** 을 선택합니다.

    :::image type="content" source="../media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="새 컬렉션이 강조 표시된 데이터 탐색기 스크린샷":::

## <a name="azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell

전용 처리량이 있는 컨테이너를 만들려면 다음을 참조하세요.

* [Azure CLI를 사용하여 컨테이너 만들기](manage-with-cli.md#create-a-container)
* [PowerShell을 사용하여 컨테이너 만들기](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra 및 MongoDB API를 제외한 모든 Azure Cosmos DB API의 처리량을 프로비전할 때 Cosmos SDKs for SQL API를 사용합니다.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [데이터베이스에 표준(수동) 처리량을 프로비저닝하는 방법](how-to-provision-database-throughput.md)
* [데이터베이스에 자동 크기 조정 처리량을 프로비저닝하는 방법](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)
