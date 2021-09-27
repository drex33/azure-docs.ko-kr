---
title: 'Azure ExpressRoute: 모니터링, 메트릭 및 경고'
description: Azure의 모든 메트릭, 경고, 진단 로그에 대한 원스톱 상점과도 같은 Azure Monitor를 사용하여 Azure ExpressRoute 모니터링, 메트릭 및 경고에 대해 알아봅니다.
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/14/2021
ms.author: duau
ms.openlocfilehash: ebb661500fdf14d19218704906d24f391389bec8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667214"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 모니터링, 메트릭 및 경고

이 문서는 Azure Monitor를 사용하여 ExpressRoute 모니터링, 메트릭 및 경고를 이해하는 데 도움이 됩니다. Azure Monitor에서는 Azure 전체의 모든 메트릭, 경고, 진단 로그를 확인할 수 있습니다.
 
>[!NOTE]
>**클래식 메트릭** 을 사용하면 좋지 않습니다.
>

## <a name="expressroute-metrics"></a>ExpressRoute 메트릭

**메트릭** 을 보려면 *Azure Monitor* 페이지로 이동하여 *메트릭* 을 선택합니다. **ExpressRoute** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링합니다. **Global Reach** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링하고 Global Reach를 사용하도록 설정한 ExpressRoute 회로 리소스를 선택합니다. **ExpressRoute Direct** 메트릭을 보려면 *ExpressRoute 포트* 를 기준으로 리소스 종류를 필터링합니다. 

메트릭을 선택하면 기본 집계가 적용됩니다. 필요에 따라 분할을 적용하여 차원이 다른 메트릭을 표시할 수 있습니다.

> [!IMPORTANT]
> Azure Portal ExpressRoute 메트릭을 볼 때 최상의 결과를 위해 **5분 이상** 시간 세분성을 선택합니다.
> 
> :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metric-granularity.png" alt-text="시간 세분성 옵션의 스크린샷.":::

### <a name="aggregation-types"></a>집계 형식:

메트릭 탐색기는 [집계 형식](../azure-monitor/essentials/metrics-charts.md#aggregation)으로 SUM, MAX, MIN, AVG 및 COUNT를 지원합니다. 각 ExpressRoute 메트릭에 대한 인사이트를 검토할 때 권장되는 집계 유형을 사용해야 합니다.

* Sum: 집계 간격 중에 캡처된 모든 값의 합계입니다. 
* Count: 집계 간격 중에 캡처된 측정 수입니다. 
* Average: 집계 간격 중에 캡처된 메트릭 값의 평균입니다. 
* Min: 집계 간격 중에 캡처된 가장 작은 값입니다. 
* Max: 집계 간격 중에 캡처된 가장 큰 값입니다. 

### <a name="expressroute-circuit"></a>ExpressRoute 회로

| 메트릭 | 범주 | 단위 | 집계 형식 | Description | 차원 |  진단 설정을 통해 내보내기 가능? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [Arp 가용성](#arp) | 가용성 | 백분율 | 평균 | 모든 피어에 대한 MSEE의 ARP 가용성 | PeeringType, 피어 |  Yes | 
| [Bgp 가용성](#bgp) | 가용성 | 백분율 | 평균 | 모든 피어에 대한 MSEE의 BGP 가용성 | PeeringType, 피어 |  Yes | 
| [BitsInPerSecond](#circuitbandwidth) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 수신 비트 | PeeringType | No | 
| [BitsOutPerSecond](#circuitbandwidth) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 송신 비트 | PeeringType | No | 
| DroppedInBitsPerSecond | 트래픽 | BitsPerSecond | 평균 | 초당 삭제된 수신 데이터 비트 수 | 피어링 유형 | 예 | 
| DroppedOutBitsPerSecond | 트래픽 | BitPerSecond | 평균 | 초당 삭제된 송신 데이터 비트 수 | 피어링 유형 | Yes | 
| GlobalReachBitsInPerSecond | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 수신 비트 | PeeredCircuitSKey | 예 | 
| GlobalReachBitsOutPerSecond | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 송신 비트 | PeeredCircuitSKey | No | 

>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* 및 *GlobalGlobalReachBitsOutPerSecond* 사용은 하나 이상의 Global Reach 연결이 설정된 경우에만 표시됩니다.
>

### <a name="expressroute-gateways"></a>ExpressRoute 게이트웨이

| 메트릭 | 범주 | 단위 | 집계 형식 | Description | 차원 | 진단 설정을 통해 내보내기 가능? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [CPU 사용률](#cpu) | 성능 | 개수 | 평균 | ExpressRoute 게이트웨이의 CPU 사용률 | roleInstance | Yes | 
| [초당 패킷 수](#packets) | 성능 | 초당 개수 | 평균 | ExpressRoute 게이트웨이의 패킷 수 | roleInstance | No | 
| [피어에 알린 경로 수](#advertisedroutes) | 가용성 | 개수 | 최대 | ExpressRouteGateway에서 피어에 알린 경로 수 | roleInstance | Yes | 
| [피어에서 학습 한 경로 수](#learnedroutes)| 가용성 | 개수 | 최대 | ExpressRouteGateway가 피어에서 학습한 경로 수 | roleInstance | Yes | 
| [경로 변경 빈도](#frequency) | 가용성 | 개수 | 합계 | ExpressRoute 게이트웨이의 경로 변경 빈도 | roleInstance | No | 
| [가상 네트워크의 Vm 수](#vm) | 가용성 | 개수 | 최대 | Virtual Network의 VM 수 | 차원 없음 | No | 

### <a name="expressroute-gateway-connections"></a>Express 경로 게이트웨이 연결

| 메트릭 | 범주 | 단위 | 집계 형식 | Description | 차원 | 진단 설정을 통해 내보내기 가능? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [BitsInPerSecond](#connectionbandwidth) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 수신 비트 | 연결 이름 | No | 
| [BitsOutPerSecond](#connectionbandwidth) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 송신 비트 | 연결 이름 | No | 
| DroppedInBitsPerSecond | 트래픽 | BitsPerSecond | 평균 | 초당 삭제된 수신 데이터 비트 수 | 연결 이름 | 예 | 
| DroppedOutBitsPerSecond | 트래픽 | BitPerSecond | 평균 | 초당 삭제된 송신 데이터 비트 수 | 연결 이름 | Yes | 

### <a name="expressroute-direct"></a>ExpressRoute Direct

| 메트릭 | 범주 | 단위 | 집계 형식 | Description | 차원 | 진단 설정을 통해 내보내기 가능? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [BitsInPerSecond](#directin) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 수신 비트 | 링크 | Yes | 
| [BitsOutPerSecond](#directout) | 트래픽 | BitsPerSecond | 평균 | 초당 Azure 송신 비트 | 링크 | 예 | 
| DroppedInBitsPerSecond | 트래픽 | BitsPerSecond | 평균 | 초당 삭제된 수신 데이터 비트 수 | 링크 | 예 | 
| DroppedOutBitsPerSecond | 트래픽 | BitPerSecond | 평균 | 초당 삭제된 송신 데이터 비트 수 | 링크  | Yes | 
| [AdminState](#admin) | 물리적 연결 | 개수 | 평균 | 포트의 관리 상태 | 링크 | Yes | 
| [LineProtocol](#line) | 물리적 연결 | 개수 | 평균 | 포트의 회선 프로토콜 상태 | 링크 | Yes | 
| [RxLightLevel](#rxlight) | 물리적 연결 | 개수 | 평균 | Rx Light 수준(dBm) | 링크, 레인 | Yes | 
| [TxLightLevel](#txlight) | 물리적 연결 | 개수 | 평균 | Tx Light 수준(dBm) | 링크, 레인 | Yes |

## <a name="circuits-metrics"></a>회로 메트릭

### <a name="bits-in-and-out---metrics-across-all-peerings"></a><a name = "circuitbandwidth"></a>비트 입력 및 출력 - 모든 피어링에 대한 메트릭

집계 형식: *Avg*

지정된 ExpressRoute 회로의 모든 피어링에 대한 메트릭을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="회로 메트릭":::

### <a name="bits-in-and-out---metrics-per-peering"></a>비트 입력 및 출력 - 피어링당 메트릭

집계 형식: *Avg*

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="피어링당 메트릭":::

### <a name="bgp-availability---split-by-peer"></a><a name = "bgp"></a>BGP 가용성-피어로 분할  

집계 형식: *Avg*

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 BGP(계층 3 연결)의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 기본 BGP 세션 상태가 개인 피어링에 대해 가동 상태이고 두 번째 BGP 세션 상태는 개인 피어링에 대해 다운 상태인 것을 보여줍니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="피어당 BGP 가용성":::

### <a name="arp-availability---split-by-peering"></a><a name = "arp"></a>ARP 가용성-피어 링으로 분할  

집계 형식: *Avg*

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 [ARP](./expressroute-troubleshooting-arp-resource-manager.md)(계층 3 연결)의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 개인 피어링 ARP 세션 상태가 두 피어 모두에서 가동 상태이지만 두 피어에 대한 Microsoft 피어링에서는 다운 상태인 것을 보여줍니다. 두 피어에서 기본 집계(평균)를 활용했습니다.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="피어당 ARP 가용성":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 메트릭

### <a name="admin-state---split-by-link"></a><a name = "admin"></a>관리 상태 - 링크당 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 각 링크에 대한 관리 상태를 볼 수 있습니다. 관리 상태는 실제 포트가 켜지거나 꺼져 있는지 여부를 나타냅니다. 이 상태는 ExpressRoute Direct 연결을 통해 트래픽을 전달하는 데 필요합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 관리 상태":::

### <a name="bits-in-per-second---split-by-link"></a><a name = "directin"></a>초당 비트 수-링크로 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 입력 수를 볼 수 있습니다. 이 대시보드를 모니터링하여 두 링크의 인바운드 대역폭을 비교합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 입력 수":::

### <a name="bits-out-per-second---split-by-link"></a><a name = "directout"></a>초 당 비트 아웃-링크로 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 출력 수를 볼 수도 있습니다. 이 대시보드를 모니터링하여 두 링크의 아웃바운드 대역폭을 비교합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 출력 수":::

### <a name="line-protocol---split-by-link"></a><a name = "line"></a>줄 프로토콜-링크로 분할

집계 형식: *Avg*

ExpressRoute Direct 포트 쌍의 각 링크에서 라인 프로토콜을 볼 수 있습니다. 회선 프로토콜은 물리적 링크가 작동되어 ExpressRoute Direct를 통해 실행 중인지 여부를 나타냅니다. 이 대시보드를 모니터링하고 물리적 연결이 끊어진 시기를 알 수 있도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 라인 프로토콜":::

### <a name="rx-light-level---split-by-link"></a><a name = "rxlight"></a>Rx 라이트 수준-링크로 분할

집계 형식: *Avg*

각 포트에 대해 Rx 라이트 수준(ExpressRoute Direct 포트가 **수신** 하는 라이트 수준)을 볼 수 있습니다. 정상 Rx 라이트 수준은 일반적으로 -10dBm~0dBm 사이입니다. Rx 라이트 수준이 정상 범위를 벗어나면 알림을 받도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 라인 Rx 라이트 수준":::

### <a name="tx-light-level---split-by-link"></a><a name = "txlight"></a>Tx 라이트 수준-링크로 분할

집계 형식: *Avg*

각 포트에 대해 Tx 라이트 수준(ExpressRoute Direct 포트가 **전송** 하는 라이트 수준)을 볼 수 있습니다. 정상 Tx 라이트 수준은 일반적으로 -10dBm~0dBm 사이입니다. Tx 라이트 수준이 정상 범위를 벗어나면 알림을 받도록 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 라인 Tx 라이트 수준":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 가상 네트워크 게이트웨이 메트릭

집계 형식: *Avg*

ExpressRoute 게이트웨이를 배포하면 Azure에서 게이트웨이의 컴퓨팅 및 기능이 관리됩니다. 게이트웨이 성능을 더 잘 파악하는 데 사용할 수 있는 6개의 게이트웨이 메트릭이 있습니다.

* CPU 사용률
* 초당 패킷 수
* 피어에 보급된 경로 수
* 피어에서 학습된 경로 수
* 경로 변경 빈도
* 가상 네트워크의 VM 수  

게이트웨이에 성능 문제가 발생할 수 있는 시기를 인식할 수 있도록 이러한 각 메트릭에 대한 경고를 설정하는 것이 좋습니다.

### <a name="cpu-utilization---split-instance"></a><a name = "cpu"></a>CPU 사용률 - 분할 인스턴스

집계 형식: *Avg*

각 게이트웨이 인스턴스의 CPU 사용률을 볼 수 있습니다. 일상적인 호스트 유지 관리 중에 CPU 사용률이 일시적으로 급증할 수 있지만 높은 CPU 사용률이 오래 지속되면 게이트웨이가 성능 병목에 도달했음을 나타낼 수 있습니다. ExpressRoute 게이트웨이의 크기를 늘리면 이 문제가 해결될 수 있습니다. CPU 사용률이 특정 임계값을 초과하는 빈도에 대한 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 사용률-분할 메트릭 스크린샷":::

### <a name="packets-per-second---split-by-instance"></a><a name = "packets"></a>초당 패킷-인스턴스당 분할

집계 형식: *Avg*

이 메트릭은 ExpressRoute 게이트웨이를 통과하는 인바운드 패킷 수를 캡처합니다. 게이트웨이가 온-프레미스 네트워크에서 트래픽을 수신하는 경우 여기에서 일관된 데이터 스트림을 볼 수 있습니다. 초당 패킷 수가 임계값 아래로 떨어지면 게이트웨이가 더 이상 트래픽을 수신하지 않는다는 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="초당 패킷-분할 메트릭 스크린샷":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a><a name = "advertisedroutes"></a>인스턴스당 피어 분할에 알린 경로 수

집계 형식: *Count*

이 메트릭은 ExpressRoute 게이트웨이가 회로에 보급하는 경로 수입니다. 주소 공간에는 VNet 피어링을 사용하여 연결되고 원격 ExpressRoute 게이트웨이를 사용하는 가상 네트워크가 포함될 수 있습니다. 가상 네트워크 주소 공간이 자주 변경되지 않는 한 경로 수가 일정하게 유지되어야 합니다. 보급된 경로 수가 알고 있는 가상 네트워크 주소 공간의 수에 대한 임계값 아래로 떨어질 때 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="피어에 보급된 경로 수 스크린샷":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a><a name = "learnedroutes"></a>인스턴스당 피어 분할에서 배운 경로 수

집계 형식: *Max*

이 메트릭은 ExpressRoute 회로에 연결된 피어에서 ExpressRoute 게이트웨이가 학습하는 경로 수를 보여줍니다. 이러한 경로는 동일한 회로에 연결된 다른 가상 네트워크에서 가져오거나 온-프레미스에서 학습할 수 있습니다. 학습된 경로의 수가 특정 임계값 아래로 떨어지는 경우에 대한 경고를 설정합니다. 게이트웨이에 성능 문제가 있거나 원격 피어가 ExpressRoute 회로에 더 이상 경로를 보급하지 않는 것을 나타낼 수 있습니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="피어에서 학습된 경로 수 스크린":::

### <a name="frequency-of-routes-change---split-by-instance"></a><a name = "frequency"></a>경로 변경 빈도-인스턴스당 분할

집계 형식: *Sum*

이 메트릭은 원격 피어에서 학습되거나 원격 피어에 보급되는 경로의 빈도를 보여줍니다. 먼저 온-프레미스 디바이스를 조사하여 네트워크가 자주 변경되는 이유를 파악해야 합니다. 경로 변경의 빈도가 높으면 ExpressRoute 게이트웨이에 성능 문제가 있을 수 있으며, 이 경우 게이트웨이 SKU를 수직 확장하면 문제가 해결될 수 있습니다. ExpressRoute 게이트웨이에 비정상적인 경로 변경이 보일 때 인식할 수 있도록 빈도 임계값에 대한 경고를 설정합니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="경로 변경 빈도 메트릭 스크린샷":::

### <a name="number-of-vms-in-the-virtual-network"></a><a name = "vm"></a>Virtual Network Vm 수

집계 형식: *Max*

이 메트릭은 ExpressRoute 게이트웨이를 사용하는 가상 머신의 수를 보여줍니다. 가상 머신의 수에는 동일한 ExpressRoute 게이트웨이를 사용하는 피어링된 가상 네트워크의 VM이 포함될 수 있습니다. VM 수가 게이트웨이 성능에 영향을 줄 수 있는 특정 임계값을 초과하는 경우 이 메트릭에 대한 경고를 설정합니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="가상 네트워크의 가상 머신 수 메트릭 스크린샷":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a><a name = "connectionbandwidth"></a>ExpressRoute 게이트웨이 연결(비트/초)

집계 형식: *Avg*

이 메트릭은 ExpressRoute 회로에 대한 특정 연결에 대한 대역폭 사용량을 보여줍니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="게이트웨이 연결 대역폭 사용량 메트릭 스크린샷":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성하려면 **Azure Monitor** 로 이동하여 **경고** 를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="경고":::
2. **+대상 선택** 을 선택하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="대상":::
3. 경고 세부 정보를 정의합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="작업 그룹":::
4. 작업 그룹을 정의하고 추가합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="작업 그룹 추가":::

## <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="각 피어링":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건** 에서 신호 유형으로 **활동 로그** 를 선택하고 신호를 선택할 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="활동 로그"::::

## <a name="more-metrics-in-log-analytics"></a>Log Analytics의 추가 메트릭

ExpressRoute 회로 리소스로 이동하고 *로그* 탭을 선택하여 ExpressRoute 메트릭을 볼 수도 있습니다. 쿼리하는 모든 메트릭에 대해 아래 열이 출력에 포함됩니다.

| **열** | **형식** | **설명** | 
|  ---  |  ---  |  ---  | 
| TimeGrain | 문자열 | PT1M(메트릭 값은 1분마다 푸시됨) | 
| 개수 | real | 일반적으로 2와 같음(각 MSEE는 1분마다 단일 메트릭 값을 푸시함) | 
| 최소 | real | 두 MSEE에 의해 푸시되는 두 메트릭 값의 최소값 | 
| 최대 | real | 두 MSEE에 의해 푸시되는 두 메트릭 값의 최대값 | 
| 평균 | real | (최소 + 최대)/2와 같음 | 
| 합계 | real | 두 MSEE의 두 메트릭 값 합계(쿼리된 메트릭에 대해 초점을 맞출 주 값) | 
  
## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
* [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
* [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
