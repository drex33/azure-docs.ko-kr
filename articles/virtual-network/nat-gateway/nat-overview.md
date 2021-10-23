---
title: Azure Virtual Network NAT란?
titlesuffix: Azure Virtual Network
description: Virtual Network NAT의 기능, 리소스, 아키텍처 및 구현에 대한 개요입니다. Virtual Network NAT의 작동 방식과 클라우드에서 NAT 게이트웨이 리소스를 사용하는 방법을 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: allensu
ms.openlocfilehash: 5e2b9baeef03163e74ad83c97d8e8148d7f6cfaf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228321"
---
# <a name="what-is-virtual-network-nat"></a>Virtual Network NAT란?

Virtual Network NAT는 완전히 관리 되 고 매우 탄력적으로 복원 가능한 NAT (네트워크 주소 변환) 서비스입니다. VNet NAT는 가상 네트워크에 대 한 아웃 바운드 인터넷 연결을 간소화 합니다. 서브넷에 구성 된 경우 모든 아웃 바운드 연결은 VNet NAT의 고정 공용 IP 주소를 사용 합니다. 

:::image type="content" source="./media/nat-overview/flow-map.png" alt-text="그림은 내부 서브넷에서 트래픽을 수신하고 PIP(공용 IP) 및 IP 접두사로 전송하는 NAT를 보여줍니다.":::

*그림: Virtual Network NAT*

## <a name="vnet-nat-benefits"></a>VNet NAT 혜택

### <a name="security"></a>보안
NAT를 사용 하는 경우 개별 Vm (또는 기타 계산 리소스)에 공용 IP 주소가 필요 하지 않으며 완전히 비공개로 유지할 수 있습니다. 공용 IP 주소가 없는 이러한 리소스는 여전히 VNet 외부의 외부 원본에 도달할 수 있습니다. 공용 IP 접두사를 연결 하 여 인접 한 ip 집합이 아웃 바운드에 사용 되도록 할 수도 있습니다. 그런 다음이 예측 가능한 IP 목록을 기반으로 대상 방화벽 규칙을 구성할 수 있습니다.

### <a name="resiliency"></a>복원력 
NAT는 완전히 관리 되 고 분산 된 서비스입니다. Vm 또는 단일 물리적 게이트웨이 장치와 같은 개별 계산 인스턴스에 종속 되지 않습니다. 소프트웨어 정의 네트워킹을 활용 하 여 복원 력이 높습니다. 

### <a name="scalability"></a>확장성
NAT는 서브넷에 연결 될 수 있으며 해당 서브넷의 모든 계산 리소스에서 사용할 수 있습니다. 또한 VNet의 모든 서브넷은 동일한 리소스를 활용할 수 있습니다. 공용 Ip 접두사와 연결 된 경우 아웃 바운드에 필요한 IP 주소 수로 자동으로 크기가 조정 됩니다.

### <a name="performance"></a>성능
NAT는 소프트웨어에 정의 된 네트워킹 서비스 이므로 계산 리소스의 네트워크 대역폭에 영향을 주지 않습니다. [NAT 게이트웨이의 성능](nat-gateway-resource.md#performance)에 대해 자세히 알아보세요.


## <a name="vnet-nat-basics"></a>VNet NAT 기본 사항

NAT는 특정 가용성 영역에서 만들 수 있으며 지정 된 영역 내에서 중복성이 기본 제공 됩니다. NAT는 기본적으로 영역에 있지 않습니다. [가용성 영역](../../availability-zones/az-overview.md) 시나리오를 만들 때 NAT는 특정 영역에 격리 될 수 있습니다. 이를 영역 배포 라고 합니다.

NAT는 처음부터 완전히 확장됩니다. 램프 업 또는 스케일 아웃 작업은 필요하지 않습니다.  Azure는 사용자를 대신하여 NAT 작업을 관리합니다.  NAT는 항상 여러 장애 도메인을 포함하고 있으므로 여러 오류를 서비스 중단 없이 유지할 수 있습니다.

* NAT를 사용하여 각 서브넷에 대한 아웃바운드 연결을 정의할 수 있습니다.  동일한 가상 네트워크 내의 여러 서브넷에는 서로 다른 NAT가 있을 수 있습니다. 서브넷은 사용할 NAT 게이트웨이 리소스를 지정하여 구성됩니다.  고객이 구성하지 않고도 NAT에서 서브넷의 모든 아웃바운드 트래픽을 자동으로 처리합니다.  사용자 정의 경로는 필요하지 않습니다. NAT는 다른 아웃바운드 시나리오보다 우선적으로 적용되며 서브넷의 기본 인터넷 대상을 대체합니다.
* NAT는 TCP 및 UDP 프로토콜만 지원 합니다. ICMP는 지원되지 않습니다.
* NAT 게이트웨이 리소스는 다음을 사용할 수 있습니다.

  * 공용 IP
  * 공용 IP 접두사
* NAT는 표준 SKU 공용 IP 주소 또는 공용 IP 접두사 리소스 또는 둘의 조합과 호환 됩니다. 공용 IP 접두사를 직접 사용하거나 접두사의 공용 IP 주소를 여러 NAT 게이트웨이 리소스에 배포할 수 있습니다. NAT는 모든 트래픽을 접두사의 IP 주소 범위로 그루밍합니다. 기본 리소스 (예: 기본 Load Balancer 또는 기본 공용 IP)는 NAT와 호환 되지 않습니다.  기본 리소스는 NAT 게이트웨이와 연결 되지 않은 서브넷에 배치 해야 합니다.
* NAT는 IPv6 공용 IP 주소 또는 IPv6 공용 IP 접두사에 연결할 수 없습니다. 그러나 이중 스택 서브넷에 연결할 수 있습니다.
* NAT를 사용 하면 가상 네트워크에서 VNet 외부 서비스로의 흐름을 만들 수 있습니다. 인터넷에서 반환하는 트래픽은 활성 흐름에 대한 응답으로만 허용됩니다. VNet 외부의 서비스는 인스턴스에 대 한 연결을 시작할 수 없습니다.
* NAT는 여러 가상 네트워크에 걸쳐 있을 수 없습니다.
* NAT는 [게이트웨이 서브넷](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) 에 배포할 수 없습니다.
* NAT (가상 머신 인스턴스 또는 기타 계산 리소스)의 개인 측에서는 존재 하지 않는 TCP 연결에 대 한 통신 시도를 위해 TCP 다시 설정 패킷을 보냅니다. 한 가지 예로 유휴 시간 제한에 도달한 연결이 있습니다. 수신되는 다음 패킷은 TCP 다시 설정을 개인 IP 주소로 반환하여 신호를 보내고 연결을 강제로 종료합니다. NAT의 퍼블릭 쪽은 TCP 다시 설정 패킷 또는 다른 트래픽을 생성하지 않으며,  고객의 가상 네트워크에서 생성된 트래픽만 내보냅니다.
* 사용되는 기본 TCP 유휴 시간 제한은 4분이며 최대 120분까지 늘릴 수 있습니다. 또한 흐름의 모든 활동에서 TCP keepalive를 포함하여 유휴 타이머를 다시 설정할 수도 있습니다.

## <a name="pricing-and-sla"></a>가격 및 SLA

가격 세부 정보는 [가상 네트워크 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)을 참조하세요. NAT 데이터 경로는 99.9% 이상 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure NAT Gateway를 사용하여 아웃바운드 연결을 개선하는 방법](https://www.youtube.com/watch?v=2Ng_uM0ZaB4)을 알아봅니다.
* [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대해 알아봅니다.
* [NAT 게이트웨이 메트릭](./nat-metrics.md)에 대해 자세히 알아봅니다.
