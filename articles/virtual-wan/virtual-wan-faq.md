---
title: Azure Virtual WAN FAQ | Microsoft Docs
description: Azure Virtual WAN 네트워크, 클라이언트, 게이트웨이, 디바이스, 파트너, 연결에 대해 자주 묻는 질문에 대한 답변을 참조하세요.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: troubleshooting
ms.date: 08/18/2021
ms.author: cherylmc
ms.openlocfilehash: c4c31314ca8e559748425518258e0eec965d9c09
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754430"
---
# <a name="virtual-wan-faq"></a>가상 WAN FAQ

### <a name="is-azure-virtual-wan-in-ga"></a>Azure Virtual WAN이 GA에 있나요?

예, Azure Virtual WAN은 GA(일반 공급)로 제공됩니다. 그러나 Virtual WAN은 몇 가지 기능과 시나리오로 구성됩니다. Virtual WAN에는 Microsoft가 미리 보기 태그를 적용하는 기능 또는 시나리오가 있습니다. 이러한 경우 특정 기능 또는 시나리오 자체는 미리 보기로 제공됩니다. 특정 미리 보기 기능을 사용하지 않는 경우 일반 GA 지원이 적용됩니다. 미리 보기 지원에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Azure Virtual WAN을 사용하려면 사용자에게 SD-WAN/VPN 디바이스를 사용하는 허브 및 스포크가 있어야 하나요?

Virtual WAN은 단일 창에 기본 제공되는 다양한 기능, 즉 사이트/사이트 간 VPN 연결, 사용자/P2S 연결, ExpressRoute 연결, Virtual Network 연결, VPN ExpressRoute 상호 연결, VNet 간 전이적 연결, 중앙 집중식 라우팅, Azure 방화벽 및 방화벽 관리자 보안, 모니터링, ExpressRoute 암호화 및 기타 다양한 기능을 제공합니다. Virtual WAN 사용을 시작하는 데에는 이러한 사용 사례 중 일부만 필요합니다. 하나의 사용 사례만으로도 시작할 수 있습니다.

Virtual WAN 아키텍처는 분기(VPN/SD-WAN 디바이스), 사용자(Azure VPN 클라이언트, openVPN 또는 IKEv2 클라이언트), ExpressRoute 회로, Virtual Networks가 가상 허브의 스포크 역할을 하는 허브 및 스포크 아키텍처이며, 스케일링 및 성능이 기본적으로 제공됩니다. 모든 허브가 표준 Virtual WAN에서 풀 메시로 연결되므로 사용자는 Microsoft 백본을 임의(모든 스포크) 연결에 쉽게 사용할 수 있습니다. SD-WAN/VPN 디바이스를 사용하는 허브 및 스포크의 경우 사용자는 Azure Virtual WAN 포털에서 수동으로 설정하거나 Virtual WAN 파트너 CPE(SD-WAN/VPN)를 사용하여 Azure에 대한 연결을 설정할 수 있습니다.

Virtual WAN 파트너는 디바이스 정보를 Azure로 내보내고, Azure 구성을 다운로드하고, Azure Virtual WAN 허브에 대한 연결을 설정하는 기능인 연결 자동화를 제공합니다. 지점 및 사이트 간/사용자 VPN 연결의 경우 [Azure VPN 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN 또는 IKEv2 클라이언트를 지원합니다.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Virtual WAN에서 완전한 메시형 허브를 사용하지 않도록 설정할 수 있나요?

Virtual WAN은 다음 두 가지 버전으로 제공됩니다. 기본 및 표준 기본 Virtual WAN에서는 허브가 메시되지 않습니다. 표준 Virtual WAN에서는 가상 WAN이 처음으로 설정될 때 허브가 메시되어 자동으로 연결됩니다. 사용자는 아무것도 할 필요가 없습니다. 또한 사용자는 메시된 허브를 획득하기 위해 이 기능을 해제 또는 설정할 필요가 없습니다. Virtual WAN은 모든 스포크(VNet, VPN 또는 ExpressRoute) 간의 트래픽을 조정하는 여러 라우팅 옵션을 제공합니다. 이를 통해 완전 메시형 허브를 간편하게 사용할 수 있을 뿐 아니라 트래픽을 필요한 대로 유연하게 라우팅할 수 있습니다.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Virtual WAN에서 가용성 영역 및 복원력은 어떻게 처리되나요?

Virtual WAN은 허브 내에서 사용할 수 있는 허브 및 서비스 컬렉션입니다. 사용자는 Virtual WAN을 필요한 만큼 사용할 수 있습니다. 가상 WAN 허브에는 VPN, Express 경로 등과 같은 여러 서비스가 있습니다. 이러한 각 서비스는 지역에서 가용성 영역를 지 원하는 경우 Azure 방화벽을 제외 하 고 Availabitlity 영역에 자동으로 배포 됩니다. 허브에 처음 배포한 후 지역이 가용성 영역으로 되면 사용자는 게이트웨이를 다시 만들 수 있으며, 이 경우 가용성 영역 배포가 트리거됩니다. 모든 게이트웨이는 허브에 활성-활성으로 프로비저닝되며, 이는 허브 내에서 복원력이 기본 제공된다는 의미입니다. 사용자는 여러 지역에서 복원력이 필요한 경우 여러 허브에 연결하면 됩니다. 

현재 azure 방화벽 관리자 포털,  [PowerShell](/powershell/module/az.network/new-azfirewall?view=azps-6.3.0#example-6--create-a-firewall-with-no-rules-and-with-availability-zones) 또는 CLI를 사용 하 여 가용성 영역을 지원 하도록 azure 방화벽을 배포할 수 있습니다. 현재는 가용성 영역 간에 배포 되도록 기존 방화벽을 구성할 수 있는 방법이 없습니다. Azure 방화벽을 삭제 하 고 다시 배포 해야 합니다. 

Virtual WAN은 개념적으로는 글로벌이지만, 실제 Virtual WAN 리소스는 Resource Manager 기반이며 지역 단위로 배포됩니다. 가상 WAN 지역 자체에 문제가 있는 경우 해당 가상 WAN의 모든 허브는 계속해서 있는 그대로 작동하지만, 사용자는 가상 WAN 지역을 사용할 수 있을 때까지 새 허브를 만들 수 없게 됩니다.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure Virtual WAN 사용자 VPN(지점 및 사이트 간)에서 지원하는 클라이언트는 무엇인가요?

Virtual WAN은 [Azure VPN 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN 클라이언트 또는 모든 IKEv2 클라이언트를 지원합니다. Azure VPN 클라이언트는 Azure AD 인증을 지원합니다. 최소 Windows 10 클라이언트 OS 버전 17763.0 이상이 필요합니다.  OpenVPN 클라이언트는 인증서 기반 인증을 지원할 수 있습니다. 게이트웨이에서 인증서 기반 인증을 선택하면 디바이스에 다운로드할 .ovpn* 파일이 표시됩니다. IKEv2는 인증서 및 RADIUS 인증을 모두 지원합니다. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>사용자 VPN(지점 및 사이트 간)의 경우 P2S 클라이언트 풀이 두 경로로 분할되는 이유는 무엇인가요?

각 게이트웨이에는 두 개의 인스턴스가 있습니다. 각 게이트웨이 인스턴스가 연결된 클라이언트에 대해 독립적으로 클라이언트 IP를 할당할 수 있도록 분할이 수행되고, 가상 네트워크로부터의 트래픽이 게이트웨이 간 인스턴스 홉을 방지하기 위해 올바른 게이트웨이 인스턴스로 다시 라우팅됩니다.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>P2S 클라이언트용 DNS 서버를 추가하려면 어떻게 하나요?

P2S 클라이언트용 DNS 서버를 추가하는 두 가지 옵션이 있습니다. 첫 번째 방법은 클라이언트 대신 게이트웨이에 사용자 지정 DNS 서버를 추가하는 것이며, 많은 사람들이 선호하는 방법입니다.

1. 다음 PowerShell 스크립트를 사용하여 사용자 지정 DNS 서버를 추가합니다. 환경에 맞게 값을 바꿉니다.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. 또는 Azure VPN 클라이언트를 Windows 10에 사용하는 경우 다운로드한 프로필 XML 파일을 수정하고 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** 태그를 추가한 후에 가져올 수 있습니다.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>사용자 VPN(지점 및 사이트 간)의 경우 지원되는 클라이언트 수는 어떻게 되나요?

각 사용자 VPN P2S 게이트웨이에는 두 가지 인스턴스가 있습니다. 각 인스턴스는 배율 단위가 변경됨에 따라 특정 개수의 연결을 지원합니다. 1~3 배율 단위는 연결 500개, 4~6 배율 단위는 연결 1,000개, 7~12 배율 단위는 연결 5,000개, 13~18 배율 단위는 연결 최대 10,000개를 지원합니다.

예를 들어 사용자가 1 배율 단위를 선택한다고 가정해 보겠습니다. 각 배율 단위는 배포된 활성-활성 게이트웨이를 의미하며, 각 인스턴스(이 경우 2)에서 최대 500개의 연결을 지원합니다. 게이트웨이당 500 * 2개 연결이 가능하므로, 이 배율 단위에 대해 500개 대신 1000개를 계획하는 것이 아닙니다. 권장 연결 수를 초과할 경우 추가 500개 연결이 중단될 수 있는 기간 동안 인스턴스를 처리해야 할 수도 있습니다. 또한 배율 단위를 확장 또는 축소하거나 VPN 게이트웨이에서 지점 및 사이트 간 구성을 변경하려는 경우에는 가동 중지 시간을 계획해야 합니다.

### <a name="what-are-virtual-wan-gateway-scale-units"></a>Virtual WAN 게이트웨이 배율 단위란?

배율 단위는 가상 허브에서 게이트웨이의 집계 처리량을 선택하기 위해 정의된 단위입니다. VPN의 1 배율 단위 = 500Mbps ExpressRoute의 1개 확장 단위 = 2Gbps 예제: VPN의 10개 확장 단위는 500Mbps * 10 = 5Gbps를 의미합니다.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 가상 네트워크 게이트웨이(VPN Gateway)와 Azure Virtual WAN VPN Gateway의 차이는 무엇인가요?

Virtual WAN은 대규모 사이트 간 연결을 제공하며 처리량, 확장성 및 사용 편의성을 높여 줍니다. 사이트를 Virtual WAN VPN Gateway에 연결할 경우 게이트웨이 유형 'VPN'을 사용하는 일반 가상 네트워크 게이트웨이와는 다릅니다. 마찬가지로, ExpressRoute 회로를 Virtual WAN 허브에 연결할 경우 게이트웨이 유형 'ExpressRoute'를 사용하는 일반 가상 네트워크 게이트웨이와는 달리 ExpressRoute 게이트웨이에 다른 리소스를 사용합니다. 

Virtual WAN은 VPN과 ExpressRoute 모두에 최대 20Gbps의 집계 처리량을 지원합니다. Virtual WAN에는 또한 CPE 분기 디바이스 파트너의 에코시스템 연결을 위한 자동화 기능이 있습니다. CPE 분기 디바이스에는 Azure Virtual WAN을 자동으로 프로비저닝하고 연결하는 기본 자동화 기능이 있습니다. 이러한 디바이스는 SD-WAN 및 VPN 파트너의 증가하는 에코시스템에서 사용할 수 있습니다. [선호 파트너 목록](virtual-wan-locations-partners.md)을 참조하세요.

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Virtual WAN은 Azure 가상 네트워크 게이트웨이와 어떻게 다른가요?

가상 네트워크 게이트웨이 VPN은 터널 30개로 제한됩니다. 연결에는 대규모 VPN을 위한 Virtual WAN을 사용해야 합니다. 허브당 20Gbps의 집계를 사용하여 가상 허브당 최대 1,000개의 분기 연결을 연결할 수 있습니다. 연결은 온-프레미스 VPN 디바이스에서 가상 허브로의 활성-활성 터널입니다. 지역당 여러 가상 허브를 포함할 수도 있습니다. 즉, 해당 Azure 지역에 각각 자체 사이트 간 VPN 게이트웨이가 있는 여러 Virtual WAN 허브를 배포하여 단일 Azure 지역에 1,000개가 넘는 분기를 연결할 수 있습니다.

### <a name="what-is-the-recommended-packets-per-second-limit-per-ipsec-tunnel"></a>IPSEC 터널당 권장되는 초당 패킷 수 제한은 무엇인가요?

최적의 성능을 위해 IPSEC 암호화 및 무결성 둘 다에 대해 GCMAES256 알고리즘으로 약 95,000개의 PPS를 보내는 것이 좋습니다. 95,000PPS를 초과하여 보내는 경우 트래픽이 차단되지 않지만 대기 시간 및 패킷 삭제와 같은 성능 저하가 예상될 수 있습니다. 더 많은 PPS가 필요한 경우 추가 터널을 만듭니다.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>어떤 디바이스 공급자(Virtual WAN 파트너)가 지원되나요?

현재 많은 파트너가 완전히 자동화된 Virtual WAN 환경을 지원합니다. 자세한 내용은 [Virtual WAN 파트너](virtual-wan-locations-partners.md)를 참조하세요.

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN 파트너 자동화 단계란?

자세한 내용은 [Virtual WAN 파트너 자동화](virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>원하는 파트너 디바이스를 사용해야 하나요?

아니요. IKEv2/IKEv1 IPsec 지원에 대한 Azure 요구 사항을 준수하는 모든 VPN 지원 디바이스를 사용할 수 있습니다. 또한 Virtual WAN에는 Azure Virtual WAN에 대한 연결을 자동화하는 CPE 파트너 솔루션이 포함되어 있으므로 IPsec VPN 연결을 간편하게 대규모로 설정할 수 있습니다.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN 파트너는 Azure Virtual WAN과 연결을 어떻게 자동화하나요?

일반적으로 소프트웨어 정의 연결 솔루션은 컨트롤러 또는 디바이스 프로비저닝 센터를 사용하여 해당 분기 디바이스를 관리합니다. 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 자동화에는 분기 정보 업로드, Azure 구성 다운로드, Azure 가상 허브로의 IPsec 터널 설정, 분기 디바이스에서 Azure Virtual WAN으로의 연결 자동 설정이 포함됩니다. 수백 개의 분기가 있을 경우 온보딩 환경에서 대규모 IPsec 연결을 설정, 구성, 관리할 필요가 없기 때문에 Virtual WAN CPE 파트너를 사용하여 연결하는 것이 쉽습니다. 자세한 내용은 [Virtual WAN 파트너 자동화](virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>사용 중인 디바이스가 Virtual WAN 파트너 목록에 없는 경우 어떻게 되나요? 계속 Azure Virtual WAN VPN에 연결할 수 있나요?

네, 디바이스가 IPsec IKEv1 또는 IKEv2를 지원하는 경우 연결할 수 있습니다. Virtual WAN 파트너는 디바이스에서 Azure VPN 엔드포인트로의 연결을 자동화합니다. 이는 '분기 정보 업로드', 'IPsec 및 구성', '연결' 등의 단계를 자동화하는 것을 의미합니다. 디바이스가 Virtual WAN 파트너 에코시스템에서 온 것이 아니기 때문에 IPsec 연결을 설정하려면 Azure 구성을 가져오고 디바이스를 업데이트하는 작업을 수동으로 많이 수행해야 합니다.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>출시 파트너 목록에 없는 새 파트너는 어떻게 온보딩하나요?

모든 Virtual WAN API는 개방형 API입니다. [Virtual WAN 파트너 자동화](virtual-wan-configure-automation-providers.md) 설명서를 참조하여 기술적 실행 가능성을 평가할 수 있습니다. IKEv1 또는 IKEv2 IPsec 연결용으로 프로비전 가능한 디바이스가 있는 파트너가 적합합니다. 회사에서 위에 제공된 자동화 지침에 따라 CPE 디바이스에 대한 자동화 작업을 완료하면 [파트너를 통해 연결](virtual-wan-locations-partners.md#partners)에 나열될 azurevirtualwan@microsoft.com에 연결할 수 있습니다. 특정 회사 솔루션을 Virtual WAN 파트너로 나열하려는 고객의 경우 회사에서 azurevirtualwan@microsoft.com으로 이메일을 보내 Virtual WAN에 문의하도록 하세요.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN이 SD-WAN 디바이스를 지원하는 방법

Virtual WAN 파트너는 Azure VPN 엔드포인트로의 IPsec 연결을 자동화합니다. Virtual WAN 파트너가 SD-WAN 공급 기업인 경우 SD-WAN 컨트롤러가 자동화 및 Azure VPN 엔드포인트로의 IPsec 연결을 관리함을 의미합니다. 독점적인 SD-WAN 기능을 위해 Azure VPN 대신 SD-WAN 디바이스가 자체 엔드포인트를 필요로 하는 경우, Azure VNet에 SD-WAN 엔드포인트를 배포하고 Azure Virtual WAN과 공존할 수 있습니다.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>한 허브에 연결할 수 있는 VPN 디바이스의 수는 몇 개인가요?

가상 허브당 최대 1,000개의 연결이 지원됩니다. 각 연결은 네 개의 링크로 구성되며, 각 링크 연결은 액티브-액티브 구성인 두 개 터널을 지원합니다. 터널은 Azure 가상 허브 VPN 게이트웨이에서 종료됩니다. 링크는 분기/VPN 디바이스의 실제 ISP 링크를 나타냅니다.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure Virtual WAN에 대한 분기 연결이란?

분기 또는 VPN 디바이스에서 Azure Virtual WAN으로 연결하는 것은 가상 허브에서 VPN 사이트와 Azure VPN Gateway를 가상으로 연결하는 VPN 연결입니다.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>온-프레미스 VPN 디바이스에 Azure Virtual WAN VPN 게이트웨이에 대한 터널이 1개만 있는 경우 어떻게 되나요?

Azure Virtual WAN 연결은 2개의 터널로 구성됩니다. Virtual WAN VPN 게이트웨이는 활성-활성 모드로 가상 허브에 배포됩니다. 즉, 별도의 인스턴스에서 종료되는 온-프레미스 디바이스의 별도 터널이 있음을 의미합니다. 이는 모든 사용자에게 권장되는 사항입니다. 그러나 사용자가 Virtual WAN VPN 게이트웨이 인스턴스 중 하나에 대한 터널을 1개만 보유하도록 선택하는 경우 어떤 이유(유지 관리, 패치 등)로든 게이트웨이 인스턴스를 오프라인 상태로 전환하면 터널이 보조 활성 인스턴스로 이동되고 사용자가 다시 연결될 수 있습니다. BGP 세션은 인스턴스 간에 이동하지 않습니다.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>온-프레미스 VPN 디바이스가 여러 허브에 연결할 수 있나요?

예. 트래픽 흐름이 시작되면 온-프레미스 디바이스에서 가장 가까운 Microsoft 네트워크 에지로, 그리고 가상 허브로 이동합니다.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN에 사용 가능한 새 Resource Manager 리소스가 있나요?
  
예, Virtual WAN에는 새 Resource Manager 리소스가 포함되어 있습니다. 자세한 내용은 [개요](virtual-wan-about.md)를 참조하세요.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN과 함께 즐겨 찾는 네트워크 가상 어플라이언스(NVA VNet에서)를 배포 및 사용할 수 있나요?

예. 원하는 NVA(네트워크 가상 어플라이언스) VNet을 Azure Virtual WAN에 연결할 수 있습니다.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>가상 허브 내에 네트워크 가상 어플라이언스를 만들 수 있나요?

NVA(네트워크 가상 어플라이언스)는 가상 허브 내에 배포할 수 없습니다. 그러나 가상 허브에 연결된 스포크 VNet에서 네트워크 가상 어플라이언스를 만들고, 필요에 따라 적절한 라우팅을 사용하여 트래픽을 보낼 수 있습니다.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>스포크 VNet이 가상 네트워크 게이트웨이를 가질 수 있나요?

아니요. 가상 허브에 연결되어 있는 경우 스포크 VNet은 가상 네트워크 게이트웨이를 가질 수 없습니다.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN 연결에서 BGP가 지원되나요?

예, BGP가 지원됩니다. VPN 사이트를 만들 때 BGP 매개 변수를 제공할 수 있습니다. 이는 해당 사이트에 대해 Azure에서 생성된 모든 연결이 BGP에 대해 활성화됨을 의미합니다.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN에 대한 라이선스 또는 가격 책정 정보가 있나요?

예. [가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure Virtual WAN을 만들 수 있나요?

[빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)을 사용하면 허브와 vpnsite가 각각 하나씩 포함된 단일 Virtual WAN의 단순한 구성을 만들 수 있습니다. Virtual WAN은 주로 REST 또는 포털 기반 서비스입니다.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>가상 허브에 연결된 VNet 스포크는 서로 통신(V2V 전송)할 수 있나요?

예. 표준 Virtual WAN은 VNet이 연결된 Virtual WAN 허브를 통해 VNet 간 전이적 연결을 지원합니다. Virtual WAN 용어에서는 이러한 경로를 단일 지역 내의 Virtual WAN 허브에 연결된 VNet의 경우 "로컬 Virtual WAN VNet 전송"이라고 하며, 둘 이상의 지역에 걸친 여러 Virtual WAN 허브를 통해 연결된 VNet의 경우 "글로벌 Virtual WAN VNet 전송"이라고 합니다.

일부 시나리오에서는 로컬 또는 글로벌 Virtual WAN VNet 전송 외에도 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하여 스포크 VNet 간에 직접 피어링할 수도 있습니다. 이 경우 VNet 피어링은 Virtual WAN 허브를 통한 전이적 연결보다 우선적으로 적용됩니다.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN에서 분기-분기 연결이 가능한가요?

예, Virtual WAN에서 분기-분기 연결을 사용할 수 있습니다. 분기는 개념적으로 VPN 사이트, ExpressRoute 회로 또는 지점 및 사이트 간/사용자 VPN 사용자에게 적용됩니다. 분기-분기 연결이 기본적으로 사용되며 WAN **구성** 설정에서 찾을 수 있습니다. 이렇게 하면 VPN 분기/사용자가 다른 VPN 분기에 연결할 수 있을 뿐 아니라 VPN과 ExpressRoute 사용자 간에 전송 연결이 설정됩니다.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>분기에서 분기로의 트래픽은 Azure Virtual WAN을 통과하나요?

예. 분기에서 분기로의 트래픽은 Azure Virtual WAN을 통과합니다.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Virtual WAN은 각 사이트의 ExpressRoute가 필요한가요?

아니요. Virtual WAN은 각 사이트의 ExpressRoute가 필요하지 않습니다. 사이트는 ExpressRoute 회로를 사용하여 공급자 네트워크에 연결할 수 있습니다. ExpressRoute를 사용하여 가상 허브에 연결되는 사이트 및 동일한 허브에 연결되는 IPsec VPN의 경우 가상 허브는 VPN과 ExpressRoute 사용자 간에 전송 연결을 제공합니다.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN을 사용할 때 네트워크 처리량 또는 연결 제한이 있나요?

네트워크 처리량은 가상 WAN 허브의 서비스 단위로 제한됩니다. 가상 WAN을 원하는 만큼 사용할 수 있지만, 각 Virtual WAN은 지역마다 1개의 허브를 허용합니다. 각 허브에서 VPN 집계 처리량은 최대 20Gbps, ExpressRoute 집계 처리량은 최대 20Gbps, 사용자 VPN/지점 및 사이트 간 VPN 집계 처리량은 최대 20Gbps입니다. 가상 허브의 라우터는 VNet 간 트래픽 흐름에 대해 최대 50Gbps를 지원하며, 단일 가상 허브에 연결된 모든 VNet에 있는 전체 VM 워크로드가 2000개라고 가정합니다.

VPN 사이트는 허브에 연결할 때 연결을 사용합니다. Virtual WAN은 가상 허브당 최대 1000개 연결 또는 2000개 IPsec 터널을 지원합니다. 원격 사용자는 가상 허브에 연결할 때 P2S VPN 게이트웨이에 연결하며, 이 게이트웨이는 가상 허브의 P2S VPN 게이트웨이에 대해 선택한 배율 단위(대역폭)에 따라 최대 10,000명의 사용자를 지원합니다.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN 터널 및 연결의 총 VPN 처리량이란?

허브의 총 VPN 처리량은 선택한 VPN 게이트웨이의 배율 단위에 따라 최대 20Gbps입니다. 처리량은 모든 기존 연결에서 공유됩니다. 연결의 각 터널은 최대 1Gbps를 지원할 수 있습니다.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>내 VPN 연결에서 NAT-T를 사용할 수 있나요?

예, NAT-T(NAT traversal)가 지원됩니다. Virtual WAN VPN 게이트웨이는 IPsec 터널과 주고받는 내부 패킷에서 NAT 같은 기능을 수행하지 않습니다. 이 구성에서 온-프레미스 디바이스가 IPSec 터널을 시작하는지 확인합니다.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>포털에는 가상 허브에 대한 20Gbps 설정이 표시되지 않습니다. 이를 구성하려면 어떻게 하나요?

포털에서 허브 내의 VPN Gateway로 이동한 다음, 배율 단위를 클릭하고 적절한 설정으로 변경합니다.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN은 온-프레미스 디바이스가 여러 ISP를 동시에 사용하는 것을 허용하나요, 아니면 항상 단일 VPN 터널인가요?

온-프레미스 디바이스 솔루션은 트래픽 정책을 적용하여 여러 터널의 트래픽을 Azure Virtual WAN 허브(가상 허브의 VPN 게이트웨이)로 분산할 수 있습니다.

### <a name="what-is-global-transit-architecture"></a>글로벌 전송 아키텍처란 무엇인가요?

글로벌 전송 아키텍처에 대한 자세한 내용은 [글로벌 전송 네트워크 아키텍처 및 Virtual WAN](virtual-wan-global-transit-network-architecture.md)을 참조하세요.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure 백본에서 트래픽은 어떻게 라우팅되나요?

트래픽은 분기 디바이스 -> ISP -> Microsoft 네트워크 에지 -> Microsoft DC(허브 VNet) -> Microsoft 네트워크 에지 -> ISP -> 분기 디바이스 패턴을 따릅니다.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>이 모델은 각 사이트에서 무엇이 필요합니까? 인터넷 연결만 있으면 되나요?

예. IPsec을 지원하는 인터넷 연결과 물리적 디바이스(가급적이면 Microsoft의 통합 [Virtual WAN 파트너](virtual-wan-locations-partners.md))만 있으면 됩니다. 선택적으로 선호하는 디바이스에서 Azure로의 연결과 구성을 수동으로 관리할 수 있습니다.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>연결(VPN, ExpressRoute 또는 Virtual Network)에 기본 경로(0.0.0.0/0)를 사용하도록 설정하려면 어떻게 할까요?

연결의 플래그가 '사용'일 경우 가상 허브가 학습된 기본 경로를 가상 네트워크/사이트 간 VPN/ExpressRoute 연결에 전파할 수 있습니다. 사용자가 가상 네트워크 연결, VPN 연결 또는 ExpressRoute 연결을 편집할 때 이 플래그를 볼 수 있습니다. 사이트 또는 ExpressRoute 회로가 허브에 연결된 경우 기본적으로 이 플래그는 사용하지 않도록 설정됩니다. VNet을 가상 허브에 연결하기 위해 가상 네트워크 연결을 추가하면 기본적으로 사용하도록 설정됩니다.

기본 경로는 Virtual WAN 허브에서 시작되지 않습니다. 허브에 방화벽을 배포한 결과로 Virtual WAN 허브에서 학습했거나 다른 연결된 사이트에서 강제 터널링을 사용할 경우 기본 경로가 전파됩니다. 기본 경로는 허브 간(인터허브)에 전파되지 않습니다.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>가상 WAN의 가상 허브는 여러 허브 중에서 경로에 가장 적합한 경로를 어떻게 선택할까요?

가상 허브는 여러 원격 허브의 동일한 경로를 학습할 때 다음과 같은 순서로 결정합니다.

1. 가장 긴 접두사 일치
1. Interhub를 통한 로컬 경로(가상 허브는 interhub AS에 65520-65520을 할당)
1. BGP를 통한 고정 경로 이는 가상 허브 라우터에서 결정하는 내용에 따라 좌우됩니다. 그러나 BGP를 통해 경로를 보급하거나 고정 주소 접두사를 제공하는 VPN 게이트웨이가 결정권자인 경우 BGP 경로를 통해 고정 경로를 사용하는 방법이 더 좋을 수 있습니다.
1. VPN을 통한 ER(ExpressRoute): 컨텍스트가 로컬 허브인 경우 VPN을 통한 ER이 선호됩니다. ExpressRoute 회로 간의 전송 연결은 Global Reach를 통해서만 사용할 수 있습니다. 따라서 ExpressRoute 회로가 한 허브에 연결되고 또 다른 ExpressRoute 회로가 VPN 연결을 통해 다른 허브에 연결되는 시나리오에서는 허브 간 시나리오에 VPN을 사용하는 것이 더 좋을 수 있습니다.
1. AS 경로 길이

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Virtual WAN 허브는 ExpressRoute 회로 간 연결을 허용하나요?

ER 간 전송은 항상 Global Reach를 통해 수행됩니다. 가상 허브 게이트웨이는 DC 또는 Azure 지역에 배포됩니다. 두 개의 ExpressRoute 회로가 Global Reach를 통해 연결되는 경우 에지 라우터에서 가상 허브 DC로 트래픽을 보낼 필요가 없습니다.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Azure Virtual WAN ExpressRoute 회로 또는 VPN 연결에 가중치 개념이 있나요?

하나의 가상 허브에 여러 ExpressRoute 회로가 연결된 경우 연결의 라우팅 가중치는 가상 허브의 ExpressRoute 회로 간에 선호도를 결정하는 메커니즘을 제공합니다. VPN 연결에 대한 가중치를 설정하는 메커니즘은 없습니다. Azure는 항상 단일 허브 내에서 VPN 연결을 통하는 ExpressRoute 연결을 선호합니다.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Virtual WAN은 트래픽 집계 Azure에 VPN을 통한 ExpressRoute를 선호하나요?

예. Virtual WAN은 트래픽 집계 Azure에 VPN을 통한 ExpressRoute를 선호합니다.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Virtual WAN 허브에 ExpressRoute 회로와 VPN 사이트가 연결된 경우 ExpressRoute보다 VPN 연결 경로가 선호되는 이유는 무엇인가요?

ExpressRoute 회로가 가상 허브에 연결되면 온-프레미스와 Azure 간의 통신에 사용되는 첫 번째 노드는 Microsoft 에지 라우터입니다. 이러한 에지 라우터는 Virtual WAN ExpressRoute 게이트웨이와 통신하고, 이러한 게이트웨이는 Virtual WAN의 모든 게이트웨이 간 경로를 제어하는 가상 허브 라우터의 경로를 학습합니다. Microsoft 에지 라우터는 가상 허브 ExpressRoute 경로를 온-프레미스에서 학습한 경로보다 우선적으로 처리합니다.

어떠한 이유로 VPN 연결이 가상 허브가 경로를 학습하는 주요 매체가 되는 경우(예: ExpressRoute와 VPN 간의 장애 조치(failover) 시나리오) VPN 사이트의 AS 경로 길이가 더 길지 않은 이상, 가상 허브는 VPN 학습 경로를 ExpressRoute 게이트웨이와 계속 공유합니다. 따라서 Microsoft 에지 라우터는 온-프레미스 경로보다 VPN 경로를 선호하게 됩니다.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>두 개의 허브(허브 1 및 2)가 연결되어 있고 ExpressRoute 회로가 두 개의 허브에 보타이로 연결된 경우 허브 1에 연결된 VNet이 허브 2에 연결된 VNet에 연결하는 경로는 무엇입니까?

현재 동작은 VNet 간 연결에서 허브 간 경로보다 ExpressRoute 회로 경로를 선호합니다. 그러나 Virtual WAN 설정에서는 이 방법을 권장하지 않습니다. 이 문제를 해결하려면 다음 두 가지 중 하나를 수행하면 됩니다.

 * 여러 ExpressRoute 회로(여러 공급자)를 하나의 허브에 연결하도록 구성하고 Virtual WAN에서 제공하는 허브 간 연결을 지역 간 트래픽 흐름에 사용합니다.

 * 제어된 공개 미리 보기에 참여하려면 제품 팀에 문의하세요. 이 미리 보기에서 두 허브 간의 트래픽은 각 허브의 Azure Virtual WAN 라우터를 통과하고 ExpressRoute 경로 대신 허브 간 경로를 사용합니다(Microsoft Edge 라우터/MSEE를 통과). 미리 보기 중에 이 기능을 사용하려면 이메일( **previewpreferh2h@microsoft.com** )을 보내 Virtual WAN ID, 구독 ID 및 Azure 지역을 제공합니다. 기능 활성화가 확인되면 업무 시간(월요일-금요일)으로 48시간 이내에 응답이 제공됩니다.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Virtual WAN의 다른 리소스 그룹에 허브를 만들 수 있나요?

예. 이 옵션은 현재 PowerShell을 통해서만 사용할 수 있습니다. Virtual WAN 포털은 Virtual WAN 리소스 자체와 동일한 리소스 그룹에 허브가 있어야 합니다.

### <a name="what-is-the-recommended-hub-address-space-during-hub-creation"></a>허브 생성 중에 권장되는 허브 주소 공간은 무엇인가요?

권장되는 Virtual WAN 허브 주소 공간은 /23입니다. Virtual WAN 허브는 다양한 게이트웨이(ExpressRoute, 사이트 간 VPN, 지점 및 사이트 간 VPN, Azure Firewall, 가상 허브 라우터)에 서브넷을 할당합니다. NVA가 가상 허브 내에 배포되는 시나리오의 경우 일반적으로 NVA 인스턴스에 대해 /28이 표시됩니다. 그러나 사용자가 여러 NVA를 프로비전할 경우 /27 서브넷이 할당될 수 있습니다. 따라서 Virtual WAN 허브가 최소 크기 /24로 배포되는 동안 향후 아키텍처를 고려하여 생성 시 사용자가 입력에 권장되는 허브 공간은 /23입니다.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Virtual WAN에서 IPv6를 지원하나요?

IPv6는 Virtual WAN 허브 및 해당 게이트웨이에서 지원되지 않습니다. IPv4 및 IPv6를 지원하는 VNet이 있고 해당 VNet을 Virtual WAN에 연결하려는 경우 이 시나리오는 현재 지원되지 않습니다.

Azure Firewall을 통해 인터넷이 중단되는 지점 및 사이트 간 사용자 VPN 시나리오의 경우 Virtual WAN 허브에 트래픽을 강제로 적용하기 위해 클라이언트 디바이스에서 IPv6 연결을 해제해야 할 수 있습니다. 최신 디바이스는 기본적으로 IPv6 주소를 사용하기 때문입니다.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>스크립트에서 다양한 Virtual WAN 기능을 자동화하는 데 사용할 수 있는 권장 API 버전은 무엇인가요?

05-01-2020(2020년 5월 1일) 이상 버전이 필요합니다.

### <a name="are-there-any-virtual-wan-limits"></a>Virtual WAN 제한이 있나요?

구독 및 서비스 제한 페이지에서 [Virtual WAN 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) 섹션을 참조하세요.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Virtual WAN 유형(기본 및 표준) 사이의 차이점은 무엇인가요?

[기본 및 표준 Virtual WAN](virtual-wan-about.md#basicstandard)을 참조하세요. 가격 책정에 대해서는 [가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요.

### <a name="does-virtual-wan-store-customer-data"></a>Virtual WAN은 고객 데이터를 저장하나요?

아니요. Virtual WAN은 고객 데이터를 저장하지 않습니다.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>서비스로서의 사용자용 Virtual WAN을 관리할 수 있는 관리되는 서비스 공급자가 있나요?

예. Azure Marketplace를 통해 사용하도록 설정된 MSP(관리되는 서비스 공급자) 솔루션 목록은 [Azure Networking MSP 파트너에서 제공하는 Azure Marketplace](../networking/networking-partners-msp.md#msp)를 참조하세요.

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Virtual WAN 허브 라우팅은 VNet의 Azure Route Server와 어떻게 다른가요?

Azure Route Server는 NVA(네트워크 가상 어플라이언스)가 DIY 허브 VNet의 경로 서버에서 경로를 학습하는 데 사용할 수 있는 BGP(Border Gateway Protocol) 피어링 서비스를 제공합니다. Virtual WAN 라우팅은 VNet 간 전송 라우팅, 사용자 지정 라우팅, 사용자 지정 경로 연결 및 전파를 포함한 여러 가지 기능을 제공하며, ExpressRoute, 사이트 VPN, 원격 사용자/대규모 P2S VPN 및 보안 허브(Azure Firewall) 기능을 제공하는 연결 서비스와 함께 완전 메시형 무인 허브 서비스를 제공합니다. NVA와 Azure Route Server 간에 BGP 피어링을 설정할 때 NVA의 IP 주소를 가상 네트워크에 보급할 수 있습니다. 전송 라우팅, 사용자 지정 라우팅 등의 모든 고급 라우팅 기능에서 Virtual WAN 라우팅을 사용할 수 있습니다.

### <a name="if-i-am-using-a-third-party-security-provider-zscalar-iboss-or-checkpoint-to-secure-my-internet-traffic-why-dont-i-see-the-vpn-site-associated-to-the-third-party-security-provider-in-the-azure-portal"></a>타사 보안 공급자(ZScalar, iBoss 또는 Checkpoint)를 사용하여 내 인터넷 트래픽을 보호하는 경우 Azure Portal에서 타사 보안 공급자와 연결된 VPN 사이트가 표시되지 않는 이유는 무엇인가요?

사용자에 대한 인터넷 액세스를 보호하기 위해 보안 파트너 공급자를 배포하도록 선택하는 경우 타사 보안 공급자는 사용자를 대신하여 VPN 사이트를 만듭니다. 타사 보안 공급자는 공급자에 의해 자동으로 생성되고 사용자가 만든 VPN 사이트는 아니므로 이 VPN 사이트는 Azure Portal에 표시되지 않습니다.

사용 가능한 옵션 타사 보안 공급자 및 이를 설정하는 방법에 대한 자세한 내용은 [보안 파트너 공급자 배포](../firewall-manager/deploy-trusted-security-partner.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [Virtual WAN 정보](virtual-wan-about.md)를 참조하세요.

