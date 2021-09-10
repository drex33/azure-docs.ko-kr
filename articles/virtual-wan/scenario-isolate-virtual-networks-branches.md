---
title: '시나리오: 가상 네트워크 및 분기에 대한 사용자 지정 격리'
titleSuffix: Azure Virtual WAN
description: 선택한 VNet과 분기가 서로 연결할 수 없도록 하는 Virtual WAN 라우팅 시나리오에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 49e6a65bf14b3d7678a996685efde1b1a2c0a9d3
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719689"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>시나리오: 가상 네트워크 및 분기에 대한 사용자 지정 격리

Virtual WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. VNet(가상 네트워크) 및 분기 둘 다에 대한 사용자 지정 격리 시나리오에서 목표는 특정 VNet 세트가 다른 VNet 세트에 도달하는 것을 방지하는 것입니다. 마찬가지로 분기(VPN/ER/사용자 VPN) 또한 특정 VNet 세트에만 도달할 수 있습니다.

또한 Azure Firewall에서 분기-VNet 및 VNet-분기 트래픽을 검사해야 하지만 VNet-VNet 트래픽은 검사하지 **않습니다**.  

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="design"></a><a name="design"></a>디자인

필요한 경로 테이블 수를 파악하기 위해 연결 행렬을 빌드할 수 있습니다. 이 시나리오에서 연결 행렬은 다음과 같은데, 여기서 각 셀은 원본(행)이 대상(열)과 통신할 수 있는지 여부를 나타냅니다.

| 시작 | 아래와 같이 변경합니다.| *파랑 VNet* | *빨강 VNet* | *빨강 분기*| *파랑 분기*| 
|---|---|---|---|---|---|
| **파랑 VNet** |   &#8594;|   직접     |           |   |  AzFW|
| **빨강 VNet**  |   &#8594;|              |   직접  |  AzFW  | 
| **빨강 분기**   |   &#8594;|   |   AzFW  |  직접 | 직접
| **파랑 분기**| &#8594;| AzFW  |   |직접   | 직접

위의 표에 나와 있는 각 셀은 Virtual WAN 연결(흐름의 ‘시작’ 쪽, 즉 행 머리글)이 대상(흐름의 ‘대상’ 쪽, 즉 이탤릭체 열 머리글)과 통신하는지 여부를 설명합니다. **직접** 은 트래픽이 Virtual WAN을 통해 직접 흐르는 것을 암시하지만, **AzFW** 는 트래픽이 대상에 전달되기 전에 Azure Firewall에서 검사된다는 것을 의미합니다. 빈 항목은 설정에서 흐름이 차단됨을 의미합니다.

이 경우 경로에 Azure Firewall이 없는 VNet 격리 목표를 달성하기 위해 VNet에 대한 두 경로 테이블이 필요합니다. 이러한 경로 테이블을 **RT_BLUE** 및 **RT_RED** 라고 지칭하겠습니다.

또한 분기는 항상 **기본** 경로 테이블에 연결되어 있어야 합니다. Azure Firewall에서 분기로 들어오고 나가는 트래픽을 검사하도록 하기 위해 **기본**, **RT_RED** 및 **RT_BLUE** 경로 테이블에 Azure Firewall로 트래픽을 전송하는 고정 경로를 추가하고 원하는 트래픽을 허용하는 네트워크 규칙을 설정합니다. 또한 분기가 **RT_BLUE** 및 **RT_RED** 에 전파되지 **않도록** 합니다.

따라서 최종 디자인은 다음과 같습니다.

* 파랑 가상 네트워크:
  * 연결된 경로 테이블: **RT_BLUE**
  * 경로 테이블로 전파: **RT_BLUE**
* 빨강 가상 네트워크:
  * 연결된 경로 테이블: **RT_RED**
  * 경로 테이블로 전파: **RT_RED** 
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**
* 고정 경로:
    * **기본 경로 테이블**: 다음 홉 Azure Firewall이 있는 가상 네트워크 주소 공간
    * **RT_RED**: 다음 홉 Azure Firewall이 있는 0.0.0.0/0
    * **RT_BLUE**: 다음 홉 Azure Firewall이 있는 0.0.0.0/0
* 방화벽 네트워크 규칙:
    * **허용 규칙** **원본 접두사**: 파랑 분기 주소 접두사 **대상 접두사**: 파랑 VNet 접두사 
    * **허용 규칙** **원본 접두사**: 빨강 분기 주소 접두사 **대상 접두사**: 빨강 VNet 접두사

> [!NOTE]
> 모든 분기가 Default 경로 테이블에 연결되어 있어야 하고 동일한 라우팅 테이블 세트로 전파되어야 하므로 모든 분기의 연결 프로필이 동일합니다. 즉, VNet의 빨강/파랑 개념은 분기에 적용될 수 없습니다. 그러나 분기에 대한 사용자 지정 라우팅을 구현하기 위해 분기에서 Azure Firewall로 트래픽을 전달할 수 있습니다.

> [!NOTE]
> Azure Firewall은 기본적으로 제로 트러스트 모델의 트래픽을 거부합니다. 검사된 패킷과 일치하는 명시적 **허용** 규칙이 없는 경우 Azure Firewall은 패킷을 삭제합니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.



## <a name="workflow"></a><a name="architecture"></a>워크플로

**그림 1** 에는 Blue 및 Red VNet, Blue 또는 Red VNet에 액세스할 수 있는 분기가 나와 있습니다.

* 파랑으로 연결된 VNet은 서로 연결될 수 있으며 모든 파랑 분기(VPN/ER/P2S) 연결에 도달할 수 있습니다. 다이어그램에서 파랑 분기는 사이트 간 VPN 사이트입니다.
* 빨강으로 연결된 VNet은 서로 연결될 수 있으며 모든 빨강 분기(VPN/ER/P2S) 연결에 도달할 수 있습니다. 다이어그램에서 빨강 분기는 지점 및 사이트 간 VPN 사이트입니다.

라우팅을 설정하는 경우 다음 단계를 고려하세요.

1. 이러한 VNet으로의 트래픽을 사용자 지정하기 위해 Azure Portal에서 두 개의 사용자 지정 경로 테이블인 **RT_BLUE** 및 **RT_RED** 를 만듭니다.
2. 경로 테이블 **RT_BLUE** 의 경우 파랑 VNet이 다른 모든 파랑 VNet의 주소 접두사를 학습할 수 있도록 다음 설정을 적용합니다.
   * **연결**: 파랑 VNet을 모두 선택합니다.
   * **전파**: 파랑 VNet을 모두 선택합니다.
3. 빨강 VNet의 **RT_RED** 경로 테이블에 대해 동일한 단계를 반복합니다.
4. VIrtual WAN에서 Azure Firewall을 프로비저닝합니다. Virtual WAN 허브의 Azure Firewall에 대한 자세한 내용은 [ 허브에서 Azure Firewall 구성](howto-firewall.md)을 참조하세요.
5. VNet 주소 공간(파랑 및 빨강 모두)으로 향하는 모든 트래픽을 Azure Firewall로 전달하는 가상 허브의 **기본** 경로 테이블에 고정 경로를 추가합니다. 이 단계를 통해 분기의 모든 패킷이 검사를 위해 Azure Firewall로 전송됩니다.
    * 예제: **대상 접두사**: 10.0.0.0/8 **다음 홉**: Azure Firewall
    >[!NOTE]
    > "프라이빗 트래픽 보안" 옵션을 선택하여 Firewall Manager를 통해 이 단계를 수행할 수도 있습니다. 그러면 VNet 및 분기에 적용 가능한 모든 RFC1918 개인 IP 주소에 대한 경로가 추가됩니다. RFC1918과 호환되지 않는 분기 또는 가상 네트워크에서는 수동으로 추가해야 합니다. 

6. **RT_RED** 및 **RT_BLUE** 에 모든 트래픽을 Azure Firewall로 보내는 고정 경로를 추가합니다. 이 단계를 수행하면 VNet에서 분기에 직접 액세스할 수 없습니다. 이 단계는 이러한 가상 네트워크가 기본 경로 테이블과 연결되어 있지 않으므로 Firewall Manager를 통해서는 수행할 수 없습니다.
    * 예제: **대상 접두사**: 0.0.0.0/0 **다음 홉**: Azure Firewall

    > [!NOTE]
    > 라우팅은 LPM(가장 긴 접두사 일치)을 사용하여 수행됩니다. 따라서 0.0.0.0/0 고정 경로는 **BLUE_RT** 및 **RED_RT** 에 존재하는 정확한 접두사보다 선호되지 **않습니다**. 결과적으로, Azure Firewall에서 VNet 내 트래픽을 검사하지 않게 됩니다.

이렇게 하면 아래 그림에 표시된 것처럼 라우팅 구성이 변경됩니다.

**그림 1**
[ ![그림 1](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
