---
title: .NET SDK를 사용하는 Azure Cosmos DB의 트랜잭션 일괄 처리 작업
description: Azure Cosmos DB .NET SDK에서 TransactionalBatch를 사용하여 성공 또는 실패하는 지점 작업 그룹을 수행하는 방법을 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f3bef945fe5e676b221650d9f43a9955e4483133
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117070"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>.NET SDK를 사용하는 Azure Cosmos DB의 트랜잭션 일괄 처리 작업
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

트랜잭션 일괄 처리는 성공 또는 실패해야 하는 지점 작업 그룹과 컨테이너의 동일한 파티션 키를 함께 제공합니다. .NET SDK에서 `TransactionalBatch` 클래스는 이러한 작업 일괄 처리를 정의하는 데 사용됩니다. 트랜잭션 일괄 처리 작업 내에 설명된 순서대로 모든 작업이 성공적으로 수행되면 트랜잭션이 커밋됩니다. 그러나 어느 한 작업이라도 실패하면 전체 트랜잭션이 롤백됩니다.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB에서 트랜잭션이란?

일반적인 데이터베이스의 트랜잭션은 하나의 논리적 작업 단위로 수행되는 작업 시퀀스로 정의할 수 있습니다. 각 트랜잭션은 ACID(원자성, 일관성, 격리, 내구성) 속성을 보장합니다.

* **원자성** 은 트랜잭션 내부에서 수행된 모든 작업이 하나의 단위로 처리되어 모두 커밋되거나 커밋되지 않도록 합니다.
* **일관성** 은 데이터가 트랜잭션 간에 항상 올바른 상태로 유지되도록 합니다.
* **격리** 는 두 트랜잭션이 서로를 방해하지 않도록 합니다. 많은 상용 시스템은 애플리케이션 요구에 따라 사용할 수 있는 여러 격리 수준을 제공합니다.
* **내구성** 은 데이터베이스에서 커밋된 변경 내용이 항상 유지되도록 합니다.
Azure Cosmos DB는 동일한 [논리 파티션 키](../partitioning-overview.md) 내의 작업에 대해 [스냅샷 격리를 사용하는 완전한 ACID 규격 트랜잭션](database-transactions-optimistic-concurrency.md)을 지원합니다.

## <a name="transactional-batch-operations-vs-stored-procedures"></a>트랜잭션 일괄 처리 작업 및 저장 프로시저

현재, Azure Cosmos DB는 작업에 대한 트랜잭션 범위도 제공하는 저장 프로시저를 지원합니다. 그러나 트랜잭션 일괄 처리 작업은 다음과 같은 이점을 제공합니다.

* **언어 옵션** – 트랜잭션 일괄 처리는 이미 작업 중인 SDK 및 언어에서 지원되지만 저장 프로시저는 JavaScript로 작성해야 합니다.
* **코드 버전 관리** - 애플리케이션 코드의 버전을 관리하고 CI/CD 파이프라인에 온보딩하는 것은 저장 프로시저의 업데이트를 오케스트레이션하고 적절한 시간에 롤오버를 수행하는 것보다 훨씬 자연스럽게 진행됩니다. 또한 변경 내용을 보다 쉽게 롤백할 수 있습니다.
* **성능** - 저장 프로시저 실행과 비교할 때 해당 작업에 대한 대기 시간이 최대 30% 줄어듭니다.
* **콘텐츠 serialization** – 트랜잭션 일괄 처리 내의 각 작업은 해당 페이로드에 대한 사용자 지정 serialization 옵션을 활용할 수 있습니다.

## <a name="how-to-create-a-transactional-batch-operation"></a>트랜잭션 일괄 처리 작업을 만드는 방법

트랜잭션 일괄 처리 작업을 만들 때 컨테이너 인스턴스에서 시작하고 `CreateTransactionalBatch`를 호출합니다.

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

다음으로 일괄 처리에 대해 `ExecuteAsync`를 호출해야 합니다.

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

응답이 수신되면 성공 여부를 검사하고 결과를 추출합니다.

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

오류가 발생하는 경우 실패한 작업에 해당 오류에 대한 상태 코드가 포함됩니다. 다른 모든 작업에는 424 상태 코드(실패한 종속성)가 포함됩니다. 아래 예제에서 작업은 이미 존재하는 항목을 만들려고 하므로 실패합니다(409 HttpStatusCode.Conflict). 이 상태 코드를 사용하여 트랜잭션 오류의 원인을 식별할 수 있습니다.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>트랜잭션 일괄 처리 작업을 실행하는 방법

`ExecuteAsync` 메서드가 호출되면 `TransactionalBatch` 개체의 모든 작업이 그룹화되고, 단일 페이로드로 serialize되고, 단일 요청으로 Azure Cosmos DB 서비스에 전송됩니다.

이 서비스는 요청을 수신하고 트랜잭션 범위 내에서 모든 작업을 실행하며 동일한 serialization 프로토콜을 사용하여 응답을 반환합니다. 이 응답은 성공 또는 실패이며 작업당 개별 작업 응답을 제공합니다.

SDK는 결과를 확인하고 필요에 따라 각 내부 작업 결과를 추출하기 위한 응답을 제공합니다.

## <a name="limitations"></a>제한 사항

현재는 다음과 같은 두 가지 알려진 제한 사항이 있습니다.

* Azure Cosmos DB 요청 크기 제한은 `TransactionalBatch` 페이로드의 크기를 2MB를 초과하지 않도록 제한하고 최대 실행 시간은 5초입니다.
* 성능이 SLA 내에서 예상대로 유지되도록 하기 위해 현재 `TransactionalBatch`별로 작업이 100개로 제한됩니다.

## <a name="next-steps"></a>다음 단계

* [TransactionalBatch로 수행할 수 있는 작업](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)에 대해 자세히 알아봅니다.

* Cosmos DB .NET SDK를 사용하는 방법에 대한 자세한 내용은 [샘플](sql-api-dotnet-v3sdk-samples.md)을 참조하세요.
