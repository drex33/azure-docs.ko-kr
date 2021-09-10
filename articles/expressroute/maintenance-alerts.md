---
title: Azure ExpressRoute 회로 유지 관리 경고를 보고 구성하는 방법
description: Azure Portal의 Service Health 페이지를 사용하여 ExpressRoute 회로 유지 관리에 대한 사용자 지정 경고를 구성하는 방법을 알아봅니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 05/10/2021
ms.author: mialdrid
ms.openlocfilehash: 93335c6f163e016c619fd4803a595ffcc3418a9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528664"
---
# <a name="how-to-view-and-configure-alerts-for-azure-expressroute-circuit-maintenance"></a>Azure ExpressRoute 회로 유지 관리 경고를 보고 구성하는 방법

ExpressRoute는 Azure Service Health를 사용하여 계획되고 예정된 ExpressRoute 회로 유지 관리를 알립니다. Service Health를 사용하면 Azure Portal에서 계획된 유지 관리와 과거 유지 관리를 보고 요구 사항에 가장 적합한 경고와 알림을 구성할 수 있습니다. Azure Service Health에 대한 자세한 내용은 [Azure Service Health란?](../service-health/overview.md)을 참조하세요.

> [!NOTE]
> * 유지 관리 작업 중이거나 하나의 연결에 영향을 미치는 계획되지 않은 이벤트의 경우 트래픽을 정상 연결로 드레이닝하기 위해 AS 경로 앞에 추가하는 것이 좋습니다. Microsoft에서 경로 앞에 추가를 구성하고 서비스 중단을 방지하기 위해 필요한 경로 보급을 적절하게 구성한 경우 트래픽이 정상적인 경로를 통해 라우팅할 수 있는지 확인해야 합니다. 
> * 상태 저장 디바이스에서 ExpressRoute BGP 연결을 종료하면 Microsoft 또는 ExpressRoute 공급자의 계획되거나 계획되지 않은 유지 관리 중에 장애 조치(failover) 문제가 발생할 수 있습니다. 트래픽이 올바르게 장애 조치되는지 확인하고 가능한 경우 상태 비저장 디바이스에서 BGP 세션을 종료하도록 설정을 테스트해야 합니다.
>

## <a name="view-planned-maintenance"></a>계획된 유지 관리 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Service Health 페이지로 이동합니다. 

    :::image type="content" source="./media/maintenance-alerts/search-service-health.png" alt-text="Service Health 페이지를 검색하는 스크린샷."::: 

1. 페이지 왼쪽의 **활성 이벤트** 아래에서 *계획된 유지 관리* 를 선택합니다. 이 페이지에서는 대상 구독, Azure 지역 및 Azure 서비스를 기준으로 필터링하여 개별 유지 관리 이벤트를 볼 수 있습니다.

    :::image type="content" source="./media/maintenance-alerts/view-maintenance.png" alt-text="Service Health의 계획된 유지 관리 페이지 스크린샷." lightbox="./media/maintenance-alerts/view-maintenance-expanded.png"::: 

1. ExpressRoute 관련 유지 관리만 보려면 *서비스* 드롭다운에서 **ExpressRoute** 를 선택합니다. 그런 다음, 목록에서 이슈를 선택하여 이벤트 요약을 봅니다. 진행 중인 유지 관리에 대한 자세한 내용을 보려면 **Issues updates(이슈 업데이트)** 탭을 선택합니다.

    :::image type="content" source="./media/maintenance-alerts/summary.png" alt-text="ExpressRoute 유지 관리 요약의 스크린샷." lightbox="./media/maintenance-alerts/summary-expanded.png":::

## <a name="view-past-maintenance"></a>과거 유지 관리 보기

1. 과거 유지 관리 이벤트를 보려면 페이지 왼쪽의 *기록* 섹션 아래에서 **상태 기록** 을 선택합니다. 

    :::image type="content" source="./media/maintenance-alerts/health-history.png" alt-text="Service Health에서 상태 기록을 선택하는 스크린샷." lightbox="./media/maintenance-alerts/health-history-expanded.png"::: 

1. 이 페이지에서는 대상 구독과 Azure 지역을 기준으로 필터링하여 개별 유지 관리 이벤트를 검토할 수 있습니다. 상태 기록 이벤트의 범위를 더 좁히려면 상태 이벤트 유형을 선택하고 과거 시간 범위를 정의할 수 있습니다. 계획된 ExpressRoute 회로 유지 관리를 필터링하려면 상태 이벤트 유형을 **계획된 유지 관리** 로 설정합니다.

    :::image type="content" source="./media/maintenance-alerts/past-maintenance.png" alt-text="상태 기록 페이지의 과거 유지 관리 스크린샷." lightbox="./media/maintenance-alerts/past-maintenance-expanded.png"::: 

## <a name="create-alerts-and-notifications-for-maintenance-events"></a>유지 관리 이벤트에 대한 경고 및 알림 만들기

1. Azure Service Health는 유지 관리 이벤트에 대한 사용자 지정 경고를 지원합니다. ExpressRoute 경로 회로 유지 관리에 대한 경고를 구성하려면 페이지 왼쪽에 있는 *경고* 섹션에서 **상태 경고** 로 이동합니다. 여기에서 이전에 구성된 경고 테이블을 볼 수 있습니다.

1.  새 경고를 만들려면 페이지 맨 위에 있는 **서비스 상태 경고 추가** 를 선택합니다.

    :::image type="content" source="./media/maintenance-alerts/health-alerts.png" alt-text="Service Health에서 상태 경고를 선택하는 스크린샷." lightbox="./media/maintenance-alerts/health-alerts-expanded.png"::: 

1. 다음 정보를 선택하거나 입력하여 경고 규칙을 만듭니다.

    | 범주 | 설정 | 값 | 
    | --- | -------- | ----- |
    | **Condition** | Subscription | 대상 구독을 선택합니다. |
    |               | 서비스 | *ExpressRoute \ ExpressRoute 회로* |
    |               | 지역 | 지역을 선택하거나 모든 지역의 상태 이벤트에 대해 *전체* 로 둡니다.
    |               | 이벤트 유형 | *계획된 유지 관리* 를 선택합니다. |
    | **actions** | 작업 그룹 이름 | *작업 그룹* 은 알림 유형을 결정하고 알림이 전송되는 대상을 정의합니다. 작업 그룹을 만들고 관리하는 방법에 대한 자세한 내용은 Azure Portal에서 [작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조하세요. |
    | **경고 규칙 세부 정보** | 경고 규칙 이름 | 경고 규칙을 식별할 *이름* 을 입력합니다. |
    |                        | Description | 이 경고 규칙이 수행하는 작업에 대한 설명을 제공합니다. | 
    |                        | 리소스 그룹에 경고 규칙 저장 | 이 경고 규칙을 만들 *리소스 그룹* 을 선택합니다. |
    |                        | 경고 규칙을 만들면 바로 사용 | 만든 경고 규칙을 바로 사용하려면 이 확인란을 선택합니다. |

1. **경고 규칙 만들기** 를 선택하여 구성을 저장합니다.

    :::image type="content" source="./media/maintenance-alerts/create-alert-rule.png" alt-text="경고 규칙 만들기 페이지의 스크린샷."::: 

## <a name="next-steps"></a>다음 단계

* [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md) 및 [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)에 관해 자세히 알아봅니다.
* [메트릭을 사용자 지정](expressroute-monitoring-metrics-alerts.md)하고 [연결 모니터](../network-watcher/connection-monitor-overview.md)를 만듭니다.

