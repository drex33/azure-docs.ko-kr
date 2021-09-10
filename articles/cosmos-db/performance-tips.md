---
title: .NET SDK v2에 대한 Azure Cosmos DB 성능 팁
description: Azure Cosmos DB .NET v2 SDK 성능을 개선하기 위한 클라이언트 구성 옵션을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/08/2021
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: b120d955b6cfc86922a7ea57af755c528877538b
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113516634"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Azure Cosmos DB 및 .NET SDK v2에 대한 성능 팁
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. Azure Cosmos DB를 사용하여 데이터베이스를 스케일링하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요는 없습니다. 규모를 확장 및 축소하는 방법이 API 호출을 하나 만드는 것만큼 쉽습니다. 자세한 내용은 [컨테이너 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md) 또는 [데이터베이스 처리량을 프로비전하는 방법](how-to-provision-database-throughput.md)을 참조하세요. 그러나 네트워크 호출을 통해 Azure Cosmos DB에 액세스하기 때문에 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)를 사용할 때 최대 성능을 얻기 위해 클라이언트 쪽에서 최적화 작업을 수행할 수 있습니다.

따라서 데이터베이스 성능을 향상시키려는 경우 다음 옵션을 고려합니다.

## <a name="upgrade-to-the-net-v3-sdk"></a>.NET V3 SDK로 업그레이드

[.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3)가 릴리스되었습니다. .NET v3 SDK를 사용하는 경우, 다음 정보에 대한 [.NET v3 성능 가이드](performance-tips-dotnet-sdk-v3-sql.md)를 참조하세요.

- 직접 TCP 모드로 기본값 지정
- Stream API 지원
- System.Text.JSON 사용을 허용하는 사용자 지정 직렬 변환기 지원
- 통합 일괄 처리 및 대량 지원

## <a name="hosting-recommendations"></a><a id="hosting"></a> 호스팅 권장 사항

**쿼리 집약적인 워크로드의 경우 Linux 또는 Windows 32비트가 아니라 Windows 64비트 호스트 처리 사용**

성능을 향상시키려면 Windows 64비트 호스트 처리를 사용하는 것이 좋습니다. SQL SDK에는 로컬에서 쿼리를 구문 분석하고 최적화하는 네이티브 ServiceInterop.dll이 포함되어 있습니다. ServiceInterop.dll은 Windows x64 플랫폼에서만 지원됩니다. ServiceInterop.dll을 사용할 수 없는 Linux 및 기타 지원되지 않는 플랫폼의 경우 게이트웨이에 대한 네트워크 호출을 추가로 수행하여 최적화된 쿼리를 가져옵니다. 다음 유형의 애플리케이션은 기본적으로 32비트 호스트 처리를 사용합니다. 호스트 처리를 64비트 처리로 변경하려면 애플리케이션 유형에 따라 다음 단계를 따르세요.

- 실행 가능한 애플리케이션의 경우 **프로젝트 속성** 창의 **빌드** 탭에서 [플랫폼 대상](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019)을 **x64** 로 설정하여 호스트 처리를 변경할 수 있습니다.

- VSTest 기반 테스트 프로젝트의 경우 Visual Studio **테스트** 메뉴에서 **테스트** > **테스트 설정** > **기본 프로세서 아키텍처를 X64로 설정** 을 선택하여 호스트 처리를 변경할 수 있습니다.

- 로컬로 배포된 ASP.NET 웹 애플리케이션의 경우 **도구** > **옵션** > **프로젝트 및 솔루션** > **웹 프로세스** 에서 **웹 사이트 및 프로젝트에 64비트 버전의 IIS Express 사용** 을 선택하면 호스트 처리를 변경할 수 있습니다.

- Azure에 배포된 ASP.NET 웹 애플리케이션의 경우 Azure Portal의 **애플리케이션 설정** 에서 **64비트** 플랫폼을 선택하여 호스트 처리를 변경할 수 있습니다.

> [!NOTE] 
> 기본적으로 새 Visual Studio 프로젝트는 **임의 CPU** 로 설정됩니다. **x86** 으로 전환되지 않도록 프로젝트를 **x64** 로 설정하는 것이 좋습니다. **모든 CPU** 로 설정된 프로젝트는 x86 전용 종속성이 추가되면 **x86** 으로 쉽게 전환할 수 있습니다.<br/>
> ServiceInterop.dll은 SDK DLL이 실행되는 폴더에 있어야 합니다. 이는 DLL을 수동으로 복사하거나 사용자 지정 빌드/배포 시스템이 있는 경우에만 문제가 됩니다.
    
**서버 측 가비지 수집(GC) 켜기**

경우에 따라 가비지 수집의 빈도를 줄이는 것이 도움이 될 수 있습니다. .NET에서 [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)를 `true`로 설정합니다.

**클라이언트 워크로드 스케일 아웃**

높은 처리량 수준(50,000RU/s 이상)에서 테스트하는 경우 컴퓨터가 CPU 또는 네트워크 사용을 제한하기 때문에 클라이언트 애플리케이션에 병목 현상이 발생할 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

> [!NOTE] 
> CPU 사용량이 많으면 대기 시간이 증가하고 요청 시간 제한 예외가 발생할 수 있습니다.

## <a name="networking"></a><a id="networking"></a> 네트워킹

**연결 정책: 직접 연결 모드 사용**

.NET V2 SDK 기본 연결 모드는 게이트웨이입니다. `ConnectionPolicy` 매개 변수를 사용하여 `DocumentClient` 인스턴스를 구성하는 동안 연결 모드를 구성합니다. 직접 모드를 사용하는 경우 `ConnectionPolicy` 매개 변수를 사용하여 `Protocol`도 설정해야 합니다. 다양한 연결 옵션에 관한 자세한 정보는 [연결 모드](sql-sdk-connection-modes.md) 문서를 참조하세요.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**사용 후 삭제되는 포트 고갈**

인스턴스에서 연결 볼륨이 높거나 포트 사용량이 많으면 먼저 클라이언트 인스턴스가 싱글톤인지 확인합니다. 즉, 클라이언트 인스턴스는 애플리케이션의 수명 동안 고유해야 합니다.

TCP 프로토콜에서 실행되는 경우 클라이언트는 HTTPS 프로토콜과는 반대로 수명이 긴 연결을 사용하여 대기 시간을 최적화합니다. 그러면 2분 동안 활동이 없을 경우 연결이 종료됩니다.

스파스 액세스 권한이 있고 게이트웨이 모드 액세스보다 연결 수가 더 많은 경우 다음을 수행할 수 있습니다.

* [ConnectionPolicy.PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) 속성을 `PrivatePortPool`(프레임워크 버전>= 4.6.1 및 .NET core 버전 >= 2.0에서 유효)로 구성합니다. 이 속성을 사용하면 다른 Azure Cosmos DB 대상 엔드포인트에 대해 SDK가 임시 포트의 작은 풀을 사용할 수 있습니다.
* [ConnectionPolicy.IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) 속성을 10분 이상으로 구성해야 합니다. 권장 값은 20분에서 24시간 사이입니다.

**첫 번째 요청 시 시작 대기 시간을 피하기 위해 OpenAsync 호출**

기본적으로 첫 번째 요청은 주소 라우팅 테이블을 가져와야 하기 때문에 대기 시간이 더 깁니다. [SDK V2](sql-api-sdk-dotnet.md)를 사용하는 경우 초기화 중에 `OpenAsync()`를 한 번 호출하여 첫 번째 요청에서 이러한 시작 대기 시간을 방지하세요. 호출은 다음과 같습니다. `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync`는 계정의 모든 컨테이너에 대한 주소 라우팅 테이블을 얻기 위한 요청을 생성합니다. 많은 컨테이너가 있지만 애플리케이션이 컨테이너의 하위 집합에 액세스하는 계정의 경우 `OpenAsync`는 불필요한 양의 트래픽을 생성하여 초기화를 느리게 생성합니다. 따라서 `OpenAsync`를 사용하면 애플리케이션 시작 속도가 느려지므로 이 시나리오에서 유용하지 않을 수 있습니다.

**성능을 위해 동일한 Azure 지역에 클라이언트 배치**

가능한 경우 Azure Cosmos DB를 호출하는 모든 애플리케이션을 Azure Cosmos DB 데이터베이스와 같은 지역에 배치합니다. 대략 비교한다면, 같은 지역 내의 Azure Cosmos DB 호출은 1-2ms 내에 완료되지만, 미국 서부와 동부 해안 사이의 대기 시간은 50ms보다 큽니다. 클라이언트에서 Azure 데이터 센터 경계로 요청이 전달되는 경로에 따라 이러한 요청 간 대기 시간은 달라질 수 있습니다. 호출하는 애플리케이션이 프로비저닝된 Azure Cosmos DB 엔드포인트와 같은 Azure 지역 내에 있게 하면 대기 시간을 최대한 줄일 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 연결 정책" border="false":::

**스레드/작업 수 늘리기**
<a id="increase-threads"></a>

Azure Cosmos DB 호출은 네트워크를 통해 수행되므로 클라이언트 애플리케이션이 요청 간에 대기하는 시간이 최소한이 되도록 요청의 병렬 처리 수준을 다양하게 지정해야 할 수 있습니다. 예를 들어 .NET [작업 병렬 라이브러리](/dotnet/standard/parallel-programming/task-parallel-library-tpl)를 사용 하는 경우 Azure Cosmos DB에서 읽거나 쓸 수 있는 수백 개의 작업 순서를 만듭니다.

**가속화된 네트워킹 사용**
 
대기 시간과 CPU 지터를 줄이려면 클라이언트 가상 머신에서 가속화된 네트워킹을 사용하도록 설정하는 것이 좋습니다. [가속화된 네트워킹으로 Windows 가상 머신 생성하기](../virtual-network/create-vm-accelerated-networking-powershell.md) 또는 [가속화된 네트워킹으로 Linux 가상 머신 생성하기](../virtual-network/create-vm-accelerated-networking-cli.md)를 참조하세요.

## <a name="sdk-usage"></a>SDK 사용

**최신 SDK 설치**

Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.

**애플리케이션 수명 동안 singleton Azure Cosmos DB 클라이언트 사용**

각 `DocumentClient` 인스턴스는 스레드로부터 안전하고 직접 모드에서 작동하는 경우 효율적인 연결 관리와 주소 캐싱을 수행합니다. 효율적으로 연결을 관리하고 SDK 클라이언트 성능을 향상시키려면 애플리케이션 수명 동안 `AppDomain`당 단일 인스턴스를 사용하는 것이 좋습니다.

**차단되는 호출 방지**

Cosmos DB SDK는 많은 요청을 동시에 처리하도록 설계되어야 합니다. 비동기 API를 사용하면 차단 호출을 기다리지 않고 작은 스레드 풀에서 수천 개의 동시 요청을 처리할 수 있습니다. 스레드는 장기 실행 동기 작업이 완료되기를 기다리는 대신 다른 요청에서 작업할 수 있습니다.

Cosmos DB SDK를 사용하는 앱의 일반적인 성능 문제는 비동기식일 수 있는 호출을 차단하는 것입니다. 많은 동기 차단 호출은 [스레드 풀 결핍](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) 및 응답 시간 저하로 이어집니다.

**수행하지 않아야 할 작업**:

* [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) 또는 [Task.Result](/dotnet/api/system.threading.tasks.task-1.result)를 호출하여 비동기 실행을 차단합니다.
* [Task.Run](/dotnet/api/system.threading.tasks.task.run)을 사용하여 동기식 API를 비동기식으로 생성합니다.
* 공통 코드 경로에서 잠금을 획득합니다. Cosmos DB .NET SDK는 코드를 병렬로 실행하도록 설계될 때 가장 성능이 좋습니다.
* [Task.Run](/dotnet/api/system.threading.tasks.task.run)을 호출하고 즉시 기다립니다. ASP.NET Core는 이미 일반 스레드 풀 스레드에서 앱 코드를 실행하므로 Task.Run을 호출하면 불필요한 스레드 풀 일정만 추가로 발생합니다. 예약된 코드가 스레드를 차단하더라도 Task.Run은 이를 방지하지 않습니다.
* 차단 호출을 사용하여 쿼리를 동기적으로 드레이닝하는 `DocumentClient.CreateDocumentQuery(...)`에서 ToList()를 사용하지 마세요. 쿼리를 비동기식으로 드레이닝하려면 [AsDocumentQuery()](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/a4348f8cc0750434376b02ae64ca24237da28cd7/samples/code-samples/Queries/Program.cs#L690)를 사용합니다.

**해야 할 일**:

* Cosmos DB .NET API를 비동기식으로 호출합니다.
* 전체 호출 스택은 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 패턴을 활용하기 위해 비동기식입니다.

[PerfView](https://github.com/Microsoft/perfview)와 같은 프로파일러를 사용하여 [스레드 풀](/windows/desktop/procthread/thread-pools)에 자주 추가되는 스레드를 찾을 수 있습니다. `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 이벤트는 스레드 풀에 추가된 스레드를 나타냅니다.

**게이트웨이 모드를 사용하는 경우 호스트당 System.Net MaxConnections 늘리기**

Azure Cosmos DB 요청은 게이트웨이 모드를 사용할 때 HTTPS/REST를 통해 수행됩니다. 호스트 이름이나 IP 주소당 기본 연결 한도가 적용됩니다. 클라이언트 라이브러리가 Azure Cosmos DB에 대한 다중 동시 연결을 활용할 수 있도록 `MaxConnections`를 더 높은 값(100-1,000)으로 설정해야 할 수도 있습니다. .NET SDK 1.8.0 이상에서 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit)의 기본값은 50입니다. 값을 변경하려면 [Documents.Client.ConnectionPolicy.MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit)를 더 높은 값으로 설정할 수 있습니다.

**분할된 컬렉션에 대한 병렬 쿼리 튜닝**

SQL .NET SDK 1.9.0 이상은 분할된 컬렉션을 병렬로 쿼리할 수 있는 병렬 쿼리를 지원합니다. 자세한 내용은 SDK 사용과 관련된 [코드 샘플](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs)을 참조하세요. 병렬 쿼리는 직렬 쿼리보다 쿼리 대기 시간과 처리량을 개선하도록 설계되었습니다. 병렬 쿼리는 요구 사항에 맞게 튜닝할 수 있는 두 가지 매개 변수를 제공합니다. 
- `MaxDegreeOfParallelism`은 병렬로 쿼리할 수 있는 최대 파티션 수를 제어합니다. 
- `MaxBufferedItemCount`는 미리 가져온 결과의 수를 제어합니다.

***병렬 처리 수준 튜닝***

병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동합니다. 그러나 개별 파티션의 데이터는 쿼리와 관련하여 순차적으로 가져옵니다. [SDK V2](sql-api-sdk-dotnet.md)의 `MaxDegreeOfParallelism`을 파티션 수로 설정하면 다른 모든 시스템 조건을 동일하게 유지하는 경우 가장 성능이 뛰어난 쿼리를 달성할 수 있습니다. 파티션 수를 모르면 병렬 처리 수준을 높은 수로 설정할 수 있습니다. 시스템은 최소값(파티션 수, 사용자 제공 입력)을 병렬 처리 수준으로 선택합니다.

데이터가 쿼리와 관련하여 모든 파티션에 균등하게 분산된 경우 병렬 쿼리가 최고의 성능을 발휘합니다. 쿼리에서 반환된 전체 또는 대부분의 데이터가 몇 개의 파티션(최악의 경우 하나의 파티션)에 집중되도록 분할된 컬렉션이 분할되는 경우 해당 파티션으로 인해 쿼리 성능에 병목 상태가 발생합니다.

***MaxBufferedItemCount 튜닝***
    
병렬 쿼리는 클라이언트에서 현재 결과 일괄 처리를 처리하는 동안 결과를 프리페치하도록 설계되었습니다. 프리페치를 사용하면 쿼리의 전체 대기 시간을 단축할 수 있습니다. `MaxBufferedItemCount` 매개 변수는 프리페치된 결과 수를 제한합니다. `MaxBufferedItemCount`를 반환된 결과의 예상 수(또는 더 높은 숫자)로 설정하면 쿼리가 프리페치의 최대 이점을 얻을 수 있습니다.

프리페치는 병렬 처리 수준과 관계없이 같은 방식으로 작동하고, 모든 파티션의 데이터를 위한 단일 버퍼가 있습니다.  

**RetryAfter 간격으로 백오프 구현**

성능 테스트 중에는 작은 비율의 요청이 제한될 때까지 로드를 늘려야 합니다. 요청이 제한되면 클라이언트 애플리케이션은 서버에서 지정한 다시 시도 간격 동안 스로틀에서 백오프해야 합니다. 백오프를 적용하면 다시 시도 사이에 기다리는 시간을 최소화할 수 있습니다. 

다시 시도 정책 지원은 다음 SDK에 포함되어 있습니다.
- [SQL용 .NET SDK](sql-api-sdk-dotnet.md) 및 [SQL용 Java SDK](sql-api-sdk-java.md) 버전 1.8.0 이상
- [SQL용 Node.js SDK](sql-api-sdk-node.md) 및 [SQL용 Python SDK](sql-api-sdk-python.md) 버전 1.9.0 이상
- 지원되는 모든 버전의 [.NET Core](sql-api-sdk-dotnet-core.md) SDK 

자세한 내용은 [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter)를 참조하세요.
    
.NET SDK 버전 1.19 이상에서는 다음 샘플과 같이 추가 진단 정보를 기록하고 대기 시간 문제를 해결하기 위한 메커니즘이 있습니다. 읽기 대기 시간이 더 긴 요청에 대한 진단 문자열을 기록할 수 있습니다. 캡처된 진단 문자열을 통해 지정된 요청에 대해 429 오류를 받은 횟수를 알 수 있습니다.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**짧은 읽기 대기 시간 동안 문서 URI 캐시**

최상의 문서 읽기 성능이 필요할 때마다 문서 URI를 캐시합니다. 리소스를 만들 때 리소스 ID를 캐싱하는 논리를 정의해야 합니다. 리소스 ID 기반 조회는 이름 기반 조회보다 빠르므로 이러한 값을 캐싱하면 성능이 향상됩니다.

**성능 향상을 위해 쿼리/읽기 피드에 맞게 페이지 크기 조정**

읽기 피드 기능을 사용하여 대량의 문서 읽기를 수행하거나(예: `ReadDocumentFeedAsync`) SQL 쿼리를 실행하면 결과 집합이 너무 큰 경우 결과가 분할되어 반환됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

적용 가능한 모든 결과를 검색하는 데 필요한 네트워크 왕복 횟수를 줄이려면 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)를 사용하여 최대 1,000개의 헤더를 요청하여 페이지 크기를 늘릴 수 있습니다. 사용자 인터페이스 또는 애플리케이션 API에서 한 번에 10개의 결과만 반환하는 경우와 같이 몇 가지 결과만 표시해야 하는 경우 페이지 크기를 10으로 줄여 읽기 및 쿼리에 사용되는 처리량을 줄일 수도 있습니다.

> [!NOTE] 
> `maxItemCount` 속성은 페이지 매김에만 사용하는 것이 아닙니다. 주요 용도는 단일 페이지에서 반환되는 최대 항목 수를 줄여 쿼리 성능을 향상시키는 것입니다.  

또한 Azure Cosmos DB SDK를 사용하여 페이지 크기를 설정할 수도 있습니다. `FeedOptions`의 [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount) 속성을 사용하면 열거 작업에서 반환할 최대 항목 수를 설정할 수 있습니다. `maxItemCount`가 -1로 설정되면 SDK는 문서 크기에 따라 최적의 값을 자동으로 찾습니다. 예를 들면 다음과 같습니다.
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
쿼리가 실행되면 결과 데이터가 TCP 패킷 내에서 전송됩니다. `maxItemCount` 값을 너무 낮게 지정하면 TCP 패킷 내에서 데이터를 전송하는 데 필요한 이동 횟수가 많아져 성능에 영향을 줍니다. 따라서 `maxItemCount` 속성에 대해 어떤 값을 설정할지 확실하지 않은 경우 -1로 설정하고 SDK가 기본값을 선택하도록 하는 것이 가장 좋습니다.

**스레드/작업 수 늘리기**

이 문서의 네트워킹 섹션에서 [스레드/작업 수 늘리기](#increase-threads)를 참조하세요.

## <a name="indexing-policy"></a>인덱싱 정책
 
**더 빠른 쓰기에 대한 인덱싱에서 사용하지 않는 경로 제외**

Azure Cosmos DB 인덱싱 정책을 사용하면 인덱싱 경로(IndexingPolicy.IncludedPaths 및 IndexingPolicy.ExcludedPaths)를 사용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수도 있습니다. 인덱싱 경로는 패턴을 미리 알고 있는 시나리오의 경우 인덱스 스토리지를 줄이고 쓰기 성능을 향상시킬 수 있습니다. 인덱싱 비용은 인덱싱된 고유 경로 수와 직접 상관되기 때문입니다. 예를 들어, 이 코드는 “*” 와일드카드를 사용하여 인덱싱에서 문서의 전체 섹션(하위 트리라고도 함)을 제외하는 방법을 보여 줍니다.

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="throughput"></a><a id="measure-rus"></a> 처리량

**낮은 요청 단위/초 사용량 측정 및 튜닝**

Azure Cosmos DB에서는 다양한 데이터베이스 작업 세트를 제공합니다. 이 작업에는 데이터베이스 컬렉션 내부의 문서에서 모두 작동하는 UDF, 저장 프로시저, 트리거와 함께 관계형 쿼리와 계층형 쿼리가 포함되어 있습니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 애플리케이션 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

처리량은 각 컨테이너에 설정된 [요청 단위](request-units.md) 수에 따라 프로비저닝됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 해당 컨테이너에 프로비저닝된 요청 단위 비율을 초과하는 애플리케이션은 비율이 컨테이너에 프로비저닝된 수준 아래로 떨어질 때까지 제한됩니다. 애플리케이션에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로비저닝하여 처리량을 늘릴 수 있습니다.

쿼리의 복잡성은 작업에 사용되는 요청 단위의 양에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수, 원본 데이터 세트의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.

모든 작업(만들기, 업데이트 또는 삭제)의 오버헤드를 측정하려면 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 헤더(또는 .NET SDK의 `ResourceResponse\<T>`나 `FeedResponse\<T>`의 해당 `RequestCharge` 속성)를 검사하여 작업에 사용된 요청 단위 수를 측정합니다.

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

이 헤더에서 반환된 요청 비용은 프로비저닝된 처리량(즉, 2,000RU/초)의 일부입니다. 예를 들어, 앞의 쿼리가 1,000개의 1KB 문서를 반환하는 경우 작업 비용은 1,000이 됩니다. 따라서 1초 이내에 서버는 이후 요청의 속도를 제한하기 전에 해당 두 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md)와 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.
<a id="429"></a>

**너무 큰 속도 제한/요청 속도 처리**

클라이언트가 계정에 대해 예약된 처리량을 초과하려 할 때도 서버의 성능이 저하되거나 예약된 수준 이상의 처리량이 사용되지 않습니다. 서버는 RequestRateTooLarge(HTTP 상태 코드 429)로 요청을 선제적으로 종료합니다. 사용자가 요청을 다시 시도하기 전에 기다려야 하는 시간(밀리초)을 나타내는 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 헤더를 반환합니다.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

두 개 이상의 클라이언트가 요청 속도를 초과하여 누적해서 계속 작동할 경우 클라이언트가 내부적으로 현재 9로 설정한 기본 재시도 횟수가 충분하지 않을 수 있습니다. 이 경우 클라이언트는 애플리케이션에 상태 코드 429를 표시하며 DocumentClientException 예외 처리를 합니다. 

`ConnectionPolicy` 인스턴스에서 `RetryOptions`를 설정하여 기본 재시도 횟수를 변경할 수 있습니다. 기본적으로, 요청이 계속 요청 속도 이상으로 작동하는 경우 상태 코드가 429인 DocumentClientException은 누적 대기 시간 30초 후에 반환됩니다. 이 오류는 현재 값이 기본값인 9인지 아니면 사용자 정의 값인지에 상관없이 현재 재시도 횟수가 최대 재시도 횟수보다 적은 경우에도 반환됩니다.

재시도 동작을 자동화하면 대부분의 애플리케이션에 대한 복원력과 유용성을 개선할 수 있습니다. 그러나 성능 벤치마크를 수행할 때, 특히 대기 시간을 측정할 때 최상의 동작이 아닐 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [요청 단위](request-units.md)를 참조하세요.

**처리량을 높이기 위해 문서 크기를 줄이도록 설계**

지정된 작업의 요청 요금(즉, 요청 처리 비용)은 문서의 크기와 직접 상관됩니다. 큰 문서에서 작업하는 경우 작은 문서 작업보다 비용이 많이 듭니다.

## <a name="next-steps"></a>다음 단계

몇 개의 클라이언트 컴퓨터에서 고성능 시나리오에 대한 Azure Cosmos DB를 평가하는 데 사용된 애플리케이션 예제는 [Azure Cosmos DB를 사용한 성능 및 스케일 테스트](performance-testing.md)를 참조하세요.

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partitioning-overview.md)을 참조하세요.