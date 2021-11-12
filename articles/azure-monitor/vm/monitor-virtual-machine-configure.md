---
title: 'Azure Monitor를 사용하여 가상 머신 모니터링: 모니터링 구성'
description: Azure Monitor에서 모니터링하도록 가상 머신을 구성하는 방법을 알아봅니다. Azure Monitor 시나리오를 사용하여 가상 머신 및 해당 워크로드를 모니터링합니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 6eb624fd132823afe25a91414c2a316f9dc7bab3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310341"
---
# <a name="monitor-virtual-machines-with-azure-monitor-configure-monitoring"></a>Azure Monitor를 사용하여 가상 머신 모니터링: 모니터링 구성
이 문서는 [Azure Monitor에서 가상 머신 및 해당 워크로드 모니터링](monitor-virtual-machine.md) 시나리오의 일부입니다. Azure Monitor에서 Azure 및 하이브리드 가상 머신의 모니터링을 구성하는 방법을 설명합니다.

이 문서에서는 가상 머신 호스트와 해당 게스트 운영 체제를 모니터링하는 가장 일반적인 Azure Monitor 기능에 대해 설명합니다. 특정 환경 및 비즈니스 요구 사항에 따라 이 구성에서 사용하도록 설정된 모든 기능을 구현하지 않을 수 있습니다. 각 섹션에서는 해당 구성에서 사용하도록 설정되는 기능과 이로 인해 잠재적으로 추가 비용이 발생하는지 여부에 대해 설명합니다. 이 정보는 구성의 각 단계를 수행할지 여부를 평가하는 데 도움이 됩니다. 자세한 가격 책정 정보는 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

이 구성에서 사용하도록 설정된 각 기능에 대한 일반적인 설명은 [시나리오 개요](monitor-virtual-machine.md)에서 제공됩니다. 이 문서에는 요구 사항을 평가하는 데 도움이 되도록 각 기능에 대한 자세한 설명을 제공하는 콘텐츠에 대한 링크도 포함되어 있습니다.

> [!NOTE]
> 구성에서 사용하도록 설정된 기능은 가상 머신에서 실행되는 워크로드의 모니터링을 지원합니다. 그러나 특정 워크로드에 따라 일반적으로 추가 구성이 필요합니다. 이 추가 구성에 대한 자세한 내용은 [워크로드 모니터링](monitor-virtual-machine-workloads.md)을 참조하세요.

## <a name="configuration-overview"></a>구성 개요
다음 표에는 이 구성에 대해 수행해야 하는 단계가 나와 있습니다. 각 단계는 해당 구성 단계에 대한 자세한 설명이 있는 섹션으로 연결됩니다.

| 단계 | 설명 |
|:---|:---|
| [구성 없음](#no-configuration) | Azure 가상 머신 호스트에 대한 활동 로그 및 플랫폼 메트릭이 구성 없이 자동으로 수집됩니다. |
| [Log Analytics 작업 영역 만들기 및 준비](#create-and-prepare-a-log-analytics-workspace) | Log Analytics 작업 영역을 만들고 VM 인사이트를 사용하도록 구성합니다. 특정 요구 사항에 따라 여러 작업 영역을 구성할 수 있습니다. |
| [Log Analytics 작업 영역에 활동 로그 보내기](#send-an-activity-log-to-a-log-analytics-workspace) | 활동 로그를 작업 영역에 보내 다른 로그 데이터와 함께 분석합니다. |
| [하이브리드 컴퓨터 준비](#prepare-hybrid-machines) | 하이브리드 컴퓨터에는 Azure 가상 머신처럼 관리할 수 있도록 Azure Arc에서 사용하도록 설정된 서버 에이전트가 설치되어 있거나 해당 에이전트가 수동으로 설치되어 있어야 합니다. |
| [컴퓨터에서 VM 인사이트 사용](#enable-vm-insights-on-machines) | 필요한 에이전트를 배포하고 게스트 운영 체제에서 데이터 수집을 시작하는 컴퓨터를 VM 인사이트에 온보딩합니다. |
| [메트릭에 게스트 성능 데이터 보내기](#send-guest-performance-data-to-metrics) |Azure Monitor 에이전트를 설치하여 성능 데이터를 Azure Monitor 메트릭에 보냅니다. |

## <a name="no-configuration"></a>구성 없음
Azure Monitor는 구성 없이 Azure 가상 머신에 대한 기본 수준의 모니터링을 무료로 제공합니다. Azure 가상 머신에 대한 플랫폼 메트릭에는 CPU, 네트워크 및 디스크 사용률과 같은 중요한 메트릭이 포함됩니다. 이러한 메트릭은 Azure Portal에서 컴퓨터에 대한 [개요 페이지](monitor-virtual-machine-analyze.md#single-machine-experience)에 표시됩니다. 활동 로그도 자동으로 수집되며, 구성 변경 및 중지/시작 시간과 같은 컴퓨터의 최근 활동이 포함됩니다.

## <a name="create-and-prepare-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기 및 준비
VM 인사이트를 지원하고 Log Analytics 에이전트에서 원격 분석을 수집하려면 하나 이상의 Log Analytics 작업 영역이 필요합니다. 작업 영역에 대한 비용은 없지만, 데이터를 수집할 때 수집 및 보존 비용이 발생합니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../logs/manage-cost-storage.md)를 참조하세요.

많은 환경에서 단일 작업 영역을 모니터링하는 모든 가상 머신 및 기타 Azure 리소스에 사용합니다. 많은 고객이 보안 데이터에서 가용성 및 성능 원격 분석을 분리 하도록 선택 하는 경우 [에도 Microsoft Defender에서 클라우드 및 Microsoft 센티널에](monitor-virtual-machine-security.md)사용 하는 작업 영역을 공유할 수도 있습니다. Azure Monitor를 시작하는 경우 단일 작업 영역에서 시작하여 요구 사항이 진화함에 따라 더 많은 작업 영역을 만드는 것이 좋습니다.

작업 영역 구성을 설계할 때 고려해야 하는 논리에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../logs/design-logs-deployment.md)을 참조하세요.

### <a name="multihoming-agents"></a>에이전트 멀티호밍
멀티호밍은 여러 작업 영역에 연결하는 가상 머신을 나타냅니다. 일반적으로 Azure Monitor 전용 에이전트를 멀티호밍할 이유가 거의 없습니다. 에이전트에서 데이터를 여러 작업 영역에 보내도록 하면 중복 데이터가 각 작업 영역에 만들어져 전체 비용이 증가할 가능성이 높습니다. [작업 영역 간 쿼리](../logs/cross-workspace-query.md) 및 [통합 문서](../visualizations/../visualize/workbooks-overview.md)를 사용하여 여러 작업 영역의 데이터를 결합할 수 있습니다.

그러나 멀티 호 밍을 고려해 야 할 수 있는 한 가지 이유는 클라우드 용 Microsoft Defender를 사용 하는 환경 또는 Azure Monitor 별도의 작업 영역에 저장 된 Microsoft 센티널이 있는 경우입니다. 각 서비스에서 모니터링하는 컴퓨터는 데이터를 각 작업 영역에 보내야 합니다. Windows 에이전트는 최대 4개의 작업 영역에 보낼 수 있으므로 이 시나리오를 지원합니다. Linux 에이전트는 현재 단일 작업 영역에만 보낼 수 있습니다. Azure Monitor 및 클라우드 용 Microsoft Defender 또는 Microsoft 센티널에서 일반적인 Linux 컴퓨터 집합을 모니터링 하려는 경우 서비스는 동일한 작업 영역을 공유 해야 합니다.

에이전트를 멀티호밍하는 다른 이유는 [하이브리드 모니터링 모델](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring)을 사용하는 경우입니다. 이 모델에서는 Azure Monitor 및 Operations Manager를 함께 사용하여 동일한 컴퓨터를 모니터링합니다. Log Analytics 에이전트와 Operations Manager용 Microsoft 관리 에이전트는 동일한 에이전트입니다. 경우에 따라 다른 이름으로 참조됩니다.

### <a name="workspace-permissions"></a>작업 영역 권한
작업 영역의 액세스 모드는 다양한 데이터 세트에 액세스할 수 있는 사용자를 정의합니다. 액세스 모드를 정의하고 권한을 구성하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그 데이터 및 작업 영역에 대한 액세스 관리](../logs/manage-access.md)를 참조하세요. Azure Monitor를 지금 시작하는 경우 나중에 작업 영역을 만들고 권한을 구성할 때 기본값을 적용하는 것이 좋습니다.

### <a name="prepare-the-workspace-for-vm-insights"></a>VM 인사이트를 위한 작업 영역 준비
가상 머신에 대한 모니터링을 사용하도록 설정하기 전에 VM 인사이트를 사용하도록 각 작업 영역을 준비합니다. 이 단계에서는 Log Analytics 에이전트에서 데이터 수집을 지원하는 필수 솔루션을 설치합니다. 이 구성은 각 작업 영역에 대해 한 번만 완료합니다. 다른 방법 외에도 Azure Portal을 사용하여 이렇게 구성하는 방법에 대한 자세한 내용은 [VM 인사이트 사용 개요](vminsights-enable-overview.md)를 참조하세요.

## <a name="send-an-activity-log-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 활동 로그 보내기
Azure Portal에서 각 가상 머신 호스트에 대해 수집된 플랫폼 메트릭 및 활동 로그를 볼 수 있습니다. 이 데이터를 VM 인사이트와 동일한 Log Analytics 작업 영역에 보내 가상 머신에 대해 수집된 다른 모니터링 데이터와 함께 분석합니다. Azure 구독의 모든 리소스에 대한 단일 활동 로그가 있으므로 다른 Azure 리소스에 대한 모니터링을 구성할 때 이미 이 작업을 수행했을 수 있습니다.

활동 로그 데이터를 수집하거나 보존하는 데 드는 비용은 없습니다. 활동 로그를 Log Analytics 작업 영역에 보내는 진단 설정을 만드는 방법에 대한 자세한 내용은 [진단 설정 만들기](../essentials/diagnostic-settings.md)를 참조하세요.

### <a name="network-requirements"></a>네트워크 요구 사항
Linux 및 Windows용 Log Analytics 에이전트는 443 TCP 포트를 통해 Azure Monitor 서비스로의 아웃바운드 통신을 수행합니다. 종속성 에이전트는 Log Analytics 에이전트를 모든 통신에 사용하므로 다른 포트가 필요하지 않습니다. 방화벽 및 프록시를 구성하는 방법에 대한 자세한 내용은 [네트워크 요구 사항](../agents/log-analytics-agent.md#network-requirements)을 참조하세요.

:::image type="content" source="media/monitor-virtual-machines/network-diagram.png" alt-text="네트워크를 보여 주는 다이어그램" lightbox="media/monitor-virtual-machines/network-diagram.png":::

### <a name="gateway"></a>게이트웨이
Log Analytics 게이트웨이를 사용하면 단일 게이트웨이를 통해 온-프레미스 컴퓨터에서 통신 채널로 전달할 수 있습니다. 그러나 Log Analytics 게이트웨이에서는 Azure Arc 사용 서버 에이전트를 사용할 수 없습니다. 보안 정책에 게이트웨이가 필요한 경우 온-프레미스 컴퓨터용 에이전트를 수동으로 설치해야 합니다. Log Analytics 게이트웨이를 구성하고 사용하는 방법에 대한 자세한 내용은 [Log Analytics 게이트웨이](../agents/gateway.md)를 참조하세요.

### <a name="azure-private-link"></a>Azure Private Link
Azure Private Link를 사용하여 Log Analytics 작업 영역에 대한 프라이빗 엔드포인트를 만들 수 있습니다. 구성되면 이 프라이빗 엔드포인트를 통해 작업 영역에 대한 모든 연결을 설정해야 합니다. Private Link는 DNS 재정의를 사용하여 작동하므로 개별 에이전트에 대한 구성 요구 사항이 없습니다. Private Link에 대한 자세한 내용은 [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](../logs/private-link-security.md)을 참조하세요.

## <a name="prepare-hybrid-machines"></a>하이브리드 컴퓨터 준비
하이브리드 컴퓨터는 Azure에서 실행되지 않는 컴퓨터입니다. 다른 클라우드 또는 호스트된 공급자에서 실행되는 가상 머신이거나 데이터 센터에서 온-프레미스로 실행되는 가상 또는 물리적 컴퓨터입니다. 하이브리드 컴퓨터에서 [Azure Arc 사용 서버](../../azure-arc/servers/overview.md) 를 사용 하 여 azure 가상 머신과 유사 하 게 관리할 수 있습니다. Azure Monitor에서 VM insights를 사용 하 여 Azure virtual machines와 마찬가지로 동일한 프로세스를 사용 하 여 Azure Arc 사용 서버에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. Azure에 대 한 하이브리드 컴퓨터를 준비 하는 방법에 대 한 전체 가이드는 [Azure Arc 사용 서버 계획 및 배포](../../azure-arc/servers/plan-at-scale-deployment.md)를 참조 하세요. 이 작업에는 개별 컴퓨터를 사용하도록 설정하고, [Azure Policy](../../governance/policy/overview.md)를 사용하여 전체 하이브리드 환경을 규모에 맞게 사용하도록 설정하는 작업이 포함됩니다.

Azure Arc 사용 서버에 대 한 비용은 더 이상 없지만, 사용 하도록 설정 하는 다른 옵션에 대 한 비용이 있을 수 있습니다. 자세한 내용은 [Azure Arc 가격](https://azure.microsoft.com/pricing/details/azure-arc/)을 참조하세요. 하이브리드 컴퓨터가 VM 인사이트에 사용하도록 설정되면 작업 영역에서 수집된 데이터에 대한 비용이 있습니다.

### <a name="machines-that-cant-use-azure-arc-enabled-servers"></a>Azure Arc 사용 서버를 사용할 수 없는 컴퓨터
다음 기준과 일치 하는 하이브리드 컴퓨터가 있는 경우 Azure Arc 사용 서버를 사용할 수 없습니다.

- 컴퓨터의 운영 체제는 Azure Arc에서 사용하도록 설정된 서버 에이전트에서 지원되지 않습니다. 자세한 내용은 [지원되는 운영 체제](../../azure-arc/servers/agent-overview.md#prerequisites)를 참조하세요.
- 보안 정책은 컴퓨터에서 Azure에 직접 연결하도록 허용하지 않습니다. Log Analytics 에이전트는 Azure Arc 사용 서버가 설치 되어 있는지 여부에 관계 없이 [Log Analytics 게이트웨이](../agents/gateway.md) 를 사용할 수 있습니다. Azure Arc에서 사용하도록 설정된 서버 에이전트에서 Azure에 직접 연결해야 합니다.

Azure Monitor를 사용하여 이러한 컴퓨터를 모니터링할 수 있지만 해당 에이전트를 수동으로 설치해야 합니다. Log Analytics 에이전트 및 종속성 에이전트를 이러한 하이브리드 컴퓨터에 수동으로 설치하려면 [하이브리드 가상 머신에 대한 VM 인사이트 사용](vminsights-enable-hybrid.md)을 참조하세요.

> [!NOTE]
> Azure Arc 사용 서버에 대 한 개인 끝점은 현재 공개 미리 보기로 제공 됩니다. 엔드포인트를 사용하면 하이브리드 컴퓨터에서 가상 네트워크의 개인 IP 주소를 사용하여 Azure에 안전하게 연결할 수 있습니다.

## <a name="enable-vm-insights-on-machines"></a>컴퓨터에서 VM 인사이트 사용
컴퓨터에서 VM 인사이트를 사용하도록 설정하면 Log Analytics 에이전트 및 종속성 에이전트를 설치하고, 작업 영역에 연결하고, 성능 데이터 수집을 시작합니다. 성능 보기 및 통합 문서를 사용하여 다양한 게스트 운영 체제 메트릭에 대한 추세를 분석하고, 실행되는 프로세스와 컴퓨터 간의 종속성을 분석하기 위해 VM 인사이트의 맵 기능을 사용하도록 설정하고, 다양한 경고 규칙을 만드는 데 필요한 데이터를 수집할 수 있습니다.

Azure virtual machines 및 Azure Arc 사용 서버에 대해 동일한 방법을 사용 하 여 개별 컴퓨터에서 VM insights를 사용 하도록 설정할 수 있습니다. 이러한 방법에는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 개별 컴퓨터를 온보딩하거나 Azure Policy를 사용하여 컴퓨터를 규모에 맞게 사용하도록 설정하는 방법이 포함됩니다. VM 인사이트에 대한 직접적인 비용은 없지만 Log Analytics 작업 영역에서 수집된 데이터의 수집 및 보존에 대한 비용이 있습니다.

VM 인사이트를 컴퓨터에 사용하도록 설정하는 다양한 옵션은 [VM 인사이트 사용 개요](vminsights-enable-overview.md)를 참조하세요. 새 컴퓨터가 만들어질 때 VM 인사이트를 자동으로 사용하도록 설정하는 정책을 만들려면 [Azure Policy를 사용하여 VM 인사이트 사용](vminsights-enable-policy.md)을 참조하세요.


## <a name="send-guest-performance-data-to-metrics"></a>메트릭에 게스트 성능 데이터 보내기
[Azure Monitor 에이전트](../agents/azure-monitor-agent-overview.md) 는 Azure Monitor, microsoft Defender for Cloud 및 microsoft 센티널을 완벽 하 게 지 원하는 Log Analytics 에이전트를 대체 합니다. 그때까지는 Log Analytics 에이전트와 함께 설치하여 성능 데이터를 컴퓨터의 게스트 운영 체제에서 Azure Monitor 메트릭으로 보낼 수 있습니다. 이 구성을 사용하면 메트릭 탐색기를 사용하여 이 데이터를 평가하고 메트릭 경고를 사용할 수 있습니다.

Azure Monitor 에이전트에는 수집해야 하는 데이터와 해당 데이터를 보내야 하는 위치를 정의하는 하나 이상의 DCR(데이터 수집 규칙)이 필요합니다. 단일 DCR은 동일한 리소스 그룹의 모든 컴퓨터에서 사용할 수 있습니다.

다음 데이터 원본을 사용하여 모니터링할 컴퓨터가 있는 각 리소스 그룹에 대해 단일 DCR을 만듭니다. 

- **데이터 원본 유형**: 성능 카운터
- **대상**: Azure Monitor 메트릭

Log Analytics 에이전트에서 이미 수집 중인 데이터와 중복될 수 있으므로 데이터를 로그에 보내지 않도록 주의해야 합니다.

Azure virtual machines 및 Azure Arc 사용 서버에 동일한 방법을 사용 하 여 개별 컴퓨터에 Azure Monitor 에이전트를 설치할 수 있습니다. 이러한 방법에는 Azure Portal 또는 Resource Manager 템플릿을 사용하여 개별 컴퓨터를 온보딩하거나 Azure Policy를 사용하여 컴퓨터를 규모에 맞게 사용하도록 설정하는 방법이 포함됩니다. Azure Arc 사용 서버를 사용할 수 없는 하이브리드 컴퓨터의 경우 에이전트를 수동으로 설치 합니다.

Azure Portal을 사용하여 DCR을 만들고 Azure Monitor 에이전트를 하나 이상의 에이전트에 배포하려면 [Azure Portal에서 규칙 및 연결 만들기](../agents/data-collection-rule-azure-monitor-agent.md)를 참조하세요. 다른 설치 방법은 [Azure Monitor 에이전트 설치](../agents/azure-monitor-agent-install.md)에서 설명하고 있습니다. 에이전트 및 DCR이 만들어질 때 새 컴퓨터에 자동으로 배포하는 정책을 만들려면 [Azure Policy를 사용하여 대규모로 Azure Monitor 배포](../best-practices.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [가상 머신에 대해 수집된 모니터링 데이터 분석](monitor-virtual-machine-analyze.md)
* [수집된 데이터에서 경고 만들기](monitor-virtual-machine-alerts.md)
* [가상 머신에서 실행되는 워크로드 모니터링](monitor-virtual-machine-workloads.md)
