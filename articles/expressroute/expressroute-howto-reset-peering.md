---
title: 'Azure ExpressRoute: 회로 피어링 초기화'
description: Azure PowerShell을 사용하여 Azure ExpressRoute 회로에 피어링을 사용 설정하고 중단하는 방법을 알아봅니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559576"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute 회로 피어링 다시 설정

이 문서에서는 PowerShell을 사용하여 ExpressRoute 회로의 피어링을 사용 설정하고 중단하는 방법을 설명합니다. 피어링을 만들 때 기본적으로 사용됩니다. 피어링을 사용하지 않는 경우 ExpressRoute 회로의 기본 연결과 보조 연결 둘 다에서 BGP 세션이 종료됩니다. Microsoft에 대한 이 피어링에 대한 연결이 끊어지게 됩니다. 피어링을 사용하는 경우 ExpressRoute 회로의 기본 연결과 보조 연결 둘 다에서 BGP 세션이 구축됩니다. 이 피어링에 대해 Microsoft에 대한 연결이 복원됩니다. ExpressRoute 회로에서 Microsoft 피어링 및 Azure 개인 피어링에 대한 피어링을 별도로 사용 및 사용하지 않을 수 있습니다.

ExpressRoute 피어링을 다시 설정하는 것이 도움이 되는 두 가지 시나리오가 있습니다.
* 재해 복구 디자인 및 구현을 테스트하려는 경우입니다. 예를 들어 두 개의 ExpressRoute 회로가 있습니다. 한 회로의 피어링은 사용하지 않고 네트워크 트래픽을 다른 회로로 강제로 장애 조치(failover)할 수 있습니다.
* ExpressRoute 회로의 Azure 개인 피어링 또는 Microsoft 피어링에서 BFD(양방향 전달 검색)를 사용합니다. 2018년 8월 1일 이후 ExpressRoute 회로를 만들고 2020년 1월 10일 이후 Microsoft 피어링을 만든 경우 Azure 개인 피어링에서 기본적으로 BFD를 사용합니다. 나열된 날짜 이전에 회로를 만든 경우 BFD를 사용하도록 피어링을 다시 설정해야 합니다. 

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>피어링 다시 설정

1. PowerShell을 로컬로 실행하는 경우 상승된 권한으로 PowerShell 콘솔을 열고, 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```azurepowershell
   Connect-AzAccount
   ```
2. Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. ExpressRoute 회로를 검색하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 사용하거나 사용하지 않도록 설정하려는 피어링을 식별합니다. *피어링* 은 배열입니다. 다음 예제에서 피어링[0]은 Azure 프라이빗 피어링이고 피어링[1]은 Microsoft 피어 링입니다.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 피어링의 상태를 변경하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   피어링은 설정한 상태를 유지합니다. 

## <a name="next-steps"></a>다음 단계
ExpressRoute 문제 해결에 도움이 필요한 경우 다음 문서를 참조하세요.
* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
