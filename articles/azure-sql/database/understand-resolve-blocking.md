---
title: Azure SQL 차단 문제의 이해 및 해결
titleSuffix: Azure SQL Database
description: 차단 및 문제 해결에 대한 Azure SQL Database 관련 항목의 개요입니다.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: faf2d60162a7cd39b901414aedb8cda1406173f2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226936"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Azure SQL Database 차단 문제의 이해 및 해결
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Objective

이 문서에서는 Azure SQL Database의 차단을 설명하고 차단 문제를 해결하는 방법을 보여줍니다. 

이 문서에서 연결이라는 용어는 데이터베이스의 단일 로그온 세션을 나타냅니다. 각 연결은 많은 DMV에서 세션 ID(SPID) 또는 session_id로 나타납니다. 이러한 각각의 SPID는 종종 프로세스라고 하지만, 일반적인 의미에서 별도의 프로세스 컨텍스트는 아닙니다. 대신, 각 SPID는 주어진 클라이언트의 단일 연결 요청을 처리하는 데 필요한 서버 리소스와 데이터 구조로 구성됩니다. 단일 클라이언트 응용 프로그램에 하나 이상의 연결이 있을 수 있습니다. Azure SQL Database의 관점에서 보면 단일 클라이언트 컴퓨터의 단일 클라이언트 응용 프로그램에서의 다중 연결과 여러 클라이언트 응용 프로그램 또는 여러 클라이언트 컴퓨터에서의 다중 연결 사이에는 차이가 없으며, 있더라도 매우 미미한 차이입니다. 원본 클라이언트에 관계없이 한 연결이 다른 연결을 차단할 수 있습니다.

> [!NOTE]
> **이 콘텐츠는 Azure SQL Database를 중점적으로 다룹니다.** Azure SQL Database은 안정적인 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 하므로 문제 해결 옵션과 도구는 다를 수 있지만 많은 내용이 유사합니다. SQL Server 차단에 대한 자세한 내용은 [SQL Server 차단 문제의 이해 및 해결](/troubleshoot/sql/performance/understand-resolve-blocking)을 참조하세요.

## <a name="understand-blocking"></a>차단 이해하기 
 
차단은 잠금 기반 동시성을 갖는 RDBMS(관계형 데이터베이스 관리 시스템)의 불가피하며 의도된 특성입니다. 앞서 언급한 바와 같이 SQL Server에서 차단은 한 세션이 특정 리소스에 대한 잠금을 보류하고 두 번째 SPID가 동일한 리소스에 대해 충돌하는 잠금 유형을 획득하려고 할 때 발생합니다. 일반적으로 첫 번째 SPID가 리소스를 잠그는 시간 프레임이 작습니다. 소유 세션에서 잠금을 해제하면 두 번째 연결이 자유롭게 리소스에 대한 자체 잠금을 획득하고 처리를 계속할 수 있습니다. 이는 정상적인 동작이며 하루 종일 시스템 성능에 별다른 영향을 주지 않으면서 여러 번 발생할 수 있습니다.

쿼리의 기간 및 트랜잭션 컨텍스트는 잠금이 유지되는 기간을 결정하여 다른 쿼리에 미치는 영향을 결정합니다. 쿼리가 트랜잭션 내에서 실행되지 않고 잠금 힌트가 사용되지 않는 경우, SELECT 문에 대한 잠금은 쿼리 중이 아니라 실제로 읽고 있는 리소스에 대해서만 유지됩니다. INSERT, UPDATE 및 DELETE 문의 경우 데이터 일관성과 필요에 따라 쿼리를 롤백할 수 있도록 쿼리 중에 잠금이 유지됩니다.

트랜잭션 내에서 실행되는 쿼리의 경우 잠금이 보류되는 기간은 쿼리 유형, 트랜잭션 격리 수준 및 쿼리에 잠금 힌트가 사용되는지 여부에 따라 결정됩니다. 잠금, 잠금 힌트 및 트랜잭션 격리 수준에 대한 설명은 다음 문서를 참조하세요.

* [데이터베이스 엔진에서의 잠금](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [잠금 및 행 버전 관리 사용자 지정](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [잠금 모드](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [잠금 호환성](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [트랜잭션](/sql/t-sql/language-elements/transactions-transact-sql)

잠금 및 차단이 시스템 성능에 부정적인 영향을 미칠 정도로 지속될 경우 이는 다음 이유 중 하나로 인한 것입니다.

* SPID는 리소스 집합을 해제하기 전에 장기간에 걸쳐 잠금을 유지합니다. 이러한 유형의 차단은 시간이 지남에 따라 자체적으로 해결되지만 성능 저하를 초래할 수 있습니다.

* SPID는 리소스 집합의 잠금을 유지하며 해제하지 않습니다. 이러한 유형의 차단은 자체적으로 해결되지 않으며 영향을 받는 리소스에 대한 액세스를 무기한으로 차단합니다.

첫 번째 시나리오에서는 서로 다른 SPID가 시간이 지남에 따라 서로 다른 리소스에 대한 차단을 유발하여 이동 대상을 생성하므로 상황이 매우 유동적일 수 있습니다. 이러한 상황은 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 문제를 개별 쿼리로 좁히기가 어렵습니다. 반면에 두 번째 상황은 일관된 상태를 유지하여 더 쉽게 진단할 수 있습니다.

## <a name="applications-and-blocking"></a>애플리케이션 및 차단

차단 문제가 생기면 서버측 튜닝과 플랫폼 문제에 집중하는 경향이 있을 수 있습니다. 하지만 데이터베이스에만 주의를 기울이면 해결이 되지 않을 수 있으며, 클라이언트 응용 프로그램과 제출되는 쿼리를 검사하는 데 필요한 시간과 에너지를 더 잘 받아들일 수 있습니다. 데이터베이스 호출과 관련하여 애플리케이션이 노출하는 가시성의 수준이 어떠하든지, 차단 문제는 애플리케이션이 제출한 정확한 SQL 문과 쿼리 취소, 연결 관리, 모든 결과 행 가져오기 등과 관련한 애플리케이션의 정확한 동작이 모두 필요합니다. 개발 도구에서 연결 관리, 쿼리 취소, 쿼리 시간 제한, 결과 가져오기 등에 대한 명시적인 제어를 허용하지 않을 경우 차단 문제가 해결되지 않을 수 있습니다. Azure SQL Database, 특히 성능에 민감한 OLTP 환경의 경우 애플리케이션 개발 도구를 선택하기 전에 이 가능성을 면밀히 검토해야 합니다. 

데이터베이스 및 애플리케이션의 디자인 및 구성 단계에서 데이터베이스 성능에 주의합니다. 특히 각 쿼리에 대해 리소스 사용, 격리 수준 및 트랜잭션 경로 길이를 평가해야 합니다. 각 쿼리 및 트랜잭션은 가능한 간단하게 만들어야 합니다. 좋은 연결 관리 원칙은 반드시 실행되어야 하며, 이러한 원칙이 없을 때 애플리케이션은 적은 사용자 수에서 허용 가능한 성능을 나타낼 수 있지만, 사용자 수가 증가하면 성능이 크게 저하될 수 있습니다. 

적절한 애플리케이션 및 쿼리 설계를 통해 Azure SQL Database는 단일 서버에서 수천 명의 동시 사용자를 거의 차단하지 않고 지원할 수 있습니다.

> [!Note]
> 애플리케이션 개발 지침에 대한 자세한 내용은 [Azure SQL Database 및 Azure SQL Managed Instance 및 ](troubleshoot-common-errors-issues.md)일시적인 오류 처리[의 연결 문제 및 기타 오류 문제 해결](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)을 참조하세요.

## <a name="troubleshoot-blocking"></a>차단 문제 해결

어떠한 차단 상황에 있든 관계없이 잠금 문제를 해결하는 방법은 동일합니다. 이러한 논리적인 격리가 이 문서의 나머지 구성에 영향을 미칠 것입니다. 개념은 헤드 블로커를 찾아 쿼리가 수행하는 작업과 해당 쿼리가 차단되는 원인을 식별하는 것입니다. 문제가 있는 쿼리(즉, 장기간 잠금을 유지하는 경우)가 식별되면 다음 단계는 차단이 발생하는 원인을 분석하고 확인하는 것입니다. 원인을 파악한 후 쿼리와 트랜잭션을 재설계하여 변경할 수 있습니다.

문제 해결 단계:

1. 주 차단 세션(헤드 블로커) 식별

2. 차단을 유발하는 쿼리 및 트랜잭션 찾기(장기간 잠금을 유지하는 경우)

3. 장기간 차단이 발생하는 원인 분석/이해

4. 쿼리 및 트랜잭션을 다시 디자인하여 차단 문제 해결

이제 적절한 데이터 캡처를 통해 주 차단 세션을 정확히 찾아내는 방법에 대해 알아보겠습니다.

## <a name="gather-blocking-information"></a>차단 정보 수집

데이터베이스 관리자는 Azure SQL Database에서 잠금 및 차단 상태를 지속적으로 모니터링하는 SQL 스크립트를 사용하여 차단 문제 해결의 어려움에 대처합니다. 기본적으로 두 가지 방법으로 이 데이터를 수집할 수 있습니다. 

첫 번째는 DMO(분산 관리 개체)를 쿼리하고 시간 경과에 따른 비교를 위해 결과를 저장하는 것입니다. 이 문서에서 참조한 개체 중 일부는 DMV(동적 관리 뷰)이고 일부는 DMF(동적 관리 함수)입니다. 두 번째 방법은 XEvent를 사용하여 실행 중인 내용을 캡처하는 것입니다. 


## <a name="gather-information-from-dmvs"></a>DMV에서 정보 수집

차단 문제를 해결하기 위해 DMV를 참조하는 것은 차단 체인 및 SQL 문의 헤드에서 SPID(세션 ID)를 식별하는 것을 목표로 합니다. 차단 중인 피해 SPID를 찾습니다. SPID가 다른 SPID에 의해 차단되는 경우 리소스를 소유한 SPID(차단 SPID)를 조사합니다. 소유자 SPID도 차단되고 있나요? 체인을 탐색하여 헤드 블로커를 찾은 다음 체인이 잠금을 유지하는 원인을 조사할 수 있습니다.

대상 Azure SQL Database에서 이러한 각 스크립트를 실행해야 합니다.

* sp_who 및 sp_who2 명령은 현재 세션을 모두 표시하는 이전 명령입니다. DMV `sys.dm_exec_sessions`는 더 쉽게 쿼리하고 필터링할 수 있는 더 많은 데이터를 결과 집합에 반환합니다. 다른 쿼리의 핵심에서 `sys.dm_exec_sessions`를 찾을 수 있습니다. 

* 특정 세션이 이미 식별된 경우 `DBCC INPUTBUFFER(<session_id>)`를 사용하여 세션에서 제출한 마지막 문을 찾을 수 있습니다. session_id와 request_id를 제공하여 쿼리 및 필터링이 용이한 결과 집합에서 `sys.dm_exec_input_buffer` DMF(동적 관리 함수)를 사용하여 유사한 결과를 반환할 수 있습니다. 예를 들어, session_id 66 및 request_id 0에서 제출한 최신 쿼리를 반환하려면,

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* `sys.dm_exec_requests`의 `blocking_session_id` 열을 참조하세요. `blocking_session_id` = 0이면 세션이 차단되지 않습니다. `sys.dm_exec_requests`는 현재 실행 중인 요청만 나열하지만, 모든 연결(활성 또는 비활성)은 `sys.dm_exec_sessions`에 나열됩니다. 다음 쿼리에서 `sys.dm_exec_requests` 및 `sys.dm_exec_sessions` 간에 이 공통 조인을 작성합니다.

* [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 또는 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV를 사용하여 적극적으로 실행 중인 쿼리와 현재 SQL 배치 텍스트 또는 입력 버퍼 텍스트를 찾으려면 이 샘플 쿼리를 실행합니다. `sys.dm_exec_sql_text`의 `text` 필드에서 반환된 데이터가 NULL이면 해당 쿼리는 현재 실행 중이 아닙니다. 이 경우 `sys.dm_exec_input_buffer`의 `event_info` 필드는 SQL 엔진에 전달된 마지막 명령 문자열을 포함합니다. 이 쿼리를 사용하여 session_id당 차단된 session_ids 목록을 포함하여 다른 세션을 차단하는 세션을 식별할 수도 있습니다. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Microsoft 지원에서 제공하는 이 보다 정교한 샘플 쿼리를 실행하여 차단 체인에 관련된 세션의 쿼리 텍스트를 포함하여 여러 세션 차단 체인의 헤드를 식별합니다.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* 장기 실행 중이거나 커밋되지 않은 트랜잭션을 캐치하려면 [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) 및 sys.dm_exec_sql_text를 포함하여 현재 열려 있는 트랜잭션을 보도록 다른 DMV 집합을 사용합니다. 추적 트랜잭션과 연결된 여러 DMV가 있습니다. 여기에서 [트랜잭션에 대한 추가 DMV](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql)를 참조하세요. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* SQL의 스레드/작업 계층에 있는 참조 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)입니다. 현재 요청에서 경험하고 있는 SQL 대기 유형에 대한 정보를 반환합니다. `sys.dm_exec_requests`와 마찬가지로 활성 요청만 `sys.dm_os_waiting_tasks`에 의해 반환됩니다. 

> [!Note]
> 시간 경과에 따른 집계된 대기 통계를 포함한 대기 유형에 대한 자세한 내용은 DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)를 참조하세요. 이 DMV는 현재 데이터베이스에 대한 집계 대기 통계만 반환합니다.

* 쿼리에 의해 배치된 잠금에 대한 세부 정보를 보려면 [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV를 사용합니다. 이 DMV는 프로덕션 SQL Server에서 많은 양의 데이터를 반환할 수 있으며 현재 보유 중인 잠금을 진단하는 데 유용합니다. 

`sys.dm_os_waiting_tasks`의 INNER JOIN으로 인해 다음 쿼리는 `sys.dm_tran_locks`의 출력을 현재 차단된 요청, 해당 대기 상태 및 잠금으로만 제한합니다.

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* DMV를 사용하여 시간 경과에 따라 쿼리 결과를 저장하면 지정된 시간 간격 동안 차단을 검토하여 지속적인 차단 또는 추세를 식별할 수 있는 데이터 포인트가 제공됩니다. 

## <a name="gather-information-from-extended-events"></a>확장 이벤트에서 정보 수집

이전 정보 외에도 서버에서 활동 추적을 캡처하여 Azure SQL Database의 차단 문제를 철저히 조사해야 하는 경우가 많습니다. 예를 들어, 세션이 트랜잭션 내에서 여러 문을 실행하는 경우 제출된 마지막 문만 표시됩니다. 하지만 이전 문 중 하나가 잠금이 유지되는 원인일 수 있습니다. 추적을 사용하면 현재 트랜잭션 내에서 세션에 의해 실행된 모든 명령을 볼 수 있습니다.

SQL Server에서 추적을 캡처하는 방법에는 확장 이벤트(Xevent) 및 프로파일러 추적의 두 가지가 있습니다. 하지만 [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler)는 Azure SQL Database에 지원되지 않는 사용되지 않는 추적 기술입니다. [확장 이벤트](/sql/relational-databases/extended-events/extended-events)는 영향을 줄일 수 있는 새로운 추적 기술이며, 인터페이스가 SSMS(SQL Server Management Studio)에 통합되어 있습니다. 

SSMS에서 [확장 이벤트 새 세션 마법사](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)를 사용하는 방법을 설명하는 문서를 참조하세요. 하지만 Azure SQL Database의 경우 SSMS는 개체 탐색기의 각 데이터베이스에 확장 이벤트 하위 폴더를 제공합니다. 확장 이벤트 세션 마법사를 사용하여 다음과 같은 유용한 이벤트를 캡처할 수 있습니다. 

-   범주 오류:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   범주 경고: 
    -   Missing_join_predicate

-   범주 실행:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   잠금
    -   Lock_deadlock

-   세션
    -   Existing_connection
    -   로그인
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>일반적인 차단 시나리오 식별 및 해결

이전 정보를 조사하면 대부분의 차단 문제의 원인을 확인할 수 있습니다. 이 문서의 나머지 부분에서는 이 정보를 사용하여 몇 가지 일반적인 차단 시나리오를 식별하고 해결하는 방법에 대해 설명합니다. 이 토론에서는 차단 스크립트(이전 내용 참조)를 사용하여 차단 SPID에 대한 정보를 캡처하고 XEvent 세션을 사용하여 애플리케이션 활동을 캡처했다고 가정합니다.

## <a name="analyze-blocking-data"></a>차단 데이터 분석 

* `sys.dm_exec_requests` 및 `sys.dm_exec_sessions`의 출력을 검사하여 `blocking_these` 및 `session_id`를 사용하여 차단 체인의 헤드를 판단합니다. 이렇게 하면 차단되는 요청과 차단 중인 요청을 가장 명확하게 식별할 수 있습니다. 차단된 세션 및 차단 중인 세션을 자세히 살펴봅니다. 차단 체인에 공통 요소 또는 루트가 있나요? 차단 체인은 공통 테이블을 공유하고 있으며, 차단 체인과 관련된 세션 중 하나 이상이 쓰기 작업을 수행하고 있습니다. 

* 차단 체인의 헤드에 있는 SPID에 대한 정보는 `sys.dm_exec_requests` 및 `sys.dm_exec_sessions`의 출력을 검사합니다. 다음 필드를 찾습니다. 

    -    `sys.dm_exec_requests.status`  
    이 열은 특정 요청의 상태를 표시합니다. 일반적으로 중지 중 상태는 SPID가 실행을 완료했으며 애플리케이션이 다른 쿼리나 배치를 제출하기를 기다리고 있음을 나타냅니다. 실행 가능 또는 실행 중 상태는 SPID가 현재 쿼리를 처리하고 있음을 나타냅니다. 다음 표에서는 다양한 상태 값에 대한 간략한 설명을 제공합니다.

    | 상태 | 의미 |
    |:-|:-|
    | 배경 | SPID가 교착 상태 검색, 로그 기록기 또는 검사점과 같은 백그라운드 작업을 실행하고 있습니다. |
    | 중지 중 | SPID가 현재 실행되고 있지 않습니다. 이는 일반적으로 SPID가 애플리케이션에서 명령을 대기 중임을 나타냅니다. |
    | 실행 중 | SPID가 현재 스케줄러에서 실행되고 있습니다. |
    | 실행 가능 | SPID가 스케줄러의 실행 가능한 큐에 있으며 스케줄러 시간을 얻기 위해 기다리는 중입니다. |
    | 일시 중단 | SPID가 잠금 또는 래치와 같은 리소스를 기다리는 중입니다. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    이 필드는 이 세션에서 열린 트랜잭션 수를 알려줍니다. 이 값이 0보다 크면 SPID는 열린 트랜잭션 내에 있으며 트랜잭션 내의 모든 문이 획득한 잠금을 보유할 수 있습니다.

    -    `sys.dm_exec_requests.open_transaction_count`  
    마찬가지로 이 필드는 이 요청에서 열린 트랜잭션 수를 알려줍니다. 이 값이 0보다 크면 SPID는 열린 트랜잭션 내에 있으며 트랜잭션 내의 모든 문이 획득한 잠금을 보유할 수 있습니다.

    -   `sys.dm_exec_requests.wait_type`, `wait_time`, `last_wait_type`  
     `sys.dm_exec_requests.wait_type` 은 NULL이며, 요청은 현재 대기 중이 아니며  `last_wait_type`  값은 요청이 마지막으로 발견한  `wait_type` 을 나타냅니다.  `sys.dm_os_wait_stats`에 대한 자세한 내용 및 가장 일반적인 대기 유형에 대한 설명은 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)를 참조하세요.  `wait_time`  값을 사용하여 요청을 진행 중인지 여부를 확인할 수 있습니다.  `sys.dm_exec_requests` 테이블에 대한 쿼리가  `wait_time` 열에 이전 쿼리인  `sys.dm_exec_requests`의  `wait_time` 값보다 작은 값을 반환하는 경우, 이전 잠금을 획득하여 해제한 다음, 현재 새 잠금에서 대기하고 있음을 나타냅니다(0이 아닌 `wait_time`인 것으로 가정). 이는 요청이 대기 중인 리소스를 표시하는  `sys.dm_exec_requests` 출력 간의 `wait_resource`를 비교하여 확인할 수 있습니다.

    -   `sys.dm_exec_requests.wait_resource` 이 필드는 차단된 요청이 대기 중인 리소스를 나타냅니다. 다음 테이블은 일반  `wait_resource`  형식 및 해당 의미를 나열합니다.

    | 리소스 | 형식 | 예제 | 설명 | 
    |:-|:-|:-|:-|
    | 테이블 | DatabaseID:ObjectID:IndexID | TAB: 5:261575970:1 | 이 경우 데이터베이스 ID 5는 Pubs 샘플 데이터베이스, 개체 ID 261575970은 제목 테이블, 1은 클러스터형 인덱스입니다. |
    | 페이지 | DatabaseID:FileID:PageID | PAGE: 5:1:104 | 이 경우 데이터베이스 ID 5는 pubs, 파일 ID 1은 기본 데이터 파일, 페이지 104는 제목 테이블에 속하는 페이지입니다. 페이지가 속한 object_id를 식별하려면 `wait_resource`에서 DatabaseID, FileId, PageId를 전달하는 동적 관리 함수 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)를 사용합니다. | 
    | 키 | DatabaseID:Hobt_id(인덱스 키에 대한 해시 값) | KEY: 5:72057594044284928 (3300a4f361aa) | 이 경우 데이터베이스 ID 5는 Pubs, Hobt_ID 72057594044284928은 object_id 261575970(제목 표)의 index_id 2에 해당합니다. hobt_id를 특정 `index_id` 및 `object_id`에 연결하려면 `sys.partitions` 카탈로그 뷰를 사용합니다. 인덱스 키 해시를 특정 키 값으로 해시하는 방법은 없습니다. |
    | 행 | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | 이 경우 데이터베이스 ID 5는 pubs, 파일 ID 1은 기본 데이터 파일, 페이지 104는 제목 테이블에 속하는 페이지, 슬롯 3은 페이지의 행 위치를 나타냅니다. |
    | Compile  | DatabaseID:FileID:PageID:Slot(row) | RID: 5:1:104:3 | 이 경우 데이터베이스 ID 5는 pubs, 파일 ID 1은 기본 데이터 파일, 페이지 104는 제목 테이블에 속하는 페이지, 슬롯 3은 페이지의 행 위치를 나타냅니다. |

    -    `sys.dm_tran_active_transactions` [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV는 커밋 또는 롤백을 대기 중인 트랜잭션의 전체 그림을 위해 다른 DMV에 조인할 수 있는 열린 트랜잭션에 대한 데이터를 포함합니다. 다음 쿼리를 사용하여 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)를 포함하는 다른 DMV에 조인된 열린 트랜잭션에 대한 정보를 반환합니다. 트랜잭션의 현재 상태, `transaction_begin_time` 및 기타 상황적 데이터를 고려하여 차단의 출처인지 여부를 평가합니다.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   기타 열

        [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 및 [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)에 나머지 열은 문제의 근원에 대한 통찰력도 제공할 수 있습니다. 이러한 유용성은 문제의 상황에 따라 달라집니다. 예를 들어, 특정 클라이언트(호스트 이름)에서, 특정 네트워크 라이브러리(net_library)에서, SPID에 의해 제출된 마지막 배치가 `sys.dm_exec_sessions`에서 `last_request_start_time`이었던 때, `sys.dm_exec_requests`에서 `start_time`을 사용하여 요청이 실행된 기간에 문제가 발생하는지 확인할 수 있습니다.


## <a name="common-blocking-scenarios"></a>일반적인 차단 시나리오

아래 표에서는 일반적인 증상을 가능한 원인에 매핑합니다.  

Waittype, Open_Tran 및 Status 열은 [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)에서 반환된 정보를 참조하며, 다른 열은 [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)에서 반환될 수 있습니다. "해결 여부" 열은 차단이 자체적으로 해결되는지 또는 세션을 `KILL` 명령을 통해 종료해야 하는지 여부를 나타냅니다. 자세한 내용은 [KILL(Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql)을 참조하세요.

| 시나리오 | Waittype | Open_Tran | 상태 | 해결 여부 | 기타 증상 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | runnable | 예(쿼리가 완료되는 경우). | `sys.dm_exec_sessions`에서 `reads`, `cpu_time` 및/또는 `memory_usage` 열은 시간이 지남에 따라 증가합니다. 작업을 완료하면 쿼리의 기간이 증가합니다. |
| 2 | NULL | \>0 | sleeping | 아니요. 하지만 SPID는 종료할 수 있습니다. | 이 SPID에 대한 확장 이벤트 세션에서 쿼리 시간 제한 또는 취소가 발생했음을 나타내는 주의 신호가 표시될 수 있습니다. |
| 3 | NULL | \>= 0 | runnable | 아니요. 클라이언트가 모든 행을 가져오거나 연결을 닫을 때까지 해결되지 않습니다. SPID는 종료할 수 있지만 최대 30초까지 걸릴 수 있습니다. | open_transaction_count = 0이고 트랜잭션 격리 수준이 기본값(READ COMMMITTED)인 동안 SPID가 잠금을 유지하는 경우 이 문제가 원인일 수 있습니다. |  
| 4 | 상황에 따라 다름 | \>= 0 | runnable | 아니요. 클라이언트가 쿼리를 취소하거나 연결을 닫을 때까지 해결되지 않습니다. SPID는 종료할 수 있지만 최대 30초까지 걸릴 수 있습니다. | 차단 체인의 헤드에 있는 SPID에 대한 `sys.dm_exec_sessions`에 있는 `hostname` 열은 차단 중인 SPID 중 하나와 동일합니다. |  
| 5 | NULL | \>0 | 롤백 | 예. | 이 SPID에 대한 확장 이벤트 세션에서 쿼리 시간 제한 또는 취소가 발생했거나 롤백 문이 발행되었음을 나타내는 주의 신호가 표시될 수 있습니다. |  
| 6 | NULL | \>0 | sleeping | 결국. Windows NT에서 세션이 더 이상 활성 상태가 아니라고 판단하면 Azure SQL Database 연결이 끊어집니다. | `sys.dm_exec_sessions`의 `last_request_start_time` 값이 현재 시간보다 훨씬 이전입니다. |

## <a name="detailed-blocking-scenarios"></a>자세한 차단 시나리오

1.  실행 시간이 긴 정상적으로 실행되는 쿼리로 인한 차단

    **해결 방법**: 이러한 유형의 차단 문제에 대한 솔루션은 쿼리를 최적화할 수 있는 방법을 찾는 것입니다. 실제로 이 클래스의 차단 문제는 단지 성능 문제일 수 있으며, 따라서 이를 추진해야 합니다. 실행 속도가 느린 특정 쿼리 문제 해결에 대한 자세한 내용은 [SQL Server에서 실행 속도가 느린 쿼리 문제를 해결하는 방법](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)을 참조하세요. 자세한 내용은 [성능 모니터링 및 조정](/sql/relational-databases/performance/monitor-and-tune-for-performance)을 참조하세요. 

    SSMS의 [쿼리 저장소](/sql/relational-databases/performance/best-practice-with-the-query-store)에 있는 보고서는 또한 가장 비용이 많이 드는 쿼리, 차선책 실행 계획을 식별하기 위한 매우 권장되고 가치 있는 도구입니다. 또한 [Query Performance Insight](query-performance-insight-use.md)를 포함하여 Azure SQL database에 대한 Azure Portal의 [지능형 성능](intelligent-insights-overview.md) 섹션을 검토합니다.

    다른 사용자를 차단하고 있고 최적화할 수 없는 장기 실행 쿼리가 있는 경우, OLTP 환경에서 [데이터베이스의 동기식 읽기 전용 복제본](read-scale-out.md)인 전용 보고 시스템으로 쿼리를 이동하는 것이 좋습니다.

1.  커밋되지 않은 트랜잭션이 있는 일시 중지 SPID로 인한 차단

    이러한 유형의 차단은 흔히 중지 중이거나 명령을 기다리고 있지만 트랜잭션 중첩 수준(`sys.dm_exec_requests`의 `@@TRANCOUNT`, `open_transaction_count`)이 0보다 큰 SPID로 식별할 수 있습니다. 이 문제는 애플리케이션이 필요한 수의 롤백 및/또는 커밋 문을 발행하지 않고 쿼리 시간 제한을 경험하거나 취소를 발행하는 경우에 발생할 수 있습니다. SPID는 쿼리 시간 제한이나 취소를 수신하면 현재 쿼리 및 배치를 종료하지만 트랜잭션을 자동으로 롤백하거나 커밋하지는 않습니다. Azure SQL Database는 단일 쿼리가 취소되어 전체 트랜잭션이 롤백되어야 한다고 가정할 수 없기 때문에 애플리케이션이 이를 담당합니다. 쿼리 시간 제한 또는 취소는 확장 이벤트 세션에서 SPID에 대한 주의 신호 이벤트로 표시됩니다.

    커밋되지 않은 명시적 트랜잭션을 시연하려면 다음 쿼리를 실행합니다.

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    그런 다음 동일한 창에서 이 쿼리를 실행합니다.
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    두 번째 쿼리의 출력은 트랜잭션 중첩 수준이 하나임을 나타냅니다. 트랜잭션에서 획득한 모든 잠금은 트랜잭션이 커밋되거나 롤백될 때까지 유지됩니다. 애플리케이션이 트랜잭션을 명시적으로 열고 커밋할 경우, 통신 또는 기타 오류가 세션과 해당 트랜잭션을 열린 상태로 유지할 수 있습니다. 

    `sys.dm_tran_active_transactions`에 따라 이 문서의 앞부분에 나오는 스크립트를 사용하여 인스턴스 간에 현재 커밋되지 않은 트랜잭션을 식별합니다.

    **해결 방법**:

     -   또한 이 클래스의 차단 문제도 성능 문제일 수 있으므로 이를 추진해야 합니다. 쿼리 실행 시간을 줄일 수 있는 경우 쿼리 시간 제한 또는 취소가 발생하지 않습니다. 애플리케이션에서 발생해야 하는 시간 제한 또는 취소 시나리오를 처리할 수 있는 것이 중요하지만 쿼리의 성능을 검사하는 것도 유용할 수 있습니다. 
     
     -    애플리케이션은 트랜잭션 중첩 수준을 적절하게 관리해야 합니다. 그렇지 않으면 이러한 방식으로 쿼리를 취소한 후 차단 문제가 발생할 수 있습니다. 다음을 살펴보세요.  

            *    클라이언트 응용 프로그램의 오류 처리기에서 클라이언트 응용 프로그램에서 트랜잭션이 열려 있다고 생각하지 않더라도 오류 발생 후 `IF @@TRANCOUNT > 0 ROLLBACK TRAN`을 실행합니다. 일괄 처리 중에 호출된 저장 프로시저가 클라이언트 응용 프로그램 모르게 트랜잭션을 시작할 수 있기 때문에 열린 트랜잭션을 확인해야 합니다. 쿼리 취소와 같은 특정 조건은 현재 문을 지나 프로시저가 실행되지 않도록 하기 때문에 절차에 `IF @@ERROR <> 0`을 확인하고 트랜잭션을 중단하는 논리가 있더라도 이러한 경우에는 이 롤백 코드가 실행되지 않습니다.  
            *    연결 풀링이 연결을 열고 웹 기반 애플리케이션과 같이 풀로 연결을 해제하기 전에 소수의 쿼리를 실행하는 애플리케이션에서 사용되는 경우 연결 풀링을 일시적으로 해제하면 클라이언트 응용 프로그램이 오류를 적절하게 처리하도록 수정할 때까지 문제가 완화되는 데 도움이 될 수 있습니다. 연결 풀링을 사용하지 않도록 설정하여 연결을 해제하면 Azure SQL Database 연결이 물리적으로 끊겨 서버가 열려 있는 트랜잭션을 롤백하게 됩니다.  
            *    연결 또는 트랜잭션을 시작하고 오류 후 정리되지 않는 저장 프로시저에 `SET XACT_ABORT ON`을 사용합니다. 런타임 오류가 발생할 경우 이 설정은 열려 있는 트랜잭션을 중단하고 클라이언트로 컨트롤을 반환합니다. 자세한 내용은 [SET XACT_ABORT(Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql)를 검토하세요.

    > [!NOTE]
    > 연결 풀에서 다시 사용할 때까지 연결이 재설정되지 않으므로 사용자가 트랜잭션을 연 다음 연결 풀로의 연결을 해제할 수 있지만, 몇 초 동안 다시 사용되지 않을 수 있으며 이 기간 동안 트랜잭션이 열린 상태로 유지됩니다. 연결을 다시 사용하지 않으면 연결 시간이 초과되어 연결 풀에서 제거될 때 트랜잭션이 중단됩니다. 따라서 클라이언트 응용 프로그램이 오류 처리기에서 트랜잭션을 중단하거나 `SET XACT_ABORT ON`을 사용하여 이러한 잠재적 지연을 방지하는 것이 좋습니다.

    > [!CAUTION]
    > `SET XACT_ABORT ON` 후에는 오류를 일으키는 문 뒤에 있는 T-SQL 문이 실행되지 않습니다. 이는 기존 코드의 의도된 흐름에 영향을 미칠 수 있습니다.

1.  해당 클라이언트 응용 프로그램이 완료될 때까지 모든 결과 행을 가져오지 않은 SPID로 인한 차단

    서버에 쿼리를 보낸 후 모든 애플리케이션에서 즉시 모든 결과 행을 가져와 완료해야 합니다. 애플리케이션이 모든 결과 행을 가져오지 않으면 테이블에 잠금을 남겨 두고 다른 사용자를 차단할 수 있습니다. SQL 문을 서버에 투명하게 제출하는 애플리케이션을 사용하는 경우 애플리케이션이 모든 결과 행을 가져와야 합니다. 그렇지 않은 경우(그렇게 구성할 수 없는 경우) 차단 문제를 해결하지 못할 수 있습니다. 이 문제를 방지하려면 기본 OLTP 데이터베이스와 별도로 제대로 동작하지 않는 애플리케이션을 보고 또는 의사 결정 지원 데이터베이스로 제한할 수 있습니다.
    
    > [!NOTE]
    > Azure SQL Database에 연결하는 애플리케이션에 대한 [재시도 논리에 대한 지침](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)을 참조하세요. 
    
    **해결 방법**: 결과의 모든 행을 가져와 완료하려면 애플리케이션을 다시 작성해야 합니다. 이는 서버측 페이징을 수행하기 위해 쿼리의 [ORDER BY 절의 OFFSET 및 FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) 사용을 배제하지 않습니다.

1.  롤백 상태의 세션으로 인한 차단

    사용자 정의 트랜잭션 외부에서 종료 또는 취소된 데이터 수정 쿼리가 롤백됩니다. 클라이언트 네트워크 세션 연결이 끊기거나 요청이 교착 상태로 선택된 경우에도 이러한 문제가 발생할 수 있습니다. 이 문제는 ROLLBACK 명령을 나타내는 `sys.dm_exec_requests`의 출력을 관찰하여 확인할 수 있으며, `percent_complete` 열에 진행률이 표시될 수 있습니다. 

    2019에 도입된 [가속 데이터베이스 복구 기능](../accelerated-database-recovery.md) 덕분에 긴 롤백이 거의 발생하지 않습니다.
    
    **해결 방법**: SPID가 변경된 내용을 롤백할 때까지 기다립니다. 

    이러한 상황을 방지하려면 OLTP 시스템에서 바쁜 시간 동안 대규모 일괄 처리 작업이나 인덱스 생성 또는 유지 관리 작업을 수행하지 않습니다. 가능하면 활동이 적은 기간 동안 이러한 작업을 수행합니다.

1.  분리된 연결로 인한 차단

    클라이언트 응용 프로그램이 오류를 트랩하거나 클라이언트 워크스테이션이 재시작된 경우, 어떤 상황에서는 서버에 대한 네트워크 세션이 즉시 취소되지 않을 수 있습니다. Azure SQL Database 관점에서 클라이언트는 여전히 존재 하는 것처럼 보이지만 획득한 모든 잠금은 계속 유지될 수 있습니다. 자세한 내용은 [SQL Server에서 분리된 연결 문제를 해결하는 방법](/sql/t-sql/language-elements/kill-transact-sql#remarks)을 참조하세요. 

    **해결 방법**: 클라이언트 응용 프로그램의 리소스를 적절하게 정리하지 않고 연결을 끊은 경우 `KILL` 명령을 사용하여 SPID를 종료할 수 있습니다. `KILL` 명령은 SPID 값을 입력으로 사용합니다. 예를 들어, SPID 99를 종료하려면 다음 명령을 발행합니다.

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>참고 항목

* [Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝](./monitor-tune-overview.md)
* [쿼리 저장소를 사용한 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [트랜잭션 잠금 및 행 버전 관리 지침](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [빠른 시작: SQL Server의 확장 이벤트](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [AI를 사용하여 데이터베이스 성능을 모니터링하고 문제를 해결하는 Intelligent Insights](intelligent-insights-overview.md)

## <a name="learn-more"></a>자세한 정보

* [Azure SQL Database: 자동 튜닝을 사용하여 성능 튜닝 향상](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Azure SQL을 사용하여 일관된 성능 제공](/learn/modules/azure-sql-performance/)
* [Azure SQL Database 및 Azure SQL Managed Instance의 연결 문제 및 기타 오류 문제 해결](troubleshoot-common-errors-issues.md)
* [일시적인 오류 처리](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
* [Azure SQL Database에서 최대 병렬 처리 수준(MAXDOP) 구성](configure-max-degree-of-parallelism.md)
