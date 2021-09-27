---
title: Azure Cosmos DB 작업에 대한 서버 쪽 대기 시간을 모니터링하는 방법
description: Azure Cosmos DB 계정 또는 컨테이너 작업에 대한 서버 대기 시간을 모니터링하는 방법을 알아봅니다. Azure Cosmos DB 계정의 소유자는 Azure Cosmos 계정의 서버 쪽 대기 시간 문제를 알아볼 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
ms.author: esarroyo
author: StefArroyo
ms.date: 09/16/2021
ms.openlocfilehash: bd1dcc24533e7d537a350d7f8fd516e697b3fdb5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593822"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Azure Cosmos DB 컨테이너 또는 계정의 작업에 대한 서버 쪽 대기 시간을 모니터링하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다. 서버 쪽 대기 시간 메트릭은 작업의 서버 쪽 대기 시간을 보는 데 사용됩니다. Azure Cosmos DB 직접 연결을 통해 지점 읽기/쓰기 작업에 대해 10ms 미만의 SLA를 제공합니다. 지점 읽기 및 쓰기 작업의 경우 SLA는 [SLA 문서](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)에 설명된 대로 계산됩니다.

다음과 같은 지점 작업에 대해 비정상적으로 높은 대기 시간이 표시되는 경우 서버 쪽 대기 시간을 모니터링할 수 있습니다.

* 직접 연결 모드에서 파티션 키 및 ID가 있는 GET 또는 SET 작업
* 읽기 또는 쓰기 작업 또는
* 쿼리.

진단 로그를 조회하여 반환된 데이터의 크기를 확인할 수 있습니다. 쿼리 작업에 대한 대기 시간이 지속적으로 높은 경우 더 높은 [처리량 또는 사용된 RU/s](cosmosdb-monitor-logs-basic-queries.md)에 대한 진단 로그를 조회해야 합니다. 서버 쪽 대기 시간은 데이터가 클라이언트에 반환되기 전에 백 엔드 인프라에 소요된 시간을 보여줍니다. 백 엔드 대기 시간 문제를 제외하려면 이 메트릭을 살펴보는 것이 중요합니다.

## <a name="view-the-server-side-latency-metric"></a>서버 쪽 대기 시간 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
   
1. 왼쪽 탐색 모음에서 **모니터를** 선택한 다음 **메트릭** 을 선택합니다.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창" border="true":::

1. **메트릭** 창 > **리소스 선택** 에서 필요한 **구독** 및 **리소스 그룹** 을 선택합니다. **리소스 유형** 으로 **Azure Cosmos DB 계정** 을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용** 을 선택합니다.
   
   :::image type="content" source="./media/monitor-account-key-updates/select-account-scope.png" alt-text="메트릭을 볼 계정 범위를 선택합니다." border="true":::

1. 다음으로 사용 가능한 메트릭 목록에서 **서버 쪽 대기 시간** 메트릭을 선택합니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예시에서는 **서버 쪽 대기 시간** 을 선택하고 **Avg** 를 집계 값으로 합니다. 이러한 세부 정보 외에 메트릭의 **시간 범위** 와 **시간 단위** 를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다. 선택한 기간에 분당 서버 쪽 대기 시간을 볼 수 있습니다.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Azure Portal에서 서버 쪽 대기 시간 메트릭" border="true":::을 선택합니다

## <a name="filters-for-server-side-latency"></a>서버 쪽 대기 시간 필터

메트릭을 필터링하고 특정 **CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**, **PublicAPIType** 에 의해 표시되는 차트를 얻을 수도 있습니다. 

메트릭을 필터링하려면 **필터 추가** 를 선택하고 필요한 속성(예: **PublicAPIType**)을 선택하며 **sql** 와 같은 값을 선택합니다. **OperationType** 에 대한 다른 필터를 추가합니다. 그러면 그래프는 선택한 기간 동안 여러 작업에 대한 서버 쪽 대기 시간을 표시합니다. 저장 프로시저를 통해 실행되는 작업은 기록되지 않으므로 OperationType 메트릭 아래에서 사용할 수 없습니다.

각 작업에 대한 **서버 쪽 대기 시간** 메트릭은 다음 이미지와 같이 표시됩니다.

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="서버 쪽 대기 시간 메트릭에 대한 필터" border="true":::

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
