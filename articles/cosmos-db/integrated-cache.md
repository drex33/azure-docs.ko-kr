---
title: Azure Cosmos DB 통합 캐시
description: Azure Cosmos DB 통합 캐시는 요청 볼륨이 증가할 때 비용을 관리하고 짧은 대기 시간을 보장하는 데 도움이 되는 메모리 내 캐시입니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.openlocfilehash: 2642f1e85e12ce0251e9b7bfff84b5d468a342d2
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361408"
---
# <a name="azure-cosmos-db-integrated-cache---overview-preview"></a>Azure Cosmos DB 통합 캐시 - 개요(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 통합 캐시는 요청 볼륨이 증가할 때 비용을 관리하고 짧은 대기 시간을 보장하는 데 도움이 되는 메모리 내 캐시입니다. 통합 캐시는 쉽게 설정할 수 있으며 캐시 무효화를 위한 사용자 지정 코드를 작성하거나 백 엔드 인프라를 관리하는 데 시간을 낭비할 필요가 없습니다. 통합 캐시는 Azure Cosmos DB 계정 내에서 [전용 게이트웨이](dedicated-gateway.md)를 사용합니다. 통합 캐시는 성능 향상을 위해 전용 게이트웨이를 활용하는 여러 Azure Cosmos DB 기능 중 첫 번째 기능입니다. 워크로드에 필요한 코어 수 및 메모리에 따라 세 가지 전용 게이트웨이 크기 중에 선택할 수 있습니다.

통합 캐시는 전용 게이트웨이 내에서 자동으로 구성됩니다. 통합 캐시는 다음과 같은 두 부분으로 나뉩니다. 

* 지점 읽기를 위한 항목 캐시 
* 쿼리를 위한 쿼리 캐시

통합 캐시는 LRU(오래 전에 사용한 항목) 제거 정책을 사용하는 읽기-통과 쓰기-통과 캐시입니다. 항목 캐시와 쿼리 캐시는 통합 캐시 내에서 동일한 용량을 공유하며 LRU 제거 정책은 두 쿼리에 모두 적용됩니다. 즉, 지점 읽기인지 아니면 쿼리인지에 관계없이 오래 전에 사용한 항목을 기준으로 캐시에서 데이터가 제거됩니다.

> [!NOTE]
> 통합 캐시에 관한 피드백이 있나요? 많은 의견 부탁드립니다. 언제든지 자유롭게 Azure Cosmos DB 엔지니어링 팀(cosmoscachefeedback@microsoft.com)과 피드백을 직접 공유해 주세요.

## <a name="workloads-that-benefit-from-the-integrated-cache"></a>통합 캐시의 이점을 활용할 수 있는 워크로드

통합 캐시의 주요 목표는 읽기 작업이 많은 워크로드 비용을 절감하는 것입니다. Azure Cosmos DB는 이미 캐싱 없이 빠르기 때문에 짧은 대기 시간은 좋기는 하지만 통합 캐시의 주요 이점은 아닙니다.

통합 캐시에 도달한 지점 읽기 및 쿼리는 RU를 사용하지 않습니다. 즉, 캐시 적중 시에 RU 요금은 0입니다. 캐시 적중 시 백 엔드 데이터베이스에서 읽는 것보다 작업당 비용이 훨씬 낮습니다.

다음 특징에 해당하는 워크로드는 통합 캐시가 비용 절감에 도움이 되는지 평가해야 합니다.

-   읽기 작업이 많은 워크로드
-   많은 항목에서 지점 읽기가 반복
-   높은 RU 쿼리가 많이 반복
-   읽기의 핫 파티션 키

예상 절감액을 좌우하는 가장 큰 요인은 읽기가 반복되는 정도입니다. 워크로드에서 짧은 시간 동안 동일한 지점 읽기 또는 쿼리를 지속적으로 실행하는 경우 통합 캐시를 사용하면 매우 좋습니다. 반복되는 읽기에 통합 캐시를 사용하면 첫 번째 읽기에만 RU 기능이 사용됩니다. 동일한 전용 게이트웨이 노드(`MaxIntegratedCacheStaleness` 창 내부 및 데이터를 제거하지 않은 경우)를 통해 라우팅되는 후속 읽기는 처리량을 사용하지 않습니다.

다음과 같은 일부 워크로드는 통합 캐시를 사용하지 않는 것이 좋습니다.

-   쓰기 작업이 많은 워크로드
-  가끔 반복되는 지점 읽기 또는 쿼리

## <a name="item-cache"></a>항목 캐시

지점 읽기에 항목 캐시를 사용할 수 있습니다. 즉, 키/값이 항목 ID 및 파티션 키를 기준으로 조회됩니다.

### <a name="populating-the-item-cache"></a>항목 캐시 채우기

- 새 쓰기, 업데이트 및 삭제가 자동으로 항목 캐시에 채워집니다.
- 앱이 이전에는 캐시에 없었던(캐시 누락) 특정 항목을 읽으려고 하면 해당 항목이 이제는 항목 캐시에 저장됩니다.

### <a name="item-cache-invalidation-and-eviction"></a>항목 캐시 무효화 및 제거

- 항목 업데이트 또는 삭제
- LRU(오래 전에 사용한 항목)
- 캐시 보존 시간(즉, `MaxIntegratedCacheStaleness`)

## <a name="query-cache"></a>쿼리 캐시

쿼리 캐시를 사용하여 쿼리를 캐시할 수 있습니다. 쿼리 캐시는 쿼리를 키/값 조회로 변환합니다. 여기서 키는 쿼리 텍스트이고 값은 쿼리 결과입니다. 통합 캐시는 쿼리 엔진이 없으며 각 쿼리에 대한 키/값 조회만 저장합니다.

### <a name="populating-the-query-cache"></a>쿼리 캐시 채우기

- 캐시에 해당 쿼리에 대한 결과가 없으면(캐시 누락) 쿼리가 백 엔드로 전송됩니다. 쿼리가 실행된 후에는 캐시가 해당 쿼리에 대한 결과를 저장합니다.

### <a name="query-cache-eviction"></a>쿼리 캐시 제거

- LRU(오래 전에 사용한 항목)
- 캐시 보존 시간(즉, `MaxIntegratedCacheStaleness`)

### <a name="working-with-the-query-cache"></a>쿼리 캐시 작업

쿼리의 결과 페이지가 여러 개 있더라도 쿼리 캐시를 작업할 때 특수 코드가 필요하지 않습니다. 쿼리가 통합 캐시에 도달하든 백 엔드 쿼리 엔진에서 실행되든, 쿼리 페이지 지정에 대한 모범 사례와 코드는 동일합니다.

쿼리 캐시는 해당하는 경우 쿼리 연속 토큰을 자동으로 캐시합니다. 쿼리의 결과 페이지가 여러 개 있는 경우 통합 캐시에 저장된 모든 페이지에는 RU 요금이 부과되지 않습니다. 쿼리 결과의 후속 페이지에서 백 엔드 실행이 필요한 경우 이전 작업과 중복되지 않도록 이전 페이지의 연속 토큰이 사용됩니다.

> [!NOTE]
> 서로 다른 전용 게이트웨이 노드에 있는 통합 캐시 인스턴스의 캐시는 서로 독립적입니다. 한 노드 내에서 캐시된 데이터가 다른 노드에서도 반드시 캐시되는 것은 아닙니다.

## <a name="integrated-cache-consistency"></a>통합 캐시 일관성

통합 캐시는 최종 [일관성](consistency-levels.md)만 지원합니다. 읽기에 일관적인 접두사, 세션, 제한된 부실 또는 강력한 일관성이 있는 경우 읽기는 항상 통합 캐시를 무시합니다.

모든 읽기에 대해 최종 일관성을 구성하는 가장 쉬운 방법은 [계정 수준에서 설정](consistency-levels.md#configure-the-default-consistency-level)하는 것입니다. 그러나 일부 읽기만 최종 일관성을 유지하려면 [요청 수준](how-to-manage-consistency.md#override-the-default-consistency-level)에서 일관성을 구성할 수도 있습니다.

## <a name="maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness

`MaxIntegratedCacheStaleness`는 캐시된 지점 읽기와 쿼리에 대해 허용 가능한 최대 부실입니다. `MaxIntegratedCacheStaleness`는 요청 수준에서 구성할 수 있습니다. 예를 들어, 2시간의 `MaxIntegratedCacheStaleness`를 설정하면 요청은 2시간 이내의 데이터인 경우 캐시된 데이터만 반환합니다. 통합 캐시를 사용하여 반복 읽기의 가능성을 높이려면 `MaxIntegratedCacheStaleness`를 비즈니스 요구 사항에서 허용하는 만큼 높게 설정해야 합니다.

`MaxIntegratedCacheStaleness`는 최종적으로 캐시를 채우는 요청에서 구성된 경우 요청이 캐시되는 기간에 영향을 주지 않는다는 것을 이해해야 합니다. `MaxIntegratedCacheStaleness`는 캐시된 데이터를 사용하려고 할 때 일관성을 적용합니다. 전역 TTL 또는 캐시 보존 설정이 없으므로 통합 캐시가 가득 찼거나 새 읽기가 현재 캐시된 항목의 보존 기간보다 낮은 `MaxIntegratedCacheStaleness`를 사용하여 실행되는 경우에만 캐시에서 데이터가 제거됩니다.

이는 대부분의 캐시가 작동하는 방식에서 향상된 기능이며 다음과 같은 추가 사용자 지정을 허용합니다.

- 각 지점 읽기 또는 쿼리에 대한 다양한 부실 요구 사항을 설정할 수 있습니다.
- 서로 다른 클라이언트는 동일한 지점 읽기나 쿼리를 실행하는 경우에도 서로 다른 `MaxIntegratedCacheStaleness` 값을 구성할 수 있습니다.
- 캐시된 데이터를 사용할 때 읽기 일관성을 수정하려고 한 경우 `MaxIntegratedCacheStaleness`를 변경하면 읽기 일관성에 즉시 영향을 줍니다.

> [!NOTE]
> 명시적으로 구성되지 않은 경우 MaxIntegratedCacheStaleness는 기본적으로 5분으로 설정됩니다.

`MaxIntegratedCacheStaleness` 매개 변수를 더 잘 이해하려면 다음 예제를 고려합니다.

| 시간       | 요청                                         | 응답                                                     |
| ---------- | ----------------------------------------------- | ------------------------------------------------------------ |
| t = 0초  | MaxIntegratedCacheStaleness = 30초를 사용하여 쿼리 A 실행 | 백 엔드 데이터베이스에서 결과 반환(일반 RU 요금) 및 캐시 채우기     |
| t = 0초  | MaxIntegratedCacheStaleness = 60초를 사용하여 쿼리 B 실행 | 백 엔드 데이터베이스에서 결과 반환(일반 RU 요금) 및 캐시 채우기     |
| t = 20초 | MaxIntegratedCacheStaleness = 30초를 사용하여 쿼리 A 실행 | 통합 캐시에서 결과 반환(0 RU 요금)           |
| t = 20초 | MaxIntegratedCacheStaleness = 60초를 사용하여 쿼리 B 실행 | 통합 캐시에서 결과 반환(0 RU 요금)           |
| t = 40초 | MaxIntegratedCacheStaleness = 30초를 사용하여 쿼리 A 실행 | 백 엔드 데이터베이스에서 결과 반환(일반 RU 요금) 및 캐시 새로 고침 |
| t = 40초 | MaxIntegratedCacheStaleness = 60초를 사용하여 쿼리 B 실행 | 통합 캐시에서 결과 반환(0 RU 요금)           |
| t = 50초 | MaxIntegratedCacheStaleness = 20초를 사용하여 쿼리 B 실행 | 백 엔드 데이터베이스에서 결과 반환(일반 RU 요금) 및 캐시 새로 고침 |

> [!NOTE]
> `MaxIntegratedCacheStaleness` 사용자 지정은 최신 .NET 및 Java 미리 보기 SDK에서만 지원됩니다.

[`MaxIntegratedCacheStaleness`를 구성하는 방법을 알아봅니다.](how-to-configure-integrated-cache.md#adjust-maxintegratedcachestaleness)

## <a name="metrics"></a>메트릭

통합 캐시를 사용하는 경우 몇 가지 주요 메트릭을 모니터링하면 도움이 됩니다. 통합 캐시 메트릭은 다음과 같습니다.

- `DedicatedGatewayAverageCpuUsage` - 전용 게이트웨이 노드에서 평균 CPU 사용량입니다.
- `DedicatedGatewayMaxCpuUsage` - 전용 게이트웨이 노드에서 최대 CPU 사용량입니다.
- `DedicatedGatewayAverageMemoryUsage` - 요청 라우팅과 데이터 캐싱에 둘 다 사용되는 전용 게이트웨이 노드에서 평균 메모리 사용량입니다.
- `DedicatedGatewayRequests` - 모든 전용 게이트웨이 인스턴스에서 총 전용 게이트웨이 요청 수입니다.
- `IntegratedCacheEvictedEntriesSize` – 전용 게이트웨이 노드에서 LRU로 인해 통합 캐시에서 제거된 평균 데이터 양입니다. 이 값에는 `MaxIntegratedCacheStaleness` 시간 초과로 인해 만료된 데이터가 포함되지 않습니다.
- `IntegratedCacheItemExpirationCount` - 캐시된 지점 읽기의 `MaxIntegratedCacheStaleness` 시간 초과로 인해 통합 캐시에서 제거되는 항목 수입니다. 이 값은 모든 전용 게이트웨이 노드에서 통합 캐시 인스턴스의 평균입니다.
- `IntegratedCacheQueryExpirationCount` - 캐시된 쿼리의 `MaxIntegratedCacheStaleness` 시간 초과로 인해 통합 캐시에서 제거되는 쿼리 수입니다. 이 값은 모든 전용 게이트웨이 노드에서 통합 캐시 인스턴스의 평균입니다.
- `IntegratedCacheItemHitRate` - 통합 캐시를 사용한 지점 읽기의 비율입니다(최종 일관성을 적용하여 전용 게이트웨이를 통해 라우팅된 모든 지점 읽기 중에서). 이 값은 모든 전용 게이트웨이 노드에서 통합 캐시 인스턴스의 평균입니다.
- `IntegratedCacheQueryHitRate` - 통합 캐시를 사용한 쿼리의 비율입니다(최종 일관성을 적용하여 전용 게이트웨이를 통해 라우팅된 모든 쿼리 중에서). 이 값은 모든 전용 게이트웨이 노드에서 통합 캐시 인스턴스의 평균입니다.

모든 기존 메트릭은 기본적으로 메트릭 클래식이 아닌 **메트릭 블레이드** 에서 사용할 수 있습니다.

   :::image type="content" source="./media/integrated-cache/integrated-cache-metrics.png" alt-text="통합 캐시 메트릭의 위치를 보여주는 이미지" border="false":::

메트릭은 모든 전용 게이트웨이 노드에서 평균, 최댓값 또는 합계입니다. 예를 들어, 노드가 5개인 전용 게이트웨이 클러스터를 프로비저닝하는 경우 메트릭은 모든 5개 노드에서 집계된 값을 반영합니다. 각 개별 노드에 대한 메트릭 값은 확인할 수 없습니다.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

아래 예제에서는 몇 가지 일반적인 시나리오를 디버그하는 방법을 보여줍니다.

### <a name="i-cant-tell-if-my-application-is-using-the-dedicated-gateway"></a>애플리케이션에서 전용 게이트웨이를 사용하고 있는지 알 수 없습니다.

`DedicatedGatewayRequests`를 확인합니다. 이 메트릭은 요청이 통합 캐시에 도달했는지 여부에 관계없이 전용 게이트웨이를 사용하는 모든 요청을 포함합니다. 애플리케이션에서 표준 게이트웨이 또는 직접 모드와 원본 연결 문자열을 사용하는 경우 오류 메시지가 표시되지 않지만 `DedicatedGatewayRequests`는 0입니다.

### <a name="i-cant-tell-if-my-requests-are-hitting-the-integrated-cache"></a>요청이 통합 캐시에 도달하는지 알 수 없습니다.

`IntegratedCacheItemHitRate` 및 `IntegratedCacheQueryHitRate`를 확인합니다. 두 값이 모두 0이면 요청이 통합 캐시를 적중하지 않습니다. 전용 게이트웨이 연결 문자열을 사용 중이고, [게이트웨이 모드로 연결 중](sql-sdk-connection-modes.md)이며, [최종 일관성을 설정했는지](consistency-levels.md#configure-the-default-consistency-level) 확인합니다.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-small"></a>전용 게이트웨이가 너무 작은지 알아보려 합니다.

`IntegratedCacheItemHitRate` 및 `IntegratedCacheQueryHitRate`를 확인합니다. 이 값이 크면(예: 0.7~0.8 초과) 전용 게이트웨이가 충분히 크다는 좋은 신호입니다.

`IntegratedCacheItemHitRate` 또는 `IntegratedCacheQueryHitRate`가 작으면 `IntegratedCacheEvictedEntriesSize`를 확인합니다. `IntegratedCacheEvictedEntriesSize`가 크면 전용 게이트웨이 크기를 늘리는 것이 더 좋다는 의미일 수 있습니다. 전용 게이트웨이 크기를 늘리고 새 `IntegratedCacheItemHitRate` 및 `IntegratedCacheQueryHitRate`를 비교하여 실험할 수 있습니다. 전용 게이트웨이가 더 커도 `IntegratedCacheItemHitRate` 또는 `IntegratedCacheQueryHitRate`가 개선되지 않는 경우에는 통합 캐시가 영향을 줄 만큼 읽기가 충분히 반복되지 않는 것일 수 있습니다.

### <a name="i-want-to-understand-if-my-dedicated-gateway-is-too-large"></a>전용 게이트웨이가 너무 큰지 알아보려 합니다.

전용 게이트웨이가 너무 작은 경우 측정하는 것보다 전용 게이트웨이가 너무 큰 경우 측정하는 것이 더 어렵습니다. 일반적으로 작게 시작하고 `IntegratedCacheItemHitRate` 및 `IntegratedCacheQueryHitRate`가 더 이상 향상되지 않을 때까지 전용 게이트웨이 크기를 서서히 늘려야 합니다. 경우에 따라 두 캐시 적중 메트릭 중 하나만 중요하며 둘 다 중요하지는 않습니다. 예를 들어, 워크로드가 주로 지점 읽기가 아닌 쿼리인 경우 `IntegratedCacheQueryHitRate`가 `IntegratedCacheItemHitRate`보다 훨씬 더 중요합니다.

대부분의 데이터가 LRU가 아닌 `MaxIntegratedCacheStaleness`를 초과하여 캐시에서 제거되는 경우 캐시가 필요 이상으로 큰 것일 수 있습니다. 결합된 `IntegratedCacheItemExpirationCount` 및 `IntegratedCacheQueryExpirationCount`가 거의 `IntegratedCacheEvictedEntriesSize`만큼 큰 경우 더 작은 전용 게이트웨이 크기로 실험하고 성능을 비교할 수 있습니다.

### <a name="i-want-to-understand-if-i-need-to-add-more-dedicated-gateway-nodes"></a>전용 게이트웨이 노드를 더 추가해야 하는지 알아보려고 합니다.

경우에 따라 대기 시간이 예기치 않게 높은 경우 더 큰 노드보다는 전용 게이트웨이 노드가 더 필요할 수 있습니다. `DedicatedGatewayMaxCpuUsage` 및 `DedicatedGatewayAverageMemoryUsage`를 확인하여 전용 게이트웨이 노드를 더 추가하면 대기 시간이 감소하는지 확인합니다. 통합 캐시의 모든 인스턴스가 서로 독립적이므로 전용 게이트웨이 노드를 더 추가해도 `IntegratedCacheEvictedEntriesSize`가 감소하지 않는다는 점을 기억하는 것이 좋습니다. 노드를 더 추가하면 전용 게이트웨이 클러스터에서 처리할 수 있는 요청 볼륨은 향상됩니다.

## <a name="next-steps"></a>다음 단계

- [통합 캐시 FAQ](integrated-cache-faq.md)
- [통합 캐시 구성](how-to-configure-integrated-cache.md)
- [전용 게이트웨이](dedicated-gateway.md)