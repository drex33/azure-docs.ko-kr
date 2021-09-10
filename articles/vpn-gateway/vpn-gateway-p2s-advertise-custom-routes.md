---
title: 지점 및 사이트 간 VPN Gateway 클라이언트에 대한 사용자 지정 경로 보급
titleSuffix: Azure VPN Gateway
description: VPN Gateway 지점 및 사이트 간 클라이언트에 사용자 지정 경로를 보급하는 방법을 알아봅니다. 이 문서에는 VPN 클라이언트 강제 터널링 단계가 포함되어 있습니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/21/2021
ms.author: cherylmc
ms.openlocfilehash: cefbd6d014dda28a5e88a41a0131eeb92fc5f311
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440725"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN 클라이언트에 대한 사용자 지정 경로 보급

모든 지점 및 사이트 간 VPN 클라이언트에 사용자 지정 경로를 보급하는 것이 좋습니다. 예를 들어 VNet에서 스토리지 엔드포인트를 사용하도록 설정했으며 원격 사용자가 VPN 연결을 통해 해당 스토리지 계정에 액세스할 수 있게 하려는 경우 스토리지 계정에 대한 트래픽이 공용 인터넷이 아닌 VPN 터널을 통해 전달되도록 스토리지 엔드포인트의 IP 주소를 모든 원격 사용자에게 보급할 수 있습니다. VPN 클라이언트에 대한 강제 터널링을 구성하기 위해 사용자 지정 경로를 사용할 수도 있습니다.

:::image type="content" source="./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png" alt-text="사용자 지정 경로 보급의 다이어그램.":::

## <a name="advertise-custom-routes"></a><a name="advertise"></a>사용자 지정 경로 보급

사용자 지정 경로를 보급하려면 `Set-AzVirtualNetworkGateway cmdlet`을 사용합니다. 다음 예제에서는 [Contoso 스토리지 계정 테이블](https://contoso.table.core.windows.net)의 IP를 보급하는 방법을 보여 줍니다.

1. *contoso.table.core.windows.net* 을 ping하고 IP 주소를 적어 둡니다. 예를 들면 다음과 같습니다.

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 다음 PowerShell 명령을 실행합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 여러 개의 사용자 지정 경로를 추가하려면 쉼표와 공백을 사용하여 주소를 구분합니다. 예를 들면 다음과 같습니다.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```

## <a name="advertise-custom-routes---forced-tunneling"></a><a name="forced-tunneling"></a>사용자 지정 경로 보급 - 강제 터널링

0\.0.0.0/1 및 128.0.0.0/1을 클라이언트에 대한 사용자 지정 경로로 보급하여 모든 트래픽을 VPN 터널로 보낼 수 있습니다. 0\.0.0.0/0을 두 개의 더 작은 서브넷으로 나누는 이유는, 이러한 더 작은 접두사가 로컬 네트워크 어댑터에 이미 구성되어 있을 수 있는 기본 경로보다 더 구체적이어서 트래픽을 라우팅할 때 선호되기 때문입니다.

> [!NOTE]
> 인터넷 연결은 VPN 게이트웨이를 통해 제공되지 않습니다. 따라서 인터넷에 바인딩된 모든 트래픽이 삭제됩니다.
>

1. 강제 터널링을 사용하도록 설정하려면 다음 명령을 사용합니다.

    ```azurepowershell-interactive    
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 0.0.0.0/1 , 128.0.0.0/1
    ```

## <a name="view-custom-routes"></a><a name="view"></a>사용자 지정 경로 보기

사용자 지정 경로를 보려면 다음 예제를 사용합니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="delete-custom-routes"></a><a name="delete"></a>사용자 지정 경로 삭제

사용자 지정 경로를 삭제하려면 다음 예제를 사용합니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>다음 단계

P2S 라우팅에 대한 자세한 내용은 [지점 및 사이트 간 라우팅 정보](vpn-gateway-about-point-to-site-routing.md)를 참조하세요.
