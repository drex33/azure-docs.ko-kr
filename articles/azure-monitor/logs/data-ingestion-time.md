---
title: Azure Monitor의 로그 데이터 수집 시간 | Microsoft Docs
description: Azure Monitor에서 로그 데이터를 수집할 때 대기 시간에 영향을 주는 다양한 요인에 대해 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: ff7f250fa7fae2fb0a68710c94a9437204a61439
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714831"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure Monitor의 로그 데이터 수집 시간
Azure Monitor는 점점 더 빠른 속도로 매달 테라바이트 단위의 데이터를 보내는 수천 명의 고객을 처리하는 대규모 데이터 서비스입니다. 로그 데이터가 수집된 후 사용할 수 있기까지 걸리는 시간에 대해 질문하는 경우가 많습니다. 이 문서에서는 이 대기 시간에 영향을 주는 여러 요인에 대해 설명합니다.

## <a name="typical-latency"></a>일반적인 대기 시간
대기 시간은 모니터링되는 시스템에서 데이터가 생성된 시간과 Azure Monitor에서 데이터를 분석에 사용할 수 있는 시간을 참조합니다. 로그 데이터를 수집하기 위한 일반적인 대기 시간이 **20초에서 3분 사이입니다.** 그렇지만 특정 데이터에 대한 특정 대기 시간은 아래에 설명된 다양한 요인에 따라 달라집니다.


## <a name="factors-affecting-latency"></a>대기 시간에 영향을 주는 요인
특정 데이터 집합의 총 수집 시간을 다음과 같은 상위 수준 영역으로 분석할 수 있습니다. 

- **에이전트 시간** - 이벤트를 검색하고 수집한 다음 Azure Monitor 로그 수집 지점으로 로그 레코드로 보내는 시간입니다. 대부분의 경우, 이 프로세스는 에이전트가 처리합니다. 네트워크에서 추가 대기 시간을 도입할 수 있습니다.
- **파이프라인 시간** - 로그 레코드를 처리하는 데 사용할 수 있는 집계 파이프라인 시간입니다. 여기에는 이벤트 속성 구문 분석, 잠재적으로 계산된 정보 추가 등이 포함됩니다.
- **인덱싱 시간** - Azure Monitor 빅 데이터 저장소에 로그 레코드를 수집하기 위해 소요된 시간입니다.

이 프로세스에 도입되는 다양한 대기 시간에 대한 세부 정보는 아래에 설명되어 있습니다.

### <a name="agent-collection-latency"></a>에이전트 수집 대기 시간

**시간은 다양합니다.**

에이전트 및 관리 솔루션은 다양한 전략을 사용하여 가상 머신에서 데이터를 수집하므로 대기 시간에 영향을 줄 수 있습니다. 몇 가지 특정 예제는 다음과 같습니다.

| 데이터 형식  | 수집 빈도  | 참고 |
|:--------------|:----------------------|:------|
| Windows 이벤트, syslog 이벤트 및 성능 메트릭 | 즉시 수집| | 
| Linux 성능 카운터 | 30초 간격으로 폴링| |
| IIS 로그 및 사용자 지정 로그 | 타임스탬프가 변경되면 수집됩니다. | IIS 로그의 경우, [IIS에 구성된 롤오버 일정](../agents/data-sources-iis-logs.md)의 영향을 받습니다. |
| Active Directory 복제 솔루션 | 5일마다 평가 | 에이전트는 평가가 완료된 경우에만 이러한 로그를 수집합니다.|
| Active Directory 평가 솔루션 | Active Directory 인프라의 주간 평가 | 에이전트는 평가가 완료된 경우에만 이러한 로그를 수집합니다.|

### <a name="agent-upload-frequency"></a>에이전트 업로드 빈도

**1분 미만**

Log Analytics 에이전트를 경량으로 유지하기 위해 에이전트는 로그를 버퍼링하고 주기적으로 Azure Monitor에 업로드합니다. 업로드 빈도는 데이터 형식에 따라 30초에서 2분 사이입니다. 대부분의 데이터는 1분 이내에 업로드됩니다. 

### <a name="network"></a>네트워크

**상황에 따라 다름**

네트워크 조건 때문에 이 데이터가 Azure Monitor 로그 수집 지점에 도달하는 대기 시간이 지연될 수도 있습니다.

### <a name="azure-metrics-resource-logs-activity-log"></a>Azure 메트릭, 리소스 로그, 활동 로그

**30초~15분**

Azure 데이터는 처리를 위해 Azure Monitor 로그 수집 지점에서 사용할 수 있는 추가 시간을 추가합니다.

- **Azure 플랫폼 메트릭은 메트릭** 데이터베이스에서 1분 이내에 사용할 수 있지만 Azure Monitor 로그 검색 지점으로 내보내는 데 3분이 더 걸립니다.
- **리소스 로그는** 일반적으로 Azure 서비스에 따라 30-90초를 추가합니다. 일부 Azure 서비스(특히 Azure SQL Database 및 Azure Virtual Network)는 현재 5분 간격으로 로그를 보고합니다. 이를 개선하기 위한 작업이 진행 중입니다. 사용자 환경에서 이 대기 시간을 검사하려면 [아래의 쿼리](#checking-ingestion-time)를 참조하세요.
- **활동 로그** 데이터는 권장 구독 수준 진단 설정을 사용하여 Azure Monitor 로그로 보낼 때 30초 이내에 수집됩니다. 그러나 레거시 통합을 대신 사용하는 경우 10-15분이 걸릴 수 있습니다.  

### <a name="management-solutions-collection"></a>관리 솔루션 수집

**상황에 따라 다름**

일부 솔루션은 에이전트에서 데이터를 수집하지 않고 추가 대기 시간을 도입하는 수집 방법을 사용할 수 있습니다. 일부 솔루션은 거의 실시간으로 수집하지 않고 주기적으로 데이터를 수집합니다. 특정 예제는 다음과 같습니다.

- Microsoft 365 솔루션은 현재 거의 실시간 대기 시간을 보장하지 않는 관리 작업 API를 사용하여 활동 로그를 폴링합니다.
- Windows Analytics 솔루션(예: 업데이트 준수) 데이터는 매일 솔루션에 의해 수집됩니다.

컬렉션 빈도를 확인하려면 [각 솔루션에 대한 설명서를](../insights/solutions.md) 참조하세요.

### <a name="pipeline-process-time"></a>파이프라인 프로세스 시간

**30~60초**

수집 시점에서 데이터를 사용할 수 있게 되면 쿼리하는 데 30-60초가 더 걸립니다.

로그 레코드가 Azure Monitor 파이프라인에 수집되고 나면([_TimeReceived](./log-standard-columns.md#_timereceived) 속성에 식별), 테넌트 격리를 보장하고 해당 데이터가 손실되지 않도록 임시 스토리지에 기록됩니다. 이 프로세스로 인해 일반적으로 5~15초가 추가됩니다. 일부 관리 솔루션은 데이터가 스트리밍될 때 데이터를 집계하고 인사이트를 파생하기 위해 부하가 높은 알고리즘을 구현합니다. 예를 들어, 네트워크 성능 모니터링은 들어오는 데이터를 3분 간격으로 집계하므로 대기 시간 3분이 추가됩니다. 대기 시간을 증가시키는 또 다른 프로세스는 사용자 지정 로그를 처리하는 프로세스입니다. 경우에 따라 이 프로세스로 인해 로그에 에이전트가 파일에서 수집하는 대기 시간이 몇 분 정도 추가될 수 있습니다.

### <a name="new-custom-data-types-provisioning"></a>새 사용자 지정 데이터 형식 프로비저닝

[사용자 지정 로그](../agents/data-sources-custom-logs.md) 또는 [데이터 수집기 API](../logs/data-collector-api.md)에서 새 사용자 지정 데이터 형식이 생성되는 경우, 시스템이 전용 스토리지 컨테이너를 만듭니다. 이는 이 데이터 형식이 처음 나타날 때만 발생하는 일회성 오버헤드입니다.

### <a name="surge-protection"></a>서지 보호

**일반적으로 1분 미만이지만 더 많을 수 있습니다.** 

Azure Monitor의 최우선 과제는 고객 데이터가 손실되지 않도록 하는 것이므로, 시스템에 데이터 서지에 대한 보호 기능이 기본 제공됩니다. 여기에는 부하가 높은 경우에도 시스템이 계속 작동하도록 유지하는 버퍼가 포함됩니다. 부하가 정상적일 때는 이러한 제어로 인해 1분 미만이 추가되지만, 극단적인 조건과 오류 상황에서는 데이터를 안전하게 유지하는 반면 상당한 시간이 추가될 수 있습니다.

### <a name="indexing-time"></a>인덱싱 시간

**5분 이하**

모든 빅 데이터 플랫폼에는 데이터를 즉시 액세스할 수 있도록 하는 것과 분석 및 고급 검색 기능을 제공하는 것 사이의 적절한 균형이 기본 제공됩니다. Azure Monitor를 사용하면 수십억 개의 레코드에 대해 강력한 쿼리를 실행하고 몇 초 내에 결과를 얻을 수 있습니다. 이는 인프라가 수집 중에 데이터를 완전히 변환하고 고유한 압축 구조에 저장하기 때문에 가능합니다. 이러한 구조를 만들기에 충분할 때까지 데이터가 시스템에 버퍼링됩니다. 이 작업이 완료되어야 로그 레코드가 검색 결과에 나타납니다.

현재 이 프로세스는 데이터 양이 적을 경우, 약 5분 정도 걸리지만, 데이터 속도가 빠를수록 시간이 단축됩니다. 모순되는 것 같지만, 이 프로세스를 통해 대량 프로덕션 워크로드의 대기 시간을 최적화할 수 있습니다.

## <a name="checking-ingestion-time"></a>수집 시간 확인
수집 시간은 리소스와 상황에 따라 달라질 수 있습니다. 로그 쿼리를 사용하여 현재 환경의 특정 동작을 파악할 수 있습니다. 다음 표에서는 레코드를 만들고 Azure Monitor로 전송할 때 레코드에 대해 다른 시간을 결정하는 방법을 지정합니다.

| 단계 | 속성 또는 함수 | 주석 |
|:---|:---|:---|
| 데이터 원본에 생성되는 레코드 | [TimeGenerated](./log-standard-columns.md#timegenerated) <br>데이터 원본에서 이 값을 설정하지 않으면 _TimeReceived와 같은 시간으로 설정됩니다. |
| Azure Monitor 수집 엔드포인트에서 수신된 레코드 | [_TimeReceived](./log-standard-columns.md#_timereceived) | 이 필드는 대량 처리를 위해 최적화되지 않았으므로 대규모 데이터 세트를 필터링하는 데 사용하면 안 됩니다. |
| 작업 영역에 저장되어 쿼리에 사용할 수 있는 레코드 | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | 특정 기간에 수집된 레코드만 필터링해야 하는 경우에는 ingestion_time()을 사용하는 것이 좋습니다. 이 경우 더 큰 범위로 TimeGenerated 필터를 추가하는 것도 좋습니다. |

### <a name="ingestion-latency-delays"></a>수집 대기 시간 지연
[ingestion_time()](/azure/kusto/query/ingestiontimefunction) 함수의 결과를 _TimeGenerated_ 속성의 내용과 비교하면 특정 레코드의 대기 시간을 측정할 수 있습니다. 다양한 집계에서 이 데이터를 사용하여 수집 대기 시간이 발생하는 방식을 확인할 수 있습니다. 수집 시간의 백분위수 몇 개를 검사하면 대량의 데이터 수집 시간 관련 정보를 파악할 수 있습니다. 

예를 들어 다음 쿼리는 8시간 전에 수집 시간이 가장 긴 컴퓨터를 보여 줍니다. 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

위의 백분위 수 검사는 대기 시간의 일반적인 추세를 찾는 데 유용합니다. 대기 시간의 단기 스파이크를 식별하기 위해서는 최대값(`max()`)이 더 효과적일 수 있습니다.

일정 기간 동안 특정 컴퓨터의 수집 시간을 드릴다운하려는 경우 다음 쿼리를 사용합니다. 이 쿼리도 전날의 데이터를 그래프에 표시합니다. 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
컴퓨터가 있는 국가/지역(해당 IP 국가를 기준으로 함)별 컴퓨터 수집 시간을 표시하려면 다음 쿼리를 사용합니다. 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
에이전트에서 생성되는 각 데이터 형식의 수집 대기 시간은 서로 다를 수 있으므로 위의 쿼리를 다른 형식에 사용할 수도 있습니다. 여러 Azure 서비스의 수집 시간을 검사하려면 다음 쿼리를 사용합니다. 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>응답하지 않는 리소스 
리소스에서 데이터 전송이 중지되는 경우도 있습니다. 리소스가 데이터를 전송하고 있는지를 파악하려면 표준 _TimeGenerated_ 필드를 통해 식별 가능한 최신 레코드를 확인하세요.  

VM 가용성을 확인하려면 _Heartbeat_ 테이블을 사용합니다. 에이전트에서는 1분마다 하트비트를 전송하기 때문입니다. 최근 하트비트를 보고하지 않은 활성 컴퓨터 목록을 표시하려면 다음 쿼리를 사용합니다. 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>다음 단계
* Azure Monitor에 대한 [SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/monitor/v1_3/)를 읽어 보세요.