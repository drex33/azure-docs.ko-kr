---
title: Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
description: 클라이언트 쪽 로깅 기타 타사 도구 등의 기능을 사용하여 .NET SDK를 사용할 때 Azure Cosmos DB 문제를 파악, 진단 및 해결합니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 26c16583dbbbc49d2f1be51d0cbf1a6e26e70299
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117084"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

이 문서에서는 Azure Cosmos DB SQL API 계정으로 [.NET SDK](sql-api-sdk-dotnet.md)를 사용할 때 일반적인 문제, 해결, 진단 단계 및 도구를 설명합니다.
.NET SDK는 Azure Cosmos DB SQL API에 액세스하기 위한 클라이언트 쪽 논리적 표현을 제공합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

## <a name="checklist-for-troubleshooting-issues"></a>문제 해결을 위한 검사 목록

애플리케이션을 프로덕션으로 이동하기 전에 다음 검사 목록을 고려합니다. 검사 목록을 사용하면 발생할 수 있는 몇 가지 일반적인 문제를 방지할 수 있습니다. 문제가 발생할 때 신속하게 진단할 수도 있습니다.

*    최신 [SDK](sql-api-sdk-dotnet-standard.md)를 사용합니다. 미리 보기 SDK는 프로덕션에 사용해서는 안 됩니다. 이렇게 하면 이미 수정된 알려진 문제가 발생하지 않습니다.
*    [성능 팁](performance-tips.md)을 검토하고 제안된 사례를 따릅니다. 이렇게 하면 확장, 대기 시간 및 기타 성능 문제를 방지하는 데 도움이 됩니다.
*    문제를 해결하는 데 도움이 되도록 SDK 로깅을 사용하도록 설정합니다. 로깅을 사용하도록 설정하면 성능에 영향을 줄 수 있으므로 문제를 해결할 때만 사용하도록 설정하는 것이 가장 좋습니다. 다음 로그를 사용하도록 설정할 수 있습니다.
*    Azure Portal을 사용하여 [로그 메트릭](../monitor-cosmos-db.md)을 사용하도록 설정할 수 있습니다. 포털 메트릭은 Azure Cosmos DB 원격 분석을 보여 주므로 문제가 Azure Cosmos DB에 해당하는지 또는 클라이언트 측에서 발생하는지 확인하는 데 유용합니다.
*    포인트 작업 응답에서 V2 SDK의 [진단 문자열](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 또는 V3 SDK의 [진단](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)을 로그합니다.
*    모든 쿼리 응답에서 [SQL 쿼리 메트릭](sql-api-query-metrics.md)을 로그합니다. 
*    [SDK 로깅]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) 설정을 따릅니다.

이 문서의 [일반적인 문제 및 해결 방법](#common-issues-workarounds) 섹션을 살펴봅니다.

적극적으로 모니터링되는 [GitHub 문제 섹션](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)을 확인합니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 솔루션을 찾지 못한 경우 GitHub 문제를 제출합니다. 긴급한 문제에 대한 지원 티켓을 열 수 있습니다.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="general-suggestions"></a>일반 제안
* 가능하면 Azure Cosmos DB 계정과 동일한 Azure 지역에서 앱을 실행합니다. 
* 클라이언트 컴퓨터의 리소스 부족으로 인해 연결/가용성 문제가 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행하는 노드에서 CPU 사용률을 모니터링하고 부하가 높은 경우 노드를 스케일 업/아웃하는 것이 좋습니다.

### <a name="check-the-portal-metrics"></a>포털 메트릭 확인
[포털 메트릭](../monitor-cosmos-db.md)을 확인하면 클라이언트 측 문제인지 또는 서비스에 문제가 있는지 확인하는 데 도움이 됩니다. 예를 들어 메트릭에 높은 비율의 비율 제한 요청(HTTP 상태 코드 429)이 포함되어 요청이 제한되고 있음을 의미하는 경우 [요청 비율이 너무 큼](troubleshoot-request-rate-too-large.md) 섹션을 확인합니다. 

## <a name="retry-logic"></a>재시도 논리 <a id="retry-logics"></a>
IO 실패 시 Cosmos DB SDK는 SDK에서 다시 시도할 수 있는 경우 실패한 작업을 다시 시도합니다. 모든 실패 작업에 대해 다시 시도를 수행하는 것이 좋습니다. 특히 쓰기 실패는 반드시 처리/다시 시도해야 합니다. 재시도 논리는 지속적으로 개선되므로 최신 SDK를 사용하는 것이 좋습니다.

1. 읽기 및 쿼리 IO 오류의 경우 최종 사용자에게 표시하지 않고 SDK에서 다시 시도합니다.
2. 쓰기(Create, Upsert, Replace, Delete)는 결과가 '달라지므로' SDK에서 실패한 쓰기 작업을 무조건 다시 시도할 수는 없습니다. 사용자의 애플리케이션 논리에서 오류를 처리하고 다시 시도해야 합니다.
3. [SDK 가용성 문제 해결](troubleshoot-sdk-availability.md)에서는 다중 지역 Cosmos DB 계정의 재시도에 대해 설명합니다.

### <a name="retry-design"></a>디자인 다시 시도

애플리케이션은 다시 시도가 도움이 되지 않는 알려진 문제가 아닌 한 모든 예외에서 다시 시도하도록 디자인해야 합니다. 예를 들어, 애플리케이션은 408 요청 시간 초과 시 다시 시도해야 합니다. 이 시간 초과는 일시적일 수 있으므로 다시 시도가 성공할 수 있습니다. 애플리케이션은 400초에서 다시 시도하면 안 됩니다. 이는 일반적으로 먼저 해결해야 하는 요청에 문제가 있음을 의미합니다. 400에서 다시 시도해도 문제가 해결되지 않으며 다시 시도하면 동일한 오류가 발생합니다. 아래 표는 알려진 실패와 다시 시도할 실패를 보여 줍니다.

## <a name="common-error-status-codes"></a>일반 오류 상태 코드 <a id="error-codes"></a>

| 상태 코드 | 다시 시도 가능 | 설명 | 
|----------|-------------|-------------|
| 400 | No | 잘못된 요청(예: 잘못된 json, 잘못된 헤더, 헤더의 잘못된 파티션 키)| 
| 401 | No | [권한 없음](troubleshoot-unauthorized.md) | 
| 403 | No | [Forbidden](troubleshoot-forbidden.md) |
| 404 | No | [리소스를 찾을 수 없음](troubleshoot-not-found.md) |
| 408 | Yes | [요청 시간이 초과됨](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | No | 충돌 실패는 쓰기 작업에서 리소스에 제공된 ID가 기존 리소스에 의해 사용된 경우입니다. ID는 동일한 파티션 키 값을 가진 모든 문서 내에서 고유해야 하므로 이 문제를 해결하려면 리소스에 다른 ID를 사용합니다. |
| 410 | Yes | 사라짐 예외(SLA를 위반해서는 안 되는 일시적인 오류) |
| 412 | No | 필수 조건 실패는 작업이 서버에서 사용 가능한 버전과 다른 eTag를 지정한 경우입니다. 낙관적 동시성 오류입니다. 리소스의 최신 버전을 확인하고 요청의 eTag를 업데이트한 후에 요청을 다시 시도해야 합니다.
| 413 | No | [요청 엔터티가 너무 큼](../concepts-limits.md#per-item-limits) |
| 429 | Yes | 429에서 다시 시도하는 것이 안전합니다. 이는 [요청이 너무 많은 경우](troubleshoot-request-rate-too-large.md) 링크를 따라가면 피할 수 있습니다.|
| 449 | Yes | 쓰기 작업에서만 발생하는 일시적인 오류이며 다시 시도하는 것이 안전합니다. 이는 너무 많은 동시 작업이 Cosmos DB에서 동일한 개체를 업데이트하려고 하는 디자인 문제를 나타낼 수 있습니다. |
| 500 | Yes | 예기치 않은 서비스 오류로 인해 작업이 실패했습니다. [Azure 지원 문제](https://aka.ms/azure-support)를 제출하여 지원팀에 문의하세요. |
| 503 | Yes | [서비스를 사용할 수 없음](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT(PAT) 포트 고갈

[공용 IP 주소 없이 앱이 Azure Virtual Machines](../../load-balancer/load-balancer-outbound-connections.md)에 배포되는 경우 기본적으로 [Azure SNAT 포트](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)는 VM 외부의 모든 엔드포인트에 대한 연결을 설정하는 데 사용됩니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)으로 제한됩니다. 이 상황은 연결 제한, 연결 종료 또는 위에서 언급한 [요청 시간 초과](troubleshoot-dot-net-sdk-request-timeout.md)로 이어질 수 있습니다.

 Azure SNAT 포트는 VM에 개인 IP 주소가 있는 경우에만 공용 IP 주소에 연결하는 데 사용됩니다. Azure SNAT 제한을 피하기 위한 두 가지 해결 방법이 있습니다(전체 애플리케이션에서 이미 단일 클라이언트 인스턴스를 사용하고 있는 경우).

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl)을 사용하여 방화벽에 서브넷을 추가합니다.
* [Azure VM에 공용 IP](../../load-balancer/troubleshoot-outbound-connection.md#assignilpip)를 할당합니다.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>높은 네트워크 대기 시간
높은 네트워크 대기 시간은 V2 SDK의 [진단 문자열](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 또는 V3 SDK의 [진단](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)을 사용하여 식별할 수 있습니다.

[시간 초과](troubleshoot-dot-net-sdk-request-timeout.md)가 없고 진단에 대기 시간이 긴 것이 분명한 단일 요청이 표시되는 경우

# <a name="v3-sdk"></a>[V3 SDK](#tab/diagnostics-v3)

진단은 `Diagnostics` 속성에 의해 모든 `ResponseMessage`, `ItemResponse`, `FeedResponse` 또는 `CosmosException`에서 얻을 수 있습니다.

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

진단의 네트워크 상호 작용은 예를 들면 다음과 같습니다.

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

여기서 `duration in milliseconds`는 대기 시간을 표시합니다.

# <a name="v2-sdk"></a>[V2 SDK](#tab/diagnostics-v2)

진단은 클라이언트가 `RequestDiagnosticsString` 속성을 통해 [직접 모드](sql-sdk-connection-modes.md)로 구성된 경우 사용할 수 있습니다.

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

그리고 대기 시간은 `ResponseTime`과 `RequestStartTime`의 차이에 달려 있습니다.

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

이 대기 시간에는 여러 가지 원인이 있을 수 있습니다.

* 애플리케이션이 Azure Cosmos DB 계정과 동일한 지역에서 실행되고 있지 않습니다.
* [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 또는 [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 구성이 올바르지 않으며 애플리케이션이 현재 실행 중인 다른 지역에 연결하려고 합니다.
* 높은 트래픽으로 인해 네트워크 인터페이스에 병목 현상이 있을 수 있습니다. 애플리케이션이 Azure Virtual Machines에서 실행 중인 경우 가능한 해결 방법이 있습니다.
    * [가속화된 네트워킹이 사용하도록 설정된 가상 머신](../../virtual-network/create-vm-accelerated-networking-powershell.md) 사용을 고려합니다.
    * [기존 가상 머신에서 가속화된 네트워킹](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)을 사용하도록 설정합니다.
    * [고급 가상 머신](../../virtual-machines/sizes.md)을 사용해 보세요.

### <a name="common-query-issues"></a>일반적인 쿼리 문제

[쿼리 메트릭](sql-api-query-metrics.md)은 쿼리가 가장 많은 시간을 소비하는 위치를 파악하는 데 도움이 됩니다. 쿼리 메트릭에서 클라이언트와 백 엔드에서 얼마나 많이 사용되고 있는지 확인할 수 있습니다. [쿼리 성능 문제 해결](troubleshoot-query-performance.md)에 대해 자세히 알아봅니다.

* 백 엔드 쿼리가 빠르게 반환되고 클라이언트에서 많은 시간을 보내는 경우 시스템의 부하를 확인합니다. 리소스가 충분하지 않고 SDK가 응답을 처리하는 데 사용할 수 있는 리소스를 기다리고 있을 수 있습니다.
* 백 엔드 쿼리가 느린 경우 [쿼리를 최적화](troubleshoot-query-performance.md)하고 현재 [인덱스 생성 정책](../index-overview.md)을 살펴보세요.

    > [!NOTE]
    > 성능을 향상시키려면 Windows 64 비트 호스트 처리를 권장합니다. SQL SDK에는 로컬에서 쿼리를 구문 분석하고 최적화하는 네이티브 ServiceInterop.dll이 포함되어 있습니다. ServiceInterop.dll은 Windows x64 플랫폼에서만 지원됩니다. ServiceInterop.dll을 사용할 수 없는 Linux 및 기타 지원되지 않는 플랫폼의 경우 게이트웨이에 대한 네트워크 호출을 추가로 수행하여 최적화된 쿼리를 가져옵니다.

`Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` 오류가 발생하고 Windows를 사용하는 경우 최신 Windows 버전으로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계

* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET V2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* [Reactor 기반 Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)에 대해 알아보기

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
