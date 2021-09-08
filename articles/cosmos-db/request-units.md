---
title: Azure Cosmos DB에서 처리량 및 성능 통화로서의 요청 단위
description: Azure Cosmos DB에서 요청 단위 요구 사항을 지정하고 예측하는 방법에 대한 자세한 정보
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: e58e0911911035578c70ebe218af8f7e8c25bcf5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123032529"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB의 요청 단위
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. 요청 단위는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화입니다.

1 KB 항목에 대한 지점 읽기(즉, ID 및 파티션 키 값을 기준으로 단일 항목을 페치)의 비용은 1 요청 단위(또는 1 RU)입니다. RU를 사용하여 다른 모든 데이터베이스 작업에 비슷하게 비용이 할당됩니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다.

다음 이미지는 RU의 개략적인 개념을 보여 줍니다.

:::image type="content" source="./media/request-units/request-units.png" alt-text="데이터베이스 작업은 요청 단위를 사용함" border="false":::

용량을 관리하고 계획하려면 Azure Cosmos DB에서는 지정된 데이터 세트를 통해 지정된 데이터베이스 작업에 대한 RU 수가 결정적이 되도록 확인합니다. 응답 헤더를 검사하여 모든 데이터베이스 작업에서 사용하는 RU 수를 추적할 수 있습니다. 애플리케이션의 처리량 요구 사항 및 [RU 요금에 영향을 주는 요소](request-units.md#request-unit-considerations)를 파악하면 애플리케이션을 비용 효과적으로 실행할 수 있습니다.

사용 중인 Azure Cosmos 계정 형식에 따라 사용된 RU가 청구되는 방식이 결정됩니다. 계정을 만들 수 있는 3 가지 모드가 있습니다.

1. **프로비전된 처리량 모드**: 이 모드에서는 초당 100 RU 증분으로 애플리케이션에 대한 RU 수를 프로비전합니다. 애플리케이션에 대해 프로비전되는 처리량 크기를 조정하기 위해 100 RU의 증분 또는 감량으로 언제든지 RU 수를 늘리거나 줄일 수 있습니다. 프로그래밍 방식으로 또는 Azure Portal을 사용하여 변경할 수 있습니다. 프로비전한 초당 RU의 양에 대해 시간 단위로 요금이 청구됩니다. 자세한 내용은 [프로비전된 처리량](set-throughput.md) 문서를 참조하세요.

   두 가지 고유 단위로 처리량을 프로비전할 수 있습니다.

   * **컨테이너**: 자세한 내용은 [Azure Cosmos 컨테이너의 처리량 프로비전](how-to-provision-container-throughput.md)을 참조하세요.
   * **데이터베이스**: 자세한 내용은 [Azure Cosmos 데이터베이스의 처리량 프로비전](how-to-provision-database-throughput.md)을 참조하세요.

2. **서버리스 모드**: 이 모드에서는 Azure Cosmos 계정에 리소스를 만들 때 처리량을 프로비전할 필요가 없습니다. 청구 기간이 종료되면 데이터베이스 작업에 사용된 요청 단위 수에 대한 요금이 청구됩니다. 자세한 내용은 [서버리스 처리량](serverless.md) 문서를 참조하세요. 

3. **자동 크기 조정 모드**: 이 모드에서는 워크로드의 가용성, 대기 시간, 처리량 또는 성능에 영향을 주지 않고 사용량에 따라 데이터베이스 또는 컨테이너의 처리량(RU/s)을 자동으로 즉시 크기 조정할 수 있습니다. 이 모드는 변수 또는 예측할 수 없는 트래픽 패턴이 있는 중요 업무용 워크로드에 적합하며 고성능과 규모에 대한 SLA가 필요합니다. 자세한 내용은 [자동 크기 조정 처리량](provision-throughput-autoscale.md) 문서를 참조하세요. 

## <a name="request-unit-considerations"></a>요청 단위에 대한 고려 사항

워크로드에서 사용하는 RU 수를 예상하는 동안 다음 요인을 고려합니다.

* **항목 크기**: 항목 크기가 늘어나면 항목을 읽거나 쓰는 데 사용되는 RU 수도 증가합니다.

* **항목 인덱싱**: 기본적으로 각 항목은 자동으로 인덱싱됩니다. 컨테이너에서 일부 항목을 인덱싱하지 않도록 선택하면 사용되는 RU 수가 감소합니다.

* **항목 속성 수**: 모든 속성에 기본 인덱싱이 있다고 가정하면 항목 속성 수가 증가함에 따라 항목을 작성하는 데 사용되는 RU 수도 증가합니다.

* **인덱싱되는 속성 수**: 각 컨테이너의 인덱스 정책에 따라 기본적으로 인덱싱되는 속성이 결정됩니다. 쓰기 작업에 필요한 RU 사용량을 줄이려면 인덱싱된 속성 수를 제한합니다.

* **데이터 일관성**: 강력하고 제한된 부실 일관성 수준은 다른 완화된 일관성 수준과 비교할 경우 읽기 작업을 수행하면서 동시에 약 두 배 이상의 RU를 사용합니다.

* **읽기 유형**: 지점 읽기는 쿼리보다 RU의 비용을 크게 줄일 수 있습니다.

* **쿼리 패턴**: 쿼리의 복잡성은 작업에 사용되는 RU 수에 영향을 줍니다. 쿼리 작업 비용에 영향을 주는 요소는 다음과 같습니다. 
 
  * 쿼리 결과 수
  * 조건자 수
  * 조건자 특성
  * 사용자 정의 함수의 수
  * 원본 데이터 크기
  * 결과 집합 크기
  * 프로젝션 수

  동일한 데이터의 동일한 쿼리는 반복 실행에서 항상 동일한 RU 수를 사용합니다.

* **스크립트 사용량**: 쿼리와 마찬가지로, 저장 프로시저 및 트리거는 수행하는 작업의 복잡성에 따라 RU를 사용합니다. 애플리케이션을 개발하는 과정에서 각 작업에 사용되는 RU 용량을 더 잘 파악할 수 있도록 [요청 요금 헤더](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request)를 살펴보세요.

## <a name="request-units-and-multiple-regions"></a>요청 단위 및 여러 영역

Cosmos 컨테이너(또는 데이터베이스)에서 *'R'* RU를 프로비전하는 경우 Cosmos DB를 통해 Cosmos 계정과 연결된 *각* 영역에서 *'R'* RU를 사용할 수 있습니다. 특정 영역에 RU를 선택적으로 할당할 수 없습니다. Cosmos 컨테이너(또는 데이터베이스)에 프로비전된 RU는 Cosmos 계정과 연결된 모든 영역에서 프로비전됩니다.

Cosmos 컨테이너가 *' R '* RU로 구성되어 있고 Cosmos 계정에 연결된 *' N '* 영역이 있다고 가정하면 컨테이너에서 전역적으로 사용할 수 있는 총 RU는 *R* x *N* 입니다.

[일관성 모델](consistency-levels.md)의 선택도 처리량에 영향을 줍니다. 더 강력한 일관성 수준(예: *제한된 부실* 또는 *강력한* 일관성)과 비교하여 보다 완화된 일관성 수준(예: *세션*, *일관적인 접두사* 및 *궁극적* 일관성)의 경우 약 2배의 읽기 처리량을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량을 프로비전](set-throughput.md)하는 방법을 자세히 알아봅니다.
- [Azure Cosmos DB의 서버리스](serverless.md)에 대해 자세히 알아보세요.
- [논리 파티션](./partitioning-overview.md)에 대해 자세히 알아봅니다.
- [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
- [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
- [작업에 대한 요청 단위 요금 찾기](find-request-unit-charge.md)에 대해 알아봅니다.
- [Azure Cosmos DB에서 프로비전된 처리량 비용 최적화하기](optimize-cost-throughput.md)에 대해 알아봅니다.
- [Azure Cosmos DB에서 읽기 및 쓰기 비용 최적화하기](optimize-cost-reads-writes.md)에 대해 알아봅니다.
- [Azure Cosmos DB에서 쿼리 비용 최적화하기](./optimize-cost-reads-writes.md)에 대해 알아봅니다.
- [메트릭을 사용한 처리량 모니터링](use-metrics.md)에 대해 알아봅니다.
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
