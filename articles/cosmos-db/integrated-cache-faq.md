---
title: Azure Cosmos DB 통합 캐시 관련 질문과 대답
description: Azure Cosmos DB 통합 캐시에 대한 질문과 대답입니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: a8d1dc69ef64726f55dffd20221a19c3f3114964
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595145"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Azure Cosmos DB 통합 캐시 관련 질문과 대답
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 통합 캐시는 Azure Cosmos DB에 기본 제공되는 메모리 내 캐시입니다. 이 문서에서는 Azure Cosmos DB 통합 캐시에 대한 일반적인 질문과 대답을 제공합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>통합 캐시에 전용 게이트웨이가 필요한 이유는 무엇인가요?

게이트웨이 모드를 사용하여 Azure Cosmos DB에 연결하는 경우 표준 게이트웨이가 사용됩니다. Azure Cosmos DB 백 엔드(프로비전된 처리량 및 스토리지)는 컨테이너당 전용 용량이 있지만 표준 게이트웨이는 여러 고객 간에 공유됩니다. 많은 고객이 표준 게이트웨이를 공유하면 각 개별 고객이 소비하는 컴퓨팅 리소스가 최소화되므로 실용적입니다. 통합 캐시는 Azure Cosmos DB 계정으로 한정되며 상당한 CPU 및 메모리가 필요하기 때문에 전용 게이트웨이 노드가 필요합니다.

### <a name="what-is-a-dedicated-gateway"></a>전용 게이트웨이란 무엇인가요?

[전용 게이트웨이](dedicated-gateway.md)는 서버 쪽 컴퓨팅으로, Azure Cosmos DB 계정의 데이터에 대한 프런트 엔드입니다. 전용 게이트웨이 엔드포인트에 연결하면 애플리케이션에서 전용 게이트웨이로 요청을 보낸 다음, 요청을 여러 백 엔드 파티션으로 라우팅합니다.

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>전용 게이트웨이를 사용할 경우 표준 게이트웨이를 사용할 때와 다른 성능상 이점이 있나요?

일반적으로 전용 게이트웨이에서 라우팅하는 요청은 표준 게이트웨이에서 라우팅하는 요청보다 약간 더 짧고 일관된 대기 시간이 발생합니다. 통합 캐시를 사용하지 않는 요청도 표준 게이트웨이보다 대기 시간이 약간 짧습니다.

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>통합 캐시에 어떤 종류의 대기 시간을 예상할 수 있나요?

캐시된 데이터는 백 엔드가 아닌 전용 게이트웨이의 메모리 내에 저장되기 때문에 통합 캐시의 요청 처리 속도가 더 빠릅니다. 캐시된 지점 읽기의 경우 2~4ms의 대기 시간이 예상됩니다.

캐시된 쿼리의 경우 쿼리에 따라 대기 시간이 달라집니다. 쿼리 캐시는 특정 쿼리에 대한 쿼리 엔진의 응답을 캐시하는 방식으로 작동합니다. 그리고 이 응답은 처리를 위해 클라이언트 쪽에서 SDK로 다시 전송됩니다. 간단한 쿼리의 경우 SDK에서 최소한의 작업이 필요하며 2~4ms의 대기 시간이 일반적입니다. 그러나 `GROUP BY` 또는 `DISTINCT`가 포함된 더 복잡한 쿼리는 SDK에서 더 많은 처리가 필요하므로 쿼리 캐시를 사용하더라도 대기 시간이 더 길어질 수 있습니다.

이전에 직접 모드를 사용하여 Azure Cosmos DB 연결하고 전용 게이트웨이를 사용한 연결로 전환한 경우 일부 요청의 대기 시간이 약간 늘어날 수 있습니다. 게이트웨이 모드를 사용하려면 요청을 게이트웨이(이 경우 전용 게이트웨이)로 보낸 다음, 백 엔드로 적절히 라우팅해야 합니다. 직접 모드는 이름에서 알 수 있듯이, 클라이언트가 백 엔드와 직접 통신하여 추가 홉을 제거할 수 있도록 합니다. 

앱이 이전에 직접 모드를 사용한 경우, 통합 캐시의 대기 시간 이점은 다음 시나리오에서만 두드러집니다.

- 대용량 항목(>16KB)의 지점 읽기 대기 시간
- 높은 RU 또는 복잡한 쿼리

앱이 이전에 표준 게이트웨이를 사용하는 게이트웨이 모드를 사용한 경우에 통합 캐시를 사용하면 거의 모든 시나리오에서 대기 시간이 줄어듭니다. 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>Azure Cosmos DB 가용성 SLA가 전용 게이트웨이 및 통합 캐시로 확장되나요?

기능이 일반 공급되면 전용 게이트웨이(통합 캐시도)에 가용성 SLA/SLO가 있습니다. 고가용성이 필요한 시나리오의 경우, 필요한 전용 게이트웨이 인스턴스 수의 3배를 프로비전해야 합니다. 예를 들어 프로덕션에 하나의 전용 게이트웨이 노드가 필요한 경우, 가능한 가동 중지 시간 또는 중단을 고려하여 두 개의 추가 전용 게이트웨이 노드를 프로비전해야 합니다.

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>통합 캐시는 현재 SQL(Core) API에만 사용할 수 있습니다. 다른 API에 대해서도 릴리스할 계획이 있나요?

SQL API 너머로 통합 캐시를 확장하는 것은 장기 로드맵에 계획되어 있지만 통합 캐시의 초기 공개 미리 보기 이후로 예정되어 있습니다.

### <a name="what-consistency-does-the-integrated-cache-support"></a>통합 캐시는 어떤 일관성을 지원하나요?

통합 캐시는 세션 및 최종 일관성을 모두 지원합니다. 캐시된 데이터에 상한을 배치하는 선택적 [MaxIntegratedCacheStaleness](integrated-cache.md#maxintegratedcachestaleness)를 구성할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [통합 캐시](integrated-cache.md)
- [통합 캐시 구성](how-to-configure-integrated-cache.md)
- [전용 게이트웨이](dedicated-gateway.md)
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.