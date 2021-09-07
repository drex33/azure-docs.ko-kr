---
title: 컨테이너 인사이트의 로그 경고 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트에서 메모리 및 CPU 사용률에 대한 사용자 지정 로그 경고를 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528794"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>컨테이너 인사이트에서 로그 경고를 만드는 방법

컨테이너 인사이트는 관리형 또는 자체 관리형 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 중요한 문제가 생겼을 경우 경고하기 위해, 이 문서에서는 AKS 클러스터를 사용하여 다음과 같은 상황에 대한 로그 기반 경고를 만드는 방법을 설명합니다.

- 클러스터 노드의 CPU 또는 메모리 사용률이 임계값을 초과하는 경우
- 컨트롤러 내 컨테이너의 CPU 또는 메모리 사용률이 해당 리소스에 설정된 제한과 비교하여 임계값을 초과하는 경우
- *NotReady* 상태 노드 개수
- *Failed*, *Pending*, *Unknown*, *Running* 또는 *Succeeded* Pod 단계 개수
- 클러스터 노드의 사용 가능한 디스크 공간이 임계값을 초과하는 경우

높은 CPU나 메모리 사용률, 또는 클러스터 노드에서 사용 가능한 디스크 공간 부족에 대해 경고하려면 제공된 쿼리를 사용하여 메트릭 경고 또는 메트릭 측정 경고를 만듭니다. 메트릭 경고는 로그 경고보다 대기 시간이 낮지만, 로그 경고는 고급 쿼리 기능을 제공하며 한층 정교합니다. 로그 경고 쿼리는 *now* 연산자를 사용하고 1시간 이전으로 돌아가는 방법으로 현재와 날짜/시간을 비교합니다. (컨테이너 인사이트는 모든 날짜를 UTC(협정 세계시) 형식으로 저장합니다.)

> [!IMPORTANT]
> 대부분의 경고 규칙에는 규칙 유형, 포함된 차원 수 및 실행 빈도에 따라 달라지는 비용이 있습니다. 경고 규칙을 만들기 전에 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에서 **경고 규칙** 을 참조하세요.

Azure Monitor 경고에 익숙하지 않은 경우 시작하기 전에 [Microsoft Azure의 경고 개요](../alerts/alerts-overview.md)를 참조하세요. 로그 쿼리를 사용하는 경고에 대한 자세한 내용은 [Azure Monitor의 로그 경고](../alerts/alerts-unified-log.md)를 참조하세요. 메트릭 경고에 대한 자세한 내용은 [Azure Monitor의 메트릭 경고](../alerts/alerts-metric-overview.md)를 참조하세요.

## <a name="log-query-measurements"></a>로그 쿼리 측정값
로그 쿼리 경고는 각각 가상 머신을 모니터링하는 고유한 시나리오를 지원하는 로그 쿼리 결과의 두 가지 측정을 수행할 수 있습니다.

[메트릭 측정](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)은 정의된 임계값을 초과하는 숫자 값이 있는 쿼리 결과의 각 레코드에 대해 별도의 경고를 만듭니다. 이러한 측정은 CPU와 같은 숫자 데이터에 적합합니다.

[결과 수](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)는 쿼리에서 지정된 수 이상의 레코드를 반환할 때 단일 경고를 만듭니다. 이러한 측정은 숫자 이외의 데이터를 구하거나 여러 컴퓨터에서 성능 추세를 분석하는 데 적합합니다. 경고 수를 최소화하거나 여러 머신에 동일한 오류 조건이 있는 경우에만 경고를 만들려는 경우에도 이 전략을 선택할 수 있습니다.

> [!NOTE]
> 현재 퍼블릭 미리 보기로 제공되는 리소스 중심 로그 경고 규칙은 로그 쿼리 경고를 간소화하고 현재 메트릭 측정 쿼리에서 제공하는 기능을 대체합니다. AKS 클러스터를 영향을 받는 리소스로 더 잘 식별하는 규칙의 대상으로 사용할 수 있습니다. 리소스 센터 로그 쿼리 경고가 일반화되면 이 시나리오의 지침이 업데이트됩니다.

## <a name="create-a-log-query-alert-rule"></a>로그 쿼리 경고 규칙 만들기
[로그 쿼리 경고 측정값 비교](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures)에서는 각 측정 유형에 대한 로그 쿼리 경고 규칙의 전체 연습이 제공됩니다. 여기에는 각 측정값을 지원하는 로그 쿼리의 비교가 포함됩니다. 이러한 동일한 프로세스를 사용하여 이 문서의 쿼리와 유사한 쿼리를 사용하여 AKS 클러스터에 대한 경고 규칙을 만들 수 있습니다.

## <a name="resource-utilization"></a>리소스 사용률 

**분당 멤버 노드 CPU 활용률의 평균으로 계산한 평균 CPU 활용률(메트릭 측정값)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

**분당 멤버 노드 메모리 활용률의 평균으로 계산한 평균 메모리 활용률(메트릭 측정값)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>다음 쿼리는 자리 표시자 값 \<your-cluster-name> 및 \<your-controller-name>를 사용하여 클러스터와 컨트롤러를 나타냅니다. 경고 설정 시 해당 값을 사용자 환경에 맞는 값으로 바꿉니다.

**1분마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 CPU 사용률로 계산한 컨트롤러에 있는 모든 컨테이너의 평균 CPU 사용률(메트릭 측정값)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

**1분마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 메모리 사용률로 계산한 컨트롤러에 있는 모든 컨테이너의 평균 메모리 사용률(메트릭 측정값)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>리소스 가용성 

**상태가 Ready 및 NotReady인 노드 및 개수(메트릭 측정값)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
다음 쿼리는 모든 단계(*Failed*, *Pending*, *Unknown*, *Running* 또는 *Succeeded*)를 기반으로 Pod 단계 개수를 반환합니다.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>*Pending*, *Failed* 또는 *Unknown* 과 같은 특정 Pod 단계에 대해 경고하려면 쿼리의 마지막 줄을 수정합니다. 예를 들어 *FailedCount* 에 대해 경고하려면 다음을 사용합니다. <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

다음 쿼리는 사용 가능한 공간의 사용률이 90%를 초과하는 클러스터 노드 디스크를 반환합니다. 클러스터 ID를 가져오려면 먼저 다음 쿼리를 실행하고 `ClusterId` 속성의 값을 복사합니다.

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



**개별 컨테이너 다시 시작(결과 수)**<br>
개별 시스템 컨테이너 다시 시작 횟수가 지난 10분 동안 임계값을 초과하면 경고합니다.

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>다음 단계

- 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-query.md)를 확인하세요.

- Azure Monitor 및 Kubernetes 클러스터의 기타 측면을 모니터링하는 방법에 대한 자세한 내용은 [Kubernetes 클러스터 성능 보기](container-insights-analyze.md) 및 [Kubernetes 클러스터 상태 보기](./container-insights-overview.md)를 참조하세요.