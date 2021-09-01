---
title: Azure Cosmos DB NoSQL 및 관계형 데이터베이스 간 차이점 이해
description: 이 문서에서는 NoSQL 및 관계형 데이터베이스 간 차이점을 열거합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 7926994766646f89a2ea853df3cc60715005b814
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097791"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>NoSQL 및 관계형 데이터베이스 간 차이점 이해
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 관계형 데이터베이스 대비 NoSQL 데이터베이스의 주요 이점 중 일부를 열거합니다. NoSQL을 사용하는 경우의 몇 가지 과제에 대해서도 설명합니다. 존재하는 다양한 데이터 저장소를 자세히 살펴보려면 [올바른 데이터 저장소 선택](/azure/architecture/guide/technology-choices/data-store-overview)에 대한 문서를 참조하세요.

## <a name="high-throughput"></a>높은 처리량

관계형 데이터베이스 시스템을 유지 관리할 때 가장 명백한 과제 중 하나는 대부분의 관계형 엔진이 엄격한 [ACID 의미 체계](https://en.wikipedia.org/wiki/ACID)를 적용하기 위해 잠금 및 래치를 적용한다는 것입니다. 이 방법은 데이터베이스 내에서 일관된 데이터 상태를 보장한다는 측면에서 이점이 있습니다. 그러나 동시성, 대기 시간, 가용성과 관련해서 상당한 절충이 수반됩니다. 기본적인 아키텍처 제한으로 인해 트랜잭션 볼륨이 많은 경우 데이터를 수동으로 분할해야 할 수 있습니다. 수동 분할 구현은 시간이 오래 걸리고 번거로운 작업이 될 수 있습니다.

이 시나리오에서는 [분산 데이터베이스](https://en.wikipedia.org/wiki/Distributed_database)가 스케일링 성능이 더 뛰어난 솔루션을 제공할 수 있습니다. 그러나 유지 관리는 여전히 비용과 시간이 많이 소요되는 작업일 수 있습니다. 관리자는 시스템의 분산 특성이 투명하게 유지되도록 추가 작업을 수행해야 할 수 있습니다. 데이터베이스의 “연결 끊김” 특성도 고려해야 할 수 있습니다.

[Azure Cosmos DB](./introduction.md)는 전 세계 모든 Azure 지역에 배포되어 관련 과제를 간소화합니다. 애플리케이션에 맞게 데이터베이스를 원활하게 확장하는 동시에 고가용성을 유지하도록 파티션 범위를 동적으로 세분화할 수 있습니다. 세분화된 다중 테넌시 및 긴밀하게 제어되는 클라우드 네이티브 리소스 거버넌스는 [놀라운 대기 시간 보장](./consistency-levels.md#consistency-levels-and-latency)과 예측 가능한 성능을 지원합니다. 분할은 완전 관리형이므로 관리자가 코드를 작성하거나 파티션을 관리할 필요가 없습니다.

트랜잭션 볼륨이 초당 수천 개의 트랜잭션과 같이 극단적인 수준에 도달하는 경우 분산된 NoSQL 데이터베이스를 고려해야 합니다. 효율성을 최대화하고, 유지 관리 편의성을 높이고, 총 소유 비용을 줄이려면 Azure Cosmos DB를 사용하는 것이 좋습니다.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="백 엔드" border="false":::

## <a name="hierarchical-data"></a>계층 데이터

데이터베이스의 트랜잭션에 다양한 부모-자식 관계가 포함될 수 있는 사용 사례가 많습니다. 이러한 관계는 시간이 지남에 따라 상당히 증가하여 관리하기 어려울 수 있습니다. [계층적 데이터베이스](https://en.wikipedia.org/wiki/Hierarchical_database_model) 형태는 1980년대에 등장했지만 스토리지의 비효율성 때문에 널리 사용되지 않았습니다. 또한 [Ted Codd 관계형 모델](https://en.wikipedia.org/wiki/Relational_model)이 거의 모든 일반 데이터베이스 관리 시스템에서 사용하는 사실상 표준이 되었기 때문에 인기를 끌지 못했습니다.

그러나 지금은 문서 스타일 데이터베이스의 인기가 상당히 높아졌습니다. 이 데이터베이스는 계층적 데이터베이스 패러다임의 재창조로 간주될 수 있으며, 이제 디스크에 데이터를 저장하는 비용 관련 문제의 제약을 받지 않습니다. 따라서 이제 관계형 데이터베이스에서 여러 복잡한 부모-자식 엔터티 관계를 유지 관리하는 것은 최신 문서 지향 방법과 비교하여 안티패턴으로 간주될 수 있습니다.

[개체 지향 디자인](https://en.wikipedia.org/wiki/Object-oriented_design)의 등장 및 관계형 모델과 결합할 때 발생하는 [임피던스 부정합](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)도 특정 사용 사례의 관계형 데이터베이스 안티패턴을 강조합니다. 결과적으로, 숨겨지긴 하지만 종종 상당한 유지 관리 비용이 발생할 수 있습니다. [ORM 방법](https://en.wikipedia.org/wiki/Object-relational_mapping)이 발전하면서 이 문제를 부분적으로 완화했지만 문서 지향 데이터베이스는 개체 지향 방법과 훨씬 효율적으로 병합됩니다. 이 방법을 선택할 경우 개발자가 ORM 드라이버 또는 언어별 맞춤형 [OO 데이터베이스 엔진](https://en.wikipedia.org/wiki/Object_database)을 강제로 사용하지 않아도 됩니다. 데이터에 많은 부모-자식 관계가 있고 계층 구조 수준이 깊은 경우 [Azure Cosmos DB SQL API](./introduction.md)와 같은 NoSQL 문서 데이터베이스를 사용하는 것이 좋습니다.

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>복잡한 네트워크 및 관계

이름과는 달리, 관계형 데이터베이스는 심층적이고 복잡한 관계를 모델링하기에 적합하지 않은 솔루션을 제공합니다. 그 이유는 관계형 데이터베이스에 엔터티 간 관계가 실제로 존재하지 않기 때문입니다. 쿼리를 사용한 매핑을 허용하기 위해 카티전 조인이 필요한 복잡한 관계를 사용하여 런타임에 계산해야 합니다. 따라서 관계가 증가함에 따라 계산 측면에서 작업 비용도 기하급수적으로 증가합니다. 해당 엔터티를 관리하려는 관계형 데이터베이스를 사용할 수 없게 되는 경우도 있습니다.

관계형 데이터베이스가 등장한 시간 동안 다양한 형태의 “네트워크” 데이터베이스가 등장했지만 계층적 데이터베이스와 마찬가지로 해당 시스템도 인기를 얻지 못했습니다. 채택 속도가 느렸던 이유는 당시에 사용 사례가 없었고 스토리지가 비효율적이었기 때문입니다. 현재, 그래프 데이터베이스 엔진은 네트워크 데이터베이스 패러다임의 재등장으로 간주될 수 있습니다. 이 시스템의 주요 이점은 관계가 데이터베이스 내의 “최우선 개체”로 저장된다는 것입니다. 따라서 새 조인이나 교차곱마다 시간 복잡성이 증가하지 않고 일정한 시간 내에 관계를 트래버스할 수 있습니다.

데이터베이스에서 복잡한 관계 네트워크를 유지 관리하는 경우 이 데이터를 관리하기 위해 [Azure Cosmos DB Gremlin API](./graph-introduction.md)와 같은 그래프 데이터베이스를 사용하는 것이 좋습니다.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="서로 연결된 여러 직원과 부서를 보여 주는 데이터베이스 다이어그램":::

Azure Cosmos DB는 열 패밀리, 문서, 그래프, 키-값 등의 모든 주요 NoSQL 모델 형식에 대한 API 프로젝션을 제공하는 다중 모델 데이터베이스 서비스입니다. [Gremlin(그래프)](./gremlin-support.md) 및 SQL(Core) 문서 API 계층은 완전히 상호 운용됩니다. 이 때문에 프로그래밍 기능 수준에서 서로 다른 모델 간에 전환하는 데 이점이 있습니다. 복잡한 네트워크 순회 및 동일한 저장소에 문서 레코드로 모델링된 트랜잭션 측면 둘 다에서 그래프 저장소를 쿼리할 수 있습니다.

## <a name="fluid-schema"></a>유체 스키마

관계형 데이터베이스의 또 다른 특징은 디자인 타임에 스키마를 정의해야 한다는 것입니다. 이 때문에 데이터의 참조 무결성과 적합성 측면에서 이점이 있습니다. 그러나 애플리케이션이 확장됨에 따라 제한 요인이 될 수도 있습니다. 동일한 테이블 또는 데이터베이스 정의를 공유하는, 논리적으로 분리된 모델에서의 스키마 변경에 대응하는 것은 시간이 지남에 따라 복잡해질 수 있습니다. 이 사용 사례에서는 스키마를 애플리케이션에 위임하여 레코드별로 관리하는 것이 유용한 경우가 많습니다. 이렇게 하려면 데이터베이스가 “스키마 중립적”이어야 하며, 레코드에 포함된 데이터 측면에서 레코드가 “자체 설명적”일 수 있도록 해야 합니다.

구조가 높은 속도로 꾸준히 변경되는 데이터를 관리하는 경우, 특히 데이터베이스 전체에 적합성을 적용하기 어려운 외부 원본에서 트랜잭션이 제공될 수 있는 경우에는 Azure Cosmos DB와 같은 관리형 NoSQL 데이터베이스 서비스로 보다 스키마 중립적인 방법을 사용하는 것이 좋습니다.

## <a name="microservices"></a>마이크로 서비스

[마이크로 서비스](https://en.wikipedia.org/wiki/Microservices) 패턴이 최근 몇 년 동안 상당히 증가했습니다. 이 패턴은 [서비스 지향 아키텍처](https://en.wikipedia.org/wiki/Service-oriented_architecture)를 기반으로 합니다. 최신 마이크로 서비스 아키텍처에서 사실상의 데이터 전송 표준은 [JSON](https://en.wikipedia.org/wiki/JSON)으로, 대부분의 문서 지향 NoSQL 데이터베이스의 스토리지 매체이기도 합니다. 이 때문에 NoSQL 문서 저장소는 [이벤트 소싱 패턴](https://en.wikipedia.org/wiki/Event-driven_architecture)을 사용하여 복잡한 마이크로 서비스 구현에서 지속성과 동기화 둘 다를 훨씬 더 원활하게 충족할 수 있습니다. 해당 아키텍처에서 기존 관계형 데이터베이스는 유지 관리가 훨씬 더 복잡할 수 있습니다. API 간 상태 및 동기화에 필요한 변환 작업량이 더 많기 때문입니다. 특히 Azure Cosmos DB에는 많은 NoSQL 데이터베이스보다 훨씬 더 원활하게 JSON 기반 마이크로 서비스 아키텍처를 지원할 수 있는 다양한 기능이 있습니다.

* 순수 JSON 데이터 형식 선택
* 데이터베이스에 기본 제공되는 JavaScript 엔진 및 [쿼리 API](sql/javascript-query-api.md)
* 클라이언트에서 컨테이너 수정 알림을 받기 위해 구독할 수 있는 첨단 [변경 피드](./change-feed.md)

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 데이터베이스와 관련된 몇 가지 과제

NoSQL 데이터베이스를 구현할 경우 명백한 여러 이점이 있지만 고려해야 할 몇 가지 과제도 있습니다. 관계형 모델을 사용하는 경우 다음 항목이 동일한 수준으로 표시되지 않을 수도 있습니다.

* 동일한 엔터티를 가리키는 많은 관계가 있는 트랜잭션
* 전체 데이터 세트에서 강력한 일관성이 필요한 트랜잭션

첫 번째 과제의 경우 NoSQL 데이터베이스의 경험에 따른 규칙은 일반적으로 비정규화를 나타내며, 앞에서 설명한 것처럼 분산 시스템에서 보다 효율적인 읽기를 수행합니다. 그러나 이 방법을 사용하면 몇 가지 디자인 과제가 발생합니다. 하나의 범주 및 여러 태그와 관련된 제품을 예로 들어 살펴보겠습니다.

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="조인":::

NoSQL 문서 데이터베이스의 모범 사례 방법은 “제품 문서”에서 직접 범주 이름과 태그 이름을 비정규화하는 것입니다. 그러나 범주, 태그, 제품을 동기화 상태로 유지하기 위해 작업을 지원하는 디자인 옵션 때문에 유지 관리가 복잡해졌습니다. “일 대 다” 관계의 단순 업데이트 및 데이터 검색을 위한 조인이 아니라 제품의 여러 레코드에 데이터가 복제되기 때문입니다. 

반면에, 비정규화된 레코드에서 더 효율적인 읽기가 가능하며 개념적으로 조인된 엔터티 수가 증가함에 따라 점점 더 효율적이 된다는 장점이 있습니다. 그러나 비정규화된 레코드에서 조인된 엔터티 수가 증가함에 따라 읽기 효율성이 향상되는 것처럼 엔터티를 동기화 상태로 유지하기 위한 유지 관리도 더욱 복잡해집니다. 이 절충을 완화하는 한 가지 방법은 [하이브리드 데이터 모델](./modeling-data.md#hybrid-data-models)을 만드는 것입니다.

NoSQL 데이터베이스에서 보다 유연하게 절충을 처리할 수 있지만 유연성이 증가한 만큼 필요한 디자인 결정도 증가할 수 있습니다. [실제 예제를 사용하여 Azure Cosmos DB에서 데이터를 모델링하고 분할하는 방법](./how-to-model-partition-example.md) 문서를 참조하세요. 이 문서에서 설명하는, [비정규화된 사용자 데이터를 동기화 상태로 유지](./how-to-model-partition-example.md#denormalizing-usernames)하는 방법에서는 사용자가 있는 파티션뿐 아니라 컨테이너도 각기 다릅니다.

강력한 일관성을 고려하여, 이 방법이 전체 데이터 세트에서 필요한 경우는 드뭅니다. 그러나 필요한 경우에도 분산 데이터베이스에서는 어려울 수 있습니다. 강력한 일관성을 보장하려면 클라이언트가 데이터를 읽도록 허용하기 전에 모든 복제본과 지역에서 데이터를 동기화해야 합니다. 이로 인해 읽기의 대기 시간이 길어질 수 있습니다.

Azure Cosmos DB는 관련된 다양한 절충에 대해 관계형 데이터베이스보다 뛰어난 유연성을 제공하지만, 소규모 구현에서 이 방법을 사용할 경우 디자인 고려 사항이 더 추가될 수도 있습니다. 이 토픽에 대한 자세한 내용은 [일관성, 가용성 및 성능 절충](./consistency-levels.md)에 대한 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Cosmos 계정 및 기타 개념을 관리하는 방법을 알아봅니다.

* [Azure Cosmos 계정을 관리하는 방법](how-to-manage-database-account.md)
* [글로벌 분포](distribute-data-globally.md)
* [일관성 수준](consistency-levels.md)
* [Azure Cosmos 컨테이너와 항목으로 작업](account-databases-containers-items.md)
* [Azure Cosmos 계정에 대한 VNET 서비스 엔드포인트](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대한 IP 방화벽](how-to-configure-firewall.md)
* [Azure Cosmos 계정에서 Azure 지역을 추가하고 제거하는 방법](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)