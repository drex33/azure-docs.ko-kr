---
title: Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s 모니터링
description: Azure Cosmos DB에서 작업의 정규화된 요청 단위 사용량을 모니터링하는 방법을 알아봅니다. 어떤 작업이 더 많은 요청 단위를 소비하는지를 Azure Cosmos DB 계정의 소유자가 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: e7e52208602ed2e82fed8201d1b4ba91c295946d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593860"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s를 모니터링하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다.

**정규화된 RU 소비** 메트릭은 트래픽과 관련하여 파티션 키가 얼마나 포화 상태인지 확인하는 데 사용됩니다. Azure Cosmos DB는 처리량을 모든 파티션 키 범위에 균등하게 분산합니다. 이 메트릭은 파티션 키 범위에 대한 최대 처리량 사용률의 초당 보기를 제공합니다. 이 메트릭을 사용하여 지정된 컨테이너에 대한 파티션 키 범위에서 RU/s 사용량을 계산할 수 있습니다. 이 메트릭을 사용하는 경우 Azure 모니터의 모든 파티션 키 범위에서 요청 단위 사용률이 높은 것으로 확인되면 워크로드 요구 사항을 충족하도록 처리량을 늘려야 합니다. 예: 정규화된 사용률은 모든 파티션 키 범위에서 RU/s 사용률의 최대값으로 정의됩니다. 예를 들어 최대 처리량이 20,000RU/s이고 각각 10,000RU/s까지 확장될 수 있는 두 개의 파티션 키 범위 P_1 및 P_2가 있다고 가정합니다. 특정 초에 P_1이 6,000RU를 사용하고, P_2가 8,000RU를 사용한 경우 정규화된 사용률은 MAX(6,000RU / 10,000RU, 8,000RU / 10,000RU) = 0.8입니다.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>정규화된 RU/s가 높은 경우 필요한 항목 및 수행할 작업

정규화된 RU/s 사용률이 지정된 파티션 키 범위에 대해 100%에 도달하고 클라이언트가 해당 시간 창에서 특정 파티션 키 범위에 대해 1초 동안 계속 요청을 수행하는 경우에는 속도 제한 오류를 수신합니다. 클라이언트는 제안된 대기 시간을 준수하고 요청을 다시 시도해야 합니다. SDK를 사용하면 적절한 대기 시간으로 미리 구성된 시간을 다시 시도하여 이러한 상황을 쉽게 처리할 수 있습니다.  단지 정규화된 RU가 100%에 도달했기 때문에 RU 속도 제한 오류가 표시되는 것은 아닙니다. 정규화된 RU는 모든 파티션 키 범위에 대한 최대 사용량을 나타내는 단일 값이므로 파티션 키 범위 하나가 사용 중이더라도 다른 파티션 키 범위에서는 문제 없이 요청을 처리할 수 있기 때문입니다. 예를 들어, 파티션 키 범위의 모든 RU/s를 소비하는 저장 프로시저와 같은 단일 작업을 수행하면 정규화된 RU/s 소비에 짧은 급등이 발생합니다. 이러한 경우 요청 속도가 낮거나 다른 파티션 키 범위의 다른 파티션에 대한 요청을 수행하는 경우 즉각적인 트래픽률 제한 오류가 발생하지 않습니다. 

Azure Monitor 메트릭은 **총 요청** 메트릭을 사용하여 SQL API용 상태 코드별로 작업을 찾는 데 유용합니다. 나중에 이러한 요청을 429 상태 코드로 필터링하고 **작업 유형** 별로 분할할 수 있습니다.  

속도가 제한된 요청을 찾는 데 권장되는 방법은 진단 로그를 통해 이 정보를 구하는 것이 좋습니다.

여러 파티션 키 범위 전반에서 정규화된 RU/s 소비가 계속 최대치 100%이거나 100%에 가까우면 처리량을 늘리는 것이 좋습니다. Azure Monitor 메트릭 및 Azure Monitor 진단 로그를 활용하여 부하가 높은 작업이 무엇인지 이 작업의 최대 사용량은 얼마인지 확인할 수 있습니다.

요약하면 **정규화된 RU 소비** 메트릭은 사용 측면에서 어떤 파티션 키 범위가 더 웜(warm)한지 확인하는 데 사용됩니다. 따라서 파티션 키 범위에 대한 처리량의 기울기를 얻을 수 있습니다. 나중에 Azure Monitor 로그에서 **PartitionKeyRUConsumption** 로그를 확인하고 사용량 측면에서 어떤 논리 파티션 키가 핫(hot)한지에 대한 정보를 얻을 수 있습니다. 이는 파티션 키를 선택하거나 애플리케이션 로직의 변경을 가리킵니다. 속도 제한을 해결하려면 데이터의 부하를 여러 파티션에 분산하거나 필요에 따라 처리량을 늘려야 합니다. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>정규화된 요청 단위 소비 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **모니터** 를 선택하고 **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창" border="true":::

3. **메트릭** 창 > **리소스 선택** 에서 필요한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 **Azure Cosmos DB 계정** 을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용** 을 선택합니다.

   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="메트릭을 볼 계정 범위를 선택 합니다." border="true":::

4. 다음에는 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 스토리지, 대기 시간, 가용성, Cassandra 등과 관련된 메트릭을 선택할 수 있습니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예에서는 **정규화된 RU 소비** 메트릭과 **Max** 를 집계 값으로 선택하겠습니다.

   이러한 세부 정보 외에 메트릭의 **시간 범위** 와 **시간 단위** 를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Azure Portal에서 메트릭 선택" border="true":::

### <a name="filters-for-normalized-request-unit-consumption"></a>정규화된 요청 단위 소비에 대한 필터

특정 **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**, **Region** 별로 표시되는 메트릭 및 차트를 필터링할 수도 있습니다. 메트릭을 필터링하려면 **필터 추가** 를 선택하고 필요한 속성(예: **CollectionName** 및 관심 있는 값)을 선택합니다. 그러면 선택한 기간 동안 컨테이너에 소비된 정규화된 RU 소비 단위가 그래프에 표시됩니다.  

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수 있습니다. 공유 처리량 데이터베이스의 경우 정규화된 RU 메트릭은 데이터를 데이터베이스 세분성으로만 표시하고 컬렉션당 데이터는 표시하지 않습니다. 따라서 공유 처리량 데이터베이스의 경우 컬렉션 이름으로 분할을 적용하면 데이터가 표시되지 않습니다.

각 컨테이너에 대한 정규화된 요청 단위 소비 메트릭은 다음 이미지와 같이 표시됩니다.

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="정규화된 요청 단위 소비 메트릭에 필터 적용" border="true":::

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
