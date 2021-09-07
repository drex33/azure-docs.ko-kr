---
title: Azure 독일 네트워킹 서비스 | Microsoft Docs
description: Azure 독일에 대한 프라이빗 연결을 위한 기능 및 지침 비교를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: aa1d648cf3e3f43d4a29c5dc199420f8d731848a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122566175"
---
# <a name="azure-germany-networking-services"></a>Azure 독일 네트워킹 서비스

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="expressroute-private-connectivity"></a>ExpressRoute(프라이빗 연결)
Azure ExpressRoute는 Azure 독일에서 일반 공급됩니다. 자세한 내용(파트너 및 피어링 위치 포함)은 [ExpressRoute 글로벌 설명서](../expressroute/index.yml)를 참조하세요.

### <a name="variations"></a>변형

* Azure 독일 고객은 전용 Azure 독일 ExpressRoute 연결을 통해 물리적으로 격리된 용량에 연결합니다.
* Azure 독일은 적어도 400Km 이상 떨어져 있는 여러 지역 쌍을 활용하여 가용성 및 내구성을 향상시킵니다. 
* 기본적으로 모든 Azure 독일 ExpressRoute 연결은 버스트 지원을 통해 액티브-액티브 중복 방식으로 구성되며 최대 10GB 회로 용량을 제공합니다.
* Azure 독일 ExpressRoute 위치는 고객과 Azure 독일 지역 중복 지역에 대해 최적화된 경로(최단 홉, 짧은 대기 시간 및 고성능 포함)를 제공합니다.
* Azure 독일 ExpressRoute 프라이빗 연결은 인터넷을 사용하거나 트래버스하거나 의존하지 않습니다.
* Azure 독일의 물리적 및 논리적 인프라는 물리적으로 전용되며 국제 Microsoft 클라우드 네트워크와 분리되어 있습니다.
* Azure 독일 ExpressRoute는 Microsoft Azure 클라우드 서비스에 대한 프라이빗 연결을 제공하지만 Microsoft 365 또는 Dynamics 365 클라우드 서비스에 대한 연결은 제공하지 않습니다.

### <a name="considerations"></a>고려 사항
두 가지 기본 서비스는 Azure 독일에 대한 프라이빗 네트워크 연결인 ExpressRoute 및 VPN(일반적인 조직의 경우 사이트 간)을 제공합니다.

ExpressRoute를 사용하여 Azure 독일 데이터 센터와 온-프레미스 인프라 간 또는 공동 배치 환경에서 프라이빗 연결을 만들 수 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 일반적인 인터넷 연결보다 더 나은 안정성, 더 빠른 속도, 짧은 대기 시간을 제공합니다. 경우에 따라 온-프레미스 시스템과 Azure 간 데이터 전송에 ExpressRoute 연결을 사용하여 상당한 비용 혜택을 얻습니다.   

ExpressRoute를 사용하면 ExpressRoute 위치(예: ExpressRoute Exchange 공급자 시설)에서 Azure에 대한 연결을 설정합니다. 또는 네트워크 서비스 공급자가 제공하는 MPLS(다중프로토콜 레이블 전환) VPN과 같은 기존 WAN에서 Azure에 직접 연결합니다.

네트워크 서비스에서 Azure 독일 고객 애플리케이션 및 솔루션을 지원하려면 ExpressRoute(프라이빗 연결)를 구현하여 Azure 독일에 연결하는 것이 좋습니다. VPN 연결을 사용하는 경우 다음을 고려하세요.

* 프라이빗 연결 또는 다른 보안 연결 메커니즘이 필요한지 여부를 확인하고 추가 제한을 식별하려면 권한 부여 공식 기관/에이전시에 문의하세요.
* 사이트 간 VPN에서 프라이빗 연결 영역을 통해 라우팅하도록 지시할지 여부를 결정합니다.
* 허가된 프라이빗 연결 액세스 공급자를 통해 MPLS 회로 또는 VPN을 획득해야 합니다.

프라이빗 연결 아키텍처를 사용하는 경우 Azure 독일의 GN/I(게이트웨이 네트워크/인터넷) 에지 라우터 경계 지점에 대한 연결을 위해 적절한 구현이 설정되고 유지 관리되는지 유효성을 검사해야 합니다. 마찬가지로 조직은 Azure 독일에 대한 온-프레미스 환경 및 GN/C(게이트웨이 네트워크/고객) 에지 라우터 경계 지점 간 네트워크 연결을 설정해야 합니다.

Azure 독일 지역 내의 한 피어링 위치에서 ExpressRoute를 통해 Microsoft에 연결하는 경우 독일 경계 내의 모든 지역에서 모든 Microsoft Azure 클라우드 서비스에 액세스할 수 있습니다. 예를 들어 ExpressRoute를 통해 베를린의 Microsoft에 연결하는 경우 Azure 독일에서 호스트되는 모든 Microsoft 클라우드 서비스에 액세스할 수 있습니다.

위치 및 파트너에 대한 자세한 내용 및 Azure 독일 피어링 위치의 자세한 ExpressRoute 목록은 [ExpressRoute 글로벌 설명서](../expressroute/index.yml)의 **개요** 탭을 참조하세요.

ExpressRoute 회로를 두 개 이상 구입할 수 있습니다. 여러 연결이 있으면 지리적 중복으로 인해 고가용성에 상당한 이점을 제공합니다. ExpressRoute 회로가 여러 개 있는 경우 퍼블릭 피어링의 Microsoft에서 보급된 동일한 접두사 집합을 받게 됩니다. 즉, 네트워크에서 Microsoft까지 여러 경로가 있습니다. 이 상황에서는 잠재적으로 네트워크에서 최적이 아닌 라우팅이 결정될 수 있습니다. 결과적으로 다른 서비스에 최적이 아닌 연결 환경이 발생할 수도 있습니다. 자세한 내용은 [ExpressRoute 글로벌 설명서](../expressroute/index.yml)의 **방법 가이드 > 모범 사례** 탭을 참조하고 **라우팅 최적화** 를 선택합니다.

## <a name="support-for-load-balancer"></a>부하 분산 장치에 대한 지원
Azure Load Balancer는 Azure 독일에서 일반 공급됩니다. 자세한 내용은 [Load Balancer 글로벌 설명서](../load-balancer/load-balancer-overview.md)를 참조하세요. 

## <a name="support-for-traffic-manager"></a>Traffic Manager에 대한 지원
Azure Traffic Manager는 일반적으로 Azure 독일에서 제공합니다. 자세한 내용은 [Traffic Manager 글로벌 설명서](../traffic-manager/traffic-manager-overview.md)를 참조하세요. 

## <a name="support-for-virtual-network-peering"></a>가상 네트워크 피어링에 대한 지원 
가상 네트워크 피어링은 Azure 독일에서 일반 공급됩니다. 자세한 내용은 [가상 네트워크 피어링 글로벌 설명서](../virtual-network/virtual-network-peering-overview.md)를 참조하세요. 

## <a name="support-for-vpn-gateway"></a>VPN Gateway에 대한 지원 
Azure VPN Gateway는 Azure 독일에서 일반 공급됩니다. 자세한 내용은 [VPN Gateway 글로벌 설명서](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure 독일 블로그](/archive/blogs/azuregermany/)를 구독하세요.