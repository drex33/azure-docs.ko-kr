---
title: 대량 가져오기 및 업데이트 작업을 위해 Azure Cosmos DB에서 대량 실행기 .NET 라이브러리 사용
description: Bulk Executor .NET 라이브러리를 사용하여 Azure Cosmos DB 문서를 대량으로 가져오고 업데이트합니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 57d61d8f1a1de83349d844aa06d62ebbff6c0de5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117182"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>대량 실행기 .NET 라이브러리를 사용하여 Azure Cosmos DB에서 대량 작업 수행
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!NOTE]
> 이 문서에서 설명하는 이 대량 실행기 라이브러리는 .NET SDK 2.x 버전을 사용하는 애플리케이션에 대해 유지 관리됩니다. 새 애플리케이션의 경우 [.NET SDK 버전 3.x](tutorial-sql-api-dotnet-bulk-import.md)에서 직접 제공되는 **대량 지원** 을 사용할 수 있으며 외부 라이브러리가 필요하지 않습니다. 

> 현재 대량 실행기 라이브러리를 사용 중이고 최신 SDK에서 대량 지원으로 마이그레이션할 계획이라면 [마이그레이션 가이드](how-to-migrate-from-bulk-executor-library.md)의 단계를 사용하여 애플리케이션을 마이그레이션합니다.

이 자습서에서는 Bulk Executor .NET 라이브러리를 사용하여 Azure Cosmos 컨테이너에 문서를 가져오고 업데이트하는 데 관한 지침을 제공합니다. Bulk Executor 라이브러리와 방대한 처리량 및 스토리지를 활용하는 방법에 대한 자세한 내용은 [Bulk Executor 라이브러리 개요](../bulk-executor-overview.md) 문서를 참조하세요. 이 자습서에서는 임의로 생성된 문서를 Azure Cosmos 컨테이너에 대량으로 가져오는 샘플 .NET 애플리케이션을 보게 됩니다. 가져온 후에는 특정 문서 필드에서 수행할 작업으로 패치를 지정하여 가져온 데이터를 대량으로 업데이트할 수 있는 방법을 보여 줍니다.

현재 Azure Cosmos DB SQL API 및 Gremlin API 계정에서만 Bulk Executor 라이브러리가 지원됩니다. 이 문서에서는 SQL API 계정을 통해 Bulk Executor .NET 라이브러리를 사용하는 방법을 설명합니다. Bulk Executor .NET 라이브러리 사용에 대해 알아보려면 [Azure Cosmos DB Gremlin API 계정에서 대량 작업 수행](../graph/bulk-executor-graph-dotnet.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Visual Studio 2019가 아직 설치되지 않은 경우 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 "Azure 개발"을 사용하도록 설정합니다.

* Azure 구독이 없는 경우 시작하기 전에 먼저 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* Azure 구독, 요금 및 약정 없이 [무료로 Azure Cosmos DB를 사용해 볼 수 있습니다](https://azure.microsoft.com/try/cosmosdb/). 또는 엔드포인트가 `https://localhost:8081`인 [Azure Cosmos DB 에뮬레이터](../local-emulator.md)를 사용할 수 있습니다. 기본 키는 [인증 요청](../local-emulator.md#authenticate-requests)에 제공됩니다.

* .NET 빠른 시작 문서의 [데이터베이스 계정 만들기](create-sql-api-dotnet.md#create-account) 섹션에 설명된 단계를 사용하여 Azure Cosmos DB SQL API 계정을 만듭니다.

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 샘플 .NET 애플리케이션을 다운로드하여 코드 작업으로 전환해 보겠습니다. 이 애플리케이션은 Azure Cosmos 계정에 저장된 데이터에 대해 대량 작업을 수행합니다. 애플리케이션을 복제하려면 명령 프롬프트를 열고 복사할 디렉터리로 이동한 후 다음 명령을 실행합니다.

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

복제된 리포지토리에는 “BulkImportSample” 및 “BulkUpdateSample” 두 개의 샘플이 포함됩니다. 샘플 애플리케이션 중 하나를 열고, Azure Cosmos DB 계정의 연결 문자열로 App.config 파일에서 연결 문자열을 업데이트하고, 솔루션을 빌드하고 실행할 수 있습니다.

“BulkImportSample” 애플리케이션은 임의의 문서를 생성하고 Azure Cosmos 계정에 대량으로 가져옵니다. “BulkUpdateSample” 애플리케이션은 특정 문서 필드에서 수행할 작업으로 패치를 지정하여 가져온 문서를 대량으로 업데이트합니다. 다음 섹션에서는 이러한 각 샘플 앱에서 코드를 검토하겠습니다.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Azure Cosmos 계정으로 데이터 대량 가져오기

1. “BulkImportSample” 폴더로 이동하고 “BulkImportSample.sln” 파일을 엽니다.  

2. 다음 코드에 나와 있는 것처럼 Azure Cosmos DB의 연결 문자열이 App.config 파일에서 검색됩니다.  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   대량 가져오기는 App.config 파일에 지정된 데이터베이스 이름, 컨테이너 이름 및 처리량 값이 포함된 새 데이터베이스 및 컨테이너를 만듭니다.

3. 다음으로 DocumentClient 개체가 직접 TCP 연결 모드를 사용하여 초기화됩니다.  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor 개체는 대기 시간 및 스로틀된 요청을 위해 높은 다시 시도 값으로 초기화됩니다. 그런 다음, 정체 제어를 해당 수명에 대한 BulkExecutor로 전달하도록 0으로 설정됩니다.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. 애플리케이션은 BulkImportAsync API를 호출합니다. .NET 라이브러리는 대량 가져오기 API의 두 가지 오버로드를 제공합니다. 하나는 직렬화된 JSON 문서 목록을 허용하고 다른 하나는 역직렬화된 POCO 문서 목록을 허용합니다. 이러한 오버로드된 메서드의 정의를 자세히 알아보려면 [API 설명서](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync)를 참조하세요.

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync 메서드는 다음과 같은 매개 변수를 수락합니다.**
   
   |**매개 변수**  |**설명** |
   |---------|---------|
   |enableUpsert    |   문서에 대한 upsert 작업을 사용하도록 설정하는 플래그입니다. 지정된 ID가 있는 문서가 이미 존재하는 경우 업데이트됩니다. 기본적으로 false로 설정됩니다.      |
   |disableAutomaticIdGeneration    |    ID의 자동 생성을 사용하지 않도록 설정하는 플래그입니다. 기본적으로 true로 설정됩니다.     |
   |maxConcurrencyPerPartitionKeyRange    | 파티션 키 범위당 최대 동시성 수준으로, null로 설정하면 라이브러리는 기본값 20을 사용하게 됩니다. |
   |maxInMemorySortingBatchSize     |  각 단계에서 API 호출로 전달되는 문서 열거자에서 가져온 최대 문서 수입니다. 대량 가져오기 전에 발생하는 메모리 내 정렬 단계의 경우 이 매개 변수를 null로 설정하면 라이브러리가 기본 최소값(documents.count, 1000000)을 사용하게 됩니다.       |
   |cancellationToken    |    대량 가져오기 작업을 정상적으로 종료하기 위한 취소 토큰입니다.     |

   **대량 가져오기 응답 개체 정의** 대량 가져오기 API 호출의 결과에는 다음과 같은 특성이 포함되어 있습니다.

   |**매개 변수**  |**설명**  |
   |---------|---------|
   |NumberOfDocumentsImported(long)   |  대량 가져오기 API 호출에 적용된 총 문서 중에서 성공적으로 가져온 총 문서 수입니다.       |
   |TotalRequestUnitsConsumed(double)   |   대량 가져오기 API 호출에서 사용된 총 요청 단위(RU)입니다.      |
   |TotalTimeTaken(TimeSpan)    |   실행을 완료하기까지 대량 가져오기 API 호출에서 사용하는 총 시간입니다.      |
   |BadInputDocuments(List\<object>)   |     대량 가져오기 API 호출에 성공적으로 가져오지 못한 잘못된 형식의 문서 목록입니다. 반환된 문서를 수정하고 가져오기를 다시 시도하세요. 잘못된 형식의 문서에는 ID 값이 문자열이 아닌 문서가 포함됩니다(null 또는 다른 데이터 형식이 잘못된 것으로 간주됨).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Azure Cosmos 계정의 대량 업데이트 데이터

BulkUpdateAsync API를 사용하여 기존 문서를 업데이트할 수 있습니다. 이 예제에서는 `Name` 필드를 새 값으로 설정하고 기존 문서에서 `Description` 필드를 제거합니다. 지원되는 업데이트 작업의 전체 집합은 [API 설명서](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)를 참조하세요.

1. “BulkUpdateSample” 폴더로 이동하고 “BulkUpdateSample.sln” 파일을 엽니다.  

2. 해당 필드 업데이트 작업과 함께 업데이트 항목을 정의합니다. 이 예에서는 `SetUpdateOperation`을 사용하여 `Name` 필드를 업데이트하고 `UnsetUpdateOperation`을 사용하여 모든 문서에서 `Description` 필드를 제거합니다. 특정 값으로 문서 필드 증가와 같은 다른 작업을 수행하거나, 배열 필드에 특정 값을 푸시하거나, 배열 필드에서 특정 값을 제거할 수 있습니다. 대량 업데이트 API에서 제공하는 다른 방법을 알아보려면 [API 설명서](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)를 참조하세요.

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. 애플리케이션은 BulkUpdateAsync API를 호출합니다. BulkUpdateAsync 메서드의 정의에 대해 알아보려면 [API 문서](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync)를 참조하세요.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync 메서드는 다음과 같은 매개 변수를 수락합니다.**

   |**매개 변수**  |**설명** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   파티션 키 범위당 최대 동시성 정도, 이 매개 변수를 null로 설정하면 라이브러리가 기본값(20)을 사용하게 됩니다.   |
   |maxInMemorySortingBatchSize    |    각 단계에서 API 호출에 전달된 업데이트 항목 열거자에서 가져온 업데이트 항목의 최대 수입니다. 대량 업데이트 전에 발생하는 메모리 내 정렬 단계의 경우 이 매개 변수를 null로 설정하면 라이브러리가 기본 최솟값(updateItems.count, 1000000)을 사용하게 됩니다.     |
   | cancellationToken|대랑 업데이트 작업을 정상적으로 종료하기 위한 취소 토큰입니다. |

   **대량 업데이트 응답 개체 정의** 대량 업데이트 API 호출의 결과에는 다음과 같은 특성이 포함되어 있습니다.

   |**매개 변수**  |**설명** |
   |---------|---------|
   |NumberOfDocumentsUpdated(long)    |   대량 업데이트 API 호출에 적용된 총 문서 중에서 성공적으로 업데이트된 문서 수입니다.      |
   |TotalRequestUnitsConsumed(double)   |    대량 업데이트 API 호출에서 사용한 총 RU(요청 단위)입니다.    |
   |TotalTimeTaken(TimeSpan)   | 실행을 완료하기까지 대량 업데이트 API 호출에서 사용하는 총 시간입니다. |
    
## <a name="performance-tips"></a>성능 팁 

Bulk Executor 라이브러리를 사용하는 경우 성능 향상을 위해 다음 사항을 고려합니다.

* 최상의 성능을 위해 Azure Cosmos 계정 작성 지역과 동일한 지역의 Azure 가상 머신에서 애플리케이션을 실행합니다.  

* 특정 Azure Cosmos 컨테이너에 해당하는 단일 가상 머신 내에서 전체 애플리케이션에 대해 단일 `BulkExecutor` 개체를 인스턴스화하는 것이 좋습니다.  

* 단일 대량 작업 API 실행은 클라이언트 시스템의 CPU 및 네트워크 IO의 큰 청크를 소비하기 때문에(이는 내부적으로 여러 작업을 생성하여 발생합니다.) 대량 작업 API 호출을 실행하는 애플리케이션 프로세스 내에서 동시 작업을 여러 개 생성하지 않는 것이 좋습니다. 단일 가상 머신에서 실행되는 단일 대량 작업 API 호출에서 전체 컨테이너의 처리량을 사용할 수 없는 경우(컨테이너의 처리량이 1백만 RU/s 미만인 경우) 대량 작업 API 호출을 동시에 실행하도록 개별 가상 머신을 만드는 것이 좋습니다.  

* `InitializeAsync()` 메서드가 대상 Cosmos DB 컨테이너 파티션 맵을 가져오기 위해 BulkExecutor 개체를 인스턴스화한 후 호출되는지 확인합니다.  

* 애플리케이션의 App.Config에서 성능 향상을 위해 **gcServer** 를 사용할 수 있도록 설정되었는지 확인합니다.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* 라이브러리는 로그 파일 또는 콘솔에 수집할 수 있는 추적을 내보냅니다. 둘 다 사용하도록 설정하려면 애플리케이션의 App.Config 파일에 다음 코드를 추가합니다.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>다음 단계

* NuGet 패키지 세부 정보 및 출시 정보에 대한 자세한 내용은 [대량 실행기 SDK 세부 정보](sql-api-sdk-bulk-executor-dot-net.md)를 참조하세요.
