---
title: '자습서: ExpressRoute 회로에 VNet 연결 - Azure PowerShell'
description: 이 자습서에서는 Resource Manager 배포 모델 및 Azure PowerShell을 사용하여 VNet(가상 네트워크)을 ExpressRoute 회로에 연결하는 방법에 대해 간략히 설명합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: duau
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 366426ee04bd13239a734bbc721cbd6822a34ddd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650024"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>자습서: Virtual Network를 ExpressRoute 회로에 연결
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-linkvnet-classic.md)
>

이 문서를 참조하면 Resource Manager 배포 모델 및 PowerShell을 사용하여 VNet(가상 네트워크)을 Azure ExpressRoute 회로에 연결할 수 있습니다. 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다. 또한 이 문서는 가상 네트워크 링크를 업데이트하는 방법을 보여줍니다.

* 최대 10개의 가상 네트워크를 표준 ExpressRoute 회로에 연결할 수 있습니다. 표준 ExpressRoute 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다. 

* 단일 VNet을 최대 16개의 ExpressRoute 회로에 연결할 수 있습니다. 이 문서의 단계를 사용하여 연결하려는 각 ExpressRoute 회로에 대한 새 연결 개체를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.

* ExpressRoute 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로의 지정학적 지역 외부에서 가상 네트워크를 연결할 수 있습니다. 또한 프리미엄 추가 항목을 사용하면 선택한 대역폭에 따라 10개가 넘는 가상 네트워크를 ExpressRoute 회로에 연결할 수도 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

* ExpressRoute 회로에서 대상 ExpressRoute 가상 네트워크 게이트웨이로 연결을 만들려면 로컬 또는 피어링된 가상 네트워크에서 보급된 주소 공간의 수가 **200** 개 이하여야 합니다. 연결이 성공적으로 만들어지면 로컬 또는 피어링된 가상 네트워크에 최대 1,000개의 추가 주소 공간을 추가할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 동일한 구독에 있는 가상 네트워크를 회로에 연결
> - 다른 구독에 있는 가상 네트워크를 회로에 연결
> - 가상 네트워크 연결 수정
> - ExpressRoute FastPath 구성

## <a name="prerequisites"></a>필수 구성 요소

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다. 
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-arm.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
  * 회로에 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-arm.md) 을 참조하세요. 
  * Azure 개인 피어링이 구성되고 엔드투엔드 연결을 위해 네트워크와 Microsoft 간에 BGP 피어링이 설정되어 있는지 확인합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute의 가상 네트워크 게이트웨이는 GatewayType으로 VPN이 아닌 'ExpressRoute'를 사용합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>동일한 구독에 있는 가상 네트워크를 회로에 연결
다음 cmdlet를 사용하여 ExpressRoute 회로에 가상 네트워크 게이트웨이를 연결할 수 있습니다. cmdlet을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>다른 구독에 있는 가상 네트워크를 회로에 연결
여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

> [!NOTE]
> Azure 소버린 클라우드와 퍼블릭 Azure 클라우드 간의 가상 네트워크 연결은 지원되지 않습니다. 동일한 클라우드에 있는 다른 구독의 가상 네트워크만 연결할 수 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서에서 자체 구독을 사용하여 서비스를 배포하지만, 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크에 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 ExpressRoute 회로를 소유할 수 있습니다. 조직 내의 다른 구독은 ExpressRoute 회로를 사용할 수 있습니다.

> [!NOTE]
> ExpressRoute 회로에 대한 연결 및 대역폭 요금은 구독 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="구독 간 연결":::

### <a name="administration---circuit-owners-and-circuit-users"></a>관리 - 회로 소유자 및 회로 사용자

’회로 소유자’는 ExpressRoute 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 '회로 사용자'가 사용할 수 있는 권한 부여를 만들 수 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**권한 부여를 만들려면**

회로 소유자는 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결하기 위해 회로 사용자가 사용할 권한 부여 키를 만드는 권한 부여를 만듭니다. 권한 부여는 하나의 연결에만 유효합니다.

다음 cmdlet 조각은 권한 부여를 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

이전 명령에 대한 응답에는 권한 부여 키와 상태가 포함됩니다.

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**권한 부여를 검토하려면**

회로 소유자는 다음 cmdlet을 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**권한 부여를 추가하려면**

회로 소유자는 다음 cmdlet를 사용하여 권한 부여를 추가할 수 있습니다.

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**권한 부여를 삭제하려면**

회로 소유자는 다음 cmdlet을 실행하여 권한 부여를 취소/삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>회로 사용자 작업

회로 사용자는 회로 소유자로부터 권한 부여 키를 받아야 하며 피어 ID가 필요합니다. 권한 부여 키는 GUID입니다.

다음 명령에서 피어 ID를 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**연결 권한 부여를 사용하려면**

회로 사용자는 다음 cmdlet을 실행하여 링크 권한 부여를 사용할 수 있습니다.

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**연결 권한 부여를 해제하려면**

ExpressRoute 회로와 가상 네트워크의 연결을 삭제하여 권한 부여를 해제할 수 있습니다.

## <a name="modify-a-virtual-network-connection"></a>가상 네트워크 연결 수정
가상 네트워크 연결의 특정 속성을 업데이트할 수 있습니다. 

**연결 무게를 업데이트하려면**

가상 네트워크를 여러 ExpressRoute 회로에 연결할 수 있습니다. 둘 이상의 ExpressRoute 회로에서 동일한 접두사를 수신할 수도 있습니다. 이 접두사를 대상으로 하는 트래픽을 전송할 연결을 선택하기 위해 연결의 *RoutingWeight* 를 변경할 수 있습니다. 트래픽은 제일 높은 *RoutingWeight* 를 사용한 연결로 전송됩니다.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*RoutingWeight* 의 범위는 0에서 32000입니다. 기본값은 0입니다.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath 구성 
가상 네트워크 게이트웨이가 Ultra Performance 또는 ErGw3AZ인 경우 [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md)를 사용하도록 설정할 수 있습니다. FastPath는 온-프레미스 네트워크와 가상 네트워크 간의 초당 패킷 수 및 초당 연결 수와 같은 데이터 경로 성능을 향상시킵니다. 

**새 연결에서 FastPath 구성**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**FastPath를 사용하도록 기존 연결 업데이트**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

> [!NOTE]
> [연결 모니터](how-to-configure-connection-monitor.md)를 사용하여 트래픽이 FastPath를 사용하여 목적지에 도달하고 있는지 확인할 수 있습니다.
>

## <a name="enroll-in-expressroute-fastpath-features-preview"></a>ExpressRoute FastPath 기능에 등록(미리 보기)

가상 네트워크 피어링에 대한 FastPath 지원은 이제 공개 미리 보기로 제공됩니다.

### <a name="fastpath-and-virtual-network-peering"></a>FastPath 및 가상 네트워크 피어링

FastPath 및 가상 네트워크 피어링을 사용하면 데이터 경로에서 ExpressRoute 가상 네트워크 게이트웨이를 바이패스하여 로컬 또는 피어링된 가상 네트워크의 VM에 직접 ExpressRoute 연결을 사용하도록 설정할 수 있습니다.

이 미리 보기에 등록하려면 대상 Azure 구독에서 다음 Azure PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName ExpressRouteVnetPeeringGatewayBypass -ProviderNamespace Microsoft.Network
```

> [!NOTE] 
> 대상 구독에서 FastPath에 대해 구성된 모든 연결은 이 미리 보기에 등록됩니다. 프로덕션 구독에서는 이 미리 보기를 사용하도록 설정하지 않는 것이 좋습니다.
> 이미 FastPath가 구성되어 있고 미리 보기 기능에 등록하려면 다음을 수행해야 합니다.
> 1. 위의 Azure PowerShell 명령을 사용하여 FastPath 미리 보기 기능에 등록합니다.
> 1. 대상 연결에서 FastPath를 사용하지 않도록 설정했다가 다시 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

ExpressRoute 연결이 더 이상 필요하지 않은 경우 게이트웨이가 있는 구독에서 `Remove-AzVirtualNetworkGatewayConnection` 명령을 사용하여 게이트웨이와 회로 간의 연결을 제거합니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>다음 단계
ExpressRoute에 대한 자세한 내용은 ExpressRoute FAQ를 참조하세요.

> [!div class="nextstepaction"]
> [ExpressRoute FAQ](expressroute-faqs.md)
