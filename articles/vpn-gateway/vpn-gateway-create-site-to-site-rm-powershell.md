---
title: '온-프레미스 네트워크를 Azure VNet에 연결: 사이트 간 VPN: PowerShell'
description: PowerShell을 사용하여 온-프레미스 네트워크와 Azure VNet 간에 사이트 간 VPN Gateway 연결을 만드는 방법을 알아봅니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7994fa2c2f45cf11e7d2a29a082942c5b7cbe282
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535791"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>PowerShell을 사용하여 사이트 간 VPN 연결로 VNet 만들기

이 문서에서는 PowerShell을 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. 이 문서의 단계는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.

![사이트 간 VPN Gateway 크로스-프레미스 연결 다이어그램](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 호환되는 VPN 디바이스 및 이 디바이스를 구성할 수 있는 사람이 있는지 확인합니다. 호환되는 VPN 디바이스 및 디바이스 구성에 대한 자세한 내용은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 디바이스에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>예제 값

이 문서의 예제에서는 다음 값을 사용합니다. 이러한 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. 가상 네트워크 및 게이트웨이 서브넷 만들기

가상 네트워크가 아직 없는 경우 만듭니다. 가상 네트워크를 만들 때 지정하는 주소 공간이 온-프레미스 네트워크에 있는 주소 공간과 겹치지 않는지 확인하세요. 

>[!NOTE]
>이 VNet을 온-프레미스 위치에 연결하려면 온-프레미스 네트워크 관리자와 협의하여 이 가상 네트워크에 특별히 사용할 수 있는 IP 주소 범위를 만들어야 합니다. VPN 연결의 양쪽 모두에 중복 주소 범위가 있는 경우 트래픽이 예상대로 라우팅되지 않습니다. 또한 이 VNet을 다른 VNet에 연결하려면 주소 공간이 다른 VNet과 겹치면 안됩니다. 이에 따라 네트워크 구성을 적절히 계획하도록 주의해야 합니다.
>
>

### <a name="about-the-gateway-subnet"></a>게이트웨이 서브넷 정보

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>가상 네트워크 및 게이트웨이 서브넷 만들기

이 예제에서는 가상 네트워크 및 게이트웨이 서브넷을 만듭니다. 게이트웨이 서브넷을 추가해야 하는 가상 네트워크가 이미 있는 경우 [이미 만든 가상 네트워크에 게이트웨이 서브넷을 추가하려면](#gatewaysubnet)을 참조하세요.

리소스 그룹 만들기:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

가상 네트워크를 만듭니다.

1. 변수를 설정합니다.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. VNet을 만듭니다.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>이미 만든 가상 네트워크에 게이트웨이 서브넷을 추가하려면

이미 가상 네트워크가 있지만 게이트웨이 서브넷을 추가해야 하는 경우 이 섹션의 단계를 사용합니다.

1. 변수를 설정합니다.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. 게이트웨이 서브넷을 만듭니다.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. 구성을 설정합니다.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a>로컬 네트워크 게이트웨이 만들기

LNG(로컬 네트워크 게이트웨이)는 일반적으로 온-프레미스 위치를 가리킵니다. 가상 네트워크 게이트웨이와 동일하지 않습니다. Azure가 참조할 수 있는 사이트 이름을 지정한 다음, 연결을 만들 온-프레미스 VPN 디바이스의 IP 주소를 지정합니다. 또한 VPN Gateway를 통해 VPN 디바이스로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되면 이러한 접두사를 쉽게 업데이트할 수 있습니다.

다음 값을 사용합니다.

* *GatewayIPAddress* 는 온-프레미스 VPN 디바이스의 IP 주소입니다.
* *AddressPrefix* 는 온-프레미스 주소 공간입니다.

로컬 네트워크 게이트웨이에 단일 주소 접두사를 추가하려면:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

로컬 네트워크 게이트웨이에 중복 주소 접두사를 추가하려면:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 수정하려면:

경우에 따라 로컬 네트워크 게이트웨이 접두사를 변경합니다. IP 주소 접두사를 수정하기 위해 수행하는 단계는 VPN Gateway 연결을 만들었는지에 따라 달라집니다. 이 문서의 [로컬 네트워크 게이트웨이에 대한 IP 주소 접두사 수정](#modify) 섹션을 참조하세요.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. 공용 IP 주소 요청

VPN Gateway에는 공용 IP 주소가 있어야 합니다. 먼저 IP 주소 리소스를 요청하고, 가상 네트워크 게이트웨이를 만들 때 참조합니다. VPN Gateway가 생성될 때 IP 주소는 리소스에 동적으로 할당됩니다. 

현재 VPN Gateway는 *동적* 공용 IP 주소 할당만 지원합니다. 고정 공용 IP 주소 할당을 요청할 수 없습니다. 하지만 IP 주소가 VPN 게이트웨이에 할당된 후 변경되는 것은 아닙니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

가상 네트워크 VPN Gateway에 할당할 공용 IP 주소를 요청합니다.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. 게이트웨이 IP 주소 지정 구성 만들기

게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷(‘GatewaySubnet’)을 정의합니다. 다음 예제를 사용하여 게이트웨이 구성을 만듭니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. VPN 게이트웨이 만들기

가상 네트워크 VPN Gateway 만들기.

다음 값을 사용합니다.

* 사이트 간 구성에 대한 *-GatewayType* 은 *Vpn* 입니다. 게이트웨이 유형은 항상 구현하는 구성에 따라 다릅니다. 예를 들어 다른 게이트웨이 구성인 GatewayType ExpressRoute가 필요할 수 있습니다.
* *-VpnType* 은 *경로 기반*(일부 설명서에서는 동적 게이트웨이라고도 함)이거나 *정책 기반*(일부 설명서에서는 고정 게이트웨이라고도 함)일 수 있습니다. VPN Gateway 형식에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.
* 사용할 게이트웨이 SKU를 선택합니다. 특정 SKU에 대한 구성 제한이 있습니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요. -GatewaySku와 관련하여 VPN 게이트웨이를 만들 때 오류가 발생하면 최신 버전의 PowerShell cmdlet을 설치했는지 확인합니다.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. VPN 디바이스 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 디바이스가 필요합니다. 이 단계에서는 VPN 디바이스를 구성합니다. VPN 디바이스를 구성할 때 다음 항목이 필요합니다.

- 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
- 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. PowerShell을 사용하여 가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 다음 예제를 사용합니다. 이 예제에서 VNet1GWPIP는 이전 단계에서 만든 공용 IP 주소 리소스의 이름입니다.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. VPN 연결 생성

다음으로 가상 네트워크 게이트웨이와 VPN 디바이스 사이에 사이트 간 VPN 연결을 만듭니다. 사용자 고유의 값으로 대체해야 합니다. 공유 키는 VPN 디바이스 구성에 사용한 값과 일치해야 합니다. 사이트 간 '-ConnectionType'은 **IPsec** 입니다.

1. 변수를 설정합니다.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. 연결을 만듭니다.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

잠시 후, 연결이 설정됩니다.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. VPN 연결 확인

VPN 연결을 확인하는 몇 가지 방법이 있습니다.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>가상 컴퓨터에 연결하려면

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>로컬 네트워크 게이트웨이에 대한 IP 주소 접두사를 수정하려면

온-프레미스 위치에 라우팅하려는 IP 주소 접두사를 변경하는 경우 로컬 네트워크 게이트웨이를 수정할 수 있습니다. 이러한 예제를 사용하는 경우 사용자 환경과 일치하도록 값을 수정합니다.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>로컬 네트워크 게이트웨이에 대한 IP 주소를 수정하려면

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>게이트웨이 연결 삭제

연결의 이름을 모르는 경우 ‘Get-AzVirtualNetworkGatewayConnection’ cmdlet을 사용하여 찾을 수 있습니다.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>다음 단계

*  연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](../index.yml)를 참조하세요.
* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
* Azure Resource Manager 템플릿을 사용하여 사이트 간 VPN 연결을 만드는 방법은 [사이트 간 VPN 연결 만들기](https://azure.microsoft.com/resources/templates/site-to-site-vpn-create/)를 참조하세요.
* Azure Resource Manager 템플릿을 사용하여 VNet 간 VPN 연결을 만드는 방법은 [HBase 지역 복제 배포](https://azure.microsoft.com/resources/templates/hdinsight-hbase-replication-geo/)를 참조하세요.
