---
title: Azure Load Balancer 문제 해결
description: Azure Load Balancer의 알려진 문제를 해결하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/24/2021
ms.author: allensu
ms.openlocfilehash: c0cde104c549f0c6143463cbc4f479a029796f22
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128876"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Azure Load Balancer 백엔드 트래픽 응답 문제 해결

이 페이지에서는 Azure Load Balancer 질문에 대한 문제 해결 정보를 제공합니다.

## <a name="vms-behind-load-balancer-are-receving-uneven-distribution-of-traffic"></a>Load Balancer 뒤의 VM이 균일하지 않은 트래픽 분산을 받고 있습니다.
백 엔드 풀 멤버가 트래픽을 수신하는 것으로 의심되는 경우 다음과 같은 원인 때문일 수 있습니다. Azure Load Balancer 연결에 따라 트래픽을 분산합니다. 패킷이 아닌 연결당 트래픽 분포를 확인해야 합니다. 미리 구성된 Load Balancer Insights 대시보드 에서 Flow **배포** 탭을 사용하는지 [확인합니다.](load-balancer-insights.md#flow-distribution)

Azure Load Balancer 진정한 라운드 로빈 부하 분산을 지원하지 않지만 해시 기반 [배포 모드](distribution-mode-concepts.md)를 지원합니다. 

## <a name="cause-1-you-have-session-persistence-configured"></a>원인 1: 세션 지속성이 구성되어 있습니다.

원본 지속성 배포 모드를 사용하면 트래픽의 균일하지 않은 배포가 발생할 수 있습니다. 이렇게 하지 않으면 백 엔드 풀의 모든 정상 인스턴스에 트래픽이 분산되도록 세션 지속성을 **없음으로** 업데이트합니다. [Azure Load Balancer 배포 모드에](distribution-mode-concepts.md)대해 자세히 알아봅니다.

## <a name="cause-2-you-have-a-proxy-configured"></a>원인 2: 프록시가 구성되어 있습니다.

프록시 뒤에서 실행되는 클라이언트는 Load Balancer 관점에서 하나의 고유한 클라이언트 애플리케이션으로 표시될 수 있습니다.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Load Balancer 뒤의 VM이 구성된 데이터 포트의 트래픽에 응답하지 않습니다.

백 엔드 풀 VM이 정상으로 표시되고 상태 프로브에 응답하지만 여전히 부하 분산에 참여하지 않거나 데이터 트래픽에 응답하지 않을 경우 다음 이유 중 하나 때문일 수 있습니다.
* Load Balancer 백 엔드 풀 VM이 데이터 포트에서 수신하지 않습니다.
* 네트워크 보안 그룹이 Load Balancer 백 엔드 풀 VM에서 포트를 차단하고 있습니다.  
* 동일한 VM 및 NIC에서 Load Balancer에 액세스
* 참여하는 Load Balancer 백 엔드 풀 VM에서 인터넷 Load Balancer 프런트 엔드에 액세스

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>원인 1: Load Balancer 백 엔드 풀 VM이 데이터 포트에서 수신 대기하지 않습니다.

VM이 데이터 트래픽에 응답하지 않을 경우 대상 포트가 참여 VM에서 열려 있지 않거나 VM이 해당 포트에서 수신하지 않기 때문일 수 있습니다. 

**유효성 검사 및 해결**

1. 백 엔드 VM에 로그인합니다. 
2. 명령 프롬프트를 열고 다음 명령을 실행하여 데이터 포트에서 수신 대기하는 애플리케이션이 있는지 확인합니다.  
            netstat -an 
3. 포트가 "LISTENING" 상태로 표시되지 않으면 해당 수신기 포트를 구성합니다. 
4. 포트가 Listening으로 표시되어 있는 경우 해당 포트의 대상 애플리케이션에 문제가 있는지 확인합니다.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>원인 2: 네트워크 보안 그룹이 Load Balancer 백 엔드 풀 VM에서 포트를 차단하고 있습니다.  

서브넷 또는 VM에 구성된 하나 이상의 네트워크 보안 그룹이 원본 IP 또는 포트를 차단하는 경우 VM이 응답할 수 없습니다.

공용 부하 분산 장치의 경우 클라이언트와 부하 분산 장치 백 엔드 VM 간의 통신에 인터넷 클라이언트의 IP 주소가 사용됩니다. 백 엔드 VM의 네트워크 보안 그룹에서 클라이언트의 IP 주소가 허용되는지 확인합니다.

1. 백 엔드 VM에 구성된 네트워크 보안 그룹을 나열합니다. 자세한 내용은 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하세요.
1. 네트워크 보안 그룹 목록에서 다음을 확인합니다.
    - 데이터 포트에서 들어오거나 나가는 트래픽에 간섭이 있습니다. 
    - VM 또는 서브넷의 NIC에 대해 Load Balancer 프로브 및 트래픽을 허용하는 기본 규칙보다 우선 순위가 더 높은 **모두 거부** 네트워크 보안 그룹 규칙(네트워크 보안 그룹은 프로브 포트에 해당하는 168.63.129.16의 부하 분산 장치 IP를 허용해야 함).
1. 규칙에 의해 트래픽이 차단되는 경우 해당 규칙을 제거한 후 데이터 트래픽을 허용하도록 다시 구성합니다.  
1. VM이 상태 프로브에 응답하기 시작했는지 테스트합니다.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>원인 3: 동일한 VM 및 네트워크 인터페이스에서 Load Balancer에 액세스 

Load Balancer의 백 엔드 VM에서 호스트된 애플리케이션이 동일한 네트워크 인터페이스를 통해 동일한 백엔드 VM에서 호스트되는 다른 애플리케이션에 액세스하려고 하는 경우 이는 지원되지 않는 시나리오이며 실패합니다. 

**해결 방법** 다음 방법 중 하나를 사용하여 이 문제를 해결할 수 있습니다.
* 애플리케이션마다 별도 백 엔드 풀 VM을 구성합니다. 
* 각 애플리케이션이 자체 네트워크 인터페이스 및 IP 주소를 사용하도록 이중 NIC VM에 애플리케이션을 구성합니다. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>원인 4: 참여하는 Load Balancer 백 엔드 풀 VM에서 내부 Load Balancer 프런트 엔드에 액세스

내부 Load Balancer가 VNet 내에서 구성되고, 참여하는 백 엔드 Load Balancer 중 하나가 내부 Load Balancer 프런트 엔드에 액세스하려고 하면 흐름이 원본 VM에 매핑될 때 오류가 발생할 수 있습니다. 이 시나리오는 지원되지 않습니다.

**해결 방법** 프록시를 사용하여이 시나리오의 차단을 해제하는 방법이 몇 가지 있습니다. Application Gateway 또는 기타 타사 프록시(예: nginx 또는 haproxy)를 평가하세요. Application Gateway에 대한 자세한 내용은 [Application Gateway에 대한 개요](../application-gateway/overview.md)를 참조하세요.

**세부 정보** Load Balancer는 둘 다 개인 IP 주소 공간에 있으므로 아웃바운드에서 시작된 연결을 내부 Load Balancer의 프런트 엔드로 변환하지 않습니다. 퍼블릭 Load Balancer는 가상 네트워크 내부의 프라이빗 IP 주소에서 퍼블릭 IP 주소로의 [아웃바운드 연결](load-balancer-outbound-connections.md)을 제공합니다. 내부 Load Balancer의 경우, 이렇게 하면 변환이 필요하지 않으면 고유한 내부 IP 주소 공간 내에서 SNAT 포트가 고갈될 가능성이 방지됩니다.

부작용은 백 엔드 풀에 있는 VM에서의 아웃바운드 흐름이 해당 풀에 있는 내부 Load Balancer의 프런트 엔드로 흐름을 유도하 _고_ 자체로 매핑되는 경우, 흐름의 두 레그가 일치하지 않는다는 것입니다. 두 레그가 일치하지 않기 때문에 흐름이 실패합니다. 흐름은 프런트 엔드로의 흐름을 생성한 백 엔드 풀의 동일한 VM에 다시 매핑되지 않으면 성공하게 됩니다.

흐름이 스스로에게 다시 매핑되는 경우 아웃바운드 흐름은 VM에서 시작된 후 프런트 엔드에 나타나며 해당 인바운드 흐름은 VM에서 시작된 후 자신에게 나타납니다. 게스트 운영 체제의 관점에서 동일한 흐름의 인바운드 및 아웃바운드 부분은 가상 머신 내에서 일치하지 않습니다. TCP 스택은 동일한 흐름의 이러한 절반을 동일한 흐름의 일부로서 인식하지 않습니다. 소스와 대상은 일치하지 않습니다. 흐름이 백 엔드 풀에서 다른 모든 VM에 매핑되는 경우 흐름의 절반은 일치하며 VM은 흐름에 응답할 수 있습니다.

이 시나리오에 대한 증상은 흐름이 흐름을 처음 시작한 동일한 백 엔드로 돌아갈 때 일시적인 연결 시간 제한입니다. 일반적인 해결 방법은 내부 Load Balancer 뒤에 프록시 계층을 삽입하고 DSR(Direct Server Return) 스타일 규칙을 사용하는 것입니다. 자세한 내용은 [Azure Load Balancer의 다중 프런트 엔드](load-balancer-multivip-overview.md)를 참조하세요.

타사 프록시에 내부 Load Balancer를 결합하거나 HTTP/HTTPS가 있는 프록시 시나리오에 대해 내부 [Application Gateway](../application-gateway/overview.md)를 사용할 수 있습니다. 퍼블릭 Load Balancer를 사용하여 이 문제를 완화할 수는 있지만 결과 시나리오에서는 [SNAT 고갈](load-balancer-outbound-connections.md)이 발생할 가능성이 높습니다. 신중하게 관리하지 못할 경우에는 이 두 번째 방법을 사용하지 마세요.

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
