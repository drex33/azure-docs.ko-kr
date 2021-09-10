---
title: Azure VPN Gateway FAQ
description: VPN Gateway 프레미스 간 연결, 하이브리드 구성 연결 및 가상 네트워크 게이트웨이와 관련하여 자주 묻는 질문에 대해 알아보세요. 이 FAQ에는 지점 및 사이트 간, 사이트 간 및 VNet 간 구성 설정에 대한 포괄적인 정보가 포함되어 있습니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: yushwang
ms.openlocfilehash: 0551cf435b732b621ad5493914251afd67f18882
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707130"
---
# <a name="vpn-gateway-faq"></a>VPN Gateway FAQ

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>가상 네트워크에 연결

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>다양한 Azure 지역에서 가상 네트워크를 연결할 수 있습니까?

예. 실제로 지역 제약 조건이 없습니다. 가상 네트워크를 동일한 지역 또는 다른 Azure 지역의 다른 가상 네트워크에 연결할 수 있습니다.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>다른 구독의 가상 네트워크를 연결할 수 있습니까?

예.

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>VPN Gateway를 구성할 때 VNet에서 프라이빗 DNS 서버를 지정할 수 있나요?

VNet을 만들 때 DNS 서버를 지정한 경우 VPN Gateway는 지정한 DNS 서버를 사용합니다. DNS 서버를 지정하는 경우 DNS 서버가 Azure에 필요한 도메인 이름을 확인할 수 있는지 확인합니다.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>단일 가상 네트워크에서 여러 사이트에 연결할 수 있습니까?

Windows PowerShell 및 Azure REST API를 사용하여 여러 사이트에 연결할 수 있습니다. [다중 사이트 및 VNet 간 연결](#V2VMulti) FAQ 섹션을 참조하세요.

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>VPN 게이트웨이를 활성-활성으로 설정기 위한 추가 비용이 있나요?

아니요.

### <a name="what-are-my-cross-premises-connection-options"></a>내 프레미스 간 연결 옵션은 무엇입니까?

다음 프레미스 간 연결을 지원합니다.

* 사이트 간 – IPsec 통한 VPN 연결(IKE v1 및 IKE v2). 이 연결 유형은 VPN 디바이스 또는 RRAS가 필요합니다. 자세한 내용은 [사이트 간](./tutorial-site-to-site-portal.md)을 참조하세요.
* 지점 및 사이트 간 - SSTP(Secure Socket Tunneling Protocol) 또는 IKE v2를 통한 VPN 연결. 이 연결에는 VPN 디바이스가 필요하지 않습니다. 자세한 내용은 [지점 및 사이트 간](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요.
* VNet 간 - 이 유형의 연결은 사이트 간 구성과 동일합니다. VNet 간 연결은 IPsec를 통한 VPN 연결(IKE v1 및 IKE v2)입니다. VPN 디바이스가 필요하지 않습니다. 자세한 내용은 [VNet 간](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조하세요.
* 다중 사이트 - 가상 네트워크에 여러 온-프레미스 사이트를 연결할 수 있는 사이트 간 구성의 변형입니다. 자세한 내용은 [다중 사이트](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)를 참조하세요.
* ExpressRoute - ExpressRoute는 공용 인터넷을 통한 VPN 연결이 아닌 WAN에서 Azure에 대한 프라이빗 연결입니다. 자세한 내용은 [ExpressRoute 기술 개요](../expressroute/expressroute-introduction.md) 및 [ExpressRoute FAQ](../expressroute/expressroute-faqs.md)를 참조하세요.

VPN Gateway 연결에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>사이트 간 연결과 지점 및 사이트 간 연결의 차이점은 무엇입니까?

**사이트 간**(IPsec/IKE VPN 터널) 구성은 온-프레미스 위치와 Azure 사이에 있습니다. 즉, 라우팅 및 사용 권리를 어떻게 구성하는지에 따라 프레미스에 있는 원하는 컴퓨터에서 가상 네트워크 내에 있는 원하는 가상 머신 또는 역할 인스턴스에 연결할 수 있습니다. 이 연결은 항상 사용할 수 있는 프레미스 간 연결에 유용한 옵션이며 하이브리드 구성에 적합합니다. 이 연결 유형에서는 네트워크의 가장자리에 배포되어야 하는 IPsec VPN 어플라이언스(하드웨어 디바이스 또는 소프트 어플라이언스)를 사용합니다. 이 형식의 연결을 만들려면 외부 연결 IPv4 주소가 있어야 합니다.

**지점 및 사이트 간**(SSTP를 통한 VPN) 구성을 사용하면 모든 위치의 단일 컴퓨터에서 가상 네트워크에 있는 모든 컴퓨터에 연결할 수 있습니다. Windows 인박스 VPN 클라이언트를 사용합니다. 지점 및 사이트 간 구성 중에 가상 네트워크 내의 가상 머신 또는 역할 인스턴스에 컴퓨터를 연결할 수 있도록 해주는 설정이 포함된 인증서 및 VPN 클라이언트 구성 패키지를 설치합니다. 가상 네트워크에 연결하려고 하지만 온-프레미스에 없는 경우에 유용합니다. 사이트 간 연결에 필요한 VPN 하드웨어 또는 외부 연결 IPv4 주소에 액세스할 수 없을 때 유용한 옵션입니다.

게이트웨이에 경로 기반 VPN 유형을 사용하여 사이트 간 연결을 만들기만 하면 사이트 간 연결과 지점 및 사이트 간 연결을 동시에 사용하도록 가상 네트워크를 구성할 수 있습니다. 경로 기반 VPN 유형은 클래식 배포 모델에서 동적 게이트웨이라고 합니다.

## <a name="privacy"></a><a name="privacy"></a>개인 정보

### <a name="does-the-vpn-service-store-or-process-customer-data"></a>VPN 서비스에서 고객 데이터를 저장하거나 처리하나요?

아니요.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>가상 네트워크 게이트웨이

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>VPN Gateway는 가상 네트워크 게이트웨이인가요?

VPN Gateway는 가상 네트워크 게이트웨이의 유형입니다. VPN Gateway는 공용 연결을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송합니다. 또한 VPN 게이트웨이를 사용하여 가상 네트워크 간에 트래픽을 전송할 수 있습니다. VPN Gateway를 만드는 경우 -GatewayType 값 'Vpn'을 사용합니다. 자세한 내용은 [VPN Gateway 구성 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.

### <a name="what-is-a-policy-based-static-routing-gateway"></a>정책 기반(고적 라우팅) 게이트웨이란?

정책 기반 게이트웨이는 정책 기반 VPN을 구현합니다. 정책 기반 VPN은 온-프레미스 네트워크와 Azure VNet 간의 주소 접두사의 조합에 따라 IPsec 터널을 통해 패킷을 암호화하고 전달합니다. 정책 또는 트래픽 선택기는 일반적으로 VPN 구성에서 액세스 목록으로 정의됩니다.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>경로 기반(동적 라우팅) 게이트웨이란?

경로 기반 게이트웨이는 경로 기반 VPN을 구현합니다. 경로 기반 VPN은 IP 전달 또는 라우팅 테이블에서 “경로"를 사용하여 패킷을 해당 터널 인터페이스에 전달합니다. 그런 다음 터널 인터페이스는 터널로 들어오는 터널에서 나가는 패킷을 암호화하거나 암호 해독합니다. 경로 기반 VPN에 대한 정책 또는 트래픽 선택기는 임의 또는 와일드카드로 구성됩니다.

### <a name="can-i-specify-my-own-policy-based-traffic-selectors"></a>자체 정책 기반 트래픽 선택기를 지정할 수 있나요?

예, 트래픽 선택기는 [New-AzIpsecTrafficSelectorPolicy](/powershell/module/az.network/new-azipsectrafficselectorpolicy) PowerShell 명령을 통해 연결의 *trafficSelectorPolicies* 특성을 통해 정의할 수 있습니다. 지정된 트래픽 선택기를 적용하려면 [정책 기반 트래픽 선택기 사용](vpn-gateway-connect-multiple-policybased-rm-ps.md#enablepolicybased) 옵션이 사용하도록 설정되어 있는지 확인합니다.

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>정책 기반 VPN Gateway를 경로 기반으로 업데이트할 수 있나요?

아니요. 게이트웨이 형식은 정책 기반에서 경로 기반으로 변경하거나 경로 기반에서 정책 기반으로 변경할 수 없습니다. 게이트웨이 형식을 변경하려면 게이트웨이를 삭제하고 다시 만들어야 합니다. 이 프로세스는 60분 정도 걸립니다. 새 게이트웨이를 만들 때 원래 게이트웨이의 IP 주소를 유지할 수 없습니다.

1. 게이트웨이와 연결된 모든 연결을 삭제합니다.

1. 다음 문서 중 하나를 사용하여 게이트웨이를 삭제합니다.

   * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell - 클래식](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. 원하는 게이트웨이 형식을 사용하여 새 게이트웨이를 만든 다음, VPN 설치를 완료합니다. 단계는 [사이트 간 자습서](./tutorial-site-to-site-portal.md#VNetGateway)를 참조하세요.

### <a name="do-i-need-a-gatewaysubnet"></a>'GatewaySubnet'이 필요한가요?

예. 게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 IP 주소를 포함합니다. 가상 네트워크 게이트웨이를 구성하려면 VNet의 게이트웨이 서브넷을 만들어야 합니다. 모든 게이트웨이 서브넷이 제대로 작동하려면 이름을 'GatewaySubnet'으로 지정해야 합니다. 게이트웨이 서브넷에 다른 이름을 지정하지 않습니다. 게이트웨이 서브넷에 VM 또는 다른 항목을 배포하지 않습니다.

게이트웨이 서브넷을 만드는 경우 서브넷이 포함하는 IP 주소의 수를 지정합니다. 게이트웨이 서브넷의 IP 주소는 게이트웨이 서비스에 할당됩니다. 일부 구성은 게이트웨이 서비스에 다른 구성보다 더 많은 IP 주소가 할당되어야 합니다. 이후 성장 및 새로운 연결 구성이 추가될 가능성에 대비하여 게이트웨이 서브넷에 IP 주소가 충분히 포함되어 있는지 확인하는 것이 좋습니다. 따라서 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 게이트웨이 서브넷을 /27 이상으로 만드는 것이 좋습니다(/27, /26, /25 등). 만들려는 구성에 대한 요구 사항을 검토하고 가지고 있는 게이트웨이 서브넷이 그러한 요구 사항을 충족하는지 확인하세요.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Virtual Machines 또는 역할 인스턴스를 내 게이트웨이 서브넷에 배포할 수 있습니까?

아니요.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>VPN 게이트웨이 IP 주소를 만들기 전에 내 VPN 게이트웨이 IP 주소를 가져올 수 있나요?

영역 중복과 영역 게이트웨이(이름에 _AZ_ 가 있는 게이트웨이 SKU)는 모두 _표준 SKU_ Azure 공용 IP 리소스를 사용합니다. Azure Standard SKU 공용 IP 리소스는 정적 할당 방법을 사용해야 합니다. 따라서 사용하려는 표준 SKU 공용 IP 리소스를 만드는 즉시 VPN 게이트웨이의 공용 IP 주소가 생깁니다.

비영역 중복 및 비영역 게이트웨이(이름에 _AZ_ 가 _없는_ 게이트웨이 SKU)의 경우 VPN 게이트웨이 IP 주소를 만들기 전에 가져올 수 없습니다. VPN 게이트웨이를 삭제하고 다시 만드는 경우에만 IP 주소가 변경됩니다.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>VPN Gateway에 고정 공용 IP 주소를 요청할 수 있나요?

위에 설명한 대로, 영역 중복과 영역 게이트웨이(이름에 _AZ_ 가 있는 게이트웨이 SKU)는 모두 _표준 SKU_ Azure 공용 IP 리소스를 사용합니다. Azure Standard SKU 공용 IP 리소스는 정적 할당 방법을 사용해야 합니다.

비영역 중복 및 비영역 게이트웨이(이름에 _AZ_ 가 _없는_ 게이트웨이 SKU)의 경우 동적 IP 주소 할당만 지원됩니다. 하지만 IP 주소가 VPN Gateway에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 VPN Gateway IP 주소가 변경됩니다. VPN 게이트웨이 공용 IP 주소는 VPN 게이트웨이의 크기 조정, 다시 설정 또는 기타 내부 유지 관리와 업그레이드를 완료할 때 변경되지 않습니다.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>내 VPN 터널을 어떻게 인증합니까?

Azure VPN은 PSK(미리 공유한 키) 인증을 사용합니다. VPN 터널을 만들 때 PSK(미리 공유한 키)를 생성합니다. Set Pre-Shared Key PowerShell cmdlet 또는 REST API를 사용하여 자동 생성된 PSK를 자체 PSK로 변경할 수 있습니다.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>사전 공유 키 설정 API를 사용하여 정책 기반(고정 라우팅) 게이트웨이 VPN을 구성할 수 있습니까?

예, 사전 공유 키 설정 API 및 PowerShell cmdlet을 Azure 정책 기반(고정) VPN 및 경로 기반(동적) 라우팅 VPN을 구성하는 데 사용할 수 있습니다.

### <a name="can-i-use-other-authentication-options"></a>다른 인증 옵션을 사용할 수 있습니까?

인증에는 PSK(미리 공유한 키)를 사용하도록 제한됩니다.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>VPN 게이트웨이를 통해 전송되는 트래픽을 지정하려면 어떻게 해야 합니까?

#### <a name="resource-manager-deployment-model"></a>리소스 관리자 배포 모델

* PowerShell: "AddressPrefix"를 사용하여 로컬 네트워크 게이트웨이에 대한 트래픽을 지정합니다.
* Azure Portal: [로컬 네트워크 게이트웨이 > 구성> 주소 공간]으로 이동합니다.

#### <a name="classic-deployment-model"></a>클래식 배포 모델

* Azure Portal: 클래식 가상 네트워크 > VPN 연결 > 사이트 간 VPN 연결 > 로컬 사이트 이름 > 로컬 사이트 > 클라이언트 주소 공간으로 이동합니다.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>내 VPN 연결에서 NAT-T를 사용할 수 있나요?

예, NAT-T(NAT traversal)가 지원됩니다. Azure VPN Gateway는 IPsec 터널과 주고받는 내부 패킷에서 NAT 같은 기능을 수행하지 않습니다.  이 구성에서 온-프레미스 디바이스가 IPSec 터널을 시작하는지 확인하세요.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Azure에서 내 VPN 서버를 설정하여 온-프레미스 네트워크에 연결하는 데 사용할 수 있습니까?

예, Azure Marketplace에서 또는 사용자 VPN 라우터를 만들어서 Azure에 사용자 VPN 게이트웨이 또는 서버를 배포할 수 있습니다. 온-프레미스 네트워크와 가상 네트워크 서브넷 간에 트래픽이 적절하게 라우팅되도록 하려면 가상 네트워크에서 사용자 정의 경로를 구성해야 합니다.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>내 가상 네트워크 게이트웨이에서 특정 포트가 열리는 이유는?

Azure 인프라 통신을 위해 필요합니다. Azure 인증서에 의해 보호(잠김)됩니다. 적절한 인증서가 없는 경우 해당 게이트웨이 고객을 포함하여 외부 엔터티는 해당 엔드포인트에 어떤 영향도 미칠 수 없습니다.

가상 네트워크 게이트웨이는 기본적으로 고객 프라이빗 네트워크에 연결하는 하나의 NIC와 공용 네트워크를 연결하는 하나의 NIC를 갖춘 멀티홈 디바이스입니다. Azure 인프라 엔터티는 규정 준수 이유로 고객 프라이빗 네트워크에 연결할 수 없으므로 인프라 통신용 공용 엔드포인트를 이용해야 합니다. 공용 엔드포인트는 Azure 보안 감사에서 정기적으로 검색됩니다.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>게이트웨이 유형, 요구 사항 및 처리량에 대한 자세한 내용

자세한 내용은 [VPN Gateway 구성 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>사이트 간 연결 및 VPN 디바이스

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>VPN 디바이스를 선택할 때 고려할 사항은 무엇입니까?

디바이스 공급업체와 협력하여 표준 사이트 간 VPN 디바이스의 유효성을 검사했습니다. 알려진 호환 VPN 디바이스, 해당 구성 지침 또는 샘플 및 디바이스 사양 목록은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md) 문서에서 확인할 수 있습니다. 호환하는 것으로 알려진 목록의 디바이스 제품군에 포함된 모든 디바이스는 Virtual Network에서 작동합니다. VPN 디바이스를 구성하려면 적절한 디바이스 제품군에 해당하는 디바이스 구성 샘플 또는 링크를 참조하세요.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>VPN 디바이스 구성 설정을 어디서 찾을 수 있나요?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>VPN 디바이스 구성 샘플을 편집하려면 어떻게 하나요?

디바이스 구성 샘플을 편집하는 방법에 대한 정보는 [샘플 편집](vpn-gateway-about-vpn-devices.md#editing)을 참조하세요.

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>IPsec 및 IKE 매개 변수를 어디서 찾을 수 있나요?

IPsec/IKE 매개 변수는 [매개 변수](vpn-gateway-about-vpn-devices.md#ipsec)를 참조하세요.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>트래픽이 유휴 상태일 때 정책 기반 VPN 터널이 다운되는 이유는 무엇인가요?

정책 기반(정적 라우팅이라고도 함) VPN 게이트웨이에서 예상되는 동작입니다. 터널의 트래픽이 5분 이상 유휴 상태인 경우 터널은 삭제됩니다. 하지만 트래픽이 어느 방향으로든 흐름이 시작되면 즉시 터널이 다시 설정됩니다.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>소프트웨어 VPN을 사용하여 Azure에 연결할 수 있습니까?

사이트 사이 프레미스 간 구성에 대해 Windows Server 2012 RRAS(라우팅 및 원격 액세스) 서버를 지원합니다.

다른 소프트웨어 VPN 솔루션은 업계 표준 IPsec 구현을 따르는 경우에만 Microsoft 게이트웨이에 사용할 수 있습니다. 구성 및 지원 지침은 소프트웨어 공급 업체에 문의하세요.

## <a name="point-to-site---certificate-authentication"></a><a name="P2S"></a>지점 및 사이트 간 - 인증서 인증

이 섹션은 Resource Manager 배포 모델에 적용됩니다.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site---radius-authentication"></a><a name="P2SRADIUS"></a>지점 및 사이트 간 - RADIUS 인증

이 섹션은 Resource Manager 배포 모델에 적용됩니다.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>VNet 간 연결 및 다중 사이트 연결

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>사이트 간 VPN 연결과 내 ExpressRoute 간의 라우팅을 사용으로 설정하려면 어떻게 할까요?

ExpressRoute에 연결된 분기와 사이트 간 VPN 연결에 연결된 분기 간의 라우팅을 사용하려면 [Azure Route Server](../route-server/expressroute-vpn-support.md)를 설정해야 합니다.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Azure VPN 게이트웨이를 사용하여 온-프레미스 사이트 간에 또는 다른 가상 네트워크에 트래픽을 전송할 수 있습니까?

**리소스 관리자 배포 모델**<br>
예. 자세한 내용은 [BGP](#bgp) 섹션을 참조하세요.

**클래식 배포 모델**<br>
 Azure VPN 게이트웨이 통한 전송 트래픽은 클래식 배포 모델을 사용할 수 있지만 네트워크 구성 파일에서 정적으로 정의된 주소 공간의 영향을 받습니다. BGP는 클래식 배포 모델을 사용하는 Azure Virtual Networks 및 VPN 게이트웨이에서 아직 지원되지 않습니다. BGP를 사용하지 않고 전송 주소 공간을 수동으로 정의하면 오류가 발생하기 쉬우므로 사용하지 않는 것이 좋습니다.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure는 동일한 가상 네트워크의 모든 VPN 연결에 대해 동일한 IPsec/IKE 미리 공유한 키를 생성합니까?

아니요, 기본적으로 Azure는 VPN 연결마다 다른 미리 공유한 키를 생성합니다. 하지만 VPN Gateway 키 생성 REST API 또는 PowerShell cmdlet을 사용하여 원하는 키 값을 설정할 수 있습니다. 키는 인쇄 가능한 ASCII 문자여야 합니다.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>단일 가상 네트워크보다 더 많은 사이트 간 VPN을 사용하면 대역폭이 증가합니까?

아니요, 지점 및 사이트 간 VPN을 포함하여 모든 VPN 터널은 동일한 Azure VPN 게이트웨이 및 사용 가능한 대역폭을 공유합니다.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>다중 사이트 VPN을 사용하여 내 가상 네트워크와 온-프레미스 사이트 간에 여러 터널을 구성할 수 있습니까?

예, 하지만 두 터널의 BGP를 동일한 위치로 구성해야 합니다.

### <a name="does-azure-vpn-gateway-honor-as-path-prepending-to-influence-routing-decisions-between-multiple-connections-to-my-on-premises-sites"></a>Azure VPN Gateway는 내 온-프레미스 사이트에 대한 여러 연결 간의 라우팅 결정에 도움이 되도록 추가된 AS 경로를 적용하나요?

예. Azure VPN 게이트웨이는 BGP를 사용할 때 라우팅 결정을 내리는 데 도움이 되도록 AS 경로 추가를 적용합니다. BGP 경로 선택에서는 더 짧은 AS 경로가 우선 적용됩니다.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>여러 VPN 터널을 포함하는 내 가상 네트워크에서 지점 및 사이트 간 VPN을 사용할 수 있습니까?

예, 여러 온-프레미스 사이트 및 기타 가상 네트워크에 연결하는 VPN 게이트웨이에서 지정 및 사이트 간(P2S) VPN을 사용할 수 있습니다.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>IPsec VPN을 사용하는 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니까?

예, 지원됩니다. 자세한 내용은 [공존하는 ExpressRoute 및 사이트 간 VPN 연결 구성](../expressroute/expressroute-howto-coexist-classic.md)을 참조하세요.

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/IKE 정책

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP 및 라우팅

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>강제 터널링을 구성할 수 있나요?

예. [강제 터널링 구성](vpn-gateway-about-forced-tunneling.md)을 참조하세요.

## <a name="nat"></a><a name="nat"></a>NAT

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>크로스-프레미스 연결 및 VM

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>가상 컴퓨터가 가상 네트워크에 있고 프레미스 간 연결을 사용하는 경우 VM에 연결하려면 어떻게 해야 합니까?

몇 가지 옵션이 있습니다. VM에 RDP를 사용하도록 설정한 경우 개인 IP 주소를 사용하여 가상 머신에 연결할 수 있습니다. 이 경우 개인 IP 주소 및 연결하려는 포트(일반적으로 3389)를 지정합니다. 가상 머신에서 트래픽에 대한 포트를 구성해야 합니다.

동일한 가상 네트워크에 있는 다른 가상 머신의 개인 IP 주소를 통해 가상 머신에 연결할 수도 있습니다. 가상 네트워크의 외부 위치에서 연결할 경우 개인 IP 주소를 사용하여 가상 머신에 RDP할 수 없습니다. 예를 들어 지점 및 사이트 간 가상 네트워크를 구성하고 컴퓨터에서 연결을 설정하지 않은 경우 개인 IP 주소를 통해 가상 머신에 연결할 수 없습니다.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>내 가상 머신이 프레미스 간 연결을 사용하는 가상 네트워크에 포함된 경우 내 VM의 모든 트래픽이 해당 연결을 통해 이동됩니까?

아니요. 대상 IP가 지정된 가상 네트워크 로컬 네트워크 IP 주소 범위에 포함되는 트래픽만 가상 네트워크 게이트웨이를 통해 이동됩니다. 대상 IP가 가상 네트워크 내에 있는 트래픽은 가상 네트워크 내에 유지됩니다. 다른 트래픽은 부하 분산 장치를 통해 공용 네트워크에 전송되고, 강제 터널링을 사용하는 경우 Azure VPN 게이트웨이를 통해 전송됩니다.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>VM에 대한 RDP 연결 문제를 해결하려면 어떻게 하나요?

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>Virtual Network FAQ

[Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)에서 추가적인 가상 네트워크 정보를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.
* VPN Gateway 구성 설정에 대한 자세한 내용은 [VPN Gateway 구성 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요.

**“OpenVPN”은 OpenVPN Inc.의 상표입니다.**
