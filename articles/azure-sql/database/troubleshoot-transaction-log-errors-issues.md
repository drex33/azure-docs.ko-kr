---
title: 트랜잭션 로그 문제 해결
titleSuffix: Azure SQL Database and Azure SQL Managed Instance
description: Azure SQL Database 또는 Azure SQL Managed Instance에서 Azure SQL Database 트랜잭션 로그 문제를 해결하는 단계를 안내합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 07/23/2021
ms.openlocfilehash: 2bddb630cd2dad83992cfd740fb44b127a770c51
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672802"
---
# <a name="troubleshooting-transaction-log-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance의 트랜잭션 로그 오류 문제 해결
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

트랜잭션 로그가 가득 차서 새 트랜잭션을 수락할 수 없을 때 9002 또는 40552 오류가 표시될 수 있습니다. 이러한 오류는 Azure SQL Database 또는 Azure SQL Managed Instance를 통해 관리되는 데이터베이스 트랜잭션 로그가 공간 임계값을 초과하여 트랜잭션을 더 이상 수락할 수 없는 경우에 발생합니다. 

이러한 오류는 SQL Server에서 트랜잭션 로그가 가득 찬 문제와 비슷하지만, Azure SQL Database 또는 Azure SQL Managed Instance에서는 해결 방법이 다릅니다.

> [!NOTE]
> **이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance를 중점적으로 다룹니다.** Azure SQL Database 및 Azure SQL Managed Instance는 안정적인 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 하므로 문제 해결 옵션과 도구는 다를 수 있지만 많은 내용이 유사합니다. SQL Server의 트랜잭션 로그 문제 해결에 대한 자세한 내용은 [꽉 찬 트랜잭션 로그 문제 해결(SQL Server 오류 9002)](/sql/relational-databases/logs/troubleshoot-a-full-transaction-log-sql-server-error-9002)을 참조하세요.

## <a name="automated-backups-and-the-transaction-log"></a>자동화된 백업 및 트랜잭션 로그

데이터베이스 파일 공간 관리와 관련하여 Azure SQL Database와 Azure SQL Managed Instance 사이에는 몇 가지 큰 차이점이 있습니다. 

- Azure SQL Database 또는 Azure SQL Managed Instance에서는 트랜잭션 로그 백업이 자동으로 수행됩니다. 빈도, 보존 기간 및 자세한 내용은 [자동화된 백업 - Azure SQL Database 및 SQL Managed Instance](automated-backups-overview.md)를 참조하세요. 
- Azure SQL Database에서는 사용 가능한 디스크 공간, 데이터베이스 파일 증가 및 파일 위치도 관리되므로 트랜잭션 로그 문제의 일반적인 원인과 해결 방법이 SQL Server와 다릅니다. 
- Azure SQL Managed Instance에서는 데이터베이스 파일의 위치와 이름을 관리할 수 없지만, 관리자는 데이터베이스 파일 및 파일 자동 증가 설정을 관리할 수 있습니다. 트랜잭션 로그 문제의 일반적인 원인과 해결 방법은 SQL Server와 비슷합니다. 

SQL Server와 마찬가지로 각 데이터베이스의 트랜잭션 로그는 로그 백업이 수행될 때마다 잘립니다. 잘린 만큼 로그 파일에 빈 공간이 생기고, 새 트랜잭션에 액세스할 수 있게 됩니다. 로그 백업 시 로그 파일을 자를 수 없는 경우 새 트랜잭션을 수용할 수 있도록 로그 파일이 커집니다. 로그 파일이 Azure SQL Database 또는 Azure SQL Managed Instance의 최대 한도까지 커지면 새 트랜잭션을 수락할 수 없습니다. 이러한 상황은 매우 드뭅니다.

## <a name="prevented-transaction-log-truncation"></a>트랜잭션 로그 자르기가 차단됨

로그 자르기를 막는 원인을 알아보려면 `sys.databases`의 `log_reuse_wait_desc`를 참조하세요. 로그 재사용 대기는 정기적 로그 백업 시에 트랜잭션 로그를 자르지 못하게 막는 조건 또는 원인을 알려줍니다. 자세한 내용은 [sys.databases&#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)를 참조하세요. 

```sql
SELECT [name], log_reuse_wait_desc FROM sys.databases;
```

`sys.databases`의 다음 `log_reuse_wait_desc` 값은 데이터베이스의 트랜잭션 로그 자르기가 차단되는 이유를 나타낼 수 있습니다.

| log_reuse_wait_desc | 진단 | 응답 필요 |
|--|--|--|
| **NOTHING** | 일반적인 상태입니다. 아무 것도 로그 자르기를 차단하지 않습니다. | 아니요. |
| **CHECKPOINT** | 로그 자르기의 검사점이 필요합니다. 매우 드물게 발생합니다. | 문제가 지속되지 않는 한 응답이 필요하지 않습니다. 문제가 지속되는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다. | 
| **LOG BACKUP** | 로그 백업이 진행 중입니다. | 문제가 지속되지 않는 한 응답이 필요하지 않습니다. 문제가 지속되는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다. | 
| **ACTIVE BACKUP OR RESTORE** | 데이터베이스 백업이 진행 중입니다. | 문제가 지속되지 않는 한 응답이 필요하지 않습니다. 문제가 지속되는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다. | 
| **ACTIVE TRANSACTION** | 진행 중인 트랜잭션이 로그 자르기를 차단하고 있습니다. | 활성 및/또는 커밋되지 않은 트랜잭션으로 인해 로그 파일을 자를 수 없습니다. 다음 섹션을 참조하십시오.| 
| **REPLICATION** | Azure SQL Database에서는 [CDC(변경 데이터 캡처)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) 기능이 원인일 가능성이 높습니다.<BR>Azure SQL Managed Instance에서는 [복제](../managed-instance/replication-transactional-overview.md) 또는 CDC가 원인입니다. | Azure SQL Database에서는 [sys.dm_cdc_errors](/sql/relational-databases/system-dynamic-management-views/change-data-capture-sys-dm-cdc-errors)를 쿼리하여 오류를 해결합니다. 해결할 수 없는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다.<BR>Azure SQL Managed Instance에서 문제가 지속되는 경우 CDC 또는 복제와 관련된 에이전트를 조사합니다. CDC 문제 해결을 위해 [msdb.dbo.cdc_jobs](/sql/relational-databases/system-tables/dbo-cdc-jobs-transact-sql)의 작업을 쿼리합니다. 작업이 없는 경우 [sys.sp_cdc_add_job](/sql/relational-databases/system-stored-procedures/sys-sp-cdc-add-job-transact-sql)을 통해 추가합니다. 복제인 경우 [트랜잭션 복제 문제 해결](/sql/relational-databases/replication/troubleshoot-tran-repl-errors)을 고려해 봅니다. 해결할 수 없는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다. | 
| **AVAILABILITY_REPLICA** | 보조 복제본에 대한 동기화가 진행 중입니다. | 문제가 지속되지 않는 한 응답이 필요하지 않습니다. 문제가 지속되는 경우 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)을 통해 지원 요청을 제출합니다. | 

### <a name="log-truncation-prevented-by-an-active-transaction"></a>활성 트랜잭션에 의해 로그 자르기가 차단됨

새 트랜잭션을 수락할 수 없는 트랜잭션 로그에 대한 가장 일반적인 시나리오는 장기 실행 또는 차단된 트랜잭션입니다.

다음과 같은 작업을 수행하는 아래의 샘플 쿼리를 실행하여 커밋되지 않은 트랜잭션 또는 활성 트랜잭션 및 해당 속성을 찾습니다.

- [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)에서 트랜잭션 속성에 대한 정보를 반환합니다.
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)에서 세션 연결 정보를 반환합니다.
- [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)에서 요청 정보(활성 요청의 경우)를 반환합니다. 이 쿼리를 사용하여 차단되는 세션을 식별하고 `request_blocked_by`를 찾을 수도 있습니다. 차단에 대한 자세한 내용은 [차단 정보 수집](understand-resolve-blocking.md#gather-blocking-information)을 참조하세요. 
- [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 또는 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV를 사용하여 현재 요청의 텍스트 또는 입력 버퍼 텍스트를 반환합니다. `sys.dm_exec_sql_text`의 `text` 필드에서 반환한 데이터가 NULL이면 요청이 활성 상태는 아니지만 미해결 트랜잭션이 있습니다. 이 경우 `sys.dm_exec_input_buffer`의 `event_info` 필드에는 데이터베이스 엔진에 전달된 마지막 명령 문자열이 포함됩니다. 

```sql
SELECT [database_name] = db_name(s.database_id)
, tat.transaction_id, tat.transaction_begin_time, tst.session_id 
, session_open_transaction_count = tst.open_transaction_count --uncommitted and unrolled back transactions open. 
, transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime())
, input_buffer = ib.event_info
, request_text = CASE  WHEN r.statement_start_offset = 0 and r.statement_end_offset= 0 THEN left(est.text, 4000)
                       ELSE    SUBSTRING ( est.[text],    r.statement_start_offset/2 + 1, 
                                           CASE WHEN r.statement_end_offset = -1 THEN LEN (CONVERT(nvarchar(max), est.[text])) 
                                                ELSE r.statement_end_offset/2 - r.statement_start_offset/2 + 1
                                           END  )  END
, request_status = r.status
, request_blocked_by = r.blocking_session_id
, transaction_state = CASE tat.transaction_state    
                     WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                     WHEN 1 THEN 'The transaction has been initialized but has not started.'
                     WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                     WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                     WHEN 4 THEN 'The commit process has been initiated on the distributed transaction. This is for distributed transactions only. The distributed transaction is still active but further processing cannot take place.'
                     WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                     WHEN 6 THEN 'The transaction has been committed.'
                     WHEN 7 THEN 'The transaction is being rolled back.'
                     WHEN 8 THEN 'The transaction has been rolled back.' END 
, transaction_name = tat.name
, azure_dtc_state    --Applies to: Azure SQL Database only
             =    CASE tat.dtc_state 
                 WHEN 1 THEN 'ACTIVE'
                 WHEN 2 THEN 'PREPARED'
                 WHEN 3 THEN 'COMMITTED'
                 WHEN 4 THEN 'ABORTED'
                 WHEN 5 THEN 'RECOVERED' END
, transaction_type = CASE tat.transaction_type    WHEN 1 THEN 'Read/write transaction'
                                             WHEN 2 THEN 'Read-only transaction'
                                             WHEN 3 THEN 'System transaction'
                                             WHEN 4 THEN 'Distributed transaction' END
, tst.is_user_transaction
, local_or_distributed = CASE tst.is_local WHEN 1 THEN 'Local transaction, not distributed' WHEN 0 THEN 'Distributed transaction or an enlisted bound session transaction.' END
, transaction_uow    --for distributed transactions. 
, s.login_time, s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
, session_cpu_time = s.cpu_time, session_logical_reads = s.logical_reads, session_reads = s.reads, session_writes = s.writes
, observed = sysdatetimeoffset()
FROM sys.dm_tran_active_transactions AS tat 
INNER JOIN sys.dm_tran_session_transactions AS tst  on tat.transaction_id = tst.transaction_id
INNER JOIN Sys.dm_exec_sessions AS s on s.session_id = tst.session_id 
LEFT OUTER JOIN sys.dm_exec_requests AS r on r.session_id = s.session_id
CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib 
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) AS est;
```


### <a name="file-management-to-free-more-space"></a>더 많은 공간을 확보하도록 파일 관리

트랜잭션 로그를 자를 수 없도록 차단된 경우 데이터베이스 파일 할당에서 공간을 확보하면 문제 해결에 도움이 될 수 있습니다. 그러나 트랜잭션 로그 파일 자르기를 차단하는 근본 원인을 해결하는 것이 중요합니다. 

경우에 따라 임시로 더 많은 디스크 공간을 만들면 장기 실행 트랜잭션이 완료되고, 일반적인 트랜잭션 로그 백업 시 트랜잭션 로그 파일 자르기를 차단하는 조건이 제거됩니다. 하지만 할당에서 공간을 확보하더라도 트랜잭션 로그가 다시 증가할 때까지 일시적으로만 문제가 완화됩니다. 

데이터베이스 및 탄력적 풀의 파일 공간 관리에 대한 자세한 내용은 [Azure SQL Database의 데이터베이스에 대한 파일 공간 관리](file-space-manage.md)를 참조하세요.


### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>오류 40552: 트랜잭션 로그 공간 사용량이 너무 많아 세션이 종료됨

`40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.`

이 문제를 해결하려면 다음 방법을 사용해 보세요.

1. 이 문제는 삽입, 업데이트 또는 삭제 작업으로 인해 발생할 수 있습니다. 트랜잭션을 검토하여 불필요한 쓰기를 방지하세요. 일괄 처리를 구현하거나 여러 개의 여러 개의 작은 트랜잭션으로 분할하여 즉시 실행되는 행 수를 줄여 보세요. 자세한 내용은 [배치를 사용하여 SQL Database 애플리케이션 성능을 개선하는 방법](../performance-improve-use-batching.md)을 참조하세요.
2. 인덱스 다시 빌드 작업으로 인해 이 문제가 발생할 수 있습니다. 이 문제를 방지하려면 (테이블에서 영향을 받는 행의 수) * (업데이트된 필드의 평균 크기(바이트) + 80) < 2GB(기가바이트) 수식이 true이어야 합니다. 대형 테이블의 경우 파티션을 만들고 테이블의 일부 파티션에서만 인덱스 유지 관리를 수행하는 것이 좋습니다. 자세한 내용은 [분할된 테이블 및 인덱스 만들기](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=azuresqldb-current&preserve-view=true)를 참조하세요.
3. `bcp.exe` 유틸리티 또는 `System.Data.SqlClient.SqlBulkCopy` 클래스를 사용하여 대량 삽입을 수행하는 경우 `-b batchsize` 또는 `BatchSize` 옵션을 사용하여 각 트랜잭션에서 서버로 복사된 행의 수를 제한하세요. 자세한 내용은 [bcp Utility](/sql/tools/bcp-utility)를 참조하세요.
4. `ALTER INDEX` 문을 사용하여 인덱스를 다시 작성하는 경우 `SORT_IN_TEMPDB = ON` 및 `ONLINE = ON` 옵션을 사용합니다. 자세한 내용은 [ALTER INDEX(Transact-SQL)](/sql/t-sql/statements/alter-index-transact-sql)를 참조하세요.

> [!NOTE]
> 다른 리소스 관리자 오류에 대한 자세한 내용은 [리소스 거버넌스 오류](troubleshoot-common-errors-issues.md#resource-governance-errors)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 및 Azure SQL Managed Instance의 연결 문제 및 기타 오류 문제 해결](troubleshoot-common-errors-issues.md)
- [SQL Database 및 SQL Managed Instance에서 일시적인 연결 오류 문제 해결](troubleshoot-common-connectivity-issues.md)


트랜잭션 로그 크기에 대한 자세한 내용은 다음을 참조하세요. 
- 단일 데이터베이스의 vCore 리소스 한도에 대해서는 [vCore 구매 모델을 사용하는 단일 데이터베이스의 리소스 한도](resource-limits-vcore-single-databases.md)를 참조하세요.
- 탄력적 풀의 vCore 리소스 한도에 대해서는 [vCore 구매 모델을 사용하는 탄력적 풀의 리소스 한도](resource-limits-vcore-elastic-pools.md)를 참조하세요.
- 단일 데이터베이스의 DTU 리소스 한도는 [DTU 구매 모델을 사용한 단일 데이터베이스의 리소스 한도](resource-limits-dtu-single-databases.md)를 참조하세요.
- 탄력적 풀의 DTU 리소스 한도에 대해서는 [DTU 구매 모델을 사용하는 탄력적 풀의 리소스 한도](resource-limits-dtu-elastic-pools.md)를 참조하세요.
- SQL Managed Instance의 리소스 한도는 [SQL Managed Instance의 리소스 한도](../managed-instance/resource-limits.md)를 참조하세요.
