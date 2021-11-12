---
title: Azure Monitor를 사용하여 가상 머신 모니터링
description: Azure Monitor를 사용하여 가상 머신과 해당 워크로드의 상태와 성능을 모니터링하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/02/2021
ms.openlocfilehash: c1506fe5fae652c198d53b5b354e818ede5c8a46
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315789"
---
# <a name="monitor-virtual-machines-with-azure-monitor"></a>Azure Monitor를 사용하여 가상 머신 모니터링
이 시나리오에서는 Azure Monitor를 사용하여 가상 머신과 해당 워크로드의 상태와 성능을 모니터링하는 방법을 설명합니다. 여기에는 추세를 식별하기 위해 수집된 데이터의 모니터링, 분석, 시각화에 중요한 원격 분석 컬렉션과 중요한 문제에 대해 사전에 알림을 받도록 경고를 구성하는 방법이 포함됩니다.

이 문서에서는 시나리오를 소개하고 Azure Monitor의 가상 머신 모니터링에 관한 일반적인 개념을 제공합니다. 특정 영역으로 바로 이동하려면 다음 표에 설명된 이 시나리오의 일부인 다른 문서 중 하나를 참조하세요.

| 아티클 | 설명 |
|:---|:---|
| [모니터링 사용](monitor-virtual-machine-configure.md) | VM 인사이트 사용과 모니터링할 각 가상 머신 사용을 포함하여 가상 머신을 모니터링하도록 Azure Monitor를 구성합니다.  |
| [분석](monitor-virtual-machine-analyze.md) | Azure Monitor가 가상 머신 및 해당 게스트 운영 체제와 애플리케이션에서 수집한 모니터링 데이터를 분석하여 추세와 중요한 정보를 식별합니다. |
| [경고](monitor-virtual-machine-alerts.md)   | 모니터링 데이터에서 중요한 문제를 사전에 식별하는 경고를 만듭니다. |
| [보안 모니터링](monitor-virtual-machine-security.md) | 가상 머신의 보안을 모니터링할 Azure 서비스를 검색합니다. |
| [워크로드 모니터링](monitor-virtual-machine-workloads.md) | 가상 머신에서 실행되는 애플리케이션과 기타 워크로드를 모니터링합니다. |

> [!IMPORTANT]
> 이 시나리오에는 일반 공급되지 않는 기능이 포함되지 않습니다. [가상 머신 게스트 상태](vminsights-health-overview.md)와 같은 퍼블릭 미리 보기의 기능은 여기에 제공된 권장 사항을 대폭 수정할 수 있는 가능성이 있습니다. 시나리오는 미리 보기 기능이 일반 공급으로 전환될 때 업데이트됩니다.

## <a name="types-of-machines"></a>머신 유형
이 시나리오에서는 Azure Monitor를 사용하여 다음 유형의 머신을 모니터링합니다. 여기에 설명된 대다수 프로세스는 머신 유형에 관계없이 동일합니다. 다른 유형의 머신에 관한 고려 사항은 해당하는 경우 분명하게 식별됩니다. 머신 유형은 다음과 같습니다. 

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- 관리형 서비스 공급자와 함께 다른 클라우드에서 실행되거나 온-프레미스에서 실행되는 가상 머신인 하이브리드 머신. 온-프레미스에서 실행되는 물리적 머신도 포함됩니다.

## <a name="layers-of-monitoring"></a>모니터링 계층
모니터링이 필요한 가상 머신에는 기본적으로 4개 계층이 있습니다. 각 계층에는 고유한 원격 분석 및 모니터링 요구 사항 세트가 있습니다. 

| 계층 | 설명 |
|:---|:---|
| 가상 머신 호스트 | Azure의 호스트 가상 머신입니다. Azure Monitor는 다른 클라우드의 호스트에는 액세스할 수 없지만 게스트 운영 체제에서 수집된 정보를 사용해야 합니다. 호스트는 구성 변경 등의 작업을 추적하는 데 유용할 수 있지만 일반적으로 중요한 경고에는 사용되지 않습니다. |
| 게스트 운영 체제 | 가상 머신에서 실행되는 운영 체제로, Windows 또는 Linux의 일부 버전입니다. 게스트 운영 체제에서는 성능 데이터, 이벤트 등 많은 양의 모니터링 데이터를 사용할 수 있습니다. Azure Monitor의 VM 인사이트는 게스트 운영 체제의 상태와 성능을 모니터링하기 위한 상당한 양의 논리를 제공합니다. |
| 워크로드 | 비즈니스 애플리케이션을 지원하는 게스트 운영 체제에서 실행되는 워크로드입니다. Azure Monitor는 일부 워크로드에 대해 미리 정의된 모니터링을 제공합니다. 일반적으로는 다른 워크로드가 생성하는 모니터링 데이터를 사용하여 해당 워크로드에 대한 데이터 수집과 경고를 구성해야 합니다. |
| 애플리케이션 | [Application Insights](../app/app-insights-overview.md)를 사용하여 가상 머신을 사용하는 비즈니스 애플리케이션을 모니터링할 수 있습니다. 

:::image type="content" source="media/monitor-virtual-machines/monitoring-layers.png" alt-text="모니터링 계층을 보여 주는 다이어그램" lightbox="media/monitor-virtual-machines/monitoring-layers.png":::

## <a name="vm-insights"></a>VM 인사이트
이 시나리오에서는 가상 머신을 모니터링하기 위한 Azure Monitor의 기본 기능인 [VM 인사이트](../vm/vminsights-overview.md)를 중점적으로 설명합니다. VM 인사이트는 다음 기능을 제공합니다.

- 가상 머신 게스트 운영 체제와 워크로드의 모니터링을 사용하도록 간소화된 에이전트 온보딩. 
- 가상 머신의 게스트 운영 체제에서 핵심 성능 메트릭을 분석하는 데 사용할 수 있는 미리 정의된 추세 성능 차트 및 통합 문서.
- 각 가상 머신에서 실행 중인 프로세스와 다른 머신 및 외부 원본과 상호 연결된 구성 요소를 표시하는 종속성 맵

## <a name="agents"></a>에이전트
Azure Monitor와 같은 모든 모니터링 도구를 사용하려면 게스트 운영 체제에서 데이터를 수집하기 위해 머신에 에이전트가 설치되어 있어야 합니다. Azure Monitor에는 현재 다양한 데이터를 수집하고, 다양한 위치로 데이터를 보내고, 다양한 기능을 지원하는 여러 에이전트가 있습니다. VM 인사이트는 대부분의 고객이 사용하는 에이전트의 배포와 구성을 관리합니다. 다양한 에이전트는 지원되는 특정 시나리오가 필요한 경우 다음 표에서 설명합니다. 다양한 에이전트의 자세한 설명과 비교는 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요.

> [!NOTE]
> Azure Monitor 에이전트는 필요한 기능을 얻은 후 Log Analytics 에이전트, 진단 확장, Telegraf 에이전트를 완전히 대체합니다. 이러한 다른 에이전트는 VM 인사이트, 클라우드용 Microsoft Defender 및 Microsoft Sentinel과 같은 기능에 여전히 필요합니다.

- [Azure Monitor 에이전트](../agents/agents-overview.md#azure-monitor-agent): Azure, 다른 클라우드 환경, 온-프레미스에서 가상 머신을 지원합니다. Azure Monitor 메트릭과 로그에 데이터를 보냅니다. VM 인사이트, Microsoft Defender for Cloud 및 Microsoft Sentinel을 완전히 지원하는 경우 Log Analytics 에이전트 및 진단 확장을 완전히 대체합니다.
- [Log Analytics 에이전트](../agents/agents-overview.md#log-analytics-agent): Azure, 다른 클라우드 환경, 온-프레미스에서 가상 머신을 지원합니다. Azure Monitor 로그에 데이터를 보냅니다. VM 인사이트 및 모니터링 솔루션을 지원합니다. 이 에이전트는 System Center Operations Manager에 사용되는 것과 동일한 에이전트입니다.
- [종속성 에이전트](../agents/agents-overview.md#dependency-agent): 가상 머신에서 실행되는 프로세스와 해당 종속성에 관한 데이터를 수집합니다. Log Analytics 에이전트를 사용하여 Azure로 데이터를 전송하고 VM 인사이트, 서비스 맵 및 Wire Data 2.0 솔루션을 지원합니다.
- [Azure 진단 확장](../agents/agents-overview.md#azure-diagnostics-extension): Azure Monitor 가상 머신에만 사용할 수 있습니다. Azure Event Hubs와 Azure Storage에 데이터를 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[가상 머신에 대해 수집된 모니터링 데이터 분석](monitor-virtual-machine-analyze.md)
