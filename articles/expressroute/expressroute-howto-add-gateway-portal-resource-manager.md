---
title: '자습서: Azure ExpressRoute - VNet에 게이트웨이 추가(Azure Portal)'
description: 이 자습서에서는 Azure Portal을 사용하여 ExpressRoute용 VNet에 가상 네트워크 게이트웨이를 추가하는 과정을 안내합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 01a82e178725cc310e5e693c738de047f09c1b7a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505596"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

이 자습서에서는 기존 VNet에 대한 가상 네트워크 게이트웨이를 추가하는 단계를 안내합니다. 이 문서에서는 기존 VNet에 대한 가상 네트워크(VNet) 게이트웨이를 추가, 제거하고 크기를 조정하는 단계를 안내합니다. 이 구성에서 수행하는 단계는 Resource Manager 배포 모델을 사용하여 만든 VNet(ExpressRoute 구성에서 사용됨)에만 해당합니다. 가상 네트워크 게이트웨이 및 ExpressRoute의 게이트웨이 구성 설정에 대한 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)를 참조하세요. 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 게이트웨이 서브넷을 만듭니다.
> - 가상 네트워크 게이트웨이를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 작업의 단계는 다음 구성 참조 목록의 값을 기반으로 VNet을 사용합니다. 예제 단계에서 이 목록을 사용합니다. 목록을 복사하여 참조로 사용하고 값을 사용자 값으로 바꿀 수 있습니다.

**구성 참조 목록**

* Virtual Network 이름 = "TestVNet"
* Virtual Network 주소 공간: 192.168.0.0/16
* 서브넷 이름= “FrontEnd” 
    * 서브넷 주소 공간 = “192.168.1.0/24”
* 리소스 그룹: "TestRG"
* 위치 = “미국 동부”
* 게이트웨이 서브넷 이름: "GatewaySubnet" 게이트웨이 서브넷의 이름을 항상 *GatewaySubnet* 으로 지정해야 합니다.
    * 게이트웨이 서브넷 주소 공간 = "192.168.200.0/26"
* 게이트웨이 이름 = “ERGW”
* 게이트웨이 공용 IP 이름 = “MyERGWVIP”
* 게이트웨이 유형 = “ExpressRoute” Express 경로 구성에 이 유형이 필요합니다.

구성을 시작하기 전에 이러한 단계의 [비디오](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)를 시청하십시오.

## <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기

1. [포털](https://portal.azure.com)에서 가상 네트워크 게이트웨이를 만들려는 Resource Manager 가상 네트워크로 이동합니다.
1. VNet의 **설정** 섹션에서 **서브넷** 을 선택하여 서브넷 설정을 확장합니다.
1. **서브넷** 설정에서 **+ 게이트웨이 서브넷** 을 선택하여 게이트웨이 서브넷을 추가합니다. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="게이트웨이 서브넷 추가":::

1. 서브넷의 **이름** 에 'GatewaySubnet' 값이 자동으로 채워집니다. Azure가 서브넷을 게이트웨이 서브넷으로 인식하기 위해 이 값이 필요합니다. 자동으로 채워진 **주소 범위** 값을 구성 요구 사항과 일치하도록 조정합니다. /27 이상의 게이트웨이 서브넷을 만드는 것이 좋습니다(/26, /25 등). 16개의 ExpressRoute 회로를 게이트웨이에 연결하려는 경우 /26개 이상의 게이트웨이 서브넷을 **만들어야 합니다**.

    이중 스택 가상 네트워크를 사용하고 ExpressRoute를 통해 IPv6 기반 프라이빗 피어링을 사용하려는 경우 **IP6 주소 공간 추가** 를 클릭하고 **IPv6 주소 범위** 값을 입력합니다.

    그런 다음, **확인** 을 선택하여 값을 저장하고 게이트웨이 서브넷을 만듭니다.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="서브넷 추가":::

## <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

1. 포털의 왼쪽에서 **+ 리소스 만들기** 를 선택하고 검색에서 '가상 네트워크 게이트웨이'를 입력합니다. 검색 결과에서 **가상 네트워크 게이트웨이** 를 찾아서 항목을 선택합니다. **가상 네트워크 게이트웨이** 페이지에서 **만들기** 를 선택합니다.
1. **가상 네트워크 게이트웨이 만들기** 페이지에서 이 설정을 입력하거나 선택합니다.

    | 설정 | 값 |
    | --------| ----- |
    | Subscription | 올바른 구독을 선택하는지 확인합니다. |
    | 리소스 그룹 | 가상 네트워크를 선택하면 리소스 그룹이 자동으로 선택됩니다. | 
    | 속성 | 게이트웨이 이름을 지정합니다. 이는 게이트웨이 서브넷 이름 지정과 동일하지는 않습니다. 만들고 있는 게이트웨이 개체의 이름입니다.|
    | 지역 | 가상 네트워크가 있는 위치를 가리키도록 **영역** 필드를 변경합니다. 위치에서 가상 네트워크가 있는 영역을 가리키고 있지 않으면 가상 네트워크가 '가상 네트워크 선택' 드롭다운에 나타나지 않습니다. |
    | 게이트웨이 유형 | **ExpressRoute** 를 선택합니다.|
    | SKU | 드롭다운에서 게이트웨이 SKU를 선택합니다. |
    | 가상 네트워크 | *TestVNet* 을 선택합니다. |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다.|
    | 공용 IP 주소 이름 | 공용 IP 주소의 이름을 입력합니다. |

    > [!IMPORTANT]
    > ExpressRoute를 통해 IPv6 기반 프라이빗 피어링을 사용하려는 경우 [PowerShell 지침](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway)를 사용하여 표준, 정적 형식의 공용 IP 주소로 게이트웨이를 만들어야 합니다.
    > 
    > 

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 게이트웨이를 만들기 시작합니다. 설정이 검증되었으며 게이트웨이가 배포됩니다. 가상 네트워크 게이트웨이 만들기는 완료되는 데 최대 45분까지 소요됩니다.

## <a name="clean-up-resources"></a>리소스 정리

ExpressRoute 게이트웨이가 더 이상 필요하지 않으면 가상 네트워크 리소스 그룹에서 게이트웨이를 찾아 **삭제** 를 선택합니다. 게이트웨이가 회로에 연결되어 있지 않은지 확인합니다.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="가상 네트워크 게이트웨이 삭제":::

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. 

> [!div class="nextstepaction"]
> [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)
