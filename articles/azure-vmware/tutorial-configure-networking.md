---
title: 자습서 - Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성
description: Azure에서 프라이빗 클라우드를 배포하는 데 필요한 네트워킹을 만들고 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/30/2021
ms.openlocfilehash: 99389c55ab13b6c6c181a4d7d1bcf14c3b8ba08d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597800"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성

Azure VMware Solution 프라이빗 클라우드에는 Azure Virtual Network가 필요합니다. Azure VMware Solution이 온-프레미스 vCenter를 지원하지 않으므로 온-프레미스 환경과 통합하려면 추가 단계가 필요합니다. ExpressRoute 회로와 가상 네트워크 게이트웨이도 설정해야 합니다.

[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)]


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기 
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결

>[!NOTE]
>새 VNet을 만들기 전에 Azure에 기존 VNet이 이미 있는지 확인하고 이를 사용해서 Azure VMware Solution에 연결할지 또는 VNet을 완전히 새로 만들지 여부를 계획합니다.  
>* Azure VMware Solution과 동일한 Azure 구독에서 기존 vNet을 사용하려면 **연결** 의 **[Azure vNet 연결](#select-an-existing-vnet)** 탭을 사용합니다. 
>* Azure VMware Solution과 다른 Azure 구독에서 기존 vNet을 사용하려면 **[프라이빗 클라우드로 수동 연결](#connect-to-the-private-cloud-manually)** 의 지침을 사용합니다. 
>* Azure VMware Solution과 동일한 Azure 구독에서 새 vNet을 만들려면 **[Azure vNet 연결](#create-a-new-vnet)** 탭을 사용하거나 [수동으로](#create-a-vnet-manually) 만듭니다.

## <a name="connect-with-the-azure-vnet-connect-feature"></a>Azure VNet 연결 기능을 사용하여 연결

**Azure VNet 연결** 기능을 사용하여 기존 VNet을 사용하거나 새 VNet을 만들어서 Azure VMware Solution에 연결할 수 있습니다.   

>[!NOTE]
>VNet의 주소 공간은 Azure VMware Solution 프라이빗 클라우드 CIDR과 겹칠 수 없습니다.


### <a name="select-an-existing-vnet"></a>기존 VNet 선택

기존 VNet을 선택하면 VNet 및 기타 리소스를 만드는 ARM(Azure Resource Manager) 템플릿이 다시 배포됩니다. 이 경우 리소스는 공용 IP, 게이트웨이, 게이트웨이 연결 및 ExpressRoute 권한 부여 키입니다. 모든 항목이 설정되었으면 배포가 아무 것도 변경하지 않습니다. 하지만 누락된 항목이 있으면 자동으로 생성됩니다. 예를 들어 GatewaySubnet이 누락되었으면 배포 중에 추가됩니다.

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **연결** 을 선택합니다.

2. **Azure VNet 연결** 탭을 선택한 후 기존 VNet을 선택합니다.

   :::image type="content" source="media/networking/azure-vnet-connect-tab.png" alt-text="기존 VNet이 선택된 상태로 연결 아래의 Azure VNet 연결 탭을 보여주는 스크린샷입니다.":::

3. **저장** 을 선택합니다.

   이 시점에서 VNet은 Azure VMware Solution과 VNet 사이에 겹치는 IP 주소 공간이 검색되었는지 확인합니다. 검색되었으면 겹치지 않도록 프라이빗 클라우드 또는 VNet의 네트워크 주소를 변경합니다. 


### <a name="create-a-new-vnet"></a>새 VNet 만들기

새 VNet을 만들 때 Azure VMware Solution에 연결하는 데 필요한 구성 요소가 자동으로 생성됩니다.

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **연결** 을 선택합니다.

2. **Azure VNet 연결** 탭을 선택한 후 **새로 만들기** 를 선택합니다.

   :::image type="content" source="media/networking/azure-vnet-connect-tab-create-new.png" alt-text="연결 아래의 Azure VNet 연결 탭을 보여주는 스크린샷입니다.":::

3. 새 VNet에 대해 정보를 제공하거나 업데이트한 후 **확인** 을 선택합니다.

   이 시점에서 VNet은 Azure VMware Solution과 VNet 사이에 겹치는 IP 주소 공간이 검색되었는지 확인합니다. 검색되었으면 겹치지 않도록 프라이빗 클라우드 또는 VNet의 네트워크 주소를 변경합니다. 

   :::image type="content" source="media/networking/create-new-virtual-network.png" alt-text="가상 네트워크 만들기 창을 보여주는 스크린샷입니다.":::

제공된 주소 공간 및 GatewaySubnet을 포함하는 VNet이 구독 및 리소스 그룹에 생성됩니다.  


## <a name="connect-to-the-private-cloud-manually"></a>프라이빗 클라우드에 수동으로 연결

### <a name="create-a-vnet-manually"></a>수동으로 VNet 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [프라이빗 클라이드 만들기 자습서](tutorial-create-private-cloud.md)에서 만든 리소스 그룹으로 이동하고 **+ 추가** 를 선택하여 새 리소스를 정의합니다. 

1. **Marketplace 검색** 텍스트 상자에 **Virtual Network** 를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크** 페이지에서 **만들기** 를 선택하여 프라이빗 클라우드에 대한 가상 네트워크를 설정합니다.

1. **Virtual Network 만들기** 페이지에서 가상 네트워크에 대한 세부 정보를 입력합니다.

1. **기본** 탭에서 가상 네트워크의 이름을 입력하고, 적절한 지역을 선택하고, **다음: IP 주소** 를 선택합니다.

1. **IP 주소** 탭의 **IPv4 주소 공간** 아래에서 이전 자습서에서 만든 주소 공간을 입력합니다.

   > [!IMPORTANT]
   > 이전 자습서에서 프라이빗 클라우드를 만들 때 사용한 주소 공간과 **겹치지 않는** 주소 공간을 사용해야 합니다.

1. **+ 서브넷 추가** 를 선택하고, **서브넷 추가** 페이지에서 이름과 적절한 주소 범위를 서브넷에 지정합니다. 마쳤으면 **추가** 를 선택합니다.

1. **검토 + 만들기** 를 선택합니다.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="새 가상 네트워크에 대한 설정을 보여주는 스크린샷." border="true":::

1. 정보를 확인하고 **만들기** 를 선택합니다. 배포가 완료되면 리소스 그룹에 가상 네트워크가 표시됩니다.



### <a name="create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

가상 네트워크를 만들었으므로 이제 가상 네트워크 게이트웨이를 만듭니다.

1. 리소스 그룹에서 **+ 추가** 를 선택하여 새 리소스를 추가합니다.

1. **Marketplace 검색** 텍스트 상자에서 **가상 네트워크 게이트웨이** 를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크 게이트웨이** 페이지에서 **만들기** 를 선택합니다.

1. **가상 네트워크 게이트웨이 만들기** 페이지의 기본 탭에서 필드 값을 제공한 다음, **검토 + 만들기** 를 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 리소스 그룹이 속하는 구독으로 미리 채워진 값입니다. |
   | **리소스 그룹** | 현재 리소스 그룹에 대해 이미 채워진 값입니다. 값은 이전 테스트에서 만든 리소스 그룹이어야 합니다. |
   | **이름** | 가상 네트워크 게이트웨이에 대한 고유한 이름을 입력합니다. |
   | **지역** | 가상 네트워크 게이트웨이의 지리적 위치를 선택합니다. |
   | **게이트웨이 유형** | **ExpressRoute** 를 선택합니다. |
   | **SKU** | 기본값(**표준**)을 그대로 둡니다. |
   | **가상 네트워크** | 이전에 만든 가상 네트워크를 선택합니다. 가상 네트워크가 표시되지 않으면 게이트웨이의 지역이 가상 네트워크의 지역과 일치하는지 확인합니다. |
   | **게이트웨이 서브넷 주소 범위** | 이 값은 가상 네트워크를 선택할 때 채워집니다. 기본값을 변경하지 마세요. |
   | **공용 IP 주소** | **새로 만들기** 를 선택합니다. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="가상 네트워크 게이트웨이에 대한 세부 정보를 보여주는 스크린샷." border="true":::

1. 세부 정보가 올바른지 확인하고 **만들기** 를 선택하여 가상 네트워크 게이트웨이의 배포를 시작합니다.

1. 배포가 완료되면 다음 섹션으로 이동하여 ExpressRoute 연결을 Azure VMware Solution 프라이빗 클라우드가 포함된 가상 네트워크 게이트웨이에 연결합니다.

### <a name="connect-expressroute-to-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이에 ExpressRoute 연결

가상 네트워크 게이트웨이를 배포했으므로 이제 게이트웨이와 Azure VMware Solution 프라이빗 클라우드 간에 연결을 추가합니다.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * VNet 연결 기능을 사용하여 가상 네트워크 만들기
> * 수동으로 가상 네트워크 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결


다음 자습서를 계속 진행하여 vCenter에서 VM에 사용되는 NSX-T 네트워크 세그먼트를 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [NSX-T 네트워크 세그먼트 만들기](./tutorial-nsx-t-network-segment.md)
