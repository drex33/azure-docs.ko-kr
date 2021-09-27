---
title: Azure Cosmos DB에서 페이지 매김
description: 페이징 개념 및 연속 토큰에 대해 알아보기
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: fd4f7d583df6c949fd549cfdc3020f15468f0e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661225"
---
# <a name="pagination-in-azure-cosmos-db"></a>Azure Cosmos DB에서 페이지 매김
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB에서 쿼리는 여러 결과 페이지를 가질 수 있습니다. 이 문서에서는 Azure Cosmos DB의 쿼리 엔진에서 쿼리 결과를 여러 페이지로 분할할지 여부를 결정할 때 사용하는 조건을 알아봅니다. 연속 토큰을 필요에 따라 사용하여 여러 페이지에 걸친 쿼리 결과를 관리할 수 있습니다.

## <a name="understanding-query-executions"></a>쿼리 실행 이해하기

쿼리 결과가 여러 페이지에 걸쳐 분할되는 경우가 있습니다. 각 페이지의 결과는 별도의 쿼리 실행에 따라 생성됩니다. 단일 실행으로 쿼리 결과를 반환할 수 없는 경우 Azure Cosmos DB는 자동으로 결과를 여러 페이지로 분할합니다.

`MaxItemCount`를 설정하여 쿼리에서 반환되는 최대 항목 수를 지정할 수 있습니다. `MaxItemCount`는 요청별로 지정되며 쿼리 엔진에서 해당 항목 수 이하를 반환하도록 지시합니다. 쿼리 실행당 결과 수에 제한을 두지 않으려는 경우 `MaxItemCount`를 `-1`로 설정할 수 있습니다.

또한 쿼리 엔진이 쿼리 결과를 여러 페이지로 분할해야 하는 여러 다른 이유들이 있습니다. 여기에는 다음이 포함됩니다.

- 컨테이너가 제한되었으며 추가 쿼리 결과를 반환하는 데 사용할 수 있는 RU가 없음
- 쿼리 실행의 응답이 너무 큼
- 쿼리 실행 시간이 너무 오래 지속됨
- 쿼리 엔진이 추가 실행에서 결과를 반환하는 것이 더 효율적입니다.

쿼리 실행마다 반환되는 항목 수는 항상 `MaxItemCount`보다 작거나 같습니다. 그러나 다른 조건에서 쿼리가 반환할 수 있는 결과 수가 제한되었을 수 있습니다. 동일한 쿼리를 여러 번 실행하는 경우 페이지 수가 일정하지 않을 수 있습니다. 예를 들어 쿼리가 제한되는 경우 페이지당 사용 가능한 결과가 줄어들 수 있습니다. 즉 쿼리에 추가 페이지가 생깁니다. 경우에 따라 쿼리에서 빈 결과 페이지를 반환할 수도 있습니다.

## <a name="handling-multiple-pages-of-results"></a>여러 페이지의 결과 처리

정확한 쿼리 결과를 얻으려면 모든 페이지에 걸쳐 진행해야 합니다. 추가 페이지가 없을 때까지 계속해서 쿼리를 실행해야 합니다.

다음은 여러 페이지가 있는 쿼리에서 결과를 처리하는 몇 가지 예입니다.

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L294)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>연속 토큰

필요에 따라 .NET SDK 및 Java SDK에서 연속 토큰을 쿼리의 진행에 대한 책갈피로 사용할 수 있습니다. Azure Cosmos DB 쿼리 실행은 서버 쪽에서 상태 비저장이며, 연속 토큰을 사용하여 언제든지 다시 시작할 수 있습니다. 연속 토큰은 Node.js SDK에서 지원되지 않습니다. Python SDK의 경우 단일 파티션 쿼리에 지원되며, 쿼리 자체에 PK를 포함하기에 충분하지 않으므로 옵션 개체에 PK를 지정해야 합니다.

연속 토큰을 사용하는 몇 가지 예는 다음과 같습니다.

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L230)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

쿼리가 연속 토큰을 반환하는 경우 추가 쿼리 결과가 생깁니다.

Azure Cosmos DB의 REST API에서 `x-ms-continuation` 헤더를 사용하여 연속 토큰을 관리할 수 있습니다. .NET 또는 Java SDK가 사용된 쿼리와 마찬가지로 `x-ms-continuation` 응답 헤더가 비어 있지 않을 경우 이는 쿼리에 추가 결과가 있음을 의미합니다.

동일한 SDK 버전을 사용하는 한 연속 토큰은 만료되지 않습니다. 필요에 따라 [연속 토큰의 크기를 제한](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb)할 수 있습니다. 데이터의 양 또는 컨테이너의 실제 파티션 수와 관계없이 쿼리는 단일 연속 토큰을 반환합니다.

[GROUP BY](sql-query-group-by.md) 또는 [DISTINCT](sql-query-keywords.md#distinct) 쿼리에 상당한 양의 상태를 저장해야 하므로 해당 쿼리에는 연속 토큰을 사용할 수 없습니다. `DISTINCT` 쿼리의 경우 쿼리에 `ORDER BY`를 추가하여 연속 토큰을 사용할 수 있습니다.

다음은 연속 토큰을 사용할 수 있는 `DISTINCT` 쿼리 예제입니다.

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](../introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY 절](sql-query-order-by.md)
