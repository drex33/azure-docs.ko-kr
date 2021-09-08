---
title: 대량 실행기 라이브러리에서 Azure Cosmos DB .NET V3 SDK의 대량 지원으로 마이그레이션
description: 대량 실행기 라이브러리 사용에서 Azure Cosmos DB SDK V3의 대량 지원으로 애플리케이션을 마이그레이션하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8b7feb4e9c90690ac6716c2344756d7718d2e4ac
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116925"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>대량 실행기 라이브러리에서 Azure Cosmos DB .NET V3 SDK의 대량 지원으로 마이그레이션
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 [.NET 대량 실행기 라이브러리](bulk-executor-dot-net.md)를 사용하는 기존 애플리케이션의 코드를 최신 버전의 .NET SDK에서 [대량 지원](tutorial-sql-api-dotnet-bulk-import.md) 기능으로 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="enable-bulk-support"></a>대량 지원 사용

[AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) 구성을 통해 `CosmosClient` 인스턴스에서 대량 지원을 사용합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>각 작업(operation)에 대한 작업(task) 만들기

.NET SDK의 대량 지원은 [작업 병렬 라이브러리](/dotnet/standard/parallel-programming/task-parallel-library-tpl)와 동시에 발생하는 그룹화 작업을 활용하여 작동합니다. 

SDK에는 문서 또는 작업 목록을 입력 매개 변수로 사용하는 단일 메서드가 없지만 대량으로 실행하려는 각 작업에 대해 작업을 만든 다음 완료될 때까지 기다리기만 하면 됩니다.

예를 들어 초기 입력이 각 항목에 다음 스키마가 있는 항목 목록인 경우:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

대량 가져오기(BulkExecutor.BulkImportAsync 사용과 유사)를 수행하려면 `CreateItemAsync`에 대한 동시 호출이 있어야 합니다. 예를 들면 다음과 같습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

대량 *업데이트*([BulkExecutor.BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync) 사용과 유사)를 수행하려면 항목 값을 업데이트한 후 `ReplaceItemAsync` 메서드에 대한 동시 호출이 있어야 합니다. 예를 들면 다음과 같습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

그리고 대량 *삭제*([BulkExecutor.BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync) 사용과 유사)를 수행하려면 각 항목의 `id` 및 파티션 키를 사용하여 `DeleteItemAsync`에 대한 동시 호출을 수행해야 합니다. 예를 들면 다음과 같습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>작업 결과 상태 캡처

이전 코드 예에서는 동시 작업 목록을 만들고 각 작업에서 `CaptureOperationResponse` 메서드를 호출했습니다. 이 메서드는 오류를 캡처하고 [요청 단위 사용량](../request-units.md)을 추적하여 BulkExecutor와 *유사한 응답 스키마* 를 유지할 수 있게 해주는 확장입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

여기서 `OperationResponse`는 다음과 같이 선언됩니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>작업을 동시에 실행

전체 작업 목록의 범위를 추적하기 위해 다음 도우미 클래스를 사용합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync` 메서드는 모든 작업이 완료될 때까지 기다리며 다음과 같이 사용할 수 있습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>통계 캡처

이전 코드는 모든 작업이 완료될 때까지 기다렸다가 필요한 통계를 계산합니다. 이러한 통계는 대량 실행기 라이브러리의 [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse) 통계와 유사합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`에는 다음이 포함됩니다.

1. 대량 지원을 통해 작업 목록을 처리하는 데 걸린 총 시간입니다.
1. 성공한 작업의 수입니다.
1. 사용된 총 요청 단위입니다.
1. 오류가 있는 경우 예외가 포함된 튜플 목록과 로깅 및 식별을 위한 관련 항목이 표시됩니다.

## <a name="retry-configuration"></a>재시도 구성

대량 실행기 라이브러리에는 라이브러리에 제어를 위임하기 위해 [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions)의 `MaxRetryWaitTimeInSeconds` 및 `MaxRetryAttemptsOnThrottledRequests`를 `0`으로 설정하는 것을 언급한 [지침](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)이 있었습니다.

.NET SDK의 대량 지원에는 숨겨진 동작이 없습니다. [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) 및 [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)를 통해 직접 재시도 옵션을 구성할 수 있습니다.

> [!NOTE]
> 프로비저닝된 요청 단위가 데이터 양에 따라 예상보다 훨씬 낮은 경우 높은 값으로 설정하는 것을 고려할 수 있습니다. 대량 작업은 시간이 더 오래 걸리지만 재시도 횟수가 높기 때문에 완전히 성공할 확률이 더 높습니다.

## <a name="performance-improvements"></a>성능 개선

.NET SDK를 사용한 다른 작업과 마찬가지로 스트리밍 API를 사용하면 성능이 향상되고 불필요한 직렬화가 방지됩니다. 

스트리밍 API 사용은 사용하는 데이터의 특성이 바이트 스트리밍(예: 파일 스트리밍)의 특성과 일치하는 경우에만 가능합니다. 이러한 경우 `CreateItemStreamAsync`, `ReplaceItemStreamAsync` 또는 `DeleteItemStreamAsync` 메서드를 사용하고 `ResponseMessage`(`ItemResponse` 대신)를 사용하면 달성할 수 있는 처리량이 증가합니다.

## <a name="next-steps"></a>다음 단계

* .NET SDK 릴리스에 대한 자세한 내용은 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 문서를 참조하세요.
* GitHub에서 전체 [마이그레이션 소스 코드](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)를 가져옵니다.
* [GitHub의 추가 대량 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.