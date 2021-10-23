---
title: 'Azure ExpressRoute: Azure PowerShell을 사용하여 IPv6 지원 추가'
description: Azure PowerShell을 사용하여 Azure 배포에 연결하기 위해 IPv6 지원을 추가하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 04248f1cf0e0d9ecb659ca3271000a0a28a6a97b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242315"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 피어 링에 대 한 IPv6 지원 추가

이 문서에서는 Azure PowerShell을 사용하여 ExpressRoute를 통해 Azure의 리소스에 연결하기 위해 IPv6 지원을 추가하는 방법을 설명합니다.

## <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 회로에 IPv6 개인 피어링 추가

1. [ExpressRoute 회로를 만들거나](./expressroute-howto-circuit-arm.md) 기존 회로를 사용합니다. **Get-AzExpressRouteCircuit** 명령을 실행하여 회로를 검색합니다.

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. **Get-AzExpressRouteCircuitPeeringConfig** 를 실행하여 회로에 대한 개인 피어링 구성을 검색합니다.

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. 기존 IPv4 개인 피어링 구성에 IPv6 개인 피어링을 추가합니다. 기본 링크 및 보조 링크에 대해 소유하고 있는 /126 IPv6 서브넷 한 쌍을 제공합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.

    > [!Note]
    > 피어 ASN 및 VlanId는 IPv4 개인 피어링 구성에 있는 것과 일치해야 합니다.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. 구성이 저장되면 **Get-AzExpressRouteCircuit** 명령을 실행하여 회로를 다시 가져옵니다. 응답은 다음 예제와 유사해야 합니다.

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 연결 업데이트

IPv6 개인 피어링을 사용하려는 Azure 리소스의 기존 환경이 있는 경우 다음 단계를 수행합니다.

1. ExpressRoute 회로가 연결된 가상 네트워크를 검색합니다.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. 가상 네트워크에 IPv6 주소 공간을 추가합니다.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. 게이트웨이 서브넷에 IPv6 주소 공간을 추가합니다. 게이트웨이 IPv6 서브넷은 /64 이상이어야 합니다.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. 기존 영역 중복 게이트웨이가 있는 경우 다음을 실행하여 IPv6 연결을 사용하도록 설정합니다(변경 사항이 반영되는 데 최대 1시간이 걸릴 수 있음). 그렇지 않으면 SKU를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](./expressroute-howto-add-gateway-resource-manager.md). FastPath를 사용하려는 경우 UltraPerformance 또는 ErGw3AZ를 사용합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에서만 사용 가능).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```
>[!NOTE]
> 영역 중복이 아닌 기존 게이트웨이 (표준, 고성능 또는 Ultra Performance SKU)가 있는 경우 SKU 및 표준 고정 공용 IP 주소를 사용 하 여 게이트웨이를 삭제 하 고 [다시 만들어야](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) 합니다.

## <a name="create-a-connection-to-a-new-virtual-network"></a>새 가상 네트워크에 대한 연결 만들기

IPv6 프라이빗 피어링을 사용하여 새로운 Azure 리소스 집합에 연결하려는 경우 아래 단계를 따르세요.

1. IPv4/IPv6 주소 공간을 모두 사용하여 이중 스택 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 참조하세요.

2. [이중 스택 게이트웨이 서브넷을 만듭니다](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. SKU를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](./expressroute-howto-add-gateway-resource-manager.md#add-a-gateway). FastPath를 사용하려는 경우 UltraPerformance 또는 ErGw3AZ를 사용합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에서만 사용 가능).

4. [가상 네트워크를 ExpressRoute 회로에 연결합니다](./expressroute-howto-linkvnet-arm.md).

## <a name="limitations"></a>제한 사항
IPv6은 공용 Azure 지역에서 배포에 연결하는 데 사용할 수 있지만 다음과 같은 사용 사례를 지원하지는 않습니다.

* 영역 중복 되지 않는 *기존* express 경로 게이트웨이에 대 한 연결입니다. 표준 고정 IP 주소를 사용 하 여 모든 SKU (영역 중복 및 아님 모두)의 *새로* 만든 express 경로 게이트웨이는 이중 스택 express 경로 연결에 사용할 수 있습니다.
* ExpressRoute 회로 간의 Global Reach 연결
* 가상 WAN으로 ExpressRoute 사용
* 비ExpressRoute Direct 회로가 있는 FastPath
* 다음 피어링 위치, 즉 두바이에 회로가 있는 FastPath
* VPN Gateway와 함께 사용

## <a name="next-steps"></a>다음 단계

ExpressRoute 문제를 해결하려면 다음 문서를 참조하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
