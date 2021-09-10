---
title: SQL Database와 SQL Managed Instance의 데이터베이스 엔진 기능 비교
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 이 문서에서는 Azure SQL Database와 Azure SQL Managed Instance의 데이터베이스 엔진 기능을 비교합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: bonova, mathoma, danil
ms.date: 08/12/2021
ms.openlocfilehash: 302b6e6cdce7fb95962b66a593ae02b9fe3922a1
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568315"
---
# <a name="features-comparison-azure-sql-database-and-azure-sql-managed-instance"></a>기능 비교: Azure SQL Database와 Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database와 SQL Managed Instance는 안정적인 최신 버전의 SQL Server를 사용하여 공통 코드 베이스를 공유합니다. 대부분의 표준 SQL 언어, 쿼리 처리, 데이터베이스 관리 기능은 동일합니다. SQL Server와 SQL Database 또는 SQL Managed Instance 간의 일반적인 기능은 다음과 같습니다.

- 언어 기능 - [흐름 언어 키워드](/sql/t-sql/language-elements/control-of-flow), [커서](/sql/t-sql/language-elements/cursors-transact-sql), [데이터 형식](/sql/t-sql/data-types/data-types-transact-sql), [DML 문](/sql/t-sql/queries/queries), [조건자](/sql/t-sql/queries/predicates), [시퀀스 번호](/sql/relational-databases/sequence-numbers/sequence-numbers), [저장 프로시저](/sql/relational-databases/stored-procedures/stored-procedures-database-engine), [변수](/sql/t-sql/language-elements/variables-transact-sql) 컨트롤
- 데이터베이스 기능 - [자동 조정(계획 강제 적용)](/sql/relational-databases/automatic-tuning/automatic-tuning), [변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [데이터베이스 데이터 정렬](/sql/relational-databases/collations/set-or-change-the-database-collation), [포함된 데이터베이스](/sql/relational-databases/databases/contained-databases), [포함된 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [데이터 압축](/sql/relational-databases/data-compression/data-compression), [데이터베이스 구성 설정](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [온라인 인덱스 작업](/sql/relational-databases/indexes/perform-index-operations-online), [분할](/sql/relational-databases/partitions/partitioned-tables-and-indexes), [temporal 테이블](/sql/relational-databases/tables/temporal-tables)([시작 가이드 참조](../temporal-tables.md))
- 보안 기능 - [애플리케이션 역할](/sql/relational-databases/security/authentication-access/application-roles), [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)([시작 가이드 참조](dynamic-data-masking-overview.md)), [행 수준 보안](/sql/relational-databases/security/row-level-security) 및 위협 탐지([SQL Database](threat-detection-configure.md) 및 [SQL Managed Instance](../managed-instance/threat-detection-configure.md)의 시작 가이드 참조)
- 다중 모델 기능 - [Graph 처리](/sql/relational-databases/graphs/sql-graph-overview), [JSON 데이터](/sql/relational-databases/json/json-data-sql-server)([시작 가이드 참조](json-features.md)), [OPENXML](/sql/t-sql/functions/openxml-transact-sql), [공간](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) 및 [XML 인덱스](/sql/t-sql/statements/create-xml-index-transact-sql)

Azure는 데이터베이스를 관리하고 데이터베이스의 고가용성을 보장합니다. 고가용성에 영향을 줄 수 있거나 PaaS 세계에서 사용할 수 없는 일부 기능은 SQL Database와 SQL Managed Instance에서 기능이 제한됩니다. 다음 표에서 이러한 기능에 관해 설명합니다.

차이점에 대한 자세한 내용이 필요한 경우 별도의 페이지에서 찾을 수 있습니다.
- [Azure SQL Database와 SQL Server 차이점 비교](transact-sql-tsql-differences-sql-server.md)
- [Azure SQL Managed Instance와 SQL Server 차이점 비교](../managed-instance/transact-sql-tsql-differences-sql-server.md)


## <a name="features-of-sql-database-and-sql-managed-instance"></a>SQL Database와 SQL Managed Instance의 기능

다음 표에서는 SQL Server의 주요 기능을 나열하고 기능에 대한 자세한 정보의 링크와 함께 기능이 Azure SQL Database와 Azure SQL Managed Instance에서 부분적으로 지원되는지 아니면 완전히 지원되는지에 대한 정보를 제공합니다.

| **기능** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](always-encrypted-certificate-store-configure.md) 및 [키 자격 증명 모음](always-encrypted-azure-key-vault-configure.md) 참조 | 예 - [인증서 저장소](always-encrypted-certificate-store-configure.md) 및 [키 자격 증명 모음](always-encrypted-azure-key-vault-configure.md) 참조 |
| [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 모든 데이터베이스에 대해 [99.99~99.995%의 가용성](high-availability-sla.md)이 보장됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. | [99.99%의 가용성](high-availability-sla.md)이 모든 데이터베이스에 대해 보장되며 [사용자가 이를 관리할 수 없습니다](../managed-instance/transact-sql-tsql-differences-sql-server.md#availability). 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 사용하여 다른 지역에서 보조 SQL Managed Instance를 구성합니다. SQL Server 인스턴스와 SQL Database는 SQL Managed Instance의 보조로 사용할 수 없습니다. |
| [데이터베이스 연결](/sql/relational-databases/databases/attach-a-database) | 예 | 예 |
| [감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](auditing-overview.md)| [예](../managed-instance/auditing-configure.md), 몇 가지 [차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) 있음. |
| [Azure AD(Azure Active Directory) 인증](authentication-aad-overview.md) | 예. Azure AD 사용자만. | 예. 서버 수준 Azure AD 로그인 포함. |
| [BACKUP 명령](/sql/t-sql/statements/backup-transact-sql) | 아니요. 시스템에서 시작한 자동 백업만 - [자동 백업](automated-backups-overview.md) 참조 | 예, 사용자가 시작한 Azure Blob Storage로 복사 전용 백업(사용자가 자동 시스템 백업 시작 불가) - [백업 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 참조 |
| [기본 제공 함수](/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [BULK INSERT 문](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 예, 하지만 Azure Blob Storage에서 원본으로만. | 예, 하지만 Azure Blob Storage에서 원본으로만 - [차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset) 참조 |
| [인증서 및 비대칭 키](/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 예, `BACKUP` 및 `CREATE` 작업을 위해 파일 시스템에 액세스할 수 없음. | 예, `BACKUP` 및 `CREATE` 작업을 위해 파일 시스템에 액세스할 수 없음 - [인증서 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#certificates) 참조 |
| [CDC(변경 데이터 캡처)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 예 | 예 |
| [데이터 정렬 - 서버/인스턴스](/sql/relational-databases/collations/set-or-change-the-server-collation) | 아니요, 기본 서버 데이터 정렬 `SQL_Latin1_General_CP1_CI_AS`가 항상 사용됨 | 예, [인스턴스를 만들](../managed-instance/create-template-quickstart.md) 때 설정할 수 있으며 나중에 업데이트할 수 없음 |
| [columnstore 인덱스](/sql/relational-databases/indexes/columnstore-indexes-overview) | 예 - [프리미엄 계층, 표준 계층 - S3 이상, 범용 계층, 중요 비즈니스용 및 하이퍼스케일 계층](/sql/relational-databases/indexes/columnstore-indexes-overview) |예 |
| [CLR(공용 언어 런타임)](/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 예 | 예, 하지만 `CREATE ASSEMBLY` 문에서 파일 시스템에 액세스할 수 없음 - [CLR 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#clr) 참조 |
| [자격 증명](/sql/relational-databases/security/authentication-access/credentials-database-engine) | 예, 하지만 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)만 | 예, 하지만 **Azure Key Vault** 와 `SHARED ACCESS SIGNATURE`만 지원됨 - [세부 정보](../managed-instance/transact-sql-tsql-differences-sql-server.md#credential) 참조 |
| [데이터베이스 간/세 부분으로 구성되는 이름 쿼리](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](elastic-query-overview.md) 참조 | 예. 추가적으로 [탄력적 쿼리](elastic-query-overview.md) |
| [데이터베이스 간 트랜잭션](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 예 | 예, 인스턴스 내. 인스턴스 간 쿼리는 [연결된 서버 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)을 참조하세요. |
| [데이터베이스 메일 - DbMail](/sql/relational-databases/database-mail/database-mail) | 예 | 예 |
| [데이터베이스 미러링](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 예 | [아니요](../managed-instance/transact-sql-tsql-differences-sql-server.md#database-mirroring) |
| [데이터베이스 스냅샷](/sql/relational-databases/databases/database-snapshots-sql-server) | 예 | 예 |
| [DBCC 문](/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 | 예 - [DBCC 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) 참조 |
| [DDL 문](/sql/t-sql/statements/statements) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [DDL 트리거](/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스에만 해당 |  예 |
| [분산된 파티션 뷰](/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 예 | 예 |
| [분산된 트랜잭션 - MS DTC](/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](elastic-transactions-overview.md) 참조 |  아니요 - [연결된 서버 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) 참조 마이그레이션 중 여러 분산 SQL Server 인스턴스의 데이터베이스를 하나의 SQL Managed Instance로 통합해봅니다. |
| [DML 트리거](/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 |  예 |
| [DMV](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 대부분 - 개별 DMV 참조 |  예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [탄력적 쿼리](elastic-query-overview.md)(공개 미리 보기) | 예, 필수 RDBMS 형식 사용 | 아니요 |
| [이벤트 알림](/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](alerts-insights-configure-portal.md) 참조 | 예 |
| [식](/sql/t-sql/language-elements/expressions-transact-sql) |예 | 예 |
| [확장 이벤트(XEvent)](/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](xevent-db-diff-from-svr.md) 참조 | 예 - [확장 이벤트 차이](../managed-instance/transact-sql-tsql-differences-sql-server.md#extended-events) 참조 |
| [확장된 저장 프로시저](/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 예 | 예 |
| [파일 및 파일 그룹](/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 | 예. 파일 경로는 자동으로 할당되며 `ALTER DATABASE ADD FILE` [문](../managed-instance/transact-sql-tsql-differences-sql-server.md#alter-database-statement)에 파일 위치를 지정할 수 없습니다.  |
| [FileStream](/sql/relational-databases/blob/filestream-sql-server) | 예 | [아니요](../managed-instance/transact-sql-tsql-differences-sql-server.md#filestream-and-filetable) |
| [FTS(전체 텍스트 검색)](/sql/relational-databases/search/full-text-search) |  예, 하지만 타사 단어 분리기는 지원되지 않음 | 예, [하지만 타사 단어 분리기는 지원되지 않음](../managed-instance/transact-sql-tsql-differences-sql-server.md#full-text-semantic-search) |
| [함수](/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [메모리 내 최적화](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [프리미엄 및 중요 비즈니스용 서비스 계층](../in-memory-oltp-overview.md)에서 예</br> [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)의 메모리 최적화 테이블 변수와 같은 비영구적 메모리 내 OLTP 개체에 대한 제한된 지원| [중요 비즈니스용 서비스 계층](../managed-instance/sql-managed-instance-paas-overview.md)에서 예 |
| [언어 요소](/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [연결된 서버](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](elastic-query-horizontal-partitioning.md) 참조 | 예. 분산 트랜잭션이 없는 [SQL Server 및 SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)에만 |
| 파일(CSV, Excel)에서 읽은 [연결된 서버](/sql/relational-databases/linked-servers/linked-servers-database-engine)| 아니요. CSV 형식 대신 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)를 사용합니다. | 아니요. CSV 형식 대신 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)를 사용합니다. [SQL Managed Instance 피드백 항목](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)에서 이러한 요청을 추적합니다.|
| [로그 전달](/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [고가용성](high-availability-sla.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. | [Azure DMS(Data Migration Service)](../../dms/tutorial-sql-server-to-managed-instance.md) 마이그레이션 프로세스의 일부로 기본 제공됩니다. 사용자 지정 데이터 마이그레이션 프로젝트에 사용하도록 외부 [LRS(Log Replay Service)](../managed-instance/log-replay-service-migrate.md)로 기본 제공됩니다.<br /> 고가용성 솔루션으로는 사용할 수 없습니다. 다른 [고가용성](high-availability-sla.md) 방법이 모든 데이터베이스에 포함되어 있으므로 로그 전달을 HA 대체로 사용하지 않는 것이 좋습니다. 재해 복구는 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. 데이터베이스 간 복제 메커니즘으로 사용할 수 없습니다. [중요 비즈니스용 계층](service-tier-business-critical.md), [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 또는 [트랜잭션 복제](../managed-instance/replication-transactional-overview.md)의 보조 복제본을 대안으로 사용합니다. |
| [로그인 및 사용자](/sql/relational-databases/security/authentication-access/principals-database-engine) | 예, 하지만 `CREATE` 및 `ALTER` 로그인 문이 모든 옵션을 제공하지는 않습니다(Windows 및 서버 수준 Azure Active Directory 로그인 없음). `EXECUTE AS LOGIN`은 지원되지 않습니다. `EXECUTE AS USER`를 대신 사용합니다.  | 예, 몇 가지 [차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)이 있음. Windows 로그인은 지원되지 않으며 Azure Active Directory 로그인으로 바꿔야 합니다. |
| [대량 가져오기에서 최소 로깅](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아니요, 전체 복구 모델만 지원됨. | 아니요, 전체 복구 모델만 지원됨. |
| [시스템 데이터 수정](/sql/relational-databases/databases/system-databases) | 예 | 예 |
| [OLE Automation](/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 예 | 예 |
| [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)|예|예, SQL Database, SQL Managed Instance 및 SQL Server에만 해당. [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조|
| [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql)|예|예, SQL Database, SQL Managed Instance 및 SQL Server에만 해당. [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조|
| [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)|예, Azure Blob Storage에서 가져오기에만 해당. |예, SQL Database, SQL Managed Instance 및 SQL Server와 Azure Blob Storage에서 가져오기에만 해당. [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조|
| [연산자](/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [Polybase](/sql/relational-databases/polybase/polybase-guide) | 아니요. `OPENROWSET` 함수를 사용하여 Azure Blob Storage에 있는 파일의 데이터를 쿼리하거나 [Synapse Analytics에서 서버리스 SQL 풀을 참조하는 외부 테이블](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)을 사용할 수 있습니다. | 아니요. `OPENROWSET` 함수, [Synapse Analytics에서 서버리스 SQL 풀을 참조하는 연결된 서버](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) 또는 SQL Server나 [Synapse Analytics에서 서버리스 SQL 풀](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)을 참조하는 외부 테이블(공개 미리 보기)을 사용하여 Azure Blob Storage에 있는 파일의 데이터를 쿼리할 수 있습니다. |
| [쿼리 알림](/sql/relational-databases/native-client/features/working-with-query-notifications) | 예 | 예 |
| [Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning)(_이전의 R Services_)| No | 예, [Azure SQL Managed Instance의 Machine Learning Services](../managed-instance/machine-learning-services-overview.md)를 참조하세요. |
| [복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server) | 고가용성을 보장하는 전체 복구만 지원됩니다. 단순 및 대량 로그 복구 모델을 사용할 수 없습니다. | 고가용성을 보장하는 전체 복구만 지원됩니다. 단순 및 대량 로그 복구 모델을 사용할 수 없습니다. |
| [리소스 관리자](/sql/relational-databases/resource-governor/resource-governor) | 예 | 예 |
| [RESTORE 문](/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 예 | 예, Azure Blob Storage에 배치된 백업 파일에 대한 필수 `FROM URL` 옵션 포함. [복원 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#restore-statement) 참조 |
| [백업에서 데이터베이스 복원](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 자동 백업에서만 - [SQL Database 복구](recovery-using-backups.md) 참조 | 자동 백업에서([SQL Database 복구](recovery-using-backups.md) 참조) 및 Azure Blob Storage에 배치된 전체 백업에서([백업 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 참조) |
| [SQL Server로 데이터베이스 복원](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 아니요. 네이티브 복원 대신 BACPAC 또는 BCP를 사용합니다. | 아니요. SQL Managed Instance에서 사용되는 SQL Server 데이터베이스 엔진의 버전이 온-프레미스에서 사용되는 SQL Server의 RTM 버전보다 높기 때문. 대신 BACPAC, BCP 또는 트랜잭션 복제를 사용합니다. |
| [의미 체계 검색](/sql/relational-databases/search/semantic-search-sql-server) | 예 | 예 |
| [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | 예 | 예, 하지만 인스턴스 내에서만. 원격 Service Broker 경로를 사용하는 경우 마이그레이션 중 여러 분산 SQL Server 인스턴스의 데이터베이스를 하나의 SQL Managed Instance로 통합하고 로컬 경로만 사용합니다. [Service Broker 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker) 참조 |
| [서버 구성 설정](/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 예 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [Set 문](/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조|
| [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업(미리 보기)](elastic-jobs-overview.md) 참조 | 예 - [SQL Server Agent 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) 참조 |
| [SQL Server 감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](auditing-overview.md) 참조 | 예 - [감사 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) 참조 |
| [시스템 저장 함수](/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 저장 프로시저](/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) 참조 |
| [시스템 테이블](/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [시스템 카탈로그 뷰](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 | 예 - [T-SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md) 참조 |
| [TempDB](/sql/relational-databases/databases/tempdb-database) | 예. [모든 데이터베이스에 대해 코어당 32GB 크기](resource-limits-vcore-single-databases.md) | 예. [전체 GP 계층에 대해 vCore당 24GB 크기 및 BC 계층의 인스턴스 크기로 제한됨](../managed-instance/resource-limits.md#service-tier-characteristics)  |
| [임시 테이블](/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 로컬 및 데이터베이스 범위 전역 임시 테이블 | 로컬 및 인스턴스 범위 전역 임시 테이블 |
| 표준 시간대 선택 | 예 | [예](../managed-instance/timezones-overview.md), SQL Managed Instance를 만들 때 구성해야 함. |
| [추적 플래그](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 예 | 예, 하지만 제한된 전체 추적 플래그 세트만. [DBCC 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) 참조 |
| [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) | 예, [트랜잭션 및 스냅샷 복제 구독자만 해당](migrate-to-database-from-sql-server.md) | 예, [공개 미리 보기](/sql/relational-databases/replication/replication-with-sql-database-managed-instance)에서. [여기](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)에서 제약 조건 참조 |
| [TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 - 범용 및 중요 비즈니스 서비스 계층만 해당| [예](transparent-data-encryption-tde-overview.md) |
| Windows 인증 | 예 | 예 |
| [Windows Server 장애 조치(failover) 클러스터링](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 아니요. [고가용성](high-availability-sla.md)을 제공하는 다른 기술은 모든 데이터베이스에 포함되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. | 아니요. [고가용성](high-availability-sla.md)을 제공하는 다른 기술은 모든 데이터베이스에 포함되어 있습니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 설명합니다. |

## <a name="platform-capabilities"></a>플랫폼 기능

Azure 플랫폼은 표준 데이터베이스 기능에 추가 값으로 추가되는 다양한 PaaS 기능을 제공합니다. Azure SQL Database와 함께 사용할 수 있는 여러 외부 서비스가 있습니다.

| **플랫폼 기능** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [활성 지역 복제](active-geo-replication-overview.md) | 예 - 하이퍼스케일 이외의 모든 서비스 계층 | 아니요, 대안으로 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 참조 |
| [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) | 예 - 하이퍼스케일 이외의 모든 서비스 계층 | 예, [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 참조|
| 자동 크기 조정 | 예, 하지만 [서버리스 모델](serverless-tier-overview.md)에서만. 비 서버리스 모델에서는 서비스 계층 변경(vCore, 스토리지 또는 DTU의 변경)이 빠르고 온라인에서 이루어집니다. 서비스 계층 변경에는 최소의 가동 중지 시간이 필요하거나 가동 중지 시간이 필요하지 않습니다. | 아니요, 예약된 컴퓨팅과 스토리지를 선택해야 함. 서비스 계층(vCore 또는 최대 스토리지) 변경이 온라인에서 이루어지며 최소의 가동 중지 시간이 필요하거나 가동 중지 시간이 필요하지 않습니다. |
| [자동 백업](automated-backups-overview.md) | 예. 전체 백업은 7일, 차등 백업은 12시간, 로그 백업은 5~10분 간격으로 수행됩니다. | 예. 전체 백업은 7일, 차등 백업은 12시간, 로그 백업은 5~10분 간격으로 수행됩니다. |
| [자동 조정(인덱스)](/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](automatic-tuning-overview.md)| 예 |
| [가용성 영역](../../availability-zones/az-overview.md) | 예 | 예 |
| [Azure Resource Health](../../service-health/resource-health-overview.md) | 예 | 예 |
| 백업 보존 | 예. 기본 7일, 최대 35일. | 예. 기본 7일, 최대 35일. |
| [DMS(데이터 마이그레이션 서비스)](/sql/dma/dma-overview) | 예 | 예 |
| [탄력적 작업](elastic-jobs-overview.md) | 예 - [탄력적 작업(미리 보기)](elastic-jobs-overview.md) 참조 | 아니요(대신 [SQL 에이전트](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)를 사용할 수 있음). |
| 파일 시스템 액세스 | 아니요. 대안으로 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)를 사용하여 Azure Blob Storage의 데이터를 액세스하고 로드합니다. | 아니요. 대안으로 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 또는 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)를 사용하여 Azure Blob Storage의 데이터를 액세스하고 로드합니다. |
| [지역 복원](recovery-using-backups.md#geo-restore) | 예 | 예 |
| [하이퍼스케일 아키텍처](service-tier-hyperscale.md) | 예 | 예 |
| [LTR(장기 백업 보존)](long-term-retention-overview.md) | 예, 자동으로 가져온 백업을 최대 10년까지 보관합니다. | 예, 자동으로 가져온 백업을 최대 10년까지 보관합니다. |
| 일시 중지/다시 시작 | 예, [서버리스 모델](serverless-tier-overview.md)에서 | 예 |
| [정책 기반 관리](/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 예 | 예 |
| 공용 IP 주소 | 예. 방화벽 또는 서비스 엔드포인트를 사용하여 액세스를 제한할 수 있습니다.  | 예. 명시적으로 사용하도록 설정해야 하며, NSG 규칙에서 포트 3342를 사용하도록 설정해야 합니다. 필요한 경우 공용 IP를 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [공용 엔드포인트](../managed-instance/public-endpoint-overview.md)를 참조하세요. |
| [지정 시간 데이터베이스 복원](/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예 - 하이퍼스케일 이외의 모든 서비스 계층 - [SQL Database 복구](recovery-using-backups.md#point-in-time-restore) 참조 | 예 - [SQL Database 복구](recovery-using-backups.md#point-in-time-restore) 참조 |
| 리소스 풀 | 예, [탄력적 풀](elastic-pool-overview.md)로 | 예. SQL Managed Instance의 단일 인스턴스에 동일한 리소스 풀을 공유하는 여러 데이터베이스가 있을 수 있습니다. 또한 리소스를 공유할 수 있는 [인스턴스 풀(미리 보기)](../managed-instance/instance-pools-overview.md)에 SQL Managed Instance의 여러 인스턴스를 배포할 수 있습니다. |
| 확장 또는 축소(온라인) | 예, 최소 가동 중지 시간으로 DTU, 예약 vCore 또는 최대 스토리지를 변경할 수 있습니다. | 예, 최소 가동 중지 시간으로 예약 vCore 또는 최대 스토리지를 변경할 수 있습니다. |
| [SQL 별칭](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 아니요, [DNS 별칭](dns-alias-overview.md) 사용 | 아니요, [Clicongf](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022)를 사용하여 클라이언트 머신에서 별칭을 설정합니다. |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md) | 예 | 예 |
| [SQL 데이터 동기화](sql-data-sync-sql-server-configure.md) | 예 | 예 |
| [SSAS(SQL Server Analysis Services)](/sql/analysis-services/analysis-services) | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)는 별도의 Azure 클라우드 서비스입니다. | 아니요, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)는 별도의 Azure 클라우드 서비스입니다. |
| [SSIS(SQL Server Integration Services)](/sql/integration-services/sql-server-integration-services) | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Azure SQL Database에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](../../data-factory/create-azure-ssis-integration-runtime.md)를 참조하세요. <br/><br/>SQL Database와 SQL Managed Instance의 SSIS 기능을 비교하려면 [SQL Database와 SQL Managed Instance 비교](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요. | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용. 여기서 패키지는 SQL Managed Instance에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됨. [ADF에서 Azure-SSIS IR 만들기](../../data-factory/create-azure-ssis-integration-runtime.md) 참조 <br/><br/>SQL Database와 SQL Managed Instance의 SSIS 기능을 비교하려면 [SQL Database와 SQL Managed Instance 비교](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요. |
| [SSRS(SQL Server Reporting Services)](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니요 - [Power BI 참조](/power-bi/) | 아니요 - Azure VM에서 SSRS를 호스트하는 대신, [Power BI의 페이지 매김 보고서](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)를 사용합니다. SQL Managed Instance는 SSRS를 서비스로 실행할 수는 없지만 SQL Server 인증을 사용하여 Azure Virtual Machine에 설치된 보고 서버용 [SSRS 카탈로그 데이터베이스](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)를 호스트할 수 있습니다. |
| [QPI(쿼리 성능 Insight)](query-performance-insight-use.md) | 예 | 아니요. SQL Server Management Studio와 Azure Data Studio의 기본 제공 보고서 사용. |
| [VNet](../../virtual-network/virtual-networks-overview.md) | 부분적, [VNet 엔드포인트](vnet-service-endpoint-rule-overview.md)를 사용하여 제한된 액세스 사용. | 예, SQL Managed Instance는 고객의 VNet에 삽입됨. [서브넷](../managed-instance/transact-sql-tsql-differences-sql-server.md#subnet) 및 [VNet](../managed-instance/transact-sql-tsql-differences-sql-server.md#vnet) 참조 |
| VNet 서비스 엔드포인트 | [예](vnet-service-endpoint-rule-overview.md) | 예 |
| VNet 글로벌 피어링 | 예, [개인 IP 및 서비스 엔드포인트](vnet-service-endpoint-rule-overview.md) 사용 | 예, [가상 네트워크 피어링](https://techcommunity.microsoft.com/t5/azure-sql/new-feature-global-vnet-peering-support-for-azure-sql-managed/ba-p/1746913) 사용. |
| [프라이빗 연결](../../private-link/private-link-overview.md) | 예. [프라이빗 링크](../../private-link/private-endpoint-overview.md)를 사용합니다. | 예. VNet을 사용합니다. | 

## <a name="tools"></a>도구

Azure SQL Database와 Azure SQL Managed Instance는 데이터 관리를 위한 다양한 데이터 도구를 지원합니다.

| **도구** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| Azure portal | 예 | 예 |
| Azure CLI | 예 | 예|
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 예 | 예 |
| Azure PowerShell | 예 | 예 |
| [BACPAC 파일(내보내기)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](database-export.md) 참조 | 예 - [SQL Managed Instance 내보내기](database-export.md) 참조 |
| [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](database-import.md) 참조 | 예 - [SQL Managed Instance 가져오기](database-import.md) 참조 |
| [DQS(Data Quality Services)](/sql/data-quality-services/data-quality-services) | 예 | 예 |
| [MDS(Master Data Services)](/sql/master-data-services/master-data-services-overview-mds) | 예 | 예 |
| [SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [예](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 예 [버전 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SSDT(SQL Server Data Tools)](/sql/ssdt/download-sql-server-data-tools-ssdt) | 예 | 예 |
| [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) | 예 | 예 [버전 18.0 이상](/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | 예 | 예 |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](xevent-db-diff-from-svr.md) 참조 | 예 |
| [System Center Operations Manager](/system-center/scom/welcome) | [예](https://www.microsoft.com/download/details.aspx?id=38829) | [예](https://www.microsoft.com/en-us/download/details.aspx?id=101203) |

## <a name="migration-methods"></a>마이그레이션 방법

서로 다른 마이그레이션 방법을 사용하여 SQL Server, Azure SQL Database 및 Azure SQL Managed Instance 간에 데이터를 이동할 수 있습니다. 일부 메서드는 **온라인** 이며 마이그레이션을 실행하는 동안 원본의 모든 변경 내용을 선택하는 반면, **오프라인** 메서드에서는 마이그레이션이 진행되는 동안 원본의 데이터를 수정하는 워크로드를 중지해야 합니다.

| **원본** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| SQL Server(온-프레미스, AzureVM, Amazon RDS) | **온라인:**  [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프라인:** [DMS(Data Migration Service)](/sql/dma/dma-overview), [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **온라인:** [DMS(Data Migration Service)](/sql/dma/dma-overview), [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프라인:** 네이티브 백업/복원, [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅샷 복제](../managed-instance/replication-transactional-overview.md) |
| 단일 데이터베이스 | **오프라인:** [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP | **오프라인:** [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP |
| SQL Managed Instance | **온라인:** [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프라인:** [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅샷 복제](../managed-instance/replication-transactional-overview.md) | **온라인:** [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) <br/> **오프라인:** 인스턴스 간 특정 시점 복원([Azure PowerShell](/powershell/module/az.sql/restore-azsqlinstancedatabase#examples) 또는 [Azure CLI](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208)), [네이티브 백업/복원](../managed-instance/restore-sample-database-quickstart.md), [BACPAC 파일(가져오기)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database), BCP, [스냅샷 복제](../managed-instance/replication-transactional-overview.md) |

## <a name="next-steps"></a>다음 단계

Microsoft는 Azure SQL Database에 계속해서 기능을 추가하고 있습니다. Azure용 서비스 업데이트 웹 페이지에서 다음 필터를 사용하여 최신 업데이트를 확인하세요.

- [Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)로 필터링됨.
- SQL Database 기능에 대한 [GA\(일반 공급\) 알림](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)으로 필터링됨.

Azure SQL Database 및 Azure SQL Managed Instance에 대한 자세한 내용은 다음을 참조하세요.

- [Azure SQL Database란?](sql-database-paas-overview.md)
- [Azure SQL Managed Instance란?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Azure SQL Managed Instance 풀이란?](../managed-instance/instance-pools-overview.md)
