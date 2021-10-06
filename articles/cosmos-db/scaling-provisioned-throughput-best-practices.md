---
title: 프로 비전 된 처리량 확장에 대 한 모범 사례 (r u/초)
description: 수동 및 자동 크기 조정 처리량에 대해 프로 비전 된 처리량 확장에 대 한 모범 사례를 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/20/2021
ms.author: dech
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c8a2ab0b904c60e2d1d1c44a9d596cf62d0403d6
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546015"
---
# <a name="best-practices-for-scaling-provisioned-throughput-rus"></a>프로 비전 된 처리량 확장에 대 한 모범 사례 (r u/초) 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 데이터베이스 또는 컨테이너 (컬렉션, 테이블 또는 그래프)의 처리량 (r u/초)을 확장 하기 위한 모범 사례 및 전략을 설명 합니다. 이 개념은 Azure Cosmos DB api에 대 한 모든 리소스의 자동 크기 조정 max/s 또는 프로 비전 된 수동 r u/s를 늘릴 때 적용 됩니다. 
 
## <a name="prerequisites"></a>필수 조건
- Azure Cosmos DB 분할 및 크기 조정을 처음 접하는 경우 먼저 [Azure Cosmos DB의 분할 및 수평 크기 조정](partitioning-overview.md)문서를 참조 하는 것이 좋습니다.
- 429 예외로 인해 r u/초를 확장할 계획인 경우 [진단 및 문제 해결 Azure Cosmos DB 요청 비율 너무 큼 (429) 예외](troubleshoot-request-rate-too-large.md)에 대 한 지침을 검토 하세요. R u/s를 확대 하기 전에 문제의 근본 원인을 파악 하 고, r o u/s가 적절 한 솔루션 인지 여부를 확인 합니다.
 
## <a name="background-on-scaling-rus"></a>크기 조정의 배경

요청한 r u/s 및 현재 실제 파티션 레이아웃에 따라 데이터베이스 또는 컨테이너의 o s/s를 늘리기 위해 요청을 보내면 수직 확장 작업이 즉시 또는 비동기적으로 완료 됩니다 (일반적으로 4-6 시간). 
- **즉시 확장**
    - 현재 실제 파티션 레이아웃에서 요청 된 r u/s를 지원할 수 있으면 Azure Cosmos DB 새 파티션을 분할 하거나 추가할 필요가 없습니다.
    - 결과적으로 작업이 즉시 완료 되 고 s e r/s를 사용할 수 있습니다. 
- **비동기 확장**
    - 요청 된 r u/s가 실제 파티션 레이아웃에서 지원할 수 있는 것 보다 높으면 Azure Cosmos DB는 기존 실제 파티션을 분할 합니다. 이는 리소스에 요청 된 r u/s를 지 원하는 데 필요한 최소 개수의 파티션이 있을 때까지 발생 합니다. 
    - 따라서 작업을 완료 하는 데 다소 시간이 걸릴 수 있습니다. 일반적으로 4-6 시간입니다.
각 실제 파티션은 처리량 및 50 GB의 저장소에 대 한 최대 1만 r u/초 (모든 Api에 적용)를 지원할 수 있습니다 (30GB의 저장소를 포함 하는 Cassandra를 제외 하 고 모든 Api에 적용 됨). 

- **인스턴트 축소**
    - 규모 축소 작업의 경우에는 Azure Cosmos DB 새 파티션을 분할 하거나 추가할 필요가 없습니다. 
    - 결과적으로 작업이 즉시 완료 되 고 r o s/s를 사용할 수 있습니다. 
    - 이 작업의 주요 결과는 파티션 범위 당 RUs가 감소 한다는 것입니다.
    
## <a name="how-to-scale-up-rus-without-changing-partition-layout"></a>파티션 레이아웃을 변경 하지 않고/s를 확장 하는 방법

### <a name="step-1-find-the-current-number-of-physical-partitions"></a>1 단계: 현재 실제 파티션 수를 확인 합니다. 

PartitionKeyRangeID에서 **Insights**  >  **처리량**  >  **정규화 된 (%)** 로 이동 합니다. PartitionKeyRangeIds의 고유 수를 계산 합니다. 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/number-of-physical-partitions.png" alt-text="PartitionKeyRangeID 차트에서 정규화 된 PartitionKeyRangeIds 수 (%)의 고유 수를 계산 합니다.":::

> [!NOTE]
> 차트에는 최대 50 PartitionKeyRangeIds 표시 됩니다. 리소스가 50 보다 많은 경우 [Azure Cosmos DB REST API](/rest/api/cosmos-db/get-partition-key-ranges#example) 를 사용 하 여 총 파티션 수를 계산할 수 있습니다. 

각 PartitionKeyRangeId는 하나의 실제 파티션에 매핑되고 가능한 해시 값 범위에 대 한 데이터를 보유 하도록 할당 됩니다. 

Azure Cosmos DB는 파티션 키에 따라 논리적 및 물리적 파티션 간에 데이터를 분산 하 여 수평 확장을 가능 하 게 합니다. 데이터가 기록 될 때 파티션 키 값의 해시를 사용 하 여 데이터가 있는 논리적 파티션과 실제 파티션을 결정 Azure Cosmos DB.

### <a name="step-2-calculate-the-default-maximum-throughput"></a>2 단계: 기본 최대 처리량 계산
분할 된 파티션 Azure Cosmos DB를 트리거하지 않고 크기를 조정할 수 있는 가장 높은 o s/s는와 동일 `Current number of physical partitions * 10,000 RU/s` 합니다.

#### <a name="example"></a>예제
5 개의 실제 파티션이 있고 수동 프로 비전 된 처리량이 3만 r u/초 인 기존 컨테이너가 있다고 가정 합니다. R u/초를 5 * 1만 r u/s = 5만 r u/s로 즉시 늘릴 수 있습니다. 마찬가지로 자동 크기 조정 최대 3만 r u/초/초 (3000-3만 r u/초 사이 크기 조정)를 사용 하는 컨테이너가 있는 경우 최대 u s/s를 5만 o s/s로 신속 하 게 늘릴 수 있습니다 (5000-5만 r u/초 사이 크기 조정). 
> [!TIP]
> 요청 속도 너무 큰 예외 (429s)에 응답 하기 위해 r u/s를 확장 하는 경우, 현재 실제 파티션 레이아웃에서 지원 되는 최고/s로 증가 하는 것이 좋습니다.
 
## <a name="how-to-ensure-even-data-distribution-during-asynchronous-scaling"></a>비동기 크기 조정 중에도 데이터 배포를 보장 하는 방법

### <a name="background"></a>배경

현재 수/초를 실제 파티션 수를 초과 하는 경우 * 1만 r u/초를 초과 하는 경우 새 파티션 수를 사용할 때까지 기존 파티션을 Azure Cosmos DB 분할 `ROUNDUP(requested RU/s / 10,000 RU/s)` 합니다. 분할 하는 동안 부모 파티션은 두 개의 자식 파티션으로 분할 됩니다. 

예를 들어 3 개의 실제 파티션이 있는 컨테이너가 있고 수동 프로 비전 된 처리량이 3만 r u/초입니다. 처리량이 45000 r u/s로 증가 한 경우 기존 실제 파티션 두 개를 분할 하 여 총 5 개의 실제 파티션이 Azure Cosmos DB 합니다 `ROUNDUP(45,000 RU/s / 10,000 RU/s)` . 

> [!NOTE]
> 애플리케이션은 분할하는 동안 언제든지 데이터를 수집하거나 쿼리할 수 있습니다. Azure Cosmos DB 클라이언트 sdk 및 서비스는이 시나리오를 자동으로 처리 하 고 요청을 올바른 실제 파티션으로 라우팅해야 하므로 추가 사용자 작업이 필요 하지 않습니다. 
 
저장소 및 요청 볼륨에 대해 매우 균등 하 게 분산 된 작업 (일반적으로/id와 같은 높은 카디널리티 필드를 분할 하 여 수행)을 사용 하는 경우 모든 파티션이 균등 하 게 분할 되도록 강화를 설정 하는 것이 좋습니다. 
 
이유를 확인 하기 위해 두 개의 물리적 파티션, 2만 r u/s 및 80 GB의 데이터가 있는 기존 컨테이너가 있는 경우를 예로 들어 보겠습니다.

높은 카디널리티를 사용 하 여 좋은 파티션 키를 선택 해 주셔서 감사 하는 데이터는 두 실제 파티션에서 거의 균등 하 게 분산 됩니다. 각 실제 파티션에는 가능한 해시 값의 총 범위로 정의 된 keyspace의 약 50%가 할당 됩니다.

또한 Azure Cosmos DB은 모든 실제 파티션에 동일 하 게/s를 균등 하 게 분산 합니다. 결과적으로 각 실제 파티션에는 총 데이터의 1만 r u/s와 50% (40 GB)가 있습니다. 다음 다이어그램에서는 현재 상태를 보여 줍니다. 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-1-baseline.png" alt-text="PartitionKeyRangeIds 2 개, 각각 1만 r u/s, 40 GB 및 50%의 총 keyspace":::

이제 2만 r u/초에서 3만 r u/초까지 증가 하려고 합니다. 
 
R u/초를 3만/s/s로 늘리면 파티션 중 하나만 분할 됩니다. 분할 후에는 다음을 수행 합니다.
-   데이터의 50%를 포함 하는 파티션 하나 (이 파티션은 분할 되지 않음)
-   각각 데이터의 25%를 포함 하는 두 개의 파티션 (분할 된 부모에서 가져온 자식 파티션)

Azure Cosmos DB은 모든 실제 파티션에 동일 하 게/s를 균등 하 게 분산 하기 때문에 각 실제 파티션은 1만 r u/초를 계속 받습니다. 그러나 이제 저장소 및 요청 배포의 불균형을 갖게 됩니다. 

다음 다이어그램에서는 파티션 3과 4 (파티션 2의 자식 파티션)에 각각 1만 r u/s를 사용 하 여 20gb의 데이터에 대 한 요청을 처리 하는 반면, 파티션 1은 1만 r u/초를 사용 하 여 데이터 양 (40 g b)에 대 한 두 배의 요청을 처리 합니다.

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-2-uneven-partition-split.png" alt-text="분할 후에는 각각 1만 r u/s가 있는 3 개의 PartitionKeyRangeIds 있습니다. 그러나 PartitionKeyRangeIds 중 하나는 총 keyspace (40 GB)의 50%이 고, PartitionKeyRangeIds 중 두 개는 총 keyspace의 25% (20gb)를 포함 합니다.":::

저장소 배포를 계속 유지 하기 위해 먼저 모든 파티션이 분할 되도록 r u p/s를 확장할 수 있습니다. 그런 다음 원하는 상태로 다시 낮출 수 있습니다.

따라서 파티션이 사후 분할 되도록 보장 하기 위해 두 개의 실제 파티션으로 시작 하는 경우 4 개의 실제 파티션이 종료 되도록 r u/s를 설정 해야 합니다. 이를 위해 먼저 partition/s = 4 * 1만 r u/s = 4만 r u/초를 설정 합니다. 분할이 완료 된 후에는 r u/초를 3만 r u/초까지 낮출 수 있습니다. 

결과적으로, 다음 다이어그램에서 각 실제 파티션은 20gb의 데이터에 대 한 요청을 처리 하기 위해 3만 o s/s/4 = 7500 r u/초를 가져옵니다. 전반적으로, 저장소를 유지 관리 하 고 파티션에 분산을 요청 합니다. 

:::image type="content" source="media/scaling-provisioned-throughput-best-practices/diagram-3-even-partition-split.png" alt-text="분할이 완료 되 고 r u/s/s가 4만 r u/초에서 3만 r u/초로 내려간 후에는 각각 7500 r u/s와 총 keyspace의 25% (20GB)의 PartitionKeyRangeIds 4 개가 있습니다.":::

### <a name="general-formula"></a>일반 수식

#### <a name="step-1-increase-your-rus-to-guarantee-that-all-partitions-split-evenly"></a>1 단계: 모든 파티션이 균등 하 게 분할 되도록 사용자/초 늘리기

일반적으로 실제 파티션 수가 시작 `P` 되 고 원하는 r u/초를 설정 하려는 경우 `S` :

R u/초를로 늘립니다 `10,000 * P * 2 ^ (ROUNDUP(LOG_2 (S/(10,000 * P)))` . 그러면 모든 파티션이 균등 하 게 분할 되도록 하는 데 필요한 값에 가장 가까운 o s/s가 제공 됩니다. 

> [!NOTE]
> 데이터베이스 또는 컨테이너의 o s/s를 늘리면 나중에 낮출 수 있는 최소 추가/s에 영향을 줄 수 있습니다. 일반적으로 최소 r u/s는 MAX (400 r u/초, 현재 저장소 GB * 10 r u/초, 최고 r u/초 프로 비전/100)와 같습니다. 예를 들어,로 크기를 조정 하는 최고가 수/s가 10만 r u/초 인 경우 나중에 설정할 수 있는 가장 낮은 o s/s는 1000 r u/초입니다. [최소 r u/s](concepts-limits.md#minimum-throughput-limits)에 대해 자세히 알아보세요.

#### <a name="step-2-lower-your-rus-to-the-desired-rus"></a>2 단계: 원하는 r u/초까지
 
예를 들어 5 개의 실제 파티션이 있고, 5만 r u/초 이며 15만 r u/s로 크기를 조정 하려는 경우를 가정해 보겠습니다. 먼저 `10,000 * 5 * 2 ^ (ROUND(LOG_2(150,000/(10,000 * 5)))` = 20만 o s/s를 설정 하 고 15만 r u/초로 설정 해야 합니다. 

최대 20만 u s/s로 확장 하는 경우 나중에 설정할 수 있는 가장 낮은 수동 작업 시간/초는 2000 r u/초입니다. 설정할 수 있는 [가장 낮은 자동 크기 조정 최대 r u/초](autoscale-faq.yml#lowering-the-max-ru-s) 는 2만 r u/초 (2000-2만 r u/초 사이 크기 조정)입니다. 대상 r o s/s가 15만 o s/s 이므로 최소 r u/s의 영향을 받지 않습니다.

## <a name="how-to-optimize-rus-for-large-data-ingestion"></a>대량 데이터 수집을 위해 r u/s를 최적화 하는 방법
 
많은 양의 데이터를 Azure Cosmos DB로 마이그레이션하거나 수집 하려는 경우에는 해당 컨테이너의 r u/s를 설정 하는 것이 좋습니다 .이를 통해 사전에 수집 하려는 총 데이터 양을 저장 하는 데 필요한 실제 파티션을 Azure Cosmos DB 사전 프로 비전 할 수 있습니다. 그렇지 않고 수집 하는 동안 Azure Cosmos DB는 파티션을 분할 해야 할 수 있으므로 데이터 수집에 시간이 더 늘어납니다. 
 
컨테이너를 만드는 동안 Azure Cosmos DB의 추론 수식을 사용 하 여 시작 하는 실제 파티션 수를 계산 하는 것을 활용할 수 있습니다. 
 
### <a name="step-1-review-the-choice-of-partition-key"></a>1 단계: 파티션 키 선택 확인
파티션 키를 선택 하는 [모범 사례](partitioning-overview.md#choose-partitionkey) 를 따라 요청 볼륨 및 저장소 마이그레이션 후에도 배포 합니다. 
 
### <a name="step-2-calculate-the-number-of-physical-partitions-youll-need"></a>2 단계: 필요한 실제 파티션 수 계산 
`Number of physical partitions = Total data size in GB / Target data per physical partition in GB`

각 실제 파티션은 최대 50 GB의 저장소 (Cassandra API의 경우 30gb)를 보유할 수 있습니다. 에 대해 선택 해야 하는 값은 `Target data per physical partition in GB` 실제 파티션을 얼마나 잘 압축 하 고 마이그레이션 후에 얼마나 많은 저장소를 확장 하는 지에 따라 달라 집니다. 

예를 들어 저장소를 계속 증가 시킬 것으로 예상 되는 경우 값을 30gb로 설정 하도록 선택할 수 있습니다. 저장소를 균등 하 게 분산 하는 좋은 파티션 키를 선택 했다고 가정 하면 각 파티션은 ~ 60% full (50 GB 중 30gb)이 됩니다. 이후 데이터가 작성 될 때 서비스에서 더 많은 실제 파티션을 즉시 추가 하도록 요구 하지 않고도 기존 실제 파티션 집합에 저장할 수 있습니다.

이와 대조적으로 저장소의 마이그레이션이 이후에 크게 증가 하지 않는다고 생각 되는 경우 값을 더 높게 설정 하도록 선택할 수 있습니다 (예: 45 GB). 즉, 각 파티션은 ~ 90% full (45 GB/50 GB)이 됩니다. 이렇게 하면 데이터를 분산 하는 실제 파티션 수가 최소화 됩니다. 즉, 각 실제 파티션은 프로 비전 된 총 r u/초를 초과할 수 있습니다. 
 
### <a name="step-3-calculate-the-number-of-rus-to-start-with"></a>3 단계: 시작 하는 데 사용 되는 o s/s의 수 계산
`Starting RU/s = Number of physical partitions * Initial throughput per physical partition`.
- `Initial throughput per physical partition` = 자동 크기 조정 또는 공유 처리량 데이터베이스를 사용 하는 경우 = 1만 r u/초
- `Initial throughput per physical partition` = 6000 r u/s 수동 처리량을 사용 하는 경우 
 
### <a name="example"></a>예제
수집 하려는 1TB (1000 g b)의 데이터가 있고 수동 처리량을 사용 하려고 한다고 가정해 보겠습니다. Azure Cosmos DB의 각 실제 파티션에는 50 GB의 용량이 있습니다. 향후 성장을 위해 공간을 80% full (40 GB)로 유지 하는 것을 목표로 하는 경우를 가정해 보겠습니다. 
 
즉, 1TB의 데이터에는 1000 g b/40 GB = 25 개의 실제 파티션이 필요 합니다. 실제 파티션이 25 개를 갖도록 하기 위해 수동 처리량을 사용 하는 경우 먼저 25 * 6000 o s/s = 15만 r u/초를 프로 비전 합니다. 그런 다음 컨테이너가 생성 된 후 수집이 더 빠르게 진행 되도록 하기 위해 수집을 시작 하기 전에 r o s/s를 25만 r u/초로 늘립니다 (실제 파티션이 25 개 있기 때문에 즉시 발생 함). 이렇게 하면 각 파티션이 최대 1만 r u/초를 가져올 수 있습니다. 

자동 크기 조정 처리량 또는 공유 처리량 데이터베이스를 사용 하는 경우 25 개의 실제 파티션을 얻으려면 먼저 25 * 1만 o s/s = 25만 r u/초를 프로 비전 합니다. 25 개의 실제 파티션으로 지원 될 수 있는 최고/초를 이미 사용 하 고 있으므로 수집 하기 전에 프로 비전 된 r u/초를 추가로 늘리지 않습니다.
 
이론적으로 25만 r u/초 및 1tb의 데이터를 사용 하 여 쓰기에 1kb 문서와 10 개의 RUs가 필요한 경우이 수집은 이론적으로 완료할 수 있습니다. 1000 GB * (100만 k b/1gb) * (1 개 문서/1 kb) * (10 r u/문서) * (1 시간/25만) * (1 시간/3600 초) = 11.1 시간 

수집을 수행 하는 클라이언트가 처리량을 완벽 하 게 포화 하 고 모든 실제 파티션에 쓰기를 분산 하는 것으로 가정 하는 계산입니다. 모범 사례로, 클라이언트 쪽에서 데이터를 "순서 섞기" 하는 것이 좋습니다. 이렇게 하면 클라이언트가 두 번째로 많은 고유 논리적 (즉, 실제) 파티션에 쓰고 있습니다. 
 
마이그레이션이 완료 되 면 필요에 따라/s를 낮출 수도 있고 자동 크기 조정을 사용 하도록 설정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* 데이터베이스 또는 컨테이너의 [정규화 RU/초 사용량을 모니터링](monitor-normalized-request-units.md)합니다.
* 요청 률 너무 큼 (429) 예외 [진단 및 문제 해결](troubleshoot-request-rate-too-large.md)
* [데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용 하도록 설정](provision-throughput-autoscale.md)합니다.
