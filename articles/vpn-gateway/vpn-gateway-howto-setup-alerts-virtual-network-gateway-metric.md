---
title: Azure VPN Gateway 메트릭에 대한 경고 설정
description: Azure Portal을 사용하여 가상 네트워크 VPN Gateway의 메트릭에 따라 Azure Monitor 경고를 설정하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e17f86d08b5892c7df0a761e53e1f16dd43f127a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158306"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway 메트릭에 대한 경고 설정

이 문서는 Azure VPN Gateway 메트릭에 대한 경고를 설정하는 데 도움이 됩니다. Azure Monitor는 Azure 리소스에 대한 경고 설정 기능을 제공합니다. "VPN" 형식의 가상 네트워크 게이트웨이에 대한 경고를 설정할 수 있습니다.

| **메트릭**                                 | **단위**     | **세분성**     | **설명**                                                                         |
| -------------------------------------------| ------------ | ------------------- | --------------------------------------------------------------------------------------- |
| **BGP 피어 상태**                        | 개수        | 5분           | 피어 및 인스턴스당 평균 BGP 연결 상태입니다.                              |
| **보급된 BGP 경로**                  | 개수        | 5분           | 피어 및 인스턴스당 알린 경로 수입니다.                                  |
| **학습된 BGP 경로**                     | 개수        | 5분           | 피어 및 인스턴스당 학습 된 경로 수입니다.                                     |
| **게이트웨이 P2S 대역폭**                  | 바이트/초      | 1분            | 게이트웨이에서 모든 지점 및 사이트 간 연결의 결합된 평균 대역폭 사용률입니다. |
| **게이트웨이 S2S 대역폭**                  | 바이트/초      | 5분           | 게이트웨이에서 모든 사이트 간 연결의 결합된 평균 대역폭 사용률입니다.  |
| **P2S 연결 수**                   | 개수        | 1분            | 게이트웨이의 지점 및 사이트 간 연결 수입니다.                                      |
| **터널 대역폭**                       | 바이트/초      | 5분           | 게이트웨이에서 생성된 터널의 평균 대역폭 사용률입니다.                        |
| **터널 송신 바이트**                    | 바이트        | 5분           | 터널의 송신 바이트 수입니다.                                                 |
| **Tunnel 송신 패킷 삭제 횟수**        | 개수        | 5분           | 터널에 의해 삭제 된 나가는 패킷 수입니다.                                         |
| **터널 송신 패킷**                  | 개수        | 5분           | 터널에서 나가는 패킷 수입니다.                                               |
| **터널 송신 TS 불일치 패킷 삭제**  | 개수        | 5분           | 트래픽 선택기 불일치로 인해 발생 한 터널에 의해 삭제 된 나가는 패킷 수입니다.      |
| **터널 수신 바이트**                   | 바이트        | 5분           | 터널에 대 한 수신 바이트 수입니다.                                                   |
| **터널 수신 패킷 드롭 수**       | 개수        | 5분           | 터널에 의해 삭제 된 들어오는 패킷의 수입니다.                                         |
| **터널 수신 패킷**                 | 개수        | 5분           | 터널에 들어오는 패킷 수입니다.                                                 |
| **터널 수신 TS 불일치 패킷 삭제** | 개수        | 5분           | 트래픽 선택기 불일치로 인해 발생 한 터널에 의해 삭제 된 수신 패킷 수입니다.      |
| **터널 MMSA 수**                      | 개수        | 5분           | 주 모드 보안 연결의 수입니다.                                      |
| **터널 최고 PPS**                        | 개수        | 5분           | 터널 당 초당 최대 패킷 수입니다.                                            |
| **터널 QMSA 수**                      | 개수        | 5분           | 빠른 모드 보안 연결의 수입니다.                                     |
| **터널 총 흐름 수**                | 개수        | 5분           | 터널 당 생성 된 고유 흐름의 수입니다.                                            |
| **사용자 Vpn 경로 수**                   | 개수        | 5분           | VPN Gateway에 구성 된 사용자 VPN 경로 수입니다.                                |
| **VNet 주소 접두사 수**              | 개수        | 5분           | 게이트웨이에서 사용/알린 VNet 주소 접두사의 수입니다.                |

## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure Portal을 사용하여 메트릭에 따라 Azure Monitor 경고 설정

다음 예제 단계를 통해 아래의 항목에 대하여 게이트웨이에 대한 경고를 만듭니다.

- **메트릭:** TunnelAverageBandwidth
- **조건:** 대역폭 > 10바이트/초
- **기간:** 5분
- **경고 작업:** 이메일



1. 가상 네트워크 게이트웨이 리소스로 이동하고 **모니터링** 탭에서 **경고** 를 선택합니다. 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집합니다.

   ![경고 규칙을 만들기 위한 선택 영역](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "생성")

2. VPN Gateway를 리소스로 선택합니다.

   ![선택 단추 및 리소스 목록의 VPN Gateway](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "선택")

3. 경고에 대해 구성할 메트릭을 선택합니다.

   ![메트릭 목록에서 선택한 메트릭](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "선택")
4. 신호 논리를 구성합니다. 여기에는 세 가지 구성 요소가 있습니다.

    a. **차원**: 메트릭에 차원이 있는 경우 경고가 해당 차원의 데이터만 평가하도록 특정 차원 값을 선택할 수 있습니다. 선택 사항입니다.

    b. **조건**: 메트릭 값을 평가하는 작업입니다.

    다. **시간**: 메트릭 데이터의 세분성과 경고를 평가할 기간을 지정합니다.

   ![신호 논리 구성에 대한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "선택")

5. 구성된 규칙을 보려면 **경고 규칙 관리** 를 선택합니다.

   ![경고 규칙 관리 단추](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "선택")

## <a name="next-steps"></a>다음 단계

터널 리소스 로그에 대한 경고를 구성하려면 [VPN Gateway 리소스 로그에 대한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조하세요.
