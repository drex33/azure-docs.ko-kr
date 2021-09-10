---
title: 'VPN Gateway에 BGP 구성: 포털'
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway용 BGP를 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: df42925b50cba4d32ea554ae54dbcb09a5e04377
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205956"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Azure VPN Gateway에서 BGP 구성 방법

이 문서에서는 Azure Portal을 사용하여 프레미스 간 S2S(사이트 간) VPN 연결 및 VNet 간 연결에서 BGP를 사용하도록 설정하는 단계를 안내합니다.

## <a name="about-bgp"></a><a name="about"></a>BGP 정보

BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 정보를 교환하도록 인터넷에서 일반적으로 사용하는 표준 라우팅 프로토콜입니다. BGP를 통해 Azure VPN 게이트웨이 및 온-프레미스 VPN 디바이스(BGP 피어 또는 인접이라고 함)는 관련된 게이트웨이 또는 라우터를 거치도록 해당 접두사의 가용성 및 연결 가능성에 대한 정보를 두 게이트웨이에 제공하는 "경로"를 교환할 수 있습니다. BGP 게이트웨이가 하나의 BGP 피어에서 파악한 경로를 다른 모든 BGP 피어로 전파하여 BGP를 통해 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다.

BGP의 이점에 대한 자세한 내용 및 BGP 사용의 기술 요구 사항과 고려 사항을 이해하려면 [Azure VPN Gateway에서의 BGP 개요](vpn-gateway-bgp-overview.md)를 참조하세요.

## <a name="getting-started"></a>시작

이 문서의 각 부는 네트워크 연결에서 BGP를 사용하기 위한 기본 구성 요소를 형성하는 데 도움이 됩니다. 세 부를 모두 완료하면 다이어그램 1에 표시된 대로 토폴로지를 빌드합니다.

**다이어그램 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="네트워크 아키텍처 및 설정을 보여 주는 다이어그램" border="false":::

파트를 결합하여 필요에 따라 더 복잡한 다중 홉 전송 네트워크를 빌드할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>1부: 가상 네트워크 게이트웨이에서 BGP 구성

이 섹션에서는 가상 네트워크를 만들고 구성하고, BGP 매개 변수를 사용하여 가상 네트워크 게이트웨이를 만들고 구성하고, Azure BGP 피어 IP 주소를 가져옵니다. 다이어그램 2는 이 섹션의 단계를 수행할 때 사용할 구성 설정을 보여 줍니다.

**다이어그램 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="가상 네트워크 게이트웨이에 대한 설정을 보여 주는 다이어그램" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. TestVNet1만들기 및 구성

이 단계에서는 TestVNet1을 만들고 구성합니다. [게이트웨이 만들기 자습서](./tutorial-create-gateway-portal.md)의 단계를 사용하여 Azure 가상 네트워크 및 VPN 게이트웨이를 만들고 구성합니다. 아래 스크린샷에서 참조 설정을 사용합니다.

* 가상 네트워크:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="해당 주소 접두사가 있는 TestVNet1":::

* 서브넷:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1서브넷":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. BGP 매개 변수를 사용하여 TestVNet1용 VPN 게이트웨이 만들기

이 단계에서는 해당 BGP 매개 변수를 사용하여 VPN 게이트웨이를 만듭니다.

1. Azure Portal에서 Marketplace의 **Virtual Network 게이트웨이** 리소스로 이동하고 **만들기** 를 선택합니다.

1. 아래에 표시된 매개 변수를 입력합니다.

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="VNG1 만들기":::

1. 페이지의 선택된 **BGP 구성** 섹션에서 다음 설정을 구성합니다.

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="BGP 구성":::

   * BGP 구성 화면을 표시하려면 **BGP 구성** - **사용** 을 선택합니다.

   * ASN(자치 시스템 번호)을 입력합니다.

   * **AZURE APIPA BGP IP 주소** 필드는 선택 사항입니다. 온-프레미스 VPN 디바이스에서 BGP에 대해 APIPA 주소를 사용하는 경우 VPN에 대한 Azure 예약 APIPA 주소 범위(**169.254.21.0** 에서 **169.254.22.255** 로)의 주소를 선택해야 합니다. 이 예에서는 169.254.21.11을 사용합니다.

   * 활성-활성 VPN 게이트웨이를 만드는 경우 BGP 섹션에 추가 **두 번째 사용자 지정 Azure APIPA BGP IP 주소** 가 표시됩니다. 허용되는 APIPA 범위(**169.254.21.0** ~ **169.254.22.255**)에서 다른 IP 주소를 선택합니다. 두 번째 IP 주소는 첫 번째 주소와 달라야 합니다.

   > [!IMPORTANT]
   >
   > * 기본적으로 Azure는 Azure VPN 게이트웨이에서 Azure BGP IP 주소로 자동으로 게이트웨이 서브넷 접두사 범위에서 개인 IP 주소를 할당합니다. 온-프레미스 VPN 디바이스에서 BGP IP로 APIPA 주소(169.254.0.1 ~ 169.254.255.254)를 사용하는 경우 사용자 지정 Azure APIPA BGP 주소가 필요합니다. 해당 로컬 네트워크 게이트웨이 리소스(온-프레미스 네트워크)에 BGP 피어 IP로 APIPA 주소가 있으면 Azure VPN Gateway에서 사용자 지정 APIPA 주소를 선택합니다. 로컬 네트워크 게이트웨이에서 APIPA가 아닌 일반 IP 주소를 사용하는 경우 Azure VPN Gateway는 게이트웨이 서브넷 범위에서 개인 IP 주소로 돌아갑니다.
   >
   > * APIPA BGP 주소는 온-프레미스 VPN 디바이스와 연결된 모든 Azure VPN 게이트웨이 간에 겹치지 않아야 합니다.
   >
   > * Azure VPN 게이트웨이에서 APIPA 주소를 사용하는 경우 게이트웨이는 APIPA 원본 IP 주소를 사용하여 BGP 피어링 세션을 시작하지 않습니다. 온-프레미스 VPN 디바이스에서 BGP 피어링 연결을 시작해야 합니다.
   >

1. **검토 + 만들기** 를 선택하여 유효성 검사를 실행합니다. 유효성 검사를 통과하면 **만들기** 를 선택하여 VPN 게이트웨이를 배포합니다. 게이트웨이에서 완전히 만들고 배포하는 데 최대 45분이 걸릴 수 있습니다. 배포 상태는 게이트웨이에 대한 [개요] 페이지에서 확인할 수 있습니다.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Azure BGP 피어 IP 주소 가져오기

게이트웨이를 만든 후 Azure VPN 게이트웨이에서 BGP 피어 IP 주소를 가져올 수 있습니다. 이러한 주소는 Azure VPN 게이트웨이를 사용하여 BGP 세션을 설정하도록 온-프레미스 VPN 디바이스를 구성하는 데 필요합니다.

1. 가상 네트워크 게이트웨이 리소스로 이동하고 **구성** 페이지를 선택하여 다음 스크린샷에 표시된 대로 BGP 구성 정보를 확인합니다. 이 페이지에서는 Azure VPN 게이트웨이의 모든 BGP 구성 정보를 볼 수 있습니다. ASN, 공용 IP 주소 및 Azure 측(기본 및 APIPA)의 해당 BGP 피어 IP 주소입니다.

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP 게이트웨이":::

1. **구성** 페이지에서 다음과 같이 구성을 변경할 수 있습니다.

   * 필요한 경우 ASN 또는 APIPA BGP IP 주소를 업데이트할 수 있습니다.
   * 활성-활성 VPN 게이트웨이가 있는 경우 이 페이지에는 두 번째 Azure VPN 게이트웨이 인스턴스의 공용 IP 주소, 기본 및 APIPA BGP IP 주소가 표시됩니다.

1. 변경한 경우 **저장** 을 선택하여 Azure VPN 게이트웨이에 변경 내용을 커밋합니다.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>2부: 프레미스 간 S2S 연결에서 BGP 구성

프레미스 간 연결을 설정하려면 [사이트 간 연결 만들기](./tutorial-site-to-site-portal.md)에 설명된 대로 온-프레미스 VPN 디바이스를 나타내는 *로컬 네트워크 게이트웨이* 를 만들고 VPN 게이트웨이와 로컬 네트워크 게이트웨이를 연결하는 *연결* 을 만들어야 합니다. 이 문서에는 BGP 구성 매개 변수를 지정하는 데 필요한 추가 속성이 포함되어 있습니다.

**다이어그램 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="IPsec을 보여 주는 다이어그램" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. 로컬 네트워크 게이트웨이에서 BGP 구성

이 단계에서는 로컬 네트워크 게이트웨이에서 BGP를 구성합니다. 예제로 다음 스크린샷을 사용합니다. 스크린샷에는 다이어그램 3에 지정된 매개 변수를 사용하여 로컬 네트워크 게이트웨이(Site5)가 표시됩니다.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="로컬 네트워크 게이트웨이에 대한 BGP 구성":::

#### <a name="important-configuration-considerations"></a>중요한 구성 고려 사항

* ASN 및 BGP 피어 IP 주소는 온-프레미스 VPN 라우터 구성과 일치해야 합니다.
* BGP를 사용하여 이 네트워크에 연결하는 경우에만 **주소 공간** 을 비워 둘 수 있습니다. Azure VPN 게이트웨이는 내부적으로 BGP 피어 IP 주소의 경로를 해당하는 IPsec 터널에 추가합니다. Azure VPN 게이트웨이와 이 특정 네트워크 간에 BGP를 사용하지 **않는** 경우 **주소 공간** 에 대한 유효한 주소 접두사 목록을 **제공해야 합니다**.
* 필요한 경우 **APIPA IP 주소**(169.254)를 온-프레미스 BGP 피어 IP로 사용할 수 있습니다. 그러나 Azure VPN 게이트웨이에 대한 이 문서의 앞부분에서 설명한 대로 APIPA IP 주소를 지정해야 합니다. 그러지 않으면 BGP 세션에서 이 연결을 설정할 수 없습니다.
* 로컬 네트워크 게이트웨이를 만드는 동안 BGP 구성 정보를 입력하거나, 로컬 네트워크 게이트웨이 리소스의 **구성** 페이지에서 BGP 구성을 추가하거나 변경할 수 있습니다.

**예제**

이 예제에서는 169.254.100.1(APIPA 주소)를 온-프레미스 BGP 피어 IP 주소로 사용합니다.

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="로컬 네트워크 게이트웨이 APIPA 및 BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. BGP를 사용하여 S2S 연결 구성

이 단계에서는 BGP를 사용하도록 설정된 새 연결을 만듭니다. 이미 연결이 있고 BGP를 사용하도록 설정하려는 경우 [기존 연결을 업데이트](#update)할 수 있습니다.

#### <a name="to-create-a-connection-with-bgp-enabled"></a>BGP를 사용하여 연결을 만들려면

BGP를 사용하여 새 연결을 만들려면 **연결 추가** 페이지에서 값을 입력한 다음 **BGP 사용** 옵션을 선택하여 이 연결에 대해 BGP를 사용하도록 설정합니다. **확인** 을 선택하여 연결을 만듭니다.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="BGP를 사용하여 IPsec 프레미스 간 연결 설정":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>기존 연결을 업데이트하는 방법

연결에서 BGP 옵션을 변경하려면 연결 리소스의 **구성** 페이지로 이동한 후 다음 예제에서 강조 표시된 대로 **BGP** 옵션을 설정/해제합니다. **저장** 을 선택하여 변경 내용을 저장합니다.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="연결에 대한 BGP 업데이트":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>3부: VNet 간 연결에서 BGP 구성

VNet 간 연결에서 BGP를 사용하거나 사용하지 않도록 설정하는 단계는 [2부](#crosspremises)의 S2S 단계와 동일합니다. 연결을 만들 때 BGP를 사용하도록 설정하거나 기존 VNet 간 연결에 대한 구성을 업데이트할 수 있습니다.

>[!NOTE] 
>BGP를 사용하지 않는 VNet 간 연결에서는 두 개의 연결된 VNet만 통신을 제한합니다. BGP를 사용하도록 설정하여 이 두 VNet의 다른 S2S 또는 VNet 간 연결에 대한 전송 라우팅 기능을 허용합니다.
>

**다이어그램 4** 와 같이 컨텍스트에 대해 TestVNet2와 TestVNet1간에 BGP를 사용하지 않도록 설정하는 경우 TestVNet2는 온-프레미스 네트워크 사이트 5에 대한 경로를 알 수 없으므로 사이트 5와 통신할 수 없습니다. 다이어그램 4와 같이 BGP를 사용하도록 설정하면 세 네트워크는 모두 IPsec 및 VNet 간 연결을 통해 통신할 수 있습니다.

**다이어그램 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="전체 네트워크를 보여 주는 다이어그램" border="false":::

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.