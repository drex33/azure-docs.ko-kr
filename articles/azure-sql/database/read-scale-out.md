---
title: 복제본에 대한 쿼리 읽기
description: Azure SQL은 읽기 확장이라는 읽기 워크로드에 읽기 전용 복제본의 용량을 사용하는 기능을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 09/23/2021
ms.openlocfilehash: f5acdf621c04ba48664004bbb1f28ae1c0914fcb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447040"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>읽기 전용 복제본을 사용하여 읽기 전용 쿼리 워크로드의 오프로드
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[고가용성 아키텍처](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)의 일부인 프리미엄 및 중요 비즈니스용 서비스 계층의 각 단일 데이터베이스, 탄력적 풀 데이터베이스, 관리형 인스턴스는 주 읽기-쓰기 복제본과 여러 보조 읽기 전용 복제본으로 자동으로 프로비저닝됩니다. 보조 복제본은 주 복제본과 동일한 컴퓨팅 크기로 프로비저닝됩니다. ‘읽기 확장’ 기능을 사용하면 읽기 전용 복제본에서 실행하는 대신 읽기 전용 복제본 중 하나의 컴퓨팅 용량을 사용하여 읽기 전용 워크로드를 오프로드할 수 있습니다. 이러한 방식으로 일부 읽기 전용 워크로드는 읽기-쓰기 워크로드에서 격리될 수 있으며 성능은 그대로 유지됩니다. 이 기능은 분석과 같이 논리적으로 격리된 읽기 전용 워크로드를 포함한 애플리케이션을 위한 것입니다. 프리미엄 및 중요 비즈니스용 서비스 계층에서 애플리케이션은 추가 비용 없이 이 추가 용량을 사용하여 성능상의 이점을 얻을 수 있습니다.

*읽기 확장* 기능은 하나 이상의 [보조 복제본이](service-tier-hyperscale-replicas.md) 추가된 경우 하이퍼스케일 서비스 계층에서도 사용할 수 있습니다. 명명된 하이퍼스케일 보조 [복제본은](service-tier-hyperscale-replicas.md#named-replica-in-preview) 독립적인 크기 조정, 액세스 격리, 워크로드 격리, 대규모 읽기 확장 및 기타 이점을 제공합니다. 여러 보조 HA 복제본은 하나의 보조 [HA 복제본에서](service-tier-hyperscale-replicas.md#high-availability-replica) 사용할 수 있는 것보다 더 많은 리소스가 필요한 읽기 전용 워크로드의 부하를 분산하는 데 사용할 수 있습니다. 

기본, 표준, 범용 서비스 계층의 고가용성 아키텍처에는 복제본이 포함되지 않습니다. 이러한 서비스 계층에서는 ‘읽기 확장’ 기능을 사용할 수 없습니다.

다음 다이어그램에서는 Premium 및 중요 비즈니스용 데이터베이스와 관리되는 인스턴스의 기능을 보여 줍니다.

![읽기 전용 복제본](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

‘읽기 확장’ 기능은 새 프리미엄, 중요 비즈니스용, 하이퍼스케일 데이터베이스에서 기본적으로 사용하도록 설정됩니다.

> [!NOTE]
> 읽기 확장은 항상 Managed Instance 중요 비즈니스용 서비스 계층 및 하나 이상의 보조 복제본이 있는 하이퍼스케일 데이터베이스에서 사용하도록 설정됩니다.

SQL 연결 문자열이 `ApplicationIntent=ReadOnly`로 구성된 경우 해당 데이터베이스 또는 관리형 인스턴스의 읽기 전용 복제본으로 애플리케이션이 리디렉션됩니다. `ApplicationIntent` 속성을 사용하는 방법에 대한 자세한 내용은 [애플리케이션 의도 지정](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)을 참조하세요.

SQL 연결 문자열의 `ApplicationIntent` 설정에 관계없이 애플리케이션이 주 복제본에 연결되도록 하려면 데이터베이스를 만들 때 또는 구성을 변경할 때 읽기 확장을 명시적으로 사용 중지해야 합니다. 예를 들어 표준 또는 범용 계층에서 Premium 또는 중요 비즈니스용 데이터베이스를 업그레이드하고 모든 연결이 주 복제본으로 계속 이동하도록 하려면 읽기 스케일 아웃을 사용하지 않도록 설정합니다. 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [읽기 확장 사용 및 사용 안 함을 참조하세요.](#enable-and-disable-read-scale-out)

> [!NOTE]
> 읽기 전용 복제본에서는 쿼리 저장소 및 SQL Profiler 기능이 지원되지 않습니다. 

## <a name="data-consistency"></a>데이터 일관성

주 복제본에서 변경된 데이터는 읽기 전용 복제본에 비동기식으로 전파됩니다. 읽기 전용 복제본에 연결된 세션 내에서 읽기는 항상 트랜잭션 일관성이 있습니다. 그러나 데이터 전파 대기 시간은 가변적이므로 서로 다른 복제본이 기본 및 서로에 대해 약간 다른 시점에서 데이터를 반환할 수 있습니다. 읽기 전용 복제본을 사용할 수 없게 되어 세션이 다시 연결되면 원래 복제본과 다른 시점에 있는 복제본에 연결할 수 있습니다. 마찬가지로 애플리케이션이 읽기-쓰기 세션을 사용하여 데이터를 변경하고 읽기 전용 세션을 사용하여 즉시 읽는 경우 최신 변경이 읽기 전용 복제본에 즉시 표시되지 않을 수 있습니다.

주 복제본과 읽기 전용 복제본 간의 일반적인 데이터 전파 대기 시간은 수십 밀리초에서 몇 초까지 다양합니다. 그러나 데이터 전파 대기 시간에 대한 고정된 상한은 없습니다. 복제본의 높은 리소스 사용률과 같은 조건은 대기 시간을 상당히 증가시킬 수 있습니다. 세션 간에 보장된 데이터 일관성이 필요하거나 커밋된 데이터를 즉시 읽을 수 있어야 하는 애플리케이션은 주 복제본을 사용해야 합니다.

> [!NOTE]
> 데이터 전파 대기 시간을 모니터링하려면 [읽기 전용 복제본 모니터링 및 문제 해결](#monitoring-and-troubleshooting-read-only-replicas)을 참조하세요.

## <a name="connect-to-a-read-only-replica"></a>읽기 전용 복제본에 연결

데이터베이스에 대한 읽기 확장을 사용하도록 설정하면 클라이언트에서 제공하는 연결 문자열의 `ApplicationIntent` 옵션은 연결이 쓰기 복제본 또는 읽기 전용 복제본으로 라우팅되는지 여부를 나타냅니다. 특히 `ApplicationIntent` 값이 `ReadWrite`(기본값)이면 연결이 읽기/쓰기 복제본으로 전달됩니다. `ApplicationIntent`이 연결 문자열에 포함되지 않은 경우의 동작과 동일합니다. `ApplicationIntent` 값이 `ReadOnly`이면 연결이 읽기 전용 복제본으로 라우팅됩니다.

예를 들어 다음 연결 문자열은 클라이언트를 읽기 전용 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

다음 연결 문자열 중 하나는 클라이언트를 읽기/쓰기 복제본에 연결합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>읽기 전용 복제본에 대한 연결인지 확인

데이터베이스 컨텍스트에서 다음 쿼리를 실행하여 읽기 전용 복제본에 연결되어 있는지 여부를 확인할 수 있습니다. 읽기 전용 복제본에 연결된 경우 READ_ONLY가 반환됩니다.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> 프리미엄 및 중요 비즈니스용 서비스 계층에서는 지정된 시간에 읽기 전용 복제본 중 하나에만 액세스할 수 있습니다. 하이퍼스케일은 여러 읽기 전용 복제본을 지원합니다.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>읽기 전용 복제본 모니터링 및 문제 해결

읽기 전용 복제본에 연결된 경우 DMV(동적 관리 뷰)는 복제본의 상태를 반영하며 모니터링 및 문제 해결을 위해 쿼리할 수 있습니다. 데이터베이스 엔진은 다양한 모니터링 데이터를 표시하기 위해 다양한 뷰를 제공합니다. 

복제본 모니터링 및 문제 해결에 일반적으로 사용되는 보기는 다음과 같습니다.

| Name | 목적 |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| CPU, 데이터 IO, 서비스 목표 한도와 관련된 로그 쓰기 사용률을 포함하여 지난 1시간 동안 리소스 사용률 메트릭을 제공합니다.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| 데이터베이스 엔진 인스턴스에 대한 집계 대기 통계를 제공합니다. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| 복제본 성능 상태 및 동기화 통계를 제공합니다. 읽기 전용 복제본의 데이터 전파 대기 시간의 지표로 다시 실행 큐 크기 및 다시 실행 속도 제공이 표시됩니다. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| 데이터베이스 엔진 성능 카운터를 제공합니다.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 실행 수, CPU 사용 시간 등 쿼리별 실행 통계를 제공합니다.|
|[sys.dm_exec_query_plan()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| 캐시된 쿼리 계획을 제공합니다. |
|[sys.dm_exec_sql_text()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| 캐시된 쿼리 계획에 대한 쿼리 텍스트를 제공합니다.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 쿼리가 실행되는 동안 실시간 쿼리 진행 현황을 제공합니다.|
|[sys.dm_exec_query_plan_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 쿼리의 런타임 통계를 포함하여 마지막으로 알려진 실제 실행 계획을 제공합니다.|
|[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| 모든 데이터베이스 파일에 대한 스토리지 IOPS, 처리량, 대기 시간 통계를 제공합니다. |

> [!NOTE]
> 논리적 master 데이터베이스의 `sys.resource_stats` 및 `sys.elastic_pool_resource_stats` DMV는 주 복제본의 리소스 사용률 데이터를 반환합니다.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>확장 이벤트를 사용하여 읽기 전용 복제본 모니터링

읽기 전용 복제본에 연결된 경우에는 확장 이벤트 세션을 만들 수 없습니다. 그러나 Azure SQL Database에서는, 주 복제본에서 만들어지고 변경된 데이터베이스 범위 지정 [확장 이벤트](xevent-db-diff-from-svr.md) 세션의 정의가 지역 복제본을 포함한 읽기 전용 복제본에 복제되고 읽기 전용 복제본에 대한 이벤트를 캡처합니다.

주 복제본의 세션 정의를 기반으로 하는 읽기 전용 복제본에서 확장 이벤트 세션을 주 복제본과 별개로 시작하고 중지할 수 있습니다. 주 복제본에서 확장 이벤트 세션이 중단되면 모든 읽기 전용 복제본에서도 중단됩니다.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>읽기 전용 복제본의 트랜잭션 격리 수준

읽기 전용 복제본에서 실행되는 쿼리는 항상 [스냅샷](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) 트랜잭션 격리 수준에 매핑됩니다. 스냅샷 격리는 판독기에서 작성기를 차단하는 차단 시나리오를 방지하기 위해 행 버전 관리를 사용합니다.

드문 경우지만, 스냅샷 격리 트랜잭션이 다른 동시 트랜잭션에서 수정된 개체 메타데이터에 액세스하는 경우, 다음과 같은 [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error) 오류 메시지가 표시될 수 있습니다. “이 트랜잭션이 시작된 이후 다른 동시 트랜잭션의 DDL 문에 의해 ‘%.*ls’ 데이터베이스에서 스냅샷 격리 트랜잭션이 실패했습니다. 메타데이터 버전이 관리되지 않기 때문에 허용되지 않습니다. 메타데이터에 대한 동시 업데이트로 인해 스냅샷 격리와 혼동되면 불일치가 발생할 수 있습니다.”

### <a name="long-running-queries-on-read-only-replicas"></a>읽기 전용 복제본에 대한 장기 실행 쿼리

읽기 전용 복제본에서 실행되는 쿼리는 쿼리에서 참조된 개체(테이블, 인덱스, 통계 등)에 대한 메타데이터에 액세스해야 합니다. 드문 경우지만 쿼리가 읽기 전용 복제본의 동일한 개체에 대한 잠금을 보유하는 동안 주 복제본에서 개체 메타데이터가 수정되는 경우 쿼리는 주 복제본에서 읽기 전용 복제본으로 변경 내용을 적용하는 프로세스를 [차단할](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) 수 있습니다. 이러한 쿼리가 오랜 시간 동안 실행되는 경우 읽기 전용 복제본이 주 복제본과 동기화되지 않을 수 있습니다. 잠재적인 장애 조치(failover) 대상인 복제본(Premium 및 중요 비즈니스용 서비스 계층의 보조 복제본, 하이퍼스케일 HA 복제본 및 모든 지역 복제본)의 경우 장애 조치(failover)가 발생할 경우 데이터베이스 복구가 지연되어 예상보다 긴 가동 중지 시간이 발생합니다.

읽기 전용 복제본에서 장기 실행 쿼리로 인해 직접 또는 간접적으로 이러한 종류의 차단이 발생하는 경우 과도한 데이터 대기 시간 및 잠재적인 데이터베이스 가용성 영향을 방지하기 위해 자동으로 종료될 수 있습니다. 오류 1219, “높은 우선 순위 DDL 작업으로 인해 세션의 연결이 해제되었습니다.” 또는 오류 3947, “보조 컴퓨팅에서 다시 실행을 캐치업하지 못해 트랜잭션이 중단되었습니다. 트랜잭션을 다시 시도하세요.” 메시지가 세션에 표시됩니다.

> [!NOTE]
> 읽기 전용 복제본에 대해 쿼리를 실행할 때 오류 3961, 1219 또는 3947이 발생하면 쿼리를 다시 시도합니다. 또는 장기 실행 쿼리가 보조 복제본에서 실행되는 동안 주 복제본에서 개체 메타데이터(스키마 변경, 인덱스 유지 관리, 통계 업데이트 등)를 수정하는 작업을 방지합니다.

> [!TIP]
> 프리미엄 및 중요 비즈니스용 서비스 계층에서 읽기 전용 복제본에 연결된 경우, [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV의 `redo_queue_size` 및 `redo_rate` 열을 사용하여 읽기 전용 복제본의 데이터 전파 대기 시간 지표로 제공되는 데이터 동기화 프로세스를 모니터링할 수 있습니다.
> 

## <a name="enable-and-disable-read-scale-out"></a>읽기 확장 사용 설정 및 사용 중지

읽기 확장은 프리미엄, 중요 비즈니스용, 하이퍼스케일 서비스 계층에서 기본적으로 사용하도록 설정되어 있습니다. 기본, 표준 또는 범용 서비스 계층에서는 읽기 확장을 사용하도록 설정할 수 없습니다. 읽기 확장은 0개의 보조 복제본으로 구성된 하이퍼스케일 데이터베이스에서 자동으로 비활성화됩니다.

다음 방법을 사용하여 프리미엄 또는 중요 비즈니스용 서비스 계층의 단일 데이터베이스 및 탄력적 풀 데이터베이스에서 읽기 확장을 사용 중지하고 다시 사용 설정할 수 있습니다.

> [!NOTE]
> 이전 버전과의 호환성을 위해, 단일 데이터베이스 및 탄력적 풀 데이터베이스의 경우 읽기 확장을 사용 중지하는 기능이 제공됩니다. 중요 비즈니스용 관리형 인스턴스에서는 읽기 확장을 사용 중지할 수 없습니다.

### <a name="azure-portal"></a>Azure Portal

데이터베이스 블레이드 **구성** 에서 읽기 확장 설정을 관리할 수 있습니다.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. Azure Resource Manager 모듈에는 적어도 2020년 12월까지 버그 수정이 계속될 예정입니다.  Az 모듈 및 Azure Resource Manager 모듈의 명령에 대한 인수는 사실상 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

Azure PowerShell에서 읽기 확장을 관리하려면 2016년 12월 Azure PowerShell 릴리스 이상이 필요합니다. 최신 PowerShell 버전은 [Azure PowerShell](/powershell/azure/install-az-ps)을 참조하세요.

Azure PowerShell에서 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet을 호출하고 `-ReadScale` 매개 변수에 대해 원하는 값(`Enabled` 또는 `Disabled`)을 전달하여 읽기 확장을 사용 중지하거나 다시 사용 설정할 수 있습니다.

기존 데이터베이스에 대해 읽기 확장을 사용하지 않도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

새로운 데이터베이스에 대해 읽기 확장을 사용하지 않도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

기존 데이터베이스에 대해 읽기 확장을 다시 사용하도록 설정하려면 다음을 수행합니다(꺾쇠 괄호 안의 항목을 사용자 환경에 맞는 값으로 바꾸고 꺾쇠 괄호는 삭제함).

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

읽기 확장을 사용하지 않도록 설정된 데이터베이스를 만들거나 기존 데이터베이스에 대한 설정을 변경하려면 다음 샘플 요청과 같이 `readScale` 속성이 `Enabled` 또는 `Disabled`로 설정된 다음 메서드를 사용합니다.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

자세한 내용은 [데이터베이스 - 만들기 또는 업데이트](/rest/api/sql/databases/createorupdate)를 참조하세요.

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>읽기 전용 복제본에서 `tempdb` 데이터베이스 사용

주 복제본의 `tempdb` 데이터베이스는 읽기 전용 복제본으로 복제되지 않습니다. 각 복제본마다 복제본을 만들 때 `tempdb` 데이터베이스가 생성됩니다. 이를 통해 `tempdb`를 업데이트할 수 있고 쿼리를 실행하는 동안 수정할 수 있습니다. 읽기 전용 작업에서 개체 사용에 의존 하 `tempdb` 는 경우 읽기 전용 복제본에 연결 된 상태에서 동일한 작업의 일부로 이러한 개체를 만들어야 합니다.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>지역에서 복제된 데이터베이스에서 읽기 확장 사용

지리적으로 복제 된 보조 데이터베이스는 주 데이터베이스와 동일한 고가용성 아키텍처를 가집니다. 읽기 확장을 사용하도록 설정된 지역에서 복제된 보조 데이터베이스에 연결하는 경우에는 `ApplicationIntent=ReadOnly`를 사용하는 세션이 기본 쓰기 가능 데이터베이스에서 라우팅되는 것과 같은 방식으로 고가용성 복제본 중 하나로 라우팅됩니다. `ApplicationIntent=ReadOnly`가 없는 세션은 역시 읽기 전용인 지역 복제된 보조 데이터베이스의 주 복제본으로 라우팅됩니다. 

이러한 방식으로 지역 복제본을 만들면 읽기/쓰기 주 데이터베이스에 대해 여러 개의 읽기 전용 복제본을 추가로 제공할 수 있습니다. 각 추가 지역 복제본은 다른 읽기 전용 복제본 집합을 제공 합니다. 주 데이터베이스의 지역을 포함하여 모든 Azure 지역에서 지역 복제본을 만들 수 있습니다.

> [!NOTE]
> 두 개 이상의 HA 복제본이 포함 된 하이퍼 규모 지역 복제본을 제외 하 고 지역에서 복제 된 보조 데이터베이스의 복제본 간에 자동 라운드 로빈 또는 다른 부하 분산 라우팅이 없습니다. 이 경우 읽기 전용 의도가 있는 세션은 지역 복제본의 모든 HA 복제본에 배포 됩니다.

## <a name="feature-support-on-read-only-replicas"></a>읽기 전용 복제본에 대 한 기능 지원

읽기 전용 복제본에 대 한 일부 기능의 동작 목록은 다음과 같습니다.
* 읽기 전용 복제본에 대 한 감사는 자동으로 설정 됩니다. 저장소 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대 한 자세한 내용은 [SQL Database 감사 로그 형식](audit-log-format.md)을 참조 하세요.
* [Query Performance Insight](query-performance-insight-use.md) 는 현재 읽기 전용 복제본에 대 한 작업을 추적 하지 않는 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)의 데이터를 사용 합니다. Query Performance Insight는 읽기 전용 복제본에서 실행 되는 쿼리를 표시 하지 않습니다.
* 자동 조정은 [자동 조정 문서](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)에 설명 된 대로 쿼리 저장소에 의존 합니다. 자동 조정은 주 복제본에서 실행 되는 워크 로드에만 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- SQL Database 하이퍼스케일 제공에 대한 자세한 내용은 [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)을 참조하세요.
