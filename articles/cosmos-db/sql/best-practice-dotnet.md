---
title: .NET SDK v3의 Azure Cosmos DB 모범 사례
description: Azure Cosmos DB .NET SDK v3 사용을 위한 모범 사례를 알아봅니다.
author: StefArroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: esarroyo
ms.openlocfilehash: b07162b95419a73be4bdecc5ff3160ded1c2a0e5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219615"
---
# <a name="best-practices-for-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK에 대한 모범 사례
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB .NET SDK 사용을 위한 모범 사례를 살펴봅니다. 이러한 연습을 수행하면 대기 시간, 가용성 및 전반적인 성능을 향상시킬 수 있습니다. 

Cosmos DB 엔지니어가 .NET SDK를 사용하는 방법에 대한 자세한 내용을 보려면 아래 비디오를 시청하세요!


> [!VIDEO https://www.youtube.com/embed/McZIQhZpvew?start=118]
>

## <a name="checklist"></a>검사 목록
|선택됨  | 토픽  |세부 정보/링크  |
|---------|---------|---------|
|<input type="checkbox"/> |    SDK 버전    |   항상 [최신 버전](sql-api-sdk-dotnet-standard.md) 의 Cosmos DB SDK를 사용하여 최적의 성능을 제공합니다.     |
| <input type="checkbox"/>   |    싱글톤 클라이언트     |       애플리케이션의 수명에 대한 `CosmosClient`의 [단일 인스턴스](/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet&preserve-view=true)를 사용하여 [성능을 향상](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)합니다.     |
| <input type="checkbox"/>  |     영역     |   대기 시간을 줄이기 위해 가능한 경우, Azure Cosmos DB 계정과 동일한 [Azure 지역](../distribute-data-globally.md)에서 애플리케이션을 실행해야 합니다. 2~4개의 지역을 사용하도록 설정하고 [최상의 가용성](../distribute-data-globally.md)을 위해 여러 지역에서 계정을 복제합니다. 프로덕션 작업의 경우, [자동 장애 조치(failover)](../how-to-manage-database-account.md#configure-multiple-write-regions)를 사용하도록 설정합니다. 이 구성이 없는 경우, 지역 연결이 부족하여 수동 장애 조치가 성공하지 않으므로 계정의 쓰기 지역 가동 중단 시간에 대한 쓰기 가용성이 손실됩니다. .NET SDK를 사용하여 여러 지역을 추가하는 방법에 대한 자세한 내용은 [여기](tutorial-global-distribution-sql-api.md)를 참조하세요.   |
| <input type="checkbox"/>   |   가용성 및 장애 조치(failover)     |  V3 SDK에서 [ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions?view=azure-dotnet&preserve-view=true) 또는 [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion?view=azure-dotnet&preserve-view=true)을 설정하고, [기본 지역 목록](./tutorial-global-distribution-sql-api.md?tabs=dotnetv3%2capi-async#preferred-locations)을 사용하여 v2 SDK의 [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet&preserve-view=true)를 설정합니다. 장애 조치(failover) 중에는 쓰기 작업이 현재 쓰기 지역으로 전송되고 모든 읽기가 기본 지역 목록 내의 첫 번째 지역으로 전송됩니다. 지역 장애 조치(failover) 메커니즘에 대한 자세한 내용은 [가용성 문제 해결 가이드](troubleshoot-sdk-availability.md)를 참조하세요. |
|  <input type="checkbox"/> |    CPU     |  클라이언트 컴퓨터에 리소스가 부족하여 연결/가용성 문제가 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행하는 노드에서 CPU 사용률을 모니터링하고 사용량이 매우 높은 경우 스케일 업/아웃합니다.      |
| <input type="checkbox"/>   |    Hosting      |   가능한 경우, 최상의 성능을 위해 [Windows 64비트 호스트](performance-tips.md#hosting) 처리를 사용합니다.       |
| <input type="checkbox"/> |    연결 모드    |    최상의 성능을 위해 [직접 모드](sql-sdk-connection-modes.md)를 사용합니다.  이 작업을 수행하는 방법에 대한 지침은 [V3 SDK 설명서](performance-tips-dotnet-sdk-v3-sql.md#networking) 또는 [V2 SDK 설명서](performance-tips.md#networking)를 참조하세요.|
|<input type="checkbox"/>  |    네트워킹   | 가상 머신을 사용하여 애플리케이션을 실행하는 경우 VM에서 [가속화된 네트워킹](../../virtual-network/create-vm-accelerated-networking-powershell.md)을 사용하도록 설정하여 높은 트래픽으로 인한 병목 상태를 지원하고 대기 시간 또는 CPU 지터를 줄입니다. 최대 CPU 사용량이 70% 미만이 되도록 보다 고사양 가상 머신을 사용하는 것을 고려할 수도 있습니다.    |
|<input type="checkbox"/> |  임시 포트 고갈      | 스파스 또는 산발적 연결의 경우, [`IdleConnectionTimeout`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout?view=azure-dotnet&preserve-view=true) 및 [`PortReuseMode`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode?view=azure-dotnet&preserve-view=true)를 `PrivatePortPool`로 설정합니다. `IdleConnectionTimeout` 속성은 사용되지 않은 연결을 닫는 시간을 제어하는 데 도움이 됩니다. 이렇게 하면 사용되지 않은 연결 수가 줄어듭니다. 기본적으로 유휴 연결은 무기한으로 열려 있습니다. 값 설정은 10분보다 크거나 같아야 합니다. 20분~24시간의 값을 권장합니다.  `PortReuseMode` 속성을 사용하면 SDK가 다양한 Azure Cosmos DB 대상 엔드포인트에 작은 임시 포트 풀을 사용할 수 있습니다.    |
|<input type="checkbox"/> |  비동기/대기 사용     |  차단 호출인 `Task.Result`, `Task.Wait`, `Task.GetAwaiter().GetResult()`를 방지합니다. 전체 호출 스택은 [비동기/대기](/dotnet/csharp/programming-guide/concepts/async/) 패턴을 활용하기 위해 비동기식입니다. 많은 동기 차단 호출은 [스레드 풀 결핍](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) 및 응답 시간 저하로 이어집니다. |
|<input type="checkbox"/>  |   엔드투엔드 시간 제한 | 엔드투엔드 시간 제한을 가져오려면 `RequestTimeout` 및 `CancellationToken` 매개 변수를 모두 사용해야 합니다. Cosmos DB 시간 제한에 대한 자세한 내용 [참조하기](troubleshoot-dot-net-sdk-request-timeout.md) |
|<input type="checkbox"/>  |   재시도 논리      |   일시적인 오류는 근본 원인을 바탕으로 자체적으로 해결되는 오류입니다. 데이터베이스에 연결하는 애플리케이션은 이러한 일시적인 오류를 예상하도록 빌드되어야 합니다. 이를 처리하기 위해 사용자에게 애플리케이션 오류로 표시되는 대신 해당 코드에서 다시 시도 논리를 구현합니다. SDK에는 읽기 또는 쿼리 작업과 같이 다시 시도할 수 있는 요청에서 이러한 일시적인 오류를 처리하는 기본 제공 논리가 있습니다. 쓰기가 idempotent가 아니므로 SDK는 일시적인 오류에 대해 쓰기를 다시 시도하지 않습니다. SDK를 사용하면 사용자가 제한에 대해 다시 시도 논리를 구성할 수 있습니다. 다시 시도할 오류에 대한 자세한 내용 [참조하기](troubleshoot-dot-net-sdk.md#retry-logics) |
|<input type="checkbox"/>  |     데이터베이스/컬렉션 이름 캐싱    |    구성에서 데이터베이스 및 컨테이너의 이름을 검색하거나 시작할 때 이름을 캐시합니다. `ReadDatabaseAsync`, `ReadDocumentCollectionAsync`, `CreateDatabaseQuery`, `CreateDocumentCollectionQuery`와 같은 호출은 시스템 예약된 RU 제한을 사용하는 서비스에 대한 메타데이터 호출을 발생시킵니다. `CreateIfNotExist`는 데이터베이스를 설정하는 데 한 번만 사용해야 합니다. 전체적으로 이러한 작업은 자주 수행되지 않습니다.       |
|<input type="checkbox"/> |     대량 지원      |     대기 시간을 최적화하지 않아도 되는 시나리오에서 대량의 데이터 덤프를 위한 [대량 지원](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)을 사용하는 것이 좋습니다.    |
| <input type="checkbox"/>  |     병렬 쿼리     |    Cosmos DB SDK는 쿼리의 대기 시간 및 처리량 향상을 위해 [쿼리를 병렬로 실행](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)할 수 있도록 지원합니다.  `QueryRequestsOptions` 내에서 `MaxConcurrency` 속성은 보유하고 있는 파티션 수로 설정하는 것이 좋습니다. 파티션 수를 모르는 경우에는 가장 짧은 대기 시간을 제공하는 `int.MaxValue`를 사용하여 시작합니다. 그런 다음, 높은 CPU 문제를 방지하기 위해 환경의 리소스 제한에 도달할 때까지 수를 줄입니다. 또한 `MaxBufferedItemCount`를 예상되는 반환 결과 수로 설정하여 미리 가져온 결과 수를 제한합니다. |
| <input type="checkbox"/> |     성능 테스트 백오프      |    애플리케이션에 대한 테스트를 수행하는 경우, [`RetryAfter`](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage) 간격에 따라 백오프를 구현해야 합니다. 백오프를 사용하면 재시도 사이 대기 기간을 최소화할 수 있습니다.   |
|  <input type="checkbox"/>   |   인덱싱     |   Azure Cosmos DB 인덱싱 정책을 통해 인덱싱 경로(IndexingPolicy.IncludedPaths 및 IndexingPolicy.ExcludedPaths)를 사용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수도 있습니다.  쓰기 속도를 높이기 위해 인덱싱에서 사용하지 않는 경로를 제외합니다.  SDK를 사용하여 인덱스를 만드는 방법에 대한 샘플 [참조하기](performance-tips-dotnet-sdk-v3-sql.md#indexing-policy)   |
|  <input type="checkbox"/>   |    문서 크기  |    지정된 작업의 요청 요금은 문서의 크기와 직접 관련됩니다. 큰 문서에 대한 작업은 더 작은 문서에 대한 작업보다 비용이 많이 들기 때문에 문서 크기를 더 줄이는 것이 좋습니다.      |
| <input type="checkbox"/>   |     스레드/작업 수 늘리기    |    Azure Cosmos DB 호출은 네트워크를 통해 수행되므로 클라이언트 애플리케이션이 요청 간에 대기하는 시간이 최소한이 되도록 요청의 동시 처리 수준을 다양하게 지정해야 할 수 있습니다. 예를 들어 [.NET 작업 병렬 라이브러리](/dotnet/standard/parallel-programming/task-parallel-library-tpl)를 사용하는 경우, Azure Cosmos DB에서 읽거나 쓰는 수백 개의 작업에 대한 순서를 생성합니다.     |
|  <input type="checkbox"/> |    쿼리 메트릭 사용     |  백 엔드 쿼리 실행에 대한 추가 로깅을 위해 .NET SDK를 사용하여 SQL 쿼리 메트릭을 사용하도록 설정할 수 있습니다. SQL 쿼리 메트릭을 수집하는 방법에 대한 지침 [참조하기](profile-sql-api-query.md)    |
|  <input type="checkbox"/>    | SDK 로깅   | SDK 로깅을 사용하여 추가 진단 정보를 캡처하고 대기 시간 문제를 해결할 수 있습니다.  서비스에 대한 현재 요청과 관련된 자세한 cosmos 진단 정보를 보려면 V2 SDK 또는 v3 SDK [`Diagnostics`](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet&preserve-view=true)의 [진단 문자열](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet&preserve-view=true)을 기록합니다. 사용 사례를 예로 들면, 모든 예외에 대한 진단을 캡처하고, `Diagnostics.ElapsedTime`이 지정된 임계값보다 큰 경우 완료된 작업에 대한 진단을 캡처합니다(즉, SLA가 10초이면 `ElapsedTime` > 10초일 때 진단 캡처). 성능 테스트 중에만 이러한 진단을 사용하는 것이 좋습니다.     |

## <a name="best-practices-when-using-gateway-mode"></a>게이트웨이 모드를 사용하는 경우의 모범 사례
게이트웨이 모드를 사용하는 경우, `System.Net MaxConnections`를 호스트 단위로 늘립니다. Azure Cosmos DB 요청은 게이트웨이 모드를 사용할 때 HTTPS/REST를 통해 수행됩니다. 호스트 이름이나 IP 주소당 기본 연결 한도가 적용됩니다. 클라이언트 라이브러리가 Azure Cosmos DB에 대한 다중 동시 연결을 활용할 수 있도록 `MaxConnections`를 더 높은 값(100-1,000)으로 설정해야 할 수도 있습니다. .NET SDK 1.8.0 이상에서 `ServicePointManager.DefaultConnectionLimit`의 기본값은 50입니다. 값을 변경하려면 `Documents.Client.ConnectionPolicy.MaxConnectionLimit`를 더 높은 값으로 설정할 수 있습니다.

## <a name="best-practices-for-write-heavy-workloads"></a>쓰기 집약적인 워크로드의 모범 사례
만들기 페이로드가 많은 워크로드의 경우 `EnableContentResponseOnWrite`요청 옵션을 `false`로 설정합니다. 서비스에서는 만들거나 업데이트된 리소스를 더는 SDK에 반환하지 않습니다. 일반적으로 애플리케이션에는 생성 중인 개체가 있으므로 서비스에서 반환하지 않아도 됩니다. 요청 요금처럼 헤더 값에 계속 액세스할 수 있습니다. 콘텐츠 응답을 사용하지 않게 설정하면 SDK에서 더는 메모리를 할당하거나 응답 본문을 직렬화할 필요가 없기 때문에 성능 향상에 도움이 될 수 있습니다. 또한 네트워크 대역폭 사용량을 줄여 성능을 개선할 수 있습니다.

## <a name="next-steps"></a>다음 단계
몇 개의 클라이언트 컴퓨터에서 고성능 시나리오에 대한 Azure Cosmos DB를 평가하는 데 사용된 애플리케이션 예제는 [Azure Cosmos DB를 사용한 성능 및 스케일 테스트](performance-testing.md)를 참조하세요.

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](../partitioning-overview.md)을 참조하세요.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB Capacity Planner를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
