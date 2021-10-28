---
title: '자습서: ExpressRoute 회로에 대한 피어링 구성 - Azure PowerShell'
description: 이 자습서에서는 PowerShell을 사용하여 Resource Manager 배포 모델에서 ExpressRoute 회로에 대한 라우팅 구성을 만들고 관리하는 방법을 보여 줍니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79d49a7a9acd30a9af03921c774e6b2430dc848b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228947"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>자습서: PowerShell을 사용하여 ExpressRoute 회로의 피어링 만들기 및 수정

이 자습서는 PowerShell을 사용하여 Resource Manager 배포 모델에서 ExpressRoute 회로에 대한 라우팅 구성을 만들고 관리하는 데 도움이 됩니다. ExpressRoute 회로에 대한 피어링의 상태를 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행할 수도 있습니다. 회로를 사용하는 다른 메서드를 사용하려는 경우 다음 목록에서 문서를 선택합니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [공용 피어링](about-public-peering.md)
> * [비디오 - 프라이빗 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-routing-classic.md)
> 

이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리형 계층 3 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.

> [!IMPORTANT]
> 현재는 서비스 관리 포털을 통해 서비스 공급자가 구성한 피어링을 보급하지 않습니다. 이 기능을 곧 사용할 수 있도록 개발 중입니다. BGP 피어링을 구성하기 전에 서비스 공급자에게 확인하세요.
> 

ExpressRoute 회로에 대해 개인 피어링 및 Microsoft 피어링을 구성할 수 있습니다(Azure 공용 피어링은 새 회로에서 사용되지 않음). 피어링은 선택하는 순서에 관계없이 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [ExpressRoute 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요. 공용 피어링에 대한 자세한 내용은 [ExpressRoute 공용 피어링](about-public-peering.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 회로에 대한 Microsoft 피어링 구성, 업데이트 및 삭제
> - 회로에 대한 Azure 개인 피어링 구성, 업데이트 및 삭제

## <a name="prerequisites"></a>필수 구성 요소

* 구성을 시작하기 전에 다음 페이지를 검토했는지 확인합니다.
    * [필수 구성 요소](expressroute-prerequisites.md) 
    * [라우팅 요구 사항](expressroute-routing.md)
    * [워크플로](expressroute-workflows.md)
* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침에 따라 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-arm.md), 계속하기 전에 연결 공급자가 회로를 사용하도록 설정합니다. 이 문서에서 cmdlet을 실행하려면 ExpressRoute 회로가 프로비저닝되고 사용하도록 설정된 상태여야 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft 피어링

이 섹션은 ExpressRoute 회로에 Microsoft 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다. 자세한 내용은 [Microsoft 피어링에 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.
> 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면

1. 로그인하고 구독을 선택합니다.

   PowerShell을 로컬로 설치한 경우 로그인합니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

   ```azurepowershell
   Connect-AzAccount
   ```

   ExpressRoute 회로를 만들려는 구독을 선택합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. ExpressRoute 회로를 만듭니다.

   지침에 따라 [ExpressRoute 회로](expressroute-howto-circuit-arm.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Microsoft 피어링을 사용하도록 할 수 있습니다. 다음 섹션에 나열된 지침을 따를 필요는 없습니다. 그러나 연결 공급자가 라우팅을 관리하지 않는 경우 회로를 만든 후에 다음 단계를 사용하여 구성을 계속합니다. 

3. 또한 ExpressRoute 회로가 프로비저닝되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   응답은 다음 예제와 유사합니다.

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. 회로에 Microsoft 피어링을 구성합니다. 계속하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 또는 /126 서브넷입니다. 주소 블록은 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 또는 IPv6 접두사여야 합니다.
   * 보조 링크에 대한 /30 또는 /126 서브넷입니다. 주소 블록은 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 또는 IPv6 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사의 목록을 제공합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다. IPv4 BGP 세션에는 IPv4 보급 접두사가 필요하고 IPv6 BGP 세션에는 IPv6 보급 접두사가 필요합니다. 
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.
   * 선택 사항:
     * 고객 ASN: 피어링 AS 숫자에 등록되지 않은 접두사를 보급하는 경우 등록된 AS 번호를 지정할 수 있습니다.
     * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다.

> [!IMPORTANT]
> Microsoft는 인터넷 라우팅 레지스트리에서 지정된 '보급된 공용 접두사' 및 '피어 ASN'(또는 '고객 ASN')이 사용자에게 할당되었는지 확인합니다. 다른 엔터티에서 공용 접두사를 가져오는 경우, 라우팅 레지스트리에 할당이 기록되지 않은 경우 자동 유효성 검사가 완료되지 않으며 수동 유효성 검사가 필요합니다. 자동 유효성 검사가 실패하면 "Get-AzExpressRouteCircuitPeeringConfig" 출력에서 'AdvertisedPublicPrefixesState'가 '유효성 검사 필요'로 표시됩니다(다음 섹션의 "Microsoft 피어링 정보를 가져오려면" 참조). 
> 
> '유효성 검사 필요' 메시지가 표시되면 라우팅 레지스트리에서 접두사의 소유자로 나열된 엔터티에서 조직에 할당한 공용 접두사를 보여 주는 문서를 수집하고, 지원 티켓을 열어 수동 유효성 검사를 위해 이러한 문서를 제출합니다. 
> 

   다음 예제를 실행하여 회로에 Microsoft 피어링을 구성합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft 피어링 정보를 가져오려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure 프라이빗 피어링

이 섹션은 ExpressRoute 회로에 Azure 프라이빗 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

### <a name="to-create-azure-private-peering"></a>Azure 프라이빗 피어링을 만들려면

1. ExpressRoute에 대한 PowerShell 모듈을 가져옵니다.

   [PowerShell 갤러리](https://www.powershellgallery.com/)에서 최신 PowerShell 설치 관리자를 설치합니다. 그런 다음, ExpressRoute cmdlet 사용을 시작하기 위해 Azure Resource Manager 모듈을 PowerShell 세션으로 가져옵니다. 관리자 권한으로 PowerShell을 실행해야 합니다.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   알려진 의미 체계 버전 범위 내의 모든 Az.\* 모듈을 가져옵니다.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   또한 알려진 의미 체계 버전 범위의 선택 모듈만 가져올 수 있습니다.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   계정에 로그인합니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   ExpressRoute 회로를 만들려는 구독을 선택합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. ExpressRoute 회로를 만듭니다.

   지침에 따라 [ExpressRoute 회로](expressroute-howto-circuit-arm.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 다음 섹션에 나열된 지침을 따를 필요는 없습니다. 그러나 연결 공급자가 라우팅을 관리하지 않는 경우 회로를 만든 후에 다음 단계를 사용하여 구성을 계속합니다.

3. 또한 ExpressRoute 회로가 프로비저닝되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   응답은 다음 예제와 유사합니다.

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. 회로에 Azure 프라이빗 피어링을 구성합니다. 다음 단계를 계속하기 전에 다음 항목이 있는지 확인합니다.

   * 가상 네트워크에 예약된 주소 공간의 일부가 아닌 서브넷 쌍입니다. 한 서브넷은 기본 링크에 사용되고 다른 서브넷은 보조 링크에 사용됩니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다. 이 서브넷 쌍에 대한 세 가지 옵션이 있습니다.
       * IPv4: 2개의 /30 서브넷
       * IPv6: 2개의 /126 서브넷
       * 둘 다: 두 개의 /30 서브넷과 두 개의 /126 서브넷
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에 프라이빗 AS 숫자를 사용할 수 있습니다. 65515를 사용하지 않는지 확인합니다.
   * 선택 사항:
     * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 사용하여 회로에 Azure 프라이빗 피어링을 구성합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Azure 프라이빗 피어링 세부 정보를 가져오려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure 프라이빗 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 500개로 업데이트됩니다.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>리소스 정리

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft 피어링을 삭제하려면

다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure 프라이빗 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

> [!WARNING]
> 이 예제를 실행하기 전에 모든 가상 네트워크 및 ExpressRoute Global Reach 연결을 제거해야 합니다. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>다음 단계

Azure 개인 피어링이 구성되면 ExpressRoute 게이트웨이를 만들어 가상 네트워크를 회로에 연결할 수 있습니다. 

> [!div class="nextstepaction"]
> [ExpressRoute에 대한 가상 네트워크 게이트웨이 구성](expressroute-howto-add-gateway-resource-manager.md)
