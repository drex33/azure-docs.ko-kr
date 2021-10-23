---
title: 'Azure ExpressRoute: Azure Portal을 사용하여 IPv6 지원 추가'
description: Azure Portal를 사용하여 Azure 배포에 연결하기 위해 IPv6 지원을 추가하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: fb7f606b0427a5b3f5293fd82bfd9a0544bc945f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226341"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal"></a>Azure Portal를 사용 하 여 개인 피어 링에 대 한 IPv6 지원 추가

이 문서에서는 Azure Portal를 사용하여 Azure에서 ExpressRoute를 통해 Azure의 리소스에 연결하기 위해 IPv6 지원을 추가하는 방법을 설명합니다.

>[!NOTE]
> 포털 환경의 일부 측면은 아직 구현 중입니다. 따라서 포털을 통해 IPv6 지원을 추가 하려면이 문서에서 제공 하는 지침의 정확한 순서를 따르세요. 특히 포털에서 새 가상 네트워크 게이트웨이를 만들기 *전에* 가상 네트워크 및 서브넷을 만들거나 기존 가상 네트워크 및 서브넷에 IPv6 주소 공간을 추가 해야 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고 Azure 계정으로 로그인합니다.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 회로에 IPv6 개인 피어링 추가

1. [ExpressRoute 회로를 만들거나](expressroute-howto-circuit-portal-resource-manager.md) 변경하려는 기존 회로로 이동합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute 회로 목록의 스크린샷.":::

1. **Azure 프라이빗** 피어링 구성을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="ExpressRoute 개요 페이지의 스크린샷.":::

1. **서브넷** 에 대해 "모두"를 선택하여 기존 IPv4 개인 피어링 구성에 IPv6 개인 피어링을 추가하거나, "IPv6"를 선택하여 새 회로에서 IPv6 개인 피어링만 사용하도록 설정합니다. 기본 링크 및 보조 링크에 대해 소유하고 있는 /126 IPv6 서브넷 한 쌍을 제공합니다. 이러한 각 서브넷에서는 Microsoft에서 사용 가능한 첫 번째 IP 주소를 해당 라우터의 두 번째 사용 가능한 IP로 사용하므로 이를 라우터로 할당합니다. 모든 매개 변수를 지정한 후에 피어링 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="개인 피어링 페이지에서 Ipv6를 추가하는 스크린샷.":::

1. 구성이 성공적으로 수락되고 나면 다음 예와 유사한 내용을 볼 수 있습니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="개인 피어링용으로 구성된 Ipv6 스크린샷.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 연결 업데이트

IPv6 개인 피어링을 사용하려는 Azure 리소스의 기존 환경이 있는 경우 다음 단계를 수행합니다.

1. ExpressRoute 회로가 연결된 가상 네트워크로 이동합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="가상 네트워크 목록의 스크린샷.":::

1. **주소 공간** 탭으로 이동하여 가상 네트워크에 IPv6 주소 공간을 추가합니다. 주소 공간을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="가상 네트워크에 Ipv6 주소 공간을 추가하는 스크린샷.":::

1. **서브넷** 탭으로 이동하여 **게이트웨이 서브넷** 을 선택합니다. **IPv6 주소 공간 추가** 를 선택하고 서브넷에 대한 IPv6 주소 공간을 제공합니다. 게이트웨이 IPv6 서브넷은/64 이상이어야 합니다. 모든 매개 변수를 지정한 후에 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="서브넷에 Ipv6 주소 공간을 추가하는 스크린샷.":::
    
1. 기존 영역 중복 게이트웨이가 있는 경우 PowerShell에서 다음을 실행하여 IPv6 연결을 사용하도록 설정합니다(변경 사항이 반영되는 데 최대 1시간이 걸릴 수 있음). 그렇지 않으면 SKU와 표준, 고정 공용 IP 주소를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](./expressroute-howto-add-gateway-portal-resource-manager.md). FastPath를 사용하려는 경우, UltraPerformance 또는 ErGw3AZ를 사용 합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에만 사용할 수 있음).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    
>[!NOTE]
> If you have an existing gateway that is not zone-redundant (meaning it is Standard, High Performance, or Ultra Performance SKU), you will need to delete and [recreate the gateway](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) using any SKU and a Standard, Static public IP address.

## Create a connection to a new virtual network

Follow the steps below if you plan to connect to a new set of Azure resources using your IPv6 Private Peering.

1. Create a dual-stack virtual network with both IPv4 and IPv6 address space. For more information, see [Create a virtual network](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Create the dual-stack gateway subnet](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Create the virtual network gateway](expressroute-howto-add-gateway-resource-manager.md) using any SKU and a Standard, Static public IP address. If you plan to use FastPath, use UltraPerformance or ErGw3AZ (note that this option is only available for circuits using ExpressRoute Direct). **NOTE:** Please use the PowerShell instructions for this step as the Azure portal experience is still under development.

1. [Link your virtual network to your ExpressRoute circuit](expressroute-howto-linkvnet-portal-resource-manager.md).

## Limitations
While IPv6 support is available for connections to deployments in Public Azure regions, it doesn't support the following use cases:

* Connections to *existing* ExpressRoute gateways that are not zone-redundant. Note that *newly* created ExpressRoute gateways of any SKU (both zone-redundant and not) using  a Standard, Static IP address can be used for dual-stack ExpressRoute connections
* Global Reach connections between ExpressRoute circuits
* Use of ExpressRoute with virtual WAN
* FastPath with non-ExpressRoute Direct circuits
* FastPath with circuits in the following peering locations: Dubai
* Coexistence with VPN Gateway

## Next steps

To troubleshoot ExpressRoute problems, see the following articles:

* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)
