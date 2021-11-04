---
title: Azure Virtual Network NAT 연결 문제 해결
titleSuffix: Azure Virtual Network
description: Virtual Network NAT와 관련된 문제를 해결합니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 610d24576386b311bf5e4ef5a98c1c22a705aff0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466412"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Azure Virtual Network NAT 연결 문제 해결

이 문서는 Virtual Network NAT를 사용하는 경우 관리자가 연결 문제를 진단하고 해결하는 데 도움이 됩니다.

## <a name="problems"></a>문제

* [SNAT 소모](#snat-exhaustion)
* [ICMP ping이 실패함](#icmp-ping-is-failing)
* [연결 실패](#connectivity-failures)
* [IPv6 공존](#ipv6-coexistence)
* [NAT 게이트웨이 IP에서 연결이 시작되지 않음](#connection-doesnt-originate-from-nat-gateway-ips)

이러한 문제를 해결하려면 다음 섹션의 단계를 따르세요.

## <a name="resolution"></a>해결 방법

### <a name="snat-exhaustion"></a>SNAT 소모

단일 [NAT 게이트웨이 리소스](nat-gateway-resource.md)는 64,000~1,000,000개의 동시 흐름을 지원합니다.  각 IP 주소는 64,000개의 SNAT 포트를 사용 가능한 인벤토리에 제공합니다. NAT 게이트웨이 리소스당 최대 16개의 IP 주소를 사용할 수 있습니다.  SNAT 메커니즘은 [여기](nat-gateway-resource.md#source-network-address-translation)서 자세히 설명하고 있습니다.

SNAT 소모의 근본 원인은 아웃바운드 연결의 설정, 관리 또는 구성 가능한 타이머가 기본값에서 변경되는 방식에 대한 앤티 패턴인 경우가 많습니다.  이 섹션을 주의 깊게 검토하세요.

#### <a name="steps"></a>단계

1. 기본 유휴 시간 제한을 4분보다 큰 값으로 수정했는지 확인합니다.
2. 애플리케이션에서 아웃바운드 연결을 만드는 방법(예: 코드 검토 또는 패킷 캡처)을 조사합니다. 
3. 이 활동이 예상되는 동작인지, 아니면 애플리케이션이 잘못 작동하는지를 확인합니다.  Azure Monitor에서 [메트릭](nat-metrics.md)을 사용하여 검색 결과를 구체화합니다. "실패" 범주를 SNAT 연결 메트릭에 사용합니다.
4. 적절한 패턴을 따르는지 여부를 평가합니다.
5. NAT 게이트웨이 리소스에 할당된 추가 IP 주소를 사용하여 SNAT 포트 소모가 완화되는지 여부를 평가합니다.

#### <a name="design-patterns"></a>디자인 패턴

가능한 경우 항상 연결 다시 사용 및 연결 풀링을 활용합니다.  이러한 패턴은 리소스 소모 문제를 방지하여 예측 가능한 동작을 수행합니다. 이러한 패턴의 기본 형식은 다양한 개발 라이브러리 및 프레임워크에서 찾을 수 있습니다.

_**해결 방법:**_ 적절한 패턴 및 모범 사례 사용

- NAT 게이트웨이 리소스의 기본 TCP 유휴 시간 제한은 4분입니다.  이 설정이 더 높은 값으로 변경되 면 NAT는 더 오래 흐름을 유지하고 [SNAT 포트 인벤토리에서 불필요한 압력](nat-gateway-resource.md#timers)이 발생할 수 있습니다.
- 원자성 요청(연결당 요청 하나)은 좋지 않은 디자인 선택입니다. 이러한 안티패턴은 규모를 제한하고, 성능을 저하시키고, 안정성을 떨어뜨립니다. 그 대신, HTTP/S 연결을 다시 사용하여 연결 수와 연결된 SNAT 포트 수를 줄입니다. TLS를 사용하면 핸드셰이크, 오버헤드 및 암호화 작업 비용이 감소하므로 애플리케이션 규모가 증가하고 성능이 향상됩니다.
- 클라이언트에서 DNS 해결 프로그램 결과를 캐싱하지 않으면 DNS가 볼륨에 여러 개별 흐름을 들일 수 있습니다. 캐싱을 사용하세요.
- UDP 흐름(예: DNS 조회)은 유휴 시간 제한 동안 SNAT 포트를 할당합니다. 유휴 시간 제한이 길수록 SNAT 포트가 받는 압력이 높아집니다. 유휴 시간 제한을 짧게(예: 4분) 설정하세요.
- 연결 풀을 사용하여 연결 볼륨을 셰이핑하세요.
- 절대로 TCP 흐름을 자동으로 중단하고 TCP 타이머를 사용하여 흐름을 정리하지 마세요. TCP에서 연결을 명시적으로 닫지 않는 경우 상태는 중간 시스템 및 엔드포인트에서 할당된 상태를 유지하고 다른 연결에서 SNAT 포트를 사용할 수 없게 만듭니다. 이 패턴은 애플리케이션 오류 및 SNAT 고갈이 트리거할 수 있습니다. 
- 영향에 대한 전문 지식이 없으면 OS 수준 TCP 닫기 관련 타이머 값을 변경하지 마세요. TCP 스택이 복구되는 동안 연결의 엔드포인트에 예상과 다른 부분이 있으면 애플리케이션 성능에 부정적인 영향을 미칠 수 있습니다. 타이머를 변경하고 싶다는 생각이 들면 일반적으로 디자인에 근본적인 문제가 있다는 신호입니다. 다음 권장 사항을 검토하세요.

기본 애플리케이션의 다른 안티패턴 때문에 SNAT 고갈이 더 심해질 수 있습니다. 다음 추가 패턴 및 모범 사례를 검토하여 서비스의 규모와 안정성을 개선하세요.

- SNAT 포트 인벤토리를 해제하기 위해 기본 유휴 시간 제한(4분)을 포함하여 [TCP 유휴 시간 제한](nat-gateway-resource.md#timers)을 감소시켜 값을 낮출 수 있는 영향을 살펴봅니다.
- [비동기 폴링 패턴](/azure/architecture/patterns/async-request-reply)을 장기 실행 작업에 사용하여 다른 작업에 대한 연결 리소스를 확보하는 것이 좋습니다.
- 중간 시스템의 시간이 초과되지 않도록 방지하기 위해 수명이 긴 흐름(예: 다시 사용되는 TCP 연결)에서 TCP keepalive 또는 애플리케이션 계층 keepalive를 사용해야 합니다. 유휴 시간 제한을 늘리는 것은 최후의 수단이며 근본 원인을 해결하지 못할 수 있습니다. 시간 제한이 길면 시간 제한이 만료될 때 오류 시간이 짧아지고 시간 지연과 불필요한 오류가 발생할 수 있습니다.
- 일시적 실패 또는 오류 복구 중에 공격적인 다시 시도/버스트를 방지하기 위해 정상적인 [다시 시도 패턴](/azure/architecture/patterns/retry)을 사용해야 합니다.
모든 HTTP 작업에 대한 새 TCP 연결("원자성 연결"이라고도 함)을 만드는 것은 안티패턴입니다.  원자성 연결은 애플리케이션의 크기가 효율적으로 조정되지 않고 리소스가 낭비되지 않도록 합니다.  항상 여러 작업을 동일한 연결로 파이프라인합니다.  애플리케이션은 트랜잭션 속도 및 리소스 비용에서 이점을 활용할 수 있습니다.  애플리케이션에서 전송 계층 암호화(예: TLS)를 사용하는 경우 새 연결 처리와 관련된 상당한 비용이 발생합니다.  추가 모범 사례 패턴은 [Azure 클라우드 디자인 패턴](/azure/architecture/patterns/)을 검토하세요.

#### <a name="additional-possible-mitigations"></a>가능한 추가 완화 조치

_**해결 방법:**_ 다음과 같이 아웃바운드 연결의 크기를 조정합니다.

| 시나리오 | 증명 정보 |완화 방법 |
|---|---|---|
| 사용량이 많은 기간 동안 SNAT 포트 및 SNAT 포트 소모에 대한 경합이 발생하고 있습니다. | Azure Monitor의 SNAT 연결 [메트릭](nat-metrics.md)에 대한 "실패" 범주는 시간이 지남에 따른 일시적 또는 지속적 실패와 많은 연결 볼륨을 보여 줍니다.  | 추가 공용 IP 주소 리소스 또는 공용 IP 접두사 리소스를 추가할 수 있는지 확인합니다. 이렇게 추가하면 최대 16개의 IP 주소를 NAT 게이트웨이에 사용할 수 있습니다. 이 추가 기능을 통해 사용 가능한 SNAT 포트에 대한 더 많은 인벤토리(IP 주소당 64,000개)가 제공되며 시나리오를 더 확장할 수 있습니다.|
| 16개의 IP 주소를 이미 제공했지만 여전히 SNAT 포트 소모가 발생하고 있습니다. | 추가 IP 주소를 추가하려는 시도가 실패합니다. 공용 IP 주소 리소스 또는 공용 IP 접두사 리소스의 총 IP 주소 수가 총 16개를 초과합니다. | 애플리케이션 환경을 여러 서브넷에 분산하고, 각 서브넷에 대한 NAT 게이트웨이 리소스를 제공합니다.  디자인 패턴을 다시 평가하여 이전 [지침](#design-patterns)에 따라 최적화합니다. |

>[!NOTE]
>SNAT 소모가 발생하는 이유를 이해하는 것이 중요합니다. 확장 가능하고 안정적인 시나리오에 적합한 패턴을 사용하고 있는지 확인하세요.  요구의 원인을 이해하지 않고 SNAT 포트를 시나리오에 추가하는 것은 최후의 수단이어야 합니다. 시나리오에서 SNAT 포트 인벤토리를 압박하는 이유를 이해할 수 없는 경우 더 많은 IP 주소를 추가하여 SNAT 포트를 인벤토리에 추가하면 애플리케이션 크기 조정과 동일한 소진 오류가 지연될 뿐입니다.  다른 비효율성 및 안티패턴을 마스킹할 수 있습니다.

### <a name="icmp-ping-is-failing"></a>ICMP ping이 실패함

[Virtual Network NAT](nat-overview.md)는 IPv4 UDP 및 TCP 프로토콜을 지원합니다. ICMP는 지원되지 않으며 실패할 것으로 예상됩니다.  

_**해결 방법:**_ 대신 TCP 연결 테스트(예: "TCP ping") 및 UDP별 애플리케이션 계층 테스트를 사용하여 엔드투엔드 연결의 유효성을 검사합니다.

다음 표는 테스트를 시작하는 데 사용할 수 있는 도구의 시작 시점으로 사용할 수 있습니다.

| 운영 체제 | 제네릭 TCP 연결 테스트 | TCP 애플리케이션 계층 테스트 | UDP |
|---|---|---|---|
| Linux | nc(제네릭 연결 테스트) | curl(TCP 애플리케이션 계층 테스트) | 애플리케이션별 |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 애플리케이션별 |

### <a name="connectivity-failures"></a>연결 실패

[Virtual Network NAT](nat-overview.md)의 연결 문제는 다음과 같은 여러 가지 이슈로 인해 발생할 수 있습니다.

* 구성 실수로 인한 영구 오류입니다.
* NAT 게이트웨이의 일시적 또는 영구적 [SNAT 소모](#snat-exhaustion)
* Azure 인프라의 일시적 실패 
* Azure 및 퍼블릭 인터넷 대상 간 경로의 일시적 실패 
* 퍼블릭 인터넷 대상의 일시적 또는 지속적 실패

다음과 같은 도구를 사용하여 연결 상태의 유효성을 검사합니다. [ICMP ping은 지원되지 않습니다](#icmp-ping-is-failing).

| 운영 체제 | 제네릭 TCP 연결 테스트 | TCP 애플리케이션 계층 테스트 | UDP |
|---|---|---|---|
| Linux | nc(제네릭 연결 테스트) | curl(TCP 애플리케이션 계층 테스트) | 애플리케이션별 |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 애플리케이션별 |

#### <a name="configuration"></a>구성

구성 확인:
1. NAT 게이트웨이 리소스에 하나 이상의 공용 IP 리소스 또는 하나의 공용 IP 접두사 리소스가 있나요? 아웃바운드 연결을 제공할 수 있으려면 NAT 게이트웨이와 연결된 IP 주소가 하나 이상 있어야 합니다.
2. 가상 네트워크의 서브넷이 NAT 게이트웨이를 사용하도록 구성되어 있나요?
3. UDR(사용자 정의 경로)을 사용하고 있으며 대상을 재정의하고 있나요?  NAT 게이트웨이 리소스는 구성된 서브넷에서 기본 경로(0/0)가 됩니다.

#### <a name="snat-exhaustion"></a>SNAT 소모

이 문서의 [SNAT 소모](#snat-exhaustion) 섹션을 검토하세요.

#### <a name="azure-infrastructure"></a>Azure 인프라

Azure는 인프라를 모니터링하면서 매우 세심하게 운영합니다. 일시적인 오류가 발생할 수 있으며, 전송되는 데이터가 손실되지 않는다는 보장은 없습니다.  TCP 애플리케이션에 SYN 재전송을 허용하는 디자인 패턴을 사용합니다. 손실된 SYN 패킷으로 인한 일시적인 영향을 줄일 수 있도록 TCP SYN 재전송을 허용할 만큼 충분한 연결 시간 제한을 사용합니다.

_**해결 방법:**_

* [SNAT 소모](#snat-exhaustion)를 확인합니다.
* SYN 재전송 동작을 제어하는 TCP 스택의 구성 매개 변수를 RTO([재전송 시간 제한](https://tools.ietf.org/html/rfc793))라고 합니다. RTO 값은 기본적으로 지수 백오프를 사용하여 조정할 수 있지만, 일반적으로 1초 이상입니다.  애플리케이션의 연결 시간 제한이 너무 짧으면(예: 1초) 간헐적인 연결 시간 제한이 표시될 수 있습니다.  애플리케이션 연결 시간 제한을 늘립니다.
* 기본 애플리케이션 동작에서 예기치 않은 더 오랜 시간 제한이 발생하는 경우 추가 문제 해결을 위한 지원 사례를 엽니다.

TCP 연결 시간 제한을 인위적으로 줄이거나 RTO 매개 변수를 튜닝하지 않는 것이 좋습니다.

#### <a name="public-internet-transit"></a>퍼블릭 인터넷 전송

대상에 대한 경로가 길어지고 중간 시스템이 많아질수록 일시적 실패의 가능성이 커집니다. 이 경우 [Azure 인프라](#azure-infrastructure)를 통한 일시적 실패의 빈도가 증가할 수 있습니다. 

이전 [Azure 인프라](#azure-infrastructure) 섹션과 동일한 지침을 따릅니다.

#### <a name="internet-endpoint"></a>인터넷 엔드포인트

이전 섹션은 통신이 설정된 인터넷 엔드포인트와 함께 적용됩니다. 연결 성공에 영향을 줄 수 있는 다른 요인은 다음과 같습니다.

* 다음을 포함하여 대상 쪽의 트래픽 관리
- 대상 쪽에서 적용한 API 속도 제한
- 대규모 DDoS 완화 또는 전송 계층 트래픽 셰이핑
* 대상의 방화벽 또는 기타 구성 요소 

현재 발생 중인 작업을 확인하려면 일반적으로 원본 및 대상에서 패킷을 캡처해야 합니다(사용 가능한 경우).

_**해결 방법:**_

* [SNAT 소모](#snat-exhaustion)를 확인합니다. 
* 동일한 지역 또는 다른 지역의 엔드포인트에 대한 연결의 유효성을 검사하여 비교합니다.  
* 대량 또는 트랜잭션 속도 테스트를 만드는 경우 속도를 낮추면 실패 발생 빈도가 줄어드는지 살펴봅니다.
* 속도 변경이 실패율에 영향을 주는 경우 대상 쪽의 API 속도 제한 또는 에서 다른 제약 조건에 도달했는지 확인합니다.
* 조사 결과가 충분하지 않은 경우 추가 문제 해결을 위한 지원 사례를 엽니다.

#### <a name="tcp-resets-received"></a>TCP 다시 설정이 수신됨

NAT 게이트웨이는 진행 중인 것으로 인식되지 않는 트래픽의 원본 VM에 대해 TCP 재설정을 생성합니다.

한 가지 가능한 이유는 TCP 연결의 유휴 시간이 초과되었기 때문입니다.  유휴 시간 제한은 4분에서 최대 120분까지 조정할 수 있습니다.

NAT 게이트웨이 리소스의 퍼블릭 쪽에는 TCP 재설정이 생성되지 않습니다. 대상 쪽의 TCP 재설정은 NAT 게이트웨이 리소스가 아닌 원본 VM에서 생성합니다.

_**해결 방법:**_

* [디자인 패턴](#design-patterns) 추천 사항을 검토합니다.  
* 필요한 경우 추가 문제 해결을 위한 지원 사례를 엽니다.

### <a name="ipv6-coexistence"></a>IPv6 공존

[Virtual Network NAT](nat-overview.md)는 IPv4 UDP 및 TCP 프로토콜을 지원합니다. NAT는 IPv6 공용 IP 주소 또는 IPv6 공용 IP 접두사에 연결할 수 없습니다. 그러나 NAT는 이중 스택 서브넷에 배포할 수 있습니다.

_**해결 방법:**_ 이중 스택 서브넷에 NAT 게이트웨이를 배포 합니다.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>NAT 게이트웨이 IP에서 연결이 시작되지 않음

NAT 게이트웨이, 사용할 IP 주소 및 NAT 게이트웨이 리소스를 사용해야 하는 서브넷을 구성합니다. 그러나 NAT 게이트웨이가 배포되기 전에 있던 가상 머신 인스턴스에서의 연결은 IP 주소를 사용하지 않습니다.  NAT 게이트웨이 리소스와 함께 사용되지 않는 IP 주소를 사용하는 것으로 나타납니다.

_**해결 방법:**_

[Virtual Network NAT](nat-overview.md)는 구성된 서브넷에 대한 아웃바운드 연결을 대체합니다. 기본 SNAT 또는 부하 분산 장치 아웃바운드 SNAT에서 NAT 게이트웨이를 사용하도록 전환하는 경우 NAT 게이트웨이 리소스와 연결된 IP 주소를 사용하여 새 연결이 즉시 시작됩니다.  그러나 NAT 게이트웨이 리소스로 전환하는 동안 가상 머신에 설정된 연결이 있는 경우 연결이 설정되었을 때 할당된 이전 SNAT IP 주소를 사용하여 연결이 계속됩니다.  OS 또는 브라우저가 연결 풀에서 연결을 캐싱하기 때문에 이미 존재하는 연결을 다시 사용하는 대신 새 연결을 설정하고 있는지 확인합니다.  예를 들어 PowerShell에서 _curl_ 을 사용하는 경우 새 연결을 적용하려면 _-DisableKeepalive_ 매개 변수를 지정해야 합니다.  브라우저를 사용하는 경우에는 연결이 풀링될 수도 있습니다.

NAT 게이트웨이 리소스에 대한 서브넷을 구성하는 가상 머신을 다시 부팅할 필요는 없습니다.  그러나 가상 머신이 다시 부팅되면 연결 상태가 플러시됩니다.  연결 상태가 플러시된 경우 모든 연결은 NAT 게이트웨이 리소스의 IP 주소를 사용하여 시작됩니다.  그러나 이는 가상 머신이 다시 부팅될 때의 부작용이므로, 다시 부팅이 필수라는 뜻은 아닙니다.

여전히 문제가 발생하는 경우 지원 사례를 열어 추가 문제 해결을 확인하세요.

## <a name="next-steps"></a>다음 단계

* [Virtual Network NAT](nat-overview.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스](nat-gateway-resource.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스에 대한 메트릭 및 경고](nat-metrics.md)에 대해 알아봅니다.
