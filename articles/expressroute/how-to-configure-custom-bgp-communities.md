---
title: Azure ExpressRoute 프라이빗 피어링에 대한 사용자 지정 BGP 커뮤니티 구성(미리 보기)
description: 새 가상 네트워크 또는 기존 가상 네트워크에 BGP 커뮤니티 값을 적용하거나 업데이트하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/18/2021
ms.author: duau
ms.openlocfilehash: da3be1f9fbfc2872c31ed4f40d01a1d38ef300ef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482901"
---
# <a name="configure-custom-bgp-communities-for-azure-expressroute-private-peering-preview"></a>Azure ExpressRoute 프라이빗 피어링에 대한 사용자 지정 BGP 커뮤니티 구성(미리 보기)

BGP 커뮤니티는 커뮤니티 값으로 태그가 지정된 IP 접두사 그룹화입니다. 이 값은 라우터의 인프라에 대한 라우팅 결정을 내리는 데 사용할 수 있습니다. BGP 커뮤니티 태그를 사용하여 Azure에서 온-프레미스로 전송된 트래픽에 대해 필터를 적용하거나 라우팅 기본 설정을 지정할 수 있습니다. 이 문서에서는 Azure PowerShell 사용하여 가상 네트워크에 사용자 지정 BGP 커뮤니티 값을 적용하는 방법을 설명합니다. 구성되면 지역 BGP 커뮤니티 가치와 가상 네트워크의 사용자 지정 커뮤니티 값을 볼 수 있습니다. 이 값은 해당 가상 네트워크에서 발생하는 경우 ExpressRoute를 통해 전송되는 아웃바운드 트래픽에 사용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다. 
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-arm.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
  * 회로에 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-arm.md) 을 참조하세요. 
  * Azure 개인 피어링이 구성되고 엔드투엔드 연결을 위해 네트워크와 Microsoft 간에 BGP 피어링이 설정되어 있는지 확인합니다.
  
### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="apply-a-custom-bgp-community-value-for-a-new-virtual-network"></a>새 가상 네트워크에 대한 사용자 지정 BGP 커뮤니티 값 적용

1. 구성을 시작하려면 Azure 계정에 로그인하고 사용할 구독을 선택합니다.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

1. 새 가상 네트워크를 저장할 리소스 그룹을 만듭니다.

    ```azurepowershell-interactive
    $rg = @{
        Name = 'myERRG'
        Location = 'WestUS'
    }
    New-AzResourceGroup @rg
    ```

1. 플래그가 있는 새 가상 네트워크를 만들어 `-BgpCommunity` BGP 커뮤니티 값을 적용합니다.

    ```azurepowershell-interactive
    $vnet = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
        Location = 'WestUS'
        AddressPrefix = '10.0.0.0/16'
        BgpCommunity = '12076:20001'    
    }
    New-AzVirtualNetwork @vnet
    ```
    
    > [!NOTE]
    > `12076:`는 사용자 지정 커뮤니티 값 앞에 필요합니다.
    >

1. 가상 네트워크를 검색하고 해당 속성을 검토합니다. **RegionalCommunity** 값과 **VirtualNetworkCommunity** 값이 포함된 *BgpCommunities* 섹션을 확인할 수 있습니다. *RegionalCommunity* 값은 가상 네트워크의 Azure 지역에 따라 미리 정의됩니다. *VirtualNetworkCommunity* 값은 사용자 지정 정의와 일치해야 합니다.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNewtork @virtualnetwork
    ```

## <a name="applying-or-updating-the-custom-bgp-value-for-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 사용자 지정 BGP 값 적용 또는 업데이트

1. BGP 커뮤니티 값을 적용하거나 업데이트하려는 가상 네트워크를 가져온 다음 변수에 저장합니다.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    $vnet = Get-AzVirtualNewtork @virtualnetwork
    ```

1. 가상 `VirtualNetworkCommunity` 네트워크의 값을 업데이트합니다.

    ```azurepowershell-interactive
    $vnet.BgpCommunities.VirtualNetworkCommunity = '12076:20002'
    $vnet | Set-AzVirtualNetwork
    ```

    > [!NOTE]
    > `12076:`는 사용자 지정 커뮤니티 값 앞에 필요합니다.
    >

1. 가상 네트워크를 검색하고 업데이트된 속성을 검토합니다. **RegionalCommunity** 값은 가상 네트워크의 Azure 지역에 따라 미리 정의됩니다. **VirtualNetworkCommunity** 값은 사용자 지정 정의와 일치해야 합니다.

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNetwork @virtualnetwork
    ```

> [!IMPORTANT]
>  기존 가상 네트워크가 이미 ExpressRoute 회로에 연결된 경우 사용자 지정 BGP 커뮤니티 값을 적용한 후 ExpressRoute 연결을 삭제하고 다시 만들어야 합니다. 방법을 알아보려면 [가상 네트워크를 ExpressRoute 회로](expressroute-howto-linkvnet-arm.md)에 연결을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- [ExpressRoute 연결을 확인합니다.](expressroute-troubleshooting-expressroute-overview.md)
- [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
