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
ms.openlocfilehash: 4f07659381c852e35585b5127d97c932a05088c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566715"
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

### <a name="aggregation-types"></a>집계 형식

메트릭 탐색기는 SUM, MAX, MIN, AVG 및 COUNT를 [집계 형식](../azure-monitor/essentials/metrics-charts.md#aggregation)으로 지원합니다. 각 ExpressRoute 메트릭에 대한 인사이트를 검토할 때 권장 집계 형식을 사용하세요.

* Sum: 집계 간격 중에 캡처된 모든 값의 합계입니다. 
* Count: 집계 간격 중에 캡처된 측정 수입니다. 
* Average: 집계 간격 중에 캡처된 메트릭 값의 평균입니다. 
* Min: 집계 간격 중에 캡처된 가장 작은 값입니다. 
* Max: 집계 간격 중에 캡처된 가장 큰 값입니다.

>[!NOTE]
>**클래식 메트릭** 을 사용하면 좋지 않습니다.
>

### <a name="circuits-metrics"></a>회로 메트릭

#### <a name="bits-in-and-out---metrics-across-all-peerings"></a>비트 입력 및 출력 - 모든 피어링에 대한 메트릭

집계 형식: *Avg*

지정된 ExpressRoute 회로의 모든 피어링에 대한 메트릭을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="회로 메트릭":::

#### <a name="bits-in-and-out---metrics-per-peering"></a>비트 입력 및 출력 - 피어링당 메트릭

집계 형식: *Avg*

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="피어링당 메트릭":::

#### <a name="bgp-availability---split-by-peer"></a>BGP 가용성 - 피어별 분할  

집계 형식: *Avg*

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 BGP(계층 3 연결)의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 기본 BGP 세션 상태가 개인 피어링에 대해 가동 상태이고 두 번째 BGP 세션 상태는 개인 피어링에 대해 다운 상태인 것을 보여줍니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="피어당 BGP 가용성":::

### <a name="arp-availability---split-by-peering"></a>ARP 가용성 - 피어링별 분할  

집계 형식: *Avg*

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 [ARP](./expressroute-troubleshooting-arp-resource-manager.md)(계층 3 연결)의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 개인 피어링 ARP 세션 상태가 두 피어 모두에서 가동 상태이지만 두 피어에 대한 Microsoft 피어링에서는 다운 상태인 것을 보여줍니다. 두 피어에서 기본 집계(평균)를 활용했습니다.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="피어당 ARP 가용성":::

### <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 메트릭

#### <a name="admin-state---split-by-link"></a>관리 상태 - 링크당 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 각 링크에 대한 관리 상태를 볼 수 있습니다. 관리 상태는 실제 포트가 켜지거나 꺼져 있는지 여부를 나타냅니다. 이 상태는 ExpressRoute Direct 연결을 통해 트래픽을 전달하는 데 필요합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 관리 상태":::

#### <a name="bits-in-per-second---split-by-link"></a>초당 비트 입력 - 링크별 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 입력 수를 볼 수 있습니다. 이 대시보드를 모니터링하여 두 링크의 인바운드 대역폭을 비교합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 입력 수":::

#### <a name="bits-out-per-second---split-by-link"></a>초당 비트 출력 - 링크별 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 출력 수를 볼 수도 있습니다. 이 대시보드를 모니터링하여 두 링크의 아웃바운드 대역폭을 비교합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 출력 수":::

#### <a name="line-protocol---split-by-link"></a>라인 프로토콜 - 링크별 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 각 링크에서 라인 프로토콜을 볼 수 있습니다. 회선 프로토콜은 물리적 링크가 작동되어 ExpressRoute Direct를 통해 실행 중인지 여부를 나타냅니다. 이 대시보드를 모니터링하고 물리적 연결이 끊어진 시기를 알 수 있도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 라인 프로토콜":::

#### <a name="rx-light-level---split-by-link"></a>Rx 라이트 수준 - 링크별 분할

집계 형식: *Avg*

각 포트에 대해 Rx 라이트 수준(ExpressRoute Direct 포트가 **수신** 하는 라이트 수준)을 볼 수 있습니다. 정상 Rx 라이트 수준은 일반적으로 -10dBm~0dBm 사이입니다. Rx 라이트 수준이 정상 범위를 벗어나면 알림을 받도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 라인 Rx 라이트 수준":::

#### <a name="tx-light-level---split-by-link"></a>Tx 라이트 수준 - 링크별 분할

집계 형식: *Avg*

각 포트에 대해 Tx 라이트 수준(ExpressRoute Direct 포트가 **전송** 하는 라이트 수준)을 볼 수 있습니다. 정상 Tx 라이트 수준은 일반적으로 -10dBm~0dBm 사이입니다. Tx 라이트 수준이 정상 범위를 벗어나면 알림을 받도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 라인 Tx 라이트 수준":::

### <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 가상 네트워크 게이트웨이 메트릭

집계 형식: *Avg*

ExpressRoute 게이트웨이를 배포하면 Azure에서 게이트웨이의 컴퓨팅 및 기능이 관리됩니다. 게이트웨이 성능을 더 잘 파악하는 데 사용할 수 있는 6개의 게이트웨이 메트릭이 있습니다.

* CPU 사용률
* 초당 패킷 수
* 피어에 보급된 경로 수
* 피어에서 학습된 경로 수
* 경로 변경 빈도
* 가상 네트워크의 VM 수  

게이트웨이에 성능 문제가 발생할 수 있는 시기를 인식할 수 있도록 이러한 각 메트릭에 대한 경고를 설정하는 것이 좋습니다.

#### <a name="cpu-utilization---split-instance"></a>CPU 사용률 - 인스턴스 분할

집계 형식: *Avg*

각 게이트웨이 인스턴스의 CPU 사용률을 볼 수 있습니다. 일상적인 호스트 유지 관리 중에 CPU 사용률이 일시적으로 급증할 수 있지만 높은 CPU 사용률이 오래 지속되면 게이트웨이가 성능 병목에 도달했음을 나타낼 수 있습니다. ExpressRoute 게이트웨이의 크기를 늘리면 이 문제가 해결될 수 있습니다. CPU 사용률이 특정 임계값을 초과하는 빈도에 대한 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 사용률-분할 메트릭 스크린샷":::

#### <a name="packets-per-second---split-by-instance"></a>초당 패킷 수 - 인스턴스별 분할

집계 형식: *Avg*

이 메트릭은 ExpressRoute 게이트웨이를 통과하는 인바운드 패킷 수를 캡처합니다. 게이트웨이가 온-프레미스 네트워크에서 트래픽을 수신하는 경우 여기에서 일관된 데이터 스트림을 볼 수 있습니다. 초당 패킷 수가 임계값 아래로 떨어지면 게이트웨이가 더 이상 트래픽을 수신하지 않는다는 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="초당 패킷-분할 메트릭 스크린샷":::

#### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>피어에 보급된 경로 수 - 인스턴스별 분할

집계 형식: *Count*

이 메트릭은 ExpressRoute 게이트웨이가 회로에 보급하는 경로 수입니다. 주소 공간에는 VNet 피어링을 사용하여 연결되고 원격 ExpressRoute 게이트웨이를 사용하는 가상 네트워크가 포함될 수 있습니다. 가상 네트워크 주소 공간이 자주 변경되지 않는 한 경로 수가 일정하게 유지되어야 합니다. 보급된 경로 수가 알고 있는 가상 네트워크 주소 공간의 수에 대한 임계값 아래로 떨어질 때 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="피어에 보급된 경로 수 스크린샷":::

#### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>피어에서 학습된 경로 수 - 인스턴스별 분할

집계 형식: *Max*

이 메트릭은 ExpressRoute 회로에 연결된 피어에서 ExpressRoute 게이트웨이가 학습하는 경로 수를 보여줍니다. 이러한 경로는 동일한 회로에 연결된 다른 가상 네트워크에서 가져오거나 온-프레미스에서 학습할 수 있습니다. 학습된 경로의 수가 특정 임계값 아래로 떨어지는 경우에 대한 경고를 설정합니다. 삭제되는 경로 수가 많으면 게이트웨이에 성능 문제가 있거나 원격 피어가 ExpressRoute 회로에 더 이상 경로를 보급하지 않는 것을 나타낼 수 있습니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="피어에서 학습된 경로 수 스크린":::

#### <a name="frequency-of-routes-change---split-by-instance"></a>경로 변경 빈도 - 인스턴스별 분할

집계 형식: *Sum*

이 메트릭은 원격 피어에서 학습되거나 원격 피어에 보급되는 경로의 빈도를 보여줍니다. 먼저 온-프레미스 디바이스를 조사하여 네트워크가 자주 변경되는 이유를 파악해야 합니다. 경로 변경의 빈도가 높으면 ExpressRoute 게이트웨이에 성능 문제가 있을 수 있으며, 이 경우 게이트웨이 SKU를 수직 확장하면 문제가 해결될 수 있습니다. ExpressRoute 게이트웨이에 비정상적인 경로 변경이 보일 때 인식할 수 있도록 빈도 임계값에 대한 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="경로 변경 빈도 메트릭 스크린샷":::

#### <a name="number-of-vms-in-the-virtual-network"></a>가상 네트워크의 VM 수

집계 형식: *Max*

이 메트릭은 ExpressRoute 게이트웨이를 사용하는 가상 머신의 수를 보여줍니다. 가상 머신의 수에는 동일한 ExpressRoute 게이트웨이를 사용하는 피어링된 가상 네트워크의 VM이 포함될 수 있습니다. VM 수가 게이트웨이 성능에 영향을 줄 수 있는 특정 임계값을 초과하는 경우 이 메트릭에 대한 경고를 설정합니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="가상 네트워크의 가상 머신 수 메트릭 스크린샷":::

#### <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 게이트웨이 연결(비트/초)

집계 형식: *Avg*

이 메트릭은 ExpressRoute 회로에 대한 특정 연결에 대한 대역폭 사용량을 보여줍니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="게이트웨이 연결 대역폭 사용량 메트릭 스크린샷":::

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
