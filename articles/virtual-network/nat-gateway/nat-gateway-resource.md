---
title: NAT 게이트웨이 리소스를 사용하여 가상 네트워크 설계
titleSuffix: Azure Virtual Network NAT
description: NAT 게이트웨이 리소스를 사용하여 가상 네트워크를 설계하는 방법을 알아봅니다.
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
ms.openlocfilehash: d0af0d38ef53e4ba90dcf0e7f4cbcc60c52b87a8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373140"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>NAT 게이트웨이 리소스를 사용하여 가상 네트워크 설계

NAT 게이트웨이 리소스는 [Virtual Network NAT](nat-overview.md)의 일부이며, 가상 네트워크에 있는 하나 이상의 서브넷에 대한 아웃바운드 인터넷 연결을 제공합니다. 가상 네트워크의 서브넷에는 사용할 NAT 게이트웨이가 명시됩니다. NAT는 서브넷에 대한 SNAT(Source Network Address Translation)를 제공합니다.  NAT 게이트웨이 리소스는 아웃바운드 흐름을 만들 때 가상 머신에서 사용하는 고정 IP 주소를 지정합니다. 고정 IP 주소는 공용 IP 주소 리소스(PIP), 공용 IP 접두사 리소스 또는 둘 다에서 제공됩니다. 공용 IP 접두사 리소스를 사용하는 경우 전체 공용 IP 접두사 리소스의 모든 IP 주소는 NAT 게이트웨이 리소스에서 사용됩니다. NAT 게이트웨이 리소스는 둘 중 하나에서 최대 16개의 고정 IP 주소를 사용할 수 있습니다.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="인터넷으로의 아웃바운드를 위한 Virtual Network NAT">
</p>

*그림: 인터넷으로의 아웃바운드를 위한 Virtual Network NAT*

## <a name="how-to-deploy-nat"></a>NAT를 배포하는 방법

NAT 게이트웨이를 구성하고 사용하는 작업은 의도적으로 간단합니다.  

NAT 게이트웨이 리소스:
- 지역 또는 영역 NAT 게이트웨이 리소스 만들기
- IP 주소를 할당합니다.
- 필요한 경우 TCP 유휴 시간 제한을 수정합니다(선택 사항).  기본값을 변경하기 <ins>전에</ins> [타이머](#timers)를 검토합니다.

가상 네트워크:
- NAT 게이트웨이를 사용하도록 가상 네트워크 서브넷을 구성합니다.

사용자 정의 경로는 필요하지 않습니다.


## <a name="design-guidance"></a>디자인 지침

이 섹션을 검토하여 NAT를 통해 가상 네트워크를 설계할 때 고려해야 하는 사항을 숙지합니다.  

### <a name="connecting-to-azure-services"></a>Azure 서비스에 연결

Azure 서비스에 연결 하는 경우 [개인 링크](../../private-link/private-link-overview.md)를 활용 하는 것이 좋습니다. 

개인 링크를 사용 하 여 Azure 리소스를 가상 네트워크에 연결 하 고 Azure 서비스 리소스에 대 한 액세스를 제어 합니다. 예를 들어 Azure storage에 액세스 하는 경우 저장소에 대 한 개인 끝점을 사용 하 여 연결이 완전히 개인 지 확인 합니다.

### <a name="connecting-to-the-internet"></a>인터넷에 연결

NAT는 공용 끝점에 연결 해야 하는 모든 프로덕션 워크 로드에 대 한 아웃 바운드 시나리오에 권장 됩니다. 다음 시나리오는 아웃 바운드에 대 한 NAT 게이트웨이와 인바운드의 공존을 보장 하는 방법의 예입니다.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>인스턴스 수준 공용 IP를 사용하는 NAT 및 VM

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="인스턴스 수준 공용 IP를 사용하는 Virtual Network NAT 및 VM">
</p>

*그림: 인스턴스 수준 공용 IP를 사용하는 Virtual Network NAT 및 VM*

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 인스턴스 수준 공용 IP를 사용하는 VM |
| 아웃바운드 | NAT 게이트웨이 |

VM은 NAT 게이트웨이를 아웃바운드에 사용합니다.  시작된 인바운드는 영향을 받지 않습니다.

#### <a name="nat-and-vm-with-standard-public-load-balancer"></a>표준 공용 Load Balancer를 사용 하는 NAT 및 VM

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="공용 Load Balancer를 사용하는 Virtual Network NAT 및 VM">
</p>

*그림: 공용 Load Balancer를 사용하는 Virtual Network NAT 및 VM*

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 공용 Load Balancer |
| 아웃바운드 | NAT 게이트웨이 |

부하 분산 규칙 또는 아웃바운드 규칙의 모든 아웃바운드 구성이 NAT 게이트웨이로 대체됩니다.  시작된 인바운드는 영향을 받지 않습니다.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-standard-public-load-balancer"></a>인스턴스 수준 공용 IP 및 표준 공용 Load Balancer를 사용 하는 NAT 및 VM

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="인스턴스 수준 공용 IP 및 공용 Load Balancer를 사용하는 Virtual Network NAT 및 VM">
</p>

*그림: 인스턴스 수준 공용 IP 및 공용 Load Balancer를 사용하는 Virtual Network NAT 및 VM*

| Direction | 리소스 |
|:---:|:---:|
| 인바운드 | 인스턴스 수준 공용 IP 및 공용 Load Balancer를 사용하는 VM |
| 아웃바운드 | NAT 게이트웨이 |

부하 분산 규칙 또는 아웃바운드 규칙의 모든 아웃바운드 구성이 NAT 게이트웨이로 대체됩니다.  또한 VM은 NAT 게이트웨이를 아웃바운드에 사용합니다.  시작된 인바운드는 영향을 받지 않습니다.

## <a name="performance"></a>성능

각 NAT 게이트웨이 리소스는 최대 50Gbps의 처리량을 제공할 수 있습니다. 배포를 여러 서브넷으로 분할하고 각 서브넷 또는 서브넷 그룹에 NAT 게이트웨이를 할당하여 스케일 아웃할 수 있습니다.

각 NAT 게이트웨이는 할당된 아웃 바운드 IP 주소 마다 각각 TCP 및 UDP에 대해 64000 흐름을 지원할 수 있습니다.  자세한 내용은 SNAT(Source Network Address Translation)에서 다음 섹션을 검토하고 특정 문제 해결 지침은 [문제 해결 문서](./troubleshoot-nat.md)를 검토합니다.

## <a name="source-network-address-translation"></a>SNAT(Source Network Address Translation)

SNAT는 다른 IP 주소에서 시작하도록 흐름의 원본을 다시 작성합니다.  NAT 게이트웨이 리소스는 일반적으로 PAT(Port Address Translation)라고 하는 SNAT 변형을 사용합니다. PAT는 원본 주소와 원본 포트를 다시 작성합니다. SNAT를 사용하는 경우 개인 주소 수와 변환된 해당 공용 주소 간에 고정된 관계가 없습니다.  

### <a name="fundamentals"></a>기본 사항

기본 개념을 설명하기 위해 4가지 흐름의 예를 살펴보겠습니다.  NAT 게이트웨이에서 공용 IP 주소 리소스 65.52.1.1를 사용 중 이며 VM이 65.52.0.1에 연결하는 중입니다.

| 흐름 | 원본 튜플 | 대상 튜플 |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

PAT가 수행되면 이러한 흐름은 다음과 같습니다.

| 흐름 | 원본 튜플 | SNAT로 변환된 원본 튜플 | 대상 튜플 | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

대상에는 할당된 포트가 표시된 흐름의 원본이 65.52.0.1(SNAT 원본 튜플)로 표시됩니다.  위의 표에 나와 있는 PAT를 포트 위장 SNAT라고도 합니다.  여러 프라이빗 원본이 IP 및 포트 뒤에 위장되어 있습니다.  

#### <a name="source-snat-port-reuse"></a>원본(SNAT) 포트 다시 사용

NAT 게이트웨이 대해 선택적으로 원본(SNAT) 포트를 사용합니다.  다음은이 개념을 이전 흐름 집합의 추가 흐름으로 보여 줍니다.  예제의 VM은 65.52.0.2에 대한 흐름입니다.

| 흐름 | 원본 튜플 | 대상 튜플 |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

NAT 게이트웨이는 흐름 4를 다른 대상에도 사용할 수 있는 포트로 변환할 가능성이 높습니다.  IP 주소 프로 비전의 올바른 크기 조정에 대한 자세한 내용은 [크기 조정](#scaling)을 참조하세요.

| 흐름 | 원본 튜플 | SNAT로 변환된 원본 튜플 | 대상 튜플 | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

원본 포트가 위 예제에서 할당된 특정 방식에 대한 종속성을 사용하지 않습니다.  위의 개념에서는 기본 개념만 보여 줍니다.

NAT에서 제공하는 SNAT는 여러 측면에서 [Load Balancer](../../load-balancer/load-balancer-outbound-connections.md)와 다릅니다.

### <a name="on-demand"></a>요청 시

NAT는 새로운 아웃바운드 트래픽 흐름을 위한 주문형 SNAT 포트를 제공합니다. 인벤토리에서 사용 가능한 모든 SNAT 포트는 NAT로 구성된 서브넷의 모든 가상 머신에서 사용됩니다. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT 주문형 아웃바운드 SNAT">
</p>

*그림: Virtual Network NAT 주문형 아웃바운드 SNAT*

가상 머신의 모든 IP 구성은 필요에 따라 주문형 아웃바운드 흐름을 만들 수 있습니다.  인스턴스당 최악의 경우 오버프로비저닝을 포함한 인스턴스 계획당 미리 할당은 필요하지 않습니다.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="소모 시나리오의 차이점">
</p>

*그림: 소모 시나리오의 차이점*

SNAT 포트가 해제되면 NAT로 구성된 서브넷의 모든 가상 머신에서 사용할 수 있습니다.  주문형 할당을 통해 서브넷의 동적 및 확산 워크로드에서 필요에 따라 SNAT 포트를 사용할 수 있습니다.  사용할 수 있는 SNAT 포트 인벤토리가 있으면 SNAT 흐름이 성공합니다. 대신 SNAT 포트 핫스폿은 더 큰 인벤토리에서 혜택을 받을 수 있습니다. SNAT 포트는 적극적으로 필요하지 않은 가상 머신에도 사용된 상태로 남아 있습니다.

### <a name="scaling"></a>확장

NAT 크기 조정은 주로 사용 가능한 공유 SNAT 포트 인벤토리를 관리하는 기능입니다. NAT는 NAT 게이트웨이 리소스에 연결된 모든 서브넷의 예상 최대 아웃바운드 흐름을 처리하기에 충분한 SNAT 포트 인벤토리가 필요합니다.  공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 다 사용하여 SNAT 포트 인벤토리를 만들 수 있습니다.  

>[!NOTE]
>공용 IP 접두사 리소스를 할당하는 경우 전체 공용 IP 접두사가 사용됩니다.  공용 IP 접두사 리소스를 할당한 다음, 개별 IP 주소를 분리하여 다른 리소스에 할당할 수 없습니다.  공용 IP 접두사에서 여러 리소스로 개별 IP 주소를 할당하려면 공용 IP 접두사 리소스에서 개별 공용 IP 주소를 만들고, 필요에 따라 공용 IP 접두사 리소스 자체 대신 해당 주소를 할당해야 합니다.

SNAT는 개인 주소를 하나 이상의 공용 IP 주소에 매핑하고, 프로세스의 원본 주소와 원본 포트를 다시 작성합니다. NAT 게이트웨이 리소스는 이 변환에 구성된 공용 IP 주소마다 64,000개의 포트(SNAT 포트)를 사용합니다. NAT 게이트웨이 리소스는 최대 16개의 IP 주소와 1,000,000개의 SNAT 포트로 확장할 수 있습니다. 공용 IP 접두사 리소스가 제공되는 경우 접두사 내의 각 IP 주소는 SNAT 포트 인벤토리를 제공합니다. 그리고 더 많은 공용 IP 주소를 추가하면 사용 가능한 인벤토리 SNAT 포트도 증가합니다. TCP와 UDP는 서로 관련이 없는 별도의 SNAT 포트 인벤토리입니다.

NAT 게이트웨이 리소스는 선택적으로 원본 포트를 다시 사용합니다. 크기 조정을 위한 디자인 지침과 같이 각 흐름에 새 SNAT 포트가 필요하다고 가정하고 아웃바운드 트래픽에 사용할 수 있는 총 IP 주소 수를 조정해야 합니다.  디자인하는 규모를 신중하게 고려하고 IP 주소 수량을 적절하게 프로비전해야 합니다.

여러 대상에 대한 SNAT 포트는 가능한 경우 다시 사용될 가능성이 높습니다. SNAT 포트 소모 방법으로 인해 흐름이 성공하지 못할 수 있습니다.  

예는 [SNAT 기본 사항](#source-network-address-translation)을 참조하세요.


### <a name="protocols"></a>프로토콜

NAT 게이트웨이 리소스는 UDP 및 TCP 흐름의 IP 및 IP 전송 헤더와 상호 작용하며 애플리케이션 계층 페이로드와 관련이 없습니다.  다른 IP 프로토콜은 지원되지 않습니다.

### <a name="timers"></a>타이머

>[!IMPORTANT]
>유휴 타이머가 길면 SNAT 고갈 가능성이 불필요하게 높아질 수 있습니다. 지정하는 타이머가 길수록 SNAT 포트가 최종적으로 유휴 시간 제한을 초과할 때까지 NAT는 SNAT 포트를 잡고 있게 됩니다. 흐름이 유휴 시간 제한을 초과하면 흐름이 결국 실패하고 SNAT 포트 인벤토리를 불필요하게 소모합니다.  2시간에 실패하는 흐름은 기본 4분에도 실패합니다. 유휴 시간 제한을 늘리는 방법은 가끔 사용해야 하는 최후의 수단입니다. 흐름이 유휴 상태로 전환되지 않으면 흐름이 유휴 타이머의 영향을 받지 않습니다.

TCP 유휴 시간 제한은 모든 흐름에 대해 4분(기본값)에서 120분(2시간)까지 조정할 수 있습니다.  또한 유휴 타이머를 흐름의 트래픽에 따라 다시 설정할 수 있습니다.  긴 유휴 연결 및 엔드포인트 활동성 검색을 새로 고치는 데 추천되는 패턴은 TCP keepalive입니다.  TCP keepalive는 엔드포인트에 중복된 ACK로 표시되며, 오버헤드가 낮고, 애플리케이션 계층에는 표시되지 않습니다.

SNAT 포트를 해제하는 데 사용되는 타이머는 다음과 같습니다.

| Timer | 값 |
|---|---|
| TCP FIN | 60초 |
| TCP RST | 10초 |
| TCP 절반 열기 | 30초 |

SNAT 포트는 5초 후에 동일한 대상 IP 주소 및 대상 포트에 다시 사용할 수 있습니다.

>[!NOTE] 
>이러한 타이머 설정은 변경될 수 있습니다. 이러한 값은 문제를 해결하는 데 도움을 주기 위해 제공되며, 현재는 특정 타이머를 사용하지 않아야 합니다.

## <a name="limitations"></a>제한 사항

- 기본 Load Balancer 및 기본 공용 IP 주소는 NAT와 호환 되지 않습니다. 대신 표준 SKU 부하 분산 장치 및 공용 Ip를 사용 합니다.
- IP 조각화는 NAT 게이트웨이를 통해 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [가상 네트워크 NAT](nat-overview.md)에 대해 알아봅니다.
* [NAT 게이트웨이 리소스에 대한 메트릭 및 경고](nat-metrics.md)에 대해 알아봅니다.
* [NAT 게이트웨이 리소스 문제 해결](troubleshoot-nat.md)에 대해 알아봅니다.
