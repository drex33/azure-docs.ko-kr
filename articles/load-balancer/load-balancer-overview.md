---
title: Azure Load Balancer란?
titleSuffix: Azure Load Balancer
description: Azure Load Balancer 기능, 아키텍처 및 구현에 대한 개요입니다. Load Balancer의 작동 방법과 클라우드에서 이를 활용하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 3d3c1d9937382080ea5a735e3f67e9767919366f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740780"
---
# <a name="what-is-azure-load-balancer"></a>Azure Load Balancer란?

*부하 분산* 은 백 엔드 리소스 또는 서버의 그룹에서 로드(들어오는 네트워크 트래픽)를 효율적으로 분산하는 것을 의미합니다. 

Azure Load Balancer는 OSI(Open Systems Interconnect) 모델의 계층 4에서 작동합니다. 클라이언트의 단일 연락 지점입니다. 부하 분산 장치는 부하 분산 장치의 프런트 엔드에 도착하는 인바운드 흐름을 백 엔드 풀 인스턴스에 분산합니다. 이러한 흐름은 구성된 부하 분산 규칙 및 상태 프로브에 따라 진행됩니다. 백 엔드 풀 인스턴스는 Azure Virtual Machines 또는 가상 머신 확장 집합의 인스턴스일 수 있습니다.

**[공용 부하 분산 장치](./components.md#frontend-ip-configurations)** 를 사용하여 가상 네트워크 내의 VM(가상 머신)에 대해 아웃바운드 연결을 제공합니다. 이러한 연결은 개인 IP 주소를 공용 IP 주소로 변환하여 수행됩니다. 공용 Load Balancer는 인터넷 트래픽 부하를 VM에 분산하는 데 사용됩니다.

**[내부(또는 개인) 부하 분산 장치](./components.md#frontend-ip-configurations)** 는 사설 IP가 프런트 엔드에서만 필요한 경우에 사용됩니다. 내부 부하 분산 장치는 트래픽 부하를 가상 네트워크 내에 분산하는 데 사용됩니다. 하이브리드 시나리오의 온-프레미스 네트워크에서 부하 분산 장치 프런트 엔드에 액세스할 수 있습니다.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*그림: 퍼블릭 및 내부 Load Balancer를 둘 다 사용하여 다중 계층 애플리케이션 부하 분산*

개별 부하 분산 장치 구성 요소에 대한 자세한 내용은 [Azure Load Balancer 구성 요소](./components.md)를 참조하세요.

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다. 
> * DNS 기반 글로벌 라우팅을 수행하고 HTTP/HTTPS 요청 또는 애플리케이션 계층 처리당 TLS(Transport Layer Security) 프로토콜 종료("SSL 오프로드")에 대한 요구 사항이 **없는** 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 검토하세요. 
> * 애플리케이션 계층에서 한 지역의 서버 간에 부하를 분산하려면 [Application Gateway](../application-gateway/overview.md)를 검토하세요.
> * 웹 트래픽의 글로벌 라우팅을 최적화하고 빠른 글로벌 장애 조치(failover)를 통해 최상위 계층 최종 사용자 성능과 안정성을 최적화해야 하는 경우 [Front Door](../frontdoor/front-door-overview.md)를 참조하세요.
> 
> 필요에 따라 엔드투엔드 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.
> Azure 부하 분산 옵션 비교는 [Azure의 부하 분산 옵션 개요](/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.


## <a name="why-use-azure-load-balancer"></a>Azure Load Balancer를 사용하는 이유
Azure Load Balancer를 사용하면 애플리케이션 크기를 조정하고 고가용성 서비스를 만들 수 있습니다. 부하 분산 장치는 인바운드 및 아웃바운드 시나리오를 모두 지원합니다. 부하 분산 장치는 짧은 대기 시간과 높은 처리량을 제공하고, 모든 TCP 및 UDP 애플리케이션에 대해 최대 수백만 개의 흐름으로 확장합니다.

Azure 표준 Load Balancer를 사용하여 수행할 수 있는 주요 시나리오는 다음과 같습니다.

- **[내부](./quickstart-load-balancer-standard-internal-portal.md)** 및 **[외부](./quickstart-load-balancer-standard-public-portal.md)** 트래픽을 Azure 가상 머신으로 부하를 분산합니다.

- 영역 **[내](./tutorial-load-balancer-standard-public-zonal-portal.md)** 및 **[전반에서](./quickstart-load-balancer-standard-public-portal.md)** 리소스를 배포하여 가용성을 높입니다.

- Azure 가상 머신에 대한 **[아웃바운드 연결](./load-balancer-outbound-connections.md)** 을 구성합니다.

- **[상태 프로브](./load-balancer-custom-probe-overview.md)** 를 사용하여 부하가 분산된 리소스를 모니터링합니다.

- **[포트 전달](./tutorial-load-balancer-port-forwarding-portal.md)** 을 사용하여 공용 IP 주소 및 포트로 가상 네트워크의 가상 머신에 액세스합니다.

- **[IPv6](../virtual-network/ipv6-overview.md)** 의 **[부하 분산](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** 을 위한 지원을 사용하도록 설정합니다.

- 표준 부하 분산 장치는 [Azure Monitor](../azure-monitor/overview.md)를 통해 다차원 메트릭을 제공합니다.  이러한 메트릭은 지정된 차원에 대해 필터링, 그룹화 및 나눌 수 있습니다.  이러한 메트릭은 서비스의 성능과 상태에 대한 현재 및 이전 정보를 제공합니다. [Azure Load Balancer에 대한 인사이트](./load-balancer-insights.md)는 이러한 메트릭에 대한 유용한 시각화가 포함된 미리 구성된 대시보드를 제공합니다.  리소스 상태도 지원됩니다. 자세한 내용은 **[표준 부하 분산 장치 진단](load-balancer-standard-diagnostics.md)** 을 검토하세요.

- 부하 분산 장치를 **[여러 포트, 여러 IP 주소 또는 둘 다](./load-balancer-multivip-overview.md)** 에서 사용합니다.

- **[내부](./move-across-regions-internal-load-balancer-portal.md)** 및 **[외부](./move-across-regions-external-load-balancer-portal.md)** 부하 분산 장치 리소스를 Azure 지역에 걸쳐 이동합니다.

- **[HA 포트](./load-balancer-ha-ports-overview.md)** 를 사용하여 모든 포트에서 TCP 및 UDP 흐름의 부하를 동시에 분산합니다.

### <a name="secure-by-default"></a><a name="securebydefault"></a>기본적으로 보안 적용

* 표준 부하 분산 장치는 제로 신뢰 네트워크 보안 모델을 기반으로 합니다.

* 기본적으로 보호되는 표준 Load Balancer는 가상 네트워크의 일부입니다. 가상 네트워크는 격리된 프라이빗 네트워크입니다.  

* 표준 부하 분산 장치 및 표준 공용 IP 주소는 네트워크 보안 그룹에서 열지 않는 한 인바운드 연결에 대해 닫힙니다. NSG는 허용된 트래픽을 명시적으로 허용하는 데 사용됩니다.  서브넷에 NSG가 없거나 가상 머신 리소스의 NIC가 없으면 트래픽이 이 리소스에 도달할 수 없습니다. NSG와 이를 시나리오에 적용하는 방법을 알아보려면 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.

* 기본 부하 분산 장치는 기본적으로 인터넷에 열려 있습니다. 

* 부하 분산 장치는 고객 데이터를 저장하지 않습니다.

## <a name="pricing-and-sla"></a>가격 및 SLA

표준 부하 분산 장치 가격 정보에 대해서는 [부하 분산 장치 가격](https://azure.microsoft.com/pricing/details/load-balancer/)을 참조하세요.
기본 부하 분산 장치는 무료로 제공됩니다.
[부하 분산 장치용 SLA](https://aka.ms/lbsla)를 참조하세요. 기본 부하 분산 장치에는 SLA가 없습니다.

## <a name="whats-new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) 페이지에서 최신 Azure Load Balancer 기능 업데이트를 확인합니다.

## <a name="next-steps"></a>다음 단계

부하 분산 장치를 사용하여 시작하려면 [공용 표준 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.

Azure Load Balancer 제한 사항 및 구성 요소에 대한 자세한 내용은 [Azure Load Balancer 구성 요소](./components.md) 및 [Azure Load Balancer 개념](./concepts.md)을 참조하세요.