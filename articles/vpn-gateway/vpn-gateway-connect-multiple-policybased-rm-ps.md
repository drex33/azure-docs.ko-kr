---
title: 여러 온-프레미스 정책 기반 VPN 디바이스에 VPN 게이트웨이 연결
titleSuffix: Azure VPN Gateway
description: PowerShell을 사용하여 여러 정책 기반 VPN 디바이스에 대해 Azure 경로 기반 VPN 게이트웨이를 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2b9076e419392d472f93bbd14eb89c7f4c9dbe17
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132866616"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>PowerShell을 사용하여 여러 온-프레미스 정책 기반 VPN 디바이스에 Azure VPN Gateway 연결

이 문서에서는 S2S VPN 연결에서 사용자 지정 IPsec/IKE 정책을 활용하여 여러 온-프레미스 정책 기반 VPN 디바이스에 연결하도록 Azure 경로 기반 VPN Gateway를 구성하는 방법을 설명합니다.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>정책 기반 및 경로 기반 VPN 게이트웨이 정보

정책 기반 *대* 경로 기반 VPN 디바이스는 연결에서 IPsec 트래픽 선택기가 설정되는 방식이 서로 다릅니다.

* **정책 기반** VPN 디바이스는 두 네트워크의 접두사 조합을 사용하여 트래픽이 IPsec 터널을 통해 암호화/암호 해독되는 방법을 정의합니다. 이는 일반적으로 패킷 필터링을 수행하는 방화벽 디바이스에 구축됩니다. IPsec 터널 암호화 및 암호 해독이 패킷 필터링 및 처리 엔진에 추가됩니다.
* **경로 기반** VPN 디바이스는 임의(와일드 카드) 트래픽 선택기를 사용하며, 라우팅/전달 테이블이 서로 다른 IPsec 터널로 트래픽을 전달하도록 합니다. 이는 일반적으로 각 IPsec 터널이 네트워크 인터페이스 또는 VTI(가상 터널 인터페이스)로 모델링되는 라우터 플랫폼에 구축됩니다.

다음 다이어그램은 두 모델을 강조 표시합니다.

### <a name="policy-based-vpn-example"></a>정책 기반 VPN 예제
![정책 기반](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>경로 기반 VPN 예제
![경로 기반](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>정책 기반 VPN에 대한 Azure 지원
현재 Azure에서는 VPN Gateway의 두 가지 모드(경로 기반 VPN Gateway 및 정책 기반 VPN Gateway)를 모두 지원합니다. 이러한 게이트웨이는 서로 다른 플랫폼에 구축되므로 사양이 서로 다릅니다.

| 범주 | PolicyBased VPN Gateway | RouteBased VPN Gateway | RouteBased VPN Gateway |  RouteBased VPN Gateway
| -------- | ----------------------- | ---------------------- | ---------------------- | ----------------------- |
| **Azure Gateway SKU**    | 기본                       | 기본                            | VpnGw1, VpnGw2, VpnGw3  | VpnGw4 및 VpnGw5 |
| **IKE 버전**          | IKEv1                       | IKEv2                            | IKEv1, IKEv2         | IKEv1, IKEv2   |
| **최대 S2S 연결** | **1**                       | 10                               | 30                      | 100               |
|                          |                             |                                  |                         |                   |

이제 사용자 지정 IPsec/IKE 정책을 사용하여 "**PolicyBasedTrafficSelectors**" 옵션과 함께 접두사 기반 트래픽 선택기를 사용하여 온-프레미스 정책 기반 VPN 디바이스에 연결하도록 Azure 경로 기반 VPN 게이트웨이를 구성할 수 있습니다. 이 기능을 사용하면 Azure Virtual Network 및 VPN Gateway에서 여러 온-프레미스 정책 기반 VPN/방화벽 디바이스에 연결할 수 있으므로 현재 Azure 정책 기반 VPN Gateway에서 단일 연결 제한이 제거됩니다.

> [!IMPORTANT]
> 1. 이 연결을 사용하려면 온-프레미스 정책 기반 VPN 디바이스가 Azure 경로 기반 VPN Gateway에 연결하도록 **IKEv2** 를 지원해야 합니다. VPN 디바이스 사양을 확인하세요.
> 2. 이 메커니즘을 사용하여 정책 기반 VPN 디바이스를 통해 연결되는 온-프레미스 네트워크는 Azure Virtual Network에 연결할 수 있습니다. **동일한 Azure VPN Gateway를 통해 다른 온-프레미스 네트워크 또는 가상 네트워크로 전송할 수는 없습니다**.
> 3. 구성 옵션은 사용자 지정 IPsec/IKE 연결 정책의 일부입니다. 정책 기반 트래픽 선택기 옵션을 사용하는 경우 전체 정책(IPsec/IKE 암호화 및 무결성 알고리즘, 키 수준 및 SA 수명)을 지정해야 합니다.

아래 다이어그램에서는 Azure VPN Gateway를 통한 전송 라우팅이 정책 기반 옵션에서 작동하지 않는 이유를 보여 줍니다.

![정책 기반 전송](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

다이어그램에 나와 있는 것처럼 Azure VPN Gateway의 트래픽 선택기는 가상 네트워크에서 각각의 온-프레미스 네트워크 접두사로는 연결되지만 교차 연결 접두사로는 연결되지 않습니다. 예를 들어 온-프레미스 사이트 2, 3 및 4는 각각 VNet1과 통신할 수 있지만 Azure VPN Gateway를 통해 서로 연결할 수는 없습니다. 다이어그램에서는 이 구성을 사용할 경우 Azure VPN Gateway에서 사용할 수 없는 상호 연결 트래픽 선택기를 보여줍니다.

## <a name="workflow"></a><a name="workflow"></a>워크플로

이 문서의 지침은 [S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)에 설명된 동일한 예제에 따라 S2S VPN 연결을 설정합니다. 다음 다이어그램에 이러한 연결이 나와 있습니다.

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

이 연결을 사용하도록 설정하는 워크플로는 다음과 같습니다.
1. 프레미스 간 연결에 대한 가상 네트워크, VPN 게이트웨이 및 로컬 네트워크 게이트웨이를 만듭니다.
2. IPsec/IKE 정책을 만듭니다.
3. S2S 또는 VNet 간 연결을 만들 때 정책을 적용하고 연결에서 **정책 기반 트래픽 선택기를 사용하도록 설정** 합니다.
4. 연결이 이미 생성된 경우 기존 연결에 정책을 적용하거나 업데이트할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>정책 기반 트래픽 선택기 사용

이 섹션에서는 연결에서 정책 기반 트래픽 선택기를 사용하도록 설정하는 방법을 보여 줍니다. [IPsec/IKE 정책 구성 문서 구성의 3부](vpn-gateway-ipsecikepolicy-rm-powershell.md)를 완료했는지 확인하세요. 이 문서의 단계에서는 동일한 매개 변수가 사용됩니다.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>구독에 연결하고 변수 선언

1. 컴퓨터에서 로컬로 PowerShell을 실행하는 경우 *Connect-AzAccount* cmdlet을 사용하여 로그인합니다. 또는 브라우저에서 Azure Cloud Shell을 대신 사용합니다.

2. 변수 선언. 이 연습에서는 다음 변수가 사용됩니다.

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

1. 리소스 그룹을 만듭니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. 다음 예제를 사용하여 세 개의 서브넷과 VPN 게이트웨이가 있는 가상 네트워크 TestVNet1을 만듭니다. 값을 대체하려면, 게이트웨이 서브넷의 이름을 항상 'GatewaySubnet'으로 지정하는 것이 중요합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>2단계 - IPsec/IKE 정책을 사용하여 S2S VPN 연결 만들기

1. IPsec/IKE 정책을 만듭니다.

   > [!IMPORTANT]
   > 연결에서 "UsePolicyBasedTrafficSelectors" 옵션을 사용하기 위해 IPsec/IKE 정책을 만들어야 합니다.

   아래 예제에서는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS 없음, SA 수명 14400초 및 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. 정책 기반 트래픽 선택기와 IPsec/IKE 정책을 사용하여 S2S VPN 연결을 만들고 이전 단계에서 만든 IPsec/IKE 정책을 적용합니다. 연결에서 정책 기반 트래픽 선택기를 사용도록 설정하는 추가 매개 변수 "-UsePolicyBasedTrafficSelectors $True"가 필요합니다.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. 단계를 완료하면 S2S VPN 연결에서 정의된 IPsec/IKE 정책을 사용하며 연결에서 정책 기반 트래픽 선택기를 활성화합니다. 동일한 단계를 반복하여 동일한 Azure VPN 게이트웨이에서 추가 온-프레미스 정책 기반 VPN 디바이스에 연결을 더 추가할 수 있습니다.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>정책 기반 트래픽 선택기 업데이트
이 섹션에서는 기존 S2S VPN 연결에 대한 정책 기반 트래픽 선택기 옵션을 업데이트하는 방법을 보여 줍니다.

1. 연결 리소스를 가져옵니다.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. 정책 기반 트래픽 선택기 옵션을 확인합니다.
다음 줄은 정책 기반 트래픽 선택기가 연결에 사용되는지 여부를 표시합니다.

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   이 줄에서 "**True**"가 반환되면 연결에 정책 기반 트래픽 선택기가 구성되어 있는 것이고, 그렇지 않으면 "**False**"가 반환됩니다.
1. 연결 리소스를 가져온 후에는 연결에서 정책 기반 트래픽 선택기를 사용하거나 사용하지 않도록 설정할 수 있습니다.

   - 사용

      아래 예제에서는 IPsec/IKE 정책을 변경되지 않은 상태로 두고 정책 기반 트래픽 선택기 옵션을 사용하도록 설정합니다.

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - 사용 안 함

      아래 예제에서는 IPsec/IKE 정책을 변경되지 않은 상태로 두고 정책 기반 트래픽 선택기 옵션을 사용하지 않도록 설정합니다.

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>다음 단계
연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.

사용자 지정 IPsec/IKE 정책에 대한 자세한 내용은 [S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.
