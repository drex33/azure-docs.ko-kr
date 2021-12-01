---
title: NAT 게이트웨이 리소스를 사용하는 가상 네트워크 디자인
titleSuffix: Azure Virtual Network NAT
description: NAT(Network Address Translation) 게이트웨이 리소스를 사용하는 가상 네트워크를 디자인하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: 0bcb5f248f2ecda187c7f20f9a543e30feac6eaa
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133425242"
---
# <a name="design-virtual-networks-that-use-nat-gateway-resources"></a>NAT 게이트웨이 리소스를 사용하는 가상 네트워크 디자인

NAT(Network Address Translation) 게이트웨이 리소스는 가상 네트워크의 하나 이상의 서브넷에 대한 아웃바운드 인터넷 연결을 제공합니다. 가상 네트워크의 서브넷은 가상 네트워크에서 사용하는 NAT 게이트웨이를 명시합니다. [Azure Virtual Network NAT](nat-overview.md)NAT 게이트웨이 리소스를 디자인하고 배포할 수 있습니다.

NAT는 서브넷에 대한 SNAT(원본 네트워크 주소 변환)를 제공합니다. NAT 게이트웨이 리소스는 VM이 아웃바운드 흐름을 만들 때 사용하는 고정 IP 주소를 지정합니다. 고정 IP 주소는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 다에서 제공됩니다. NAT 게이트웨이 리소스가 공용 IP 접두사 리소스를 사용하는 경우 전체 공용 IP 접두사 리소스의 모든 IP 주소는 NAT 게이트웨이 리소스에서 사용됩니다. NAT 게이트웨이 리소스는 두 유형의 공용 IP 리소스에서 최대 16개의 고정 IP 주소를 사용할 수 있습니다.

다음 그림에서는 인터넷에 아웃바운드되는 흐름에 대한 Virtual Network NAT 설명합니다.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Diagram that depicts a NAT gateway resource that consumes all I P addresses for a public I P prefix and directs that traffic to and from two subnets of V Ms and a virtual machine scale set." width="256" title="인터넷에 아웃바운드되는 흐름에 대한 Virtual Network NAT">
</p>

## <a name="nat-deployment"></a>NAT 배포

몇 가지 단계에서 NAT 게이트웨이를 설정하고 사용할 수 있습니다.

1. NAT 게이트웨이 리소스 설정:

   1. 지역 또는 영역 NAT 게이트웨이 리소스를 만듭니다.
   1. IP 주소를 할당합니다.
   1. (선택 사항) 필요한 경우 TCP 유휴 시간 초과를 수정합니다. TCP 유휴 시간 초과를 기본값에서 *변경하기 전에* [타이머를 검토합니다.](#timers)

1. 가상 네트워크 설정:

   - NAT 게이트웨이를 사용하도록 가상 네트워크 서브넷을 구성합니다.

NAT 게이트웨이에 대한 사용자 정의 경로를 만들 필요가 없습니다.

## <a name="design-guidance"></a>디자인 지침

NAT 게이트웨이 리소스를 사용하도록 가상 네트워크를 디자인하기 시작할 때 고려해야 할 요소는 다음 섹션을 검토하세요.

### <a name="connect-to-azure-services"></a>Azure 서비스에 연결

[Azure Private Link](../../private-link/private-link-overview.md) 사용하여 Azure 서비스에 연결하는 것이 좋습니다. Private Link Azure 리소스를 가상 네트워크에 연결합니다.

Private Link Azure 서비스 리소스에 대한 액세스를 관리하는 데도 도움이 됩니다. 예를 들어 Azure Storage 액세스할 때 프라이빗 엔드포인트를 사용하여 연결이 프라이빗인지 확인합니다.

### <a name="connect-to-the-internet"></a>인터넷에 커넥트

퍼블릭 엔드포인트에 연결하는 모든 프로덕션 워크로드에 대해 아웃바운드 시나리오에서 NAT를 사용하는 것이 좋습니다. 다음 시나리오는 아웃바운드 인터넷에 NAT 게이트웨이를 사용할 때 인바운드 인터넷의 공존을 보장하는 방법의 예입니다.

## <a name="nat-and-vm-scenarios"></a>NAT 및 VM 시나리오

다음 섹션에서는 서브넷에서 NAT 및 VM을 사용하도록 설정할 수 있는 시나리오에 대해 설명합니다.

### <a name="instance-level-public-ip"></a>인스턴스 수준 공용 IP

다음 그림에서는 인스턴스 수준 공용 IP를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Diagram that depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public I P." width="300" title="인스턴스 수준 공용 IP를 Virtual Network NAT 및 VM">
</p>

다음 표에서는 인스턴스 수준 공용 IP를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 인스턴스 수준 공용 IP가 있는 VM |
| 아웃바운드 | NAT 게이트웨이 |

VM은 아웃바운드에 NAT 게이트웨이를 사용합니다. 시작된 인바운드는 영향을 받지 않습니다.

### <a name="standard-public-load-balancer"></a>표준 퍼블릭 부하 분산 장치

다음 그림에서는 표준 공용 부하 분산기를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Diagram that depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="표준 공용 부하 분산 Virtual Network NAT 및 VM">
</p>

다음 표에서는 표준 공용 부하 분산기를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 공용 부하 분산이 있는 VM |
| 아웃바운드 | NAT 게이트웨이 |

NAT 게이트웨이는 부하 분산 규칙 또는 아웃바운드 규칙에서 아웃바운드 구성을 대체합니다. 시작된 인바운드는 영향을 받지 않습니다.

#### <a name="instance-level-public-ip-and-standard-public-load-balancer"></a>인스턴스 수준 공용 IP 및 표준 공용 부하 분산

다음 그림에서는 인스턴스 수준 공용 IP 및 표준 공용 부하 분산기를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Diagram that depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public I P and a public load balancer." width="425" title="인스턴스 수준 공용 IP 및 표준 공용 부하 분산 Virtual Network NAT 및 VM">
</p>

다음 표에서는 인스턴스 수준 공용 IP 및 표준 공용 부하 분산기를 사용하는 VM에서 NAT를 사용하는 것을 설명합니다.

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 인스턴스 수준 공용 IP 및 공용 부하 분산이 있는 VM |
| 아웃바운드 | NAT 게이트웨이 |

NAT 게이트웨이는 부하 분산 규칙 또는 아웃바운드 규칙에서 아웃바운드 구성을 대체합니다. 또한 VM은 아웃바운드에 NAT 게이트웨이를 사용합니다. 시작된 인바운드는 영향을 받지 않습니다.

## <a name="performance"></a>성능

각 NAT 게이트웨이 리소스는 최대 50Gbps의 처리량을 제공할 수 있습니다. 배포를 여러 서브넷으로 분할하고 각 서브넷 또는 서브넷 그룹에 NAT 게이트웨이를 할당하여 확장할 수 있습니다.

각 NAT 게이트웨이는 할당된 아웃바운드 IP 주소당 TCP 및 UDP에 대해 각각 64,000개의 흐름을 지원할 수 있습니다. 자세한 내용은 [SNAT를 참조하세요.](#snat) 특정 문제를 해결하는 지침은 [Azure Virtual Network NAT 연결 문제 해결을 참조하세요.](./troubleshoot-nat.md)

## <a name="snat"></a>SNAT

SNAT는 흐름이 다른 IP 주소에서 시작되도록 흐름의 원본을 다시 작성합니다. NAT 게이트웨이 리소스는 일반적으로 *PAT(포트 주소 변환)라고* 하는 SNAT의 변형을 사용합니다. PAT는 원본 주소와 원본 포트를 다시 작성합니다. SNAT에서는 개인 주소 수와 번역된 공용 주소 간에 고정된 관계가 없습니다.

<a id="source-network-address-translation"></a>

### <a name="fundamentals"></a>기본 사항

네 가지 흐름의 예를 살펴보겠습니다. 다음 표에서는 NAT 게이트웨이가 공용 IP 주소 리소스 65.52.1.1을 사용하고 VM이 65.52.0.1에 연결되는 예제를 보여 줍니다.

| 흐름 | 원본 튜플 | 대상 튜플 |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

다음 표에서는 PAT 이후의 흐름 모양을 보여 줍니다.

| 흐름 | 원본 튜플 | SNAT 이후 소스 튜플 | 대상 튜플 |
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

대상은 테이블에 표시된 할당된 포트와 함께 흐름의 원본을 65.52.0.1(SNAT 이후의 원본 튜플)로 표시합니다. 표에 표시된 PAT를 포트 *가장 SNAT* 라고도 부립니다. 여러 프라이빗 원본이 IP 및 포트 뒤에 위장되어 있습니다.

#### <a name="source-snat-port-reuse"></a>원본(SNAT) 포트 재사용

NAT 게이트웨이 대해 선택적으로 원본(SNAT) 포트를 사용합니다. 다음 표에서는 앞의 예제에서 사용된 흐름 집합에 흐름을 추가하여 이 개념을 보여 줍니다. 예제의 VM은 65.52.0.2에 대한 흐름입니다.

| 흐름 | 원본 튜플 | 대상 튜플 |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

NAT 게이트웨이는 다음 표에 표시된 것처럼 흐름 4를 다른 대상에도 사용할 수 있는 포트로 변환할 수 있습니다. IP 주소 프로비전 크기를 올바르게 조정하는 방법에 대한 자세한 내용은 [크기 조정을 참조하세요.](#scaling)

| 흐름 | 원본 튜플 | SNAT 이후 소스 튜플 | 대상 튜플 |
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

예제의 원본 포트는 예제에만 해당합니다. 사용해야 하는 설정 값을 나타내지 않습니다.

NAT에서 제공하는 SNAT는 여러 측면에서 [부하 분산기에서](../../load-balancer/load-balancer-outbound-connections.md) 제공하는 SNAT와 다릅니다.

### <a name="on-demand"></a>요청 시

NAT는 새로운 아웃바운드 트래픽 흐름을 위한 주문형 SNAT 포트를 제공합니다. NAT를 사용하도록 구성된 서브넷의 VM은 인벤토리에서 사용할 수 있는 모든 SNAT 포트를 사용합니다.

다음 그림에서는 NAT를 사용하도록 구성된 서브넷의 모든 VM에서 사용하는 사용 가능한 모든 SNAT 포트의 인벤토리를 설명합니다.

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Diagram that depicts the inventory of all available SNAT ports used by any V M on subnets configured with NAT." width="550" title="아웃바운드 SNAT에 대한 주문형 Virtual Network NAT">
</p>

VM의 모든 IP 구성은 필요에 따라 주문형 아웃바운드 흐름을 만들 수 있습니다. 인스턴스별 최악의 경우 초과 프로비저닝을 포함하여 사전 할당 또는 인스턴스별 계획을 고려할 필요가 없습니다.

다음 그림에서는 고갈 임계값으로 NAT를 사용하도록 구성된 서브넷의 모든 VM에서 사용하는 사용 가능한 모든 SNAT 포트의 인벤토리를 설명합니다.

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Diagram that depicts the inventory of all available SNAT ports used by any V M on subnets configured with NAT with an exhaustion threshold." width="550" title="소모 시나리오의 차이점">
</p>

SNAT 포트가 릴리스되면 NAT를 사용하도록 구성된 서브넷의 모든 VM에서 사용할 수 있습니다. 주문형 할당을 사용하면 서브넷의 동적 및 분기 워크로드가 필요에 따라 SNAT 포트를 사용할 수 있습니다. SNAT 포트 인벤토리를 사용할 수 있는 경우 SNAT 흐름이 성공합니다. SNAT 포트 핫스폿은 더 큰 인벤토리를 활용합니다. SNAT 포트는 적극적으로 필요하지 않은 VM에 사용되지 않습니다.

### <a name="scaling"></a>확장

NAT 크기 조정은 주로 사용 가능한 공유 SNAT 포트 인벤토리를 관리하는 기능입니다. NAT는 NAT 게이트웨이 리소스에 연결된 모든 서브넷에 대해 예상되는 최대 아웃바운드 흐름에 충분한 SNAT 포트 인벤토리가 필요합니다. 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 다 사용하여 SNAT 포트 인벤토리를 만들 수 있습니다. 

> [!NOTE]
> 공용 IP 접두사 리소스를 할당하는 경우 전체 공용 IP 접두사 가 사용됩니다. 공용 IP 접두사 리소스를 할당한 다음, 개별 IP 주소를 분리하여 다른 리소스에 할당할 수 없습니다. 공용 IP 접두사에서 여러 리소스에 개별 IP 주소를 할당하려면 공용 IP 접두사 리소스에서 개별 공용 IP 주소를 만들고 공용 IP 접두사 리소스 자체를 할당하는 대신 필요에 따라 할당합니다.

SNAT는 개인 주소를 하나 이상의 공용 IP 주소에 매핑합니다. 매핑 프로세스 중에 SNAT는 원본 주소와 원본 포트를 다시 작성합니다. NAT 게이트웨이 리소스는 이 변환에 대해 구성된 공용 IP 주소당 64,000개 포트(SNAT 포트)를 사용합니다. NAT 게이트웨이 리소스는 최대 16개의 IP 주소와 1백만 개의 SNAT 포트까지 확장할 수 있습니다. 공용 IP 접두사 리소스가 제공되는 경우 접두사 내의 각 IP 주소는 SNAT 포트 인벤토리를 제공합니다. 공용 IP 주소를 더 추가하면 사용 가능한 인벤토리 SNAT 포트가 증가합니다. TCP 및 UDP는 별도의 SNAT 포트 인벤토리이며 NAT 게이트웨이 리소스와 관련이 없습니다.

NAT 게이트웨이 리소스는 선택적으로 원본 포트를 다시 사용합니다. 크기 조정을 디자인할 때 각 흐름에 새 SNAT 포트가 필요하다고 가정한 다음 아웃바운드 트래픽에 사용할 수 있는 총 IP 주소 수를 조정합니다. 디자인할 규모를 신중하게 고려한 다음, 그에 따라 IP 주소 수량을 프로비전합니다.

가능한 경우 다른 대상으로 설정된 SNAT 포트가 다시 사용될 가능성이 높습니다. SNAT 포트 소모가 가까워지면 흐름이 성공하지 못할 수 있습니다.

SNAT 예제는 [SNAT 기본 사항 을 참조하세요.](#source-network-address-translation)

### <a name="protocols"></a>프로토콜

NAT 게이트웨이 리소스는 UDP 및 TCP 흐름의 IP 및 IP 전송 헤더와 상호 작용합니다. NAT 게이트웨이 리소스는 애플리케이션 계층 페이로드에 독립적입니다. 다른 IP 프로토콜은 지원되지 않습니다.

### <a name="timers"></a>타이머

> [!IMPORTANT]
> 유휴 타이머가 길면 SNAT 소모 가능성이 불필요하게 증가할 수 있습니다. 타이머를 설정할수록 NAT가 SNAT 포트에 더 오래 유지되기 때문에 결국 유휴 시간제한이 될 때까지 유지합니다. 흐름 시간이 유휴 상태이면 결국 실패하고 불필요하게 SNAT 포트 인벤토리를 소비합니다. 2시간에 실패한 흐름도 기본 4분에서 실패합니다. 유휴 시간 제한 늘리기는 드물게 사용해야 하는 최후의 수단 옵션입니다. 흐름이 유휴 상태가 되지 않으면 유휴 타이머의 영향을 받지 않습니다.

모든 흐름의 경우 TCP 유휴 시간 제한은 4분(기본값)에서 120분(2시간)으로 조정할 수 있습니다. 흐름의 트래픽을 통해 유휴 타이머를 다시 설정할 수도 있습니다. 긴 유휴 연결 및 엔드포인트 활동성 검색을 새로 고치는 데 권장되는 패턴은 TCP keepalives를 사용하는 것입니다. TCP keepalives는 엔드포인트에 대한 중복 승인(ACK)으로 표시되고, 오버헤드가 낮으며, 애플리케이션 계층에 표시되지 않습니다.

다음 표에 나열된 타이머는 SNAT 포트 릴리스에 사용됩니다.

| Timer | 값 |
|---|---|
| TCP FIN | 60초 |
| TCP RST | 10초 |
| TCP 절반 열기 | 30초 |

SNAT 포트는 5초 후에 동일한 대상 IP 주소 및 대상 포트에 다시 사용할 수 있습니다.

> [!NOTE]
> 앞의 표에 설명된 타이머 설정은 변경될 수 있습니다. 문제를 해결 하는 데 도움이 되는 값이 제공 되며 예를 들어 있습니다.

## <a name="limitations"></a>제한 사항

NAT 게이트웨이 리소스에는 다음과 같은 제한 사항이 있습니다.

- 기본 부하 분산 장치 및 기본 공용 IP 주소는 NAT와 호환 되지 않습니다. 대신 표준 SKU 부하 분산 장치 및 공용 Ip를 사용 합니다.
- NAT 게이트웨이를 사용 하는 경우 IP 조각화를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 NAT](nat-overview.md)를 검토 합니다.
- [NAT 게이트웨이 리소스에 대한 메트릭 및 경고](nat-metrics.md)에 대해 알아봅니다.
- [NAT 게이트웨이 리소스 문제를 해결](troubleshoot-nat.md)하는 방법을 알아봅니다.
