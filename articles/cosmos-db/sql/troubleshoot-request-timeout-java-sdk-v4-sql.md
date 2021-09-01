---
title: Java v4 SDK를 사용하여 Azure Cosmos DB HTTP 408 또는 요청 시간 초과 문제 해결
description: Java v4 SDK를 사용하여 Java SDK 요청 시간 초과 예외를 진단하고 해결하는 방법을 알아봅니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 718a0aa96579b3234bfa242408565246602762a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116395"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Azure Cosmos DB Java v4 SDK 요청 시간 제한 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 408 오류는 SDK가 시간 제한이 발생하기 전에 요청을 완료할 수 없는 경우에 발생합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 초과 예외에 대한 알려진 원인과 솔루션이 포함되어 있습니다.

### <a name="existing-issues"></a>기존 문제
더 오랜 기간 동안 요청이 중단되거나 더 자주 시간이 초과되는 경우 Java v4 SDK를 최신 버전으로 업그레이드하세요. 참고: 버전 4.7.0 이상을 사용하는 것이 좋습니다. 자세한 내용은 [Java V4 SDK 릴리스 정보](sql-api-sdk-java-v4.md)를 참조하세요.

### <a name="high-cpu-utilization"></a>높은 CPU 사용률
높은 CPU 사용률은 가장 일반적인 경우입니다. 최적 대기 시간을 위해 CPU 사용량은 약 40%가 되어야 합니다. 최대(평균 아님) CPU 사용률을 모니터링하려면 간격으로 10초를 사용합니다. CPU 급증은 단일 쿼리에 대해 여러 연결을 수행할 수 있는 파티션 간 쿼리에서 더 일반적으로 나타나는 현상입니다.

#### <a name="solution"></a>해결책:
SDK를 사용하는 클라이언트 애플리케이션을 스케일 업하거나 스케일 아웃해야 합니다.

### <a name="connection-throttling"></a>연결 제한
연결 제한은 호스트 컴퓨터의 연결 제한 또는 Azure SNAT(PAT) 포트 고갈 중 하나로 인해 발생할 수 있습니다.

### <a name="connection-limit-on-a-host-machine"></a>호스트 컴퓨터의 연결 제한
일부 Linux 시스템(예: 'Red Hat')에는 열려 있는 파일의 총 수에 상한이 있습니다. Linux의 소켓은 파일로 구현되므로 이 숫자는 총 연결 수도 제한합니다. 다음 명령을 실행합니다.

```bash
ulimit -a
```

#### <a name="solution"></a>해결책:
"nofile"로 식별되는 허용되는 열린 파일의 최대 수는 10,000개 이상이어야 합니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md)을 참조하세요.

### <a name="socket-or-port-availability-might-be-low"></a>소켓 또는 포트 가용성이 낮을 수 있음
Azure에서 실행하는 경우 Java SDK를 사용하는 클라이언트는 Azure SNAT(PAT) 포트 고갈에 도달할 수 있습니다.

#### <a name="solution-1"></a>해결 방법 1:
Azure VM에서 실행하는 경우 [SNAT 포트 고갈 가이드](troubleshoot-java-sdk-v4-sql.md#snat)를 따르세요.

#### <a name="solution-2"></a>해결 방법 2:
Azure App Service에서 실행하는 경우 [연결 오류 문제 해결 가이드](../../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)를 따르고 [App Service 진단을 사용](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)합니다.

#### <a name="solution-3"></a>솔루션 3:
Azure Functions에서 실행하는 경우 모든 관련 서비스(Azure Cosmos DB 포함)에 대해 싱글톤 또는 정적 클라이언트를 유지 관리하는 [Azure Functions 권장 사항](../../azure-functions/manage-connections.md#static-clients)을 준수하는지 확인합니다. 함수 앱 호스팅의 형식 및 크기에 따라 [서비스 제한](../../azure-functions/functions-scale.md#service-limits)을 확인합니다.

#### <a name="solution-4"></a>솔루션 4:
HTTP 프록시를 사용하는 경우 SDK `GatewayConnectionConfig`에서 구성된 연결 수를 지원할 수 있는지 확인합니다. 그렇지 않으면 연결 문제가 발생할 수 있습니다.

### <a name="create-multiple-client-instances"></a>여러 클라이언트 인스턴스 만들기
여러 클라이언트 인스턴스를 만들면 연결 경합 및 시간 초과 문제가 발생할 수 있습니다.

#### <a name="solution-1"></a>해결 방법 1:
[성능 팁](performance-tips-java-sdk-v4-sql.md#sdk-usage)을 따르고 전체 애플리케이션에서 단일 CosmosClient 인스턴스를 사용합니다.

#### <a name="solution-2"></a>해결 방법 2:
싱글톤 CosmosClient를 애플리케이션에 포함할 수 없는 경우 CosmosClient에서 이 API `connectionSharingAcrossClientsEnabled(true)`를 통해 여러 Cosmos 클라이언트 간에 연결 공유를 사용하는 것이 좋습니다. 동일한 JVM에 여러 Cosmos 계정과 상호 작용하는 Cosmos 클라이언트 인스턴스가 여러 개인 경우 Cosmos 클라이언트 인스턴스 간에 직접 모드에서(가능하면) 연결 공유를 사용할 수 있습니다. 이 옵션을 설정하면 인스턴스화된 첫 번째 클라이언트의 연결 구성(예: 소켓 시간 초과 구성, 유휴 시간 초과 구성)이 다른 모든 클라이언트 인스턴스에 사용됩니다.

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
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.
