---
title: Azure Monitor를 사용하여 Azure 가상 머신 모니터링
description: Azure Monitor를 사용하여 Azure의 가상 머신에서 모니터링 데이터를 수집하고 분석하는 방법을 설명합니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 5a3ed2de24127f09a901fb5bcb06c84124751d74
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530150"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure Monitor를 사용하여 Azure 가상 머신 모니터링
이 문서에서는 Azure Monitor를 사용하여 Azure 가상 머신에서 모니터링 데이터를 수집 및 분석하면서 상태를 유지하는 방법을 설명합니다. 가상 머신은 [다른 Azure 리소스](../essentials/monitor-azure-resource.md)와 같이 Azure Monitor를 사용하여 가용성과 성능을 모니터링할 수 있지만 게스트 운영 체제와 여기에서 실행되는 워크로드를 모니터링해야 하기 때문에 다른 리소스와는 다릅니다. 

> [!NOTE]
> 이 문서에서는 Azure Monitor에서 가상 머신을 모니터링하는 데 필요한 개념과 옵션에 대한 전반적인 개요를 제공합니다. 기본 개념에 집중하지 않고 가상 머신 모니터링을 신속하게 시작하려면 [빠른 시작: Azure Monitor를 사용하여 Azure 가상 머신 모니터링](./monitor-virtual-machine.md)을 참조하세요.


## <a name="differences-from-other-azure-resources"></a>다른 Azure 리소스와의 차이점
[Azure Monitor를 사용하여 Azure 가상 머신 모니터링](../essentials/monitor-azure-resource.md)에서는 Azure 리소스에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터를 분석하고 경고하는 방법을 설명합니다. 다음과 같은 차이점이 있지만 Azure Virtual Machines에서 동일한 모니터링 데이터를 수집하고 관련 작업을 수행할 수 있습니다.

-  [플랫폼 메트릭](../essentials/data-platform-metrics.md)은 가상 머신 뿐만 아니라 [가상 머신 호스트](#monitoring-data)에 대해서도 자동으로 수집됩니다. 게스트 운영 체제에서 성능 데이터를 수집하려면 에이전트가 필요합니다. 
- 가상 머신은 Azure 리소스 내에서 수행된 작업에 대한 인사이트를 제공하는 [리소스 로그](../essentials/platform-logs-overview.md)를 생성하지 않습니다. 에이전트를 사용하여 게스트 운영 체제에서 로그 데이터를 수집합니다.
- 가상 머신에 대한 [진단 설정](../essentials/diagnostic-settings.md)을 만들어 플랫폼 메트릭을 Storage 및 Event Hubs 등의 다른 대상으로 보낼 수 있지만 이러한 진단 설정은 Azure Portal에서 구성할 수 없습니다. 

## <a name="monitoring-data"></a>데이터 모니터링
Azure의 가상 머신은 다음 다이어그램에 표시되는 것처럼 [로그](../logs/data-platform-logs.md) 및 [메트릭](../essentials/data-platform-metrics.md)을 생성합니다.

![개요](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>가상 머신 호스트
Azure의 가상 머신은 [모니터링 데이터](../essentials/monitor-azure-resource.md#monitoring-data)에 설명된 대로 다른 Azure 리소스와 동일하게 가상 머신 호스트에 대해 다음 데이터를 생성합니다.

- [플랫폼 메트릭](../essentials/data-platform-metrics.md) - 정기적으로 자동 수집되며 특정 시간의 리소스 특성을 설명하는 수치 값입니다. 플랫폼 메트릭은 가상 머신 호스트에 대해 수집되지만, 게스트 운영 체제에 대한 메트릭을 수집하려면 진단 확장이 필요합니다.
- [활동 로그](../essentials/platform-logs-overview.md) - 외부에서 구독에 있는 각 Azure 리소스에 대한 작업을 살펴볼 수 있도록 인사이트를 제공합니다. 가상 머신의 경우 머신이 시작될 때와 구성이 변경될 때 이러한 정보를 포함합니다.


### <a name="guest-operating-system"></a>게스트 운영 체제
가상 머신의 게스트 운영 체제에서 데이터를 수집하려면 각 가상 머신에서 로컬로 실행되고 Azure Monitor로 데이터를 전송하는 에이전트가 필요합니다. 서로 다른 데이터를 수집하고 다른 위치에 데이터를 기록하는 여러 에이전트를 Azure Monitor에 사용할 수 있습니다. [Azure Monitor 에이전트 개요](../agents/agents-overview.md)에서 다른 에이전트에 대한 자세한 비교 정보를 확인하세요. 

- [Log Analytics 에이전트](../agents/agents-overview.md#log-analytics-agent) - Azure의 가상 머신, 다른 클라우드 환경 및 온-프레미스에서 사용할 수 있습니다. Azure Monitor 로그에 데이터를 수집합니다. VM 인사이트 및 모니터링 솔루션을 지원합니다. System Center Operations Manager에 사용되는 것과 동일한 에이전트입니다.
- [종속성 에이전트](../agents/agents-overview.md#dependency-agent) - 가상 머신에서 실행되는 프로세스 및 해당 종속성에 대한 데이터를 수집합니다. Log Analytics 에이전트를 사용하여 Azure로 데이터를 전송하고 VM 인사이트, 서비스 맵 및 Wire Data 2.0 솔루션을 지원합니다.
- [Azure 진단 확장](../agents/agents-overview.md#azure-diagnostics-extension) - Azure Monitor 가상 머신에만 사용할 수 있습니다. 여러 위치로 데이터를 수집할 수 있지만 주로 게스트 성능 데이터를 Windows 가상 머신에 대한 Azure Monitor 메트릭으로 수집하는 데 사용됩니다.
- [Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md) - Linux VM에서 Azure Monitor 메트릭으로 성능 데이터를 수집합니다.


## <a name="configuration-requirements"></a>구성 요구 사항
가상 머신을 모니터링하는 Azure Monitor의 모든 기능을 사용하도록 설정하려면 가상 머신 호스트 및 게스트 운영 체제의 모니터링 데이터를 [Azure Monitor 메트릭](../logs/data-platform-logs.md) 및 [Azure Monitor 로그](../logs/data-platform-logs.md) 둘 다로 수집해야 합니다. 다음 표에서는 이 수집을 사용하도록 설정하기 위해 수행해야 하는 구성을 보여 줍니다. 특정 요구 사항에 따라 이러한 단계를 일부만 수행하도록 선택할 수 있습니다.

| 구성 단계 | 완료되는 작업 | 사용되는 기능 |
|:---|:---|:---|
| 구성 없음 | - 메트릭에 호스트 플랫폼 메트릭이 수집됨<br>- 활동 로그가 수집됨 | - 호스트의 메트릭 탐색기<br>- 호스트의 메트릭 경고<br>- 활동 로그 경고 |
| [VM 인사이트 사용](#enable-vm-insights) | - Log Analytics 에이전트가 설치됨<br>- 종속성 에이전트가 설치됨<br>- 게스트 성능 데이터가 로그에 수집됨<br>- 프로세스 및 종속성 세부 정보가 로그에 수집됨 | - 게스트 성능 데이터에 대한 성능 차트 및 통합 문서<br>- 게스트 성능 데이터에 대한 로그 쿼리<br>- 게스트 성능 데이터에 대한 로그 경고<br>- 종속성 맵 |
| [진단 확장 및 telegraf 에이전트 설치](#enable-diagnostics-extension-and-telegraf-agent) | - 게스트 성능 데이터가 메트릭에 수집됨 | - 게스트의 메트릭 탐색기<br>- 게스트에 대한 메트릭 경고  |
| [Log Analytics 작업 영역 구성](#configure-log-analytics-workspace) | - 게스트에서 이벤트가 수집됨 | - 게스트 이벤트에 대한 로그 쿼리<br>- 게스트 이벤트에 대한 로그 경고 |
| [가상 머신에 대한 진단 설정 만들기](#collect-platform-metrics-and-activity-log) | - 로그에 플랫폼 메트릭이 수집됨<br>- 로그에 활동 로그가 수집됨 | - 호스트 메트릭에 대한 로그 쿼리<br>- 호스트 메트릭에 대한 로그 경고<br>- 활동 로그에 대한 로그 쿼리

이러한 각 구성 단계는 다음 섹션에 설명되어 있습니다.

### <a name="enable-vm-insights"></a>VM 인사이트 사용
[VM 인사이트](../vm/vminsights-overview.md)는 Azure Monitor에서 가상 머신을 모니터링하는 기본 도구인 Azure Monitor의 [인사이트](../monitor-reference.md)입니다. 표준 Azure Monitor 기능에 외에 다음과 같은 추가 가치를 제공합니다.

- 가상 머신 게스트 운영 체제 및 워크로드의 모니터링을 사용하도록 설정하는 Log Analytics 에이전트 및 종속성 에이전트를 간편하게 온보딩할 수 있습니다. 
- 가상 머신의 게스트 운영 체제에서 핵심 성능 메트릭을 분석할 수 있는 미리 정의된 추세 성능 차트 및 통합 문서
- 각 가상 머신에서 실행 중인 프로세스와 다른 머신 및 외부 원본과 상호 연결된 구성 요소를 표시하는 종속성 맵

![VM 인사이트 성능 보기](media/monitor-vm-azure/vminsights-01.png)

![VM 인사이트 맵 보기](media/monitor-vm-azure/vminsights-02.png)


Azure Portal의 가상 머신 메뉴에 있는 **인사이트** 옵션에서 VM 인사이트를 사용하도록 설정합니다. 세부 정보 및 기타 구성 방법은 [VM 인사이트 사용 개요](vminsights-enable-overview.md)를 참조하세요.

![VM 인사이트 사용](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Log Analytics 작업 영역 구성
VM 인사이트에서 사용하는 Log Analytics 에이전트는 [Log Analytics 작업 영역](../logs/data-platform-logs.md)으로 데이터를 보냅니다. Log Analytics 작업 영역을 구성하여 에이전트에서 추가 성능 데이터, 이벤트 및 기타 모니터링 데이터의 수집을 사용하도록 설정할 수 있습니다. 작업 영역에 연결하는 에이전트가 자동으로 구성을 다운로드하고 정의된 데이터 수집을 즉시 시작하므로 한 번만 구성하면 됩니다. 

**시작** 에서 **작업 영역 구성** 을 선택하여 VM 인사이트에서 직접 작업 영역에 대한 구성에 액세스할 수 있습니다. 작업 영역 이름을 클릭하여 해당 메뉴를 엽니다.

![작업 영역 구성](media/monitor-vm-azure/workspace-configuration.png)

작업 영역 메뉴에서 **에이전트 구성** 을 선택하여 데이터 원본을 구성합니다. Windows 에이전트의 경우 **Windows 이벤트 로그** 를 선택하고 *시스템* 및 *애플리케이션* 과 같은 일반적인 이벤트 로그를 추가합니다. Linux 에이전트의 경우 **Syslog** 를 선택하고 *커닝* 및 *디먼* 등의 일반적인 기능을 추가 합니다. 사용할 수 있는 데이터 원본의 목록과 구성 세부 사항에 대한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](../agents/agent-data-sources.md)을 참조하세요. 

![이벤트 구성](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 작업 영역 구성에서 수집되는 성능 카운터를 구성할 수 있지만 VM 인사이트에서 수집된 성능 카운터와 중복될 수 있습니다. VM 인사이트는 분당 1회 빈도로 가장 일반적인 카운터 세트를 수집합니다. VM 인사이트에서 아직 수집하지 않은 카운터를 수집하려고 하거나 성능 데이터를 사용하는 기존 쿼리가 있는 경우에만 작업 영역을 통해 성능 카운터를 수집하도록 구성합니다.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>진단 확장 및 Telegraf 에이전트 사용
VM 인사이트는 Log Analytics 작업 영역으로 데이터를 보내는 Log Analytics 에이전트를 기반으로 합니다. 이 기능은 로그 쿼리[로그 경고](../alerts/alerts-log.md), [통합 문서](../visualize/workbooks-overview.md) 등, [Azure Monitor의 여러 기능](../logs/log-query-overview.md)을 지원합니다. [진단 확장](../agents/diagnostics-extension-overview.md)은 Windows 가상 머신의 게스트 운영 체제에서 Azure Storage로 성능 데이터를 수집하고, 필요에 따라 성능 데이터를 [Azure Monitor 메트릭](../essentials/data-platform-metrics.md)으로 보냅니다. Linux 가상 머신의 경우 Azure 메트릭에 데이터를 전송하기 위해 [Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md)가 필요합니다.  이를 통해 [메트릭 탐색기](../essentials/metrics-getting-started.md) 및 [메트릭 경고](../alerts/alerts-metric.md) 등의 다른 Azure Monitor 기능을 사용할 수 있습니다. Azure Event Hubs를 사용하여 Azure Monitor 외부에서 이벤트 및 성능 데이터를 보내도록 진단 확장을 구성할 수도 있습니다.

VM 메뉴의 **진단 설정** 옵션에서 Azure Portal에 단일 Windows 가상 머신에 대한 진단 확장을 설치합니다. **싱크** 탭에서 **Azure Monitor** 를 사용하도록 설정하는 옵션을 선택합니다. 여러 가상 머신에 대한 템플릿 또는 명령줄에서 확장을 사용하도록 설정하려면 [설치 및 구성](../agents/diagnostics-extension-overview.md#installation-and-configuration)을 참조하세요. Log Analytics 에이전트와 달리, 수집할 데이터는 각 가상 머신의 확장 구성에 정의됩니다.

![진단 설정](media/monitor-vm-azure/diagnostic-setting.png)

Linux 가상 머신에서 Telegraf 에이전트를 구성하는 방법에 대한 자세한 내용은 [Telegraf 설치 및 구성](../essentials/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf)를 참조하세요. **진단 설정** 메뉴 옵션은 Linux에서 사용할 수 있지만 Azure Storage로만 데이터를 보낼 수 있도록 합니다.

### <a name="collect-platform-metrics-and-activity-log"></a>플랫폼 메트릭 및 활동 로그 수집
Azure Portal에서 각 가상 머신 호스트에 대해 수집된 플랫폼 메트릭 및 활동 로그를 볼 수 있습니다. 이 데이터를 VM 인사이트와 동일한 Log Analytics 작업 영역으로 수집하여 가상 머신에 대해 수집된 다른 모니터링 데이터와 함께 분석할 수 있습니다. 이 수집은 [진단 설정](../essentials/diagnostic-settings.md)를 사용하여 구성됩니다. [구독에 대한 진단 설정](../essentials/diagnostic-settings.md#create-in-azure-portal)을 사용하여 활동 로그를 수집합니다.

가상 머신에 대한 진단 설정을 사용하여 플랫폼 메트릭을 수집합니다. 다른 Azure 리소스와 달리 Azure Portal에서 가상 머신에 대한 진단 설정을 만들 수 없지만 [다른 방법](../essentials/diagnostic-settings.md#create-using-powershell)을 사용해야 합니다. 다음 예제에서는 PowerShell 및 CLI를 사용하여 가상 머신에 대한 메트릭을 수집하는 방법을 보여 줍니다.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 
가상 머신에 대한 모니터링 데이터 수집을 구성하고 나면 Azure Portal에서 이 구성에 액세스하기 위한 여러 옵션이 제공됩니다.

- **Azure Monitor** 메뉴를 사용하여 모니터링되는 모든 리소스의 데이터에 액세스할 수 있습니다. 
- 대규모로 가상 머신 세트를 모니터링하려면 VM 인사이트를 사용합니다.
- Azure Portal의 메뉴에서 단일 가상 머신에 대한 데이터를 분석합니다. 아래 표에는 가상 머신 메뉴 모니터링을 위한 다양한 옵션이 나와 있습니다.

![Azure Portal에서 모니터링](media/monitor-vm-azure/monitor-menu.png)

| 메뉴 옵션 | Description |
|:---|:---|
| 개요 | 가상 머신 호스트에 대한 [플랫폼 메트릭](../essentials/data-platform-metrics.md)을 표시합니다. [메트릭 탐색기](../essentials/metrics-getting-started.md)에서 이 데이터를 사용하려면 그래프를 클릭합니다. |
| 활동 로그 | 현재 가상 머신에 대해 필터링된 [활동 로그](../essentials/activity-log.md#view-the-activity-log) 항목입니다. |
| 자세한 정보 | 선택한 현재 가상 머신에 대한 맵을 사용하여 [VM 인사이트](../vm/vminsights-overview.md)를 엽니다. |
| 경고 | 현재 가상 머신에 대한 [경고](../alerts/alerts-overview.md)를 봅니다.  |
| 메트릭 | 범위가 현재 가상 머신으로 설정된 [메트릭 탐색기](../essentials/metrics-getting-started.md)를 엽니다. |
| 진단 설정 | 현재 가상 머신에 대한 [진단 확장](../agents/diagnostics-extension-overview.md)를 사용하도록 설정하고 구성합니다. |
| Advisor 권장 사항 | [Azure Advisor](../../advisor/index.yml)에서 제공하는 현재 가상 머신에 대한 권장 사항입니다. |
| 로그 | [범위](../logs/scope.md)가 현재 가상 머신으로 설정된 [Log Analytics](../logs/log-analytics-overview.md)를 엽니다. |
| 연결 모니터 | [Network Watcher 연결 모니터](../../network-watcher/connection-monitor-overview.md)를 열어 현재 가상 머신과 다른 가상 머신 간의 연결을 모니터링합니다. |


## <a name="analyzing-metric-data"></a>메트릭 데이터 분석
가상 머신 메뉴에서 **메트릭** 을 열어 메트릭 탐색기를 통해 가상 머신의 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../essentials/metrics-getting-started.md)을 참조하세요. 

가상 머신에서 메트릭에 사용하는 세 가지 네임스페이스는 다음과 같습니다.

| 네임스페이스 | Description | 요구 사항 |
|:---|:---|:---|
| 가상 머신 호스트 | 모든 Azure Virtual Machines에 대해 자동으로 수집되는 호스트 메트릭입니다. [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)에 제공되는 자세한 메트릭 목록입니다. | 구성 필요 없이 자동으로 수집됩니다. |
| 게스트(클래식) | 제한된 게스트 운영 체제 및 애플리케이션 성능 데이터 세트입니다. 메트릭 탐색기에서 사용할 수 있지만 메트릭 알림과 같은 다른 Azure Monitor 기능에서는 사용할 수 없습니다.  | [진단 확장](../agents/diagnostics-extension-overview.md)이 설치되었습니다. Azure Storage에서 데이터를 읽습니다.  |
| 가상 머신 게스트 | 메트릭을 사용하는 모든 Azure Monitor 기능에 사용할 수 있는 게스트 운영 체제 및 애플리케이션 성능 데이터입니다. | Windows의 경우 Azure Monitor 싱크가 사용하도록 설정된 상태로 [진단 확장이 설치](../agents/diagnostics-extension-overview.md)되어 있습니다. Linux의 경우 [Telegraf 에이전트가 설치](../essentials/collect-custom-metrics-linux-telegraf.md)되어 있습니다. |

![Azure Portal의 메트릭 탐색기](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>로그 데이터 분석
Azure Virtual Machines는 Azure Monitor 로그에 다음 데이터를 수집합니다. 

VM 인사이트는 *InsightsMetrics* 테이블에 기록되는 미리 정의된 성능 카운터 세트를 수집할 수 있습니다. 이는 [컨테이너 인사이트](../containers/container-insights-overview.md)에서 사용하는 것과 동일한 테이블입니다. 

| 데이터 원본 | 요구 사항 | 테이블 |
|:---|:---|:---|
| VM 인사이트 | 각 가상 머신에서 사용하도록 설정합니다. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>자세한 내용은 [VM 인사이트에서 로그를 쿼리하는 방법](../vm/vminsights-log-search.md)을 참조하세요. |
| 활동 로그 | 구독에 대한 진단 설정입니다. | AzureActivity |
| 호스트 메트릭 | 가상 머신에 대한 진단 설정입니다. | AzureMetrics |
| 게스트 운영 체제의 데이터 원본입니다. | Log Analytics 에이전트를 사용하도록 설정하고 데이터 원본을 구성합니다. | 각 데이터 원본에 대한 설명서를 참조하세요. |


> [!NOTE]
> Log Analytics 에이전트에서 수집하는 성능 데이터는 *Perf* 테이블에 기록되고, VM 인사이트는 *InsightsMetrics* 테이블에 수집됩니다. 데이터는 동일하지만 테이블의 구조는 다릅니다. *성능* 을 기준으로 하는 기존 쿼리가 있는 경우 *InsightsMetrics* 를 사용하도록 다시 작성해야 합니다.


## <a name="alerts"></a>경고
Azure Monitor의 [경고](../alerts/alerts-overview.md)는 모니터링 데이터에서 중요한 상태가 확인될 때 사용자에게 미리 알리고, 논리 앱 시작 또는 webhook 호출과 같은 작업을 시작할 수 있습니다. 경고 규칙은 경고를 만들어야 하는 시기를 결정하는 데 사용되는 논리를 정의합니다. Azure Monitor는 경고 규칙에서 사용하는 데이터를 수집하지만 Azure 구독에서 경고 조건을 정의하기 위한 규칙을 만들어야 합니다.

다음 섹션에서는 경고 규칙 유형과 각 규칙 유형을 사용할 때의 권장 사항을 설명합니다. 이 권장 사항은 경고 규칙 유형의 기능 및 비용을 기준으로 합니다. 경고에 대한 자세한 가격 정보는 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.


### <a name="activity-log-alert-rules"></a>활동 로그 경고 규칙
[활동 로그 경고 규칙](../alerts/alerts-activity-log.md)은 활동 로그에서 특정 조건과 일치하는 항목이 생성될 때 발생합니다. 비용이 발생하지 않으므로 필요한 논리가 활동 로그에 있는 경우 우선적으로 선택하는 것이 좋습니다. 

활동 로그 경고의 대상 리소스는 특정 가상 머신, 리소스 그룹의 모든 가상 머신 또는 구독의 모든 가상 머신일 수 있습니다.

예를 들어, 신호 이름으로 *가상 머신 전원 끄기* 를 선택하여 중요한 가상 머신이 중지되는 경우 경고를 만듭니다.

![활동 로그 경고](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>메트릭 경고 규칙
메트릭 값이 임계값을 초과하는 경우 [메트릭 경고 규칙](../alerts/alerts-metric.md)이 발생합니다. 특정 임계값을 정의하거나 Azure Monitor를 사용하여 기록 데이터를 기준으로 임계값을 동적으로 결정할 수 있습니다.  메트릭 경고는 로그 경고 규칙보다 비용은 덜 들고 응답성은 더 높으므로 메트릭 데이터를 사용할 때마다 사용하는 것이 좋습니다. 또한 메트릭이 임계값 아래로 떨어질 때 자체적으로 해결함을 의미하는 상태 저장 방식이기도 합니다.

활동 로그 경고의 대상 리소스는 특정 가상 머신 또는 리소스 그룹의 모든 가상 머신일 수 있습니다.

예를 들어, 가상 머신의 프로세서가 특정 값을 초과하는 경우 경고를 만들려면 신호 유형으로 *백분율 CPU* 를 사용하여 메트릭 경고 규칙을 만듭니다. 특정 임계값을 설정하거나 Azure Monitor에서 동적 임계값을 설정하도록 허용합니다. 

![메트릭 경고](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>로그 경고
예약된 로그 쿼리의 결과가 특정 조건과 일치하는 경우 [로그 경고 규칙](../alerts/alerts-log.md)이 발생합니다. 로그 쿼리 경고는 경고 규칙 중에서 가장 비용이 많이 들고 응답성이 떨어지지만 가장 다양한 데이터에 액세스할 수 있고 다른 경고 규칙에서 수행할 수 없는 복잡한 논리를 수행할 수 있습니다. 

로그 쿼리의 대상 리소스는 Log Analytics 작업 영역입니다. 쿼리에서 특정 머신을 필터링합니다.

예를 들어, 특정 리소스 그룹의 가상 머신이 오프라인 상태인지 확인하는 경고를 만들려면 지난 10분 동안 하트비트가 누락된 각 머신에 대한 레코드를 반환하는 다음 쿼리를 사용합니다. 하나 이상의 머신에서 하트비트가 누락된 경우 발생하는 임계값 1을 사용합니다.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![누락된 하트비트에 대한 로그 경고](media/monitor-vm-azure/log-alert-01.png)

구독의 모든 Windows 가상 머신에서 과도한 횟수의 로그온 실패가 발생할 경우 경고를 만들려면 지난 1시간 동안 실패한 각 로그온 이벤트에 대한 레코드를 반환하는 다음 쿼리를 사용합니다. 허용되는 실패한 로그온 횟수로 설정된 임계값을 사용합니다. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![실패한 로그온에 대한 로그 경고](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager는 가상 머신의 워크로드에 대한 세분화된 모니터링을 제공합니다. 모니터링 플랫폼과 다양한 구현 전략을 비교하려면 [클라우드 모니터링 가이드](/azure/cloud-adoption-framework/manage/monitor/)를 참조하세요.

계속 사용하려는 기존 Operations Manager 환경이 있는 경우 Azure Monitor와 통합하여 추가 기능을 제공할 수 있습니다. Azure Monitor에서 사용하는 Log Analytics 에이전트는 Operations Manager에 사용되는 것과 동일하므로, 모니터링되는 가상 머신이 둘 모두로 데이터를 전송하도록 합니다. VM 인사이트에 에이전트를 추가하고 위에 지정된 대로 추가 데이터를 수집하도록 작업 영역을 구성해야 하지만, 가상 머신은 수정 없이도 Operations Manager 환경에서 기존 관리 팩을 계속 실행할 수 있습니다.

기존 Operations Manager 기능을 보완하는 Azure Monitor 기능에는 다음이 포함됩니다.

- Log Analytics를 사용하여 로그 및 성능 데이터를 대화형으로 분석할 수 있습니다.
- 로그 경고를 사용하여 여러 가상 머신에서 경고 조건을 정의하고 Operations Manager에서 경고를 사용할 수 없게 되는 장기적인 추세를 파악합니다.   

기존 Operations Manager 관리 그룹을 Log Analytics 작업 영역에 연결하는 방법에 대한 자세한 내용은 [Azure Monitor에 Operations Manager 연결](../agents/om-agents.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [로그 쿼리를 사용하여 Azure Monitor 로그의 데이터를 분석하는 방법을 알아봅니다.](../logs/get-started-queries.md)
* [Azure Monitor에서 메트릭과 로그를 사용하여 경고에 대해 알아봅니다.](../alerts/alerts-overview.md)