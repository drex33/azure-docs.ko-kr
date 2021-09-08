---
title: .NET SDK를 사용하여 Azure Cosmos DB HTTP 408 또는 요청 시간 초과 문제 해결
description: .NET SDK 요청 시간 초과 예외를 진단하고 수정하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 3d731685825b09cdee52d9aae525d169d1d85ace
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117068"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Azure Cosmos DB .NET SDK 요청 시간 제한 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 408 오류는 SDK가 시간 제한이 발생하기 전에 요청을 완료할 수 없는 경우에 발생합니다.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK에서 시간 초과 사용자 지정

SDK에는 각각 다른 범위를 가진 시간 초과를 제어하는 두 가지 고유한 대안이 있습니다.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout`(또는 SDK v2의 경우 `ConnectionPolicy.RequestTimeout`) 구성을 사용하면 각 개별 네트워크 요청에 영향을 미치는 시간 초과를 설정할 수 있습니다. 사용자가 시작한 작업은 여러 네트워크 요청에 걸쳐 있을 수 있습니다(예: 제한이 있을 수 있음). 이 구성은 다시 시도 시 각 네트워크 요청에 적용됩니다. 이 시간 초과는 엔드투엔드 작업 요청 시간 초과가 아닙니다.

### <a name="cancellationtoken"></a>CancellationToken

SDK의 모든 비동기 작업에는 선택적 CancellationToken 매개 변수가 있습니다. 이 [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) 매개 변수는 모든 네트워크 요청에서 전체 작업에 사용됩니다. 네트워크 요청 사이에 취소 토큰을 확인하고 관련 토큰이 만료되면 작업이 취소될 수 있습니다. 취소 토큰을 사용하여 작업 범위에 대한 대략적인 예상 시간 초과를 정의해야 합니다.

> [!NOTE]
> `CancellationToken` 매개 변수는 라이브러리가 [잘못된 상태를 발생시키지 않는](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/) 경우 취소를 확인하는 메커니즘입니다. 취소에 정의된 시간이 다 되었을 때 작업이 바로 취소되지 않을 수 있습니다. 대신 시간이 지나면 안전할 때 취소됩니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 초과 예외에 대한 알려진 원인과 솔루션이 포함되어 있습니다.

### <a name="high-cpu-utilization"></a>높은 CPU 사용률
높은 CPU 사용률은 가장 일반적인 경우입니다. 최적 대기 시간을 위해 CPU 사용량은 약 40%가 되어야 합니다. 최대(평균 아님) CPU 사용률을 모니터링하려면 간격으로 10초를 사용합니다. CPU 급증은 단일 쿼리에 대해 여러 연결을 수행할 수 있는 파티션 간 쿼리에서 더 일반적으로 나타나는 현상입니다.

오류에 `TransportException` 정보가 포함된 경우 `CPU History`도 포함될 수 있습니다.

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* CPU 측정값이 70%를 초과하면 CPU 소모로 인해 시간 초과가 발생할 수 있습니다. 이 경우 해결 방법은 높은 CPU 사용률의 출처를 조사하여 줄이거나 컴퓨터를 더 큰 리소스 크기로 조정하는 것입니다.
* CPU 측정이 10초마다 발생하지 않는 경우(예: 간격 또는 측정 시간이 측정 사이에 더 큰 시간을 나타냄) 원인은 스레드 부족입니다. 이 경우 해결 방법은 스레드 부족(잠재적으로 잠긴 스레드)의 출처를 조사하거나 컴퓨터를 더 큰 리소스 크기로 조정하는 것입니다.

#### <a name="solution"></a>해결책:
SDK를 사용하는 클라이언트 애플리케이션을 스케일 업하거나 스케일 아웃해야 합니다.

### <a name="socket-or-port-availability-might-be-low"></a>소켓 또는 포트 가용성이 낮을 수 있음
Azure에서 실행할 때 .NET SDK를 사용하는 클라이언트는 Azure SNAT(PAT) 포트 고갈에 다다를 수 있습니다.

#### <a name="solution-1"></a>해결 방법 1:
Azure VM에서 실행하는 경우 [SNAT 포트 고갈 가이드](troubleshoot-dot-net-sdk.md#snat)를 따르세요.

#### <a name="solution-2"></a>해결 방법 2:
Azure App Service에서 실행하는 경우 [연결 오류 문제 해결 가이드](../../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)를 따르고 [App Service 진단을 사용](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)합니다.

#### <a name="solution-3"></a>솔루션 3:
Azure Functions에서 실행하는 경우 모든 관련 서비스(Azure Cosmos DB 포함)에 대해 싱글톤 또는 정적 클라이언트를 유지 관리하는 [Azure Functions 권장 사항](../../azure-functions/manage-connections.md#static-clients)을 준수하는지 확인합니다. 함수 앱 호스팅의 형식 및 크기에 따라 [서비스 제한](../../azure-functions/functions-scale.md#service-limits)을 확인합니다.

#### <a name="solution-4"></a>솔루션 4:
HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다. 그렇지 않으면 연결 문제가 발생할 수 있습니다.

### <a name="create-multiple-client-instances"></a>여러 클라이언트 인스턴스 만들기
여러 클라이언트 인스턴스를 만들면 연결 경합 및 시간 초과 문제가 발생할 수 있습니다.

#### <a name="solution"></a>해결 방법:
[성능 팁](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)을 따르고 전체 프로세스에서 단일 CosmosClient 인스턴스를 사용합니다.

### <a name="hot-partition-key"></a>핫 파티션 키
Azure Cosmos DB는 전체 프로비저닝된 처리량을 물리적 파티션에 고르게 분산합니다. 핫 파티션이 있는 경우 물리적 파티션에 있는 하나 이상의 논리적 파티션 키가 모든 물리적 파티션의 초당 요청 단위(RU/s)를 사용하고 있습니다. 동시에 다른 물리 분할의 RU는 사용되지 않습니다. 증상으로, 소비되는 총 RU/s는 데이터베이스 또는 컨테이너에서 프로비저닝된 전체 RU/s보다 적지만 핫 논리적 파티션 키 관련 요청에 대해 제한(429s)이 계속 표시됩니다. [정규화된 RU 소비 메트릭](../monitor-normalized-request-units.md)을 사용하여 워크로드에 핫 파티션이 발생하는지 확인합니다. 

#### <a name="solution"></a>해결책:
요청 볼륨과 스토리지를 고르게 분배하는 좋은 파티션 키를 선택합니다. [파티션 키 변경](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/) 방법에 대해 알아봅니다.

### <a name="high-degree-of-concurrency"></a>높은 수준의 동시성
애플리케이션이 높은 수준의 동시성을 수행하여 채널에서 경합이 발생할 수 있습니다.

#### <a name="solution"></a>해결책:
SDK를 사용하는 클라이언트 애플리케이션을 스케일 업하거나 스케일 아웃해야 합니다.

### <a name="large-requests-or-responses"></a>대량 요청 또는 응답
요청 또는 응답이 많으면 비교적 낮은 동시성 수준일 때도 채널에 대해 HOL(Head of Line) 차단이 진행되고 경합 상황이 악화될 수 있습니다.

#### <a name="solution"></a>해결책:
SDK를 사용하는 클라이언트 애플리케이션을 스케일 업하거나 스케일 아웃해야 합니다.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>실패율이 Azure Cosmos DB SLA을 벗어나지 않습니다.
애플리케이션은 일시적인 오류를 처리하고 필요한 경우 다시 시도할 수 있어야 합니다. 408 예외는 만들기 경로에서 서비스가 항목을 만들었는지 여부를 알 수 없기 때문에 다시 시도되지 않습니다. 만들기에 대해 동일한 항목을 다시 전송하면 충돌 예외가 발생합니다. 사용자 애플리케이션 비즈니스 논리에는 충돌을 처리하는 사용자 지정 논리가 있을 수 있습니다. 이러한 논리는 기존 항목의 모호성 및 만들기 다시 시도에 따른 충돌을 방지합니다.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>실패율이 Azure Cosmos DB SLA를 위반합니다.
[Azure 지원](https://aka.ms/azure-support)에 문의하세요.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.