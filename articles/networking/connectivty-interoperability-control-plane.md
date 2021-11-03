---
title: 'Azure의 상호 운용성: 컨트롤 플레인 분석'
description: 이 문서에서는 ExpressRoute, 사이트 간 VPN 및 Azure의 가상 네트워크 피어링 간의 상호 운용성을 분석하는 데 사용할 수 있는 테스트 설정의 제어 평면 분석에 대해 설명합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7a777208563e13945bd0bdd0aac43560143d0a49
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005065"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Azure의 상호 운용성: 컨트롤 플레인 분석

이 문서에서는 [테스트 설정][Setup]의 제어 평면 분석에 대해 설명합니다. 테스트 설정의 [테스트 설정 구성][Configuration] 및 [데이터 평면 분석][Data-Analysis]을 검토할 수도 있습니다.

제어 평면 분석에서는 기본적으로 토폴로지 내의 네트워크 간에 교환되는 경로를 검사합니다. 제어 평면 분석은 다양한 네트워크에서 토폴로지를 보는 방법을 이해하는 데 도움이 됩니다.

## <a name="hub-and-spoke-vnet-perspective"></a>허브 및 스포크 VNet 관점

다음 그림에서는 허브 가상 네트워크(VNet) 및 스포크 VNet 관점의 네트워크를 보여줍니다(파란색으로 강조 표시). 또한 이 그림은 다양한 네트워크의 ASN(Autonomous System Number) 및 서로 다른 네트워크 간에 교환되는 경로를 보여줍니다. 

![1][1]

VNet의 Azure ExpressRoute 게이트웨이의 ASN이 Microsoft MSEE(Microsoft Enterprise Edge Router)의 ASN과 다른 것을 알 수 있습니다. ExpressRoute 게이트웨이는 프라이빗 ASN(**65515** 의 값)을 사용하며 MSEE는 공용 ASN(**12076** 의 값)을 전역적으로 사용합니다. MSEE는 피어이므로 ExpressRoute 피어링을 구성할 때 피어 ASN으로 **12076** 을 사용합니다. Azure 쪽의 경우 MSEE는 ExpressRoute 게이트웨이를 사용하여 eBGP 피어링을 설정합니다. MSEE가 각 ExpressRoute 피어링에 대해 설정하는 이중 eBGP 피어링은 제어 평면 수준에서 투명합니다. 따라서 ExpressRoute 경로 테이블을 볼 때 VNet의 접두사에 대해 VNet의 ExpressRoute 게이트웨이 ASN이 표시됩니다. 

다음 그림에는 예제 ExpressRoute 경로 테이블을 보여줍니다. 

![5][5]

Azure 내에서 ASN은 피어링 관점에서만 중요합니다. 기본적으로 ExpressRoute 게이트웨이 및 Azure VPN Gateway에서 VPN Gateway의 ASN은 **65515** 입니다.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>ExpressRoute 1을 통한 온-프레미스 위치 1 및 원격 VNet 관점

온-프레미스 위치 1 및 원격 VNet은 모두 ExpressRoute 1을 통해 허브 VNet에 연결됩니다. 다음 다이어그램에 나와 있는 것처럼 동일한 토폴로지 측면을 공유합니다.

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>사이트 간 VPN을 통한 온-프레미스 위치 1 및 분기 VNet 관점

온-프레미스 위치 1과 분기 VNet은 둘 다 사이트 간 VPN 연결을 통해 허브 VNet의 VPN Gateway에 연결됩니다. 다음 다이어그램에 나와 있는 것처럼 동일한 토폴로지 측면을 공유합니다.

![3][3]

## <a name="on-premises-location-2-perspective"></a>온-프레미스 위치 2 관점

온-프레미스 위치 2는 ExpressRoute 2의 프라이빗 피어링을 통해 허브 VNet에 연결됩니다. 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

ExpressRoute Microsoft 피어링을 사용하여 온-프레미스 네트워크와 Azure VNet 간에 개인적으로 데이터를 교환하도록 사이트 간 VPN을 구성할 수 있습니다. 이 구성을 사용하면 비밀성, 신뢰성 및 무결성을 유지하며 데이터를 교환할 수 있습니다. 데이터 교환은 재생 방지이기도 합니다. ExpressRoute Microsoft 피어링을 사용하여 터널 모드에서 사이트 간 IPsec VPN을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 사용하는 사이트 간 VPN 구성에 대한 기본 제한은 처리량입니다. IPsec 터널을 통한 처리량은 VPN Gateway 용량에 의해 제한됩니다. VPN Gateway 처리량은 ExpressRoute 처리량보다 낮습니다. 이 시나리오에서 매우 안전한 트래픽의 경우 IPsec 터널 및 모든 다른 트래픽의 경우 프라이빗 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN

ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 테스트 설정에서 설명한 것처럼, Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 만드는 데 사이트 간 VPN을 사용할 수 있습니다. ExpressRoute 및 사이트 간 VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 ExpressRoute를 우선 순위로 지정합니다. ExpressRoute와 사이트 간 VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 사이트 간 VPN 연결을 사용하기 전에 ExpressRoute 연결을 사용하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN의 공존 연결을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [Azure에서 허브-스포크 네트워크 토폴로지 구현][Hub-n-Spoke]을 참조하세요.

지역 내의 VNet 피어링에서 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

서로 다른 지역에 있는 분기 VNet 및 온-프레미스 네트워크를 허브 VNet을 통해 서로 통신하도록 할 수 있습니다. 이 구성에 대한 네이티브 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안은 허브의 라우팅에 대해 NVA(네트워크 가상 어플라이언스)를 사용하는 것입니다.

자세한 내용은 [VPN Gateway란?][VPN] 및 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

테스트 설정의 [데이터 평면 분석][Data-Analysis] 및 Azure 네트워크 모니터링 기능 보기에 대해 알아봅니다.

다음에 대해 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.
-   ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 개수를 알아봅니다.
-   ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 개수를 알아봅니다.
-   ExpressRoute의 다른 크기 조정 제한 사항에 대해 알아봅니다.


<!--Image References-->
[1]: ./media/backend-interoperability/hubview.png "토폴로지의 허브 및 스포크 VNet 관점"
[2]: ./media/backend-interoperability/loc1exrview.png "ExpressRoute 1을 통한 토폴로지의 위치 1 및 원격 VNet 관점"
[3]: ./media/backend-interoperability/loc1vpnview.png "사이트 간 VPN을 통한 토폴로지의 위치 1 및 분기 VNet 관점"
[4]: ./media/backend-interoperability/loc2view.png "토폴로지의 위치 2 관점"
[5]: ./media/backend-interoperability/exr1-routetable.png "ExpressRoute 1 경로 테이블"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: /azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
