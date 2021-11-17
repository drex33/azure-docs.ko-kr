---
title: 메모리 문제 해결
titleSuffix: Azure SQL Database
description: Azure SQL Database 메모리 부족 문제를 해결하는 단계를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 11/03/2021
ms.openlocfilehash: 07ff08303cc68934d5d7191952536b9101e12cf6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494713"
---
# <a name="troubleshoot-out-of-memory-errors-with-azure-sql-database"></a>Azure SQL Database 메모리 부족 오류 문제 해결  
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL 데이터베이스 엔진이 쿼리를 실행하는 데 충분한 메모리를 할당하지 못한 경우 오류 메시지가 표시될 수 있습니다. 이는 선택한 서비스 목표의 제한, 워크로드 메모리 요구 집계 및 쿼리의 메모리 요구를 비롯한 다양한 이유로 인해 발생할 수 있습니다. Azure SQL Databases의 메모리 리소스 제한에 대한 자세한 내용은 [Azure SQL Database 리소스 관리를](resource-limits-logical-server.md#memory)참조하세요.

> [!NOTE]
> **이 문서는 Azure SQL Database 중 하나입니다.** SQL Server 메모리 부족 문제 해결에 대한 자세한 내용은 [MSSQLSERVER_701.](/sql/relational-databases/errors-events/mssqlserver-701-database-engine-error)

다음에 대한 응답으로 다음 조사 방법을 시도합니다.

- "리소스 풀 '%ls'에 시스템 메모리가 부족하여 이 쿼리를 실행할 수 없습니다."라는 오류 메시지와 함께 오류 코드 701이 표시됩니다.
- "버퍼 풀에서 사용할 수 있는 메모리가 부족합니다."라는 오류 메시지와 함께 오류 코드 802

## <a name="investigate-memory-allocation"></a>메모리 할당 조사

메모리 부족 오류가 Azure SQL Database 지속되면 Azure Portal 데이터베이스의 서비스 수준 목표를 일시적으로 늘리는 것이 좋습니다. 메모리 부족 오류가 지속되면 다음 쿼리를 사용하여 메모리 부족 상태에 영향을 미칠 수 있는 비정상적으로 높은 쿼리 메모리 부여를 찾습니다. 오류가 발생한 데이터베이스에서 다음 예제 쿼리를 실행합니다(Azure `master` SQL 논리 서버의 데이터베이스에 있지 않음).  

### <a name="use-dmvs-to-view-memory-clerks"></a>DMV를 사용하여 메모리 클럭 보기

메모리 부족 오류가 최근에 발생한 경우 메모리 클럭에 대한 메모리 할당을 확인하여 광범위한 조사를 시작합니다. 메모리 클럭은 이 Azure SQL Database 데이터베이스 엔진 내부입니다. 할당된 페이지 측면에서 상위 메모리 클럭은 가장 많은 메모리를 사용하는 SQL Server 쿼리 유형 또는 기능에 정보를 제공합니다. 


```sql
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY pages_kb DESC;
GO
SELECT [type], [name], pages_kb, virtual_memory_committed_kb
FROM sys.dm_os_memory_clerks
WHERE memory_node_id <> 64 -- ignore Dedicated Admin Connection (DAC) node
ORDER BY virtual_memory_committed_kb DESC;
```

 - MEMORYCLERK_SQLQERESERVATIONS 같은 일부 일반적인 메모리 클럭은 메모리 부여가 큰 쿼리를 식별하고 인덱싱 및 인덱스 튜닝을 개선하여 성능을 향상시켜 가장 잘 해결됩니다. 
 - OBJECTSTORE_LOCK_MANAGER 메모리 부여와 관련이 없지만, 예를 들어 비활성화된 잠금 에스컬레이션 또는 매우 큰 트랜잭션으로 인해 쿼리가 많은 잠금을 클레임하는 경우 높은 것으로 예상됩니다. 
 - 일부 클럭은 사용률이 가장 높을 것으로 예상됩니다. MEMORYCLERK_SQLBUFFERPOOL 거의 항상 최상위 클럭이지만 columnstore 인덱스를 사용할 때는 CACHESTORE_COLUMNSTOREOBJECTPOOL 높습니다. 이러한 클럭의 최고 사용률이 예상됩니다. 

 메모리 클럭 유형에 대한 자세한 내용은 [sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql)참조하세요. 

### <a name="use-dmvs-to-investigate-active-queries"></a>DMV를 사용하여 활성 쿼리 조사 

대부분의 경우 실패한 쿼리는 이 오류의 원인이 아닙니다. 

Azure SQL Database 대한 다음 샘플 쿼리는 현재 메모리 부여를 보유하거나 대기 중인 트랜잭션에 대한 중요한 정보를 반환합니다. 검사 및 성능 튜닝을 위해 식별된 상위 쿼리를 대상으로 지정하고 의도한 대로 실행 중인지 여부를 평가합니다. 메모리 집약적인 보고 쿼리 또는 유지 관리 작업의 타이밍을 고려합니다.

```sql
--Active requests with memory grants
SELECT
--Session data 
  s.[session_id], s.open_transaction_count
--Memory usage
, r.granted_query_memory, mg.grant_time, mg.requested_memory_kb, mg.granted_memory_kb, mg.required_memory_kb, mg.used_memory_kb, mg.max_used_memory_kb     
--Query 
, query_text = t.text, input_buffer = ib.event_info, query_plan_xml = qp.query_plan, request_row_count = r.row_count, session_row_count = s.row_count
--Session history and status
, s.last_request_start_time, s.last_request_end_time, s.reads, s.writes, s.logical_reads, session_status = s.[status], request_status = r.status
--Session connection information
, s.host_name, s.program_name, s.login_name, s.client_interface_name, s.is_user_process
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests AS r 
    ON r.[session_id] = s.[session_id]
LEFT OUTER JOIN sys.dm_exec_query_memory_grants AS mg 
    ON mg.[session_id] = s.[session_id]
OUTER APPLY sys.dm_exec_sql_text (r.[sql_handle]) AS t
OUTER APPLY sys.dm_exec_input_buffer(s.[session_id], NULL) AS ib 
OUTER APPLY sys.dm_exec_query_plan (r.[plan_handle]) AS qp 
WHERE mg.granted_memory_kb > 0
ORDER BY mg.granted_memory_kb desc, mg.requested_memory_kb desc;
```

KILL 문을 사용하여 대용량 메모리 부여를 보유하거나 대기 중인 현재 실행 중인 쿼리를 중지할 수 있습니다. 특히 중요 비즈니스용 프로세스가 실행 중인 경우 이 문을 신중하게 사용합니다. 자세한 내용은 [KILL&#40;Transact-SQL&#41;](/sql/t-sql/language-elements/kill-transact-sql)을 참조하세요. 


### <a name="use-query-store-to-investigate-past-query-memory-usage"></a>쿼리 저장소 사용하여 과거 쿼리 메모리 사용량 조사

이전 샘플 쿼리는 라이브 쿼리 결과만 보고하지만 다음 쿼리는 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 사용하여 과거 쿼리 실행에 대한 정보를 반환합니다. 이는 과거에 발생한 메모리 부족 오류를 조사하는 데 유용할 수 있습니다.

Azure SQL Database 대한 다음 샘플 쿼리는 쿼리 저장소 기록된 쿼리 실행에 대한 중요한 정보를 반환합니다. 검사 및 성능 튜닝을 위해 식별된 상위 쿼리를 대상으로 지정하고 의도한 대로 실행 중인지 여부를 평가합니다. 시간 필터를 사용하여 `qsp.last_execution_time` 결과를 최근 기록으로 제한합니다. TOP 절을 조정하여 환경에 따라 더 많거나 적은 결과를 생성할 수 있습니다.

```sql
SELECT TOP 10 PERCENT --limit results
  a.plan_id, query_id, plan_group_id, query_sql_text
, query_plan = TRY_CAST(query_plan as XML)
, avg_query_max_used_memory
, min_query_max_used_memory
, max_query_max_used_memory
, last_query_max_used_memory 
, last_execution_time  
, query_count_executions
    FROM (
    SELECT 
      qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    , last_execution_time = MAX(qsp.last_execution_time)
    , query_count_executions = SUM(qsrs.count_executions) 
    , avg_query_max_used_memory = AVG(qsrs.avg_query_max_used_memory)
    , min_query_max_used_memory = MIN(qsrs.min_query_max_used_memory)
    , max_query_max_used_memory = MAX(qsrs.max_query_max_used_memory)
    , last_query_max_used_memory = MAX(qsrs_latest.last_query_max_used_memory) --only from latest result
    FROM sys.query_store_plan AS qsp 
    INNER JOIN sys.query_store_query AS qsq
        ON qsp.query_id = qsq.query_id
    INNER JOIN sys.query_store_query_text AS qsqt
        ON qsq.query_text_id = qsqt.query_text_id 
    INNER JOIN sys.query_store_runtime_stats AS qsrs
        ON qsp.plan_id = qsrs.plan_id 
    INNER JOIN (SELECT plan_id
            , last_query_max_used_memory 
            , rownum = ROW_NUMBER() OVER (PARTITION BY plan_id ORDER BY last_execution_time DESC)
            FROM sys.query_store_runtime_stats qsrs) AS qsrs_latest
        ON qsrs_latest.plan_id = qsp.plan_id
        AND qsrs_latest.rownum = 1 --use latest last_query_max_used_memory per plan_id
    WHERE DATEADD(hour, -24, sysdatetime()) < qsp.last_execution_time --past 24 hours only
    AND qsrs_latest.last_query_max_used_memory > 0
    GROUP BY qsp.plan_id, qsp.query_id, qsp.plan_group_id, qsp.query_plan, qsqt.query_sql_text
    ) AS a
ORDER BY max_query_max_used_memory DESC, avg_query_max_used_memory DESC;
```

### <a name="extended-events"></a>확장 이벤트
이전 정보 외에도 서버에서 작업의 추적을 캡처하여 Azure SQL Database 메모리 부족 문제를 철저히 조사하는 것이 유용할 수 있습니다. 

SQL Server에서 추적을 캡처하는 방법에는 확장 이벤트(Xevent) 및 프로파일러 추적의 두 가지가 있습니다. 하지만 [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler)는 Azure SQL Database에 지원되지 않는 사용되지 않는 추적 기술입니다. [확장 이벤트](/sql/relational-databases/extended-events/extended-events)는 영향을 줄일 수 있는 새로운 추적 기술이며, 인터페이스가 SSMS(SQL Server Management Studio)에 통합되어 있습니다. 

SSMS에서 [확장 이벤트 새 세션 마법사](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)를 사용하는 방법을 설명하는 문서를 참조하세요. 하지만 Azure SQL Database의 경우 SSMS는 개체 탐색기의 각 데이터베이스에 확장 이벤트 하위 폴더를 제공합니다. 확장 이벤트 세션을 사용하여 이러한 유용한 이벤트를 캡처하고 이를 생성하는 쿼리를 식별합니다. 

-   범주 오류:
    - error_reported
    - exchange_spill
    - hash_spill_details
    
-   범주 실행:
    - excessive_non_grant_memory_used

-   범주 메모리:
    - query_memory_grant_blocking
    - query_memory_grant_usage

메모리 부여 블록, 메모리 부여 유출 또는 과도한 메모리 부여 캡처는 쿼리가 과거보다 더 많은 메모리를 갑자기 사용하게 되는 잠재적인 단서를 될 수 있으며, 기존 워크로드의 메모리 부족 오류에 대한 잠재적인 설명일 수 있습니다.

### <a name="in-memory-oltp-out-of-memory"></a>메모리 내 OLTP 메모리 부족 

In-Memory OLTP를 사용하는 경우 발생할 수 `Error code 41805: There is insufficient memory in the resource pool '%ls' to run this operation` 있습니다. 메모리 최적화 테이블 및 메모리 최적화 테이블 값 매개 변수의 데이터 양을 줄이거나 더 많은 메모리를 갖도록 데이터베이스를 더 높은 서비스 목표로 확장합니다. SQL Server In-Memory OLTP의 메모리 부족 문제에 대한 자세한 내용은 [메모리 부족 문제 해결을](/sql/relational-databases/in-memory-oltp/resolve-out-of-memory-issues)참조하세요.

### <a name="get-azure-sql-db-support"></a>Azure SQL DB 지원 받기 

메모리 부족 오류가 Azure SQL Database 지속되면 Azure 지원 [사이트에서](https://azure.microsoft.com/support/options) **지원 받기를** 선택하여 Azure 지원 요청을 제출합니다.

## <a name="next-steps"></a>다음 단계

- [SQL 데이터베이스의 지능형 쿼리 처리](/sql/relational-databases/performance/intelligent-query-processing)
- [쿼리 처리 아키텍처 가이드](/sql/relational-databases/query-processing-architecture-guide)    
- [SQL Server 데이터베이스 엔진 및 Azure SQL Database에 대한 성능 센터](/sql/relational-databases/performance/performance-center-for-sql-server-database-engine-and-azure-sql-database)  
- [Azure SQL Database 및 Azure SQL Managed Instance의 연결 문제 및 기타 오류 문제 해결](troubleshoot-common-errors-issues.md)
- [SQL Database 및 SQL Managed Instance에서 일시적인 연결 오류 문제 해결](troubleshoot-common-connectivity-issues.md)
- [지능형 쿼리 처리 시연](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/intelligent-query-processing)   
- [Azure SQL Database 의 리소스 관리](resource-limits-logical-server.md#memory)
