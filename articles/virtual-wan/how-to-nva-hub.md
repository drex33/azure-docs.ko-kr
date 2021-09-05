---
title: 'Azure Virtual WAN: 허브에서 NVA(네트워크 가상 어플라이언스) 만들기'
description: Virtual WAN 허브에 네트워크 가상 어플라이언스를 배포하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 0252305167f0f77d310810af0a147106fae9e0c6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528483"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>Azure Virtual WAN 허브에서 네트워크 가상 어플라이언스를 만드는 방법

이 문서에서는 Azure에서 Virtual WAN을 사용하여 NVA(**네트워크 가상 어플라이언스**)를 통해 Azure의 리소스에 연결하는 방법을 보여 줍니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN이란?](virtual-wan-about.md)을 참조하세요.

이 문서의 단계를 통해 Virtual WAN 허브에 **Barracuda CloudGen WAN** 네트워크 가상 어플라이언스를 만들 수 있습니다. 이 연습을 완료하려면 시작하기 전에 허브에 배포하는 Barracuda CloudGen WAN 어플라이언스에 대한 Barracuda CPE(Cloud Premise Device) 및 라이선스가 있어야 합니다.

Azure Virtual WAN 내의 **CISCO SD-WAN** 배포 설명서는 [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)(다중 클라우드용 Cisco Cloud OnRamp)를 참조하세요. 

Azure Virtual WAN 내의 **VMware SD-WAN** 배포 설명서는 [Deployment Guide for VMware SD-WAN in Virtual WAN Hub](https://kb.vmware.com/s/article/82746)(Virtual WAN 허브의 VMware SD-WAN 배포 가이드)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* Barracuda CloudGen WAN 게이트웨이에 대한 라이선스를 획득합니다. 이 작업을 수행하는 방법에 대해 자세히 알아보려면 [Barracuda CloudGen WAN 설명서](https://www.barracuda.com/products/cloudgenwan)를 참조하세요.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스 사이트에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtual WAN 만들기

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>허브 만들기

허브는 사이트 간, ExpressRoute, 지점 및 사이트 간 또는 네트워크 가상 어플라이언스 기능을 위한 게이트웨이를 포함할 수 있는 가상 네트워크입니다. 허브가 생성되면 사이트를 연결하지 않아도 허브 요금이 청구됩니다. 사이트 간 VPN 게이트웨이를 만들도록 선택하는 경우 가상 허브에서 사이트 간 VPN 게이트웨이를 만드는 데 30분이 소요됩니다. 사이트 간, ExpressRoute 또는 지점 및 사이트 간 연결과는 달리, 허브 VNet에 네트워크 가상 어플라이언스를 배포하기 전에 허브를 만들어야 합니다.

1. 생성한 Virtual WAN을 찾습니다. **Virtual WAN** 페이지의 **연결** 섹션에서 **허브** 를 선택합니다.
1. **허브** 페이지에서 +새 허브를 선택하여 **가상 허브 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="기본 사항":::
1. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   **프로젝트 세부 정보**

   * 지역(이전에는 위치라고 했음)
   * Name
   * 허브 프라이빗 주소 공간. 허브를 만들기 위한 최소 주소 공간은 /24이며 이것은 /25부터 /32까지의 모든 범위가 생성 중에 오류가 발생함을 의미합니다. Microsoft에서 관리되는 서비스에 해당하는 Azure Virtual WAN은 다양한 게이트웨이/서비스에 대한 가상 허브에 적절한 서브넷을 만듭니다. (예: VPN 게이트웨이, ExpressRoute 게이트웨이, 사용자 VPN/지점 및 사이트 간 게이트웨이, 방화벽, 라우팅 등) Microsoft가 서비스의 일부로 이 작업을 수행하므로 사용자가 Virtual Hub의 서비스에 대한 서브넷 주소 공간을 명시적으로 계획할 필요가 없습니다.
1. **검토 + 만들기** 를 선택하여 유효한지 확인합니다.
1. **만들기** 를 선택하여 허브를 만듭니다.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>허브에서 네트워크 가상 어플라이언스 만들기

이 단계에서는 허브에 네트워크 가상 어플라이언스를 만듭니다. 각 NVA에 대한 절차는 NVA 파트너의 제품마다 다릅니다. 이 예제에서는 Barracuda CloudGen WAN 게이트웨이를 만듭니다.

1. 이전 단계에서 만든 Virtual WAN 허브를 찾아 엽니다.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="가상 허브":::
1. 네트워크 가상 어플라이언스 타일을 찾고 **만들기** 링크를 선택합니다.
1. **네트워크 가상 어플라이언스** 블레이드에서 **Barracuda CloudGen WAN** 을 선택하고 **만들기** 단추를 선택합니다.

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA 선택":::
1. 그러면 Barracuda CloudGen WAN 게이트웨이의 Azure Marketplace 제품으로 이동됩니다. 약관을 읽은 다음, 준비가 되면 **만들기** 단추를 선택합니다.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA 기본 사항":::
1. **기본 사항** 페이지에서 다음 정보를 제공해야 합니다.

   * **구독** - Virtual WAN 및 허브를 배포하는 데 사용한 구독을 선택합니다.
   * **리소스 그룹** - Virtual WAN 및 허브를 배포하는 데 사용한 것과 동일한 리소스 그룹을 선택합니다.
   * **지역** - 가상 허브 리소스가 있는 동일한 지역을 선택합니다.
   * **애플리케이션 이름** - Barracuda NextGen WAN은 관리형 애플리케이션입니다. 이 리소스를 쉽게 식별할 수 있도록 하는 이름을 선택합니다. 이 이름은 구독에서 리소스를 지칭하는 이름이 됩니다.
   * **관리되는 리소스 그룹** - Barracuda에서 관리하는 리소스를 배포하는 관리되는 리소스 그룹의 이름입니다. 이 이름은 미리 채워집니다.
1. **다음: CloudGen WAN 게이트웨이** 단추를 선택합니다.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN 게이트웨이":::
1. 다음 정보를 제공합니다.

   * **Virtual WAN 허브** - 이 NVA를 배포하려는 Virtual WAN 허브입니다.
   * **NVA 인프라 단위** - 이 NVA를 배포하려는 NVA 인프라 단위의 수를 나타냅니다. 이 NVA를 통해 이 허브에 연결되는 모든 분기 사이트에서 제공할 집계 대역폭 용량 크기를 선택합니다.
   * **토큰** - Barracuda는 사용자를 이 제품의 등록된 사용자로 식별하기 위해 여기에 인증 토큰을 제공할 것을 요구합니다. 이 토큰은 Barracuda에서 받아야 합니다.
1. **검토 및 만들기** 단추를 선택하여 계속합니다.
1. 이 페이지에 공동 관리자 액세스 계약 약관에 동의하라는 메시지가 표시됩니다. 게시자가 이 배포의 일부 리소스에 액세스할 수 있는 관리형 애플리케이션에서는 이러한 메시지가 기본적으로 표시됩니다. **위의 사용 약관에 동의함** 확인란을 선택하고 **만들기** 를 선택합니다.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>허브에 VNet 연결

이 섹션에서는 허브와 VNet 간의 연결을 만듭니다.

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대해 자세히 알아보려면 [Virtual WAN이란?](virtual-wan-about.md) 페이지를 참조하세요.
* Virtual WAN 허브의 NVA에 대해 자세히 알아보려면 [Virtual WAN 허브의 네트워크 가상 어플라이언스 정보](about-nva-hub.md)를 참조하세요.
