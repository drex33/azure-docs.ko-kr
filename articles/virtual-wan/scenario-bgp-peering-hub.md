---
title: 가상 허브와 BGP 피어링에 대한 정보
titleSuffix: Azure Virtual WAN
description: Azure Virtual WAN 가상 허브와 BGP 피어링에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 5cc5b3dd26d0cb88460fabbd2ceb3cd28b107121
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129076"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>시나리오: 가상 허브와 BGP 피어링(미리 보기)

> [!IMPORTANT]
> Virtual WAN 허브 기능을 사용하는 BGP 피어링은 현재 제어된 공개 미리 보기 상태입니다. 이 기능을 사용하려는 경우 Virtual WAN 리소스의 리소스 ID와 함께 이메일( **previewbgpwithvhub@microsoft.com** )을 보내세요. 기능 사용 확인이 수신된 후 다음 구조화 [페이지에](create-bgp-peering-hub-portal.md) 따라 주요 고려 사항 및 자세한 구성 가이드를 확인하세요. 
>
> 리소스 ID를 찾으려면 Azure Portal을 열고 Virtual WAN 리소스로 이동하고, **설정 > 속성 > 리소스 ID** 를 클릭합니다.<br> 예: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>


가상 허브 라우터라고도 하는 Azure Virtual WAN 허브 라우터는 경로 관리자 역할을 하며, 가상 허브 내부와 가상 허브 간의 라우팅 작업을 단순화합니다. 즉, 가상 허브 라우터는 다음을 수행합니다.

* VPN, ExpressRoute, P2S 및 NVA(네트워크 가상 어플라이언스)와 같은 게이트웨이와 통신하는 중앙 라우팅 엔진이 되어 라우팅 관리를 간소화합니다. 
* 사용자 지정 경로 테이블, 연결 및 경로 전파라는 고급 라우팅 시나리오를 지원합니다.
* 가상 허브에 연결된 가상 네트워크 간/로 전송되는 트래픽의 라우터 역할을 합니다.

이제 가상 허브 라우터는 피어링 기능을 노출하므로, BGP(Border Gateway Protocol) 라우팅 프로토콜을 통해 직접 라우팅 정보를 교환할 수 있습니다. 가상 허브에 연결된 가상 네트워크에 프로비저닝된 NVA 또는 BGP 엔드포인트는 BGP 라우팅 프로토콜을 지원하고 NVA의 ASN이 가상 허브 ASN과 다르게 설정되도록 보장하는 경우 가상 허브 라우터와 직접 피어링할 수 있습니다.

## <a name="benefits-and-considerations"></a>이점 및 고려 사항

**주요 이점**

* 가상 네트워크 주소가 업데이트될 때마다 NVA에서 라우팅 테이블을 수동으로 업데이트할 필요는 없습니다.
* 이제 NVA가 새 경로를 알리거나 이전 경로를 취소할 때마다 사용자 정의 경로를 수동으로 업데이트할 필요가 없습니다.
* 가상 허브에 연결된 가상 네트워크의 NVA는 가상 허브 게이트웨이(VPN, ExpressRoute 또는 관리형 NVA) 경로를 학습할 수 있습니다.
* NVA의 여러 인스턴스를 가상 허브 라우터와 피어링할 수 있습니다. NVA에서 BGP 특성을 구성할 수 있으며, 디자인(활성-활성인지 아니면 활성-수동인지)에 따라 가상 허브 라우터는 어떤 NVA 인스턴스가 활성 또는 수동인지 알 수 있습니다.

**고려 사항**

* 가상 허브 라우터를 가상 네트워크에 프로비저닝된 Azure Route Server와 피어링할 수 없습니다.
* 가상 허브 라우터는 16비트(2바이트) ASN만 지원합니다.
* NVA BGP 연결 엔드포인트가 있는 가상 네트워크 연결은 항상 defaultRouteTable에 연결하고 전파해야 합니다. 사용자 지정 경로 테이블은 현재 지원되지 않습니다.
* 가상 허브 라우터는 가상 허브에 연결된 가상 네트워크 간의 전송 연결을 지원합니다. Virtual WAN이 이미 전송 연결을 지원하므로 이것은 BGP 피어링 기능과 아무 관련이 없습니다. 예제:
  * VNET1: NVA1이 가상 허브 1에 연결 -> (전송 연결) -> VNET2: NVA2가 가상 허브 1에 연결
  * VNET1: NVA1이 가상 허브 1에 연결 -> (전송 연결) -> VNET2: NVA2가 가상 허브 2에 연결
* 네트워크 가상 어플라이언스에서 고유 퍼블릭 ASN 또는 프라이빗 ASN을 사용할 수 있습니다. Azure 또는 IANA에서 예약한 범위는 사용할 수 없습니다. 다음 ASNs는 Azure 또는 IANA에서 예약됩니다.
   * Azure에서 예약된 ASN:
     * 공용 ASN: 8074, 8075, 12076
     * 프라이빗 ASN: 65515, 65517, 65518, 65519, 65520
   * IANA에서 예약한 ASN: 23456, 64496-64511, 65535-65551
* 가상 허브 라우터는 NVA와 BGP 경로를 교환하고 가상 네트워크에 전파하지만, 가상 허브에 호스트되는 게이트웨이(VPN Gateway/ExpressRoute 게이트웨이/관리형 NVA 게이트웨이)를 통해 온-프레미스에서 직접 경로를 전파하도록 지원합니다. 

   가상 허브 라우터에는 다음과 같은 제한이 있습니다.

   | 리소스 | 제한 |
   |---|---|
   |  각 BGP 피어가 Azure Route Server에 보급할 수 있는 경로 수| 허브는 연결된 리소스의 경로를 10,000개(합계)까지 수락할 수 있습니다. 예를 들어 가상 허브에 연결된 가상 네트워크, 분기, 가상 허브 등의 경로가 총 6000개인 경우 새로운 BGP와 NVA의 피어링이 구성되면 NVA는 최대 4000개의 경로를 보급할 수 있습니다. |
* BGP를 통해 가상 허브에 보급될 때 가상 네트워크 주소 공간보다 더 구체적인 가상 네트워크의 NVA 경로는 온-프레미스로 더 이상 전파되지 않습니다.
* 가상 허브에 직접 연결된 가상 네트워크의 주소로 향하는 트래픽은 허브와 NVA 간의 BGP 피어링을 사용하여 NVA를 통해 라우팅되도록 구성할 수 없습니다. 이는 스포크 가상 네트워크 연결을 만들 때 가상 허브가 스포크 가상 네트워크의 주소와 연결된 시스템 경로에 대해 자동으로 학습하기 때문입니다. 이러한 자동으로 학습된 시스템 경로는 BGP를 통해 허브에서 학습한 경로보다 선호됩니다.

## <a name="bgp-peering-scenarios"></a>BGP 피어링 시나리오

이 섹션에서는 BGP 피어링 기능을 활용하여 라우팅을 구성할 수 있는 시나리오에 대해 설명합니다.

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>전송 VNet 연결

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="VNet 간 라우팅을 보여주는 그래픽":::

이 시나리오에서 "Hub 1"이라는 가상 허브는 여러 가상 네트워크에 연결되어 있습니다. 목표는 가상 네트워크 VNET1과 VNET5 간의 라우팅을 설정하는 것입니다.

### <a name="configuration-steps-without-bgp-peering"></a>BGP 피어링을 사용하지 않는 구성 단계

가상 허브에서 BGP 피어링을 사용하지 않는 경우 다음 단계가 필수입니다.

가상 허브 구성

* Hub 1의 defaultRouteTable에서, VNET2 연결을 가리키는 VNET5(서브넷 10.2.1.0/24)의 고정 경로를 구성합니다.
* Hub 1의 VNET2에 대한 가상 네트워크 연결에서, VNET2 NVA IP(서브넷 10.2.0.5)를 가리키는 VNET5에 대한 고정 경로를 구성합니다.
* Hub 1에서, VNET1 및 VNET2에 대한 연결의 경로를 defaultRouteTable에 전파하고, defaultRouteTable에 연결합니다.

가상 네트워크 구성

* VNET5에서, VNET2 NVA IP를 가리키도록 UDR(사용자 정의 경로)을 설정합니다.

### <a name="configuration-steps-with-bgp-peering"></a>BGP 피어링을 사용하는 구성 단계

이전 구성에서는 VNET5 구성이 자주 변경되면 고정 경로 및 UDR의 유지 관리가 복잡해질 수 있습니다. 이러한 문제를 해결하려면 가상 허브와 BGP 피어링 기능을 사용하고 다음 단계에 따라 라우팅 구성을 변경해야 합니다.

가상 허브 구성

* Hub 1에서, VNET2 NVA를 BGP 피어로 구성합니다. 또한 Hub 1과 BGP 피어링되도록 VNET2 NVA를 구성합니다.
* Hub 1에서, VNET1 및 VNET2에 대한 연결의 경로를 defaultRouteTable에 전파하고, defaultRouteTable에 연결합니다.

가상 네트워크 구성

* VNET5에서, VNET2 NVA IP를 가리키도록 UDR(사용자 정의 경로)을 설정합니다.

#### <a name="effective-routes"></a>유효한 경로

아래 표에서는 defaultRouteTable의 허브 1 유효 경로 몇 가지를 보여줍니다. VNET5(서브넷 10.2.1.0/24)의 경로를 보면 VNET1 및 VNET5가 서로 통신할 수 있다는 것을 확인할 수 있습니다.

| 대상 접두사 |  다음 홉| 원본 | ASN 경로|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | VNet 연결 ID | - |
| 10.2.1.0/24 |NVA에 대한 BGP 피어 연결 ID  | NVA에 대한 BGP 피어 연결 ID |  65510|
|  10.4.1.0/24 | Hub 2 | Hub 2 | - |

이 기능을 사용하여 이러한 방식으로 라우팅을 구성하면 가상 허브에서 고정 경로 항목이 필요 없습니다. 따라서 구성이 간단하며, 연결된 가상 네트워크(예: VNET5)의 구성이 변경되면 경로 테이블이 동적으로 업데이트됩니다.

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet"></a>분기 VNet 연결

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="분기-VNet 라우팅을 보여주는 그래픽":::

이 시나리오에 나오는 "NVA Branch 1"이라는 온-프레미스 사이트에는 VNET2 NVA에서 종료하도록 구성된 VPN이 있습니다. 목표는 NVA Branch 1과 가상 네트워크 VNET1 간의 라우팅을 구성하는 것입니다.

### <a name="configuration-steps-without-bgp-peering"></a>BGP 피어링을 사용하지 않는 구성 단계

가상 허브에서 BGP 피어링을 사용하지 않는 경우 다음 단계가 필수입니다.

가상 허브 구성

* Hub 1의 defaultRouteTable에서, VNET2 연결을 가리키는 NVA Branch 1의 고정 경로를 구성합니다.
* Hub 1의 VNET2에 대한 가상 네트워크 연결에서, VNET2 NVA IP(서브넷 10.2.0.5)를 가리키는 NVA Branch 1에 대한 고정 경로를 구성합니다.
* Hub 1에서, VNET1 및 VNET2에 대한 연결의 경로를 defaultRouteTable에 전파하고, defaultRouteTable에 연결합니다.

가상 네트워크 구성

* VNET2 NVA와 NVA Branch 1 간의 BGP 피어링, VNET2 NVA에서 NVA Branch 1으로 VNET1에 대한 경로 보급 알림

### <a name="configuration-steps-with-bgp-peering"></a>BGP 피어링을 사용하는 구성 단계

시간이 지나면 NVA Branch 1의 대상 접두사가 변경되거나, NVA Branch 1과 같은 여러 사이트가 VNET1에 연결해야 할 수도 있습니다. 이렇게 되면 Hub 1 및 VNET2 연결의 고정 경로를 업데이트해야 하며, 이 작업은 번거로울 수 있습니다. 이 경우 가상 허브와 BGP 피어링 기능을 사용할 수 있으며 라우팅 연결 구성 단계는 아래와 같습니다.

가상 허브 구성

* Hub 1에서, VNET2 NVA를 BGP 피어로 구성합니다. 또한 Hub 1과 BGP 피어링되도록 VNET2 NVA를 구성합니다.
* Hub 1에서, VNET1 및 VNET2에 대한 연결의 경로를 defaultRouteTable에 전파하고, defaultRouteTable에 연결합니다.

가상 네트워크 구성

* VNET2 NVA와 NVA Branch 1 간의 BGP 피어링, VNET2 NVA에서 NVA Branch 1으로 VNET1에 대한 경로 보급 알림

#### <a name="effective-routes"></a>유효한 경로

아래 표에서는 defaultRouteTable의 허브 1 유효 경로 몇 가지를 보여줍니다. NVA Branch 1(서브넷 192.168.1.0/24)에 대한 경로는 NVA와의 BGP 피어링을 통해 학습됩니다.

 | 대상 접두사 |  다음 홉| 원본 | ASN 경로|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | VNet 연결 ID | - |
| 192.168.1.0/24 | NVA에 대한 BGP 피어 연결 ID  | NVA에 대한 BGP 피어 연결 ID |  65510|

NVA Branch 1의 네트워크 변경 내용을 관리하거나 NVA Branch 1과 같은 새 사이트 간에 연결을 설정하려는 경우 Hub 1과 NVA 간의 BGP 피어링이 경로 테이블을 동적으로 업데이트하기 때문에 Hub 1에서 추가 구성이 필요 없습니다. 따라서 구성 및 유지 관리가 간단합니다.

## <a name="next-steps"></a>다음 단계

* [가상 허브와 BGP 피어링 구성](create-bgp-peering-hub-portal.md)
