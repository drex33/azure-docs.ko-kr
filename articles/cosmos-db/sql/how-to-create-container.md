---
title: Azure Cosmos DB SQL API에서 컨테이너 만들기
description: Azure Portal, .NET, Java, Python, Node.js 및 기타 SDK를 사용하여 Azure Cosmos DB SQL API에서 컨테이너를 만드는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f45236f4ab035e03e3736811995d3dc62df7b55
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117010"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서 컨테이너 만들기
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB SQL API에서 컨테이너를 만드는 다양한 방법을 설명합니다. Azure Portal, Azure CLI, PowerShell 또는 지원되는 SDK를 사용하여 컨테이너를 만드는 방법을 보여줍니다. 이 문서에서는 컨테이너를 만들고 파티션 키를 지정하고 처리량을 프로비저닝하는 방법을 보여줍니다.

이 문서에서는 Azure Cosmos DB SQL API에서 컨테이너를 만드는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 [MongoDB용 API](../mongodb/how-to-create-container-mongodb.md), [Cassandra API](../cassandra/how-to-create-container-cassandra.md), [Gremlin API](../graph/how-to-create-container-gremlin.md) 및 [Table API](../table/how-to-create-container.md) 문서를 참조하여 컨테이너를 만듭니다.

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Azure Portal을 사용하여 컨테이너 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 계정을 선택합니다.

1. **Data Explorer** 창을 열어 **새 컨테이너** 를 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * **컨테이너 ID** 를 입력합니다.
   * **파티션 키** 값을 입력합니다(예: `/ItemID`).
   * **자동 크기 조정** 또는 **수동** 처리량을 선택하고 필요한 **컨테이너 처리량**(예: 1000RU/s)을 입력합니다. 프로비저닝하려는 처리량을 입력합니다(예: 1000RU).
   * **확인** 을 선택합니다.

    :::image type="content" source="../media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="새 컬렉션이 강조 표시된 데이터 탐색기 스크린샷":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Azure CLI를 사용하여 컨테이너 만들기

[Azure CLI를 사용하여 컨테이너를 만듭니다](manage-with-cli.md#create-a-container). 모든 Azure Cosmos DB API에 대한 모든 Azure CLI 샘플 목록은 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)을 참조하세요.

## <a name="create-a-container-using-powershell"></a>PowerShell을 사용하여 컨테이너 만들기

[PowerShell을 사용하여 컨테이너를 만듭니다](manage-with-powershell.md#create-container). 모든 Azure Cosmos DB API에 대한 모든 PowerShell 샘플 목록은 [PowerShell Samples](powershell-samples.md)를 참조하세요.

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>.NET SDK를 사용하여 컨테이너 만들기

컬렉션을 만들 때 시간 제한 예외가 발생하면 읽기 작업을 수행하여 컬렉션이 성공적으로 만들어졌는지 확인합니다. 읽기 작업은 컬렉션 만들기 작업이 성공할 때까지 예외를 발생시킵니다. 만들기 작업에서 지원되는 상태 코드 목록은 [Azure Cosmos DB에 대한 HTTP 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 문서를 참조하세요.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](../partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](../request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md)
* [Azure Cosmos 계정 작업](../account-databases-containers-items.md)