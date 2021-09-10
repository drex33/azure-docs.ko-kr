---
title: ExpressRoute 개인 피어링을 통한 사이트 간 VPN 연결
titleSuffix: Azure VPN Gateway
description: 트래픽을 암호화하기 위해 ExpressRoute 개인 피어링을 통해 사이트 간 VPN 연결을 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 68dee7e8d00f3f1b6853a7be5c9328647d96371d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272082"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>ExpressRoute 개인 피어링을 통한 사이트 간 VPN 구성

RFC 1918 IP 주소를 사용하여 ExpressRoute 개인 피어링을 통해 사이트 간 VPN을 가상 네트워크 게이트웨이에 구성할 수 있습니다. 이렇게 구성하면 다음과 같은 이점이 있습니다.

* 개인 피어링을 통한 트래픽이 암호화됩니다.

* 가상 네트워크 게이트웨이에 연결하는 지점 및 사이트 간 사용자는 ExpressRoute(사이트 간 터널을 통해)를 사용하여 온-프레미스 리소스에 액세스할 수 있습니다.

* ExpressRoute 개인 피어링을 통해 사이트 간 VPN 연결을 동일한 VPN 게이트웨이에서 인터넷을 통한 사이트 간 VPN 연결과 동시에 배포할 수 있습니다.

>[!NOTE]
>이 기능은 영역 중복 게이트웨이에서만 지원됩니다. 예: VpnGw1AZ, VpnGw2AZ 등
>

이 구성을 완료하려면 다음 사전 요구 사항을 충족하는지 확인합니다.

* VPN Gateway가 생성되거나 생성될 VNet에 연결되는 작동하는 ExpressRoute 회로가 있습니다.

* ExpressRoute 회로를 통해 VNet의 RFC1918(개인) IP를 통해 리소스에 연결할 수 있습니다.

## <a name="routing"></a><a name="routing"></a>라우팅

**그림 1** 에서는 ExpressRoute 개인 피어링을 통한 VPN 연결의 예를 보여 줍니다. 이 예제에서는 ExpressRoute 개인 피어링을 통해 Azure 허브 VPN Gateway에 연결된 온-프레미스 네트워크 내의 네트워크를 볼 수 있습니다. 이 구성에서 중요한 점은 ExpressRoute와 VPN 경로를 통한 온-프레미스 네트워크와 Azure 간의 라우팅입니다.

그림 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="그림 1":::

다음과 같이 연결 설정은 간단합니다.

1. ExpressRoute 회로와 개인 피어링을 사용하여 ExpressRoute 연결을 설정합니다.

1. 이 문서에 안내된 단계를 사용하여 VPN 연결을 설정합니다.

### <a name="traffic-from-on-premises-networks-to-azure"></a>온-프레미스 네트워크에서 Azure로의 트래픽

온-프레미스 네트워크에서 Azure로의 트래픽에 대해 Azure 접두사는 ExpressRoute 개인 피어링 BGP와 VPN BGP 모두를 통해 보급됩니다. 그러면 온-프레미스 네트워크에서 Azure에 대한 두 개의 네트워크 경로(경로)가 생성됩니다.

• IPsec로 보호된 경로를 통한 하나의 네트워크 경로

• IPsec를 보호하지 않고 ExpressRoute를 직접 통한 하나의 네트워크 경로

통신에 암호화를 적용하려면 **그림 1** 에서 VPN에 연결된 네트워크에 대해 온-프레미스 VPN Gateway를 통한 Azure 경로가 직접 ExpressRoute보다 우선적으로 사용되도록 해야 합니다.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure에서 온-프레미스 네트워크로의 트래픽

Azure에서 온-프레미스 네트워크로의 트래픽에는 동일한 요구 사항이 적용됩니다. IPsec 경로가 직접 ExpressRoute 경로(IPsec 없이)보다 우선적으로 사용되도록 하기 위해 다음 두 가지 옵션을 사용할 수 있습니다.

• **VPN 연결 네트워크에 대한 VPN BGP 세션에서 더 많은 특정 접두사를 보급** 합니다. ExpressRoute 개인 피어링을 통해 VPN에 연결된 네트워크를 포함하는 더 큰 범위를 보급하고 VPN BGP 세션에서 더 구체적인 범위를 확인할 수 있습니다. 예를 들어, ExpressRoute를 통한 10.0.0.0/16 및 VPN을 통한 10.0.1.0/24를 보급합니다.

• **VPN 및 ExpressRoute에 대한 분리형 접두사를 보급** 합니다. VPN에 연결된 네트워크 범위가 다른 ExpressRoute 연결 네트워크와 분리된 경우 VPN 및 ExpressRoute BGP 세션에서 각각 접두사를 보급할 수 있습니다. 예를 들어, ExpressRoute를 통한 10.0.0.0/24및 VPN을 통한 10.0.1.0/24를 보급합니다.

이러한 두 예제에서 Azure는 VPN 보호 없이 ExpressRoute를 직접 사용하는 대신 VPN 연결을 통해 10.0.1.0/24로 트래픽을 전송합니다.

>[!Warning]
>ExpressRoute와 VPN 연결에 모두 동일한 접두사를 보급하는 경우 Azure는 VPN 보호 없이 직접 ExpressRoute 경로를 사용합니다.
>

## <a name="portal-steps"></a><a name="portal"></a>포털 단계

1. 사이트 간 연결을 구성합니다. 단계는 [사이트 간 구성](./tutorial-site-to-site-portal.md) 문서를 참조하세요. 게이트웨이에 대한 영역 중복 게이트웨이 SKU를 선택해야 합니다. 

   영역 중복 SKU는 SKU의 끝에 "AZ"가 있습니다. 예를 들면 **VpnGw1AZ** 입니다. 영역 중복 게이트웨이는 가용성 영역 서비스를 사용할 수 있는 지역에서만 사용할 수 있습니다. 가용성 영역을 지원하는 지역에 대한 자세한 내용은 [가용성 영역을 지원하는 지역](../availability-zones/az-region.md)을 참조하세요.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="게이트웨이 개인 IP":::
1. 게이트웨이에서 개인 IP를 사용하도록 설정합니다. **구성** 을 선택한 다음 **게이트웨이 개인 IP** 를 **사용** 을 설정합니다. **저장** 을 선택하여 변경 내용을 저장합니다.
1. **개요** 페이지에서 **더 보기** 를 선택하여 개인 IP 주소를 봅니다. 나중에 구성 단계에서 사용할 수 있도록 이 정보를 적어 둡니다.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="개요 페이지" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 연결에서 **Azure 개인 IP 주소 사용** 을 사용하도록 설정하려면 **구성** 을 선택합니다. **Azure 개인 IP 주소 사용** 을 **사용** 으로 설정한 다음 **저장** 을 선택합니다.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="게이트웨이 개인 IP - 사용":::
1. 3단계에서 적어둔 개인 IP를 방화벽에서 ping합니다. 개인 IP는 ExpressRoute 개인 피어링을 통해 연결할 수 있어야 합니다.
1. 이 개인 IP를 온-프레미스 방화벽에서 원격 IP로 사용하여 ExpressRoute 개인 피어링을 통한 사이트 간 터널을 설정할 수 있습니다.

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell 단계

1. 사이트 간 연결을 구성합니다. 단계에 대하 자세한 내용은 [사이트 간 VPN 구성](./tutorial-site-to-site-portal.md) 문서를 참조하세요. 게이트웨이에 대한 영역 중복 게이트웨이 SKU를 선택해야 합니다. 영역 중복 SKU는 SKU의 끝에 "AZ"가 있습니다. 예를 들면 VpnGw1AZ입니다.
1. 다음 PowerShell 명령을 사용하여 게이트웨이에서 개인 IP를 사용하도록 플래그를 설정합니다.

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   공용 및 개인 IP 주소가 표시되어야 합니다. 출력의 "TunnelIpAddresses" 섹션 아래에 있는 IP 주소를 적어 둡니다. 이후 단계에서 이 정보가 필요합니다.
1. 다음 PowerShell 명령을 사용하여 개인 IP 주소를 사용하도록 연결을 설정합니다.

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. 2단계에서 적어둔 개인 IP를 방화벽에서 ping합니다. 개인 IP는 ExpressRoute 개인 피어링을 통해 연결할 수 있어야 합니다.
1. 이 개인 IP를 온-프레미스 방화벽에서 원격 IP로 사용하여 ExpressRoute 개인 피어링을 통한 사이트 간 터널을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VPN Gateway에 대한 자세한 내용은 [VPN Gateway란?](vpn-gateway-about-vpngateways.md) 참조
