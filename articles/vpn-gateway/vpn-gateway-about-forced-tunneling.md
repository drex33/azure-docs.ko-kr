---
title: '강제 터널링 구성 - 사이트 간 연결: 클래식'
titleSuffix: Azure VPN Gateway
description: 클래식 배포 모델을 사용하여 만든 가상 네트워크에 대해 강제 터널링을 구성하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 24c2643da85527a3ff4b2392d607540e2c2801fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566486"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 강제 터널링 구성

강제 터널링을 사용하면 검사 및 감사에 대한 사이트 간 VPN 터널을 통해 모든 인터넷 바인딩된 트래픽을 온-프레미스 위치에 다시 리디렉션하거나 "force"할 수 있습니다. 대부분의 엔터프라이즈 IT 정책에 있어서 중요한 보안 요구 사항입니다. 강제 터널링 없이 Azure의 VM에서 인터넷 바인딩된 트래픽은 항상 트래픽을 검사 또는 감사하도록 허용하는 옵션 없이 Azure 네트워크 인프라에서 직접 인터넷으로 트래버스합니다. 인증되지 않은 인터넷 액세스는 잠재적으로 정보 공개 또는 다른 유형의 보안 위반을 발생시킬 수 있습니다.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

이 문서에서는 클래식 배포 모델을 사용하여 만든 가상 네트워크에 대한 강제 터널링을 구성하는 과정을 안내합니다. 강제 터널링은 포털을 통해서가 아닌 PowerShell을 사용하여 구성할 수 있습니다. [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 대한 강제 터널링을 구성하려면 다음 드롭다운 목록에서 Resource Manager 문서를 선택합니다.

> [!div class="op_single_selector"]
> * [클래식](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

Azure에서 강제 터널링은 가상 네트워크 UDR(사용자 정의 경로)을 통해 구성됩니다. 온-프레미스 사이트에 트래픽을 리디렉션하는 것은 Azure VPN Gateway에 기본 경로로 표현됩니다. 다음 섹션에서는 Azure Virtual Network에 대한 라우팅 테이블 및 경로의 현재 제한 사항을 나열합니다.

* 각 가상 네트워크 서브넷에는 기본 제공 시스템 라우팅 테이블이 있습니다. 시스템 라우팅 테이블에는 다음 3개의 경로 그룹이 있습니다.

  * **로컬 VNet 경로:** 동일한 가상 네트워크에서 대상 VM으로 직접
  * **온-프레미스 경로:** Azure VPN Gateway로
  * **기본 경로:** 인터넷으로 직접. 이전의 두 경로를 벗어나는 개인 IP 주소로 향하는 패킷은 삭제됩니다.
* 사용자 정의 경로가 릴리스되면서 라우팅 테이블을 만들어 기본 경로에 추가한 다음 라우팅 테이블을 VNet 서브넷에 연결하여 해당 서브넷에 강제 터널링을 사용할 수 있습니다.
* 가상 네트워크에 연결된 크로스-프레미스 로컬 사이트 사이에서 "기본 사이트"를 설정해야 합니다.
* 강제 터널링은 동적 라우팅 VPN Gateway(정적 게이트웨이 아님)가 있는 VNet에 연결되어야 합니다.
* ExpressRoute 강제 터널링은 이 메커니즘을 통해 구성되지 않지만 대신 ExpressRoute BGP 피어링 세션을 통해 기본 경로를 보급하여 활성화됩니다. 자세한 내용은 [ExpressRoute란?](../expressroute/expressroute-introduction.md)을 참조하세요.

## <a name="configuration-overview"></a>구성 개요

다음 예에서 프런트 엔드 서브넷은 강제 터널링되지 않았습니다. 프런트 엔드 서브넷에서 작업은 계속해서 인터넷에서 직접 고객의 요청을 수락하고 응답할 수 있습니다. 중간 계층 및 백 엔드 서브넷은 강제 터널링됩니다. 이러한 두 서브넷에서 인터넷으로의 모든 아웃바운드 연결은 S2S VPN 터널 중 하나를 통해 온-프레미스 사이트로 다시 force되거나 리디렉션됩니다.

이를 통해 필요한 다중 계층 서비스 아키텍처를 계속 사용하면서 Azure의 가상 머신 또는 클라우드 서비스에서 인터넷 액세스를 제한하고 검사할 수 있습니다. 가상 네트워크에 인터넷 연결 작업이 없는 경우 강제 터널링을 전체 가상 네트워크에 적용할 수도 있습니다.

![강제 터널링](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>사전 요구 사항

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* 구성된 가상 네트워크입니다. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>강제 터널링 구성

다음 절차에 따라 가상 네트워크에 대한 강제 터널링을 지정할 수 있습니다. 구성 단계는 VNet 네트워크 구성 파일에 해당합니다.  이 예제에서 가상 네트워크 'MultiTier-VNet'에는 3개의 서브넷(Frontend', 'Midtier' 및 'Backend' 서브넷)과 함께 4개의 크로스 프레미스 연결(DefaultSiteHQ 및 3개의 분기)이 있습니다.


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

다음 단계에서는 강제 터널링에 대한 기본 사이트 연결로 ‘DefaultSiteHQ’를 설정하고 강제 터널링을 사용하도록 Midtier 및 Backend 서브넷을 구성합니다.

1. 상승된 권한으로 PowerShell 콘솔을 엽니다. 다음 예제를 사용하여 계정에 연결합니다.

   ```powershell
   Add-AzureAccount
   ```

1. 라우팅 테이블을 만듭니다. 경로 테이블을 만들려면 다음 cmdlet을 사용합니다.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. 라우팅 테이블에 기본 경로를 추가합니다. 

   다음 예제는 기본 경로를 1단계에서 생성된 라우팅 테이블에 추가합니다. 경로만 지원되는 경우는 "VPNGateway" 다음 홉에 대한 "0.0.0.0/0"의 대상 접두사입니다.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. 서브넷에 라우팅 테이블을 연결합니다. 

   라우팅 테이블을 만들고 경로를 추가한 후 다음 예제를 사용하여 경로 테이블을 VNet 서브넷에 추가하거나 연결합니다. 예제는 경로 테이블 "MyRouteTable"을 VNet MultiTier-VNet의 중간 계층 및 백 엔드 서브넷에 추가합니다.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. 강제 터널링에 대한 기본 사이트를 할당합니다. 

   이전 단계에서 샘플 cmdlet 스크립트는 라우팅 테이블을 만들고 경로 테이블을 두 개의 VNet 서브넷에 연결했습니다. 나머지 단계는 기본 사이트 또는 터널로 가상 네트워크의 다중 사이트 연결 중에서 로컬 사이트를 선택하는 것입니다.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>추가 PowerShell cmdlet

### <a name="to-delete-a-route-table"></a>경로 테이블을 삭제하려면

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>경로 테이블을 나열하려면

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>경로 테이블에서 경로를 삭제하려면

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>서브넷에서 경로를 제거하려면

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>서브넷에 연결된 경로 테이블을 나열하려면

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>VNet VPN Gateway에서 기본 사이트를 제거하려면

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
