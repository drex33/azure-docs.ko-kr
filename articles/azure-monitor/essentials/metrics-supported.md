---
title: Azure Monitor에서 지원되는 리소스 종류별 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 09/10/2021
ms.author: robb
ms.openlocfilehash: ed095c4848d5e24113402145657f25bb821f8d8b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234626"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭

> [!NOTE]
> 이 목록은 대부분 자동으로 생성됩니다. GitHub를 통해 수정된 내용이 경고 없이 적용될 수 있습니다. 영구적으로 업데이트하는 방법에 대한 자세한 내용은 이 문서의 작성자에게 문의하세요.

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 

이 문서는 Azure Monitor의 통합 메트릭 파이프라인에서 현재 사용할 수 있는 모든 플랫폼(즉, 자동으로 수집된) 메트릭의 전체 목록입니다. 이 문서의 맨 위에 있는 날짜 이후에 변경되거나 추가된 메트릭은 아직 아래에 표시되지 않을 수 있습니다. 메트릭 목록을 프로그래밍 방식으로 쿼리하고 액세스하려면 [2018-01-01 API 버전](/rest/api/monitor/metricdefinitions)을 사용하세요. 이 목록에 없는 다른 메트릭은 포털에서 또는 레거시 API를 통해 사용할 수 있습니다.

메트릭은 리소스 공급자와 리소스 종류별로 구성됩니다. 서비스와 해당 서비스에 속한 리소스 공급자 및 리소스 종류 목록은 [Azure 서비스의 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)를 참조하세요.  

## <a name="exporting-platform-metrics-to-other-locations"></a>플랫폼 메트릭을 다른 위치로 내보내기

Azure Monitor 파이프라인에서 다른 위치로 플랫폼 메트릭을 내보내는 방법은 두 가지입니다.
1. [메트릭 REST API](/rest/api/monitor/metrics/list)를 사용합니다.
2. [진단 설정](../essentials/diagnostic-settings.md)을 사용하여 플랫폼 메트릭 라우팅 
    - Azure Storage
    - Azure Monitor Logs(따라서 Log Analytics도 함께)
    - 이벤트 허브: Microsoft가 아닌 시스템으로 플랫폼 메트릭을 가져올 때 사용 

메트릭을 라우팅하는 가장 쉬운 방법은 진단 설정을 사용하는 것이지만 몇 가지 제한 사항이 있습니다. 

- **일부는 내보낼 수 없음** - 모든 메트릭은 REST API를 사용하여 내보낼 수 있지만 일부 메트릭은 Azure Monitor 백 엔드의 복잡성 진단 설정을 사용하여 내보낼 수 없습니다. 아래 표의 *진단 설정을 통해 내보낼 수 있는* 열에는 이 방법으로 내보낼 수 있는 메트릭이 나열되어 있습니다.  

- **다차원 메트릭** - 진단 설정을 통해 다차원 메트릭을 다른 위치로 보내는 것은 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다. *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.

## <a name="guest-os-and-host-os-metrics"></a>게스트 OS 및 호스트 OS 메트릭

> [!WARNING]
> Azure Virtual Machines, Service Fabric 및 Cloud Services에서 실행되는 게스트 OS(게스트 운영 체제)에 대한 메트릭은 여기에 나열되지 **않았습니다**. 게스트 OS 메트릭은 게스트 운영 체제 위에서 또는 게스트 운영 체제의 일부로 실행되는 하나 이상의 에이전트를 통해 수집해야 합니다.  게스트 OS 메트릭에는 게스트 CPU 백분율 또는 메모리 사용량을 추적하는 성능 카운터가 포함되어 있으며, 둘 다 자동 스케일링 또는 경고에 자주 사용됩니다. 
>
> **호스트 OS 메트릭 ARE를 사용할 수 있으며 아래에 나열되어 있습니다.** 이러한 메트릭은 서로 다릅니다. 호스트 OS 메트릭은 게스트 OS 세션을 호스트하는 Hyper-V 세션과 관련이 있습니다. 

> [!TIP]
> Azure Monitor Agent를 사용 및 구성하여 플랫폼 메트릭이 저장된 Azure Monitor 메트릭 데이터베이스에 게스트 OS 성능 메트릭을 전송하는 것이 가장 좋은 방법입니다. 에이전트는 [사용자 지정 메트릭](../essentials/metrics-custom-overview.md) API를 통해 게스트 OS 메트릭을 라우팅합니다. 그 후 차트로 만들고 경고하거나, 플랫폼 메트릭과 같은 게스트 OS 메트릭을 사용할 수 있습니다. 또는 동일한 에이전트를 사용하여 게스트 OS 메트릭을 Azure Monitor Logs에 보낼 수도 있습니다. Log Analytics를 사용하여 메트릭이 아닌 데이터와 함께 이러한 메트릭을 쿼리할 수 있습니다. 

Azure Monitor Agent는 이 라우팅에 이전에 사용된 Azure Diagnostics 확장 및 Log Analytics 에이전트를 대체합니다. 중요한 추가 정보는 [모니터링 에이전트 개요](../agents/agents-overview.md)를 참조하세요.

## <a name="table-formatting"></a>테이블 서식 지정

> [!IMPORTANT] 
> 이 최신 업데이트에서는 새 열을 추가되고 메트릭을 사전순으로 다시 정렬했습니다. 추가 정보는 브라우저 창의 너비에 따라 아래쪽에 가로 스크롤 막대가 있을 수 있다는 것을 의미합니다. 누락된 정보가 있다고 생각되는 경우 스크롤 막대를 사용하여 테이블 전체를 확인하세요.


## <a name="microsoftaadiamazureadmetrics"></a>microsoft.aadiam/azureADMetrics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ThrottledRequests|예|ThrottledRequests|개수|평균|azureADMetrics 형식 메트릭|차원 없음|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|예|메모리: 클리너 현재 가격|개수|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|예|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|CleanerMemoryShrinkable|예|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CommandPoolBusyThreads|예|스레드: 명령 풀 사용 중인 스레드|개수|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|예|스레드: 명령 풀 유휴 상태 스레드|개수|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|예|명령 풀의 작업 큐 길이|개수|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|CurrentConnections|예|연결: 현재 연결|개수|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CurrentUserSessions|예|현재 사용자 세션 수|개수|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|LongParsingBusyThreads|예|스레드: 긴 구문 분석 사용 중인 스레드|개수|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|예|스레드: 긴 구문 분석 유휴 상태 스레드|개수|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|예|스레드: 긴 구문 분석 작업 큐 길이|개수|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|mashup_engine_memory_metric|예|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|
|mashup_engine_private_bytes_metric|예|M 엔진 프라이빗 바이트|바이트|평균|매시업 엔진 프로세스의 프라이빗 바이트 사용량|ServerResourceType|
|mashup_engine_qpu_metric|예|M 엔진 QPU|개수|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_virtual_bytes_metric|예|M 엔진 가상 바이트|바이트|평균|매시업 엔진 프로세스의 가상 바이트 사용량|ServerResourceType|
|memory_metric|예|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|memory_thrashing_metric|예|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|MemoryLimitHard|예|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|예|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|예|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|예|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryUsage|예|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|private_bytes_metric|예|프라이빗 바이트|바이트|평균|프라이빗 바이트|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|예|스레드: 처리 풀 사용 중인 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|예|스레드: 처리 풀 사용 중인 비-I/O 스레드|개수|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|예|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|예|스레드: 처리 풀 유휴 상태 비-I/O 스레드|개수|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|예|스레드: 처리 중인 풀 I/O 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|예|처리 풀의 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|qpu_metric|예|QPU|개수|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|QueryPoolBusyThreads|예|쿼리 풀의 사용 중인 스레드|개수|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|예|스레드: 쿼리 풀 유휴 상태 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|예|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|할당량|예|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|예|메모리: 차단된 할당량|개수|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|RowsConvertedPerSec|예|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsReadPerSec|예|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|예|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|ShortParsingBusyThreads|예|스레드: 짧은 구문 분석 사용 중인 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|예|스레드: 짧은 구문 분석 유휴 상태 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|예|스레드: 짧은 구문 분석 작업 큐 길이|개수|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|예|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|예|총 연결 실패 수|개수|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|TotalConnectionRequests|예|총 연결 요청 수|개수|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|VertiPaqNonpaged|예|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|예|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|virtual_bytes_metric|예|가상 바이트|바이트|평균|가상 바이트|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BackendDuration|예|백 엔드 요청 지속 시간|밀리초|평균|밀리초 단위의 백 엔드 요청 기간|위치, 호스트 이름|
|용량|예|용량|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭. 참고: Premium 이외의 skus의 경우, 'Max' 집계는 값을 0으로 표시합니다.|위치|
|ConnectionAttempts|예|WebSocket 연결 시도(미리 보기)|개수|합계|선택한 원본 및 대상에 따라 WebSocket 연결 시도 횟수|위치, 원본, 대상, 상태|
|Duration|예|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|EventHubDroppedEvents|예|삭제된 EventHub 이벤트|개수|합계|큐 크기 제한에 도달하여 건너뛴 이벤트 수|위치|
|EventHubRejectedEvents|예|거부된 EventHub 이벤트|개수|합계|거부된 EventHub 이벤트 수(잘못 구성되었거나 권한 없음)|위치|
|EventHubSuccessfulEvents|예|성공한 EventHub 이벤트|개수|합계|성공한 EventHub 이벤트 수|위치|
|EventHubThrottledEvents|예|제한된 EventHub 이벤트|개수|합계|제한된 EventHub 이벤트 수|위치|
|EventHubTimedoutEvents|예|시간 초과된 EventHub 이벤트|개수|합계|시간 초과된 EventHub 이벤트 수|위치|
|EventHubTotalBytesSent|예|EventHub 이벤트 크기|바이트|합계|EventHub 이벤트의 총 크기(바이트)|위치|
|EventHubTotalEvents|예|총 EventHub 이벤트|개수|합계|EventHub로 전송된 이벤트 수|위치|
|EventHubTotalFailedEvents|예|실패한 EventHub 이벤트|개수|합계|실패한 EventHub 이벤트 수|위치|
|FailedRequests|예|실패한 게이트웨이 요청(사용되지 않음)|개수|합계|게이트웨이 요청의 실패 횟수 - GatewayResponseCodeCategory 차원에서 다차원 요청 메트릭을 대신 사용합니다.|위치, 호스트 이름|
|NetworkConnectivity|예|리소스의 네트워크 연결 상태(미리 보기)|개수|평균|API Management 서비스에 종속된 리소스 종류의 네트워크 연결 상태|위치, ResourceType|
|OtherRequests|예|기타 게이트웨이 요청(사용되지 않음)|개수|합계|다른 게이트웨이 요청 횟수 - GatewayResponseCodeCategory 차원에서 다차원 요청 메트릭을 대신 사용합니다.|위치, 호스트 이름|
|요청|예|요청|개수|합계|다차원을 사용하는 게이트웨이 요청 메트릭|위치, 호스트이름, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|예|성공적인 게이트웨이 요청(사용되지 않음)|개수|합계|성공한 게이트웨이 요청 횟수 - GatewayResponseCodeCategory 차원에서 다차원 요청 메트릭을 대신 사용합니다.|위치, 호스트 이름|
|TotalRequests|예|총 게이트웨이 요청(사용되지 않음)|개수|합계|게이트웨이 요청 횟수 - GatewayResponseCodeCategory 차원에서 다차원 요청 메트릭을 대신 사용합니다.|위치, 호스트 이름|
|UnauthorizedRequests|예|권한이 없는 게이트웨이 요청(사용되지 않음)|개수|합계|권한 없는 게이트웨이 요청 횟수 - GatewayResponseCodeCategory 차원에서 다차원 요청 메트릭을 대신 사용합니다.|위치, 호스트 이름|
|WebSocketMessages|예|WebSocket 메시지 (미리 보기)|개수|합계|선택한 원본 및 대상을 기준으로 하는 WebSocket 메시지 수|위치, 원본, 대상|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|예|HttpIncomingRequestCount|개수|개수|들어오는 http 요청의 총 수|StatusCode, 인증|
|HttpIncomingRequestDuration|예|HttpIncomingRequestDuration|개수|평균|http 요청의 대기 시간|StatusCode, 인증|
|ThrottledHttpRequestCount|예|ThrottledHttpRequestCount|개수|개수|제한된 http 요청|차원 없음|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active-timer-count|예|active-timer-count|개수|평균|현재 활성 상태인 타이머 수|배포, AppName, Pod|
|alloc-rate|예|alloc-rate|바이트|평균|관리되는 힙에 할당된 바이트 수|배포, AppName, Pod|
|AppCpuUsage|예|앱 CPU 사용량 |백분율|평균|앱의 최근 CPU 사용량|배포, AppName, Pod|
|assembly-count|예|assembly-count|개수|평균|로드된 어셈블리 수|배포, AppName, Pod|
|cpu-usage|예|cpu-usage|백분율|평균|프로세스에서 CPU를 사용한 시간(%)|배포, AppName, Pod|
|current-requests|예|current-requests|개수|평균|프로세스 수명 동안 처리 중인 총 요청 수|배포, AppName, Pod|
|exception-count|예|exception-count|개수|합계|예외 수|배포, AppName, Pod|
|failed-requests|예|failed-requests|개수|평균|프로세스 수명 동안 실패한 총 요청 수|배포, AppName, Pod|
|gc-heap-size|예|gc-heap-size|개수|평균|GC에서 보고한 총 힙 크기(MB)|배포, AppName, Pod|
|gen-0-gc-count|예|gen-0-gc-count|개수|평균|0세대 GC 수|배포, AppName, Pod|
|gen-0-size|예|gen-0-size|바이트|평균|0세대 힙 크기|배포, AppName, Pod|
|gen-1-gc-count|예|gen-1-gc-count|개수|평균|1세대 GC 수|배포, AppName, Pod|
|gen-1-size|예|gen-1-size|바이트|평균|1세대 힙 크기|배포, AppName, Pod|
|gen-2-gc-count|예|gen-2-gc-count|개수|평균|2세대 GC 수|배포, AppName, Pod|
|gen-2-size|예|gen-2-size|바이트|평균|2세대 힙 크기|배포, AppName, Pod|
|IngressBytesReceived|예|수신된 바이트|바이트|평균|클라이언트에서 Azure Spring Cloud 받은 바이트 수|호스트 이름, HttpStatus|
|IngressBytesReceivedRate|예|처리량 (바이트/초)|초당 바이트 수|평균|클라이언트에서 Azure 스프링 클라우드의 초당 받은 바이트 수입니다.|호스트 이름, HttpStatus|
|IngressBytesSent|예|보낸 바이트|바이트|평균|Azure 스프링 클라우드에서 클라이언트에 보낸 바이트 수|호스트 이름, HttpStatus|
|IngressBytesSentRate|예|처리량 (바이트/초)|초당 바이트 수|평균|Azure 스프링 클라우드에서 클라이언트에 초당 보낸 바이트 수|호스트 이름, HttpStatus|
|IngressFailedRequests|예|실패한 요청|개수|평균|클라이언트에서 Azure 스프링 클라우드의 실패 한 요청 수|호스트 이름, HttpStatus|
|IngressRequests|예|요청|개수|평균|클라이언트의 Azure 스프링 클라우드의 요청 수|호스트 이름, HttpStatus|
|IngressResponseStatus|예|응답 상태|개수|평균|Azure 스프링 클라우드에서 반환 된 HTTP 응답 상태입니다. 응답 상태 코드 배포를 추가로 범주화 하 여 2xx, 3xx, 4xx 및 5xx 범주의 응답을 표시할 수 있습니다.|호스트 이름, HttpStatus|
|IngressResponseTime|예|응답 시간|초|평균|Azure 스프링 클라우드의 Http 응답 시간 반환|호스트 이름, HttpStatus|
|jvm.gc.live.data.size|예|jvm.gc.live.data.size|바이트|평균|전체 GC 후의 이전 세대 메모리 풀 크기|배포, AppName, Pod|
|jvm.gc.max.data.size|예|jvm.gc.max.data.size|바이트|평균|이전 세대 메모리 풀의 최대 크기|배포, AppName, Pod|
|jvm.gc.memory.allocated|예|jvm.gc.memory.allocated|바이트|최대|한 GC 이후부터 그 다음 GC 전까지 젊은 생성 메모리 풀 크기가 커진 만큼 증가|배포, AppName, Pod|
|jvm.gc.memory.promoted|예|jvm.gc.memory.promoted|바이트|최대|GC 전에서 GC 이후에 이전 세대 메모리 풀의 크기가 증가한 수|배포, AppName, Pod|
|jvm.gc.pause.total.count|예|jvm.gc.pause.total.count|개수|합계|GC 일시 중지 횟수|배포, AppName, Pod|
|jvm.gc.pause.total.time|예|jvm.gc.pause.total.time|밀리초|합계|GC 일시 중지 총 시간|배포, AppName, Pod|
|jvm.memory.committed|예|jvm.memory.committed|바이트|평균|JVM에 할당된 메모리(바이트)|배포, AppName, Pod|
|jvm.memory.max|예|jvm.memory.max|바이트|최대|메모리 관리에 사용할 수 있는 최대 메모리 양(바이트)|배포, AppName, Pod|
|jvm.memory.used|예|jvm.memory.used|바이트|평균|사용된 앱 메모리(바이트)|배포, AppName, Pod|
|loh-size|예|loh-size|바이트|평균|LOH 힙 크기|배포, AppName, Pod|
|monitor-lock-contention-count|예|monitor-lock-contention-count|개수|평균|모니터를 잠그려고 시도할 때 경합이 발생한 횟수|배포, AppName, Pod|
|PodCpuUsage|예|앱 CPU 사용량|백분율|평균|앱의 최근 CPU 사용량|배포, AppName, Pod|
|PodMemoryUsage|Yes|앱 메모리 사용량|백분율|평균|앱의 최근 메모리 사용량|배포, AppName, Pod|
|process.cpu.usage|예|process.cpu.usage|백분율|평균|JVM 프로세스의 최근 CPU 사용량|배포, AppName, Pod|
|requests-per-second|예|requests-rate|개수|평균|요청 속도|배포, AppName, Pod|
|system.cpu.usage|예|system.cpu.usage|백분율|평균|전체 시스템의 최근 CPU 사용량|배포, AppName, Pod|
|threadpool-completed-items-count|예|threadpool-completed-items-count|개수|평균|ThreadPool 완료된 작업 항목 수|배포, AppName, Pod|
|threadpool-queue-length|예|threadpool-queue-length|개수|평균|ThreadPool 작업 항목 큐 길이|배포, AppName, Pod|
|threadpool-thread-count|예|threadpool-thread-count|개수|평균|ThreadPool 스레드 수|배포, AppName, Pod|
|time-in-gc|예|time-in-gc|백분율|평균|마지막 GC 이후 GC의 시간(%)|배포, AppName, Pod|
|tomcat.global.error|예|tomcat.global.error|개수|합계|Tomcat 전역 오류|배포, AppName, Pod|
|tomcat.global.received|예|tomcat.global.received|바이트|합계|Tomcat 총 수신 바이트|배포, AppName, Pod|
|tomcat.global.request.avg.time|예|tomcat.global.request.avg.time|밀리초|평균|Tomcat 요청 평균 시간|배포, AppName, Pod|
|tomcat.global.request.max|예|tomcat.global.request.max|밀리초|최대|Tomcat 요청 최대 시간|배포, AppName, Pod|
|tomcat.global.request.total.count|예|tomcat.global.request.total.count|개수|합계|Tomcat 요청 총 수|배포, AppName, Pod|
|tomcat.global.request.total.time|예|tomcat.global.request.total.time|밀리초|합계|Tomcat 요청 총 시간|배포, AppName, Pod|
|tomcat.global.sent|예|tomcat.global.sent|바이트|합계|Tomcat 총 보낸 바이트|배포, AppName, Pod|
|tomcat.sessions.active.current|예|tomcat.sessions.active.current|개수|합계|Tomcat 세션 활성 수|배포, AppName, Pod|
|tomcat.sessions.active.max|예|tomcat.sessions.active.max|개수|합계|Tomcat 세션 최대 활성 수|배포, AppName, Pod|
|tomcat.sessions.alive.max|예|tomcat.sessions.alive.max|밀리초|최대|Tomcat 세션 최대 연결 시간|배포, AppName, Pod|
|tomcat.sessions.created|예|tomcat.sessions.created|개수|합계|Tomcat 세션 생성 횟수|배포, AppName, Pod|
|tomcat.sessions.expired|예|tomcat.sessions.expired|개수|합계|Tomcat 세션 만료 수|배포, AppName, Pod|
|tomcat.sessions.rejected|예|tomcat.sessions.rejected|개수|합계|Tomcat 세션 거부 횟수|배포, AppName, Pod|
|tomcat.threads.config.max|예|tomcat.threads.config.max|개수|합계|Tomcat Config 최대 스레드 수|배포, AppName, Pod|
|tomcat.threads.current|예|tomcat.threads.current|개수|합계|Tomcat 현재 스레드 수|배포, AppName, Pod|
|total-requests|예|total-requests|개수|평균|프로세스 수명 동안 총 요청 수|배포, AppName, Pod|
|working-set|예|working-set|개수|평균|프로세스에서 사용한 작업 세트의 양(MB)|배포, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|TotalJob|예|총 작업 수|개수|합계|총 작업 수|Runbook, 상태|
|TotalUpdateDeploymentMachineRuns|예|총 업데이트 배포 머신 실행|개수|합계|소프트웨어 업데이트 배포 실행의 총 소프트웨어 업데이트 배포 머신 실행 횟수|SoftwareUpdateConfigurationName, 상태, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|예|총 업데이트 배포 실행|개수|합계|총 소프트웨어 업데이트 배포 실행 횟수|SoftwareUpdateConfigurationName, 상태|


## <a name="microsoftavsprivateclouds"></a>microsoft.avs/privateClouds

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CapacityLatest|예|데이터 저장소 디스크 총 용량|바이트|평균|데이터 저장소의 총 디스크 용량|dsname|
|DiskUsedPercentage|예| 사용되는 데이터 저장소 디스크 백분율|백분율|평균|데이터 저장소에 사용되는 가용 디스크의 백분율|dsname|
|EffectiveCpuAverage|예|백분율 CPU|백분율|평균|클러스터에서 사용되는 CPU 리소스의 백분율|clustername|
|EffectiveMemAverage|예|평균 유효 메모리|바이트|평균|클러스터에서 사용 가능한 총 머신 메모리 양|clustername|
|OverheadAverage|예|평균 메모리 오버헤드|바이트|평균|가상화 인프라에서 사용하는 호스트 실제 메모리|clustername|
|TotalMbAverage|예|평균 총 메모리|바이트|평균|클러스터의 총 메모리|clustername|
|UsageAverage|예|평균 메모리 사용량|백분율|평균|구성된 또는 사용 가능한 총 메모리의 백분율로 구하는 메모리 사용량|clustername|
|UsedLatest|예|사용된 데이터 저장소 디스크|바이트|평균|데이터 저장소에 사용된 총 디스크 양|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CoreCount|예|전용된 코어 수|개수|합계|배치 계정의 총 전용 코어 수|차원 없음|
|CreatingNodeCount|예|노드 수 만들기|개수|합계|만든 노드 수|차원 없음|
|IdleNodeCount|예|유휴 상태인 노드 수|개수|합계|유휴 노드 수|차원 없음|
|JobDeleteCompleteEvent|예|작업 삭제 완료 이벤트|개수|합계|정상적으로 삭제된 총 작업 수입니다.|jobId|
|JobDeleteStartEvent|예|작업 삭제 시작 이벤트|개수|합계|삭제되도록 요청된 총 작업 수입니다.|jobId|
|JobDisableCompleteEvent|예|작업 비활성화 완료 이벤트|개수|합계|정상적으로 사용하지 않도록 설정된 총 작업 수입니다.|jobId|
|JobDisableStartEvent|예|작업 비활성화 시작 이벤트|개수|합계|비활성화되도록 요청된 총 작업 수입니다.|jobId|
|JobStartEvent|예|작업 시작 이벤트|개수|합계|정상적으로 시작된 총 작업 수입니다.|jobId|
|JobTerminateCompleteEvent|예|작업 종료 완료 이벤트|개수|합계|정상적으로 종료된 총 작업 수입니다.|jobId|
|JobTerminateStartEvent|예|작업 종료 시작 이벤트|개수|합계|종료되도록 요청된 총 작업 수입니다.|jobId|
|LeavingPoolNodeCount|예|나가는 풀 노드 수|개수|합계|풀을 나가는 노드 수|차원 없음|
|LowPriorityCoreCount|예|LowPriority 코어 수|개수|합계|배치 계정의 우선 순위가 낮은 총 코어 수|차원 없음|
|OfflineNodeCount|예|오프라인 노드 수|개수|합계|오프라인 노드 수|차원 없음|
|PoolCreateEvent|예|풀 만들기 이벤트|개수|합계|만든 총 풀 수|poolId|
|PoolDeleteCompleteEvent|예|풀 삭제 완료 이벤트|개수|합계|완료된 총 풀 삭제 수|poolId|
|PoolDeleteStartEvent|예|풀 삭제 시작 이벤트|개수|합계|시작된 총 풀 삭제 수|poolId|
|PoolResizeCompleteEvent|예|풀 크기 조정 완료 이벤트|개수|합계|완료된 총 풀 크기 조정 수|poolId|
|PoolResizeStartEvent|예|풀 크기 조정 시작 이벤트|개수|합계|시작된 총 풀 크기 조정 작업 수|poolId|
|PreemptedNodeCount|예|선점된 노드 수|개수|합계|선점된 노드 수|차원 없음|
|RebootingNodeCount|예|재부팅 노드 수|개수|합계|다시 부팅하는 노드의 수|차원 없음|
|ReimagingNodeCount|예|이미지로 다시 설치하는 노드 수|개수|합계|이미지로 다시 설치하는 노드의 수|차원 없음|
|RunningNodeCount|예|실행 노드 수|개수|합계|실행 중인 노드의 수|차원 없음|
|StartingNodeCount|예|시작 노드 수|개수|합계|시작 노드 수|차원 없음|
|StartTaskFailedNodeCount|예|시작 작업 실패 노드 수|개수|합계|시작 작업이 실패한 노드 수|차원 없음|
|TaskCompleteEvent|예|작업 완료 이벤트|개수|합계|완료된 총 작업 수|poolId, jobId|
|TaskFailEvent|예|작업 실패 이벤트|개수|합계|실패한 상태로 완료된 총 작업 수|poolId, jobId|
|TaskStartEvent|예|작업 시작 이벤트|개수|합계|시작된 총 작업 수|poolId, jobId|
|TotalLowPriorityNodeCount|예|우선 순위가 낮은 노드 수|개수|합계|배치 계정의 우선 순위가 낮은 총 노드 수|차원 없음|
|TotalNodeCount|예|전용된 노드 수|개수|합계|배치 계정의 총 전용 노드 수|차원 없음|
|UnusableNodeCount|예|사용 불가 노드 수|개수|합계|사용할 수 없는 노드 수|차원 없음|
|WaitingForStartTaskNodeCount|예|작업 시작 대기 노드 수|개수|합계|시작 작업 완료를 기다리는 노드 수|차원 없음|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|활성 코어|예|활성 코어|개수|평균|활성 코어 수|시나리오, ClusterName|
|활성 노드|예|활성 노드|개수|평균|실행 중인 노드의 수|시나리오, ClusterName|
|유휴 코어|예|유휴 코어|개수|평균|유휴 코어 수|시나리오, ClusterName|
|유휴 노드|예|유휴 노드|개수|평균|유휴 노드 수|시나리오, ClusterName|
|작업 완료됨|예|작업 완료됨|개수|합계|완료한 작업 수|시나리오, ClusterName, ResultType|
|작업 제출됨|예|작업 제출됨|개수|합계|제출한 작업 수|시나리오, ClusterName|
|나가는 코어|예|나가는 코어|개수|평균|나가는 코어 수|시나리오, ClusterName|
|나가는 노드|예|나가는 노드|개수|평균|나가는 노드 수|시나리오, ClusterName|
|선점된 코어|예|선점된 코어|개수|평균|선점된 코어 수|시나리오, ClusterName|
|선점된 노드|예|선점된 노드|개수|평균|선점된 노드 수|시나리오, ClusterName|
|할당량 사용률|예|할당량 사용률|개수|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|
|총 코어 수|예|총 코어 수|개수|평균|총 코어 수|시나리오, ClusterName|
|총 노드 수|예|총 노드 수|개수|평균|총 노드 수|시나리오, ClusterName|
|사용 불가 코어|예|사용 불가 코어|개수|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|사용 불가 노드|예|사용 불가 노드|개수|평균|사용할 수 없는 노드 수|시나리오, ClusterName|


## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BlockedCalls|예|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수|ApiName, ServingRegion, StatusCode|
|ClientErrors|예|클라이언트 오류|개수|합계|클라이언트 오류(HTTP 상태 코드 4xx)가 있는 호출 수|ApiName, ServingRegion, StatusCode|
|DataIn|예|데이터 입력|바이트|합계|들어오는 요청 Content-Length(바이트)|ApiName, ServingRegion, StatusCode|
|DataOut|예|데이터 출력|바이트|합계|나가는 응답 Content-Length(바이트)|ApiName, ServingRegion, StatusCode|
|대기 시간|예|대기 시간|밀리초|평균|대기 시간(밀리초)|ApiName, ServingRegion, StatusCode|
|ServerErrors|예|서버 오류|개수|합계|서버 오류(HTTP 상태 코드 5xx)가 있는 호출 수|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls|예|성공한 호출|개수|합계|성공한 호출 수(HTTP 상태 코드 2xx)|ApiName, ServingRegion, StatusCode|
|TotalCalls|예|총 호출|개수|합계|총 호출 수|ApiName, ServingRegion, StatusCode|
|TotalErrors|예|총 오류|개수|합계|오류(HTTP 상태 코드 4xx 또는 5xx)가 있는 호출 수|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|예|BroadcastProcessedCountDisplayName|개수|평균|처리된 트랜잭션 수|노드, 채널, 유형, 상태|
|ChaincodeExecuteTimeouts|예|ChaincodeExecuteTimeoutsDisplayName|개수|평균|시간이 초과된 chaincode 실행(Init 또는 Invoke) 횟수|노드, chaincode|
|ChaincodeLaunchFailures|예|ChaincodeLaunchFailuresDisplayName|개수|평균|실패한 chaincode 시작 횟수|노드, chaincode|
|ChaincodeLaunchTimeouts|예|ChaincodeLaunchTimeoutsDisplayName|개수|평균|시간 초과된 chaincode 시작 횟수|노드, chaincode|
|ChaincodeShimRequestsCompleted|예|ChaincodeShimRequestsCompletedDisplayName|개수|평균|완료된 chaincode shim 요청 수|노드, 형식, 채널, chaincode, 성공|
|ChaincodeShimRequestsReceived|예|ChaincodeShimRequestsReceivedDisplayName|개수|평균|수신된 chaincode shim 요청 수|노드, 형식, 채널, chaincode|
|ClusterCommEgressQueueCapacity|예|ClusterCommEgressQueueCapacityDisplayName|개수|평균|송신 큐의 용량|노드, 호스트, msg_type, 채널|
|ClusterCommEgressQueueLength|예|ClusterCommEgressQueueLengthDisplayName|개수|평균|송신 큐의 길이|노드, 호스트, msg_type, 채널|
|ClusterCommEgressQueueWorkers|예|ClusterCommEgressQueueWorkersDisplayName|개수|평균|송신 큐 작업자 수|노드, 채널|
|ClusterCommEgressStreamCount|예|ClusterCommEgressStreamCountDisplayName|개수|평균|다른 노드로 전송되는 스트림 수|노드, 채널|
|ClusterCommEgressTlsConnectionCount|예|ClusterCommEgressTlsConnectionCountDisplayName|개수|평균|다른 노드에 대한 TLS 연결 수|노드|
|ClusterCommIngressStreamCount|예|ClusterCommIngressStreamCountDisplayName|개수|평균|다른 노드에서 오는 스트림 수|노드|
|ClusterCommMsgDroppedCount|예|ClusterCommMsgDroppedCountDisplayName|개수|평균|삭제된 메시지 수|노드, 호스트, 채널|
|ConnectionAccepted|예|수락된 연결|개수|합계|수락된 연결|노드|
|ConnectionActive|예|활성 연결 수|개수|평균|활성 연결 수|노드|
|ConnectionHandled|예|처리된 연결|개수|합계|처리된 연결|노드|
|ConsensusEtcdraftActiveNodes|예|ConsensusEtcdraftActiveNodesDisplayName|개수|평균|이 채널의 활성 노드 수|노드, 채널|
|ConsensusEtcdraftClusterSize|예|ConsensusEtcdraftClusterSizeDisplayName|개수|평균|이 채널의 노드 수|노드, 채널|
|ConsensusEtcdraftCommittedBlockNumber|예|ConsensusEtcdraftCommittedBlockNumberDisplayName|개수|평균|최근에 커밋된 블록의 블록 번호|노드, 채널|
|ConsensusEtcdraftConfigProposalsReceived|예|ConsensusEtcdraftConfigProposalsReceivedDisplayName|개수|평균|구성 유형 트랜잭션에 대해 수신된 총 제안 수|노드, 채널|
|ConsensusEtcdraftIsLeader|예|ConsensusEtcdraftIsLeaderDisplayName|개수|평균|현재 노드의 리더십 상태. 리더이면 1, 그렇지 않으면 0|노드, 채널|
|ConsensusEtcdraftLeaderChanges|예|ConsensusEtcdraftLeaderChangesDisplayName|개수|평균|프로세스가 시작된 후 리더 변경 횟수|노드, 채널|
|ConsensusEtcdraftNormalProposalsReceived|예|ConsensusEtcdraftNormalProposalsReceivedDisplayName|개수|평균|일반 유형 트랜잭션에 대해 수신된 총 제안 수|노드, 채널|
|ConsensusEtcdraftProposalFailures|예|ConsensusEtcdraftProposalFailuresDisplayName|개수|평균|제안 실패 횟수|노드, 채널|
|ConsensusEtcdraftSnapshotBlockNumber|예|ConsensusEtcdraftSnapshotBlockNumberDisplayName|개수|평균|최신 스냅샷의 블록 번호|노드, 채널|
|ConsensusKafkaBatchSize|예|ConsensusKafkaBatchSizeDisplayName|개수|평균|토픽으로 전송된 평균 일괄 처리 크기(바이트)|노드, 토픽|
|ConsensusKafkaCompressionRatio|예|ConsensusKafkaCompressionRatioDisplayName|개수|평균|토픽의 평균 압축 비율(백분율)|노드, 토픽|
|ConsensusKafkaIncomingByteRate|예|ConsensusKafkaIncomingByteRateDisplayName|개수|평균|broker에서 읽는 초당 바이트 수|노드, broker_id|
|ConsensusKafkaLastOffsetPersisted|예|ConsensusKafkaLastOffsetPersistedDisplayName|개수|평균|가장 최근에 커밋된 블록의 블록 메타데이터에 지정된 오프셋|노드, 채널|
|ConsensusKafkaOutgoingByteRate|예|ConsensusKafkaOutgoingByteRateDisplayName|개수|평균|broker에 쓰는 초당 바이트 수|노드, broker_id|
|ConsensusKafkaRecordSendRate|예|ConsensusKafkaRecordSendRateDisplayName|개수|평균|토픽에 전송되는 초당 레코드 수|노드, 토픽|
|ConsensusKafkaRecordsPerRequest|예|ConsensusKafkaRecordsPerRequestDisplayName|개수|평균|토픽에 전송되는 요청당 평균 레코드 수|노드, 토픽|
|ConsensusKafkaRequestLatency|예|ConsensusKafkaRequestLatencyDisplayName|개수|평균|broker의 평균 요청 대기 시간(밀리초)|노드, broker_id|
|ConsensusKafkaRequestRate|예|ConsensusKafkaRequestRateDisplayName|개수|평균|broker에 전송되는 초당 요청 수|노드, broker_id|
|ConsensusKafkaRequestSize|예|ConsensusKafkaRequestSizeDisplayName|개수|평균|브로커의 평균 요청 크기(바이트)|노드, broker_id|
|ConsensusKafkaResponseRate|예|ConsensusKafkaResponseRateDisplayName|개수|평균|broker에 전송되는 초당 요청 수|노드, broker_id|
|ConsensusKafkaResponseSize|예|ConsensusKafkaResponseSizeDisplayName|개수|평균|브로커의 평균 응답 크기(바이트)|노드, broker_id|
|CpuUsagePercentageInDouble|예|CPU 사용 백분율|백분율|최대|CPU 사용 백분율|노드|
|DeliverBlocksSent|예|DeliverBlocksSentDisplayName|개수|평균|전송 서비스에서 보낸 블록 수|노드, 채널, 필터링, data_type|
|DeliverRequestsCompleted|예|DeliverRequestsCompletedDisplayName|개수|평균|완료된 전송 요청 수|노드, 채널, 필터링, data_type, 성공|
|DeliverRequestsReceived|예|DeliverRequestsReceivedDisplayName|개수|평균|수신된 전송 요청 수|노드, 채널, 필터링, data_type|
|DeliverStreamsClosed|예|DeliverStreamsClosedDisplayName|개수|평균|전송 서비스에 대해 닫힌 GRPC 스트림의 수|노드|
|DeliverStreamsOpened|예|DeliverStreamsOpenedDisplayName|개수|평균|전송 서비스에 대해 열린 GRPC 스트림의 수|노드|
|EndorserChaincodeInstantiationFailures|예|EndorserChaincodeInstantiationFailuresDisplayName|개수|평균|실패한 chaincode 인스턴스화 또는 업그레이드 수|노드, 채널, chaincode|
|EndorserDuplicateTransactionFailures|예|EndorserDuplicateTransactionFailuresDisplayName|개수|평균|트랜잭션 ID 중복으로 인해 실패한 제안 수|노드, 채널, chaincode|
|EndorserEndorsementFailures|예|EndorserEndorsementFailuresDisplayName|개수|평균|실패한 인증 수|노드, 채널, chaincode, chaincodeerror|
|EndorserProposalAclFailures|예|EndorserProposalAclFailuresDisplayName|개수|평균|ACL 검사에 실패한 제안 수|노드, 채널, chaincode|
|EndorserProposalSimulationFailures|예|EndorserProposalSimulationFailuresDisplayName|개수|평균|실패한 제안 시뮬레이션 수|노드, 채널, chaincode|
|EndorserProposalsReceived|예|EndorserProposalsReceivedDisplayName|개수|평균|받은 제안 수|노드|
|EndorserProposalValidationFailures|예|EndorserProposalValidationFailuresDisplayName|개수|평균|초기 유효성 검사에 실패한 제안 수|노드|
|EndorserSuccessfulProposals|예|EndorserSuccessfulProposalsDisplayName|개수|평균|성공한 제안 수|노드|
|FabricVersion|예|FabricVersionDisplayName|개수|평균|활성 버전의 패브릭|노드, 버전|
|GossipCommMessagesReceived|예|GossipCommMessagesReceivedDisplayName|개수|평균|받은 메시지 수|노드|
|GossipCommMessagesSent|예|GossipCommMessagesSentDisplayName|개수|평균|보낸 메시지 수|노드|
|GossipCommOverflowCount|예|GossipCommOverflowCountDisplayName|개수|평균|나가는 큐 버퍼 오버플로의 수|노드|
|GossipLeaderElectionLeader|예|GossipLeaderElectionLeaderDisplayName|개수|평균|피어가 리더(1) 또는 팔로워(0)|노드, 채널|
|GossipMembershipTotalPeersKnown|예|GossipMembershipTotalPeersKnownDisplayName|개수|평균|알려진 총 피어 수|노드, 채널|
|GossipPayloadBufferSize|예|GossipPayloadBufferSizeDisplayName|개수|평균|페이로드 버퍼 크기|노드, 채널|
|GossipStateHeight|예|GossipStateHeightDisplayName|개수|평균|현재 원장 높이|노드, 채널|
|GrpcCommConnClosed|예|GrpcCommConnClosedDisplayName|개수|평균|닫힌 gRPC 연결 수. 열린 수에서 닫힌 수를 뺀 값이 활성 연결 수입니다.|노드|
|GrpcCommConnOpened|예|GrpcCommConnOpenedDisplayName|개수|평균|열린 gRPC 연결 수. 열린 수에서 닫힌 수를 뺀 값이 활성 연결 수입니다.|노드|
|GrpcServerStreamMessagesReceived|예|GrpcServerStreamMessagesReceivedDisplayName|개수|평균|받은 스트림 메시지 수|노드, 서비스, 메서드|
|GrpcServerStreamMessagesSent|예|GrpcServerStreamMessagesSentDisplayName|개수|평균|보낸 스트림 메시지 수|노드, 서비스, 메서드|
|GrpcServerStreamRequestsCompleted|예|GrpcServerStreamRequestsCompletedDisplayName|개수|평균|완료된 스트림 요청 수|노드, 서비스, 메서드, 코드|
|GrpcServerUnaryRequestsReceived|예|GrpcServerUnaryRequestsReceivedDisplayName|개수|평균|받은 단항 요청 수|노드, 서비스, 메서드|
|IOReadBytes|예|IO 읽기 바이트|바이트|합계|IO 읽기 바이트|노드|
|IOWriteBytes|예|IO 쓰기 바이트|바이트|합계|IO 쓰기 바이트|노드|
|LedgerBlockchainHeight|예|LedgerBlockchainHeightDisplayName|개수|평균|블록에서 체인의 높이|노드, 채널|
|LedgerTransactionCount|예|LedgerTransactionCountDisplayName|개수|평균|처리된 트랜잭션 수|노드, 채널, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|예|LoggingEntriesCheckedDisplayName|개수|평균|활성 로깅 수준에 대해 확인된 로그 항목 수|노드, 수준|
|LoggingEntriesWritten|예|LoggingEntriesWrittenDisplayName|개수|평균|기록된 로그 항목 수|노드, 수준|
|MemoryLimit|예|메모리 제한|바이트|평균|메모리 제한|노드|
|MemoryUsage|예|메모리 사용량|바이트|평균|메모리 사용량|노드|
|MemoryUsagePercentageInDouble|예|메모리 사용 백분율|백분율|평균|메모리 사용 백분율|노드|
|PendingTransactions|예|보류 중인 트랜잭션|개수|평균|보류 중인 트랜잭션|노드|
|ProcessedBlocks|예|처리된 블록|개수|합계|처리된 블록|노드|
|ProcessedTransactions|예|처리된 트랜잭션|개수|합계|처리된 트랜잭션|노드|
|QueuedTransactions|예|지연 트랜잭션|개수|평균|지연 트랜잭션|노드|
|RequestHandled|예|처리된 요청|개수|합계|처리된 요청|노드|
|StorageUsage|예|스토리지 사용량|바이트|평균|스토리지 사용량|노드|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|RequestLatency|예|요청 대기 시간|밀리초|합계|서버에서 요청을 처리하는 데 걸린 시간|작업, 인증, 프로토콜, 데이터 센터|
|RequestsTraffic|예|요청 트래픽|백분율|개수|수행된 요청 수|작업, 인증, 프로토콜, StatusCode, StatusCodeClass, 데이터 센터|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|allcachehits|예|캐시 적중 수(인스턴스 기반)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allcachemisses|예|캐시 누락 수(인스턴스 기반)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allcacheRead|예|캐시 읽기(인스턴스 기반)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allcacheWrite|예|캐시 쓰기(인스턴스 기반)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allconnectedclients|예|연결된 클라이언트 수(인스턴스 기반)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allevictedkeys|예|제거된 키 수(인스턴스 기반)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allexpiredkeys|예|만료된 키 수(인스턴스 기반)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allgetcommands|예|가져온 수(인스턴스 기반)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|alloperationsPerSecond|예|초당 작업 수(인스턴스 기반)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allpercentprocessortime|예|CPU(인스턴스 기반)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allserverLoad|예|서버 부하(인스턴스 기반)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allsetcommands|예|설정한 수(인스턴스 기반)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|alltotalcommandsprocessed|예|총 작업 수(인스턴스 기반)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|alltotalkeys|예|총 키 수(인스턴스 기반)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allusedmemory|예|사용된 메모리(인스턴스 기반)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allusedmemorypercentage|예|사용된 메모리 백분율(인스턴스 기반)|백분율|최대|키/값 쌍에 사용되는 캐시 메모리의 백분율. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|allusedmemoryRss|예|사용된 메모리 RSS(인스턴스 기반)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, 포트, 기본|
|cachehits|예|캐시 적중|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cachehits0|예|캐시 적중(분할 0)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits1|예|캐시 적중(분할 1)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits2|예|캐시 적중(분할 2)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits3|예|캐시 적중(분할 3)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits4|예|캐시 적중(분할 4)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits5|예|캐시 적중(분할 5)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits6|예|캐시 적중(분할 6)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits7|예|캐시 적중(분할 7)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits8|예|캐시 적중(분할 8)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachehits9|예|캐시 적중(분할 9)|개수|합계|성공한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheLatency|예|캐시 대기 시간 마이크로초(미리 보기)|개수|평균|캐시에 대한 대기 시간(마이크로초). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cachemisses|예|캐시 누락|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cachemisses0|예|캐시 누락(분할 0)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses1|예|캐시 누락(분할 1)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses2|예|캐시 누락(분할 2)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses3|예|캐시 누락(분할 3)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses4|예|캐시 누락(분할 4)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses5|예|캐시 누락(분할 5)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses6|예|캐시 누락(분할 6)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses7|예|캐시 누락(분할 7)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses8|예|캐시 누락(분할 8)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemisses9|예|캐시 누락(분할 9)|개수|합계|실패한 키 조회의 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cachemissrate|예|캐시 누락률|백분율|cachemissrate|누락된 get 요청 백분율(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cacheRead|예|캐시 읽기|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cacheRead0|예|캐시 읽기(분할 0)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead1|예|캐시 읽기(분할 1)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead2|예|캐시 읽기(분할 2)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead3|예|캐시 읽기(분할 3)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead4|예|캐시 읽기(분할 4)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead5|예|캐시 읽기(분할 5)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead6|예|캐시 읽기(분할 6)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead7|예|캐시 읽기(분할 7)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead8|예|캐시 읽기(분할 8)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheRead9|예|캐시 읽기(분할 9)|초당 바이트 수|최대|캐시에서 읽은 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite|예|캐시 쓰기|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|cacheWrite0|예|캐시 쓰기(분할 0)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite1|예|캐시 쓰기(분할 1)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite2|예|캐시 쓰기(분할 2)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite3|예|캐시 쓰기(분할 3)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite4|예|캐시 쓰기(분할 4)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite5|예|캐시 쓰기(분할 5)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite6|예|캐시 쓰기(분할 6)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite7|예|캐시 쓰기(분할 7)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite8|예|캐시 쓰기(분할 8)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|cacheWrite9|예|캐시 쓰기(분할 9)|초당 바이트 수|최대|캐시에 쓰는 초당 메가바이트(MB/s) 단위의 데이터 양. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients|예|연결된 클라이언트|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|connectedclients0|예|연결된 클라이언트(분할 0)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients1|예|연결된 클라이언트(분할 1)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients2|예|연결된 클라이언트(분할 2)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients3|예|연결된 클라이언트(분할 3)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients4|예|연결된 클라이언트(분할 4)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients5|예|연결된 클라이언트(분할 5)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients6|예|연결된 클라이언트(분할 6)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients7|예|연결된 클라이언트(분할 7)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients8|예|연결된 클라이언트(분할 8)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|connectedclients9|예|연결된 클라이언트(분할 9)|개수|최대|캐시에 대한 클라이언트 연결 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|오류|예|오류|개수|최대|캐시에서 발생한 오류 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId, ErrorType|
|evictedkeys|예|제거된 키|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|evictedkeys0|예|제거된 키(분할 0)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys1|예|제거된 키(분할 1)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys2|예|제거된 키(분할 2)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys3|예|제거된 키(분할 3)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys4|예|제거된 키(분할 4)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys5|예|제거된 키(분할 5)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys6|예|제거된 키(분할 6)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys7|예|제거된 키(분할 7)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys8|예|제거된 키(분할 8)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|evictedkeys9|예|제거된 키(분할 9)|개수|합계|캐시에서 제거된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys|예|만료된 키|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|expiredkeys0|예|만료된 키(분할 0)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys1|예|만료된 키(분할 1)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys2|예|만료된 키(분할 2)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys3|예|만료된 키(분할 3)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys4|예|만료된 키(분할 4)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys5|예|만료된 키(분할 5)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys6|예|만료된 키(분할 6)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys7|예|만료된 키(분할 7)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys8|예|만료된 키(분할 8)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|expiredkeys9|예|만료된 키(분할 9)|개수|합계|캐시에서 만료된 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands|예|가져오기|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|getcommands0|예|가져오기(분할 0)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands1|예|가져오기(분할 1)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands2|예|가져오기(분할 2)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands3|예|가져오기(분할 3)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands4|예|가져오기(분할 4)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands5|예|가져오기(분할 5)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands6|예|가져오기(분할 6)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands7|예|가져오기(분할 7)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands8|예|가져오기(분할 8)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|getcommands9|예|가져오기(분할 9)|개수|합계|캐시에서의 가져오기 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond|예|초당 작업|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|operationsPerSecond0|예|초당 작업(분할 0)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond1|예|초당 작업(분할 1)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond2|예|초당 작업(분할 2)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond3|예|초당 작업(분할 3)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond4|예|초당 작업(분할 4)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond5|예|초당 작업(분할 5)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond6|예|초당 작업(분할 6)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond7|예|초당 작업(분할 7)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond8|예|초당 작업(분할 8)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|operationsPerSecond9|예|초당 작업(분할 9)|개수|최대|캐시에서 실행되는 초당 즉각적인 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime|예|CPU|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|percentProcessorTime0|예|CPU(분할 0)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime1|예|CPU(분할 1)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime2|예|CPU(분할 2)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime3|예|CPU(분할 3)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime4|예|CPU(분할 4)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime5|예|CPU(분할 5)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime6|예|CPU(분할 6)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime7|예|CPU(분할 7)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime8|예|CPU(분할 8)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|percentProcessorTime9|예|CPU(분할 9)|백분율|최대|Azure Redis Cache 서버 CPU 사용률(%). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad|예|서버 부하|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|serverLoad0|예|서버 부하(분할 0)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad1|예|서버 부하(분할 1)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad2|예|서버 부하(분할 2)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad3|예|서버 부하(분할 3)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad4|예|서버 부하(분할 4)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad5|예|서버 부하(분할 5)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad6|예|서버 부하(분할 6)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad7|예|서버 부하(분할 7)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad8|예|서버 부하(분할 8)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|serverLoad9|예|서버 부하(분할 9)|백분율|최대|Redis 서버가 작업을 처리하는 중이며 유휴 상태로 메시지를 대기하고 있지 않은 주기 비율입니다. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands|예|집합|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|setcommands0|예|설정(분할 0)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands1|예|집합(분할 1)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands2|예|설정(분할 2)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands3|예|설정(분할 3)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands4|예|설정(분할 4)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands5|예|설정(분할 5)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands6|예|설정(분할 6)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands7|예|설정(분할 7)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands8|예|설정(분할 8)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|setcommands9|예|설정(분할 9)|개수|합계|캐시에 대한 집합 작업 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed|예|총 작업|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|totalcommandsprocessed0|예|총 작업(분할 0)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed1|예|총 작업(분할 1)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed2|예|총 작업(분할 2)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed3|예|총 작업(분할 3)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed4|예|총 작업(분할 4)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed5|예|총 작업(분할 5)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed6|예|총 작업(분할 6)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed7|예|총 작업(분할 7)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed8|예|총 작업(분할 8)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalcommandsprocessed9|예|총 작업(분할 9)|개수|합계|캐시 서버가 처리한 총 명령 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys|예|전체 키|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|totalkeys0|예|총 키(분할 0)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys1|예|총 키(분할 1)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys2|예|총 키(분할 2)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys3|예|총 키(분할 3)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys4|예|총 키(분할 4)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys5|예|총 키(분할 5)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys6|예|총 키(분할 6)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys7|예|총 키(분할 7)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys8|예|총 키(분할 8)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|totalkeys9|예|총 키(분할 9)|개수|최대|캐시 내의 총 항목 수. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory|예|사용된 메모리|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|usedmemory0|예|사용된 메모리(분할 0)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory1|예|사용된 메모리(분할 1)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory2|예|사용된 메모리(분할 2)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory3|예|사용된 메모리(분할 3)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory4|예|사용된 메모리(분할 4)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory5|예|사용된 메모리(분할 5)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory6|예|사용된 메모리(분할 6)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory7|예|사용된 메모리(분할 7)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory8|예|사용된 메모리(분할 8)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemory9|예|사용된 메모리(분할 9)|바이트|최대|캐시의 키/값 쌍에 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemorypercentage|예|사용된 메모리 비율|백분율|최대|키/값 쌍에 사용되는 캐시 메모리의 백분율. 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|usedmemoryRss|예|사용된 메모리 RSS|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|ShardId|
|usedmemoryRss0|예|사용된 메모리 RSS(분할 0)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss1|예|사용된 메모리 RSS(분할 1)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss2|예|사용된 메모리 RSS(분할 2)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss3|예|사용된 메모리 RSS(분할 3)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss4|예|사용된 메모리 RSS(분할 4)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss5|예|사용된 메모리 RSS(분할 5)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss6|예|사용된 메모리 RSS(분할 6)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss7|예|사용된 메모리 RSS(분할 7)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss8|예|사용된 메모리 RSS(분할 8)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|
|usedmemoryRss9|예|사용된 메모리 RSS(분할 9)|바이트|최대|조각 및 메타데이터를 포함하여 사용된 캐시 메모리의 양(MB). 자세한 내용은 https://aka.ms/redis/metrics를 참조하세요.|차원 없음|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|cachehits|예|캐시 적중|개수|합계||차원 없음|
|cacheLatency|예|캐시 대기 시간 마이크로초(미리 보기)|개수|평균||InstanceId|
|cachemisses|예|캐시 누락|개수|합계||InstanceId|
|cacheRead|예|캐시 읽기|초당 바이트 수|최대||InstanceId|
|cacheWrite|예|캐시 쓰기|초당 바이트 수|최대||InstanceId|
|connectedclients|예|연결된 클라이언트|개수|최대||InstanceId|
|오류|예|오류|개수|최대||InstanceId, ErrorType|
|evictedkeys|예|제거된 키|개수|합계||차원 없음|
|expiredkeys|예|만료된 키|개수|합계||차원 없음|
|getcommands|예|가져오기|개수|합계||차원 없음|
|operationsPerSecond|예|초당 작업|개수|최대||차원 없음|
|percentProcessorTime|예|CPU|백분율|최대||InstanceId|
|serverLoad|예|서버 부하|백분율|최대||차원 없음|
|setcommands|예|집합|개수|합계||차원 없음|
|totalcommandsprocessed|예|총 작업|개수|합계||차원 없음|
|totalkeys|예|전체 키|개수|최대||차원 없음|
|usedmemory|예|사용된 메모리|바이트|최대||차원 없음|
|usedmemorypercentage|예|사용된 메모리 비율|백분율|최대||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|예|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ByteHitRatio|예|바이트 적중률|백분율|평균|캐시에서 제공하는 총 바이트 수를 총 응답 바이트 수와 비교한 비율|엔드포인트|
|OriginHealthPercentage|예|원본 상태 비율|백분율|평균|AFDX에서 백 엔드로 성공한 상태 프로브의 백분율|원본, OriginGroup|
|OriginLatency|예|원본 대기 시간|밀리초|평균|AFDX 에지가 백 엔드로 요청을 보낸 시점부터 AFDX가 밴 엔드로부터 마지막 응답 바이트를 받은 시점까지 얼마나 걸렸는지 계산하여 구하는 시간|원본, 엔드포인트|
|OriginRequestCount|예|원본 요청 수|개수|합계|AFDX에서 원본으로 보낸 요청 수|HttpStatus, HttpStatusGroup, 원본, 엔드포인트|
|Percentage4XX|예|4XX의 백분율|백분율|평균|응답 상태 코드가 4XX인 모든 클라이언트 요청의 백분율|엔드포인트, ClientRegion, ClientCountry|
|Percentage5XX|예|5XX의 백분율|백분율|평균|응답 상태 코드가 5XX인 모든 클라이언트 요청의 백분율|엔드포인트, ClientRegion, ClientCountry|
|RequestCount|예|요청 수|개수|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, 엔드포인트|
|RequestSize|예|요청 크기|바이트|합계|클라이언트에서 AFDX로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, 엔드포인트|
|ResponseSize|예|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, 엔드포인트|
|TotalLatency|예|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, 엔드포인트|
|WebApplicationFirewallRequestCount|예|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트/초|예|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|RoleInstanceId|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|RoleInstanceId|
|디스크 쓰기 바이트/초|예|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|RoleInstanceId|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|RoleInstanceId|
|네트워크 인|예|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|RoleInstanceId|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|RoleInstanceId|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트/초|예|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|차원 없음|
|디스크 쓰기 바이트/초|예|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|차원 없음|
|네트워크 인|예|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|차원 없음|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|차원 없음|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|차원 없음|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 엔드투엔드 대기 시간(밀리초). 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 대기 시간(밀리초). 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|예|사용된 용량|바이트|평균|계정 사용 용량|차원 없음|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|BlobCapacity|예|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트).|BlobType, 계층|
|BlobCount|예|Blob 수|개수|평균|스토리지 계정의 Blob service에 있는 Blob 수.|BlobType, 계층|
|ContainerCount|예|Blob 컨테이너 수|개수|평균|스토리지 계정의 Blob service에 있는 컨테이너 수.|차원 없음|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|IndexCapacity|예|인덱스 용량|바이트|평균|ADLS Gen2(계층 구조) 인덱스에서 사용하는 스토리지 크기(바이트)|차원 없음|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 엔드투엔드 대기 시간(밀리초). 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 대기 시간(밀리초). 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증, FileShare|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증, FileShare|
|FileCapacity|예|파일 용량|바이트|평균|스토리지 계정의 파일 서비스가 사용하는 스토리지의 양(바이트).|FileShare|
|FileCount|예|파일 수|개수|평균|스토리지 계정의 파일 서비스에 있는 파일 수.|FileShare|
|FileShareCount|예|파일 공유 수|개수|평균|스토리지 계정의 파일 서비스에 있는 파일 공유 수.|차원 없음|
|FileShareQuota|예|파일 공유 할당량 크기|바이트|평균|Azure Files 서비스에서 사용할 수 있는 스토리지 양(바이트)의 상한|FileShare|
|FileShareSnapshotCount|예|파일 공유 스냅샷 개수|개수|평균|스토리지 계정의 Files 서비스에서 공유에 있는 스냅샷 수.|FileShare|
|FileShareSnapshotSize|예|파일 공유 스냅샷 크기|바이트|평균|스토리지 계정의 File 서비스에서 스냅샷에 사용하는 스토리지 양(바이트).|FileShare|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증, FileShare|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 엔드투엔드 대기 시간(밀리초). 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증, FileShare|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 대기 시간(밀리초). 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증, FileShare|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, 인증, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|QueueCapacity|예|큐 용량|바이트|평균|스토리지 계정의 큐 서비스가 사용하는 스토리지의 양(바이트).|차원 없음|
|QueueCount|예|큐 수|개수|평균|스토리지 계정의 큐 서비스에 있는 큐 수.|차원 없음|
|QueueMessageCount|예|큐 메시지 수|개수|평균|스토리지 계정의 큐 서비스에 있는 대략적인 큐 메시지 수.|차원 없음|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 엔드투엔드 대기 시간(밀리초). 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 대기 시간(밀리초). 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 엔드투엔드 대기 시간(밀리초). 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 대기 시간(밀리초). 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|TableCapacity|예|테이블 용량|바이트|평균|스토리지 계정의 Table service가 사용하는 스토리지의 양(바이트).|차원 없음|
|TableCount|예|테이블 수|개수|평균|스토리지 계정의 Table service에 있는 테이블 수.|차원 없음|
|TableEntityCount|예|테이블 엔터티 수|개수|평균|스토리지 계정의 Table service에 있는 테이블 엔터티 수.|차원 없음|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcloudtesthostedpools"></a>Microsoft.Cloudtest/hostedpools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|Allocated|Yes|Allocated|개수|평균|할당된 리소스|PoolId, SKU, Images, ProviderName|
|AllocationDurationMs|Yes|AllocationDurationMs|밀리초|평균|요청을 할당하는 평균 시간(ms)|PoolId, Type, ResourceRequestType, Image|
|개수|Yes|개수|개수|개수|마지막 덤프의 요청 수|RequestType, Status, PoolId, Type, ErrorCode, FailureStage|
|NotReady|Yes|NotReady|개수|평균|사용할 준비가 되지 않은 리소스|PoolId, SKU, Images, ProviderName|
|PendingReimage|Yes|PendingReimage|개수|평균|이미지로 다시 설치 보류 중인 리소스|PoolId, SKU, Images, ProviderName|
|PendingReturn|Yes|PendingReturn|개수|평균|반환 보류 중인 리소스|PoolId, SKU, Images, ProviderName|
|프로비전됨|Yes|프로비전됨|개수|평균|프로비전되는 리소스|PoolId, SKU, Images, ProviderName|
|준비|예|준비|개수|평균|사용할 준비가 된 리소스|PoolId, SKU, Images, ProviderName|
|시작 중|Yes|시작 중|개수|평균|시작하는 리소스|PoolId, SKU, Images, ProviderName|
|합계|Yes|합계|개수|평균|총 리소스 수|PoolId, SKU, Images, ProviderName|


## <a name="microsoftcloudtestpools"></a>Microsoft.Cloudtest/pools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|Allocated|Yes|Allocated|개수|평균|할당된 리소스|PoolId, SKU, Images, ProviderName|
|AllocationDurationMs|Yes|AllocationDurationMs|밀리초|평균|요청을 할당하는 평균 시간(ms)|PoolId, Type, ResourceRequestType, Image|
|개수|Yes|개수|개수|개수|마지막 덤프의 요청 수|RequestType, Status, PoolId, Type, ErrorCode, FailureStage|
|NotReady|Yes|NotReady|개수|평균|사용할 준비가 되지 않은 리소스|PoolId, SKU, Images, ProviderName|
|PendingReimage|Yes|PendingReimage|개수|평균|이미지로 다시 설치 보류 중인 리소스|PoolId, SKU, Images, ProviderName|
|PendingReturn|Yes|PendingReturn|개수|평균|반환 보류 중인 리소스|PoolId, SKU, Images, ProviderName|
|프로비전됨|Yes|프로비전됨|개수|평균|프로비전되는 리소스|PoolId, SKU, Images, ProviderName|
|준비|예|준비|개수|평균|사용할 준비가 된 리소스|PoolId, SKU, Images, ProviderName|
|시작 중|Yes|시작 중|개수|평균|시작하는 리소스|PoolId, SKU, Images, ProviderName|
|합계|Yes|합계|개수|평균|총 리소스 수|PoolId, SKU, Images, ProviderName|


## <a name="microsoftclusterstornodes"></a>Microsoft.ClusterStor/nodes

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|TotalCapacityAvailable|No|TotalCapacityAvailable|바이트|평균|Lustre 파일 시스템에서 사용할 수 있는 총 용량|filesystem_name, category, system|
|TotalCapacityUsed|No|TotalCapacityUsed|바이트|평균|Lustre 파일 시스템에서 사용되는 총 용량|filesystem_name, category, system|
|TotalRead|No|TotalRead|초당 바이트 수|평균|초당 총 lustre 파일 시스템 읽기|filesystem_name, category, system|
|TotalWrite|No|TotalWrite|초당 바이트 수|평균|초당 총 lustre 파일 시스템 쓰기|filesystem_name, category, system|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AudioSecondsTranscribed|예|오디오 초 전사|개수|합계|기록된 시간(초)|ApiName, FeatureName, UsageChannel, 지역|
|AudioSecondsTranslated|예|오디오 초 변환|개수|합계|변환된 시간(초)|ApiName, FeatureName, UsageChannel, 지역|
|BlockedCalls|예|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|ApiName, OperationName, 지역|
|CharactersTrained|예|학습된 문자(사용되지 않습니다.)|개수|합계|학습된 총 문자 수|ApiName, OperationName, 지역|
|CharactersTranslated|예|번역된 문자(사용되지 않습니다.)|개수|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|ApiName, OperationName, 지역|
|ClientErrors|예|클라이언트 오류|개수|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|ComputerVisionTransactions|예|트랜잭션 Computer Vision|개수|합계|Computer Vision 트랜잭션 수|ApiName, FeatureName, UsageChannel, 지역|
|CustomVisionTrainingTime|예|Custom Vision 학습 시간|초|합계|Custom Vision 학습 시간|ApiName, FeatureName, UsageChannel, 지역|
|CustomVisionTransactions|예|Custom Vision 트랜잭션|개수|합계|Custom Vision 예측 트랜잭션 수|ApiName, FeatureName, UsageChannel, 지역|
|DataIn|예|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|DataOut|예|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|DocumentCharactersTranslated|예|번역된 문서 문자|개수|합계|문서 번역 요청의 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|DocumentCustomCharactersTranslated|예|번역된 문서 사용자 지정 문자|개수|합계|사용자 지정 문서 번역 요청의 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|FaceImagesTrained|예|Face Images Trained|개수|합계|학습된 이미지 수입니다. 트랜잭션당 학습된 이미지 1,000개|ApiName, FeatureName, UsageChannel, 지역|
|FacesStored|예|저장된 얼굴|개수|합계|저장된 얼굴 수, 비례 배분된 일별 얼굴 수입니다. 저장된 얼굴 수는 매일 보고됩니다.|ApiName, FeatureName, UsageChannel, 지역|
|FaceTransactions|예|Face Transactions|개수|합계|Face 서비스에 대한 API 호출 수|ApiName, FeatureName, UsageChannel, 지역|
|ImagesStored|예|저장된 이미지|개수|합계|저장된 Custom Vision 이미지 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|대기 시간|예|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|ApiName, OperationName, 지역|
|LearnedEvents|예|학습한 이벤트 수|개수|합계|학습한 이벤트 수|IsMatchBaseline, 모드, RunId|
|LUISSpeechRequests|예|LUIS 음성 요청|개수|합계|의도 이해 요청에 대한 LUIS 음성 수|ApiName, FeatureName, UsageChannel, 지역|
|LUISTextRequests|예|LUIS 텍스트 요청|개수|합계|LUIS 텍스트 요청 수|ApiName, FeatureName, UsageChannel, 지역|
|MatchedRewards|예|일치하는 보상 수|개수|합계|일치하는 보상 수|Mode, RunId|
|NumberofSpeakerProfiles|예|화자 프로필 수|개수|합계|등록된 화자 프로필 수입니다. 시간별 비례 배분.|ApiName, FeatureName, UsageChannel, 지역|
|ObservedRewards|예|관찰된 보상 수|개수|합계|관찰된 보상 수|Mode, RunId|
|ProcessedCharacters|예|처리된 문자 수|개수|합계|몰입형 리더에서 처리 되는 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|ProcessedHealthTextRecords|예|처리 된 상태 텍스트 레코드|개수|합계|처리 된 상태 텍스트 레코드 수|ApiName, FeatureName, UsageChannel, 지역|
|Processeages|예|처리 된 이미지|개수|합계|처리 된 이미지 수|ApiName, FeatureName, UsageChannel, 지역|
|ProcessedPages|예|처리 된 페이지|개수|합계|처리 된 페이지 수|ApiName, FeatureName, UsageChannel, 지역|
|ProcessedTextRecords|예|처리된 텍스트 레코드 수|개수|합계|텍스트 레코드 수|ApiName, FeatureName, UsageChannel, 지역|
|ServerErrors|예|서버 오류|개수|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|SpeakerRecognitionTransactions|예|Speaker Recognition 트랜잭션|개수|합계|스피커 인식 트랜잭션 수|ApiName, FeatureName, UsageChannel, 지역|
|SpeechModelHostingHours|예|음성 모델 호스팅 시간|개수|합계|음성 모델 호스팅 시간 수|ApiName, FeatureName, UsageChannel, 지역|
|SpeechSessionDuration|예|음성 세션 기간 (사용 되지 않음)|초|합계|음성 세션의 총 기간(초)입니다.|ApiName, OperationName, 지역|
|SuccessfulCalls|예|성공한 호출|개수|합계|성공한 호출 수입니다.|ApiName, OperationName, 지역|
|SynthesizedCharacters|예|합성 문자|개수|합계|문자 수|ApiName, FeatureName, UsageChannel, 지역|
|TextCharactersTranslated|예|텍스트 문자 변환|개수|합계|들어오는 텍스트 번역 요청의 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|TextCustomCharactersTranslated|예|텍스트 사용자 지정 문자 변환|개수|합계|들어오는 사용자 지정 텍스트 번역 요청의 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|TextTrainedCharacters|예|텍스트 학습 문자|개수|합계|텍스트 번역을 사용 하 여 학습 된 문자 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|TotalCalls|예|총 호출|개수|합계|총 호출 수.|ApiName, OperationName, 지역|
|TotalErrors|예|총 오류|개수|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|ApiName, OperationName, 지역|
|TotalTokenCalls|예|총 토큰 호출|개수|합계|총 토큰 호출 수입니다.|ApiName, OperationName, 지역|
|TotalTransactions|예|총 트랜잭션 (사용 되지 않음)|개수|합계|총 트랜잭션 수|차원 없음|
|VoiceModelHostingHours|예|음성 모델 호스팅 시간|개수|합계|시간(시)|ApiName, FeatureName, UsageChannel, 지역|
|VoiceModelTrainingMinutes|예|음성 모델 학습(분)|개수|합계|시간(분)|ApiName, FeatureName, UsageChannel, 지역|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|예|인증 API 요청 수|개수|개수|Communication Services 인증 엔드포인트에 대한 모든 요청 수|작업, StatusCode, StatusCodeClass|
|APIRequestChat|예|채팅 API 요청 수|개수|개수|Communication Services 채팅 엔드포인트에 대한 모든 요청 수|작업, StatusCode, StatusCodeClass|
|APIRequestSMS|예|SMS API 요청 수|개수|개수|Communication Services SMS 엔드포인트에 대한 모든 요청 수|Operation, StatusCode, StatusCodeClass, ErrorCode|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능한 메모리(바이트)|Yes|사용 가능한 메모리(바이트)(미리보기)|바이트|평균|가상 머신에서 프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리의 양(바이트)|RoleInstanceId, RoleId|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|RoleInstanceId, RoleId|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|RoleInstanceId, RoleId|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|RoleInstanceId, RoleId|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|RoleInstanceId, RoleId|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|RoleInstanceId, RoleId|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|RoleInstanceId, RoleId|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능한 메모리(바이트)|Yes|사용 가능한 메모리(바이트)(미리보기)|바이트|평균|가상 머신에서 프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리의 양(바이트)|RoleInstanceId, RoleId|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|RoleInstanceId, RoleId|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|RoleInstanceId, RoleId|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|RoleInstanceId, RoleId|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|RoleInstanceId, RoleId|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|RoleInstanceId, RoleId|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|RoleInstanceId, RoleId|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|복합 디스크 읽기 바이트/초|예|디스크 읽기 바이트/초(미리 보기)|바이트|평균|모니터링 기간 중에 디스크에서 읽은 초당 바이트 수. 이 메트릭은 현재 미리 보기이며 일반 공급 전에 변경될 수 있습니다.|차원 없음|
|복합 디스크 읽기 작업/초|예|디스크 읽기 작업/(미리 보기)|바이트|평균|모니터링 기간 중에 디스크에서 수행된 읽기 IO 수. 이 메트릭은 현재 미리 보기이며 일반 공급 전에 변경될 수 있습니다.|차원 없음|
|복합 디스크 쓰기 바이트/초|예|디스크 쓰기 바이트/초(미리 보기)|바이트|평균|모니터링 기간 중에 디스크에 쓴 초당 바이트 수. 이 메트릭은 현재 미리 보기이며 일반 공급 전에 변경될 수 있습니다.|차원 없음|
|복합 디스크 쓰기 작업/초|예|디스크 쓰기 작업/초(미리 보기)|바이트|평균|모니터링 기간 중에 디스크에서 수행된 쓰기 IO 수. 이 메트릭은 현재 미리 보기이며 일반 공급 전에 변경될 수 있습니다.|차원 없음|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능한 메모리(바이트)|Yes|사용 가능한 메모리(바이트)(미리보기)|바이트|평균|가상 머신에서 프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리의 양(바이트)|차원 없음|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용한 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|소비한 데이터 디스크 대역폭 백분율|예|소비한 데이터 디스크 대역폭 백분율|백분율|평균|분당 소비한 데이터 디스크 대역폭 백분율|LUN|
|소비한 데이터 디스크 IOPS 백분율|예|소비한 데이터 디스크 IOPS 백분율|백분율|평균|분당 소비한 데이터 디스크 I/O 백분율|LUN|
|데이터 디스크 최대 버스트 대역폭|예|데이터 디스크 최대 버스트 대역폭|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 초당 바이트 처리량|LUN|
|데이터 디스크 최대 버스트 IOPS|예|데이터 디스크 최대 버스트 IOPS|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 대상 대역폭|예|데이터 디스크 대상 대역폭|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN|
|데이터 디스크 대상 IOPS|예|데이터 디스크 대상 IOPS|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN|
|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 대역폭 크레딧의 백분율|LUN|
|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 I/O 크레딧의 백분율|LUN|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|LUN|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수(VM으로 들어가는 트래픽)|차원 없음|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름(VM으로 들어가는 트래픽)의 최대 생성 속도|차원 없음|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한 청구 가능 바이트 수(들어오는 트래픽)(사용되지 않음)|차원 없음|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 발신한 청구 가능 바이트 수(나가는 트래픽)(사용되지 않음)|차원 없음|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|소비한 OS 디스크 대역폭 백분율|예|소비한 OS 디스크 대역폭 백분율|백분율|평균|분당 소비한 운영 체제 디스크 대역폭의 백분율|LUN|
|소비한 OS 디스크 IOPS 백분율|예|소비한 OS 디스크 IOPS 백분율|백분율|평균|분당 소비한 운영 체제 디스크 I/O의 백분율|LUN|
|OS 디스크 최대 버스트 대역폭|예|OS 디스크 최대 버스트 대역폭|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 초당 최대 바이트 처리량|LUN|
|OS 디스크 최대 버스트 IOPS|예|OS 디스크 최대 버스트 IOPS|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|차원 없음|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|차원 없음|
|OS 디스크 대상 대역폭|예|OS 디스크 대상 대역폭|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN|
|OS 디스크 대상 IOPS|예|OS 디스크 대상 IOPS|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN|
|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 대역폭 크레딧의 백분율|LUN|
|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|예|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 I/O 크레딧의 백분율|LUN|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|차원 없음|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|차원 없음|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 현재 흐름 수(VM에서 나가는 트래픽)|차원 없음|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름(VM에서 나가는 트래픽)의 최대 생성 속도|차원 없음|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|차원 없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|차원 없음|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|차원 없음|
|소비한 VM 캐시 대역폭 백분율|예|소비한 VM 캐시 대역폭 백분율|백분율|평균|VM에서 소비한 캐시 디스크 대역폭의 백분율|차원 없음|
|소비한 VM 캐시 IOPS 백분율|예|소비한 VM 캐시 IOPS 백분율|백분율|평균|VM에서 소비한 캐시 디스크 IOPS의 백분율|차원 없음|
|VM 캐시되지 않은 대역폭 사용량 백분율|예|VM 캐시되지 않은 대역폭 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 대역폭의 백분율|차원 없음|
|VM 캐시되지 않은 IOPS 사용량 백분율|예|VM 캐시되지 않은 IOPS 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 IOPS의 백분율|차원 없음|
|VmAvailabilityMetric|예|VM 가용성 메트릭 (미리 보기)|개수|평균|시간이 지남에 따라 가상 머신의 가용성을 측정 합니다. 참고:이 메트릭은 데이터 품질 및 일관성 개선을 위한 우선 순위를 지정 하는 시점에 소수의 고객 에게만 미리 봅니다. 데이터 표준을 개선 하면서이 기능을 단계적으로 fleetwide 수 있습니다.|차원 없음|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능한 메모리(바이트)|Yes|사용 가능한 메모리(바이트)(미리보기)|바이트|평균|가상 머신에서 프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리의 양(바이트)|VMName|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용한 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|소비한 데이터 디스크 대역폭 백분율|예|소비한 데이터 디스크 대역폭 백분율|백분율|평균|분당 소비한 데이터 디스크 대역폭 백분율|LUN, VMName|
|소비한 데이터 디스크 IOPS 백분율|예|소비한 데이터 디스크 IOPS 백분율|백분율|평균|분당 소비한 데이터 디스크 I/O 백분율|LUN, VMName|
|데이터 디스크 최대 버스트 대역폭|예|데이터 디스크 최대 버스트 대역폭|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 초당 바이트 처리량|LUN, VMName|
|데이터 디스크 최대 버스트 IOPS|예|데이터 디스크 최대 버스트 IOPS|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN, VMName|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN, VMName|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|LUN, VMName|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|LUN, VMName|
|데이터 디스크 대상 대역폭|예|데이터 디스크 대상 대역폭|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN, VMName|
|데이터 디스크 대상 IOPS|예|데이터 디스크 대상 IOPS|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN, VMName|
|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 대역폭 크레딧의 백분율|LUN, VMName|
|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 I/O 크레딧의 백분율|LUN, VMName|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|LUN, VMName|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|LUN, VMName|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|VMName|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|VMName|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|VMName|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|VMName|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수(VM으로 들어가는 트래픽)|VMName|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름(VM으로 들어가는 트래픽)의 최대 생성 속도|VMName|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한 청구 가능 바이트 수(들어오는 트래픽)(사용되지 않음)|VMName|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|VMName|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 발신한 청구 가능 바이트 수(나가는 트래픽)(사용되지 않음)|VMName|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|VMName|
|소비한 OS 디스크 대역폭 백분율|예|소비한 OS 디스크 대역폭 백분율|백분율|평균|분당 소비한 운영 체제 디스크 대역폭의 백분율|LUN, VMName|
|소비한 OS 디스크 IOPS 백분율|예|소비한 OS 디스크 IOPS 백분율|백분율|평균|분당 소비한 운영 체제 디스크 I/O의 백분율|LUN, VMName|
|OS 디스크 최대 버스트 대역폭|예|OS 디스크 최대 버스트 대역폭|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 초당 최대 바이트 처리량|LUN, VMName|
|OS 디스크 최대 버스트 IOPS|예|OS 디스크 최대 버스트 IOPS|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN, VMName|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|VMName|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|VMName|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|VMName|
|OS 디스크 대상 대역폭|예|OS 디스크 대상 대역폭|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN, VMName|
|OS 디스크 대상 IOPS|예|OS 디스크 대상 IOPS|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN, VMName|
|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 대역폭 크레딧의 백분율|LUN, VMName|
|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|예|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 I/O 크레딧의 백분율|LUN, VMName|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|VMName|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|VMName|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 현재 흐름 수(VM에서 나가는 트래픽)|VMName|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름(VM에서 나가는 트래픽)의 최대 생성 속도|VMName|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|VMName|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN, VMName|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN, VMName|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|VMName|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|VMName|
|소비한 VM 캐시 대역폭 백분율|예|소비한 VM 캐시 대역폭 백분율|백분율|평균|VM에서 소비한 캐시 디스크 대역폭의 백분율|VMName|
|소비한 VM 캐시 IOPS 백분율|예|소비한 VM 캐시 IOPS 백분율|백분율|평균|VM에서 소비한 캐시 디스크 IOPS의 백분율|VMName|
|VM 캐시되지 않은 대역폭 사용량 백분율|예|VM 캐시되지 않은 대역폭 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 대역폭의 백분율|VMName|
|VM 캐시되지 않은 IOPS 사용량 백분율|예|VM 캐시되지 않은 IOPS 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 IOPS의 백분율|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능한 메모리(바이트)|Yes|사용 가능한 메모리(바이트)(미리보기)|바이트|평균|가상 머신에서 프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리의 양(바이트)|차원 없음|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용한 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수. B-시리즈 버스트 가능 VM에서만 사용 가능|차원 없음|
|소비한 데이터 디스크 대역폭 백분율|예|소비한 데이터 디스크 대역폭 백분율|백분율|평균|분당 소비한 데이터 디스크 대역폭 백분율|LUN|
|소비한 데이터 디스크 IOPS 백분율|예|소비한 데이터 디스크 IOPS 백분율|백분율|평균|분당 소비한 데이터 디스크 I/O 백분율|LUN|
|데이터 디스크 최대 버스트 대역폭|예|데이터 디스크 최대 버스트 대역폭|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 초당 바이트 처리량|LUN|
|데이터 디스크 최대 버스트 IOPS|예|데이터 디스크 최대 버스트 IOPS|개수|평균|데이터 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에서 읽은 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 대상 대역폭|예|데이터 디스크 대상 대역폭|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN|
|데이터 디스크 대상 IOPS|예|데이터 디스크 대상 IOPS|개수|평균|데이터 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN|
|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 대역폭 크레딧의 백분율|LUN|
|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|예|데이터 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 데이터 디스크 버스트 I/O 크레딧의 백분율|LUN|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초|초당 바이트 수|평균|모니터링 기간 중 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초|초당 개수|평균|모니터링 기간 중 단일 디스크에서 IOPS 쓰기|LUN|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 중 디스크에서 읽은 바이트 수|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 중 디스크에 쓴 바이트 수|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수(VM으로 들어가는 트래픽)|차원 없음|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름(VM으로 들어가는 트래픽)의 최대 생성 속도|차원 없음|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한 청구 가능 바이트 수(들어오는 트래픽)(사용되지 않음)|차원 없음|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 발신한 청구 가능 바이트 수(나가는 트래픽)(사용되지 않음)|차원 없음|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|소비한 OS 디스크 대역폭 백분율|예|소비한 OS 디스크 대역폭 백분율|백분율|평균|분당 소비한 운영 체제 디스크 대역폭의 백분율|LUN|
|소비한 OS 디스크 IOPS 백분율|예|소비한 OS 디스크 IOPS 백분율|백분율|평균|분당 소비한 운영 체제 디스크 I/O의 백분율|LUN|
|OS 디스크 최대 버스트 대역폭|예|OS 디스크 최대 버스트 대역폭|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 초당 최대 바이트 처리량|LUN|
|OS 디스크 최대 버스트 IOPS|예|OS 디스크 최대 버스트 IOPS|개수|평균|OS 디스크가 버스팅을 사용하여 달성할 수 있는 최대 IOPS|LUN|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 읽은 바이트/초|차원 없음|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 읽기|차원 없음|
|OS 디스크 대상 대역폭|예|OS 디스크 대상 대역폭|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 초당 바이트 처리량|LUN|
|OS 디스크 대상 IOPS|예|OS 디스크 대상 IOPS|개수|평균|OS 디스크가 버스팅 없이 달성할 수 있는 기준 IOPS|LUN|
|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|예|OS 디스크에서 사용한 버스트 BPS 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 대역폭 크레딧의 백분율|LUN|
|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|예|OS 디스크에서 사용한 버스트 IO 크레딧 백분율|백분율|평균|지금까지 사용한 OS 디스크 버스트 I/O 크레딧의 백분율|LUN|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초|초당 바이트 수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에 쓴 바이트/초|차원 없음|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초|초당 개수|평균|OS 디스크의 모니터링 기간 중 단일 디스크에서 IOPS 쓰기|차원 없음|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃바운드 흐름은 아웃바운드 방향의 현재 흐름 수(VM에서 나가는 트래픽)|차원 없음|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃바운드 흐름(VM에서 나가는 트래픽)의 최대 생성 속도|차원 없음|
|CPU 사용률|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|차원 없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|차원 없음|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|차원 없음|
|소비한 VM 캐시 대역폭 백분율|예|소비한 VM 캐시 대역폭 백분율|백분율|평균|VM에서 소비한 캐시 디스크 대역폭의 백분율|차원 없음|
|소비한 VM 캐시 IOPS 백분율|예|소비한 VM 캐시 IOPS 백분율|백분율|평균|VM에서 소비한 캐시 디스크 IOPS의 백분율|차원 없음|
|VM 캐시되지 않은 대역폭 사용량 백분율|예|VM 캐시되지 않은 대역폭 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 대역폭의 백분율|차원 없음|
|VM 캐시되지 않은 IOPS 사용량 백분율|예|VM 캐시되지 않은 IOPS 사용량 백분율|백분율|평균|VM에서 소비한 캐시되지 않은 디스크 IOPS의 백분율|차원 없음|


## <a name="microsoftconnectedvehicleplatformaccounts"></a>Microsoft.ConnectedVehicle/platformAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ClaimsProviderRequestLatency|예|클레임 요청 실행 시간|밀리초|평균|고객 클레임 공급자 엔드포인트에 대한 요청의 평균 실행 시간(밀리초)|VehicleId, DeviceName|
|ClaimsProviderRequests|예|클레임 공급자 요청|개수|합계|클레임 공급자에 대한 요청 수|VehicleId, DeviceName|
|ConnectionServiceRequestRuntime|예|차량 연결 서비스 요청 실행 시간|밀리초|평균|차량 연결 요청 실행 시간 평균(밀리초)|VehicleId, DeviceName|
|ConnectionServiceRequests|예|차량 연결 서비스 요청|개수|합계|차량 연결 요청의 총 수|VehicleId, DeviceName|
|ProvisionerServiceRequestRuntime|예|차량 프로비저닝 실행 시간|밀리초|평균|차량 프로비저닝 요청의 평균 실행 시간(밀리초)|VehicleId, DeviceName|
|ProvisionerServiceRequests|예|차량 프로비저닝 서비스 요청|개수|합계|차량 프로비저닝 요청의 총 수|VehicleId, DeviceName|
|StateStoreReadRequestLatency|예|상태 저장소 읽기 실행 시간|밀리초|평균|상태 저장소 읽기 요청 실행 시간 평균(밀리초)|VehicleId, DeviceName|
|StateStoreReadRequests|예|상태 저장소 읽기 요청|개수|합계|상태 저장소에 대한 읽기 요청 수|VehicleId, DeviceName|
|StateStoreWriteRequestLatency|예|상태 저장소 쓰기 실행 시간|밀리초|평균|상태 저장소 쓰기 요청 실행 시간 평균(밀리초)|VehicleId, DeviceName|
|StateStoreWriteRequests|예|상태 저장소 쓰기 요청|개수|합계|상태 저장소에 대한 쓰기 요청 수|VehicleId, DeviceName|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CpuUsage|예|CPU 사용량|개수|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|예|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|
|NetworkBytesReceivedPerSecond|예|초당 수신된 네트워크 바이트|바이트|평균|초당 수신된 네트워크 바이트입니다.|차원 없음|
|NetworkBytesTransmittedPerSecond|예|초당 전송된 네트워크 바이트|바이트|평균|초당 전송된 네트워크 바이트입니다.|차원 없음|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|예|AgentPool CPU 시간|초|합계|AgentPool CPU 시간(초)|차원 없음|
|RunDuration|예|실행 기간|밀리초|합계|실행 기간(밀리초)|차원 없음|
|StorageUsed|예|스토리지 사용됨|바이트|평균|컨테이너 레지스트리에서 사용되는 스토리지 양 레지스트리 계정의 경우 레지스트리 내의 모든 리포지토리에서 사용하는 용량의 합계입니다. 각 리포지토리의 공유 계층, 매니페스트 파일 및 복제본에서 사용하는 용량의 합계입니다.|지리적 위치|
|SuccessfulPullCount|예|성공한 끌어오기 횟수|개수|합계|성공한 이미지 끌어오기 횟수|차원 없음|
|SuccessfulPushCount|예|성공한 밀어넣기 횟수|개수|합계|성공한 이미지 밀어넣기 횟수|차원 없음|
|TotalPullCount|예|총 끌어오기 횟수|개수|합계|총 이미지 끌어오기 횟수|차원 없음|
|TotalPushCount|예|총 밀어넣기 횟수|개수|합계|총 이미지 밀어넣기 횟수|차원 없음|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|예|실행 중 요청 수|개수|평균|마지막 1초 동안 요청 종류별로 apiserver에서 현재 사용되는 최대 실행 중 요청 수|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|예|클러스터 상태|개수|평균|클러스터 Autoscaler가 클러스터에서 작업을 수행하는지 여부를 결정|차원 없음|
|cluster_autoscaler_scale_down_in_cooldown|예|스케일 다운 쿨다운|개수|평균|스케일 다운이 쿨다운 중인지 확인 - 이 기간에는 노드가 제거되지 않음|차원 없음|
|cluster_autoscaler_unneeded_nodes_count|예|불필요한 노드|개수|평균|클러스터 자동 크기 조정기는 이러한 노드를 삭제 후보로 표시하며 결과적으로는 삭제됩니다|차원 없음|
|cluster_autoscaler_unschedulable_pods_count|예|예약되지 않은 Pod|개수|평균|클러스터에서 현재 예약할 수 없는 Pod 수|차원 없음|
|kube_node_status_allocatable_cpu_cores|예|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|개수|평균|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|차원 없음|
|kube_node_status_allocatable_memory_bytes|예|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|평균|관리 클러스터에서 사용 가능한 총 메모리 양|차원 없음|
|kube_node_status_condition|예|다양한 노드 조건에 대한 상태|개수|평균|다양한 노드 조건에 대한 상태|조건, 상태, 상태2, 노드|
|kube_pod_status_phase|예|단계별 Pod 수|개수|평균|단계별 Pod 수|단계, 네임스페이스, Pod|
|kube_pod_status_ready|예|준비 상태인 Pod 수|개수|평균|준비 상태인 Pod 수|네임스페이스, Pod, 조건|
|node_cpu_usage_millicores|예|CPU 사용량 밀리코어|밀리코어|평균|클러스터 전체에서 집계한 CPU 사용률 측정값(밀리코어)|노드, nodepool|
|node_cpu_usage_percentage|예|CPU 사용 백분율|백분율|평균|클러스터 전체에서 집계한 평균 CPU 사용률 측정값(백분율)|노드, nodepool|
|node_disk_usage_bytes|예|사용되는 디스크 바이트|바이트|평균|디바이스에서 사용하는 디스크 공간(바이트)|노드, nodepool, 디바이스|
|node_disk_usage_percentage|예|사용되는 디스크 백분율|백분율|평균|디바이스에서 사용하는 디스크 공간(백분율)|노드, nodepool, 디바이스|
|node_memory_rss_bytes|예|메모리 RSS 바이트|바이트|평균|사용되는 컨테이너 RSS 메모리(바이트)|노드, nodepool|
|node_memory_rss_percentage|예|메모리 RSS 백분율|백분율|평균|사용되는 컨테이너 RSS 메모리(백분율)|노드, nodepool|
|node_memory_working_set_bytes|예|메모리 작업 세트 바이트|바이트|평균|사용되는 컨테이너 작업 세트 메모리(바이트)|노드, nodepool|
|node_memory_working_set_percentage|예|메모리 작업 세트 백분율|백분율|평균|사용되는 컨테이너 작업 세트 메모리(백분율)|노드, nodepool|
|node_network_in_bytes|예|네트워크 입력 바이트|바이트|평균|네트워크 수신 바이트|노드, nodepool|
|node_network_out_bytes|예|네트워크 출력 바이트|바이트|평균|네트워크 전송 바이트|노드, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|FailedRequests|예|실패한 요청|개수|합계|사용자 지정 리소스 공급자에 사용할 수 있는 로그를 가져옵니다.|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|예|성공한 요청|개수|합계|사용자 지정 공급자가 수행한 성공한 요청 수|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AvailableCapacity|예|사용 가능한 용량|바이트|평균|보고 기간 동안 사용 가능한 용량(바이트)|차원 없음|
|BytesUploadedToCloud|예|업로드된 클라우드 바이트(디바이스)|바이트|평균|보고 기간 동안 디바이스에서 Azure로 업로드된 총 바이트 수|차원 없음|
|BytesUploadedToCloudPerShare|예|업로드된 클라우드 바이트(공유)|바이트|평균|보고 기간 동안 공유에서 Azure로 업로드된 총 바이트 수|공유|
|CloudReadThroughput|예|클라우드 다운로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 다운로드된 클라우드 다운로드 처리량|차원 없음|
|CloudReadThroughputPerShare|예|클라우드 다운로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로 다운로드된 처리량|공유|
|CloudUploadThroughput|예|클라우드 업로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 업로드된 클라우드 업로드 처리량|차원 없음|
|CloudUploadThroughputPerShare|예|클라우드 업로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로 업로드된 처리량|공유|
|HyperVMemoryUtilization|예|Edge 컴퓨팅 - 메모리 사용량|백분율|평균|사용 중인 RAM 크기|InstanceName|
|HyperVVirtualProcessorUtilization|예|Edge 컴퓨팅 - 백분율 CPU|백분율|평균|CPU 사용률|InstanceName|
|NICReadThroughput|예|읽기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대한 보고 기간에 디바이스의 네트워크 인터페이스에서 발생하는 읽기 처리량|InstanceName|
|NICWriteThroughput|예|쓰기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대한 보고 기간에 디바이스의 네트워크 인터페이스에서 발생하는 쓰기 처리량|InstanceName|
|TotalCapacity|예|총 용량|바이트|평균|보고 기간 동안 디바이스의 총 용량(바이트).|차원 없음|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DataAssetCount|예|생성된 데이터 자산|개수|최대|생성된 데이터 자산 수|DataAssetName|
|PipelineCount|예|생성된 파이프라인|개수|최대|생성된 파이프라인 수|PipelineName|
|ProposalCount|예|생성된 제안|개수|최대|생성된 제안 수|ProposalName|
|ScriptCount|예|생성된 스크립트|개수|최대|생성된 스크립트 수|스크립트 이름|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|FailedRuns|예|실패한 실행|개수|합계||pipelineName, activityName|
|SuccessfulRuns|예|성공한 실행|개수|합계||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|예|취소된 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|예|실패한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|예|성공한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|예|총 팩터리 크기(GB 단위)|개수|최대||차원 없음|
|IntegrationRuntimeAvailableMemory|예|통합 런타임 사용 가능한 메모리|바이트|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|예|통합 런타임 사용 가능 노드 수|개수|평균||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|예|Integration runtime 큐 지속 시간|초|평균||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|예|통합 런타임 CPU 사용률|백분율|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|예|Integration runtime 큐 길이|개수|평균||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|예|허용되는 최대 팩터리 크기(GB 단위)|개수|최대||차원 없음|
|MaxAllowedResourceCount|예|허용되는 최대 엔터티 수|개수|최대||차원 없음|
|PipelineCancelledRuns|예|취소된 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|PipelineElapsedTimeRuns|예|경과된 시간 파이프라인 실행 메트릭|개수|합계||RunId, 이름|
|PipelineFailedRuns|예|실패한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|PipelineSucceededRuns|예|성공한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|ResourceCount|예|총 엔터티 수|개수|최대||차원 없음|
|SSISIntegrationRuntimeStartCancel|예|취소된 SSIS 통합 런타임 시작 메트릭|개수|합계||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|예|실패한 SSIS 통합 런타임 시작 메트릭|개수|합계||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|예|성공한 SSIS 통합 런타임 시작 메트릭|개수|합계||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|예|중단된 SSIS 통합 런타임 중지 메트릭|개수|합계||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|예|성공한 SSIS 통합 런타임 중지 메트릭|개수|합계||IntegrationRuntimeName|
|SSISPackageExecutionCancel|예|취소된 SSIS 패키지 실행 메트릭|개수|합계||IntegrationRuntimeName|
|SSISPackageExecutionFailed|예|실패한 SSIS 패키지 실행 메트릭|개수|합계||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|예|성공한 SSIS 패키지 실행 메트릭|개수|합계||IntegrationRuntimeName|
|TriggerCancelledRuns|예|취소된 트리거 실행 메트릭|개수|합계||Name, FailureType|
|TriggerFailedRuns|예|실패한 트리거 실행 메트릭|개수|합계||Name, FailureType|
|TriggerSucceededRuns|예|성공한 트리거 실행 메트릭|개수|합계||Name, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|예|취소된 AU 시간|초|합계|취소된 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedFailure|예|실패한 AU 시간|초|합계|실패한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobAUEndedSuccess|예|성공한 AU 시간|초|합계|성공한 작업에 대한 총 AU 시간입니다.|차원 없음|
|JobEndedCancelled|예|취소된 작업|개수|합계|취소된 작업 수입니다.|차원 없음|
|JobEndedFailure|예|실패한 작업|개수|합계|실패한 작업 수입니다.|차원 없음|
|JobEndedSuccess|예|성공한 작업|개수|합계|성공한 작업의 수입니다.|차원 없음|
|JobStage|예|스테이지의 작업|개수|합계|각 단계의 작업 수|차원 없음|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DataRead|예|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|차원 없음|
|DataWritten|예|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|차원 없음|
|ReadRequests|예|읽기 요청|개수|합계|계정에 데이터 읽기 요청 수.|차원 없음|
|TotalStorage|예|총 스토리지|바이트|최대|계정에 저장된 총 데이터 양.|차원 없음|
|WriteRequests|예|쓰기 요청|개수|합계|계정에 데이터 쓰기 요청 수.|차원 없음|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|예|받은 공유 실패 스냅샷|개수|개수|계정의 받은 공유 실패 스냅샷 수|차원 없음|
|FailedShareSynchronizations|예|보낸 공유 실패 스냅샷|개수|개수|계정의 보낸 공유 실패 스냅샷 수|차원 없음|
|ShareCount|예|보낸 공유|개수|최대|계정의 보낸 공유 수|ShareName|
|ShareSubscriptionCount|예|받은 공유|개수|최대|계정의 받은 공유 수|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|예|받은 공유 성공 스냅샷|개수|개수|계정의 받은 공유 성공 스냅샷 수|차원 없음|
|SucceededShareSynchronizations|예|보낸 공유 성공 스냅샷|개수|개수|계정의 보낸 공유 성공 스냅샷 수|차원 없음|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|seconds_behind_master|예|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|aborted_connections|예|중단된 연결|개수|합계|중단된 연결|차원 없음|
|active_connections|예|활성 연결 수|개수|최대|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|최대|사용된 백업 스토리지|차원 없음|
|cpu_credits_consumed|예|사용된 CPU 크레딧|개수|최대|사용된 CPU 크레딧|차원 없음|
|cpu_credits_remaining|예|남은 CPU 크레딧|개수|최대|남은 CPU 크레딧|차원 없음|
|cpu_percent|예|호스트 CPU 백분율|백분율|최대|호스트 CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|최대|IO 백분율|차원 없음|
|memory_percent|예|호스트 메모리 백분율|백분율|최대|호스트 메모리 백분율|차원 없음|
|network_bytes_egress|예|호스트 네트워크 출력|바이트|합계|호스트 네트워크 송신(바이트)|차원 없음|
|network_bytes_ingress|예|호스트 네트워크 입력|바이트|합계|호스트 네트워크 수신(바이트)|차원 없음|
|쿼리|예|쿼리|개수|합계|쿼리|차원 없음|
|replication_lag|예|복제 지연 시간(초)|초|최대|복제 지연 시간(초)|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 백분율|백분율|최대|스토리지 백분율|차원 없음|
|storage_used|예|사용한 스토리지|바이트|최대|사용한 스토리지|차원 없음|
|total_connections|예|총 연결 수|개수|합계|총 연결 수|차원 없음|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|seconds_behind_master|예|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|connections_succeeded|예|성공한 연결 수|개수|합계|성공한 연결 수|차원 없음|
|cpu_credits_consumed|예|사용된 CPU 크레딧|개수|평균|데이터베이스 서버에서 사용한 총 크레딧 수|차원 없음|
|cpu_credits_remaining|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|disk_queue_depth|예|디스크 큐 깊이|개수|평균|데이터 디스크에 대한 미해결 I/O 작업 수|차원 없음|
|IOPS|예|IOPS|개수|평균|초당 IO 작업 수|차원 없음|
|maximum_used_transactionIDs|예|사용되는 최대 트랜잭션 ID 수|개수|평균|사용되는 최대 트랜잭션 ID 수|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|read_iops|예|읽기 IOPS|개수|평균|초당 데이터 디스크 I/O 읽기 작업 수|차원 없음|
|read_throughput|예|읽기 처리량 바이트/초|개수|평균|모니터링 기간 동안 데이터 디스크에서 초당 읽은 바이트 수|차원 없음|
|storage_free|예|사용 가능한 스토리지|바이트|평균|사용 가능한 스토리지|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|
|txlogs_storage_used|예|사용되는 트랜잭션 로그 스토리지|바이트|평균|사용되는 트랜잭션 로그 스토리지|차원 없음|
|write_iops|예|쓰기 IOPS|개수|평균|초당 데이터 디스크 I/O 쓰기 작업 수|차원 없음|
|write_throughput|예|쓰기 처리량 바이트/초|개수|평균|모니터링 기간 동안 데이터 디스크에 초당 쓴 바이트 수|차원 없음|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|ServerName|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|ServerName|
|IOPS|예|IOPS|개수|평균|초당 IO 작업 수|ServerName|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|ServerName|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|ServerName|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|ServerName|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|ServerName|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|pg_replica_log_delay_in_bytes|예|복제본 간 최대 지연 시간|바이트|최대|가장 많이 지연되는 복제본의 지연(바이트)|차원 없음|
|pg_replica_log_delay_in_seconds|예|복제본 지연 시간|초|최대|복제본 지연(초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|IOPS|예|IOPS|개수|평균|초당 IO 작업 수|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|예|요청된 사용 요금|백분율|평균|요청된 사용 요금|차원 없음|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|예|중단된 C2D 메시지|개수|합계|디바이스에서 중단한 클라우드-디바이스 메시지 수|차원 없음|
|c2d.commands.egress.complete.success|예|C2D 메시지 배달 완료|개수|합계|디바이스에서 성공적으로 완료한 클라우드-디바이스 메시지 전송 수|차원 없음|
|c2d.commands.egress.reject.success|예|거부된 C2D 메시지|개수|합계|디바이스에서 거부한 클라우드-디바이스 메시지 수|차원 없음|
|c2d.methods.failure|예|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.requestSize|예|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.responseSize|예|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.success|예|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.twin.read.failure|예|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.size|예|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d.twin.read.success|예|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.update.failure|예|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.size|예|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d.twin.update.success|예|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|C2DMessagesExpired|예|만료된 C2D 메시지|개수|합계|만료된 클라우드-디바이스 메시지 수|차원 없음|
|구성|예|구성 메트릭|개수|합계|구성 작업에 대한 메트릭|차원 없음|
|connectedDeviceCount|예|연결된 디바이스|개수|평균|IoT 허브에 연결된 디바이스 수|차원 없음|
|d2c.endpoints.egress.builtIn.events|예|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.eventHubs|예|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusQueues|예|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusTopics|예|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage|예|라우팅: 스토리지에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.blobs|예|라우팅: 스토리지에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.bytes|예|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|차원 없음|
|d2c.endpoints.latency.builtIn.events|예|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.eventHubs|예|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusQueues|예|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusTopics|예|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.storage|예|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 스토리지 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.telemetry.egress.dropped|예|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|차원 없음|
|d2c.telemetry.egress.fallback|예|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|차원 없음|
|d2c.telemetry.egress.invalid|예|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|차원 없음|
|d2c.telemetry.egress.orphaned|예|라우팅: 분리된 원격 분석 메시지 |개수|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |차원 없음|
|d2c.telemetry.egress.success|예|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|차원 없음|
|d2c.telemetry.ingress.allProtocol|예|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.sendThrottle|예|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|차원 없음|
|d2c.telemetry.ingress.success|예|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.twin.read.failure|예|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.size|예|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c.twin.read.success|예|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.update.failure|예|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.size|예|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|d2c.twin.update.success|예|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|dailyMessageQuotaUsed|예|사용된 전체 메시지 수|개수|최대|현재 사용되는 전체 메시지 수|차원 없음|
|deviceDataUsage|예|총 디바이스 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|deviceDataUsageV2|예|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|devices.connectedDevices.allProtocol|예|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 디바이스 수|차원 없음|
|devices.totalDevices|예|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|차원 없음|
|EventGridDeliveries|예|Event Grid 전송|개수|합계|Event Grid에 게시된 IoT Hub 이벤트 수. 성공 및 실패한 요청 수에 대한 결과 차원을 사용합니다. EventType 차원은 이벤트 유형(https://aka.ms/ioteventgrid) )을 표시합니다.|결과, EventType|
|EventGridLatency|예|Event Grid 대기 시간|밀리초|평균|Iot Hub 이벤트가 생성된 시간부터 Event Grid에 게시된 시간까지의 평균 대기 시간(밀리초). 이 숫자는 모든 이벤트 유형의 평균입니다. 특정 이벤트 유형의 대기 시간을 확인하려면 EventType 차원을 사용합니다.|EventType|
|jobs.cancelJob.failure|예|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.success|예|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.completed|예|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.failure|예|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.success|예|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.failure|예|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.success|예|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.failed|예|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|차원 없음|
|jobs.listJobs.failure|예|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.listJobs.success|예|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.failure|예|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.success|예|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|RoutingDataSizeInBytesDelivered|예|라우팅 전송 메시지 크기(바이트)(미리 보기)|바이트|합계|IoT 허브에서 엔드포인트로 전송된 메시지의 총 크기(바이트). EndpointName 및 EndpointType 차원을 사용하여 다른 엔드포인트에 전송된 메시지 크기(바이트)를 볼 수 있습니다. 메시지가 여러 엔드포인트에 전송되는 경우와 메시지가 동일한 엔드포인트에 여러 번 전송되는 경우를 포함하여 메시지가 전송될 때마다 이 메트릭 값이 증가합니다.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|예|라우팅 전송(미리 보기)|개수|합계|IoT Hub가 라우팅을 사용하여 모든 엔드포인트에 메시지를 전송하려고 시도한 횟수. 성공 또는 실패한 시도의 횟수를 확인하려면 결과 차원을 사용합니다. 잘못됨, 삭제됨, 분리됨 등과 같은 실패 원인을 확인하려면 FailureReasonCategory 차원을 사용합니다. 또한 EndpointName 및 EndpointType 차원을 사용하여 여러 엔드포인트에 전송된 메시지 수를 파악할 수 있습니다. 메시지가 여러 엔드포인트에 전송되는 경우와 메시지가 동일한 엔드포인트에 여러 번 전송되는 경우를 포함하여 메시지 전송 시도가 있을 때마다 이 메트릭 값이 증가합니다.|EndpointType, EndpointName, FailureReasonCategory, 결과, RoutingSource|
|RoutingDeliveryLatency|예|라우팅 전송 대기 시간(미리 보기)|밀리초|평균|IoT Hub에서 메시지를 수신하는 시간과 엔드포인트에서 원격 분석 메시지를 수신하는 시간 사이의 평균 대기 시간(밀리초). EndpointName 및 EndpointType 차원을 사용하여 여러 엔드포인트의 대기 시간을 파악할 수 있습니다.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|예|요청된 사용 요금|백분율|평균|요청된 사용 요금|차원 없음|
|totalDeviceCount|예|총 디바이스|개수|평균|IoT 허브에 등록된 디바이스 수|차원 없음|
|twinQueries.failure|예|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.resultSize|예|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|twinQueries.success|예|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|예|중단된 C2D 메시지|개수|합계|디바이스에서 중단한 클라우드-디바이스 메시지 수|차원 없음|
|c2d.commands.egress.complete.success|예|C2D 메시지 배달 완료|개수|합계|디바이스에서 성공적으로 완료한 클라우드-디바이스 메시지 전송 수|차원 없음|
|c2d.commands.egress.reject.success|예|거부된 C2D 메시지|개수|합계|디바이스에서 거부한 클라우드-디바이스 메시지 수|차원 없음|
|c2d.methods.failure|예|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.requestSize|예|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.responseSize|예|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.success|예|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.twin.read.failure|예|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.size|예|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d.twin.read.success|예|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.update.failure|예|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.size|예|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d.twin.update.success|예|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|C2DMessagesExpired|예|만료된 C2D 메시지|개수|합계|만료된 클라우드-디바이스 메시지 수|차원 없음|
|구성|예|구성 메트릭|개수|합계|구성 작업에 대한 메트릭|차원 없음|
|connectedDeviceCount|예|연결된 디바이스|개수|평균|IoT 허브에 연결된 디바이스 수|차원 없음|
|d2c.endpoints.egress.builtIn.events|예|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.eventHubs|예|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusQueues|예|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusTopics|예|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage|예|라우팅: 스토리지에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.blobs|예|라우팅: 스토리지에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.bytes|예|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|차원 없음|
|d2c.endpoints.latency.builtIn.events|예|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.eventHubs|예|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusQueues|예|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusTopics|예|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.storage|예|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 스토리지 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.telemetry.egress.dropped|예|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|차원 없음|
|d2c.telemetry.egress.fallback|예|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|차원 없음|
|d2c.telemetry.egress.invalid|예|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|차원 없음|
|d2c.telemetry.egress.orphaned|예|라우팅: 분리된 원격 분석 메시지 |개수|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |차원 없음|
|d2c.telemetry.egress.success|예|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|차원 없음|
|d2c.telemetry.ingress.allProtocol|예|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.sendThrottle|예|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|차원 없음|
|d2c.telemetry.ingress.success|예|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.twin.read.failure|예|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.size|예|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c.twin.read.success|예|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.update.failure|예|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.size|예|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|d2c.twin.update.success|예|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|dailyMessageQuotaUsed|예|사용된 전체 메시지 수|개수|최대|현재 사용되는 전체 메시지 수|차원 없음|
|deviceDataUsage|예|총 디바이스 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|deviceDataUsageV2|예|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|devices.connectedDevices.allProtocol|예|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 디바이스 수|차원 없음|
|devices.totalDevices|예|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|차원 없음|
|EventGridDeliveries|예|Event Grid 전송|개수|합계|Event Grid에 게시된 IoT Hub 이벤트 수. 성공 및 실패한 요청 수에 대한 결과 차원을 사용합니다. EventType 차원은 이벤트 유형(https://aka.ms/ioteventgrid) )을 표시합니다.|결과, EventType|
|EventGridLatency|예|Event Grid 대기 시간|밀리초|평균|Iot Hub 이벤트가 생성된 시간부터 Event Grid에 게시된 시간까지의 평균 대기 시간(밀리초). 이 숫자는 모든 이벤트 유형의 평균입니다. 특정 이벤트 유형의 대기 시간을 확인하려면 EventType 차원을 사용합니다.|EventType|
|jobs.cancelJob.failure|예|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.success|예|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.completed|예|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.failure|예|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.success|예|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.failure|예|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.success|예|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.failed|예|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|차원 없음|
|jobs.listJobs.failure|예|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.listJobs.success|예|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.failure|예|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.success|예|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|RoutingDataSizeInBytesDelivered|예|라우팅 전송 메시지 크기(바이트)(미리 보기)|바이트|합계|IoT 허브에서 엔드포인트로 전송된 메시지의 총 크기(바이트). EndpointName 및 EndpointType 차원을 사용하여 다른 엔드포인트에 전송된 메시지 크기(바이트)를 볼 수 있습니다. 메시지가 여러 엔드포인트에 전송되는 경우와 메시지가 동일한 엔드포인트에 여러 번 전송되는 경우를 포함하여 메시지가 전송될 때마다 이 메트릭 값이 증가합니다.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|예|라우팅 전송(미리 보기)|개수|합계|IoT Hub가 라우팅을 사용하여 모든 엔드포인트에 메시지를 전송하려고 시도한 횟수. 성공 또는 실패한 시도의 횟수를 확인하려면 결과 차원을 사용합니다. 잘못됨, 삭제됨, 분리됨 등과 같은 실패 원인을 확인하려면 FailureReasonCategory 차원을 사용합니다. 또한 EndpointName 및 EndpointType 차원을 사용하여 여러 엔드포인트에 전송된 메시지 수를 파악할 수 있습니다. 메시지가 여러 엔드포인트에 전송되는 경우와 메시지가 동일한 엔드포인트에 여러 번 전송되는 경우를 포함하여 메시지 전송 시도가 있을 때마다 이 메트릭 값이 증가합니다.|EndpointType, EndpointName, FailureReasonCategory, 결과, RoutingSource|
|RoutingDeliveryLatency|예|라우팅 전송 대기 시간(미리 보기)|밀리초|평균|IoT Hub에서 메시지를 수신하는 시간과 엔드포인트에서 원격 분석 메시지를 수신하는 시간 사이의 평균 대기 시간(밀리초). EndpointName 및 EndpointType 차원을 사용하여 여러 엔드포인트의 대기 시간을 파악할 수 있습니다.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|예|총 디바이스|개수|평균|IoT 허브에 등록된 디바이스 수|차원 없음|
|twinQueries.failure|예|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.resultSize|예|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|twinQueries.success|예|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AttestationAttempts|예|증명 시도|개수|합계|디바이스 증명 시도 수|ProvisioningServiceName, 상태, 프로토콜|
|DeviceAssignments|예|할당된 디바이스|개수|합계|IoT Hub에 할당된 디바이스 수|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|예|등록 시도|개수|합계|디바이스 등록 시도 수|ProvisioningServiceName, IotHubName, 상태|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ApiRequests|예|API 요청|개수|합계|Digital Twins 쌍 읽기, 쓰기, 삭제 및 쿼리 작업에 대해 수행된 API 요청의 수|작업, 인증, 프로토콜, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|예|API 요청 실패율|백분율|평균|서비스가 인스턴스에 대해 수신하는 API 요청 중 Digital Twins 읽기, 쓰기, 삭제 및 쿼리 작업에 대한 내부 오류(500) 응답 코드를 반환하는 요청의 백분율입니다.|작업, 인증, 프로토콜|
|ApiRequestsLatency|예|API 요청 대기 시간|밀리초|평균|API 요청의 응답 시간. 즉, Azure Digital Twins가 요청을 받은 시간부터 서비스가 Digital Twins 읽기, 쓰기, 삭제 및 쿼리 작업에 대한 성공/실패 결과를 보낼 때까지 걸리는 시간입니다.|작업, 인증, 프로토콜, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|예|청구 API 작업|개수|합계|Azure Digital Twins 서비스를 대상으로 수행된 모든 API 요청 수에 대한 청구 메트릭입니다.|MeterId|
|BillingMessagesProcessed|예|처리된 청구 메시지|개수|합계|Azure Digital Twins에서 외부 엔드포인트로 전송된 메시지 수에 대한 청구 메트릭입니다.|MeterId|
|BillingQueryUnits|예|청구 쿼리 단위|개수|합계|쿼리 실행에 사용된 서비스 리소스 사용량을 내부적으로 계산하여 측정한 쿼리 단위 수|MeterId|
|IngressEvents|예|수신 이벤트|개수|합계|Azure Digital Twins로 들어오는 원격 분석 이벤트의 수|결과|
|IngressEventsFailureRate|예|수신 이벤트 실패율|백분율|평균|서비스에서 내부 오류(500) 응답 코드를 반환하는 수신 원격 분석 이벤트의 백분율|차원 없음|
|IngressEventsLatency|예|수신 이벤트 대기 시간|밀리초|평균|이벤트가 도착하는 시점부터 Azure Digital Twins가 이벤트를 보낼 준비가 완료되는 시점까지 걸리는 시간. 이 시점에서 서비스는 성공/실패 결과를 전송합니다.|결과|
|ModelCount|예|모델 수|개수|합계|Azure Digital Twins 인스턴스의 총 모델 수. 이 메트릭을 사용하여 인스턴스당 허용되는 최대 모델 수에 대한 서비스 제한에 근접하고 있는지 확인할 수 있습니다.|차원 없음|
|라우팅|예|라우팅된 메시지|개수|합계|Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스로 라우팅된 메시지 수|EndpointType, 결과|
|RoutingFailureRate|예|라우팅 실패율|백분율|평균|Azure Digital Twins에서 Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스로 라우팅될 때 오류가 발생하는 이벤트의 백분율입니다.|EndpointType|
|RoutingLatency|예|라우팅 대기 시간|밀리초|평균|이벤트가 Azure Digital Twins에서 라우팅되는 시점부터 Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스에 게시되는 시점까지 경과한 시간|EndpointType, 결과|
|TwinCount|예|트윈 수|개수|합계|Azure Digital Twins 인스턴스의 총 트윈 수. 이 메트릭을 사용하여 인스턴스당 허용되는 최대 트윈 수에 대한 서비스 제한에 근접하고 있는지 확인할 수 있습니다.|차원 없음|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/DatabaseAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AddRegion|예|추가된 지역|개수|개수|추가된 지역|지역|
|AutoscaleMaxThroughput|예|자동 스케일링 최대 처리량|개수|최대|자동 스케일링 최대 처리량|DatabaseName, CollectionName|
|AvailableStorage|예|(사용되지 않음) 사용 가능한 스토리지|바이트|합계|"사용 가능한 스토리지"는 2023년 9월 말에 Azure Monitor에서 제거됩니다. 이제 Cosmos DB 컬렉션 스토리지 크기는 제한되지 않습니다. 유일한 제한 사항으로 각 논리 파티션 키의 스토리지 크기가 20GB로 제한됩니다. 진단 로그에서 PartitionKeyStatistics를 사용하도록 설정하여 상위 파티션 키에 사용되는 스토리지 사용량을 파악할 수 있습니다. Cosmos DB 스토리지 할당량에 대한 자세한 내용은 https://docs.microsoft.com/azure/cosmos-db/concepts-limits 문서를 참조하세요. 사용 중단 후, 사용되지 않는 메트릭에 여전히 정의된 나머지 경고 규칙은 사용 중단 날짜 이후에 자동으로 비활성화됩니다.|CollectionName, DatabaseName, 지역|
|CassandraConnectionClosures|예|Cassandra 연결 차단|개수|합계|종료된 Cassandra 연결 수. 1분 단위로 보고됩니다.|APIType, Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|예|Cassandra 커넥터 평균 ReplicationLatency|밀리초|평균|Cassandra 커넥터 평균 ReplicationLatency|차원 없음|
|CassandraConnectorReplicationHealthStatus|예|Cassandra 커넥터 복제 상태|개수|개수|Cassandra 커넥터 복제 상태|NotStarted, ReplicationInProgress, 오류|
|CassandraKeyspaceCreate|예|생성된 Cassandra 키스페이스 수|개수|개수|생성된 Cassandra 키스페이스 수|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraKeyspaceDelete|예|삭제된 Cassandra 키스페이스 수|개수|개수|삭제된 Cassandra 키스페이스 수|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|예|업데이트된 Cassandra 키스페이스 처리량|개수|개수|업데이트된 Cassandra 키스페이스 처리량|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|예|업데이트된 Cassandra 키스페이스|개수|개수|업데이트된 Cassandra 키스페이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraRequestCharges|예|Cassandra 요청 요금|개수|합계|Cassandra 요청에 사용된 RU|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|예|Cassandra 요청|개수|개수|수행된 Cassandra 요청 수|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|예|생성된 Cassandra 테이블|개수|개수|생성된 Cassandra 테이블|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CassandraTableDelete|예|삭제된 Cassandra 테이블|개수|개수|삭제된 Cassandra 테이블|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|예|업데이트된 Cassandra 테이블 처리량|개수|개수|업데이트된 Cassandra 테이블 처리량|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|예|업데이트된 Cassandra 테이블|개수|개수|업데이트된 Cassandra 테이블|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|CreateAccount|예|생성된 계정|개수|개수|생성된 계정|차원 없음|
|DataUsage|예|데이터 사용량|바이트|합계|5분 단위로 보고되는 총 데이터 사용량|CollectionName, DatabaseName, 지역|
|DedicatedGatewayAverageCPUUsage|아니요|DedicatedGatewayAverageCPUUsage|백분율|평균|전용 게이트웨이 인스턴스의 평균 CPU 사용량|Region, MetricType|
|DedicatedGatewayAverageMemoryUsage|아니요|DedicatedGatewayAverageMemoryUsage|바이트|평균|전용 게이트웨이 인스턴스의 평균 메모리 사용량(요청 라우팅 및 데이터 캐싱 둘 다에 사용)|지역|
|DedicatedGatewayMaximumCPUUsage|아니요|DedicatedGatewayMaximumCPUUsage|백분율|평균|전용 게이트웨이 인스턴스의 평균 최대 CPU 사용량|Region, MetricType|
|DedicatedGatewayRequests|예|DedicatedGatewayRequests|개수|개수|전용 게이트웨이의 요청|DatabaseName, CollectionName, CacheExercised, OperationName, 지역|
|DeleteAccount|예|삭제된 계정|개수|개수|삭제된 계정|차원 없음|
|DocumentCount|예|문서 수|개수|합계|5분, 1시간, 1일 단위로 보고된 총 문서 수|CollectionName, DatabaseName, 지역|
|DocumentQuota|예|문서 할당량|바이트|합계|5분 단위로 보고되는 총 스토리지 할당량|CollectionName, DatabaseName, 지역|
|GremlinDatabaseCreate|예|생성된 Gremlin 데이터베이스|개수|개수|생성된 Gremlin 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinDatabaseDelete|예|삭제된 Gremlin 데이터베이스|개수|개수|삭제된 Gremlin 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|예|업데이트된 Gremlin 데이터베이스 처리량|개수|개수|업데이트된 Gremlin 데이터베이스 처리량|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|예|업데이트된 Gremlin 데이터베이스|개수|개수|업데이트된 Gremlin 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinGraphCreate|예|생성된 Gremlin 그래프|개수|개수|생성된 Gremlin 그래프|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|GremlinGraphDelete|예|삭제된 Gremlin 그래프|개수|개수|삭제된 Gremlin 그래프|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|예|업데이트된 Gremlin Graph 처리량|개수|개수|업데이트된 Gremlin Graph 처리량|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|예|업데이트된 Gremlin 그래프|개수|개수|업데이트된 Gremlin 그래프|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|IndexUsage|예|인덱스 사용량|바이트|합계|5분 단위로 보고되는 총 인덱스 사용량|CollectionName, DatabaseName, 지역|
|IntegratedCacheEvictedEntriesSize|예|IntegratedCacheEvictedEntriesSize|바이트|평균|통합 캐시에서 제거된 항목의 크기|지역|
|IntegratedCacheItemExpirationCount|아니요|IntegratedCacheItemExpirationCount|개수|평균|TTL 만료로 인해 통합 캐시에서 제거된 항목 수|Region, CacheEntryType|
|IntegratedCacheItemHitRate|아니요|IntegratedCacheItemHitRate|백분율|평균|최종 일관성을 적용하여 통합 캐시를 사용한 지점 읽기 수를 전용 게이트웨이를 통해 라우팅된 지점 읽기 수로 나눈 값|Region, CacheEntryType|
|IntegratedCacheQueryExpirationCount|아니요|IntegratedCacheQueryExpirationCount|개수|평균|TTL 만료로 인해 통합 캐시에서 제거된 쿼리 수|Region, CacheEntryType|
|IntegratedCacheQueryHitRate|아니요|IntegratedCacheQueryHitRate|백분율|평균|최종 일관성을 적용하여 통합 캐시를 사용한 쿼리 수를 전용 게이트웨이를 통해 라우팅된 쿼리 수로 나눈 값|Region, CacheEntryType|
|MetadataRequests|예|메타데이터 요청|개수|개수|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|DatabaseName, CollectionName, Region, StatusCode, Role|
|MongoCollectionCreate|예|생성된 Mongo 컬렉션|개수|개수|생성된 Mongo 컬렉션|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoCollectionDelete|예|삭제된 Mongo 컬렉션|개수|개수|삭제된 Mongo 컬렉션|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|예|업데이트된 Mongo 컬렉션 처리량|개수|개수|업데이트된 Mongo 컬렉션 처리량|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|예|업데이트된 Mongo 컬렉션|개수|개수|업데이트된 Mongo 컬렉션|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoDatabaseDelete|예|삭제된 Mongo 데이터베이스|개수|개수|삭제된 Mongo 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|예|업데이트된 Mongo 데이터베이스 처리량|개수|개수|업데이트된 Mongo 데이터베이스 처리량|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseCreate|예|생성된 Mongo 데이터베이스|개수|개수|생성된 Mongo 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoDBDatabaseUpdate|예|업데이트된 Mongo 데이터베이스|개수|개수|업데이트된 Mongo 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|MongoRequestCharge|예|Mongo 요청 요금|개수|합계|사용된 Mongo 요청 단위|DatabaseName, CollectionName, 지역, CommandName, ErrorCode, 상태|
|MongoRequests|예|Mongo 요청|개수|개수|생성된 Mongo 요청 수|DatabaseName, CollectionName, 지역, CommandName, ErrorCode, 상태|
|NormalizedRUConsumption|예|정규화된 RU 사용량|백분율|최대|분당 최대 RU 사용량 백분율|CollectionName, DatabaseName, 지역, PartitionKeyRangeId|
|ProvisionedThroughput|예|프로비전된 처리량|개수|최대|프로비전된 처리량|DatabaseName, CollectionName|
|RegionFailover|예|장애 조치(failover)된 지역|개수|개수|장애 조치(failover)된 지역|차원 없음|
|RemoveRegion|예|제거된 지역|개수|개수|제거된 지역|지역|
|ReplicationLatency|예|P99 복제 대기 시간|밀리초|평균|지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간|SourceRegion, TargetRegion|
|ServerSideLatency|예|서버 쪽 대기 시간|밀리초|평균|서버 쪽 대기 시간|DatabaseName, CollectionName, 지역, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|예|서비스 가용성|백분율|평균|1시간, 1일 또는 1개월 단위의 계정 요청 가용성|차원 없음|
|SqlContainerCreate|예|생성된 Sql 컨테이너|개수|개수|생성된 Sql 컨테이너|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlContainerDelete|예|삭제된 Sql 컨테이너|개수|개수|삭제된 Sql 컨테이너|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|예|업데이트된 Sql 컨테이너 처리량|개수|개수|업데이트된 Sql 컨테이너 처리량|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|예|업데이트된 Sql 컨테이너|개수|개수|업데이트된 Sql 컨테이너|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlDatabaseCreate|예|생성된 Sql 데이터베이스|개수|개수|생성된 Sql 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|SqlDatabaseDelete|예|삭제된 Sql 데이터베이스|개수|개수|삭제된 Sql 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|예|업데이트된 Sql 데이터베이스 처리량|개수|개수|업데이트된 Sql 데이터베이스 처리량|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|예|업데이트된 Sql 데이터베이스|개수|개수|업데이트된 Sql 데이터베이스|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TableTableCreate|예|생성된 AzureTable 테이블|개수|개수|생성된 AzureTable 테이블|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TableTableDelete|예|삭제된 AzureTable 테이블|개수|개수|삭제된 AzureTable 테이블|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|예|업데이트된 AzureTable 테이블 처리량|개수|개수|업데이트된 AzureTable 테이블 처리량|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|예|업데이트된 AzureTable 테이블|개수|개수|업데이트된 AzureTable 테이블|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest, OperationType|
|TotalRequests|예|총 요청 수|개수|개수|요청 수|DatabaseName, CollectionName, 지역, StatusCode, OperationType, 상태|
|TotalRequestUnits|예|총 요청 단위|개수|합계|사용된 요청 단위|DatabaseName, CollectionName, 지역, StatusCode, OperationType, 상태|
|UpdateAccountKeys|예|업데이트된 계정 키|개수|개수|업데이트된 계정 키|KeyType|
|UpdateAccountNetworkSettings|예|업데이트된 계정 네트워크 설정|개수|개수|업데이트된 계정 네트워크 설정|차원 없음|
|UpdateAccountReplicationSettings|예|업데이트된 계정 복제 설정|개수|개수|업데이트된 계정 복제 설정|차원 없음|
|UpdateDiagnosticsSettings|예|업데이트된 계정 진단 설정|개수|개수|업데이트된 계정 진단 설정|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, 오류, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|토픽, ErrorType, 오류|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|항목|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|Error, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|차원 없음|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|차원 없음|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|차원 없음|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|예|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|차원 없음|
|AvailableMemory|예|사용 가능한 메모리|백분율|최대|이벤트 허브 클러스터에 사용할 수 있는 메모리. 총 메모리의 백분율로 표시합니다.|역할|
|CaptureBacklog|예|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|차원 없음|
|CapturedBytes|예|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|차원 없음|
|CapturedMessages|예|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|차원 없음|
|ConnectionsClosed|예|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|차원 없음|
|ConnectionsOpened|예|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|차원 없음|
|CPU|예|CPU|백분율|최대|이벤트 허브 클러스터에 대한 CPU 사용률(백분율)|역할|
|IncomingBytes|예|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|차원 없음|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|차원 없음|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|차원 없음|
|OutgoingBytes|예|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|차원 없음|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|차원 없음|
|QuotaExceededErrors|예|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|OperationResult|
|ServerErrors|예|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|OperationResult|
|크기|예|크기|바이트|평균|EventHub 크기(바이트)|역할|
|SuccessfulRequests|예|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|OperationResult|
|ThrottledRequests|예|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|OperationResult|
|UserErrors|예|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|예|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|차원 없음|
|CaptureBacklog|예|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|EntityName|
|CapturedBytes|예|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|EntityName|
|CapturedMessages|예|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|EntityName|
|ConnectionsClosed|예|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|EntityName|
|ConnectionsOpened|예|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|EntityName|
|EHABL|예|백로그 메시지 보관(사용되지 않음)|개수|합계|네임스페이스의 백로그에 들어 있는 Event Hub 보관 메시지(사용되지 않음)|차원 없음|
|EHAMBS|예|메시지 보관 처리량(사용되지 않음)|바이트|합계|네임스페이스의 Event Hub 보관 메시지 처리량(사용되지 않음)|차원 없음|
|EHAMSGS|예|메시지 보관(사용되지 않음)|개수|합계|네임스페이스의 Event Hub 보관 메시지 수(사용되지 않음)|차원 없음|
|EHINBYTES|예|들어오는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 대한 Event Hub 수신 메시지 처리량(사용되지 않음)|차원 없음|
|EHINMBS|예|들어오는 바이트(구식)(사용되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 들어오는 바이트 메트릭을 사용하세요.|차원 없음|
|EHINMSGS|예|들어오는 메시지(사용되지 않음)|개수|합계|네임스페이스에 대한 총 수신 메시지 수(사용되지 않음)|차원 없음|
|EHOUTBYTES|예|보내는 바이트(사용되지 않음)|바이트|합계|네임스페이스에 대한 Event Hub 발신 메시지 처리량(사용되지 않음)|차원 없음|
|EHOUTMBS|예|나가는 바이트(구식)(사용되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 나가는 바이트 메트릭을 사용하세요.|차원 없음|
|EHOUTMSGS|예|보내는 메시지(사용되지 않음)|개수|합계|네임스페이스에 대한 총 발신 메시지 수(사용되지 않음)|차원 없음|
|FAILREQ|예|실패한 요청(사용되지 않음)|개수|합계|네임스페이스에 대한 총 실패한 요청 수(사용되지 않음)|차원 없음|
|IncomingBytes|예|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|EntityName|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|EntityName|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|EntityName|
|INMSGS|예|들어오는 메시지(구식)(사용되지 않음)|개수|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 들어오는 메시지 메트릭을 사용하세요.|차원 없음|
|INREQS|예|들어오는 요청(사용되지 않음)|개수|합계|네임스페이스에 대한 총 들어오는 전송 요청 수(사용되지 않음)|차원 없음|
|INTERR|예|내부 서버 오류(사용되지 않음)|개수|합계|네임스페이스에 대한 총 내부 서버 오류 수(사용되지 않음)|차원 없음|
|MISCERR|예|기타 오류(사용 되지 않음)|개수|합계|네임스페이스에 대한 총 실패한 요청 수(사용되지 않음)|차원 없음|
|OutgoingBytes|예|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|EntityName|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|EntityName|
|OUTMSGS|예|나가는 메시지(구식)(사용되지 않음)|개수|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 보내는 메시지 메트릭을 사용하세요.|차원 없음|
|QuotaExceededErrors|예|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|EntityName, OperationResult|
|ServerErrors|예|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|EntityName, OperationResult|
|크기|예|크기|바이트|평균|EventHub 크기(바이트)|EntityName|
|SuccessfulRequests|예|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|EntityName, OperationResult|
|SUCCREQ|예|성공한 요청(사용되지 않음)|개수|합계|네임스페이스에 대한 총 성공한 요청 수(사용되지 않음)|차원 없음|
|SVRBSY|예|서버 작업 중 오류(사용되지 않음)|개수|합계|네임스페이스에 대한 총 서버 작업 중 오류 수(사용되지 않음)|차원 없음|
|ThrottledRequests|예|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|EntityName, OperationResult|
|UserErrors|예|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|예|분류된 게이트웨이 요청|개수|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|예|게이트웨이 요청|개수|합계|게이트웨이 요청 수|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|예|REST 프록시 소비자 RequestThroughput|초당 개수|합계|Kafka REST 프록시에 대한 소비자 요청 수|머신, 토픽|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|예|REST 프록시 소비자 실패한 요청|초당 개수|합계|소비자 요청 예외|머신, 토픽|
|KafkaRestProxy.ConsumerRequestTime.p95|예|REST 프록시 소비자 RequestLatency|밀리초|평균|Kafka REST 프록시를 통한 소비자 요청의 메시지 대기 시간|머신, 토픽|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|예|REST 프록시 소비자 요청 백로그|밀리초|평균|소비자 REST 프록시 큐 길이|머신, 토픽|
|KafkaRestProxy.MessagesIn.m1_delta|예|REST 프록시 생산자 MessageThroughput|초당 개수|합계|Kafka REST 프록시를 통한 생산자 메시지 수|머신, 토픽|
|KafkaRestProxy.MessagesOut.m1_delta|예|REST 프록시 소비자 MessageThroughput|초당 개수|합계|Kafka REST 프록시를 통한 소비자 메시지 수|머신, 토픽|
|KafkaRestProxy.OpenConnections|예|REST 프록시 ConcurrentConnections|개수|합계|Kafka REST 프록시를 통한 동시 연결 수|머신, 토픽|
|KafkaRestProxy.ProducerRequest.m1_delta|예|REST 프록시 생산자 RequestThroughput|초당 개수|합계|Kafka REST 프록시에 대한 생산자 요청 수|머신, 토픽|
|KafkaRestProxy.ProducerRequestFail.m1_delta|예|REST 프록시 생산자 실패한 요청|초당 개수|합계|생산자 요청 예외|머신, 토픽|
|KafkaRestProxy.ProducerRequestTime.p95|예|REST 프록시 생산자 RequestLatency|밀리초|평균|Kafka REST 프록시를 통한 생산자 요청의 메시지 대기 시간|머신, 토픽|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|예|REST 프록시 생산자 요청 백로그|밀리초|평균|생산자 REST 프록시 큐 길이|머신, 토픽|
|NumActiveWorkers|예|활성 작업자 수|개수|최대|활성 작업자 수|MetricName|
|PendingCPU|Yes|보류 중인 CPU|개수|최대|YARN에서 보류 중인 CPU 요청|차원 없음|
|PendingMemory|Yes|보류 중인 메모리|개수|최대|YARN에서 보류 중인 메모리 요청|차원 없음|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|서비스 가동률|차원 없음|
|CosmosDbCollectionSize|예|Cosmos DB 컬렉션 크기|바이트|합계|지원 Cosmos DB 컬렉션의 크기(바이트)|차원 없음|
|CosmosDbIndexSize|예|Cosmos DB 인덱스 크기|바이트|합계|지원 Cosmos DB 컬렉션의 인덱스 크기(바이트)|차원 없음|
|CosmosDbRequestCharge|예|Cosmos DB RU 사용량|개수|합계|서비스의 지원 Cosmos DB에 대한 요청의 RU 사용량|작업, ResourceType|
|CosmosDbRequests|예|서비스 Cosmos DB 요청 수|개수|합계|서비스의 지원 Cosmos DB에 대해 수행된 총 요청 수|작업, ResourceType|
|CosmosDbThrottleRate|예|서비스 Cosmos DB 스로틀 속도|개수|합계|서비스의 지원 Cosmos DB에서 오는 총 429개의 응답 수|작업, ResourceType|
|IoTConnectorDeviceEvent|예|들어오는 메시지 수|개수|합계|정규화 이전에 Azure IoT Connector for FHIR이 받은 총 메시지 수|작업, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|예|평균 정규화 단계 대기 시간|밀리초|평균|이벤트 수집 시간과 정규화를 위해 이벤트가 처리되는 시간 사이의 평균 시간|작업, ConnectorName|
|IoTConnectorMeasurement|예|측정 수|개수|합계|Azure IoT Connector for FHIR의 FHIR 변환 단계에서 받은 정규화된 값 판독 수|작업, ConnectorName|
|IoTConnectorMeasurementGroup|예|메시지 그룹의 수|개수|합계|FHIR 변환 단계에서 생성된 형식, 디바이스, 환자 및 구성된 시간 동안 측정한 총 측정값 그룹 수|작업, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|예|평균 그룹 단계 대기 시간|밀리초|평균|IoT 커넥터가 디바이스 데이터를 받은 시간과 FHIR 변환 단계에서 데이터를 처리한 시간 사이의 기간|작업, ConnectorName|
|IoTConnectorNormalizedEvent|예|정규화된 메시지 수|개수|합계|Azure IoT Connector for FHIR의 정규화 단계에서 출력된 총 매핑된 정규화 값 수입|작업, ConnectorName|
|IoTConnectorTotalErrors|예|총 오류 수|개수|합계|Azure IoT Connector for FHIR에서 기록한 총 오류 수|이름, 작업, ErrorType, ErrorSeverity, ConnectorName|
|TotalErrors|예|총 오류|개수|합계|서비스에서 발생한 총 내부 서버 오류 수|프로토콜, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|예|총 대기 시간|밀리초|평균|서비스의 응답 대기 시간|프로토콜|
|TotalRequests|예|총 요청 수|개수|합계|서비스에서 받은 총 요청 수|프로토콜|


## <a name="microsofthealthcareapisworkspacesiotconnectors"></a>Microsoft.HealthcareApis/workspaces/iotconnectors

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeviceEvent|예|들어오는 메시지 수|개수|합계|정규화 이전에 Azure IoT Connector for FHIR이 받은 총 메시지 수|Operation, ResourceName|
|DeviceEventProcessingLatencyMs|예|평균 정규화 단계 대기 시간|밀리초|평균|이벤트 수집 시간과 정규화를 위해 이벤트가 처리되는 시간 사이의 평균 시간|Operation, ResourceName|
|측정|예|측정 수|개수|합계|Azure IoT Connector for FHIR의 FHIR 변환 단계에서 받은 정규화된 값 판독 수|Operation, ResourceName|
|MeasurementGroup|예|메시지 그룹의 수|개수|합계|FHIR 변환 단계에서 생성된 형식, 디바이스, 환자 및 구성된 시간 동안 측정한 총 측정값 그룹 수|Operation, ResourceName|
|MeasurementIngestionLatencyMs|예|평균 그룹 단계 대기 시간|밀리초|평균|IoT 커넥터가 디바이스 데이터를 받은 시간과 FHIR 변환 단계에서 데이터를 처리한 시간 사이의 기간|Operation, ResourceName|
|NormalizedEvent|예|정규화된 메시지 수|개수|합계|Azure IoT Connector for FHIR의 정규화 단계에서 출력된 총 매핑된 정규화 값 수입|Operation, ResourceName|
|TotalErrors|예|총 오류 수|개수|합계|Azure IoT Connector for FHIR에서 기록한 총 오류 수|Name, Operation, ErrorType, ErrorSeverity, ResourceName|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|예|평균 CPU 사용률|백분율|평균|1분 간격으로 측정되는 가상 CPU 사용률의 총 평균 백분율. 총 가상 CPU 수는 사용자가 SKU 정의에서 구성한 값을 기반으로 합니다. 추가 필터는 SKU에 정의된 RoleName를 기반으로 적용할 수 있습니다.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|예|평균 CPU 사용률|백분율|평균|1분 간격으로 측정되는 가상 CPU 사용률의 총 평균 백분율. 총 가상 CPU 수는 사용자가 SKU 정의에서 구성한 값을 기반으로 합니다. 추가 필터는 SKU에 정의된 RoleName를 기반으로 적용할 수 있습니다.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|MetricThreshold|예|메트릭 임계값|개수|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|예|관찰된 용량|개수|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|차원 없음|
|ObservedMetricValue|예|관찰된 메트릭 값|개수|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|ScaleActionsInitiated|예|시작된 크기 조정 작업|개수|합계|크기 조정 작업의 방향입니다.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|예|가용성|백분율|평균|성공적으로 완료된 가용성 테스트 백분율|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|예|가용성 테스트|개수|개수|가용성 테스트 횟수|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|예|가용성 테스트 기간|밀리초|평균|가용성 테스트 기간|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|예|페이지 로드 네트워크 연결 시간|밀리초|평균|사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.|차원 없음|
|browserTimings/processingDuration|예|클라이언트 처리 시간|밀리초|평균|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.|차원 없음|
|browserTimings/receiveDuration|예|응답 수신 시간|밀리초|평균|첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.|차원 없음|
|browserTimings/sendDuration|예|요청 전송 시간|밀리초|평균|네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.|차원 없음|
|browserTimings/totalDuration|예|브라우저 페이지 로드 시간|밀리초|평균|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.|차원 없음|
|dependencies/count|예|종속성 호출|개수|개수|외부 리소스에 대해 애플리케이션이 만든 호출 수입니다.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|예|종속성 기간|밀리초|평균|외부 리소스에 대한 서버 애플리케이션의 호출 기간입니다.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|예|종속성 호출 실패|개수|개수|외부 리소스에 대해 애플리케이션이 만든 실패한 종속성 호출 수입니다.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|exceptions/browser|예|브라우저 예외|개수|개수|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.|cloud/roleName|
|exceptions/count|예|예외|개수|개수|모든 Catch되지 않은 예외의 결합된 수입니다.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|예|서버 예외|개수|개수|서버 애플리케이션에서 발생된 확인할 수 없는 예외의 수입니다.|cloud/roleName, cloud/roleInstance|
|pageViews/count|예|페이지 보기|개수|개수|페이지 보기 수입니다.|operation/synthetic, cloud/roleName|
|pageViews/duration|예|페이지 보기 로드 시간|밀리초|평균|페이지 보기 로드 시간|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|예|예외 속도|초당 개수|평균|.NET 예외 및 .NET 예외로 변환되는 관리되지 않는 예외를 포함하여 Windows 보고되는 처리된 예외 및 처리되지 않은 예외 수입니다.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|예|사용 가능한 메모리|바이트|평균|프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리입니다.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|예|CPU 프로세스|백분율|평균|모든 프로세스 스레드가 명령을 실행하기 위해 프로세서를 사용한 경과된 시간의 백분율입니다. 0~100 사이로 달라질 수 있습니다. 이 메트릭은 w3wp 프로세스만의 성능을 나타냅니다.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|예|프로세스 IO 속도|초당 바이트 수|평균|파일, 네트워크 및 디바이스에서 읽고 쓴 초당 총 바이트 수입니다.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|예|프로세서 시간|백분율|평균|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|예|프로세스 프라이빗 바이트|바이트|평균|모니터링되는 애플리케이션의 프로세스에 독점적으로 할당된 메모리입니다.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|예|HTTP 요청 실행 시간|밀리초|평균|가장 최근 요청의 실행 시간입니다.|cloud/roleInstance|
|performanceCounters/requestsInQueue|예|애플리케이션 큐의 HTTP 요청|개수|평균|애플리케이션 요청 큐의 길이입니다.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|예|HTTP 요청 속도|초당 개수|평균|ASP.NET에서 애플리케이션에 전송된 모든 요청의 속도(초)입니다.|cloud/roleInstance|
|requests/count|예|서버 요청|개수|개수|완료된 HTTP 요청 수입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|예|서버 응답 시간|밀리초|평균|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|예|실패한 요청|개수|개수|실패한 것으로 표시된 HTTP 요청 수입니다. 대부분의 경우 응답 코드가 >= 400이고 401과 같지 않은 요청입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|요청/속도|예|서버 요청 속도|초당 개수|평균|초당 서버 요청 속도|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|예|Traces|개수|개수|문서 개수 추적|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|c2d.commands.failure|예|실패한 명령 호출|개수|합계|IoT Central에서 시작된 모든 실패한 명령 요청 수|차원 없음|
|c2d.commands.requestSize|예|명령 호출의 요청 크기|바이트|합계|IoT Central에서 시작된 모든 명령 요청의 요청 크기|차원 없음|
|c2d.commands.responseSize|예|명령 호출의 응답 크기|바이트|합계|IoT Central에서 시작된 모든 명령 응답의 응답 크기|차원 없음|
|c2d.commands.success|예|성공한 명령 호출|개수|합계|IoT Central에서 시작된 모든 성공한 명령 요청 수|차원 없음|
|c2d.property.read.failure|예|IoT Central에서 실패한 디바이스 속성 읽기|개수|합계|IoT Central에서 시작된 모든 실패한 속성 읽기의 수|차원 없음|
|c2d.property.read.success|예|IoT Central에서 성공한 디바이스 속성 읽기|개수|합계|IoT Central에서 시작된 모든 성공한 속성 읽기의 수|차원 없음|
|c2d.property.update.failure|예|IoT Central에서 실패한 디바이스 속성 업데이트|개수|합계|IoT Central에서 시작된 모든 실패한 속성 업데이트의 수|차원 없음|
|c2d.property.update.success|예|IoT Central에서 성공한 디바이스 속성 업데이트|개수|합계|IoT Central에서 시작된 모든 성공한 속성 업데이트의 수|차원 없음|
|connectedDeviceCount|예|연결된 총 디바이스 수|개수|평균|IoT Central에 연결된 디바이스 수|차원 없음|
|d2c.property.read.failure|예|디바이스에서 실패한 디바이스 속성 읽기|개수|합계|디바이스에서 시작된 모든 실패한 속성 읽기의 수|차원 없음|
|d2c.property.read.success|예|디바이스에서 성공한 디바이스 속성 읽기|개수|합계|디바이스에서 시작된 모든 성공한 속성 읽기의 수|차원 없음|
|d2c.property.update.failure|예|디바이스에서 실패한 디바이스 속성 업데이트|개수|합계|디바이스에서 시작된 모든 실패한 속성 업데이트의 수|차원 없음|
|d2c.property.update.success|예|디바이스에서 성공한 디바이스 속성 업데이트|개수|합계|디바이스에서 시작된 모든 성공한 속성 업데이트의 수|차원 없음|
|d2c.telemetry.ingress.allProtocol|예|총 원격 분석 메시지 보내기 시도 횟수|개수|합계|IoT Central 애플리케이션으로 전송하려고 시도한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.success|예|총 보낸 원격 분석 메시지 수|개수|합계|IoT Central 애플리케이션으로 전송한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|dataExport.error|예|데이터 내보내기 오류|개수|합계|데이터 내보내기에서 발생한 오류 수|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|예|필터링된 데이터 내보내기 메시지|개수|합계|데이터 내보내기에서 필터를 통과한 메시지 수|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|예|받은 데이터 내보내기 메시지|개수|합계|필터링 및 보강 처리 전에 데이터 내보내기로 들어오는 메시지 수|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|예|쓴 데이터 내보내기 메시지|개수|합계|대상에 기록된 메시지 수|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|예|데이터 내보내기 상태 변경|개수|합계|상태 변경 수|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage|예|총 디바이스 데이터 사용량|바이트|합계|IoT Central 애플리케이션에 연결된 디바이스 간에 전송된 바이트|차원 없음|
|provisionedDeviceCount|아니요|총 프로비저닝된 디바이스|개수|평균|IoT Central 애플리케이션에서 프로비저닝된 디바이스 수|차원 없음|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|전체 서비스 가용성|백분율|평균|서비스 요청 가용성|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|예|Service API 총 방문 횟수|개수|개수|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|예|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|전체 자격 증명 모음 가용성|백분율|평균|자격 증명 모음 요청 가용성|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|예|전체 자격 증명 모음 채도|백분율|평균|사용된 자격 증명 모음 용량|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|예|Service API 총 방문 횟수|개수|개수|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|예|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|예|Service API 총 결과|개수|개수|Service API의 총 결과 수|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|예|연결된 클러스터의 총 CPU 코어 수|개수|합계|연결된 클러스터의 총 CPU 코어 수|차원 없음|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BatchBlobCount|예|일괄 처리 Blob 수|개수|평균|수집을 위해 집계된 일괄 처리의 데이터 원본 수|데이터베이스|
|BatchDuration|예|일괄 처리 기간|초|평균|수집 흐름에서 집계 단계의 기간|데이터베이스|
|BatchesProcessed|예|처리된 일괄 처리|개수|합계|수집을 위해 집계된 일괄 처리의 수. 일괄 처리 유형: 일괄 처리가 일괄 처리 정책에 설정된 일괄 처리 시간, 데이터 크기 또는 파일 수 제한에 도달했는지 여부|데이터베이스, SealReason|
|BatchSize|예|Batch 크기|바이트|평균|수집을 위해 집계된 일괄 처리에서 압축되지 않은 데이터의 예상 크기|데이터베이스|
|BlobsDropped|예|삭제된 Blob|개수|합계|구성 요소에서 영구적으로 거부한 Blob 수|데이터베이스, ComponentType, ComponentName|
|BlobsProcessed|예|처리된 Blob|개수|합계|구성 요소에서 처리한 Blob 수|데이터베이스, ComponentType, ComponentName|
|BlobsReceived|예|받은 Blob|개수|합계|구성 요소가 입력 스트림에서 받은 Blob 수|데이터베이스, ComponentType, ComponentName|
|CacheUtilization|예|캐시 사용률|백분율|평균|클러스터 범위의 사용률 수준|차원 없음|
|CacheUtilizationFactor|Yes|캐시 사용률 팩터|백분율|평균|현재 인스턴스 수와 최적 인스턴스 수 간의 백분율 차이(캐시 사용률 당)|차원 없음|
|ContinuousExportMaxLatenessMinutes|예|연속 내보내기 최대 지연 시간|개수|최대|클러스터의 연속 내보내기 작업에서 보고한 지연 시간(분)|차원 없음|
|ContinuousExportNumOfRecordsExported|예|연속 내보내기 - 내보낸 레코드 수|개수|합계|내보낸 레코드 수 - 내보내기 작업 중에 작성된 모든 스토리지 아티팩트에 대해 발생|ContinuousExportName, 데이터베이스|
|ContinuousExportPendingCount|예|보류 중인 연속 내보내기 수|개수|최대|실행 준비가 완료된 보류 중인 연속 내보내기 작업의 수|차원 없음|
|ContinuousExportResult|예|연속 내보내기 결과|개수|개수|연속 내보내기가 성공했는지 아니면 실패했는지 표시|ContinuousExportName, 결과, 데이터베이스|
|CPU|예|CPU|백분율|평균|CPU 사용률 수준|차원 없음|
|DiscoveryLatency|예|검색 대기 시간|초|평균|데이터 연결(있는 경우)에서 보고합니다. 메시지가 큐에 추가된 시간 또는 이벤트가 생성된 시간부터 데이터 연결에서 메시지 또는 이벤트를 발견할 때까지 걸리는 시간(초)입니다. 이 시간은 Azure Data Explorer 총 수집 기간에 포함되지 않습니다.|ComponentType, ComponentName|
|EventsDropped|예|삭제된 이벤트|개수|합계|데이터 연결에서 영구적으로 삭제한 이벤트 수. 실패 이유가 포함된 수집 결과 메트릭이 전송됩니다.|ComponentType, ComponentName|
|EventsProcessed|예|처리된 이벤트|개수|합계|클러스터에서 처리한 이벤트 수|ComponentType, ComponentName|
|EventsProcessedForEventHubs|예|처리한 이벤트(Event/IoT Hubs에 대해)|개수|합계|Event/IoT Hub에서 수집하는 경우 클러스터에서 처리된 이벤트 수|EventStatus|
|EventsReceived|예|받은 이벤트|개수|합계|데이터 연결에서 받은 이벤트 수|ComponentType, ComponentName|
|ExportUtilization|예|내보내기 사용률|백분율|최대|내보내기 사용률|차원 없음|
|IngestionLatencyInSeconds|예|수집 대기 시간|초|평균|클러스터에서 데이터를 수신한 시점부터 쿼리 준비가 될 때까지 수집된 데이터의 지연 시간입니다. 수집 대기 시간은 수집 시나리오에 따라 달라집니다.|차원 없음|
|IngestionResult|예|수집 결과|개수|합계|수집에 실패했거나 성공한 총 원본 수. 메트릭을 상태별로 분할하면 수집 작업의 상태에 대한 자세한 정보를 볼 수 있습니다.|IngestionResultDetails, FailureKind|
|IngestionUtilization|예|수집 사용률|백분율|평균|클러스터에서 사용되는 수집 슬롯의 비율|차원 없음|
|IngestionVolumeInMB|예|수집 볼륨|바이트|합계|클러스터에 수집된 데이터의 전체 볼륨|데이터베이스|
|InstanceCount|예|인스턴스 수|개수|평균|총 인스턴스 수|차원 없음|
|KeepAlive|예|연결 유지|개수|평균|온전성 검사는 쿼리에 대한 클러스터 응답을 나타냅니다.|차원 없음|
|MaterializedViewAgeMinutes|예|구체화된 뷰 사용 기간|개수|평균|구체화 된 뷰 사용 기간(분)|데이터베이스, MaterializedViewName|
|MaterializedViewAgeSeconds|예|구체화된 뷰 사용 기간|초|평균|구체화된 뷰 사용 기간(초)|데이터베이스, MaterializedViewName|
|MaterializedViewDataLoss|예|구체화된 뷰 데이터 손실|개수|최대|구체화된 뷰의 잠재적 데이터 손실을 표시|데이터베이스, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|예|구체화된 뷰 익스텐트 다시 빌드|개수|평균|익스텐트 다시 빌드 수|데이터베이스, MaterializedViewName|
|MaterializedViewHealth|예|구체화된 뷰 상태|개수|평균|구체화된 뷰의 상태(정상이면 1, 정상이 아니면 0)|데이터베이스, MaterializedViewName|
|MaterializedViewRecordsInDelta|예|델타에서 구체화된 뷰 레코드|개수|평균|뷰에서 구체화되지 않은 부분의 레코드 수|데이터베이스, MaterializedViewName|
|MaterializedViewResult|예|구체화된 뷰 결과|개수|평균|구체화 프로세스의 결과|데이터베이스, MaterializedViewName, 결과|
|QueryDuration|예|쿼리 기간|밀리초|평균|쿼리 기간(초)|QueryStatus|
|QueryResult|예|쿼리 결과|개수|개수|총 쿼리 수|QueryStatus|
|QueueLength|예|큐 길이|개수|평균|구성 요소의 큐에 들어 있는 보류 중인 메시지 수|ComponentType|
|QueueOldestMessage|예|큐 가장 오래된 메시지|개수|평균|큐에서 가장 오래된 메시지가 삽입된 시간(초)|ComponentType|
|ReceivedDataSizeBytes|예|받은 데이터 크기 바이트|바이트|평균|데이터 연결에서 받은 데이터의 크기. 데이터 스트림 또는 원시 데이터(제공된 경우)의 크기입니다.|ComponentType, ComponentName|
|StageLatency|예|단계 대기 시간|초|평균|메시지를 발견한 시점부터 보고 구성 요소에서 메시지를 처리하기 위해 메시지를 받은 시점까지 걸린 누적 시간(발견 시간은 메시지가 수집 큐에 추가된 시간 또는 데이터 연결에서 메시지를 발견한 시간으로 설정됨)|데이터베이스, ComponentType|
|SteamingIngestRequestRate|예|스트리밍 수집 요청 속도|개수|RateRequestsPerSecond|스트리밍 수집 요청 속도(초당 요청 수)|차원 없음|
|StreamingIngestDataRate|예|스트리밍 수집 데이터 속도|개수|평균|스트리밍 수집 데이터 속도(초당 MB)|차원 없음|
|StreamingIngestDuration|예|스트리밍 수집 지속 시간|밀리초|평균|스트리밍 수집 기간(밀리초)|차원 없음|
|StreamingIngestResults|예|스트리밍 수집 결과|개수|평균|스트리밍 수집 결과|결과|
|TotalNumberOfConcurrentQueries|예|총 동시 쿼리 수|개수|최대|총 동시 쿼리 수|차원 없음|
|TotalNumberOfExtents|예|총 익스텐트 수|개수|합계|총 데이터 익스텐트 수|차원 없음|
|TotalNumberOfThrottledCommands|예|제한된 명령의 총 수|개수|합계|제한된 명령의 총 수|CommandType|
|TotalNumberOfThrottledQueries|예|제한된 쿼리의 총 수|개수|최대|제한된 쿼리의 총 수|차원 없음|
|WeakConsistencyLatency|Yes|약한 일관성 대기 시간|초|평균|이전 메타데이터 동기화와 다음 동기화 사이의 최대 대기 시간(DB/노드 범위)|Database, RoleInstance|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/IntegrationServiceEnvironments

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActionLatency|예|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionsCompleted|예|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsFailed|예|작업 실패함 |개수|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|예|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|차원 없음|
|ActionsStarted|예|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|예|작업 성공함 |개수|합계|성공한 워크플로 작업 수|차원 없음|
|ActionSuccessLatency|예|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|IntegrationServiceEnvironmentConnectorMemoryUsage|예|통합 서비스 환경의 커넥터 메모리 사용량|백분율|평균|통합 서비스 환경의 커넥터 메모리 사용량|차원 없음|
|IntegrationServiceEnvironmentConnectorProcessorUsage|예|통합 서비스 환경의 커넥터 프로세서 사용량|백분율|평균|통합 서비스 환경의 커넥터 프로세서 사용량|차원 없음|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|예|통합 서비스 환경의 워크플로 메모리 사용량|백분율|평균|통합 서비스 환경의 워크플로 메모리 사용량|차원 없음|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|예|통합 서비스 환경의 워크플로 프로세서 사용량|백분율|평균|통합 서비스 환경의 워크플로 프로세서 사용량|차원 없음|
|RunLatency|예|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunsCancelled|예|실행 취소됨|개수|합계|실행 취소된 워크플로 수|차원 없음|
|RunsCompleted|예|실행 완료됨|개수|합계|실행 완료된 워크플로 수|차원 없음|
|RunsFailed|예|실행 실패함|개수|합계|실행 실패한 워크플로 수|차원 없음|
|RunsStarted|예|실행 시작됨|개수|합계|실행 시작된 워크플로 수|차원 없음|
|RunsSucceeded|예|실행 성공함|개수|합계|실행 성공한 워크플로 수|차원 없음|
|RunSuccessLatency|예|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|TriggerFireLatency|예|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerLatency|예|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggersCompleted|예|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersFailed|예|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersFired|예|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|예|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersStarted|예|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|예|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggerSuccessLatency|예|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/Workflows

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActionLatency|예|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionsCompleted|예|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsFailed|예|작업 실패함 |개수|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|예|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|차원 없음|
|ActionsStarted|예|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|예|작업 성공함 |개수|합계|성공한 워크플로 작업 수|차원 없음|
|ActionSuccessLatency|예|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|ActionThrottledEvents|예|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|BillableActionExecutions|예|청구 가능한 작업 실행|개수|합계|청구되는 워크플로 작업 실행 수.|차원 없음|
|BillableTriggerExecutions|예|청구 가능한 트리거 실행|개수|합계|청구되는 워크플로 트리거 실행 수.|차원 없음|
|BillingUsageNativeOperation|예|기본 작업 실행에 대한 청구 사용량|개수|합계|청구되는 기본 작업 실행 수입니다.|차원 없음|
|BillingUsageStandardConnector|예|표준 커넥터 실행에 대한 청구 사용량|개수|합계|청구되는 표준 커넥터 실행 수입니다.|차원 없음|
|BillingUsageStorageConsumption|예|스토리지 소비 실행에 대한 청구 사용량|개수|합계|청구되는 스토리지 소비 실행 수입니다.|차원 없음|
|RunFailurePercentage|예|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|RunLatency|예|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunsCancelled|예|실행 취소됨|개수|합계|실행 취소된 워크플로 수|차원 없음|
|RunsCompleted|예|실행 완료됨|개수|합계|실행 완료된 워크플로 수|차원 없음|
|RunsFailed|예|실행 실패함|개수|합계|실행 실패한 워크플로 수|차원 없음|
|RunsStarted|예|실행 시작됨|개수|합계|실행 시작된 워크플로 수|차원 없음|
|RunsSucceeded|예|실행 성공함|개수|합계|실행 성공한 워크플로 수|차원 없음|
|RunStartThrottledEvents|예|실행 시작 제한 이벤트|개수|합계|워크플로 실행 시작 제한 이벤트의 수|차원 없음|
|RunSuccessLatency|예|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|RunThrottledEvents|예|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|TotalBillableExecutions|예|총 청구 가능 실행|개수|합계|청구되는 워크플로 실행 수.|차원 없음|
|TriggerFireLatency|예|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerLatency|예|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggersCompleted|예|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersFailed|예|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersFired|예|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|예|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersStarted|예|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|예|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggerSuccessLatency|예|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|예|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|차원 없음|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|활성 코어|예|활성 코어|개수|평균|활성 코어 수|시나리오, ClusterName|
|활성 노드|예|활성 노드|개수|평균|활성 노드 수. 작업을 능동적으로 실행 중인 노드입니다.|시나리오, ClusterName|
|취소가 요청된 실행|예|취소가 요청된 실행|개수|합계|이 작업 영역에 대해 취소가 요청된 실행 수. 실행 취소 요청이 수신되면 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|취소된 실행|예|취소된 실행|개수|합계|이 작업 영역에 대해 취소된 실행 수. 실행이 성공적으로 취소되면 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|완료된 실행|예|완료된 실행|개수|합계|이 작업 영역에 대한 성공적으로 완료된 실행 수. 실행이 완료되고 출력이 수집되면 수가 업데이트 됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuCapacityMillicores|예|CpuCapacityMillicores|개수|평균|CPU 노드의 최대 용량(밀리코어). 용량은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|CpuMemoryCapacityMegabytes|Yes|CpuMemoryCapacityMegabytes|개수|평균|CPU 노드의 최대 메모리 사용률(MB). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|CpuMemoryUtilizationMegabytes|Yes|CpuMemoryUtilizationMegabytes|개수|평균|CPU 노드의 메모리 사용률(MB). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|CpuMemoryUtilizationPercentage|Yes|CpuMemoryUtilizationPercentage|개수|평균|CPU 노드의 메모리 사용률(%). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|CpuUtilization|예|CpuUtilization|개수|평균|CPU 노드의 사용률(백분율). 사용률은 1분 간격으로 보고됩니다.|시나리오, runId, NodeId, ClusterName|
|CpuUtilizationMillicores|예|CpuUtilizationMillicores|개수|평균|CPU 노드의 사용률(밀리코어). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|CpuUtilizationPercentage|예|CpuUtilizationPercentage|개수|평균|CPU 노드의 사용률(%). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|오류|예|오류|개수|합계|이 작업 영역의 실행 오류 수. 실행에서 오류가 발생할 때마다 수가 업데이트됩니다.|시나리오|
|실패한 실행|예|실패한 실행|개수|합계|이 작업 영역에 대해 실패한 실행 수. 실행이 실패하면 수가 업데이트 됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|완료 중인 실행|예|완료 중인 실행|개수|합계|이 작업 영역에 대해 완료 단계에 진입한 실행 수. 실행이 완료되었지만 출력 수집이 아직 진행 중일 때 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuCapacityMilliGPUs|예|GpuCapacityMilliGPUs|개수|평균|GPU 디바이스의 최대 용량(밀리 GPU). 용량은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|GpuEnergyJoules|예|GpuEnergyJoules|개수|합계|GPU 노드의 간격 에너지(Joules). 에너지는 1분 간격으로 보고됩니다.|Scenario, runId, rootRunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryCapacityMegabytes|예|GpuMemoryCapacityMegabytes|개수|평균|GPU 디바이스의 최대 메모리 용량(MB). 용량은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryUtilization|예|GpuMemoryUtilization|개수|평균|GPU 노드의 메모리 사용률(백분율). 사용률은 1분 간격으로 보고됩니다.|시나리오, runId, NodeId, DeviceId, ClusterName|
|GpuMemoryUtilizationMegabytes|예|GpuMemoryUtilizationMegabytes|개수|평균|GPU 디바이스의 메모리 사용률(MB). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|GpuMemoryUtilizationPercentage|예|GpuMemoryUtilizationPercentage|개수|평균|GPU 디바이스의 메모리 사용률(%). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|GpuUtilization|예|GpuUtilization|개수|평균|GPU 노드의 사용률(백분율). 사용률은 1분 간격으로 보고됩니다.|시나리오, runId, NodeId, DeviceId, ClusterName|
|GpuUtilizationMilliGPUs|예|GpuUtilizationMilliGPUs|개수|평균|GPU 디바이스 사용률(밀리초). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|GpuUtilizationPercentage|예|GpuUtilizationPercentage|개수|평균|GPU 디바이스의 사용률(%). 사용률은 1분 간격으로 집계됩니다.|RunId, InstanceId, DeviceId, ComputeName|
|IBReceiveMegabytes|Yes|IBReceiveMegabytes|개수|평균|InfiniBand를 통해 수신된 네트워크 데이터(MB). 메트릭은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|IBTransmitMegabytes|Yes|IBTransmitMegabytes|개수|평균|InfiniBand를 통해 발송된 네트워크 데이터(MB). 메트릭은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|유휴 코어|예|유휴 코어|개수|평균|유휴 코어 수|시나리오, ClusterName|
|유휴 노드|예|유휴 노드|개수|평균|유휴 노드 수. 유휴 노드는 현재 작업을 실행하고 있지 않지만 새 작업이 있으면 수락할 수 있는 노드입니다.|시나리오, ClusterName|
|나가는 코어|예|나가는 코어|개수|평균|나가는 코어 수|시나리오, ClusterName|
|나가는 노드|예|나가는 노드|개수|평균|나가는 노드 수. 나가는 노드란 방금 작업 처리를 완료했으며 곧 유휴 상태로 전환될 노드입니다.|시나리오, ClusterName|
|모델 배포 실패|예|모델 배포 실패|개수|합계|이 작업 영역에서 실패한 모델 배포 수|시나리오, StatusCode|
|모델 배포 시작|예|모델 배포 시작|개수|합계|이 작업 영역에서 시작한 모델 배포 수|시나리오|
|모델 배포 성공|예|모델 배포 성공|개수|합계|이 작업 영역에서 성공한 모델 배포 수|시나리오|
|모델 레지스터 실패|예|모델 레지스터 실패|개수|합계|이 작업 영역에서 실패한 모델 등록 수|시나리오, StatusCode|
|모델 레지스터 성공|예|모델 레지스터 성공|개수|합계|이 작업 영역에서 성공한 모델 등록 수|시나리오|
|NetworkInputMegabytes|Yes|NetworkInputMegabytes|개수|평균|수신된 네트워크 데이터(MB). 메트릭은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|NetworkOutputMegabytes|Yes|NetworkOutputMegabytes|개수|평균|발송된 네트워크 데이터(MB). 메트릭은 1분 간격으로 집계됩니다.|RunId, InstanceId, ComputeName|
|응답 없는 실행|예|응답 없는 실행|개수|합계|이 작업 영역에 대해 응답하지 않는 실행 수. 실행이 응답 없음 상태로 전환될 때 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|시작되지 않은 실행|예|시작되지 않은 실행|개수|합계|이 작업 영역에 대해 시작되지 않음 상태의 실행 수. 실행을 만드는 요청이 수신되었지만 실행 정보가 아직 채워지지 않은 경우 수가 업데이트됩니다. |시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|선점된 코어|예|선점된 코어|개수|평균|선점된 코어 수|시나리오, ClusterName|
|선점된 노드|예|선점된 노드|개수|평균|선점된 노드 수. 사용 가능한 노드 풀에서 가져온 낮은 우선 순위 노드입니다.|시나리오, ClusterName|
|준비 중인 실행|예|준비 중인 실행|개수|합계|이 작업 영역에 대해 준비 중인 실행 수. 실행 환경을 준비하는 동안 실행이 준비 상태에 진입하면 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|프로비저닝 중인 실행|예|프로비저닝 중인 실행|개수|합계|이 작업 영역에 대해 프로비저닝 중인 실행 수. 실행이 컴퓨팅 대상 생성 또는 프로비저닝을 기다릴 때 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|큐에서 대기 중인 실행|예|큐에서 대기 중인 실행|개수|합계|이 작업 영역에 대해 큐에서 대기 중인 실행 수. 실행이 컴퓨팅 대상의 큐에서 대기할 때 수가 업데이트됩니다. 필요한 컴퓨팅 노드가 준비될 때까지 대기하는 동안 발생할 수 있습니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|할당량 사용률|예|할당량 사용률|개수|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|
|실행 시작|예|실행 시작|개수|합계|이 작업 영역에 대해 실행 중인 실행 수. 실행이 필요한 리소스에서 실행되기 시작할 때 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|시작 중인 실행|예|시작 중인 실행|개수|합계|이 작업 영역에 대해 시작된 실행 수. 실행 및 실행 정보(예: 실행 ID)를 만드는 요청이 채워지면 수가 업데이트됩니다.|시나리오, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|총 코어 수|예|총 코어 수|개수|평균|총 코어 수|시나리오, ClusterName|
|총 노드 수|예|총 노드 수|개수|평균|총 노드 수. 이 합계에는 활성 노드, 유휴 노드, 사용할 수 없는 노드, 선점된 노드, 나가는 노드 중 일부가 포함됩니다.|시나리오, ClusterName|
|사용 불가 코어|예|사용 불가 코어|개수|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|사용 불가 노드|예|사용 불가 노드|개수|평균|사용할 수 없는 노드 수. 사용할 수 없는 노드는 확인할 수 없는 문제 때문에 작동하지 않는 노드입니다. Azure에서는 이러한 노드를 재활용합니다.|시나리오, ClusterName|
|경고|예|경고|개수|합계|이 작업 영역의 실행 경고 수. 실행에서 경고가 발생할 때마다 수가 업데이트됩니다.|시나리오|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|API 가용성|ApiCategory, ApiName|
|CreatorUsage|아니요|작성자 사용량|바이트|평균|Azure Maps 작성자 사용량 통계|ServiceName|
|사용량|예|사용|개수|개수|API 호출 수|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AssetCount|예|자산 수|개수|평균|현재 미디어 서비스 계정에서 이미 만든 자산 수|차원 없음|
|AssetQuota|예|자산 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용되는 자산 수|차원 없음|
|AssetQuotaUsedPercentage|예|사용된 자산 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용되는 자산 백분율|차원 없음|
|ChannelsAndLiveEventsCount|예|라이브 이벤트 수|개수|평균|현재 미디어 서비스 계정에 있는 총 라이브 이벤트 수|차원 없음|
|ContentKeyPolicyCount|예|콘텐츠 키 정책 수|개수|평균|현재 미디어 서비스 계정에서 이미 만든 콘텐츠 키 정책 수|차원 없음|
|ContentKeyPolicyQuota|예|콘텐츠 키 정책 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용되는 콘텐츠 키 정책 수|차원 없음|
|ContentKeyPolicyQuotaUsedPercentage|예|사용된 콘텐츠 키 정책 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용되는 콘텐츠 키 정책 백분율|차원 없음|
|JobsScheduled|Yes|예약된 작업|개수|평균|예약된 상태의 작업 수. 이 메트릭의 수는 v3 API를 통해 제출된 작업만 반영합니다. v2(레거시) API를 통해 제출된 작업은 계산되지 않습니다.|차원 없음|
|MaxChannelsAndLiveEventsCount|예|최대 라이브 이벤트 할당량|개수|평균|현재 미디어 서비스 계정에서 허용되는 최대 라이브 이벤트 수|차원 없음|
|MaxRunningChannelsAndLiveEventsCount|예|최대 실행 중 라이브 이벤트 할당량|개수|평균|현재 미디어 서비스 계정에서 허용되는 최대 실행 중 라이브 이벤트 수|차원 없음|
|RunningChannelsAndLiveEventsCount|예|실행 중 라이브 이벤트 수|개수|평균|현재 미디어 서비스 계정에서 실행 중인 라이브 이벤트 수|차원 없음|
|StreamingPolicyCount|예|스트리밍 정책 수|개수|평균|현재 미디어 서비스 계정에서 이미 만든 스트리밍 정책 수|차원 없음|
|StreamingPolicyQuota|예|스트리밍 정책 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용되는 스트리밍 정책 수|차원 없음|
|StreamingPolicyQuotaUsedPercentage|예|사용된 스트리밍 정책 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용되는 스트리밍 정책 백분율|차원 없음|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|IngestBitrate|예|라이브 이벤트 수집 비트 전송률|BitsPerSecond|평균|라이브 이벤트에 대해 수집되는 들어오는 비트 전송률(비트/초)|TrackName|
|IngestDriftValue|예|라이브 이벤트 수집 드리프트 값|초|최대|수집된 콘텐츠의 타임스탬프와 시스템 클록 간의 드리프트. 초/분 단위로 측정됩니다. 0이 아닌 값은 수집된 콘텐츠가 시스템 클록 시간보다 늦게 도착한다는 뜻입니다.|TrackName|
|IngestLastTimestamp|예|라이브 이벤트 수집 마지막 타임스탬프|밀리초|최대|라이브 이벤트에 대해 마지막으로 수집된 타임스탬프|TrackName|
|LiveOutputLastTimestamp|예|마지막 출력 타임스탬프|밀리초|최대|라이브 이벤트 출력을 위해 스토리지에 업로드된 마지막 조각의 타임스탬프|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CPU|예|CPU 사용량|백분율|평균|프리미엄 스트리밍 엔드포인트의 CPU 사용량. 표준 스트리밍 엔드포인트에는 이 데이터가 제공되지 않습니다.|차원 없음|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)|OutputFormat|
|EgressBandwidth|예|송신 대역폭|BitsPerSecond|평균|송신 대역폭(비트/초)|차원 없음|
|요청|예|요청|개수|합계|스트리밍 엔드포인트에 대한 요청|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|예|성공 엔드투엔드 대기 시간|밀리초|평균|성공한 요청의 평균 대기 시간(밀리초)|OutputFormat|


## <a name="microsoftmediavideoanalyzers"></a>Microsoft. 미디어/비디오 분석기

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ReceivedBytes|예|받은 바이트|바이트|합계|파이프라인 노드에서 받은 바이트 수입니다.|PipelineTopology, 파이프라인, 노드|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|예|활성 렌더링 세션|개수|평균|총 활성 렌더링 세션 수|SessionType, SDKVersion|
|AssetsConverted|예|변환된 자산|개수|합계|변환된 총 자산 수|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AnchorsCreated|예|만든 앵커|개수|합계|만든 앵커 수|DeviceFamily, SDKVersion|
|AnchorsDeleted|예|삭제된 앵커|개수|합계|삭제된 앵커 수|DeviceFamily, SDKVersion|
|AnchorsQueried|예|쿼리된 앵커|개수|합계|쿼리된 Spatial Anchors 수|DeviceFamily, SDKVersion|
|AnchorsUpdated|예|업데이트된 앵커|개수|합계|업데이트된 앵커 수|DeviceFamily, SDKVersion|
|PosesFound|예|발견한 포즈|개수|합계|반환된 포즈 수|DeviceFamily, SDKVersion|
|TotalDailyAnchors|예|총 일간 앵커|개수|평균|총 앵커 수 - 일간|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|예|할당된 풀 크기|바이트|평균|이 풀의 프로비전 크기|차원 없음|
|VolumePoolAllocatedToVolumeThroughput|예|할당된 풀 처리량|초당 바이트 수|평균|풀에 속한 모든 볼륨의 처리량 합계|차원 없음|
|VolumePoolAllocatedUsed|예|볼륨 크기에 할당된 풀|바이트|평균|풀의 사용되는 할당된 크기|차원 없음|
|VolumePoolProvisionedThroughput|예|풀에 프로비저닝된 처리량|초당 바이트 수|평균|이 풀에 프로비저닝된 처리량|차원 없음|
|VolumePoolTotalLogicalSize|예|풀 사용 크기|바이트|평균|풀에 속한 모든 볼륨의 논리적 크기의 합계|차원 없음|
|VolumePoolTotalSnapshotSize|예|풀의 총 스냅샷 크기|바이트|평균|이 풀에 있는 모든 볼륨의 스냅샷 크기 합계|차원 없음|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageReadLatency|예|평균 읽기 대기 시간|밀리초|평균|작업당 평균 읽기 대기 시간(밀리초)|차원 없음|
|AverageWriteLatency|예|평균 쓰기 대기 시간|밀리초|평균|작업당 평균 쓰기 대기 시간(밀리초)|차원 없음|
|CbsVolumeBackupActive|예|볼륨 백업이 일시 중단됨|개수|평균|볼륨에 대한 백업 정책이 일시 중단되었습니까? 0은 예, 1은 아니요.|차원 없음|
|CbsVolumeLogicalBackupBytes|예|볼륨 백업 바이트|바이트|평균|이 볼륨에 대해 백업된 총 바이트 수|차원 없음|
|CbsVolumeOperationComplete|예|볼륨 백업 작업 완료|개수|평균|마지막 볼륨 백업 또는 복원 작업이 성공적으로 완료되었습니까? 1은 예, 0은 아니요|차원 없음|
|CbsVolumeOperationTransferredBytes|예|볼륨 백업 마지막 전송 바이트|바이트|평균|마지막 백업 또는 복원 작업에 대해 전송된 총 바이트 수|차원 없음|
|CbsVolumeProtected|예|볼륨 백업이 사용됨|개수|평균|볼륨에 백업이 사용되나요? 1은 예, 0은 아니요|차원 없음|
|OtherThroughput|예|기타 처리량|초당 바이트 수|평균|읽기 또는 쓰기를 제외한 기타 처리량(바이트/초)|차원 없음|
|ReadIops|예|읽기 IOPS|초당 개수|평균|초당 읽기 입력/출력 작업|차원 없음|
|ReadThroughput|예|읽기 처리량|초당 바이트 수|평균|읽기 처리량(바이트/초)|차원 없음|
|TotalThroughput|예|총 처리량|초당 바이트 수|평균|모든 처리량 합계(바이트/초)|차원 없음|
|VolumeAllocatedSize|예|할당된 볼륨 크기|바이트|평균|프로비저닝된 볼륨 크기|차원 없음|
|VolumeConsumedSizePercentage|예|사용된 볼륨 크기 백분율|백분율|평균|스냅샷을 포함하여 사용된 볼륨의 백분율|차원 없음|
|VolumeCoolTierDataReadSize|예|볼륨 쿨 계층 데이터 읽기 크기|바이트|평균|볼륨당 GET을 사용하여 읽은 데이터|차원 없음|
|VolumeCoolTierDataWriteSize|예|볼륨 쿨 계층 데이터 쓰기 크기|바이트|평균|볼륨당 PUT을 사용하여 계층화한 데이터|차원 없음|
|VolumeCoolTierSize|예|볼륨 쿨 계층 크기|바이트|평균|쿨 계층의 볼륨 공간|차원 없음|
|VolumeLogicalSize|예|사용된 볼륨 크기|바이트|평균|볼륨의 논리적 크기(사용되는 바이트)|차원 없음|
|VolumeSnapshotSize|예|볼륨 스냅샷 크기|바이트|평균|볼륨의 모든 스냅샷 크기|차원 없음|
|WriteIops|예|쓰기 IOPS|초당 개수|평균|초당 쓰기 입력/출력 작업|차원 없음|
|WriteThroughput|예|쓰기 처리량|초당 바이트 수|평균|쓰기 처리량(바이트/초)|차원 없음|
|XregionReplicationHealthy|예|볼륨 복제 상태가 정상|개수|평균|관계의 조건으로 1 또는 0|차원 없음|
|XregionReplicationLagTime|예|볼륨 복제 지연 시간|초|평균|미러의 데이터가 원본 뒤에서 지연되는 시간(초)|차원 없음|
|XregionReplicationLastTransferDuration|예|볼륨 복제 마지막 전송 기간|초|평균|마지막 전송을 완료하는 데 걸린 시간(초)|차원 없음|
|XregionReplicationLastTransferSize|예|볼륨 복제 마지막 전송 크기|바이트|평균|마지막 전송의 일부로 전송된 총 바이트 수|차원 없음|
|XregionReplicationRelationshipProgress|예|볼륨 복제 진행률|바이트|평균|현재 전송 작업에 대해 전송된 총 데이터 양|차원 없음|
|XregionReplicationRelationshipTransferring|예|볼륨 복제 전송 중|개수|평균|볼륨 복제 상태가 '전송 중'인지 여부|차원 없음|
|XregionReplicationTotalTransferBytes|예|볼륨 복제 총 전송|바이트|평균|관계에 대해 전송된 누적 바이트 수|차원 없음|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationgateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|예|Application Gateway 총 시간|밀리초|평균|요청을 처리하고 응답을 보내는 데 걸리는 평균 시간. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 평균 간격으로 계산됩니다. 일반적으로 Application Gateway 처리 시간, 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간, 백 엔드 서버에서 응답하는 데 걸린 시간이 포함됩니다.|수신기|
|AvgRequestCountPerHealthyHost|예|분당 요청 수/정상 호스트|개수|평균|분당 평균 요청 수/풀의 정상 백 엔드 호스트|BackendSettingsPool|
|BackendConnectTime|예|백 엔드 연결 시간|밀리초|평균|백 엔드 서버와의 연결을 설정하는 데 걸린 시간|수신기, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|예|백 엔드 첫 번째 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대한 연결 설정을 시작한 시간과 응답 헤더의 첫 번째 바이트를 수신한 시간 사이의 간격. 백 엔드 서버의 처리 시간을 근사치로 계산|수신기, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|예|백 엔드 마지막 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대한 연결을 설정하기 시작한 시간과 응답 본문의 마지막 바이트를 받은 시간 사이의 간격|수신기, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|예|백 엔드 응답 상태|개수|합계|백 엔드 멤버가 생성한 HTTP 응답 코드의 수. Application Gateway에서 생성한 응답 코드는 여기에 포함되지 않습니다.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BackendTlsNegotiationError|예|백 엔드 TLS 연결 오류|개수|합계|Application Gateway 백 엔드에 대한 TLS 연결 오류|BackendHttpSetting, BackendPool, ErrorType|
|BlockedCount|예|웹 애플리케이션 방화벽 차단 요청 규칙 배포|개수|합계|Web Application Firewall 차단 요청 규칙 배포|RuleGroup, RuleId|
|BlockedReqCount|예|웹 애플리케이션 방화벽 차단 요청 수|개수|합계|Web Application Firewall 차단 요청 수|차원 없음|
|BytesReceived|예|수신된 바이트|바이트|합계|Application Gateway가 클라이언트에서 받은 총 바이트 수|수신기|
|BytesSent|예|보낸 바이트|바이트|합계|Application Gateway가 클라이언트에 보낸 총 바이트 수|수신기|
|CapacityUnits|예|현재 용량 단위|개수|평균|소비한 용량 단위|차원 없음|
|ClientRtt|예|클라이언트 RTT|밀리초|평균|클라이언트와 Application Gateway 간의 평균 왕복 시간. 이 메트릭은 연결을 설정하고 승인을 반환하는 데 걸리는 시간을 나타냅니다.|수신기|
|ComputeUnits|예|현재 컴퓨팅 단위|개수|평균|소비된 컴퓨팅 단위|차원 없음|
|CpuUtilization|예|CPU 사용률|백분율|평균|Application Gateway의 현재 CPU 사용률|차원 없음|
|CurrentConnections|예|현재 연결|개수|합계|Application Gateway와 설정된 현재 연결 수|차원 없음|
|EstimatedBilledCapacityUnits|예|예상 청구 용량 단위|개수|평균|요금이 부과될 예상 용량 단위|차원 없음|
|FailedRequests|예|실패한 요청|개수|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|FixedBillableCapacityUnits|예|고정 청구 가능 용량 단위|개수|평균|요금이 부과될 최소 용량 단위|차원 없음|
|HealthyHostCount|예|정상 호스트 수|개수|평균|정상 백 엔드 호스트 수|BackendSettingsPool|
|MatchedCount|예|웹 애플리케이션 방화벽 총 규칙 배포|개수|합계|들어오는 트래픽에 대한 Web Application Firewall 총 규칙 배포|RuleGroup, RuleId|
|NewConnectionsPerSecond|예|초당 새 연결 수|초당 개수|평균|Application Gateway와 설정된 초당 새 연결 수|차원 없음|
|RejectedConnections|예|Rejected Connections|개수|합계|Application Gateway 프런트 엔드에 대해 거부된 연결 수|차원 없음|
|ResponseStatus|예|응답 상태|개수|합계|Application Gateway에서 반환된 HTTP 응답 상태|HttpStatusGroup|
|처리량|예|처리량|초당 바이트 수|평균|Application Gateway에서 제공하는 초당 바이트 수|차원 없음|
|TlsProtocol|예|클라이언트 TLS 프로토콜|개수|합계|Application Gateway와 연결을 설정한 클라이언트에서 시작한 TLS 및 비 TLS 요청 수. TLS 프로토콜 배포를 보려면 차원 TLS 프로토콜로 필터링합니다.|수신기, TlsProtocol|
|TotalRequests|예|총 요청 수|개수|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|UnhealthyHostCount|예|비정상 호스트 수|개수|평균|비정상 백 엔드 호스트 수|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|예|애플리케이션 규칙 적중 횟수|개수|합계|애플리케이션 규칙이 적중한 횟수|상태, 이유, 프로토콜|
|DataProcessed|예|처리된 데이터|바이트|합계|이 방화벽에서 처리한 총 데이터 양|차원 없음|
|FirewallHealth|예|Firewall 성능 상태|백분율|평균|이 방화벽의 전반적인 상태를 표시|상태, 이유|
|NetworkRuleHit|예|네트워크 규칙 적중 횟수|개수|합계|네트워크 규칙이 적중한 횟수|상태, 이유, 프로토콜|
|SNATPortUtilization|예|SNAT 포트 사용률|백분율|평균|현재 사용 중인 아웃바운드 SNAT 포트의 백분율|프로토콜|
|처리량|예|처리량|BitsPerSecond|평균|이 방화벽에서 처리한 처리량|차원 없음|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|pingmesh|예|Bastion 통신 상태|개수|평균|모든 통신이 양호하면 통신 상태가 1로 표시되고 불량하면 0으로 표시됩니다.|차원 없음|
|세션|예|세션 수|개수|합계|Bastion의 세션 수. 합계 및 인스턴스당 수를 볼 수 있습니다.|호스트|
|total|예|총 메모리|개수|평균|총 메모리 통계|호스트|
|usage_user|예|CPU 사용량|개수|평균|CPU 사용량 통계|cpu, 호스트|
|프로토콜|예|메모리 사용량|개수|평균|메모리 사용량 통계|호스트|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BitsInPerSecond|예|BitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|예|BitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|차원 없음|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|QueryVolume|예|쿼리 볼륨|개수|합계|DNS 영역에 대해 제공된 쿼리 수|차원 없음|
|RecordSetCapacityUtilization|예|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|차원 없음|
|RecordSetCount|예|레코드 집합 수|개수|최대|DNS 영역의 레코드 집합 수|차원 없음|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ArpAvailability|예|Arp 가용성|백분율|평균|모든 피어에 대한 MSEE의 ARP 가용성|PeeringType, 피어|
|BgpAvailability|예|Bgp 가용성|백분율|평균|모든 피어에 대한 MSEE의 BGP 가용성|PeeringType, 피어|
|BitsInPerSecond|예|BitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|PeeringType, DeviceRole|
|BitsOutPerSecond|예|BitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|예|GlobalReachBitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|예|GlobalReachBitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|PeeredCircuitSKey|
|QosDropBitsInPerSecond|예|DroppedInBitsPerSecond|BitsPerSecond|평균|초당 삭제된 수신 데이터 비트 수|차원 없음|
|QosDropBitsOutPerSecond|예|DroppedOutBitsPerSecond|BitsPerSecond|평균|초당 삭제된 송신 데이터 비트 수|차원 없음|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BitsInPerSecond|예|BitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|예|BitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|차원 없음|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|예|BitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|연결 이름|
|ErGatewayConnectionBitsOutPerSecond|예|BitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|연결 이름|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|예|피어에 알린 경로 수(미리 보기)|개수|최대|ExpressRouteGateway에서 피어에 알린 경로 수|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|예|피어에서 학습한 경로 수(미리 보기)|개수|최대|ExpressRouteGateway가 피어에서 학습한 경로 수|roleInstance|
|ExpressRouteGatewayCpuUtilization|예|CPU 사용률|백분율|평균|ExpressRoute 게이트웨이의 CPU 사용률|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|예|경로 변경 빈도(미리 보기)|개수|합계|ExpressRoute 게이트웨이의 경로 변경 빈도|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|예|Virtual Network의 VM 수(미리 보기)|개수|최대|Virtual Network의 VM 수|차원 없음|
|ExpressRouteGatewayPacketsPerSecond|예|초당 패킷 수|초당 개수|평균|ExpressRoute 게이트웨이의 패킷 수|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdminState|예|AdminState|개수|평균|포트의 관리 상태|링크|
|LineProtocol|예|LineProtocol|개수|평균|포트의 회선 프로토콜 상태|링크|
|PortBitsInPerSecond|예|BitsInPerSecond|BitsPerSecond|평균|초당 Azure 수신 비트|링크|
|PortBitsOutPerSecond|예|BitsOutPerSecond|BitsPerSecond|평균|초당 Azure 송신 비트|링크|
|RxLightLevel|예|RxLightLevel|개수|평균|Rx Light 수준(dBm)|링크, 레인|
|TxLightLevel|예|TxLightLevel|개수|평균|Tx Light 수준(dBm)|링크, 레인|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|예|백 엔드 상태 비율|백분율|평균|HTTP/S 프록시에서 백 엔드로 성공한 상태 프로브의 비율|Backend, BackendPool|
|BackendRequestCount|예|백 엔드 요청 수|개수|합계|HTTP/S 프록시에서 백 엔드로 전송된 요청 수|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|예|백 엔드 요청 대기 시간|밀리초|평균|HTTP/S 프록시에서 백 엔드의 마지막 응답 바이트를 받을 때까지 HTTP/S 프록시에서 백 엔드로 요청이 전송될 때 계산된 시간|백 엔드|
|BillableResponseSize|예|청구 대상 응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트에 응답으로 전송된 청구 가능 바이트 수(요청당 최소 2KB)|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|예|요청 수|개수|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|예|요청 크기|바이트|합계|클라이언트에서 HTTP/S 프록시로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|예|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|예|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|예|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|예|할당된 SNAT 포트|개수|평균|기간 내에 할당된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|ByteCount|예|바이트 수|바이트|합계|기간 내에 전송된 총 바이트 수|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|예|상태 프로브 상태|개수|평균|기간당 평균 Load Balancer 상태 프로브 상태|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|예|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|예|SNAT 연결 수|개수|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|예|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|예|사용된 SNAT 포트|개수|평균|기간 내에 사용된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|VipAvailability|예|데이터 경로 가용성|개수|평균|기간당 평균 Load Balancer 데이터 경로 가용성|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ByteCount|예|바이트|바이트|합계|기간 내에 전송된 총 바이트 수|프로토콜, 방향|
|DatapathAvailability|예|Datapath 가용성(미리 보기)|개수|평균|NAT Gateway Datapath 가용성|차원 없음|
|PacketCount|예|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|프로토콜, 방향|
|PacketDropCount|예|삭제된 패킷 수|개수|합계|삭제된 패킷 수|차원 없음|
|SNATConnectionCount|예|SNAT 연결 수|개수|합계|총 동시 활성 연결 수|프로토콜, ConnectionState|
|TotalConnectionCount|예|총 SNAT 연결 수|개수|합계|총 활성 SNAT 연결 수|프로토콜|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesReceivedRate|예|수신된 바이트|바이트|합계|네트워크 인터페이스가 수신한 바이트 수|차원 없음|
|BytesSentRate|예|보낸 바이트|바이트|합계|네트워크 인터페이스가 보낸 바이트 수|차원 없음|
|PacketsReceivedRate|예|수신된 패킷|개수|합계|네트워크 인터페이스가 수신한 패킷 수|차원 없음|
|PacketsSentRate|예|보낸 패킷|개수|합계|네트워크 인터페이스가 보낸 패킷 수|차원 없음|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|예|평균 왕복 시간(ms)(클래식)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|차원 없음|
|ChecksFailedPercent|예|검사 실패 백분율|백분율|평균|실패한 연결 모니터링 검사 백분율|SourceAddress, SourceName, SourceResourceId, SourceType, 프로토콜, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|예|실패한 프로브 백분율(클래식)|백분율|평균|실패한 연결 모니터링 프로브 %|차원 없음|
|RoundTripTimeMs|예|왕복 시간(ms)|밀리초|평균|연결 모니터링 검사의 왕복 시간(밀리초)|SourceAddress, SourceName, SourceResourceId, SourceType, 프로토콜, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|예|테스트 결과|개수|평균|연결 모니터 테스트 결과|SourceAddress, SourceName, SourceResourceId, SourceType, 프로토콜, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>microsoft.network/p2svpngateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|P2SBandwidth|예|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 지점 및 사이트 간 대역폭(바이트)|인스턴스|
|P2SConnectionCount|예|P2S 연결 수|개수|합계|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜, 인스턴스|
|UserVpnRouteCount|No|사용자 Vpn 경로 수|개수|합계|게이트웨이에서 학습한 P2S 사용자 Vpn 경로 수|RouteType, Instance|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|QueryVolume|예|쿼리 볼륨|개수|합계|프라이빗 DNS 영역에 대해 제공된 쿼리 수|차원 없음|
|RecordSetCapacityUtilization|예|레코드 집합 용량 사용률|백분율|최대|프라이빗 DNS 영역에서 사용한 레코드 집합 용량 백분율|차원 없음|
|RecordSetCount|예|레코드 집합 수|개수|최대|프라이빗 DNS 영역의 레코드 집합 수|차원 없음|
|VirtualNetworkLinkCapacityUtilization|예|Virtual Network 링크 용량 사용률|백분율|최대|프라이빗 DNS 영역에서 사용하는 Virtual Network 링크 용량 백분율|차원 없음|
|VirtualNetworkLinkCount|예|Virtual Network 링크 수|개수|최대|프라이빗 DNS 영역에 연결된 Virtual Network 수|차원 없음|
|VirtualNetworkWithRegistrationCapacityUtilization|예|Virtual Network 등록 링크 용량 사용률|백분율|최대|프라이빗 DNS 영역에서 사용하는 자동 등록 용량이 포함된 Virtual Network 링크의 백분율|차원 없음|
|VirtualNetworkWithRegistrationLinkCount|예|Virtual Network 등록 링크 수|개수|최대|자동 등록을 사용하는 프라이빗 DNS 영역에 연결된 Virtual Network 수|차원 없음|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PEBytesIn|아니요|바이트 입력|개수|합계|총 바이트 출력 수|차원 없음|
|PEBytesOut|아니요|바이트 출력|개수|합계|총 바이트 출력 수|차원 없음|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PLSBytesIn|예|바이트 입력|개수|합계|총 바이트 출력 수|PrivateLinkServiceId|
|PLSBytesOut|예|바이트 출력|개수|합계|총 바이트 출력 수|PrivateLinkServiceId|
|PLSNatPortsUsage|예|Nat 포트 사용량|백분율|평균|Nat 포트 사용량|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ByteCount|예|바이트 수|바이트|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|BytesDroppedDDoS|예|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|차원 없음|
|BytesForwardedDDoS|예|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|차원 없음|
|BytesInDDoS|예|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|차원 없음|
|DDoSTriggerSYNPackets|예|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|차원 없음|
|DDoSTriggerTCPPackets|예|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|차원 없음|
|DDoSTriggerUDPPackets|예|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|차원 없음|
|IfUnderDDoSAttack|예|DDoS 공격 진행 여부|개수|최대|DDoS 공격 진행 여부|차원 없음|
|PacketCount|예|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|PacketsDroppedDDoS|예|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|차원 없음|
|PacketsForwardedDDoS|예|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|차원 없음|
|PacketsInDDoS|예|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|차원 없음|
|SynCount|예|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|
|TCPBytesDroppedDDoS|예|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|차원 없음|
|TCPBytesForwardedDDoS|예|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|차원 없음|
|TCPBytesInDDoS|예|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|차원 없음|
|TCPPacketsDroppedDDoS|예|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|차원 없음|
|TCPPacketsForwardedDDoS|예|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|차원 없음|
|TCPPacketsInDDoS|예|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|차원 없음|
|UDPBytesDroppedDDoS|예|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|차원 없음|
|UDPBytesForwardedDDoS|예|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|차원 없음|
|UDPBytesInDDoS|예|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|차원 없음|
|UDPPacketsDroppedDDoS|예|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|차원 없음|
|UDPPacketsForwardedDDoS|예|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|차원 없음|
|UDPPacketsInDDoS|예|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|차원 없음|
|VipAvailability|예|데이터 경로 가용성|개수|평균|기간당 평균 IP 주소 가용성|포트|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|예|엔드포인트별 엔드포인트 상태|개수|최대|엔드포인트의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|
|QpsByEndpoint|예|반환된 엔드포인트별 쿼리|개수|합계|Traffic Manager 엔드포인트가 주어진 시간 내에 반환된 횟수|EndpointName|


## <a name="microsoftnetworkvirtualhubs"></a>Microsoft.Network/virtualHubs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BgpPeerStatus|No|Bgp 피어 상태|개수|최대|1 - 연결 됨, 0 - 연결 되지 않음|routeserviceinstance, bgppeerip, bgppeertype|
|CountOfRoutesAdvertisedToPeer|No|피어에 보급된 경로 수|개수|최대|피어에 보급된 총 경로 수|routeserviceinstance, bgppeerip, bgppeertype|
|CountOfRoutesLearnedFromPeer|No|피어에서 학습된 경로 수|개수|최대|피어에서 학습된 총 경로 수|routeserviceinstance, bgppeerip, bgppeertype|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>microsoft.network/virtualnetworkgateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageBandwidth|예|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 사이트 간 대역폭(바이트)|인스턴스|
|BgpPeerStatus|No|BGP 피어 상태|개수|평균|BGP 피어의 상태|BgpPeerAddress, Instance|
|BgpRoutesAdvertised|Yes|보급된 BGP 경로|개수|합계|터널을 통해 보급된 Bgp 경로 수|BgpPeerAddress, Instance|
|BgpRoutesLearned|Yes|학습된 BGP 경로|개수|합계|터널을 통해 학습된 Bgp 경로 수|BgpPeerAddress, Instance|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|예|피어에 알린 경로 수(미리 보기)|개수|최대|ExpressRouteGateway에서 피어에 알린 경로 수|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|예|피어에서 학습한 경로 수(미리 보기)|개수|최대|ExpressRouteGateway가 피어에서 학습한 경로 수|roleInstance|
|ExpressRouteGatewayCpuUtilization|예|CPU 사용률|백분율|평균|ExpressRoute 게이트웨이의 CPU 사용률|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|예|경로 변경 빈도(미리 보기)|개수|합계|ExpressRoute 게이트웨이의 경로 변경 빈도|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|예|가상 네트워크 내의 VM 수(미리 보기)|개수|최대|Virtual Network의 VM 수|roleInstance|
|ExpressRouteGatewayPacketsPerSecond|예|초당 패킷 수|초당 개수|평균|ExpressRoute 게이트웨이의 패킷 수|roleInstance|
|MmsaCount|Yes|터널 MMSA 수|개수|합계|MMSA 수|ConnectionName, RemoteIP, Instance|
|P2SBandwidth|예|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 지점 및 사이트 간 대역폭(바이트)|인스턴스|
|P2SConnectionCount|예|P2S 연결 수|개수|합계|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜, 인스턴스|
|QmsaCount|Yes|터널 QMSA 수|개수|합계|QMSA 수|ConnectionName, RemoteIP, Instance|
|TunnelAverageBandwidth|예|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP, Instance|
|TunnelEgressBytes|예|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropCount|Yes|Tunnel Egress 패킷 드롭 횟수|개수|합계|터널에서 드롭된 발신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropTSMismatch|예|터널 송신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP, Instance|
|TunnelEgressPackets|예|터널 송신 패킷|개수|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressBytes|예|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropCount|Yes|터널 수신 패킷 드롭 수|개수|합계|터널에서 드롭된 수신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropTSMismatch|예|터널 수신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressPackets|예|터널 수신 패킷|개수|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelNatAllocations|예|터널 NAT 할당|개수|합계|터널의 NAT 규칙에 대한 할당 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedBytes|예|터널 NATed 바이트 수|바이트|합계|NAT 규칙에 따라 터널에서 NAT가 설정된 바이트 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedPackets|예|터널 NAT 패킷|개수|합계|NAT 규칙에 따라 터널에서 NAT가 설정된 패킷 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatFlowCount|예|터널 NAT 흐름|개수|합계|흐름 유형 및 NAT 규칙별 터널의 NAT 흐름 수|NatRule, FlowType, ConnectionName, RemoteIP, Instance|
|TunnelNatPacketDrop|예|터널 NAT 패킷 삭제|개수|합계|터널에서 NAT가 설정된 패킷 중 삭제 유형 및 NAT 규칙에 따라 삭제된 패킷 수|NatRule, DropType, ConnectionName, RemoteIP, Instance|
|TunnelPeakPackets|Yes|터널 최고 PPS|개수|최대|초당 터널 최대 패킷|ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedBytes|예|터널 역방향 NAT 바이트 수|바이트|합계|NAT 규칙에 따라 터널에서 역방향 NAT가 설정된 바이트 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedPackets|예|터널 역방향 NAT 패킷 수|개수|합계|NAT 규칙에 따라 역방향 NAT가 설정된 터널의 패킷 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelTotalFlowCount|Yes|터널 총 흐름 수|개수|합계|터널의 총 흐름 수|ConnectionName, RemoteIP, Instance|
|UserVpnRouteCount|No|사용자 Vpn 경로 수|개수|합계|게이트웨이에서 학습한 P2S 사용자 Vpn 경로 수|RouteType, Instance|
|VnetAddressPrefixCount|Yes|VNet 주소 접두사 수|개수|합계|게이트웨이의 Vnet 주소 접두사 수|인스턴스|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesDroppedDDoS|예|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|ProtectedIPAddress|
|BytesForwardedDDoS|예|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|ProtectedIPAddress|
|BytesInDDoS|예|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|ProtectedIPAddress|
|DDoSTriggerSYNPackets|예|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|ProtectedIPAddress|
|DDoSTriggerTCPPackets|예|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|ProtectedIPAddress|
|DDoSTriggerUDPPackets|예|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|ProtectedIPAddress|
|IfUnderDDoSAttack|예|DDoS 공격 진행 여부|개수|최대|DDoS 공격 진행 여부|ProtectedIPAddress|
|PacketsDroppedDDoS|예|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|ProtectedIPAddress|
|PacketsForwardedDDoS|예|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|ProtectedIPAddress|
|PacketsInDDoS|예|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|ProtectedIPAddress|
|PingMeshAverageRoundtripMs|예|VM에 대한 Ping 왕복 시간|밀리초|평균|대상 VM에 전송된 Ping의 왕복 시간|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|예|VM에 대한 Ping 실패|백분율|평균|대상 VM에 전송된 총 Ping과 실패한 Ping 수의 백분율|SourceCustomerAddress, DestinationCustomerAddress|
|TCPBytesDroppedDDoS|예|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|ProtectedIPAddress|
|TCPBytesForwardedDDoS|예|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|ProtectedIPAddress|
|TCPBytesInDDoS|예|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|ProtectedIPAddress|
|TCPPacketsDroppedDDoS|예|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|ProtectedIPAddress|
|TCPPacketsForwardedDDoS|예|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|ProtectedIPAddress|
|TCPPacketsInDDoS|예|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|ProtectedIPAddress|
|UDPBytesDroppedDDoS|예|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|ProtectedIPAddress|
|UDPBytesForwardedDDoS|예|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|ProtectedIPAddress|
|UDPBytesInDDoS|예|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|ProtectedIPAddress|
|UDPPacketsDroppedDDoS|예|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|ProtectedIPAddress|
|UDPPacketsForwardedDDoS|예|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|ProtectedIPAddress|
|UDPPacketsInDDoS|예|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|ProtectedIPAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PeeringAvailability|예|Bgp 가용성|백분율|평균|VirtualRouter와 원격 피어 간의 BGP 가용성|Peer|


## <a name="microsoftnetworkvpngateways"></a>microsoft.network/vpngateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageBandwidth|예|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 사이트 간 대역폭(바이트)|인스턴스|
|BgpPeerStatus|No|BGP 피어 상태|개수|평균|BGP 피어의 상태|BgpPeerAddress, Instance|
|BgpRoutesAdvertised|Yes|보급된 BGP 경로|개수|합계|터널을 통해 보급된 Bgp 경로 수|BgpPeerAddress, Instance|
|BgpRoutesLearned|Yes|학습된 BGP 경로|개수|합계|터널을 통해 학습된 Bgp 경로 수|BgpPeerAddress, Instance|
|MmsaCount|Yes|터널 MMSA 수|개수|합계|MMSA 수|ConnectionName, RemoteIP, Instance|
|QmsaCount|Yes|터널 QMSA 수|개수|합계|QMSA 수|ConnectionName, RemoteIP, Instance|
|TunnelAverageBandwidth|예|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP, Instance|
|TunnelEgressBytes|예|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropCount|Yes|Tunnel Egress 패킷 드롭 횟수|개수|합계|터널에서 드롭된 발신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelEgressPacketDropTSMismatch|예|터널 송신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP, Instance|
|TunnelEgressPackets|예|터널 송신 패킷|개수|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressBytes|예|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropCount|Yes|터널 수신 패킷 드롭 수|개수|합계|터널에서 드롭된 수신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressPacketDropTSMismatch|예|터널 수신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP, Instance|
|TunnelIngressPackets|예|터널 수신 패킷|개수|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP, Instance|
|TunnelNatAllocations|예|터널 NAT 할당|개수|합계|터널의 NAT 규칙에 대한 할당 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedBytes|예|터널 NATed 바이트 수|바이트|합계|NAT 규칙에 따라 터널에서 NAT가 설정된 바이트 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatedPackets|예|터널 NAT 패킷|개수|합계|NAT 규칙에 따라 터널에서 NAT가 설정된 패킷 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelNatFlowCount|예|터널 NAT 흐름|개수|합계|흐름 유형 및 NAT 규칙별 터널의 NAT 흐름 수|NatRule, FlowType, ConnectionName, RemoteIP, Instance|
|TunnelNatPacketDrop|예|터널 NAT 패킷 삭제|개수|합계|터널에서 NAT가 설정된 패킷 중 삭제 유형 및 NAT 규칙에 따라 삭제된 패킷 수|NatRule, DropType, ConnectionName, RemoteIP, Instance|
|TunnelPeakPackets|Yes|터널 최고 PPS|개수|최대|초당 터널 최대 패킷|ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedBytes|예|터널 역방향 NAT 바이트 수|바이트|합계|NAT 규칙에 따라 터널에서 역방향 NAT가 설정된 바이트 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelReverseNatedPackets|예|터널 역방향 NAT 패킷 수|개수|합계|NAT 규칙에 따라 역방향 NAT가 설정된 터널의 패킷 수|NatRule, ConnectionName, RemoteIP, Instance|
|TunnelTotalFlowCount|Yes|터널 총 흐름 수|개수|합계|터널의 총 흐름 수|ConnectionName, RemoteIP, Instance|
|VnetAddressPrefixCount|Yes|VNet 주소 접두사 수|개수|합계|게이트웨이의 Vnet 주소 접두사 수|인스턴스|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|incoming|예|들어오는 메시지|개수|합계|성공한 모든 API 호출 전송의 수입니다. |차원 없음|
|incoming.all.failedrequests|예|들어오는 모든 실패한 요청|개수|합계|알림 허브에 대해 들어오는 실패한 전체 요청|차원 없음|
|incoming.all.requests|예|들어오는 모든 요청|개수|합계|알림 허브에 대해 들어오는 전체 요청|차원 없음|
|incoming.scheduled|예|전송된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|incoming.scheduled.cancel|예|취소된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|installation.all|예|설치 관리 작업|개수|합계|설치 관리 작업|차원 없음|
|installation.delete|예|설치 작업 삭제|개수|합계|설치 작업 삭제|차원 없음|
|installation.get|예|설치 작업 가져오기|개수|합계|설치 작업 가져오기|차원 없음|
|installation.patch|예|설치 작업 패치|개수|합계|설치 작업 패치|차원 없음|
|installation.upsert|예|설치 작업 만들기 또는 업데이트|개수|합계|설치 작업 만들기 또는 업데이트|차원 없음|
|notificationhub.pushes|예|모든 나가는 알림 수|개수|합계|알림 허브의 모든 나가는 알림 수|차원 없음|
|outgoing.allpns.badorexpiredchannel|예|잘못되거나 만료된 채널 오류|개수|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.channelerror|예|채널 오류|개수|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.invalidpayload|예|페이로드 오류|개수|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.pnserror|예|외부 알림 시스템 오류|개수|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.success|예|성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.apns.badchannel|예|APNS 잘못된 채널 오류|개수|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|차원 없음|
|outgoing.apns.expiredchannel|예|APNS 만료된 채널 오류|개수|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|차원 없음|
|outgoing.apns.invalidcredentials|예|APNS 권한 부여 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.invalidnotificationsize|예|APNS 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|차원 없음|
|outgoing.apns.pnserror|예|APNS 오류|개수|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.success|예|APNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.authenticationerror|예|GCM 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|차원 없음|
|outgoing.gcm.badchannel|예|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|차원 없음|
|outgoing.gcm.expiredchannel|예|GCM 만료된 채널 오류|개수|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|차원 없음|
|outgoing.gcm.invalidcredentials|예|GCM 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.invalidnotificationformat|예|GCM 잘못된 알림 형식|개수|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|차원 없음|
|outgoing.gcm.invalidnotificationsize|예|GCM 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|차원 없음|
|outgoing.gcm.pnserror|예|GCM 오류|개수|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.success|예|GCM 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.throttled|예|GCM 제한된 알림|개수|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|차원 없음|
|outgoing.gcm.wrongchannel|예|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|차원 없음|
|outgoing.mpns.authenticationerror|예|MPNS 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.badchannel|예|MPNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.mpns.channeldisconnected|예|MPNS 채널 연결 끊김|개수|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|차원 없음|
|outgoing.mpns.dropped|예|MPNS 삭제된 알림|개수|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|차원 없음|
|outgoing.mpns.invalidcredentials|예|MPNS 잘못된 자격 증명|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.invalidnotificationformat|예|MPNS 잘못된 알림 형식|개수|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.pnserror|예|MPNS 오류|개수|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.success|예|MPNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.mpns.throttled|예|MPNS 제한된 알림|개수|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|차원 없음|
|outgoing.wns.authenticationerror|예|WNS 인증 오류|개수|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.badchannel|예|WNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.wns.channeldisconnected|예|WNS 채널 연결 끊김|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.channelthrottled|예|WNS 채널 제한|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|차원 없음|
|outgoing.wns.dropped|예|WNS 삭제된 알림|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.expiredchannel|예|WNS 만료된 채널 오류|개수|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|차원 없음|
|outgoing.wns.invalidcredentials|예|WNS 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|차원 없음|
|outgoing.wns.invalidnotificationformat|예|WNS 잘못된 알림 형식|개수|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|차원 없음|
|outgoing.wns.invalidnotificationsize|예|WNS 잘못된 알림 크기 오류|개수|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|차원 없음|
|outgoing.wns.invalidtoken|예|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|차원 없음|
|outgoing.wns.pnserror|예|WNS 오류|개수|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.success|예|WNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.wns.throttled|예|WNS 제한된 알림|개수|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|차원 없음|
|outgoing.wns.tokenproviderunreachable|예|WNS 권한 부여 오류(연결할 수 없음)|개수|합계|Windows Live에 연결할 수 없습니다.|차원 없음|
|outgoing.wns.wrongtoken|예|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공된 토큰은 유효하지만 다른 애플리케이션에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|차원 없음|
|registration.all|예|등록 작업|개수|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |차원 없음|
|registration.create|예|등록 만들기 작업|개수|합계|성공한 모든 등록 만들기의 수입니다.|차원 없음|
|registration.delete|예|등록 삭제 작업|개수|합계|성공한 모든 등록 삭제의 수입니다.|차원 없음|
|registration.get|예|등록 읽기 작업|개수|합계|성공한 모든 등록 쿼리의 수입니다.|차원 없음|
|registration.update|예|등록 업데이트 작업|개수|합계|성공한 모든 등록 업데이트의 수입니다.|차원 없음|
|scheduled.pending|예|보류 중인 예약된 알림|개수|합계|보류 중인 예약된 알림|차원 없음|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|Average_% Available Memory|예|% 사용 가능한 메모리|개수|평균|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|예|% 사용 가능한 스왑 공간|개수|평균|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|예|% Committed Bytes In Use|개수|평균|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|예|% DPC 시간|개수|평균|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|예|% 사용 가능한 Inodes|개수|평균|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|예|% 사용 가능한 공간|개수|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|예|% 유휴 시간|개수|평균|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|예|% 인터럽트 시간|개수|평균|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|예|% IO 대기 시간|개수|평균|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|예|% Nice 시간|개수|평균|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|예|% 권한이 부여된 시간|개수|평균|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|예|% Processor Time|개수|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|예|% 사용된 Inodes|개수|평균|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|예|% 사용된 메모리|개수|평균|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|예|% 사용된 공간|개수|평균|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|예|% 사용된 스왑 공간|개수|평균|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|예|% 사용자 시간|개수|평균|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|예|Available MBytes|개수|평균|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|예|사용 가능한 MB 메모리|개수|평균|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|예|사용 가능한 MB 스왑|개수|평균|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|예|평균 디스크 초/읽기|개수|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/전송|예|평균 디스크 초/전송|개수|평균|Average_Avg. 디스크 초/전송|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|예|평균 디스크 초/쓰기|개수|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|예|Bytes Received/sec|개수|평균|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|예|Bytes Sent/sec|개수|평균|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|예|Bytes Total/sec|개수|평균|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|예|Current Disk Queue Length|개수|평균|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|예|디스크 읽기 바이트/초|개수|평균|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|예|디스크 읽기/초|개수|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|예|디스크 전송/초|개수|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|예|디스크 쓰기 바이트/초|개수|평균|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|예|디스크 쓰기/초|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|예|사용 가능한 메가바이트|개수|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|예|사용 가능한 실제 메모리|개수|평균|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|예|페이징 파일에 사용 가능한 공간|개수|평균|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|예|사용 가능한 가상 메모리|개수|평균|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|예|논리 디스크 바이트/초|개수|평균|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|예|페이지 읽기/초|개수|평균|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|예|페이지 쓰기/초|개수|평균|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|예|페이지/초|개수|평균|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|예|Pct 권한이 부여된 시간|개수|평균|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|예|Pct 사용자 시간|개수|평균|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|예|물리적 디스크 바이트/초|개수|평균|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|예|프로세스|개수|평균|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|예|프로세서 큐 길이|개수|평균|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|예|페이징 파일에 저장된 크기|개수|평균|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|예|총 바이트|개수|평균|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|예|받은 총 바이트|개수|평균|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|예|전송된 총 바이트|개수|평균|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|예|총 충돌|개수|평균|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|예|받은 총 패킷|개수|평균|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|예|전송된 총 패킷|개수|평균|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|예|총 Rx 오류|개수|평균|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|예|총 Tx 오류|개수|평균|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|예|작동 시간|개수|평균|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|예|사용된 MB 스왑 공간|개수|평균|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|예|사용된 메모리 KB|개수|평균|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|예|사용된 메모리 MB|개수|평균|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|예|사용자|개수|평균|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|예|가상 공유 메모리|개수|평균|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|이벤트|예|이벤트|개수|평균|이벤트|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|하트비트|예|Heartbeat|개수|합계|하트비트|Computer, OSType, Version, SourceComputerId|
|업데이트|예|업데이트|개수|평균|업데이트|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|EgressTrafficRate|예|송신 트래픽 속도|BitsPerSecond|평균|송신 트래픽 속도(비트/초)|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|예|수신 트래픽 속도|BitsPerSecond|평균|수신 트래픽 속도(비트/초)|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|예|세션 가용성|개수|평균|피어링 세션의 가용성|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PrefixLatency|예|접두사 대기 시간|밀리초|평균|중앙값 접두사 대기 시간|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|예|메모리: 클리너 현재 가격|개수|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|차원 없음|
|CleanerMemoryNonshrinkable|예|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|차원 없음|
|CleanerMemoryShrinkable|예|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|차원 없음|
|CommandPoolBusyThreads|예|스레드: 명령 풀 사용 중인 스레드|개수|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|차원 없음|
|CommandPoolIdleThreads|예|스레드: 명령 풀 유휴 상태 스레드|개수|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|차원 없음|
|CommandPoolJobQueueLength|예|명령 풀의 작업 큐 길이|개수|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|cpu_metric|예|CPU(Gen2)|백분율|평균|CPU 사용률. Power BI Embedded 2세대 리소스에 대해서만 지원됩니다.|차원 없음|
|cpu_workload_metric|Yes|작업당 CPU(Gen2)|백분율|평균|작업당 CPU 사용률. Power BI Embedded 2세대 리소스에 대해서만 지원됩니다.|작업|
|CurrentConnections|예|연결: 현재 연결|개수|평균|현재 설정된 클라이언트 연결 수입니다.|차원 없음|
|CurrentUserSessions|예|현재 사용자 세션 수|개수|평균|현재 설정된 사용자 세션 수입니다.|차원 없음|
|LongParsingBusyThreads|예|스레드: 긴 구문 분석 사용 중인 스레드|개수|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|차원 없음|
|LongParsingIdleThreads|예|스레드: 긴 구문 분석 유휴 상태 스레드|개수|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|차원 없음|
|LongParsingJobQueueLength|예|스레드: 긴 구문 분석 작업 큐 길이|개수|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|memory_metric|예|메모리(Gen1)|바이트|평균|메모리. 범위 0-3GB(A1), 0-5GB(A2), 0-10GB(A3), 0-25GB(A4), 0-50GB(A5) 및 0-100GB(A6). Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|memory_thrashing_metric|예|메모리 스래싱(데이터 세트)(Gen1)|백분율|평균|평균 메모리 쓰래싱입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|MemoryLimitHard|예|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|차원 없음|
|MemoryLimitHigh|예|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|차원 없음|
|MemoryLimitLow|예|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|차원 없음|
|MemoryLimitVertiPaq|예|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|차원 없음|
|MemoryUsage|예|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|차원 없음|
|overload_metric|예|오버로드(Gen2)|개수|평균|리소스가 오버로드되면 1이고 그렇지 않으면 0입니다. Power BI Embedded 2세대 리소스에 대해서만 지원됩니다.|차원 없음|
|ProcessingPoolBusyIOJobThreads|예|스레드: 처리 풀 사용 중인 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|차원 없음|
|ProcessingPoolBusyNonIOThreads|예|스레드: 처리 풀 사용 중인 비-I/O 스레드|개수|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|차원 없음|
|ProcessingPoolIdleIOJobThreads|예|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|차원 없음|
|ProcessingPoolIdleNonIOThreads|예|스레드: 처리 풀 유휴 상태 비-I/O 스레드|개수|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|차원 없음|
|ProcessingPoolIOJobQueueLength|예|스레드: 처리 중인 풀 I/O 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|차원 없음|
|ProcessingPoolJobQueueLength|예|처리 풀의 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|차원 없음|
|qpu_high_utilization_metric|예|QPU 높은 사용률(Gen1)|개수|합계|지난 1분간 QPU 높은 사용률이며, 높은 QPU 사용률은 1, 그 외는 0입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|qpu_metric|예|QPU(Gen1)|개수|평균|QPU. A1의 범위는 0-20, A2는 0-40, A3은 0-40, A4는 0-80, A5는 0-160, A6은 0-320입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|QueryDuration|예|쿼리 기간(데이터 세트)(Gen1)|밀리초|평균|마지막 간격의 DAX 쿼리 기간입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|QueryPoolBusyThreads|예|쿼리 풀의 사용 중인 스레드|개수|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|차원 없음|
|QueryPoolIdleThreads|예|스레드: 쿼리 풀 유휴 상태 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|차원 없음|
|QueryPoolJobQueueLength|예|쿼리 풀 작업 큐 길이(데이터 세트)(Gen1)|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|차원 없음|
|할당량|예|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|차원 없음|
|QuotaBlocked|예|메모리: 차단된 할당량|개수|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|차원 없음|
|RowsConvertedPerSec|예|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|차원 없음|
|RowsReadPerSec|예|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|차원 없음|
|RowsWrittenPerSec|예|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|차원 없음|
|ShortParsingBusyThreads|예|스레드: 짧은 구문 분석 사용 중인 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|차원 없음|
|ShortParsingIdleThreads|예|스레드: 짧은 구문 분석 유휴 상태 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|차원 없음|
|ShortParsingJobQueueLength|예|스레드: 짧은 구문 분석 작업 큐 길이|개수|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|SuccessfullConnectionsPerSec|예|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|차원 없음|
|TotalConnectionFailures|예|총 연결 실패 수|개수|평균|실패한 총 연결 시도 수입니다.|차원 없음|
|TotalConnectionRequests|예|총 연결 요청 수|개수|평균|도착한 총 연결 요청 수입니다.|차원 없음|
|VertiPaqNonpaged|예|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|차원 없음|
|VertiPaqPaged|예|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|차원 없음|
|workload_memory_metric|Yes|작업당 메모리(Gen1)|바이트|평균|작업당 메모리. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|작업|
|workload_qpu_metric|Yes|작업당 QPU(Gen1)|개수|평균|작업당 QPU. A1의 범위는 0-20, A2는 0-40, A3은 0-40, A4는 0-80, A5는 0-160, A6은 0-320입니다. Power BI Embedded 1세대 리소스에 대해서만 지원됩니다.|작업|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ScanCancelled|예|취소된 검사|개수|합계|취소된 검사 수를 나타냅니다.|차원 없음|
|ScanCompleted|예|완료된 검사|개수|합계|성공적으로 완료된 검사 수를 나타냅니다.|차원 없음|
|ScanFailed|예|실패한 검사|개수|합계|실패한 검사 수를 나타냅니다.|차원 없음|
|ScanTimeTaken|예|소요된 검사 시간|초|합계|총 검사 시간(초)을 나타냅니다.|차원 없음|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BackupHealthEvent|예|백업 상태 이벤트(미리 보기)|개수|개수|백업 작업 상태와 관련된 상태 이벤트의 수|dataSourceURL, backupInstanceUrl, dataSourceType, healthStatus, backupInstanceName|
|RestoreHealthEvent|예|상태 이벤트 복원(미리 보기)|개수|개수|복원 작업 상태와 관련된 상태 이벤트의 수|dataSourceURL, backupInstanceUrl, dataSourceType, healthStatus, backupInstanceName|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|예|ActiveConnections|개수|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|예|ActiveListeners|개수|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|예|BytesTransferred|바이트|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerConnections-ClientError|예|ListenerConnections-ClientError|개수|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|EntityName, OperationResult|
|ListenerConnections-ServerError|예|ListenerConnections-ServerError|개수|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|EntityName, OperationResult|
|ListenerConnections-Success|예|ListenerConnections-Success|개수|합계|Microsoft.Relay의 성공적인 ListenerConnections.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|예|ListenerConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|ListenerDisconnects|예|ListenerDisconnects|개수|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderConnections-ClientError|예|SenderConnections-ClientError|개수|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|EntityName, OperationResult|
|SenderConnections-ServerError|예|SenderConnections-ServerError|개수|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|EntityName, OperationResult|
|SenderConnections-Success|예|SenderConnections-Success|개수|합계|Microsoft.Relay의 성공적인 SenderConnections.|EntityName, OperationResult|
|SenderConnections-TotalRequests|예|SenderConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|SenderDisconnects|예|SenderDisconnects|개수|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|대기 시간|No|대기 시간|초|평균|Azure Resource Manager에 대 한 모든 요청에 대 한 대기 시간 데이터|IsCustomerOriginated, Method, Namespace, RequestRegion, ResourceType, StatusCode, StatusCodeClass, Microsoft. SubscriptionId|
|트래픽|아니요|트래픽|개수|개수|Azure Resource Manager에 대 한 모든 요청에 대 한 트래픽 데이터|IsCustomerOriginated, Method, Namespace, RequestRegion, ResourceType, StatusCode, StatusCodeClass, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DocumentsProcessedCount|예|처리된 문서 수|개수|합계|처리된 문서 수|DataSourceName, Failed, IndexerName, IndexName, SkillsetName|
|SearchLatency|예|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|차원 없음|
|SearchQueriesPerSecond|예|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|차원 없음|
|SkillExecutionCount|예|기술 실행 호출 수|개수|합계|기술 실행 수|DataSourceName, Failed, IndexerName, SkillName, SkillsetName, SkillType|
|ThrottledSearchQueriesPercentage|예|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|차원 없음|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|예|ActiveConnections|개수|합계|Microsoft.ServiceBus에 대한 총 활성 연결.|차원 없음|
|ActiveMessages|예|큐/토픽에 있는 활성 메시지 수|개수|평균|큐/토픽에 있는 활성 메시지 수|EntityName|
|ConnectionsClosed|예|끊어진 연결.|개수|평균|Microsoft.ServiceBus에 대한 끊어진 연결.|EntityName|
|ConnectionsOpened|예|열린 연결.|개수|평균|Microsoft.ServiceBus에 대한 열린 연결.|EntityName|
|CPUXNS|예|CPU(사용되지 않음)|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용량 메트릭. 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 CPU 메트릭(NamespaceCpuUsage)을 사용하세요.|복제본|
|DeadletteredMessages|예|큐/토픽에서 배달 못한 메시지 수입니다.|개수|평균|큐/토픽에서 배달 못한 메시지 수입니다.|EntityName|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.ServiceBus에 대한 들어오는 메시지.|EntityName|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.ServiceBus에 대한 들어오는 요청.|EntityName|
|메시지|예|큐/토픽에 있는 메시지 수|개수|평균|큐/토픽에 있는 메시지 수|EntityName|
|NamespaceCpuUsage|예|CPU|백분율|최대|서비스 버스 프리미엄 네임스페이스 CPU 사용량 메트릭.|복제본|
|NamespaceMemoryUsage|예|메모리 사용량|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용량 메트릭|복제본|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.ServiceBus에 대한 보내는 메시지.|EntityName|
|ScheduledMessages|예|큐/토픽에서 예약된 메시지 수입니다.|개수|평균|큐/토픽에서 예약된 메시지 수입니다.|EntityName|
|ServerErrors|예|서버 오류.|개수|합계|Microsoft.ServiceBus에 대한 서버 오류.|EntityName, OperationResult|
|크기|예|크기|바이트|평균|큐/토픽 크기(바이트)|EntityName|
|SuccessfulRequests|예|성공한 요청|개수|합계|네임스페이스에 대한 총 성공한 요청|EntityName, OperationResult|
|ThrottledRequests|예|제한된 요청.|개수|합계|Microsoft.ServiceBus에 대한 제한된 요청.|EntityName, OperationResult|
|UserErrors|예|사용자 오류.|개수|합계|Microsoft.ServiceBus에 대한 사용자 오류.|EntityName, OperationResult|
|WSXNS|예|메모리 사용량(사용되지 않음)|백분율|최대|서비스 버스 프리미엄 네임스페이스 메모리 사용량 메트릭 이 메트릭은 사용되지 않습니다. 이 메트릭 대신 메모리 사용량(NamespaceMemoryUsage) 메트릭을 사용하세요.|복제본|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ConnectionCount|예|연결 수|개수|최대|사용자 연결의 양.|엔드포인트|
|InboundTraffic|예|인바운드 트래픽|바이트|합계|서비스의 인바운드 트래픽|차원 없음|
|MessageCount|예|메시지 수|개수|합계|총 메시지 양|차원 없음|
|OutboundTraffic|예|아웃바운드 트래픽|바이트|합계|서비스의 아웃바운드 트래픽|차원 없음|
|SystemErrors|예|시스템 오류|백분율|최대|시스템 오류의 비율|차원 없음|
|UserErrors|예|사용자 오류|백분율|최대|사용자 오류의 비율|차원 없음|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|InboundTraffic|예|인바운드 트래픽|바이트|합계|서비스 외부에서 내부로 시작되는 트래픽. 트래픽의 모든 바이트를 추가하여 집계됩니다.|차원 없음|
|OutboundTraffic|예|아웃바운드 트래픽|바이트|합계|서비스 내부에서 외부로 시작되는 트래픽. 트래픽의 모든 바이트를 추가하여 집계됩니다.|차원 없음|
|TotalConnectionCount|예|연결 수|개수|최대|서비스에 설정된 사용자 연결 수. 모든 온라인 연결을 추가하여 집계됩니다.|차원 없음|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|avg_cpu_percent|예|평균 CPU 비율|백분율|평균|평균 CPU 비율|차원 없음|
|io_bytes_read|예|읽은 IO 바이트|바이트|평균|읽은 IO 바이트|차원 없음|
|io_bytes_written|예|기록된 IO 바이트|바이트|평균|기록된 IO 바이트|차원 없음|
|io_requests|예|IO 요청 수|개수|평균|IO 요청 수|차원 없음|
|reserved_storage_mb|예|예약된 스토리지 공간|개수|평균|예약된 스토리지 공간|차원 없음|
|storage_space_used_mb|예|사용된 스토리지 공간|개수|평균|사용된 스토리지 공간|차원 없음|
|virtual_core_count|예|가상 코어 수|개수|평균|가상 코어 수|차원 없음|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_queries|예|활성 쿼리|개수|합계|모든 워크로드 그룹의 활성 쿼리. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|allocated_data_storage|예|할당된 데이터 공간|바이트|평균|할당된 데이터 스토리지. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|app_cpu_billed|예|청구된 앱 CPU|개수|합계|요금이 청구된 앱 CPU. 서버리스 데이터베이스에 적용됩니다.|차원 없음|
|app_cpu_percent|예|앱 CPU 백분율|백분율|평균|앱 CPU 백분율. 서버리스 데이터베이스에 적용됩니다.|차원 없음|
|app_memory_percent|예|앱 메모리 비율|백분율|평균|앱 메모리 백분율. 서버리스 데이터베이스에 적용됩니다.|차원 없음|
|base_blob_size_bytes|예|기본 Blob 스토리지 크기|바이트|최대|기본 Blob 스토리지 크기. 하이퍼스케일 데이터베이스에 적용됩니다.|차원 없음|
|blocked_by_firewall|예|방화벽에 의해 차단|개수|합계|방화벽에 의해 차단|차원 없음|
|cache_hit_percent|예|캐시 적중 비율|백분율|최대|캐시 적중 비율. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|cache_used_percent|예|캐시 사용 비율|백분율|최대|캐시 사용 비율. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|connection_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|connection_successful|예|성공적인 연결|개수|합계|성공적인 연결|차원 없음|
|cpu_limit|예|CPU 제한|개수|평균|CPU 제한. vCore 기반 데이터베이스에 적용됩니다.|차원 없음|
|cpu_percent|예|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|cpu_used|예|사용된 CPU|개수|평균|사용된 CPU. vCore 기반 데이터베이스에 적용됩니다.|차원 없음|
|교착 상태|예|교착 상태|개수|합계|교착 상태. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|delta_num_of_bytes_read|예|원격 데이터 읽기|바이트|합계|원격 데이터 읽기(바이트)|차원 없음|
|delta_num_of_bytes_total|예|읽고 쓴 총 원격 바이트 수|바이트|합계|컴퓨팅에서 읽고 쓴 총 원격 바이트 수|차원 없음|
|delta_num_of_bytes_written|예|원격 로그 쓰기|바이트|합계|원격 로그 쓰기(바이트)|차원 없음|
|diff_backup_size_bytes|예|차등 백업 스토리지 크기|바이트|최대|누적 차등 백업 스토리지 크기. vCore 기반 데이터베이스에 적용됩니다. 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|차원 없음|
|dtu_consumption_percent|예|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 데이터베이스에 적용됩니다.|차원 없음|
|dtu_limit|예|DTU 제한|개수|평균|DTU 제한. DTU 기반 데이터베이스에 적용됩니다.|차원 없음|
|dtu_used|예|DTU 사용됨|개수|평균|사용한 DTU. DTU 기반 데이터베이스에 적용됩니다.|차원 없음|
|dwu_consumption_percent|예|DWU 백분율|백분율|최대|DWU 백분율 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|dwu_limit|예|DWU 제한|개수|최대|DWU 제한. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|dwu_used|예|DWU 사용됨|개수|최대|사용한 DWU. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|full_backup_size_bytes|예|전체 백업 스토리지 크기|바이트|최대|누적 전체 백업 스토리지 크기. vCore 기반 데이터베이스에 적용됩니다. 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|차원 없음|
|local_tempdb_usage_percent|예|로컬 tempdb 백분율|백분율|평균|로컬 tempdb 백분율. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|log_backup_size_bytes|예|로그 백업 스토리지 크기|바이트|최대|누적 로그 백업 스토리지 크기. vCore 기반 및 하이퍼스케일 데이터베이스에 적용됩니다.|차원 없음|
|log_write_percent|예|로그 IO 비율|백분율|평균|로그 IO 백분율. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|memory_usage_percent|예|메모리 백분율|백분율|최대|메모리 백분율. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|physical_data_read_percent|예|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|queued_queries|예|큐에 대기 중인 쿼리|개수|합계|모든 워크로드 그룹의 큐에 대기 중인 쿼리. 데이터 웨어하우스에만 적용됩니다.|차원 없음|
|sessions_percent|예|세션 백분율|백분율|평균|세션 백분율. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|snapshot_backup_size_bytes|예|스냅샷 백업 스토리지 크기|바이트|최대|누적 스냅샷 백업 스토리지 크기. 하이퍼스케일 데이터베이스에 적용됩니다.|차원 없음|
|sqlserver_process_core_percent|예|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타내는 CPU 사용량. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|sqlserver_process_memory_percent|예|SQL Server 프로세스 메모리 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타내는 메모리 사용량. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|스토리지|예|사용된 데이터 공간|바이트|최대|사용된 데이터 공간. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|storage_percent|예|사용된 데이터 공간 백분율|백분율|최대|사용된 데이터 공간 백분율. 데이터 웨어하우스 또는 하이퍼스케일 데이터베이스에는 적용되지 않습니다.|차원 없음|
|tempdb_data_size|예|Tempdb 데이터 파일 크기(KB)|개수|최대|tempdb 데이터 파일에 사용되는 공간(KB). 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|tempdb_log_size|예|Tempdb 로그 파일 크기(KB)|개수|최대|tempdb 트랜잭션 로그 파일에 사용되는 공간(KB). 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|tempdb_log_used_percent|예|로그가 사용된 Tempdb 백분율|백분율|최대|tempdb 트랜잭션 로그 파일에 사용되는 공간 백분율. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|wlg_active_queries|예|작업 그룹 활성 쿼리|개수|합계|워크로드 그룹 내의 활성 쿼리. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|예|작업 그룹 쿼리 시간 제한|개수|합계|시간이 초과된 워크로드 그룹에 대한 쿼리. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|예|시스템 비율별 작업 그룹 할당|백분율|최대|워크로드 그룹당 전체 시스템을 기준으로 할당된 리소스 백분율. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|예|상한 리소스 기준 워크로드 그룹 할당 백분율|백분율|최대|워크로드 그룹별로 지정된 상한 리소스를 기준으로 할당된 리소스 백분율. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|예|유효 상한 리소스 비율|백분율|최대|다른 워크로드 그룹에 할당된 유효 최소 리소스 백분율율 고려하여 워크로드 그룹에 허용된 리소스 백분율에 대한 엄격한 제한. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|예|유효 최소 리소스 비율|백분율|최대|최소 서비스 수준을 고려하여 워크로드 그룹에 대해 예약되고 격리되는 리소스의 최소 백분율. 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|예|작업 그룹 큐에 대기 중인 쿼리|개수|합계|워크로드 그룹 내에서 큐에 대기 중인 쿼리 데이터 웨어하우스에만 적용됩니다.|WorkloadGroupName, IsUserDefined|
|workers_percent|예|작업자 백분율|백분율|평균|작업자 백분율. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|
|xtp_storage_percent|예|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 백분율. 데이터 웨어하우스에는 적용되지 않습니다.|차원 없음|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|allocated_data_storage|예|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|차원 없음|
|allocated_data_storage_percent|예|데이터 공간 할당 백분율|백분율|최대|데이터 공간 할당 백분율|차원 없음|
|cpu_limit|예|CPU 제한|개수|평균|CPU 제한. vCore 기반 탄력적 풀에 적용됩니다.|차원 없음|
|cpu_percent|예|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|cpu_used|예|사용된 CPU|개수|평균|사용된 CPU. vCore 기반 탄력적 풀에 적용됩니다.|차원 없음|
|database_allocated_data_storage|예|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|DatabaseResourceId|
|database_cpu_limit|예|CPU 제한|개수|평균|CPU 제한|DatabaseResourceId|
|database_cpu_percent|예|CPU 비율|백분율|평균|CPU 비율|DatabaseResourceId|
|database_cpu_used|예|사용된 CPU|개수|평균|사용된 CPU|DatabaseResourceId|
|database_dtu_consumption_percent|예|DTU 비율|백분율|평균|DTU 비율|DatabaseResourceId|
|database_eDTU_used|예|eDTU 사용|개수|평균|eDTU 사용|DatabaseResourceId|
|database_log_write_percent|예|로그 IO 비율|백분율|평균|로그 IO 비율|DatabaseResourceId|
|database_physical_data_read_percent|예|데이터 IO 비율|백분율|평균|데이터 IO 비율|DatabaseResourceId|
|database_sessions_percent|예|세션 백분율|백분율|평균|세션 백분율|DatabaseResourceId|
|database_storage_used|예|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|DatabaseResourceId|
|database_workers_percent|예|작업자 백분율|백분율|평균|작업자 백분율|DatabaseResourceId|
|dtu_consumption_percent|예|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 탄력적 풀에 적용됩니다.|차원 없음|
|eDTU_limit|예|eDTU 제한|개수|평균|eDTU 제한. DTU 기반 탄력적 풀에 적용됩니다.|차원 없음|
|eDTU_used|예|eDTU 사용|개수|평균|사용된 eDTU. DTU 기반 탄력적 풀에 적용됩니다.|차원 없음|
|log_write_percent|예|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|physical_data_read_percent|예|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|sessions_percent|예|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|sqlserver_process_core_percent|예|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타내는 CPU 사용량. 탄력적 풀에 적용됩니다.|차원 없음|
|sqlserver_process_memory_percent|예|SQL Server 프로세스 메모리 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타내는 메모리 사용량. 탄력적 풀에 적용됩니다.|차원 없음|
|storage_limit|예|데이터 최대 크기|바이트|평균|데이터 최대 크기|차원 없음|
|storage_percent|예|사용된 데이터 공간 백분율|백분율|평균|사용된 데이터 공간 백분율|차원 없음|
|storage_used|예|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|차원 없음|
|tempdb_data_size|예|Tempdb 데이터 파일 크기(KB)|개수|최대|tempdb 데이터 파일에 사용되는 공간(KB).|차원 없음|
|tempdb_log_size|예|Tempdb 로그 파일 크기(KB)|개수|최대|tempdb 트랜잭션 로그 파일에 사용되는 공간(KB).|차원 없음|
|tempdb_log_used_percent|예|로그가 사용된 Tempdb 백분율|백분율|최대|tempdb 트랜잭션 로그 파일에 사용되는 공간 백분율|차원 없음|
|workers_percent|예|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|xtp_storage_percent|예|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 백분율|차원 없음|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수에는 Azure Storage에서 외부 클라이언트로의 송신뿐 아니라 Azure 내의 송신도 포함됩니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|예|사용된 용량|바이트|평균|스토리지 계정에서 사용한 스토리지 양입니다. 표준 스토리지 계정의 경우 이는 Blob, 테이블, 파일 및 큐에서 사용한 용량의 합계입니다. 프리미엄 스토리지 계정 및 Blob 스토리지 계정의 경우 BlobCapacity 또는 FileCapacity와 동일합니다.|차원 없음|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|BlobCapacity|예|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트).|BlobType, 계층|
|BlobCount|예|Blob 수|개수|평균|스토리지 계정에 저장된 Blob 개체 수입니다.|BlobType, 계층|
|BlobProvisionedSize|예|Blob 프로비전 크기|바이트|평균|스토리지 계정의 Blob service에서 프로비전된 스토리지 양(바이트).|BlobType, 계층|
|ContainerCount|예|Blob 컨테이너 수|개수|평균|스토리지 계정의 컨테이너 수입니다.|차원 없음|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수에는 Azure Storage에서 외부 클라이언트로의 송신뿐 아니라 Azure 내의 송신도 포함됩니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|IndexCapacity|예|인덱스 용량|바이트|평균|Azure Data Lake Storage Gen2 계층 인덱스에서 사용하는 스토리지 양|차원 없음|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증, FileShare|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수에는 Azure Storage에서 외부 클라이언트로의 송신뿐 아니라 Azure 내의 송신도 포함됩니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증, FileShare|
|FileCapacity|예|파일 용량|바이트|평균|스토리지 계정에 사용한 File Storage 양입니다.|FileShare|
|FileCount|예|파일 수|개수|평균|스토리지 계정의 파일 수입니다.|FileShare|
|FileShareCapacityQuota|예|파일 공유 용량 할당량|바이트|평균|Azure Files 서비스에서 사용할 수 있는 스토리지 양(바이트)의 상한|FileShare|
|FileShareCount|예|파일 공유 수|개수|평균|스토리지 계정의 파일 공유 수입니다.|차원 없음|
|FileShareProvisionedIOPS|예|파일 공유 프로비저닝되는 IOPS|초당 개수|평균|프리미엄 파일 스토리지 계정의 프리미엄 파일 공유에 프로비저닝되는 기준 IOPS 수. 이 수는 공유 용량의 프로비전 크기(할당량)를 기반으로 계산됩니다.|FileShare|
|FileShareSnapshotCount|예|파일 공유 스냅샷 개수|개수|평균|스토리지 계정의 Files 서비스에서 공유에 있는 스냅샷 수.|FileShare|
|FileShareSnapshotSize|예|파일 공유 스냅샷 크기|바이트|평균|스토리지 계정의 File 서비스에서 스냅샷에 사용하는 스토리지 양(바이트).|FileShare|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증, FileShare|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증, FileShare|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증, FileShare|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, 인증, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수에는 Azure Storage에서 외부 클라이언트로의 송신뿐 아니라 Azure 내의 송신도 포함됩니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|QueueCapacity|예|큐 용량|바이트|평균|스토리지 계정에 사용한 Queue Storage 양입니다.|차원 없음|
|QueueCount|예|큐 수|개수|평균|스토리지 계정의 큐 수입니다.|차원 없음|
|QueueMessageCount|예|큐 메시지 수|개수|평균|스토리지 계정의 만료되지 않은 큐 메시지 수입니다.|차원 없음|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|가용성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수에는 Azure Storage에서 외부 클라이언트로의 송신뿐 아니라 Azure 내의 송신도 포함됩니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|TableCapacity|예|테이블 용량|바이트|평균|스토리지 계정에 사용한 Table Storage 양입니다.|차원 없음|
|TableCount|예|테이블 수|개수|평균|스토리지 계정의 테이블 수입니다.|차원 없음|
|TableEntityCount|예|테이블 엔터티 수|개수|평균|스토리지 계정의 테이블 엔터티 수입니다.|차원 없음|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ClientIOPS|예|총 클라이언트 IOPS|개수|평균|캐시에서 처리하는 클라이언트 파일 작업의 속도|차원 없음|
|ClientLatency|예|평균 클라이언트 대기 시간|밀리초|평균|캐시에 대한 클라이언트 파일 작업의 평균 대기 시간|차원 없음|
|ClientLockIOPS|예|클라이언트 잠금 IOPS|초당 개수|평균|초당 클라이언트 파일 잠금 작업 수|차원 없음|
|ClientMetadataReadIOPS|예|클라이언트 메타데이터 읽기 IOPS|초당 개수|평균|데이터 읽기를 제외하고, 영구 상태를 수정하지 않는 캐시로 보낸 클라이언트 파일 작업의 속도|차원 없음|
|ClientMetadataWriteIOPS|예|클라이언트 메타데이터 쓰기 IOPS|초당 개수|평균|데이터 쓰기를 제외하고, 영구 상태를 수정하지 않는 캐시로 보낸 클라이언트 파일 작업의 속도|차원 없음|
|ClientReadIOPS|예|클라이언트 읽기 IOPS|초당 개수|평균|초당 클라이언트 읽기 작업 수|차원 없음|
|ClientReadThroughput|예|평균 캐시 읽기 처리량|초당 바이트 수|평균|클라이언트 읽기 데이터 전송 속도|차원 없음|
|ClientStatus|Yes|클라이언트 상태|개수|합계|클라이언트 연결 정보.|ClientSource, CacheAddress, ClientAddress, Protocol, ConnectionType|
|ClientWriteIOPS|예|클라이언트 쓰기 IOPS|초당 개수|평균|초당 클라이언트 쓰기 작업 수|차원 없음|
|ClientWriteThroughput|예|평균 캐시 쓰기 처리량|초당 바이트 수|평균|클라이언트 쓰기 데이터 전송 속도|차원 없음|
|FileOps|Yes|파일 작업|초당 개수|평균|초당 파일 작업 수.|SourceFile, Rank, FileType|
|FileReads|Yes|파일 읽기|초당 바이트 수|평균|파일에서 읽은 초당 바이트 수.|SourceFile, Rank, FileType|
|FileUpdates|Yes|파일 업데이트|초당 개수|평균|초당 디렉터리 업데이트 및 메타데이터 작업 수.|SourceFile, Rank, FileType|
|FileWrites|Yes|파일 쓰기|초당 바이트 수|평균|파일에 쓴 초당 바이트 수.|SourceFile, Rank, FileType|
|StorageTargetAsyncWriteThroughput|예|StorageTarget 비동기 쓰기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget에 비동기적으로 데이터를 쓰는 속도. 클라이언트를 차단하지 않는 편의적 쓰기입니다.|StorageTarget|
|StorageTargetBlocksRecycled|Yes|재활용된 스토리지 대상 블록|개수|평균|재활용된 스토리지 대상 당 16k 캐시 블록 (해제) 총 수.|StorageTarget|
|StorageTargetFillThroughput|예|StorageTarget 채우기 처리량|초당 바이트 수|평균|캐시 누락을 처리하기 위해 캐시에서 StorageTarget의 데이터를 읽는 속도|StorageTarget|
|StorageTargetFreeReadSpace|Yes|스토리지 대상 사용 가능한 읽기 공간|바이트|평균|스토리지 대상에 연결된 파일을 캐싱하는 데 사용할 수 있는 읽기 공간.|StorageTarget|
|StorageTargetFreeWriteSpace|Yes|스토리지 대상 사용 가능한 쓰기 공간|바이트|평균|스토리지 대상과 연결된 더티 데이터에 사용 가능한 쓰기 공간.|StorageTarget|
|StorageTargetHealth|예|스토리지 대상 상태|개수|평균|캐시와 스토리지 대상 간 연결 테스트의 부울 결과|차원 없음|
|StorageTargetIOPS|예|총 StorageTarget IOPS|개수|평균|캐시에서 특정 StorageTarget에 보내는 모든 파일 작업의 속도|StorageTarget|
|StorageTargetLatency|예|StorageTarget 대기 시간|밀리초|평균|캐시에서 특정 StorageTarget에 보내는 모든 파일 작업의 평균 왕복 대기 시간|StorageTarget|
|StorageTargetMetadataReadIOPS|예|StorageTarget 메타데이터 읽기 IOPS|초당 개수|평균|캐시에서 특정 StorageTarget에 보내는 읽기 작업을 제외한, 영구 상태를 수정하지 않는 파일 작업의 속도|StorageTarget|
|StorageTargetMetadataWriteIOPS|예|StorageTarget 메타데이터 쓰기 IOPS|초당 개수|평균|캐시에서 특정 StorageTarget에 보내는 쓰기 작업을 제외한, 영구 상태를 수정하는 파일 작업의 속도|StorageTarget|
|StorageTargetReadAheadThroughput|예|StorageTarget 미리 읽기 처리량|초당 바이트 수|평균|캐시에서 StorageTarget의 데이터를 편의적으로 읽는 속도|StorageTarget|
|StorageTargetReadIOPS|예|StorageTarget 읽기 IOPS|초당 개수|평균|캐시에서 특정 StorageTarget에 보내는 파일 읽기 작업의 속도|StorageTarget|
|StorageTargetRecycleRate|Yes|스토리지 대상 재생률|초당 바이트 수|평균|HPC 캐시의 스토리지 대상과 관련된 캐시 공간 재생률. 새 데이터를 위한 공간을 생성하기 위해 캐시에서 기존 데이터를 삭제하는 비율입니다.|StorageTarget|
|StorageTargetSyncWriteThroughput|예|StorageTarget 동기 쓰기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget에 동기적으로 데이터를 쓰는 속도. 클라이언트를 차단하는 쓰기입니다.|StorageTarget|
|StorageTargetTotalReadThroughput|예|StorageTarget 총 읽기 처리량|초당 바이트 수|평균|캐시에서 특정 StorageTarget의 데이터를 읽는 총 속도|StorageTarget|
|StorageTargetTotalWriteThroughput|예|StorageTarget 총 쓰기 처리량|초당 바이트 수|평균|캐시에서 특정 StorageTarget에 데이터를 쓰는 총 속도|StorageTarget|
|StorageTargetUsedReadSpace|Yes|스토리지 대상에 사용되는 읽기 공간|바이트|평균|스토리지 대상과 연결된 캐시된 파일에 사용되는 공간을 읽습니다.|StorageTarget|
|StorageTargetUsedWriteSpace|Yes|스토리지 대상에 사용되는 쓰기 공간|바이트|평균|스토리지 대상과 연결된 더티 데이터에서 사용되는 쓰기 공간.|StorageTarget|
|StorageTargetWriteIOPS|예|StorageTarget 쓰기 IOPS|개수|평균|캐시에서 특정 StorageTarget에 보내는 파일 쓰기 작업의 속도|StorageTarget|
|TotalBlocksRecycled|Yes|재활용되는 총 블록|개수|평균|HPC 캐시에 대해 재활용된 16k 캐시 블록 총 수(해제).|차원 없음|
|TotalFreeReadSpace|Yes|사용 가능한 읽기 공간|바이트|평균|읽기 파일 캐싱에 사용할 수 있는 총 공간.|차원 없음|
|TotalFreeWriteSpace|Yes|사용 가능한 쓰기 읽기 공간|바이트|평균|캐시에 변경된 데이터를 저장하는 데 사용할 수 있는 총 쓰기 공간.|차원 없음|
|TotalRecycleRate|Yes|재생률|초당 바이트 수|평균|HPC 캐시의 총 캐시 공간 재생률. 새 데이터를 위한 공간을 생성하기 위해 캐시에서 기존 데이터를 삭제하는 비율입니다.|차원 없음|
|TotalUsedReadSpace|Yes|사용된 읽기 공간|바이트|평균|HPC 캐시에 대해 더티 데이터가 사용하는 총 읽기 공간.|차원 없음|
|TotalUsedWriteSpace|Yes|사용되는 쓰기 공간|바이트|평균|HPC 캐시에 대해 더티 데이터가 사용하는 총 쓰기 공간.|차원 없음|
|작동 시간|예|작동 시간|개수|평균|캐시와 모니터링 시스템 간 연결 테스트의 부울 결과|차원 없음|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|예|동기화 세션 결과|개수|평균|서버 엔드포인트가 클라우드 엔드포인트와 동기화 세션을 성공적으로 완료할 때마다 1 값을 기록하는 메트릭|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|예|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송된 총 파일 크기|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncComputedCacheHitRate|Yes|클라우드 계층화 캐시 적중률|백분율|평균|캐시에서 제공된 바이트 백분율|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecallComputedSuccessRate|예|클라우드 계층화 회수 성공률|백분율|평균|성공한 모든 회수의 백분율|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecalledNetworkBytesByApplication|예|애플리케이션별 클라우드 계층화 회수 크기|바이트|합계|애플리케이션에서 회수한 데이터 크기|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|예|클라우드 계층화 회수 크기|바이트|합계|회수되는 데이터 크기|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncRecallThroughputBytesPerSecond|예|클라우드 계층화 회수 처리량|초당 바이트 수|평균|데이터 회수 처리량 크기|SyncGroupName, ServerName, ServerEndpointName|
|StorageSyncServerHeartbeat|예|서버 온라인 상태|개수|최대|등록된 서버가 클라우드 엔드포인트와의 하트비트를 성공적으로 기록할 때마다 1 값을 기록하는 메트릭|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|예|동기화된 파일 수|개수|합계|동기화된 파일 수|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|예|동기화 상태가 아닌 파일|개수|평균|동기화에 실패한 파일 수|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncTieringCacheSizeBytes|Yes|서버 캐시 크기|바이트|평균|서버에 캐시된 데이터의 크기|SyncGroupName, ServerName, ServerEndpointName|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|예|실패한 기능 요청|개수|합계|실패한 기능 요청|LogicalName, PartitionId, ProcessorInstance|
|AMLCalloutInputEvents|예|함수 이벤트|개수|합계|함수 이벤트|LogicalName, PartitionId, ProcessorInstance|
|AMLCalloutRequests|예|기능 요청|개수|합계|기능 요청|LogicalName, PartitionId, ProcessorInstance|
|ConversionErrors|예|데이터 변환 오류|개수|합계|데이터 변환 오류|LogicalName, PartitionId, ProcessorInstance|
|DeserializationError|예|입력 역직렬화 오류|개수|합계|입력 역직렬화 오류|LogicalName, PartitionId, ProcessorInstance|
|DroppedOrAdjustedEvents|예|잘못된 이벤트|개수|합계|잘못된 이벤트|LogicalName, PartitionId, ProcessorInstance|
|EarlyInputEvents|예|초기 입력 이벤트|개수|합계|초기 입력 이벤트|LogicalName, PartitionId, ProcessorInstance|
|오류|예|런타임 오류|개수|합계|런타임 오류|LogicalName, PartitionId, ProcessorInstance|
|InputEventBytes|예|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|LogicalName, PartitionId, ProcessorInstance|
|InputEvents|예|입력 이벤트|개수|합계|입력 이벤트|LogicalName, PartitionId, ProcessorInstance|
|InputEventsSourcesBacklogged|예|백로그된 입력 이벤트|개수|최대|백로그된 입력 이벤트|LogicalName, PartitionId, ProcessorInstance|
|InputEventsSourcesPerSecond|예|수신된 입력 원본|개수|합계|수신된 입력 원본|LogicalName, PartitionId, ProcessorInstance|
|LateInputEvents|예|늦은 입력 이벤트|개수|합계|늦은 입력 이벤트|LogicalName, PartitionId, ProcessorInstance|
|OutputEvents|예|출력 이벤트|개수|합계|출력 이벤트|LogicalName, PartitionId, ProcessorInstance|
|OutputWatermarkDelaySeconds|예|워터마크 지연|초|최대|워터마크 지연|LogicalName, PartitionId, ProcessorInstance|
|ProcessCPUUsagePercentage|예|CPU 사용률(%)(미리 보기)|백분율|최대|CPU 사용률(%)(미리 보기)|LogicalName, PartitionId, ProcessorInstance|
|ResourceUtilization|예|SU % 사용률|백분율|최대|SU % 사용률|LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|예|처리된 데이터(바이트)|바이트|합계|쿼리에서 처리된 데이터 양|차원 없음|
|BuiltinSqlPoolLoginAttempts|예|로그인 시도|개수|합계|성공 또는 실패한 로그인 시도 횟수|결과|
|BuiltinSqlPoolRequestsEnded|예|종료된 요청|개수|합계|성공, 실패 또는 취소된 요청 수|결과|
|IntegrationActivityRunsEnded|예|종료된 활동 실행|개수|합계|성공, 실패 또는 취소된 통합 작업 수|결과, FailureType, 작업, ActivityType, 파이프라인|
|IntegrationPipelineRunsEnded|예|종료된 파이프라인 실행|개수|합계|성공, 실패 또는 취소된 통합 파이프라인 실행 수|결과, FailureType, 파이프라인|
|IntegrationTriggerRunsEnded|예|종료된 트리거 실행|개수|합계|성공, 실패 또는 취소된 통합 트리거 수|결과, FailureType, 트리거|
|SQLStreamingBackloggedInputEventSources|아니요|백로그된 입력 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 백로그된 입력 이벤트의 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors|아니요|데이터 변환 오류(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 예상 출력 스키마로 변환할 수 없는 출력 이벤트의 수입니다. 이 시나리오에서 발생하는 이벤트를 삭제하기 위해 오류 정책을 ‘삭제’로 변경할 수 있습니다.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|아니요|입력 역직렬화 오류(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 역직렬화할 수 없는 입력 이벤트 수입니다.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|아니요|초기 입력 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 이른 도착 정책에 따라 애플리케이션 시간이 도착 시간과 비교하여 이른 것으로 간주되는 입력 이벤트 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|아니요|입력 이벤트 바이트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 스트리밍 작업이 받은 데이터의 양(바이트)입니다. 입력 소스로 전송되는 이벤트의 유효성을 검사하는 데 사용할 수 있습니다.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents|아니요|입력 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 입력 노드 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|아니요|수신된 입력 원본(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 초당 입력 이벤트 원본 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|아니요|늦은 입력 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 늦은 도착 정책에 따라 애플리케이션 시간이 도착 시간과 비교하여 늦은 것으로 간주되는 입력 이벤트 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|아니요|잘못된 순서 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 이벤트 순서 지정 정책에 따라 이벤트 허브 입력 어댑터에서 수신되거나, 순서가 잘못되어 삭제되었거나 조정된 타임스탬프가 지정된 상태로 수신된 이벤트 허브 이벤트(직렬화된 메시지)의 수입니다.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|아니요|출력 이벤트(미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 출력 이벤트 수.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|아니요|워터마크 지연(미리 보기)|개수|최대|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 출력 워터마크 지연 시간(초).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|아니요|리소스 사용률(%)(미리 보기)|백분율|최대|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다.
 리소스 사용률은 백분율로 표현됩니다. 사용률이 높으면 작업에서 할당된 최대 리소스에 가까운 용량을 사용하고 있음을 나타냅니다.|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors|아니요|런타임 오류 (미리 보기)|개수|합계|미국 동부, 서유럽에서 사용할 수 있는 미리 보기 메트릭입니다. 쿼리 처리와 관련된 총 오류 수(이벤트 수집 또는 결과 출력 중에 발견된 오류 제외).|SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|예|할당된 vCore|개수|최대|Apache Spark 풀에 할당된 vCore 수|SubmitterId|
|BigDataPoolAllocatedMemory|예|할당된 메모리(GB)|개수|최대|Apach Spark 풀에 할당된 메모리(GB)|SubmitterId|
|BigDataPoolApplicationsActive|예|활성 Apache Spark 애플리케이션|개수|최대|활성 Apache Spark 풀 애플리케이션의 총 수|JobState|
|BigDataPoolApplicationsEnded|예|종료된 Apache Spark 애플리케이션|개수|합계|종료된 Apache Spark 풀 애플리케이션의 수|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveQueries|예|활성 쿼리|개수|합계|활성 쿼리. 이 메트릭을 필터링 및 분할하지 않고 그대로 사용하면 시스템에서 실행 중인 모든 활성 쿼리가 표시됩니다.|IsUserDefined|
|AdaptiveCacheHitPercent|예|적응 캐시 적중률|백분율|최대|워크로드가 적응 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 이 메트릭을 캐시 적중률 메트릭과 함께 사용하여 추가 용량을 스케일링할 것인지 아니면 워크로드를 다시 실행하여 캐시를 하이드레이션할 것인지 결정할 수 있습니다.|차원 없음|
|AdaptiveCacheUsedPercent|예|적응 캐시 사용률|백분율|최대|워크로드가 적응 캐시를 얼마나 잘 활용하고 있는지 측정합니다. 이 메트릭을 캐시 사용률 메트릭과 함께 사용하여 추가 용량을 스케일링할 것인지 아니면 워크로드를 다시 실행하여 캐시를 하이드레이션할 것인지 결정할 수 있습니다.|차원 없음|
|Connections|예|Connections|개수|합계|SQL 풀에 대한 총 로그인 수|결과|
|ConnectionsBlockedByFirewall|예|방화벽에서 차단한 연결|개수|합계|방화벽 규칙에서 차단한 연결 수. 이 수가 높으면 SQL 풀의 액세스 제어 정책을 다시 방문하여 연결을 모니터링합니다.|차원 없음|
|CPUPercent|예|CPU 사용률|백분율|최대|SQL 풀에 있는 모든 노드의 CPU 사용률|차원 없음|
|DWULimit|예|DWU 제한|개수|최대|SQL 풀의 서비스 수준 목표|차원 없음|
|DWUUsed|예|DWU 사용됨|개수|최대|SQL 풀의 대략적인 사용량을 나타냅니다. DWU 제한 * DWU 백분율로 측정합니다.|차원 없음|
|DWUUsedPercent|예|DWU 사용률|백분율|최대|SQL 풀의 대략적인 사용량을 나타냅니다. CPU 백분율과 데이터 IO 백분율 사이의 최댓값을 사용하여 측정됩니다.|차원 없음|
|LocalTempDBUsedPercent|예|로컬 tempdb 사용률|백분율|최대|모든 컴퓨팅 노드의 로컬 tempdb 사용률 - 5분마다 값을 내보냅니다.|차원 없음|
|MemoryUsedPercent|예|메모리 사용률|백분율|최대|SQL 풀에 있는 모든 노드의 메모리 사용률|차원 없음|
|QueuedQueries|예|큐에 대기 중인 쿼리|개수|합계|최대 동시성 제한에 도달한 후에 큐에 대기 중인 누적 요청 수|IsUserDefined|
|WLGActiveQueries|예|작업 그룹 활성 쿼리|개수|합계|워크로드 그룹 내의 활성 쿼리. 이 메트릭을 필터링 및 분할하지 않고 그대로 사용하면 시스템에서 실행 중인 모든 활성 쿼리가 표시됩니다.|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|예|작업 그룹 쿼리 시간 제한|개수|합계|시간이 초과된 워크로드 그룹에 대한 쿼리. 이 메트릭에서 보고한 쿼리 시간 제한은 쿼리 실행이 시작된 후에만 해당됩니다(잠금 또는 리소스 대기로 인한 대기 시간은 포함되지 않음).|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|예|최대 리소스 비율별 작업 그룹 할당|백분율|최대|워크로드 그룹당 유효 상한 리소스 백분율에 대한 상대적 리소스 할당률을 표시합니다. 이 메트릭은 워크로드 그룹의 효과적인 사용률을 제공합니다.|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|예|시스템 비율별 작업 그룹 할당|백분율|최대|전체 시스템에 대한 상대적인 리소스 할당률|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|예|유효 상한 리소스 비율|백분율|최대|워크로드 그룹에 대한 유효 상한 리소스 비율. min_percentage_resource가 0보다 큰 다른 워크로드 그룹이 있으면 effective_cap_percentage_resource는 그에 비례해서 감소합니다.|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|예|유효 최소 리소스 비율|백분율|최대|서비스 수준 및 워크로드 그룹 설정을 고려하여 허용된 유효 최소 리소스 백분율. 낮은 서비스 수준에서 유효 min_percentage_resource를 높일 수 있습니다.|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|예|작업 그룹 큐에 대기 중인 쿼리|개수|합계|최대 동시성 제한에 도달한 후에 큐에 대기 중인 누적 요청 수|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|예|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|차원 없음|
|IngressReceivedInvalidMessages|예|수신된 잘못된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|차원 없음|
|IngressReceivedMessages|예|수신된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|차원 없음|
|IngressReceivedMessagesCountLag|예|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이|차원 없음|
|IngressReceivedMessagesTimeLag|예|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressStoredBytes|예|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|예|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|WarmStorageMaxProperties|예|웜 스토리지 최대 속성|개수|최대|S1/S2 SKU 환경에서 허용하는 최대 속성 수 및 PAYG SKU용 웜 저장소에서 허용하는 최대 속성 수|차원 없음|
|WarmStorageUsedProperties|예|웜 스토리지 사용 속성 |개수|최대|S1/S2 SKU 환경에서 사용하는 속성 수 및 PAYG SKU용 웜 저장소에서 사용하는 속성 수|차원 없음|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|예|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|차원 없음|
|IngressReceivedInvalidMessages|예|수신된 잘못된 메시지|개수|합계|이벤트 원본에서 읽은 잘못된 메시지 수|차원 없음|
|IngressReceivedMessages|예|수신된 메시지|개수|합계|이벤트 원본에서 읽은 메시지 수|차원 없음|
|IngressReceivedMessagesCountLag|예|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이|차원 없음|
|IngressReceivedMessagesTimeLag|예|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressStoredBytes|예|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|예|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|WarmStorageMaxProperties|예|웜 스토리지 최대 속성|개수|최대|S1/S2 SKU 환경에서 허용하는 최대 속성 수 및 PAYG SKU용 웜 저장소에서 허용하는 최대 속성 수|차원 없음|
|WarmStorageUsedProperties|예|웜 스토리지 사용 속성 |개수|최대|S1/S2 SKU 환경에서 사용하는 속성 수 및 PAYG SKU용 웜 저장소에서 사용하는 속성 수|차원 없음|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|샘플 기간 동안 읽기 작업으로 인한 총 디스크 처리량|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 읽기 작업 수. 이러한 작업의 크기는 가변적일 수 있습니다.|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|샘플 기간 동안 쓰기 작업으로 인한 총 디스크 처리량|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 쓰기 작업 수. 이러한 작업의 크기는 가변적일 수 있습니다.|차원 없음|
|DiskReadBytesPerSecond|예|디스크 읽기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 읽기 작업으로 인한 평균 디스크 처리량|차원 없음|
|DiskReadLatency|예|디스크 읽기 대기 시간|밀리초|평균|총 읽기 대기 시간. 디바이스 및 커널 읽기 대기 시간의 합계입니다.|차원 없음|
|DiskReadOperations|예|디스크 읽기 작업|개수|합계|이전 샘플 기간의 IO 읽기 작업 수. 이러한 작업의 크기는 가변적일 수 있습니다.|차원 없음|
|DiskWriteBytesPerSecond|예|디스크 쓰기 바이트/초|초당 바이트 수|평균|샘플 기간 동안 쓰기 작업으로 인한 평균 디스크 처리량|차원 없음|
|DiskWriteLatency|예|디스크 쓰기 대기 시간|밀리초|평균|총 쓰기 대기 시간. 디바이스 및 커널 쓰기 대기 시간의 합계입니다.|차원 없음|
|DiskWriteOperations|예|디스크 쓰기 작업|개수|합계|이전 샘플 기간의 IO 쓰기 작업 수. 이러한 작업의 크기는 가변적일 수 있습니다.|차원 없음|
|MemoryActive|예|메모리 활성|바이트|평균|지난 짧은 시간 동안 VM에서 사용한 메모리 양. 현재 VM에 "진짜" 필요한 메모리 양입니다. 게스트 성능에 영향을 주지 않고, 사용되지 않는 추가 메모리를 교환하거나 ballooning할 수 있습니다.|차원 없음|
|MemoryGranted|예|부여된 메모리|바이트|평균|호스트가 VM에 부여된 메모리 양. 메모리는 한 번 닿기 전에는 호스트에 부여되지 않으며, VMkernel에 메모리가 필요한 경우 부여된 메모리를 교환하거나 ballooning할 수 있습니다.|차원 없음|
|MemoryUsed|예|사용된 메모리|바이트|평균|VM에서 사용 중인 머신 메모리 양|차원 없음|
|네트워크 인|예|네트워크 인|바이트|합계|받은 트래픽의 총 네트워크 처리량|차원 없음|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|전송된 트래픽의 총 네트워크 처리량|차원 없음|
|NetworkInBytesPerSecond|예|네트워크 입력 바이트/초|초당 바이트 수|평균|받은 트래픽의 평균 네트워크 처리량|차원 없음|
|NetworkOutBytesPerSecond|예|네트워크 출력 바이트/초|초당 바이트 수|평균|전송된 트래픽의 평균 네트워크 처리량|차원 없음|
|CPU 사용률|예|백분율 CPU|백분율|평균|CPU 사용률. 이 값은 시스템의 모든 프로세서 코어를 나타내는 100%로 보고됩니다. 예를 들어 4코어 시스템의 50%를 사용하는 양방향 VM은 2개 코어를 완전히 사용합니다.|차원 없음|
|PercentageCpuReady|예|CPU 준비 백분율|밀리초|합계|준비 시간은 CPU를 이전 업데이트 간격으로 사용할 수 있을 때까지 기다린 시간입니다.|차원 없음|


## <a name="microsoftwebconnections"></a>Microsoft.Web/connections

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|요청|No|요청|개수|합계|API 연결 요청|HttpStatusCode, ClientIPAddress|


## <a name="microsoftwebhostingenvironments"></a>Microsoft.Web/hostingEnvironments

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveRequests|예|활성 요청(사용되지 않음)|개수|합계|ActiveRequests|인스턴스|
|AverageResponseTime|예|평균 응답 시간(사용 되지 않음)|초|평균|AverageResponseTime|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|BytesReceived|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|BytesSent|인스턴스|
|CpuPercentage|예|CPU 비율|백분율|평균|CpuPercentage|인스턴스|
|DiskQueueLength|예|디스크 큐 길이|개수|평균|DiskQueueLength|인스턴스|
|Http101|예|Http 101|개수|합계|Http101|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|Http2xx|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|Http3xx|인스턴스|
|Http401|예|Http 401|개수|합계|Http401|인스턴스|
|Http403|예|Http 403|개수|합계|Http403|인스턴스|
|Http404|예|Http 404|개수|합계|Http404|인스턴스|
|Http406|예|Http 406|개수|합계|Http406|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|Http4xx|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|Http5xx|인스턴스|
|HttpQueueLength|예|Http 큐 길이|개수|평균|HttpQueueLength|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|HttpResponseTime|인스턴스|
|LargeAppServicePlanInstances|예|대형 App Service 계획 작업자|개수|평균|대형 App Service 계획 작업자|차원 없음|
|MediumAppServicePlanInstances|예|중형 App Service 계획 작업자|개수|평균|중형 App Service 계획 작업자|차원 없음|
|MemoryPercentage|예|메모리 비율|백분율|평균|MemoryPercentage|인스턴스|
|요청|예|요청|개수|합계|요청|인스턴스|
|SmallAppServicePlanInstances|예|소형 App Service 계획 작업자|개수|평균|소형 App Service 계획 작업자|차원 없음|
|TotalFrontEnds|예|총 프런트 엔드|개수|평균|총 프런트 엔드|차원 없음|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveRequests|예|활성 요청(사용되지 않음)|개수|합계|활성 요청|인스턴스|
|AverageResponseTime|예|평균 응답 시간(사용 되지 않음)|초|평균|프런트 엔드에서 요청을 처리하는 데 걸린 평균 시간(초)|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|모든 프런트 엔드에서 사용하는 평균 수신 대역폭(MiB)|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|모든 프런트 엔드에서 사용하는 평균 수신 대역폭(MiB)|인스턴스|
|CpuPercentage|예|CPU 비율|백분율|평균|프런트 엔드의 모든 인스턴스에서 사용하는 평균 CPU|인스턴스|
|DiskQueueLength|예|디스크 큐 길이|개수|평균|스토리지에 대기 중인 평균 읽기 및 쓰기 요청 수입니다. 디스크 큐 길이 값이 높으면 과도한 디스크 I/O로 인해 앱의 속도가 느려질 수 있음을 나타냅니다.|인스턴스|
|Http101|예|Http 101|개수|합계|HTTP 상태 코드 101을 생성하는 요청 수|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|반환되는 HTTP 상태 코드가 200이거나 300 미만인 요청 수|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|반환되는 HTTP 상태 코드가 300이거나 400 미만인 요청 수|인스턴스|
|Http401|예|Http 401|개수|합계|결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http403|예|Http 403|개수|합계|결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http404|예|Http 404|개수|합계|결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http406|예|Http 406|개수|합계|결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|반환되는 HTTP 상태 코드가 400이거나 500 미만인 요청 수|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|반환되는 HTTP 상태 코드가 500이거나 600 미만인 요청 수|인스턴스|
|HttpQueueLength|예|Http 큐 길이|개수|평균|처리하기 전에 큐에 배치해야 하는 평균 HTTP 요청 수입니다. HTTP 큐 길이 값이 높거나 증가하면 계획이 과부하 상태에 있음을 나타냅니다.|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|프런트 엔드에서 요청을 처리하는 데 걸린 시간(초)|인스턴스|
|LargeAppServicePlanInstances|예|대형 App Service 계획 작업자|개수|평균|대형 App Service 계획 작업자|차원 없음|
|MediumAppServicePlanInstances|예|중형 App Service 계획 작업자|개수|평균|중형 App Service 계획 작업자|차원 없음|
|MemoryPercentage|예|메모리 비율|백분율|평균|프런트 엔드의 모든 인스턴스에서 사용하는 평균 메모리|인스턴스|
|요청|예|요청|개수|합계|결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다.|인스턴스|
|SmallAppServicePlanInstances|예|소형 App Service 계획 작업자|개수|평균|소형 App Service 계획 작업자|차원 없음|
|TotalFrontEnds|예|총 프런트 엔드|개수|평균|총 프런트 엔드|차원 없음|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CpuPercentage|예|CPU 비율|백분율|평균|작업자 풀의 모든 인스턴스에서 사용하는 평균 CPU|인스턴스|
|MemoryPercentage|예|메모리 비율|백분율|평균|작업자 풀의 모든 인스턴스에서 사용하는 평균 메모리|인스턴스|
|WorkersAvailable|예|사용 가능한 작업자|개수|평균|사용 가능한 작업자|차원 없음|
|WorkersTotal|예|총 작업자|개수|평균|총 작업자|차원 없음|
|WorkersUsed|예|사용된 작업자|개수|평균|사용된 작업자|차원 없음|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesReceived|예|데이터 입력|바이트|합계|계획의 모든 인스턴스 간에 사용된 평균 들어오는 대역폭입니다.|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|계획의 모든 인스턴스 간에 사용된 평균 나가는 대역폭입니다.|인스턴스|
|CpuPercentage|예|CPU 비율|백분율|평균|계획의 모든 인스턴스 간에 사용된 평균 CPU입니다.|인스턴스|
|DiskQueueLength|예|디스크 큐 길이|개수|평균|스토리지에 대기 중인 평균 읽기 및 쓰기 요청 수입니다. 디스크 큐 길이 값이 높으면 과도한 디스크 I/O로 인해 앱의 속도가 느려질 수 있음을 나타냅니다.|인스턴스|
|HttpQueueLength|예|Http 큐 길이|개수|평균|처리하기 전에 큐에 배치해야 하는 평균 HTTP 요청 수입니다. HTTP 큐 길이 값이 높거나 증가하면 계획이 과부하 상태에 있음을 나타냅니다.|인스턴스|
|MemoryPercentage|예|메모리 비율|백분율|평균|계획의 모든 인스턴스 간에 사용된 평균 메모리입니다.|인스턴스|
|SocketInboundAll|예|인바운드 요청에 대한 소켓 수|개수|평균|플랜의 모든 인스턴스에서 들어오는 HTTP 요청에 사용된 평균 소켓 수입니다.|인스턴스|
|SocketLoopback|예|루프백 연결에 대한 소켓 수|개수|평균|플랜의 모든 인스턴스에서 루프백 연결에 사용된 평균 소켓 수입니다.|인스턴스|
|SocketOutboundAll|예|아웃바운드 요청에 대한 소켓 수|개수|평균|해당 TCP 상태에 관계없이 플랜의 모든 인스턴스에서 아웃바운드 연결에 사용된 평균 소켓 수입니다. 아웃바운드 연결이 너무 많으면 연결 오류가 발생할 수 있습니다.|인스턴스|
|SocketOutboundEstablished|예|아웃바운드 요청에 대해 설정된 소켓 수|개수|평균|플랜의 모든 인스턴스에서 아웃바운드 연결에 사용된 ESTABLISHED 상태의 평균 소켓 수입니다.|인스턴스|
|SocketOutboundTimeWait|예|아웃바운드 요청에 대한 시간 대기 소켓 수|개수|평균|플랜의 모든 인스턴스에서 아웃바운드 연결에 사용된 TIME_WAIT 상태의 평균 소켓 수입니다. TIME_WAIT 상태에서 아웃바운드 소켓 수가 많거나 증가하면 연결 오류가 발생할 수 있습니다.|인스턴스|
|TcpCloseWait|예|TCP 닫기 대기|개수|평균|플랜의 모든 인스턴스에서 CLOSE_WAIT 상태의 평균 소켓 수입니다.|인스턴스|
|TcpClosing|예|TCP 닫는 중|개수|평균|플랜의 모든 인스턴스에서 CLOSING 상태의 평균 소켓 수입니다.|인스턴스|
|TcpEstablished|예|TCP 설정됨|개수|평균|플랜의 모든 인스턴스에서 ESTABLISHED 상태의 평균 소켓 수입니다.|인스턴스|
|TcpFinWait1|예|TCP FIN 대기 1|개수|평균|플랜의 모든 인스턴스에서 FIN_WAIT_1 상태의 평균 소켓 수입니다.|인스턴스|
|TcpFinWait2|예|TCP FIN 대기 2|개수|평균|플랜의 모든 인스턴스에서 FIN_WAIT_2 상태의 평균 소켓 수입니다.|인스턴스|
|TcpLastAck|예|TCP 마지막 ACK|개수|평균|플랜의 모든 인스턴스에서 LAST_ACK 상태의 평균 소켓 수입니다.|인스턴스|
|TcpSynReceived|예|TCP SYN 받음|개수|평균|플랜의 모든 인스턴스에서 SYN_RCVD 상태의 평균 소켓 수입니다.|인스턴스|
|TcpSynSent|예|TCP SYN 보냄|개수|평균|플랜의 모든 인스턴스에서 SYN_SENT 상태의 평균 소켓 수입니다.|인스턴스|
|TcpTimeWait|예|TCP 시간 대기|개수|평균|플랜의 모든 인스턴스에서 TIME_WAIT 상태의 평균 소켓 수입니다.|인스턴스|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AppConnections|예|Connections|개수|평균|샌드박스(w3wp.exe 및 해당 자식 프로세스)에 있는 바인딩된 소켓의 수입니다. 바인딩된 소켓은 bind()/connect() API를 호출하여 만들어지며, 해당 소켓이 CloseHandle()/closesocket()으로 닫힐 때까지 유지됩니다. WebApps 및 FunctionApps용.|인스턴스|
|AverageMemoryWorkingSet|예|평균 메모리 작업 집합|바이트|평균|앱에 사용된 메가바이트(MiB) 크기의 평균 메모리 양입니다. WebApps 및 FunctionApps용.|인스턴스|
|AverageResponseTime|예|평균 응답 시간(사용 되지 않음)|초|평균|앱에서 요청을 처리하는 데 걸린 평균 소요 시간(초) WebApps 및 FunctionApps용.|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|앱에서 사용한 들어오는 대역폭 양(MiB)입니다. WebApps 및 FunctionApps용.|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|앱에서 사용한 나가는 대역폭 양(MiB)입니다. WebApps 및 FunctionApps용.|인스턴스|
|CpuTime|예|CPU 시간|초|합계|앱에서 사용한 CPU의 양(초)입니다. 이 메트릭에 대한 자세한 내용은 https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU 시간 및 CPU 비율)에서 확인할 수 있습니다. WebApps에만 해당 합니다.|인스턴스|
|CurrentAssemblies|예|현재 어셈블리|개수|평균|이 애플리케이션의 모든 AppDomains에 로드된 어셈블리의 현재 개수입니다. WebApps 및 FunctionApps용.|인스턴스|
|FileSystemUsage|예|파일 시스템 사용량|바이트|평균|앱에서 사용하는 파일 시스템 할당량(백분율) WebApps 및 FunctionApps용.|차원 없음|
|FunctionExecutionCount|예|함수 실행 횟수|개수|합계|함수 실행 횟수. FunctionApps 전용.|인스턴스|
|FunctionExecutionUnits|예|함수 실행 단위|개수|합계|함수 실행 단위. FunctionApps 전용.|인스턴스|
|Gen0Collections|예|Gen 0 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 0세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다. WebApps 및 FunctionApps용.|인스턴스|
|Gen1Collections|예|Gen 1 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 1세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다. WebApps 및 FunctionApps용.|인스턴스|
|Gen2Collections|예|Gen 2 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 2세대 개체가 가비지 수집된 횟수입니다. WebApps 및 FunctionApps용.|인스턴스|
|핸들|예|핸들 개수|개수|평균|앱 프로세스에서 현재 열려 있는 총 핸들 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|HealthCheckStatus|예|상태 검사 상태|개수|평균|WebApps 및 FunctionApps에 대한 상태 검사 상태.|인스턴스|
|Http101|예|Http 101|개수|합계|HTTP 상태 코드 101을 생성하는 요청 수 WebApps 및 FunctionApps용.|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|반환되는 HTTP 상태 코드가 200이거나 300 미만인 요청 수 WebApps 및 FunctionApps용.|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|반환되는 HTTP 상태 코드가 300이거나 400 미만인 요청 수 WebApps 및 FunctionApps용.|인스턴스|
|Http401|예|Http 401|개수|합계|결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|Http403|예|Http 403|개수|합계|결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|Http404|예|Http 404|개수|합계|결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|Http406|예|Http 406|개수|합계|결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|반환되는 HTTP 상태 코드가 400이거나 500 미만인 요청 수 WebApps 및 FunctionApps용.|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|반환되는 HTTP 상태 코드가 500이거나 600 미만인 요청 수 WebApps 및 FunctionApps용.|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|앱에서 요청을 처리하는 데 걸린 시간(초) WebApps 및 FunctionApps용.|인스턴스|
|IoOtherBytesPerSecond|예|초당 IO 기타 바이트 수|초당 바이트 수|합계|앱 프로세스가 컨트롤 작업과 같이 데이터와 무관한 I/O 작업에 대해 바이트를 실행하는 속도 WebApps 및 FunctionApps용.|인스턴스|
|IoOtherOperationsPerSecond|예|초당 IO 기타 작업 수|초당 바이트 수|합계|앱 프로세스가 읽기 또는 쓰기 작업이 아닌 I/O 작업을 실행하는 속도 WebApps 및 FunctionApps용.|인스턴스|
|IoReadBytesPerSecond|예|초당 IO 읽기 바이트 수|초당 바이트 수|합계|앱 프로세스에서 I/O 작업의 바이트를 읽는 속도입니다. WebApps 및 FunctionApps용.|인스턴스|
|IoReadOperationsPerSecond|예|초당 IO 읽기 작업 수|초당 바이트 수|합계|앱 프로세스에서 읽기 I/O 작업을 실행하는 속도입니다. WebApps 및 FunctionApps용.|인스턴스|
|IoWriteBytesPerSecond|예|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|앱 프로세스에서 I/O 작업에 바이트를 쓰는 속도입니다. WebApps 및 FunctionApps용.|인스턴스|
|IoWriteOperationsPerSecond|예|초당 IO 쓰기 작업 수|초당 바이트 수|합계|앱 프로세스에서 쓰기 I/O 작업을 실행하는 속도입니다. WebApps 및 FunctionApps용.|인스턴스|
|MemoryWorkingSet|예|메모리 작업 집합|바이트|평균|현재 앱에 사용된 메모리 양(MiB)입니다. WebApps 및 FunctionApps용.|인스턴스|
|PrivateBytes|예|프라이빗 바이트|바이트|평균|전용 바이트는 앱 프로세스가 할당하여 다른 프로세스와는 공유할 수 없는 메모리의 현재 크기(바이트)입니다. WebApps 및 FunctionApps용.|인스턴스|
|요청|예|요청|개수|합계|결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|RequestsInApplicationQueue|예|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 요청 큐의 요청 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|스레드|예|스레드 개수|개수|평균|앱 프로세스에서 현재 활성 상태인 스레드의 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|TotalAppDomains|예|총 앱 도메인|개수|평균|이 애플리케이션에 로드된 AppDomains의 현재 수입니다. WebApps 및 FunctionApps용.|인스턴스|
|TotalAppDomainsUnloaded|예|언로드된 총 앱 도메인|개수|평균|애플리케이션이 시작된 이후 언로드된 총 AppDomains 수입니다. WebApps 및 FunctionApps용.|인스턴스|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AppConnections|예|Connections|개수|평균|샌드박스(w3wp.exe 및 해당 자식 프로세스)에 있는 바인딩된 소켓의 수입니다. 바인딩된 소켓은 bind()/connect() API를 호출하여 만들어지며, 해당 소켓이 CloseHandle()/closesocket()으로 닫힐 때까지 유지됩니다.|인스턴스|
|AverageMemoryWorkingSet|예|평균 메모리 작업 집합|바이트|평균|앱에 사용된 메가바이트(MiB) 크기의 평균 메모리 양입니다.|인스턴스|
|AverageResponseTime|예|평균 응답 시간(사용 되지 않음)|초|평균|앱에서 요청을 처리하는 데 걸린 평균 소요 시간(초)|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|앱에서 사용한 들어오는 대역폭 양(MiB)입니다.|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|앱에서 사용한 나가는 대역폭 양(MiB)입니다.|인스턴스|
|CpuTime|예|CPU 시간|초|합계|앱에서 사용한 CPU의 양(초)입니다. 이 메트릭에 대한 자세한 내용은 https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU 시간 및 CPU 비율)에서 확인할 수 있습니다.|인스턴스|
|CurrentAssemblies|예|현재 어셈블리|개수|평균|이 애플리케이션의 모든 AppDomains에 로드된 어셈블리의 현재 개수입니다.|인스턴스|
|FileSystemUsage|예|파일 시스템 사용량|바이트|평균|앱에서 사용하는 파일 시스템 할당량(백분율)|차원 없음|
|FunctionExecutionCount|예|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|FunctionExecutionUnits|예|함수 실행 단위|개수|합계|함수 실행 단위|인스턴스|
|Gen0Collections|예|Gen 0 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 0세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|인스턴스|
|Gen1Collections|예|Gen 1 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 1세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|인스턴스|
|Gen2Collections|예|Gen 2 가비지 수집|개수|합계|앱 프로세스가 시작된 이후 2세대 개체가 가비지 수집된 횟수입니다.|인스턴스|
|핸들|예|핸들 개수|개수|평균|앱 프로세스에서 현재 열려 있는 총 핸들 수입니다.|인스턴스|
|HealthCheckStatus|예|상태 검사 상태|개수|평균|상태 검사 상태|인스턴스|
|Http101|예|Http 101|개수|합계|HTTP 상태 코드 101을 생성하는 요청 수|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|반환되는 HTTP 상태 코드가 200이거나 300 미만인 요청 수|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|반환되는 HTTP 상태 코드가 300이거나 400 미만인 요청 수|인스턴스|
|Http401|예|Http 401|개수|합계|결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http403|예|Http 403|개수|합계|결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http404|예|Http 404|개수|합계|결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http406|예|Http 406|개수|합계|결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다.|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|반환되는 HTTP 상태 코드가 400이거나 500 미만인 요청 수|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|반환되는 HTTP 상태 코드가 500이거나 600 미만인 요청 수|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|앱에서 요청을 처리하는 데 걸린 시간(초)|인스턴스|
|IoOtherBytesPerSecond|예|초당 IO 기타 바이트 수|초당 바이트 수|합계|앱 프로세스가 컨트롤 작업과 같이 데이터와 무관한 I/O 작업에 대해 바이트를 실행하는 속도|인스턴스|
|IoOtherOperationsPerSecond|예|초당 IO 기타 작업 수|초당 바이트 수|합계|앱 프로세스가 읽기 또는 쓰기 작업이 아닌 I/O 작업을 실행하는 속도|인스턴스|
|IoReadBytesPerSecond|예|초당 IO 읽기 바이트 수|초당 바이트 수|합계|앱 프로세스에서 I/O 작업의 바이트를 읽는 속도입니다.|인스턴스|
|IoReadOperationsPerSecond|예|초당 IO 읽기 작업 수|초당 바이트 수|합계|앱 프로세스에서 읽기 I/O 작업을 실행하는 속도입니다.|인스턴스|
|IoWriteBytesPerSecond|예|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|앱 프로세스에서 I/O 작업에 바이트를 쓰는 속도입니다.|인스턴스|
|IoWriteOperationsPerSecond|예|초당 IO 쓰기 작업 수|초당 바이트 수|합계|앱 프로세스에서 쓰기 I/O 작업을 실행하는 속도입니다.|인스턴스|
|MemoryWorkingSet|예|메모리 작업 집합|바이트|평균|현재 앱에 사용된 메모리 양(MiB)입니다.|인스턴스|
|PrivateBytes|예|프라이빗 바이트|바이트|평균|전용 바이트는 앱 프로세스가 할당하여 다른 프로세스와는 공유할 수 없는 메모리의 현재 크기(바이트)입니다.|인스턴스|
|요청|예|요청|개수|합계|결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다.|인스턴스|
|RequestsInApplicationQueue|예|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 요청 큐의 요청 수입니다.|인스턴스|
|스레드|예|스레드 개수|개수|평균|앱 프로세스에서 현재 활성 상태인 스레드의 수입니다.|인스턴스|
|TotalAppDomains|예|총 앱 도메인|개수|평균|이 애플리케이션에 로드된 AppDomains의 현재 수입니다.|인스턴스|
|TotalAppDomainsUnloaded|예|언로드된 총 앱 도메인|개수|평균|애플리케이션이 시작된 이후 언로드된 총 AppDomains 수입니다.|인스턴스|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesSent|예|데이터 출력|바이트|합계|BytesSent|인스턴스|
|FunctionErrors|예|FunctionErrors|개수|합계|FunctionErrors|인스턴스|
|FunctionHits|예|FunctionHits|개수|합계|FunctionHits|인스턴스|
|SiteErrors|예|SiteErrors|개수|합계|SiteErrors|인스턴스|
|SiteHits|예|SiteHits|개수|합계|SiteHits|인스턴스|


## <a name="wandiscofusionmigrators"></a>Wandisco/migrators

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|초당 바이트 수|예|초당 바이트 수입니다.|초당 바이트 수|평균|마이그레이션자에 사용 중인 바이트/초의 처리량 속도입니다.||
|DirectoriesCreatedCount|예|만든 디렉터리 수|개수|합계|이렇게 하면 마이그레이션의 일부로 생성된 디렉터리 수를 확인할 수 있습니다.||
|FileMigrationCount|예|파일 마이그레이션 수|개수|합계|이렇게 하면 마이그레이션된 파일의 총 수를 확인할 수 있습니다.||
|InitialScanDataMigratedInBytes|예|마이그레이션된 초기 검사 데이터(바이트)|바이트|합계|이를 통해 온-프레미스 파일 시스템의 초기 검색 결과로 새 마이그레이션기에서 전송된 총 바이트의 뷰를 제공합니다. 초기 검사 마이그레이션 후 마이그레이션에 추가되는 모든 데이터는 이 메트릭에 포함되지 않습니다.||
|LiveDataMigratedInBytes|예|실시간 데이터 마이그레이션(바이트)|개수|합계|마이그레이션이 시작된 이후 클라이언트 활동으로 인해 변경된 LiveData의 실행 합계를 제공합니다.||
|MigratorCPULoad|예|마이그레이션기 CPU 로드|백분율|평균|마이그레이션기 프로세스의 CPU 사용량입니다.||
|NumberOfExcludedPaths|예|제외된 경로 수|개수|합계|제외 규칙으로 인해 마이그레이션에서 제외된 경로의 실행 횟수를 제공합니다.||
|NumberOfFailedPaths|예|실패한 경로 수|개수|합계|마이그레이션에 실패한 경로의 수입니다.||
|SystemCPULoad|예|시스템 CPU 로드|백분율|평균|총 CPU 사용량.||
|TotalMigratedDataInBytes|예|총 마이그레이션된 데이터(바이트)|바이트|합계|지정된 마이그레이션자에 대해 성공적으로 마이그레이션된 바이트를 볼 수 있습니다.||
|TotalTransactions|예|총 트랜잭션|개수|합계|이렇게 하면 사용자에게 요금이 청구될 수 있는 데이터 트랜잭션의 총계가 제공됩니다.||


## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 메트릭에 대해 읽기](../data-platform.md)
- [메트릭에 대한 경고 만들기](../alerts/alerts-overview.md)
- [스토리지, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](../essentials/platform-logs-overview.md)
