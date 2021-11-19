---
title: Azure Virtual Machines 모니터링
description: 여기에서 시작하여 Azure Virtual Machines 모니터링하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/17/2021
ms.openlocfilehash: b92417fc19942504fa1f9060273586dd20ad846a
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817894"
---
# <a name="monitoring-azure-virtual-machines"></a>Azure Virtual Machines 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Virtual Machines 생성된 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터를 분석하고 [경고하는](/azure/azure-monitor/overview) 방법을 설명합니다.

> [!NOTE]
> 이 문서에서는 Azure Virtual Machine 모니터링을 시작하기 위한 기본 정보를 제공합니다. Azure 및 하이브리드 가상 머신의 전체 환경을 모니터링하는 전체 가이드는 [Azure Monitor 사용하여 가상 머신 모니터링을](../azure-monitor/vm/monitor-virtual-machine.md)참조하세요.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
[Azure Monitor](/azure/azure-monitor/overview) Azure 리소스를 모니터링하는 전체 기능 집합을 제공하는 전체 스택 모니터링 서비스입니다. 하지만 다양한 모니터링 작업을 수행하기 위해 Azure Monitor 직접 상호 작용할 필요는 없습니다. 해당 기능은 모니터링하는 Azure 서비스의 Azure Portal 통합되기 때문에 수행합니다. Azure Monitor Azure 리소스에서 작동하는 방식에 대한 개요가 있는 자습서에 관심이 있는 경우 Azure Monitor 을 통해 [Azure 리소스 모니터링을](../azure-monitor/essentials/monitor-azure-resource.md)참조하세요.

## <a name="monitoring-data"></a>데이터 모니터링

Azure Virtual Machines Azure 리소스에서 데이터 모니터링에 설명된 다른 Azure 리소스와 동일한 종류의 [모니터링 데이터를 수집합니다.](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) Azure Virtual Machines 만든 메트릭 및 로그에 대한 자세한 내용은 Azure Virtual Machines [데이터 참조 모니터링을](monitor-vm-reference.md) 참조하세요.

## <a name="overview-page"></a>개요 페이지
가상 머신의 **개요** 페이지에서 **모니터링** 탭으로 시작합니다. 여기에는 평균 CPU 및 네트워크 사용률과 같은 주요 상태 메트릭을 표시하는 차트가 포함됩니다. 기간을 클릭하여 차트의 시간 범위를 변경하거나 차트를 클릭하여 메트릭 탐색기를 열어 자세히 드릴다운하거나 경고 규칙을 만듭니다. 

:::image type="content" source="media/monitor-vm/overview.png" lightbox="media/monitor-vm/overview.png" alt-text="Azure Virtual Machine 개요 페이지":::

## <a name="activity-log"></a>활동 로그
[활동 로그는](../azure-monitor/essentials/activity-log.md) 구성 변경 내용 및 중지 및 시작 시기를 포함하여 가상 머신의 최근 활동을 보여 줍니다. Azure Portal 활동 로그를 보거나 진단 설정을 만들어 시간에 따라 이벤트를 보거나 수집된 다른 데이터로 분석할 수 있는 [Log Analytics 작업 영역으로 보냅니다.](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

:::image type="content" source="media/monitor-vm/activity-log.png" lightbox="media/monitor-vm/activity-log.png" alt-text="활동 로그":::

## <a name="vm-insights"></a>VM 인사이트
Azure의 일부 서비스에는 미리 빌드된 통합 문서 및 해당 특정 서비스에 대한 기타 특수 기능을 포함하는 Azure Monitor 사용자 지정된 모니터링 환경이 있습니다. 이러한 환경을 *인사이트라고 합니다.* 

VM 인사이트는 단일 인터페이스에서 전체 Azure 및 하이브리드 가상 머신 세트를 함께 모니터링하도록 설계되었습니다. Azure 환경에서 여러 가상 머신 모니터링을 시작하는 경우 다음 기능에 대해 VM 인사이트를 사용하도록 설정하는 것이 좋습니다.

- 가상 머신 게스트 운영 체제 및 워크로드의 모니터링을 사용하도록 설정하는 Log Analytics 에이전트 및 종속성 에이전트를 간편하게 온보딩할 수 있습니다.
- 가상 머신의 게스트 운영 체제에서 핵심 성능 메트릭을 분석할 수 있는 미리 정의된 추세 성능 차트 및 통합 문서
- 각 가상 머신에서 실행 중인 프로세스와 다른 머신 및 외부 원본과 상호 연결된 구성 요소를 표시하는 종속성 맵

:::image type="content" source="media/monitor-vm/vminsights-01.png" lightbox="media/monitor-vm/vminsights-01.png" alt-text="VM 인사이트 성능 보기":::

:::image type="content" source="media/monitor-vm/vminsights-02.png" lightbox="media/monitor-vm/vminsights-02.png" alt-text="VM 인사이트 맵 보기":::

VM 인사이트를 구성하고 [가상 머신에 대한](../azure-monitor/vm/vminsights-enable-portal.md) 모니터링을 사용하도록 설정하는 빠른 단계는 Azure Portal 단일 가상 머신 또는 가상 머신 확장 집합에 Azure Monitor 사용을 참조하세요. 가상 머신을 온보딩하기 위한 인사이트 및 다양한 방법을 사용하도록 설정하는 방법에 대한 일반적인 내용은 [VM 인사이트](../azure-monitor/vm/vminsights-enable-overview.md) 사용 개요를 참조하세요.

## <a name="collect-guest-metrics-and-logs"></a>게스트 메트릭 및 로그 수집
Azure Monitor 머신을 만들 때 가상 머신 호스트에 대한 메트릭 데이터 수집을 자동으로 시작합니다. 하지만 가상 머신의 게스트 운영 체제에서 메트릭을 수집하려면 에이전트를 설치해야 합니다. [VM 인사이트를](#vm-insights)사용하도록 설정하면 Log Analytics 에이전트가 설치되고 성능 데이터를 Azure Monitor 로그로 보내기 시작합니다. 그러면 **성능** 및 **맵** 보기가 가능합니다. 가상 머신에 대해 VM 인사이트를 사용하도록 설정하는 자습서는 [자습서: Azure Virtual Machine에](../azure-monitor/vm/tutorial-monitor-vm-enable.md) 대한 모니터링 사용을 참조하세요.

VM 인사이트를 사용하도록 설정한 후 [Azure Monitor 에이전트를](../azure-monitor/agents/azure-monitor-agent-overview.md) 설치하여 가상 머신에서 게스트 로그를 수집하고 게스트 메트릭을 Azure Monitor 메트릭으로 보냅니다. 이렇게 하면 [메트릭 탐색기 를 사용하여 메트릭을 분석할 수 있습니다.](#analyze-metrics)  [자습서: Azure Virtual Machine에서 게스트 로그 및 메트릭 수집을](../azure-monitor/vm/tutorial-monitor-vm-guest.md) 참조하여 Azure Monitor 에이전트를 설치하고 수집할 데이터를 정의하는 데이터 수집 규칙을 만드는 방법을 알아봅니다.

## <a name="analyze-metrics"></a>메트릭 분석
메트릭은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 가상 머신 호스트에 대한 플랫폼 메트릭은 자동으로 수집되지만 게스트 메트릭을 수집하려면 [Azure Monitor 에이전트를 설치해야](#collect-guest-metrics-and-logs) 합니다. 

**개요** 페이지에는 가장 일반적인 호스트 메트릭이 포함되어 있으며 메트릭 탐색기를 사용하여 다른 메트릭에 액세스할 수 있습니다.  이 도구를 사용하면 메트릭 값에서 차트를 만들고 추세를 시각적으로 상호 연관시킬 수 있습니다. 메트릭 경고 규칙을 만들거나 차트를 Azure 대시보드에 고정할 수도 있습니다. 이 도구 사용에 [대한 자습서는 Azure 리소스에 대한 메트릭 분석을](/azure-monitor/essentials/tutorial-metrics) 참조하세요.

:::image type="content" source="media/monitor-vm/metrics-explorer.png" lightbox="media/monitor-vm/metrics-explorer.png" alt-text="메트릭 탐색기":::

사용 가능한 메트릭 목록은 [Azure Virtual Machines 데이터 모니터링 참조를 참조하세요.](monitor-vm-reference.md#metrics) 

## <a name="analyze-logs"></a>로그 분석
Azure Monitor 로그의 데이터는 각각 고유한 속성 집합이 있는 테이블로 구분되는 Log Analytics 작업 영역에 저장됩니다. 

VM 인사이트는 수집한 데이터를 로그에 저장하지만 데이터를 대화형으로 분석할 수 있는 성능 및 맵 보기를 제공합니다. 추가로 드릴다운하거나 사용자 지정 분석을 수행하려는 경우 이 데이터로 직접 작업할 수 있습니다. 이 데이터에 대한 세부 정보 및 샘플 [쿼리는 VM 인사이트에서 로그를](../azure-monitor/vm/vminsights-log-search.md) 쿼리하는 방법을 참조하세요.

가상 머신에서 수집하는 다른 로그 데이터를 분석하려면 [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)에서 [로그 쿼리를](../azure-monitor/logs/get-started-queries.md) 사용합니다. 가상 머신에 대한 몇 가지 [기본 제공 쿼리를](../azure-monitor/logs/queries.md) 사용할 수 있거나 직접 만들 수 있습니다. 이러한 쿼리의 결과를 대화형으로 사용하거나 통합 문서에 포함시켜 다른 사용자가 사용할 수 있도록 하거나 결과에 따라 경고를 생성할 수 있습니다.

:::image type="content" source="media/monitor-vm/log-analytics-query.png" lightbox="media/monitor-vm/log-analytics-query.png" alt-text="Log Analytics 쿼리":::

## <a name="alerts"></a>경고
Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](/azure/azure-monitor/platform/alerts-metric-overview), [로그](/azure/azure-monitor/platform/alerts-unified-log) 및 [활동 로그](/azure/azure-monitor/platform/activity-log-alerts)에서 경고를 설정할 수 있습니다. 

Azure Virtual Machines 대한 경고를 설명하는 여러 리소스를 사용할 수 있습니다.

- 가상 머신이 응답하지 않는 경우 로그 쿼리 경고 만들기에 대한 자습서는 [Azure Virtual Machine을 사용할 수 없는](../azure-monitor/vm/tutorial-monitor-vm-alert.md) 경우 경고 만들기를 참조하세요.
- [Azure Monitor 있는 가상 머신 모니터링: 가상 머신에](../azure-monitor/vm/monitor-virtual-machine-alerts.md) 대한 일반적인 경고 규칙에 대한 경고를 참조하세요. 
- Azure Monitor 있는 가상 머신 모니터링: 경고에 활용할 수 있는 가상 머신에서 실행되는 워크로드에서 수집할 수 있는 데이터에 대한 [워크로드를](../azure-monitor/vm/monitor-virtual-machine-workloads.md) 참조하세요.
- 일반적인 로그 경고 규칙은 *가상 머신의* 리소스 종류 및 경고 **유형에** 대한 Log **Analytics의 쿼리를 참조하세요.**  [로그 쿼리 경고 규칙 만들기에 대한](../azure-monitor/alerts/tutorial-log-alert.md) 자습서는 Azure 리소스에 대한 로그 쿼리 경고 만들기를 참조하세요.


## <a name="next-steps"></a>다음 단계

- Azure Virtual Machines 만든 로그 및 메트릭에 대한 참조는 Azure [Virtual Machine 모니터링 데이터 참조를](monitor-vm-reference.md) 참조하세요.
