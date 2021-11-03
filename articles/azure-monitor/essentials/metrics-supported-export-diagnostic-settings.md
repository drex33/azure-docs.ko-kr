---
title: NULL 및 0 값을 사용하여 메트릭 내보내기 동작
description: 메트릭을 내보낼 때 NULL 값과 0 값에 대한 설명 및 내보낼 수 없는 메트릭 목록에 대한 포인터입니다.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.openlocfilehash: 53c5aad7061afb0479e9678a188fe99779893fef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017885"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭

Azure Monitor는 기본적으로 구성 없이 [플랫폼 메트릭](../essentials/data-platform-metrics.md)을 제공합니다. 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 플랫폼 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. [metrics-supported](./metrics-supported.md)에서 Azure Monitor의 통합 메트릭 파이프라인에서 현재 사용 가능한 플랫폼 메트릭의 전체 목록을 참조하세요. 이러한 메트릭을 쿼리하고 액세스하려면 [2018-01-01 api-version](/rest/api/monitor/metricdefinitions)을 사용하세요. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>진단 설정을 통해 내보낼 수 없는 메트릭

"exportable?"을 참조하세요. [지원되는 Azure Monitor 메트릭 목록의 열입니다.](./metrics-supported.md#exporting-platform-metrics-to-other-locations)

진단 설정을 통해 메트릭을 내보낼 때는 제한이 있습니다. 그러나 모든 메트릭은 REST API 사용하여 내보낼 수 있습니다.

## <a name="exported-zero-vs-null-values"></a>내보낸 0과 NULL 값

메트릭은 0 또는 NULL 값을 처리할 때 다른 동작을 갖습니다.  일부 메트릭은 데이터를 가져오지 못한 경우(예: http 오류에 대한 메트릭) 0을 보고합니다. 다른 메트릭은 리소스가 오프라인임을 나타낼 수 있으므로 데이터를 가져오지 않을 때 NUL을 저장합니다. NULL 값이 [파선으로](metrics-troubleshoot.md#chart-shows-dashed-line)표시되는 이러한 메트릭을 차트로 표시할 때 차이를 확인할 수 있습니다. 

진단 설정을 통해 플랫폼 메트릭을 내보낼 수 있는 경우 메트릭의 동작과 일치합니다. 즉, 리소스에서 데이터를 보내지 않을 때 NUL을 내보냅니다.  기본 리소스에서 실제로 내보낸 경우에만 '0'을 내보냅니다. 

리소스 그룹 또는 특정 리소스를 삭제하면 영향을 받는 리소스의 메트릭 데이터가 더 이상 진단 설정 내보내기 대상으로 전송되지 않습니다. 즉, 더 이상 Event Hubs, Azure Storage 계정 및 Log Analytics 작업 영역에 표시되지 않습니다.

### <a name="metrics-that-used-to-export-zero-for-null"></a>NULL에 대해 0을 내보내는 데 사용된 메트릭

2020년 6월 1일 이전에는 데이터가 없을 때 아래 메트릭을 **사용하여** '0'을 내보났습니다. 그런 다음 이러한 0을 Log Analytics 및 Azure Storage와 같은 다운스트림 시스템으로 내보낼 수 있습니다.  이 동작은 실제 '0s'(리소스에서 내보내기)와 해석된 '0s'(NUL)를 혼동하여 이전 섹션에서 설명한 대로 기본 메트릭의 동작과 일치하도록 변경되었습니다. 

모든 퍼블릭 및 프라이빗 클라우드에서 변경이 발생했습니다.

이 변경은 다음 환경의 동작에 영향을 주지 않았습니다. 
   - 진단 설정 통해 내보낸 플랫폼 리소스 로그
   - 메트릭 탐색기의 메트릭 차트 작성
   - 플랫폼 메트릭에 대한 경고
 
다음은 동작이 변경된 메트릭 목록입니다. 

| ResourceType                    | 메트릭               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  권한이 없는 게이트웨이 요청(사용되지 않음)  | 
| Microsoft.ApiManagement/service | TotalRequests |  총 게이트웨이 요청(사용되지 않음)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  성공적인 게이트웨이 요청(사용되지 않음)  | 
| Microsoft.ApiManagement/service | 요청 |  요청  | 
| Microsoft.ApiManagement/service | OtherRequests |  기타 게이트웨이 요청(사용되지 않음)  | 
| Microsoft.ApiManagement/service | FailedRequests |  실패한 게이트웨이 요청(사용되지 않음)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  실패한 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  총 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  EventHub 이벤트 크기  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  시간 초과된 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  제한된 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  성공한 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  거부된 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  삭제된 EventHub 이벤트  | 
| Microsoft.ApiManagement/service | Duration |  게이트웨이 요청의 전체 기간  | 
| Microsoft.ApiManagement/service | BackendDuration |  백 엔드 요청 지속 시간  | 
| Microsoft.DBforMariaDB/servers | storage_used |  스토리지 사용됨  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  스토리지 비율  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  스토리지 제한  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  사용된 서버 로그 스토리지  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  서버 로그 스토리지 비율  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  서버 로그 스토리지 제한  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  복제 지연 시간(초)  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  네트워크 인  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  네트워크 아웃  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  메모리 백분율  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  IO 백분율  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU 백분율  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  실패한 연결  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  사용된 백업 스토리지  | 
| Microsoft.DBforMariaDB/servers | active_connections |  활성 연결 수  | 
| Microsoft.DBforMySQL/servers | storage_used |  스토리지 사용됨  | 
| Microsoft.DBforMySQL/servers | storage_percent |  스토리지 비율  | 
| Microsoft.DBforMySQL/servers | storage_limit |  스토리지 제한  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  사용된 서버 로그 스토리지  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  서버 로그 스토리지 비율  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  서버 로그 스토리지 제한  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  복제 지연 시간(초)  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  네트워크 인  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  네트워크 아웃  | 
| Microsoft.DBforMySQL/servers | memory_percent |  메모리 백분율  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  IO 백분율  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU 백분율  | 
| Microsoft.DBforMySQL/servers | connections_failed |  실패한 연결  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  사용된 백업 스토리지  | 
| Microsoft.DBforMySQL/servers | active_connections |  활성 연결 수  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Digital Twins 노드 원격 분석 자리 표시자  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  성공한 쌍 쿼리  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  쌍 쿼리 결과 크기  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  실패한 쌍 쿼리  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  성공한 작업 쿼리  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  실패한 작업 쿼리  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  목록 작업에 대한 성공한 호출  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  목록 작업에 대한 실패한 호출  | 
| Microsoft.Devices/IotHubs | jobs.failed |  실패한 작업  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  쌍 업데이트 작업에 대한 성공한 만들기  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  쌍 업데이트 작업에 대한 실패한 만들기  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  메서드 호출 작업에 대한 성공한 만들기  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  실패한 메서드 호출 작업 만들기  | 
| Microsoft.Devices/IotHubs | jobs.completed |  완료된 작업  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  성공한 작업 취소  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  실패한 작업 취소  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Event Grid 대기 시간(미리 보기)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Event Grid 배달(미리 보기)  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  총 디바이스(사용되지 않음)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  연결된 디바이스(사용되지 않음)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  총 디바이스 데이터 사용량(미리 보기)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  총 디바이스 데이터 사용량  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  사용된 전체 메시지 수  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  디바이스에서의 성공한 쌍 업데이트  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  디바이스에서의 쌍 업데이트 크기  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  디바이스에서의 실패한 쌍 업데이트  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  디바이스에서의 성공한 쌍 읽기  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  디바이스에서의 쌍 읽기 응답 크기  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  디바이스에서의 실패한 쌍 읽기  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  보낸 원격 분석 메시지  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  제한 오류 수  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  원격 분석 메시지 보내기 시도  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  라우팅: 배달된 원격 분석 메시지  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  라우팅: 분리된 원격 분석 메시지   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  라우팅: 원격 분석 메시지 호환되지 않음  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  라우팅: 대체에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  라우팅: 삭제된 원격 분석 메시지   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  라우팅: 스토리지에 대한 메시지 대기 시간  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  라우팅: Service Bus 토픽에 대한 메시지 대기 시간  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  라우팅: Service Bus 큐에 대한 메시지 대기 시간  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  라우팅: 이벤트 허브에 대한 메시지 대기 시간  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  라우팅: 메시지/이벤트에 대한 메시지 대기 시간  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  라우팅: 스토리지에 배달된 데이터  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  라우팅: 스토리지에 배달된 Blob  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  라우팅: 스토리지에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  라우팅: Service Bus 토픽에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  라우팅: Service Bus 큐에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  라우팅: 이벤트 허브에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  라우팅: 메시지/이벤트에 배달된 메시지  | 
| Microsoft.Devices/IotHubs | 구성 |  구성 메트릭  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  만료된 C2D 메시지(미리 보기)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  백 엔드에서의 성공한 쌍 업데이트  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  백 엔드에서의 쌍 업데이트 크기  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  백 엔드에서의 실패한 쌍 업데이트  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  백 엔드에서의 성공한 쌍 읽기  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  백 엔드에서의 쌍 읽기 응답 크기  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  백 엔드에서의 실패한 쌍 읽기  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  성공한 직접 메서드 호출  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  직접 메서드 호출의 응답 크기  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  직접 메서드 호출의 요청 크기  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  실패한 직접 메서드 호출  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  거부된 C2D 메시지  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  C2D 메시지 배달 완료  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  중단된 C2D 메시지  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  등록 시도  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  할당된 디바이스  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  증명 시도  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  총 요청 단위  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  총 요청 수  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo 요청  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo 요청 요금  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  게시 성공 대기 시간  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  게시된 이벤트  | 
| Microsoft.EventGrid/domains | PublishFailCount |  실패한 이벤트 게시  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  일치하는 이벤트  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  삭제된 이벤트  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  배달된 이벤트  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  배달 못한 편지 이벤트  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  일치하는 이벤트  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  삭제된 이벤트  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  배달된 이벤트  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  배달 못한 편지 이벤트  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  일치하지 않는 이벤트  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  게시 성공 대기 시간  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  게시된 이벤트  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  실패한 이벤트 게시  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  일치하지 않는 이벤트  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  게시 성공 대기 시간  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  게시된 이벤트  | 
| Microsoft.EventGrid/topics | PublishFailCount |  실패한 이벤트 게시  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  보내는 메시지  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  보내는 바이트  | 
| Microsoft.EventHub/clusters | IncomingRequests |  들어오는 요청  | 
| Microsoft.EventHub/clusters | IncomingMessages |  들어오는 메시지  | 
| Microsoft.EventHub/clusters | IncomingBytes |  들어오는 바이트  | 
| Microsoft.EventHub/namespaces | SVRBSY |  서버 작업 중 오류(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  성공한 요청(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  나가는 메시지(구식)(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  보내는 메시지  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  보내는 바이트  | 
| Microsoft.EventHub/namespaces | MISCERR |  기타 오류(사용 되지 않음)  | 
| Microsoft.EventHub/namespaces | INTERR |  내부 서버 오류(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | INREQS |  들어오는 요청(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | INMSGS |  들어오는 메시지(구식)(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  들어오는 요청  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  들어오는 메시지  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  들어오는 바이트  | 
| Microsoft.EventHub/namespaces | FAILREQ |  실패한 요청(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  보내는 메시지(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  나가는 바이트(구식)(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  보내는 바이트(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  들어오는 메시지(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  들어오는 바이트(구식)(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  들어오는 바이트(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  메시지 보관(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  메시지 보관 처리량(사용되지 않음)  | 
| Microsoft.EventHub/namespaces | EHABL |  백로그 메시지 보관(사용되지 않음)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  활성 작업자 수  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  게이트웨이 요청  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  분류된 게이트웨이 요청  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  시작된 크기 조정 작업  | 
| Microsoft.Insights/Components | traces/count |  Traces  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  HTTP 요청 속도  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  애플리케이션 큐의 HTTP 요청  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  예외 속도  | 
| Microsoft.Insights/Components | pageViews/count |  페이지 보기  | 
| Microsoft.Insights/Components | exceptions/count |  예외  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  스트리밍 수집 결과  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  스트리밍 수집 지속 시간  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  스트리밍 수집 데이터 속도  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  스트리밍 수집 요청 속도  | 
| Microsoft.Kusto/Clusters | QueryDuration |  쿼리 기간  | 
| Microsoft.Kusto/Clusters | KeepAlive |  연결 유지  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  수집 볼륨(MB)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  수집 사용률  | 
| Microsoft.Kusto/Clusters | IngestionResult |  수집 결과  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  수집 대기 시간(초)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  내보내기 사용률  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  이벤트 처리됨(Event/IoT Hubs의 경우)  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  연속 내보내기 결과  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  보류 중인 연속 내보내기 수  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  연속 내보내기 - 내보낸 레코드 수  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  연속 내보내기 최대 지연 시간(분)  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  캐시 사용률  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  트리거 제한 이벤트  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  트리거 성공함   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  트리거 시작됨   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  트리거 생략함  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  트리거 실행됨   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  트리거 실패함   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  트리거 완료됨   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  실행 제한 이벤트  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  실행 시작 제한 이벤트  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  실행 성공함  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  실행 시작됨  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  실행 실패함  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  실행 완료됨  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  취소된 실행  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  실행 오류 비율  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  작업 제한 이벤트  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  작업 성공함   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  작업 시작됨   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  작업 생략됨   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  작업 실패함   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  작업 완료됨   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  트리거 제한 이벤트  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  트리거 성공함   | 
| Microsoft.Logic/workflows | TriggersStarted |  트리거 시작됨   | 
| Microsoft.Logic/workflows | TriggersSkipped |  트리거 생략함  | 
| Microsoft.Logic/workflows | TriggersFired |  트리거 실행됨   | 
| Microsoft.Logic/workflows | TriggersFailed |  트리거 실패함   | 
| Microsoft.Logic/workflows | TriggersCompleted |  트리거 완료됨   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  총 청구 가능 실행  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  실행 제한 이벤트  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  실행 시작 제한 이벤트  | 
| Microsoft.Logic/workflows | RunsSucceeded |  실행 성공함  | 
| Microsoft.Logic/workflows | RunsStarted |  실행 시작됨  | 
| Microsoft.Logic/workflows | RunsFailed |  실행 실패함  | 
| Microsoft.Logic/workflows | RunsCompleted |  실행 완료됨  | 
| Microsoft.Logic/workflows | RunsCancelled |  취소된 실행  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  실행 오류 비율  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  스토리지 소비 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  스토리지 소비 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  표준 커넥터 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  표준 커넥터 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  기본 작업 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  기본 작업 실행에 대한 청구 사용량  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  청구 가능한 트리거 실행  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  청구 가능한 작업 실행  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  작업 제한 이벤트  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  작업 성공함   | 
| Microsoft.Logic/workflows | ActionsStarted |  작업 시작됨   | 
| Microsoft.Logic/workflows | ActionsSkipped |  작업 생략됨   | 
| Microsoft.Logic/workflows | ActionsFailed |  작업 실패함   | 
| Microsoft.Logic/workflows | ActionsCompleted |  작업 완료됨   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  웹 애플리케이션 방화벽 요청 수  | 
| Microsoft.Network/frontdoors | TotalLatency |  총 대기 시간  | 
| Microsoft.Network/frontdoors | ResponseSize |  응답 크기  | 
| Microsoft.Network/frontdoors | RequestSize |  요청 크기  | 
| Microsoft.Network/frontdoors | RequestCount |  요청 수  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  청구 대상 응답 크기  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  백 엔드 요청 대기 시간  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  백 엔드 요청 수  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  백 엔드 상태 비율  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  반환된 엔드포인트별 쿼리  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  보류 중인 예약된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  등록 업데이트 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  등록 읽기 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  등록 삭제 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  등록 만들기 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  등록 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  WNS 권한 부여 오류(잘못된 토큰)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  WNS 권한 부여 오류(연결할 수 없음)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  WNS 제한된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  WNS 성공적인 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  WNS 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  WNS 권한 부여 오류(잘못된 토큰)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  WNS 잘못된 알림 크기 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  WNS 잘못된 알림 형식  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  WNS 권한 부여 오류(잘못된 자격 증명)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  WNS 만료된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  WNS 삭제된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  WNS 채널 제한  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  WNS 채널 연결 끊김  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  WNS 잘못된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  WNS 인증 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  MPNS 제한된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  MPNS 성공적인 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  MPNS 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  MPNS 잘못된 알림 형식  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  MPNS 잘못된 자격 증명  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  MPNS 삭제된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  MPNS 채널 연결 끊김  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  MPNS 잘못된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  MPNS 인증 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  GCM 잘못된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  GCM 제한된 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  GCM 성공적인 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  GCM 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  GCM 잘못된 알림 크기 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  GCM 잘못된 알림 형식  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  GCM 권한 부여 오류(잘못된 자격 증명)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  GCM 만료된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  GCM 잘못된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  GCM 인증 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  APNS 성공적인 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  APNS 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  APNS 잘못된 알림 크기 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  APNS 권한 부여 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  APNS 만료된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  APNS 잘못된 채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  성공적인 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  외부 알림 시스템 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  페이로드 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  채널 오류  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  모든 나가는 알림 수  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  설치 작업 만들기 또는 업데이트  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  설치 작업 패치  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  설치 작업 가져오기  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  설치 작업 삭제  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  설치 관리 작업  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  취소된 예약된 푸시 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  전송된 예약된 푸시 알림  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  들어오는 모든 요청  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  들어오는 모든 실패한 요청  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  들어오는 메시지  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  보내는 메시지  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  들어오는 요청  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  들어오는 메시지  | 
| Microsoft.SignalRService/SignalR | UserErrors |  사용자 오류  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  시스템 오류  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  아웃바운드 트래픽  | 
| Microsoft.SignalRService/SignalR | MessageCount |  메시지 수  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  인바운드 트래픽  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  연결 수  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  평균 CPU 비율  | 
| Microsoft.Sql/servers | dtu_used |  DTU 사용됨  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU 비율  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  메모리 내 OLTP 스토리지 백분율  | 
| Microsoft.Sql/servers/databases | workers_percent |  작업자 백분율  | 
| Microsoft.Sql/servers/databases | sessions_percent |  세션 백분율  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  데이터 IO 비율  | 
| Microsoft.Sql/servers/databases | log_write_percent |  로그 IO 비율  | 
| Microsoft.Sql/servers/databases | dwu_used |  DWU 사용됨  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  DWU 백분율  | 
| Microsoft.Sql/servers/databases | dtu_used |  DTU 사용됨  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU 비율  | 
| Microsoft.Sql/servers/databases | 교착 상태 |  교착 상태  | 
| Microsoft.Sql/servers/databases | cpu_used |  사용된 CPU  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU 비율  | 
| Microsoft.Sql/servers/databases | connection_successful |  성공적인 연결  | 
| Microsoft.Sql/servers/databases | connection_failed |  실패한 연결  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  캐시 적중 비율  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  방화벽에 의해 차단  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  메모리 내 OLTP 스토리지 백분율  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  작업자 백분율  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  세션 백분율  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  데이터 IO 비율  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  로그 IO 비율  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  eDTU 사용  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  DTU 비율  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  CPU 비율  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  총 프런트 엔드  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  소형 App Service 계획 작업자  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | 요청 |  요청  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  메모리 비율  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  중형 App Service 계획 작업자  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  대형 App Service 계획 작업자  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Http 큐 길이  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  Http 서버 오류  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  디스크 큐 길이  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU 비율  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  데이터 출력  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  데이터 입력  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  평균 응답 시간  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  활성 요청  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  사용된 작업자  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  총 작업자  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  사용 가능한 작업자  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  메모리 비율  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  CPU 비율  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP 시간 대기  | 
| Microsoft.Web/serverfarms | TcpSynSent |  TCP SYN 보냄  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  TCP SYN 받음  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP 마지막 ACK  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP FIN 대기 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP FIN 대기 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP 설정됨  | 
| Microsoft.Web/serverfarms | TcpClosing |  TCP 닫는 중  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  TCP 닫기 대기  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  메모리 비율  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Http 큐 길이  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  디스크 큐 길이  | 
| Microsoft.Web/serverfarms | CpuPercentage |  CPU 비율  | 
| Microsoft.Web/serverfarms | BytesSent |  데이터 출력  | 
| Microsoft.Web/serverfarms | BytesReceived |  데이터 입력  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  언로드된 총 앱 도메인  | 
| Microsoft.Web/sites | TotalAppDomains |  총 앱 도메인  | 
| Microsoft.Web/sites | 스레드 |  스레드 개수  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  애플리케이션 큐의 요청 수  | 
| Microsoft.Web/sites | 요청 |  요청  | 
| Microsoft.Web/sites | PrivateBytes |  프라이빗 바이트  | 
| Microsoft.Web/sites | MemoryWorkingSet |  메모리 작업 집합  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  초당 IO 쓰기 작업 수  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  초당 IO 쓰기 바이트 수  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  초당 IO 읽기 작업 수  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  초당 IO 읽기 바이트 수  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  초당 IO 기타 작업 수  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  초당 IO 기타 바이트 수  | 
| Microsoft.Web/sites | HttpResponseTime |  응답 시간  | 
| Microsoft.Web/sites | Http5xx |  Http 서버 오류  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  상태 검사 상태  | 
| Microsoft.Web/sites | 핸들 |  핸들 개수  | 
| Microsoft.Web/sites | Gen2Collections |  Gen 2 가비지 수집  | 
| Microsoft.Web/sites | Gen1Collections |  Gen 1 가비지 수집  | 
| Microsoft.Web/sites | Gen0Collections |  Gen 0 가비지 수집  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  함수 실행 단위  | 
| Microsoft.Web/sites | FunctionExecutionCount |  함수 실행 횟수  | 
| Microsoft.Web/sites | CurrentAssemblies |  현재 어셈블리  | 
| Microsoft.Web/sites | CpuTime |  CPU 시간  | 
| Microsoft.Web/sites | BytesSent |  데이터 출력  | 
| Microsoft.Web/sites | BytesReceived |  데이터 입력  | 
| Microsoft.Web/sites | AverageResponseTime |  평균 응답 시간  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  평균 메모리 작업 집합  | 
| Microsoft.Web/sites | AppConnections |  Connections  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  언로드된 총 앱 도메인  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  총 앱 도메인  | 
| Microsoft.Web/sites/slots | 스레드 |  스레드 개수  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  애플리케이션 큐의 요청 수  | 
| Microsoft.Web/sites/slots | 요청 |  요청  | 
| Microsoft.Web/sites/slots | PrivateBytes |  프라이빗 바이트  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  메모리 작업 집합  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  초당 IO 쓰기 작업 수  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  초당 IO 쓰기 바이트 수  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  초당 IO 읽기 작업 수  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  초당 IO 읽기 바이트 수  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  초당 IO 기타 작업 수  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  초당 IO 기타 바이트 수  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  응답 시간  | 
| Microsoft.Web/sites/slots | Http5xx |  Http 서버 오류  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  상태 검사 상태  | 
| Microsoft.Web/sites/slots | 핸들 |  핸들 개수  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Gen 2 가비지 수집  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Gen 1 가비지 수집  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Gen 0 가비지 수집  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  함수 실행 단위  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  함수 실행 횟수  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  현재 어셈블리  | 
| Microsoft.Web/sites/slots | CpuTime |  CPU 시간  | 
| Microsoft.Web/sites/slots | BytesSent |  데이터 출력  | 
| Microsoft.Web/sites/slots | BytesReceived |  데이터 입력  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  평균 응답 시간  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  평균 메모리 작업 집합  | 
| Microsoft.Web/sites/slots | AppConnections |  Connections  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU 비율 | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | 데이터 IO 비율 | 
| Microsoft.Sql/servers/databases | log_write_percent | 로그 IO 비율 | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 비율 | 
| Microsoft.Sql/servers/databases | connection_successful | 성공적인 연결 | 
| Microsoft.Sql/servers/databases | connection_failed | 실패한 연결 | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | 방화벽에 의해 차단 | 
| Microsoft.Sql/servers/databases | 교착 상태 | 교착 상태 | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | 메모리 내 OLTP 스토리지 백분율 | 
| Microsoft.Sql/servers/databases | workers_percent | 작업자 백분율 | 
| Microsoft.Sql/servers/databases | sessions_percent | 세션 백분율 | 
| Microsoft.Sql/servers/databases | dtu_used | DTU 사용됨 | 
| Microsoft.Sql/servers/databases | cpu_used | 사용된 CPU | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 백분율 | 
| Microsoft.Sql/servers/databases | dwu_used | DWU 사용됨 | 
| Microsoft.Sql/servers/databases | cache_hit_percent | 캐시 적중 비율 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | 시스템 비율별 작업 그룹 할당 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | 최대 리소스 비율별 작업 그룹 할당 | 
| Microsoft.Sql/servers/databases | wlg_active_queries | 작업 그룹 활성 쿼리 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | 작업 그룹 큐에 대기 중인 쿼리 | 
| Microsoft.Sql/servers/databases | active_queries | 활성 쿼리 | 
| Microsoft.Sql/servers/databases | queued_queries | 큐에 대기 중인 쿼리 | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | 작업 그룹 쿼리 시간 제한 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | 작업 그룹 대기 쿼리 시간 제한 | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | 유효 최소 리소스 비율 | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | 유효 상한 리소스 비율 | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 비율 | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 데이터 IO 비율 | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | 로그 IO 비율 | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 비율 | 
| Microsoft.Sql/servers/elasticPools | workers_percent | 작업자 백분율 | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | 세션 백분율 | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU 사용 | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 메모리 내 OLTP 스토리지 백분율 | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU 비율 | 
| Microsoft.Sql/servers | dtu_used | DTU 사용됨 | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | 평균 CPU 비율 |