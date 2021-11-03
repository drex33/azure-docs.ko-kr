---
title: Azure Route Server에 관한 질문과 대답
description: Azure Route Server에 관한 질문과 대답을 확인합니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 47584994586e735647be4116fb49de610e5f97f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453272"
---
# <a name="azure-route-server-faq"></a>Azure Route Server FAQ

## <a name="what-is-azure-route-server"></a>Azure Route Server란?

Azure Route Server는 NVA(네트워크 가상 어플라이언스)와 가상 네트워크 간의 라우팅을 쉽게 관리할 수 있는 완전 관리형 서비스입니다.

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server는 단순히 VM인가요?

아니요. Azure Route Server는 고가용성을 사용하여 설계된 서비스입니다. [가용성 영역](../availability-zones/az-overview.md)을 지원하는 Azure 지역에 배포된 경우 영역 수준 중복성이 있습니다.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>가상 네트워크에서 몇 대의 경로 서버를 만들 수 있나요?

가상 네트워크에서는 하나의 경로 서버만 만들 수 있습니다. *RouteServerSubnet* 이라는 전용 서브넷에 배포해야 합니다.

### <a name="does-azure-route-server-support-virtual-network-peering"></a>Azure Route Server에서 가상 네트워크 피어링을 지원하나요?

예, Azure Route Server를 호스트하는 가상 네트워크를 다른 가상 네트워크에 피어링하고 두 번째 가상 네트워크에서 원격 게이트웨이 사용을 활성화하면, Azure Route Server는 해당 가상 네트워크의 주소 공간을 학습하고 피어링된 모든 NVA로 보냅니다. 또한 NVA에서 피어링된 가상 네트워크에 있는 VM의 라우팅 테이블로 경로를 프로그래밍합니다. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure Route Server에서 지원하는 라우팅 프로토콜은 무엇인가요?

Azure Route Server는 BGP(Border Gateway Protocol)만 지원합니다. 가상 네트워크의 전용 서브넷에 Azure Route Server를 배포해야 하므로 NVA는 다중 홉 외부 BGP를 지원해야 합니다. 선택한 [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet))은 NVA에서 BGP를 구성할 때 Azure Route Server에서 사용하는 것과 달라야 합니다.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server에서 내 NVA와 내 VM 간에 데이터 트래픽을 라우팅하나요?

아니요. Azure Route Server는 NVA와 BGP 경로만 교환합니다. 데이터 트래픽은 NVA에서 선택한 대상 VM으로 직접 이동하고 VM에서 NVA로 직접 이동합니다.

### <a name="does-azure-route-server-store-customer-data"></a>Azure Route Server에서 고객 데이터를 저장하나요?
아니요. Azure Route Server는 NVA와 BGP 경로만 교환한 다음, 가상 네트워크로 전파합니다.

### <a name="why-does-azure-route-server-require-a-public-ip-address"></a>Azure Route Server에 공용 IP 주소가 필요한 이유는 무엇인가요?

Azure Route Server는 공용 IP 주소가 필요하므로 Route Server 구성을 관리하는 백엔드 서비스에 대한 연결을 보장해야 합니다. 

### <a name="does-azure-route-server-support-ipv6"></a>Azure Route Server는 i p v 6을 지원 하나요?

아니요. IPv6 지원은 나중에 추가 될 예정입니다. 

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Azure Route Server가 둘 이상의 NVA에서 동일한 경로를 수신하는 경우 어떻게 처리하나요?

경로의 AS 경로 길이가 동일한 경우 Azure Route Server는 가상 네트워크의 VM에 대해 각각 다른 다음 홉이 있는 경로의 여러 복사본을 프로그래밍합니다. VM이 이 경로의 대상으로 트래픽을 보낼 때 VM 호스트는 ECMP(등가 다중 경로) 라우팅을 수행합니다. 그러나 한 NVA가 다른 NVA보다 짧은 AS 경로 길이로 경로를 보내는 경우 Azure Route Server는 다음 홉이 이 NVA로 설정된 경로만 가상 네트워크의 VM으로 프로그래밍합니다.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Azure Route Server는 수신하는 경로의 BGP 커뮤니티를 유지하나요?

예. Azure Route Server는 BGP 커뮤니티를 있는 그대로 사용하여 경로를 전파합니다.

### <a name="what-is-the-bgp-timer-setting-of-azure-route-server"></a>Azure Route Server의 BGP 타이머 설정은 무엇 인가요?
Keep-alive 타이머는 60 초 및 대기 시간 180 초로 설정 됩니다.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>어떤 ASN(자율 시스템 번호)을 사용할 수 있나요?

네트워크 가상 어플라이언스에서 고유 퍼블릭 ASN 또는 프라이빗 ASN을 사용할 수 있습니다. Azure 또는 IANA에서 예약한 범위는 사용할 수 없습니다.
다음 ASNs는 Azure 또는 IANA에서 예약됩니다.

* Azure에서 예약된 ASN:
    * 공용 ASN: 8074, 8075, 12076
    * 프라이빗 ASN: 65515, 65517, 65518, 65519, 65520
* [IANA에서 예약한](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ASN:
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>32비트(4바이트) ASN을 사용할 수 있나요?

아니요, Azure Route Server는 16비트(2바이트) ASN만 지원합니다.

### <a name="can-i-associate-a-user-defined-route-udr-to-the-routeserversubnet"></a>UDR (사용자 정의 경로)을 RouteServerSubnet에 연결할 수 있나요?

아니요, Azure 경로 서버는 RouteServerSubnet에서 UDR 구성을 지원 하지 않습니다.

### <a name="can-i-associate-a-network-security-group-nsg-to-the-routeserversubnet"></a>RouteServerSubnet에 NSG (네트워크 보안 그룹)를 연결할 수 있나요?

아니요, Azure 경로 서버는 RouteServerSubnet에 대 한 NSG 연결을 지원 하지 않습니다.

### <a name="can-i-peer-two-route-servers-in-two-peered-virtual-networks-and-enable-the-nvas-connected-to-the-route-servers-to-talk-to-each-other"></a>피어링된 두 개의 가상 네트워크에서 두 개의 경로 서버를 피어링하고, 경로 서버에 연결된 NVA가 서로 통신하도록 할 수 있나요? 

***토폴로지: NVA1 -> RouteServer1 -> (VNet 피어링을 통해) -> RouteServer2 -> NVA2***

아니요. Azure Route Server는 데이터 트래픽을 전달하지 않습니다. NVA를 통한 전송 연결을 사용하도록 설정하려면 NVA 간에 직접 연결(예: IPsec 터널)을 설정하고 동적 경로 전파를 위해 경로 서버를 사용합니다. 

### <a name="can-i-use-azure-route-server-to-direct-traffic-between-subnets-in-the-same-virtual-network-to-flow-inter-subnet-traffic-through-the-nva"></a>Azure Route Server를 사용 하 여 NVA를 통해 서브넷 간 트래픽을 전달 하기 위해 동일한 가상 네트워크의 서브넷 간에 트래픽을 보낼 수 있나요?

아니요. BGP 경로가 더 구체적인 경우에도, 가상 네트워크, 가상 네트워크 피어링 또는 가상 네트워크 서비스 엔드포인트와 관련된 트래픽에 대한 시스템 경로가 기본 경로입니다. 경로 서버는 BGP를 사용 하 여 경로를 보급 하므로 현재 디자인에서 지원 되지 않습니다. 계속 해 서 UDRs를 사용 하 여 경로를 강제로 재정의 해야 하며, BGP를 사용 하 여 이러한 경로를 빠르게 장애 조치 (failover) 할 수 없습니다. 장애 조치 (failover) 상황에서 API를 통해 udrs를 업데이트 하려면 계속 해 서 타사 솔루션을 사용 하거나, HA 포트 모드를 사용 하는 Azure Load Balancer를 사용 하 여 트래픽을 직접 보내야 합니다.

여전히 경로 서버를 사용 하 여 다른 가상 네트워크의 서브넷 간에 NVA를 사용 하는 흐름으로 트래픽을 보낼 수 있습니다. 가능한 유일한 디자인은 "스포크" 가상 네트워크 당 하나의 서브넷이 고 모든 가상 네트워크는 "허브" 가상 네트워크에 피어 링,이는 매우 제한적 이며, 가상 네트워크 및 서브넷에 대 한 확장 고려 사항 및 Azure의 최대 한도를 고려해 야 합니다.

## <a name="route-server-limits"></a><a name = "limitations"></a>Route Server 한도

Azure Route Server에는 배포당 다음과 같은 한도가 있습니다.

| 리소스 | 제한 |
|----------|-------|
| 지원되는 BGP 피어 수 | 8 |
| 각 BGP 피어가 Azure Route Server에 보급할 수 있는 경로 수 | 1000 |
| Azure Route Server가 ExpressRoute 또는 VPN 게이트웨이에 보급할 수 있는 경로 수 | 200 |
| Azure Route Server에서 지원할 수 있는 가상 네트워크(피어링된 가상 네트워크 포함)의 VM 수 | 2000 |

Azure Route Server에서 지원할 수 있는 Vm 수는 하드 제한 되지 않습니다. 이는 Azure 지역 내에서 경로 서버 인프라를 배포 하는 방법에 따라 달라 집니다.

NVA가 한도보다 많은 경로를 보급하면 BGP 세션이 끊깁니다. 게이트웨이와 Azure Route Server 간에 BGP 세션이 끊어지면 온-프레미스 네트워크에서 Azure로의 연결이 끊깁니다. 자세한 내용은 [Azure 가상 머신 라우팅 문제 진단](../virtual-network/diagnose-network-routing-problem.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

[Azure Route Server 구성](quickstart-configure-route-server-powershell.md) 방법 알아보기
