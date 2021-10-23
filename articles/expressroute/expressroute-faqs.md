---
title: FAQ - Azure ExpressRoute | Microsoft Docs
description: ExpressRoute FAQ는 Azure 서비스, 비용, 데이터 및 연결, SLA, 공급자 및 위치, 대역폭 및 기타 기술 세부 정보에 대한 정보를 포함합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: a9402ac0a69cff3cd2b420754aceeb452811e1a1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246778"
---
# <a name="expressroute-faq"></a>ExpressRoute FAQ

## <a name="what-is-expressroute"></a>ExpressRoute란?

ExpressRoute는 온-프레미스 또는 공동 장소 환경의 Microsoft 데이터 센터와 인프라 사이에 프라이빗 연결을 만들 수 있게 해 주는 서비스입니다. ExpressRoute 연결은 퍼블릭 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 보안성과 안정성이 뛰어나며 대기 시간이 짧아 속도가 빠릅니다.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute 및 프라이빗 네트워크 연결을 사용할 경우 이점은 무엇인가요?

ExpressRoute 연결은 퍼블릭 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반적인 연결보다 더 짧고 일관된 대기 시간으로 더 높은 보안, 안정성 및 속도를 제공합니다. 경우에 따라 온-프레미스 디바이스와 Azure 간 데이터 전송에 ExpressRoute 연결을 사용하면 상당한 비용 혜택을 얻을 수 있습니다.

### <a name="where-is-the-service-available"></a>서비스를 사용할 수 있는 곳은 어디인가요?

서비스 위치 및 가용성은 이 페이지를 참조하세요. [ExpressRoute 파트너 및 위치](expressroute-locations.md)

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute 통신업체 중 한 곳과 파트너십을 맺지 않았는데 ExpressRoute를 사용하여 Microsoft에 연결할 수 있는 방법이 있나요?

지역 통신 업체를 선택하고 지원되는 exchange 공급자 위치 중 하나에 이더넷 연결을 제공할 수 있습니다. 그러면 공급자 위치에서 Microsoft와 피어링할 수 있습니다. [ 파트너 및 위치](expressroute-locations.md)의 마지막 섹션을 검사하여 서비스 공급자가 Exchange 위치 중 하나에 있는지 확인합니다. 그런 다음 Azure에 연결하려면 서비스 공급자를 통해 ExpressRoute 회로를 주문할 수 있습니다.

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute 비용

가격 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 를 참조하세요.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>지정된 대역폭의 ExpressRoute 회로에 대한 요금을 지불하는 경우 이 대역폭이 수신 및 송신 트래픽에 별도로 할당되나요?

예, ExpressRoute 회로 대역폭은 이중입니다. 예를 들어 200mbps ExpressRoute 회로를 구매하는 경우, 수신 트래픽용 200mbps 및 송신 트래픽용 200mbps가 확보됩니다.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>지정된 대역폭의 ExpressRoute 회로에 대한 비용을 지불하는 경우, 네트워크 서비스 공급자로부터 구입한 프라이빗 연결은 동일한 속도여야 하나요?

아니요. 서비스 공급자로부터 모든 속도의 프라이빗 연결을 구입할 수 있습니다. 그러나 Azure에 대한 연결은 구입한 ExpressRoute 회로 대역폭으로 제한됩니다.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>지정된 대역폭의 ExpressRoute 회로에 대한 요금을 지불하는 경우 내가 확보한 대역폭을 초과하는 용량을 사용할 수 있나요?

예, ExpressRoute 회로의 보조 연결에서 사용할 수 있는 대역폭을 사용하여 확보한 대역폭 제한의 최대 2배를 사용할 수 있습니다. 회로의 기본 제공 중복은 두 개의 MSEE(Microsoft Enterprise Edge) 라우터에 대한 기본 및 보조 연결(확보한 각 대역폭)을 사용하여 구성됩니다. 필요한 경우 보조 연결을 통해 사용할 수 있는 대역폭을 추가 트래픽에 사용할 수 있습니다. 그러나 보조 연결은 중복을 위한 것이므로 보장되지 않으며 지속적으로 추가 트래픽에 사용해서는 안 됩니다. 두 연결을 사용하여 트래픽을 전송하는 방법에 대한 자세한 내용은 [AS PATH 접두사 사용](./expressroute-optimize-routing.md#solution-use-as-path-prepending)을 참조하세요.

트래픽을 전송할 때 기본 연결만 사용하려는 경우에는 연결 대역폭이 고정되고 이를 초과 구독하려고 하면 패킷 삭제가 늘어납니다. 트래픽이 ExpressRoute Gateway를 지나가는 경우 게이트웨이 SKU에 대한 대역폭이 고정되고 확장되지 않습니다. 각 게이트웨이 SKU의 대역폭은 [ExpressRoute 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md#aggthroughput)를 참조하세요.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>가상 네트워크 및 다른 Azure 서비스와 동일한 프라이빗 네트워크 연결을 동시에 사용할 수 있나요?

예. ExpressRoute 회로는 일단 설정되면 가상 네트워크 내 서비스와 다른 Azure 서비스에 동시에 액세스할 수 있습니다. 프라이빗 피어링 경로를 통해 가상 네트워크에 연결하고, Microsoft 피어링 경로를 통해 다른 서비스에 연결합니다.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>VNet은 ExpressRoute 개인 피어링에서 어떻게 보급되나요?

ExpressRoute 게이트웨이는 Azure VNet의 *주소 공간* 을 보급하며, 서브넷 수준에서 포함/제외할 수 없습니다. 항상 보급된 VNet 주소 공간입니다. 또한 VNet 피어링을 사용하고 피어링된 VNet에 "원격 게이트웨이 사용"을 설정하면 피어링된 VNet의 주소 공간도 보급됩니다.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>ExpressRoute 개인 피어링의 VNet에서 온-프레미스로 보급할 수 있는 접두사는 몇 개입니까?

단일 ExpressRoute 연결에서 또는 게이트웨이 전송을 사용하는 VNet 피어링을 통해 보급되는 IPv4 접두사는 최대 1000개입니다. 예를 들어 ExpressRoute 회로에 연결된 단일 VNet에 999개의 주소 공간이 있는 경우 해당 접두사의 모든 999개 주소 공간은 온-프레미스에 보급됩니다. 또는 "원격 게이트웨이 허용" 옵션을 사용하여 1개의 주소 공간과 500개의 스포크 Vnet이 설정된 게이트웨이 전송을 VNet에서 허용하도록 설정한 경우 이 게이트웨이를 사용하여 배포된 VNet은 501개의 접두사를 온-프레미스에 보급합니다.

이중 스택 회로를 사용하는 경우 단일 ExpressRoute 연결 또는 게이트웨이 전송을 사용하는 VNet 피어링을 통해 최대 100개의 IPv6 접두사가 있습니다. 이는 위에서 설명한 제한에 추가됩니다.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>ExpressRoute 연결에서 접두사 제한을 초과하면 어떻게 되나요?

ExpressRoute 회로와 게이트웨이(그리고 해당하는 경우 게이트웨이 전송을 사용하여 피어링된 VNet) 간의 연결이 중단됩니다. 접두사 제한을 더 이상 초과하지 않으면 다시 설정됩니다.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>온-프레미스 네트워크에서 오는 경로를 필터링할 수 있나요?

경로를 필터링/포함하는 유일한 방법은 온-프레미스 에지 라우터에 있습니다. VNet에서 사용자 정의 경로를 추가하여 특정 라우팅에 영향을 줄 수 있지만 이는 정적이며, BGP 보급의 일부가 아닙니다.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute는 SLA(서비스 수준 약정)를 제공하나요?

자세한 내용은 [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) 페이지를 참조하세요.

## <a name="supported-services"></a>지원되는 서비스

ExpressRoute는 다양한 서비스 유형에 개인 피어링, Microsoft 피어링 및 공용 피어링(사용되지 않음) 같은 [3개의 라우팅 도메인](expressroute-circuit-peerings.md)을 지원합니다.

### <a name="private-peering"></a>프라이빗 피어링

**지원됨:**

* 모든 가상 머신 및 클라우드 서비스를 포함한 가상 네트워크

### <a name="microsoft-peering"></a>Microsoft 피어링

Azure Microsoft 피어링에 ExpressRoute 회로를 사용하도록 설정하면 회로를 통해 Azure에서 사용되는 [공용 IP 주소 범위](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses)에 액세스할 수 있습니다. Azure Microsoft 피어링은 현재 Azure에서 호스팅되는 서비스에 대한 액세스를 제공합니다(회로의 SKU에 따라 지리적 제한 적용). 특정 서비스에 대한 가용성의 유효성을 검사하려면 해당 서비스에 대한 설명서를 보고 해당 서비스에 예약된 범위가 게시되었는지 확인합니다. 그런 다음, 대상 서비스의 IP 범위를 조회하고 [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드 XML 파일](https://www.microsoft.com/download/details.aspx?id=56519)에 나열된 범위와 비교합니다. 또는 확인을 위해 해당 서비스의 지원 티켓을 열 수 있습니다.

**지원됨:**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI - Azure 지역 커뮤니티를 통해 사용할 수 있습니다. Power BI 테넌트의 지역을 확인하는 방법은 [여기](/power-bi/service-admin-where-is-my-tenant-located)를 참조하세요.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/)(Azure 글로벌 서비스 커뮤니티)
* IaaS(Virtual Machines, Virtual Network Gateway, 부하 분산 장치 등)용 Azure 공용 IP 주소  
* 대부분의 다른 Azure 서비스도 지원됩니다. 사용하려는 서비스의 지원 유무를 직접 확인하세요.

**지원되지 않음:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-factor Authentication 서버(레거시)
* Traffic Manager
* Logic Apps

### <a name="public-peering"></a>공용 피어링

공용 피어링이 새 ExpressRoute 회로에서 사용되지 않도록 설정되었습니다. 이제 Azure 서비스를 Microsoft 피어링에서 사용할 수 있습니다. 공용 피어 링이 더 이상 사용 되지 않는 회로를 사용 하는 경우 원하는 서비스에 따라 Microsoft 피어 링 또는 공용 피어 링을 사용 하도록 선택할 수 있습니다.

공용 피어링에 대한 자세한 내용 및 구성 단계는 [ExpressRoute 공용 피어링](about-public-peering.md)을 참조하세요.

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Microsoft 피어링을 구성하는 동안 '보급된 공용 접두사' 상태가 '유효성 검사 필요'로 표시되는 이유는 무엇인가요?

Microsoft는 인터넷 라우팅 레지스트리에서 지정된 '보급된 공용 접두사' 및 '피어 ASN'(또는 '고객 ASN')이 사용자에게 할당되었는지 확인합니다. 다른 엔터티에서 공용 접두사를 가져오는 경우, 라우팅 레지스트리에 할당이 기록되지 않은 경우 자동 유효성 검사가 완료되지 않으며 수동 유효성 검사가 필요합니다. 자동 유효성 검사에 실패하면 '유효성 검사 필요' 메시지가 표시됩니다.

'유효성 검사 필요' 메시지가 표시되면 라우팅 레지스트리에서 접두사의 소유자로 표시되는 엔터티가 조직에 할당한 공용 접두사를 보여 주는 문서를 수집하고, 아래와 같이 지원 티켓을 열어 수동 유효성 검사를 위해 이러한 문서를 제출합니다.

!["공용 접두사에 대한 소유권 증명"을 위한 새 지원 요청(지원 티켓)을 보여 주는 스크린샷.](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>ExpressRoute에서 Dynamics 365가 지원되나요?

Dynamics 365 및 CDS(Common Data Service) 환경은 Azure에서 호스팅되므로 고객은 Azure 리소스에 대한 기본 ExpressRoute 지원을 활용할 수 있습니다. 라우터 필터에 Dynamics 365/CDS 환경이 호스팅되는 Azure 지역이 포함된 경우 해당 서비스 엔드포인트에 연결할 수 있습니다.

> [!NOTE]
> ExpressRoute 회로를 동일한 [지정학적 지역](./expressroute-locations-providers.md#expressroute-locations) 내에 배포하는 경우 Azure ExpressRoute를 통한 Dynamics 365 연결에 [ExpressRoute Premium](#expressroute-premium)이 필요하지 **않습니다**.

## <a name="data-and-connections"></a>데이터 및 연결

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute를 사용하여 전송할 수 있는 데이터의 양에 제한이 있나요?

데이터 전송 크기에 대한 제한을 설정하지 않습니다. 대역폭 요금에 대한 정보는 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 을 참조하세요.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute에서 지원되는 연결 속도는 무엇인가요?

지원되는 대역폭은 다음을 제공합니다.

50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 2Gbps, 5Gbps, 10Gbps

### <a name="which-service-providers-are-available"></a>어떤 서비스 공급자를 사용할 수 있나요?

서비스 공급자 및 위치 목록은 [ExpressRoute 파트너 및 위치](expressroute-locations.md) 를 참조하세요.

## <a name="technical-details"></a>기술 세부 정보

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>내 온-프레미스 위치를 Azure에 연결하기 위한 기술 요구 사항은 무엇인가요?

요구 사항은 [ExpressRoute 필수 구성 요소 페이지](expressroute-prerequisites.md)를 참조하세요.

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute에 대한 연결이 중복되나요?

예. 각 ExpressRoute 회로에는 고가용성을 제공하도록 구성된 중복 쌍의 교차연결이 있습니다.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>내 ExpressRoute 링크 중 하나가 실패하면 연결이 끊어지나요?

교차 연결 중 하나가 실패할 경우 연결이 손실되지 않습니다. 네트워크의 부하를 지원하고 ExpressRoute 회로의 고가용성을 제공하기 위해 중복 연결을 사용할 수 있습니다. 회로 수준 복원력을 얻기 위해 다른 피어링 위치에 추가로 회로를 만들 수 있습니다.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>개인 피어링에 대한 중복성을 구현하려면 어떻게 해야 하나요?

단일 회로를 사용할 수 없게 되는 경우에 대비해 서로 다른 피어링 위치의 여러 ExpressRoute 회로 또는 동일한 피어링 위치의 최대 4개 연결을 동일한 가상 네트워크에 연결하여 고가용성을 제공할 수 있습니다. 그런 다음, 특정 회로를 선호하도록 로컬 연결 중 하나에 [더 높은 가중치를 할당](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection)할 수 있습니다. 고객은 단일 실패 지점이 발생하지 않도록 두 개 이상의 ExpressRoute 회로를 설치하는 것이 좋습니다. 

고가용성을 설계하는 방법은 [여기](./designing-for-high-availability-with-expressroute.md)를 참조하고 재해 복구를 설계하는 방법은 [여기](./designing-for-disaster-recovery-with-expressroute-privatepeering.md)를 참조하세요.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Microsoft 피어링에 중복성을 구현하는 방법은 무엇인가요?

Microsoft 피어링을 사용하여 Azure Storage 또는 Azure SQL 같은 Azure 공용 서비스에 액세스하거나 Microsoft 365에 Microsoft 피어링을 사용하는 고객은 단일 실패 지점을 방지하기 위해 서로 다른 피어링 위치에 여러 회로를 구현하는 것이 좋습니다. 고객은 두 회로에 동일한 접두사를 보급하고 [AS PATH 접두사](./expressroute-optimize-routing.md#solution-use-as-path-prepending)를 사용하거나 다른 접두사를 보급하여 온-프레미스의 경로를 확인할 수 있습니다.

고가용성을 위한 설계에 대한 내용은 [여기](./designing-for-high-availability-with-expressroute.md)를 참조하세요.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>ExpressRoute에 연결된 가상 네트워크에서 고가용성을 보장하려면 어떻게 해야 하나요?

동일한 피어링 위치에 있는 최대 4개의 ExpressRoute 회로를 가상 네트워크에 연결하거나 서로 다른 피어링 위치(예: 싱가포르, 싱가포르 2)에 있는 최대 16개의 ExpressRoute 회로를 가상 네트워크에 연결하여 고가용성을 구현할 수 있습니다. 한 ExpressRoute 회로가 다운되면 연결이 다른 ExpressRoute 회로로 장애 조치(Failover)됩니다. 기본적으로 가상 네트워크에서 나오는 트래픽은 ECMP(동일 비용 다중 경로 라우팅)를 기준으로 라우팅됩니다. 연결 가중치를 사용하여 특정 회로를 기본 회로로 사용할 수 있습니다. 자세한 내용은 [ExpressRoute 라우팅 최적화](expressroute-optimize-routing.md)를 참조하세요.

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Microsoft 피어링 또는 공용 피어링의 Azure Storage 및 Azure SQL 같은 Azure 공용 서비스로 향하는 트래픽이 ExpressRoute 경로에서 선호되는지 확인하려면 어떻게 해야 하나요?

온-프레미스에서 Azure로 가는 경로가 항상 ExpressRoute 회로에서 선호되도록 하려면 라우터에 *로컬 기본 설정* 특성을 구현해야 합니다.

BGP 경로 선택 및 공통 라우터 구성에 대한 자세한 내용은 [여기](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings)의 추가 정보를 참조하세요. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>클라우드 교환에 공동 배치되지 않았으며 서비스 공급자가 점 대 점 연결을 공급하는 경우 온-프레미스 네트워크와 Microsoft 간에 두 개의 실제 연결을 주문해야 하나요?

서비스 공급자가 실제 연결을 통해 두 개의 이더넷 가상 회로를 설정할 수 있으면 하나의 실제 연결만 필요합니다. 실제 연결(예: 광섬유)은 레이어 1(L1) 디바이스에서 종료됩니다(이미지 참조). 두 개의 이더넷 가상 회로에는 서로 다른 VLAN ID로 태그가 지정되는데, 하나는 기본 회로용이고 다른 하나는 보조 회로용입니다. 해당 VLAN ID는 외부 802.1Q 이더넷 헤더에 있습니다. 내부 802.1Q 이더넷 헤더(표시되지 않음)는 특정 [ExpressRoute 라우팅 도메인](expressroute-circuit-peerings.md)에 매핑됩니다.

![고객 사이트와 ExpressRoute 위치의 스위치 사이에 물리적 연결을 구성하는 L1(레이어 1) 기본 및 보조 가상 회로를 요약한 다이어그램.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>ExpressRoute를 사용하여 Azure에 대한 내 VLAN 중 하나를 확장할 수 있나요?

아니요. Azure까지의 계층 2 연결 확장을 지원하지 않습니다.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>내 구독에 둘 이상의 ExpressRoute 회로가 있을 수 있나요?

예. 사용자 구독에 둘 이상의 ExpressRoute 회로가 있을 수 있습니다. 기본 제한은 10으로 설정되어 있습니다. 필요한 경우 Microsoft 지원에 문의하여 제한을 늘릴 수 있습니다.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>다른 서비스 공급자의 ExpressRoute 회로를 사용할 수 있나요?

예. 여러 서비스 공급자의 ExpressRoute 회로가 있을 수 있습니다. 각 ExpressRoute 회로마다 하나의 서비스 공급자와만 연결됩니다. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>동일한 메트로에 두 ExpressRoute 피어링 위치(예: Singapore 및 Singapore2)가 있습니다. 내 ExpressRoute 회로를 어느 피어링 위치에 만들어야 합니까?
서비스 공급자가 두 사이트 모두에서 ExpressRoute를 제공할 경우 공급자와 함께 ExpressRoute를 설정할 사이트를 선택할 수 있습니다. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>같은 메트로에 여러 ExpressRoute 회로를 포함할 수 있나요? 동일한 가상 네트워크에 연결할 수 있나요?

예. 같거나 다른 서비스 공급자로 여러 ExpressRoute 회로를 포함할 수 있습니다. 메트로에 여러 개의 ExpressRoute 피어링 위치가 있고 다른 피어링 위치에 회로가 만들어지는 경우 이러한 회로를 동일한 가상 네트워크에 연결할 수 있습니다. 회로가 동일한 피어링 위치에 생성되면 동일한 가상 네트워크에 최대 4개의 회로를 연결할 수 있습니다.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>가상 네트워크를 ExpressRoute 회로에 연결하려면 어떻게 해야 하나요?

기본 단계는 다음과 같습니다.

* ExpressRoute 회로를 설정하고 서비스 공급자가 이를 사용하도록 설정합니다.
* 사용자 또는 공급자가 BGP 피어링을 구성해야 합니다.
* 가상 네트워크를 ExpressRoute 회로에 연결합니다.

자세한 내용은 [회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>내 ExpressRoute 회로에 대한 연결 경계가 있나요?

예. [ExpressRoute 파트너 및 위치](expressroute-locations.md) 문서에서 ExpressRoute 회로에 대한 연결 경계를 간략하게 설명하고 있습니다. ExpressRoute 회로에 대한 연결은 단일 지역으로 제한됩니다. 연결은 ExpressRoute 프리미엄 기능을 사용하여 지역을 교차하도록 확장할 수 있습니다.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>둘 이상의 가상 네트워크를 ExpressRoute 회로에 연결할 수 있나요?

예. 표준 ExpressRoute 회로에는 가상 네트워크 연결을 최대 10개, [프리미엄 ExpressRoute 회로](#expressroute-premium)에는 최대 100개를 포함할 수 있습니다. 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>가상 네트워크를 포함하는 여러 Azure 구독을 가지고 있습니다. 개별 구독에 속한 가상 네트워크를 단일 ExpressRoute 회로에 연결할 수 있나요?

예. 회로와 동일한 구독 또는 단일 ExpressRoute 회로를 사용하는 서로 다른 구독에서 최대 10개 가상 네트워크를 연결할 수 있습니다. ExpressRoute 프리미엄 기능을 사용하여 이 한도를 늘릴 수 있습니다. 전용 회로의 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.

자세한 내용은 [여러 구독에서 ExpressRoute 회로 공유](expressroute-howto-linkvnet-arm.md)를 참조하세요.

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>다른 Azure Active Directory 테넌트 또는 기업계약 등록에 연결된 여러 Azure 구독이 있습니다. 별도 테넌트 및 등록에 속하는 가상 네트워크를 동일한 테넌트 또는 등록에 없는 단일 ExpressRoute 회로에 연결할 수 있나요?

예. ExpressRoute 인증은 추가 구성 없이도 구독, 테넌트 및 등록 간에 적용될 수 있습니다. 전용 회로의 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.

자세한 내용은 [여러 구독에서 ExpressRoute 회로 공유](expressroute-howto-linkvnet-arm.md)를 참조하세요.

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>가상 네트워크가 서로 격리된 동일한 회로에 연결되어 있나요?

아니요. 라우팅 관점에서 동일한 ExpressRoute 회로에 연결된 모든 가상 네트워크는 동일한 라우팅 도메인의 일부이며 서로 격리되지 않습니다. 경로 격리가 필요하면 별도의 ExpressRoute 회로를 만들어야 합니다.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>하나의 가상 네트워크를 둘 이상의 ExpressRoute 회로에 연결할 수 있나요?

예. 단일 가상 네트워크를 동일한 위치에 있는 최대 4개의 ExpressRoute 회로 또는 다른 피어링 위치에 있는 최대 16개의 ExpressRoute 회로와 연결할 수 있습니다. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 회로에 연결된 가상 네트워크에서 인터넷에 액세스할 수 있나요?

예. BGP 세션을 통해 기본 경로(0.0.0.0/0) 또는 인터넷 경로 접두사를 보급하지 않았다면 ExpressRoute 회로에 연결된 가상 네트워크에서 인터넷에 연결할 수 있습니다.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 회로에 연결된 가상 네트워크에 대한 인터넷 연결을 차단할 수 있나요?

예. 기본 경로(0.0.0.0/0)를 보급하여 가상 네트워크 내에 배포된 가상 머신에 대한 모든 인터넷 연결을 차단하고 ExpressRoute 회로를 통해 모든 트래픽을 라우팅할 수 있습니다.

> [!NOTE]
> 서비스 중단 또는 잘못된 구성 등으로 인해 보급된 경로에서 보급된 경로 0.0.0.0/0이 철회되면 Azure는 인터넷 연결을 제공하기 위해 연결된 가상 네트워크의 리소스에 대한 [시스템 경로](../virtual-network/virtual-networks-udr-overview.md#system-routes)를 제공합니다.  인터넷으로 나가는 송신 트래픽이 차단되도록 하려면 인터넷 트래픽에 대한 아웃바운드 거부 규칙을 사용하여 모든 서브넷에 네트워크 보안 그룹을 배치하는 것이 좋습니다.

기본 경로를 보급하는 경우 Microsoft 피어링(예: Azure storage 및 SQL DB)을 통해 프레미스에 다시 제공된 서비스로 트래픽을 수행합니다. Microsoft 피어링 경로 또는 인터넷을 통해 트래픽을 Azure로 반환하도록 라우터를 구성해야 합니다. 서비스에 대해 서비스 엔드포인트를 사용하도록 설정한 경우 서비스에 대한 트래픽은 프레미스에 강제로 전송되지 않습니다. 트래픽은 Azure 백본 네트워크 내에 유지됩니다. 서비스 엔드포인트에 대한 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)를 참조하세요.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>동일한 ExpressRoute 회로에 연결된 가상 네트워크가 서로 통신할 수 있나요?

예. 동일한 ExpressRoute 회로에 연결된 가상 네트워크에 배포된 가상 머신은 서로 통신할 수 있습니다. 이러한 통신을 용이하게 하려면 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 설정하는 것이 좋습니다.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>ExpressRoute와 함께 내 가상 네트워크에 대한 사이트 간 VPN 연결을 설정할 수 있나요?

예. ExpressRoute는 사이트 간 VPN과 공존할 수 있습니다. [ExpressRoute 및 사이트 간 공존 연결 구성](expressroute-howto-coexist-resource-manager.md)을 참조하세요.

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>사이트 간 VPN 연결과 내 ExpressRoute 간의 라우팅을 사용으로 설정하려면 어떻게 할까요?

ExpressRoute에 연결된 분기와 사이트 간 VPN 연결에 연결된 분기 간의 라우팅을 사용하려면 [Azure Route Server](../route-server/expressroute-vpn-support.md)를 설정해야 합니다.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>가상 네트워크에서 ExpressRoute 게이트웨이와 연결된 공용 IP 주소가 있는 이유는 무엇인가요?

공용 IP 주소는 내부 관리용으로만 사용되며, 가상 네트워크의 보안 노출을 구성하지 않습니다.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>보급할 수 있는 경로의 수에 제한이 있나요?

예. 프라이빗 피어링에 대해 최대 4000개의 경로 접두사를 허용하고, Microsoft 피어링에 대해 200개의 경로 접두사를 허용합니다. ExpressRoute 프리미엄 기능을 사용하도록 설정하면 프라이빗 피어링에 대해 10,000개의 경로까지 늘릴 수 있습니다.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP 세션을 통해 보급할 수 있는 IP 범위에 제한 사항이 있나요?

Microsoft 피어링 BGP 세션에 대해 프라이빗 접두사(RFC1918)는 허용하지 않습니다. Microsoft와 개인 피어링 모두에서 모든 접두사 크기(최대 32자)가 허용됩니다.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP 제한을 초과하면 어떻게 되나요?

BGP 세션이 삭제됩니다. 접두사 개수가 제한보다 적으면 재설정됩니다.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP 확보 시간이란 무엇인가요? 조정할 수 있나요?

확보 시간은 180입니다. 연결 유지 메시지는 60초마다 전송됩니다. 이 설정은 Microsoft 쪽에서 고정된 설정으로, 변경할 수 없습니다. 다른 타이머를 구성 할 수 있으며, 이에 따라 BGP 세션 매개 변수가 협상됩니다.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 회로의 대역폭을 변경할 수 있나요?

예, Azure Portal 또는 PowerShell을 사용하여 ExpressRoute 회로의 대역폭을 늘릴 수 있습니다. 회로가 만들어진 실제 포트에 사용 가능한 용량이 있으면 성공적으로 변경된 것입니다. 

변경이 실패하면 현재 포트에 남아 있는 용량이 부족하거나(이 경우 더 높은 대역폭으로 새 ExpressRoute 회로를 만들어야 함) 해당 위치에 추가 용량이 없는 것입니다(이 경우 대역폭을 늘릴 수 없음). 

또한 연결 공급자는 네트워크 내 제한을 업데이트하여 대역폭 증가를 지원하도록 후속 작업해야 합니다. 하지만 ExpressRoute 회로의 대역폭은 줄일 수 없습니다. 낮은 대역폭으로 새 ExpressRoute 회로를 만들고, 이전 회로는 삭제해야 합니다.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 회로의 대역폭을 변경하려면 어떻게 해야 하나요?

Azure Portal, REST API, PowerShell 또는 Azure CLI를 사용하여 ExpressRoute 회로의 대역폭을 업데이트할 수 있습니다.

### <a name="i-received-a-notification-about-maintenance-on-my-expressroute-circuit-what-is-the-technical-impact-of-this-maintenance"></a>내 ExpressRoute 회로에서 유지 관리에 대한 알림을 받았습니다. 이 유지 관리의 기술적 영향은 무엇인가요?

[활성-활성 모드](./designing-for-high-availability-with-expressroute.md#active-active-connections)로 회로를 운영하는 경우 유지 관리 중에 영향을 최소화해야 합니다. 회로의 기본 및 보조 연결에 대해 개별적으로 유지 관리를 수행합니다. 예약된 유지 관리는 일반적으로 피어링 위치의 표준 시간대를 기준으로 업무 외 시간에 수행되며, 사용자가 유지 관리 시간을 선택할 수 없습니다.

### <a name="i-received-a-notification-about-a-software-upgrade-or-maintenance-on-my-expressroute-gateway-what-is-the-technical-impact-of-this-maintenance"></a>내 ExpressRoute 게이트웨이에서 소프트웨어 업그레이드 또는 유지 관리에 대한 알림을 받았습니다. 이 유지 관리의 기술적 영향은 무엇인가요?

게이트웨이에서 소프트웨어를 업그레이드하거나 유지 관리하는 동안에는 영향을 최소화해야 합니다. ExpressRoute 게이트웨이는 여러 인스턴스로 구성되며, 업그레이드하는 동안 인스턴스는 한 번에 하나씩 오프라인으로 전환됩니다. 이로 인해 게이트웨이가 일시적으로 가상 네트워크에 대한 낮은 네트워크 처리량을 지원할 수 있지만 게이트웨이 자체에는 가동 중지 시간이 발생하지 않습니다.


## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>ExpressRoute 프리미엄이란?

ExpressRoute Premium은 다음 기능의 모음입니다.

* 증가된 라우팅 테이블은 프라이빗 피어링에 대해 4000개의 경로에서 경로 10, 000개의 경로로 제한합니다.
* ExpressRoute 회로에서 사용할 수 있는 VNet 및 ExpressRoute Global Reach 연결 수가 증가했습니다(기본값은 10). 자세한 내용은 [ExpressRoute 제한](#limits) 표를 참조하세요.
* Microsoft 365에 연결
* Microsoft 핵심 네트워크를 통해 전역 연결합니다. 이제 한 지리적 지역의 VNet을 다른 지역의 ExpressRoute 회로와 연결할 수 있습니다.<br>
    **예:**

    *  유럽 서부에서 만든 VNet을 실리콘밸리에서 만든 ExpressRoute 회로에 연결할 수 있습니다. 
    *  Microsoft 피어링에서는 실리콘밸리의 회로에서 유럽 서부의 SQL Azure에 연결할 수 있는 것처럼 다른 지리적 지역의 접두사가 보급됩니다.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>ExpressRoute 프리미엄을 사용하면 ExpressRoute 회로에서 VNet 및 ExpressRoute Global Reach 연결을 몇 개나 설정할 수 있나요?

다음 표는 ExpressRoute 회로당 허용되는 ExpressRoute 한도와 VNet 및 ExpressRoute Global Reach 연결 수를 보여줍니다.

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute 프리미엄을 사용하려면 어떻게 하나요?

ExpressRoute Premium 기능은 사용하도록 설정되면 활성화할 수 있으며, 회로 상태를 업데이트하여 종료할 수 있습니다. ExpressRoute Premium은 회로를 만들 때 또는 REST API/PowerShell cmdlet을 호출하여 사용하도록 설정할 수 있습니다.

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute 프리미엄을 사용하지 않도록 하려면 어떻게 하나요?

ExpressRoute Premium은 REST API/PowerShell cmdlet을 호출하여 사용하지 않도록 설정할 수 있습니다. ExpressRoute Premium을 사용하지 않도록 설정하기 전에 기본 제한을 충족하도록 연결 요구 사항을 조정했는지 확인해야 합니다. 사용률이 기본 제한을 초과하면 ExpressRoute Premium을 사용하지 않도록 설정하는 요청이 실패합니다.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>프리미엄 기능 집합에서 원하는 기능을 선택할 수 있나요?

아니요. 기능은 선택할 수 없습니다. ExpressRoute 프리미엄을 켜면 모든 기능을 사용합니다.

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium 비용

비용은 [가격 정보](https://azure.microsoft.com/pricing/details/expressroute/) 을 참조하세요.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>표준 ExpressRoute 요금 외에도 ExpressRoute premium에 대한 납부 여부

예. ExpressRoute 프리미엄 요금은 ExpressRoute 회로 요금 및 연결 공급자에서 필요한 요금 위에 적용됩니다.

## <a name="expressroute-local"></a>ExpressRoute 로컬

### <a name="what-is-expressroute-local"></a>ExpressRoute 로컬이란?

ExpressRoute 로컬은 표준 SKU 및 프리미엄 SKU와 더불어 ExpressRoute 회로의 SKU입니다. 로컬의 핵심 기능은 ExpressRoute 피어링 위치의 로컬 회로에서 동일한 지하철 근처의 Azure 지역 한두 곳에 대한 액세스만 제공하는 것입니다. 반면, 표준 회로는 지정학적 영역의 모든 Azure 지역에 대한 액세스를 제공하고, 프리미엄 회로는 모든 Azure 지역에 대한 액세스를 전역적으로 제공합니다. 특히 로컬 SKU를 사용하는 경우에는 ExpressRoute 회로의 해당 로컬 지역에서 Microsoft 및 개인 피어링을 통해 경로를 보급할 수만 있습니다. 정의된 로컬 지역과 다른 지역에 대한 경로를 수신할 수 없습니다.

### <a name="what-are-the-benefits-of-expressroute-local"></a>ExpressRoute 로컬의 이점은 무엇인가요?

표준 또는 프리미엄 ExpressRoute 회로의 경우 송신 데이터 전송 비용을 지불해야 하지만 ExpressRoute 로컬 회로의 경우 송신 데이터 전송 비용을 별도로 지불하지 않습니다. 즉, ExpressRoute 로컬 가격에 데이터 전송 요금이 포함되어 있습니다. ExpressRoute 로컬은 전송할 데이터가 많고 개인 연결을 통해 원하는 Azure 지역 근처의 ExpressRoute 피어링 위치로 데이터를 가져올 수 있는 경우에 보다 경제적인 솔루션입니다. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>사용할 수 있는 기능과 ExpressRoute 로컬에 없는 기능은 무엇인가요?

로컬 회로에는 다음을 제외하고 표준 ExpressRoute 회로와 동일한 기능 세트가 있습니다.
* 위에서 설명한 Azure 지역에 대한 액세스 범위
* 로컬에서는 ExpressRoute Global Reach를 사용할 수 없습니다.

또한 ExpressRoute 로컬의 리소스에 대한 자세한 사항(예: 회로당 VNet 수)은 표준과 동일합니다. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>ExpressRoute 로컬을 사용할 수 있고 각 피어링 위치가 매핑되는 Azure 지역은 어디인가요?

ExpressRoute 로컬은 1개 또는 2개의 Azure 지역이 인접한 피어링 위치에서 사용할 수 있습니다. 해당 시/도 또는 국가/지역에 Azure 지역이 없는 피어링 위치에서는 사용할 수 없습니다. [위치 페이지](expressroute-locations-providers.md)에서 정확한 매핑을 참조하세요.  

## <a name="expressroute-for-microsoft-365"></a>Microsoft 365용 ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Microsoft 365 서비스에 연결할 ExpressRoute 회로를 만들려면 어떻게 해야 하나요?

1. [ExpressRoute 필수 구성 요소 페이지](expressroute-prerequisites.md)를 검토하여 요구 사항을 충족하는지 확인합니다.
2. 사용자 연결 요구 사항이 충족되는지 확인하려면 [ExpressRoute 파트너 및 위치](expressroute-locations.md) 문서에서 서비스 공급자 및 위치 목록을 검토합니다.
3. [Microsoft 365에 대한 네트워크 계획 및 성능 튜닝](/microsoft-365/enterprise/network-planning-and-performance)을 검토하여 용량 요구 사항을 계획합니다.
4. [회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로](expressroute-workflows.md)의 연결 설정 워크플로에 나열된 단계를 따릅니다.

> [!IMPORTANT]
> Microsoft 365 서비스에 대한 연결을 구성할 때 ExpressRoute Premium 추가 기능을 사용하도록 설정했는지 확인하세요.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>기존 ExpressRoute 회로가 Microsoft 365 서비스에 대한 연결을 지원할 수 있나요?

예. 기존 ExpressRoute 회로가 Microsoft 365 서비스에 대한 연결을 지원하도록 구성할 수 있나요? Microsoft 365 서비스에 연결하는 데 충분한 용량이 있는지와 프리미엄 추가 기능을 사용하도록 설정했는지를 확인합니다. [Microsoft 365의 네트워크 계획 및 성능 튜닝](/microsoft-365/enterprise/network-planning-and-performance)을 참조하면 연결 요구 사항을 계획할 수 있습니다. [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-classic.md)도 참조하세요.

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 연결을 통해 어느 Microsoft 365 서비스에 액세스할 수 있나요?

ExpressRoute에서 지원되는 최신 서비스 목록은 [Microsoft 365 URL 및 IP 주소 범위](/microsoft-365/enterprise/urls-and-ip-address-ranges) 페이지를 참조하세요.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Microsoft 365 서비스용 ExpressRoute의 비용은 얼마인가요?

Microsoft 365 서비스를 사용하려면 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 자세한 내용은 [가격 세부 정보 페이지](https://azure.microsoft.com/pricing/details/expressroute/)를 참조하세요.

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Microsoft 365용 ExpressRoute가 지원되는 지역은 어디인가요?

[ExpressRoute 파트너 및 위치](expressroute-locations.md)를 참조하세요.

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>ExpressRoute가 내 조직에 대해 구성된 경우라도 인터넷을 통해 Microsoft 365에 액세스할 수 있나요?

예. ExpressRoute가 네트워크에 구성되어 있더라도 인터넷을 통해 Microsoft 365 서비스 엔드포인트에 연결할 수 있습니다. 사용자의 위치에서 네트워크가 ExpressRoute 통해 Microsoft 365 서비스에 연결되도록 구성하는 경우 조직의 네트워킹 팀에 문의하세요.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Azure ExpressRoute에서 Microsoft 365 네트워크 트래픽에 대한 고가용성을 어떻게 계획할 수 있나요?
[Azure ExpressRoute의 고가용성 및 장애 조치](/microsoft-365/enterprise/network-planning-with-expressroute)에 대한 권장 사항 참조

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Azure 미국 정부 ExpressRoute 회로를 통해 Office 365 미국 정부 커뮤니티(GCC) 서비스에 액세스할 수 있나요?

예. Office 365 GCC 서비스 엔드포인트는 Azure 미국 정부 ExpressRoute를 통해 연결할 수 있습니다. 그러나 먼저 Azure Portal에서 지원 티켓을 열어 Microsoft에 보급하려는 접두사를 제공해야 합니다. 지원 티켓이 확인된 후 Office 365 GCC 서비스로의 연결이 설정됩니다. 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft 피어링용 경로 필터

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Microsoft 피어링을 처음 설정해 봅니다. 어떤 경로를 확인해야 하나요?

경로는 표시되지 않습니다. 접두사 보급을 시작하려면 회로에 경로 필터를 연결해야 합니다. 자세한 내용은 [Microsoft 피어링용 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Microsoft 피어링을 사용하도록 설정했고, 지금은 Exchange Online을 선택하려고 했지만 이 작업을 수행할 권한이 없다는 오류 메시지가 표시됩니다.

경로 필터를 사용하면 모든 고객이 Microsoft 피어링을 설정할 수 있습니다. 그러나 Microsoft 365 서비스를 사용하려면 여전히 Microsoft 365에서 권한을 부여받아야 합니다.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>2017년 8월 1일 이전에 Microsoft 피어링을 사용하도록 설정했습니다. 경로 필터를 사용하려면 어떻게 해야 하나요?

기존 회로가 Microsoft 365용 접두사를 계속 보급하게 됩니다. 동일한 Microsoft 피어링을 통해 Azure 공용 접두사 보급 알림을 추가하려면 경로 필터를 만들고, 보급해야 하는 서비스(필요한 Microsoft 365 서비스 포함)를 선택하고, 해당 필터를 Microsoft 피어링에 추가할 수 있습니다. 자세한 내용은 [Microsoft 피어링용 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>한 위치에서 Microsoft 피어링을 사용하고 있습니다. 이제 다른 위치에서 사용하도록 설정하려고 했지만 접두사가 표시되지 않습니다.

* 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다.

* 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다. 접두사는 기본적으로 표시되지 않습니다.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>개인 정보 취급 방침

### <a name="does-the-expressroute-service-store-customer-data"></a>ExpressRoute 서비스에서 고객 데이터를 저장하나요?

아니요.