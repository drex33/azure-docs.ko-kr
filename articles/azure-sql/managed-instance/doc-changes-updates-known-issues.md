---
title: 알려진 문제
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 현재 알려진 문제 및 가능한 해결 방법 또는 해결 방법에 대해 알아봅니다.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 97cf7977d6e867d0c3bbc106f599bc69db4d987b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465234"
---
# <a name="known-issues-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance 알려진 문제
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 현재 알려진 [Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance)문제 및 해결 날짜 또는 가능한 해결 방법을 나열합니다. Azure SQL Managed Instance 대한 자세한 내용은 [개요](sql-managed-instance-paas-overview.md)및 [새로운 을](doc-changes-updates-release-notes-whats-new.md)참조하세요. 

 
## <a name="known-issues"></a>알려진 문제

|문제  |발견된 날짜  |상태  |해결된 날짜  |
|---------|---------|---------|---------|
|[서비스 주체의 다시 Azure Portal 제안하는 잘못된 오류 메시지](#misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal)|2021년 9월|||
|[연결 유형을 변경해도 장애 조치(failover) 그룹 엔드포인트를 통한 연결에는 영향을 주지 않습니다.](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|2021년 1월|해결 방법 있음||
|[ @query매개 변수를 사용할 때 sp_send_dbmail 프로시저가 일시적으로 실패할 수 있습니다.](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|2021년 1월|해결 방법 있음||
|[서버 신뢰 그룹에서 관리되는 인스턴스를 제거한 후 분산 트랜잭션을 실행할 수 있습니다.](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020년 10월|해결 방법 있음||
|[관리되는 인스턴스 크기 조정 작업 후에는 분산 트랜잭션을 실행할 수 없습니다.](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020년 10월|해결됨|2021년 5월|
|[이전에 삭제한 논리 서버와 동일한 이름으로 SQL Managed Instance를 만들 수 없습니다.](#cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted)|2020년 8월|해결 방법 있음||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / Azure의 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) SQL 및 `BACKUP` / `RESTORE` SQL Managed Instance 문은 Azure AD 관리 ID를 사용하여 Azure Storage에 인증할 수 없습니다.|2020년 9월|해결 방법 있음||
|[서비스 주체는 Azure AD 및 AKV에 액세스할 수 없음](#service-principal-cannot-access-azure-ad-and-akv)|2020년 8월|해결 방법 있음||
|[CHECKSUM 없는 수동 백업 복원이 실패할 수 있음](#restoring-manual-backup-without-checksum-might-fail)|2020년 5월|해결됨|2020년 6월|
|[기존 작업을 수정하면 또는 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않음](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020년 5월|해결됨|2020년 6월|
|[리소스 그룹에 대한 권한이 SQL Managed Instance에 적용되지 않음](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020년 2월|해결됨|2020년 11월|
|[장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020년 1월|해결 방법 있음||
|[SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한 필요](#in-memory-oltp-memory-limits-are-not-applied)|2019년 12월|해결 방법 있음||
|[에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019년 12월|해결됨|2020년 3월|
|[Azure AD 로그인 및 사용자가 SSDT에서 지원되지 않음](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019년 11월|해결 방법 없음||
|[메모리 내 OLTP 메모리 제한이 적용되지 않음](#in-memory-oltp-memory-limits-are-not-applied)|2019년 10월|해결 방법 있음||
|[비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019년 10월|해결 방법 있음||
|[진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019년 9월|해결 방법 있음||
|[장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019년 9월|해결 방법 있음||
|[서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화를 다시 초기화해야 함](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019년 8월|해결 방법 있음||
|[Azure AD 로그인 유형의 가장이 지원되지 않음](#impersonation-of-azure-ad-login-types-is-not-supported)|2019년 7월|해결 방법 없음||
|[@query 매개 변수가 sp_send_db_mail에서 지원되지 않음](#-parameter-not-supported-in-sp_send_db_mail)|2019년 4월|해결됨|2021년 1월|
|[지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019년 3월|해결 방법 없음||
|[복원 작업 중에 임시 데이터베이스가 사용됨](#temporary-database-is-used-during-restore-operation)||해결 방법 있음||
|[TEMPDB 구조와 콘텐츠가 다시 생성됨](#tempdb-structure-and-content-is-re-created)||해결 방법 없음||
|[작은 데이터베이스 파일로 스토리지 공간 초과](#exceeding-storage-space-with-small-database-files)||해결 방법 있음||
|[데이터베이스 이름 대신 GUID 값이 표시됨](#guid-values-shown-instead-of-database-names)||해결 방법 있음||
|[오류 로그가 지속되지 않음](#error-logs-arent-persisted).||해결 방법 없음||
|[동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||해결 방법 있음|2020년 3월|
|[CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||해결 방법 있음||
|Azure Blob Storage에서 데이터베이스를 복원한 후 DBCC CHECKDB를 사용하여 데이터베이스 일관성을 확인하지 못함||해결됨|2019년 11월|
|원본 데이터베이스에 메모리 내 OLTP 개체가 포함되어 있으면 중요 비즈니스용 계층에서 범용 계층으로 특정 시점 데이터베이스 복원에 실패함||해결됨|2019년 10월|
|보안 연결을 사용하는 외부(비 Azure) 메일 서버와 데이터베이스 메일 기능||해결됨|2019년 10월|
|포함된 데이터베이스는 SQL Managed Instance에서 지원되지 않습니다.||해결됨|2019년 8월|
||||| 

## <a name="resolved"></a>해결됨

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM 없는 수동 백업 복원이 실패할 수 있음

특정 상황에서 CHECKSUM 없이 관리형 인스턴스에서 수행한 데이터베이스 수동 백업이 복원되지 않을 수 있습니다. 이러한 경우 성공할 때까지 백업 복원을 다시 시도하세요.

**해결 방법**: CHECKSUM을 사용하도록 설정한 상태로 관리형 인스턴스에서 데이터베이스 수동 백업을 수행합니다.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>기존 작업을 수정하면 또는 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않음

특정 상황에서 기존 작업을 수정하거나 사용하거나 사용하지 않도록 설정하면 에이전트가 응답하지 않을 수 있습니다. 이 문제는 감지되면 자동으로 완화되어 에이전트 프로세스가 다시 시작됩니다.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>리소스 그룹에 대한 권한이 SQL Managed Instance에 적용되지 않음

SQL Managed Instance 기여자 Azure 역할이 RG(리소스 그룹)에 적용되면 SQL Managed Instance에 적용되지 않으며 효과가 없습니다.

**해결 방법**: 사용자용 SQL Managed Instance 기여자 역할을 구독 수준에서 설정합니다.

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음

**(2020년 3월에 해결됨)** SQL 에이전트는 작업이 시작될 때마다 새 세션을 생성하여 메모리 사용이 점차적으로 증가합니다. 예약된 작업 실행이 차단되는 내부 메모리 제한에 도달하지 않도록 메모리 사용량이 임계값에 도달하면 에이전트 프로세스가 다시 시작됩니다. 이로 인해 다시 시작될 때 실행 중인 작업이 중단될 수 있습니다.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query 매개 변수가 sp_send_db_mail에서 지원되지 않음

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저의 `@query` 매개 변수가 작동하지 않습니다.

## <a name="has-workaround"></a>해결 방법

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>연결 유형을 변경해도 장애 조치(failover) 그룹 엔드포인트를 통한 연결에는 영향을 주지 않습니다.

인스턴스가 [자동 장애 조치(failover) 그룹](../database/auto-failover-group-overview.md)에 참여하는 경우 인스턴스의 [연결 유형](../managed-instance/connection-types-overview.md)을 변경해도 장애 조치(failover) 그룹 수신기 엔드포인트를 통해 설정된 연결에는 적용되지 않습니다.

**해결 방법**: 연결 유형을 변경한 후 자동 장애 조치(failover) 그룹을 삭제하고 다시 만듭니다.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>@query 매개 변수를 사용하면 sp_send_dbmail 프로시저가 일시적으로 실패할 수 있음

`@query` 매개 변수를 사용하면 `sp_send_dbmail` 프로시저가 일시적으로 실패할 수 있음 이 문제가 발생하면 sp_send_dbmail 프로시저의 두 번째 실행이 `Msg 22050, Level 16, State 1` 오류 및 `Failed to initialize sqlcmd library with error number -2147467259` 메시지와 함께 실패합니다. 이 오류를 제대로 확인하려면 `@exclude_query_output` 매개 변수에 대해 기본값 0으로 프로시저를 호출해야 합니다. 그러지 않으면 오류가 전파되지 않습니다.
이 문제는 `sp_send_dbmail`이 가장 및 연결 풀링을 사용하는 방법과 관련된 알려진 버그로 인해 발생합니다.
이 문제를 해결하려면 출력 매개 변수 `@mailitem_id`에 의존하는 다시 시도 논리로 이메일을 보내기 위한 랩 코드를 사용합니다. 실행이 실패하면 매개 변수 값이 NULL이 되어 이메일을 성공적으로 전송하기 위해 `sp_send_dbmail`을 한 번 더 호출해야 함을 나타냅니다. 다음은 이 다시 시도 논리의 예입니다.

```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>서버 신뢰 그룹에서 관리되는 인스턴스를 제거한 후 분산 트랜잭션을 실행할 수 있습니다.

[서버 신뢰 그룹은](../managed-instance/server-trust-group-overview.md) [분산 트랜잭션을](../database/elastic-transactions-overview.md)실행하기 위한 필수 조건인 관리되는 인스턴스 간에 트러스트를 설정하는 데 사용됩니다. 서버 신뢰 그룹에서 관리되는 인스턴스를 제거하거나 그룹을 삭제한 후에도 분산 트랜잭션을 실행할 수 있습니다. 분산 트랜잭션이 비활성화되고 관리되는 인스턴스에서 사용자가 시작한 수동 장애 [조치(failover)되도록](../managed-instance/user-initiated-failover.md) 적용할 수 있는 해결 방법이 있습니다.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>관리되는 인스턴스 크기 조정 작업 후에는 분산 트랜잭션을 실행할 수 없습니다.

SQL 서비스 계층 또는 vCore 수를 변경하는 작업을 포함하는 Managed Instance 크기 조정 작업은 백 엔드에서 서버 신뢰 그룹 설정을 다시 설정하고 [분산 트랜잭션](../database/elastic-transactions-overview.md)실행을 사용하지 않도록 설정합니다. 해결 방법으로 Azure Portal에서 새 [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)을 삭제하고 만듭니다.

### <a name="cannot-create-sql-managed-instance-with-the-same-name-as-logical-server-previously-deleted"></a>이전에 삭제한 논리 서버와 동일한 이름으로 SQL Managed Instance를 만들 수 없습니다.

의 DNS `<name>.database.windows.com` 레코드는 [azure에서](../database/logical-servers.md) Azure SQL Database 논리 서버를 만들 때와 SQL Managed Instance 만들 때 만들어집니다. DNS 레코드는 고유해야 합니다. 따라서 SQL Database 논리 서버를 만든 다음 삭제하는 경우 레코드에서 이름이 해제되기까지 7일의 임계값 기간이 있습니다. 해당 기간에는 삭제된 논리 서버와 동일한 이름으로 SQL Managed Instance 만들 수 없습니다. 해결하려면 SQL Managed Instance 다른 이름을 사용하거나 지원 티켓을 만들어 논리 서버 이름을 해제합니다.  


### <a name="bulk-insert-and-backuprestore-statements-should-use-sas-key-to-access-azure-storage"></a>BULK INSERT 및 BACKUP/RESTORE 문은 SAS 키를 사용하여 Azure Storage에 액세스해야 합니다.

현재 Azure Storage `DATABASE SCOPED CREDENTIAL` 인증하기 위해 관리 ID에서 구문이 지원되지 않습니다. 대량 삽입을 위해 Azure Storage [액세스하는](../../storage/common/storage-sas-overview.md) 경우 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-credential-transact-sql#d-creating-a-credential-using-a-sas-token)에 공유 액세스 서명을 사용하는 것이 `BACKUP` `RESTORE` `OPENROWSET` 좋습니다. 예를 들면 다음과 같습니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL sas_cred WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=2016-12-29T16:55:34Z***************';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/invoices', CREDENTIAL= sas_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

SAS 키와 함께 `BULK INSERT`를 사용하는 또 다른 예는 [스토리지 인증을 위한 공유 액세스 서명](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)을 참조하세요. 

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>서비스 주체는 Azure AD 및 AKV에 액세스할 수 없습니다.

경우에 따라 Azure AD 및 AKV(Azure Key Vault) 서비스에 액세스하는 데 사용되는 서비스 주체에 문제가 있을 수 있습니다. 따라서 이 문제는 SQL Managed Instance를 사용하여 Azure AD 인증 및 TDE(투명한 데이터베이스 암호화) 사용에 영향을 줍니다. 이는 일시적인 연결 문제 또는 다음과 같은 문을 실행할 수 없는 경우 발생할 수 있습니다. `CREATE LOGIN/USER FROM EXTERNAL PROVIDER` 또는 `EXECUTE AS LOGIN/USER`. 새 Azure SQL Managed Instance에서 고객 관리형 키로 TDE를 설정하는 것도 일부 상황에서 작동하지 않을 수 있습니다.

**해결 방법**: 업데이트 명령을 실행하기 전에 SQL Managed Instance에서 이 문제가 발생하지 않도록 하거나 업데이트 명령 후에 이미 이 문제가 발생한 경우 Azure Portal로 이동하여 SQL Managed Instance [Active Directory 관리 페이지](../database/authentication-aad-configure.md?tabs=azure-powershell#azure-portal)에 액세스합니다. 다음 오류 메시지가 표시되는지 확인합니다. "Azure Active Directory에 액세스하려면 관리되는 인스턴스에 서비스 주체가 필요합니다. 서비스 주체를 만들려면 여기를 클릭하세요." 이 오류 메시지가 발생한 경우 해당 메시지를 클릭하고 이 오류가 해결될 때까지 제공된 단계별 지침을 따르세요.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항

장애 조치(failover) 그룹이 서로 다른 Azure 구독이나 리소스 그룹의 인스턴스에 걸쳐 있으면 장애 조치(failover) 그룹의 주 인스턴스에서 수동 장애 조치(failover)를 시작할 수 없습니다.

**해결 방법**: 지리적 보조 인스턴스에서 포털을 통해 장애 조치(failover)를 시작합니다.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한이 필요함

비 sysadmin 로그인이 [SQL 에이전트 고정 데이터베이스 역할](/sql/ssms/agent/sql-server-agent-fixed-database-roles)에 추가되어 있는 경우 해당 로그인이 작동하려면 master 데이터베이스의 세 개 저장 프로시저에 명시적인 EXECUTE 권한을 부여해야 하는 문제가 있습니다. 이런 문제가 발생하면 "개체 <object_name>에 대한 EXECUTE 권한이 거부되었습니다(Microsoft SQL Server, 오류: 229)" 오류 메시지가 표시됩니다.

**해결 방법**: SQL 에이전트 고정 데이터베이스 역할인 SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole에 로그인을 추가하면 이러한 역할에 추가된 각 로그인에 대해 아래 T-SQL 스크립트를 실행하여 나열된 저장 프로시저에 EXECUTE 권한을 명시적으로 부여합니다.

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>메모리 내 OLTP 메모리 제한이 적용되지 않음

중요 비즈니스용 서비스 계층이 [메모리 최적화 개체에 대한 최대 메모리 제한](../managed-instance/resource-limits.md#in-memory-oltp-available-space)을 올바르게 적용하지 않는 경우가 있습니다. SQL Managed Instance를 사용하면 워크로드가 메모리 내 OLTP 작업에 메모리를 더 많이 사용할 수 있기 때문에 인스턴스의 가용성과 안정성에 영향을 줄 수 있습니다. 한도에 도달한 메모리 내 OLTP 쿼리는 즉시 실패하지 않을 수도 있습니다. 이 문제는 곧 해결될 예정입니다. 메모리 내 OLTP 메모리를 더 많이 사용하는 쿼리는 [한도](../managed-instance/resource-limits.md#in-memory-oltp-available-space)에 도달하면 더 빨리 실패합니다.

**해결 방법:** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring)를 사용하여 [메모리 내 OLTP 스토리지 사용량을 모니터링](../in-memory-oltp-monitor-space.md)하여 워크로드가 사용 가능한 메모리보다 많은 메모리를 사용하지 않도록 합니다. vCore 수에 따라 메모리 한도를 늘리거나 메모리를 덜 사용하도록 워크로드를 최적화합니다.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨

SQL Server 및 SQL Managed Instance에서는 [사용자가 비어 있지 않은 파일을 삭제할 수 없습니다](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). `ALTER DATABASE REMOVE FILE` 문을 사용하여 비어 있지 않은 데이터 파일을 삭제하려고 하면 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 오류가 즉시 반환되지 않습니다. SQL Managed Instance에서 파일을 삭제하려고 계속 시도하며, 30분이 지나면 `Internal server error`로 인해 작업이 실패합니다.

**해결 방법**: `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 명령을 사용하여 파일의 콘텐츠를 제거합니다. 파일 그룹에 연결된 테이블 또는 파티션에서 데이터를 삭제해야 하는 이 파일 그룹의 유일한 파일인 경우에는 파일을 축소하기 전에 필요에 따라 해당 데이터를 다른 테이블/파티션에 로드합니다.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨

`RESTORE` 문, Data Migration Service 마이그레이션 프로세스, 기본 제공 특정 시점 복원을 실행 중이면 복원 프로세스가 완료될 때까지 서비스 계층 업데이트 또는 기존 인스턴스 크기 조정 및 새 인스턴스 만들기가 차단됩니다. 

복원 프로세스는 복원 프로세스를 실행 중인 동일한 서브넷의 Managed Instance 및 인스턴스 풀에서 이러한 작업을 차단합니다. 인스턴스 풀의 인스턴스는 영향을 받지 않습니다. 서비스 계층 생성 또는 변경 작업이 실패하거나 시간 초과되지 않고 복원 프로세스가 완료되거나 취소되면 계속 진행됩니다.

**해결 방법**: 복원 프로세스가 완료될 때까지 기다리거나, 서비스 계층 만들기 또는 업데이트 작업의 우선 순위가 더 높으면 복원 프로세스를 취소합니다.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음

사용자 워크로드에 할당된 리소스를 제한할 수 있는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 기능이 장애 조치(failover) 후에 또는 사용자가 시작한 서비스 계층 변경(예: 최대 vCore 변경 또는 최대 인스턴스 스토리지 크기 변경) 후에 일부 사용자 워크로드를 잘못 분류할 수 있습니다.

**해결 방법**: `ALTER RESOURCE GOVERNOR RECONFIGURE`를 정기적으로 실행하거나 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)를 사용하는 경우에는 인스턴스가 시작될 때 SQL 작업을 실행하는 SQL 에이전트 작업의 일부로 실행합니다.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화를 다시 초기화해야 함

데이터베이스 간 Service Broker dialog는 서비스 계층 변경 작업 후에 다른 데이터베이스의 서비스로 메시지를 배달하지 않습니다. 메시지는 *손실되지 않으며* 보낸 사람 큐에서 찾을 수 있습니다. SQL Managed Instance에서 vCore 또는 인스턴스 스토리지 크기를 변경하면 모든 데이터베이스에 대해 [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 뷰의 `service_broke_guid` 값이 변경됩니다. 다른 데이터베이스의 Service Broker를 참조하는 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 문을 사용하여 생성된 `DIALOG`가 있으면 대상 서비스에 메시지 배달이 중지됩니다.

**해결 방법**: 서비스 계층을 업데이트하기 전에 데이터베이스 간 Service Broker 대화를 사용하는 작업을 중지했다가 다시 초기화합니다. 서비스 계층 변경 후 배달되지 않은 메시지가 남아 있으면 소스 큐에서 메시지를 읽어서 대상 큐로 다시 보냅니다.

### <a name="temporary-database-is-used-during-restore-operation"></a>복원 작업 중에 임시 데이터베이스가 사용됨

데이터베이스가 SQL Managed Instance에서 복원되는 경우 복원 서비스는 먼저 원하는 이름으로 빈 데이터베이스를 만들고 인스턴스에 이름을 할당합니다. 잠시 후 이 데이터베이스는 삭제되고 실제 데이터베이스 복원이 시작됩니다. 

*복원 중* 상태인 데이터베이스는 임시로 이름 대신 무작위 GUID 값을 갖습니다. 임시 이름은 복원 프로세스가 끝나면 `RESTORE` 문에 지정된 원하는 이름으로 변경됩니다. 

초기 단계에서 사용자는 빈 데이터베이스에 액세스할 수 있고 이 데이터베이스에 테이블을 만들거나 데이터를 로드할 수도 있습니다. 임시 데이터베이스는 복원 서비스가 두 번째 단계를 시작하면 삭제됩니다.

**해결 방법**: 복원이 완료된 것을 볼 때까지 복원 중인 데이터베이스에 액세스하지 마십시오.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, `RESTORE DATABASE` 문은 인스턴스가 Azure Storage 제한에 도달할 수 있기 때문에 실패할 수 있습니다.

각 범용 SQL Managed Instance의 인스턴스에는 최대 35TB의 스토리지가 Azure 프리미엄 디스크 공간용으로 예약되어 있습니다. 각 데이터베이스 파일은 별도의 실제 디스크에 배치됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크에서 사용되지 않은 공간은 요금이 부과되지 않지만 Azure 프리미엄 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요하지 않은 관리형 인스턴스는 내부 조각화로 인해 스토리지 크기에 대한 35TB Azure 제한을 초과할 수 있습니다.

예를 들어 SQL Managed Instance의 범용 인스턴스에는 4TB 디스크에 크기가 1.2TB인 대용량 파일이 하나 있을 수 있습니다. 또한 각각 1GB이고 별도의 128GB 디스크에 있는 248개의 파일이 있을 수 있습니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예는 특정 상황에서 특정 파일 배포로 인해 SQL Managed Instance의 인스턴스가 연결되지 않은 Azure 프리미엄 디스크에 예약되어 있는 35TB 한도에 도달할 수 있음을 보여 줍니다.

이 예에서 기존 데이터베이스는 계속 작동하며, 새 파일이 추가되지 않는 한 문제 없이 커질 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브에 대한 충분한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환되는 오류가 명확하지 않습니다.

시스템 뷰를 사용하여 [남은 파일 수를 식별](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)할 수 있습니다. 이 제한에 도달하면 [DBCC SHRINKFILE 문을 사용하여 작은 파일 중 일부를 비우고 삭제](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)해보거나 [이런 제한이 없는 중요 비즈니스용 계층](../managed-instance/resource-limits.md#service-tier-characteristics)으로 전환합니다.


### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 GUID 값이 표시됨

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체되기 때문에 사용하지 마세요.

**해결 방법**: `sys.databases` 뷰를 사용하여 실제 데이터베이스 이름에서 GUID 데이터베이스 식별자 형식으로 지정된 실제 데이터베이스 이름을 확인합니다.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4;
```

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음

**(2020년 3월에 해결됨)** 동일한 트랜잭션 범위의 동일한 인스턴스 내에서 데이터베이스 두 개에 쿼리 두 개를 보내면 .NET의 `TransactionScope` 클래스가 작동하지 않습니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**해결 방법(2020년 3월 이후 필요하지 않음):** [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)를 사용하여 두 연결을 사용하는 대신 연결 컨텍스트에서 다른 데이터베이스를 사용합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음

SQL Managed Instance 및 연결된 서버 또는 현재 인스턴스를 참조하는 분산 쿼리의 CLR 모듈에서 로컬 인스턴스의 IP를 확인할 수 없는 경우가 있습니다. 이 오류는 일시적인 문제입니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음

**(2020년 3월에 해결됨)** 동일한 트랜잭션 범위의 동일한 인스턴스 내에서 데이터베이스 두 개에 쿼리 두 개를 보내면 .NET의 `TransactionScope` 클래스가 작동하지 않습니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**해결 방법(2020년 3월 이후 필요하지 않음):** [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)를 사용하여 두 연결을 사용하는 대신 연결 컨텍스트에서 다른 데이터베이스를 사용합니다.


## <a name="no-resolution"></a>해결 방법 없음

### <a name="misleading-error-message-on-azure-portal-suggesting-recreation-of-the-service-principal"></a>서비스 주체의 다시 Azure Portal 제안하는 잘못된 오류 메시지

서비스 주체가 이미 있는 경우에도 Azure SQL Managed Instance 대한 Azure Portal _Active Directory 관리자_ 블레이드에 다음 오류 메시지가 표시될 수 있습니다.

"Managed Instance Azure Active Directory 액세스하려면 서비스 주체가 필요합니다. 서비스 주체를 만들려면 여기를 클릭하세요."

관리되는 인스턴스의 서비스 주체가 이미 있는 경우 이 오류 메시지를 무시하거나 관리되는 인스턴스에서 인증을 AAD 수 있습니다. 

서비스 주체가 있는지 확인하려면 _Azure Portal Enterprise 애플리케이션_ 페이지로 이동하고, 애플리케이션 _유형_ 드롭다운 목록에서 _관리 ID를_ 선택하고, _적용을_ 선택하고, 검색 상자에 관리되는 인스턴스의 이름을 입력합니다. 인스턴스 이름이 결과 목록에 표시되면 서비스 주체가 이미 존재하며 추가 작업이 필요하지 않습니다.

오류 메시지의 지침을 이미 따르고 오류 메시지에서 링크를 클릭한 경우 관리되는 인스턴스의 서비스 주체가 다시 생성되었습니다. 이 경우 Azure AD 인증이 제대로 작동하려면 새로 만든 서비스 주체에 Azure AD 읽기 권한을 할당하세요. 이 작업은 지침에 따라 Azure PowerShell 통해 수행할 수 [있습니다.](../database/authentication-aad-configure.md?tabs=azure-powershell#powershell)

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD 로그인 및 사용자가 SSDT에서 지원되지 않음

SQL Server Data Tools는 Azure AD 로그인 및 사용자를 완전히 지원하지 않습니다.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD 로그인 유형의 가장이 지원되지 않음

다음 Azure AD(Azure Active Directory) 주체의 `EXECUTE AS USER` 또는 `EXECUTE AS LOGIN`을 사용한 가장은 지원되지 않습니다.
- 별칭이 지정된 Azure AD 사용자. 이 경우 다음과 같은 오류가 반환됩니다. `15517`.
- Azure AD 애플리케이션 또는 서비스 주체를 기반으로 하는 Azure AD 로그인 및 사용자. 이 경우 다음과 같은 오류가 반환됩니다. `15517` 및 `15406`.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함

자동 장애 조치(failover) 그룹의 데이터베이스에서 트랜잭션 복제를 사용하도록 설정된 경우에는 다른 지역으로 장애 조치(failover)가 발생한 후 SQL Managed Instance 관리자가 모든 게시를 이전 주 데이터베이스에서 정리하고 새로운 주 데이터베이스에서 다시 구성해야 합니다. 자세한 내용은 [복제](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)를 참조하세요.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조와 콘텐츠가 다시 생성됨

`tempdb` 데이터베이스는 항상 12개의 데이터 파일로 분할되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기는 변경할 수 없으며, `tempdb`에 새 파일을 추가할 수 없습니다. `Tempdb`는 인스턴스가 시작되거나 장애 조치(failover)될 때 항상 빈 데이터베이스로 다시 생성되며 `tempdb`에서 변경한 내용은 유지되지 않습니다.


### <a name="error-logs-arent-persisted"></a>오류 로그가 지속되지 않음

SQL Managed Instance에서 사용할 수 있는 오류 로그는 유지되지 않으며, 해당 크기는 최대 스토리지 한도에 포함되지 않습니다. 장애 조치(failover)가 발생하면 오류 로그가 자동으로 지워질 수 있습니다. 여러 가상 머신에서 SQL Managed Instance가 여러 번 이동했기 때문에 오류 로그 기록에 차이가 있을 수 있습니다.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SQL Managed Instance 업데이트 및 개선의 목록은 서비스 업데이트 [SQL Managed Instance 참조하세요.](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance)

모든 Azure 서비스에 대한 개선 사항 및 업데이트는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.
