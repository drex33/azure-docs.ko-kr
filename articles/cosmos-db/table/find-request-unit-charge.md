---
title: Azure Cosmos DB에서 Table API 쿼리에 대한 RU(요청 단위) 요금 찾기
description: Azure Cosmos 컨테이너에 대해 실행되는 Table API 쿼리에 대한 RU(요청 단위) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, .NET, Java, Python 및 Node.js 언어를 사용하여 RU 요금을 찾을 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: db03c2b0c7926b4c42e8aa3effeaa3cf2ded435d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528511"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API에서 실행되는 작업에 대한 요청 단위 요금 찾기
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. 요청 요금은 모든 데이터베이스 작업에서 사용되는 요청 단위입니다. RU는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화로 생각할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다. 자세한 내용은 [요청 단위 및 고려 사항](../request-units.md) 문서를 참조하세요.

이 문서에서는 Azure Cosmos DB Table API의 컨테이너에 대해 실행한 작업의 [RU(요청 단위)](../request-units.md) 사용량을 알아보는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 초당 RU 요금은 [MongoDB용 API](../mongodb/find-request-unit-charge-mongodb.md), [Cassandra API](../cassandra/find-request-unit-charge-cassandra.md), [Gremlin API](../find-request-unit-charge-gremlin.md), [SQL API](../find-request-unit-charge.md) 문서를 참조하세요.

## <a name="use-the-net-sdk"></a>.NET SDK 사용

현재 테이블 작업에 대한 RU 요금을 반환하는 유일한 SDK는 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)입니다. `TableResult` 개체는 Azure Cosmos DB Table API에 대해 사용할 경우 SDK에 의해 자동으로 채워지는 `RequestCharge` 속성을 표시합니다.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

자세한 내용은 [빠른 시작: .NET SDK 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드](create-table-dotnet.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](../optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](../optimize-cost-reads-writes.md)