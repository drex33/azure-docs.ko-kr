---
title: Azure의 네트워크 보안 개념 및 요구 사항 | Microsoft Docs
description: 이 문서에서는 네트워크 보안의 핵심 개념과 요구 사항을 기본적으로 설명하고, 이러한 각 영역에서 Azure가 제공하는 사항에 대한 정보를 제공합니다.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: a62e6d5b9450ded54d4dce51cbd4469abbbdcd3b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297376"
---
# <a name="azure-network-security-overview"></a>Azure 네트워크 보안 개요

네트워크 보안은 네트워크 트래픽에 컨트롤을 적용하여 무단 액세스나 공격으로부터 리소스를 보호하는 프로세스로 정의할 수 있습니다. 목표는 정당한 트래픽만 허용되도록 하는 것입니다. Azure에는 사용자의 애플리케이션과 서비스 연결 요구 사항을 지원하기 위한 강력한 네트워킹 인프라가 포함되어 있습니다. 네트워크 연결은 Azure에 위치한 리소스 간, 온-프레미스 리소스와 Azure 호스팅 리소스 간, 그리고 인터넷과 Azure 간에 가능합니다.

이 문서에서는 Azure가 네트워크 보안 영역에서 제공하는 일부 옵션에 대해 설명합니다. 다음에 대해 알아볼 수 있습니다.

* Azure 네트워킹
* 네트워크 액세스 제어
* Azure Firewall
* 안전한 원격 액세스 및 크로스-프레미스 연결
* 가용성
* 이름 확인
* 경계 네트워크(DMZ) 아키텍처
* Azure DDoS 보호
* Azure Front Door
* 트래픽 관리자
* 감사 및 위협 검색

## <a name="azure-networking"></a>Azure 네트워킹

Azure에서는 가상 머신을 Azure Virtual Network에 연결해야 합니다. 가상 네트워크는 물리적 Azure 네트워크 패브릭 위에 구축되는 논리적 구조체입니다. 각 가상 네트워크는 다른 모든 가상 네트워크와 분리됩니다. 이는 사용자 배포의 네트워크 트래픽에 다른 Azure 고객이 액세스하지 못하도록 해줍니다.

자세한 정보:

* [Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>네트워크 액세스 제어

네트워크 액세스 제어는 Virtual Network 내의 특정 디바이스 또는 서브넷 간 연결을 제한하는 행위입니다. 네트워크 액세스 제어의 목표는 가상 머신 및 서비스에 대한 액세스를 승인 된 사용자 및 디바이스로 제한하는 것입니다. 액세스 제어는 가상 머신 또는 서비스 간 연결을 허용하거나 거부할지 결정에 따라 이루어집니다.

Azure는 다음과 같은 다양한 유형의 네트워크 액세스 제어를 지원합니다.

* 네트워크 계층 제어
* 경로 제어 및 터널링 적용
* 가상 네트워크 보안 어플라이언스

### <a name="network-layer-control"></a>네트워크 계층 제어

모든 보안 배포에는 몇 가지 네트워크 액세스 제어가 필요합니다. 네트워크 액세스 제어의 목표는 가상 머신 통신을 필요한 시스템으로 제한하는 것입니다. 다른 통신 시도는 차단됩니다.

> [!NOTE]
> 스토리지 방화벽은 [Azure Storage 보안 개요](../../storage/blobs/security-recommendations.md) 문서에서 다룹니다.

#### <a name="network-security-rules-nsgs"></a>네트워크 보안 그룹(NSG)

기본적인 네트워크 수준 액세스 제어(IP 주소 및 TCP 또는 UDP 프로토콜 기반)가 필요한 경우 NSG(네트워크 보안 그룹)를 사용할 수 있습니다. NSG는 기본적인 상태 저장 패킷 필터링 방화벽이며, [5-튜플](https://www.techopedia.com/definition/28190/5-tuple)에 기반하여 액세스를 제어할 수 있게 합니다. NSG는 구성 실수 가능성을 줄이고 관리를 간소화하는 기능을 포함합니다.

* **보강된 보안 규칙** 은 NSG 규칙 정의를 간소화하며, 동일한 결과를 달성하려면 여러 간단한 규칙을 만드는 것보다는 복잡한 규칙을 만들 수 있습니다.
* **서비스 태그** 는 IP 주소 그룹을 나타내는 Microsoft에서 만든 레이블입니다. 서비스 태그는 레이블에서 포함을 정의하는 조건을 충족하는 IP 범위를 포함하도록 동적으로 업데이트합니다. 예를 들어 동부 지역에서 모든 Azure 스토리지에 적용되는 규칙을 만들려는 경우 Storage.EastUS를 사용할 수 있습니다.
* **애플리케이션 보안 그룹** 을 사용하면 애플리케이션 그룹에 리소스를 배포하고 해당 애플리케이션 그룹을 사용하는 규칙을 만들어 해당 리소스에 대한 액세스를 제어할 수 있습니다. 예를 들어 webservers를 'Webservers' 애플리케이션 그룹에 배포한 경우 인터넷에서 'Webservers' 애플리케이션 그룹의 모든 시스템까지 443 트래픽을 허용하는 NSG를 적용한 규칙을 만들 수 있습니다.

NSG는 애플리케이션 계층 검사 또는 인증된 액세스 제어를 제공하지 않습니다.

자세한 정보:

* [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)

#### <a name="defender-for-cloud-just-in-time-vm-access"></a>클라우드 just-in-time VM 액세스를 위한 Defender

[클라우드 용 Microsoft Defender](../../security-center/security-center-introduction.md) 는 Vm에서 nsgs를 관리 하 고 적절 한 azure 역할 기반 액세스 제어 [azure RBAC](../../role-based-access-control/overview.md) 권한이 있는 사용자가 액세스를 요청할 때까지 vm에 대 한 액세스를 잠글 수 있습니다. 사용자가 성공적으로 권한이 부여 되 면 Defender는 지정 된 시간 동안 선택한 포트에 대 한 액세스를 허용 하도록 NSGs를 수정 합니다. 시간이 만료된 경우 NSG는 이전의 안전한 상태로 복원됩니다.

자세한 정보:

* [클라우드 Just-in-time 액세스를 위한 Microsoft Defender](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>서비스 엔드포인트

서비스 엔드포인트는 트래픽에 대해 제어를 적용하는 다른 방법입니다. 지원되는 서비스와의 통신을 직접 연결을 통한 VNet으로 제한할 수 있습니다. VNet에서 특정 Azure 서비스로의 트래픽은 Microsoft Azure 백본 네트워크에서 유지됩니다.  

자세한 정보:

* [서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>경로 제어 및 터널링 적용

가상 네트워크에서 라우팅 동작을 제어하는 기능은 매우 중요합니다. 라우팅이 잘못 구성된 경우, 가상 머신에 호스팅된 애플리케이션 및 서비스는 잠재적 공격자가 소유하거나 작동하는 시스템을 비롯하여 허가되지 않은 디바이스에 연결될 수 있습니다.

Azure 네트워킹은 가상 네트워크의 네트워크 트래픽에 대한 라우팅 동작을 사용자 지정할 수 있는 기능을 지원하므로 사용자는 가상 네트워크의 기본 라우팅 테이블 항목을 변경할 수 있습니다. 라우팅 동작을 제어하면 특정 디바이스 또는 디바이스 그룹의 모든 트래픽이 특정 위치를 통해 가상 네트워크에 진입하거나 가상 네트워크에서 나가도록 할 수 있습니다.

예를 들어, 가상 네트워크에 가상 네트워크 보안 어플라이언스가 있다고 가정합니다. 가상 네트워크에 출입하는 모든 트래픽이 이 특정 가상 보안 어플라이언스를 거치도록 하고자 합니다. 그러려면 Azure에서 [UDR(사용자 정의 경로)](../../virtual-network/virtual-networks-udr-overview.md)을 구성하면 됩니다.

[터널링 적용](https://www.petri.com/azure-forced-tunneling)은 사용자의 서비스가 인터넷에서 디바이스에 대한 연결을 개시하지 못하게 하도록 사용할 수 있는 메커니즘입니다. 수신 연결을 수락한 다음 응답하는 것과는 다릅니다. 프런트 엔드 웹 서버는 인터넷 호스트의 요청에 응답해야 하므로, 인터넷 출처의 트래픽은 이러한 웹 서버에 대한 인바운드가 허용되며 웹 서버는 응답할 수 있습니다.

허용하지 않으려는 것은 프런트 엔드 웹 서버가 아웃바운드 요청을 개시하는 것입니다. 이러한 요청은 보안상 위험을 가져올 수 있습니다. 이러한 연결이 맬웨어 다운로드에 사용될 수 있기 때문입니다. 이러한 프런트 엔드 서버가 인터넷에 아웃바운드 요청을 개시하기를 원하는 경우에도 온-프레미스 웹 프록시를 거치게 할 수 있습니다. 이렇게 하면 URL 필터링 및 로깅 기능을 활용할 수 있습니다.

대신, 이를 방지하기 위해 터널링 적용을 사용할 수 있습니다. 강제 터널링을 사용할 경우 인터넷에 대한 모든 연결은 사용자의 온-프레미스 게이트웨이를 통해 강제됩니다. UDR을 활용하여 강제 터널링을 구성할 수 있습니다.

자세한 정보:

* [사용자 정의된 경로 및 IP 전달이란?](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>가상 네트워크 보안 어플라이언스

NSG, UDR 및 강제 터널링이 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)의 네트워크 계층 및 전송 계층에서 일정 수준의 보안을 제공할 수 있지만, 경우에 따라 네트워크 계층보다 높은 수준에서 보안을 구현해야 할 수도 있습니다.

예를 들어, 다음과 같은 보안 요구 사항이 있을 수 있습니다.

* 애플리케이션에 대한 액세스를 허용하기 전에 인증 및 권한 부여
* 침입 감지 및 침입 대응
* 높은 수준의 프로토콜에 대한 애플리케이션 계층 검사
* URL 필터링
* 네트워크 수준 바이러스 백신 및 맬웨어 방지
* 안티봇 보호
* 애플리케이션 액세스 제어
* 추가적인 DDoS 보호(Azure 패브릭 자체에서 제공하는 DDoS 보호 이외)

Azure 파트너 솔루션을 사용하여 이러한 향상된 네트워크 보안 기능에 액세스할 수 있습니다. 가장 최신의 Azure 파트너 네트워크 보안 솔루션은 [Azure Marketplace](https://azure.microsoft.com/marketplace/)를 방문하여 "보안" 및 "네트워크 보안"을 검색하면 찾을 수 있습니다.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 일부 기능은 다음과 같습니다.

* 고가용성
* 클라우드 확장성
* 애플리케이션 FQDN 필터링 규칙
* 네트워크 트래픽 필터링 규칙

자세한 정보:

* [Azure Firewall 개요](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>안전한 원격 액세스 및 크로스-프레미스 연결

Azure 리소스의 설정, 구성 및 관리는 원격으로 수행해야 합니다. 또한, 온-프레미스와 Azure 퍼블릭 클라우드에 구성 요소가 있는 [하이브리드 IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) 솔루션을 배포할 수 있습니다. 이러한 시나리오에는 보안 원격 액세스가 필요합니다.

Azure 네트워킹은 다음 보안 원격 액세스 시나리오를 지원합니다.

* 가상 네트워크에 개별 워크스테이션 연결
* VPN으로 가상 네트워크에 온-프레미스 네트워크 연결
* 전용 WAN 링크로 가상 네트워크에 온-프레미스 네트워크 연결
* 가상 네트워크 간 연결

### <a name="connect-individual-workstations-to-a-virtual-network"></a>가상 네트워크에 개별 워크스테이션 연결

개별 개발자 또는 작업 담당자가 Azure에서 가상 머신 및 서비스를 관리하도록 하려는 경우가 있을 수 있습니다. 예를 들어, 가상 네트워크의 가상 머신에 액세스해야 하지만 보안 정책에서 개별 가상 머신에 대한 RDP 또는 SSH 원격 액세스를 허용하지 않는 경우가 있습니다. 이 경우에는 [지점 및 사이트 간 VPN](../../vpn-gateway/point-to-site-about.md) 연결을 사용할 수 있습니다.

지점 및 사이트 간 VPN 연결에서는 사용자와 가상 네트워크 간 프라이빗 및 보안 연결을 설정할 수 있습니다. VPN 연결이 설정되면 사용자는 VPN 링크를 통해 RDP 또는 SSH를 가상 네트워크의 가상 머신에 연결할 수 (사용자가 인증할 수 있고 권한이 부여되었다고 가정합니다.) 지점 및 사이트 간 VPN 지원:

* SSTP(Secure Socket Tunneling Protocol) - 독점적인 SSL 기반 VPN 프로토콜입니다. 대부분의 방화벽에서 TLS/SSL을 사용하는 443 TCP 포트를 열기 때문에 SSL VPN 솔루션이 방화벽을 통과할 수 있습니다. SSTP는 Windows 디바이스에서만 지원됩니다. Azure는 SSTP가 설치된 모든 Windows 버전(Windows 7 이상)을 지원합니다.

* IKEv2 VPN - 표준 기반 IPsec VPN 솔루션입니다. IKEv2 VPN은 Mac 디바이스(OSX 버전 10.11 이상)에서 연결하는 데 사용할 수 있습니다.

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

자세한 정보:

* [PowerShell을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>VPN으로 가상 네트워크에 온-프레미스 네트워크 연결

회사 네트워크 전체 또는 일부를 가상 네트워크에 연결해야 할 경우가 있을 수 있습니다. 이는 조직에서 [온-프레미스 데이터 센터를 Azure로 확장](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)하는 하이브리드 IT 시나리오에서 흔히 일어나는 일입니다. 대부분 경우 조직에서는 Azure와 온-프레미스에서 서비스의 일부를 호스팅합니다. 예를 들어, Azure의 프런트-엔드 웹 서버와 온-프레미스의 백-엔드 데이터베이스가 솔루션에 포함된 경우에 그럴 수 있습니다. 이러한 유형의 "크로스-프레미스" 연결은 또한 Azure에 위치한 리소스를 더 안전하게 관리하고 Active Directory 도메인 컨트롤러를 Azure로 확장하는 등의 시나리오를 가능하게 합니다.

이 작업을 수행하는 한 가지 방법은 [사이트 간 VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)을 사용하는 것입니다. 사이트 간 VPN과 지점 및 사이트 간 VPN의 차이점은, 지점 및 사이트 간 VPN은 단일 디바이스를 Virtual Network에 연결하는 반면, 사이트 간 VPN은 네트워크 전체(온-프레미스 네트워크 등)를 가상 네트워크에 연결한다는 점입니다. 가상 네트워크의 사이트 간 VPN은 매우 안전한 IPsec 터널 모드 VPN 프로토콜을 사용합니다.

자세한 정보:

* [Azure Portal을 사용하여 사이트 간 VPN 연결로 Resource Manager VNet 만들기](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [VPN Gateway 정보](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>전용 WAN 링크로 가상 네트워크에 온-프레미스 네트워크 연결

지점 및 사이트 간과 사이트 간 VPN 연결은 크로스-프레미스 연결에 효과적입니다. 그러나 일부 조직에서는 다음과 같은 장애가 있는지 고려합니다.

* VPN 연결은 인터넷을 통해 데이터를 이동합니다. 따라서 이러한 연결은 공용 네트워크를 통한 데이터 이동과 관련된 잠재적인 보안 문제에 노출됩니다. 또한, 인터넷 연결의 안정성 및 가용성을 보장할 수 없습니다.
* Virtual Network에 VPN 연결 시 일부 애플리케이션 및 용도에 사용할 수 있는 대역폭(최대 약 200Mbps)이 없을 수도 있습니다.

크로스-프레미스 연결에 대해 가장 높은 수준의 보안 및 가용성이 필요한 조직은 일반적으로 원격 사이트에 연결하기 위해 전용 WAN 링크를 사용합니다. Azure에서는 가상 네트워크에 온-프레미스 네트워크를 연결하기 위해 사용할 수 있는 전용 WAN 링크를 사용할 수 있습니다. Azure ExpressRoute, ExpressRoute Direct 및 ExpressRoute Global Reach에서 이 링크를 사용할 수 있습니다.

자세한 정보:

* [ExpressRoute 기술 개요](../../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../../expressroute/expressroute-erdirect-about.md)
* [ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>가상 네트워크 간 연결

배포에 많은 가상 네트워크를 사용할 수 있습니다. 예를 들어, 관리를 간소화하거나 보안을 강화하는 등 여러 가지 이유로 이렇게 할 수 있습니다. 여러 가상 네트워크에 리소스를 두는 동기에 관계없이 각 네트워크의 리소스를 상호 연결하려는 상황이 있을 수 있습니다.

한 가지 옵션은 인터넷을 통해 "루프백"을 실행하여 특정 가상 네트워크의 서비스를 다른 가상 네트워크의 서비스에 연결하는 것입니다. 연결은 하나의 가상 네트워크에서 시작하여 인터넷을 거친 다음, 대상 가상 네트워크로 돌아옵니다. 이 옵션을 사용하면 인터넷 기반 통신에 내재된 보안 문제에 연결이 노출됩니다.

더 나은 옵션은 두 가상 네트워크를 연결하는 사이트 간 VPN을 만드는 것입니다. 이 방법에서는 위에 언급된 크로스-프레미스 사이트 간 VPN 연결과 동일한 [IPSec 터널 모드](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) 프로토콜을 사용합니다.

이 접근 방법의 이점은 VPN 연결이 인터넷을 통해 연결되지 않고 Azure 네트워크 패브릭을 통해 설정된다는 것입니다. 따라서 인터넷을 통해 연결하는 사이트 간 VPN에 비해 추가적인 보안 계층을 제공합니다.

자세한 정보:

* [Azure Resource Manager 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

가상 네트워크를 연결하는 다른 방법은 [VNET 피어링](../../virtual-network/virtual-network-peering-overview.md)입니다. 이 기능을 사용하면 두 Azure 네트워크를 연결하여 인터넷을 통해 이동하지 않고 네트워크 간 통신이 Microsoft 백본 인프라에서 발생하게 할 수 있습니다. VNET 피어링은 Azure 지역에 걸쳐 두 VNET 또는 동일한 지역 내 두 VNET을 연결할 수 있습니다. NSG는 다른 서브넷 또는 시스템 간 연결을 제한하는 데 사용될 수 있습니다.

## <a name="availability"></a>가용성

가용성은 보안 프로그램의 핵심 구성 요소입니다. 사용자와 시스템이 네트워크를 통해 액세스하기 위해 필요한 것에 액세스할 수 없는 경우 서비스는 손상된 것으로 간주할 수 있습니다. Azure에는 다음 고가용성 메커니즘을 지원하는 네트워킹 기술이 있습니다.

* HTTP 기반 부하 분산
* 네트워크 수준 부하 분산
* 전역 부하 분산

부하 분산은 여러 디바이스 사이의 연결을 고르게 분산하도록 설계된 메커니즘입니다. 부하 분산의 목표는 다음과 같습니다.

* 가용성 증가. 여러 디바이스에 연결 부하를 분산하면 하나 이상의 디바이스가 사용할 수 없게 되더라도 서비스가 영향을 받지 않습니다. 즉, 나머지 온라인 디바이스에서 실행 중인 서비스가 해당 서비스에서 콘텐츠를 계속 제공할 수 있습니다.
* 성능 향상. 여러 디바이스에서 연결 부하를 분산시키면 단일 디바이스에서 모든 처리를 실행하지 않아도 됩니다. 대신, 콘텐츠 제공에 대한 처리 및 메모리 요구는 여러 디바이스에 분산됩니다.

### <a name="http-based-load-balancing"></a>HTTP 기반 부하 분산

웹 기반 서비스를 실행하는 조직은 이러한 웹 서비스 앞에 HTTP 기반 부하 분산 장치를 두기를 원하는 경우가 종종 있습니다. 이는 적정한 수준의 성능과 고가용성을 보장하는 데 도움이 됩니다. 기존 네트워크 기반 부하 분산 장치는 네트워크 및 전송 계층 프로토콜을 기반으로 하는 반면, HTTP 기반 부하 분산 장치는 HTTP 프로토콜의 특성을 기반으로 의사 결정을 내립니다.

Azure Application Gateway는 웹 기반 서비스에 대한 HTTP 기반 부하 분산을 제공합니다. Application Gateway는 다음을 지원합니다.

* 쿠키 기반 세션 선호도. 이 기능은 해당 부하 분산 장치 뒤의 서버 중 하나에 설정된 연결이 클라이언트와 서버 사이에서 그대로 유지되도록 해줍니다. 따라서 트랜잭션의 안정성이 보장됩니다.
* TLS 오프로드. 클라이언트가 부하 분산 장치와 연결된 경우 해당 세션은 HTTPS(TLS) 프로토콜을 사용하여 암호화됩니다. 그러나 성능을 개선하기 위해 HTTP(암호화되지 않은) 프로토콜을 사용하여 부하 분산 장치와 그 뒤의 웹 서버를 연결할 수 있습니다. 부하 분산 장치 뒤의 웹 서버에서 암호화와 관련된 프로세서 오버헤드가 발생하지 않으므로 이는 “TLS 오프로드”로 참조됩니다. 요청을 보다 신속하게 제공할 수 있으므로 이를 "SSL 오프로드"라고 합니다.
* URL 기반 콘텐츠 라우팅. 이 기능을 통해 부하 분산 장치가 대상 URL에 기반하여 연결을 전달할 위치를 결정할 수 있습니다. IP 주소에 기반하여 부하 분산 결정을 내리는 솔루션보다 훨씬 더 많은 유연성을 제공합니다.

자세한 정보:

* [Application Gateway 개요](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>네트워크 수준 부하 분산

HTTP 기반 부하 분산과는 달리, 네트워크 수준 부하 분산은 IP 주소 및 포트(TCP 또는 UDP) 번호를 기반으로 결정을 내립니다.
Azure Load Balancer를 사용하여 Azure에서 네트워크 수준 부하 분산의 이점을 얻을 수 있습니다. Load Balancer의 몇 가지 주요 특징은 다음과 같습니다.

* IP 주소와 포트 번호에 기반한 네트워크 수준 부하 분산
* 모든 애플리케이션 계층 프로토콜에 대한 지원
* Azure 가상 머신 및 클라우드 서비스 역할 인스턴스에 대한 부하 분산
* 인터넷 연결(외부 부하 분산) 및 비-인터넷 연결(내부 부하 분산) 애플리케이션과 가상 머신 모두에 사용할 수 있음
* 부하 분산 장치 뒤에서 사용할 수 없게 되는 서비스가 있는지 확인하기 위해 사용하는 엔드포인트 모니터링

자세한 정보:

* [여러 가상 머신 또는 서비스 간의 인터넷 연결 부하 분산 장치](../../load-balancer/load-balancer-overview.md)
* [내부 부하 분산 장치 개요](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>전역 부하 분산

일부 조직에서는 가능한 가장 높은 수준의 가용성을 원합니다. 이 목표에 도달하는 한 가지 방법은 전 세계적으로 분산된 데이터 센터에서 애플리케이션을 호스팅하는 것입니다. 전 세계적으로 위치한 데이터 센터에서 애플리케이션을 호스팅할 경우 지역 전체가 사용할 수 없게 되어도 애플리케이션은 계속 실행할 수 있습니다.

이 부하 분산 전략을 사용하면 성능 이점도 얻을 수 있습니다. 서비스에 대한 요청을 해당 요청을 하는 디바이스에 가장 가까운 데이터 센터로 전달할 수 있습니다.

Azure에서는 Azure Traffic Manager를 사용하여 글로벌 부하 분산의 이점을 얻을 수 있습니다.

자세한 정보:

* [Traffic Manager란?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>이름 확인

이름 확인은 Azure에서 호스팅하는 모든 서비스에 대해 중요한 기능입니다. 보안 관점에서 이름 확인 기능이 손상되면 공격자가 사용자의 사이트에서 공격자의 사이트로 요청을 리디렉션할 수 있습니다. 보안 이름 확인은 사용자의 모든 클라우드 호스팅 서비스의 요구 사항입니다.

해결해야 할 두 가지 유형의 이름 확인이 있습니다.

* 내부 이름 확인. 가상 네트워크, 온-프레미스 네트워크 또는 둘 다에 있는 서비스에서 사용됩니다. 내부 이름 확인에 사용되는 이름은 인터넷을 통해 액세스할 수 없습니다. 최적의 보안을 위해 외부 사용자가 내부 이름 확인 스킴에 액세스하지 않게 하는 것이 중요합니다.
* 외부 이름 확인. 온-프레미스 네트워크와 가상 네트워크 외부의 사용자 및 디바이스에서 사용됩니다. 인터넷에 표시되며 사용자의 클라우드 기반 서비스에 직접 연결하는 데 사용되는 이름입니다.

내부 이름 확인에는 두 가지 옵션이 있습니다.

* 가상 네트워크 DNS 서버. 새 가상 네트워크를 만들면 사용자에 대한 DNS 서버가 만들어집니다. 이 DNS 서버로 해당 가상 네트워크에 있는 머신의 이름을 확인할 수 있습니다. 이 DNS 서버는 구성할 수 없고 Azure 패브릭 관리자가 관리하므로 이름 확인 솔루션의 보안을 유지하는 데 도움이 될 수 있습니다.
* 자체 DNS 서버 가져오기. 직접 선택한 DNS 서버를 가상 네트워크에 배치할 수 있습니다. 이 DNS 서버는 Active Directory 통합 DNS 서버, 또는 Azure Marketplace에서 확보할 수 있는 Azure 파트너가 제공하는 전용 DNS 서버 솔루션이 될 수 있습니다.

자세한 정보:

* [Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)
* [가상 네트워크에서 사용하는 DNS 서버 관리](../../virtual-network/manage-virtual-network.md#change-dns-servers)

외부 이름 확인에는 두 가지 옵션이 있습니다.

* 자체 외부 DNS 서버 온-프레미스 호스팅
* 서비스 공급자를 통해 자체 외부 DNS 서버 호스팅

많은 대규모 조직에서는 자체 DNS 서버 온-프레미스를 호스팅합니다. 이를 수행할 수 있는 네트워킹 전문성과 글로벌 입지가 있기 때문에 가능합니다.

대부분 경우에 서비스 공급자를 통해 DNS 이름 확인 서비스를 호스팅하는 것이 좋습니다. 이러한 서비스 공급자는 네트워크 전문성과 글로벌 입지를 보유하고 있으므로 사용자의 이름 확인 서비스에 대한 매우 높은 가용성을 보장합니다. 이름 확인 서비스에 장애가 발생할 경우 아무도 사용자의 인터넷 연결 서비스에 연결할 수 없게 되므로, 가용성은 DNS 서비스에 있어 중요합니다.

Azure는 Azure DNS의 형태로 가용성과 성능이 뛰어난 외부 DNS 솔루션을 제공합니다. 이 외부 이름 확인 솔루션은 전 세계 Azure DNS 인프라를 활용합니다. 이를 통해 다른 Azure 서비스와 같은 자격 증명, API, 도구 및 청구를 사용하여 Azure에서 도메인을 호스팅할 수 있습니다. Azure이 일부로, 플랫폼에 구축된 강력한 보안 제어를 상속하기도 합니다.

자세한 정보:

* [Azure DNS 개요](../../dns/dns-overview.md)
* [Azure DNS 프라이빗 영역](../../dns/private-dns-overview.md)을 사용하면 사용자 지정 DNS 솔루션을 추가할 필요 없이 자동으로 할당된 이름보다는 Azure 리소스에 대한 프라이빗 DNS 이름을 구성할 수 있습니다.

## <a name="perimeter-network-architecture"></a>경계 네트워크 아키텍처

많은 대규모 조직에서는 경계 네트워크를 사용하여 네트워크를 분할하고 인터넷과 자체 서비스 사이에 완충 지대를 만듭니다. 네트워크의 경계 부분은 낮은 수준의 보안 영역으로 간주되며 이 네트워크 세그먼트에는 고가의 자산이 배치되지 않습니다. 일반적으로 네트워크 보안 디바이스에는 경계 네트워크 세그먼트의 네트워크 인터페이스가 사용됩니다. 다른 네트워크 인터페이스는 인터넷에서 인바운드 연결을 수락하는 가상 머신 및 서비스가 있는 네트워크에 연결됩니다.

다양한 방법으로 경계 네트워크를 설계할 수 있습니다. 경계 네트워크 배포 및 사용할 경계 네트워크 유형에 대한 결정은 네트워크 보안 요구 사항에 따라 달라집니다.

자세한 정보:

* [Microsoft Cloud Services 및 네트워크 보안](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS 보호

DDoS(배포된 서비스 거부) 공격은 고객이 애플리케이션을 클라우드로 전환하게 만드는 가장 큰 가용성 및 보안 문제 중 일부입니다. DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 생성합니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.
Microsoft에서는 Azure 플랫폼의 일부로 **기본** 으로 알려진 DDoS 보호를 제공합니다. 이는 무료로 제공되며, 일반적인 네트워크 수준 공격에 대한 Always On 모니터링 및 실시간 완화를 포함합니다. **기본** DDoS 보호에 포함된 보호 외에도 **표준** 옵션을 사용하도록 설정할 수 있습니다. DDoS Protection 표준 기능에는 다음이 포함됩니다.

* **네이티브 플랫폼 통합:** 기본적으로 Azure에 통합됩니다. Azure Portal을 통해 구성을 포함합니다. DDoS Protection 표준은 사용자의 리소스 및 리소스 구성을 인식합니다.
* **턴키 보호:** 간소화된 구성으로 DDoS Protection 표준이 사용되는 즉시 가상 네트워크의 모든 리소스를 바로 보호할 수 있습니다. 작업 또는 사용자 정의가 필요하지 않습니다. DDoS Protection 표준은 공격이 감지되는 즉시 자동으로 공격을 완화시킵니다.
* **트래픽 항시 모니터링:** DDoS 공격의 징후를 찾기 위해 애플리케이션 트래픽 패턴이 24시간 매일 모니터링됩니다. 보호 정책이 초과되면 완화가 수행됩니다.
* **공격 완화 보고서** 공격 완화 보고서는 집계된 네트워크 데이터 흐름 데이터를 사용하여 리소스를 목표로 하는 공격에 대한 자세한 정보를 제공합니다.
* **공격 완화 흐름 로그** 공격 완화 흐름 로그를 사용하면 활성 DDoS 공격 중에 삭제된 트래픽, 전달된 트래픽 및 기타 공격 데이터를 거의 실시간으로 검토할 수 있습니다.
* **적응형 튜닝:** 지능형 트래픽 프로파일링으로 시간별 애플리케이션 트래픽을 학습하고, 사용자의 서비스에 가장 적합한 프로필을 업데이트합니다. 트래픽이 시간이 지남에 따라 변경되면서 프로필이 조정됩니다. 계층 3~계층 7 보호: 웹 애플리케이션 방화벽과 함께 사용될 경우 전체 스택 DDoS 보호를 제공합니다.
* **광범위한 완화 규모:** 가장 큰 규모로 알려진 DDoS 공격으로부터 시스템을 보호할 수 있는 글로벌 역량으로 60가지 공격을 완화할 수 있습니다.
* **공격 메트릭:** Azure Monitor를 통해 각 공격으로부터 요약된 메트릭에 액세스할 수 있습니다.
* **공격 경고:** 기본 제공되는 공격 메트릭을 사용하여 공격 시작 및 중지 시와 공격이 진행되는 동안 경고를 구성할 수 있습니다. 경고는 Microsoft Azure Monitor 로그, Splunk, Azure Storage, 메일 및 Azure Portal과 같은 작업 소프트웨어에 통합됩니다.
* **비용 보장:** 문서화된 DDoS 공격에 대한 데이터 전송 및 애플리케이션 확장 서비스 크레딧이 제공됩니다.
* **DDoS 빠른 응답** DDoS Protection 표준 고객은 이제 활성 공격 중에 빠른 응답 팀에 액세스할 수 있습니다. DRR은 공격 조사, 공격 중 사용자 지정 완화 및 공격 후 분석에 도움이 될 수 있습니다.


자세한 정보:

* [DDOS 보호 개요](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Azure Front Door 서비스를 사용하여 웹 트래픽에 대한 전역 라우팅을 정의, 관리 및 모니터링할 수 있습니다. 최상의 성능 및 고가용성을 위해 트래픽 라우팅을 최적화합니다. Azure Front Door를 사용하면 액세스 제어에 대한 사용자 지정 웹 애플리케이션 방화벽(WAF) 규칙을 작성하여 클라이언트 IP 주소, 국가 코드 및 http 매개 변수를 기준으로 HTTP/HTTPS 워크로드 만료를 방지할 수 있습니다. 또한 Front Door는 악의적인 봇 트래픽에 대해 속도 제한 규칙을 만들 수 있도록 하며, TLS 오프로딩 및 HTTP/HTTPS 기준 요청, 애플리케이션 계층 처리를 포함합니다.

Front Door 플랫폼 자체는 Azure DDoS Protection Basic으로 보호됩니다. 추가 보호를 위해 VNET에서 Azure DDoS Protection Standard를 활성화하고, 자동 튜닝 및 완화를 통해 네트워크 계층(TCP/UDP) 공격으로부터 리소스를 보호할 수 있습니다. Front Door는 계층 7 역방향 프록시이며, 웹 트래픽만 백 엔드 서버를 통과하도록 허용하고 기본적으로 다른 종류의 트래픽은 차단합니다.

자세한 정보:

* 전체 Azure Front Door 기능 집합에 대한 자세한 내용을 보려면 [Azure Front Door 개요](../../frontdoor/front-door-overview.md)를 검토할 수 있습니다.

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager는 트래픽을 전 세계 Azure 지역의 서비스에 적절하게 분산하는 한편, 고가용성과 빠른 응답성을 제공하는 DNS 기반 트래픽 부하 분산 장치입니다. Traffic Manager는 DNS를 사용하여 클라이언트 요청을 트래픽 라우팅 메서드 및 엔드포인트의 상태를 기반으로 가장 적절한 서비스 엔드포인트로 리디렉션합니다. 엔드포인트는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다. Traffic Manager는 엔드포인트를 모니터링하고 사용할 수 없는 엔드포인트로 트래픽을 전송하지 않습니다.

자세한 정보:

* [Azure Traffic Manager 개요](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>감사 및 위협 검색

Azure에서는 초기 검색, 모니터링, 네트워크 트래픽 수집 및 검토 기능으로 이 주요 영역에서 사용자를 지원합니다.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher는 문제 해결을 지원할 수 있으며, 보안 문제 식별에 도움이 되는 완전히 새로운 도구 집합을 제공합니다.

[보안 그룹 보기](../../network-watcher/network-watcher-security-group-view-overview.md)는 Virtual Machines의 감사 및 보안 준수를 도와줍니다. 이 기능을 사용하여 조직에서 정의한 기준 정책을 각 VM에 적용되는 규칙과 비교하는 프로그래밍 방식의 감사를 수행할 수 있습니다. 그러면 모든 구성 드리프트를 식별하는 데 도움이 됩니다.

[패킷 캡처](../../network-watcher/network-watcher-packet-capture-overview.md)를 사용하면 가상 머신에서 네트워크 트래픽을 캡처할 수 있습니다. 네트워크 통계를 수집하고 애플리케이션 문제를 해결할 수 있으며, 이는 네트워크 침입에 대한 조사에 매우 유용할 수 있습니다. 또한 이 기능을 Azure Functions와 함께 사용하여 특정 Azure 경고에 대한 응답으로 네트워크 캡처를 시작할 수 있습니다.

Network Watcher 및 사용자 실험실에서 몇 가지 기능 테스트를 시작하는 방법에 대한 자세한 내용은 [Azure Network Watcher 모니터링 개요](../../network-watcher/network-watcher-monitoring-overview.md)를 참조하세요.

> [!NOTE]
> 이 서비스의 가용성 및 상태에 대한 최신 알림을 보려면 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=network-watcher)를 참조하세요.

### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

클라우드 용 Microsoft Defender는 위협을 예방, 감지 및 대응 하는 데 도움이 되며 Azure 리소스의 보안에 대 한 향상 된 가시성과 제어를 제공 합니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 다양한 보안 솔루션 집합에서 작동합니다.

클라우드 용 Defender는 다음을 통해 네트워크 보안을 최적화 하 고 모니터링 하는 데 도움이 됩니다.

* 네트워크 보안 권장 사항 제공
* 네트워크 보안 구성의 상태 모니터링
* 엔드포인트 및 네트워크 수준에서 네트워크 기반 위협에 대해 경고

자세한 정보:

* [클라우드 용 Microsoft Defender 소개](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>가상 네트워크 TAP

Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 네트워크 가상 어플라이언스 파트너를 통해 제공됩니다. 동일한 가상 네트워크 TAP 리소스를 사용하여 동일하거나 다른 구독의 여러 네트워크 인터페이스에서 트래픽을 집계할 수 있습니다.

자세한 정보:

* [가상 네트워크 TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>로깅

네트워크 수준에서 로깅은 네트워크 보안 시나리오의 핵심 기능입니다. Azure에서는 NSG에 대해 획득한 정보를 기록하여 네트워크 수준 로깅 정보를 얻을 수 있습니다. NSG 로깅을 사용하여 다음에서 정보를 얻습니다.

* [활동 로그](../../azure-monitor/essentials/platform-logs-overview.md). 이러한 로그를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다. 이러한 로그는 기본적으로 사용하도록 설정되며 Azure Portal 내에서 사용할 수 있습니다. 이전에는 이러한 로그를 감사 로그 또는 작업 로그라고 했습니다.
* 이벤트 로그. 이러한 로그는 적용된 NSG 규칙에 대한 정보를 제공합니다.
* 카운터 로그. 이러한 로그는 각 NSG 규칙이 트래픽을 허용하거나 거부하는 데 적용된 횟수를 알려 줍니다.

또한 강력한 시각화 도구인 [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)를 사용하여 이러한 로그를 보고 분석할 수도 있습니다.
자세한 정보:

* [NSG(네트워크 보안 그룹)에 대한 Azure Monitor 로그](../../virtual-network/virtual-network-nsg-manage-log.md)
