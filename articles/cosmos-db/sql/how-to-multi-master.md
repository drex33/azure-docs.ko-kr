---
title: Azure Cosmos DB에서 다중 지역 쓰기를 구성하는 방법
description: Azure Cosmos DB에서 다양한 SDK를 사용하여 애플리케이션에 대한 다중 지역 쓰기를 구성하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 66f2a8cee24c1e7e26a40faecabbaf2b3e0c78d6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116967"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB를 사용하는 애플리케이션에서 다중 지역 쓰기 구성
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

사용할 수 있는 여러 쓰기 지역으로 계정이 만들어지면 Cosmos 클라이언트의 ConnectionPolicy에 대한 애플리케이션에서 두 가지를 변경하여 Azure Cosmos DB에서 다중 지역 쓰기 기능을 사용하도록 설정해야 합니다. ConnectionPolicy 내에서 UseMultipleWriteLocations를 true로 설정하고 애플리케이션이 배포되는 지역 이름을 ApplicationRegion으로 전달합니다. 이를 통해 전달된 위치에서 지역 근접도에 따라 PreferredLocations 속성이 채워집니다. 나중에 계정에 새 지역이 추가되는 경우 애플리케이션은 업데이트되거나 다시 배포될 필요 없이 가까운 지역을 자동으로 탐지하고 자동 호밍되어 지역 이벤트가 발생합니다.

> [!Note]
> 처음에 단일 쓰기 지역으로 구성된 Cosmos 계정을 중단 시간이 0인 다중 쓰기 지역에 구성할 수 있습니다. 자세한 내용은 [다중 쓰기 지역 구성](../how-to-manage-database-account.md#configure-multiple-write-regions)을 참조하세요.

## <a name="azure-portal"></a><a id="portal"></a>Azure Portal

Azure Portal에서 다중 지역 쓰기를 사용하도록 설정하려면 다음 단계를 따릅니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 메뉴에서 **전역적으로 데이터 복제** 창을 엽니다.

1. **다중 지역 쓰기** 옵션에서 **사용** 을 선택합니다. 이렇게 하면 영역을 읽고 쓰기 위해 기존 영역을 자동으로 추가합니다.

1. 지도에서 아이콘을 선택하거나 **지역 추가** 단추를 선택하여 영역을 더 추가할 수 있습니다. 추가하는 모든 지역은 읽기 및 쓰기가 모두 가능하도록 설정됩니다.

1. 지역 목록을 업데이트한 후 **저장** 을 선택하여 변경 내용을 적용합니다.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Azure Portal을 사용하여 다중 지역 쓰기를 사용하도록 설정하는 스크린샷" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 `UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `SetCurrentLocation`을 애플리케이션이 배포되고 Azure Cosmos DB가 복제되는 지역으로 설정합니다.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 `ApplicationRegion`을 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

필요에 따라 `CosmosClientBuilder` 및 `WithApplicationRegion`을 사용하여 동일한 결과를 얻을 수 있습니다.

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 클라이언트 작성기에서 `.multipleWriteRegionsEnabled(true)` 및 `.preferredRegions(preferredRegions)`를 호출합니다. 여기에서 `preferredRegions`은 하나의 요소, 즉 애플리케이션이 배포되며 Cosmos DB가 복제되는 지역을 포함하는 `List`입니다.

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a>Async Java V2 SDK

Java V2 SDK는 Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)를 사용했습니다. 애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 `policy.setUsingMultipleWriteLocations(true)` 및 `policy.setPreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript 및 TypeScript SDK

애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 `connectionPolicy.UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `connectionPolicy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

애플리케이션에서 다중 지역 쓰기를 사용하도록 설정하려면 `connection_policy.UseMultipleWriteLocations`를 `true`로 설정합니다. 또한 `connection_policy.PreferredLocations`를 애플리케이션이 배포되고 Cosmos DB가 복제되는 지역으로 설정합니다.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 세션 토큰을 사용하여 일관성 관리](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB의 충돌 유형 및 해결 정책](../conflict-resolution-policies.md)
* [Azure Cosmos DB의 고가용성](../high-availability.md)
* [Azure Cosmos DB의 일관성 수준](../consistency-levels.md)
* [Azure Cosmos DB에서 올바른 일관성 수준 선택](../consistency-levels.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](../consistency-levels.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](../consistency-levels.md)
* [전역적으로 프로비전된 처리량 크기 조정](../request-units.md)
* [전역 분산: 내부 살펴보기](../global-dist-under-the-hood.md)