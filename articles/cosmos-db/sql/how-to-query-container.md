---
title: Azure Cosmos DB에서 컨테이너 쿼리
description: 파티션 내 및 파티션 간 쿼리를 사용하여 Azure Cosmos DB에서 컨테이너를 쿼리하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: b74a2297f90d5770caa25f5b0886765bf70b0f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116963"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너 쿼리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB에서 컨테이너(컬렉션, 그래프, 테이블)를 쿼리하는 방법을 설명합니다. 특히 Azure Cosmos DB에서 파티션 내 및 파티션 간 쿼리를 사용하는 방법을 설명합니다.

## <a name="in-partition-query"></a>파티션 내 쿼리

컨테이너에서 데이터를 쿼리할 때 쿼리에 파티션 키 필터가 지정되어 있으면 Azure Cosmos DB가 쿼리를 자동으로 최적화합니다. 필터에 지정된 파티션 키 값에 해당하는 [실제 파티션](../partitioning-overview.md#physical-partitions)으로 쿼리를 라우팅합니다.

예를 들어 `DeviceId`에 등호 필터를 사용하는 아래 쿼리를 살펴보세요. `DeviceId`에서 분할된 컨테이너에 대해 이 쿼리를 실행하는 경우 이 쿼리는 단일 실제 파티션으로 필터링됩니다.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

앞의 예제와 같이 이 쿼리는 단일 파티션으로도 필터링됩니다. `Location`에 추가 필터를 추가해도 다음과 같이 변경되지 않습니다.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

다음은 파티션 키에 대해 범위 필터를 포함하는 쿼리이며 단일 실제 파티션으로 범위가 지정되지 않습니다. 파티션 내 쿼리가 되려면 쿼리에 파티션 키를 포함하는 등호 필터가 있어야 합니다.

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>파티션 간 쿼리

다음 쿼리는 파티션 키(`DeviceId`)에 대한 필터를 포함하지 않습니다. 따라서 각 파티션의 인덱스에 대해 실행되는 모든 실제 파티션으로 팬아웃해야 합니다.

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

각 실제 파티션에는 자체 인덱스가 있습니다. 따라서 컨테이너에서 파티션 간 쿼리를 실행하는 경우 실제 파티션 *당* 하나의 쿼리를 효과적으로 실행하게 됩니다. Azure Cosmos DB는 다른 실제 파티션에 대해 자동으로 결과를 집계합니다.

다른 실제 파티션의 인덱스는 서로 독립적입니다. Azure Cosmos DB에 글로벌 인덱스가 없습니다.

## <a name="parallel-cross-partition-query"></a>병렬 파티션 간 쿼리

Azure Cosmos DB SDK 1.9.0 이상은 병렬 쿼리 실행 옵션을 지원합니다. 병렬 파티션 간 쿼리를 사용하면 대기 시간이 짧아지고 파티션 간 쿼리를 수행할 수 있습니다.

다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

- **MaxConcurrency**: 컨테이너의 파티션에 대한 최대 동시 네트워크 연결 수를 설정합니다. 이 속성을 `-1`로 설정하는 경우 SDK에서 병렬 처리 수준이 관리됩니다.  `MaxConcurrency`를 `0`으로 설정하는 경우 컨테이너의 파티션에 대한 단일 네트워크 연결이 있습니다.

- **MaxBufferedItemCount**: 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률을 조정합니다. 이 매개 변수를 생략하거나 -1로 설정하는 경우 SDK는 병렬 쿼리 실행 중에 버퍼링된 항목 수를 관리합니다.

파티션 간 쿼리를 병렬화하는 Azure Cosmos DB의 기능으로 인해, 일반적으로 시스템에서 [실제 파티션](../partitioning-overview.md#physical-partitions)을 추가할 때 쿼리 대기 시간이 효과적으로 조정됩니다. 그러나 총 실제 파티션 수가 증가함에 따라 RU 요금은 현저하게 증가합니다.

파티션 간 쿼리를 실행하는 경우에는 기본적으로 개별 실제 파티션당 별도의 쿼리를 수행합니다. 파티션 간 쿼리는 인덱스를 사용하는 반면, 사용 가능한 경우 파티션 내 쿼리만큼 효율적이지는 않습니다.

## <a name="useful-example"></a>유용한 예제

다음은 파티션 간 쿼리를 보다 잘 이해할 수 있도록 하는 비유입니다.

다른 아파트 단지에 패키지를 배달해야 하는 배달 드라이버라고 가정해 보겠습니다. 각 아파트 단지에는 모든 거주자의 단위 번호를 포함하는 프레미스의 목록이 있습니다. 각 아파트 단지를 실제 파티션과 비교하고 각 목록을 실제 파티션의 인덱스와 비교할 수 있습니다.

다음 예제를 사용하여 파티션 내와 파티션 간 쿼리를 비교할 수 있습니다.

### <a name="in-partition-query"></a>파티션 내 쿼리

배달 드라이버가 정확한 아파트 단지(실제 파티션)을 알고 있는 경우 정확한 건물로 즉시 운전할 수 있습니다. 드라이버는 거주자의 단위 번호(인덱스)의 아파트 단지 목록을 확인하고 적절한 패키지를 신속하게 배달할 수 있습니다. 이 경우 드라이버는 아파트 단지까지 운전해 패키지 수신자가 살고 있는지 확인하기 위해 시간 또는 노력을 낭비하지 않습니다.

### <a name="cross-partition-query-fan-out"></a>파티션 간 쿼리(팬아웃)

배달 드라이버가 정확한 아파트 단지(실제 파티션)를 모를 경우 모든 단일 아파트 건물로 운전해 모든 거주자의 단위 번호(인덱스)로 목록을 확인해야 합니다. 각 아파트 단지에 도착하면 각 아파트의 주소 목록을 계속 사용할 수 있습니다. 그러나 패키지 수신자가 살고 있는지 여부에 관계 없이 모든 아파트 단지의 목록을 확인해야 합니다. 이는 파티션 간 쿼리가 작동하는 방식입니다. 인덱스를 사용할 수 있지만(모든 문을 두드릴 필요가 없음) 모든 실제 파티션에 대해 인덱스를 개별적으로 확인해야 합니다.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>파티션 간 쿼리(몇 개의 실제 파티션만 범위 지정)

배달 드라이버가 모든 패키지 수신자가 특정한 몇 개의 아파트 단지 내에 살고 있는 것으로 알고 있는 경우 모든 단일 단지로 운전할 필요가 없습니다. 몇 개의 아파트 단지로 운전하는 것은 여전히 단일 건물을 방문하는 것보다 더 많은 작업이 필요하지만 배달 드라이버는 여전히 상당한 시간과 노력을 절감합니다. 쿼리에 `IN` 키워드가 있는 해당 필터에 파티션 키가 있는 경우 데이터에 대한 관련된 실제 파티션의 인덱스만 확인합니다.

## <a name="avoiding-cross-partition-queries"></a>파티션 간 쿼리 방지

대부분의 컨테이너에서는 파티션 간 쿼리가 필요합니다. 파티션 간 쿼리는 정상입니다. 거의 모든 쿼리 작업은 파티션(논리적 파티션 키와 실제 파티션 모두)에서 지원됩니다. 또한 Azure Cosmos DB는 쿼리 엔진 및 클라이언트 SDK에서 많은 최적화를 포함하여 실제 파티션 간에 쿼리 실행을 병렬화합니다.

대부분의 읽기 작업이 많은 시나리오의 경우 쿼리 필터에서 가장 일반적인 속성을 선택하는 것이 좋습니다. 또한 파티션 키가 다른 [파티션 키 선택 모범 사례](../partitioning-overview.md#choose-partitionkey)를 준수하는지 확인해야 합니다.

파티션 간 쿼리를 방지하는 것은 일반적으로 규모가 큰 컨테이너에서만 문제가 됩니다. 실제 파티션에 쿼리 필터와 일치하는 항목이 없는 경우에도 결과에 대해 실제 파티션의 항목을 확인할 때마다 최소 약 2.5RU의 요금이 청구됩니다. 따라서 실제 파티션이 하나(또는 몇 개)만 있는 경우 파티션 간 쿼리는 파티션 내 쿼리보다 훨씬 더 많은 RU를 사용하지 않습니다.

실제 파티션 수는 프로비전된 RU의 양과 관련이 있습니다. 각 실제 파티션은 최대 10,000개의 프로비전된 RU를 허용하며 최대 50GB의 데이터를 저장할 수 있습니다. Azure Cosmos DB는 자동으로 실제 파티션을 관리합니다. 컨테이너의 실제 파티션 수는 프로비전된 처리량 및 사용된 스토리지에 따라 달라집니다.

워크로드가 아래 조건을 충족하는 경우 파티션 간 쿼리를 방지해야 합니다.
- 30,000개 이상의 RU를 프로비전할 계획입니다.
- 100GB 이상의 데이터를 저장할 계획입니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 분할하는 방법을 알아보세요.

- [Azure Cosmos DB에서 분할](../partitioning-overview.md)
- [Azure Cosmos DB의 가상 파티션 키](synthetic-partition-keys.md)
