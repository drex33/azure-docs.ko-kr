---
title: 'Azure VPN Gateway: P2S 라우팅 정보'
titleSuffix: Azure VPN Gateway
description: 다양한 운영 체제, 원격 액세스 프로토콜, 가상 네트워크 구성의 Azure 지점 및 사이트 간 VPN 라우팅에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/03/2021
ms.author: cherylmc
ms.openlocfilehash: a4035f05300be8b06daa5fbc0fa43cba1f95886e
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133540732"
---
# <a name="about-point-to-site-vpn-routing"></a>지점 및 사이트 간 VPN 라우팅 정보

이 문서는 Azure 지점 및 사이트 간 VPN 라우팅이 작동하는 방법을 이해하는 데 도움이 됩니다. P2S(지점 및 사이트 간) VPN 라우팅 동작은 클라이언트 OS, VPN 연결에 사용된 프로토콜 및 VNet(가상 네트워크)이 서로 연결된 방식에 따라 다릅니다. 지원 되는 프로토콜을 포함 하 여 지점 및 사이트 간 VPN에 대 한 자세한 내용은 [지점 및 사이트 간 Vpn 정보](point-to-site-about.md)를 참조 하세요.

네트워크의 토폴로지를 변경하고 Windows VPN 클라이언트를 사용하는 경우, 변경 내용을 클라이언트에 적용하기 위해 Windows 클라이언트용 VPN 클라이언트 패키지를 다운로드하여 다시 설치해야 합니다.

> [!NOTE]
> 이 문서는 IKEv2에만 적용됩니다.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>다이어그램에 대한 정보

이 문서에는 서로 다른 여러 가지 다이어그램이 있습니다. 각 섹션에서는 다른 토폴로지 또는 구성을 보여 줍니다. 이 문서에서는 S2S(사이트 간) 및 VNet 간 연결이 모두 IPsec 터널이므로 동일한 방식으로 작동합니다. 이 문서의 모든 VPN 게이트웨이는 경로 기반입니다.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>격리된 단일 VNet

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 다른 가상 네트워크(VNet1)와 연결되거나 피어링되지 않은 VNet에 대한 연결입니다. 이 예제에서 클라이언트는 VNet1에 액세스할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="격리된 VNet 라우팅" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에 액세스할 수 있습니다.

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>피어링된 여러 VNet

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 VNet2와 피어링됩니다. VNet2는 VNet3과 피어링됩니다. VNet1은 VNet4와 피어링됩니다. VNet1 및 VNet3 간에는 직접 피어링되지 않습니다. VNet1에는 “게이트웨이 전송 허용”, VNet2 및 VNet4에는 “원격 게이트웨이 사용”이 각각 사용하도록 설정되어 있습니다.

Windows를 사용하는 클라이언트는 피어링된 VNet에 직접 액세스할 수 있지만, VNet 피어링 또는 네트워크 토폴로지가 변경되면 VPN 클라이언트를 다시 다운로드해야 합니다. 비Windows 클라이언트는 피어링된 VNet에 직접 액세스할 수 있습니다. 액세스는 전이되지 않으며, 직접 피어링된 VNet으로만 제한됩니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="피어링된 여러 VNet" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2 및 VNet4에 액세스할 수 있지만, 토폴로지 변경 내용을 적용하려면 VPN 클라이언트를 다시 다운로드해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2 및 VNet4에 액세스할 수 있습니다.

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>S2S VPN을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 간에는 직접 피어링 또는 사이트 간 VPN 연결이 없습니다. 모든 사이트 간 연결에서 라우팅을 위해 BGP를 실행하지 않습니다.

Windows 또는 지원되는 다른 OS를 사용하는 클라이언트는 VNet1에만 액세스할 수 있습니다. 추가 VNet에 액세스하려면 BGP를 사용해야 합니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="여러 VNet 및 S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>S2S VPN(BGP)을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 간에는 직접 피어링 또는 사이트 간 VPN 연결이 없습니다. 모든 사이트 간 연결에서 라우팅을 위해 BGP를 실행합니다.

Windows 또는 지원되는 다른 OS를 사용하는 클라이언트는 사이트 간 VPN 연결을 사용하여 연결된 모든 VNet에 액세스할 수 있지만, 연결된 VNet에 대한 경로를 Windows 클라이언트에 수동으로 추가해야 합니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="여러 VNet 및 S2S(BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2 및 VNet3에 액세스할 수 있지만, VNet2 및 VNet3에 대한 경로를 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2 및 VNet3에 액세스할 수 있습니다.

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>단일 VNet 및 단일 지점

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 다른 가상 네트워크와 연결되거나 피어링되지 않지만, BGP를 실행하지 않는 사이트 간 VPN 연결을 통해 온-프레미스 사이트에 연결됩니다.

Windows 및 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="VNet 및 지점을 사용하여 라우팅" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>단일 VNet 및 단일 지점(BGP)

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 다른 가상 네트워크와 연결되거나 피어링되지 않지만, BGP를 실행하는 사이트 간 VPN 연결을 통해 온-프레미스 사이트(Site1)에 연결됩니다.

Windows 클라이언트는 VNet 및 지점(Site1)에 액세스할 수 있지만, Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다. 비Windows 클라이언트는 VNet 및 온-프레미스 지점에 액세스할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="VNet 및 지점을 사용하여 라우팅 - BGP" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1 및 Site1에 액세스할 수 있지만, Site1에 대한 경로를 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1 및 Site1에 액세스할 수 있습니다.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>S2S와 지점을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 네트워크 간에는 직접 피어링 또는 사이트 간 VPN 터널이 없습니다. VNet3은 사이트 간 VPN 연결을 사용하여 지점(Site1)에 연결됩니다. 모든 VPN 연결에서 BGP를 실행하지 않습니다.

모든 클라이언트는 VNet1에만 액세스할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="다중 VNet S2S 및 지점을 보여 주는 다이어그램" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>S2S와 지점(BGP)을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 네트워크 간에는 직접 피어링 또는 사이트 간 VPN 터널이 없습니다. VNet3은 사이트 간 VPN 연결을 사용하여 지점(Site1)에 연결됩니다. 모든 VPN 연결에서 BGP를 실행합니다.

Windows를 사용하는 클라이언트는 사이트 간 VPN 연결을 사용하여 연결된 VNet 및 사이트에 액세스할 수 있지만, VNet2, VNet3 및 Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다. 비Windows 클라이언트는 수동 개입 없이 사이트 간 VPN 연결을 사용하여 연결된 VNet 및 사이트에 액세스할 수 있습니다. 액세스는 전이되며, 클라이언트는 연결된 모든 VNet 및 사이트(온-프레미스)의 리소스에 액세스할 수 있습니다.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="다중 VNet S2S 및 지점" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2, VNet3 및 Site1에 액세스할 수 있지만, VNet2, VNet3 및 Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2, VNet3 및 Site1에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 P2S VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하여 P2S VPN 만들기를 시작합니다.
