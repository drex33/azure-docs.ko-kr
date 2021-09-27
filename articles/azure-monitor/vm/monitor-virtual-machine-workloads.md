---
title: Azure Monitor를 사용하여 Azure 가상 머신 모니터링 - 워크로드
description: Azure Monitor에서 가상 머신의 게스트 워크로드를 모니터링하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 64ef920e727baf559b71cac416404fb740e47625
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788556"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>Azure Monitor를 사용하여 Azure 가상 머신 모니터링 - 워크로드
이 문서는 [Azure Monitor에서 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md) 시나리오의 일부입니다. 가상 머신의 게스트 운영 체제에서 실행되는 워크로드를 모니터링하는 방법에 대해 설명합니다. 이 문서에는 가상 머신의 다양한 데이터 원본을 분석하고 경고하는 방법에 대한 자세한 내용이 포함되어 있습니다.

## <a name="configure-additional-data-collection"></a>추가 데이터 수집 구성
VM 인사이트는 사용하도록 설정된 컴퓨터의 게스트 운영 체제에서 성능 데이터만 수집합니다. Log Analytics 작업 영역을 구성하여 에이전트에서 추가 성능 데이터, 이벤트 및 기타 모니터링 데이터의 수집을 사용하도록 설정할 수 있습니다. 작업 영역에 연결하는 모든 에이전트에서 자동으로 구성을 다운로드하고 정의된 데이터 수집을 즉시 시작하므로 한 번만 구성됩니다.

사용할 수 있는 데이터 원본의 목록과 이를 구성하는 방법에 대한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](../agents/agent-data-sources.md)을 참조하세요.

> [!NOTE]
> 다른 컴퓨터에 대한 데이터 수집은 선택적으로 구성할 수 없습니다. 작업 영역에 연결된 모든 컴퓨터에서 해당 작업 영역에 대한 구성을 사용합니다.

> [!IMPORTANT]
> 필요한 데이터만 수집해야 합니다. 비용은 작업 영역에 수집된 데이터와 연결됩니다. 수집하는 데이터는 특정 분석 및 경고 시나리오만 지원해야 합니다.

## <a name="convert-management-pack-logic"></a>관리 팩 논리 변환
Azure Monitor를 구현하는 많은 고객은 현재 System Center Operations Manager에서 관리 팩을 사용하여 가상 머신 워크로드를 모니터링합니다. 플랫폼이 근본적으로 다르므로 자산을 Operations Manager에서 Azure Monitor로 변환하는 마이그레이션 도구가 없습니다. 대신 Operations Manager를 계속 사용하는 동안 마이그레이션에서 표준 Azure Monitor 구현을 구성합니다. 다양한 애플리케이션 및 구성 요소에 대한 요구 사항을 충족하도록 Azure Monitor를 사용자 지정하고 더 많은 기능이 추가됨에 따라 Operations Manager에서 다양한 관리 팩 및 에이전트의 사용 중지를 시작할 수 있습니다.

관리 팩의 전체 기능을 복제하는 대신 관리 팩에서 제공하는 중요한 모니터링을 분석합니다. 이전 섹션에서 설명한 방법을 사용하여 이러한 모니터링 요구 사항을 복제할 수 있는지 여부를 결정합니다. 대부분의 경우 특정 관리 팩을 사용 중지할 수 있는 충분한 기능을 복제하는 Azure Monitor에서 데이터 수집 및 경고 규칙을 구성할 수 있습니다. 관리 팩에서 수백, 수천 개의 규칙과 모니터를 포함할 수 있는 경우가 많습니다.

대부분의 시나리오에서 Operations Manager는 동일한 규칙 또는 모니터에서 데이터 수집과 경고 조건을 결합합니다. Azure Monitor에서 모든 경고 시나리오에 대한 데이터 수집 및 경고 규칙을 구성해야 합니다.

한 가지 전략은 환경에서 경고를 트리거한 모니터와 규칙에 집중하는 것입니다. 시간이 지남에 따라 경고를 식별하는 데 도움이 될 수 있는 **경고** 및 **가장 일반적인 경고** 와 같이 [Operations Manager에서 사용할 수 있는 기존 보고서](/system-center/scom/manage-reports-installed-during-setup)를 참조하세요. 또한 작업 데이터베이스에서 다음 쿼리를 실행하여 가장 일반적인 최근 경고를 평가할 수 있습니다.

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

출력을 평가하여 마이그레이션에 대한 특정 경고를 식별합니다. 튜닝되었거나 문제가 있는 것으로 알려진 경고는 무시합니다. 관리 팩을 검토하여 한 번도 발생하지 않은 중요한 관심 경고를 식별합니다.

## <a name="windows-or-syslog-event"></a>Windows 또는 Syslog 이벤트
이 일반적인 모니터링 시나리오에서 운영 체제와 애플리케이션은 Windows 이벤트 또는 Syslog에 기록합니다. 단일 이벤트가 발견되는 즉시 경고를 만듭니다. 또는 특정 시간 범위 내에서 일련의 일치하는 이벤트를 기다릴 수 있습니다.

이러한 이벤트를 수집하려면 [Windows 이벤트](../agents/data-sources-windows-events.md) 또는 [Syslog 이벤트](../agents/data-sources-windows-events.md)를 수집하도록 Log Analytics 작업 영역을 구성합니다. 작업 영역에서 이 데이터를 수집하고 보존하는 데 비용이 듭니다.

Windows 이벤트는 [Event](/azure/azure-monitor/reference/tables/event) 테이블에 저장되고, Syslog 이벤트는 Log Analytics 작업 영역의 [Syslog](/azure/azure-monitor/reference/tables/syslog) 테이블에 저장됩니다.

### <a name="sample-log-queries"></a>샘플 로그 쿼리

- **컴퓨터 이벤트 로그 및 이벤트 유형별 이벤트 수를 계산합니다.**

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- **컴퓨터 이벤트 로그 및 이벤트 ID별 이벤트 수를 계산합니다.**
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>샘플 경고 규칙
다음 샘플에서는 특정 Windows 이벤트가 만들어질 때 경고를 만듭니다. 메트릭 측정 경고 규칙을 사용하여 각 컴퓨터에 대해 별도의 경고를 만듭니다.

- **특정 Windows 이벤트에 대한 경고 규칙을 만듭니다.**

   다음 예제에서는 응용 프로그램 로그의 이벤트를 보여 줍니다. 0의 임계값과 0보다 큰 연속 위반을 지정합니다.

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **특정 심각도의 Syslog 이벤트에 대한 경고 규칙을 만듭니다.**

   다음 예제에서는 오류 권한 부여 이벤트를 보여 줍니다. 0의 임계값과 0보다 큰 연속 위반을 지정합니다.

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>사용자 지정 성능 카운터
애플리케이션에서 만들거나 VM 인사이트에서 수집하지 않는 게스트 운영 체제에서 만든 성능 카운터가 필요할 수 있습니다. 이 [성능 데이터](../agents/data-sources-windows-events.md)를 수집하도록 Log Analytics 작업 영역을 구성합니다. 작업 영역에서 이 데이터를 수집하고 보존하는 데 비용이 듭니다. VM 인사이트에서 이미 수집하고 있는 성능 데이터를 수집하지 않도록 주의하세요.

작업 영역에서 구성한 성능 데이터는 [Perf](/azure/azure-monitor/reference/tables/perf) 테이블에 저장됩니다. 이 테이블은 VM 인사이트에서 사용하는 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 테이블과 다른 구조입니다.

### <a name="sample-log-queries"></a>샘플 로그 쿼리

사용자 지정 성능 카운터를 사용하는 로그 쿼리의 예제는 [성능 레코드를 통한 로그 쿼리](../agents/data-sources-performance-counters.md#log-queries-with-performance-records)를 참조하세요.

### <a name="sample-alerts"></a>샘플 경고

- **카운터의 최댓값에 대한 경고를 만듭니다.**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **카운터의 평균 값에 대한 경고를 만듭니다.**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>텍스트 로그
일부 애플리케이션은 기록된 이벤트를 가상 머신에 저장된 텍스트 로그에 기록합니다. Log Analytics 작업 영역에서 이러한 이벤트를 수집할 [사용자 지정 로그](../agents/data-sources-custom-logs.md)를 정의합니다. 텍스트 로그의 위치와 자세한 구성을 정의합니다. 작업 영역에서 이 데이터를 수집하고 보존하는 데 비용이 듭니다.

텍스트 로그의 이벤트는 **MyTable_CL** 과 비슷한 이름의 테이블에 저장됩니다. 로그를 구성할 때 로그의 이름과 구조를 정의합니다.

### <a name="sample-log-queries"></a>샘플 로그 쿼리
여기에 사용된 열 이름은 예일 뿐입니다. 특정 로그를 정의할 때 해당 로그의 열 이름을 정의합니다. 로그의 열 이름은 다를 수 있습니다.

- **코드별 이벤트 수를 계산합니다.**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>샘플 경고 규칙

- **오류 이벤트에 대한 경고 규칙을 만듭니다.**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>IIS 로그
Windows 컴퓨터에서 실행되는 IIS는 로그를 텍스트 파일에 기록합니다. [IIS 로그](../agents/data-sources-iis-logs.md)를 수집하도록 Log Analytics 작업 영역을 구성합니다. 작업 영역에서 이 데이터를 수집하고 보존하는 데 비용이 듭니다.

IIS 로그의 레코드는 Log Analytics 작업 영역의 [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) 테이블에 저장됩니다.

### <a name="sample-log-queries"></a>샘플 로그 쿼리

- **www.contoso.com 호스트에 대한 URL별 IIS 로그 항목 수를 계산합니다.**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- **각 IIS 컴퓨터에서 받은 총 바이트 수를 검토합니다.**

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>샘플 경고 규칙

- **반환 상태가 500인 레코드에 대한 경고 규칙을 만듭니다.**
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>서비스 또는 디먼
Windows 서비스 또는 Linux 디먼의 상태를 모니터링하려면 [Azure Automation](../../automation/automation-intro.md)에서 [변경 내용 추적 및 인벤토리](../../automation/change-tracking/overview.md) 솔루션을 사용하도록 설정합니다. Azure Monitor에는 서비스 또는 디먼의 상태를 모니터링할 수 있는 기능이 없습니다. Windows 이벤트 로그에서 이벤트를 찾는 것과 같이 사용할 수 있는 몇 가지 방법이 있지만 이 방법은 안정적이지 않습니다. 컴퓨터에서 실행되는 서비스와 관련된 프로세스는 [VMProcess](/azure/azure-monitor/reference/tables/vmprocess) 테이블에서 찾을 수도 있습니다. 이 테이블은 1시간마다 업데이트되며, 일반적으로 경고에 충분하지 않습니다.

> [!NOTE]
> 변경 내용 추적 및 분석 솔루션은 VM 인사이트의 [변경 분석](vminsights-change-analysis.md) 기능과 다릅니다. 이 기능은 공개 미리 보기에 있지만 아직 이 시나리오에 포함되지 않았습니다.

가상 머신에서 변경 내용 추적 솔루션을 사용하도록 설정하는 다양한 옵션은 [변경 내용 추적 및 인벤토리 사용](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory)을 참조하세요. 이 솔루션에는 가상 머신을 규모에 맞게 구성하는 방법이 포함되어 있습니다. 솔루션을 지원하려면 [Azure Automation 계정을 만들어야](../../automation/quickstarts/create-account-portal.md) 합니다.

변경 내용 추적 및 인벤토리를 사용하도록 설정하면 두 개의 새 테이블이 Log Analytics 작업 영역에 만들어집니다. 다음 표를 로그 쿼리 경고 규칙에 사용합니다.

| 테이블 | 설명 |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | 게스트 내 구성 데이터 변경 내용 |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | 게스트 내 구성 데이터에 대해 마지막으로 보고된 상태 |


### <a name="sample-log-queries"></a>샘플 로그 쿼리

- **최근에 시작된 모든 서비스와 디먼을 나열합니다.**
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>샘플 경고 규칙

- **특정 서비스가 중지되는 시기를 기준으로 하는 경고 규칙을 만듭니다.**

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- **서비스 세트 중 하나가 중지되는 시기를 기준으로 하는 경고 규칙을 만듭니다.**
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>포트 모니터링
포트 모니터링은 컴퓨터가 특정 포트에서 수신 대기하는지 확인합니다. 여기서는 포트 모니터링에 대한 두 가지 잠재적 전략에 대해 설명합니다.

### <a name="dependency-agent-tables"></a>종속성 에이전트 테이블
[VMConnection](/azure/azure-monitor/reference/tables/vmconnection) 및 [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport)를 사용하여 컴퓨터의 연결 및 포트를 분석합니다. VMBoundPort 테이블은 컴퓨터에서 실행되는 각 프로세스와 수신 대기하는 포트를 사용하여 1분마다 업데이트됩니다. 누락된 하트비트 경고와 비슷한 로그 쿼리 경고를 만들어 중지된 프로세스를 찾거나 컴퓨터가 특정 포트에서 수신 대기하지 않을 때 경고할 수 있습니다.

### <a name="sample-log-queries"></a>샘플 로그 쿼리

- **VM에서 열려 있는 포트 수를 검토합니다. 이는 구성 및 보안 취약성이 있는 VM을 평가하는 데 유용합니다.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- **VM에서 바인딩된 포트를 나열합니다. 이는 구성 및 보안 취약성이 있는 VM을 평가하는 데 유용합니다.**

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **포트별 네트워크 작업을 분석하여 애플리케이션 또는 서비스를 구성하는 방법을 결정합니다.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **VM에 대해 보내고 받은 바이트 수 추세를 검토합니다.**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **시간이 지남에 따른 연결 실패 횟수를 사용하여 실패율이 안정적인지 아니면 변화하는지 확인합니다.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **상태 추세를 연결하여 컴퓨터의 동작 및 연결 상태를 분석합니다.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>연결 관리자
[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)의 [연결 모니터](../../network-watcher/connection-monitor-overview.md) 기능은 가상 머신의 포트에 대한 연결을 테스트하는 데 사용됩니다. 테스트는 컴퓨터가 포트에서 수신 대기하고 네트워크에서 액세스할 수 있는지 확인합니다.
연결 관리자를 사용하려면 테스트를 시작하는 클라이언트 컴퓨터에 Network Watcher 확장이 필요합니다. 테스트되는 컴퓨터에는 설치할 필요가 없습니다. 자세한 내용은 [자습서 - Azure Portal을 사용하여 네트워크 통신 모니터링](../../network-watcher/connection-monitor.md)을 참조하세요.

연결 관리자에 대한 추가 비용이 있습니다. 자세한 내용은 [Network Watcher 가격](https://azure.microsoft.com/pricing/details/network-watcher/)을 참조하세요.

## <a name="run-a-process-on-a-local-machine"></a>로컬 컴퓨터에서 프로세스 실행
일부 워크로드를 모니터링하려면 로컬 프로세스가 필요합니다. 예를 들어 애플리케이션에 연결하고 데이터를 수집하거나 처리하기 위해 로컬 컴퓨터에서 실행되는 PowerShell 스크립트가 있습니다. [Azure Automation](../../automation/automation-intro.md)의 일부인 [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)를 사용하여 로컬 PowerShell 스크립트를 실행할 수 있습니다. Hybrid Runbook Worker에 대해 직접 청구되는 비용은 없지만, 이를 사용하는 각 Runbook에 대한 비용이 있습니다.

Runbook은 로컬 컴퓨터의 모든 리소스에 액세스하여 필요한 데이터를 수집할 수 있습니다. 이는 데이터를 Azure Monitor에 직접 보내거나 경고를 만들 수 없습니다. 경고를 만들려면 Runbook에서 항목을 사용자 지정 로그에 쓴 다음, Azure Monitor에서 수집하도록 해당 로그를 구성합니다. 해당 로그 항목에서 실행되는 로그 쿼리 경고 규칙을 만듭니다.

## <a name="synthetic-transactions"></a>가상 트랜잭션
가상 트랜잭션은 컴퓨터에서 실행되는 애플리케이션 또는 서비스에 연결하여 사용자 연결 또는 실제 사용자 트래픽을 시뮬레이션합니다. 애플리케이션을 사용할 수 있는 경우 컴퓨터가 제대로 실행되고 있다고 가정할 수 있습니다. 이 기능은 Azure Monitor의 [Application Insights](../app/app-insights-overview.md)에서 제공합니다. 이는 인터넷에서 액세스할 수 있는 애플리케이션에서만 작동합니다. 내부 애플리케이션의 경우 테스트를 수행하는 특정 Microsoft URL에서 액세스할 수 있도록 방화벽을 열어야 합니다. 또는 System Center Operations Manager와 같은 대체 모니터링 솔루션을 사용할 수 있습니다.

|메서드 | 설명 |
|:---|:---|
| [URL 테스트](../app/monitor-web-app-availability.md) | HTTP를 사용할 수 있고 웹 페이지를 반환하는지 확인합니다. |
| [다단계 테스트](../app/availability-multistep.md) | 사용자 세션을 시뮬레이션합니다. |

## <a name="sql-server"></a>SQL Server

[SQL 인사이트](../insights/sql-insights-overview.md)를 사용하여 가상 머신에서 실행되는 SQL Server를 모니터링합니다.

## <a name="next-steps"></a>다음 단계

* [로그 쿼리를 사용하여 Azure Monitor 로그의 데이터를 분석하는 방법을 알아봅니다.](../logs/get-started-queries.md)
* [메트릭과 로그를 사용하는 Azure Monitor의 경고에 대해 알아봅니다.](../alerts/alerts-overview.md)