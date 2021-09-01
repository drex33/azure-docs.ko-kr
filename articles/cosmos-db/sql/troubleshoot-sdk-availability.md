---
title: 다중 지역 환경에서 Azure Cosmos SDK의 가용성을 진단 및 문제 해결
description: 다중 지역 환경에서 작동하는 경우, Azure Cosmos SDK 가용성 동작에 대한 모든 것을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 55912ee592ea04ec8771cf833dd92874d6d2c505
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117063"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>다중 지역 환경에서 Azure Cosmos SDK의 가용성을 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 특정 지역에 연결 문제가 있거나 지역 장애 조치(failover)가 발생하는 경우, Azure Cosmos SDK의 최신 버전의 동작을 설명합니다.

모든 Azure Cosmos SDK는 지역 기본 설정을 사용자 지정하는 옵션을 제공합니다. 다른 SDK에서 사용되는 속성은 다음과 같습니다.

* .NET V2 SDK의 [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 속성.
* .NET V3 SDK의 [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 또는 [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 속성.
* Java V4 SDK의 [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) 메서드.
* Python SDK의 [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 매개 변수.
* JS SDK의 [CosmosClientOptions.ConnectionPolicy.preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) 매개 변수.

지역 기본 설정을 지정하는 경우, 클라이언트는 다음 표에 설명된 대로 지역에 연결됩니다.

|계정 유형 |읽기 |쓰기 |
|------------------------|--|--|
| 단일 쓰기 지역 | 기본 지역 | 주 지역  |
| 다중 쓰기 지역 | 기본 지역 | 기본 지역  |

**기본 지역을 설정하지 않으면**, SDK 클라이언트는 주 지역으로 기본 설정됩니다.

|계정 유형 |읽기 |쓰기 |
|------------------------|--|--|
| 단일 쓰기 지역 | 주 지역 | 주 지역 |
| 다중 쓰기 지역 | 주 지역  | 주 지역  |

> [!NOTE]
> 주 지역은 [Azure Cosmos 계정 지역 목록](../distribute-data-globally.md)의 첫 번째 지역을 나타냅니다.
> 지역별 기본 설정으로 지정된 값이 기존 Azure 지역과 일치하지 않으면, 해당 값은 무시됩니다. 이러한 값이 기존 지역과 일치하지만 계정이 그것에 복제되지 않으면, 클라이언트는 일치하는 다음 기본 설정 영역 또는 주 지역에 연결됩니다.

> [!WARNING]
> 이 문서에서 설명하는 장애 조치(failover) 및 가용성 논리는 클라이언트 구성에서 사용하지 않도록 설정할 수 있습니다. 이는 사용자 애플리케이션이 가용성 오류 자체를 처리하지 않는 한 권장되지 않습니다. 이는 다음으로 얻을 수 있습니다.
>
> * .NET V2 SDK에서 [ConnectionPolicy.EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) 속성을 false로 설정합니다.
> * .NET V3 SDK의 [CosmosClientOptions.LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) 속성을 true로 설정합니다.
> * Java V4 SDK의 [CosmosClientBuilder.endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) 메서드를 false로 설정합니다.
> * Python SDK의 [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 매개 변수를 false로 설정합니다.
> * JS SDK의 [CosmosClientOptions.ConnectionPolicy.enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) 매개 변수를 false로 설정합니다.

일반적인 상황에서 SDK 클라이언트는 기본 지역(지역 기본 설정이 설정된 경우) 또는 주 지역(기본 설정이 되지 않은 경우)에 연결되고, 아래 시나리오 중 하나가 발생하지 않는 한 작업은 해당 지역으로 제한됩니다.

이러한 경우, Azure Cosmos SDK를 사용하는 클라이언트는 로그를 노출하고, **작업 진단 정보** 의 일부로 재시도 정보를 포함합니다.

* .NET V2 SDK의 응답에 대한 *RequestDiagnosticsString* 속성
* .NET V3 SDK의 응답 및 예외에 대한 *Diagnostics* 속성
* Java V4 SDK의 응답 및 예외에 대한 *getDiagnostics()* 메서드

우선 순위에 따라 다음 지역을 결정하는 경우, SDK 클라이언트는 계정 지역 목록을 사용하여 기본 지역(있는 경우)의 우선 순위를 지정합니다.

이러한 이벤트 중 SLA 보증에 대한 포괄적인 정보는 [가용성에 대한 SLA](../high-availability.md#slas-for-availability)를 참조하세요.

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>계정에서 지역을 제거하기

Azure Cosmos 계정에서 지역을 제거하면, 해당 계정을 사용하는 모든 SDK 클라이언트에서 백 엔드 응답 코드를 통해 지역 제거를 검색합니다. 그러면 클라이언트는 지역 엔드포인트를 사용할 수 없는 것으로 표시합니다. 클라이언트는 현재 작업을 다시 시도하고, 모든 후속 작업은 우선 순위에 따라 다음 지역으로 영구적으로 라우팅됩니다. 기본 설정 목록에 항목이 하나만 있거나 비어 있지만, 해당 계정에 다른 지역이 있으면, 계정 목록에서 다음 지역으로 라우팅합니다.

## <a name="adding-a-region-to-an-account"></a>계정에 지역을 추가하기

Azure Cosmos SDK 클라이언트는 5분마다 계정 구성을 읽고, 인식되는 지역을 새로 고칩니다.

지역을 제거하고 나중에 다시 계정에 추가하는 경우, 추가된 지역에서 현재 연결된 지역보다 SDK 구성에서 더 높은 지역 기본 설정 순서를 사용하는 경우, SDK는 이 지역을 영구적으로 사용하도록 다시 전환합니다. 추가된 지역이 삭제된 후, 모든 후속 요청이 그 지역으로 전달됩니다.

Azure Cosmos 계정에 없는 지역에 될 수 있는 대로 연결하기 위해 클라이언트를 구성하는 경우, 기본 지역은 무시됩니다. 해당 지역을 나중에 추가하면, 클라이언트에서 해당 지역을 검색하고 해당 지역으로 영구적으로 전환합니다.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>단일 쓰기 지역 계정에서 쓰기 지역을 장애 조치(failover)하기

현재 쓰기 지역의 장애 조치(failover)를 시작하면, 다음 쓰기 요청이 알려진 백 엔드 응답이 제공되며 실패합니다. 이 응답이 검색되면 클라이언트는 계정을 쿼리하여 새 쓰기 지역을 학습하고, 현재 작업을 다시 시도하며, 이후의 모든 쓰기 작업을 새 지역으로 영구적으로 라우팅합니다.

## <a name="regional-outage"></a>지역 가동 중단

계정이 단일 쓰기 지역이고, 쓰기 작업 중에 지역 가동 중단이 발생하는 경우, 이 동작은 [수동 장애 조치(failover)](#manual-failover-single-region)와 유사합니다. 읽기 요청 또는 여러 쓰기 지역 계정의 경우, 이 동작은 [지역 제거하기](#remove-region)와 비슷합니다.

## <a name="session-consistency-guarantees"></a>세션 일관성 보증

[세션 일관성](../consistency-levels.md#guarantees-associated-with-consistency-levels)을 사용하는 경우, 클라이언트는 자체 고유의 쓰기를 읽을 수 있도록 보장해야 합니다. 읽기 지역 기본 설정이 쓰기 지역과 다른 단일 쓰기 지역 계정에서는 사용자가 쓰기를 실행하고, 로컬 지역에서 읽기를 수행할 때 로컬 지역에서 데이터 복제를 아직 받지 않은 경우가 있을 수 있습니다(광원 제약 조건의 속도). 이러한 경우, SDK는 읽기 작업에서 특정 오류를 검색하고, 주 지역에서 읽기를 다시 시도하여 세션 일관성을 유지합니다.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 프로토콜의 일시적인 연결 문제

Azure Cosmos SDK 클라이언트가 TCP 프로토콜을 사용하도록 구성된 시나리오에서, 지정된 요청에 대해 네트워크 상태가 일시적으로 특정 엔드포인트와의 통신에 영향을 주는 상황이 발생할 수 있습니다. 이러한 임시 네트워크 상태에서 TCP 시간 제한 및 서비스를 사용할 수 없음(HTTP 503) 오류로 표시될 수 있습니다. 클라이언트는 오류가 발생하기 전에 몇 초 동안 동일한 엔드포인트에서 로컬로 요청을 다시 시도합니다.

사용자가 둘 이상의 지역이 포함된 기본 지역 목록을 구성하고, Azure Cosmos 계정이 여러 쓰기 지역 또는 단일 쓰기 지역이며, 작업이 읽기 요청인 경우, 클라이언트는 로컬 오류를 검색하고, 기본 설정 목록에서 다음 지역에 있는 해당 단일 작업을 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

* [가용성 SLA](../high-availability.md#slas-for-availability)를 검토합니다.
* 최신 [.NET SDK](sql-api-sdk-dotnet-standard.md) 사용
* 최신 [Java SDK](sql-api-sdk-java-v4.md) 사용
* 최신 [Python SDK](sql-api-sdk-python.md) 사용
* 최신 [Node SDK](sql-api-sdk-node.md) 사용
