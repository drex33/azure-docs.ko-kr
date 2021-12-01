---
title: 관리 효율성 및 모니터링 - 쿼리 작업, 리소스 사용률
description: Azure Synapse Analytics를 관리하고 모니터링하는 데 사용할 수 있는 기능에 대해 알아봅니다. Azure Portal과 DMV(Dynamic Management Views)를 사용하여 데이터 웨어하우스의 쿼리 작업 및 리소스 사용률을 이해합니다.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: wiassaf
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2eec6c6114087c26cf3bbdd19901d6da3e17ecea
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133356578"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 리소스 사용률 및 쿼리 작업 모니터링

Azure Synapse Analytics는 Azure Portal 내에 다양한 모니터링 환경을 제공하여 데이터 웨어하우스 워크로드와 관련된 인사이트를 제공합니다. 데이터 웨어하우스를 모니터링할 때는 구성 가능한 보존 기간, 경고, 권장 사항, 메트릭과 로그용 사용자 지정 가능한 차트 및 대시보드를 제공하는 도구인 Azure Portal을 사용하는 것이 좋습니다. 또한 이 포털을 통해 Log analytics가 있는 Azure Monitor(로그)와 같은 다른 Azure 모니터링 서비스와 통합하여 데이터 웨어하우스뿐만 아니라 Azure 분석 플랫폼 전체에 대한 전체적인 모니터링 환경을 제공함으로써 통합된 모니터링 환경을 구현할 수 있습니다. 이 문서에서는Synapse SQL을 사용하여 분석 플랫폼을 최적화하고 관리하는 데 사용할 수 있는 모니터링 기능에 대해 설명합니다.

## <a name="resource-utilization"></a>리소스 사용률

Synapse SQL용 Azure Portal에서 다음 메트릭을 사용할 수 있습니다. 이러한 메트릭은 [Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics)를 통해 나타납니다.

| 메트릭 이름             | Description                                                  | 집계 형식 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 비율          | 데이터 웨어하우스에 대한 모든 노드에서의 CPU 사용률      | 평균, 최솟값, 최댓값    |
| 데이터 IO 비율      | 데이터 웨어하우스에 대한 모든 노드에서의 IO 사용률       | 평균, 최솟값, 최댓값    |
| 메모리 비율       | 데이터 웨어하우스에 대한 모든 노드에서의 메모리 사용률(SQL Server) | 평균, 최솟값, 최댓값   |
| 활성 쿼리          | 시스템에서 실행 중인 활성 쿼리 수             | 합계              |
| 큐에 대기 중인 쿼리          | 실행을 시작하기 위해 대기 중인 쿼리 수          | 합계              |
| 성공적인 연결  | 데이터베이스에 대해 성공한 연결(로그인) 수 | 합계, 개수       |
| 실패한 연결      | 데이터베이스에 대해 실패한 연결(로그인) 수 | 합계, 개수       |
| 방화벽에 의해 차단     | 차단된 데이터 웨어하우스에 대한 로그인 횟수     | 합계, 개수       |
| DWU 제한               | 데이터 웨어하우스의 서비스 수준 목표                | 평균, 최솟값, 최댓값    |
| DWU 백분율          | CPU 비율과 데이터 IO 비율 사이의 최댓값        | 평균, 최솟값, 최댓값    |
| DWU 사용됨                | DWU 한도 * DWU 비율                                   | 평균, 최솟값, 최댓값    |
| 캐시 적중 비율    | (캐시 적중/캐시 누락) * 100, 여기서 캐시 적중은 로컬 SSD 캐시에서 적중된 모든 columnstore 세그먼트에 대한 합계이며, 캐시 누락은 모든 노드에 걸쳐 로컬 SSD 캐시에서 누락된 columnstore 세그먼트에 대한 합계입니다. | 평균, 최솟값, 최댓값    |
| 캐시 사용 비율   | (사용된 캐시/캐시 용량) * 100, 여기서 사용된 캐시는 모든 노드에 걸친 로컬 SSD 캐시의 모든 바이트에 대한 합계이며, 캐시 용량은 모든 노드에 걸친 로컬 SSD 캐시의 스토리지 용량에 대한 합계입니다 | 평균, 최솟값, 최댓값    |
| 로컬 tempdb 백분율 | 모든 컴퓨팅 노드의 로컬 tempdb 사용률 - 5분마다 값을 내보냅니다. | 평균, 최솟값, 최댓값    |

메트릭을 보고 경고를 설정할 때 고려해야 할 사항:

- 사용된 DWU는 SQL 풀 전체에서 **사용량에 대한 상위 수준 표시** 만 나타내며 포괄적인 사용률 표시기가 아닙니다. 규모를 확장 또는 축소할지를 결정하려면 DWU의 영향을 받을 수 있는 모든 요소(예: 동시성, 메모리, tempdb 및 적응 캐시 용량)를 고려합니다. 비즈니스 목표를 충족하는 데 가장 적합한 작업을 결정하기 위해 [다른 DWU 설정에서 워크로드를 실행](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units)하는 것이 좋습니다.
- 실패한 연결과 성공한 연결이 서버 자체가 아닌 특정 데이터 웨어하우스에 보고됩니다.
- 메모리 백분율은 데이터 웨어하우스가 유휴 상태에 있는 경우에도 사용률을 반영합니다. 이는 활성 작업 메모리 사용을 반영하지 않습니다. 다른 메트릭(tempdb, gen2 캐시)과 함께 이 메트릭을 사용하고 추적하여 추가 캐시 용량을 조정하면 요구 사항에 맞게 워크로드 성능이 향상되는 경우에 대한 전체적인 의사 결정을 내릴 수 있습니다.

## <a name="query-activity"></a>쿼리 작업

T-SQL을 통해 Synapse SQL을 모니터링할 때의 프로그래밍 방식 환경을 위해 서비스에서 DMV(Dynamic Management Views)의 세트를 제공합니다. 이러한 보기는 워크로드로 인한 성능 병목 상태를 적극적으로 해결하고 식별할 때 유용합니다.

Synapse SQL에 적용되는 DMV 목록을 보려면 [이 설명서](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs)를 참조하세요. 

## <a name="metrics-and-diagnostics-logging"></a>메트릭 및 진단 로깅 

메트릭과 로그는 모두 Azure Monitor, 특히 [Azure Monitor 로그](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 구성 요소로 내보낼 수 있으며, [로그 쿼리](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)를 통해 프로그래밍 방식으로 액세스할 수 있습니다. Synapse SQL의 로그 대기 시간은 약 10-15분입니다. 대기 시간에 영향을 주는 요인에 대한 자세한 내용은 다음 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 방법 가이드에서는 데이터 웨어하우스를 모니터링하고 관리할 때의 일반적인 시나리오와 사용 사례에 대해 설명합니다.

- [DMV를 사용하여 데이터 웨어하우스 워크로드 모니터링](sql-data-warehouse-manage-monitor.md)