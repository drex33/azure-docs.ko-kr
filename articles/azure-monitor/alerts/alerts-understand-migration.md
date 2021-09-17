---
title: Azure Monitor 경고에 대한 마이그레이션 이해
description: 경고 마이그레이션의 작동 방식을 이해하고 문제를 해결합니다.
ms.topic: conceptual
ms.date: 09/06/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 2167e0ea05206bc9c991353d6518090773934635
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543658"
---
# <a name="understand-migration-options-to-newer-alerts"></a>최신 경고로 마이그레이션 옵션 이해

클래식 경고는 퍼블릭 클라우드 사용자에 대해 [사용 중지됩니다.](./monitoring-classic-retirement.md) Azure Government 클라우드 및 Azure 중국 21Vianet에 대한 클래식 경고는 **2024년 2월 29일** 에 사용 중지됩니다.

이 문서에서는 나머지 경고 규칙을 마이그레이션하는 데 사용되는 수동 마이그레이션 및 자발적으로 마이그레이션 도구의 작동 방식을 설명합니다. 또한 몇 가지 일반적인 문제에 대한 해결 방법도 설명합니다.

> [!IMPORTANT]
> 활동 로그 경고(서비스 상태 경고 포함) 및 로그 경고는 마이그레이션의 영향을 받지 않습니다. 마이그레이션은 [여기에](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)설명된 클래식 경고 규칙에만 적용됩니다.

> [!NOTE]
> 클래식 경고 규칙이 유효하지 않은 경우(즉, [삭제된 메트릭](#classic-alert-rules-on-deprecated-metrics) 또는 리소스가 더 이상 사용되지 않음) 마이그레이션되지 않으며 서비스가 사용 중지된 후에는 사용할 수 없습니다.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>클래식 경고를 최신 경고로 수동으로 마이그레이션

나머지 경고를 수동으로 마이그레이션하는 데 관심이 있는 고객은 다음 섹션을 사용하여 이미 마이그레이션할 수 있습니다. 또한 사용 중지되어 직접 마이그레이션할 수 없는 메트릭도 포함됩니다.

### <a name="guest-metrics-on-virtual-machines"></a>가상 머신의 게스트 메트릭

게스트 메트릭에 대한 새 메트릭 경고를 만들려면 먼저 게스트 메트릭을 Azure Monitor 로그 저장소로 보내야 합니다. 다음 지침에 따라 경고를 만듭니다.

- [로그 분석에 게스트 메트릭 수집 사용](../agents/agent-data-sources.md)
- [Azure Monitor 로그 경고 만들기](./alerts-log.md)

게스트 메트릭을 수집하고 경고하는 더 많은 옵션이 있습니다. [자세한 내용을 알아보세요.](../agents/agents-overview.md)

### <a name="storage-and-classic-storage-account-metrics"></a>Storage 및 클래식 Storage 계정 메트릭

이러한 메트릭에 대한 경고를 제외하고 스토리지 계정에 대한 모든 클래식 경고를 마이그레이션할 수 있습니다.

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

백분율 메트릭에 대한 클래식 경고 규칙은 [이전 스토리지 메트릭과 새 스토리지 메트릭 간의 매핑에](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics)따라 마이그레이션해야 합니다. 사용 가능한 새 메트릭이 절대 메트릭이므로 임계값을 적절하게 수정해야 합니다.

동일한 기능을 제공하는 결합된 메트릭이 없으므로 AnonymousThrottlingError, SASThrottlingError 및 ThrottlingError에 대한 클래식 경고 규칙은 두 개의 새 경고로 분할되어야 합니다. 임계값을 적절하게 조정해야 합니다.

### <a name="cosmos-db-metrics"></a>Cosmos DB 메트릭

Cosmos DB 메트릭에 대한 모든 클래식 경고는 다음 메트릭에 대한 경고를 제외하고 마이그레이션할 수 있습니다.

- 초당 평균 요청 수
- 일관성 수준
- Http 2xx
- Http 3xx
- 분당 최대 RUPM 사용
- 초당 최대 CPU 수
- Mongo 기타 요청 요금
- Mongo 기타 요청 속도
- 관찰된 읽기 대기 시간
- 관찰된 쓰기 대기 시간
- 서비스 가용성
- 스토리지 용량

초당 평균 요청 수, 일관성 수준, 분당 최대 RUPM 사용량, 초당 최대 RU 수, 관찰된 읽기 대기 시간, 관찰된 쓰기 대기 시간 및 Storage 용량은 현재 [새 시스템에서](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts)사용할 수 없습니다.

요청 수가 계산되는 방식이 클래식 메트릭과 새 메트릭 간에 다르기 때문에 Http 2xx, Http 3xx 및 서비스 가용성과 같은 요청 메트릭에 대한 경고는 마이그레이션되지 않습니다. 이러한 메트릭에 대한 경고는 임계값을 조정하여 수동으로 다시 만들어야 합니다.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>사용되지 않은 메트릭에 대한 클래식 경고 규칙

다음은 이전에 지원되었지만 결국 사용되지 않는 메트릭에 대한 클래식 경고 규칙입니다. 소수의 고객이 이러한 메트릭에 대해 잘못된 클래식 경고 규칙을 가질 수 있습니다. 이러한 경고 규칙은 유효하지 않기 때문에 마이그레이션되지 않습니다.

| 리소스 종류| 사용되지 않은 메트릭 |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, 제한, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>동일한 새 경고 규칙 및 작업 그룹을 만드는 방법

마이그레이션 도구는 클래식 경고 규칙을 해당하는 새 경고 규칙 및 작업 그룹으로 변환합니다. 대부분의 클래식 경고 규칙에서 동일한 새 경고 규칙은 및 와 같은 동일한 속성을 가진 동일한 메트릭에 `windowSize` `aggregationType` 있습니다. 그러나 새 시스템에서 다른 동일한 메트릭이 있는 메트릭에 대한 몇 가지 클래식 경고 규칙이 있습니다. 아래 섹션에서 지정하지 않는 한 클래식 경고의 마이그레이션에는 다음 원칙이 적용됩니다.

- **빈도:** 클래식 또는 새 경고 규칙이 조건을 확인하는 빈도를 정의합니다. 클래식 경고 규칙의 은 `frequency` 사용자가 구성할 수 없으며 모든 리소스 종류에 대해 항상 5분이었습니다. 동등한 규칙의 빈도도 5분으로 설정됩니다.
- **집계 유형:** 관심 창에서 메트릭이 집계되는 방법을 정의합니다. `aggregationType`은 대부분의 메트릭에 대한 클래식 경고와 새 경고 간에도 동일합니다. 경우에 따라 메트릭이 클래식 경고와 새 경고 간에 다르기 때문에 `aggregationType` 메트릭에 대해 정의된 것과 동일하거나 `primary Aggregation Type` 이 사용됩니다.
- **단위:** 경고가 생성되는 메트릭의 속성입니다. 일부 해당 메트릭에는 서로 다른 단위가 있습니다. 임계값은 필요에 따라 적절하게 조정됩니다. 예를 들어 원래 메트릭에 단위로 초가 있지만 해당하는 새 메트릭에 단위로 밀리초가 있는 경우 동일한 동작을 보장하기 위해 원래 임계값에 1000을 곱합니다.
- **창 크기:** 임계값과 비교할 메트릭 데이터가 집계되는 창을 정의합니다. `windowSize`5분, 15분, 30분, 1시간, 3시간, 6시간, 12시간, 1일 등의 표준 값의 경우 해당하는 새 경고 규칙에 대한 변경 내용은 없습니다. 다른 값의 경우 가장 가까운 `windowSize` 가 사용됩니다. 대부분의 고객에게는 이 변경에 영향을 미치지 않습니다. 소수의 고객의 경우 정확히 동일한 동작을 얻기 위해 임계값을 조정해야 할 수 있습니다.

다음 섹션에서는 새 시스템에서 다른 동일한 메트릭이 있는 메트릭에 대해 자세히 설명합니다. 클래식 및 새 경고 규칙에 대해 동일하게 유지되는 모든 메트릭은 나열되지 않습니다. 새 시스템에서 지원되는 메트릭 목록은 여기에서 찾을 수 [있습니다.](../essentials/metrics-supported.md)

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts 및 Microsoft.ClassicStorage/storageAccounts

Blob, 테이블, 파일 및 큐와 같은 Storage 계정 서비스의 경우 다음 메트릭이 아래와 같이 동일한 메트릭에 매핑됩니다.

| 클래식 경고의 메트릭 | 새 경고에 해당하는 메트릭 | 의견|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| 차원이 "ResponseType"="AuthorizationError" 및 "Authentication" = "Anonymous"인 트랜잭션 메트릭| |
| AnonymousClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousClientTimeOutError| 차원이 "ResponseType" = "ClientTimeOutError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousNetworkError | "ResponseType" = "NetworkError" 및 "Authentication" = "Anonymous" 차원이 포함 된 트랜잭션 메트릭 | |
| AnonymousServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousServerTimeOutError | 차원이 "ResponseType" = "ServerTimeOutError" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AnonymousSuccess | 차원이 "ResponseType" = "Success" 및 "Authentication" = "Anonymous" 인 트랜잭션 메트릭 | |
| AuthorizationError | 차원이 "ResponseType" = "AuthorizationError" 인 트랜잭션 메트릭 | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| 용량 | BlobCapacity | `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| ClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 인 트랜잭션 메트릭  | |
| ClientTimeoutError | 차원이 "ResponseType" = "ClientTimeOutError" 인 트랜잭션 메트릭 | |
| ContainerCount | ContainerCount | `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| NetworkError | 차원이 "ResponseType" = "NetworkError" 인 트랜잭션 메트릭 | |
| ObjectCount | BlobCount| `aggregationType`' Last ' 대신 ' average '를 사용 합니다. 메트릭은 Blob 서비스에만 적용 됩니다. |
| SASAuthorizationError | "ResponseType" = "AuthorizationError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASClientOtherError | 차원이 "ResponseType" = "ClientOtherError" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| SASClientTimeOutError | "ResponseType" = "ClientTimeOutError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASNetworkError | "ResponseType" = "NetworkError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| SASServerTimeOutError | "ResponseType" = "ServerTimeOutError" 및 "Authentication" = "SAS" 차원이 포함 된 트랜잭션 메트릭 | |
| SASSuccess | 차원이 "ResponseType" = "Success" 및 "Authentication" = "SAS" 인 트랜잭션 메트릭 | |
| ServerOtherError | 차원이 "ResponseType" = "ServerOtherError" 인 트랜잭션 메트릭 | |
| ServerTimeOutError | 차원이 "ResponseType" = "ServerTimeOutError" 인 트랜잭션 메트릭  | |
| Success | 차원이 "ResponseType" = "Success" 인 트랜잭션 메트릭 | |
| TotalBillableRequests| 트랜잭션 | |
| TotalEgress | 송신 | |
| TotalIngress | 수신 | |
| TotalRequests | 트랜잭션 | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Cosmos DB에 대해 다음과 같은 메트릭이 표시 됩니다.

| 클래식 경고의 메트릭 | 새 경고에 해당 하는 메트릭 | 의견|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| 데이터 크기 | DataUsage| |
| 문서 수 | DocumentCount||
| 인덱스 크기 | IndexUsage||
| 서비스를 사용할 수 없음 | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| 제한된 요청 | 차원이 "StatusCode" = "429" 인 TotalRequests| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| 내부 서버 오류 | 차원이 "StatusCode" = "500"} 인 TotalRequests| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Http 401 | 차원이 "StatusCode" = "401" 인 TotalRequests| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Http 400 | 차원이 "StatusCode" = "400" 인 TotalRequests| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| 총 요청 수 | TotalRequests| ' Max ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 수 요청 요금| 차원이 "CommandName" = "count" 인 MongoRequestCharge||
| Mongo 수 요청 빈도 | 차원이 "CommandName" = "count" 인 MongoRequestsCount||
| Mongo 삭제 요청 요금 | 차원이 "CommandName" = "delete" 인 MongoRequestCharge||
| Mongo 삭제 요청 속도 | 차원이 "CommandName" = "delete" 인 MongoRequestsCount||
| Mongo 삽입 요청 요금 | 차원이 "CommandName" = "insert" 인 MongoRequestCharge||
| Mongo 삽입 요청 속도 | 차원이 "CommandName" = "insert" 인 MongoRequestsCount||
| Mongo 쿼리 요청 요금 | 차원이 "CommandName" = "find" 인 MongoRequestCharge||
| Mongo 쿼리 요청 속도 | 차원이 "CommandName" = "find" 인 MongoRequestsCount||
| Mongo 업데이트 요청 요금 | 차원이 "CommandName" = "update" 인 MongoRequestCharge||
| Mongo 실패 한 요청 삽입 | 차원이 "CommandName" = "insert" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 쿼리 실패 한 요청 | 차원이 "CommandName" = "query" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 수 실패 한 요청 | 차원이 "CommandName" = "count" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 업데이트 실패 요청 | 차원이 "CommandName" = "update" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 실패 한 요청 | 차원이 "CommandName" = "other" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|
| Mongo 실패 한 요청 삭제 | 차원이 "CommandName" = "delete" 및 "Status" = "failed" 인 MongoRequestCount| ' Average ' 집계 형식이 ' n u l l '로 수정 되었습니다.|

### <a name="how-equivalent-action-groups-are-created"></a>동일한 작업 그룹을 만드는 방법

클래식 경고 규칙에는 경고 규칙 자체에 연결 된 메일, webhook, 논리 앱 및 runbook 작업이 있습니다. 새 경고 규칙은 여러 경고 규칙에서 다시 사용할 수 있는 작업 그룹을 사용 합니다. 마이그레이션 도구는 작업을 사용 하는 경고 규칙의 수에 관계 없이 동일한 작업에 대 한 단일 작업 그룹을 만듭니다. 마이그레이션 도구에서 만든 작업 그룹은 명명 형식 ' Migrated_AG * '를 사용 합니다.

> [!NOTE]
> 클래식 경고는 클래식 관리자 역할에 알리기 위해 사용 하는 경우 클래식 관리자의 로캘을 기반으로 지역화 된 전자 메일을 보냈습니다. 새 경고 전자 메일은 작업 그룹을 통해 전송 되며 영어로만 전송 됩니다.

## <a name="rollout-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객에 게 단계적으로 배포 됩니다. 구독 소유자는 도구를 사용 하 여 구독을 마이그레이션할 준비가 되 면 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 도구는 단계에서 롤아웃 되기 때문에 초기 단계에서 일부 구독을 아직 마이그레이션할 준비가 되지 않은 것을 볼 수 있습니다.

대부분의 구독은 현재 마이그레이션 준비로 표시 되어 있습니다. 다음 리소스 유형에 대 한 클래식 경고가 있는 구독만 아직 마이그레이션할 준비가 되지 않았습니다.

- Microsoft.classiccompute/domainNames/슬롯/역할
- Microsoft 인 사이트/구성 요소

## <a name="who-can-trigger-the-migration"></a>Who 마이그레이션을 트리거할 수 있나요?

구독 수준에서 모니터링 참여자의 기본 제공 역할이 있는 사용자는 마이그레이션을 트리거할 수 있습니다. 다음 사용 권한으로 사용자 지정 역할이 있는 사용자는 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> 위의 권한 외에도 구독을 AlertsManagement 리소스 공급자에 등록 해야 합니다. Application Insights에서 실패 이상 경고를 성공적으로 마이그레이션하려면이 작업이 필요 합니다. 

## <a name="common-problems-and-remedies"></a>일반적인 문제 및 해결 방법

[마이그레이션을 트리거한](alerts-using-migration-tool.md)후에는 마이그레이션이 완료 되었음을 알리거나 사용자에 게 필요한 조치가 있는지 알리기 위해 제공한 주소로 전자 메일을 받게 됩니다. 이 섹션에서는 몇 가지 일반적인 문제와 이러한 문제를 처리 하는 방법을 설명 합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독의 클래식 경고 규칙에 대 한 최근 변경 내용으로 인해 구독을 마이그레이션할 수 없습니다. 이 문제는 일시적입니다. 마이그레이션 상태가 마이그레이션 **준비** 를 며칠 후에 다시 시작 하면 마이그레이션을 다시 시작할 수 있습니다.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>범위 잠금-규칙을 마이그레이션하지 못하게 합니다.

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 생성 된 다음 클래식 경고 규칙이 삭제 됩니다. 그러나 범위 잠금을 통해 리소스를 만들거나 삭제 하지 못할 수 있습니다. 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. [마이그레이션 도구](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)에 나열 된 구독, 리소스 그룹 또는 리소스에 대 한 범위 잠금을 제거 하 고 마이그레이션을 다시 트리거하는 방법으로이 문제를 해결할 수 있습니다. 범위 잠금은 사용 하지 않도록 설정할 수 없으며 마이그레이션 프로세스 중에 제거 해야 합니다. [범위 잠금 관리에 대해 자세히 알아보세요](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>' 거부 ' 효과가 적용 된 정책을 사용 하 여 규칙을 마이그레이션하지 못함

마이그레이션의 일부로 새 메트릭 경고 및 새 작업 그룹이 생성 된 다음 클래식 경고 규칙이 삭제 됩니다. 그러나 [Azure Policy](../../governance/policy/index.yml) 할당으로 인해 리소스가 생성 되지 않을 수 있습니다. 정책 할당에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. 프로세스를 차단 하는 정책 할당은 [마이그레이션 도구](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)에 나열 됩니다. 다음 중 하나를 수행 하 여이 문제를 해결 합니다.

- 정책 할당에서 마이그레이션 프로세스 중에 구독, 리소스 그룹 또는 개별 리소스를 제외 합니다. [정책 제외 범위 관리에 대해 자세히 알아보세요](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- 정책 할당에서 ' 적용 모드 '를 **사용 안 함으로** 설정 합니다. [정책 할당의 enforcementMode 속성에 대해 자세히 알아보세요](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- 구독, 리소스 그룹 또는 개별 리소스에 대 한 Azure Policy 예외 (미리 보기)를 정책 할당으로 설정 합니다. [Azure Policy 예외 구조에 대해 자세히 알아보세요](../../governance/policy/concepts/exemption-structure.md).
- ' 사용 안 함 ', ' 감사 ', ' 추가 ' 또는 ' 수정 ' (예: 누락 된 태그와 관련 된 문제를 해결할 수 있음)에 대 한 영향을 제거 하거나 변경 합니다. [정책 효과 관리에 대해 자세히 알아보세요](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구 사용 방법](alerts-using-migration-tool.md)
- [마이그레이션 준비](alerts-prepare-migration.md)
