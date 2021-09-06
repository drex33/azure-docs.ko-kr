---
title: 아웃바운드 규칙 Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer를 사용하여 인터넷 트래픽 송신을 제어하기 위한 아웃바운드 규칙을 구성하는 방법을 설명합니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: fd364d07202a486bfa47c513af601aeefe595bb5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437795"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>아웃바운드 규칙 Azure Load Balancer

아웃바운드 규칙을 사용하여 퍼블릭 부하 분산 장치에 대한 SNAT(Source Network Address Translation)를 명시적으로 정의할 수 있습니다. 이 구성을 사용하면 부하 분산 장치의 공용 IP를 사용하여 백 엔드 인스턴스에 대한 아웃바운드 인터넷 연결을 제공할 수 있습니다.

이 구성을 사용하면 다음을 수행할 수 있습니다.

* IP 위장
* 허용 목록을 단순화합니다.
* 배포할 공용 IP 리소스의 수 줄이기

아웃바운드 규칙을 사용하여 아웃바운드 인터넷 연결을 완전하게 제어할 수 있습니다. 아웃바운드 규칙을 사용하면 이 기능을 스케일링하고 특정 요구에 맞게 조정할 수 있습니다. 

아웃바운드 규칙은 백 엔드 VM에 ILPIP(인스턴스 수준 공용 IP 주소)가 없는 경우에만 준수됩니다.

![Load Balancer 아웃바운드 규칙](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

아웃바운드 규칙을 사용하여 아웃바운드 **SNAT** 동작을 명시적으로 정의할 수 있습니다.

아웃바운드 규칙을 통해 제어할 수 있는 항목은 다음과 같습니다.

* **특정 공용 IP 주소로 변환되는 가상 머신**
     * 두 가지 규칙은 백 엔드 풀 1이 파란색 IP 주소 1과 2를 사용하고 백 엔드 풀 2가 노란색 IP 접두사를 사용한다는 것입니다.
* **아웃바운드 SNAT 포트를 할당하는 방법**
     * 백 엔드 풀 2가 아웃바운드 연결을 만드는 유일한 풀인 경우 백 엔드 풀 2에 모든 SNAT 포트를 제공하고 백 엔드 풀 1에는 아무 포트도 제공하지 않도록 합니다.
* **아웃바운드 변환을 제공하는 프로토콜**
     * 백 엔드 풀 2에 아웃바운드를 위한 UDP 포트가 필요하고 백 엔드 풀 1에 TCP가 필요한 경우 TCP 포트를 1에, UDP 포트를 2에 지정합니다.
* **아웃바운드 연결 유휴 시간 제한에 사용할 기간(4-120분)**
     * 연결 유지가 적용된 장기 실행 연결이 있는 경우 최대 120분 동안 장기 실행 연결을 위해 유휴 포트를 예약합니다. 새 연결을 위해 오래된 연결이 중단되고 4분 후에 포트를 해제한다고 가정합니다. 
* **유휴 시간 제한 시 TCP 다시 설정을 보낼지 여부**
     * 유휴 연결의 시간을 초과하는 경우 흐름이 중단된 것을 알 수 있도록 TCP RST를 클라이언트 및 서버로 보내나요?

>[!Important]
> IP 주소로 구성된 백엔드 풀은 기본 아웃바운드가 사용되는 기본 부하 분산 장치처럼 동작합니다. 기본값으로 안전한 구성 및 아웃바운드 요구 사항이 까다로운 애플리케이션의 경우 NIC별로 백엔드 풀을 구성합니다.

## <a name="outbound-rule-definition"></a>아웃바운드 규칙 정의

아웃바운드 규칙은 부하 분산 및 인바운드 NAT 규칙 **프런트 엔드** + **매개 변수** + **백 엔드 풀** 과 동일한 친숙한 구문을 따릅니다. 

아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_ 에 대한 아웃바운드 NAT를 _프런트 엔드_ 로 변환하도록 구성합니다.  

_매개 변수_ 는 아웃바운드 NAT 알고리즘에 대해 세분화된 제어를 추가로 제공합니다.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 여러 IP 주소를 사용하여 아웃바운드 NAT 확장

프런트 엔드에서 제공하는 추가 IP 주소 각각은 SNAT 포트로 사용할 부하 분산 장치에 대해 추가적인 64,000개의 사용 후 삭제 포트를 제공합니다. 

여러 IP 주소를 사용하여 대규모 시나리오에 대한 계획을 세웁니다. 아웃바운드 규칙을 사용하여 [SNAT 소모](troubleshoot-outbound-connection.md#snatexhaust)를 완화합니다. 

또한 아웃바운드 규칙에 [공용 IP 접두사](./load-balancer-outbound-connections.md#outboundrules)를 직접 사용할 수 있습니다. 

공용 IP 접두사는 배포 스케일링을 늘립니다. Azure 리소스에서 시작하는 흐름의 허용 목록에 접두사를 추가할 수 있습니다. 공용 IP 주소 접두사를 참조하도록 부하 분산 장치 내에 프런트 엔드 IP 구성을 구성할 수 있습니다.  

부하 분산 장치는 공용 IP 접두사를 제어합니다. 이렇게 하면 아웃바운드 연결에 대한 공용 IP 접두사에 포함된 모든 공용 IP 주소가 아웃바운드 규칙에 자동으로 사용됩니다. 

공용 IP 접두사 내의 각 IP 주소는 부하 분산 장치에서 SNAT 포트로 사용할 IP 주소당 64,000개의 사용 후 삭제 포트를 추가로 제공합니다.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 아웃바운드 흐름 유휴 시간 제한 및 TCP 다시 설정

아웃바운드 규칙은 아웃바운드 흐름 유휴 시간 제한을 제어하고 이를 애플리케이션의 요구와 일치시키는 구성 매개 변수를 제공합니다. 아웃바운드 유휴 시간 제한은 기본적으로 4분입니다. 자세한 내용은 [유휴 시간 제한 구성](load-balancer-tcp-idle-timeout.md)을 참조하세요. 

부하 분산 장치의 기본 동작은 아웃바운드 유휴 시간 제한에 도달하는 경우 흐름을 자동으로 삭제하는 것입니다. `enableTCPReset` 매개 변수를 사용하면 예측 가능한 애플리케이션 동작 및 컨트롤을 사용할 수 있습니다. 이 매개 변수는 아웃바운드 유휴 시간 제한의 제한 시간에 양방향 TCP 다시 설정(TCP RST)을 보낼지 여부를 결정합니다. 

지역 가용성을 포함한 자세한 내용은 [유휴 시간 제한 시 TCP 재설정](./load-balancer-tcp-reset.md)을 검토하세요.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>아웃바운드 연결을 명시적으로 보호 및 제어

부하 분산 규칙은 자동 아웃바운드 NAT 프로그래밍을 제공합니다. 일부 시나리오에서는 부하 분산 규칙을 통해 자동 아웃바운드 NAT 프로그래밍을 사용하지 않도록 설정하거나 이렇게 설정해야 합니다. 규칙을 통해 사용하지 않도록 설정하면 동작을 제어하거나 구체화할 수 있습니다.  

이 매개 변수는 다음 두 가지 방법으로 사용할 수 있습니다.

1. 아웃바운드 SNAT에 대한 인바운드 IP 주소 방지. 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정합니다.
  
2. 인바운드 및 아웃바운드에 동시에 사용되는 IP 주소의 아웃바운드 **SNAT** 매개 변수를 튜닝합니다. 아웃바운드 규칙을 제어하려면 자동 아웃바운드 NAT를 사용하지 않도록 설정해야 합니다. 인바운드에도 사용되는 주소의 SNAT 포트 할당을 변경하려면 `disableOutboundSnat` 매개 변수를 true로 설정해야 합니다. 

인바운드에 사용되는 IP 주소를 다시 정의하려고 하면 아웃바운드 규칙을 구성하는 작업이 실패합니다.  먼저 부하 분산 규칙의 아웃바운드 NAT를 사용하지 않도록 설정합니다.

>[!IMPORTANT]
> 이 매개 변수를 true로 설정하고 아웃바운드 연결을 정의하는 아웃바운드 규칙이 없는 경우 가상 머신에는 아웃바운드 연결이 없습니다.  VM 또는 애플리케이션의 일부 작업은 아웃바운드 연결을 사용할 수 있는지에 따라 달라질 수 있습니다. 시나리오의 종속성을 이해하고 이러한 변경의 영향을 고려해야 합니다.

VM이 아웃바운드 흐름을 만들도록 하는 것이 바람직하지 않은 경우가 있습니다. 아웃바운드 흐름을 수신하는 대상 또는 인바운드 흐름을 시작하는 대상을 관리하기 위한 요구 사항이 있을 수도 있습니다. [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 사용하여 VM에서 도달할 수 있는 대상을 관리합니다. NSG를 사용하여 인바운드 흐름을 시작하는 퍼블릭 대상을 관리합니다.

부하 분산 VM에 NSG를 적용할 때 [서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags) 및 [기본 보안 규칙](../virtual-network/network-security-groups-overview.md#default-security-rules)에 주의해야 합니다. 

VM에서 Azure Load Balancer의 상태 프로브 요청을 수신할 수 있는지 확인합니다.

NSG가 AZURE_LOADBALANCER 기본 태그의 상태 프로브 요청을 차단할 경우 VM 상태 프로브에 실패하고 VM이 사용 불가로 표시됩니다. 부하 분산 장치는 해당 VM에 새 흐름을 보내는 작업을 중지합니다.

## <a name="scenarios-with-outbound-rules"></a>아웃바운드 규칙을 사용하는 시나리오
        

### <a name="outbound-rules-scenarios"></a>아웃바운드 규칙 시나리오


* 특정 공용 IP 또는 접두사 세트에 대한 아웃바운드 연결 구성
* [SNAT](load-balancer-outbound-connections.md) 포트 할당을 수정합니다.
* 아웃바운드만 사용하도록 설정합니다.
* VM 전용 아웃바운드 NAT(인바운드 없음).
* 내부 표준 부하 분산 장치에 대한 아웃바운드 NAT.
* 퍼블릭 표준 부하 분산 장치를 통해 아웃바운드 NAT에 TCP 및 UDP 프로토콜을 모두 사용하도록 설정합니다.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>시나리오 1: 특정 공용 IP 또는 접두사 세트에 대한 아웃바운드 연결 구성


#### <a name="details"></a>세부 정보


이 시나리오를 사용하여 퍼블릭 IP 주소 세트에서 시작되는 아웃바운드 연결을 조정할 수 있습니다. 시작 위치에 따라 허용 또는 차단 목록에 공용 IP 또는 접두사를 추가합니다.


이 공용 IP 또는 접두사는 부하 분산 규칙에서 사용하는 것과 동일할 수 있습니다. 


부하 분산 규칙에서 사용하는 것과 다른 공용 IP 또는 접두사를 사용하려면 


1. 공용 IP 접두사 또는 공용 IP 주소를 만듭니다.
2. 퍼블릭 표준 부하 분산 장치 만들기 
3. 사용할 공용 IP 접두사 또는 공용 IP 주소를 참조하는 프런트 엔드를 만듭니다. 
4. 백 엔드 풀을 다시 사용하거나 새로 만들고, 퍼블릭 부하 분산 장치의 백 엔드 풀에 VM을 배치합니다.
5. 프런트 엔드를 사용하여 이러한 VM의 아웃바운드 NAT를 사용으로 설정하도록 퍼블릭 부하 분산 장치에 대해 아웃바운드 규칙을 구성합니다 아웃바운드에 대해 부하 분산 규칙을 사용하는 것은 권장되지 않으며, 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정합니다.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>시나리오 2: [SNAT](load-balancer-outbound-connections.md) 포트 할당 수정


#### <a name="details"></a>세부 정보


아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝할 수 있습니다. 


SNAT이 고갈되는 경우 기본값 1024에서 제공되는 [SNAT](load-balancer-outbound-connections.md) 포트 수를 늘립니다. 


각 공용 IP 주소는 최대 64,000개의 사용 후 삭제 포트를 제공합니다. 백 엔드 풀의 VM 수는 각 VM에 배포되는 포트 수를 결정합니다. 백 엔드 풀의 하나의 VM은 최대 64,000개 포트에 액세스할 수 있습니다. 두 개의 VM의 경우 아웃바운드 규칙을 통해 최대 32,000개의 [SNAT](load-balancer-outbound-connections.md) 포트를 지정할 수 있습니다(2 x 32,000 = 64,000). 


아웃바운드 규칙을 사용하여 기본적으로 제공되는 SNAT 포트를 튜닝할 수 있습니다. 기본 [SNAT](load-balancer-outbound-connections.md) 포트 할당에 제공되는 것보다 더 많거나 더 작은 값을 제공합니다. 아웃바운드 규칙 프런트 엔드의 각 공용 IP 주소는 [SNAT](load-balancer-outbound-connections.md) 포트로 사용할 수 있도록 최대 64,000개의 단기 포트를 제공합니다. 


부하 분산 장치는 [SNAT](load-balancer-outbound-connections.md) 포트를 8배로 제공합니다. 8로 나눌 수 없는 값을 제공하면 구성 작업이 거부됩니다. 각 부하 분산 규칙 및 인바운드 NAT 규칙은 8개 포트의 범위를 사용합니다. 부하 분산 또는 인바운드 NAT 규칙이 다른 규칙과 동일한 8개 범위를 공유하는 경우 추가 포트가 사용되지 않습니다.


공용 IP 주소 수에 기반하여 사용할 수 있는 것보다 많은 [SNAT](load-balancer-outbound-connections.md) 포트를 제공하려고 하는 경우 구성 작업이 거부됩니다. 예를 들어, VM당 10,000개의 포트를 제공하고 백 엔드 풀의 7개 VM에서 단일 공용 IP 주소를 공유하는 경우 구성이 거부됩니다. 7x10,000개는 64,000개의 포트 제한을 초과합니다. 아웃바운드 규칙의 프런트 엔드에 더 많은 공용 IP 주소를 추가하여 시나리오를 사용하도록 설정합니다. 


포트 수로 0을 지정하여 [기본 포트 할당](load-balancer-outbound-connections.md#preallocatedports)으로 되돌립니다. 처음 50개 VM 인스턴스는 1024개 포트를 가져오고, 51-100개 VM 인스턴스는 최대 인스턴스 수까지 512개를 가져옵니다. 기본 SNAT 포트 할당에 대한 자세한 내용은 [SNAT 포트 할당 테이블](./load-balancer-outbound-connections.md#preallocatedports)을 참조하세요.


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>시나리오 3: 아웃바운드만 사용


#### <a name="details"></a>세부 정보


퍼블릭 표준 부하 분산 장치를 사용하여 VM 그룹에 아웃바운드 NAT를 제공합니다. 이 시나리오에서는 추가 규칙을 구성하지 않고 아웃바운드 규칙 자체만 사용합니다.


> [!NOTE]
> **Azure Virtual Network NAT** 은 부하 분산 장치 없이도 가상 머신에 대한 아웃바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>시나리오 4: VM 전용 아웃바운드 NAT(인바운드 없음)


> [!NOTE]
> **Azure Virtual Network NAT** 은 부하 분산 장치 없이도 가상 머신에 대한 아웃바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

#### <a name="details"></a>세부 정보


이 시나리오에서 Azure Load Balancer 아웃바운드 규칙 및 Virtual Network NAT는 가상 네트워크에서 송신에 사용할 수 있는 옵션입니다.


1. 공용 IP 또는 접두사를 만듭니다.
2. 퍼블릭 표준 부하 분산 장치를 만듭니다. 
3. 아웃바운드에 전용으로 사용되는 공용 IP 또는 접두사와 연결된 프런트 엔드를 만듭니다.
4. VM에 대한 백 엔드 풀을 만듭니다.
5. VM을 백 엔드 풀에 저장합니다.
6. 아웃바운드 NAT를 사용하도록 아웃바운드 규칙을 구성합니다.



접두사 또는 공용 IP를 사용하여 [SNAT](load-balancer-outbound-connections.md) 포트 크기를 조정합니다. 허용 또는 차단 목록에 아웃바운드 연결의 원본을 추가합니다.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>시나리오 5: 내부 표준 부하 분산 장치에 대한 아웃바운드 NAT


> [!NOTE]
> **Azure Virtual Network NAT** 는 내부 표준 부하 분산 장치를 활용하는 가상 머신에 대한 아웃바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

#### <a name="details"></a>세부 정보


아웃바운드 연결은 인스턴스 수준 공용 IP나 Virtual Network NAT를 통해 또는 백 엔드 풀 멤버를 아웃바운드 전용 부하 분산 장치 구성과 연결하여 명시적으로 선언할 때까지 내부 표준 부하 분산 장치에 사용할 수 없습니다. 


자세한 내용은 [아웃바운드 전용 부하 분산 장치 구성](./egress-only.md)을 참조하세요.




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>시나리오 6: 퍼블릭 표준 부하 부산 장치를 통해 아웃바운드 NAT에 TCP 및 UDP 프로토콜을 모두 사용


#### <a name="details"></a>세부 정보


퍼블릭 표준 부하 분산 장치를 사용하는 경우 제공되는 자동 아웃바운드 NAT는 부하 분산 규칙의 전송 프로토콜과 일치합니다. 


1. 부하 분산 규칙에서 아웃바운드 [SNAT](load-balancer-outbound-connections.md)을 사용하지 않도록 설정합니다. 
2. 동일한 부하 분산 장치에서 아웃바운드 규칙을 구성합니다.
3. VM에서 이미 사용하고 있는 백 엔드 풀을 다시 사용합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다. 


인바운드 NAT 규칙만 사용하는 경우 아웃바운드 NAT는 제공되지 않습니다. 


1. 백 엔드 풀에 VM을 배치합니다.
2. 공용 IP 주소 또는 공용 IP 접두사를 사용하여 하나 이상의 프런트 엔드 IP 구성을 정의합니다. 
3. 동일한 부하 분산 장치에서 아웃바운드 규칙을 구성합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다.


## <a name="limitations"></a>제한 사항

- 프런트 엔드 IP 주소당 사용 가능한 사용 후 삭제 포트의 최대 수는 64,000개입니다.
- 구성 가능한 아웃바운드 유휴 시간 제한 범위는 4-120분(240-7200초)입니다.
- 부하 분산 장치는 아웃바운드 NAT에 대해 ICMP를 지원하지 않습니다.
- 아웃바운드 규칙은 NIC의 기본 IP 구성에만 적용할 수 있습니다.  VM 또는 NVA의 보조 IP에 대한 아웃바운드 규칙은 만들 수 없습니다. 여러 NIC가 지원됩니다.
- **가용성 집합** 내의 모든 가상 머신을 아웃바운드 연결에 대한 백 엔드 풀에 추가해야 합니다. 
- **가상 머신 확장 집합** 내의 모든 가상 머신을 아웃바운드 연결에 대한 백 엔드 풀에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 표준 Load Balancer](load-balancer-overview.md)에 대한 자세한 정보
- [Azure Load Balancer에 대한 질문과 대답](load-balancer-faqs.yml) 참조
