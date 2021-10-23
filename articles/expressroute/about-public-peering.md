---
title: Azure ExpressRoute 퍼블릭 피어링 만들기 및 관리
description: Azure 퍼블릭 피어링에 대한 자세한 정보 및 관리
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5c2da05dd68951cada82fc888d54a0f423ce18c7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242287"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute 퍼블릭 피어링 만들기 및 관리

> [!div class="op_single_selector"]
> * [문서 - 퍼블릭 피어링](about-public-peering.md)
> * [비디오 - 공용 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [문서 - Microsoft 피어링](expressroute-circuit-peerings.md#microsoftpeering)
>

이 문서는 ExpressRoute 회로에 대한 퍼블릭 피어링 라우팅 구성을 만들고 관리하는 데 도움이 됩니다. 피어링의 상태를 확인하고, 피어링을 업데이트 또는 삭제하고, 피어링 프로비전을 해제할 수도 있습니다. 이 문서는 퍼블릭 피어링이 중단되기 전에 만든 Resource Manager 회로에 적용됩니다. 퍼블릭 피어링이 중단되기 전에 만든 기존 회로가 있는 경우 [Azure PowerShell](#powershell), [Azure CLI](#cli), [Azure Portal](#portal)을 사용하여 퍼블릭 피어링을 관리/구성할 수 있습니다.

>[!NOTE]
>퍼블릭 피어링은 더 이상 사용되지 않습니다. 새 ExpressRoute 회로에서 퍼블릭 피어링을 만들 수는 없습니다. 새 ExpressRoute 회로를 사용하는 경우 Azure 서비스에 [Microsoft 피어링](expressroute-circuit-peerings.md#microsoftpeering)을 대신 사용합니다.
>

## <a name="connectivity"></a>연결

연결은 항상 사용자의 WAN에서 Microsoft Azure 서비스로 시작됩니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다. Azure 퍼블릭 피어링에 ExpressRoute 회로를 사용할 수 있는 경우 회로를 통해 [Azure에서 사용되는 공용 IP 범위](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses)에 액세스할 수 있습니다.

퍼블릭 피어링을 사용하도록 설정한 후에는 대부분의 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다.

* Azure Storage, SQL Database, Websites 등의 서비스가 공용 IP 주소에 제공됩니다.
* 퍼블릭 피어링 라우팅 도메인을 통해 클라우드 서비스의 VIP를 비롯한 공용 IP 주소에서 호스트된 서비스에 프라이빗 모드로 연결할 수 있습니다.
* 인터넷을 통해 연결하지 않고도 공용 피어링 도메인을 DMZ에 연결하고 WAN에서 해당 공용 IP 주소의 모든 Azure 서비스에 연결할 수 있습니다.

## <a name="services"></a><a name="services"></a>Services

이 섹션에서는 퍼블릭 피어링을 통해 사용할 수 있는 서비스를 보여 줍니다. 퍼블릭 피어링은 더 이상 사용되지 않으므로 퍼블릭 피어링에 새 서비스나 더 많은 서비스를 추가할 계획은 없습니다. 퍼블릭 피어링을 사용하는데 사용하려는 서비스가 Microsoft 피어링을 통해서만 지원되는 경우 Microsoft 피어링으로 전환해야 합니다. 지원되는 서비스 목록은 [Microsoft 피어링](expressroute-faqs.md#microsoft-peering)을 참조하세요.

**지원됨:**

* Power BI
* 대부분의 Azure 서비스가 지원됩니다. 사용하려는 서비스에 직접 문의하여 지원을 확인합니다.

**지원되지 않음:**
  * CDN
  * Azure Front Door
  * Multi-factor Authentication 서버(레거시)
  * Traffic Manager

특정 서비스에 대한 가용성의 유효성을 검사하려면 해당 서비스에 대한 설명서를 보고 해당 서비스에 예약된 범위가 게시되었는지 확인합니다. 그런 다음, 대상 서비스의 IP 범위를 조회하고 [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드 XML 파일](https://www.microsoft.com/download/details.aspx?id=56519)에 나열된 범위와 비교합니다. 또는 확인을 위해 해당 서비스의 지원 티켓을 열 수 있습니다.

## <a name="peering-comparison"></a><a name="compare"></a>피어링 비교

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure 퍼블릭 피어링에서는 각 BGP 세션에 NAT IP 주소 1개가 연결됩니다. NAT IP 주소가 3개 이상인 경우 Microsoft 피어링으로 이동합니다. Microsoft 피어링을 사용하면 사용자 고유의 NAT 할당을 구성하고 선택적 접두사 보급 알림에 경로 필터를 사용할 수 있습니다. 자세한 내용은 [Microsoft 피어링으로 이동](./how-to-move-peering.md)을 참조하세요.
>

## <a name="custom-route-filters"></a>사용자 지정 경로 필터

필요한 경로만 이용하도록 네트워크 내에 사용자 지정 경로 필터를 정의할 수 있습니다. 라우팅 구성에 대한 자세한 내용은 [라우팅](expressroute-routing.md) 페이지를 참조하세요.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell 단계


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

퍼블릭 피어링은 더 이상 사용되지 않으므로 새 ExpressRoute 회로에서 퍼블릭 피어링을 구성할 수는 없습니다.

1. 프로비저닝되고 사용하도록 설정된 ExpressRoute 회로가 있는지 확인합니다. 다음 예제를 사용합니다.

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
2. 회로에 Azure 공용 피어링을 구성합니다. 더 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 선택 사항:
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 실행하여 회로에 Azure 공용 피어링을 구성합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Azure 공용 피어링 세부 정보를 가져오려면

다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure 공용 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 600개로 업데이트됩니다.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure 공용 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI 단계


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. 먼저 ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route list
   ```

   응답은 다음 예제와 유사합니다.

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. 회로에 Azure 공용 피어링을 구성합니다. 더 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 실행하여 회로에 Azure 공용 피어링을 구성합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure 공용 피어링 세부 정보를 보려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

다음 예제와 유사하게 출력됩니다.

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure 공용 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 600개로 업데이트됩니다.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure 공용 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal 단계

피어링을 구성하려면 이 문서에 포함된 PowerShell 또는 CLI 단계를 사용합니다. 피어링을 관리하려면 아래 섹션을 사용할 수 있습니다. 참조를 위해 이 단계는 [포털에서 Microsoft 피어링](expressroute-howto-routing-portal-resource-manager.md#msft)을 관리하는 경우와 비슷합니다.

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure 공용 피어링 세부 정보를 보려면

포털에서 피어링을 선택하여 Azure 퍼블릭 피어링의 속성을 확인합니다.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure 공용 피어링 구성을 업데이트하려면

피어링할 행을 선택하고 피어링 속성을 수정합니다.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure 공용 피어링을 삭제하려면

삭제 아이콘을 선택하여 피어링 구성을 제거합니다.

## <a name="next-steps"></a>다음 단계

다음 단계로, [ExpressRoute 회로에 가상 네트워크를 연결](expressroute-howto-linkvnet-arm.md)합니다.

* ExpressRoute 워크플로에 대한 자세한 내용은 [ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.