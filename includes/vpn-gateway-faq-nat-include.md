---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a237122aaeba57d8c3aa99274a0cc41232729eca
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676939"
---
### <a name="is-nat-supported-on-all-azure-vpn-gateway-skus"></a>NAT가 모든 Azure VPN Gateway SKU에서 지원되나요?

NAT는 VpnGw2~5 및 VpnGw2AZ~5AZ에서 지원됩니다.

### <a name="can-i-use-nat-on-vnet-to-vnet-or-p2s-connections"></a>VNet-VNet 또는 P2S 연결에서 NAT를 사용할 수 있나요?

아니요, NAT는 **IPsec** 크로스-프레미스 연결에만 지원됩니다.

### <a name="how-many-nat-rules-can-i-use-on-a-vpn-gateway"></a>VPN Gateway에서 사용할 수 있는 NAT 규칙은 몇 개인가요?

VPN Gateway에서 최대 100개의 NAT 규칙(수신 및 송신 규칙 결합)을 만들 수 있습니다.

### <a name="is-nat-applied-to-all-connections-on-a-vpn-gateway"></a>NAT가 VPN Gateway의 모든 연결에 적용되나요?

NAT는 NAT 규칙이 있는 연결에 적용됩니다. 연결에 NAT 규칙이 없으면 NAT가 해당 연결에 적용되지 않습니다. 동일한 VPN Gateway에서 NAT와의 일부 연결 및 NAT가 함께 작동하지 않는 다른 연결을 가질 수 있습니다.

### <a name="what-types-of-nat-is-supported-on-azure-vpn-gateways"></a>Azure VPN Gateway에서 지원되는 NAT 유형은 무엇인가요?

정적 1:1 NAT만. 동적 NAT 또는 NAT64는 지원되지 않습니다.

### <a name="does-nat-work-on-active-active-vpn-gateways"></a>NAT가 활성-활성 VPN Gateway에서 작동하나요?

예. NAT는 활성-활성 VPN Gateway와 활성-대기 VPN Gateway 모두에서 작동합니다.

### <a name="does-nat-work-with-bgp-connections"></a>NAT는 BGP 연결에서 작동하나요?

예, NAT와 함께 BGP를 사용할 수 있습니다. 몇 가지 중요한 고려 사항:

* NAT 규칙 구성 페이지에서 **BGP 경로 변환 사용** 을 선택하여 학습된 경로 및 보급된 경로가 연결과 관련된 NAT 규칙에 따라 NAT 후 주소 접두사(외부 매핑)로 변환되도록 합니다. 온-프레미스 BGP 라우터가 IngressSNAT 규칙에 정의된 대로 정확한 접두사를 보급하는지 확인해야 합니다.

* 온-프레미스 VPN 라우터가 APIPA(169.254.x.x)를 BGP 화자/피어 IP로 사용하는 경우 로컬 네트워크 게이트웨이의 **BGP 피어 IP 주소** 필드에서 직접 APIPA 주소를 사용합니다. 온-프레미스 VPN 라우터가 일반 비 APIPA 주소를 사용하고 VNet 주소 공간 또는 다른 온-프레미스 네트워크 공간과 충돌하는 경우 IngressSNAT 규칙이 BGP 피어 IP를 겹치지 않는 고유한 주소로 변환하고 로컬 네트워크 게이트웨이의 **BGP 피어 IP 주소** 필드에 NAT 후 주소를 배치하는지 확인합니다.

### <a name="do-i-need-to-create-the-matching-dnat-rules-for-the-snat-rule"></a>SNAT 규칙에 대해 일치하는 DNAT 규칙을 만들어야 하나요?

아니요. 단일 SNAT 규칙은 특별한 네트워크의 **두** 방향 모두에 대한 변환을 정의합니다.

* IngressSNAT 규칙은 온-프레미스 네트워크에서 Azure VPN 게이트웨이 **로** 들어오는 원본 IP 주소 변환을 정의합니다. 또한 VNet에서 동일한 온-프레미스 네트워크로 나가는 대상 IP 주소 변환을 처리합니다.

* EgressSNAT 규칙은 Azure VPN 게이트웨이에서 온-프레미스 네트워크로 나가는 VNet 원본 IP 주소의 변환을 정의합니다. 또한 EgressSNAT 규칙을 통한 해당 연결을 통해 VNet으로 들어오는 패킷의 대상 IP 주소 변환을 처리합니다.

* 두 경우 모두 **DNAT** 규칙이 필요 없습니다.

### <a name="what-do-i-do-if-my-vnet-or-local-network-gateway-address-space-has-two-or-more-prefixes-can-i-apply-nat-to-all-of-them-or-just-a-subset"></a>내 VNet 또는 로컬 네트워크 게이트웨이 주소 공간에 두 개 이상의 접두사가 있는 경우 어떻게 해야 합니까? NAT를 모두에게 적용할 수 있나요? 또는 하위 집합인가요?

각 NAT 규칙은 NAT에 대한 주소 접두사를 하나만 포함할 수 있으므로 NAT에 필요한 각 접두사에 대해 하나의 NAT 규칙을 만들어야 합니다. 예를 들어 로컬 네트워크 게이트웨이 주소 공간이 10.0.1.0/24 및 10.0.2.0/25로 구성된 경우 아래와 같이 두 개의 규칙을 만들 수 있습니다.

* IngressSNAT 규칙 1: 10.0.1.0/24를 100.0.1.0/24에 매핑
* IngressSNAT 규칙 2: 10.0.2.0/25를 100.0.2.0/25에 매핑

두 규칙은 해당 주소 접두사의 접두사 길이와 일치해야 합니다. VNet 주소 공간에 대한 EgressSNAT 규칙에도 동일하게 적용됩니다.

> [!IMPORTANT]
> 위의 연결에 하나의 규칙만 연결하면 다른 주소 공간은 변환되지 **않습니다**.

### <a name="can-i-use-different-egresssnat-rules-to-translate-my-vnet-address-space-to-different-prefixes-to-different-on-premises-networks"></a>다른 EgressSNAT 규칙을 사용하여 VNet 주소 공간을 다른 온-프레미스 네트워크에 대한 다른 접두사로 변환할 수 있나요?

예, 동일한 VNet 주소 공간에 대해 여러 EgressSNAT 규칙을 만들고 EgressSNAT 규칙을 다른 연결에 적용할 수 있습니다. EgressSNAT 규칙이 없는 연결의 경우

### <a name="can-i-use-the-same-ingresssnat-rule-on-different-connections"></a>다른 연결에서 동일한 IngressSNAT 규칙을 사용할 수 있나요?

예, 일반적으로 동일한 온-프레미스 네트워크에 대한 연결이 중복성을 제공하는 경우에 사용됩니다. 다른 온-프레미스 네트워크에 대한 연결인 경우 동일한 수신 규칙을 사용할 수 없습니다.

### <a name="do-i-need-both-ingress-and-egress-rules-on-a-nat-connection"></a>NAT 연결에 수신 규칙과 송신 규칙이 모두 필요한가요?

온-프레미스 네트워크 주소 공간이 VNet 주소 공간과 겹치는 경우 동일한 연결에 수신 및 송신 규칙이 모두 필요합니다. VNet 주소 공간이 연결된 모든 네트워크 간에 고유한 경우 해당 연결에 EgressSNAT 규칙이 필요하지 않습니다. 수신 규칙을 사용하여 온-프레미스 네트워크 간의 주소 겹침을 방지할 수 있습니다.