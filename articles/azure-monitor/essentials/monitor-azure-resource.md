---
title: Azure Monitor로 Azure 리소스 모니터링 | Microsoft Docs
description: Azure에서 Azure Monitor를 사용하여 리소스의 모니터링 데이터를 수집하고 분석하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/15/2021
ms.openlocfilehash: f93cd688ef22d7765edbd50e742262e9febec14c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297862"
---
# <a name="tutorial-monitor-azure-resources-with-azure-monitor"></a>자습서: Azure Monitor Azure 리소스 모니터링
Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 모니터링은 다른 클라우드 및 온-프레미스의 리소스 외에도 Azure 리소스를 모니터링하는 전체 기능 집합을 제공하는 Azure의 전체 스택 모니터링 서비스인 Azure Monitor 제공됩니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Monitor 무엇이며 다른 Azure 서비스에 대한 포털에 통합되는 방법
> * Azure 리소스에 대해 Azure Monitor 수집한 데이터 형식
> * 데이터를 수집하고 분석하는 데 사용되는 Azure Monitor 도구

> [!NOTE]
> 이 자습서에서는 Azure Monitor 개념을 설명하고 다양한 메뉴 항목을 안내합니다. Azure Monitor 기능 사용으로 바로 이동하려면 [자습서: Azure 리소스에 대한 메트릭 분석부터 시작합니다.](../essentials/tutorial-metrics.md)


## <a name="monitoring-data"></a>데이터 모니터링

### <a name="azure-monitor-data-collection"></a>Azure Monitor 데이터 수집
Azure 리소스를 만드는 즉시 Azure Monitor 사용하도록 설정되고 메트릭 및 활동 로그 수집을 시작합니다. 일부 구성에서는 추가 모니터링 데이터를 수집하고 추가 기능을 사용하도록 설정할 수 있습니다. Azure Monitor 데이터 플랫폼은 메트릭 및 로그로 구성됩니다. 각각은 다양한 종류의 데이터를 수집하고 다양한 Azure Monitor 기능을 사용하도록 설정합니다.

- [Azure Monitor 메트릭은](../essentials/data-platform-metrics.md) 모니터링되는 리소스의 숫자 데이터를 Time Series 데이터베이스에 저장합니다. 메트릭 데이터베이스는 각 Azure 구독에 대해 자동으로 만들어집니다. [메트릭 탐색기를](../essentials/tutorial-metrics.md) 사용하여 Azure Monitor 로그의 데이터를 분석합니다.
- [Azure Monitor 로그는](../logs/data-platform-logs.md) 로그 쿼리를 사용하여 다양한 방법으로 검색 및 분석할 수 있는 로그 및 성능 데이터를 수집합니다. 로그 데이터를 수집하려면 Log Analytics 작업 영역을 만들어야 합니다. [Log Analytics를](../logs/log-analytics-tutorial.md) 사용하여 Azure Monitor 로그의 데이터를 분석합니다.

### <a name="monitoring-data-from-azure-resources"></a>Azure 리소스에서 데이터 모니터링
서로 다른 Azure 서비스의 리소스는 모니터링 요구 사항이 다르지만 동일한 Azure Monitor 도구를 사용하여 모든 Azure 리소스를 분석할 수 있도록 동일한 형식으로 모니터링 데이터를 생성합니다.

Azure 리소스는 다음과 같은 모니터링 데이터를 생성합니다.

- [활동 로그](./platform-logs-overview.md) - 각 Azure 리소스에 대한 작업을 추적하는 구독 수준 이벤트입니다(예: 새 리소스 만들기 또는 가상 머신 시작). 활동 로그 이벤트는 Azure Portal 보기 위해 자동으로 생성되고 수집됩니다. 진단 설정을 만들어 활동 로그를 Azure Monitor 로그로 보낼 수 있습니다.
- [플랫폼 메트릭](../essentials/data-platform-metrics.md) - 정기적으로 자동 수집되며 특정 시간의 리소스 특성을 설명하는 수치 값입니다. 플랫폼 메트릭은 Azure Monitor 메트릭에서 자동으로 생성되고 수집됩니다.
- [리소스 로그](./platform-logs-overview.md) - Azure 리소스에서 수행된 작업에 대한 인사이트를 제공합니다(예: Key Vault 비밀을 얻거나 데이터베이스에 요청). 리소스 로그는 자동으로 생성되지만 Azure Monitor 로그로 보내는 진단 설정을 만들어야 합니다.
- [가상 머신 게스트 메트릭 및 로그]() - Azure 가상 머신의 게스트 운영 체제의 성능 및 로그 데이터입니다. 이 데이터를 수집하고 Azure Monitor 메트릭 및 Azure Monitor 로그로 보내려면 가상 머신에 에이전트를 설치해야 합니다.


## <a name="menu-options"></a>메뉴 옵션
Azure Portal **모니터** 메뉴에서 Azure Monitor 기능에 액세스할 수 있지만 Azure Monitor 기능은 다른 Azure 서비스의 메뉴에서 직접 액세스할 수 있습니다. Azure 서비스에는 약간 다른 환경이 있을 수 있지만 Azure Portal 일반적인 모니터링 옵션 집합을 공유합니다. 여기에는 **개요** 및 **활동 로그와** 메뉴의 **모니터링** 섹션에 있는 여러 옵션이 포함됩니다. 

:::image type="content" source="media/monitor-azure-resource/menu-01.png" lightbox="media/monitor-azure-resource/menu-01.png" alt-text="모니터 메뉴 1":::

:::image type="content" source="media/monitor-azure-resource/menu-02.png" lightbox="media/monitor-azure-resource/menu-02.png" alt-text="모니터 메뉴 2":::


## <a name="overview-page"></a>개요 페이지
**개요** 페이지에는 리소스에 대한 세부 정보 및 종종 현재 상태가 포함됩니다. 예를 들어 가상 머신은 현재 실행 상태를 표시합니다. 많은 Azure 서비스에는 주요 메트릭 집합에 대한 차트를 포함하는 **모니터링** 탭이 있습니다. 리소스의 작업을 빠르게 볼 수 있는 방법이며, 차트를 클릭하여 메트릭 탐색기에서 열어 더 자세한 분석을 수행할 수 있습니다. 

메트릭 탐색기 사용에 [대한 자습서는 자습서: Azure 리소스에](../essentials/tutorial-metrics.md) 대한 메트릭 분석을 참조하세요.

![개요 페이지](media/monitor-azure-resource/overview-page.png)
### <a name="activity-log"></a>활동 로그 
**활동 로그** 메뉴 항목을 사용하면 현재 리소스에 대한 [활동 로그의](../essentials/activity-log.md) 항목을 볼 수 있습니다. 

:::image type="content" source="media/monitor-azure-resource/activity-log.png" lightbox="media/monitor-azure-resource/activity-log.png" alt-text="활동 로그":::

## <a name="alerts"></a>경고
**경고** 페이지에는 리소스에 대해 발생된 최근 경고가 표시됩니다. 경고는 모니터링 데이터에 중요한 조건이 발견되고 메트릭 또는 로그의 데이터를 사용할 수 있는 경우 사전에 알려 줍니다.

경고 규칙 만들기 및 경고 보기에 [대한 자습서는 자습서: Azure 리소스에](../alerts/tutorial-metric-alert.md) 대한 메트릭 경고 만들기 또는 [자습서: Azure 리소스에](../alerts/tutorial-log-alert.md) 대한 로그 쿼리 경고 만들기를 참조하세요.

:::image type="content" source="media/monitor-azure-resource/alerts-view.png" lightbox="media/monitor-azure-resource/alerts-view.png" alt-text="경고 보기":::

## <a name="metrics"></a>메트릭
**메트릭** 메뉴 항목은 개별 메트릭을 사용하거나 여러 [메트릭을](./metrics-getting-started.md) 결합하여 상관 관계 및 추세를 식별할 수 있는 메트릭 탐색기를 엽니다. **개요** 페이지에서 차트 중 하나를 클릭할 때 열리는 것과 동일한 메트릭 탐색기입니다.

메트릭 탐색기 사용에 [대한 자습서는 자습서: Azure 리소스에](../essentials/tutorial-metrics.md) 대한 메트릭 분석을 참조하세요.

:::image type="content" source="media/monitor-azure-resource/metrics.png" lightbox="media/monitor-azure-resource/metrics.png" alt-text="메트릭 탐색기":::


## <a name="diagnostic-settings"></a>진단 설정
**진단 설정** 페이지에서 리소스에 대한 리소스 로그를 수집하는 [진단 설정을](../essentials/diagnostic-settings.md) 만들 수 있습니다. 여러 위치로 보낼 수 있지만 Log Analytics를 사용하여 분석할 수 있도록 Log Analytics 작업 영역으로 보내는 것이 가장 일반적입니다.

진단 설정 만들기에 대한 [자습서는 자습서: Azure 리소스에서 리소스 로그 수집 및 분석을](../essentials/tutorial-resource-logs.md) 참조하세요.

:::image type="content" source="media/monitor-azure-resource/diagnostic-settings.png" lightbox="media/monitor-azure-resource/diagnostic-settings.png" alt-text="진단 설정":::



## <a name="insights"></a>자세한 정보 
**Insights** 메뉴 항목은 Azure 서비스에 리소스가 있는 경우 리소스에 대한 인사이트를 엽니다. [인사이트](../monitor-reference.md)는 Azure Monitor 데이터 플랫폼과 표준 기능을 기반으로 한 사용자 지정된 모니터링 환경을 제공합니다. 


사용 가능한 인사이트 목록 및 설명서에 대한 링크는 [Insights 및 핵심 솔루션을](../monitor-reference.md#insights-and-curated-visualizations) 참조하세요.

:::image type="content" source="media/monitor-azure-resource/insights.png" lightbox="media/monitor-azure-resource/insights.png" alt-text="Insights 스크린샷":::

## <a name="next-steps"></a>다음 단계
이제 Azure Monitor 대한 기본적인 내용을 이해했으므로 Azure 리소스에 대한 일부 메트릭 분석을 시작하세요.

> [!div class="nextstepaction"]
> [Azure 리소스에 대한 메트릭 분석](../essentials/tutorial-metrics.md)
