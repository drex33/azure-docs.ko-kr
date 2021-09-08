---
title: Azure Cosmos DB API for MongoDB 작업에 대한 요청 단위 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행되는 MongoDB 쿼리에 대한 RU(요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, MongoDB .NET, Java, Node.js 드라이버를 사용할 수 있습니다.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.custom: devx-track-js
ms.openlocfilehash: c2ad6ef764fa13a6876f1727314d5a7944ce7d31
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035221"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB에서 실행되는 작업에 대한 요청 단위 요금을 찾습니다.
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. 요청 요금은 모든 데이터베이스 작업에서 사용되는 요청 단위입니다. RU는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화로 생각할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다. 자세한 내용은 [요청 단위 및 고려 사항](../request-units.md) 도움말을 참조하세요.

이 문서에서는 Azure Cosmos DB API for MongoDB의 컨테이너에 대해 실행한 작업의 [RU(요청 단위)](../request-units.md) 사용량을 알아보는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 [SQL API](../find-request-unit-charge.md), [Cassandra API](../cassandra/find-request-unit-charge-cassandra.md), [Gremlin API](../find-request-unit-charge-gremlin.md) 및 [Table API](../table/find-request-unit-charge.md) 문서를 참조하여 RU/s 요금을 찾습니다.

RU 요금은 `getLastRequestStatistics`라는 사용자 지정 [데이터베이스 명령](https://docs.mongodb.com/manual/reference/command/)을 통해 표시됩니다. 이 명령은 마지막으로 실행된 작업의 이름, 해당 작업의 요청 요금 및 해당 작업 기간이 포함된 문서를 반환합니다. Azure Cosmos DB API for MongoDB를 사용하는 경우 RU 요금을 검색하는 여러 옵션이 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들고](create-mongodb-dotnet.md#create-a-database-account) 데이터를 입력하거나, 이미 데이터가 들어 있는 기존 계정을 선택합니다.

1. **Data Explorer** 창으로 이동한 다음, 작업할 컨테이너를 선택합니다.

1. 컨테이너 이름 옆에 있는 **...** 을 선택하고 **새 쿼리** 를 선택합니다.

1. 유효한 쿼리를 입력한 다음, **쿼리 실행** 을 선택합니다.

1. **쿼리 통계** 를 선택하여 방금 실행한 요청의 실제 요청 요금을 표시합니다. 이 쿼리 편집기를 사용하면 쿼리 조건자에 대해서만 요청 단위 요금을 실행하고 볼 수 있습니다. insert 문과 같은 데이터 조작 명령에는 이 편집기를 사용할 수 없습니다.

   :::image type="content" source="../media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure Portal의 MongoDB 쿼리 요청 요금 스크린샷":::

1. 데이터 조작 명령에 대한 요청 요금을 받으려면 Mongo 셸, [Robo 3T](connect-using-robomongo.md), [MongoDB Compass](connect-using-compass.md)와 같은 셸 기반 UI 또는 셸 스크립팅을 사용하는 VS 코드 확장에서 `getLastRequestStatistics` 명령을 실행합니다.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>MongoDB .NET 드라이버 사용

[공식 MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)를 사용하는 경우 `IMongoDatabase` 개체에서 `RunCommand` 메서드를 호출하여 명령을 실행할 수 있습니다. 이 메서드를 호출하려면 다음과 같이 `Command<>` 추상 클래스를 구현해야 합니다.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

자세한 내용은 [빠른 시작: Azure Cosmos DB API for MongoDB를 사용하여 .NET 웹앱 빌드](create-mongodb-dotnet.md)를 참조하세요.

## <a name="use-the-mongodb-java-driver"></a>MongoDB Java 드라이버 사용


[공식 MongoDB Java 드라이버](https://mongodb.github.io/mongo-java-driver/)를 사용하는 경우 `MongoDatabase` 개체에서 `runCommand` 메서드를 호출하여 명령을 실행할 수 있습니다.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

자세한 내용은 [빠른 시작: Azure Cosmos DB API for MongoDB 및 Java SDK를 사용하여 웹앱 빌드](create-mongodb-java.md)를 참조하세요.

## <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js 드라이버 사용

[공식 MongoDB Node.js 드라이버](https://mongodb.github.io/node-mongodb-native/)를 사용하는 경우 `db` 개체에서 `command` 메서드를 호출하여 명령을 실행할 수 있습니다.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

자세한 내용은 [빠른 시작: 기존 MongoDB Node.js 웹앱을 Azure Cosmos DB로 마이그레이션](create-mongodb-nodejs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](../optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](../optimize-cost-reads-writes.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.