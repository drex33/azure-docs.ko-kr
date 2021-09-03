---
title: Azure Virtual WAN 모니터링
description: Azure Monitor를 사용하는 Azure Virtual WAN 로그와 메트릭에 관해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 25e12ce4fd361cb053eae8b0d9992031a91d4616
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469019"
---
# <a name="monitoring-virtual-wan"></a>Virtual WAN 모니터링

Azure Monitor를 사용하여 Azure Virtual WAN을 모니터링할 수 있습니다. Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공하는 네트워킹 서비스입니다. Virtual WAN VPN 게이트웨이, ExpressRoute 게이트웨이 및 Azure Firewall에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다.

이 문서에서는 포털을 통해 사용할 수 있는 메트릭과 진단에 대해 설명합니다. 메트릭은 경량이며 거의 실시간 시나리오를 지원할 수 있으므로 경고 및 신속한 이슈 검색에 유용합니다.

### <a name="monitoring-secured-hub-azure-firewall"></a>보안 허브 모니터링(Azure Firewall) 

Azure Firewall 로그를 사용하여 보안 허브를 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

Azure Firewall을 사용하여 가상 허브를 보호하도록 선택한 경우 [Azure Firewall 로그 및 메트릭](../firewall/logs-and-metrics.md)에서 관련 로그 및 메트릭을 사용할 수 있습니다.

## <a name="metrics"></a>메트릭

Azure Monitor의 메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 1분마다 수집되며, 자주 샘플링될 수 있으므로 경고에 유용합니다. 비교적 간단한 논리를 사용하여 경고를 신속하게 발생시킬 수 있습니다.

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 대역폭** | 게이트웨이의 평균 사이트 간 집계 대역폭(초당 바이트)입니다.|
| **터널 대역폭** | 터널의 평균 대역폭(초당 바이트)입니다.|
| **터널 송신 바이트** | 터널의 송신 바이트입니다. |
| **터널 송신 패킷** | 터널의 송신 패킷 수입니다. |
| **터널 송신 TS 불일치 패킷 삭제** | 터널 트래픽 선택기 불일치의 송신 패킷 삭제 수입니다.|
| **터널 수신 바이트** | 터널의 수신 바이트입니다.|
| **터널 수신 패킷** | 터널의 수신 패킷 수입니다.|
| **터널 수신 TS 불일치 패킷 삭제** | 터널 트래픽 선택기 불일치의 수신 패킷 삭제 수입니다.|

### <a name="point-to-site-vpn-gateways"></a>지점-사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 지점-사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 P2S 대역폭** | 게이트웨이의 평균 지점 및 사이트 간 집계 대역폭(초당 바이트)입니다. |
| **P2S 연결 수** |게이트웨이의 지점 및 사이트 간 연결 수입니다. 게이트웨이의 지점 및 사이트 간 연결 수입니다. Azure Monitor에서 정확한 메트릭을 보고 있는지 확인하려면 **P2S 연결 수** 의 **집계 유형** 으로 **합계** 를 선택합니다. 또한 **인스턴스** 로 분할하는 경우 **최댓값** 도 선택할 수 있습니다. |

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 게이트웨이

다음 메트릭은 Azure ExpressRoute 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **BitsInPerSecond** | 초당 Azure 수신 비트입니다.|
| **BitsOutPerSecond** | 초당 Azure 송신 비트입니다. |

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>게이트웨이 메트릭 보기

다음 단계를 통해 메트릭을 찾아서 볼 수 있습니다.

1. 포털에서 게이트웨이가 있는 가상 허브로 이동합니다.

2. **VPN(사이트 간)** 을 선택하여 사이트 간 게이트웨이를 찾거나 **ExpressRoute** 를 선택하여 ExpressRoute 게이트웨이를 찾거나 **사용자 VPN(지점-사이트 간)** 을 선택하여 지점-사이트 간 게이트웨이를 찾습니다. 페이지에서 게이트웨이 정보를 볼 수 있습니다. 이 정보를 복사합니다. 이 정보는 나중에 Azure Monitor를 사용하여 진단을 확인하는 데 사용됩니다.

3. **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-virtual-wan/metrics.png" alt-text="Azure Monitor에서 보기가 선택된 사이트 간 VPN 창을 보여 주는 스크린샷.":::

4. **메트릭** 페이지에서 관심 있는 메트릭을 볼 수 있습니다.

   :::image type="content" source="./media/monitor-virtual-wan/metrics-page.png" alt-text="범주가 강조 표시된 ‘메트릭’ 페이지를 보여 주는 스크린샷.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>진단 로그

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 진단은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 진단 로그** | 상태, 구성, 서비스 업데이트, 추가 진단 등의 게이트웨이 관련 진단입니다.|
| **터널 진단 로그** | 사이트 간 IPsec 터널의 연결 및 연결 해제 이벤트, 협상된 SA, 연결 해제 이유, 추가 진단 등의 IPsec 터널 관련 로그입니다.|
| **경로 진단 로그** | 정적 경로 이벤트, BGP, 경로 업데이트, 추가 진단 등과 관련된 로그입니다. |
| **IKE 진단 로그** | IPsec 연결에 대한 IKE 관련 진단입니다. |

### <a name="point-to-site-vpn-gateways"></a>지점-사이트 간 VPN 게이트웨이

다음 진단은 Azure 지점-사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

| 메트릭 | 설명|
| --- | --- |
| **게이트웨이 진단 로그** | 상태, 구성, 서비스 업데이트, 기타 진단 등의 게이트웨이 관련 진단입니다. |
| **IKE 진단 로그** | IPsec 연결에 대한 IKE 관련 진단입니다.|
| **P2P 진단 로그** | 사용자 VPN(지점 및 사이트 간) P2S 구성 및 클라이언트 이벤트입니다. 여기에는 클라이언트 연결/연결 끊기, VPN 클라이언트 주소 할당 및 기타 진단이 포함됩니다.|

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>진단 로그 보기

다음 단계를 통해 진단을 찾아서 볼 수 있습니다.

1. 포털에서 Virtual WAN 리소스로 이동합니다. 포털의 Virtual WAN 페이지 **개요** 섹션에서 **Essentials** 를 선택하여 보기를 확장하고 리소스 그룹 정보를 가져옵니다. 리소스 그룹 정보를 복사합니다.

   :::image type="content" source="./media/monitor-virtual-wan/3.png" alt-text="‘복사’ 단추를 가리키는 화살표가 있는 '개요' 섹션을 보여 주는 스크린샷.":::

2. 검색 창에서 **모니터** 로 이동한 다음, 설정 섹션에서 **진단 설정** 을 선택하고 리소스 그룹, 리소스 종류, 리소스 정보를 입력합니다. 이 정보는 이 문서의 앞부분에 있는 [게이트웨이 메트릭 보기](#metrics-steps) 섹션의 2단계에서 복사한 리소스 그룹 정보입니다.

   :::image type="content" source="./media/monitor-virtual-wan/4.png" alt-text="'리소스' 드롭다운을 가리키는 화살표가 있는 '모니터링' 섹션을 보여 주는 스크린샷.":::

3. 결과 페이지에서 **+ 진단 설정 추가** 를 선택한 다음 옵션을 선택합니다. Log Analytics로 보내거나 이벤트 허브로 스트리밍하거나 단순히 스토리지 계정에 보관하도록 선택할 수 있습니다.

   :::image type="content" source="./media/monitor-virtual-wan/5.png" alt-text="메트릭 페이지":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 샘플 쿼리

로그는 **Azure Log Analytics 작업 영역** 에 있습니다. Log Analytics에서 쿼리를 설정할 수 있습니다. 다음 예제에는 사이트 간 경로 진단을 가져오는 쿼리가 포함되어 있습니다.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

필요에 따라 **= =** 뒤에서 아래 값을 바꿉니다.

* ‘GatewayDiagnosticLog’
* ‘IKEDiagnosticLog’
* ‘P2SDiagnosticLog’
* ‘TunnelDiagnosticLog’
* ‘RouteDiagnosticLog’

## <a name="activity-logs"></a><a name="activity-logs"></a>활동 로그

**활동 로그** 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. Azure 활동 로그(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](../firewall/firewall-diagnostics.md)을 참조하세요.
* Azure Monitor의 메트릭에 대한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.
