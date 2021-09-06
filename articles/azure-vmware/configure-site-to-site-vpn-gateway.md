---
title: vWAN에서 Azure VMware Solution에 대한 사이트 간 VPN 구성
description: Azure VMware 솔루션에 VPN(IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 06/30/2021
ms.openlocfilehash: fc2f62549a9a06122b77e0e8864c029cb6af8029
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122531291"
---
# <a name="configure-a-site-to-site-vpn-in-vwan-for-azure-vmware-solution"></a>vWAN에서 Azure VMware Solution에 대한 사이트 간 VPN 구성

이 문서에서는 Microsoft Azure Virtual WAN 허브에서 VPN(IPsec IKEv1 및 IKEv2) 사이트 간 터널을 설정합니다. 해당 허브에는 Azure VMware Solution ExpressRoute 게이트웨이 및 사이트 간 VPN 게이트웨이가 포함됩니다. 이는 온-프레미스 VPN 디바이스를 Azure VMware Solution 엔드포인트와 연결합니다.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="VPN 사이트 간 터널 아키텍처를 보여 주는 다이어그램." border="false":::

## <a name="prerequisites"></a>사전 요구 사항
온-프레미스 VPN 디바이스에서 종료되는 공용 IP 주소가 있어야 합니다.

## <a name="create-an-azure-virtual-wan"></a>Azure Virtual WAN 만들기

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-virtual-hub"></a>가상 허브 만들기

가상 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 이는 지역에서 Virtual WAN 네트워크의 핵심입니다.  사이트 간 및 ExpressRoute에 대한 게이트웨이를 포함할 수 있습니다. 

>[!TIP]
>[기존 허브에서도 게이트웨이를 만들 수](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub) 있습니다.


[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-vpn-gateway"></a>VPN 게이트웨이 만들기 

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]


## <a name="create-a-site-to-site-vpn"></a>사이트 간 VPN 만들기

1. Azure Portal에서 이전에 만든 가상 WAN을 선택합니다.

2. 가상 허브의 **개요** 에서 **연결** > **VPN(사이트 간)**  > **새 VPN 사이트 만들기** 를 선택합니다.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="VPN(사이트 간) 및 새 VPN 사이트 만들기가 선택된, 가상 허브에 대한 개요 페이지의 스크린샷.":::  
 
3. **기본** 탭에서 필수 필드를 입력합니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="기본 사항 탭이 열려 있는 VPN 사이트 만들기 페이지를 보여 주는 스크린샷" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-basics.png":::

   * **지역** - 이전에는 위치라고 했습니다. 이 사이트 리소스를 만들려는 위치입니다.
   
   * **이름** - 온-프레미스 사이트를 참조할 때 사용하려는 이름입니다.
   
   * **디바이스 공급업체** - VPN 디바이스 공급업체의 이름입니다(예: Citrix, Cisco 또는 Barracuda). 이는 Azure 팀이 환경을 더 잘 이해하고 향후 최적화 가능성을 높이거나 문제를 해결하는 데 도움이 됩니다.

   * **프라이빗 주소 공간** - 온-프레미스 사이트에 있는 CIDR IP 주소 공간입니다. 이 주소 공간으로 향하는 트래픽은 로컬 사이트로 라우팅됩니다. CIDR 블록은 [BGP](../vpn-gateway/bgp-howto.md)를 사이트에 사용하지 않는 경우에만 필요합니다.
    
   >[!NOTE]
   >사이트를 만든 후 주소 공간을 편집하는 경우(예: 추가 주소 공간 추가) 구성 요소가 다시 생성되는 동안 유효 경로를 업데이트하는 데 8-10분이 걸릴 수 있습니다.


1. 분기의 실제 링크에 대한 정보를 추가하려면 **링크** 를 선택합니다. Virtual WAN 파트너 CPE 디바이스가 있는 경우 이 정보가 시스템에서 설정된 분기 정보 업로드의 일부로 Azure와 교환되는지 확인합니다.

   링크 및 공급자 이름을 지정하면 허브의 일부로 생성되는 게이트웨이의 수를 구분할 수 있습니다.  [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) 및 ASN(익명 시스템 번호)은 조직 내에서 고유해야 합니다. BGP는 Azure VMware Solution과 온-프레미스 서버 모두가 터널을 통해 경로를 보급하도록 합니다. 사용하지 않도록 설정하면 보급되어야 하는 서브넷을 수동으로 유지 관리해야 합니다. 서브넷이 누락되면 HCX에서 서비스 메시를 구성하지 못합니다. 
 
   >[!IMPORTANT]
   >기본적으로 Azure는 Azure VPN 게이트웨이에서 Azure BGP IP 주소로 자동으로 게이트웨이 서브넷 접두사 범위에서 개인 IP 주소를 할당합니다. 온-프레미스 VPN 디바이스에서 BGP IP로 APIPA 주소(169.254.0.1 ~ 169.254.255.254)를 사용하는 경우 사용자 지정 Azure APIPA BGP 주소가 필요합니다. 해당 로컬 네트워크 게이트웨이 리소스(온-프레미스 네트워크)에 BGP 피어 IP로 APIPA 주소가 있으면 Azure VPN Gateway에서 사용자 지정 APIPA 주소를 선택합니다. 로컬 네트워크 게이트웨이에서 APIPA가 아닌 일반 IP 주소를 사용하는 경우 Azure VPN Gateway는 게이트웨이 서브넷 범위에서 개인 IP 주소로 돌아갑니다.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/site-links.png" alt-text="링크 탭이 열려 있는 VPN 사이트 만들기 페이지를 보여 주는 스크린샷" lightbox="../../includes/media/virtual-wan-tutorial-site-include/site-links.png":::

1. **검토 + 만들기** 를 선택합니다. 

1. 원하는 가상 허브로 이동하고 **Hub 연결** 을 선택 취소하여 VPN 사이트를 허브에 연결합니다.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="스크린샷은 이 허브에 연결을 보여줍니다." lightbox="../../includes/media/virtual-wan-tutorial-site-include/connect.png":::   

## <a name="optional-create-policy-based-vpn-site-to-site-tunnels"></a>(선택 사항)정책 기반 VPN 사이트 간 터널 만들기

>[!IMPORTANT]
>이 단계는 선택 사항이며 정책 기반 VPN에만 적용됩니다. 

[정책 기반 VPN을 설정](../virtual-wan/virtual-wan-custom-ipsec-portal.md)하려면 허브 범위를 포함하여 온-프레미스 및 Azure VMware Solution 네트워크를 지정해야 합니다.  해당 범위는 정책 기반 VPN 터널 온-프레미스 엔드포인트의 암호화 도메인을 지정합니다.  정책 기반 트래픽 선택기 표시기 사용 설정이 필요한 쪽은 Azure VMware Solution 쪽뿐입니다. 

1. Azure Portal에서 Virtual WAN 허브 사이트로 이동하고 **연결** 에서 **VPN(사이트 간)** 을 선택합니다.

2. 사용자 지정 IPsec 정책을 설정할 VPN 사이트를 선택합니다.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png" alt-text="고객 IPsec 정책을 설정하는 기존 VPN 사이트를 보여 주는 스크린샷" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/locate.png":::

3. VPN 사이트 이름을 선택하고 맨 오른쪽에 있는 **자세히**(...)를 선택한 다음, **VPN 연결 편집** 을 선택합니다.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png" alt-text="기존 VPN 사이트의 바로 가기 메뉴를 보여 주는 스크린샷" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/contextmenu.png":::

   - IPSec(인터넷 프로토콜 보안), **사용자 지정** 을 선택합니다.

   - 정책 기반 트래픽 선택기를 사용하고 **사용** 을 선택합니다.

   - **IKE 1단계** 및 **IKE 2단계(ipsec)** 에 대한 세부 정보를 지정합니다. 

4. IPsec 설정을 기본값에서 사용자 지정으로 변경하고 IPsec 정책을 사용자 지정합니다. 그런 다음 **저장** 을 선택합니다.

   :::image type="content" source="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png" alt-text="기존 VPN 사이트를 보여 주는 스크린샷" lightbox="../virtual-wan/media/virtual-wan-custom-ipsec-portal/edit.png":::

   정책 기반 암호화 도메인에 속하는 트래픽 선택기 또는 서브넷은 다음과 같아야 합니다.
    
   - Virtual WAN 허브 `/24`

   - Azure VMware Solution 프라이빗 클라우드 `/22`

   - 연결된 Azure 가상 네트워크(있는 경우)

## <a name="connect-your-vpn-site-to-the-hub"></a>허브에 VPN 사이트 연결

1. VPN 사이트 이름을 선택하고 **VPN 사이트 연결** 을 선택합니다. 

1. **미리 공유한 키** 필드에서 온-프레미스 엔드포인트에 대해 이전에 정의한 키를 입력합니다. 

   >[!TIP]
   >이전에 정의한 키가 없는 경우 이 필드를 비워둘 수 있습니다. 자동으로 키가 생성됩니다. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="미리 공유한 키 및 관련 설정에 대해 준비된 Virtual HUB의 연결된 사이트 창을 보여 주는 스크린샷."::: 

1. 허브에 방화벽을 배포 중이고 다음 홉인 경우, **기본 경로 전파** 옵션을 **사용** 으로 설정합니다. 

   사용하도록 설정하면 Virtual WAN 허브는 허브에 방화벽을 배포할 때 허브에서 기본 경로를 이미 학습한 경우나 연결된 다른 사이트에서 강제로 터널링을 사용한 경우에만 연결에 전파합니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다.  

1. **연결** 을 선택합니다. 몇 분 후에 사이트에 연결 및 연결 상태가 표시됩니다.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="사이트 간 연결 및 연결 상태를 보여 주는 스크린샷." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

   **연결 상태:** VPN 사이트를 Azure 허브의 VPN 게이트웨이에 연결하는 연결에 대한 Azure 리소스의 상태입니다. 이 컨트롤 플레인 작업이 성공적이면 Azure VPN 게이트웨이와 온-프레미스 VPN 디바이스가 연결을 설정합니다.

   **연결 상태:** 허브에 있는 Azure의 VPN 게이트웨이와 VPN 사이트 사이의 실제 연결(데이터 경로) 상태입니다. 다음과 같은 상태가 표시될 수 있습니다.

    * **알 수 없음**: 일반적으로 백 엔드 시스템이 다른 상태로 전환 작업 중인 경우 표시됩니다.
    * **연결 중**: Azure VPN 게이트웨이가 실제 온-프레미스 VPN 사이트에 연결하는 중입니다.
    * **연결됨**: Azure VPN 게이트웨이와 온-프레미스 VPN 사이트 사이에 연결이 설정되었습니다.
    * **연결 끊김**: 일반적으로 어떤 이유로든(온-프레미스 또는 Azure에서) 연결이 끊긴 경우 표시됩니다.



1. VPN 구성 파일을 다운로드하고 온-프레미스 엔드포인트에 적용합니다.  
   
   1. VPN(사이트 간) 페이지의 위쪽 근처에서 **VPN Config 다운로드** 를 선택합니다. Azure는 'microsoft-network-\[location\]' 리소스 그룹에 스토리지 계정을 만듭니다. 여기서 location은 WAN의 위치입니다. VPN 디바이스에 구성을 적용한 후에는 이 스토리지 계정을 삭제할 수 있습니다.

   1. 만들어지면 링크를 선택하여 다운로드합니다. 

   1. 온-프레미스 VPN 디바이스에 구성을 적용합니다.

   구성 파일에 대한 자세한 내용은 [VPN 디바이스 구성 파일 정보](../virtual-wan/virtual-wan-site-to-site-portal.md#config-file)를 참조하세요.

1. Virtual WAN 허브에서 Azure VMware Solution ExpressRoute를 패치합니다. 

   >[!IMPORTANT]
   >플랫폼을 패치하려면 먼저 프라이빗 클라우드를 만들어야 합니다. 


   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

1. Virtual WAN 허브에서 Azure VMware Solution과 VPN 게이트웨이를 함께 연결합니다. 이전 단계의 권한 부여 키 및 ExpressRoute ID(피어 회로 URI)를 사용하게 됩니다.

   1. ExpressRoute 게이트웨이를 선택하고 **권한 부여 키 사용** 을 선택합니다.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="권한 부여 키 사용이 선택된 프라이빗 클라우드의 ExpressRoute 페이지 스크린샷.":::

   1. **권한 부여 키** 필드에 권한 부여 키를 붙여넣습니다.

   1. ExpressRoute ID를 **피어 회로 URI** 필드에 붙여넣습니다. 

   1. **이 ExpressRoute 회로를 자동으로 허브와 연결합니다** 확인란을 선택합니다. 

   1. **추가** 를 선택하여 링크를 설정합니다. 

1. [NSX-T 세그먼트를 만들고](./tutorial-nsx-t-network-segment.md) 네트워크에서 VM을 프로비저닝하여 연결을 테스트합니다. 온-프레미스 및 Azure VMware Solution 엔드포인트를 모두 ping합니다.

   >[!NOTE]
   >ExpressRoute 회로 뒤의 클라이언트, 예를 들어 앞서 만든 VNet의 VM에서의 연결을 테스트 하기 전에 5분 정도 기다립니다.
