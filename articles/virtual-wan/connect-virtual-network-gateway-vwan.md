---
title: 가상 네트워크 게이트웨이를 Azure Virtual WAN에 연결
description: Azure VPN 게이트웨이(가상 네트워크 게이트웨이)를 Azure Virtual WAN VPN 게이트웨이에 연결하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/01/2021
ms.author: cherylmc
ms.openlocfilehash: 51b254c42f06c3ae6a0609a05e6f36056106139b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432039"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>VPN Gateway(가상 네트워크 게이트웨이)를 Virtual WAN에 연결

이 문서는 Azure VPN Gateway(가상 네트워크 게이트웨이)에서 Azure Virtual WAN(VPN 게이트웨이)으로의 연결을 설정하는 데 도움이 됩니다. VPN Gateway(가상 네트워크 게이트웨이)에서 Virtual WAN(VPN 게이트웨이)으로의 연결을 만드는 것은 분기 VPN 사이트에서 가상 WAN으로의 연결을 설정하는 것과 비슷합니다.

두 기능 간에 발생할 수 있는 혼동을 최소화하기 위해 인용하는 기능의 이름이 게이트웨이 앞에 나옵니다. 예를 들어 VPN Gateway 가상 네트워크 게이트웨이 및 Virtual WAN VPN 게이트웨이입니다.

## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 다음 리소스를 만듭니다.

Azure 가상 WAN

* [가상 WAN을 만듭니다](virtual-wan-site-to-site-portal.md#openvwan).
* [허브를 만듭니다](virtual-wan-site-to-site-portal.md#hub). 가상 허브에는 Virtual WAN VPN 게이트웨이가 포함됩니다.

Azure Virtual Network

* 가상 네트워크 게이트웨이가 없는 가상 네트워크를 만듭니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. VPN Gateway 가상 네트워크 게이트웨이 만들기

**VPN Gateway** 가상 네트워크 게이트웨이를 가상 네트워크에 대한 활성-활성 모드로 만듭니다. 게이트웨이를 만들 때 게이트웨이의 두 인스턴스에 대해 기존 공용 IP 주소를 사용하거나 새 공용 IP를 만들 수 있습니다. 이러한 공용 IP는 Virtual WAN 사이트를 설정할 때 사용합니다. 활성-활성 VPN 게이트웨이 및 구성 단계에 대한 자세한 내용은 [활성-활성 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)을 참조하세요.

### <a name="active-active-mode-setting"></a><a name="active-active"></a>활성-활성 모드 설정

가상 네트워크 게이트웨이 **구성** 페이지에서 활성-활성 모드를 사용하도록 설정합니다.

![활성-활성](./media/connect-virtual-network-gateway-vwan/active.png "액티브-액티브")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 설정

가상 네트워크 게이트웨이 **구성** 페이지에서 필요에 따라 **BGP ASN 구성을** 선택할 수 있습니다. BGP를 구성하는 경우 포털에 표시된 기본값에서 ASN을 변경합니다. 이 구성의 경우 BGP ASN은 65515일 수 없습니다. 65515는 Azure Virtual WAN에서 사용됩니다.

![BGP ASN 구성이 선택된 가상 네트워크 게이트웨이 구성 페이지를 보여 주는 스크린샷](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>공용 IP 주소

게이트웨이가 만들어지면 **속성** 페이지로 이동합니다. 속성 및 구성 설정은 다음 예와 비슷합니다. 게이트웨이에 사용되는 두 개의 공용 IP 주소를 확인합니다.

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Virtual WAN VPN 사이트 만들기

Virtual WAN VPN 사이트를 만들려면 가상 WAN으로 이동한 다음, **연결** 아래에서 **VPN 사이트** 를 선택합니다. 이 섹션에서는 이전 섹션에서 만든 가상 네트워크 게이트웨이에 해당하는 두 개의 Virtual WAN VPN 사이트를 만듭니다.

1. **+ 사이트 만들기** 를 선택합니다.
2. **VPN 사이트 만들기** 페이지에서 다음 값을 입력합니다.

   * **지역** - Azure VPN Gateway 가상 네트워크 게이트웨이와 동일한 지역입니다.
   * **디바이스 공급업체** - 디바이스 공급업체(임의의 이름)를 입력합니다.
   * **개인 주소 공간** - 값을 입력하거나, BGP가 사용하도록 설정된 경우 비워 둡니다.
   * **Border Gateway Protocol** - Azure VPN Gateway 가상 네트워크 게이트웨이에서 BGP를 사용하도록 설정된 경우 **사용** 으로 설정합니다.
   * **허브에 연결** - 드롭다운에서 필수 구성 요소에서 만든 허브를 선택합니다. 허브가 표시되지 않으면 허브에 대한 사이트 간 VPN 게이트웨이를 만들었는지 확인합니다.
3. **링크** 아래에서 다음 값을 입력합니다.

   * **공급자 이름** - 링크 이름 및 공급자 이름(임의의 이름)을 입력합니다.
   * **속도** - 속도(임의의 숫자)입니다.
   * **IP 주소** - IP 주소를 입력합니다((VPN Gateway) 가상 네트워크 게이트웨이 속성 아래에 표시된 첫 번째 공용 IP 주소와 동일).
   * **BGP 주소** 및 **ASN** - BGP 주소 및 ASN입니다. 이러한 값은 [1단계](#vnetgw)에서 구성한 VPN Gateway 가상 네트워크 게이트웨이의 BGP 피어 IP 주소 및 ASN 중 하나와 동일해야 합니다.
4. 검토하고, **확인** 을 선택하여 사이트를 만듭니다.
5. 이전 단계를 반복하여 VPN Gateway 가상 네트워크 게이트웨이의 두 번째 인스턴스와 일치하는 두 번째 사이트를 만듭니다. VPN Gateway 구성에서 두 번째 공용 IP 주소와 두 번째 BGP 피어 IP 주소를 사용하는 것을 제외하고는 동일한 설정을 유지합니다.
6. 이제 두 사이트가 성공적으로 프로비전되었으며, 다음 섹션으로 진행하여 구성 파일을 다운로드할 수 있습니다.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. VPN 구성 파일 다운로드

이 섹션에서는 이전 섹션에서 만든 각 사이트에 대한 VPN 구성 파일을 다운로드합니다.

1. Virtual WAN **VPN 사이트** 페이지의 위쪽에서 **사이트** 를 선택한 다음, **사이트 간 VPN 구성 다운로드** 를 선택합니다. Azure에서 설정이 포함된 구성 파일을 만듭니다.

   !["사이트 간 VPN 구성 다운로드" 작업이 선택된 "VPN 사이트" 페이지를 보여 주는 스크린샷](./media/connect-virtual-network-gateway-vwan/download.png "다운로드로 사용 가능한 제품 설명서에서 데이터 공급자 설치 섹션을 참조하세요")
2. 구성 파일을 다운로드하여 엽니다.
3. 두 번째 사이트에 대해 이러한 단계를 반복합니다. 두 구성 파일을 모두 열면 다음 섹션으로 진행할 수 있습니다.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 로컬 네트워크 게이트웨이 네트워크

이 섹션에서는 두 개의 Azure VPN Gateway 로컬 네트워크 게이트웨이를 만듭니다. 이전 단계의 구성 파일에는 게이트웨이 구성 설정이 포함되어 있습니다. 이러한 설정을 사용하여 Azure VPN Gateway 로컬 네트워크 게이트웨이를 만들고 구성합니다.

1. 이러한 설정을 사용하여 로컬 네트워크 게이트웨이를 만듭니다. VPN Gateway 로컬 네트워크 게이트웨이를 만드는 방법에 대한 자세한 내용은 [로컬 네트워크 게이트웨이 만들기](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) VPN Gateway 문서를 참조하세요.

   * **IP 주소** - 구성 파일에서 *gatewayconfiguration* 에 대해 표시된 Instance0 IP 주소를 사용합니다.
   * **BGP** - 연결이 BGP를 통과하는 경우 **BGP 설정 구성** 을 선택하고, ASN '65515'를 입력합니다. BGP 피어 IP 주소를 입력합니다. 구성 파일에서 *gatewayconfiguration* 에 대해 표시된 'Instance0 BgpPeeringAddresses'를 사용합니다.
   * **주소 공간** 연결이 BGP를 통해 연결되지 않은 경우 **BGP 설정 구성이** 선택되지 않은 상태로 유지되는지 확인합니다.  가상 네트워크 게이트웨이 쪽에서 보급하려는 주소 공간을 입력합니다. 주소 공간 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 범위가 연결하려는 다른 네트워크의 범위와 겹치지 않는지 확인합니다. 
   * **구독, 리소스 그룹 및 위치** 는 Virtual WAN 허브와 동일합니다.
2. 검토하고, 로컬 네트워크 게이트웨이를 만듭니다. 로컬 네트워크 게이트웨이는 다음 예와 비슷합니다.

   ![IP 주소가 강조 표시되고 "BGP 설정 구성"이 선택된 "구성" 페이지를 보여 주는 스크린샷](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. 이러한 단계를 반복하여 다른 로컬 네트워크 게이트웨이를 만들지만, 이번에는 구성 파일의 'Instance0' 값 대신 'Instance1' 값을 사용합니다.

   ![구성 파일 다운로드](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 연결 만들기

이 섹션에서는 VPN Gateway 로컬 네트워크 게이트웨이와 가상 네트워크 게이트웨이 간의 연결을 만듭니다. VPN Gateway 연결을 만드는 방법에 대한 단계는 [연결 구성](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)을 참조하세요.

1. 포털에서 가상 네트워크 게이트웨이로 이동하여 **연결** 을 클릭합니다. 연결 페이지의 맨 위에 있는 **+추가** 를 클릭하여 **연결 추가** 페이지를 엽니다.
2. **연결 추가** 페이지에서 연결에 대해 다음 값을 구성합니다.

   * **이름:** 연결의 이름을 지정합니다.
   * **연결 형식:** **사이트 간(IPSec)** 을 선택합니다.
   * **가상 네트워크 게이트웨이:** 이 게이트웨이에서 연결하기 때문에 값이 고정됩니다.
   * **로컬 네트워크 게이트웨이:** 이 연결은 가상 네트워크 게이트웨이를 로컬 네트워크 게이트웨이에 연결합니다. 이전에 만든 로컬 네트워크 게이트웨이 중 하나를 선택합니다.
   * **공유 키:** 공유 키를 입력합니다.
   * **IKE 프로토콜:** IKE 프로토콜을 선택합니다.
3. **확인** 을 클릭하여 연결을 만듭니다.
4. 가상 네트워크 게이트웨이의 **연결** 페이지에서 연결을 볼 수 있습니다.

   ![연결](./media/connect-virtual-network-gateway-vwan/connect.png "connection")
5. 이전 단계를 반복하여 두 번째 연결을 만듭니다. 두 번째 연결의 경우 만든 다른 로컬 네트워크 게이트웨이를 선택합니다.
6. 연결이 BGP를 통과하는 경우 연결이 만들어지면 연결로 이동하여 **구성** 을 선택합니다. **구성** 페이지에서 **BGP** 에 대해 **사용** 을 선택합니다. 그런 다음 **저장** 을 클릭합니다. 두 번째 연결에 대해 반복합니다.

## <a name="6-test-connections"></a><a name="test"></a>6. 연결 테스트

두 개의 가상 머신을 VPN Gateway 가상 네트워크 게이트웨이 측면 및 Virtual WAN에 대한 가상 네트워크에 각각 하나씩 만든 다음, 두 가상 머신을 ping하여 연결을 테스트할 수 있습니다.

1. 가상 머신을 Azure VPN Gateway(Test1-VNG)에 대한 가상 네트워크(Test1-VNet)에 만듭니다. 이 가상 머신을 GatewaySubnet에 만들지 마세요.
2. 가상 WAN에 연결할 다른 가상 네트워크를 만듭니다. 가상 머신을 이 가상 네트워크의 서브넷에 만듭니다. 이 가상 네트워크에는 가상 네트워크 게이트웨이가 포함될 수 없습니다. 가상 네트워크는 [사이트 간 연결](virtual-wan-site-to-site-portal.md#vnet) 문서의 PowerShell 단계를 사용하여 빠르게 만들 수 있습니다. cmdlet을 실행하기 전에 먼저 값을 변경해야 합니다.
3. VNet을 Virtual WAN 허브에 연결합니다. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결** 을 선택한 다음, **+ 연결 추가** 를 선택합니다. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인** 을 클릭하여 가상 네트워크 연결을 만듭니다.
5. 이제 VM 간의 연결이 설정되었습니다. 통신을 차단하는 방화벽 또는 다른 정책이 없으면 한 VM을 다른 VM에서 ping할 수 있습니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 IPsec 정책을 구성하는 단계는 [Virtual WAN에 대한 사용자 지정 IPsec 정책 구성](virtual-wan-custom-ipsec-portal.md)을 참조하세요.
Virtual WAN에 대한 자세한 내용은 [Azure Virtual WAN 정보](virtual-wan-about.md) 및 [Azure Virtual WAN FAQ](virtual-wan-faq.md)를 참조하세요.