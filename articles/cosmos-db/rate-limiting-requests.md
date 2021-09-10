---
title: 속도 제한을 사용하여 Azure Cosmos DB 애플리케이션 최적화
description: 이 문서에서는 개발자에게 Azure Cosmos DB에 대한 속도 제한 요청에 대한 방법론을 제공합니다. 이 패턴을 구현하면 대상 데이터베이스 또는 컨테이너의 프로비저닝된 처리량을 초과하는 워크로드에 대한 오류를 줄이고 전체 성능을 향상시킬 수 있습니다.
author: plasne
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.author: pelasne
ms.openlocfilehash: 0677a3f4180ab429dc51a4e40c969e32e6e8f655
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098142"
---
# <a name="optimize-your-azure-cosmos-db-application-using-rate-limiting"></a>속도 제한을 사용하여 Azure Cosmos DB 애플리케이션 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 개발자에게 Azure Cosmos DB에 대한 속도 제한 요청에 대한 방법론을 제공합니다. 이 패턴을 구현하면 대상 데이터베이스 또는 컨테이너의 프로비저닝된 처리량을 초과하는 워크로드에 대한 오류를 줄이고 전체 성능을 향상시킬 수 있습니다.

Azure Cosmos DB에서 프로비저닝된 처리량을 초과하는 요청으로 인해 [TooManyRequests](troubleshoot-request-rate-too-large.md), [Timeout](troubleshoot-request-timeout.md) 및 [ServiceUnavailable](troubleshoot-service-unavailable.md)과 같은 일시적인 오류가 발생할 수 있습니다. 일반적으로 용량이 사용 가능하고 성공할 때 이러한 요청을 다시 시도합니다. 그러나 이 방법을 사용하면 코드의 오류 경로를 따라 많은 요청이 발생하고 일반적으로 처리량이 줄어듭니다.

클라이언트 쪽 워크로드 트래픽을 서버 쪽 프로비저닝된 처리량과 일치시켜 비용 및 시간에 따라 측정된 최적의 시스템 성능을 달성할 수 있습니다.

다음 시나리오를 고려하세요.

* 20,000RU/s를 사용하여 Azure Cosmos DB를 프로비저닝합니다.
* 애플리케이션은 10,000개의 레코드를 포함하는 수집 작업을 처리하며, 각 레코드는 10RU의 비용이 듭니다. 이 작업을 완료하는 데 필요한 총 용량은 100,000RU입니다.
* 전체 작업을 Azure Cosmos DB로 보내는 경우에는 많은 일시적인 오류가 발생하고 다시 시도해야 하는 요청 버퍼가 많이 발생합니다. 이 상태는 작업에 필요한 RU의 총 수(100,000)가 프로비저닝된 최댓값(20,000)보다 훨씬 크다는 것입니다. 2,000개 이하의 레코드는 데이터베이스에 허용되지만 8,000개 이하의 레코드는 거부됩니다. 다시 시도할 때 8,000개 이하의 레코드를 Azure Cosmos DB로 보내며 그중 2,000개 이하가 허용되는 식입니다. 이 패턴은 10,000개의 레코드 대신 30,000개 이하의 레코드를 전송하게 될 것으로 간주됩니다.
* 대신 5초에 걸쳐 이러한 요청을 균등하게 전송하는 경우 각 일괄 처리가 프로비저닝된 20,000개 이하에 있기 때문에 오류가 없고 전반적으로 더 빠른 처리량을 예상해야 합니다.

코드에 속도 제한 메커니즘을 도입하여 일정 기간 동안 요청을 분산할 수 있습니다.

컨테이너에 대해 프로비저닝된 RU는 물리적 파티션 수에서 균등하게 공유됩니다. 위의 예에서 Azure Cosmos DB 두 개의 물리적 파티션을 프로비저닝하는 경우 각각 10,000RU가 있습니다.

요청 단위에 대한 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.
워크로드에서 사용하는 RU 수 추정에 대한 자세한 내용은 [요청 단위 고려 사항](request-units.md#request-unit-considerations)을 참조하세요.
Azure Cosmos DB 분할에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 수평 크기 조정](partitioning-overview.md)을 참조하세요.

## <a name="methodology"></a>방법

속도 제한을 구현하는 방법은 다음과 같습니다.

1. 사용되는 쓰기 및 읽기 요청에 대한 데이터를 갖도록 애플리케이션을 프로파일링합니다.
1. 모든 인덱스를 정의합니다.
1. 적절한 양의 데이터(샘플 데이터일 수 있음)로 컬렉션을 채울 수 있습니다. 애플리케이션에 일반적으로 수백만 개의 레코드가 있다고 예상하는 경우 수백만 개의 레코드로 채웁니다.
1. 대표 문서를 작성하고 RU 비용을 기록합니다.
1. 대표 쿼리를 실행하고 RU 비용을 기록합니다.
1. 애플리케이션에서 함수를 구현하여 결과에 따라 지정된 요청의 비용을 결정합니다.
1. 초당 Azure Cosmos DB에 전송되는 모든 작업의 합계가 프로비저닝된 처리량을 초과하지 않도록 코드에서 속도 제한 메커니즘을 구현합니다.
1. 애플리케이션의 부하를 테스트하고 프로비저닝된 처리량이 초과되지 않는지 확인합니다.
1. RU 비용을 주기적으로 다시 테스트하고 필요에 따라 비용 함수를 업데이트합니다.

## <a name="indexing"></a>인덱싱

익숙한 다른 SQL 및 NoSQL 데이터베이스와 달리 새로 생성된 컨테이너에 대한 Azure Cosmos DB의 기본 인덱싱 정책은 **모든** 속성을 인덱싱합니다. 인덱싱된 각 속성은 쓰기의 RU 비용을 증가시킵니다.

기본 인덱싱 정책은 쿼리 필터 조건이 저장된 모든 필드에 분산되어 있는 읽기 작업이 많은 시스템에서 대기 시간을 낮출 수 있습니다. 예를 들어 Azure Cosmos DB에서 최종 사용자가 만든 임시 검색을 제공하는 데 대부분의 시간을 소비하는 시스템은 이점을 누릴 수 있습니다.

검색 대상이 아닌 속성을 인덱싱에서 제외할 수 있습니다. 인덱스에서 속성을 제거하면 쓰기 작업이 많거나 레코드 검색 패턴이 더 제한된 시스템에 대한 전반적인 시스템 성능(비용 및 시간)을 향상시킬 수 있습니다.

비용을 측정하기 전에 사용 사례에 적합한 인덱스 정책을 의도적으로 구성해야 합니다. 또한 나중에 인덱스를 변경하는 경우 모든 비용 계산을 다시 실행해야 합니다. 

가능한 경우 정상 및 최대 수요 조건에서 일반적인 쿼리를 반영하는 부하로 개발 중인 시스템을 테스트하면 사용할 인덱싱 정책을 나타내는 데 도움이 됩니다.

인덱스에 대한 자세한 내용은 [Azure Cosmos DB의 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="measuring-cost"></a>비용 측정

비용 측정 시 몇 가지 주요 개념이 있습니다.

* [요청 단위 고려 사항](request-units.md#request-unit-considerations)에 설명된 대로 RU 사용에 영향을 미치는 모든 요소를 고려합니다.
* 데이터베이스 또는 컨테이너의 모든 읽기 및 쓰기는 동일한 프로비저닝된 처리량을 공유합니다.
* 사용되는 Azure Cosmos DB API에 관계없이 RU 소비를 발생합니다.
* 컬렉션에 대한 파티션 전략은 시스템 비용에 상당한 영향을 줄 수 있습니다. 자세한 내용은 [Azure Cosmos DB에서 분할 및 수평 확장](partitioning-overview.md#choose-partitionkey)을 참조하세요.
* 대표 문서와 대표 쿼리를 사용합니다.
  * 이러한 문서는 운영 체제에서 제공하는 것에 가까운 문서와 쿼리입니다.
  * 이러한 대표적인 문서와 쿼리를 얻는 가장 좋은 방법은 애플리케이션의 사용을 계측하는 것입니다. 데이터 기반 결정을 내리는 것이 항상 좋습니다.
* 정기적으로 비용을 측정합니다.
  * 인덱스 변경, 인덱스 크기는 비용에 영향을 줄 수 있습니다. 
  * 대표적인 문서와 쿼리에 대해 몇 개의 반복 가능한(자동화될 수 있음) 테스트를 만드는 것이 좋습니다.
  * 대표 문서 및 쿼리가 여전히 대표적인지 확인합니다.

요청 비용을 결정하는 메서드는 API마다 다릅니다.

* [코어 API](find-request-unit-charge.md)
* [Cassandra API](cassandra/find-request-unit-charge-cassandra.md)
* [Gremlin API](find-request-unit-charge-gremlin.md)
* [Mongo DB API](mongodb/find-request-unit-charge-mongodb.md)
* [Table API](table/find-request-unit-charge.md)

## <a name="write-requests"></a>쓰기 요청

쓰기 작업의 비용은 예측하기 쉬운 경향이 있습니다. 레코드를 삽입하고 Azure Cosmos에서 보고한 비용을 문서화합니다.

크기가 다른 문서 및/또는 다른 인덱스를 사용하는 문서가 있는 경우 모든 인덱스를 측정하는 것이 중요합니다.
대표 문서는 모든 쓰기에서 단일 값을 할당할 수 있을 만큼 비용이 충분히 많이 들 수 있습니다.
예를 들어 13.14RU, 16.01RU 및 12.63RU의 비용을 확인한 경우 해당 비용을 평균 14RU로 계산할 수 있습니다.

## <a name="read-requests"></a>읽기 요청

쿼리 작업의 비용은 다음과 같은 이유로 예측하기가 훨씬 더 어려울 수 있습니다.

* 시스템에서 사용자 정의 쿼리를 지원하는 경우 들어오는 쿼리를 대표 쿼리에 매핑하여 비용을 결정해야 합니다. 이 프로세스는 다음과 같은 다양한 형식을 취할 수 있습니다.
  * 쿼리를 정확하게 일치시킬 수 있습니다. 직접 일치가 없는 경우 가장 가까운 대표 쿼리를 찾아야 할 수 있습니다.
  * 쿼리의 특성에 따라 비용을 계산할 수 있습니다. 예를 들어 쿼리의 각 절에 특정 비용이 있거나 인덱싱된 속성의 비용이 "x"인 반면 인덱싱되지 않은 속성의 비용은 "y"인 경우가 있습니다.
* 결과 수는 다를 수 있으며 통계가 없으면 반환 페이로드의 RU 영향을 예측할 수 없습니다.

쿼리 작업의 단일 비용이 아니라 쿼리를 평가하고 비용을 계산하는 일부 함수가 있을 수 있습니다.
Core API를 사용하는 경우 작업의 실제 비용을 평가하고 예측이 얼마나 정확한지 확인할 수 있습니다(이 예측의 튜닝은 코드 내에서 자동으로 발생할 수도 있습니다).

## <a name="transient-fault-handling"></a>일시적인 오류 처리

다음과 같은 이유로 속도 제한 메커니즘을 구현하더라도 애플리케이션은 여전히 일시적인 오류 처리가 필요합니다.

* 요청의 실제 비용은 예상 비용과 다를 수 있습니다.
* TooManyRequests 이외의 이유로 일시적인 오류가 발생할 수 있습니다.

그러나 애플리케이션에서 속도 제한 메커니즘을 제대로 구현하면 일시적인 오류 수가 크게 줄어듭니다.

## <a name="alternate-and-related-techniques"></a>대체 및 관련 기술

이 문서에서는 워크로드의 클라이언트 쪽 조정 및 일괄 처리를 설명하지만 전체 시스템 처리량을 관리하는 데 사용할 수 있는 다른 기술이 있습니다.

### <a name="autoscaling"></a>자동 확장

Azure Cosmos DB에서 프로비저닝된 처리량 자동 크기 조정 기능을 사용하면 데이터베이스 또는 컨테이너의 처리량(RU/s)을 자동으로 즉시 크기 조정할 수 있습니다. 처리량은 워크로드의 가용성, 대기 시간, 처리량 또는 성능에 영향을 주지 않고 사용량을 기준으로 크기가 조정됩니다.

자동 크기 조정 프로비저닝된 처리량은 변수 또는 예측할 수 없는 트래픽 패턴이 있는 중요 업무용 워크로드에 적합하며 고성능과 규모에 대한 SLA가 필요합니다.

자동 크기 조정에 대한 자세한 내용은 [자동 크기 조정 처리량으로 Azure Cosmos 컨테이너 및 데이터베이스 만들기](provision-throughput-autoscale.md)를 참조하세요.

### <a name="queue-based-load-leveling-pattern"></a>큐 기반 부하 평준화 패턴

클라이언트와 Azure Cosmos DB 사이에서 버퍼 역할을 하는 큐를 사용하여 서비스가 실패하거나 작업 시간 초과를 일으킬 수 있는 일시적으로 많은 부하를 원활하게 수행할 수 있습니다.

이 패턴은 오버로드가 발생하는 서비스를 사용하는 모든 애플리케이션에 유용합니다. 그러나 애플리케이션이 최소 대기 시간으로 서비스로부터 응답을 받을 것으로 예상되는 경우에는 이 패턴이 유용하지 않습니다.

이 패턴은 수집 작업에 적합한 경우가 많습니다.

이 패턴에 대한 자세한 내용은 [큐 기반 로드 평준화 패턴](/azure/architecture/patterns/queue-based-load-leveling)을 참조하세요.

### <a name="cache-aside-pattern"></a>캐시 배제 패턴

요청 시 데이터를 매번 Azure Cosmos DB를 쿼리하는 대신 캐시에 로드하는 것을 고려할 수 있습니다. 캐시를 사용하면 성능이 개선되고 캐시에 저장된 데이터 및 기본 데이터 저장소의 데이터 간 일관성을 유지할 수 있습니다.

자세한 내용은 [캐시 배제 패턴](/azure/architecture/patterns/cache-aside)을 참조하세요.

### <a name="materialized-view-pattern"></a>구체화된 뷰 패턴

필요한 쿼리 작업을 위해 데이터의 형식을 지정하는 것이 적합하지 않은 경우 Azure Cosmos DB에 데이터를 저장한 후 뷰를 다른 컬렉션으로 미리 채울 수 있습니다. 이 패턴은 효율적인 쿼리 및 데이터 추출을 지원하고 애플리케이션 성능을 개선하는 데 도움이 됩니다.

자세한 정보는 [구체화된 뷰 패턴](/azure/architecture/patterns/materialized-view)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에서 [TooManyRequests 오류를 해결](troubleshoot-request-rate-too-large.md)하는 방법을 알아봅니다.
* Azure Cosmos DB에서 [시간 초과 오류를 해결](troubleshoot-request-timeout.md)하는 방법을 알아봅니다.
* Azure Cosmos DB에서 [ServiceUnavailable 오류를 해결](troubleshoot-service-unavailable.md)하는 방법을 알아봅니다.
* Azure Cosmos DB의 [요청 단위](request-units.md)에 대해 자세히 알아보세요.
* Azure Cosmos DB의 [분할 및 수평적 크기 조정](partitioning-overview.md)에 대해 자세히 알아봅니다.
* Azure Cosmos DB의 [인덱싱 정책](index-policy.md)에 대해 알아봅니다.
* Azure Cosmos DB의 [자동 크기 조정](provision-throughput-autoscale.md)에 대해 알아봅니다.
* Azure Cosmos DB로 마이그레이션하기 위해 용량 계획을 하려고 하십니까? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만을 알고 있는 경우, [vCore 또는 vCPU를 사용하여 요청 단위 추정](convert-vcore-to-request-unit.md)을 참조하세요 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 빈도를 알고 있는 경우 [Azure Cosmos DB Capacity Planner를 사용하여 요청 단위 추정](estimate-ru-with-capacity-planner.md)을 읽어보세요
