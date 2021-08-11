---
title: Azure Traffic Manager - 트래픽 라우팅 방법
description: 이 문서는 Traffic Manager에서 사용하는 다양한 트래픽 라우팅 방법을 이해하는 데 도움이 됩니다.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730975"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager 라우팅 방법

Azure Traffic Manager는 다양한 서비스 엔드포인트에 네트워크 트래픽을 라우팅하는 방법을 결정하는 6가지 트래픽 라우팅 방법을 지원합니다. 모든 프로필에 대해, Traffic Manager는 관련된 트래픽 라우팅 메서드를 수신한 각 DNS 쿼리에 적용합니다. 트래픽 라우팅 메서드는 DNS 응답에서 반환된 엔드포인트를 결정합니다.

Traffic Manager에서 다음과 같은 트래픽 라우팅 방법을 사용할 수 있습니다.

* **[우선 순위](#priority-traffic-routing-method):** 모든 트래픽에 대한 기본 서비스 엔드포인트를 유지하려면 **우선 순위** 라우팅을 선택합니다. 기본 또는 백업 엔드포인트 중 하나를 사용할 수 없는 경우 여러 백업 엔드포인트를 제공할 수 있습니다.
* **[가중](#weighted):** 가중치에 따라 엔드포인트 세트에 트래픽을 분산하려는 경우 **가중** 라우팅을 선택합니다. 모든 엔드포인트에 균일하게 분산하려면 가중치를 동일하게 설정합니다.
* **[성능](#performance):** 엔드포인트가 서로 다른 지역에 있고 최종 사용자가 가장 짧은 네트워크 대기 시간을 기준으로 "가장 가까운" 엔드포인트를 사용하게 하려는 경우 **성능** 라우팅을 선택합니다.
* **[지리적](#geographic):** DNS 쿼리가 지리적으로 시작된 위치에 따라 특정 엔드포인트(Azure, 외부 또는 중첩)로 사용자를 보내려면 **지리적** 라우팅을 선택합니다. 이 라우팅 방법을 사용하면 데이터 주권 위임, 콘텐츠 및 사용자 환경 지역화, 다른 지역의 트래픽 측정과 같은 시나리오에서 규정을 준수할 수 있습니다.
* **[다중값](#multivalue):** 엔드포인트로 IPv4/IPv6 주소만 사용할 수 있는 Traffic Manager 프로필의 경우 **다중값** 을 선택합니다. 이 프로필에 대해 쿼리가 수신되면 정상 상태의 모든 엔드포인트가 반환됩니다.
* **[서브넷](#subnet):** 최종 사용자 IP 주소 범위 집합을 특정 엔드포인트로 매핑하려면 **서브넷** 트래픽 라우팅 방법을 선택합니다. 요청이 수신되면 해당 요청의 원본 IP 주소에 대해 매핑될 엔드포인트가 반환됩니다. 


모든 Traffic Manager 프로필에는 엔드포인트의 상태 모니터링 및 자동 장애 조치(failover)가 있습니다. 자세한 내용은 [Traffic Manager 엔드포인트 모니터링](traffic-manager-monitoring.md)을 참조하세요. Traffic Manager 프로필 내에서 한 번에 하나의 트래픽 라우팅 방법만 구성할 수 있습니다. 언제든지 프로필에 대해 다른 트래픽 라우팅 방법을 선택할 수 있습니다. 변경 내용은 가동 중지 시간 없이 1분 내에 적용됩니다. 중첩된 Traffic Manager 프로필을 사용하여 트래픽 라우팅 방법을 결합할 수 있습니다. 중첩 프로필을 통해 더 크고 복잡한 애플리케이션의 요구 사항을 충족하는 정교하고 유연한 트래픽 라우팅 구성이 가능합니다. 자세한 내용은 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

## <a name="priority-traffic-routing-method"></a>우선 순위 트래픽 라우팅 방법

조직에서 해당 서비스의 안정성을 제공하려는 경우가 많습니다. 이렇게 하려면 기본 서비스가 중단된 경우를 대비하여 하나 이상의 백업 서비스를 배포합니다. '우선 순위' 트래픽 라우팅 메서드를 사용하여 Azure 고객은 이러한 장애 조치(Failover) 패턴을 쉽게 구현할 수 있습니다.

![Azure Traffic Manager '우선 순위' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/priority.png)

Traffic Manager 프로필은 우선순위로 정렬된 서비스 엔드포인트 목록을 포함합니다. 기본적으로 Traffic Manager는 모든 트래픽을 기본(가장 높은 우선 순위) 엔드포인트로 전송합니다. 기본 엔드포인트를 사용할 수 없는 경우 Traffic Manager는 두 번째 엔드포인트에 트래픽을 라우팅합니다. 목록의 기본 엔드포인트 및 보조 엔드포인트를 모두 사용할 수 없는 경우 트래픽이 세 번째 엔드포인트 등으로 전송됩니다. 엔드포인트의 가용성은 구성된 상태(사용 또는 사용 안 함) 및 지속적인 엔드포인트 모니터링을 기반으로 합니다.

### <a name="configuring-endpoints"></a>엔드포인트 구성

Azure Resource Manager에서 각 엔드포인트에 대해 '우선 순위' 속성을 사용하여 엔드포인트 우선 순위를 명시적으로 구성합니다. 이 속성은 1에서 1000 사이의 값입니다. 값이 낮을수록 우선 순위가 높습니다. 엔드포인트는 우선 순위 값을 공유할 수 없습니다. 이 속성을 설정하는 작업은 선택 사항입니다. 생략하면 엔드포인트 순서에 따른 기본 우선 순위를 사용합니다.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>가중 트래픽 라우팅 방법
'가중' 트래픽 라우팅 메서드를 사용하면 균등하게 트래픽을 분산하거나 미리 정의된 가중치를 사용할 수 있습니다.

![Azure Traffic Manager '가중' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/weighted.png)

가중 트래픽 라우팅 메서드에서는 Traffic Manager 프로필 구성에서 각 엔드포인트에 가중치를 할당합니다. 가중치는 1에서 1000 사이의 정수입니다. 이 매개 변수는 선택 사항입니다. 생략되면 Traffic Manager는 '1'이라는 기본 가중치를 사용합니다. 가중치가 높을수록 우선 순위가 높아집니다.

Traffic Manager는 수신한 각 DNS 쿼리에 대해 사용 가능한 엔드포인트를 임의로 선택합니다. 엔드포인트를 선택하는 확률은 사용 가능한 모든 엔드포인트에 할당된 가중치를 기반으로 합니다. 모든 엔드포인트 결과에서 동일한 가중치를 사용하면 균등하게 트래픽이 분포됩니다. 특정 엔드포인트에 더 높은(또는 더 낮은) 가중치를 적용하면 해당 엔드포인트가 DNS 응답에서 더(또는 덜) 자주 반환됩니다.

가중 메서드를 사용하면 다음과 같은 몇 가지 유용한 시나리오를 사용할 수 있습니다.

* 점진적 애플리케이션 업그레이드: 새 엔드포인트로 라우팅할 트래픽의 백분율을 고려하면서 시간 경과에 따라 점진적으로 트래픽을 100%까지 늘립니다.
* Azure에 애플리케이션 마이그레이션: Azure 엔드포인트 및 외부 엔드포인트로 프로필을 만듭니다. 새 엔드포인트를 선호하도록 엔드포인트의 가중치를 조정합니다.
* 추가 용량을 위한 클라우드 버스트: Traffic Manager 프로필을 통해 온-프레미스 배포를 클라우드로 신속하게 확장합니다. 클라우드에 추가 용량이 필요한 경우 엔드포인트를 더 추가하거나 사용하도록 설정하고 각 엔드포인트로 전송되는 트래픽 양을 지정할 수 있습니다.

Azure Portal, Azure PowerShell, CLI 또는 REST API를 사용하여 가중치를 구성할 수 있습니다.

기억해야 할 점은 DNS 응답이 클라이언트에 의해 캐시된다는 것입니다. 클라이언트가 DNS 이름을 확인하는 데 사용하는 재귀 DNS 서버에서도 캐시됩니다. 이 캐싱은 가중 트래픽 분산에 영향을 줄 수 있습니다. 클라이언트 및 재귀 DNS 서버의 수가 큰 경우 트래픽 분산은 예상대로 작동합니다. 그러나 클라이언트 또는 재귀 DNS 서버의 수가 적을 경우 캐싱은 트래픽 배포를 상당히 왜곡시킬 수 있습니다.

일반 사용 사례는 다음과 같습니다.

* 개발 및 테스트 환경
* 애플리케이션 간 통신
* 일반적인 재귀 DNS 인프라를 공유하는 좁은 사용자 기반을 목표로 하는 애플리케이션(예: 프록시를 통해 연결하는 회사의 직원)

이러한 DNS 캐싱 효과는 Azure Traffic Manager만이 아니라 모든 DNS 기반 트래픽 라우팅 시스템에 공통적으로 적용됩니다. 경우에 따라 DNS 캐시를 명시적으로 지우면 문제가 해결될 수도 있습니다. 이렇게 해도 문제가 해결되지 않는 경우 대체 트래픽 라우팅 방법이 더 적합할 수ㄷㅎ 있습니다.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>성능 트래픽 라우팅 방법

전세계적으로 둘 이상의 위치에 엔드포인트를 배포하면 애플리케이션의 응답성을 향상시킬 수 있습니다. '성능' 트래픽 라우팅 방법을 사용하면 트래픽을 '가장 가까운' 위치로 라우팅할 수 있습니다.

![Azure Traffic Manager '성능' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/performance.png)

'가장 가까운' 엔드포인트가 반드시 지리적 거리를 기준으로 가장 가깝게 지정되는 것은 아닙니다. 대신, '성능' 트래픽 라우팅 메서드는 네트워크 대기 시간을 측정하여 가장 가까운 엔드포인트를 결정합니다. Traffic Manager에는 IP 주소 범위와 각 Azure 데이터 센터 간의 왕복 시간을 추적하는 인터넷 대기 시간 테이블이 있습니다.

Traffic Manager는 인터넷 대기 시간 테이블에서 들어오는 DNS 요청의 원본 IP 주소를 찾습니다. Traffic Manager는 해당 IP 주소 범위에 대한 가장 낮은 대기 시간을 Azure 데이터 센터에서 사용 가능한 엔드포인트를 선택합니다. 그런 다음, Traffic Manager DNS 응답에서 해당 엔드포인트를 반환합니다.

[Traffic Manager 작동 방식](traffic-manager-how-it-works.md)에 설명된 대로 Traffic Manager는 클라이언트에서 직접 DNS 쿼리를 수신하지 않습니다. 대신, DNS 쿼리는 클라이언트를 사용하도록 구성한 재귀 DNS 서비스에서 제공됩니다. 따라서 '가장 가까운' 엔드포인트를 결정하는 데 사용되는 IP 주소는 클라이언트의 IP 주소가 아니라 재귀 DNS 서비스의 IP 주소입니다. 이 IP 주소는 클라이언트에 유용한 프록시입니다.


Traffic Manager는 인터넷 대기 시간 테이블을 정기적으로 업데이트하여 전세계 인터넷 및 새 Azure 지역에서 변경 내용을 고려합니다. 그러나 애플리케이션 성능은 인터넷을 통한 부하에서 실시간 변형에 따라 달라 집니다. 성능 트래픽 라우팅은 지정된 서비스 엔드포인트에 대한 부하를 모니터링하지 않습니다. 엔드포인트를 사용할 수 없는 경우 Traffic Manager는 해당 엔드포인트를 DNS 쿼리 응답에 포함하지 않습니다.


주의할 사항:

* 프로필이 동일한 Azure 지역에서 여러 엔드포인트를 포함하는 경우 Traffic Manager는 트래픽을 해당 지역에서 사용할 수 있는 엔드포인트에 균등하게 분산합니다. 지역 내에서 다른 트래픽 분산을 원하는 경우 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용할 수 있습니다.
* 가장 가까운 Azure 지역에서 활성화된 모든 엔드포인트의 성능이 저하되면 Traffic Manager가 다음으로 가장 가까운 Azure 지역의 엔드포인트로 트래픽을 이동합니다. 기본 장애 조치 순서를 정의하려는 경우 [중첩된 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용합니다.
* 외부 엔드포인트 또는 중첩 엔드포인트에서 성능 트래픽 라우팅 방법을 사용하는 경우 해당 엔드포인트의 위치를 지정해야 합니다. 배포에 가장 가까운 Azure 지역을 선택합니다. 이러한 위치는 인터넷 대기 시간 테이블에서 지원되는 값입니다.
* 엔드포인트를 선택하는 알고리즘은 결정적입니다. 동일한 클라이언트에서 반복되는 DNS 쿼리는 동일한 엔드포인트로 전달됩니다. 일반적으로 클라이언트는 이동할 때 다른 재귀 DNS 서버를 사용합니다. 클라이언트는 다른 엔드포인트로 라우팅될 수 있습니다. 라우팅은 인터넷 대기 시간 테이블의 업데이트에 의해서도 영향을 받을 수 있습니다. 이때문에 성능 트래픽 라우팅 메서드는 클라이언트가 항상 동일한 엔드포인트로 라우팅되는 것을 보장하지 않는 것입니다.
* 인터넷 대기 시간 표가 변경될 경우 일부 클라이언트가 다른 엔드포인트로 보내진다는 것을 알 수 있습니다. 이 라우팅 변경 내용은 현재 대기 시간 데이터에 따라 더 정확합니다. 인터넷이 계속해서 진화함에 따라, 이러한 업데이트는 성능 트래픽 라우팅의 정확성을 유지하는 데 필수적입니다.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>지리적 트래픽 라우팅 방법

지리적 라우팅 메서드를 사용하도록 Traffic Manager 프로필을 구성하여 사용자가 해당 DNS 쿼리가 시작된 지리적 위치에 기반한 특정 엔드포인트(Azure, 외부 또는 중첩됨)에 지정됩니다. 이 라우팅 방법을 사용하면 데이터 주권 위임, 콘텐츠 및 사용자 환경 지역화, 다른 지역의 트래픽 측정에서 규정을 준수할 수 있습니다.
프로필이 지리적 라우팅에 구성된 경우 해당 프로필과 연결된 각 엔드포인트에는 거기에 할당된 지리적 지역이 있어야 합니다. 지리적 지역은 다음과 같은 수준으로 세분화될 수 있습니다. 
- 세계–모든 지역
- 지역 그룹화 - 예: 아프리카, 중동, 오스트레일리아/태평양 등 
- 국가/지역 - 예: 아일랜드, 페루, 홍콩 특별 행정구 등 
- 시/도 - 예: 미국-캘리포니아, 오스트레일리아-퀸즐랜드, 캐나다-앨버타 등(참고: 이 세분성 수준은 오스트레일리아, 캐나다 및 미국의 주/지방에서만 지원됨).

지역 또는 지역 집합이 엔드포인트에 할당된 경우 특정 지역의 요청은 해당 엔드포인트에만 라우트됩니다. Traffic Manager는 DNS 쿼리의 원본 IP 주소를 사용하여 사용자가 쿼리를 수행하는 지역을 확인합니다. 일반적으로 사용자를 대신하여 쿼리를 수행하는 로컬 DNS 해결 프로그램의 IP 주소입니다.  

![Azure Traffic Manager '지리적' 트래픽 라우팅 메서드](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager는 DNS 쿼리의 원본 IP 주소를 읽고 해당 사항이 발생하는 지리적 지역을 결정합니다. 그런 다음, 이 지리적 지역이 매핑되어 있는 엔드포인트가 있는지 확인합니다. 이 조회는 가장 낮은 세분성 수준(지원되는 경우 주/지방, 지원되지 않으면 국가/지역 수준)에서 시작되고 **세계** 라는 가장 높은 수준까지 진행됩니다. 이 통과를 사용해서 발견된 첫 번째 일치 항목이 쿼리 응답에 반환할 엔드포인트로 선택됩니다. 중첩 형식 엔드포인트와 일치하는 경우 해당 라우팅 방법을 기준으로 자식 프로필 내의 엔드포인트가 반환됩니다. 다음 사항을 이러한 동작에 사용할 수 있습니다.

- 지리적 지역은 라우팅 형식이 지리적 라우팅일 경우 Traffic Manager 프로필에서 엔드포인트에만 매핑될 수 있습니다. 이러한 제한 때문에 사용자 라우팅이 결정적이며 고객이 모호하지 않은 지리적 경계가 필요한 시나리오를 지원할 수 있습니다.
- 사용자 지역이 서로 다른 두 엔드포인트의 지리적 매핑에 해당하는 경우 Traffic Manager는 세분성이 가장 낮은 엔드포인트를 선택합니다. Traffic Manager는 해당 지역의 요청을 다른 엔드포인트로 라우팅하는 것을 고려하지 않습니다. 예를 들어 Endpoint1과 Endpoint2라는 두 개의 엔드포인트가 있는 지리적 라우팅 형식 프로필을 고려해 보세요. 엔드포인트1은 아일랜드에서 트래픽을 수신하도록 구성되고 엔드포인트2는 유럽에서 트래픽을 수신하도록 구성되어 있습니다. 요청이 아일랜드에서 시작되는 경우 항상 Endpoint1에 라우트됩니다.
- 지역이 한 엔드포인트에만 매핑될 수 있으므로 Traffic Manager는 엔드포인트가 정상인지 여부에 관계없이 응답을 반환합니다.

    >[!IMPORTANT]
    >지리적 라우팅 방법을 사용하는 고객은 각각에 두 개 이상의 엔드포인트가 포함된 자식 프로필이 있는 중첩 형식 엔드포인트와 연결하는 것이 좋습니다.
- 일치하는 엔드포인트가 있고 해당 엔드포인트가 **중지된** 상태이면 Traffic Manager는 NODATA 응답을 반환합니다. 이 경우 지리적 지역 계층 구조의 상위 수준에서 더 이상 조회가 수행되지 않습니다. 이 동작은 하위 프로필이 **중지됨** 또는 **비활성화** 상태인 경우 중첩 엔드포인트 유형에 적용됩니다.
- **사용 안 함** 상태로 표시되는 엔드포인트는 지역 일치 프로세스에 포함되지 않습니다. 이 동작은 엔드포인트가 **비활성화** 상태인 경우 중첩 엔드포인트 유형에도 적용됩니다.
- 쿼리가 해당 프로필에 매핑이 없는 지리적 지역에서 수행된 경우 Traffic Manager는 NODATA 응답을 반환합니다. 바로 이때문에 하나의 엔드포인트에서 지리적 라우팅을 사용하도록 강력히 권장됩니다. 자식 프로필 내에 **세계** 지역이 할당된 두 개 이상의 엔드포인트가 있는 중첩 유형에 대해 가장 이상적입니다. 이렇게 하면 지역에 매핑되지 않는 IP 주소도 처리됩니다.

[Traffic Manager 작동 방식](traffic-manager-how-it-works.md)에 설명된 대로 Traffic Manager는 클라이언트에서 직접 DNS 쿼리를 수신하지 않습니다. DNS 쿼리는 클라이언트를 사용하도록 구성한 재귀 DNS 서비스에서 제공됩니다. 이때문에 지역을 결정하는 데 사용되는 IP 주소는 클라이언트의 IP 주소가 아니라 재귀 DNS 서비스의 IP 주소인 것입니다. 이 IP 주소는 클라이언트에 유용한 프록시입니다.

### <a name="faqs"></a>FAQ

* [지리적 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [성능 라우팅 방법 또는 지리적 라우팅 방법을 사용해야 하는지를 결정하려면 어떻게 할까요?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [지리적 라우팅에 대해 Traffic Manager에서 지원되는 지역은 어디입니까?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Traffic Manager는 어떻게 사용자가 쿼리하는 위치를 결정합니까?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Traffic Manager가 모든 경우에 사용자의 정확한 지리적 위치를 올바르게 결정한다고 보장할 수 있나요?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [엔드포인트를 지리적 라우팅에 대해 구성된 것과 물리적으로 동일한 지역에 배치해야 하나요?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [지리적 라우팅을 수행하도록 구성되어 있지 않은 프로필의 엔드포인트에 지리적 지역을 할당할 수 있나요?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [기존 프로필의 라우팅 방법을 지리적으로 변경하려고 시도하면 오류가 발생하는 이유는 무엇인가요?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [고객에게 지리적 라우팅이 활성화된 프로필의 엔드포인트보다 중첩 프로필을 만드는 것을 권장하는 이유는 무엇입니까?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [이 라우팅 형식을 지원하는 API 버전에 제한이 있습니까?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>다중값 트래픽 라우팅 방법
**다중값** 트래픽 라우팅 방법을 사용하면 단일 DNS 쿼리 응답에 정상 상태의 여러 엔드포인트를 가져올 수 있습니다. 이 구성을 통해 호출자는 반환된 엔드포인트가 응답하지 않을 때 다른 엔드포인트와 클라이언트 쪽 재시도를 수행할 수 있습니다. 이 패턴은 서비스의 가용성을 향상시키고 새 DNS 쿼리와 관련된 대기 시간을 줄여 정상 상태의 엔드포인트를 얻을 수 있습니다. 다중값 라우팅 방법은 ‘외부’ 유형의 모든 엔드포인트가 IPv4 또는 IPv6 주소로 지정된 경우에만 작동합니다. 쿼리가 이 프로필에 대해 수신되면 정상 상태의 모든 엔드포인트가 반환되고, 구성 가능한 최대 반환 수 제한이 적용됩니다.

### <a name="faqs"></a>FAQ

* [다중값 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [다중값 라우팅이 사용될 때 얼마나 많은 엔드포인트가 반환되나요?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [다중값 라우팅이 사용될 때 동일한 엔드포인트 집합을 얻을 수 있나요?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>서브넷 트래픽 라우팅 방법
**서브넷** 트래픽 라우팅 방법을 사용하면 최종 사용자 IP 주소 범위 집합을 프로필의 특정 엔드포인트에 매핑할 수 있습니다. Traffic Manager는 해당 프로필에 대한 DNS 쿼리를 수신하면 해당 요청의 원본 IP 주소를 검사합니다. 그러면 매핑되는 엔드포인트를 확인한 후 쿼리 응답에서 해당 엔드포인트를 반환합니다. 대부분의 경우 원본 IP 주소는 호출자가 사용하는 DNS 확인자입니다.

엔드포인트에 매핑할 IP 주소를 CIDR 범위(예: 1.2.3.0/24) 또는 주소 범위(예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 엔드포인트와 연결된 IP 범위는 해당 프로필 내에서 고유해야 합니다. 주소 범위는 동일한 프로필에 있는 다른 엔드포인트의 IP 주소 세트와 겹치면 안 됩니다.
주소 범위가 없는 엔드포인트를 정의하는 경우 해당 엔드포인트는 대체(fallback)용으로 사용되어 남아 있는 서브넷에서 트래픽을 가져옵니다. 대체(fallback) 엔드포인트가 포함되지 않으면 Traffic Manager는 정의되지 않은 모든 범위에 대해 NODATA 응답을 보냅니다. 대체 엔드포인트를 정의하여 엔드포인트의 가능한 모든 IP 범위를 지정하려는 것이 좋습니다.

특정 IP 공간에서 연결하는 사용자에게 다른 환경을 제공하기 위해 서브넷 라우팅을 사용할 수 있습니다. 예를 들어, 회사 사무실의 모든 요청을 다른 엔드포인트로 라우팅할 수 있습니다. 이 라우팅 방법은 앱의 내부 전용 버전을 테스트하려는 경우에 특히 유용합니다. 또 다른 시나리오는 특정 ISP에서 연결하는 사용자에게 다른 환경을 제공하려는 경우입니다(예: 지정된 ISP의 사용자 차단).

### <a name="faqs"></a>FAQ

* [서브넷 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Traffic Manager는 최종 사용자의 IP 주소를 어떻게 알 수 있나요?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [서브넷 라우팅을 사용하는 경우 IP 주소를 어떻게 지정할 수 있나요?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [서브넷 라우팅을 사용하는 경우 대체(fallback) 엔드포인트를 어떻게 지정할 수 있나요?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [서브넷 라우팅 형식 프로필에서 엔드포인트가 비활성화된 경우에는 어떻게 되나요?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>다음 단계

[Traffic Manager 엔드포인트 모니터링](traffic-manager-monitoring.md)을 사용하여 고가용성 애플리케이션을 개발하는 방법을 알아봅니다.