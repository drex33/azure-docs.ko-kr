---
title: 게이트웨이에 대한 VPN NAT 규칙 구성
titleSuffix: Azure Virtual WAN
description: VWAN VPN Gateway에 대한 NAT 규칙을 구성하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6613952b5d7811c187123963a6e611602aabb5dc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113127693"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway"></a>Virtual WAN VPN 게이트웨이에 대한 NAT 규칙 구성

정적 일대일 NAT 규칙을 사용하여 Virtual WAN VPN Gateway를 구성할 수 있습니다. NAT 규칙은 IP 주소의 일대일 변환을 설정하는 메커니즘을 제공합니다. NAT는 호환되지 않거나 겹치는 IP 주소를 가진 두 개의 IP 네트워크를 상호 연결하는 데 사용할 수 있습니다. 일반적인 시나리오는 Azure VNet 리소스에 액세스하려는 겹치는 IP가 있는 분기입니다.

이 구성은 흐름 테이블을 사용하여 외부(호스트) IP 주소에서 가상 네트워크(가상 머신, 컴퓨터, 컨테이너 등) 내부의 엔드포인트와 연결된 내부 IP 주소로 트래픽을 라우팅합니다.

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="아키텍처를 보여 주는 다이어그램.":::
   
NAT를 사용하려면 VPN 디바이스에서 Any-to-Any(와일드카드) 트래픽 선택기를 사용해야 합니다. 정책 기반(좁은) 트래픽 선택기는 NAT 구성과 함께 지원되지 않습니다.

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT 규칙 구성

언제든지 VPN Gateway 설정에서 NAT 규칙을 구성하고 볼 수 있습니다.

> [!NOTE]
> 사이트 간 NAT는 정책 기반 트래픽 선택기를 사용하는 사이트 간 VPN 연결에서 지원되지 않습니다.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="규칙을 편집하는 방법을 보여 주는 스크린샷."lightbox="./media/nat-rules-vpn-gateway/edit-rules.png":::
1. 가상 허브로 이동합니다.
1. **VPN(사이트 간)** 을 선택합니다.
1. **NAT 규칙(편집)** 을 선택합니다.
1. **NAT 규칙 편집** 페이지에서 다음 값을 사용하여 NAT 규칙을 **추가/편집/삭제** 할 수 있습니다.

   * **이름:** NAT 규칙에 사용할 고유한 이름입니다.
   * **유형:** 정적입니다. 정적 일대일 NAT는 내부 주소와 외부 주소 간에 일대일 관계를 설정합니다.
   * **모드:** IngressSnat 또는 EgressSnat입니다.  
      * IngressSnat 모드(수신 원본 NAT라고도 함)는 Azure 허브의 사이트 간 VPN Gateway에 들어오는 트래픽에 적용됩니다. 
      * EgressSnat 모드(송신 원본 NAT라고도 함)는 Azure 허브의 사이트 간 VPN Gateway를 나가는 트래픽에 적용됩니다. 
   * **InternalMapping:** 외부 IP 세트에 매핑될 내부 네트워크에 있는 원본 IP의 주소 접두사 범위입니다. 즉, 사전 NAT 주소 접두사 범위입니다.
   * **ExternalMapping:** 원본 IP가 매핑될 외부 네트워크에 있는 대상 IP의 주소 접두사 범위입니다. 즉, 사후 NAT 주소 접두사 범위입니다.
   * **링크 연결:** VPN 사이트를 Azure Virtual WAN Hub의 사이트 간 VPN Gateway에 가상으로 연결하는 연결 리소스입니다.
 
> [!NOTE]
> 사이트 간 VPN Gateway가 BGP를 통해 변환된(**ExternalMapping**) 주소 접두사를 보급하도록 하려면 **BGP 변환 사용** 단추를 클릭합니다. 온-프레미스에서 송신 규칙의 NAT 사후 범위를 자동으로 학습하고, Azure(Virtual WAN Hub, 연결된 Virtual Networks, VPN 및 ExpressRoute 분기)에서 수신 규칙의 사후 NAT 범위를 자동으로 학습하기 때문입니다. **BGP 변환 사용** 설정은 Virtual WAN Hub 사이트 간 VPN Gateway의 모든 NAT 규칙에 적용됩니다. 

## <a name="example-configurations"></a><a name="examples"></a>예제 구성

### <a name="ingress-snat-bgp-enabled-vpn-site"></a>수신 SNAT(BGP 사용 VPN 사이트)

**수신 SNAT 규칙** 은 Virtual Networks WAN 사이트 간 VPN Gateway를 통해 Azure에 들어오는 패킷에 적용됩니다. 이 시나리오에서는 두 사이트 간 VPN 분기를 Azure에 연결하려고 합니다. VPN 사이트 1은 링크 A를 통해 연결되고, VPN 사이트 2는 링크 B를 통해 연결됩니다. 각 사이트에는 동일한 주소 공간 10.30.0.0/24가 있습니다.

이 예제에서는 사이트1을 127.30.0.0.0/24로 NAT합니다. Virtual WAN 스포크 Virtual Networks와 분기는 이 사후 NAT 주소 공간을 자동으로 학습합니다. 

다음 다이어그램에는 예상되는 최종 결과가 나와 있습니다.

:::image type="content" source="./media/nat-rules-vpn-gateway/diagram-bgp.png" alt-text="BGP를 사용하는 사이트의 수신 모드 NAT를 보여 주는 다이어그램.":::

1. NAT 규칙을 지정합니다.

   사이트 간 VPN Gateway가 겹치는 주소 공간(예: 10.30.0.0/24)을 사용하는 두 분기를 구분할 수 있도록 NAT 규칙을 지정합니다. 이 예제에서는 VPN 사이트 1에 대한 링크 A에 초점을 둡니다.

   다음 NAT 규칙을 설정하고 링크 A에 연결할 수 있습니다. 이는 정적 NAT 규칙이므로 **InternalMapping** 및 **ExternalMapping** 의 주소 공간에 동일한 수의 IP 주소가 포함되어 있습니다.

   * **이름:** ingressRule01
   * **유형:** 정적
   * **모드:** IngressSnat
   * **InternalMapping:** 10.30.0.0/24
   * **ExternalMapping:** 172.30.0.0/24
   * **링크 연결:** 링크 A

 2.  **BGP 경로 변환** 을 ‘사용’으로 전환합니다.

       :::image type="content" source="./media/nat-rules-vpn-gateway/enable-bgp.png" alt-text="BGP 변환을 사용하도록 설정하는 방법을 보여 주는 스크린샷.":::


3. 사이트 간 VPN Gateway가 온-프레미스 BGP 피어와 피어링할 수 있는지 확인합니다.

      이 예제에서 **수신 NAT 규칙** 은 10.30.0.132를 127.30.0.132로 변환해야 합니다. 이렇게 하려면 ‘VPN 사이트 편집’을 클릭하여 이 변환된 BGP 피어 주소(127.30.0.132)를 반영하도록 VPN 사이트 링크 A BGP 주소를 구성합니다. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site-bgp.png" alt-text="BGP 피어링 IP를 변경하는 방법을 보여 주는 스크린샷."lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

 

### <a name="considerations-if-the-vpn-site-connects-via-bgp"></a><a name="considerations"></a>VPN 사이트가 BGP를 통해 연결되는 경우 고려 사항
* 정적 일대일 NAT의 경우 내부 매핑과 외부 매핑의 서브넷 크기가 동일해야 합니다.
* **BGP 변환** 이 사용하도록 설정된 경우 사이트 간 VPN Gateway는 **송신 NAT 규칙** 의 **외부 매핑** 을 온-프레미스에, **수신 NAT 규칙** 의 **외부 매핑** 을 Azure(Virtual WAN Hub, 연결된 스포크 Virtual Networks, 연결된 VPN/ExpresRoute)에 자동으로 보급합니다. **BGP 변환** 이 사용하지 않도록 설정된 경우 변환된 경로는 온-프레미스에 자동으로 보급되지 않습니다. 따라서 VPN 사이트 링크 연결과 관련된 **수신 NAT** 규칙의 사후 NAT(**ExternalMapping**) 범위를 보급하도록 온-프레미스 BGP 스피커를 구성해야 합니다. 마찬가지로 **송신 NAT 규칙** 의 사후 NAT(**ExternalMapping**) 범위에 대한 경로를 온-프레미스 디바이스에 적용해야 합니다.
* **만약** 온-프레미스 BGP 피어 IP 주소가 **수신 NAT 규칙** 의 **내부 매핑** 내에 포함되어 있다면 사이트 간 VPN Gateway는 온-프레미스 BGP 피어 IP 주소를 자동으로 변환합니다. 따라서 VPN 사이트의 **링크 연결 BGP 주소** 는 NAT 변환 주소(외부 매핑의 일부)를 반영해야 합니다. 

    예를 들어 온-프레미스 BGP IP 주소가 10.30.0.133이고 10.30.0.0/24를 127.30.0.0/24로 변환하는 **수신 NAT 규칙** 이 있는 경우 VPN 사이트의 **링크 연결 BGP 주소** 는 변환된 주소(127.30.0.133)로 구성되어야 합니다.

 
### <a name="ingress-snat-vpn-site-with-statically-configured-routes"></a>수신 SNAT(정적으로 구성된 경로가 있는 VPN 사이트)

**수신 SNAT 규칙** 은 Virtual Networks WAN 사이트 간 VPN Gateway를 통해 Azure에 들어오는 패킷에 적용됩니다. 이 시나리오에서는 두 사이트 간 VPN 분기를 Azure에 연결하려고 합니다. VPN 사이트 1은 링크 A를 통해 연결되고, VPN 사이트 2는 링크 B를 통해 연결됩니다. 각 사이트에는 동일한 주소 공간 10.30.0.0/24가 있습니다.

이 예제에서는 VPN 사이트 1을 127.30.0.0.0/24로 NAT합니다. 그러나 VPN 사이트가 BGP를 통해 사이트 간 VPN Gateway에 연결되어 있지 않기 때문에 구성 단계는 BGP 사용 예제와 약간 다릅니다. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram-static.png" alt-text="정적 라우팅을 사용하는 VPN 사이트의 다이어그램 구성을 보여 주는 스크린샷.":::


1. NAT 규칙을 지정합니다.

    사이트 간 VPN Gateway가 동일한 주소 공간(예: 10.30.0.0/24)을 사용하는 두 분기를 구분할 수 있도록 NAT 규칙을 지정합니다. 이 예제에서는 VPN 사이트 1에 대한 링크 A에 초점을 둡니다.

    다음 NAT 규칙을 설정하고 VPN 사이트 1 중 하나의 링크 A에 연결할 수 있습니다. 이는 정적 NAT 규칙이므로 **InternalMapping** 및 **ExternalMapping** 의 주소 공간에 동일한 수의 IP 주소가 포함되어 있습니다.

    * **이름**: IngressRule01
    * **유형**: 정적
    * **모드**: IngressSnat
    * **InternalMapping**: 10.30.0.0/24
    * **ExternalMapping**: 172.30.0.0/24
    * **링크 연결**: 링크 A

2. VPN 사이트 1의 ‘프라이빗 주소 공간’ 필드를 편집하여 사이트 간 VPN Gateway에서 사후 NAT 범위(172.30.0.0/24)를 학습하도록 합니다.

   * 사이트 간 VPN Gateway가 포함된 Virtual Hub 리소스로 이동합니다. Virtual Hub 페이지의 연결에서 VPN(사이트 간)을 선택합니다.

   * 링크 A를 통해 Virtual WAN Hub에 연결된 VPN 사이트를 선택합니다. 사이트 편집을 선택하고 VPN 사이트에 대한 프라이빗 주소 공간으로 172.30.0.0/24를 입력합니다. 

   :::image type="content" source="./media/nat-rules-vpn-gateway/vpn-site-static.png" alt-text="VPN 사이트의 프라이빗 주소 공간을 편집하는 방법을 보여 주는 스크린샷" lightbox="./media/nat-rules-vpn-gateway/vpn-site-static.png":::

### <a name="considerations-if-vpn-sites-are-statically-configured-not-connected-via-bgp"></a><a name="considerationsnobgp"></a>VPN 사이트가 정적으로 구성된 경우의 고려 사항(BGP를 통해 연결되지 않음)
* 정적 일대일 NAT의 경우 내부 매핑과 외부 매핑의 서브넷 크기가 동일해야 합니다.
* Azure Portal에서 VPN 사이트를 편집하여 ‘프라이빗 주소 공간’ 필드에서 **수신 NAT 규칙** 의 **ExternalMapping** 에 접두사를 추가합니다. 
* **송신 NAT 규칙** 을 포함하는 구성의 경우 **송신 NAT 규칙** 의 **ExternalMapping** 이 포함된 경로 정책 또는 고정 경로를 온-프레미스 디바이스에 적용해야 합니다.

### <a name="packet-flow"></a>패킷 흐름

위의 예제에서 온-프레미스 디바이스는 Spoke Virtual Network의 리소스에 연결하려고 합니다. 패킷 흐름은 다음과 같습니다. NAT 변환은 굵게 표시됩니다.

1. 온-프레미스에서 오는 트래픽이 시작됩니다.
   * 원본 IP 주소: **10.30.0.4**
   * 대상 IP 주소: 10.200.0.4
1. 트래픽이 사이트 간 게이트웨이로 들어가고 NAT 규칙을 사용하여 변환된 다음, Spoke로 전송됩니다.
   * 원본 IP 주소: **172.30.0.4**
   * 대상 IP 주소: 10.200.0.4
1. Spoke에서 회신이 시작됩니다.
   * 원본 IP 주소: 10.200.0.4
   * 대상 IP 주소: **172.30.0.4**
1. 트래픽이 사이트 간 VPN Gateway로 들어가고 변환이 취소되어 온-프레미스로 전송됩니다.
   * 원본 IP 주소: 10.200.0.4
   * 대상 IP 주소: **10.30.0.4**

### <a name="verification-checks"></a>유효성 검사 확인

이 섹션에서는 구성이 제대로 설정되었는지 확인하는 검사를 보여 줍니다. 

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable, 규칙 및 경로 유효성 검사

Virtual WAN의 분기는 **DefaultRouteTable** 에 연결되며, 이는 모든 분기 연결이 DefaultRouteTable 내에 채워진 경로를 학습함을 의미합니다. DefaultRouteTable의 유효 경로에 변환된 접두사로 NAT 규칙이 표시됩니다.

이전 예제에서:

* **접두사:** 172.30.0.0/24  
* **다음 홉 유형:** VPN_S2S_Gateway
* **다음 홉:** VPN_S2S_Gateway Resource

#### <a name="validate-address-prefixes"></a>주소 접두사 유효성 검사

이 예제는 DefaultRouteTable에 연결된 Virtual Networks의 리소스에 적용됩니다.

Virtual WAN Hub에 연결된 Spoke Virtual Network에 있는 가상 머신의 NIC(네트워크 인터페이스 카드)에 대한 **유효 경로** 에는 **수신 NAT 규칙** 에 지정된 **ExternalMapping** 의 주소 접두사도 포함되어야 합니다. 

온-프레미스 디바이스에는 **송신 NAT 규칙** 의 **외부 매핑** 내에 포함된 접두사에 대한 경로도 포함되어야 합니다. 

####  <a name="common-configuration-patterns"></a>일반 구성 패턴 

> [!NOTE]
> 사이트 간 NAT는 정책 기반 트래픽 선택기를 사용하는 사이트 간 VPN 연결에서 지원되지 않습니다.

다음 표에서는 사이트 간 VPN Gateway에서 다양한 유형의 NAT 규칙을 구성할 때 발생하는 일반적인 구성 패턴을 보여 줍니다.  

| VPN 사이트의 유형 | 수신 NAT 규칙 | 송신 NAT 규칙
|--- |--- | ---|
|정적으로 구성된 경로가 있는 VPN 사이트 |NAT 규칙의 **ExternalMapping** 을 포함하도록 VPN 사이트에서 ‘프라이빗 주소 공간’을 편집합니다.| 온-프레미스 디바이스에서 NAT 규칙의 **ExternalMapping** 에 대한 경로를 적용합니다.|
|VPN 사이트(BGP 변환 사용)|VPN 사이트 링크 연결의 BGP 주소에 BGP 피어의 **ExternalMapping** 주소를 배치합니다.  | 특별 고려 사항 없음. |
| VPN 사이트(BGP 변환 사용 안 함) | 온-프레미스 BGP Speaker가 NAT 규칙의 **ExternalMapping** 에 접두사를 보급하도록 합니다. 또한 VPN 사이트 링크 연결의 BGP 주소에 BGP 피어의 ExternalMapping 주소를 배치합니다.| 온-프레미스 디바이스에서 NAT 규칙의 **ExternalMapping** 에 대한 경로를 적용합니다.|

## <a name="next-steps"></a>다음 단계

사이트간 구성에 대한 자세한 내용은 [Virtual WAN 사이트 간 연결 구성](virtual-wan-site-to-site-portal.md)을 참조하세요.
