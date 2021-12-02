---
title: Azure Load Balancer 및 가용성 영역
titleSuffix: Azure Load Balancer
description: 이 학습 경로를 통해 Azure 표준 Load Balancer 및 가용성 영역을 시작하세요.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: bc7a3728f630f3409019de373b9def24fdcab1c8
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436117"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer 및 가용성 영역

Azure Load Balancer는 가용성 영역 시나리오를 지원합니다. 표준 Load Balancer를 사용하여 리소스를 영역에 따라 정렬하고 영역에 배포하여 시나리오 전체에서 가용성을 높일 수 있습니다.  이러한 개념과 기본 시나리오 디자인 지침을 이해하려면 이 문서를 검토하세요.

Load Balancer는 **영역 중복, 영역** 또는 **비영역** 일 수 있습니다. 부하 분산 장치에 대한 영역 관련 속성(위에서 언급)을 구성하려면 필요한 적절한 유형의 프런트 엔드를 선택합니다.

## <a name="zone-redundant"></a>영역 중복

가용성 영역이 있는 지역에서 표준 Load Balancer는 영역 중복일 수 있습니다. 이 트래픽은 단일 IP 주소로 제공됩니다.

단일 프런트 엔드 IP 주소는 영역 실패 후에도 유지됩니다. 프런트 엔드 IP는 영역에 관계없이 영향을 받지 않은 모든 백 엔드 풀 구성원에 도달하는 데 사용할 수 있습니다. 하나 이상의 가용성 영역이 실패할 수 있으며, 지역의 한 영역이 정상으로 유지되는 동안 데이터 경로가 유지됩니다.

프런트 엔드의 IP 주소는 여러 가용성 영역의 여러 독립 인프라 배포를 통해 동시에 처리됩니다. 모든 다시 시도 또는 다시 설정은 영역 실패의 영향을 받지 않는 다른 영역에서 성공합니다.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*그림: 영역 중복 부하 분산 장치*

## <a name="zonal"></a>영역

프런트 엔드가 *영역* 이라고 하는 단일 영역에 대해 보장되도록 선택할 수 있습니다.  이 시나리오는 모든 인바운드 또는 아웃바운드 흐름이 한 지역의 단일 영역에서 처리됩니다.  프런트 엔드는 영역의 상태와 수명을 공유합니다.  데이터 경로는 보장된 영역 이외의 영역에서 오류가 발생해도 영향을 받지 않습니다. 영역 프런트 엔드를 사용하여 가용성 영역별 IP 주소를 공개할 수 있습니다.  

또한 각 영역 내에서 부하가 분산된 엔드포인트에 대해 영역 프런트 엔드를 직접 사용할 수 있습니다. 이 구성을 통해 부하 분산된 영역별 엔드포인트를 공개하여 각 영역을 개별적으로 모니터링할 수도 있습니다. 공용 엔드포인트의 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 같은 DNS 부하 분산 제품과 통합하고 단일 DNS 이름을 사용할 수 있습니다.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*그림: 영역 부하 분산 장치*

공용 부하 분산 장치 프런트 엔드의 경우 공용 IP에 **영역** 매개 변수를 추가합니다. 이 공용 IP는 해당 규칙에서 사용하는 프런트 엔드 IP 구성에서 참조됩니다.

내부 부하 분산 장치 프런트 엔드의 경우 내부 부하 분산 장치 프런트 엔드 IP 구성에 **zones** 매개 변수를 추가합니다. 영역 프런트 엔드는 서브넷의 IP 주소를 특정 영역에 대해 보장합니다.

## <a name="non-zonal"></a>비 영역

"비 영역" 프런트 엔드 (공용 IP 또는 공용 IP 접두사)를 사용 하 여 비 영역 구성에서 부하 분산 장치를 만들 수도 있습니다.  이 옵션은 중복성 보장을 제공 하지 않습니다. [업그레이드](../virtual-network/ip-services/public-ip-upgrade-portal.md) 된 모든 공용 IP 주소는 "영역 없음" 유형이 됩니다.

## <a name="design-considerations"></a><a name="design"></a> 디자인 고려 사항

이제 표준 Load Balancer의 영역 관련 속성을 파악했으므로 고가용성을 위해 디자인할 때 다음 디자인 고려 사항이 도움이 될 수 있습니다.

### <a name="tolerance-to-zone-failure"></a>영역 실패에 대한 내결함성

- **영역 중복** Load Balancer는 하나의 IP 주소가 있는 모든 영역에서 영역 리소스를 제공할 수 있습니다.  IP는 지역 내에서 하나 이상의 영역이 정상 상태로 유지되는 한 하나 이상의 영역 실패를 회복할 수 있습니다.
- **영역** 프런트 엔드는 서비스를 단일 영역으로 축소하고 각 영역과 수명을 공유합니다. 배포가 있는 영역이 다운되면 배포는 이 실패에서 회복할 수 없습니다.

프로덕션 워크 로드에 대해 영역 중복 Load Balancer를 사용 하는 것이 좋습니다.

### <a name="multiple-frontends"></a>여러 프런트 엔드

여러 프런트 엔드를 사용 하면 둘 이상의 포트 및/또는 IP 주소에 대 한 트래픽 부하를 분산할 수 있습니다.  아키텍처를 디자인할 때는 영역 중복성 및 여러 프런트 엔드가 상호 작용할 수 있는 방법을 고려 하는 것이 중요 합니다.  모든 프런트 엔드가 항상 오류에 대 한 복원 력을 갖도록 하려는 경우 프런트 엔드로 할당 된 모든 IP 주소는 영역 중복 이어야 합니다.   프런트 엔드 집합을 단일 영역에 연결 하려면 해당 집합의 모든 IP 주소를 해당 특정 영역과 연결 해야 합니다.  각 영역에 대 한 부하 분산 장치를 가질 필요는 없습니다. 대신 각 영역 프런트 엔드 (또는 영역 프런트 엔드 집합)를 해당 특정 가용성 영역에 속하는 백 엔드 풀의 가상 컴퓨터와 연결할 수 있습니다.

### <a name="transition-between-regional-zonal-models"></a>지역 영역 모델 간 전환

영역을 확장 하 여 [가용성 영역이](../availability-zones/az-overview.md) 영역을 유지 하지 않는 경우 아키텍처가 새로운 영역을 활용할 수 있도록 하기 위해 새 프런트 엔드 Ip를 만드는 것이 좋으며, 이러한 새 공용 Ip를 활용 하기 위해 적절 한 규칙 및 구성이 복제 됩니다.

### <a name="control-vs-data-plane-implications"></a>컨트롤 플레인 영향과 데이터 평면 영향

영역 중복은 비충돌 데이터 평면 또는 컨트롤 플레인을 의미하지 않습니다. 영역 중복 흐름은 모든 영역을 사용할 수 있으며 사용자 흐름은 지역의 모든 정상 영역을 사용합니다. 영역 실패 시 정상 영역을 사용하는 트래픽 흐름은 영향을 받지 않습니다.

영역 실패 시 영역을 사용하는 트래픽 흐름은 영향을 받을 수 있지만 애플리케이션은 복구할 수 있습니다. Azure가 영역 실패를 중심으로 수렴된 경우 트래픽은 다시 전송 시 지역 내의 정상 영역에서 계속됩니다.

실패 시나리오에 대한 애플리케이션의 복원력을 개선하기 위한 [Azure 클라우드 디자인 패턴](/azure/architecture/patterns/)을 검토합니다.

## <a name="limitations"></a>제한 사항

* 영역을 만든 후에는 리소스에 대해 변경, 업데이트 또는 만들 수 없습니다.
* 리소스는 영역에서 영역 중복으로 또는 생성 후에도 그 반대로 업데이트할 수 없습니다.

## <a name="next-steps"></a>다음 단계
- [가용성 영역](../availability-zones/az-overview.md)에 대해 자세히 알아보기
- [표준 Load Balancer](./load-balancer-overview.md)에 대해 자세히 알아봅니다.
- [영역 표준 Load Balancer를 사용하여 영역 내에 VM을 부하 분산](./quickstart-load-balancer-standard-public-cli.md)하는 방법에 대해 알아보기
- [영역 중복 표준 Load Balancer를 사용하여 영역에서 VM을 부하 분산](./quickstart-load-balancer-standard-public-cli.md)하는 방법에 대해 알아보기
- 실패 시나리오에 대한 애플리케이션의 복원력을 개선하기 위한 [Azure 클라우드 디자인 패턴](/azure/architecture/patterns/)에 대해 알아봅니다.
