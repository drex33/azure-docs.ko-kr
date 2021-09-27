---
title: '시나리오: Virtual WAN에 대한 Azure Firewall 사용자 지정 라우팅'
titleSuffix: Azure Virtual WAN
description: VNet 간에 트래픽을 직접 라우팅하지만 VNet-인터넷/분기 및 분기-VNet 트래픽 흐름에 대해 Azure Firewall을 사용하는 라우팅 시나리오에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f6581638379154675f1d93213ecaecf8b1ccb012
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649003"
---
# <a name="scenario-azure-firewall---custom"></a>시나리오: Azure Firewall - 사용자 지정

Virtual WAN 가상 허브 라우팅 작업 시 사용할 수 있는 시나리오가 상당히 많습니다. 이 시나리오에서 목표는 VNet 간에 트래픽을 직접 라우팅하는 것이지만 VNet-인터넷/분기 및 분기-VNet 트래픽 흐름에 대해 Azure Firewall을 사용합니다.

## <a name="design"></a><a name="design"></a>디자인

얼마나 많은 경로 테이블이 필요한지 파악하기 위해 연결 매트릭스를 만들 수 있습니다. 여기서 각 셀은 원본(행)이 대상(열)과 통신할 수 있는지 여부를 나타냅니다. 이 시나리오의 연결 매트릭스는 사소하지만 다른 시나리오와 일관되게 볼 수 있습니다.

**연결 매트릭스**

| 시작           | 아래와 같이 변경합니다.      | *VNets*      | *분기*    | *인터넷*   |
|---             |---       |---           |---            |---           |
| **VNets**      |   &#8594;|    직접    |     AzFW      |     AzFW     |
| **분기**   |   &#8594;|    AzFW      |    직접     |    직접    |

이전 표에서 "직접"은 트래픽이 Virtual WAN에서 Azure Firewall을 통과하지 않고 두 연결 간의 직접 연결을 나타내고 "AzFW"는 흐름이 Azure Firewall을 통과함을 나타냅니다. 매트릭스에는 두 개의 고유한 연결 패턴이 있으므로 다음과 같이 구성되는 두 개의 경로 테이블이 필요합니다.

* 가상 네트워크:
  * 연결된 경로 테이블: **RT_VNet**
  * 경로 테이블에 전파: **RT_VNet**
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**

> [!NOTE]
> 각 지역에 하나의 단일 보안 가상 허브가 있는 별도의 가상 WAN 인스턴스를 만든 다음 사이트 간 VPN을 통해 각 가상 WAN을 서로 연결할 수 있습니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="workflow"></a><a name="workflow"></a>워크플로

이 시나리오에서는 VNet-인터넷, VNet-분기 또는 분기-VNet 트래픽의 경우 Azure Firewall을 통해 트래픽을 라우팅하려고 하지만 VNet 간 트래픽에 대해서는 직접 이동하려고 합니다. Azure Firewall Manager를 사용한 경우 경로 설정이 **기본 경로 테이블** 에 자동으로 채워집니다. 프라이빗 트래픽은 VNet 및 분기에 적용되고 인터넷 트래픽은 0.0.0.0/0에 적용됩니다.

VPN, ExpressRoute 및 사용자 VPN 연결은 집합적으로 분기라고 하며 동일한(기본값) 경로 테이블에 연결됩니다. 모든 VPN, ExpressRoute, 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파합니다. 이 시나리오를 구성하려면 다음 단계를 고려해야 합니다.

1. 사용자 지정 경로 테이블 **RT_VNet** 을 만듭니다.
1. Azure Firewall을 가리키는 다음 홉으로 VNet-인터넷 및 VNet-분기: 0.0.0.0/0을 활성화하는 경로를 만듭니다. 전파 섹션에서는 보다 구체적인 경로를 보장하여 VNet 간 직접 트래픽 흐름을 허용하는 VNet이 선택되었는지 확인합니다.

   * **연결:** 에서 VNet이 이 경로 테이블의 경로에 따라 대상에 도달함을 암시하는 VNet을 선택합니다.
   * **전파:** 에서 VNet이 이 경로 테이블에 전파됨을 암시하는 VNet을 선택합니다. 즉, 보다 구체적인 경로가 이 경로 테이블에 전파되어 VNet 간 직접적인 트래픽 흐름을 보장합니다.

1. VNet에 대해 집계된 고정 경로를 **기본 경로 테이블** 에 추가하여 Azure Firewall을 통해 분기-VNet 흐름을 활성화합니다.

   * 분기는 기본 경로 테이블에 연결되고 전파된다는 점을 기억합니다.
   * 분기는 RT_VNet 경로 테이블로 전파되지 않습니다. 이렇게 하면 Azure Firewall을 통한 VNet-분기 트래픽 흐름이 보장됩니다.

그러면 **그림 1** 과 같이 라우팅 구성이 변경됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="그림 1":::


## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
* 가상 허브 라우팅을 구성하는 방법에 대한 자세한 내용은 [가상 허브 라우팅을 구성하는 방법](how-to-virtual-hub-routing.md)을 참조하세요.
