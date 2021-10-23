---
title: 컨테이너 인사이트에서 로그를 쿼리하는 방법
description: 컨테이너 인사이트는 메트릭 및 로그 데이터를 수집합니다. 이 문서에서는 레코드를 설명하며 샘플 쿼리가 포함되어 있습니다.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: ff2ce2fa2e70400b4b591f4e8f3dbb50e3dc6415
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240132"
---
# <a name="how-to-query-logs-from-container-insights"></a>컨테이너 인사이트에서 로그를 쿼리하는 방법

컨테이너 인사이트는 컨테이너 호스트 및 컨테이너에서 성능 메트릭, 인벤토리 데이터 및 상태 정보를 수집합니다. 데이터는 3분 마다 수집되고 Azure Monitor의 [Log Analytics](../logs/log-analytics-overview.md)를 사용하여 [로그 쿼리](../logs/log-query-overview.md)에 사용할 수 있는 Azure Monitor의 Log Analytics 작업 영역으로 전달됩니다. 마이그레이션 계획, 용량 분석, 검색 및 주문형 성능 문제 해결을 포함하는 시나리오에 이 데이터를 적용할 수 있습니다. Azure Monitor Logs에서는 현재 클러스터 구성이 최적 상태로 실행되고 있는지를 파악하는 데 도움이 되도록 추세를 찾아보거나, 병목 상태를 진단하거나, 예측하거나 데이터 간 상관 관계를 파악할 수 있습니다.

이러한 쿼리를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor Log Analytics에서 쿼리 사용](../logs/queries.md)을 참조하고 Log Analytics를 사용하여 쿼리를 실행하고 결과로 작업하는 방법에 대한 전체 자습서는 [Log Analytics 자습서](../logs/log-analytics-tutorial.md) 참조하세요.

## <a name="open-log-analytics"></a>Log Analytics 열기
Log Analytics를 시작할 수 있는 여러 옵션이 있으며 각 옵션은 서로 다른 [범위](../logs/scope.md)로 시작합니다. 작업 영역에서 모든 데이터에 액세스하려면, **모니터** 메뉴에서 **로그** 를 선택합니다. 단일 Kubernetes 클러스터로 데이터를 제한하려면, 해당 클러스터의 메뉴에서 **로그** 를 선택합니다. 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="Log Analytics 시작" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>기존 로그 쿼리
Log Analytics를 사용하기 위해 로그 쿼리 작성 방법을 반드시 이해할 필요는 없습니다. 선택하여 수정 없이 실행하거나 사용자 지정 쿼리의 시작점으로 사용할 수 있는 여러 쿼리가 미리 빌드되어 있습니다. Log Analytics 화면의 맨 위에 있는 **쿼리** 를 클릭하고 **Kubernetes Services** 의 **리소스 유형** 을 사용하여 쿼리를 봅니다. 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="Kubernetes에 대한 Log Analytics 쿼리" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>컨테이너 테이블
컨테이너 인사이트에서 사용하는 테이블 목록 및 자세한 설명은 [Azure Monitor 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services)를 참조하세요. 이러한 테이블은 모두 로그 쿼리에 사용할 수 있습니다.


## <a name="example-log-queries"></a>샘플 로그 쿼리
한두 가지 예제로 시작하는 쿼리를 작성한 다음, 요구 사항에 맞게 수정하는 것이 유용한 경우가 많습니다. 고급 쿼리를 작성하는 데 도움이 되도록 다음과 같은 샘플 쿼리를 테스트해 볼 수 있습니다.

### <a name="list-all-of-a-containers-lifecycle-information"></a>컨테이너의 수명 주기 정보 모두 나열

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>kubernetes 이벤트

``` kusto
KubeEvents
| where not(isempty(Namespace))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>이미지 인벤토리

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>컨테이너 CPU

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>컨테이너 메모리

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>사용자 지정 메트릭의 분당 요청 수

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc
| project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>이름 및 네임스페이스별 Pod

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>Pod 스케일 아웃(HPA)
각 배포에서 스케일 아웃된 복제본의 수를 반환합니다. HPA에 구성된 최대 복제본 수를 사용하여 스케일 아웃 비율을 계산합니다.


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Nodepool 스케일 아웃 
각 노드 풀의 활성 노드 수를 반환합니다. 자동 스케일러 설정에서 사용 가능한 활성 노드 수와 최대 노드 구성 수를 계산하여 스케일 아웃 비율을 결정합니다. **결과 수** 경고 규칙에 사용하려면, 쿼리의 주석 처리된 줄을 참조하세요.

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>시스템 컨테이너(replicaset) 가용성
시스템 컨테이너(replicasets)를 반환하고 사용할 수 없는 비율을 보고합니다. **결과 수** 경고 규칙에 사용하려면, 쿼리의 주석 처리된 줄을 참조하세요.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>시스템 컨테이너(daemonsets) 가용성
시스템 컨테이너 (daemonsets)를 반환하고 사용할 수 없는 비율을 보고합니다. **결과 수** 경고 규칙에 사용하려면, 쿼리의 주석 처리된 줄을 참조하세요.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>리소스 로그
AKS에 대한 리소스 로그는 [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블에 저장되므로 다른 로그를 **Category** 열로 구분할 수 있습니다. 각 범주에 대한 설명은 [AKS reference resource logs](../../aks/monitor-aks-reference.md)를 참조하세요. 다음 예에서는 AKS 클러스터에 대한 리소스 로그를 Log Analytics 작업 영역으로 보내기 위한 진단 확장이 필요합니다. 자세한 내용은 [모니터링 구성](../../aks/monitor-aks.md#configure-monitoring)을 참조하세요.

### <a name="api-server-logs"></a>API 서버 로그

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>각 범주에 대한 로그 수

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>Prometheus 메트릭 데이터 쿼리

다음 예제는 노드당 디스크 읽기 수/초를 보여 주는 Prometheus 메트릭 쿼리입니다.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Namespace로 필터링된 Azure Monitor로 스크랩된 Prometheus 메트릭을 보려면 "prometheus"를 지정합니다. 다음은 `default` kubernetes 네임스페이스에서 프로메테우스 메트릭을 보는 샘플 쿼리입니다.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

또한 이름으로 프로메테우스 데이터를 직접 쿼리할 수 있습니다.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>쿼리 구성 또는 스크랩핑 오류

구성 또는 스크래핑 오류를 조사하기 위해, 다음 예제 쿼리는 `KubeMonAgentEvents` 테이블에서 정보 이벤트를 반환합니다.

```
KubeMonAgentEvents | where Level != "Info" 
```

출력은 다음 예제와 유사한 결과를 표시합니다.

![에이전트의 정보 이벤트에 대한 로그 쿼리 결과](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>다음 단계

Container Insights에는 미리 정의된 경고 집합이 없습니다. [Container Insights로 성능 경고 만들기](./container-insights-log-alerts.md)를 검토하여 DevOps 또는 운영 프로세스 및 절차를 지원하기 위해 높은 CPU 및 메모리 사용률에 대한 권장 경고를 만드는 방법을 알아봅니다.
