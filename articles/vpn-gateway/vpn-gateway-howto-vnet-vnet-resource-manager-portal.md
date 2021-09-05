---
title: 'VNet 간 VPN 게이트웨이 연결 구성: Azure Portal'
titleSuffix: Azure VPN Gateway
description: VNet 간에 VPN 게이트웨이 연결을 만드는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: 3e8c2846b58499e5aabdec80f8fcd75cab3e6eb5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535785"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Azure Portal을 사용하여 VNet-VNet 간 VPN Gateway 연결 구성

이 문서에서는 VNet-VNet 연결 형식을 사용하여 VNet(가상 네트워크)를 연결합니다. 가상 네트워크가 서로 다른 지역과 구독에 있어도 됩니다. 다른 구독의 VNet을 연결할 때 구독을 동일한 Active Directory 테넌트에 연결할 필요는 없습니다. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="VNet 간 다이어그램":::

이 문서의 단계는 [Azure Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 적용되며 Azure Portal을 사용합니다. 다음 문서에 설명된 옵션을 사용하여 다른 배포 도구 또는 모델을 통해 이 구성을 만들 수 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [다양한 배포 모델 간 연결 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [다양한 배포 모델 간 연결 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>VNet 연결 정보

다음 섹션에서는 가상 네트워크를 연결하는 다양한 방법을 설명합니다.

### <a name="vnet-to-vnet"></a>VNet 간

VNet-VNet 연결 구성은 간단하게 VNet을 연결할 수 있는 방법입니다. VNet-VNet 연결 형식(VNet2VNet)을 사용하여 가상 네트워크를 다른 가상 네트워크에 연결하는 것은 온-프레미스 위치에 사이트 간 IPsec 연결을 설정하는 것과 비슷합니다. 두 연결 형식 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공하며 둘 다 동일한 방식으로 통신합니다. 그러나 로컬 네트워크 게이트웨이를 구성하는 방법이 다릅니다. 

VNet-VNet 연결을 설정할 때 로컬 네트워크 게이트웨이 주소 공간이 자동으로 생성되고 채워집니다. 한 VNet의 주소 공간을 업데이트하면 다른 VNet이 자동으로 업데이트된 주소 공간으로 라우팅됩니다. 일반적으로 사이트 간 연결보다는 VNet-VNet 연결이 더 빠르고 쉽습니다.

### <a name="site-to-site-ipsec"></a>사이트 간(IPsec)

복잡한 네트워크 구성을 작업하는 경우 [사이트 간 연결](./tutorial-site-to-site-portal.md)을 사용하여 VNet에 연결하는 것이 좋습니다. 사이트 간 IPsec 단계를 수행할 때 로컬 네트워크 게이트웨이를 수동으로 만들고 구성합니다. 각 VNet의 로컬 네트워크 게이트웨이는 다른 VNet을 로컬 사이트로 처리합니다. 이러한 단계를 통해 로컬 네트워크 게이트웨이가 트래픽을 라우팅할 추가 주소 공간을 지정할 수 있습니다. VNet의 주소 공간이 변경되면 해당 로컬 네트워크 게이트웨이를 수동으로 업데이트해야 합니다.

### <a name="vnet-peering"></a>VNet 피어링

VNet 피어링을 사용하여 VNet을 연결할 수도 있습니다. VNet 피어링은 VPN 게이트웨이를 사용하지 않으며 여러 제약 조건이 있습니다. 또한 [VNet 피어링 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)은 [VNet-VNet VPN Gateway 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway)과 다르게 계산됩니다. 자세한 내용은 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

## <a name="why-create-a-vnet-to-vnet-connection"></a>VNet-VNet 연결을 만드는 이유

VNet-VNet 연결을 사용하여 가상 네트워크를 연결하는 이유는 다음과 같습니다.

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>지역 간 지리적 중복 및 지리적 상태

* 인터넷 연결 엔드포인트로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
* Azure Traffic Manager 및 Azure Load Balancer를 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 예를 들어 여러 Azure 지역에서 SQL Server Always On 가용성 그룹을 설정할 수 있습니다.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>분리 또는 관리 경계를 가진 지역별 다중 계층 애플리케이션

* 같은 지역 내에서 격리 또는 관리 요구 사항 때문에 여러 가상 네트워크가 서로 연결된 다중 계층 애플리케이션을 설정할 수 있습니다.

VNet-VNet 통신을 다중 사이트 구성과 결합할 수 있습니다. 이렇게 구성하면 다음 다이어그램에 표시된 것처럼 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="VNet 연결 다이어그램":::

이 문서에서는 VNet-VNet 연결 형식을 사용하여 VNet을 연결하는 방법을 보여줍니다. 이러한 단계를 연습으로 사용하는 경우 다음 예제 설정 값을 사용할 수 있습니다. 예제에서는 가상 네트워크가 동일한 구독에 있지만 다른 리소스 그룹에 있습니다. VNet이 다른 구독에 있으면 포털에서 연결을 만들 수 없습니다. [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 또는 [CLI](vpn-gateway-howto-vnet-vnet-cli.md)를 대신 사용합니다. VNet-VNet 연결에 대한 자세한 내용은 [VNet-VNet FAQ](#vnet-to-vnet-faq)를 참조하세요.

### <a name="example-settings"></a>예제 설정

**VNet1에 대한 값:**

* **가상 네트워크 설정**
  * **이름**: VNet1
  * **주소 공간**: 10.1.0.0/16
  * **구독**: 사용할 구독을 선택합니다.
  * **리소스 그룹**: TestRG1
  * **위치**: 미국 동부
  * **서브넷**
    * **이름**: FrontEnd
    * **주소 범위**: 10.1.0.0/24

* **가상 네트워크 게이트웨이 설정**
  * **이름**: VNet1GW
  * **리소스 그룹**: 미국 동부
  * **세대**: 2세대
  * **게이트웨이 유형**: **VPN** 을 선택합니다.
  * **VPN 유형**: **경로 기반** 을 선택합니다.
  * **SKU**: VpnGw2
  * **가상 네트워크**: VNet1
  * **게이트웨이 서브넷 주소 범위**: 10.1.255.0/27
  * **공용 IP 주소**: 새로 만들기
  * **공용 IP 주소 이름**: VNet1GWpip

* **연결**
  * **이름**: VNet1toVNet4
  * **공유 키**: 공유 키를 직접 만들 수 있습니다. VNet 간 연결을 만들 때 값이 일치해야 합니다. 이 연습에서는 abc123을 사용합니다.

**VNet4에 대한 값:**

* **가상 네트워크 설정**
  * **이름**: VNet4
  * **주소 공간**: 10.41.0.0/16
  * **구독**: 사용할 구독을 선택합니다.
  * **리소스 그룹**: TestRG4
  * **위치**: 미국 서부
  * **서브넷**
  * **이름**: FrontEnd
  * **주소 범위**: 10.41.0.0/24

* **가상 네트워크 게이트웨이 설정**
  * **이름**: VNet4GW
  * **리소스 그룹**: 미국 서부
  * **세대**: 2세대
  * **게이트웨이 유형**: **VPN** 을 선택합니다.
  * **VPN 유형**: **경로 기반** 을 선택합니다.
  * **SKU**: VpnGw2
  * **가상 네트워크**: VNet4
  * **게이트웨이 서브넷 주소 범위**: 10.41.255.0/27
  * **공용 IP 주소**: 새로 만들기
  * **공용 IP 주소 이름**: VNet4GWpip

* **연결**
  * **이름**: VNet4toVNet1
  * **공유 키**: 공유 키를 직접 만들 수 있습니다. VNet 간 연결을 만들 때 값이 일치해야 합니다. 이 연습에서는 abc123을 사용합니다.

## <a name="create-and-configure-vnet1"></a>VNet1 만들기 및 구성

VNet이 이미 있는 경우 설정이 VPN 게이트웨이 설계와 호환되는지 확인합니다. 다른 네트워크와 겹칠 수 있는 서브넷에 특히 주의합니다. 겹치는 서브넷에 있으면 연결이 제대로 작동하지 않습니다.

### <a name="to-create-a-virtual-network"></a>가상 네트워크를 만들려면

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>VNet1 게이트웨이 만들기

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다. 연습으로 이 구성을 만드는 경우 [예제 설정](#example-settings)을 참조하세요.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이를 만들려면

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-portal-include.md)]

배포 상태는 게이트웨이에 대한 [개요] 페이지에서 확인할 수 있습니다. 게이트웨이에서 완전히 만들고 배포하는 데 45분 이상 걸릴 수 있습니다. 게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 디바이스로 표시됩니다.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>VNet4 만들기 및 구성

VNet1을 구성한 후 이전 단계를 반복하고 값을 VNet4 값으로 대체하여 VNet4 및 VNet4 게이트웨이를 만듭니다. VNet1에 대한 가상 네트워크 게이트웨이 만들기가 완료될 때까지 기다릴 필요 없이 VNet4를 구성할 수 있습니다. 고유한 값을 사용하는 경우에 주소 공간에 연결하려는 VNet을 사용하여 겹치지 않는지 확인합니다.

## <a name="configure-the-vnet1-gateway-connection"></a>VNet1 게이트웨이 연결 구성

가상 네트워크 게이트웨이 VNet1 및 VNet4를 모두 완료했을 때 게이트웨이 연결 가상 네트워크를 만들 수 있습니다. 이 섹션에서는 VNet1에서 VNet4에 연결을 만듭니다. 이러한 단계는 동일한 구독에 있는 Vnet에 대해서만 작동합니다. VNet이 다른 구독에 있으면 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)을 사용하여 연결해야 합니다. 그러나 VNet이 동일한 구독의 다른 리소스 그룹에 있는 경우 포털을 사용하여 연결할 수 있습니다.

1. Azure Portal에서 **모든 리소스** 를 선택하고, 검색 상자에 *가상 네트워크 게이트웨이* 를 입력하고, VNet의 가상 네트워크 게이트웨이로 이동합니다. 예를 들어 **VNet1GW** 입니다. 게이트웨이를 선택하여 **가상 네트워크 게이트웨이** 페이지를 엽니다.
1. 게이트웨이 페이지에서 **설정->연결** 로 이동합니다. 그런 다음, **+추가** 를 선택합니다.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="연결 페이지를 보여주는 스크린샷." border="false":::
1. **연결 추가** 페이지가 열립니다.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="연결 추가 페이지를 보여주는 스크린샷.":::

   **연결 추가** 페이지에서 연결의 값을 입력합니다.

   * **이름**: 연결의 이름을 입력합니다. 예를 들어 *VNet1toVNet4* 입니다.

   * **연결 형식**: 드롭다운에서 **VNet-VNet** 을 선택합니다.

   * **첫 번째 가상 네트워크 게이트웨이**: 이 필드 값은 지정된 가상 네트워크 게이트웨이에서 이 연결을 만들고 있으므로 자동으로 입력됩니다.

   * **두 번째 가상 네트워크 게이트웨이**: 이 필드는 연결을 만들고자 하는 VNet의 가상 네트워크 게이트웨이입니다. **다른 가상 네트워크 게이트웨이 선택** 을 선택하여 **선택 가상 네트워크 게이트웨이** 페이지를 엽니다.

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="다른 게이트웨이가 선택된 가상 네트워크 게이트웨이 선택 페이지를 보여주는 스크린샷.":::

     * 이 페이지에 나열된 가상 네트워크 게이트웨이를 봅니다. 구독에 있는 가상 네트워크 게이트웨이만 나열되어 있는지 확인합니다. 구독에 없는 가상 네트워크 게이트웨이에 연결하려면 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)을 사용합니다.

     * 연결하려는 가상 네트워크 게이트웨이를 선택합니다.

   * **공유 키(PSK)** : 이 필드에서 연결의 공유 키를 입력합니다. 이 키를 생성하거나 직접 만들 수 있습니다. 사이트 간 연결에 사용되는 키는 온-프레미스 디바이스 및 가상 네트워크 게이트웨이 연결에서 사용하는 키와 동일합니다. 개념은 비슷하지만, 여기서는 VPN 디바이스에 연결하지 않고 다른 가상 네트워크 게이트웨이를 연결합니다.
1. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="configure-the-vnet4-gateway-connection"></a>VNet4 게이트웨이 연결 구성

다음으로 VNet4에서 VNet1로의 연결을 만듭니다. 포털에서 VNet4와 연결된 가상 네트워크 게이트웨이를 찾습니다. 이전 섹션의 단계를 따라 VNet4에서 VNet1로 연결을 만들기 위해 값을 교체합니다. 동일한 공유 키를 사용하고 있는지 확인합니다.

## <a name="verify-your-connections"></a>연결 확인

1. Azure Portal에서 가상 네트워크 게이트웨이를 찾습니다. 
1. **가상 네트워크 게이트웨이** 페이지에서 **연결** 을 선택하여 가상 네트워크 게이트웨이에 대한 **연결** 페이지를 봅니다. 연결이 설정되면 **상태** 값이 **연결됨** 으로 변경됩니다.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="연결을 확인하는 연결 페이지를 보여주는 스크린샷." border="false":::
1. **이름** 열 아래에서 연결 중 하나를 선택하여 자세한 정보를 확인합니다. 데이터 흐름이 시작되면 **데이터 입력** 및 **데이터 출력** 값이 보입니다.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="스크린샷은 데이터 입력 및 데이터 출력 값이 있는 리소스 그룹을 보여줍니다." border="false":::

## <a name="add-additional-connections"></a>연결 추가

연결을 추가하려면 연결을 만들 가상 네트워크 게이트웨이로 이동하여 **연결** 을 선택합니다. 다른 VNet 대 VNet 연결을 만들거나 온-프레미스 위치에 대한 IPsec 사이트 간 연결을 만들 수 있습니다. **연결 형식** 을 만들려는 연결 형식과 일치하도록 조정해야 합니다. 추가 연결을 만들기 전에, 가상 네트워크에 대한 주소 공간이 연결하려는 주소 공간과 겹치지 않는지 확인합니다. 사이트 간 연결을 만드는 단계는 [사이트 간 연결 만들기](./tutorial-site-to-site-portal.md)를 참조하세요.

## <a name="vnet-to-vnet-faq"></a>VNet-VNet FAQ

VNet 간 연결에 대한 자세한 내용은 FAQ 세부 정보를 봅니다.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에서 리소스에 대한 네트워크 트래픽을 제한하는 방법에 대한 자세한 내용은 [네트워크 보안](../virtual-network/network-security-groups-overview.md)을 참조하세요.

* Azure에서 트래픽을 Azure, 온-프레미스 및 인터넷 리소스간에 라우팅하는 방법에 대한 정보는 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.