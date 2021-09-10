---
title: 가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성
description: 연결 목적으로 두 개의 Azure 가상 네트워크를 하나로 원활하게 연결하기 위해 가상 네트워크 피어링에 대한 게이트웨이 전송을 구성하는 방법을 알아봅니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4eef7e7470e9dbdd37613165f0ea5cb3b295762
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535784"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성

이 문서는 가상 네트워크 피어링을 위한 게이트웨이 전송을 구성하는 데 유용합니다. [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)은 두 Azure 가상 네트워크를 원활하게 연결하여 연결 목적을 위해 두 개의 가상 네트워크를 하나로 병합합니다. [게이트웨이 전송](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)은 크로스-프레미스 또는 VNet 간 연결에 대한 피어링된 가상 네트워크에서 VPN Gateway를 사용하도록 하나의 가상 네트워크를 사용하도록 하는 피어링 속성입니다. 다음 다이어그램은 가상 네트워크 피어링을 통한 게이트웨이 전송의 작동 방식을 보여 줍니다.

![게이트웨이 전송 다이어그램](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

다이어그램에서 피어링된 가상 네트워크는 게이트웨이 전송을 통해 Hub-RM에서 Azure VPN 게이트웨이를 사용할 수 있습니다. S2S, P2S 및 VNet 대 VNet 연결을 포함한 VPN 게이트웨이에서 가능한 연결은 세 개의 가상 네트워크 모두에 적용됩니다. 전송 옵션은 같거나 다른 배포 모델 간의 피어링에 사용할 수 있습니다. 서로 다른 배포 모델 간에 전송을 구성하는 경우 허브 가상 네트워크와 가상 네트워크 게이트웨이는 클래식 배포 모델이 아닌 [Resource Manager 배포 모델](../azure-resource-manager/management/deployment-models.md)에 있어야 합니다.
>

허브 및 스포크 네트워크 아키텍처에서 스포크 가상 네트워크는 게이트웨이 전송을 통해 모든 스포크 가상 네트워크에서 VPN 게이트웨이를 배포하는 대신 허브에서 VPN 게이트웨이를 공유할 수 있습니다. 게이트웨이에 연결된 가상 네트워크 또는 온-프레미스 네트워크에 대한 경로는 게이트웨이 전송을 사용하여 피어링된 가상 네트워크에 대한 라우팅 테이블에 전파됩니다. VPN 게이트웨이에서 자동 경로 전파를 비활성화할 수 있습니다. “**BGP 경로 전파를 사용하지 않도록 설정**” 옵션을 선택한 라우팅 테이블을 만들고, 경로 테이블을 서브넷에 연결하여 해당 서브넷에 대한 경로 배포를 방지합니다. 자세한 내용은 [Virtual Network 라우팅 테이블](../virtual-network/manage-route-table.md)을 참조하세요.

이 문서에는 두 가지 시나리오가 있습니다.

* **동일한 배포 모델**: 두 가상 네트워크 모두 Resource Manager 배포 모델에서 생성됩니다.
* **다양한 배포 모델**: 스포크 가상 네트워크는 클래식 배포 모델에서 생성되고 허브 가상 네트워크 및 게이트웨이는 Resource Manager 배포 모델에 있습니다.

>[!NOTE]
> 네트워크의 토폴로지를 변경하고 Windows VPN 클라이언트를 사용하는 경우, 변경 내용을 클라이언트에 적용하기 위해 Windows 클라이언트용 VPN 클라이언트 패키지를 다운로드하여 다시 설치해야 합니다.
>

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 가상 네트워크 및 사용 권한이 있는지 확인합니다.

### <a name="virtual-networks"></a><a name="vnet"></a>가상 네트워크

|VNet|배포 모델| 가상 네트워크 게이트웨이|
|---|---|---|---|
| 허브-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [예](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| 예 |
| 스포크-클래식 | [클래식](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | 예 |

### <a name="permissions"></a><a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 사용하는 계정에는 필요한 역할 또는 권한이 있어야 합니다. 아래 예에서 **허브-RM** 및 **스포크-클래식** 이라는 두 개의 가상 네트워크를 피어링하는 경우 계정에 각 가상 네트워크에 대해 다음 역할 또는 권한이 있어야 합니다.

|VNet|배포 모델|역할|사용 권한|
|---|---|---|---|
|허브-RM|리소스 관리자|[네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |클래식|[클래식 네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|해당 없음|
|스포크-클래식|리소스 관리자|[네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||클래식|[클래식 네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당(Resource Manager만 해당)에 대해 자세히 알아보세요.

## <a name="same-deployment-model"></a><a name="same"></a>동일한 배포 모델.

이 시나리오에서 가상 네트워크는 모두 Resource Manager 배포 모델에 있습니다. 다음 지침에 따라 가상 네트워크 피어링을 만들고 게이트웨이 전송을 활성화하도록 업데이트합니다.

### <a name="to-add-a-peering-and-enable-transit"></a>피어링을 추가하고 전송을 사용하도록 설정하려면

1. [Azure Portal](https://portal.azure.com)에서 허브-RM에서 가상 네트워크 피어링을 만들거나 업데이트합니다. **허브-RM** 가상 네트워크로 이동합니다. **피어링** 을 선택한 다음 **+ 추가** 를 선택하여 **피어링 추가** 를 엽니다.
1. **피어링 추가** 페이지에서 **이 가상 네트워크** 의 값을 구성합니다.

   * 피어링 링크 이름: 링크의 이름을 지정합니다. 예: **HubRMToSpokeRM**
   * 원격 가상 네트워크로의 트래픽: **허용**
   * 원격 가상 네트워크에서 전달된 트래픽 **허용**
   * 가상 네트워크 게이트웨이: **이 가상 네트워크의 게이트웨이 사용**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="피어링 추가를 보여 주는 스크린샷.":::

1. 동일한 페이지에서 **원격 가상 네트워크** 에 대한 값을 계속 구성합니다.

   * 피어링 링크 이름: 링크의 이름을 지정합니다. 예: **SpokeRMtoHubRM**
   * 배포 모델: **Resource Manager**
   * 가상 네트워크: **스포크-RM**
   * 원격 가상 네트워크로의 트래픽: **허용**
   * 원격 가상 네트워크에서 전달된 트래픽 **허용**
   * 가상 네트워크 게이트웨이: **원격 가상 네트워크의 게이트웨이 사용**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="원격 가상 네트워크의 값을 보여 주는 스크린샷.":::

1. **추가** 를 선택하여 피어링을 만듭니다.
1. 두 가상 네트워크 모두에서 피어링 상태가 **연결됨** 인지 확인합니다.

### <a name="to-modify-an-existing-peering-for-transit"></a>전송에 대한 기존 피어링을 수정하려면

피어링이 이미 생성된 경우 전송을 위해 피어링을 수정할 수 있습니다.

1. 가상 네트워크로 이동합니다. **피어링** 을 선택하고 수정할 피어링을 선택합니다.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="피어링 선택을 보여 주는 스크린샷.":::

1. VNet 피어링 업데이트

   * 원격 가상 네트워크로의 트래픽: **허용**
   * 가상 네트워크로 전달된 트래픽 **허용**
   * 가상 네트워크 게이트웨이:  **원격 가상 네트워크의 게이트웨이 사용**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="피어링 게이트웨이 수정을 보여 주는 스크린샷.":::

1. 피어링 설정을 **저장** 합니다.

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell 샘플

또한 위의 예제에서 PowerShell을 사용하여 피어링을 만들거나 업데이트할 수 있습니다. 변수를 사용자의 가상 네트워크 및 리소스 그룹의 이름으로 바꿉니다.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>다양한 배포 모델

이 구성에서 스포크 VNet **스포크-클래식** 은 클래식 배포 모델에 있고 허브 VNet **허브-RM** 은 Resource Manager 배포 모델에 있습니다. 배포 모델 간 전송을 구성할 때 가상 네트워크 게이트웨이는 클래식 VNet이 아닌 Resource Manager VNet에 대해 구성되어야 합니다.

이 구성의 경우 **허브-RM** 가상 네트워크를 구성하기만 하면 됩니다. **스포크-클래식** VNet에서 아무것도 구성할 필요가 없습니다.

1. Azure Portal에서 **허브-RM** 가상 네트워크로 이동하여 **피어링** 을 선택한 다음 **+ 추가** 를 선택합니다.
1. **피어링 추가** 페이지에서 다음 값을 구성합니다.

   * 피어링 링크 이름: 링크의 이름을 지정합니다. 예: **HubRMToClassic**
   * 원격 가상 네트워크로의 트래픽: **허용**
   * 원격 가상 네트워크에서 전달된 트래픽 **허용**
   * 가상 네트워크 게이트웨이: **이 가상 네트워크의 게이트웨이 사용**
   * 원격 가상 네트워크: **클래식**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="스포크-클래식에 대한 피어링 페이지 추가":::

1. 구독이 올바른지 확인한 다음 드롭다운에서 가상 네트워크를 선택합니다.
1. **추가** 를 선택하여 피어링을 추가합니다.
1. 허브-RM 가상 네트워크에서 피어링 상태가 **연결됨** 인지 확인합니다. 

이 구성에서는 **스포크-클래식** 가상 네트워크에서 아무것도 구성할 필요가 없습니다. 상태가 **연결됨** 으로 표시되면 스포크 가상 네트워크는 허브 가상 네트워크의 VPN Gateway를 통해 연결을 사용할 수 있습니다.

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell 샘플

또한 위의 예제에서 PowerShell을 사용하여 피어링을 만들거나 업데이트할 수 있습니다. 변수 및 구독 ID를 사용자의 가상 네트워크와 리소스 그룹 및 구독 값으로 바꿉니다. 허브 가상 네트워크에서 가상 네트워크 피어링을 만들기만 하면 됩니다.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>다음 단계

* 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 [가상 네트워크 피어링 제약 조건 및 동작](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) 및 [가상 네트워크 피어링 설정](../virtual-network/virtual-network-manage-peering.md#create-a-peering)에 대해 자세히 알아봅니다.
* 가상 네트워크 피어링 및 게이트웨이 전송을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) 방법을 알아봅니다.
* [동일한 배포 모델로 가상 네트워크 피어링을 만듭니다](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [다른 배포 모델로 가상 네트워크 피어링을 만듭니다](../virtual-network/create-peering-different-deployment-models.md).
