---
title: VPN Gateway의 NAT(Network Address Translation) 정보
titleSuffix: Azure VPN Gateway
description: 주소 공간이 겹치는 네트워크를 연결하는 Azure VPN의 NAT(Network Address Translation)를 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/22/2021
ms.author: yushwang
ms.openlocfilehash: 2bcf33466b3cbf57a67307961bb1e21e3a810a72
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112759611"
---
# <a name="about-nat-on-azure-vpn-gateway-preview"></a>Azure VPN Gateway의 NAT 정보(미리 보기)

이 문서에서는 Azure VPN Gateway의 NAT(Network Address Translation) 지원을 간략하게 설명합니다. NAT는 IP 패킷의 한 IP 주소를 다른 IP 주소로 번역하는 메커니즘을 정의합니다. NAT에 대한 여러 가지 시나리오가 있습니다.

* IP 주소가 겹치는 네트워크 여러 개 연결
* 개인 IP 주소(RFC1918)가 있는 네트워크에서 인터넷으로 연결(인터넷 분리)
* IPv6 네트워크를 IPv4 네트워크에 연결(NAT64)

> [!IMPORTANT]
> Azure VPN Gateway NAT는 온-프레미스 네트워크나 지점을 IP 주소가 겹치는 Azure 가상 네트워크에 연결하는 첫 번째 시나리오를 지원합니다. 인터넷 분리와 NAT64는 지원되지 **않습니다**.

## <a name="overlapping-address-spaces"></a><a name="why"></a>겹치는 주소 공간

조직에서는 일반적으로 개인 네트워크의 내부 통신에 RFC1918에 정의된 개인 IP 주소를 사용합니다. 인터넷을 통해 또는 프라이빗 WAN에서 VPN을 사용하여 이러한 네트워크를 연결한 경우에는 주소 공간이 겹치지 **않아야** 합니다. 그렇지 않으면 통신이 실패합니다. IP 주소가 겹치는 네트워크를 두 개 이상 연결하기 위해 NAT는 네트워크를 연결하는 게이트웨이 디바이스에 배포됩니다. 

## <a name="nat-type-static--dynamic"></a><a name="type"></a>NAT 형식: 정적 및 동적

게이트웨이 디바이스의 NAT는 주소 충돌이 방지되도록 NAT 정책이나 규칙에 따라 원본 및/또는 대상 IP 주소를 번역합니다. NAT 변환 규칙에는 다음과 같은 다양한 형식이 있습니다.

* **정적 NAT**: 정적 규칙은 고정 주소 매핑 관계를 정의합니다. 지정된 IP 주소의 경우 대상 풀의 동일한 주소에 매핑됩니다. 매핑은 고정되어 있으므로 정적 규칙에 대한 매핑은 상태 비저장입니다.

* **동적 NAT**: 동적 NAT의 경우 IP 주소를 가용성에 따라 다른 대상 IP 주소 또는 IP 주소와 TCP/UDP 포트의 다양한 조합으로 번역될 수 있습니다. 후자를 NAPT, 네트워크 주소 및 포트 변환이라고도 합니다. 동적 규칙은 지정된 시간의 트래픽 흐름에 따라 상태 저장 번역 매핑을 발생시킵니다.

또 다른 고려 사항은 번역에 사용되는 주소 풀 크기입니다. 대상 주소 풀 크기가 원래 주소 풀과 동일한 경우 정적 NAT 규칙을 사용하여 순차적으로 1:1 매핑을 정의합니다. 대상 주소 풀이 원래 주소 풀보다 작은 경우 동적 NAT 규칙을 사용하여 차이점을 수용합니다.

> [!IMPORTANT]
> * Azure VPN 게이트웨이 NAT는 정적, 1:1 NAT 규칙만 지원합니다. 동적 NAT 규칙은 지원되지 않습니다.
> * NAT는 VpnGw2~5, VpnGw2AZ~5AZ와 같은 SKU에서 지원됩니다.
> * NAT는 IPsec 크로스-프레미스 연결에만 지원됩니다. VNet 간 연결이나 P2S 연결은 지원되지 않습니다.

## <a name="nat-mode-ingress--egress"></a><a name="mode"></a>NAT 모드: 수신 및 송신

각 NAT 규칙은 해당 네트워크 주소 공간에 대한 주소 매핑이나 번역 관계를 정의합니다.

* 수신: **IngressSNAT** 규칙은 주소 겹침이 방지되도록 온-프레미스 네트워크 주소 공간을 번역된 주소 공간에 매핑합니다.

* 송신: **EgressSNAT** 규칙은 Azure VNet 주소 공간을 번역된 다른 주소 공간에 매핑합니다. 

각 NAT 규칙의 경우 다음 두 필드는 변환 전후의 주소 공간을 지정합니다.

* **내부 매핑**: 변환 **전** 의 주소 공간입니다. 수신 규칙의 경우 이 필드는 온-프레미스 네트워크의 원래 주소 공간에 해당합니다. 송신 규칙의 경우 원래 VNet 주소 공간입니다.

* **외부 매핑**: 온-프레미스 네트워크(수신)나 VNet(송신)을 변환한 **후** 의 주소 공간입니다. Azure VPN 게이트웨이에 연결된 다른 네트워크의 경우 모든 **외부 매핑** 의 주소 공간은 서로 겹쳐서는 안 되며 NAT 없이 연결된 네트워크를 사용해야 합니다.

## <a name="nat-and-routing"></a><a name="routing"></a>NAT 및 라우팅

NAT 규칙을 연결에 정의하면 연결의 유효 주소 공간이 규칙에 맞게 변경됩니다. Azure VPN 게이트웨이에서 BGP를 사용하는 경우 "BGP 경로 변환 사용"을 선택하여 NAT 규칙을 통해 연결에서 학습되고 보급된 경로를 자동으로 변환합니다.

* 학습된 경로: IngressSNAT 규칙을 사용하는 연결을 통해 학습된 경로의 대상 접두사는 내부 매핑 접두사(이전 NAT)에서 해당 규칙의 외부 매핑 접두사(NAT 이후)로 번역됩니다.

* 보급된 경로: Azure VPN 게이트웨이는 VNet 주소 공간에 대한 EgressSNAT 규칙의 외부 매핑(NAT 이후) 접두사와 다른 연결의 NAT 이후 주소 접두사가 있는 학습된 경로를 보급합니다.

* NAT'ed 온-프레미스 네트워크에 대한 BGP 피어 IP 주소 고려 사항:
   * APIPA(169.254.0.1~169.254.255.254) 주소: NAT 규칙이 필요하지 않습니다. 로컬 네트워크 게이트웨이에서 APIPA 주소를 직접 지정합니다.
   * APIPA가 아닌 주소: 로컬 네트워크 게이트웨이에서 **번역** 되거나 **NAT 이후** IP 주소를 지정합니다. **번역** 되거나 **NAT 이후** Azure BGP IP 주소를 사용하여 온-프레미스 VPN 경로를 구성합니다. NAT 규칙이 의도된 변환에 정의되어 있는지 확인합니다.

> [!NOTE]
> IngressSNAT 규칙이 없는 연결에 대한 학습된 경로는 변환되지 않습니다. EgressSNAT 규칙이 없는 연결에 보급된 VNet 경로도 변환되지 않습니다.
>

## <a name="nat-example"></a><a name="example"></a>NAT 예제

다음 다이어그램에서는 Azure VPN NAT 구성의 예를 보여줍니다.

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="NAT 다이어그램" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

이 다이어그램에서는 10.0.1.0/24 주소 공간이 있는 Azure VNet 및 온-프레미스 네트워크 2개를 보여줍니다. 이러한 두 네트워크를 Azure VNet 및 VPN 게이트웨이에 연결하려면 다음 규칙을 만듭니다.

* IngressSNAT 규칙 1: 이 규칙은 온-프레미스 주소 공간 10.0.1.0/24를 100.0.2.0/24로 번역합니다.

* IngressSNAT 규칙 2: 이 규칙은 온-프레미스 주소 공간 10.0.1.0/24를 100.0.3.0/24로 번역합니다.

* EgressSNAT 규칙 1: 이 규칙은 VNet 주소 공간 10.0.1.0/24를 100.0.1.0/24로 번역합니다.

다이어그램에서 각 연결 리소스에는 다음과 같은 규칙이 있습니다.

* 연결 1(VNet-Branch1):
    * IngressSNAT 규칙 1
    * EgressSNAT 규칙 1

* 연결 2(VNet-Branch2)
    * IngressSNAT 규칙 2
    * EgressSNAT 규칙 1

연결과 관련된 규칙에 따라 각 네트워크의 주소 공간은 다음과 같습니다.

| 네트워크  | Original    | 변역됨   |
| ---      | ---         | ---          |
| VNet     | 10.0.1.0/24 | 100.0.1.0/24 |
| 분기 1 | 10.0.1.0/24 | 100.0.2.0/24 |
| 분기 2 | 10.0.1.0/24 | 100.0.3.0/24 |

아래 다이어그램에서는 NAT 변환 전후에 분기 1에서 VNet으로 이동하는 IP 패킷을 보여줍니다.

:::image type="content" source="./media/nat-overview/nat-packet.png" alt-text="NAT 변환 전후를 보여주는 다이어그램" lightbox="./media/nat-overview/nat-packet.png" border="false":::

> [!IMPORTANT]
> 단일 SNAT 규칙은 특별한 네트워크의 **두** 방향 모두에 대한 변환을 정의합니다.
>
> * IngressSNAT 규칙은 온-프레미스 네트워크에서 Azure VPN 게이트웨이 **로** 들어오는 원본 IP 주소 변환을 정의합니다. 또한 VNet에서 동일한 온-프레미스 네트워크로 나가는 대상 IP 주소 변환을 처리합니다.
> * EgressSNAT 규칙은 Azure VPN 게이트웨이에서 온-프레미스 네트워크로 나가는 원본 IP 주소 변환을 정의합니다. 또한 EgressSNAT 규칙을 통한 해당 연결을 통해 VNet으로 들어오는 패킷의 대상 IP 주소 변환을 처리합니다.
> * 두 경우 모두 **DNAT** 규칙이 필요 없습니다.

## <a name="nat-configuration"></a><a name="config"></a>NAT 구성

위에 표시된 대로 NAT 구성을 구현하려면 먼저 Azure VPN 게이트웨이에 NAT 규칙을 만든 다음, 연결된 해당 NAT 규칙을 사용하여 연결을 만듭니다. 크로스-프레미스에 NAT을 구성하는 단계는 [Azure VPN 게이트웨이에서 NAT 구성](nat-howto.md)을 참조하세요.

## <a name="nat-faq"></a><a name="faq"></a>NAT FAQ

[!INCLUDE [vpn-gateway-faq-nat-include](../../includes/vpn-gateway-faq-nat-include.md)]

## <a name="next-steps"></a>다음 단계

크로스-프레미스에 NAT을 구성하는 단계는 [Azure VPN 게이트웨이에서 NAT 구성](nat-howto.md)을 참조하세요.