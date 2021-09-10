---
title: Azure Monitor 경고를 사용하여 Azure 가상 머신 모니터링
description: Azure Monitor를 사용하여 가상 컴퓨터와 해당 게스트 작업에서 경고를 만드는 방법에 대해 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: b272d4cb11ab948043f6c47b5be12fc0488d070f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531059"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>Azure Monitor 경고를 사용하여 Azure 가상 머신 모니터링

이 문서는 [Azure Monitor 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md)시나리오의 일부입니다. 가상 컴퓨터 및 게스트 운영 체제에 대한 경고 규칙을 만드는 방법에 대한 지침을 제공합니다. [Azure Monitor 경고는](../alerts/alerts-overview.md) 모니터링 데이터의 흥미로운 데이터와 패턴을 사전에 알립니다. 가상 컴퓨터에는 미리 구성된 경고 규칙이 없지만, VM 정보에 의해 수집된 데이터를 기반으로 직접 만들 수 있습니다. 

> [!NOTE]
> 이 문서에서 설명하는 경고는 현재 공개 미리보기로 제공되는 기능인 [VM 게스트 상태를 위한 Azure Monitorh](vminsights-health-overview.md)에서 만든 경고를 포함하지 않습니다. 이 기능이 일반 공급에 가까워짐에 따라 경고 지침이 통합됩니다.

> [!IMPORTANT]
> 대부분의 경고 규칙에는 규칙 유형, 포함된 차원 수 및 실행 빈도에 따라 달라지는 비용이 있습니다. 경고 규칙을 만들기 전에 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 있는 **경고 규칙** 을 참조하세요.

## <a name="choose-the-alert-type"></a>경고 유형 선택
Azure Monitor에서 가장 일반적인 경고 규칙 유형은 [메트릭 경고](../alerts/alerts-metric.md) 와 [로그 쿼리 경고](../alerts/alerts-log-query.md)입니다. 특정 시나리오에 만드는 경고 규칙의 유형은 사용자가 경고하는 데이터의 위치에 따라 달라집니다. 메트릭 및 로그에서 특정 경고 시나리오에 대한 데이터를 사용할 수 있는 경우가 있을 수 있으며, 사용할 규칙 유형을 결정해야 합니다. 특정 데이터를 수집하는 방법에 유연성이 있을 수 있으며, 경고 규칙 유형을 판단하여 데이터 수집 방법에 대한 판단을 내릴 수도 있습니다.

일반적으로 가능하면 로그 경고보다는 메트릭 경고를 사용하는 것이 가장 좋은 전략입니다.왜냐하면 메트릭 경고가 응답성이 높고 상태 확인이 가능하기 때문입니다. 메트릭 경고를 사용하려면 경고하려 하는 데이터를 메트릭에 사용할 수 있어야 합니다. VM 인사이트는 현재 모든 데이터를 로그에 보내기 때문에 게스트 운영 체제의 데이터로 메트릭 경고를 사용하려면 Azure Monitor 에이전트를 설치해야 합니다. 메트릭에서 사용할 수 없거나 메트릭 경고 규칙에 대해 상대적으로 단순한 논리를 넘어서는 논리가 필요한 경우 메트릭 데이터와 함께 로그 쿼리 경고를 사용합니다.

### <a name="metric-alert-rules"></a>메트릭 경고 규칙
[메트릭 경고 규칙](../alerts/alerts-metric.md)은 특정 메트릭이 임계값을 초과할 때 경고할 시에 유용합니다. 예를 들어 컴퓨터의 CPU가 과하게 실행되는 경우를 들 수 있습니다. 메트릭 경고 규칙의 대상은 특정 컴퓨터, 리소스 그룹 또는 구독일 수 있습니다. 예를 들어 컴퓨터들에게 적용되는 단일 규칙을 만들 수 있습니다.

가상 머신에 대한 메트릭 규칙은 다음 데이터를 사용할 수 있습니다.

- 자동으로 수집 되는 Azure 가상 머신의 호스트 메트릭입니다. 
- 게스트 운영 체제에서 Azure Monitor 에이전트에 의해 수집되는 메트릭입니다. 

> [!NOTE]
> VM 인사이트가 현재 공개 미리보기로 제공 되는 Azure Monitor 에이전트를 지원하는 경우 메트릭 경고를 사용할 수 있도록 게스트 운영 체제에서 메트릭으로 성능 데이터를 보냅니다.

### <a name="log-alerts"></a>로그 경고
[로그 경고](../alerts/alerts-metric.md)는 각각 가상 컴퓨터를 모니터링 하는 고유한 시나리오를 지원하는 로그 쿼리 결과의 두 가지 측정값을 수행할 수 있습니다.

- [메트릭 측정](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)는 정의된 임계값을 초과하는 숫자 값이 있는 쿼리 결과의 각 레코드에 대해 별도의 경고를 만듭니다. 메트릭 측정은 Log Analytics 에이전트가 수집하는 Windows 와 Syslog 이벤트와 같은 숫자가 아닌 데이터 또는 여러 컴퓨터에서 성능 추세를 분석 하는데 적합합니다.
- [결과수](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)는 쿼리에서 지정된 수 이상의 레코드를 반환할 때 단일 경고를 만듭니다. 결과 측정 수는 [로그 분석 에이전트](../agents/log-analytics-agent.md) 수집하는 Windows 및 Syslog 이벤트와 같은 숫자가 아닌 데이터 또는 여러 컴퓨터에서 성능 추세를 분석 하는데 적합합니다. 여러 컴퓨터에 동일한 오류 조건이 있는 경우에만 경고 수를 최소화 하거나 경고를 생성 하려는 경우에도 이 전략을 선택할 수 있습니다.

### <a name="target-resource-and-impacted-resource"></a>대상 리소스 및 영향을 받는 리소스

> [!NOTE]
> 현재 공개 미리 보기로 제공되는 리소스 중심 로그 경고 규칙은 가상 머신에 대한 로그 쿼리 경고를 간소화 하고 현재 메트릭 측정 쿼리에서 제공하는 기능을 대체합니다. 컴퓨터를 규칙의 대상으로 사용하여 영향을 받는 리소스로 식별할 수 있습니다. 특정 리소스 그룹 또는 설명의 모든 컴퓨터에 단일 경고 규칙을 적용할 수도 있습니다. 리소스 센터 로그 쿼리 경고가 일반화되면 이 시나리오의 지침이 업데이트됩니다.
> 
Azure Monitor의 각 경고에는 **영향을 받는 리소스** 속성이 있습니다 .이 속성은 규칙의 대상에 의해 정의됩니다. 메트릭 경고 규칙의 경우 영향을 받는 리소스는 컴퓨터이므로 표준 경고 보기에서 쉽게 식별할 수 있습니다. 각 컴퓨터에 대한 경고를 생성 하는 메트릭 측정 경고를 사용하는 경우에도 로그 쿼리 경고는 컴퓨터 대신 작업 영역 리소스와 연결됩니다. 영향을 받은 컴퓨터를 보려면 경고의 세부 정보를 확인해야 합니다.

컴퓨터 이름은 **영향받은 리소스** 속성에 저장 되며, 경고 세부 정보에서 볼 수 있습니다. 또한 경고에서 보낸 전자 메일의 차원으로 표시됩니다.

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="영향을 받는 리소스에 대한 경고를 보여 주는 스크린샷" lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

영향을 받는 컴퓨터와 관련된 경고를 나열하는 보기를 사용할 수 있습니다. [Resource Graph](../../governance/resource-graph/overview.md)를 사용하여 보기를 제공 하는 사용자 지정 통합 문서를 사용할 수 있습니다. 다음 쿼리를 사용하여 경고를 표시하고 통합 문서의 데이터 원본 **Azure Resource Graph** 를 사용합니다.

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>일반적인 경고 규칙
다음 섹션에서는 Azure Monitor의 가상 컴퓨터에 대한 일반적인 경고 규칙을 나열합니다. 메트릭 경고와 로그 메트릭 측정 경고에 대한 자세한 사항이 각각 제공됩니다. 사용할 경고 유형에 대한 지침은 e [경고 유형을 선택하세요 ](#choose-the-alert-type)를 참조하세요.

Azure Monitor에서 경고 규칙을 만드는 프로세스에 익숙하지 않은 경우 지침은 다음 문서를 참조하세요.

- [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../alerts/alerts-metric.md)
- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>컴퓨터를 사용할 수 없음
가장 기본적인 요구 사항은 컴퓨터를 사용할 수 없을 때 경고를 보내는 것입니다. 중지 되었거나, 게스트 운영 체제가 응답하지 않거나, 에이전트가 응답하지 않을 수 있습니다. 이 경고를 구성 하는 다양한 방법이 있지만 가장 일반적인 방법은 로그 분석 에이전트에서 보낸 하트비트를 사용하는 것입니다. 

#### <a name="log-query-alert-rules"></a>로그 쿼리 경고 규칙
로그 쿼리 경고는 [하트비트 테이블](/azure/azure-monitor/reference/tables/heartbeat)을 사용합니다 .이 테이블은 각 컴퓨터에서 1분 마다 하트비트 레코드를 포함해야 합니다. 

**개별 경고**

다음 쿼리를 사용하여 메트릭 측정 규칙을 사용합니다.

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,5m)
```

**단일 경고**

다음 쿼리를 사용하여 다양한 결과 경고를 사용합니다.

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>메트릭 경고 규칙
*하트비트* 라는 메트릭은 각 Log Analytics 작업 영역에 포함됩니다. 해당 작업 영역에 연결된 각 가상 머신은 1분 마다 하트비트 메트릭 값을 보냅니다. 컴퓨터가 메트릭에 있는 차원이기 때문에 모든 컴퓨터에서 하트비트를 보내지 못하면 경고를 발생 시킬 수 있습니다. **집계 형식** 을 **계산** 을 위해 설정하고 **평가 세분성** 과 일치하기 위해 **임계값** 을 설정하세요.

### <a name="cpu-alerts"></a>CPU 경고
#### <a name="metric-alert-rules"></a>메트릭 경고 규칙

| 대상 | 메트릭 |
|:---|:---|
| 호스트 | CPU 사용률 |
| Windows 게스트 | \Processor Information(_Total)\% Processor Time |
| Linux 게스트 | cpu/usage_active |

#### <a name="log-alert-rules"></a>로그 경고 규칙

**CPU 사용률** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**구독의 모든 컴퓨팅 리소스에 대한 CPU 사용률**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**리소스 그룹의 모든 컴퓨팅 리소스에 대한 CPU 사용률** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>메모리 경고

#### <a name="metric-alert-rules"></a>메트릭 경고 규칙

| 대상 | 메트릭 |
|:---|:---|
| Windows 게스트 | \Memory\% Committed Bytes in Use<br>\Memory\Available Bytes |
| Linux 게스트 | 메모리/사용 가능<br>메모리/사용 가능 퍼센티지 |

#### <a name="log-alert-rules"></a>로그 경고 규칙

**사용 가능한 메모리 메가바이트** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**사용 가능한 메모리 퍼센티지(%)** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])<br>\| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>디스크 경고

#### <a name="metric-alert-rules"></a>메트릭 경고 규칙

| 대상 | 메트릭 |
|:---|:---|
| Windows 게스트 | \Logical Disk\(_Total)\% Free Space<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux 게스트 | 디스크/사용 가능 공간<br>디스크/사용 가능 공간 퍼센티지 |

#### <a name="log-query-alert-rules"></a>로그 쿼리 경고 규칙

**사용된 논리 디스크-각 컴퓨터의 모든 디스크** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**논리 디스크 사용-개별 디스크** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

**논리 디스크 IOPS**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**논리 디스크 데이터 속도**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>네트워크 경고

#### <a name="metric-alert-rules"></a>메트릭 경고 규칙

| 대상 | 메트릭 |
|:---|:---|
| Windows 게스트 | \Network Interface\Bytes Sent/sec<br>\Logical Disk\(_Total)\Free Megabytes |
| Linux 게스트 | 디스크/사용 가능 공간<br>디스크/사용 가능 공간 퍼센티지 |

### <a name="log-query-alert-rules"></a>로그 쿼리 경고 규칙

**네트워크 인터페이스 수신된 바이트 -모든 인터페이스**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**네트워크 인터페이스 수신된 바이트-개별 인터페이스**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**네트워크 인터페이스 전송된 바이트-모든 인터페이스**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**네트워크 인터페이스 전송된 바이트-개별 인터페이스**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>로그 쿼리 경고 측정값 비교
두 로그 경고 측정값의 동작을 비교하기 위해 가상 머신의 CPU가 80%를 초과할 때 경고를 만드는 각 측정값의 안내는 다음과 같습니다. 필요한 데이터는 [InsightsMetrics table](/azure/azure-monitor/reference/tables/insightsmetrics)에 있습니다. 다음 쿼리는 경고에 대해 평가해야 하는 레코드를 반환합니다. 각 유형의 경고 규칙은 이 쿼리의 변형을 사용합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>메트릭 측정
**메트릭 측정값** 은 경고 규칙에 정의된 임계값을 초과하는 값을 가진 쿼리의 각 레코드에 대해 별도의 경고를 만듭니다. 이러한 경고 규칙은 각 컴퓨터에 대한 개별 경고를 만들기 때문에 가상 머신 성능 데이터에 적합합니다. 이 측정값에 대한 로그 쿼리는 각 컴퓨터에 대한 값을 반환해야 합니다. 경고 규칙의 임계값은 값이 경고를 발생시켜야 하는지 여부를 결정합니다.

> [!NOTE]
> 현재 공개 미리 보기로 제공되는 리소스 중심 로그 경고 규칙은 가상 머신에 대한 로그 쿼리 경고를 간소화 하고 현재 메트릭 측정 쿼리에서 제공하는 기능을 대체합니다. 컴퓨터를 규칙의 대상으로 사용하여 영향을 받는 리소스로 식별할 수 있습니다. 특정 리소스 그룹 또는 설명의 모든 컴퓨터에 단일 경고 규칙을 적용할 수도 있습니다. 리소스 센터 로그 쿼리 경고가 일반화되면 이 시나리오의 지침이 업데이트됩니다.

#### <a name="query"></a>쿼리
메트릭 측정을 사용하는 규칙에 대한 쿼리에는 **AggregatedValue** 숫자 속성이 있는 각 머신에 대한 레코드가 포함되어야 합니다. 이 값은 경고 규칙의 임계값과 비교됩니다. 임계값이 경고 규칙에 정의되어 있으므로 쿼리에서 이 값을 임계값과 비교할 필요가 없습니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>경고 규칙
로그 분석은 Azure Monitor 메뉴의 **로그** 에서 엽니다. 범위에 대해 올바른 작업 영역이 선택되어 있는지 확인합니다. 그렇지 않은 경우 왼쪽 위에서 **범위 선택을** 클릭하고 올바른 작업 영역을 선택합니다. 원하는 논리가 있는 쿼리에 붙여넣고 **실행** 을 선택하여 올바른 결과가 반환되는지 확인합니다.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="메트릭 측정값 경고 쿼리 결과를 보여 주는 스크린샷." lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

**새로운 경고 규칙** 을 선택하여 현재 쿼리에 새 경고를 만듭니다. 규칙은 **리소스** 에 대한 작업 영역을 사용합니다.

**조건** 을 선택하여 구성을 완료합니다. 쿼리는 이미 각 컴퓨터에 대한 쿼리에서 반환된 값의 그래픽 보기로 채워져 있습니다. **피벗된** 드롭다운 목록에서 컴퓨터를 선택합니다.

**경고 로직** 으로 스크롤을 내리고 **기준** 속성에 대한 **메트릭 측정** 을 선택합니다. 사용률이 80%를 초과할 때 경고하길 원하므로 **집계 값을** **보다 큼** 으로 설정하고 **임계값** 을 **80** 으로 설정합니다.

**경고 로직** 으로 스크롤을 내리고 **기준** 속성에 대한 **메트릭 측정** 을 선택합니다. 쿼리에서 반환된 값과 비교할 **임계값** 을 제공합니다. 이 예제에서는 **80** 이 적용됩니다. **트리거 경고에 기반** 하여 경고를 생성하기 전에 임계값을 초과해야 하는 횟수를 지정합니다. 예를 들어 프로세서가 임계값을 한 번 초과한 다음 정상으로 되돌아가는지는 신경 쓰지 않을 수 있지만, 여러 연속 측정값에 대해 임계값을 계속 초과하는지 주의해야 합니다. 이 예제에서는 **연속 위반** 을 **3** 으로 설정합니다.

**평가 기준** 으로 스크롤을 내립니다. **기간** 은 쿼리에 대한 시간 범위를 지정합니다. **15** 분에 대한 값을 지정한다는 것은 쿼리가 지난 15분 동안 수집된 데이터만 사용하는 것을 뜻합니다. **빈도** 는 쿼리가 실행되는 빈도를 지정합니다. 값이 낮을수록 경고 규칙의 응답성이 높아지지만 비용도 높아집니다. **15** 를 지정해 쿼리를 15분 마다 실행하게 합니다.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="메트릭 측정값 경고 쿼리 규칙을 보여 주는 스크린샷" lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>결과 규칙 수
**결과의 숫자** 규칙은 쿼리가 지정된 수 이상의 레코드를 반환할 때 단일 경고를 만듭니다. 이 유형의 경고 규칙의 로그 쿼리는 일반적으로 경고 조건을 식별하지만 경고 규칙의 임계값은 충분한 수의 레코드가 반환되는지 여부를 결정합니다.

#### <a name="query"></a>쿼리
이 예제에서는 CPU 사용률에 대한 임계값이 쿼리에 포함됩니다. 쿼리에서 반환되는 레코드 수는 해당 임계값을 초과하는 컴퓨터의 수입니다. 경고 규칙의 임계값은 경고를 발생시키기 위해 필요한 최소 머신 수입니다. 단일 컴퓨터가 오류일 때 경고를 원하는 경우 경고 규칙의 임계값은 0입니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>경고 규칙
로그 분석은 Azure Monitor 메뉴의 **로그** 에서 엽니다. 범위에 대해 올바른 작업 영역이 선택되어 있는지 확인합니다. 그렇지 않은 경우 왼쪽 위에서 **범위 선택** 을 클릭하고 올바른 작업 영역을 선택합니다. 원하는 논리가 있는 쿼리에 붙여넣고 **실행** 을 선택하여 올바른 결과가 반환되는지 확인합니다. 현재 임계값을 초과하는 컴퓨터가 없으므로 결과를 확인하기 위해 일시적으로 더 낮은 임계값으로 변경합니다. 그런 다음 경고 규칙을 만들기 전에 적절한 임계값을 설정합니다.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="결과 수 경고 쿼리 결과를 보여 주는 스크린샷." lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

**새로운 경고 규칙** 을 선택하여 현재 쿼리에 새 경고를 만듭니다. 규칙은 **리소스** 에 대한 작업 영역을 사용합니다.

**조건** 단추를 선택하여 구성을 저장합니다. 쿼리는 이미 지난 몇 분 동안 해당 쿼리에서 반환된 레코드 수의 그래픽 보기로 채워져 있습니다. 

**경고 논리** 로 스크롤을 내려 **기준** 속성을 위한 **결과 수** 를 선택합니다. 이 예시에서는 레코드가 하나라도 반환되면 경고를 원합니다. 즉, 하나 이상의 가상 머신에 80% 이상의 프로세서가 있음을 의미합니다. **운영자** 의 경우 **보다 큼** 을 선택하고 **임계값** 에 대해 **0** 을 입력합니다.

**평가 기준** 으로 스크롤을 내립니다. **기간** 은 쿼리에 대한 시간 범위를 지정합니다. **15** 분에 대한 값을 지정한다는 것은 쿼리가 지난 15분 동안 수집된 데이터만 사용하는 것을 뜻합니다. **빈도** 는 쿼리가 실행되는 빈도를 지정합니다. 값이 낮을수록 경고 규칙의 응답성이 높아지지만 비용도 높아집니다. **15** 를 지정해 쿼리를 15분 마다 실행하게 합니다.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="쿼리 규칙 경고 결과 수를 보여 주는 스크린샷." lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>다음 단계

* [가상 머신에서 실행되는 워크로드 모니터링.](monitor-virtual-machine-workloads.md)
* [가상 머신에 관해 수집된 모니터링 데이터 분석.](monitor-virtual-machine-analyze.md)
