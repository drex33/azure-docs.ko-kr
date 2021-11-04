---
title: Azure 가상 네트워크용 IPv6 개요
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크의 IPv6 엔드포인트 및 데이터 경로에 대한 IPv6 설명입니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 259be22f0d69326617845d4b14a249e3b5cf4939
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424612"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Azure 가상 네트워크용 IPv6란?

Azure VNet(가상 네트워크)용 IPv6을 사용하면 가상 네트워크 내부에서 또한 인터넷과 IPv6 및 IPv4 연결을 사용하여 Azure에서 애플리케이션을 호스트할 수 있습니다. 공용 IPv4 주소의 고갈로 인해 모바일 및 IoT(사물 인터넷)용 새 네트워크는 흔히 IPv6에서 빌드됩니다. 오래된 ISP 및 모바일 네트워크도 IPv6으로 전환되고 있습니다. IPv4 전용 서비스는 기존 시장과 새로운 시장 모두에서 매우 불리할 수 있습니다. 이중 스택 IPv4/IPv6 연결을 사용하면 Azure 호스팅 서비스가 기존 IPv4 디바이스 및 새 IPv6 디바이스와 네트워크 모두에 쉽게 연결하는 전 세계적으로 사용 가능한 이중 스택 서비스를 통해 이 기술 격차를 극복할 수 있습니다.

Azure의 원래 IPv6 연결은 Azure에서 호스트되는 애플리케이션에 이중 스택(IPv4/IPv6) 인터넷 연결을 쉽게 제공할 수 있습니다. 이를 통해 인바운드 및 아웃바운드 연결 모두에 부하 분산된 IPv6 연결을 사용하여 VM을 간단히 배포할 수 있습니다. 이 기능은 계속 사용할 수 있으며 자세한 정보는 [여기](../../load-balancer/load-balancer-ipv6-overview.md)에서 확인할 수 있습니다.
Azure 가상 네트워크용 IPv6은 보다 완전한 기능을 갖춰 전체 IPv6 솔루션 아키텍처를 Azure에 배포할 수 있습니다.


다음 다이어그램은 Azure에서의 간단한 이중 스택(IPv4/IPv6) 배포를 보여 줍니다.

![IPv6 네트워크 배포 다이어그램](../media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>이점

Azure VNET용 IPv6 이점:

- Azure 호스팅 애플리케이션이 늘어나는 모바일 및 사물 인터넷 시장으로 진출을 확장하는 데 도움이 됩니다.
- 이중 스택 IPv4/IPv6 VM이 최대의 서비스 배포 유연성을 제공합니다. 단일 서비스 인스턴스가 IPv4 및 IPv6 사용 인터넷 클라이언트 모두에 연결할 수 있습니다.
- 장기간 유지된 안정적인 Azure VM과 인터넷 간 IPv6 연결을 기반으로 합니다.
- 인터넷에 대한 IPv6 연결은 배포에서 명시적으로 요청하는 경우에만 설정되기 때문에 기본적으로 안전합니다.

## <a name="capabilities"></a>기능

Azure VNet용 IPv6에는 다음과 같은 기능이 포함되어 있습니다.

- Azure 고객은 자체 IPv6 가상 네트워크 주소 공간을 정의하여 애플리케이션 및 고객의 요구를 충족하거나 원활하게 온-프레미스 IP 공간으로 통합할 수 있습니다.
- 이중 스택 서브넷을 포함하는 이중 스택(IPv4 및 IPv6)을 사용하면 애플리케이션이 가상 네트워크 또는 인터넷의 IPv4 및 IPv6 리소스 모두에 연결할 수 있습니다.
    > [!IMPORTANT]
    > IPv6 서브넷은 크기가 정확히 /64여야 합니다.  그러면 일부 라우터가 /64 IPv6 경로만 허용할 수 있으므로 향후 온-프레미스 네트워크에 대한 서브넷 라우팅을 사용하도록 결정할 경우 호환성이 보장됩니다.  
- 네트워크 보안 그룹에 대한 IPv6 규칙을 사용하여 리소스를 보호합니다.
    - 또한 Azure 플랫폼의 DDoS(분산형 서비스 거부) 보호가 인터넷 연결 공용 IP로 확장됩니다.
- 특히 네트워크 가상 어플라이언스를 활용하여 애플리케이션을 보강하는 경우 사용자 정의 경로를 사용하여 가상 네트워크에서 IPv6 트래픽 라우팅을 사용자 지정합니다.
- Linux 및 Windows Virtual Machines는 모두 Azure VNET용 IPv6을 사용할 수 있습니다.
- [표준 IPv6 공용 Load Balancer](../../load-balancer/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)를 지원하여 복원력 있고 확장 가능한 애플리케이션을 만들 수 있습니다. 다음과 같은 선택적 기능이 포함됩니다.
    - 상태가 정상이고 따라서 새 흐름을 수신할 수 있는 백 엔드 풀 인스턴스를 확인하는 선택적 IPv6 상태 프로브.
    - 특정 요구에 맞게 이 기능을 확장하고 조정하도록 아웃바운드 연결에 대한 모든 선언적 제어를 제공하는 선택적 아웃바운드 규칙.
    - 단일 부하 분산 장치에서 여러 IPv6 공용 IP 주소를 사용할 수 있도록 하는 선택적 다중 프런트 엔드 구성. 프런트 엔드 주소에서 동일한 프런트 엔드 프로토콜 및 포트를 재사용할 수 있습니다.
    - 선택적 IPv6 포트는 부하 분산 규칙의 부동 IP 기능을 사용하여 백 엔드 인스턴스에서 재사용할 수 있습니다. 
    - 참고: 부하 분산은 프로토콜 변환을 수행하지 않습니다(NAT64 없음). 
- [표준 IPv6 내부 부하 분산 장치](../../load-balancer/ipv6-dual-stack-standard-internal-load-balancer-powershell.md)를 지원하여 Azure VNET 내에서 복원력 있는 다중 계층 애플리케이션을 만들 수 있습니다.   
- 레거시 배포와 호환을 위해 기본 IPv6 공용 Load Balancer를 지원합니다.
- [예약된 IPv6 공용 IP 주소 및 주소 범위](public-ip-address-prefix.md)가 안정적이고 예측 가능한 IPv6 주소를 제공하여 회사 및 고객에 대한 Azure 호스팅 애플리케이션을 용이하게 필터링할 수 있습니다.
- 인스턴스 수준 공용 IP가 개별 VM에 대한 직접 IPv6 인터넷 연결을 제공합니다.
- [기존 IPv4 전용 배포에 IPv6 추가](../../load-balancer/ipv6-add-to-existing-vnet-powershell.md) - 이 기능을 사용하면 배포를 다시 만들 필요 없이 기존 IPv4 전용 배포에 IPv6 연결을 쉽게 추가할 수 있습니다.  이 과정에서 IPv4 네트워크 트래픽은 영향을 받지 않으므로 애플리케이션 및 OS에 따라 라이브 서비스에도 IPv6을 추가할 수 있습니다.    
- IPv6(AAAA) 레코드에 대한 Azure DNS 지원으로 인터넷 클라이언트가 선택한 프로토콜을 사용하여 이중 스택 애플리케이션에 원활하게 액세스할 수 있도록 합니다. 
- IPv6을 사용하여 가상 머신 확장 집합으로 부하를 자동으로 확장하는 이중 스택 애플리케이션을 만듭니다.
- 지역 및 글로벌 피어링 모두 [VNET(Virtual Network) 피어링을](../../virtual-network/virtual-network-peering-overview.md) 사용하면 이중 스택 VNET을 원활하게 연결할 수 있습니다. 피어링된 네트워크의 VM에 있는 IPv4 및 IPv6 엔드포인트는 서로 통신할 수 있습니다. 배포를 이중 스택으로 전환하는 동안 IPv4 전용 VNET을 사용하여 이중 스택을 피어링할 수도 있습니다. 
- IPv6 문제 해결 및 진단은 부하 분산 장치 메트릭/경고 및 Network Watcher 기능(패킷 캡처, NSG 흐름 로그, 연결 문제 해결, 연결 모니터링 등)과 함께 사용할 수 있습니다.   

## <a name="scope"></a>범위
Azure VNET용 IPv6은 고객이 Azure에서 이중 스택(IPv4 + IPv6) 애플리케이션을 호스트할 수 있는 기본 기능 집합입니다.  Microsoft에서는 계속해서 더 많은 Azure 네트워킹 기능에 IPv6 지원을 추가하고 궁극적으로 Azure PaaS 서비스의 이중 스택 버전을 제공하려고 계획하고 있지만, 그동안에는 모든 Azure PaaS 서비스는 이중 스택 가상 머신의 IPv4 엔드포인트를 통해 액세스할 수 있습니다.   

## <a name="limitations"></a>제한 사항
현재 릴리스의 Azure 가상 네트워크용 IPv6에는 다음과 같은 제한 사항이 있습니다.
- VPN 게이트웨이는 현재 IPv6 트래픽만 지원하지만 이중 스택 VNET에 배포할 수 있습니다.
- Azure 플랫폼(AKS 등)은 컨테이너에 대한 IPv6 통신을 지원하지 않습니다. 
- IPv6 전용 Virtual Machines 또는 Virtual Machines Scale Sets는 지원되지 않습니다. 각 NIC는 IPv4 IP 구성을 하나 이상 포함해야 합니다. 
- 기존 IPv4 배포에 IPv6을 추가하는 경우 기존 리소스 탐색 링크를 포함하는 VNET에 IPv6 범위를 추가할 수 없습니다.  
- 현재 Azure 공용 DNS에 대해 IPv6용 정방향 DNS는 지원되지만 역방향 DNS는 아직 지원되지 않습니다.
- 동일한 NSG 내에서 IPv4 및 IPv6에 대한 NSG 규칙을 만들 수 있지만 현재 IP 접두사 지정 시 IPv4 서브넷을 동일한 규칙의 IPv6 서브넷과 결합할 수 없습니다.

## <a name="pricing"></a>가격 책정

IPv6 Azure 리소스 및 대역폭은 IPv4와 동일한 요금으로 청구됩니다. IPv6에 대한 추가 요금이나 다른 요금은 없습니다. [공용 IP 주소](https://azure.microsoft.com/pricing/details/ip-addresses/), [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/) 또는 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) 가격 책정에 대한 세부 정보를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 IPv6 이중 스택 애플리케이션을 배포](../../load-balancer/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)하는 방법을 알아봅니다.
- [Azure CLI를 사용하여 IPv6 이중 스택 애플리케이션을 배포](../../load-balancer/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)하는 방법을 알아봅니다.
- [Resource Manager 템플릿을 사용하여 IPv6 이중 스택 애플리케이션을 배포(JSON)](../../load-balancer/ipv6-configure-standard-load-balancer-template-json.md)하는 방법을 알아봅니다.
