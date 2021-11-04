---
title: '빠른 시작: Azure PowerShell을 사용하여 Route Server 만들기 및 구성'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Route Server를 만들고 구성하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.author: duau
ms.date: 09/01/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: a9467e2c876cdb324a85c9be3f42cd3867a24709
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023598"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Route Server 만들기 및 구성

이 문서는 Azure PowerShell을 사용하여 가상 네트워크의 NVA(네트워크 가상 어플라이언스)와 피어링되도록 Azure Route Server를 구성하는 데 유용합니다. Route Server는 NVA에서 경로를 학습하고 가상 네트워크의 가상 머신에 대해 프로그래밍합니다. 또한 Azure Route Server는 NVA에 가상 네트워크 경로를 보급합니다. 자세한 내용은 [Azure Route Server](overview.md)를 참조하세요.

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure PowerShell을 사용하는 Route Server 배포 환경의 다이어그램." border="false":::

> [!IMPORTANT]
> 9월 1일 이전에 만든 Azure Route Server가 있고 공용 IP 주소가 분리되지 않은 경우 관리 목적으로 IP 주소를 가져올 수 있도록 Route Server를 다시 만들어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 최신 PowerShell 모듈이 있는지 확인하거나 포털에서 Azure Cloud Shell을 사용할 수 있습니다.
* [Azure Route Server에 대한 서비스 제한](route-server-faq.md#limitations)을 검토합니다.
* 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-resource-group-and-a-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기 

Azure Route Server를 만들려면 먼저 Route Server를 호스팅할 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 **myRouteServerRG** 라는 리소스 그룹을 **WestUS** 위치에 만듭니다.

```azurepowershell-interactive
$rg = @{
    Name = 'myRouteServerRG'
    Location = 'WestUS'
}
New-AzResourceGroup @rg
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 이 예제에서는 **WestUS** 위치에 **myVirtualNetwork** 라는 이름의 기본 가상 네트워크를 만듭니다. 가상 네트워크가 있으면 다음 섹션으로 건너뛸 수 있습니다.

```azurepowershell-interactive
$vnet = @{
    Name = 'myVirtualNetwork'
    ResourceGroupName = 'myRouteServerRG'
    Location = 'WestUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-dedicated-subnet"></a>전용 서브넷 추가

Azure Route Server에는 *RouteServerSubnet* 이라는 전용 서브넷이 필요합니다. 서브넷 크기는 최소 /27 또는 짧은 접두사(예: /26 또는 /25)여야 합니다. 그렇지 않으면 Route Server를 배포할 때 오류 메시지가 표시됩니다. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)를 사용하여 **RouteServerSubnet** 이라는 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnet = @{
    Name = 'RouteServerSubnet'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet

$virtualnetwork | Set-AzVirtualNetwork
```

## <a name="create-the-route-server"></a>Route Server 만들기

1. Route Server 구성을 관리하는 백엔드 서비스에 연결되도록 보장하려면 공용 IP 주소를 할당해야 합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)로 **RouteServerIP** 라는 표준 공용 IP를 만듭니다.

    ```azurepowershell-interactive
    $ip = @{
        Name = 'myRouteServerIP'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        AllocationMethod = 'Static'
        IpAddressVersion = 'Ipv4'
        Sku = 'Standard'
    }
    $publicIp = New-AzPublicIpAddress @ip
    ```
    
2. [New-AzRouteServer](/powershell/module/az.network/new-azrouteserver)로 Azure Route Server를 만듭니다. 이 예제는 **WestUS** 위치에서 **myRouteServer** 라는 Azure Route Server를 만듭니다. *HostedSubnet* 은 이전 섹션에서 만든 RouteServerSubnet의 리소스 ID입니다.

    ```azurepowershell-interactive
    $rs = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        Location = 'WestUS'
        HostedSubnet = $subnetConfig.Id
        PublicIP = $publicIp
    }
    New-AzRouteServer @rs 
    ```

## <a name="create-bgp-peering-with-an-nva"></a>NVA와 BGP 피어링 만들기

BGP 피어링을 Route Server에서 NVA로 설정하려면 [Add-AzRouteServerPeer](/powershell/module/az.network/add-azrouteserverpeer)를 사용합니다.

“your_nva_ip”는 NVA에 할당된 가상 네트워크 IP입니다. “your_nva_asn”은 NVA에 구성된 ASN(자율 시스템 번호)입니다. ASN은 65515-65520 범위 이외의 16비트 숫자가 될 수 있습니다. 이 ASN 범위는 Microsoft에서 예약되어 있습니다.

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA"
    PeerIp = '192.168.0.1'
    PeerAsn = '65501'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = myRouteServerRG'
}
Add-AzRouteServerPeer @peer
```

중복성을 위해 다른 NVA 또는 동일한 NVA의 다른 인스턴스와 피어링을 설정하려면 다른 *PeerName*, *PeerIp* 및 *PeerAsn* 을 사용하여 위와 동일한 명령을 사용합니다.

## <a name="complete-the-configuration-on-the-nva"></a>NVA에 대한 구성 완료

NVA에 대한 구성을 완료하고 BGP 세션을 사용하도록 설정하려면 Azure Route Server의 IP 및 ASN이 필요합니다. [Get-AzRouteServer](/powershell/module/az.network/get-azrouteserver)를 사용하여 이 정보를 얻을 수 있습니다.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Get-AzRouteServer @routeserver
```

출력은 다음과 같이 표시됩니다.

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>경로 교환 구성

ExpressRoute 및 Azure VPN 게이트웨이가 동일한 가상 네트워크에 있고 이를 사용해서 경로를 교환하려면 Azure Route Server에서 경로 교환을 사용하도록 설정할 수 있습니다.

1. Azure Route Server와 게이트웨이 사이의 경로 교환을 사용하도록 설정하려면 *-AllowBranchToBranchTraffic* 플래그와 함께 [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver)를 사용합니다.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Update-AzRouteServer @routeserver 
```

2. Azure Route Server와 게이트웨이 사이의 경로 교환을 사용하지 않도록 설정하려면 *-AllowBranchToBranchTraffic* 플래그 없이 [Update-AzRouteServer](/powershell/module/az.network/update-azrouteserver)를 사용합니다.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
}  
Update-AzRouteServer @routeserver 
```

## <a name="troubleshooting"></a>문제 해결

Azure Route Server에서 보급된 경로를 보려면 [Get-AzRouteServerPeerAdvertisedRoute](/powershell/module/az.network/get-azrouteserverpeeradvertisedroute)를 사용합니다.

```azurepowershell-interactive
$remotepeer = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    PeerName = 'myNVA'
}
Get-AzRouteServerPeerAdvertisedRoute @routeserver
```

Azure Route Server에서 학습된 경로를 보려면 [Get-AzRouteServerPeerLearnedRoute](/powershell/module/az.network/get-azrouteserverpeerlearnedroute)를 사용합니다.

```azurepowershell-interactive
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
    AllowBranchToBranchTraffic
}  
Get-AzRouteServerPeerLearnedRoute @routeserver
```
## <a name="clean-up-resources"></a>리소스 정리

Azure Route Server가 더 이상 필요하지 않으면 첫 번째 명령을 사용하여 BGP 피어링을 제거한 후 두 번째 명령을 사용하여 Route Server를 제거합니다. 

1. [Remove-AzRouteServerPeer](/powershell/module/az.network/remove-azrouteserverpeer)를 사용하여 Azure Route Server와 NVA 간의 BGP 피어링을 제거합니다.

```azurepowershell-interactive
$peer = @{
    PeerName = 'myNVA'
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServerPeer @peer
```

2. [Remove-AzRouteServer](/powershell/module/az.network/remove-azrouteserver)를 사용하여 Azure Route Server를 제거합니다.

```azurepowershell-interactive 
$routeserver = @{
    RouteServerName = 'myRouteServer'
    ResourceGroupName = 'myRouteServerRG'
} 
Remove-AzRouteServer @routeserver
```

## <a name="next-steps"></a>다음 단계

Azure Route Server를 만든 후 계속해서 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법을 알아봅니다. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)
