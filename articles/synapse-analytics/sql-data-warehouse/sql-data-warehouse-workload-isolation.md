---
title: 워크로드 격리
description: Azure Synapse Analytics에서 작업 그룹을 사용하여 워크로드 격리를 설정하는 방법에 대한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dec042c66ebed15a51d5c6c1f3ef6a3e3e2fb456
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428029"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics 작업 그룹 격리

이 문서에서는 작업 그룹을 사용하여 워크로드 격리를 구성하고, 리소스를 포함하며, 쿼리 실행을 위한 런타임 규칙을 적용하는 방법에 대해 설명합니다.

## <a name="workload-groups"></a>워크로드 그룹

작업 그룹은 일단의 요청에 대한 컨테이너이며 시스템에서 워크로드 격리를 포함하여 워크로드 관리를 구성하기 위한 기반이 됩니다.  작업 그룹은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문을 사용하여 만들어집니다.  간단한 워크로드 관리 구성에서 데이터 로드와 사용자 쿼리를 관리할 수 있습니다.  예를 들어 `wgDataLoads`라는 작업 그룹은 시스템에 로드되는 데이터에 대한 워크로드 측면을 정의합니다. 또한 `wgUserQueries`라는 작업 그룹은 쿼리를 실행하는 사용자가 시스템에서 데이터를 읽는 데 필요한 워크로드 측면을 정의합니다.

다음 섹션에서는 작업 그룹에서 격리, 포함, 리소스 정의 요청 및 실행 규칙 준수를 정의하는 기능을 제공하는 방법에 대해 설명합니다.

## <a name="workload-isolation"></a>워크로드 격리

워크로드 격리는 작업 그룹 전용으로 리소스가 예약되어 있음을 의미합니다.  [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문에서 MIN_PERCENTAGE_RESOURCE 매개 변수를 0보다 크게 구성하여 워크로드를 격리할 수 있습니다.  엄격한 SLA를 준수해야 하는 연속 실행 워크로드의 경우 격리를 통해 작업 그룹에서 리소스를 항상 사용할 수 있습니다.

워크로드 격리를 구성하면 보장된 동시성 수준이 암시적으로 정의됩니다. 예를 들어 `MIN_PERCENTAGE_RESOURCE`가 30%로 설정되고 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`가 2%로 설정된 작업 그룹에는 15개의 동시성이 보장됩니다.  `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`가 구성된 방법에 관계없이 15-2%의 리소스 슬롯이 항상 작업 그룹 내에 예약되므로 동시성 수준이 보장됩니다.  `REQUEST_MAX_RESOURCE_GRANT_PERCENT`가 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`보다 크고 `CAP_PERCENTAGE_RESOURCE`가 `MIN_PERCENTAGE_RESOURCE`보다 크면 요청마다 추가 리소스가 추가됩니다.  `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 및 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`가 같고 `CAP_PERCENTAGE_RESOURCE`가 `MIN_PERCENTAGE_RESOURCE`보다 크면 추가 동시성이 가능합니다.  보장된 동시성을 결정하려면 아래 방법을 고려해 보세요.

[보장된 동시성] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> min_percentage_resource에 대한 특정 서비스 수준 실행 가능한 최솟값이 있습니다.  자세한 내용은 [유효 값](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)을 참조하세요.

워크로드 격리가 없으면 요청이 리소스의 [공유 풀](#shared-pool-resources)에서 작동합니다.  공유 풀의 리소스에 대한 액세스가 보장되지 않으며 [중요도](sql-data-warehouse-workload-importance.md) 기준으로 할당됩니다.

활성 요청이 작업 그룹에 없는 경우에도 리소스가 작업 그룹에 할당되므로 워크로드 격리 구성은 신중하게 수행해야 합니다. 격리를 과도하게 구성하면 전체 시스템 사용률이 감소될 수 있습니다.

사용자는 100% 워크로드 격리를 구성하는 워크로드 관리 솔루션을 사용하지 않아야 합니다. 모든 작업 그룹에 구성된 min_percentage_resource의 합계가 100%인 경우 100% 격리가 달성됩니다.  이러한 유형의 구성은 지나치게 제한적이고 엄격하므로 실수로 잘못 분류된 리소스 요청을 수행할 여지가 거의 없습니다. 격리하도록 구성되지 않은 작업 그룹에서 하나의 요청을 실행할 수 있도록 허용하는 프로비저닝이 있습니다. 이 요청에 할당된 리소스는 시스템 DMV에 0으로 표시되고 시스템 예약 리소스에서 smallrc 수준의 리소스 부여를 받습니다.

> [!NOTE]
> 리소스 사용률을 최적화하려면 일부 격리를 활용하여 SLA를 충족시키고 [워크로드 중요도](sql-data-warehouse-workload-importance.md)에 따라 액세스되는 공유 리소스와 혼합되는 워크로드 관리 솔루션을 고려해야 합니다.

## <a name="workload-containment"></a>워크로드 포함

워크로드 포함은 작업 그룹에서 사용할 수 있는 리소스의 양을 제한하는 것을 의미합니다.  워크로드 포함은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문에서 CAP_PERCENTAGE_RESOURCE 매개 변수를 100 미만으로 구성하여 수행됩니다.  사용자가 임시 쿼리를 통해 가상(what-if) 분석을 실행할 수 있도록 시스템에 대한 읽기 액세스 권한이 필요한 시나리오를 고려합니다.  이러한 유형의 요청은 시스템에서 실행되는 다른 워크로드에 부정적인 영향을 줄 수 있습니다.  포함을 구성하면 리소스의 양이 제한됩니다.

워크로드 포함을 구성하면 최대 동시성 수준이 암시적으로 정의됩니다.  CAP_PERCENTAGE_RESOURCE가 60%로 설정되고 REQUEST_MIN_RESOURCE_GRANT_PERCENT가 1%로 설정되면 작업 그룹에 대해 최대 60개의 동시성 수준이 허용됩니다.  최대 동시성을 결정하려면 아래의 포함 방법을 고려해 보세요.

[최대 동시성] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> MIN_PERCENTAGE_RESOURCE가 0보다 큰 수준인 작업 그룹이 만들어지면 작업 그룹의 유효 CAP_PERCENTAGE_RESOURCE가 100%에 도달하지 않습니다.  유효 런타임 값은 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

## <a name="resources-per-request-definition"></a>요청 정의당 리소스 수

작업 그룹은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문에서 REQUEST_MIN_RESOURCE_GRANT_PERCENT 및 REQUEST_MAX_RESOURCE_GRANT_PERCENT 매개 변수를 사용하여 요청당 할당되는 최소 및 최대 리소스 양을 정의하는 메커니즘을 제공합니다.  이 경우 리소스는 메모리입니다. CPU 리소스는 워크로드 그룹의 CAP_PERCENTAGE_RESOURCE 값으로 제한되며 개별 요청 수준에서 제어되지 않습니다. 이러한 값을 구성하면 시스템에서 달성할 수 있는 리소스의 양과 동시성 수준이 결정됩니다.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT는 기본적으로 REQUEST_MIN_RESOURCE_GRANT_PERCENT에 지정된 것과 동일한 값으로 설정되는 선택적 매개 변수입니다.

리소스 클래스를 선택하는 것처럼 REQUEST_MIN_RESOURCE_GRANT_PERCENT를 구성하면 요청에서 사용하는 리소스의 값이 설정됩니다.  설정된 값으로 표시된 리소스의 양은 요청이 실행되기 전에 해당 요청에 할당되도록 보장됩니다.  리소스 클래스에서 작업 그룹으로 마이그레이션하는 고객의 경우 [방법](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) 문서에 따라 시작 지점으로 리소스 클래스에서 작업 그룹으로 매핑하는 것이 좋습니다.

REQUEST_MAX_RESOURCE_GRANT_PERCENT를 REQUEST_MIN_RESOURCE_GRANT_PERCENT보다 큰 값으로 구성하면 시스템에서 요청당 더 많은 리소스를 할당할 수 있습니다.  요청을 예약하는 동안 시스템에서 공유 풀의 리소스 가용성과 시스템의 현재 부하를 기반으로 하여 REQUEST_MIN_RESOURCE_GRANT_PERCENT와 REQUEST_MAX_RESOURCE_GRANT_PERCENT 사이에서 요청에 대한 실제 리소스 할당을 결정합니다.  쿼리가 예약되면 리소스가 리소스의 [공유 풀](#shared-pool-resources)에 있어야 합니다.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 및 REQUEST_MAX_RESOURCE_GRANT_PERCENT에는 유효 MIN_PERCENTAGE_RESOURCE 및 CAP_PERCENTAGE_RESOURCE 값에 따라 달라지는 유효 값이 있습니다.  유효 런타임 값은 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

## <a name="execution-rules"></a>실행 규칙

임시 보고 시스템에서 고객은 다른 사용자의 생산성에 심각한 영향을 주는 런어웨이 쿼리를 실수로 실행할 수 있습니다.  시스템 관리자는 시스템 리소스를 확보하기 위해 런어웨이 쿼리를 종료하는 데 시간을 할애해야 합니다.  작업 그룹은 지정된 값을 초과한 쿼리를 취소하도록 쿼리 실행 시간 제한 규칙을 구성할 수 있는 기능을 제공합니다.  규칙은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문에서 `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 설정하여 구성됩니다.

## <a name="shared-pool-resources"></a>공유 풀 리소스

공유 풀 리소스는 격리하도록 구성되지 않는 리소스입니다.  MIN_PERCENTAGE_RESOURCE가 0으로 설정된 작업 그룹은 공유 풀의 리소스를 활용하여 요청을 실행합니다.  CAP_PERCENTAGE_RESOURCE가 MIN_PERCENTAGE_RESOURCE보다 큰 작업 그룹에도 공유 리소스가 사용되었습니다.  공유 풀에서 사용할 수 있는 리소스의 양은 다음과 같이 계산됩니다.

[공유 풀] = 100 - [모든 작업 그룹에 걸친 `MIN_PERCENTAGE_RESOURCE`의 합계]

공유 풀의 리소스에 대한 액세스는 [중요도](sql-data-warehouse-workload-importance.md) 기준으로 할당됩니다.  중요도 수준이 동일한 요청은 선입선출 방식으로 공유 풀 리소스에 액세스합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 워크로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [리소스 클래스를 작업 그룹으로 변환](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [워크로드 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md)  
