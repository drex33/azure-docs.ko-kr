---
title: MongoDB용 Azure Cosmos DB API에서 쓰기 성능 최적화
description: 이 문서에서는 MongoDB용 Azure Cosmos DB API에서 쓰기 성능을 최적화하여 최저 비용으로 최대한의 가능한 처리량을 얻는 방법을 설명합니다.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 2e04953e766c76275079731751cb006fe46712e7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039555"
---
# <a name="optimize-write-performance-in-azure-cosmos-db-api-for-mongodb"></a>MongoDB용 Azure Cosmos DB API에서 쓰기 성능 최적화
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

쓰기 성능을 최적화하면 MongoDB용 Azure Cosmos DB API의 무제한 스케일링을 최대한 활용할 수 있습니다. 다른 관리형 MongoDB 서비스와 달리 MongoDB용 API는 무한 스케일링을 위해 분할된 컬렉션을 사용할 때 컬렉션을 자동으로 투명하게 분할합니다. 

데이터의 쓰기 방법에서는 데이터베이스 및 컬렉션에서 쓰기를 최대한 얻기 위해 분할 간에 데이터를 병렬화하고 분산함으로써 이 점을 염두에 두어야 합니다. 이 문서에서는 쓰기 성능을 최적화하는 모범 사례를 설명합니다.

## <a name="spread-the-load-across-your-shards"></a>분할 전체에 걸쳐 부하 분산
분할된 MongoDB용 API 컬렉션에 데이터를 쓸 때 데이터는 작은 조각으로 분할되며 분할 키 필드의 값에 따라 각 분할에 기록됩니다. 각 조각은 하나의 고유한 분할 키 값이 포함된 문서만 저장하는 가상 머신의 작은 부분이라고 생각할 수 있습니다. 

애플리케이션이 단일 분할에 대량의 데이터를 쓰는 경우, 앱이 모든 분할에 부하를 분산하는 대신 한 분할의 처리량을 최대화하기 때문에 비효율적입니다. 고유한 분할 키 값이 있는 많은 문서에 병렬로 쓰기를 수행함으로써 컬렉션 전체에 균등하게 쓰기 로드를 분산할 수 있습니다.

이를 수행하는 한 가지 예는 분할된 제품 카탈로그 애플리케이션을 범주 필드에 두는 것입니다. 최대 쓰기 처리량을 달성하려면 한 번에 하나의 범주(분할)에 쓰는 대신 모든 범주에 동시에 쓰는 것이 좋습니다. 

## <a name="reduce-the-number-of-indexes"></a>인덱스의 수 줄이기
[인덱싱](../mongodb-indexing.md)은 데이터를 쿼리하는 데 걸리는 시간을 크게 줄일 수 있는 훌륭한 기능입니다. 가장 유연한 쿼리 환경을 위해 MongoDB용 API는 기본적으로 데이터에서 와일드카드 인덱스를 사용하도록 설정하여 모든 필드에 대한 쿼리 속도를 크게 높입니다. 그러나 쓰기가 컬렉션과 인덱스를 변경하기 때문에 와일드카드 인덱스를 포함하는 모든 인덱스는 데이터를 쓸 때 추가 로드를 일으킵니다. 

쿼리를 지원하는 데 필요한 인덱스로만 인덱스 수를 줄이면 쓰기가 더 빠르고 저렴해집니다. 일반적으로 다음을 수행하는 것이 좋습니다.

* 필터링하는 모든 필드에는 해당하는 단일 필드 인덱스가 있어야 합니다. 이 옵션을 통해 다중 필드 필터링도 사용할 수 있습니다.
* 정렬하는 필드 그룹에는 해당 그룹에 대한 복합 인덱스가 있어야 합니다. 

## <a name="set-ordered-to-false-in-the-mongodb-drivers"></a>MongoDB 드라이버에서 순차(ordered)를 false로 설정
기본적으로 MongoDB 드라이버는 데이터를 쓸 때 순차(ordered) 옵션을 “true”로 설정하여 각 문서를 하나씩 순서대로 씁니다. 각 쓰기 요청은 이전 쓰기 요청이 완료되기를 기다려야 하기 때문에 이 옵션은 쓰기 성능을 저하시킵니다. 데이터를 쓸 때 성능을 높이려면 이 옵션을 false로 설정합니다. 

```JavaScript
db.collection.insertMany(
   [ <doc1> , <doc2>, ... ],
   {
      ordered: false
   }
)
```

## <a name="tune-for-the-optimal-batch-size-and-thread-count"></a>최적의 일괄 처리 크기 및 스레드 수 조정
여러 스레드/프로세스 전반에 걸친 쓰기 작업의 병렬화는 쓰기의 스케일링에 매우 중요합니다. MongoDB용 API는 각 프로세스/스레드에 대해 최대 1,000개 문서의 일괄 처리로 쓰기를 허용합니다. 

프로세스/스레드당 한 번에 1,000개가 넘는 문서 쓰기를 수행하는 경우 `insertMany()`과 같은 클라이언트 함수를 대략 1,000개 문서로 제한해야 합니다. 그러지 않으면 다음 일괄 처리로 이동하기 전에 각 일괄 처리가 커밋될 때까지 클라이언트가 기다립니다. 경우에 따라 1,000개보다 적거나 약간 많은 문서의 일괄 처리를 분할하면 속도가 더 빨라집니다.



## <a name="next-steps"></a>다음 단계

* [MongoDB용 API의 인덱싱](../mongodb-indexing.md)에 관해 자세히 알아보세요.
* [Azure Cosmos DB의 분할/파티셔닝](../partitioning-overview.md)에 관해 자세히 알아보세요.
* [일반 문제 해결](error-codes-solutions.md)에 관해 자세히 알아보세요.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
