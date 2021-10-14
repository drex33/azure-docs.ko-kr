---
title: Azure Virtual WAN 모니터링
description: Azure Monitor를 사용하는 Azure Virtual WAN 로그와 메트릭에 관해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: fd0eeb94bb35248aababa3f3a8d9fe820d4abe7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002874"
---
# <a name="monitoring-virtual-wan"></a>Virtual WAN 모니터링

Azure Monitor를 사용하여 Azure Virtual WAN을 모니터링할 수 있습니다. Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공하는 네트워킹 서비스입니다. Virtual WAN VPN 게이트웨이, ExpressRoute 게이트웨이 및 Azure Firewall에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다. 

이 문서에서는 포털을 통해 사용할 수 있는 메트릭과 진단에 대해 설명합니다. 메트릭은 경량이며 거의 실시간 시나리오를 지원할 수 있으므로 경고 및 신속한 이슈 검색에 유용합니다.

### <a name="monitoring-secured-hub-azure-firewall"></a>보안 허브 모니터링(Azure Firewall) 

Azure Firewall을 사용하여 가상 허브를 보호하도록 선택한 경우 [Azure Firewall 로그 및 메트릭](../firewall/logs-and-metrics.md)에서 관련 로그 및 메트릭을 사용할 수 있습니다.
Azure 방화벽 로그 및 메트릭을 사용 하 여 보안 허브를 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.
안전 하 게 보호 하 고 안전한 허브로 변환 하는 모든 Azure 가상 WAN에 대해 허브가 있는 리소스 그룹에 명시적 방화벽 리소스 개체가 만들어집니다. 

:::image type="content" source="./media/monitor-virtual-wan/firewall-resources-portal.png" alt-text="스크린샷 vWAN 허브 리소스 그룹의 방화벽 리소스를 보여 줍니다.":::

진단 및 로깅 구성은 **진단 설정** 탭에 액세스 하 여 수행 해야 합니다.

:::image type="content" source="./media/monitor-virtual-wan/firewall-diagnostic-settings.png" alt-text="방화벽 진단 설정을 보여 주는 스크린샷":::


## <a name="metrics"></a>메트릭

Azure Monitor의 메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 1분마다 수집되며, 자주 샘플링될 수 있으므로 경고에 유용합니다. 비교적 간단한 논리를 사용하여 경고를 신속하게 발생시킬 수 있습니다.

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

#### <a name="tunnel-packet-drop-metrics"></a>Tunnel 패킷 삭제 메트릭
| 메트릭 | Description|
| --- | --- |
| **Tunnel 송신 패킷 삭제 횟수** | 터널에서 드롭된 발신 패킷 수입니다.|
| **터널 수신 패킷 드롭 수** | 터널에서 드롭된 수신 패킷 수입니다.|
| **터널 NAT 패킷 삭제** | 터널에서 NAT가 설정된 패킷 중 삭제 유형 및 NAT 규칙에 따라 삭제된 패킷 수입니다.|
| **터널 송신 TS 불일치 패킷 삭제** | 터널 트래픽 선택기 불일치의 송신 패킷 삭제 수입니다.|
| **터널 수신 TS 불일치 패킷 삭제** | 터널 트래픽 선택기 불일치의 수신 패킷 삭제 수입니다.|

#### <a name="ipsec-metrics"></a>IPSEC 메트릭
| 메트릭 | Description|
| --- | --- |
| **터널 MMSA 수** | 생성 또는 삭제된 MMSA의 수입니다.|
| **터널 QMSA 수** | 생성 또는 삭제된 IPSEC QMSA의 수입니다.|

#### <a name="routing-metrics"></a>라우팅 메트릭
| 메트릭 | Description|
| --- | --- |
| **BGP 피어 상태** | 피어 및 인스턴스당 BGP 연결 상태입니다.|
| **보급된 BGP 경로** | 피어 및 인스턴스당 알린 경로 수입니다.|
| **학습된 BGP 경로** | 피어 및 인스턴스당 학습 된 경로 수입니다.|
| **VNET 주소 접두사 수** | 게이트웨이에서 사용된/학습된 VNET 주소 접두사의 수입니다.|

**분할 적용** 을 선택 하 고 기본 설정 값을 선택 하 여 피어 및 인스턴스 메트릭을 검토할 수 있습니다. 

#### <a name="traffic-flow-metrics"></a>트래픽 흐름 메트릭
| 메트릭 | Description|
| --- | --- |
| **게이트웨이 대역폭** | 게이트웨이의 평균 사이트 간 집계 대역폭(초당 바이트)입니다.|
| **터널 대역폭** | 터널의 평균 대역폭(초당 바이트)입니다.|
| **터널 송신 바이트** | 터널의 송신 바이트입니다. |
| **터널 송신 패킷** | 터널의 송신 패킷 수입니다. |
| **터널 수신 바이트** | 터널의 수신 바이트입니다.|
| **터널 수신 패킷** | 터널의 수신 패킷 수입니다.|
| **터널 최고 PPS** | 마지막 1분 동안 링크 연결당 초당 패킷 수입니다.|
| **터널 흐름 수** | 링크 연결당 생성된 고유한 흐름의 수입니다.|

### <a name="point-to-site-vpn-gateways"></a>지점-사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 지점-사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 P2S 대역폭** | 게이트웨이의 평균 지점 및 사이트 간 집계 대역폭(초당 바이트)입니다. |
| **P2S 연결 수** |게이트웨이의 지점 및 사이트 간 연결 수입니다. 게이트웨이의 지점 및 사이트 간 연결 수입니다. Azure Monitor에서 정확한 메트릭을 볼 수 있도록 하려면 **P2S 연결 수** 에 대 한 **집계 유형을** **합계로** 선택 합니다. 또한 **인스턴스** 로 분할하는 경우 **최댓값** 도 선택할 수 있습니다. |
| **사용자 VPN 경로 수** | VPN Gateway에 구성된 사용자 VPN 경로 수입니다. 이 메트릭은 **정적** 및 **동적** 경로로 구분할 수 있습니다.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 게이트웨이

다음 메트릭은 Azure ExpressRoute 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **BitsInPerSecond** | 초당 Azure 수신 비트입니다.|
| **BitsOutPerSecond** | 초당 Azure 송신 비트입니다. |
| **CPU 사용률** | Express 경로 게이트웨이의 CPU 사용률입니다.|
| **초당 패킷 수** | Express 경로 게이트웨이의 패킷 수입니다.|
| **피어에 알린 경로 수**| ExpressRoute 게이트웨이에서 피어에 보급된 경로 수입니다. | 
| **피어에서 학습한 경로 수**| ExpressRoute 게이트웨이가 피어에서 학습한 경로 수입니다.|
| **경로 변경 빈도** | ExpressRoute 게이트웨이의 경로 변경 빈도입니다.|
| **Virtual Network VM 수**| 이 ExpressRoute 게이트웨이를 사용하는 VM의 수입니다.|

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>게이트웨이 메트릭 보기

다음 단계를 통해 메트릭을 찾아서 볼 수 있습니다.

1. 포털에서 게이트웨이가 있는 가상 허브로 이동합니다.

2. **VPN(사이트 간)** 을 선택하여 사이트 간 게이트웨이를 찾거나 **ExpressRoute** 를 선택하여 ExpressRoute 게이트웨이를 찾거나 **사용자 VPN(지점-사이트 간)** 을 선택하여 지점-사이트 간 게이트웨이를 찾습니다.

3. **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-virtual-wan/view-metrics.png" alt-text="스크린샷은 Azure Monitor 보기가 선택된 사이트-사이트 VPN 창을 보여줍니다.":::

4. **메트릭** 페이지에서 관심 있는 메트릭을 볼 수 있습니다.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="범주가 강조 표시된 ‘메트릭’ 페이지를 보여 주는 스크린샷.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>진단 로그

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 진단은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 진단 로그** | 상태, 구성, 서비스 업데이트 및 추가 진단과 같은 게이트웨이 관련 진단|
| **터널 진단 로그** | 이러한 로그는 사이트간 IPsec 터널에 대한 연결 및 연결 끊기 이벤트, 협상된 SAS, 연결 끊기 이유 및 추가 진단과 같은 IPsec 터널 관련 로그입니다.|
| **경로 진단 로그** | 정적 경로, BGP, 경로 업데이트 및 추가 진단에 대한 이벤트와 관련된 로그입니다. |
| **IKE 진단 로그** | IPsec 연결에 대한 IKE 관련 진단입니다. |

### <a name="point-to-site-vpn-gateways"></a>지점-사이트 간 VPN 게이트웨이

다음 진단은 Azure 지점-사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 진단 로그** | 상태, 구성, 서비스 업데이트 및 기타 진단과 같은 게이트웨이 관련 진단 |
| **IKE 진단 로그** | IPsec 연결에 대한 IKE 관련 진단입니다.|
| **P2P 진단 로그** | 사용자 VPN(지점 및 사이트 간) P2S 구성 및 클라이언트 이벤트입니다. 여기에는 클라이언트 연결/연결 끊기, VPN 클라이언트 주소 할당 및 기타 진단이 포함됩니다.|

### <a name="express-route-gateways"></a>Express Route 게이트웨이

Azure Virtual WAN Express Route 게이트웨이에 대한 진단 로그는 지원되지 않습니다.

### <a name="view-diagnostic-logs-configuration"></a><a name="diagnostic-steps"></a>진단 로그 구성 보기

다음 단계는 진단 설정을 만들고, 편집하고, 보는 데 도움이 됩니다.

1. 포털에서 Virtual WAN 리소스로 이동한 다음, **연결** 그룹에서 **허브를** 선택합니다. 

   :::image type="content" source="./media/monitor-virtual-wan/select-hub.png" alt-text="vWAN 포털에서 허브 선택을 보여 주는 스크린샷.":::

2. 왼쪽의 **연결** 그룹 아래에서 진단을 검사할 게이트웨이를 선택합니다.

   :::image type="content" source="./media/monitor-virtual-wan/select-hub-gateway.png" alt-text="허브에 대한 연결 섹션을 보여 주는 스크린샷.":::

3. 페이지의 오른쪽 부분에서 **Azure Monitor 보기** 링크를 마우스 오른쪽 단추로 클릭하고 **옵션을**  선택합니다. Log Analytics로 보내거나 이벤트 허브로 스트리밍하거나 단순히 스토리지 계정에 보관하도록 선택할 수 있습니다.

   :::image type="content" source="./media/monitor-virtual-wan/view-hub-gateway-logs.png" alt-text="로그에 대한 Azure Monitor 보기 선택 스크린샷":::

4. 이 페이지에서 새 진단 **설정(+진단 설정** 추가)을 만들거나 기존 설정을 편집(설정 **편집)할** 수 있습니다. 아래 예제와 같이 진단 로그를 Log Analytics로 보내거나, 이벤트 허브로 스트리밍하거나, 타사 솔루션으로 보내거나, 스토리지 계정에 보관하도록 선택할 수 있습니다.

    :::image type="content" source="./media/monitor-virtual-wan/select-gateway-settings.png" alt-text="진단 로그 설정 선택 스크린샷.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 샘플 쿼리

진단 데이터를 Log Analytics 작업 영역으로 보내도록 선택한 경우 아래 예제와 같은 SQL 같은 쿼리를 사용하여 데이터를 검사할 수 있습니다. 자세한 내용은 [Log Analytics 쿼리 언어 를 참조하세요.](/services-hub/health/log_analytics_query_language)

다음 예제에는 사이트 간 경로 진단을 가져오는 쿼리가 포함되어 있습니다.

`AzureDiagnostics | where Category == "RouteDiagnosticLog"`

이 문서의 이전 섹션에서 보고된 테이블에 따라 필요에 따라 **= =** 뒤의 아래 값을 대체합니다.

* ‘GatewayDiagnosticLog’
* ‘IKEDiagnosticLog’
* ‘P2SDiagnosticLog’
* ‘TunnelDiagnosticLog’
* ‘RouteDiagnosticLog’

쿼리를 실행하려면 진단 로그를 받도록 구성한 Log Analytics 리소스를 연 다음, 창 왼쪽의 **일반** 탭에서 **로그를** 선택해야 합니다.

:::image type="content" source="./media/monitor-virtual-wan/log-analytics-query-samples.png" alt-text="Log Analytics 쿼리 샘플.":::

Azure VPN Gateway 사이트 간 및 지점 및 사이트 간 모두에 대한 추가 Log Analytics 쿼리 샘플은 진단 로그를 사용하여 Azure VPN Gateway 문제 해결 페이지를 [참조하세요.](../vpn-gateway/troubleshoot-vpn-with-azure-diagnostics.md) Azure Firewall 경우 로그 분석을 더 쉽게 하기 위해 [통합 문서가](../firewall/firewall-workbook.md) 제공됩니다. 그래픽 인터페이스를 사용하면 Log Analytics 쿼리를 수동으로 작성하지 않고도 진단 데이터를 조사할 수 있습니다. 

## <a name="activity-logs"></a><a name="activity-logs"></a>활동 로그

**활동 로그** 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. Azure 활동 로그(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](../firewall/firewall-diagnostics.md)을 참조하세요.
* Azure Monitor의 메트릭에 대한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.
