---
title: 포함 파일
description: 포함 파일
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 01/12/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 4648693bc0dd7cebd8f8aec1cf878a8c3abfc49e
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122636723"
---
| 리소스 | 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |50 |
| Azure Resource Manager를 사용하는 구독당 지역별 ExpressRoute 회로 |10 |
| ExpressRoute 표준을 사용하는 Azure 개인 피어링에 보급되는 최대 IPv4 경로 수 |4,000 |
| ExpressRoute 프리미엄 추가 기능을 사용하는 Azure 개인 피어링에 보급되는 최대 IPv4 경로 수 |10000 |
| ExpressRoute 표준을 사용하는 Azure 개인 피어링에 보급되는 최대 IPv6 경로 수 |100 |
| ExpressRoute 프리미엄 추가 기능을 사용하는 Azure 개인 피어링에 보급되는 최대 IPv6 경로 수 |100 |
| ExpressRoute 연결을 위한 VNet 주소 공간의 Azure 개인 피어링에서 보급되는 최대 IPv4 경로 수 | 1,000 |
| ExpressRoute 연결을 위한 VNet 주소 공간의 Azure 개인 피어링에서 보급되는 최대 IPv6 경로 수 | 1,000 |
| ExpressRoute 표준을 사용하는 Microsoft 피어링에 보급되는 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용하는 Microsoft 피어링에 보급되는 최대 경로 수 |200 |
| 동일한 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |4 |
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수 |16(자세한 내용은 [게이트웨이 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md#aggthroughput)를 참조하세요.) |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |[ExpressRoute 회로당 가상 네트워크 수](#vnetpercircuit) 표를 참조하세요.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> ExpressRoute 회로당 가상 네트워크 수
| **회로 크기** | **표준에 대한 가상 네트워크 링크 수** | **프리미엄 추가 기능을 사용하는 가상 네트워크 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |
| 40Gbps* |10 |100 |
| 100Gbps* |10 |100 |

**100Gbps ExpressRoute Direct만 해당*

> [!NOTE]
> Global Reach 연결은 ExpressRoute 회로당 가상 네트워크 연결 제한에 따라 계산됩니다. 예를 들어 10Gbps 프리미엄 회로는 5개의 Global Reach 연결과 95개의 ExpressRoute 게이트웨이에 대한 연결 또는 95개의 Global Reach 연결과 5개의 ExpressRoute 게이트웨이에 대한 연결 또는 회로에 대해 최대 100개의 연결 제한까지의 기타 조합을 허용합니다.
