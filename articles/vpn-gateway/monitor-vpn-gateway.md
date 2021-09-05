---
title: Azure VPN Gateway 모니터링
description: Azure Monitor를 사용하여 VPN Gateway 메트릭을 보는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/23/2021
ms.author: alzam
ms.openlocfilehash: a55c4ac84f218b4fe657f02cac9f10a4e9dc4a68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583968"
---
# <a name="monitoring-vpn-gateway"></a>VPN Gateway 모니터링

Azure Monitor를 사용하여 Azure VPN Gateway를 모니터링할 수 있습니다. 이 문서에서는 포털을 통해 사용할 수 있는 메트릭에 대해 설명합니다. 메트릭은 경량이며 거의 실시간 시나리오를 지원할 수 있으므로 경고 및 신속한 이슈 검색에 유용합니다.


|**메트릭**   | **단위** | **세분성** | **설명** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 바이트/초  | 5분| 게이트웨이에서 모든 사이트 간 연결의 결합된 평균 대역폭 사용률입니다.     |
|**P2SBandwidth**| 바이트/초  | 1분  | 게이트웨이에서 모든 지점 및 사이트 간 연결의 결합된 평균 대역폭 사용률입니다.    |
|**P2SConnectionCount**| 개수  | 1분  | 게이트웨이의 지점 및 사이트 간 연결 수입니다.   |
|**TunnelAverageBandwidth** | 바이트/초    | 5분  | 게이트웨이에서 생성된 터널의 평균 대역폭 사용률입니다. |
|**TunnelEgressBytes** | 바이트 | 5분 | 게이트웨이에서 생성된 터널의 송신 트래픽입니다.   |
|**TunnelEgressPackets** | 개수 | 5분 | 게이트웨이에서 생성된 터널의 송신 패킷 수입니다.   |
|**TunnelEgressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 터널에서 삭제된 송신 패킷 수입니다. |
|**TunnelIngressBytes** | 바이트 | 5분 | 게이트웨이에서 생성된 터널의 수신 트래픽입니다.   |
|**TunnelIngressPackets** | 개수 | 5분 | 게이트웨이에서 생성된 터널의 수신 패킷 수입니다.   |
|**TunnelIngressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 터널에서 삭제된 수신 패킷 수입니다. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>다음 단계를 통해 메트릭을 찾아서 볼 수 있습니다.

1. Portal에서 가상 네트워크 게이트웨이 리소스로 이동합니다.
2. **개요** 를 선택하여 총 터널 수신 및 송신 메트릭을 확인합니다.

   ![경고 규칙을 만들기 위한 선택 항목](./media/monitor-vpn-gateway/overview.png "보기")

3. 위에 나열된 다른 메트릭을 보려면 가상 네트워크 게이트웨이 리소스 아래의 **메트릭** 섹션을 클릭하고 드롭다운 목록에서 메트릭을 선택합니다.

   ![선택 단추 및 리소스 목록의 VPN Gateway](./media/monitor-vpn-gateway/metrics.png "선택")

## <a name="next-steps"></a>다음 단계

터널 메트릭에 대한 경고를 구성하려면 [VPN Gateway 메트릭에 대한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)을 참조하세요.

터널 리소스 로그에 대한 경고를 구성하려면 [VPN Gateway 리소스 로그에 대한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조하세요.
