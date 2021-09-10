---
title: 사이트 간 연결의 강제 터널링 구성
description: 모든 인터넷 바인딩 트래픽을 온-프레미스 위치로 다시 리디렉션(강제 적용)하는 방법을 알아봅니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: 383636d07aa453266be43b33d6f62b93255ac24a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566479"
---
# <a name="configure-forced-tunneling"></a>강제 터널링 구성

강제 터널링을 사용하면 검사 및 감사에 대한 사이트 간 VPN 터널을 통해 모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 "force"할 수 있습니다. 대부분의 엔터프라이즈 IT 정책에 있어서 중요한 보안 요구 사항입니다. 강제 터널링을 구성하지 않는 경우 Azure의 VM에서 인터넷 바인딩된 트래픽은 항상 트래픽을 검사 또는 감사하도록 허용하는 옵션 없이 Azure 네트워크 인프라에서 직접 인터넷으로 트래버스합니다. 인증되지 않은 인터넷 액세스는 잠재적으로 정보 공개 또는 다른 유형의 보안 위반을 발생시킬 수 있습니다.

강제 터널링은 Azure PowerShell를 사용하여 구성할 수 있습니다. Azure Portal를 사용하여 구성할 수 없습니다. 이 문서는 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)을 사용하여 만든 가상 네트워크에 대해 강제 터널링을 구성하는 데 도움이 됩니다. 클래식 배포 모델에 대해 강제 터널링을 구성하려면 [강제 터널링 - 클래식](vpn-gateway-about-forced-tunneling.md)을 참조하세요.

## <a name="about-forced-tunneling"></a>강제 터널링 정보

다음 다이어그램에서는 강제 터널링 작동 방법을 보여 줍니다.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="다이어그램에 강제 터널링이 표시됩니다.":::

이 예에서 프런트 엔드 서브넷은 강제 터널링되지 않았습니다. 프런트 엔드 서브넷에서 작업은 계속해서 인터넷에서 직접 고객의 요청을 수락하고 응답할 수 있습니다. 중간 계층 및 백 엔드 서브넷은 강제 터널링됩니다. 이러한 두 서브넷에서 인터넷으로의 모든 아웃바운드 연결은 S2S(사이트 간) VPN 터널 중 하나를 통해 온-프레미스 사이트로 다시 강제로 처리되거나 리디렉션됩니다.

이를 통해 필요한 다중 계층 서비스 아키텍처를 계속 사용하면서 Azure의 가상 머신 또는 클라우드 서비스에서 인터넷 액세스를 제한하고 검사할 수 있습니다. 가상 네트워크에 인터넷 연결 작업이 없는 경우 강제 터널링을 전체 가상 네트워크에 적용할 수도 있습니다.

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

Azure에서 강제 터널링은 가상 네트워크 사용자 지정 사용자 정의 경로을 통해 구성됩니다. 온-프레미스 사이트에 트래픽을 리디렉션하는 것은 Azure VPN Gateway에 기본 경로로 표현됩니다. 사용자 정의 경로 및 가상 네트워크에 대한 자세한 내용은 [사용자 지정 사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 참조하세요.

* 각 가상 네트워크 서브넷에는 기본 제공 시스템 라우팅 테이블이 있습니다. 시스템 라우팅 테이블에는 다음 3개의 경로 그룹이 있습니다.
  
  * **로컬 VNet 경로:** 동일한 가상 네트워크에서 대상 VM으로 직접
  * **온-프레미스 경로:** Azure VPN Gateway로
  * **기본 경로:** 인터넷으로 직접. 이전의 두 경로를 벗어나는 개인 IP 주소로 향하는 패킷은 삭제됩니다.
* 이 절차는 UDR(사용자 정의 경로)을 사용하여 라우팅 테이블을 만들어 기본 경로에 추가한 다음 라우팅 테이블을 VNet 서브넷에 연결하여 해당 서브넷에 강제 터널링을 사용할 수 있습니다.
* 강제 터널링은 경로 기반 VPN 게이트웨이가 있는 VNet에 연결되어야 합니다. 가상 네트워크에 연결된 크로스-프레미스 로컬 사이트 사이에서 "기본 사이트"를 설정해야 합니다. 또한 트래픽 선택기로 0.0.0.0/0을 사용하여 온-프레미스 VPN 디바이스를 구성해야 합니다. 
* ExpressRoute 강제 터널링은 이 메커니즘을 통해 구성되지 않지만 대신 ExpressRoute BGP 피어링 세션을 통해 기본 경로를 보급하여 활성화됩니다. 자세한 내용은 [ExpressRoute 설명서](https://azure.microsoft.com/documentation/services/expressroute/)를 참조하세요.
* 동일한 VNet에 VPN Gateway 및 ExpressRoute 게이트웨이를 모두 배포하는 경우 ExpressRoute 게이트웨이가 구성된 "기본 사이트"를 VNet에 보급하므로 UDR(사용자 정의 경로)이 더 이상 필요하지 않습니다.

## <a name="configuration-overview"></a>구성 개요

다음 절차를 사용하면 리소스 그룹과 VNet을 만들 수 있습니다. 그런 다음 VPN Gateway를 만들고 강제 터널링을 구성합니다. 이 절차에서 가상 네트워크 'MultiTier-VNet'에는 3개의 서브넷( 'Frontend', 'Midtier' 및 'Backend' 서브넷)과 함께 4개의 크로스 프레미스 연결('DefaultSiteHQ' 및 3개의 분기)이 있습니다.

절차 단계에서는 강제 터널링에 대한 기본 사이트 연결로 DefaultSiteHQ를 설정하고 강제 터널링을 사용하도록 'Midtier' 및 'Backend' 서브넷을 구성합니다.

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/) 을 참조하세요.

> [!IMPORTANT]
> PowerShell cmdlet의 최신 버전을 설치해야 합니다. 그렇지 않은 경우 cmdlet 중 일부를 실행할 때 유효성 검사 오류가 발생할 수 있습니다.
>
>

### <a name="to-sign-in"></a>로그인 방법

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>강제 터널링 구성

> [!NOTE]
> “이 cmdlet의 출력 개체 형식은 이후 릴리스에서 수정될 예정입니다”라는 경고가 표시될 수 있습니다. 이는 정상적인 동작이며 사용자는 이러한 경고를 안전하게 무시할 수 있습니다.
>
>


1. 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. 가상 네트워크를 만들고 서브넷을 지정합니다.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. 로컬 네트워크 게이트웨이를 만듭니다.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. 경로 테이블 및 경로 규칙을 만듭니다.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. 중간 계층 및 백 엔드 서브넷에 경로 테이블을 연결합니다.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다. GatewaySKU 값과 관련한 ValidateSet 오류가 표시되는 경우 [PowerShell cmdlet 최신 버전](#before)을 설치했는지 확인합니다. PowerShell cmdlet 최신 버전은 최신 게이트웨이 SKU에 대한 유효성이 검사된 새 값을 포함합니다.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. 기본 사이트를 가상 네트워크 게이트웨이에 할당합니다. **-GatewayDefaultSite** 는 이 설정을 올바르게 구성할 수 있도록 강제 라우팅 구성을 수행할 수 있도록 하는 cmdlet 매개 변수입니다. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. 사이트 간 VPN 연결을 설정합니다.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
