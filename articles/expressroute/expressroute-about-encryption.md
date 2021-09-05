---
title: 'Azure ExpressRoute: 암호화 정보'
description: ExpressRoute 암호화에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: cc123580b5402b5a6daf9fc601b5f6c68ff1b1f6
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567905"
---
# <a name="expressroute-encryption"></a>ExpressRoute 암호화
 
ExpressRoute는 사용자 네트워크와 Microsoft 네트워크 사이에서 이동하는 데이터의 기밀성과 무결성을 보장하기 위한 몇 가지 암호화 기술을 지원합니다.

## <a name="point-to-point-encryption-by-macsec-faq"></a>MACsec 지점 간 암호화 FAQ
MACsec은 [IEEE 표준](https://1.ieee802.org/security/802-1ae/)입니다. MAC(미디어 액세스 제어) 수준 또는 네트워크 레이어 2에서 데이터를 암호화합니다. [ExpressRoute Direct](expressroute-erdirect-about.md)를 통해 Microsoft에 연결하는 경우 MACsec을 사용하여 네트워크 디바이스와 Microsoft 네트워크 디바이스 간의 물리적 링크를 암호화할 수 있습니다. MACsec은 기본적으로 ExpressRoute Direct 포트에서 사용하지 않도록 설정됩니다. 암호화를 위해 고유의 MACsec 키를 가져와 [Azure Key Vault](../key-vault/general/overview.md)에 저장합니다. 키를 순환할 시기를 결정합니다. 아래에 나오는 다른 FAQ를 참조하세요.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>ExpressRoute 공급자가 프로비저닝하는 ExpressRoute 회로에서 MACsec을 사용하도록 설정할 수 있나요?
아니요. MACsec은 단일 엔터티(예: 고객)가 소유한 키를 사용하여 물리적 링크의 모든 트래픽을 암호화합니다. 따라서 ExpressRoute Direct에서만 사용할 수 있습니다.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>ExpressRoute Direct 포트에서 일부 ExpressRoute 회로를 암호화하고 동일한 포트의 다른 회로는 암호화되지 않은 상태로 둘 수 있나요? 
아니요. MACsec을 사용하도록 설정하면 모든 네트워크 제어 트래픽(예: BGP 데이터 트래픽 및 고객 데이터 트래픽)이 암호화됩니다. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>MACsec을 사용/사용하지 않도록 설정하거나 MACsec 키를 업데이트하면 온-프레미스 네트워크와 ExpressRoute를 통한 Microsoft와의 연결이 끊어지나요?
예. MACsec 구성의 경우 미리 공유한 키 모드만 지원합니다. 즉, 사용자의 디바이스 및 Microsoft의 디바이스 둘 다에서(API를 통해) 키를 업데이트해야 합니다. 이 변경은 원자성이 아니므로 양쪽에서 키가 일치하지 않는 경우 연결이 끊어집니다. 구성 변경을 수행할 유지 관리 기간을 예약하는 것이 좋습니다. 가동 중지 시간을 최소화하려면 네트워크 트래픽을 다른 링크로 전환한 후 한 번에 하나의 ExpressRoute Direct 연결에서 구성을 업데이트하는 것이 좋습니다.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>내 디바이스와 Microsoft 디바이스 간에 MACsec 키가 일치하지 않으면 트래픽이 계속 흐르나요?
아니요. MACsec이 구성되고 키가 일치하지 않을 경우 Microsoft에 대한 연결이 끊어집니다. 즉, 암호화되지 않은 연결로 대체하지 않게 되므로 데이터가 노출됩니다. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>ExpressRoute Direct의 MACsec을 사용하도록 설정하면 네트워크 성능이 저하되나요?
MACsec 암호화 및 암호 해독은 사용하는 라우터의 하드웨어에서 발생합니다. Microsoft 쪽 성능에는 영향을 미치지 않습니다. 그러나 사용하는 디바이스를 네트워크 공급업체에 문의하고 MACsec이 성능에 영향을 미치는지 확인해야 합니다.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>암호화에 지원되는 암호 도구 모음은 무엇인가요?
다음 [표준 암호화](https://1.ieee802.org/security/802-1ae/)를 지원합니다.
* GCM-AES-128
* GCM-AES-256
* GCM-AES-XPN-128
* GCM-AES-XPN-256

또한 디바이스의 MACsec 구성에서 [SCI(보안 채널 식별자)](https://en.wikipedia.org/wiki/IEEE_802.1AE)를 사용하지 않도록 설정해야 합니다.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec별 엔드투엔드 암호화 FAQ
IPsec은 [IETF 표준](https://tools.ietf.org/html/rfc6071)입니다. IP(인터넷 프로토콜) 수준 또는 네트워크 계층 3에서 데이터를 암호화합니다. IPsec을 사용하여 온-프레미스 네트워크와 Azure의 VNET(가상 네트워크) 간 엔드투엔드 연결을 암호화할 수 있습니다. 아래에 나오는 다른 FAQ를 참조하세요.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>내 ExpressRoute Direct 포트에서 MACsec 외에 IPsec을 사용하도록 설정할 수 있나요?
예. MACsec은 사용자와 Microsoft 간의 물리적 연결을 보호합니다. IPsec은 사용자와 Azure 가상 네트워크 간의 엔드투엔드 연결을 보호합니다. 별도로 사용하도록 설정할 수 있습니다. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Azure VPN Gateway를 사용하여 Azure 프라이빗 피어링을 통해 IPsec 터널을 설정할 수 있나요?
예. Azure Virtual WAN을 채택하는 경우 [다음 단계](../virtual-wan/vpn-over-expressroute.md)에 따라 엔드투엔드 연결을 암호화할 수 있습니다. 일반 Azure VNET이 있는 경우 [다음 단계](../vpn-gateway/site-to-site-vpn-private-peering.md)에 따라 Azure VPN Gateway와 온-프레미스 VPN Gateway 간에 IPsec 터널을 설정할 수 있습니다.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>ExpressRoute 연결에서 IPsec을 사용하도록 설정한 후에 어떤 처리량이 발생하나요?
Azure VPN Gateway를 사용하는 경우 [여기에서 성능 수치](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 확인하세요. 타사 VPN Gateway를 사용하는 경우 공급업체에 문의하여 성능 수치를 확인합니다.

## <a name="next-steps"></a>다음 단계
MACsec 구성에 대한 자세한 내용은 [Macsec 구성](expressroute-howto-macsec.md)을 참조하세요.

IPsec 구성에 대한 자세한 내용은 [Ipsec 구성](site-to-site-vpn-over-microsoft-peering.md)을 참조하세요.
