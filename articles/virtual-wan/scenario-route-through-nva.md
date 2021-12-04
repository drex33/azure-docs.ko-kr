---
title: '시나리오: NVA(네트워크 가상 어플라이언스)를 통한 트래픽 라우팅'
titleSuffix: Azure Virtual WAN
description: NVA(네트워크 가상 어플라이언스)를 통해 트래픽을 라우팅하기 위한 Virtual WAN 라우팅 시나리오에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: a780f99b4413aadf46826a6b9688314dac36ce87
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543489"
---
# <a name="scenario-route-traffic-through-an-nva"></a>시나리오: NVA를 통한 트래픽 라우팅

Virtual WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 NVA 시나리오에서 목표는 NVA(네트워크 가상 어플라이언스)를 통해 분기에서 VNet으로, VNet에서 분기로 트래픽을 라우팅하는 것입니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

> [!NOTE]
> 새 기능 [가상 허브 라우팅 구성 방법](how-to-virtual-hub-routing.md)을 사용할 수 있기 전에 이미 라우팅을 설정한 경우 다음 문서 버전의 단계를 사용하세요.
>* [Azure Portal 문서](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 문서](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>디자인

이 시나리오에서는 명명 규칙을 사용합니다.

* 사용자가 NVA를 배포하고 다른 가상 네트워크를 스포크로 연결하는 가상 네트워크는 'NVA VNet'(이 문서 뒷부분의 **그림 2** 에서 VNet 2 및 VNet 4)
* NVA VNet에 연결된 가상 네트워크는 'NVA 스포크'(이 문서 뒷부분의 **그림 2** 에서 VNet 5, VNet 6, VNet 7 및 VNet 8)
* Virtual WAN에 연결되고 NVA 또는 다른 VNet이 피어링되지 않은 가상 네트워크는 '비 NVA VNet'(이 문서 뒷부분의 **그림 2** 에서 VNet 1 및 VNet 3)
* NVA VNet이 연결되는 Microsoft 관리형 Virtual WAN 허브는 '허브' NVA 스포크 VNet는 Virtual WAN 허브에 연결할 필요가 없고 NVA VNet에만 연결됩니다.

다음 연결 매트릭스에는 이 시나리오에서 지원되는 흐름이 요약되어 있습니다.

**연결 매트릭스**

| 시작             | 아래와 같이 변경합니다.|   *NVA 스포크*|*NVA VNet*|*비 NVA VNet*|*분기*|
|---|---|---|---|---|---|
| **NVA 스포크**   | &#8594; | NVA VNet을 통해 | 피어링 | NVA VNet을 통해 | NVA VNet을 통해 |
| **NVA VNet**    | &#8594; | 피어링 | 직접 | 직접 | 직접 |
| **비 NVA VNet**| &#8594; | NVA VNet을 통해 | 직접 | 직접 | 직접 |
| **분기**     | &#8594; | NVA VNet을 통해 | 직접 | 직접 | 직접 |

연결 매트릭스의 각 셀은 VNet 또는 분기(흐름의 '시작' 쪽, 표의 행 머리글)가 대상 VNet 또는 분기(흐름의 '도착' 쪽, 표의 기울임꼴 열 머리글)와 통신하는 방법을 설명합니다. '직접'은 Virtual WAN에 의해 기본적으로 연결이 제공됨을 의미합니다. '피어링'은 VNet의 사용자 정의 경로에서 연결이 제공됨을 의미합니다. 'NVA VNet'은 연결에서 NVA VNet에 배포된 NVA를 통과함을 의미합니다. 다음을 살펴보세요.

* NVA 스포크는 Virtual WAN에 의해 관리되지 않습니다. 그러므로 NVA 스포크가 다른 VNet 또는 분기와 통신하는 데 사용하는 메커니즘을 사용자가 유지 관리합니다. NVA VNet에 대한 연결은 VNet 피어링을 통해 제공되며, 다음 홉으로 NVA를 가리키는 0.0.0.0/0에 대한 기본 경로가 인터넷, 다른 스포크 및 분기에 대한 연결을 담당해야 합니다.
* NVA VNet은 자체 NVA 스포크를 인식하지만 다른 NVA VNet에 연결된 NVA 스포크는 인식하지 못합니다. 예를 들어 이 문서의 아래쪽에 있는 그림 2에서 VNet 2는 VNet 5 및 VNet 6을 인식하지만 VNet 7 및 VNet 8과 같은 다른 스포크는 인식하지 못합니다. 고정 경로는 다른 스포크의 접두사를 NVA VNet에 삽입하는 데 필요합니다.
* 마찬가지로, NVA 스포크는 Virtual WAN 허브에 연결되지 않으므로 분기 및 비 NVA VNet은 NVA 스포크를 인식하지 못합니다. 따라서 여기서도 고정 경로만 필요합니다.

NVA 스포크가 Virtual WAN에 의해 관리되지 않음을 고려하면, 다른 모든 행은 동일한 연결 패턴을 보입니다. 따라서 단일 경로 테이블(기본 경로)은 다음과 같습니다.

* 가상 네트워크(비 허브 VNet 및 사용자 허브 VNet):
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**

그러나 이 시나리오에서는 구성할 고정 경로를 고려해야 합니다. 각 고정 경로에는 두 가지 구성 요소가 포함됩니다. Virtual WAN 허브에서 Virtual WAN 구성 요소에 각 스포크에 사용할 연결을 지정하는 구성 요소가 하나이고, NVA(또는 여러 NVA 앞의 부하 분산 장치)에 할당된 구체적인 IP 주소를 가리키는 특정 연결에 다른 하나가 있습니다(**그림 1** 참조).

**그림 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="그림 1":::

따라서 NVA VNet 뒤의 NVA 스포크로 트래픽을 전송하기 위해 기본 테이블에 필요한 고정 경로는 다음과 같습니다.

| Description | 경로 테이블 | 고정 경로              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | 기본값     | 10.2.0.0/16 -> eastusconn |
| VNet 4       | 기본값     | 10.4.0.0/16 -> weconn     |

이제 Virtual WAN은 패킷을 보내야 할 연결을 알고 있지만, 연결은 이러한 패킷을 받을 때 수행할 작업을 알아야 합니다. 이를 위해 연결 라우팅 테이블이 사용됩니다. 여기서는 더 짧은 접두사(/16 대신 /24)를 사용하여 이러한 경로가 NVA VNet(VNet 2 및 VNet 4)에서 가져온 경로보다 우선적으로 적용되도록 합니다.

| Description | 연결 | 고정 경로            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24 -> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24 -> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24 -> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 -> 10.4.0.5 |

이제 NVA VNet, 비 NVA VNet 및 분기는 모든 NVA 스포크에 연결하는 방법을 알 수 있습니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="architecture"></a><a name="architecture"></a>아키텍처

**그림 2** 에는 두 개의 허브 **Hub1** 및 **Hub2** 가 있습니다.

* **Hub1** 및 **Hub2** 는 NVA VNet인 **VNet 2** 및 **VNet 4** 에 직접 연결됩니다.

* **VNet 5** 및 **VNet 6** 은 **VNet 2** 와 피어링됩니다.

* **VNet 7** 및 **VNet 8** 은 **VNet 4** 와 피어링됩니다.

* **VNet 5,6,7,8** 은 가상 허브와 직접 연결되지 않는 간접 스포크입니다.

**그림 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="그림 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

NVA를 통한 라우팅을 설정하려면 다음 단계를 고려해야 합니다.

1. NVA 스포크 VNet 연결을 식별합니다. **그림 2** 에서는 **VNet 2 연결(eastusconn)** 및 **VNet 4 연결(weconn)** 을 사용할 수 있습니다.

   UDR이 설정되어 있는지 확인합니다.
   * VNet 5 및 VNet 6에서 VNet 2 NVA IP로
   * VNet 7 및 VNet 8에서 VNet 4 NVA IP로 
   
   VNet 5,6,7,8을 가상 허브에 직접 연결할 필요는 없습니다. VNet 5,6,7,8의 NSG가 분기(VPN/ER/P2S) 또는 해당 원격 VNet에 연결된 VNet에 대한 트래픽을 허용하는지 확인합니다. 예를 들어, VNet 5,6은 NSG가 온-프레미스 주소 접두사 그리고 원격 허브 2에 연결된 VNet 7,8에 대한 트래픽을 허용하는지 확인해야 합니다.

Virtual WAN은 VNet 5,6이 가상 허브에 연결하고 VNet 2 NVA IP를 통해 통신하는 시나리오를 지원하지 않습니다. 따라서 VNet 5,6을 VNet2에 연결하고, 마찬가지로 VNet 7,8을 VNet 4에 연결해야 합니다.

2. VNet 2,5,6의 집계된 고정 경로 항목을 허브 1의 기본 경로 테이블에 추가합니다.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="예제":::
   
   > [!NOTE]
   > 라우팅을 간소화 하 고 가상 WAN 허브 경로 테이블의 변경 내용을 줄이려면 가상 WAN 허브 (미리 보기)와 함께 새로운 BGP 피어 링을 사용 하는 것이 좋습니다. 자세한 내용은 다음 문서를 참조하세요. 
   >* [시나리오: 가상 허브를 사용 하는 BGP 피어 링 (미리 보기)](scenario-bgp-peering-hub.md) 
   >* [가상 허브 (미리 보기)를 사용 하 여 BGP 피어 링을 만드는 방법-Azure Portal](create-bgp-peering-hub-portal.md)
   >

3. VNet 2의 가상 네트워크 연결에서 VNet 5,6의 고정 경로를 구성합니다. 가상 네트워크 연결을 위한 라우팅 구성을 설정하려면 [가상 허브 라우팅](how-to-virtual-hub-routing.md#routing-configuration)을 참조하세요.

4. VNet 4,7,8의 집계된 고정 경로 항목을 허브 1의 기본 경로 테이블에 추가합니다.

5. 허브 2의 기본 경로 테이블에 대해 2, 3 및 4단계를 반복합니다.

이렇게 하면 아래 **그림 3** 에 표시된 것처럼 라우팅 구성이 변경됩니다.

**그림 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="그림 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
