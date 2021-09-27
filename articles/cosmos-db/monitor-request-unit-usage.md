---
title: Azure Cosmos DB의 작업 처리량 사용량을 모니터링
description: Azure Cosmos DB에서 작업의 처리량 또는 요청 단위 사용량을 모니터링하는 방법에 대해 알아봅니다. 어떤 작업이 더 많은 요청 단위를 소비하는지를 Azure Cosmos DB 계정 소유자가 파악할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: 8e592e40b8885158c0b6700b0014a5982fe2d352
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548294"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Azure Cosmos DB에서 작업의 처리량 또는 요청 단위 사용량을 모니터링하는 방법입니다
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다. **Total Request Units** 메트릭은 다양한 유형의 작업에 대한 요청 단위 사용량을 산출하는 데 사용됩니다. 나중에 대부분의 처리량을 사용하는 작업을 분석할 수 있습니다. 기본적으로 처리량 데이터는 1분 간격으로 집계됩니다. 그러나 시간 세분성 옵션을 변경하여 집계 단위를 변경할 수 있습니다.

요청 단위 사용량 데이터를 분석하는 방법에는 다음 두 가지가 있습니다.

* 지정된 시간 간격 내에서 더 많은 요청 단위를 가져오는 작업입니다.
* 더 많은 요청 단위를 사용하여 일반적인 작업을 수행하는 작업입니다.
이 분석을 통해 insert, upsert 등의 작업에 집중하고 해당 인덱싱을 살펴볼 수 있습니다. 특정 필드를 과도하게, 또는 과소하게 인덱싱하고 있는지 여부를 확인하고 경로를 포함하거나 제외하도록 [인덱싱 정책을](index-policy.md#include-exclude-paths) 수정할 수 있습니다.

특정 쿼리가 더 많은 요청 단위를 사용하고 있는 경우 다음과 같은 작업을 수행할 수 있습니다.

* 적절한 양의 데이터를 요청하고 있다면 다시 고려합니다.
* filter 절을 사용해 인덱싱하도록 쿼리를 수정합니다.
* 비용이 적게 드는 UDF 함수 호출을 수행합니다.
* 파티션 키를 정의하여 다른 파티션에 대한 쿼리의 팬아웃을 최소화합니다.
* 호출 응답에서 반환된 쿼리 메트릭, 진단 로그 상세 정보를 활용하고 [쿼리 성능 튜닝](sql-api-query-metrics.md) 문서를 참조하여 쿼리 실행에 대해 자세히 알아볼 수 있습니다.
* sum에서 시작한 다음 오른쪽 차원을 사용하여 avg 사용률을 확인할 수 있습니다.

## <a name="view-the-total-request-unit-usage-metric"></a>총 요청 단위 사용량 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터를** 선택한 다음 **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창":::

1. **메트릭** 창 > **리소스 선택** 에서 필요한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 **Azure Cosmos DB 계정** 을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용** 을 선택합니다.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="메트릭을 볼 계정 범위를 선택 합니다." border="true":::

1. 다음으로 사용 가능한 메트릭 목록에서 **총 요청 단위** 메트릭을 선택합니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예에서는 **총 요청 단위를** 선택하고 **Avg** 를 집계 값으로 하겠습니다. 이러한 세부 정보 외에 메트릭의 **시간 범위** 와 **시간 단위** 를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다. 선택한 기간에 분당 사용한 평균 요청 단위 수를 볼 수 있습니다.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Azure Portal에서 메트릭 선택" border="true":::

## <a name="filters-for-request-unit-usage"></a>요청 단위 사용량 필터

메트릭을 필터링하고 특정한 **CollectionName**, **DatabaseName**, **OperationType**, **Region**, **Status**, **StatusCode** 별로 표시되는 차트를 가져올 수 있습니다. **필터 추가** 및 **분할 적용** 옵션을 사용하면 요청 단위 사용량을 필터링하고 메트릭을 그룹화할 수 있습니다.

각 작업의 요청 단위 사용량을 총합(합계) 또는 평균으로 얻으려면 다음 이미지와 같이 **분할 적용을** 선택하고 **작업 유형** 및 필터 값을 선택합니다.

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Azure 모니터에서 작업에 사용할 Cosmos DB 요청 단위":::

컬렉션별 요청 단위 사용량을 보려면 **분할 적용을** 선택하고 컬렉션 이름을 필터로 선택합니다. 대시보드 내에서 컬렉션 선택과 함께 다음과 같은 채팅이 표시됩니다. 그런 다음, 특정 컬렉션 이름을 선택하여 자세한 정보를 볼 수 있습니다.

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Azure 모니터의 컬렉션에 의한 작업에 쓰일 Cosmos DB 요청 단위" border="true":::

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
