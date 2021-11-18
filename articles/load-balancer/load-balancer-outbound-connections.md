---
title: 아웃바운드 연결을 위한 SNAT(Source Network Address Translation)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에서 아웃바운드 인터넷 연결(SNAT)을 사용하는 방법을 알아봅니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: be42635a3d9c15287b4698f02bee91ab1ed38826
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723687"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>아웃바운드 연결을 위해 SNAT(Source Network Address Translation) 사용

특정 시나리오에는 인터넷에 대한 아웃바운드 연결을 위한 가상 머신 또는 컴퓨팅 인스턴스가 필요합니다. Azure 퍼블릭 부하 분산 장치의 프런트 엔드 IP를 사용하여 백 엔드 인스턴스에 대한 인터넷에 아웃바운드 연결을 제공할 수 있습니다. 이 구성에서는 **SNAT (원본 네트워크 주소 변환)** 을 사용 하 여 가상 컴퓨터의 개인 ip를 Load Balancer의 공용 ip 주소로 변환 합니다. SNAT는 백 엔드의 IP 주소를 부하 분산 프로그램의 공용 IP 주소에 매핑합니다. SNAT는 외부 원본이 백 엔드 인스턴스에 대해 직접적인 주소를 가질 수 없습니다.  

## <a name="azures-outbound-connectivity-methods"></a><a name="scenarios"></a>Azure의 아웃바운드 연결 방법

Azure에서 다음과 같은 방법으로 인터넷에 대 한 아웃 바운드 연결을 사용 하도록 설정할 수 있습니다.

| # | 메서드 | 포트 할당 유형 | 프로덕션 등급 | 등급 |
| ------------ | ------------ | ------ | ------------ | ------------ |
| 1 | 아웃바운드 규칙을 통해 아웃바운드에 대한 Load Balancer의 프런트 엔드 IP 주소 사용 | 고정적, 명시적 | 예, 하지만 대규모는 아님 | 확인 | 
| 2 | NAT Gateway를 서브넷에 연결 | 고정적, 명시적 | 예 | 가장 좋음 | 
| 3 | 가상 머신에 공용 IP 할당 | 고정적, 명시적 | 예 | 확인 | 
| 4 | [기본 아웃 바운드 액세스](../virtual-network/ip-services/default-outbound-access.md) 사용 | 암시적 | 아니요 | 최하위 |

## <a name="1-using-the-frontend-ip-address-of-a-load-balancer-for-outbound-via-outbound-rules"></a><a name="outboundrules"></a>1. 아웃 바운드 규칙을 통해 아웃 바운드에 대해 부하 분산 장치의 프런트 엔드 IP 주소 사용

아웃 바운드 규칙을 사용 하면 표준 공용 Load Balancer에 대 한 SNAT (원본 네트워크 주소 변환)을 명시적으로 정의할 수 있습니다. 이 구성을 통해 백 엔드 인스턴스의 아웃바운드 연결에 공용 IP 또는 부하 분산 장치의 IP를 사용할 수 있습니다.

이 구성을 사용하면 다음을 수행할 수 있습니다.

- IP 위장
- Allowlists 단순화
- 배포할 공용 IP 리소스 수 줄이기

아웃바운드 규칙을 사용하여 아웃바운드 인터넷 연결을 완전하게 제어할 수 있습니다. 아웃 바운드 규칙을 사용 하면 수동 포트 할당을 통해 특정 요구에 맞게이 기능을 확장 하 고 조정할 수 있습니다. 백 엔드 풀 크기 및 frontendIPConfigurations 수를 기반으로 SNAT 포트를 수동으로 할당 하면 SNAT 소모를 방지 하는 데 도움이 될 수 있습니다. 

"인스턴스당 포트 수" 또는 "최대 백 엔드 인스턴스 수"로 SNAT 포트를 수동으로 할당할 수 있습니다. 백 엔드에서 Virtual Machines 있는 경우 "인스턴스당 포트 수"로 포트를 할당 하 여 최대 SNAT 포트 사용을 가져오는 것이 좋습니다. 

인스턴스당 포트는 아래와 같이 계산 해야 합니다. 

**프런트 엔드 Ip 수 * 64K/백 엔드 인스턴스 수** 

백 엔드에서 Virtual Machine Scale Sets 있는 경우 "최대 백 엔드 인스턴스 수"로 포트를 할당 하는 것이 좋습니다. 남은 SNAT 포트를 사용할 수 있는 것 보다 더 많은 Vm이 백 엔드에 추가 되 면 가상 머신 확장 집합 확장을 차단할 수 있거나 새 Vm이 충분 한 SNAT 포트를 수신 하지 못할 수 있습니다. 

아웃바운드 규칙에 대한 자세한 내용은 [아웃바운드 규칙](outbound-rules.md)을 참조하세요.

## <a name="2-associating-a-nat-gateway-to-the-subnet"></a>2. NAT 게이트웨이를 서브넷에 연결

Virtual Network NAT는 가상 네트워크에 대한 아웃바운드 전용 인터넷 연결을 간소화합니다. 서브넷에 구성되는 경우 모든 아웃바운드 연결에서 지정된 고정 공용 IP 주소를 사용합니다. 가상 머신에 직접 연결되는 부하 분산 장치 또는 공용 IP 주소가 없으면 아웃바운드 연결이 가능합니다. NAT는 완전 관리형이며 복원력이 높습니다.

아웃바운드 연결을 위한 가장 좋은 방법은 NAT 게이트웨이를 사용하는 것입니다. NAT 게이트웨이는 확장성이 뛰어나고 안정적이며 SNAT 포트 고갈과 동일한 문제를 해결하지는 않습니다.

Azure Virtual Network NAT에 대한 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

##  <a name="3-assigning-a-public-ip-to-the-virtual-machine"></a>3. 가상 머신에 공용 IP 할당

 | 연결 | 방법 | IP 프로토콜 |
 | ---------- | ------ | ------------ |
 | VM NIC의 공용 IP | SNAT(Source Network Address Translation) </br> 사용하지 않음. | TCP(Transmission Control Protocol) </br> UDP(사용자 데이터그램 프로토콜) </br> ICMP(Internet Control Message Protocol) </br> ESP(보안 페이로드 캡슐화) |

트래픽은 가상 머신의 공용 IP 주소(인스턴스 수준 IP)에서 요청하는 클라이언트로 돌아갑니다.
 
Azure는 모든 아웃바운드 흐름에 대해 인스턴스 NIC의 IP 구성에 할당된 공용 IP를 사용합니다. 인스턴스에 있는 모든 삭제 포트를 사용할 수 있습니다. VM이 부하 분산되었는지는 중요하지 않습니다. 이 시나리오는 다른 시나리오에 우선합니다. 

VM에 할당된 공용 IP는 1:다가 아닌 1:1 관계이며 상태 비저장 1:1 NAT로 구현됩니다.


## <a name="4-default-outbound-access"></a>4. 기본 아웃 바운드 액세스

>[!NOTE]
> 이 방법은 포트 소모 위험을 추가하기 때문에 프로덕션 워크로드에는 **권장되지 않습니다**. 잠재적인 연결 오류를 방지하려면 프로덕션 워크로드에 이 방법을 사용하지 마십시오. 

공용 IP가 연결 되어 있지 않은 모든 Azure 리소스에는 그 앞에 아웃 바운드 규칙을 사용 하는 Load Balancer 없습니다. 즉, 가상 머신 확장 집합 유연한 오케스트레이션 모드의 일부가 아니거나, 해당 서브넷에 연결 된 NAT 게이트웨이 리소스가 아웃 바운드에 대해 최소 개수의 포트에 할당 됩니다. 이 액세스는 기본 아웃 바운드 액세스 라고 하며 응용 프로그램에 아웃 바운드 연결을 제공 하는 최악의 방법입니다. 

기본 아웃 바운드 액세스의 다른 몇 가지 예는 다음과 같습니다.

- 기본 Load Balancer를 사용 하는 경우
- Azure의 가상 머신 (위에서 언급 한 연결 제외). 이 경우 아웃바운드 연결은 기본 아웃바운드 액세스 IP에서 제공됩니다. 이 IP는 제어할 수 없는 Azure에서 할당 된 동적 IP입니다. 기본 SNAT는 프로덕션 워크 로드에 권장 되지 않으며 연결 오류가 발생할 수 있습니다.
- 아웃 바운드 규칙이 없는 Load Balancer 백 엔드 풀의 VM입니다. 따라서 아웃 바운드 및 인바운드에 대해 부하 분산 장치의 프런트 엔드 IP 주소를 사용 하 고 SNAT 포트 고갈에서 연결 오류가 발생 하기 쉽습니다.

### <a name="what-are-snat-ports"></a>SNAT 포트란?

포트는 고유한 흐름을 유지하는 데 사용되는 고유 식별자 생성에 사용됩니다. 인터넷은 5개의 튜플을 사용하여 해당 고유성을 제공합니다.

인바운드 연결에 포트를 사용하는 경우 해당 포트의 인바운드 연결 요청에 대한 **수신기** 가 존재합니다. 아웃바운드 연결에는 해당 포트를 사용할 수 없습니다. 아웃바운드 연결을 설정하려면 고유한 트래픽 흐름을 전달하고 유지할 수 있는 포트를 대상에 제공하는 **임시 포트** 가 사용됩니다. 이러한 사용 후 삭제 포트를 SNAT에 사용 하는 경우 **snat 포트** 라고 합니다.

정의에 따르면 모든 IP 주소에는 65,535개의 포트가 있습니다. 각 포트는 TCP(Transmission Control Protocol) 및 UDP(User Datagram Protocol)의 인바운드 또는 아웃바운드 연결에 사용할 수 있습니다. 공용 IP 주소를 부하 분산 장치에 프런트 엔드 IP로 추가하면 64,000개의 포트가 SNAT에 적합합니다. 프런트 엔드 Ip로 추가 된 모든 공용 Ip를 할당할 수 있지만 프런트 엔드 Ip는 한 번에 하나씩 사용 됩니다. 예를 들어 2 개의 백 엔드 ip에 액세스할 수 있는 두 개의 백 엔드 인스턴스를 각각 64000 포트에 할당 하는 경우 두 백 엔드 인스턴스는 모두 64000 포트가 모두 사용 될 때까지 첫 번째 프런트 엔드 IP의 포트를 사용 합니다.  

부하 분산 또는 인바운드 NAT 규칙에 사용되는 포트는 64,000개의 포트에서 8개의 포트를 소비합니다. 이 사용량은 SNAT에 적합한 포트 수를 줄입니다. 부하 분산 또는 인바운드 NAT 규칙이 다른 규칙과 8개의 동일한 범위에 있으므로, 추가 포트를 사용하지 않습니다. 

### <a name="how-does-default-snat-work"></a>기본 SNAT는 어떻게 작동하나요?

VM이 아웃바운드 흐름을 만든 경우 Azure에서는 원본 IP 주소를 임시 IP 주소로 변환합니다. 이 변환은 SNAT를 통해 수행됩니다. 

공용 Load Balancer를 통해 아웃 바운드 규칙 없이 SNAT를 사용 하는 경우 SNAT 포트는 아래 기본 SNAT 포트 할당 테이블에 설명 된 대로 미리 할당 됩니다.

## <a name="default-port-allocation-table"></a><a name="preallocatedports"></a> 기본 포트 할당 테이블

다음 <a name="snatporttable"></a> 표에서는 백 엔드 풀 크기에 대 한 SNAT 포트 사전 할당을 보여 줍니다.

| 풀 크기(VM 인스턴스) | IP 구성 별 기본 SNAT 포트 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

## <a name="exhausting-ports"></a> 포트 고갈

동일한 대상 IP 및 대상 포트에 대한 모든 연결에서 SNAT 포트를 사용합니다. 이 연결은 백 엔드 인스턴스 또는 **클라이언트** 에서 **서버** 로의 고유한 **트래픽 흐름** 을 유지합니다. 해당 프로세스는 트래픽을 처리할 고유 포트를 서버에 제공합니다. 해당 프로세스가 없으면 클라이언트 머신은 패킷이 어떤 흐름에 속해 있는지 인식하지 못합니다.

다수의 브라우저가 https://www.microsoft.com 으로 이동한다고 가정해 보세요. 구성은 다음과 같습니다.

* 대상 IP = 23.53.254.142
* 대상 포트 = 443
* 프로토콜 = TCP

반환 트래픽(연결을 설정하는 데 사용되는 SNAT 포트)에 대해 서로 다른 대상 포트를 사용하지 않는 경우, 클라이언트는 쿼리 결과를 구분할 수 없습니다.

아웃바운드 연결이 폭주할 수 있습니다. 백 엔드 인스턴스에 충분한 포트가 할당되지 못할 수 있습니다. 응용 프로그램 내에서 **연결 재사용** 기능을 사용 합니다. **연결을 다시 사용** 하지 않을 경우 SNAT **포트 고갈** 의 위험이 증가 합니다. Azure App Service에서 연결 풀링을 사용 하는 방법에 대 한 자세한 내용은 [의 일시적인 아웃 바운드 연결 오류 문제 해결](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#avoiding-the-problem) 을 참조 하십시오 Azure App Service

포트 고갈이 발생하면 대상 IP에 대한 새 아웃바운드 연결이 실패하게 됩니다. 연결은 포트가 사용 가능하게 되는 경우 성공합니다. 고갈은 IP 주소에 있는 64,000개의 포트가 다수의 백 엔드 인스턴스에 퍼져 있을 때 발생합니다. SNAT 포트 고갈을 완화하는 방법에 대한 지침은 [문제 해결 가이드](./troubleshoot-outbound-connection.md)를 참조하세요.  

TCP 연결의 경우 부하 분산 장치는 모든 대상 IP 및 포트에 대해 단일 SNAT 포트를 사용합니다. 이렇게 다용도로 사용하면 동일한 SNAT 포트를 사용하여 동일한 대상 IP에 다수의 연결이 사용 설정됩니다. 연결이 서로 다른 대상 포트에 연결되지 않은 경우 다용도 사용이 제한됩니다.

UDP 연결의 경우 부하 분산 장치는 대상 포트에 관계 없이 대상 IP당 하나의 SNAT 포트를 사용하는 **포트 제한 원뿔형 NAT** 알고리즘을 사용합니다. 

포트는 연결에 무제한으로 재사용되지만 대상 IP 또는 포트가 서로 다른 경우에만 재사용됩니다.


## <a name="constraints"></a>제약 조건

*   새 패킷의 전송 없이 연결이 유휴 상태인 경우 포트는 4~120분 후에 해제됩니다.
  * 이 임계값은 아웃바운드 규칙을 통해 구성할 수 있습니다.
*   각 IP 주소는 SNAT에 사용할 수 있는 64,000개의 포트를 제공합니다.
*   각 포트는 대상 IP 주소에 대한 TCP 및 UDP 연결 모두에 사용할 수 있습니다.
  * UDP SNAT 포트는 대상 포트가 고유한지와 관계없이 필요합니다. 하나의 대상 IP에 대한 모든 UDP 연결의 경우 하나의 UDP SNAT 포트만이 사용됩니다.
  * 대상 포트가 서로 다른 경우 동일한 대상 IP에 대한 다수의 연결에 TCP SNAT 포트를 사용할 수 있습니다.
*   지정된 SNAT 포트에 백 엔드 인스턴스가 부족하면 SNAT 고갈이 발생합니다. 부하 분산 장치는 사용되지 않는 SNAT 포트를 계속 사용할 수 있습니다. 백 엔드 인스턴스가 사용하는 SNAT 포트가 지정된 SNAT 포트의 개수를 넘어설 경우 새 아웃바운드 연결을 설정할 수 없습니다.
*   아웃바운드가 VM의 NIC에서 인스턴스 수준 공용 IP를 통하지 않는 한 조각화된 패킷이 삭제됩니다.
*   네트워크 인터페이스의 보조 IP 구성은 Load Balancer를 통해 아웃 바운드 통신 (공용 IP가 연결 되지 않은 경우)을 수행 하지 않습니다.

## <a name="next-steps"></a>다음 단계

*   [SNAT 고갈로 인한 아웃바운드 연결 실패 문제 해결](./troubleshoot-outbound-connection.md)
*   [SNAT 메트릭을 검토](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation)하고 해당 메트릭을 필터링, 분할, 확인하는 올바른 방법을 숙지하세요.
