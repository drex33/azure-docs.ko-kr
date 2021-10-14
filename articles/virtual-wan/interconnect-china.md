---
title: '아키텍처: Azure 가상 WAN 및 보안 허브를 사용 하 여 중국과 상호 연결'
description: Azure Virtual WAN 및 보안 허브를 사용하여 중국과 상호 연결하는 방법을 알아봅니다.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 0353cf0c8c98c148ac70121849d510aadc31d131
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000805"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Azure Virtual WAN 및 보안 허브를 사용하여 중국과 상호 연결

일반적인 자동차, 제조, 물류 산업 또는 대사관과 같은 다른 기관을 살펴보면, 중국과의 상호 연결을 개선하는 방법에 대한 질문이 있는 경우가 많습니다. 이러한 개선은 Microsoft 365, Azure Global Services와 같은 클라우드 서비스를 사용하거나 중국 내 지점을 고객 백본과 상호 연결하는 것과 주로 관련이 있습니다.

대부분의 경우 고객은 대기 시간이 길고 대역폭이 낮으며 연결이 불안정하고 중국 외부(예: 유럽 또는 미국)에 연결하는 비용이 높다는 어려움을 겪고 있습니다.

이런 어려움을 겪는 이유는 인터넷에서 중국 부분을 보호하고 중국으로 유입되는 트래픽을 필터링하는 "GFW(Great Firewall of China)" 때문입니다. 홍콩 및 마카오와 같은 특별행정구를 제외하고 중화인민공화국에서 중국 외부로 흐르는 거의 모든 트래픽은 Great Firewall을 통과합니다. 홍콩과 마카오를 통과하는 트래픽은 Great Firewall에 완전히 도달하지는 않고, Great Firewall의 하위 집합에 의해 처리됩니다.

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="다이어그램은 공급자 상호 연결을 보여줍니다.":::

Virtual WAN을 사용하면 중국 사이버 보안법을 위반하지 않고도 고객이 Microsoft 클라우드 서비스와 성능에 맞는 안정적인 연결을 구축하고 자사 엔터프라이즈 네트워크에 연결할 수 있습니다.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>요구 사항 및 워크플로

중국 사이버 보안법을 준수하려면 일련의 특정 조건을 충족해야 합니다.

먼저 중국의 ICP(인터넷 콘텐츠 공급자) 라이선스를 소유한 ISP 및 네트워크와 협력해야 합니다. 대부분의 경우 다음 공급자 중 하나를 사용하게 됩니다.

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

이제 공급자 및 요구 사항에 따라 다음 네트워크 연결 서비스 중 하나를 구입하여 중국 내 지점과 상호 연결해야 합니다.

* MPLS/IPVPN 네트워크 
* SDWAN(Software Defined WAN)
* 전용 인터넷 액세스

다음으로, 베이징이나 상하이가 아닌 홍콩의 Microsoft 글로벌 네트워크 및 경계 네트워크에 대한 브레이크아웃을 제공하기로 해당 공급자와 동의해야 합니다. 이 경우 홍콩은 중국과의 실제 연결 및 위치 때문에 매우 중요합니다.

지도를 보면 싱가포르가 중국에 더 가깝게 보이기 때문에 대부분의 고객은 상호 연결에 싱가포르를 사용하는 것이 최선의 방법이라고 생각하지만 그렇지 않습니다. 네트워크 파이버 맵을 따라가다 보면 거의 모든 네트워크 연결이 베이징, 상하이 및 홍콩을 통과합니다. 때문에 중국과 상호 연결하기에는 홍콩이 더 좋은 위치입니다.

공급자에 따라 서비스 제공 사항이 다를 수 있습니다. 아래 표는 이 문서가 작성된 시점의 정보를 기반으로 공급자 및 공급자가 제공하는 서비스 예를 보여줍니다.

| 서비스 | 공급자 예제 |
| --- | --- |
| MPLS/IPVPN 네트워크 |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| 전용 인터넷 액세스 | PCCW, Hong Kong Telecom, China Mobil|

다음 두 가지 솔루션 중 Microsoft 글로벌 백본에 도달하는 데 사용할 솔루션을 공급자와 합의할 수 있습니다.

* Microsoft Azure ExpressRoute 받기가 홍콩에서 종료되었습니다. MPLS/IPVPN을 사용하는 경우도 마찬가지입니다. 현재 홍콩에 대한 ExpressRoute를 보유한 유일한 ICP 라이선스 공급자는 China Telecom Global뿐입니다. 하지만 Megaport 또는 InterCloud와 같은 Cloud Exchange 공급자를 활용하는 다른 공급자와 논의할 수도 있습니다. 자세한 내용은 [ExpressRoute 연결 공급자](../expressroute/expressroute-locations-providers.md#partners)를 참조하세요.

* 다음 Internet Exchange Point 중 하나에서 직접 인터넷 전용 액세스를 사용하거나 개인 네트워크 상호 연결을 사용합니다.

다음 목록은 홍콩에서 가능한 Internet Exchange를 보여줍니다.

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

이 연결을 사용할 때 Microsoft Services에 대한 다음 BGP 홉은 Microsoft AS #(Autonomous System Number) 8075여야 합니다. 단일 위치 또는 SDWAN 솔루션을 사용하는 경우 해당 연결을 선택할 수 있습니다.

중국 및 홍콩 특별행정구 간의 상호 연결과 관련된 현재 변경 내용으로 대부분의 네트워크 공급자는 중국 및 홍콩 특별행정구 간에 MPLS 브리지를 구축합니다.

중국 내의 사이트간 VPN 연결이 허용되고 대부분 안정적임을 알 수 있습니다. 다른 지역의 분기 간 사이트 간 연결에도 마찬가지입니다. 이제 공급자는 양쪽에 VPN/SDWAN 집계를 만들고 둘 사이의 MPLS를 통해 브리지를 만듭니다.

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="다이어그램은 중국 MPLS 브리지를 보여줍니다.":::

어느 쪽이든, 중국에 대한 두 번째 및 정규 인터넷 브레이크아웃을 두는 것이 좋습니다. 기업 간의 트래픽을 Microsoft 365 및 Azure와 같은 클라우드 서비스와 법률에 의해 규제되는 인터넷 트래픽으로 분할하기 위해서입니다.

중국 내 규격 네트워크 아키텍처는 다음 예와 같은 모양입니다.

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="다이어그램은 여러 분기를 보여줍니다.":::

이 예에서는 홍콩에 Microsoft 글로벌 네트워크와 상호 연결이 있으며, 중국 외부의 지점 및 데이터 센터에 상호 연결하고 서비스를 소비하기 위해 [Azure Virtual WAN 글로벌 전송 아키텍처](virtual-wan-global-transit-network-architecture.md) 및 Azure 보안 Virtual WAN 허브와 같은 추가 서비스를 활용할 수 있습니다.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>허브-허브 통신

이 섹션에서는 Virtual WAN 허브-허브 통신을 사용하여 상호 연결합니다. 이 시나리오에서는 새 Virtual WAN 허브 리소스를 만들어서 홍콩, 원하는 다른 지역, Azure 리소스가 이미 있거나 연결하려는 지역의 Virtual WAN 허브에 연결합니다.

샘플 아키텍처는 다음 예제와 같은 모양입니다.

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="다이어그램은 샘플 WAN을 보여줍니다.":::

이 예제에 중국 지점은 VPN 또는 MPLS 연결을 사용하여 서로 연결하거나 Azure Cloud 중국에 연결합니다. 글로벌 서비스에 연결해야 하는 지점은 홍콩에 직접 연결된 MPLS 또는 인터넷 기반 서비스를 사용합니다. 홍콩 및 기타 지역에서 ExpressRoute를 사용하려면 ExpressRoute 회로를 서로 연결하여 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md)를 구성해야 합니다.

일부 지역에서는 ExpressRoute Global Reach를 사용할 수 없습니다. 예를 들어 브라질 또는 인도와 상호 연결해야 하는 경우, [Cloud Exchange 공급자](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)를 활용하여 라우팅 서비스를 제공해야 합니다.

아래 그림은 이 시나리오에 대한 두 가지 예제를 보여줍니다.

:::image type="content" source="./media/interconnect-china/global.png" alt-text="다이어그램은 Global Reach를 보여줍니다.":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Microsoft 365에 대한 안전한 인터넷 브레이크아웃

또 다른 고려 사항은 네트워크 보안 및 중국과 Virtual WAN이 설정된 백본 구성 요소 및 고객 백본 간의 진입 점에 대한 로깅입니다. 대부분의 경우 Microsoft Edge 네트워크 및 Microsoft 365 서비스에 사용되는 Azure Front Door 서버에 직접 연결하려면 홍콩에 있는 인터넷으로 나갈 필요가 있습니다.

Virtual WAN을 사용하는 두 시나리오 모두 [Azure Virtual WAN 보안 허브](../firewall-manager/secured-virtual-hub.md)를 활용합니다. Azure Firewall Manager를 사용하면 일반 Virtual WAN 허브를 보안 허브로 변경한 다음, 이 허브 내에서 Azure Firewall을 배포하고 관리할 수 있습니다.

다음 그림은 이러한 시나리오의 예를 보여 줍니다.

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="다이어그램은 웹 및 Microsoft 서비스 트래픽을 위한 인터넷 브레이크아웃을 보여줍니다.":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>아키텍처 및 트래픽 흐름

홍콩에 대한 연결과 관련된 선택에 따라 전체 아키텍처가 약간 변경될 수 있습니다. 이 섹션에서는 VPN 또는 SDWAN 및/또는 ExpressRoute를 다르게 조합하여 사용할 수 있는 3가지 아키텍처를 보여줍니다.

이러한 모든 옵션은 Azure Virtual WAN 보안 허브를 활용하여 홍콩의 Microsoft 365에 직접 연결합니다. 이러한 아키텍처는 [Microsoft 365 다중 지역](/microsoft-365/enterprise/microsoft-365-multi-geo)에 대한 규정 준수 요구 사항을 지원하며 해당 트래픽을 다음 Azure Front Door 위치 근처에 유지합니다. 따라서 중국 이외의 지역에서 Microsoft 365 사용을 위한 개선 사항이기도 합니다.

인터넷 연결에 Azure Virtual WAN을 사용하면 모든 연결에서 [MAPS(Microsoft Azure Peering Services)](../peering-service/about.md)와 같은 추가 서비스를 활용할 수 있습니다. MAPS는 타사 인터넷 서비스 공급자로부터 Microsoft 글로벌 네트워크로 들어오는 트래픽을 최적화하기 위해 생성되었습니다.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>옵션 1: SDWAN 또는 VPN

이 섹션에서는 홍콩 및 기타 지점에 대해 SDWAN 또는 VPN을 사용하는 디자인을 설명합니다. 이 옵션은 Virtual WAN 백본의 두 사이트에서 순수한 인터넷 연결을 사용하는 경우 트래픽 흐름과 사용을 보여줍니다. 이 경우 전용 인터넷 액세스 또는 ICP 공급자 SDWAN 솔루션을 사용하여 홍콩으로 연결됩니다. 다른 지점에서도 순수한 인터넷 또는 SDWAN 솔루션을 사용합니다.

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="다이어그램은 중국과 홍콩 간 트래픽을 보여줍니다.":::

이 아키텍처에서 모든 사이트는 VPN 및 Azure Virtual WAN을 사용하여 Microsoft 글로벌 네트워크에 연결됩니다. 사이트와 홍콩 사이의 트래픽은 Microsoft 네트워크를 통해 전송되며 마지막에는 일반 인터넷 연결만 사용됩니다.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>옵션 2: ExpressRoute 및 SDWAN 또는 VPN

이 섹션에서는 홍콩에서 ExpressRoute를 사용하고 다른 지점은 VPN/SDWAN 분기를 사용하는 디자인을 설명합니다. 이 옵션은 홍콩에서 ExpressRoute 사용과 종료 및 SDWAN 또는 VPN을 통해 연결된 다른 지점을 보여줍니다. 홍콩의 ExpressRoute는 현재 간단한 공급자 목록으로 제한되어 있으며 [Express Route 파트너](../expressroute/expressroute-locations-providers.md#global-commercial-azure) 목록에서 찾을 수 있습니다.

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="중국과 홍콩 간 트래픽을 보여주는 다이어그램 - ExpressRoute.":::

예를 들어 대한민국 또는 일본에서 중국의 ExpressRoute를 종료하는 옵션도 있습니다. 하지만 규정 준수, 규제 및 대기 시간을 고려하면 현재는 홍콩이 최선의 선택입니다.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>옵션 3: ExpressRoute 전용

이 섹션에서는 ExpressRoute가 홍콩 및 기타 지점에 사용되는 디자인을 설명합니다. 이 옵션은 양쪽 끝에서 ExpressRoute를 사용하는 상호 연결을 보여줍니다. 여기는 트래픽 흐름이 다른 곳과 다릅니다. Microsoft 365 트래픽이 Azure 가상 WAN 보안 허브로 흐르고 거기에서 Microsoft Edge 네트워크 및 인터넷으로 흐릅니다.

상호 연결된 지점으로 이동하거나 이 지점에서 중국 내 위치로 이동하는 트래픽은 해당 아키텍처 내에서 다른 접근 방식을 따릅니다. 현재 가상 WAN은 ExpressRoute 간의 전송을 지원하지 않습니다. 트래픽은 가상 WAN 허브를 통과하지 않고 ExpressRoute Global Reach 또는 타사 상호 연결을 활용합니다. 한 MSEE(Microsoft Enterprise Edge)에서 다른 MSEE로 직접 흐릅니다.

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="다이어그램은 ExpressRoute Global Reach를 보여줍니다.":::

현재 일부 국가/지역에서는 ExpressRoute Global Reach를 사용할 수 없지만 Azure Virtual WAN을 사용하여 솔루션을 구성할 수 있습니다.

예를 들어 Microsoft 피어링을 사용하여 ExpressRoute를 구성하고 이 피어링을 통해 VPN 터널을 Azure Virtual WAN에 연결할 수 있습니다. 이제 Global Reach 및 Megaport Cloud와 같은 타사 공급자와 서비스 없이 다시 VPN과 ExpressRoute 간의 전송을 사용하도록 설정했습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

* [Azure Virtual WAN을 사용한 글로벌 전송 네트워크 아키텍처](virtual-wan-global-transit-network-architecture.md)

* [Virtual WAN 허브 만들기](virtual-wan-site-to-site-portal.md)

* [Virtual WAN 보안 허브 구성](../firewall-manager/secure-cloud-network.md)

* [Azure Peering Service 미리 보기 개요](../peering-service/about.md)