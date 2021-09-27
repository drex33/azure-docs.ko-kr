---
title: Azure ExpressRoute 모니터링
description: Azure ExpressRoute를 모니터링하는 방법을 알아보려면 여기에서 시작합니다.
services: expressroute
author: duongau
ms.author: duau
ms.service: expressroute
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: 2e54166f71071a21b84e5f58e27c4ebc8a7df03a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587861"
---
# <a name="monitoring-azure-expressroute"></a>Azure ExpressRoute 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 

이 문서에서는 Azure ExpressRoute에서 생성된 데이터 모니터링에 대해 설명합니다. Azure ExpressRoute는 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우 [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.
 
## <a name="expressroute-insights"></a>ExpressRoute 인사이트

Azure의 일부 서비스는 Azure Portal에서 서비스 모니터링을 위한 시작점을 제공하는 미리 빌드된 모니터링 대시보드를 특별히 제공합니다. 이러한 특수 대시보드를 “인사이트”라고 합니다.

ExpressRoute는 네트워크 인사이트를 사용하여 모든 ExpressRoute 구성 요소(피어링, 연결, 게이트웨이) 서로 간의 자세한 토폴로지 매핑을 제공합니다. 또한 ExpressRoute의 네트워크 인사이트에는 가용성, 처리량, 패킷 삭제, 게이트웨이 메트릭을 위한 메트릭 대시보드가 미리 로드되어 있습니다. 자세한 내용은 [Networking Insights를 사용하는 Azure ExpressRoute 인사이트](expressroute-network-insights.md)를 참조하세요.

## <a name="monitoring-data"></a>데이터 모니터링 

Azure ExpressRoute는 [Azure 리소스에서 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

Azure ExpressRoute에서 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Azure ExpressRoute 데이터 참조 모니터링](monitor-expressroute-reference.md)을 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. *Azure ExpressRoute* 에 대한 범주는 [Azure ExpressRoute 모니터링 데이터 참조](monitor-expressroute-reference.md#resource-logs)에 나열됩니다.

> [!IMPORTANT]
> 이러한 설정을 사용하려면 추가 Azure 서비스(스토리지 계정, 이벤트 허브 또는 Log Analytics)가 필요하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문하세요.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭** 을 열면 메트릭 탐색기를 사용하여 다른 Azure 서비스에 대한 메트릭과 함께 *Azure ExpressRoute* 에 대한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metrics-page.png" alt-text="ExpressRoute에 대한 메트릭 대시보드의 스크린샷":::

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

* **ExpressRoute** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링합니다. 
* **Global Reach** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링하고 Global Reach를 사용하도록 설정한 ExpressRoute 회로 리소스를 선택합니다. 
* **ExpressRoute Direct** 메트릭을 보려면 *ExpressRoute 포트* 를 기준으로 리소스 종류를 필터링합니다. 

메트릭을 선택하면 기본 집계가 적용됩니다. 필요에 따라 분할을 적용하여 차원이 다른 메트릭을 표시할 수 있습니다.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다. ExpressRoute 리소스 로그에 대한 스키마는 [Azure ExpressRoute 데이터 참조](monitor-expressroute-reference.md#schemas)에 있습니다. 

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 플랫폼 로깅입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.

ExpressRoute는 다음 테이블에 데이터를 저장합니다.

| 테이블 | Description |
| ----- | ----------- |
| AzureDiagnostics | 여러 서비스에서 리소스 로그를 저장하는 데 사용하는 공통 테이블입니다. ExpressRoute의 리소스 로그는 `MICROSOFT.NETWORK`로 식별할 수 있습니다. |
| AzureMetrics | 상태 및 성능을 측정하는 ExpressRoute에서 내보낸 메트릭 데이터입니다. 

이러한 테이블을 보려면 ExpressRoute 회로 리소스로 이동하여 ‘모니터링’에서 **로그** 를 선택합니다.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

다음은 Azure ExpressRoute 리소스를 모니터링하기 위해 로그 검색 창에 입력할 수 있는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](../azure-monitor/logs/log-query-overview.md)에서 작동합니다.

* 지난 12시간 동안 학습한 BGP 경로 테이블을 쿼리하려는 경우

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceType , network_s, path_s, OperationName
    ```

* 수준, 리소스 종류 및 네트워크별로 BGP 정보 메시지를 쿼리하려는 경우

    ```Kusto
    AzureDiagnostics
    | where Level == "Informational"
    | where ResourceType == "EXPRESSROUTECIRCUITS"
    | project TimeGenerated, ResourceId , Level, ResourceType , network_s, path_s
    ```

* 지난 1시간 동안의 트래픽 그래프 BitInPerSeconds를 쿼리하려는 경우
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsInPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 지난 1시간 동안의 트래픽 그래프 BitOutPerSeconds를 쿼리하려는 경우
    
    ```Kusto
    AzureMetrics
    | where MetricName == "BitsOutPerSecond"
    | summarize by Average, bin(TimeGenerated, 1h), Resource
    | render timechart
    ```

* 5분 간격으로 ArpAvailability 그래프를 쿼리하려는 경우
    
    ```Kusto
    AzureMetrics
    | where MetricName == "ArpAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

* 5분 간격으로 BGP 가용성 그래프를 쿼리하려는 경우

    ```Kusto
    AzureMetrics
    | where MetricName == "BGPAvailability"
    | summarize by Average, bin(TimeGenerated, 5m), Resource
    | render timechart
    ```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.

다음 표에는 ExpressRoute에 대한 일반적인 경고 규칙과 권장되는 경고 규칙이 나와 있습니다.

| 경고 유형 | 조건 | 설명  |
|:---|:---|:---|
| ARP 가용성 저하 | 차원 이름: 피어링 유형, 집계 유형: Avg, 연산자: 보다 작음, 임계값: 100% | 피어링 유형에 대해 ARP 가용성이 저하된 경우입니다. |
| BGP 가용성 저하 | 차원 이름: 피어, 집계 유형: Avg, 연산자: 보다 작음, 임계값: 100% | 피어에 대해 BGP 가용성이 저하된 경우입니다. |

### <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성하려면 **Azure Monitor** 로 이동하여 **경고** 를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="경고":::
2. **+ 대상 선택** 을 선택하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="대상":::
3. 경고 세부 정보를 정의합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="작업 그룹":::
4. 작업 그룹을 정의하고 추가합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="작업 그룹 추가":::


### <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="각 피어링":::

### <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건** 에서 신호 유형으로 **활동 로그** 를 선택하고 신호를 선택할 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="활동 로그"::::

## <a name="next-steps"></a>다음 단계

* ExpressRoute에서 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [ExpressRoute 데이터 참조 모니터링](monitor-expressroute-reference.md)을 참조하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/overview.md)을 참조하세요.
