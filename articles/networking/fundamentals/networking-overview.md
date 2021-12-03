---
title: Azure 네트워킹 서비스 개요
description: 연결, 애플리케이션 보호, 애플리케이션 제공 및 네트워크 모니터링 서비스를 포함하여 Azure의 네트워킹 서비스에 대해 알아봅니다.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: kumud
ms.openlocfilehash: 30346fa6c837484613acc4cb48a82e8854d13a3f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076645"
---
# <a name="azure-networking-services-overview"></a>Azure 네트워킹 서비스 개요

Azure의 네트워킹 서비스에서는 함께 또는 별도로 사용할 수 있는 다양한 네트워킹 기능을 제공합니다. 다음과 같은 주요 기능에 대해 알아보려면 그 중 하나를 클릭합니다.
- [**연결 서비스**](#connect): Azure의 VNet(Virtual Network), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Peering Service 및 Azure Bastion에서 이러한 네트워킹 서비스 중 일부 또는 조합을 사용하여 Azure 리소스 및 온-프레미스 리소스를 연결합니다.
- [**애플리케이션 보호 서비스**](#protect): Azure의 Load Balancer, 프라이빗 링크, DDoS 보호, 방화벽, 네트워크 보안 그룹, 웹 애플리케이션 방화벽 및 Virtual Network 엔드포인트에서 이러한 네트워킹 서비스의 조합을 사용하여 애플리케이션을 보호합니다.
- [ **전달 서비스**](#deliver): Azure의 CDN(Content Delivery Network), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer 및 Load Balancer에서 이러한 네트워킹 서비스의 조합을 사용하여 Azure 네트워크에서 애플리케이션을 전달합니다.
- [**네트워크 모니터링**](#monitor): Azure Network Watcher, ExpressRoute Monitor, Azure Monitor 또는 VNet TAP(터미널 액세스 지점)에서 네트워킹 서비스 중 일부 또는 조합을 사용하여 네트워크 리소스를 모니터링합니다.

## <a name="connectivity-services"></a><a name="connect"></a>연결 서비스
 
이 섹션에서는 Azure 리소스 간에 연결을 제공하고, 온-프레미스 네트워크에서 Azure 리소스에 연결하고, Azure의 VNet(Azure-Virtual Network), ExpressRoute, VPN Gateway, Virtual WAN, Virtual Network NAT Gateway, Azure DNS, Azure Peering Service 및 Azure Bastion에서 분기 연결을 제공하는 서비스에 대해 설명합니다.


### <a name="virtual-network"></a><a name="vnet"></a>가상 네트워크

Azure Virtual Network(VNet)는 Azure의 프라이빗 네트워크의 기본 구성 요소입니다. Vnet을 사용하여 다음을 수행할 수 있습니다.
- **Azure 리소스 간에 통신**: Azure App Service Environments, AKS(Azure Kubernetes Service), Azure Virtual Machine Scale Sets 등의 여러 가지 VM 및 Azure 리소스를 가상 네트워크에 배포할 수 있습니다. 가상 네트워크에 배포할 수 있는 Azure 리소스의 전체 목록을 보려면 [가상 네트워크 서비스 통합](../../virtual-network/virtual-network-for-azure-services.md)을 참조하세요.
- **서로 간에 통신**: 가상 네트워크를 서로 연결하면 각 가상 네트워크의 리소스가 가상 네트워크 피어링을 사용하여 서로 통신할 수 있습니다. 연결한 가상 네트워크는 같은 Azure 지역 또는 다른 Azure 지역에 있을 수 있습니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.
- **인터넷과 통신**: 기본적으로 VNet의 모든 리소스는 인터넷으로 아웃바운드 통신을 할 수 있습니다. 공용 IP 주소 또는 공용 부하 분산 장치를 할당하여 리소스에 대해 인바운드로 통신할 수 있습니다. [공용 IP 주소](../../virtual-network/ip-services/virtual-network-public-ip-address.md) 또는 공용 [부하 분산 장치](../../load-balancer/load-balancer-overview.md)를 사용하여 아웃바운드 연결을 관리할 수도 있습니다.
- **온-프레미스 네트워크와 통신**: [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](../../expressroute/expressroute-introduction.md) 경로를 사용하여 온-프레미스 컴퓨터 및 네트워크를 가상 네트워크에 연결할 수 있습니다.

자세한 내용은 [Azure Virtual Network란?](../../virtual-network/virtual-networks-overview.md)을 참조하세요.

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute를 사용하면 연결 공급자가 지원하는 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. 이 연결은 프라이빗 전용입니다. 트래픽은 인터넷을 통해 이동하지 않습니다. ExpressRoute를 사용하면 Microsoft Azure, Microsoft 365, Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.  자세한 내용은 [ExpressRoute란?](../../expressroute/expressroute-introduction.md)을 참조하세요.

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway를 사용하면 온-프레미스 위치에서 가상 네트워크에 대한 암호화된 프레미스 간 연결을 만들거나 VNet 간에 암호화된 연결을 만들 수 있습니다. 사이트 간, 지점 및 사이트 간 또는 VNet 간 연결 등의 VPN Gateway 연결에 대해 다양한 구성을 사용할 수 있습니다.
다음 다이어그램은 동일한 가상 네트워크에 대한 여러 사이트 간 VPN 연결을 보여 줍니다.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="사이트 간 Azure VPN Gateway 연결":::

여러 VPN 연결 유형에 대한 자세한 내용은 [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

### <a name="virtual-wan"></a><a name="virtualwan"></a>가상 WAN
Azure Virtual WAN은 Azure를 통해 최적화된 자동 분기 연결을 제공하는 네트워킹 서비스입니다. Azure 지역은 분기를 연결하도록 선택할 수 있는 허브 역할을 합니다. 또한 Azure 백본을 활용하여 분기를 연결하고 분기 및 VNet 간 연결을 설정할 수 있습니다. Azure Virtual WAN은 사이트 간 VPN, ExpressRoute, 지점 및 사이트 간 사용자 VPN과 같은 다양한 Azure 클라우드 연결 서비스를 단일 운영 인터페이스로 통합합니다. Azure VNet에 대한 연결은 가상 네트워크 연결을 사용하여 설정합니다. 자세한 내용은 [Azure Virtual WAN이란?](../../virtual-wan/virtual-wan-about.md)을 참조하세요.

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtual WAN 다이어그램":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 자세한 내용은 [Azure DNS란?](../../dns/dns-overview.md)을 참조하세요.

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 서비스는 가상 네트워크 내에서 프로비저닝하는 새로운 완전 플랫폼 관리형 PaaS 서비스입니다. TLS를 통해 Azure Portal에서 직접 가상 머신에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결하는 경우에는 가상 머신에 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Azure Bastion이란?](../../bastion/bastion-overview.md)을 참조하세요.

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure Bastion 아키텍처":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtual Network NAT Gateway
Virtual Network NAT(Network Address Translation)는 가상 네트워크에 대한 아웃바운드 전용 인터넷 연결을 간소화합니다. 서브넷에 구성되는 경우 모든 아웃바운드 연결에서 지정된 고정 공용 IP 주소를 사용합니다. 가상 머신에 직접 연결되는 부하 분산 장치 또는 공용 IP 주소가 없으면 아웃바운드 연결이 가능합니다. 자세한 내용은 [Virtual Network NAT Gateway란?](../../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Virtual Network NAT Gateway":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure Peering Service
Azure Peering Service는 Microsoft 365, Dynamics 365, SaaS(Software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대한 고객의 연결을 향상시킵니다. 자세한 내용은 [Azure Peering Service란?](../../peering-service/about.md)을 참조하세요.

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

Azure Edge Zone은 사용자 가까이에서 데이터를 처리할 수 있는 Microsoft Azure의 제품군입니다. VM, 컨테이너 및 기타 선택한 Azure 서비스를 Edge Zone에 배포하여 애플리케이션의 짧은 대기 시간과 높은 처리량 요구 사항을 해결할 수 있습니다.

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

Azure Orbital은 우주선 또는 위성 관측, 다운링크 및 업링크 데이터와 통신하고, 클라우드에서 데이터를 처리하며, 고유한 시나리오에서 서비스를 Azure 서비스와 연결하고, 고객을 위한 제품을 생성할 수 있는 완전 관리형 클라우드 기반 지상국 서비스입니다. 이 시스템은 Azure 글로벌 인프라 및 대기 시간이 짧은 글로벌 파이버 네트워크를 기반으로 하여 구축되었습니다.

## <a name="application-protection-services"></a><a name="protect"></a> 애플리케이션 보호 서비스

이 섹션에서는 네트워크 리소스를 보호하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명합니다. Azure의 DDoS Protection, Private Link, 방화벽, Web Application Firewall, 네트워크 보안 그룹 및 가상 네트워크 서비스 엔드포인트와 같은 네트워킹 서비스 중 일부 또는 조합을 사용하여 애플리케이션을 보호합니다.

### <a name="ddos-protection"></a><a name="ddosprotection">DDoS Protection</a> 
[Azure DDoS Protection](../../ddos-protection/manage-ddos-protection.md)은 매우 정교한 DDoS 위협에 대한 대책을 제공합니다. 이 서비스는 가상 네트워크에 배포된 애플리케이션 및 리소스에 대해 향상된 DDoS 완화 기능을 제공합니다. 또한 Azure DDoS Protection을 사용하는 고객은 활성 공격 중에 DDoS 전문가를 참여시키도록 DDoS Rapid Response 지원에 액세스할 수 있습니다.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services(예: Azure Storage 및 SQL Database)와 Azure 호스트 고객 소유/파트너 서비스에 액세스할 수 있습니다.
가상 네트워크와 서비스 사이의 트래픽은 Microsoft 백본 네트워크를 통해 이동합니다. 서비스를 공용 인터넷에 더 이상 노출할 필요가 없습니다. 가상 네트워크에 자체 프라이빗 링크 서비스를 만들어서 고객에게 제공할 수도 있습니다.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="프라이빗 엔드포인트 개요":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. Azure Firewall을 사용하면 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다. 

Azure Firewall에 대한 자세한 내용은 [Azure Firewall 설명서](../../firewall/overview.md)를 참조하세요.

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="방화벽 개요":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
[WAF(Azure Web Application Firewall)](../../web-application-firewall/overview.md)는 SQL 삽입, 사이트 간 스크립팅 등의 일반적인 웹 익스플로잇 및 취약성으로부터 웹 애플리케이션을 보호합니다. Azure WAF는 관리형 규칙을 통해 OWASP 상위 10개 취약점으로부터 보호하는 기본 기능을 제공합니다. 또한 고객은 원본 IP 범위를 기반으로 추가 보호 기능을 제공하며 헤더, 쿠키, 양식 데이터 필드 또는 쿼리 문자열 매개 변수와 같은 요청 속성을 제공하는 고객 관리형 규칙인 사용자 지정 규칙을 구성할 수도 있습니다.

고객은 공용 및 개인 주소 공간의 엔터티에 지역 보호를 제공하는 [Application Gateway를 사용하여 Azure WAF](../../web-application-firewall/ag/ag-overview.md)를 배포하도록 선택할 수 있습니다. 또한 고객은 네트워크 에지에서 공용 엔드포인트에 대한 보호를 제공하는 [Front Door를 사용하여 Azure WAF](../../web-application-firewall/afds/afds-overview.md)를 배포하도록 선택할 수 있습니다.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="웹 애플리케이션 방화벽":::

### <a name="network-security-groups"></a><a name="nsg"></a>네트워크 보안 그룹
Azure 가상 네트워크의 Azure 리소스와 네트워크 보안 그룹이 주고 받는 네트워크 트래픽을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)을 참조하세요.

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>서비스 엔드포인트
VNet(Virtual Network) 서비스 엔드포인트는 직접 연결을 통해 가상 네트워크 프라이빗 주소 공간 및 Azure 서비스에 대한 VNet의 ID를 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="가상 네트워크 서비스 엔드포인트":::

## <a name="application-delivery-services"></a><a name="deliver"></a> 애플리케이션 전달 서비스

이 섹션에서는 Content Delivery Network, Azure Front Door Service, Traffic Manager, Load Balancer 및 Application Gateway와 같은 애플리케이션을 제공하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명합니다.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다. Azure CDN에 대한 자세한 내용은 [Azure Content Delivery Network](../../cdn/cdn-overview.md)를 참조하세요.

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. Front Door를 사용하면 글로벌(다중 지역) 소비자 및 기업 애플리케이션을 글로벌 Azure 잠재 고객에게 도달하는 견고한 고성능의 맞춤형 최신 애플리케이션, API 및 콘텐츠로 변환할 수 있습니다. 자세한 내용은 [Azure Front Door](../../frontdoor/front-door-overview.md)를 참조하세요.

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Front Door Service 개요":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager는 트래픽을 전 세계 Azure 지역의 서비스에 적절하게 분산하는 한편, 고가용성과 빠른 응답성을 제공하는 DNS 기반 트래픽 부하 분산 장치입니다. Traffic Manager는 우선 순위, 가중치, 성능, 지리적, 다중값 또는 서브넷과 같은 트래픽을 분산하기 위한 다양한 트래픽 라우팅 방법을 제공합니다. 트래픽 라우팅 방법에 대한 자세한 내용은 [Traffic Manager 라우팅 방법](../../traffic-manager/traffic-manager-routing-methods.md)을 참조하세요.

다음 다이어그램은 Traffic Manager를 사용한 엔드포인트 우선 순위 기반 라우팅을 보여 줍니다.

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure Traffic Manager '우선 순위' 트래픽 라우팅 메서드":::

Traffic Manager에 대한 자세한 내용은 [Azure Traffic Manager란?](../../traffic-manager/traffic-manager-overview.md)을 참조하세요.

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer는 모든 UDP 및 TCP 프로토콜에 대해 대기 시간이 낮은 고성능 계층 4 부하 분산을 제공합니다. 인바운드 및 아웃 바운드 연결을 관리합니다. 내부 부하가 분산된 공용 엔드포인트를 구성할 수 있습니다. 서비스 가용성 관리 옵션을 검색하는 TCP 및 HTTP 상태를 사용하여 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다. Load Balancer에 대한 자세한 내용은 [Load Balancer 개요](../../load-balancer/load-balancer-overview.md) 문서를 참고하세요.

다음 그림에서는 외부 및 내부 부하 분산 장치를 모두 활용하는 인터넷 연결 다중 계층 애플리케이션을 보여줍니다.

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer 예제":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다. 서비스 형태의 ADC(애플리케이션 전달 컨트롤러)이며 애플리케이션에 대한 다양한 계층 7 부하 분산 기능을 제공합니다. 자세한 내용은 [Azure Application Gateway란?](../../application-gateway/overview.md)을 참조하세요.

다음 다이어그램에서는 Application Gateway를 사용하는 URL 경로 기반 라우팅을 보여 줍니다.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway 예제":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>네트워크 모니터링 서비스
이 섹션에서는 네트워크 리소스를 모니터링하는 데 도움이 되는 Network Watcher, Azure Monitor Network Insights, Azure Monitor, ExpressRoute Monitor 및 Virtual Network TAP 등의 Azure 네트워킹에 있는 서비스에 대해 설명합니다.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고 메트릭을 보고 그에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다. 자세한 내용은 [Network Watcher란?](../../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)을 참조하세요.

### <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights
구성없이 배포된 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 뷰를 제공하는 네트워크용 Azure Monitor입니다. 또한 [연결 모니터](../../network-watcher/connection-monitor-overview.md), [네트워크 보안 그룹에 대한 흐름 로깅](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)및 [트래픽 분석](../../network-watcher/traffic-analytics.md) 등의 네트워크 모니터링 기능에 대한 액세스를 제공합니다. 자세한 내용은 [Azure Monitor Network Insights](../../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조하세요.

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute 모니터
ExpressRoute 회로 메트릭, 리소스 로그 및 경고를 보는 방법에 대한 자세한 내용은 [ExpressRoute 모니터링, 메트릭 및 경고](../../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조하세요.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석 데이터를 수집, 분석하고 그에 따라 조치를 취하는 포괄적인 솔루션을 제공함으로써 애플리케이션의 성능과 가용성을 최대화합니다. 애플리케이션을 수행하는 방법과 애플리케이션 및 종속된 리소스에 영향을 주는 문제를 사전에 식별하는 방법을 파악할 수 있습니다. 자세한 내용은 [Azure Monitor 개요](../../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)를 참조하세요.
### <a name="virtual-network-tap"></a><a name="vnettap"></a>가상 네트워크 TAP
Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너를 통해 제공됩니다.

다음 이미지는 가상 네트워크 TAP이 작동하는 방법을 보여 줍니다.

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="가상 네트워크 TAP이 작동하는 방법":::

자세한 내용은 [가상 네트워크 TAP이란?](../../virtual-network/virtual-network-tap-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [첫 번째 가상 네트워크 만들기](../../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 첫 번째 가상 네트워크를 만들고 일부 VM을 연결합니다.
- [지점 및 사이트 간 연결 구성](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 가상 네트워크에 컴퓨터를 연결합니다.
- [인터넷 연결 부하 분산 장치 만들기](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 공용 서버에 인터넷 트래픽의 부하를 분산합니다.