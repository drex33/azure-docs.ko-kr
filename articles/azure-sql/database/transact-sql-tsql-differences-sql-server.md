---
title: T-SQL 차이점 마이그레이션 해결
description: T-SQL 문은 Azure SQL Database에서 완전히 지원되지는 않습니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004595"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>SQL Server와 Azure SQL Database 간의 T-SQL 차이

SQL Server에서 Azure SQL Database로 [데이터베이스를 마이그레이션하는 경우](migrate-to-database-from-sql-server.md), SQL Server Database를 마이그레이션하려면 다시 엔지니어링해야 할 수 있습니다. 이 문서에서는 엔지니어링을 다시 수행하는 데 도움이 되며 다시 엔지니어링해야 하는 근본적인 이유를 이해하는 데에도 도움이 되는 지침을 제공합니다. 비 호환성을 검색하고 데이터베이스를 Azure SQL Database로 마이그레이션하려면 [Data Migration Assistant(DMA)](/sql/dma/dma-overview)를 사용합니다.

## <a name="overview"></a>개요

애플리케이션에서 사용하는 대부분의 T-SQL 기능은 Microsoft SQL Server와 Azure SQL Database 모두에서 완전히 지원됩니다. 예를 들어 데이터 형식, 연산자, 문자열, 산술, 논리 및 커서 함수 같은 핵심 SQL 구성 요소는 SQL Server 및 SQL Database에서 동일하게 작동합니다. 그러나 데이터 정의 언어(DDL) 및 데이터 조작 언어(DML) 요소에는 몇 가지 T-SQL 차이점이 있으며 그 결과 T-SQL 문 및 쿼리가 부분적으로만 지원됩니다(이 문서의 뒷부분에서 설명).

또한 Azure SQL Database는 시스템 데이터베이스 및 운영 체제에 대한 종속성으로부터 기능을 격리하도록 설계되어 전혀 지원되지 않는 기능과 구문도 있습니다. 따라서 대부분의 인스턴스 수준 기능은 SQL Database에서 지원되지 않습니다. 인스턴스 수준 옵션 또는 운영 체제 구성 요소를 구성하거나 파일 시스템 구성을 지정하는 경우, T-SQL 문 및 옵션은 사용할 수 없습니다. 이러한 기능이 필요한 경우 SQL Database 또는 다른 Azure 기능이나 서비스에서 다른 방법으로 사용할 수 있는 적합한 대안이 있는 경우가 많습니다.

예를 들어, 고가용성은 Azure SQL Database에 기본 제공됩니다. 가용성 그룹과 관련된 T-SQL 문은 SQL Database에서 지원되지 않으며 Always On 가용성 그룹에 관련된 동적 관리 뷰도 지원되지 않습니다.

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은 [Azure SQL Database 기능 비교](features-comparison.md)를 참조하세요. 이 페이지에서는 해당 문서를 보완하고 T-SQL 문에 집중합니다.

## <a name="t-sql-syntax-statements-with-partial-differences"></a>부분적으로 차이점이 있는 T-SQL 구문

핵심 DDL 문은 사용할 수 있지만 디스크의 파일 배치와 같이 지원되지 않는 기능과 관련된 DDL 문 확장은 지원되지 않습니다.

- SQL Server의 `CREATE DATABASE` 및 `ALTER DATABASE` 문에는 36개가 넘는 옵션이 있습니다. 이러한 문에는 SQL Server에만 적용되는 파일 배치, FILESTREAM 및 Service Broker 옵션이 포함되어 있습니다. 마이그레이션하기 전에 SQL Database에 데이터베이스를 생성하는 경우에는 문제가 되지 않지만 데이터베이스를 생성하는 T-SQL 코드를 마이그레이션하는 경우, [CREATE DATABASE(Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)를 [CREATE DATABASE(SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true)의 SQL Server 구문과 비교하여 사용하는 모든 옵션이 지원되는지 확인해야 합니다. Azure SQL Database의 `CREATE DATABASE`에는 SQL Database에만 적용되는 서비스 목표와 탄력적 풀 옵션도 포함되어 있습니다.
- `CREATE TABLE` 및 `ALTER TABLE` 문에는 이러한 기능이 지원되지 않으므로, SQL Database에서 사용할 수 없는 `FILETABLE` 및 `FILESTREAM` 옵션이 포함되어 있습니다.
- `CREATE LOGIN` 및 `ALTER LOGIN` 문은 지원되지만, SQL Server에서 사용할 수 있는 옵션은 제공하지 않습니다. 데이터베이스 이식 가능성을 높이려면 SQL Database에서 가능한 경우 로그인 대신 포함된 데이터베이스 사용자를 사용하도록 권장합니다. 자세한 내용은 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true), [ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true), [로그인 및 사용자 관리](logins-create-manage.md)를 참조하세요.

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL Database에서 지원되지 않는 T-SQL 구문

[Azure SQL Database 기능 비교](features-comparison.md)에서 설명한 지원되지 않는 기능과 관련된 T-SQL 문 외에도 다음 문 및 문 그룹이 지원되지 않습니다. 따라서 마이그레이션할 데이터베이스에서 다음 기능을 사용하고 있는 경우 애플리케이션을 다시 엔지니어링하여 해당 T-SQL 기능 및 문을 제거합니다.

- 시스템 개체의 데이터 정렬.
- 연결 관련: 엔드포인트 문. SQL Database는 Windows 인증을 지원하지 않으며, Azure Active Directory 인증을 지원합니다. 여기에는 Azure Active Directory와 페더레이션된 Active Directory 보안 주체의 인증이 포함됩니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database 또는 Azure Synapse Analytics에 연결](authentication-aad-overview.md)을 참조하세요.
- 세 개 또는 네 부분으로 이루어진 이름을 사용하는 데이터베이스 간 및 인스턴스 간 쿼리. `tempdb` 데이터베이스 및 현재 데이터베이스를 참조하는 세 부분으로 구성된 이름이 지원됩니다. [탄력적 쿼리](elastic-query-overview.md)는 다른 MSSQL 데이터베이스의 테이블에 대한 읽기 전용 참조를 지원합니다.
- 데이터베이스 간 소유권 체인 및 `TRUSTWORTHY` 데이터베이스 속성.
- `EXECUTE AS LOGIN`. 대신 `EXECUTE AS USER`를 사용하세요.
- 암호화 키에 대한 확장 가능 키 관리(EKM). 투명한 데이터 암호화(TDE) [고객 관리형 키](transparent-data-encryption-byok-overview.md)와 Always Encrypted [열 마스터 키](always-encrypted-azure-key-vault-configure.md)를 Azure Key Vault에 저장할 수 있습니다.
- 이벤트: 이벤트 알림, 쿼리 알림.
- 파일 속성: SQL Database에서 자동으로 관리하는 데이터베이스 파일 이름, 배치, 크기, 기타 파일 속성과 관련된 구문입니다.
- 고가용성: SQL Database에 의해 관리되는 고가용성 및 데이터베이스 복구와 관련된 구문입니다. 백업, 복원, Always On, 데이터베이스 미러링, 로그 전달, 복구 모드에 대한 구문을 포함합니다.
- 스냅숏, 트랜잭션, 병합 복제와 관련된 구문으로 SQL Database에서 사용할 수 없습니다. [복제 구독](replication-to-sql-database.md)을 지원합니다.
- 함수: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`.
- 인스턴스 구성: 서버 메모리, 작업자 스레드 수, CPU 선호도, 추적 플래그와 관련된 구문. 그 대신 서비스 계층 및 컴퓨팅 크기를 사용합니다.
- `KILL STATS JOB`.
- `OPENQUERY`, `OPENDATASOURCE`, 네 부분으로 된 이름.
- .NET Framework: CLR 통합
- 의미 체계 검색
- 서버 자격 증명: 대신 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL)을 사용합니다.
- 서버 수준 사용 권한: 서버 수준 사용 권한의 `GRANT`, `REVOKE`, `DENY`은 지원되지 않습니다. 일부 서버 수준 사용 권한은 데이터베이스 수준 사용 권한으로 바꾸거나 기본 제공 서버 역할로 암시적으로 부여됩니다. 일부 서버 수준 DMV 및 카탈로그 뷰에는 비슷한 데이터베이스 수준 뷰가 있습니다.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`와 `RECONFIGURE`을 참조하세요. [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 지원합니다.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 에이전트: SQL Server 에이전트 또는 MSDB 데이터베이스를 사용하는 구문: 경고, 운영자, 중앙 관리 서버입니다. 대신 PowerShell과 같은 스크립팅을 사용합니다.
- SQL Server 감사: 대신 SQL Database [감사](auditing-overview.md)를 사용합니다.
- SQL Server 추적.
- 추적 플래그.
- T-SQL 디버깅.
- 서버 범위 또는 로그온 트리거.
- `USE` 문: 데이터베이스 컨텍스트를 다른 데이터베이스로 변경하려면 새 데이터베이스에 대한 새 연결을 생성해야 합니다.

## <a name="full-t-sql-reference"></a>전체 T-SQL 참조

T-SQL 문법, 사용법, 예제에 대한 자세한 내용은 [T-SQL 참조(데이터베이스 엔진)](/sql/t-sql/language-reference)를 참조하세요.

### <a name="about-the-applies-to-tags"></a>'적용 대상' 태그 정보

T SQL 참조에는 모든 최신 SQL Server 버전과 관련된 문서가 포함되어 있습니다. 문서 제목 아래에 있는 아이콘 모음에는 MSSQL 플랫폼 목록과 적용 가능 여부가 나타납니다. 예를 들어 가용성 그룹은 SQL Server 2012에서 도입되었습니다. [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) 문서에 따라 이 문은 **SQL Server(2012부터)** 에 적용됩니다. SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Synapse Analytics 또는 병렬 데이터 웨어하우스에는 이 문이 적용되지 않습니다.

경우에 따라 문서의 일반 제목이 제품에 사용될 수 있지만 제품 간에 사소한 차이가 있습니다. 차이점은 문서의 중간에 적절하게 표시됩니다. 예를 들어, `CREATE TRIGGER` 문서는 SQL Database에서 사용할 수 있습니다. 그러나 서버 수준 트리거에 대한 `ALL SERVER` 옵션은 SQL Database에서 서버 수준 트리거를 사용할 수 없음을 나타냅니다. 대신 데이터베이스 수준 트리거를 사용합니다.

## <a name="next-steps"></a>다음 단계

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은 [Azure SQL Database 기능 비교](features-comparison.md)를 참조하세요.

Azure SQL Database으로 마이그레이션하기 전에 SQL Server 데이터베이스에서 호환성 문제를 감지하고 데이터베이스를 마이그레이션하려면, [Data Migration Assistant(DMA)](/sql/dma/dma-overview)를 사용합니다.