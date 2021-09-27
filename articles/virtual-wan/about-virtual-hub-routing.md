---
title: 가상 허브 라우팅 정보
titleSuffix: Azure Virtual WAN
description: Virtual WAN 가상 허브 라우팅에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: de9488a475998b50d06d26f1d8b02bcdb1b21290
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780835"
---
# <a name="about-virtual-hub-routing"></a>가상 허브 라우팅 정보

가상 허브의 라우팅 기능은 BGP(Border Gateway Protocol)를 사용하여 게이트웨이 간의 모든 라우팅을 관리하는 라우터에서 제공합니다. 가상 허브에는 사이트 간 VPN Gateway, ExpressRoute 게이트웨이, 지점 및 사이트 간 게이트웨이, Azure Firewall과 같은 여러 게이트웨이가 포함될 수 있습니다. 또한 이 라우터는 가상 허브에 연결하는 가상 네트워크 간의 전송 연결을 제공하며 50Gbps의 최대 집계 처리량을 지원할 수 있습니다. 이러한 라우팅 기능은 표준 가상 WAN 고객에게 적용됩니다.

라우팅을 구성하려면 [가상 허브 라우팅을 구성하는 방법](how-to-virtual-hub-routing.md)을 참조하세요.

## <a name="routing-concepts"></a><a name="concepts"></a>라우팅 개념

다음 섹션에서는 가상 허브 라우팅의 주요 개념을 설명합니다.

### <a name="hub-route-table"></a><a name="hub-route"></a>허브 경로 테이블

가상 허브 경로 테이블에는 하나 이상의 경로가 포함될 수 있습니다. 경로에는 이름, 레이블, 대상 유형, 대상 접두사 목록 및 라우팅할 패킷에 대한 다음 홉 정보가 포함됩니다. **연결** 에는 일반적으로 경로 테이블과 연결되거나 경로 테이블에 전파되는 라우팅 구성이 있습니다.

### <a name="hub-routing-intent-and-policies"></a><a name= "hub-route"></a> 허브 라우팅 의도 및 정책
>[!NOTE]  
> 허브 라우팅 정책은 현재 관리형 미리 보기 상태입니다. 
>  
>이 미리 보기에 대한 액세스 권한을 얻으려면 previewinterhub@microsoft.com에 연결하여 라우팅 정책을 구성하려는 Virtual WAN ID, 구독 ID 및 Azure 지역을 제공하세요. 기능 지원이 확인되면 24-48시간 이내에 응답이 제공됩니다. 
>
> 라우팅 의도 및 정책을 구성하는 방법에 대한 자세한 내용은 다음 [문서](how-to-routing-policies.md)를 참조하세요.


이제 Azure Firewall 관리자를 사용하여 퍼블릭 및 프라이빗 트래픽에 대한 정책을 설정하는 고객은 라우팅 의도 및 라우팅 정책을 사용하여 훨씬 더 간단한 방법으로 네트워크를 설정할 수 있습니다.

라우팅 의도 및 라우팅 정책을 사용하면 Virtual WAN 허브가 인터넷 바인딩된 트래픽 및 프라이빗(지점 및 사이트 간, 사이트 간, ExpressRoute, Virtual WAN 허브 내의 네트워크 가상 어플라이언스, Virtual Network) 트래픽을 전달하는 방법을 지정할 수 있습니다. 라우팅 정책에는 인터넷 트래픽 정책 및 프라이빗 트래픽 라우팅 정책의 두 가지 유형이 있습니다. 각 Virtual WAN 허브에는 각각 다음 홉 리소스를 포함하는 최대 1개의 인터넷 트래픽 라우팅 정책과 1개의 프라이빗 트래픽 라우팅 정책이 있을 수 있습니다. 

프라이빗 트래픽은 분기 및 Virtual Network 주소 접두사를 모두 포함하지만, 라우팅 정책은 이러한 항목을 라우팅 의도 개념 내의 하나의 엔터티로 간주합니다.


* **인터넷 트래픽 라우팅 정책**: 인터넷 트래픽 라우팅 정책을 Virtual WAN 허브에 대해 구성하는 경우 모든 분기(사용자 VPN(지점 및 사이트 간 VPN), 사이트 간 VPN 및 ExpressRoute) 및 해당 Virtual WAN 허브에 대한 Virtual Network 연결을 통해 인터넷 바인딩된 트래픽을 라우팅 정책의 일부로 지정된 Azure Firewall 리소스 또는 타사 보안 공급자로 전달합니다.
 

* **프라이빗 트래픽 라우팅 정책**: 프라이빗 트래픽 라우팅 정책이 Virtual WAN 허브에 대해 구성된 경우 허브 간 트래픽을 포함하여 **모든** 분기 트래픽 및 Virtual WAN 허브 내부 및 외부의 Virtual Network 트래픽이 프라이빗 트래픽 라우팅 정책에 지정된 다음 홉 Azure Firewall 리소스로 전달됩니다. 

라우팅 의도 및 정책을 구성하는 방법에 대한 자세한 내용은 다음 [문서](how-to-routing-policies.md)를 참조하세요.

### <a name="connections"></a><a name="connection"></a>Connections

연결은 라우팅 구성이 있는 Resource Manager 리소스입니다. 네 가지 연결 유형은 다음과 같습니다.

* **VPN 연결**: VPN 사이트를 가상 허브 VPN Gateway에 연결합니다.
* **ExpressRoute 연결**:ExpressRoute 회로를 가상 허브 ExpressRoute 게이트웨이에 연결합니다.
* **P2S 구성 연결**: 사용자 VPN(지점 및 사이트 간) 구성을 가상 허브 사용자 VPN(지점 및 사이트 간) 게이트웨이에 연결합니다.
* **허브 가상 네트워크 연결**: 가상 네트워크를 가상 허브에 연결합니다.

설정하는 동안 가상 네트워크 연결에 대한 라우팅 구성을 설정할 수 있습니다. 기본적으로 모든 연결은 기본 경로 테이블에 연결되고 전파됩니다.

### <a name="association"></a><a name="association"></a>연결

각 연결은 하나의 경로 테이블에 연결됩니다. 경로 테이블에 연결하면 경로 테이블에 경로로 표시된 대상으로 트래픽을 보낼 수 있습니다. 연결의 라우팅 구성에 연결된 경로 테이블이 표시됩니다.  여러 연결을 동일한 경로 테이블에 연결할 수 있습니다. 모든 VPN, ExpressRoute 및 사용자 VPN 연결은 동일한(기본) 경로 테이블에 연결됩니다.

기본적으로 모든 연결은 가상 허브의 **기본 경로 테이블** 에 연결됩니다. 각 가상 허브에는 고정 경로를 추가하도록 편집할 수 있는 고유한 기본 경로 테이블이 있습니다. 고정적으로 추가된 경로는 동일한 접두사에 대해 동적으로 학습된 경로보다 우선합니다.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="연결"lightbox="./media/nat-rules-vpn-gateway/edit-site-bgp.png":::

### <a name="propagation"></a><a name="propagation"></a>전파

연결은 경로를 경로 테이블에 동적으로 전파합니다. VPN 연결, ExpressRoute 연결 또는 P2S 구성 연결을 사용하면 경로가 BGP를 사용하여 가상 허브에서 온-프레미스 라우터로 전파됩니다. 경로는 하나 이상의 경로 테이블에 전파될 수 있습니다.

각 가상 허브에 대해 **없음 경로 테이블** 도 사용할 수 있습니다. 없음 경로 테이블로 전파한다는 것은 연결에서 전파할 경로가 필요하지 않음을 의미합니다. VPN, ExpressRoute 및 사용자 VPN 연결은 동일한 경로 테이블 집합으로 경로를 전파합니다.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="전파":::

### <a name="labels"></a><a name="labels"></a>레이블

레이블은 경로 테이블을 논리적으로 그룹화하는 메커니즘을 제공합니다. 이는 연결에서 여러 경로 테이블로 경로를 전파하는 동안 특히 유용합니다. 예를 들어 **기본 경로 테이블** 에는 '기본'이라는 기본 제공 레이블이 있습니다. 사용자가 연결 경로를 '기본' 레이블로 전파하면 Virtual WAN의 모든 허브에 있는 모든 기본 경로 테이블에 자동으로 적용됩니다.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>가상 네트워크 연결에서 고정 경로 구성

고정 경로를 구성하면 가상 허브에 연결된 스포크 VNet에 프로비저닝된 NVA(네트워크 가상 어플라이언스)일 수 있는 다음 홉 IP를 통해 트래픽을 조정하는 메커니즘이 제공됩니다. 고정 경로는 경로 이름, 대상 접두사 목록 및 다음 홉 IP로 구성됩니다.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>기존 경로에 대한 경로 테이블

이제 경로 테이블은 연결 및 전파 기능을 제공합니다. 기존 경로 테이블은 이러한 기능이 없는 경로 테이블입니다. 허브 라우팅에 기존 경로를 사용 중이고 새 기능을 사용하려는 경우 다음 사항을 고려하세요.

* **가상 허브에서 기존 경로를 사용 중인 표준 Virtual WAN 고객**:

   Azure Portal의 허브에 대한 라우팅 섹션에 기존 경로가 있는 경우 먼저 해당 경로를 삭제한 다음, 새 경로 테이블을 만들어야 합니다(Azure Portal의 허브에 대한 경로 테이블 섹션에서 사용 가능).

* **가상 허브에서 기존 경로를 사용 중인 기본 Virtual WAN 고객**:

   Azure Portal의 허브에 대한 라우팅 섹션에 기존 경로가 있는 경우 먼저 해당 경로를 삭제한 다음, 기본 Virtual WAN을 표준 Virtual WAN으로 **업그레이드** 합니다. [가상 WAN을 기본에서 표준으로 업그레이드](upgrade-virtual-wan.md)를 참조하세요.

## <a name="hub-reset"></a><a name="reset"></a>허브 초기화

가상 허브 **초기화** 는 Azure Portal에서만 사용할 수 있습니다. 초기화는 경로 테이블, 허브 라우터 또는 가상 허브 리소스 자체와 같은 실패한 리소스를 올바른 프로비저닝 상태로 되돌릴 수 있는 방법을 제공합니다. Microsoft에 지원을 요청하기 전에 허브 초기화를 고려합니다. 이 작업은 가상 허브의 게이트웨이를 초기화하지 않습니다.

## <a name="additional-considerations"></a><a name="considerations"></a>기타 고려 사항

Virtual WAN 라우팅을 구성할 때 다음 사항을 고려합니다.

* 모든 분기 연결(지점 간, 사이트 간 및 ExpressRoute)은 기본 경로 테이블에 연결해야 합니다. 그렇게 하면 모든 분기가 동일한 접두사를 학습하게 됩니다.
* 모든 분기 연결은 동일한 경로 테이블 집합으로 경로를 전파해야 합니다. 예를 들어 분기가 기본 경로 테이블로 전파되어야 한다고 결정한 경우 이 구성은 모든 분기에서 일관되어야 합니다. 결과적으로 기본 경로 테이블과 연결된 모든 연결은 모든 분기에 연결할 수 있습니다.
* Azure Firewall을 통한 분기 간은 현재 지원되지 않습니다.
* 여러 지역에서 Azure Firewall을 사용하는 경우 모든 스포크 가상 네트워크는 동일한 경로 테이블에 연결되어야 합니다. 예를 들어 동일한 가상 허브에서 다른 VNet이 Azure Firewall을 바이패스하는 반면 Azure Firewall을 통과하는 VNet의 서브넷을 갖는 것은 불가능합니다.
* 단일 Virtual Network 연결에서 여러 개의 다음 홉 IP 주소를 지정할 수 있습니다. 그러나 Virtual Network 연결은 다음 홉 IP가 있는 경로 중 하나가 공용 IP 주소 또는 0.0.0.0/0(인터넷)으로 표시되는 스포크 Virtual Network 'if'에서 '동일한' 네트워크 가상 어플라이언스로의 '다중/고유' 다음 홉 IP를 지원하지 않습니다.
* 0\.0.0.0/0 경로와 관련된 모든 정보는 로컬 허브의 경로 테이블에만 국한됩니다. 이 경로는 허브 간에 전파되지 않습니다.
## <a name="next-steps"></a>다음 단계

* 라우팅을 구성하려면 [가상 허브 라우팅을 구성하는 방법](how-to-virtual-hub-routing.md)을 참조하세요.
* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
