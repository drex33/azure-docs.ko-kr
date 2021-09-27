---
title: Azure 네트워킹 서비스를 사용하여 원격으로 작업
description: 이 페이지에서는 원격 작업을 가능하게 하는 데 사용할 수 있는 Azure 네트워킹 서비스를 사용하는 방법 및 원격으로 작업하는 사용자 수의 증가로 인한 트래픽 문제를 완화하는 방법을 설명합니다.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 2086351f1e66cbd2ef41ac98b2ab86fe7275823b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621360"
---
# <a name="working-remotely-using-azure-networking-services"></a>Azure 네트워킹 서비스를 사용하여 원격으로 작업

>[!NOTE]
> 이 문서에서는 Azure 네트워킹 서비스, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 발생할 수 있는 네트워크 문제를 완화하는 방법을 설명합니다.

이 문서에서는 조직에서 사용자에 대한 원격 액세스를 설정하거나 사용량이 많은 기간 동안 추가 용량으로 기존 솔루션을 보완하는 데 사용할 수 있는 옵션을 설명합니다. 네트워크 설계자는 다음과 같은 문제에 직면해 있습니다.

- 네트워크 사용률 증가에 대한 해결
- 회사 및 고객의 더 많은 직원에게 안정적인 보안 연결 제공
- 전 세계 원격 위치에 대한 연결 제공

모든 네트워크(예: 프라이빗 WAN 및 회사 코어 네트워크)가 최대 원격 작업자 로드로 인해 정체되는 것은 아닙니다. 병목 상태는 일반적으로 회사의 온-프레미스 네트워크에 대한 홈 광대역 네트워크 및 VPN 게이트웨이에서만 보고됩니다.

네트워크 계획자는 서로 다른 트래픽 유형이 서로 다른 네트워크 처리 우선 순위를 필요로 한다는 것과 일부 스마트 로드 리디렉션/분배를 통해 병목 현상을 완화하고 네트워크 정체를 완화하도록 도울 수 있습니다. 예를 들어, 의사와 환자 상호 작용의 실시간 원격 의료 트래픽은 매우 중요하며 지연/지터에 민감합니다. 반면, 스토리지 간의 동일한 트래픽 복제는 지연에 민감하지 않습니다. 이전 트래픽은 서비스 품질이 높은 최적의 네트워크 경로를 통해 라우팅해야 합니다. 반면에 하위 최적 경로를 통해 나중에 트래픽을 라우팅할 수 있습니다.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>모범 사례 공유 - Azure 네트워크는 탄력성 및 고가용성을 위해 설계되었습니다.

Azure는 리소스 사용률의 급격한 변화를 견딜 수 있도록 설계되었으며, 사용량이 많은 기간 동안 매우 유용합니다. 또한 Microsoft는 세계 최대의 네트워크 중 하나를 유지 관리하고 운영합니다. Microsoft 네트워크는 단일 네트워크 요소 오류에서 전체 지역의 장애에 이르기까지 다양한 유형의 오류를 견딜 수 있는 고가용성을 위해 설계되었습니다.

Microsoft 네트워크는 요구 사항을 충족하고 Skype 및 Teams, CDN, 실시간 빅 데이터 분석, Azure 스토리지, Bing 및 Xbox의 경우 지연에 민감한 멀티미디어 트래픽을 포함하여 다양한 유형의 네트워크 트래픽에 대한 최적의 성능을 제공하도록 설계되었습니다. 다양한 트래픽 유형에 최적의 성능을 제공하기 위해 Microsoft 네트워크는 트래픽 원본에 최대한 가깝게 리소스를 통과하거나 전송하려는 모든 트래픽을 확보합니다.

>[!NOTE] 
>아래에 설명된 Azure 네트워킹 기능을 사용하면 Microsoft 글로벌 네트워크의 트래픽 확보 동작을 활용하여 더 나은 고객 네트워킹 환경을 제공할 수 있습니다. Microsoft 네트워크의 트래픽 확보 동작은 사용량이 많은 기간 동안 정체가 발생할 수 있는 첫 번째/마지막 마일 네트워크에서 가능한 한 빨리 트래픽 로드를 방지하는 데 도움이 됩니다.
>

## <a name="enable-employees-to-work-remotely"></a>직원이 원격으로 작업할 수 있도록 설정

Azure VPN 게이트웨이는 지점 및 사이트 간(P2S) 및 사이트 간(S2S) VPN 연결을 모두 지원합니다. Azure VPN 게이트웨이를 통해 직원의 연결을 확장하여 Azure가 배포한 리소스와 온-프레미스 리소스 모두에 안전하게 액세스할 수 있습니다. 자세한 내용은 [사용자가 원격으로 작업할 수 있도록 설정하는 방법](../vpn-gateway/work-remotely-support.md)을 참조하세요. 

SSTP(Secure Sockets Tunneling Protocol)를 사용하는 경우 동시 연결 수는 128개로 제한됩니다. 더 많은 수의 연결을 얻으려면 OpenVPN 또는 IKEv2로 전환하는 것이 좋습니다. 자세한 내용은 [SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환](../vpn-gateway/ikev2-openvpn-from-sstp.md
)을 참조하세요.

Azure에 배포된 리소스에 액세스하기 위해 원격 개발자는 액세스할 VM에 공용 IP를 요구하지 않고 보안 셸 액세스(RDP 또는 SSH)를 가져오는 VPN 연결 대신 Azure Bastion 솔루션을 사용할 수 있습니다. 자세한 내용은 [Azure Bastion를 사용하여 원격으로 작업](../bastion/work-remotely-support.md)을 참조하세요.

대규모 VPN 연결을 집계하는 경우, 다양한 지역 허브 및 스포크 가상 네트워크에 있는 서로 다른 온-프레미스 전체 위치의 리소스 간 모든 연결을 지원하고, 여러 홈 광대역 네트워크의 활용을 최적화하기 위해 Azure Virtual WAN을 사용할 수 있습니다. 자세한 내용은 [가정에서 작업을 수행하는 데 도움이 필요하십니까? Azure Virtual WAN이 도움이 될 수 있는 곳은 다음과 같습니다.](../virtual-wan/work-remotely-support.md)를 참조하세요.

원격 직원을 지원하는 또 다른 방법은Azure Firewall을 사용하여 보호되는 Azure Virtual Network에 호스트되는 VDI(가상 데스크톱 인프라)를 배포하는 것입니다. 예를 들어 AVD(Azure Virtual Desktop)는 Azure에서 실행되는 데스크톱 및 앱 가상화 서비스입니다. Azure Virtual Desktop을 사용하면 추가 게이트웨이 서버를 실행할 필요 없이 Azure 구독에서 확장 가능하고 유연한 환경을 설정할 수 있습니다. 가상 네트워크의 AVD 가상 머신만 담당합니다. 자세한 내용은 [Azure Firewall 원격 작업 지원](../firewall/remote-work-support.md)을 참조하세요. 

Azure에는 다양한 에코 시스템 파트너도 있습니다. Azure의 파트너 네트워크 가상 어플라이언스를 통해 VPN 연결을 확장할 수도 있습니다. 자세한 내용은 [원격 작업에 대한 NVA(네트워크 가상 어플라이언스)](../vpn-gateway/nva-work-remotely-support.md)를 참조하세요.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>직원의 연결을 확장하여 전역으로 분산되는 리소스에 액세스

다음 Azure 서비스를 통해 직원이 전역으로 배포된 리소스에 액세스할 수 있습니다. 리소스는 Azure 지역, 온-프레미스 네트워크 또는 다른 공용 또는 프라이빗 클라우드에서도 있을 수 있습니다. 

- **Azure 가상 네트워크 피어링**: 둘 이상의 Azure 지역에 리소스를 배포하거나 여러 가상 네트워크를 사용하여 원격으로 작업하는 직원의 연결을 집계하는 경우 가상 네트워크 피어링을 사용하여 여러 Azure 가상 네트워크 간에 연결을 설정할 수 있습니다. 자세한 내용은 [가상 네트워크 피어링][VNet-peer]을 참조하세요.

- **Azure VPN 기반 솔루션**: P2S 또는 S2S VPN을 통해 Azure에 연결된 원격 직원의 경우 온-프레미스 네트워크와 Azure VPN 게이트웨이 간에 S2S VPN을 구성하여 온-프레미스 네트워크에 대한 액세스를 사용하도록 설정할 수 있습니다. 자세한 내용은 [사이트 간 연결 만들기][S2S]를 참조하세요.

- **ExpressRoute**: ExpressRoute 개인 피어링을 사용하여 Azure 배포와 온-프레미스 인프라 또는 공동 위치 시설의 인프라 간에 개인 연결을 사용하도록 설정할 수 있습니다. Microsoft 피어링을 통해 ExpressRoute를 통해 온-프레미스 네트워크에서 Microsoft의 공용 엔드포인트에 액세스할 수도 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반적인 연결보다 더 짧고 일관된 대기 시간으로 보안 연결성, 안정성 및 높은 처리량을 제공합니다. 자세한 내용은 [ExpressRoute 개요][ExR]를 참조하세요. 이미 [ExpressRoute 파트너 에코시스템][ExR-eco]의 일부인 기존 네트워크 공급자를 활용하면 Microsoft에 대한 높은 대역폭 연결을 가져오는 시간을 줄일 수 있습니다.  [ExpressRoute Direct][ExR-D]를 사용하여 온-프레미스 네트워크를 Microsoft 백본에 직접 연결할 수 있습니다. ExpressRoute Direct는 이중 10Gbps 또는 100Gbps의 두 가지 다른 라인 비율 옵션을 제공합니다. 

- **Azure Virtual WAN**: Azure Virtual WAN은 VPN 연결과 ExpressRoute 회로 간의 원활한 상호 운용성을 허용합니다. 앞서 언급한 대로 Azure Virtual WAN은 다양한 지역 허브 및 스포크 가상 네트워크에 있는 서로 다른 온-프레미스 전체 위치의 리소스 간 모든 연결도 지원합니다.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>프런트 엔드 리소스에 대한 고객 연결의 비율 조정

많은 사람들이 온라인으로 전환하는 경우 많은 회사 웹 사이트에서 고객의 트래픽이 증가합니다. Azure Application Gateway는 증가된 프런트 엔드 워크로드를 관리하는 데 도움이 됩니다. 자세한 내용은 [Application Gateway 높은 트래픽 지원](../application-gateway/high-traffic-support.md)을 참조하세요.

## <a name="microsoft-support-for-multi-cloud-traffic"></a>다중 클라우드 트래픽에 대한 Microsoft 지원

다른 퍼블릭 클라우드에서 배포하는 경우 Microsoft는 전체 연결을 제공할 수 있습니다. Azure Virtual WAN, VPN 또는 ExpressRoute는 이러한 측면에서 도움이 될 수 있습니다. Azure에서 다른 클라우드로의 연결을 확장하려면 두 클라우드 간에 S2S VPN을 구성할 수 있습니다. ExpressRoute를 사용하여 Azure에서 다른 퍼블릭 클라우드로의 연결을 설정할 수도 있습니다. Oracle 클라우드는 ExpressRoute 파트너 에코시스템의 일부입니다. [Azure와 Oracle 클라우드 인프라 간의 직접 상호 관계를 설정][Az-OCI]할 수 있습니다. ExpressRoute 파트너 에코시스템의 일부인 대부분의 서비스 공급자는 다른 퍼블릭 클라우드에 대한 개인 연결도 제공합니다. 이러한 서비스 공급자를 활용하여 Azure의 배포와 ExpressRoute를 통한 다른 클라우드 간에 개인 연결을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다양한 Azure 네트워킹 기능을 사용하여 원격으로 작업하도록 사용자를 확장하는 방법에 대해 설명합니다.

| **아티클** | **설명** |
| --- | --- |
| [사용자가 원격으로 작업할 수 있도록 설정하는 방법](../vpn-gateway/work-remotely-support.md) | 사용자에 대한 원격 액세스를 설정하거나 조직에 대한 추가 용량으로 기존 솔루션을 보완하는 데 사용할 수 있는 옵션을 검토합니다.|
| [가정에서 작업을 수행하는 데 도움이 필요하십니까? Azure Virtual WAN이 도움이 될 수 있는 곳은 다음과 같습니다.](../virtual-wan/work-remotely-support.md) | Azure Virtual WAN을 사용하여 조직의 원격 연결 요구를 해결합니다.|
| [Application Gateway 높은 트래픽 지원](../application-gateway/high-traffic-support.md) | 웹 애플리케이션에 대한 트래픽을 관리하는 확장 가능하고 안전한 방법으로 WAF(웹 애플리케이션 방화벽)에서 Application Gateway를 사용합니다. |
| [원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항](../vpn-gateway/nva-work-remotely-support.md)|Azure의 NVA 활용에 대한 참고 자료를 검토하여 원격 액세스 솔루션을 제공합니다. |
| [SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환](../vpn-gateway/ikev2-openvpn-from-sstp.md) | OpenVPN 프로토콜 또는 IKEv2로 전환하여 SSTP의 128 동시 연결 제한을 극복합니다.|
| [Azure Bastion을 사용하여 원격으로 작업](../bastion/work-remotely-support.md) | 공용 IP 주소를 사용하지 않고 Azure Portal에서 직접 Azure 가상 네트워크 내의 가상 머신에 대한 안전하고 원활한 RDP/SSH 연결을 제공합니다. |
| [Azure ExpressRoute를 사용하여 원격 사용자를 지원하는 하이브리드 연결 만들기](../expressroute/work-remotely-support.md) | 하이브리드 연결을 위한 ExpressRoute를 사용하여 조직의 사용자가 원격으로 작업할 수 있도록 합니다.|
| [Azure Firewall 원격 작업 지원](../firewall/remote-work-support.md)|Azure Firewall을 사용하여 Azure 가상 네트워크 리소스를 보호합니다. |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md
