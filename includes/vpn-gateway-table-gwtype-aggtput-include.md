---
title: 포함 파일
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bebedbcd2c36203ec122ebf597ac8a0c64fb1ae4
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129679850"
---
|**VPN<br>게이트웨이<br>생성** |**SKU**   | **S2S/VNet 간<br>터널** | **P2S<br> SSTP 연결** | **P2S<br> IKEv2/OpenVPN 연결** | **집계<br>처리량 벤치마크** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**생성 1**|**기본**   | 최대 10    | 최대 128  | 지원되지 않음  | 100Mbps  | 지원되지 않음| 아니요 |
|**생성 1**|**VpnGw1**  | 최대 30*   | 최대 128  | 최대 250       | 650Mbps  | 지원됨 | 아니요 |
|**생성 1**|**VpnGw2**  | 최대 30*   | 최대 128  | 최대 500       | 1Gbps    | 지원됨 | 아니요 |
|**생성 1**|**VpnGw3**  | 최대 30*   | 최대 128  | 최대 1000      | 1.25Gbps | 지원됨 | 아니요 |
|**생성 1**|**VpnGw1AZ**| 최대 30*   | 최대 128  | 최대 250       | 650Mbps  | 지원됨 | 예 |
|**생성 1**|**VpnGw2AZ**| 최대 30*   | 최대 128  | 최대 500       | 1Gbps    | 지원됨 | 예 |
|**생성 1**|**VpnGw3AZ**| 최대 30*   | 최대 128  | 최대 1000      | 1.25Gbps | 지원됨 | 예 |
|        |            |            |           |                |           |           |     |
|**생성 2**|**VpnGw2**  | 최대 30*   | 최대 128  | 최대 500       | 1.25Gbps | 지원됨 | 아니요 |
|**생성 2**|**VpnGw3**  | 최대 30*   | 최대 128  | 최대 1000      | 2.5Gbps  | 지원됨 | 아니요 |
|**생성 2**|**VpnGw4**  | 최대 30*   | 최대 128  | 최대 5,000      | 5Gbps    | 지원됨 | 아니요 |
|**생성 2**|**VpnGw5**  | 최대 30*   | 최대 128  | 최대 10000      | 10Gbps   | 지원됨 | 아니요 |
|**생성 2**|**VpnGw2AZ**| 최대 30*   | 최대 128  | 최대 500       | 1.25Gbps | 지원됨 | 예 |
|**생성 2**|**VpnGw3AZ**| 최대 30*   | 최대 128  | 최대 1000      | 2.5Gbps  | 지원됨 | 예 |
|**생성 2**|**VpnGw4AZ**| 최대 30*   | 최대 128  | 최대 5,000      | 5Gbps    | 지원됨 | 예 |
|**생성 2**|**VpnGw5AZ**| 최대 30*   | 최대 128  | 최대 10000      | 10Gbps   | 지원됨 | 예 |

(*) S2S VPN 터널이 30개 넘게 필요한 경우 [가상 WAN](../articles/virtual-wan/virtual-wan-about.md)을 사용해야 합니다.

* VpnGw SKU 크기 조정은 기본 SKU의 크기 조정을 제외하고, 동일 생성 내에서 허용됩니다. 기본 SKU는 레거시 SKU이며 기능이 제한됩니다. 기본에서 다른 VpnGw SKU로 이동하려면 기본 SKU VPN 게이트웨이를 삭제하고 원하는 생성 및 SKU 크기 조합으로 새 게이트웨이를 만들어야 합니다. 기본 게이트웨이의 크기는 다른 레거시 SKU로만 조정할 수 있습니다([레거시 SKU 작업](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) 참조).

* 이러한 연결 제한은 별도로 적용됩니다. 예를 들어 VpnGw1 SKU 하나에 SSTP 연결 128개와 IKEv2 연결 250개가 있을 수 있습니다.

* 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

* [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 페이지에서 Service Level Agreement(서비스 수준 계약) 정보를 확인할 수 있습니다.

* 단일 터널에서 최대 1Gbps 처리량을 얻을 수 있습니다. 위 표에서 집계 처리량 벤치마크는 단일 게이트웨이를 통해 집계된 여러 터널의 측정값을 기반으로 합니다. VPN Gateway의 집계 처리량 벤치마크는 S2S + P2S 결합형입니다. **P2S 연결이 많을 경우 처리량 제한으로 인해 S2S 연결에 부정적인 영향을 줄 수 있습니다.** 집계 처리량 벤치마크는 인터넷 트래픽 조건 및 애플리케이션 동작으로 인해 처리량이 보장되지 않습니다.

고객이 다른 알고리즘을 사용하여 SKU의 상대 성능을 파악할 수 있도록, 공개적으로 사용 가능한 iPerf 및 CTSTraffic 도구를 사용하여 성능을 측정했습니다. 아래 표에서는 생성 1, VpnGw SKU의 성능 테스트 결과를 보여줍니다. 여기서 확인할 수 있듯이, IPsec 암호화와 무결성에 모두 GCMAES256 알고리즘을 사용했을 때 최고 성능을 얻었습니다. IPsec 암호화에 AES256을 사용하고 무결성에 SHA256을 사용했을 때는 평균 성능을 얻었습니다. IPsec 암호화에 DES3을 사용하고 무결성에 SHA256을 사용했을 때 성능이 가장 낮았습니다.

|**생성**|**SKU**   | **사용된<br>알고리즘** | **관찰된<br>처리량** | **관찰된<br>터널별 초당 패킷 수** |
|---           |---       | ---                 | ---            | ---                    |
|**생성 1**|**VpnGw1**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 650Mbps<br>500Mbps<br>120Mbps   | 58,000<br>50,000<br>50,000|
|**생성 1**|**VpnGw2**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1Gbps<br>500Mbps<br>120Mbps | 90,000<br>80,000<br>55,000|
|**생성 1**|**VpnGw3**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1.25Gbps<br>550Mbps<br>120Mbps | 105,000<br>90,000<br>60,000|
|**생성 1**|**VpnGw1AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 650Mbps<br>500Mbps<br>120Mbps   | 58,000<br>50,000<br>50,000|
|**생성 1**|**VpnGw2AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1Gbps<br>500Mbps<br>120Mbps | 90,000<br>80,000<br>55,000|
|**생성 1**|**VpnGw3AZ**| GCMAES256<br>AES256, SHA256<br>DES3, SHA256| 1.25Gbps<br>550Mbps<br>120Mbps | 105,000<br>90,000<br>60,000|
