---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5f52297778ec6dc30e96aeac00cd8751b6b39582
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638296"
---
1. **가상 WAN -> VPN 사이트** 로 이동하여 **VPN 사이트** 페이지를 엽니다.
1. **VPN 사이트** 페이지에서 **+사이트 만들기** 를 클릭합니다.
1. **VPN 사이트 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="스크린샷은 기본 사항 탭이 열려 있는 VPN 사이트 만들기 페이지를 보여줍니다." lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **지역**: 이전에는 위치라고 했습니다. 이 사이트 리소스를 만들려는 위치입니다.
    * **이름**: 온-프레미스 사이트를 참조할 때 사용하려는 이름입니다.
    * **디바이스 공급업체**: VPN 디바이스 공급업체의 이름입니다(예: Citrix, Cisco, Barracuda). 디바이스 공급 업체를 추가하면 Azure 팀이 향후 추가 최적화 가능성을 높이거나 문제 해결을 돕기 위해 사용자 환경을 더 잘 이해하는 데 도움이 될 수 있습니다.
    * **프라이빗 주소 공간**: 온-프레미스 사이트에 있는 IP 주소 공간입니다. 이 주소 공간으로 향하는 트래픽은 로컬 사이트로 라우팅됩니다. 사이트에 대해 BGP를 사용하도록 설정하지 않은 경우에 필요합니다.
    
      >[!NOTE]
      >사이트를 만든 후 주소 공간을 편집하는 경우(예: 추가 주소 공간 추가) 구성 요소가 다시 생성되는 동안 유효 경로를 업데이트하는 데 8-10분이 걸릴 수 있습니다.
      >
1. 분기의 실제 링크에 대한 정보를 추가하려면 **링크** 를 선택합니다. Virtual WAN 파트너 CPE 디바이스가 있는 경우 이 정보가 시스템에서 설정된 분기 정보 업로드의 일부로 Azure와 교환되는지 확인합니다.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/links.png" alt-text="스크린샷은 링크 탭이 열려 있는 VPN 사이트 만들기 페이지를 보여줍니다." lightbox="./media/virtual-wan-tutorial-site-include/links.png":::

   * **링크 이름**: VPN 사이트의 물리적 링크에 제공하려는 이름입니다. 예: mylink1
   * **링크 속도**: 분기 위치에서 VPN 디바이스의 속도입니다. 예제: 50(분기 사이트에서 VPN 디바이스의 속도가 50Mbps임을 의미함)
   * **링크 공급자 이름**: VPN 사이트에 있는 물리적 링크의 이름입니다. 예제: ATT, Verizon.
   * **링크 IP 주소/FQDN**: 이 링크를 사용하는 온-프레미스 디바이스의 공용 IP 주소입니다. 필요에 따라 Express 경로 뒤에 있는 온-프레미스 VPN 디바이스의 프라이빗 IP 주소를 제공할 수 있습니다. 정규화된 도메인 이름이 포함될 수도 있습니다. 예를 들어 *something.contoso.com* 입니다. VPN 게이트웨이에서 FQDN을 확인할 수 있어야 합니다. 이는 이 FQDN을 호스팅하는 DNS 서버에 인터넷을 통해 연결할 수 있는 경우에 가능합니다. IP 주소와 FQDN이 모두 지정된 경우 IP 주소가 우선적으로 적용됩니다.

     >[!NOTE]
     >
     >* FQDN당 하나의 IPv4 주소를 지원합니다. FQDN을 여러 IP 주소로 확인해야 하는 경우 VPN 게이트웨이는 목록에서 첫 번째 IP4 주소를 선택합니다. 현재 IPv6 주소는 지원되지 않습니다.
     >
     >* VPN 게이트웨이는 5분마다 새로 고쳐지는 DNS 캐시를 유지 관리합니다. 게이트웨이가 연결되지 않은 터널에 대해서만 FQDN을 확인하려고 합니다. 게이트웨이 다시 설정 또는 구성 변경으로 FQDN 확인이 트리거될 수도 있습니다.
     >
   * **링크 Border Gateway Protocol**: 가상 WAN 링크에서 BGP를 구성하는 것은 Azure 가상 네트워크 게이트웨이 VPN에서 BGP를 구성하는 것과 같습니다. 온-프레미스 BGP 피어 주소는 VPN 디바이스의 퍼블릭 IP 주소 또는 VPN 사이트의 VNet 주소 공간과 같으면 절대 안 됩니다. VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용하세요. 디바이스에서 루프백 인터페이스에 할당한 주소가 될 수 있습니다. 위치를 나타내는 해당 VPN 사이트에서 이 주소를 지정합니다.  BGP 필수 조건은 [Azure VPN Gateway의 BGP 정보](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요. 항상 VPN 링크 연결을 편집하여 BGP 매개 변수(링크의 피어링 IP 및 AS #)를 업데이트할 수 있습니다.
1. 더 많은 링크를 추가하거나 삭제할 수 있습니다. VPN 사이트당 4개의 링크가 지원됩니다. 예를 들어, 분기 위치에 4개의 ISP(인터넷 서비스 공급자)가 있는 경우 각 ISP당 1개씩 4개의 링크를 만들고 각 링크에 대한 정보를 제공할 수 있습니다.
1. 필드 작성이 끝나면 **검토 + 만들기** 를 선택하여 확인합니다. **만들기** 를 클릭하여 사이트를 만듭니다.
1. **VPN 사이트** 페이지에서 **허브 연결: 이 허브에 연결됨** 을 클릭하여 필터를 지웁니다.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="스크린샷은 이 허브에 연결을 보여줍니다." lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
1. 필터가 지워지면 사이트를 볼 수 있습니다.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/sites.png" alt-text="사이트를 보여 주는 스크린샷.":::
