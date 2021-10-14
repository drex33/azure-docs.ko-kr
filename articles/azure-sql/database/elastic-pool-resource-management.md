---
title: 조밀한 탄력적 풀의 리소스 관리
description: 많은 데이터베이스를 포함하는 Azure SQL Database 탄력적 풀에서 컴퓨팅 리소스를 관리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 10/13/2021
ms.openlocfilehash: 1bc89a91bde0cc720b56f52900c2e0b57542dfa4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008674"
---
# <a name="resource-management-in-dense-elastic-pools"></a>조밀한 탄력적 풀의 리소스 관리
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database [탄력적 풀](./elastic-pool-overview.md)은 다양한 리소스 사용으로 많은 데이터베이스를 관리하는 비용 효율적인 솔루션입니다. 탄력적 풀의 모든 데이터베이스는 **언제든지 풀에 있는 일부 데이터베이스만 컴퓨팅 리소스를 사용** 한다는 가정하에 CPU, 메모리, 작업자 스레드, 스토리지 공간, tempdb 등의 동일한 리소스 할당을 공유합니다. 이러한 가정을 통해 탄력적 풀을 비용 효율적으로 사용할 수 있습니다. 고객은 각 개별 데이터베이스에 필요할 수 있는 모든 리소스에 대해 지불하는 대신, 풀의 모든 데이터베이스 간에 공유되는 훨씬 더 작은 리소스 집합에 대해 지불합니다.

## <a name="resource-governance"></a>리소스 거버넌스

리소스를 공유하려면 시스템에서 리소스 사용을 신중하게 제어하여 리소스 사용량이 많은 데이터베이스가 동일한 탄력적 풀의 다른 데이터베이스에 영향을 주는 “잡음이 많은 환경” 효과를 최소화해야 합니다. 동시에 시스템은 HADR(고가용성 및 재해 복구), 백업 및 복원, 모니터링, 쿼리 저장소, 자동 튜닝 등과 같은 기능에 대한 충분한 리소스를 제공하여 안정적으로 작동해야 합니다.

Azure SQL Database는 프로세스 수준 리소스 거버넌스를 위한 Windows [작업 개체](/windows/win32/procthread/job-objects), 스토리지 할당량 관리를 위한 Windows [FSRM](/windows-server/storage/fsrm/fsrm-overview)(파일 서버 리소스 관리자), SQL Database 내에서 리소스 관리를 구현하기 위한 수정 및 확장된 버전의 SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)를 포함하여 여러 리소스 거버넌스 메커니즘을 사용하여 해당 목표를 달성합니다.

탄력적 풀의 주 디자인 목표는 비용 효율적입니다. 이러한 이유로 시스템은 고객에게 적절한 수준의 컴퓨팅 리소스를 할당하지만, 허용되는 최대 개수나 접근하는 데이터베이스의 개수를 나타내는 풀인 _조밀한_ 풀을 의도적으로 만들 수 있도록 합니다. 이와 같은 이유로 시스템은 내부 프로세스에 필요한 모든 리소스를 예약하지는 않지만 내부 프로세스와 사용자 워크로드 간에 리소스를 공유할 수 있습니다.

이 방법을 통해 고객은 조밀한 탄력적 풀을 사용하여 적절한 성능 및 주요 비용 절감을 달성할 수 있습니다. 그러나 조밀한 풀의 여러 데이터베이스에 대한 워크로드가 충분히 높은 경우, 리소스 경합이 매우 커집니다. 리소스 경합은 사용자 워크로드 성능을 줄이며 내부 프로세스에 부정적인 영향을 줄 수 있습니다.

> [!IMPORTANT]
> 활성 데이터베이스가 많은 조밀한 풀에서는 풀의 데이터베이스 수를 [DTU](resource-limits-dtu-elastic-pools.md) 및 [vCore](resource-limits-vcore-elastic-pools.md) 탄력적 풀에 대해 문서화된 최댓값까지 늘리는 것이 불가능할 수 있습니다.
>
> 리소스 경합 및 성능 문제를 발생시키지 않고 조밀한 풀에 배치할 수 있는 데이터베이스 수는 동시 활성 데이터베이스 수 및 각 데이터베이스의 사용자 워크로드에 의한 리소스 사용량에 따라 달라집니다. 이 수는 시간이 지나면서 사용자 워크로드가 달라짐에 따라 변경될 수 있습니다.
> 
> 또한 데이터베이스당 최소 vCore 수 또는 데이터베이스당 최소 DTU 설정이 0보다 큰 값으로 설정된 경우 풀의 최대 데이터베이스 수는 암시적으로 제한됩니다. 자세한 내용은 [풀된 vCore 데이터베이스의 데이터베이스 속성 및 풀된](resource-limits-vcore-elastic-pools.md#database-properties-for-pooled-databases) [DTU 데이터베이스의 데이터베이스 속성을 참조하세요.](resource-limits-dtu-elastic-pools.md#database-properties-for-pooled-databases)

조밀하게 압축된 풀에서 리소스 경합이 발생하는 경우 고객은 다음 작업 중 하나 이상을 선택하여 이를 완화할 수 있습니다.

- 쿼리 워크로드를 조정하여 리소스 사용량을 줄이거나 시간이 지남에 따라 여러 데이터베이스에 걸쳐 리소스 소비를 분산합니다.
- 일부 데이터베이스를 다른 풀로 이동하거나 독립 실행형 데이터베이스를 만들어 풀 밀도를 줄입니다.
- 풀을 스케일 업하여 더 많은 리소스를 얻습니다.

마지막 두 작업을 구현하는 방법에 대한 제안은 이 문서의 뒷부분에 나오는 [운영 권장 사항](#operational-recommendations)을 참조하세요. 리소스 경합을 줄이면 사용자 워크로드 및 내부 프로세스의 이점을 모두 활용할 수 있으며, 시스템에서 필요한 서비스 수준을 안정적으로 유지할 수 있습니다.

## <a name="monitoring-resource-consumption"></a>리소스 사용량 모니터링

조밀한 탄력적 풀을 사용하는 고객은 리소스 경합으로 인한 성능 저하를 방지하려면 리소스 사용량을 미리 모니터링하고, 리소스 경합이 증가하여 워크로드에 영향을 준다면 시기 적절한 조치를 취합니다. 시간이 지남에 따라 사용자 워크로드의 변경, 데이터 볼륨 및 배포의 변경, 풀 밀도의 변경 및 Azure SQL Database 서비스의 변경으로 인해 풀의 리소스 사용량이 변경되기 때문에 계속해서 모니터링하는 것이 중요합니다.

Azure SQL Database는 해당 유형의 모니터링과 관련된 몇 가지 메트릭을 제공합니다. 각 메트릭에 대해 권장되는 평균 값을 초과하면 풀의 리소스 경합이 표시되며 앞에서 설명한 작업 중 하나를 사용하여 주소를 지정해야 합니다.

|메트릭 이름|설명|권장 평균 값|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|기본 운영 체제에 따라 측정된 탄력적 풀과 연결된 SQL 프로세스의 CPU 사용률입니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기와 `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `sqlserver_process_core_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다. 이 값은 동일한 탄력적 풀의 모든 데이터베이스에 대해 동일합니다.|70% 미만. 때때로 최대 90%까지 짧은 급증이 허용될 수 있습니다.|
|`max_worker_percent`|[작업자 스레드](/sql/relational-databases/thread-and-task-architecture-guide) 사용률입니다. 풀 자체뿐만 아니라 풀의 각 데이터베이스에 대해 제공됩니다. 데이터베이스 수준에서 작업자 스레드 수에 대한 제한은 다르며 풀 수준에서는 두 수준 모두에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기와 `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `workers_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다.|80% 미만. 최대 100%까지 증가하면 연결 시도 및 쿼리가 실패합니다.|
|`avg_data_io_percent`|읽기 및 쓰기 물리적 IO에 대한 IOPS 사용률입니다. 풀 자체뿐만 아니라 풀의 각 데이터베이스에 대해 제공됩니다. 데이터베이스 수준과 풀 수준의 IOPS 수에는 다른 제한이 있으므로, 두 수준 모두에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기와 `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `physical_data_read_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다.|80% 미만. 때때로 최대 100%까지 짧은 급증이 허용될 수 있습니다.|
|`avg_log_write_percent`|트랜잭션 로그 쓰기 IO의 처리량 사용률입니다. 풀 자체뿐만 아니라 풀의 각 데이터베이스에 대해 제공됩니다. 데이터베이스 수준과 풀 수준의 로그 처리량에는 다른 제한이 있으므로 두 수준 모두에서 이 메트릭을 모니터링하는 것이 좋습니다. 모든 데이터베이스의 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기와 `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `log_write_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다. 이 메트릭이 100%에 가까운 경우 모든 데이터베이스 수정(INSERT, UPDATE, DELETE, MERGE statement, SELECT ... INTO, BULK INSERT 등)의 속도가 느려집니다.|90% 미만. 때때로 최대 100%까지 짧은 급증이 허용될 수 있습니다.|
|`oom_per_second`|탄력적 풀에서의 OOM(메모리 부족) 오류(메모리 압력 표시기)입니다. [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 보기에서 사용할 수 있습니다. 이 메트릭을 계산하는 예제 쿼리는 [예제](#examples)를 참조하세요.|0|
|`avg_storage_percent`|탄력적 풀 내의 모든 데이터베이스에서 데이터에 사용되는 총 스토리지 공간입니다. 데이터베이스 파일에는 빈 공간이 포함되지 않습니다. `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `storage_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다.|80% 미만. 데이터 증가가 없는 풀에 100% 접근할 수 있습니다.|
|`avg_allocated_storage_percent`|탄력적 풀 내의 모든 데이터베이스에서 스토리지의 데이터베이스 파일에 사용되는 총 스토리지 공간입니다. 데이터베이스 파일에 빈 공간이 포함됩니다. `master` 데이터베이스의 [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기에서 사용할 수 있습니다. 또한 이 메트릭은 `allocated_data_storage_percent`으로 [명명된](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) Azure Monitor로 내보내지고, Azure Portal에서 볼 수 있습니다.|90% 미만. 데이터 증가가 없는 풀에 100% 접근할 수 있습니다.|
|`tempdb_log_used_percent`|`tempdb` 데이터베이스의 트랜잭션 로그 공간 사용률입니다. 한 데이터베이스에서 만든 임시 개체가 동일한 탄력적 풀의 다른 데이터베이스에 표시되지 않더라도 `tempdb`는 동일한 풀에 있는 모든 데이터베이스에 대한 공유 리소스입니다. 풀에 있는 하나의 데이터베이스에서 시작된 `tempdb`의 장기 또는 분리된 트랜잭션은 트랜잭션 로그의 상당 부분을 소비할 수 있으며, 동일한 풀에 있는 다른 데이터베이스의 쿼리에 오류가 발생합니다. [Sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) 및 [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) 보기에서 파생됩니다. 이 메트릭은 Azure Monitor로 내보내지고 Azure Portal에서 볼 수 있습니다. 이 메트릭의 현재 값을 반환하는 샘플 쿼리는 [예제](#examples)를 참조하세요.|50% 미만. 간혹 최대 80%까지 급증하는 것은 허용됩니다.|
|||

해당 메트릭 외에도 Azure SQL Database는 실제 리소스 거버넌스 제한을 반환하는 보기와 리소스 풀 수준 및 워크로드 그룹 수준에서 리소스 사용률 통계를 반환하는 추가 보기를 제공합니다.

|뷰 이름|Description|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|현재 데이터베이스 또는 탄력적 풀의 리소스 거버넌스 메커니즘에서 사용되는 실제 구성 및 용량 설정을 반환합니다.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|현재 리소스 풀 상태, 리소스 풀의 현재 구성 및 리소스 풀 누적 통계에 대한 정보를 반환합니다.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|누적 워크로드 그룹 통계 및 워크로드 그룹의 현재 구성을 반환합니다. 이 보기는 `pool_id` 열에 있는 sys.dm_resource_governor_resource_pools와 조인하여 리소스 풀 정보를 가져올 수 있습니다.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|최근 32분 동안의 리소스 풀 사용률 통계를 반환합니다. 각 행은 20초 간격을 나타냅니다. `delta_` 열은 간격 동안의 각 통계에 대한 변경 내용을 반환합니다.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|최근 32분 동안의 워크로드 그룹 사용률 통계를 반환합니다. 각 행은 20초 간격을 나타냅니다. `delta_` 열은 간격 동안의 각 통계에 대한 변경 내용을 반환합니다.|
|||

> [!TIP]
> 서버 관리자 이외의 보안 주체를 사용하여 이러한 뷰와 기타 동적 관리 뷰를 쿼리하려면 이 보안 주체를 서버 역할 에 `##MS_ServerStateReader##` [](security-server-roles.md)추가합니다.

해당 보기를 사용하여 리소스 사용률을 모니터링하고 리소스 경합 문제를 거의 실시간으로 해결할 수 있습니다. 지역 복제본을 비롯하여 주 복제본 및 읽기 가능한 보조 복제본의 사용자 워크로드는 `SloSharedPool1` 리소스 풀 및 `UserPrimaryGroup.DBId[N]` 워크로드 그룹으로 분류됩니다. 여기서 `N`은 데이터베이스 ID 값을 나타냅니다.

현재 리소스 사용률을 모니터링하는 것 외에도 조밀한 풀을 사용하는 고객은 별도의 데이터 저장소에서 기록 리소스 사용률 데이터를 유지할 수 있습니다. 이 데이터는 예측 분석에서 기록 및 계절 추세에 따라 리소스 사용률을 사전에 관리하는 데 사용할 수 있습니다.

## <a name="operational-recommendations"></a>운영 권장 사항

**충분한 리소스 위쪽 공간을 확보하세요**. 리소스 경합 및 성능 저하가 발생하는 경우에는 이전에 설명한 대로 영향을 받는 탄력적 풀에서 일부 데이터베이스를 이동하거나 풀을 스케일 업해야 할 수 있습니다. 그러나 해당 작업을 완료하려면 추가 컴퓨팅 리소스가 필요합니다. 특히 프리미엄 및 중요 비즈니스용 풀은 이동 중인 데이터베이스의 모든 데이터, 또는 풀이 스케일 업된 경우 해당 작업을 수행하려면 탄력적 풀에 있는 모든 데이터베이스에 대한 모든 데이터를 전송해야 합니다. 데이터 전송은 장기 실행되고 리소스를 많이 사용하는 작업입니다. 풀이 이미 높은 리소스 압력 상태에 있는 경우 완화 작업 자체가 성능을 저하시킵니다. 극단적인 경우에는 필요한 리소스를 사용할 수 없기 때문에 데이터베이스 이동 또는 풀 스케일 업을 통해 리소스 경합을 해결할 수 없습니다. 이 경우 영향을 받는 탄력적 풀에 대한 쿼리 워크로드를 일시적으로 줄이는 것이 유일한 해결책이 될 수 있습니다.

조밀한 풀을 사용하는 고객은 앞에서 설명한 대로 리소스 사용률 추세를 면밀하게 모니터링하며, 메트릭이 권장 범위 내에 유지되고 탄력적 풀에 충분한 리소스가 남아 있는 동안 완화 작업을 수행해야 합니다.

리소스 사용률은 각 데이터베이스와 각 탄력적 풀에 대해 시간이 지나면서 변경되는 여러 요소에 따라 다릅니다. 조밀한 풀에서 최적의 가격/성능 비율을 달성하려면 지속적으로 모니터링하고 균형을 조정해야 합니다. 즉 더 많이 사용되는 풀에서 더 적게 사용되는 풀로 데이터베이스를 이동하고 증가된 워크로드를 수용하기 위해 필요한 대로 새 풀을 만드는 것입니다.

**“핫” 데이터베이스를 이동하지 마세요**. 기본적으로 소수의 데이터베이스를 과도하게 사용하여 풀 수준의 리소스 경합이 발생하는 경우 해당 데이터베이스를 더 적게 사용되는 풀로 이동하거나 독립 실행형 데이터베이스를 만드는 것이 좋습니다. 그러나 데이터베이스를 과도하게 사용하는 동안에는 이 작업을 수행하지 않는 것이 좋습니다. 이동 작업을 하면 이동하는 데이터베이스와 전체 풀의 성능이 더 저하되기 때문입니다. 대신 높은 사용률이 진정될 때까지 기다리거나, 풀 수준에서 리소스 압력을 완화하기 위해 더 적게 사용되는 데이터베이스를 이동합니다. 하지만 사용률이 매우 낮은 데이터베이스를 이동하는 것은 풀 수준에서 리소스 사용률을 상당히 줄이지 않기 때문에 이 경우 어떠한 이점도 없습니다.

**“격리” 풀에 새 데이터베이스를 만듭니다**. 데이터베이스당 테넌트 모델을 사용하는 애플리케이션과 같이 새 데이터베이스가 자주 만들어지는 시나리오에서 기존 탄력적 풀에 배치되는 새 데이터베이스가 예기치 않게 상당한 양의 리소스를 사용하고 풀의 다른 데이터베이스 및 내부 프로세스에 영향을 줄 수 있습니다. 이러한 위험을 완화하려면 리소스를 충분히 할당하여 별도의 “격리” 풀을 만듭니다. 아직 알 수 없는 리소스 소비 패턴의 새 데이터베이스에 이 풀을 사용합니다. 데이터베이스가 주 또는 월과 같은 비즈니스 주기 동안 이 풀에 머무르고 해당 리소스 사용량이 알려진 경우, 이 추가 리소스 사용량을 수용할 수 있는 충분한 용량이 있는 풀로 이동할 수 있습니다.

**사용되는 공간 및 할당된 공간을 모두 모니터링합니다**. 할당된 풀 공간(풀의 모든 데이터베이스에 대한 스토리지 내 모든 데이터베이스 파일의 전체 크기)이 최대 풀 크기에 도달하면 공간 부족 오류가 발생할 수 있습니다. 할당된 공간이 많아지고 최대 풀 크기에 도달하려고 하는 경우 완화 옵션은 다음과 같습니다.
- 일부 데이터베이스를 풀 외부로 이동하여 할당된 총 공간 줄이기
- 파일의 빈 할당 공간을 줄이기 위해 데이터베이스 파일 [축소](file-space-manage.md)
- 최대 풀 크기가 더 큰 서비스 목표로 풀 스케일 업

풀 공간(풀의 모든 데이터베이스에 있는 데이터의 전체 크기, 파일에 빈 공간을 포함하지 않음)이 많아지고 최대 풀 크기에 도달하려고 하는 경우 완화 옵션은 다음과 같습니다.
- 사용 중인 전체 공간을 줄이기 위해 일부 데이터베이스를 풀 외부로 이동
- 데이터베이스 외부에서 데이터 이동(보관) 또는 더 이상 필요 없는 데이터 삭제
- [데이터 압축](/sql/relational-databases/data-compression/data-compression) 구현
- 최대 풀 크기가 더 큰 서비스 목표로 풀 스케일 업

**지나치게 조밀한 서버를 사용하지 않습니다**. Azure SQL Database는 서버당 최대 5000개의 데이터베이스를 [지원](./resource-limits-logical-server.md)합니다. 수천 개의 데이터베이스에서 탄력적 풀을 사용하는 고객은 단일 서버에 여러 탄력적 풀을 배치하는 것이 좋습니다. 이 경우 총 데이터베이스 수는 지원되는 최대치입니다. 그러나 수천 개의 데이터베이스가 있는 서버는 운영 상의 문제를 만듭니다. 서버의 모든 데이터베이스를 열거해야 하는 작업(예: 포털의 데이터베이스 보기)은 더 느려집니다. 서버 수준 로그인 또는 방화벽 규칙의 잘못된 수정과 같은 운영 오류는 더 많은 데이터베이스에 영향을 줍니다. 서버를 실수로 삭제하는 경우, 삭제된 서버에서 데이터베이스를 복구하려면 Microsoft 지원의 도움이 필요하며, 영향을 받는 모든 데이터베이스의 작동이 중단될 수 있습니다.

서버당 데이터베이스 수를 지원되는 최대값보다 낮은 수로 제한하는 것이 좋습니다. 대부분의 시나리오에서 서버당 최대 1000-2000 개의 데이터베이스를 사용하는 것이 가장 좋습니다. 실수로 인한 서버 삭제 가능성을 줄이려면 서버 또는 해당 리소스 그룹에 [삭제 잠금을](../../azure-resource-manager/management/lock-resources.md) 배치합니다.

## <a name="examples"></a>예

### <a name="monitoring-memory-utilization"></a>메모리 사용률 모니터링

이 쿼리는 지난 32분 동안 각 리소스 풀에 대한 `oom_per_second` 메트릭을 계산합니다. 이 쿼리는 탄력적 풀의 모든 데이터베이스에서 실행할 수 있습니다.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>`tempdb` 로그 공간 사용률 모니터링

이 쿼리는 최대 허용 크기를 기준으로 `tempdb` 트랜잭션 로그의 상대적 사용률을 보여 주는 `tempdb_log_used_percent` 메트릭의 현재 값을 반환합니다. 이 쿼리는 탄력적 풀의 모든 데이터베이스에서 실행할 수 있습니다.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>다음 단계

- 탄력적 풀에 대한 소개는 [Azure SQL Database에서 여러 데이터베이스를 관리하고 스케일링하는 데 도움이 되는 탄력적 풀](./elastic-pool-overview.md)을 참조하세요.
- 리소스 사용률을 줄이기 위해 쿼리 워크로드를 튜닝하는 방법에 대한 자세한 내용은 [모니터링 및 튜닝](monitoring-tuning-index.yml), [모니터링 및 성능 튜닝](./monitor-tune-overview.md)을 참조하세요.
