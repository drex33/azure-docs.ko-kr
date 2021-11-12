---
title: 'Azure Monitor를 사용하여 가상 머신 모니터링: 모니터링 데이터 분석'
description: 가상 머신의 상태와 성능을 분석하는 데 사용할 수 있는 Azure Monitor의 다양한 기능에 대해 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 9eb84e0b685a41cd0ead7aae1feb38c475465f83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325675"
---
# <a name="monitor-virtual-machines-with-azure-monitor-analyze-monitoring-data"></a>Azure Monitor를 사용하여 가상 머신 모니터링: 모니터링 데이터 분석
이 문서는 [Azure Monitor에서 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md) 시나리오의 일부입니다. 구성을 완료한 후 가상 머신에 대한 모니터링 데이터를 분석하는 방법을 설명합니다.

가상 머신에서 VM 인사이트를 사용하도록 설정하면 데이터를 분석에 사용할 수 있습니다. 이 문서에서는 가상 머신의 상태와 성능을 분석하는 데 사용할 수 있는 Azure Monitor의 다양한 기능에 대해 설명합니다. 기능 중 몇 가지는 단일 머신을 분석하는지, 여러 머신을 분석하는지에 따라 다른 환경을 제공합니다. 여기서는 사용 중인 환경에 따라 각 기능의 고유한 동작으로 각 환경을 설명합니다.

> [!NOTE]
> 이 문서에는 Azure Monitor와 VM 인사이트를 통해 수집된 데이터를 분석하는 방법에 대한 지침이 포함되어 있습니다. 가상 머신에서 실행되는 워크로드를 모니터링하기 위해 구성하는 데이터에 대해서는 [워크로드 모니터링](monitor-virtual-machine-workloads.md)을 참조하세요.

## <a name="single-machine-experience"></a>단일 머신 환경
각 Azure 가상 머신과 Azure Arc 사용 서버에 대 한 Azure Portal 메뉴의 **모니터링** 섹션에서 단일 컴퓨터 분석 환경에 액세스 합니다. 이 옵션은 표시되는 데이터를 해당 머신으로 제한하거나 최소한 초기 필터를 설정합니다. 이렇게 하면 특정 머신에 집중하고, 현재 성능과 시간별 추세를 확인하고, 발생했을 수 있는 문제를 식별할 수 있습니다.

:::image type="content" source="media/monitor-virtual-machines/vm-menu.png" alt-text="Azure Portal에서 VM을 분석하는 방법을 보여 주는 스크린샷" lightbox="media/monitor-virtual-machines/vm-menu.png":::

- **개요 페이지**: **모니터링** 탭을 선택하여 가상 머신 호스트의 [플랫폼 메트릭](../essentials/data-platform-metrics.md)을 표시합니다. CPU, 네트워크, 디스크와 같은 중요한 메트릭의 기간별 추세를 빠르게 살펴볼 수 있습니다. 하지만 호스트 메트릭이기 때문에 메모리와 같은 게스트 운영 체제의 카운터는 포함되지 않습니다. 다양한 집계를 수행하고 분석을 위해 카운터를 더 추가할 수 있는 [메트릭 탐색기](../essentials/metrics-getting-started.md)에서 이 데이터를 사용할 그래프를 선택합니다.
- **활동 로그**: 현재 가상 머신에 대해 필터링된 [활동 로그](../essentials/activity-log.md#view-the-activity-log) 항목을 확인합니다. 이 로그를 사용하여 머신의 최근 활동(예: 구성 변경, 머신이 중지 및 시작된 시간)을 볼 수 있습니다. 
- **인사이트**: 현재 선택한 가상 머신의 맵이 있는 [VM 인사이트](../vm/vminsights-overview.md)를 엽니다. 맵은 머신에서 실행 중인 프로세스, 다른 머신에 대한 종속성, 외부 프로세스를 보여 줍니다. 단일 머신의 맵 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트의 맵 기능을 사용하여 애플리케이션 구성 요소 이해](vminsights-maps.md#view-a-map-from-a-vm)를 참조하세요.

    **성능** 탭을 선택하여 다양한 기간별 중요한 성능 카운터 추세를 확인합니다. 가상 머신 메뉴에서 VM 인사이트를 열면 각 디스크의 자세한 메트릭이 포함된 테이블도 있습니다. 단일 머신의 맵 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트를 사용한 차트 성능](vminsights-performance.md#view-performance-directly-from-an-azure-vm)을 참조하세요. 

- **경고**: 현재 가상 머신에 대한 [경고](../alerts/alerts-overview.md)를 확인합니다. 이 경고는 머신만 대상 리소스로 사용하므로 다른 경고가 연결되어 있을 수 있습니다. 모든 리소스에 대한 경고를 보기 위해 Azure Monitor 메뉴의 **경고** 옵션을 사용해야 할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 가상 머신 모니터링 - 경고](monitor-virtual-machine-alerts.md)를 참조하세요.
- **메트릭**: 범위가 머신으로 설정된 메트릭 탐색기를 엽니다. 이 옵션은 메트릭이 아직 추가되지 않았다는 점을 제외하고 **개요** 페이지에서 성능 차트 중 하나를 선택하는 것과 같습니다.
- **진단 설정**: 현재 가상 머신에 대한 [진단 확장](../agents/diagnostics-extension-overview.md)을 사용하도록 설정하고 구성합니다. 이 옵션은 다른 Azure 리소스에 대한 **진단 설정** 옵션과 다릅니다. 데이터를 Azure Event Hubs 또는 Azure Storage로 보내야 하는 경우에만 진단 확장을 사용하도록 설정합니다.
- **Advisor 권장 사항**: [Azure Advisor](../../advisor/index.yml)에서 제공하는 현재 가상 머신에 대한 권장 사항을 확인합니다.
- **로그**: [범위](../logs/scope.md)가 현재 가상 머신으로 설정된 [Log Analytics](../logs/log-analytics-overview.md)를 엽니다. 다양한 기존 쿼리 중에서 선택하여 이 머신에 대한 로그 및 성능 데이터로만 드릴할 수 있습니다. 
- **연결 모니터**: [Network Watcher 연결 모니터](../../network-watcher/connection-monitor-overview.md)를 열어 현재 가상 머신과 다른 가상 머신 간 연결을 모니터링합니다. 
- **통합 문서**: 단일 머신의 VM 인사이트 통합 문서가 포함된 통합 문서 갤러리를 엽니다. 개별 머신용으로 설계된 VM 인사이트 통합 문서 목록은 [VM 인사이트 통합 문서](vminsights-workbooks.md#vm-insights-workbooks)를 참조하세요.

## <a name="multiple-machine-experience"></a>여러 머신 환경
각 Azure 가상 머신과 Azure Arc 사용 서버에 대 한 Azure Portal **모니터** 메뉴에서 여러 컴퓨터 분석 환경에 액세스 합니다. 이 옵션은 비교하려는 가상 머신을 선택할 수 있도록 모든 데이터에 대한 액세스를 제공합니다.

:::image type="content" source="media/monitor-virtual-machines/monitor-menu.png" alt-text="Azure Portal에서 여러 VM을 분석하는 방법을 보여 주는 스크린샷" lightbox="media/monitor-virtual-machines/monitor-menu.png":::

- **활동 로그**: 모든 리소스에 대해 필터링된 [활동 로그](../essentials/activity-log.md#view-the-activity-log) 항목을 확인합니다. 모든 머신에 대한 이벤트를 보려면 가상 머신이나 가상 머신 확장 집합인 **리소스 종류** 에 대한 필터를 만듭니다.
- **경고**: 모든 리소스에 대한 [경고](../alerts/alerts-overview.md)를 확인합니다. 가상 머신과 관련이 있지만 작업 영역과 연결된 경고를 포함합니다. 모든 머신에 대한 경고를 보려면 가상 머신이나 가상 머신 확장 집합인 **리소스 종류** 에 대한 필터를 만듭니다. 
- **메트릭**: 범위가 선택되지 않은 [메트릭 탐색기](../essentials/metrics-getting-started.md)를 엽니다. 이 기능은 여러 머신 간 추세를 비교하려는 경우에 특히 유용합니다. 함께 분석할 머신 그룹을 빠르게 추가하려면 구독이나 리소스 그룹을 선택합니다.
- **로그**: [범위](../logs/scope.md)가 작업 영역으로 설정된 [Log Analytics](../logs/log-analytics-overview.md)를 엽니다. 다양한 기존 쿼리 중에서 선택하여 모든 머신에 대한 로그 및 성능 데이터로 드릴할 수 있습니다. 또는 사용자 지정 쿼리를 만들어 추가 분석을 수행할 수 있습니다.
- **통합 문서**: 여러 머신의 VM 인사이트 통합 문서가 포함된 통합 문서 갤러리를 엽니다. 여러 머신용으로 설계된 VM 인사이트 통합 문서 목록은 [VM 인사이트 통합 문서](vminsights-workbooks.md#vm-insights-workbooks)를 참조하세요. 
- **가상 머신**: **시작** 탭이 열려 있는 [VM 인사이트](../vm/vminsights-overview.md)를 엽니다. 이 작업은 Azure 구독의 모든 머신을 표시하고 모니터링되는 머신을 식별합니다. 이 보기를 사용하여 아직 모니터링되지 않는 개별 머신을 온보딩합니다.

    **성능** 탭을 선택하여 여러 머신의 다양한 기간별 중요한 성능 카운터 추세를 비교합니다. 구독이나 리소스 그룹에서 보기에 포함할 머신을 모두 선택합니다. 단일 머신의 맵 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트를 사용한 차트 성능](vminsights-performance.md#view-performance-directly-from-an-azure-vm)을 참조하세요.

    **맵** 탭을 선택하여 머신에서 실행 중인 프로세스, 머신 간 종속성, 외부 프로세스를 확인합니다. 구독 또는 리소스 그룹의 모든 머신을 선택하거나 단일 머신에 대한 데이터를 검사합니다. 여러 머신의 맵 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트의 맵 기능을 사용하여 애플리케이션 구성 요소 이해](vminsights-maps.md#view-a-map-from-azure-monitor)를 참조하세요. 
 
## <a name="compare-metrics-and-logs"></a>메트릭 및 로그 비교
Azure Monitor의 많은 기능에서 기능이 사용하는 데이터 형식과 기능이 저장되는 위치를 이해할 필요는 없습니다. 예를 들어 성능 보기, 맵 보기, 통합 문서를 채우는 데 사용되는 데이터를 몰라도 VM 인사이트를 사용할 수 있습니다. 분석 중인 논리에 집중하면 됩니다. 심층적 분석에서는 [메트릭](../essentials/data-platform-metrics.md)과 [로그](../logs/data-platform-logs.md) 간 차이점을 이해해야 합니다. Azure Monitor의 각 기능은 서로 다른 종류의 데이터를 사용합니다. 특정 시나리오에 사용하는 경고 유형은 특정 위치에서 해당 데이터를 사용할 수 있도록 허용하는지에 따라 달라집니다.

Azure Monitor를 처음 사용하는 경우 이 수준의 세부 정보가 헷갈릴 수도 있습니다. 다음 정보는 데이터 형식 간 차이점을 이해하는 데 도움이 됩니다.

- 이벤트와 같은 숫자가 아닌 데이터는 로그에 저장됩니다. 메트릭은 정기적으로 샘플링되는 숫자 데이터만 포함할 수 있습니다.
- 숫자 데이터는 메트릭과 로그에 모두 저장될 수 있으므로 다양한 방식으로 분석되고 다양한 유형의 경고를 지원할 수 있습니다.
- VM 인사이트는 Log Analytics 에이전트를 사용하여 게스트 운영 체제의 성능 데이터를 로그에 보냅니다.
- Azure Monitor 에이전트는 게스트 운영 체제의 성능 데이터를 메트릭에 보냅니다.

> [!NOTE]
> Azure Monitor 에이전트는 메트릭과 로그에 모두 데이터를 보냅니다. 이 시나리오에서는 현재 VM 인사이트에 필요한 대로 Log Analytics 에이전트가 데이터를 로그에 보내기 때문에 Azure Monitor 에이전트는 메트릭에만 사용됩니다. VM 인사이트에서 Azure Monitor 에이전트를 사용하는 경우에는 Log Analytics 에이전트를 제거하도록 이 시나리오가 업데이트됩니다.

## <a name="analyze-data-with-vm-insights"></a>VM 인사이트를 사용하여 데이터 분석
VM 인사이트에는 모니터링되는 머신의 작동 상태, 시간별 성능 추세, 머신과 프로세스 간 종속성을 빠르게 파악할 수 있도록 여러 성능 차트가 포함되어 있습니다. VM 인사이트는 Log Analytics 작업 영역에서 수집된 속성과 이벤트 같은, 모니터링되는 머신의 다양한 측면에 대한 통합 보기도 제공합니다.

**시작** 탭은 Azure 구독의 모든 머신을 표시하고 모니터링되는 머신을 식별합니다. 이 보기를 사용하여 모니터링되지 않는 머신을 빠르게 식별하고 아직 모니터링되지 않는 개별 머신을 온보딩할 수 있습니다.

:::image type="content" source="media/monitor-virtual-machines/vminsights-get-started.png" alt-text="VM 인사이트 시작 스크린샷" lightbox="media/monitor-virtual-machines/vminsights-get-started.png":::

**성능** 보기에는 머신의 성능을 확인하는 데 도움이 되는 몇 개의 KPI(핵심 성과 지표)가 있는 여러 차트가 포함되어 있습니다. 차트는 일정 기간의 리소스 사용률을 보여 줍니다. 차트를 사용하여 병목 상태를 식별하거나, 변칙을 확인하거나, 각 머신이 나열된 큐브 뷰로 전환하여 선택한 메트릭 기반의 리소스 사용률을 볼 수 있습니다. 성능 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트를 사용한 차트 성능](vminsights-performance.md)을 참조하세요.

:::image type="content" source="media/monitor-virtual-machines/vminsights-performance.png" alt-text="VM 인사이트 성능 스크린샷" lightbox="media/monitor-virtual-machines/vminsights-performance.png":::

**맵** 보기를 사용하여 머신에서 실행 중인 프로세스, 다른 머신에 대한 종속성, 외부 프로세스를 확인할 수 있습니다. 보기의 기간을 변경하면 다른 기간에서는 종속성이 변경되었는지 확인할 수 있습니다. 맵 보기를 사용하는 방법에 대한 자세한 내용은 [VM 인사이트의 맵 기능을 사용하여 애플리케이션 구성 요소 이해](vminsights-maps.md)를 참조하세요.

:::image type="content" source="media/monitor-virtual-machines/vminsights-map.png" alt-text="VM 인사이트 맵 스크린샷" lightbox="media/monitor-virtual-machines/vminsights-map.png":::

## <a name="analyze-metric-data-with-metrics-explorer"></a>메트릭 탐색기를 사용하여 메트릭 데이터 분석
메트릭 탐색기를 사용하면 차트를 그리고, 추세 간 상관 관계를 시각적으로 표시하고, 메트릭 값의 증가와 감소를 조사할 수 있습니다. 이 도구를 사용하는 방법에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../essentials/metrics-getting-started.md)을 참조하세요. 

가상 머신은 세 개의 네임스페이스를 사용합니다.

| 네임스페이스 | Description | 요구 사항 |
|:---|:---|:---|
| 가상 머신 호스트 | 모든 Azure Virtual Machines에 대해 자동으로 수집되는 호스트 메트릭입니다. [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)에 제공되는 자세한 메트릭 목록입니다. | 구성 필요 없이 자동으로 수집됩니다. |
| 게스트(클래식) | 제한된 게스트 운영 체제 및 애플리케이션 성능 데이터 세트입니다. 메트릭 탐색기에서 사용할 수 있지만 메트릭 경고와 같은 다른 Azure Monitor 기능에서는 사용할 수 없습니다.  | [진단 확장](../agents/diagnostics-extension-overview.md)이 설치되었습니다. Azure Storage에서 데이터를 읽어야 합니다.  |
| 가상 머신 게스트 | 메트릭을 사용하는 모든 Azure Monitor 기능에 사용할 수 있는 게스트 운영 체제 및 애플리케이션 성능 데이터입니다. | [데이터 수집 규칙](../agents/data-collection-rule-overview.md)을 사용하여 [Azure Monitor 에이전트](../agents/azure-monitor-agent-overview.md)가 설치되어 있어야 합니다. |

## <a name="analyze-log-data-with-log-analytics"></a>Log Analytics를 사용하여 로그 데이터 분석
Log Analytics를 사용하면 로그 데이터의 사용자 지정 분석이 가능합니다. VM 인사이트에서 보기를 만드는 데 사용되는 데이터의 심층적 분석을 수행하려는 경우 Log Analytics를 사용합니다. 해당 데이터의 다양한 논리 및 집계를 분석하거나, Microsoft Defender for Cloud 및 Microsoft Sentinel에서 수집한 보안 데이터를 상태 및 가용성 데이터와 상호 연결하거나, [워크로드에](monitor-virtual-machine-workloads.md)대해 수집된 데이터로 작업할 수 있습니다.

Log Analytics를 사용하기 위해 로그 쿼리 작성 방법을 반드시 이해할 필요는 없습니다. 선택하여 수정 없이 실행하거나 사용자 지정 쿼리의 시작점으로 사용할 수 있는 여러 쿼리가 미리 빌드되어 있습니다. Log Analytics 화면의 맨 위에서 **쿼리** 를 선택하고 **리소스 종류** 가 **가상 머신** 이나 **가상 머신 확장 집합** 인 쿼리를 확인합니다. 해당 쿼리를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor Log Analytics에서 쿼리 사용](../logs/queries.md)을 참조하세요. Log Analytics를 사용하여 쿼리를 실행하고 결과를 사용하는 방법에 대한 자습서는 [Log Analytics 자습서](../logs/log-analytics-tutorial.md)를 참조하세요.

:::image type="content" source="media/monitor-virtual-machines/vm-queries.png" alt-text="가상 머신 쿼리 스크린샷" lightbox="media/monitor-virtual-machines/vm-queries.png":::

**성능** 또는 **맵** 보기에서 속성 창을 사용하여 VM 인사이트에서 Log Analytics를 시작하면 선택한 컴퓨터에 대한 데이터가 있는 테이블이 나열됩니다. 테이블을 선택하여 선택한 컴퓨터에 대한 해당 테이블의 모든 레코드를 반환하는 간단한 쿼리로 Log Analytics를 엽니다. 이 결과를 사용하거나 더 복잡한 분석을 위해 쿼리를 수정합니다. 작업 영역으로 설정된 [범위](../log/../logs/scope.md)는 해당 작업 영역을 사용하는 모든 컴퓨터에 대한 데이터에 액세스할 수 있음을 의미합니다. 

:::image type="content" source="media/monitor-virtual-machines/table-query.png" alt-text="테이블 쿼리 스크린샷" lightbox="media/monitor-virtual-machines/table-query.png":::

## <a name="visualize-data-with-workbooks"></a>통합 문서를 사용하여 데이터 시각화
[통합 문서](../visualize/workbooks-overview.MD)는 Azure Portal에서 대화형 보고서를 제공하고 다양한 종류의 데이터를 단일 보기로 결합합니다. 통합 문서는 텍스트,  [로그 쿼리](/azure/data-explorer/kusto/query/), 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다. 통합 문서는 동일한 Azure 리소스에 대한 액세스 권한이 있는 다른 팀 멤버에 의해 편집될 수 있습니다.

통합 문서는 다음과 같은 시나리오에 유용합니다.

* CPU 사용률, 디스크 공간, 메모리, 네트워크 종속성 같은 관심 있는 메트릭을 사전에 알 수 없는 경우 가상 머신의 사용량을 살펴봅니다. 다른 사용량 현황 분석 도구와 달리 통합 문서를 사용하면 여러 종류의 시각화와 분석을 결합할 수 있으므로 통합 문서는 이 종류의 자유 형식 검색에 유용합니다.
* 주요 카운터 및 기타 로그 이벤트에 대한 메트릭을 표시하여 최근에 프로비전된 VM이 수행되는 방식을 팀에 설명합니다.
* VM의 크기 조정 실험 결과를 다른 멤버와 공유합니다. 실험의 목표를 텍스트로 설명할 수 있습니다. 그런 다음, 각 메트릭이 목표 위인지 또는 아래인지를 나타내는 명확한 설명선과 함께 실험을 평가하는 데 사용된 각 사용량 메트릭과 분석 쿼리를 보여 줄 수 있습니다.
* 데이터, 텍스트 설명 및 차후의 중단을 방지하기 위한 다음 단계의 논의를 결합하여 VM의 사용 현황에 대한 중단의 영향을 보고합니다.

VM 인사이트에는 다음 통합 문서가 포함되어 있습니다. 이 통합 문서를 사용하거나, 특정 요구 사항을 처리하기 위해 사용자 지정 통합 문서를 만드는 시작점으로 사용할 수 있습니다.

### <a name="single-virtual-machine"></a>단일 가상 머신

| 통합 문서 | Description |
|----------|-------------|
| 성능 | 사용하도록 설정된 Log Analytics 성능 카운터를 모두 사용하는 사용자 지정 가능한 버전의 성능 보기를 제공합니다. | 
| Connections | VM의 인바운드 및 아웃바운드 연결에 대한 심층적 보기를 제공합니다. | 

### <a name="multiple-virtual-machines"></a>여러 대의 가상 컴퓨터

| 통합 문서 | Description |
|----------|-------------|
| 성능 | 사용하도록 설정된 Log Analytics 성능 카운터를 모두 사용하는 사용자 지정 가능한 버전의 상위 N개 목록 및 차트 보기를 단일 통합 문서로 제공합니다.|
| 성능 카운터 | 다양한 성능 카운터 세트의 상위 N개 차트 보기를 제공합니다. |
| Connections | 모니터링되는 머신의 인바운드 및 아웃바운드 연결에 대한 심층적 보기를 제공합니다. |
| 활성 포트 | 모니터링되는 머신의 포트에 바인딩된 프로세스와 선택한 기간의 프로세스 작업 목록을 제공합니다. |
| 포트 열기 | 모니터링되는 머신에서 열린 포트 수와 열린 포트에 대한 세부 정보를 제공합니다. |
| 실패한 연결 | 모니터링되는 머신에서 실패한 연결 수, 실패 추세, 시간 경과에 따른 실패 비율 증가 여부를 표시합니다. |
| 보안 및 감사 | 전체 연결, 악성 연결, IP 엔드포인트가 전역적으로 상주하는 위치를 보고하는 TCP/IP 트래픽 분석을 제공합니다. 모든 기능을 사용하려면 보안 검색을 사용하도록 설정해야 합니다. |
| TCP 트래픽 | 모니터링되는 머신과 해당 머신의 전송, 수신, 전체 네트워크 트래픽이 그리드에 포함되고 추세선으로 표시되는 순위 지정 보고서를 제공합니다. |
| 트래픽 비교 | 단일 머신이나 머신 그룹의 네트워크 트래픽 추세를 비교합니다. |
| Log Analytics 에이전트 | 비정상 작업 영역에 연결하는 에이전트 수와 에이전트가 머신 성능에 미치는 영향을 포함하여 에이전트 상태를 분석합니다. 이 통합 문서는 다른 통합 문서와 같이 VM 인사이트에서 사용할 수 없습니다. Azure Monitor 메뉴에서 **통합 문서** 로 이동한 다음, **퍼블릭 템플릿** 을 선택합니다. |

사용자 고유의 사용자 지정 통합 문서를 만드는 방법에 대한 지침은 [통합 문서를 사용하여 대화형 보고서 VM 인사이트 만들기](vminsights-workbooks.md)를 참조하세요.

:::image type="content" source="media/monitor-virtual-machines/workbook-example.png" alt-text="가상 머신 통합 문서 스크린샷" lightbox="media/monitor-virtual-machines/workbook-example.png":::

## <a name="next-steps"></a>다음 단계

* [수집된 데이터에서 경고 만들기](monitor-virtual-machine-alerts.md)
* [가상 머신에서 실행되는 워크로드 모니터링](monitor-virtual-machine-workloads.md)
