---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0b97af7f7a274fec000bfee7d2cdeae92a3efc91
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132875868"
---
가상 네트워크 게이트웨이를 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 작업 부하, 처리량, 기능 및 SLA의 종류를 기반으로 하는 요구 사항을 충족하는 SKU를 선택합니다. Azure 가용성 영역의 가상 네트워크 게이트웨이 SKU는 [Azure 가용성 영역 게이트웨이 SKU](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)를 참조하세요.

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>터널, 연결 및 처리량별 게이트웨이 SKU

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU(VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 및 VpnGw5AZ)는 Resource Manager 배포 모델에 대해서만 지원됩니다. 클래식 가상 네트워크는 이전(레거시) SKU를 계속 사용해야 합니다.
>  * 레거시 게이트웨이 SKU(기본, 표준 및 HighPerformance)로 작업하는 방법에 대한 정보는 [VPN Gateway SKU(레거시 SKU) 작업](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)을 참조하세요.
>  * ExpressRoute 게이트웨이 SKU는 [ExpressRoute에 대한 Virtual Network 게이트웨이](../articles/expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요.
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>기능 집합별 게이트웨이 SKU

새 VPN Gateway SKU는 게이트웨이에서 제공한 기능 집합을 간소화합니다.

| **SKU**| **기능**|
| ---    | ---         |
|**기본**(\*\*)   | **경로 기반 VPN**: S2S/연결 터널 10개, P2S에 대한 RADIUS 인증 없음, P2S에 대한 IKEv2 없음<br>**정책 기반 VPN**: (IKEv1): S2S/연결 터널 1개, P2S 없음|
| **기본을 제외한 모든 1세대 및 2세대 SKU** | **경로 기반 VPN:** 최대 100개의 터널(*), P2S, BGP, 활성-활성, 사용자 지정 IPsec/IKE 정책, ExpressRoute/VPN 공존 |
|        |             |

(*) "PolicyBasedTrafficSelectors"를 구성하여 경로 기반 VPN 게이트웨이를 여러 온-프레미스 정책 기반 방화벽 디바이스에 연결할 수 있습니다. 자세한 내용은 [PowerShell을 사용하여 VPN Gateway를 여러 온-프레미스 정책 기반 VPN 디바이스에 연결](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

(\*\*) 기본 SKU는 레거시 SKU로 간주됩니다. Basic SKU에는 몇 가지 기능이 제한됩니다. 새 게이트웨이 SKU 중 하나에 대한 기본 SKU를 사용하는 게이트웨이의 크기를 조정할 수 없습니다. 대신 VPN Gateway를 삭제하고 다시 만드는 작업이 포함된 새 SKU로 변경해야 합니다.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>게이트웨이 SKU - 프로덕션 vs. 개발=테스트 워크로드

SLA 및 기능 집합의 차이로 인해 프로덕션 vs. 개발-테스트에 다음과 같은 SKU를 사용하는 것이 좋습니다.

| **작업**                       | **SKU**               |
| ---                                | ---                    |
| **프로덕션, 중요한 워크로드** | 기본을 제외한 모든 1세대 및 2세대 SKU |
| **개발-테스트 또는 개념 증명**   | 기본(\*\*)                 |
|                                    |                        |

(\*\*) 기본 SKU는 레거시 SKU로 간주되고 기능이 제한됩니다. 기본 SKU를 사용하기 전에 필요한 기능을 지원하는지 확인합니다.

이전 SKU(레거시)를 사용하는 경우 프로덕션 SKU 권장 사항은 표준 및 HighPerformance입니다. 이전 SKU에 대한 정보 및 지침은 [게이트웨이 SKU(레거시)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)를 참조하세요.
