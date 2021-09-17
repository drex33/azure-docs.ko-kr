---
title: Azure Load Balance에서 아웃바운드 연결 문제 해결
description: Azure Load Balancer를 통한 아웃바운드 연결의 일반적인 문제에 대한 해결 방법입니다.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: anavin
ms.openlocfilehash: e51a74ef4f01bda57e7cdef026aed9aa85111ca3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437403"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> 아웃바운드 연결 실패 문제 해결

이 문서는 Azure Load Balancer의 아웃바운드 연결에서 발생할 수 있는 일반적인 문제에 대한 해결 방법을 제공하기 위한 것입니다. 고객이 경험하는 대부분의 아웃바운드 연결 문제는 SNAT(원본 네트워크 주소 변환) 포트 소모 및 연결 시간 초과로 인해 패킷이 삭제되기 때문입니다. 이 문서에서는 이러한 각 문제를 완화하는 단계를 제공합니다.

## <a name="avoid-snat"></a>SNAT 피하기

SNAT 포트를 모두 소모하지 않도록 하는 가장 좋은 방법은 가능하면 우선 SNAT의 필요성을 제거하는 것입니다. 경우에 따라 이러한 공간 확보가 불가능할 수 있습니다. 예를 들어 공개 엔드포인트에 연결할 때. 그러나 어떤 경우에는 이것이 가능하며 리소스에 비공개로 연결하여 달성할 수 있습니다. Storage, SQL, Cosmos DB 또는 기타 [여기에 나열된 Azure 서비스](../private-link/availability.md)와 같은 Azure 서비스에 연결하는 경우 Azure Private Link를 활용하면 SNAT이 필요하지 않습니다. 결과적으로 SNAT 포트 고갈로 인한 잠재적인 연결 문제의 위험이 없습니다.

프라이빗 링크 서비스는 Snowflake, MongoDB, Confluent, Elastic 및 기타 서비스에서도 지원됩니다.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> SNAT(PAT) 포트 고갈 관리
[PAT](load-balancer-outbound-connections.md)에 사용되는 [삭제 포트](load-balancer-outbound-connections.md)는 [공용 IP 주소가 없는 독립 실행형 VM](load-balancer-outbound-connections.md) 및 [공용 IP 주소가 없는 부하 분산 VM](load-balancer-outbound-connections.md)에 설명된 것처럼 고갈될 수 있는 리소스입니다. [이](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) 가이드를 사용하여 임시 포트의 사용을 모니터링하고 현재 할당과 비교하여 SNAT 소모의 위험을 확인하거나 SNAT 소모를 확인할 수 있습니다.

동일한 대상 IP 주소 및 포트에 대해 많은 아웃바운드 TCP 또는 UDP 연결을 시작할 것인지 알고 있는 경우 실패하는 아웃바운드 연결을 확인하고, 지원 서비스에서 SNAT 포트([PAT](load-balancer-outbound-connections.md)에서 사용하는 미리 할당된 [삭제 포트](load-balancer-outbound-connections.md#preallocatedports))가 고갈될 것이라는 알림을 받는 경우 몇 가지 일반적인 완화 옵션을 사용할 수 있습니다. 다음 옵션을 검토하고 시나리오에 가장 적합한 옵션을 결정합니다. 한 가지 이상의 옵션이 이 시나리오를 관리하는 데 도움이 될 수 있습니다.

아웃바운드 연결 동작을 이해하는 데 어려움이 있는 경우 IP 스택 통계(netstat)를 사용할 수 있습니다. 또는 패킷 캡처를 사용하여 연결 동작을 관찰하면 도움이 될 수 있습니다. 이러한 패킷 캡처는 인스턴스의 게스트 OS에서 수행할 수도 있고 [패킷 캡처용 Network Watcher](../network-watcher/network-watcher-packet-capture-manage-portal.md)를 사용할 수도 있습니다. 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>수동으로 SNAT 포트를 할당하여 VM당 SNAT 포트 최대화
[미리 할당된 포트](load-balancer-outbound-connections.md#preallocatedports)에 정의된 대로 부하 분산 장치는 백 엔드의 VM 수에 따라 포트를 자동으로 할당합니다. 기본적으로 이 작업은 확장성을 보장하기 위해 신중하게 수행됩니다. 백 엔드에 포함할 최대 VM 수를 알고 있는 경우 각 아웃바운드 규칙에서 SNAT 포트를 수동으로 할당할 수 있습니다. 예를 들어 최대 10개의 VM이 있는 경우 기본 1024가 아닌 VM당 6400개의 SNAT 포트를 할당할 수 있습니다. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>연결을 다시 사용하도록 애플리케이션 수정 
애플리케이션에서 연결을 다시 사용하여 SNAT에서 사용되는 사용 후 삭제 포트에 대한 수요를 줄일 수 있습니다. 연결 재사용은 특히 연결 재사용이 기본값인 HTTP/1.1과 같은 프로토콜과 관련이 있습니다. 또한 전송으로 HTTP를 사용하는 다른 프로토콜(예: REST)도 이점을 얻을 수 있습니다. 

재사용은 언제나 각 요청에 대한 개별 원자성 TCP 연결보다 좋은 방법입니다. 재사용은 TCP 트랜잭션의 성능과 효율성을 높입니다.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>연결 풀링을 사용하도록 애플리케이션 수정
애플리케이션에서 연결 풀링 체계를 사용할 수 있습니다. 이 경우 요청이 고정된 연결 집합에서 내부적으로 분산됩니다(가능한 경우 각 연결이다 시 사용됨). 이 체계는 사용 중인 삭제 포트의 수를 제한하고 보다 예측 가능한 환경을 구축합니다. 또한 단일 연결이 작업에 회신하느라 사용 가능하지 않을 때 여러 동시 작업을 허용하여 요청 처리량을 늘릴 수 있습니다.  

연결 풀링은 애플리케이션을 개발하는 데 사용하는 프레임워크 또는 애플리케이션에 대한 구성 설정에 이미 존재할 수 있습니다. 연결 풀링을 연결 재사용과 결합할 수 있습니다. 그러면 여러 요청이 동일한 대상 IP 주소 및 포트에 고정되고 예측 가능한 수의 포트를 사용합니다. 또한 TCP 트랜잭션을 효율적으로 사용하여 대기 시간 및 리소스 사용량이 감소하기 때문에 요청에도 이득입니다. 여러 UDP 흐름을 관리하면 고갈 조건을 피하고 SNAT 포트 활용도를 관리할 수 있으므로 UDP 트랜잭션이 도움이 될 수 있습니다.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>덜 적극적인 재시도 논리를 사용하도록 애플리케이션 수정
[PAT](load-balancer-outbound-connections.md)에 사용되는 [미리 할당된 삭제 포트](load-balancer-outbound-connections.md#preallocatedports)가 고갈되거나 애플리케이션 오류가 발생하면 지연 및 백오프 논리 없는 적극적인 또는 무차별 대입 재시도로 인해 고갈 상태가 발생하거나 지속됩니다. 덜 적극적인 재시도 논리를 사용하여 사용 후 삭제 포트에 대한 수요를 줄일 수 있습니다. 

삭제 포트의 유휴 시간 제한은 4분입니다(조정 불가능). 다시 시도 횟수가 너무 엄격하면 고갈이 스스로 지울 수 있는 기회가 없습니다. 따라서 디자인할 때 애플리케이션이 트랜잭션을 어떤 방식으로 얼마나 자주 다시 시도하는지 고려하는 것이 중요합니다.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>각 VM에 공용 IP 할당
공용 IP 주소를 할당하면 시나리오가 [VM에 대한 공용 IP](load-balancer-outbound-connections.md)로 변경됩니다. 각 VM에 사용되는 공용 IP의 모든 삭제 포트를 VM에 사용할 수 있습니다. (공용 IP의 삭제 포트가 해당 백 엔드 풀과 연결된 모든 VM과 공유되는 시나리오와는 반대입니다.) 공용 IP 주소의 추가 비용 및 많은 수의 개별 IP 주소를 필터링할 때 나타날 수 있는 잠재적 영향 등이 상충되는 고려 사항일 수 있습니다.

>[!NOTE] 
>이 옵션은 웹 작업자 역할에는 사용할 수 없습니다.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>여러 프런트 엔드 사용
공용 표준 Load Balancer를 사용하는 경우 [아웃바운드 연결에 대한 여러 프런트 엔드 IP 주소](load-balancer-outbound-connections.md)를 할당하고 [사용 가능한 SNAT 포트의 수를 곱합니다](load-balancer-outbound-connections.md#preallocatedports).  프런트 엔드의 공용 IP에 대한 SNAT의 프로그래밍을 트리거하려면 프런트 엔드 IP 구성, 규칙 및 백 엔드 풀을 만듭니다.  규칙은 작동할 필요가 없으며 상태 프로브는 성공할 필요가 없습니다.  인바운드에 대해서도 여러 프런트 엔드를 사용하는 경우(아웃바운드 외에) 안정성을 유지하기 위해 사용자 지정 상태 프로브를 사용해야 합니다.

>[!NOTE]
>대부분의 경우에서 SNAT 포트 소모는 잘못된 디자인의 징후입니다.  더 많은 프런트 엔드를 사용하기 전에 SNAT 포트를 추가하기 위해 포트를 소진하는 이유를 이해해야 합니다.  나중에 오류가 발생할 수 있는 문제를 감출 수 있습니다.

## <a name="scale-out"></a><a name="scaleout"></a>규모 확장
[미리 할당된 포트](load-balancer-outbound-connections.md#preallocatedports)는 백 엔드 풀 크기를 기준으로 할당되며, 그다음으로 큰 백 엔드 풀 크기 계층을 수용하기 위해 일부 포트를 다시 할당해야 하는 경우, 중단 시간을 최소화하기 위해 계층으로 그룹화됩니다.  백 엔드 풀을 지정된 계층의 최대 크기로 확장하여 지정된 프런트 엔드에 대한 SNAT 포트 사용률을 높일 수 있습니다.  애플리케이션이 SNAT 소모 위험 없이 효율적으로 스케일 아웃하려면 기본 포트 할당이 필요합니다.

예를 들어 백 엔드 풀에 있는 2개의 가상 머신에서는 IP 구성당 1024개의 SNAT 포트를 사용할 수 있어 총 2048개의 SNAT 포트를 배포할 수 있습니다.  배포를 50개 가상 머신으로 늘리는 경우, 가상 머신당 미리 할당된 포트 수가 일정한 경우에도 총 51,200(50x1024)개의 SNAT 포트를 사용할 수 있습니다.  배포를 스케일 아웃하려는 경우, 계층당 [미리 할당된 포트](load-balancer-outbound-connections.md#preallocatedports) 수를 확인하여 해당 계층의 최댓값까지 확장되도록 합니다.  앞의 예제에서 50개가 아닌 51개의 인스턴스로 확장하도록 선택한 경우, 다음 계층으로 진행하며 합계뿐 아니라 VM당 SNAT 포트 수도 줄어듭니다.

그다음으로 큰 백 엔드 풀 크기 계층으로 확장하는 경우, 할당된 포트를 다시 할당해야 하면 잠재적으로 일부 아웃바운드 연결이 시간 초과될 수 있습니다.  SNAT 포트 중 일부만 사용하는 경우 그다음으로 큰 백 엔드 풀 크기로 확장하는 것이 중요하지 않습니다.  기존 포트 절반은 다음 백 앤드 풀 계층으로 이동할 때마다 다시 할당됩니다.  이러한 경우를 방지하려면 배포를 계층 크기에 맞게 조정해야 합니다.  또는 애플리케이션이 필요에 따라 검색하고 다시 시도할 수 있게 합니다.  TCP Keepalive는 다시 할당되어 SNAT 포트가 더 이상 작동하지 않는 경우의 검색을 지원할 수 있습니다.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>keepalive를 사용하여 아웃바운드 유휴 시간 제한 다시 설정
아웃바운드 연결에는 4분의 유휴 시간 제한이 적용됩니다. 이 시간 제한은 [아웃바운드 규칙](outbound-rules.md)을 통해 조정할 수 있습니다. 전송(예: TCP keepalive) 또는 애플리케이션 계층 keepalive를 사용하여 유휴 흐름을 새로 고치고, 필요한 경우 이 유휴 시간 제한을 다시 설정할 수도 있습니다.  

TCP Keepalive를 사용하는 경우 연결의 한 쪽에서 사용하도록 설정하는 것으로 충분합니다. 예를 들어 서버 쪽에서 흐름의 유휴 타이머를 다시 설정하기만 하면 충분하며 양쪽 모두에서 TCP keepalives를 시작할 필요는 없습니다.  데이터베이스 클라이언트 서버 구성을 포함하여 애플리케이션 계층에 대한 유사한 개념이 있습니다.  서버 쪽에서 사용 가능한 애플리케이션 관련 Keepalive 옵션을 확인합니다.

## <a name="next-steps"></a>다음 단계
Microsoft는 항상 고객의 환경을 향상시키기 위해 노력하고 있습니다. 이 문서에 나열되지 않았거나 해결되지 않은 아웃바운드 연결 문제가 있는 경우 이 페이지 하단의 GitHub를 통해 피드백을 제출하면 가능한 한 빨리 피드백을 처리하겠습니다.
