---
title: SQL Server와 Azure SQL Managed Instance 간의 T-SQL 차이점
description: 이 문서에서는 Azure SQL Managed Instance와 SQL Server 간의 T-SQL(Transact-SQL) 차이점을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: reference
author: danimir
ms.author: danil
ms.reviewer: mathoma, bonova, danil
ms.date: 10/21/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 0bc0bf771d8a50acfe09ab4b4abdded4dc078c49
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707664"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server와 Azure SQL Managed Instance 간의 T-SQL 차이점
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance와 SQL Server 간의 구문과 동작의 차이점을 요약하고 설명합니다. 


SQL Managed Instance는 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공하며 대부분의 기능은 SQL Managed Instance에서 지원됩니다.

![SQL Server에서 쉽게 마이그레이션](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL Managed Instance에 도입된 일부 PaaS 제한 사항이 있으며 SQL Server에 비해 일부 동작이 변경되었습니다. 차이점은 다음 범주로 구분됩니다. <a name="Differences"></a>

- [가용성](#availability)에는 [Always On 가용성 그룹](#always-on-availability-groups) 및 [백업](#backup)의 차이점이 포함됩니다.
- [감사](#auditing), [인증서](#certificates), [자격 증명](#credential), [암호화 공급자](#cryptographic-providers), [로그인 및 사용자](#logins-and-users), [서비스 키 및 서비스 마스터 키](#service-key-and-service-master-key)의 차이점을 비롯한 [보안](#security).
- [버퍼 풀 확장](#buffer-pool-extension), [데이터 정렬](#collation), [호환성 수준](#compatibility-levels), [데이터베이스 미러링](#database-mirroring), [데이터베이스 옵션](#database-options), [SQL Server 에이전트](#sql-server-agent), [테이블 옵션](#tables)의 차이점을 비롯한 [구성](#configuration).
- [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [분산 트랜잭션](#distributed-transactions), [확장 이벤트](#extended-events), [외부 라이브러리](#external-libraries), [파일 스트림 및 파일 테이블](#filestream-and-filetable), [전체 텍스트 의미 체계 검색](#full-text-semantic-search), [연결된 서버](#linked-servers), [PolyBase](#polybase), [복제](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [저장 프로시저, 함수 및 트리거](#stored-procedures-functions-and-triggers)를 비롯한 [기능](#functionalities).
- VNet 및 서브넷 구성과 같은 [환경 설정](#Environment).

이러한 기능 중 대부분은 아키텍처 제약 조건이며 서비스 기능을 나타냅니다.

SQL Managed Instance에서 검색 되 고 나중에 해결 될 수 있는 일시적인 알려진 문제는 [새로운 기능](doc-changes-updates-release-notes-whats-new.md)에 설명 되어 있습니다.

## <a name="availability"></a>가용성

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 가용성 그룹

[고가용성](../database/high-availability-sla.md)은 SQL Managed Instance에 기본적으로 제공되며 사용자가 제어할 수 없습니다. 다음 문은 지원되지 않습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="backup"></a>Backup

SQL Managed Instance에는 자동 백업이 있으므로 사용자는 전체 데이터베이스 `COPY_ONLY` 백업을 만들 수 있습니다. 차등, 로그 및 파일 스냅샷 백업은 지원되지 않습니다.

- SQL Managed Instance를 사용하는 경우 Azure Blob Storage 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE`, `TAPE` 및 백업 디바이스는 지원되지 않습니다.
- 대부분의 일반 `WITH` 옵션이 지원됩니다.
  - `COPY_ONLY`는 필수입니다.
  - `FILE_SNAPSHOT`는 지원되지 않습니다.
  - 테이프 옵션: `REWIND`, `NOREWIND`, `UNLOAD` 및 `NOUNLOAD`는 지원되지 않습니다.
  - 로그 관련 옵션: `NORECOVERY`, `STANDBY` 및 `NO_TRUNCATE`는 지원되지 않습니다.

제한 사항: 

- SQL Managed Instance를 사용하는 경우 최대 32개의 스트라이프가 있는 백업에 인스턴스 데이터베이스를 백업할 수 있습니다. 이 개수는 백업 압축을 사용할 경우 최대 4TB의 데이터베이스에 충분합니다.
- 서비스 관리 TDE(투명한 데이터 암호화)로 암호화된 데이터베이스에서는 `BACKUP DATABASE ... WITH COPY_ONLY`를 실행할 수 없습니다. 서비스 관리 TDE는 내부 TDE 키를 사용하여 백업을 암호화합니다. 키를 내보낼 수 없어 백업을 복원할 수 없습니다. 자동 백업 및 특정 시점 복원을 사용하거나 [고객 관리(BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key)를 대신 사용합니다. 데이터베이스에서 암호화를 사용하지 않도록 설정할 수 있습니다.
- Managed Instance에서 가져온 기본 백업은 SQL Server로 복원할 수 없습니다. 이는 Managed Instance가 SQL Server의 모든 버전과 비교하여 내부 데이터베이스 버전이 더 높기 때문입니다.
- Azure storage에서 데이터베이스를 백업 하거나 복원 하려면 SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한을 부여 하는 URI를 만들어야 합니다. [이에 대해 자세히 알아보세요](restore-sample-database-quickstart.md#restore-from-a-backup-file-using-t-sql). 이러한 시나리오에는 액세스 키를 사용 하는 것은 지원 되지 않습니다.
- SQL Managed Instance에서 `BACKUP` 명령을 사용한 최대 백업 스트라이프 크기는 최대 BLOB 크기인 195GB입니다. 개별 스트라이프 크기를 줄이고 이 제한 내로 유지하려면 백업 명령에서 스트라이프 수를 늘립니다.

    > [!TIP]
    > 이 제한을 해결하기 위해 온-프레미스 환경 또는 가상 머신의 SQL Server에서 데이터베이스를 백업하는 경우 다음 작업을 수행할 수 있습니다.
    >
    > - `URL`을 백업하는 대신 `DISK`에 백업합니다.
    > - BLOB Storage에 백업 파일을 업로드합니다.
    > - SQL Managed Instance로 복원합니다.
    >
    > 업로드된 백업 파일의 스토리지에 서로 다른 BLOB 유형이 사용되기 때문에 SQL Managed Instance의 `Restore` 명령은 백업 파일에서 더 큰 BLOB 크기를 지원합니다.

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Microsoft Azure SQL과 SQL Server 감사의 주요 차이점은 다음과 같습니다.

- SQL Managed Instance를 사용하면 감사는 서버 수준에서 작동합니다. `.xel` 로그 파일은 Azure BLOB Storage에 저장됩니다.
- Azure SQL Database를 사용하면 감사는 데이터베이스 수준에서 작동합니다. `.xel` 로그 파일은 Azure BLOB Storage에 저장됩니다.
- 온-프레미스 또는 가상 머신의 SQL Server를 사용하면 감사는 서버 수준에서 작동합니다. 이벤트는 파일 시스템 또는 Windows 이벤트 로그에 저장됩니다.
 
SQL Managed Instance의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 지원되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치되는 Azure Blob Storage 컨테이너의 URL을 지정하는 데 사용할 수 있습니다.
- SQL Managed Instance에서 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 지원되지 않습니다.

자세한 내용은 다음을 참조하세요. 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

SQL Managed Instance에서 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다.

- `CREATE FROM`/`BACKUP TO` 파일은 인증서에서 지원되지 않습니다.
- `FILE`/`ASSEMBLY`의 `CREATE`/`BACKUP` 인증서는 지원되지 않습니다. 프라이빗 키 파일은 사용할 수 없습니다. 

[CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요. 
 
**해결 방법**: 인증서의 백업을 만들고 백업을 복원하는 대신 [인증서 이진 콘텐츠 및 프라이빗 키를 가져와서 .sql 파일로 저장하고 이진에서 만듭니다](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database).

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

SQL Managed Instance는 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins-and-users"></a>로그인 및 사용자

- `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` 및 `FROM SID`를 사용하여 만든 SQL 로그인이 지원됩니다. [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 구문 또는 [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 구문을 사용하여 만든 Azure AD(Azure Active Directory) 서버 보안 주체(로그인)가 지원됩니다. 이러한 로그인은 서버 수준에서 만들어집니다.

    SQL Managed Instance는 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 구문을 사용하여 Azure AD 데이터베이스 보안 주체를 지원합니다. 이 기능을 Azure AD 포함된 데이터베이스 사용자라고도 합니다.

- `CREATE LOGIN ... FROM WINDOWS` 구문으로 만든 Windows 로그인은 지원되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- 인스턴스에 대 한 Azure AD 관리자에는 [무제한 관리자 권한이](../database/logins-create-manage.md)있습니다.
- 관리자가 아닌 Azure AD 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용하여 만들 수 있습니다. [CREATE USER ... FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조하세요.
- Azure AD 서버 보안 주체(로그인)는 단일 SQL Managed Instance 내에서만 SQL 기능을 지원합니다. 인스턴스 간 상호 작용이 필요한 기능은 동일한 Azure AD 테넌트에 있는지 또는 다른 테넌트 내에 있는지에 관계없이 Azure AD 사용자에게 지원되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제
  - 링크 서버

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다. Azure AD 그룹의 멤버는 데이터베이스에 로그인이 만들어지지 않은 경우에도 데이터베이스 소유자가 될 수 있습니다.
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 절과 같이, 다른 Azure AD 보안 주체를 사용하여 Azure AD 서버 수준 보안 주체를 가장할 수 있습니다. EXECUTE AS 제한 사항은 다음과 같습니다.

  - 이름이 로그인 이름과 다른 경우 EXECUTE AS USER는 Azure AD 사용자에 대해 지원되지 않습니다. 예를 들어 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com]을 통해 사용자를 만들고 EXEC AS USER = _myAadUser_ 를 통해 가장을 시도하는 경우입니다. Azure AD 서버 보안 주체(로그인)에서 **USER** 를 만들 때 **LOGIN** 에서 user_name을 동일한 login_name으로 지정합니다.
  - `sysadmin` 역할에 포함된 SQL 서버 수준 보안 주체(로그인)만이 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - EXECUTE AS 문으로 사용자를 가장하려면 사용자를 Azure AD 서버 보안 주체(로그인)에 직접 매핑해야 합니다. 호출자에게 지정된 사용자 이름에 대한 가장 권한이 있더라도 Azure AD 서버 보안 주체에 매핑된 Azure AD 그룹의 구성원인 사용자는 EXECUTE AS 문으로 효과적으로 가장할 수 없습니다.

- bacpac 파일을 사용하는 데이터베이스 내보내기/가져오기는 [SSMS V18.4 이상 버전](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [SQLPackage.exe](/sql/tools/sqlpackage-download)를 사용하여 SQL Managed Instance의 Azure AD 사용자에게 지원됩니다.
  - 데이터베이스 bacpac 파일을 사용하여 다음 구성이 지원됩니다. 
    - 동일한 Azure AD 도메인 내의 서로 다른 관리 인스턴스 간에 데이터베이스 내보내기/가져오기
    - SQL Managed Instance에서 데이터베이스 내보내기 및 동일한 Azure AD 도메인 내의 SQL Database로 가져오기 
    - SQL Database에서 데이터베이스 내보내기 및 동일한 Azure AD 도메인 내에서 SQL Managed Instance로 가져오기
    - SQL Managed Instance에서 데이터베이스 내보내기 및 SQL Server(버전 2012 이상)로 가져오기
      - 이 구성에서는 모든 Azure AD 사용자가 로그인 없이 SQL Server 데이터베이스 보안 주체(사용자)로 만들어집니다. 사용자 유형은 `SQL`로 나열되며 sys.database_principals에서 `SQL_USER`로 표시됩니다. 해당 사용 권한과 역할은 SQL Server 데이터베이스 메타데이터에 남아 있으며 가장에 사용할 수 있습니다. 그러나 해당 자격 증명을 사용하여 SQL Server에 액세스하고 로그인하는 데는 사용할 수 없습니다.

- 서버 수준 보안 주체 로그인(SQL Managed Instance 프로비전 프로세스를 통해 생성됨), 서버 역할의 구성원(예: `securityadmin` 또는 `sysadmin`) 또는 서버 수준에서 ALTER ANY LOGIN 권한이 있는 기타 로그인만 SQL Managed Instance에 대한 Azure AD 서버 보안 주체(로그인)를 master 데이터베이스에 만들 수 있습니다.
- 로그인이 SQL 보안 주체인 경우 `sysadmin` 역할에 포함된 로그인만 create 명령을 사용하여 Azure AD 계정에 대한 로그인을 만들 수 있습니다.
- Azure AD 로그인은 Azure SQL Managed Instance에 사용되는 것과 동일한 디렉터리 내의 Azure AD 구성원이어야 합니다.
- Azure AD 서버 보안 주체(로그인)는 SQL Server Management Studio 18.0 미리 보기 5부터 개체 탐색기에 표시됩니다.
- *sysadmin* 액세스 수준이 있는 서버 보안 주체는 인스턴스에서 사용하도록 설정되면 Azure AD 관리자 계정에 대해 자동으로 만들어집니다.
- 인증 중에 인증 보안 주체를 확인하기 위해 적용되는 시퀀스는 다음과 같습니다.

    1. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 경우(sys.server_principals에서 "E" 형식으로), 액세스 권한을 부여하고 Azure AD 서버 보안 주체(로그인)의 사용 권한을 적용합니다.
    1. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 Azure AD 그룹의 멤버인 경우(sys.server_principals에서 "X" 형식으로), 액세스 권한을 부여하고 Azure AD 그룹 로그인의 사용 권한을 적용합니다.
    1. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 직접 매핑된 경우(sys.database_principals에서 "E" 형식으로), 액세스 권한을 부여하고 Azure AD 데이터베이스 사용자의 사용 권한을 적용합니다.
    1. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 매핑된 Azure AD 그룹의 구성원인 경우 이 계정은 "X" 형식으로 sys.database_principals Azure AD 그룹 사용자의 액세스 권한을 부여하고 적용합니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업](/sql/t-sql/statements/backup-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [마스터 키 복원](/sql/t-sql/statements/restore-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 백업](/sql/t-sql/statements/backup-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 복원](/sql/t-sql/statements/restore-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.

## <a name="configuration"></a>구성

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장](/sql/database-engine/configure-windows/buffer-pool-extension)은 지원되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`는 지원되지 않습니다. [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>데이터 정렬

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원되는 호환성 수준: 100, 110, 120, 130, 140, 150
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원된 데이터베이스의 경우 호환성 수준이 100 이상이면 변경되지 않고 그대로 유지됩니다.

[ALTER DATABASE 호환성 수준](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`는 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다. 
- 범용 인스턴스당 280개의 파일 제한이 있으며 이는 데이터베이스당 최대 280개의 파일을 의미합니다. 범용 계층의 데이터와 로그 파일은 모두 이 제한으로 계산됩니다. [중요 비즈니스용 계층은 데이터베이스당 32767개의 파일을 지원합니다](./resource-limits.md#service-tier-characteristics).
- 데이터베이스는 파일 스트림 데이터를 포함한 파일 그룹을 포함할 수 없습니다. .bak에 `FILESTREAM` 데이터가 포함된 경우 복원이 실패합니다. 
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.

#### <a name="create-database-statement"></a>CREATE DATABASE 문

다음 제한 사항이 `CREATE DATABASE`에 적용됩니다.

- 파일 및 파일 그룹을 정의할 수 없습니다. 
- `CONTAINMENT` 옵션은 지원되지 않습니다. 
- `WITH` 옵션은 지원되지 않습니다. 
   > [!TIP]
   > 해결 방법으로, `CREATE DATABASE` 뒤에 `ALTER DATABASE`를 사용하여 파일을 추가하거나 포함을 설정하는 데이터베이스 옵션을 설정합니다. 

- `FOR ATTACH` 옵션은 지원되지 않습니다.
- `AS SNAPSHOT OF` 옵션은 지원되지 않습니다.

자세한 내용은 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 문에서 파일 경로를 지정할 수 없습니다. SQL Managed Instance에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다. 
- `ALTER DATABASE` 문을 사용하여 파일 이름을 변경할 수 없습니다.

기본적으로 설정되고 변경할 수 없는 옵션은 다음과 같습니다.

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

수정할 수 없는 옵션은 다음과 같습니다.

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

일부 `ALTER DATABASE` 문(예: [SET CONTAINMENT](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql))은 예를 들어 자동화된 데이터베이스 백업 중에 또는 데이터베이스가 생성된 직후에 일시적으로 실패할 수 있습니다. 이 경우 `ALTER DATABASE` 문을 다시 시도해야 합니다. 관련 오류 메시지에 대한 자세한 내용은 [설명 섹션](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)을 참조하세요.

자세한 내용은 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트

- SQL Server 에이전트를 사용하거나 사용하지 않도록 설정하는 기능은 현재 SQL Managed Instance에서 지원되지 않습니다. SQL 에이전트는 항상 실행됩니다.
- 유휴 상태의 CPU를 기반으로 하는 작업 일정 트리거는 지원되지 않습니다.
- SQL Server 에이전트 설정은 읽기 전용입니다. `sp_set_agent_properties` 프로시저는 SQL Managed Instance에서 지원되지 않습니다. 
- 작업
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅샷
    - 배포자
  - SSIS 작업 단계가 지원됩니다.
  - 다른 유형의 작업 단계는 현재 지원되지 않습니다.
    - 병합 복제 작업 단계는 지원되지 않습니다. 
    - 큐 판독기는 지원되지 않습니다. 
    - 명령 셸은 아직 지원되지 않습니다.
  - SQL Managed Instance는 외부 리소스(예: robocopy를 통한 네트워크 공유)에 액세스할 수 없습니다. 
  - SQL Server Analysis Services는 지원되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 데이터베이스 메일 프로필을 구성해야 하지만 이메일 알림이 지원됩니다. SQL Server 에이전트는 데이터베이스 메일 프로필을 하나만 사용할 수 있으며 `AzureManagedInstance_dbmail_profile`이라고 해야 합니다. 
  - 호출기는 지원되지 않습니다.
  - NetSend는 지원되지 않습니다.
  - 경고는 아직 지원되지 않습니다.
  - 프록시는 지원되지 않습니다.
- EventLog는 지원되지 않습니다.
- SQL 에이전트 작업을 생성, 수정 또는 실행하려면 사용자가 Azure AD 서버 보안 주체(로그인)에 직접 매핑되어야 합니다. 직접 매핑되지 않은 사용자(예: SQL 에이전트 작업을 생성, 수정 또는 실행할 권한이 있는 Azure AD 그룹에 속한 사용자)는 이러한 작업을 효과적으로 수행할 수 없습니다. 이는 Managed Instance 가장 및 [EXECUTE AS 제한](#logins-and-users)으로 인한 것입니다.
- Master/Target(MSX/TSX) 작업에 대한 다중 서버 관리 기능은 지원되지 않습니다.

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

다음 테이블 형식은 지원되지 않습니다.

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)(PolyBase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)(범용 계층에서만 지원되지 않음)

테이블을 만들고 변경하는 방법에 대한 자세한 내용은 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 및 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)을 참조하세요.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>BULK INSERT/OPENROWSET

SQL Managed Instance에서 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob Storage에서 파일을 가져와야 합니다.

- `DATASOURCE`는 Azure BLOB Storage에서 파일을 가져오는 동안 `BULK INSERT` 명령에 필요합니다. [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- Azure Blob Storage에서 파일의 내용을 읽을 때 `OPENROWSET` 함수에 `DATASOURCE`가 필요합니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENROWSET`을 사용하여 Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server 인스턴스에서 데이터를 읽을 수 있습니다. Oracle Database 또는 Excel 파일 등의 다른 원본은 지원되지 않습니다.

### <a name="clr"></a>CLR

SQL Managed Instance에서 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요. 
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.

### <a name="database-mail-db_mail"></a>데이터베이스 메일(db_mail)
 - `sp_send_dbmail`이 @file_attachments 매개 변수를 사용하여 첨부 파일을 보낼 수 없습니다. 이 프로시저에서는 로컬 파일 시스템 및 외부 공유 또는 Azure BLOB Storage에 액세스할 수 없습니다.
 - `@query` 매개 변수 및 인증과 관련된 알려진 문제를 참조하세요.
 
### <a name="dbcc"></a>DBCC

SQL Server에서 사용하도록 설정되었지만 문서화되지 않은 DBCC 문은 SQL Managed Instance에서 지원되지 않습니다.

- 제한된 수의 전역 추적 플래그만 지원됩니다. 세션 수준 `Trace flags`는 지원되지 않습니다. [추적 플래그](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)를 참조하세요.
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 및 [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)은 제한된 수의 전역 추적 플래그와 함께 사용합니다.
- 데이터베이스를 `SINGLE_USER` 모드로 설정할 수 없으므로 REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST 및 REPAIR_REBUILD 옵션과 함께 [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql)를 사용할 수 없습니다. [ALTER DATABASE 차이점](#alter-database-statement)을 참조하세요. 잠재적 데이터베이스 손상은 Azure 지원 팀에서 처리합니다. 데이터베이스가 손상된 경우 Azure 지원에 문의하세요.

### <a name="distributed-transactions"></a>분산 트랜잭션

[분산 트랜잭션](../database/elastic-transactions-overview.md)에 대한 부분 지원은 현재 공개 미리 보기로 제공됩니다. 분산 트랜잭션은 다음 조건에서 지원됩니다(모두 충족되어야 함).
* 모든 트랜잭션 참가자는 [서버 신뢰 그룹](./server-trust-group-overview.md)에 속한 Azure SQL Managed Instance입니다.
* 트랜잭션이 .NET(TransactionScope 클래스) 또는 Transact-SQL에서 시작됩니다.

Azure SQL Managed Instance는 현재 MSDTC 온-프레미스 또는 Azure Virtual Machines에서 정기적으로 지원하는 다른 시나리오를 지원하지 않습니다.

### <a name="extended-events"></a>확장 이벤트

확장 이벤트(Xevent)에 대한 일부 Windows 관련 대상은 지원되지 않습니다.

- `etw_classic_sync` 대상은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [etw_classic_sync 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file` 대상은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [event_file 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python 외부 라이브러리는 제한된 공개 미리 보기로 지원됩니다. [Azure SQL Managed Instance의 Machine Learning Services(미리 보기)](machine-learning-services-overview.md)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 FileTable

- 파일 스트림 데이터가 지원되지 않습니다.
- `FILESTREAM` 데이터가 있는 파일 그룹을 데이터베이스에 포함할 수 없습니다.
- `FILETABLE`는 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

자세한 내용은 [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버

SQL Managed Instance의 [연결 된 서버](/sql/relational-databases/linked-servers/linked-servers-database-engine) 는 제한 된 수의 대상을 지원 합니다.

- 지원되는 대상은 SQL Managed Instance, SQL Database, Azure Synapse SQL [서버리스](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/), 전용 풀 및 SQL Server Instances입니다. 
- 분산 쓰기 가능한 트랜잭션은 Managed Instance 간에만 가능합니다. 자세한 내용은 [분산 트랜잭션](../database/elastic-transactions-overview.md)을 참조하세요. 그러나 MS DTC는 지원되지 않습니다.
- 지원되지 않는 대상은 파일, Analysis Services 및 기타 RDBMS입니다. 파일 가져오기의 대안으로, `BULK INSERT` 또는 `OPENROWSET`을 사용하여 Azure Blob Storage에서 네이티브 CSV 가져오기를 사용하거나 [Azure Synapse Analytics의 서버리스 SQL 풀](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)을 사용하여 파일을 로드해 보세요.

작업: 

- [인스턴스 간](../database/elastic-transactions-overview.md) 쓰기 트랜잭션은 Managed Instances에서만 지원됩니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- `OPENROWSET` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리되는 컴퓨터, 온-프레미스 컴퓨터 또는 가상 머신 중 하나일 수 있습니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENDATASOURCE` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리되는 컴퓨터, 온-프레미스 컴퓨터 또는 가상 머신 중 하나일 수 있습니다. `SQLNCLI`, `SQLNCLI11` 및 `SQLOLEDB` 값만 공급자로 지원됩니다. 예제는 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`입니다. [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.
- 연결된 서버를 네트워크 공유에서 파일(Excel, CSV)을 읽는 데 사용할 수 없습니다. Azure Blob Storage에서 CSV 파일을 읽는 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)을 사용하거나 [Synapse Analytics에서 서버리스 SQL 풀을 참조하는 연결된 서버](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)를 사용해 보세요. [SQL Managed Instance 피드백 항목](https://feedback.azure.com/d365community/idea/db80cf6e-3425-ec11-b6e6-000d3a4f0f84)|에서 이 요청 추적

Azure SQL Managed Instance 연결된 서버는 SQL 인증 및 [AAD 인증을](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#linked-servers-with-azure-sql-managed-instance)지원합니다.

### <a name="polybase"></a>PolyBase

SQL Managed Instance에서 Polybase 지원을 사용하도록 설정하는 작업이 [진행 중](https://feedback.azure.com/d365community/idea/ccc44856-3425-ec11-b6e6-000d3a4f0f84)입니다. 그동안, 해결 방법으로 [Synapse Analytics의 서버리스 SQL 풀](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) 또는 SQL Server에 연결된 서버를 사용하여 Azure Data Lake 또는 Azure Storage에 저장된 파일에서 데이터를 쿼리할 수 있습니다.   
PolyBase에 대한 일반 정보는 [PolyBase](/sql/relational-databases/polybase/polybase-guide)를 참조하세요.

### <a name="replication"></a>복제

- 스냅숏과 양방향 복제 형식이 지원됩니다. 병합 복제, 피어 투 피어 복제 및 업데이트할 수 있는 구독은 지원되지 않습니다.
- [트랜잭션 복제](replication-transactional-overview.md)는 일부 제약 조건이 있는 SQL Managed Instance의 공개 미리 보기에 제공됩니다.
    - 모든 유형의 복제 참가자(게시자, 배포자, 끌어오기 구독자 및 밀어넣기 구독자)가 SQL Managed Instance에 배치될 수 있지만 게시자와 배포자는 클라우드 또는 온-프레미스에 모두 있어야 합니다.
    - SQL Managed Instance는 최신 버전의 SQL Server와 통신할 수 있습니다. 자세한 내용은 [지원되는 버전 매트릭스](replication-transactional-overview.md#supportability-matrix)를 참조하세요.
    - 트랜잭션 복제에는 [추가 네트워킹 요구 사항](replication-transactional-overview.md#requirements)이 있습니다.

트랜잭션 복제를 구성하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.
- [SQL MI 게시자와 SQL MI 구독자 간 복제](replication-between-two-instances-configure-tutorial.md)
- [SQL MI 게시자, SQL MI 배포자 및 SQL Server 구독자 간 복제](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE 문 

- 지원되는 구문:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원되지 않는 구문:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 원본: 
  - `FROM URL`(Azure Blob Storage)만 지원되는 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH` 옵션은 지원되지 않습니다. `DIFFERENTIAL`, `STATS`, `REPLACE` 등과 같은 `WITH`를 포함한 복원 시도는 실패합니다.
- `ASYNC RESTORE`: 클라이언트 연결이 중단되더라도 복원이 계속됩니다. 연결이 삭제된 경우 `sys.dm_operation_status` 보기에서 복원 작업 상태와 CREATE 및 DROP Database를 확인할 수 있습니다. 자세한 내용은 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요. 

다음은 설정 또는 재정의되고 나중에 변경할 수 없는 데이터베이스 옵션입니다. 

- `NEW_BROKER`(.bak 파일에서 브로커를 사용하도록 설정하지 않은 경우) 
- `ENABLE_BROKER`(.bak 파일에서 브로커를 사용하도록 설정하지 않은 경우) 
- `AUTO_CLOSE=OFF`(.bak 파일의 데이터베이스에 `AUTO_CLOSE=ON`이 있는 경우) 
- `RECOVERY FULL`(.bak 파일의 데이터베이스에 `SIMPLE` 또는 `BULK_LOGGED` 복구 모드가 있는 경우)
- 원본 .bak 파일에 없는 경우 메모리 최적화 파일 그룹이 추가되고 XTP라고 합니다. 
- 모든 기존 메모리에 최적화된 파일 그룹은 XTP로 이름이 변경됩니다. 
- `SINGLE_USER` 및 `RESTRICTED_USER` 옵션이 `MULTI_USER`로 변환됩니다.

제한 사항: 

- 손상된 데이터베이스의 백업은 손상 유형에 따라 복원될 수 있지만 손상이 수정될 때까지 자동화된 백업은 수행되지 않습니다. 원본 SQL Managed Instance에서 `DBCC CHECKDB`를 실행하고 이 문제를 방지하려면 `WITH CHECKSUM` 백업을 사용해야 합니다.
- 이 문서에서 설명하는 제한(예: `FILESTREAM` 또는 `FILETABLE` 개체)을 포함하는 데이터베이스의 `.BAK` 파일 복원은 SQL Managed Instance에서 복원할 수 없습니다.
- 여러 백업 세트가 포함된 `.BAK` 파일은 복원할 수 없습니다. 
- 여러 로그 파일이 포함된 `.BAK` 파일은 복원할 수 없습니다.
- 8TB보다 큰 데이터베이스, 활성 메모리 내 OLTP 개체 또는 인스턴스당 280개의 파일을 초과하는 파일 수를 포함하는 백업은 범용 인스턴스에서 복원할 수 없습니다. 
- 4TB보다 큰 데이터베이스 또는 [리소스 제한](resource-limits.md)에 설명된 크기보다 전체 크기가 큰 메모리 내 OLTP 개체가 포함된 백업은 중요 비즈니스용 인스턴스에서 복원할 수 없습니다.
복원 명령문에 대한 자세한 내용은 [RESTORE 문](/sql/t-sql/statements/restore-statements-transact-sql)을 참조하세요.

 > [!IMPORTANT]
 > 기본 제공 지정 시점 복원 작업에도 동일한 제한이 적용됩니다. 예를 들어 4TB보다 큰 범용 데이터베이스는 중요 비즈니스용 인스턴스에서 복원할 수 없습니다. 메모리 내 OLTP 파일 또는 280개보다 많은 파일을 포함하는 중요 비즈니스용 데이터베이스는 범용 인스턴스에서 복원할 수 없습니다.

### <a name="service-broker"></a>Service Broker

인스턴스 간 Service Broker 메시지 교환은 Azure SQL Managed Instance 사이에만 지원됩니다.

- `CREATE ROUTE`: 다른 SQL Managed Instance의 `LOCAL`또는 DNS 이름 이외의 `ADDRESS`가 포함된 `CREATE ROUTE`는 사용할 수 없습니다. 포트는 항상 4022입니다.
- `ALTER ROUTE`: 다른 SQL Managed Instance의 `LOCAL`또는 DNS 이름 이외의 `ADDRESS`가 포함된 `ALTER ROUTE`는 사용할 수 없습니다. 포트는 항상 4022입니다.

전송 보안이 지원되며, 대화 보안은 지원되지 않습니다.
- `CREATE REMOTE SERVICE BINDING`은 지원되지 않습니다.

Service Broker는 기본적으로 사용하도록 설정되어 있으며, 비활성화할 수 없습니다. 다음 ALTER DATABASE 옵션은 지원되지 않습니다.
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>저장 프로시저, 함수, 트리거

- `NATIVE_COMPILATION`은 범용 계층에서 지원되지 않습니다.
- 지원되지 않는 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- 다음 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 무시되고 영향을 주지 않습니다. 
  - `Ole Automation Procedures`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`는 지원되지 않습니다. [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `Extended stored procedures` 는 지원되지 않으며 여기에는 `sp_addextendedproc` 및 `sp_dropextendedproc`이 포함됩니다. 이 기능은 SQL Server의 사용 중단 경로에 있기 때문에 지원되지 않습니다. 자세한 내용은 [확장 저장 프로시저](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)를 참조하세요.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

### <a name="system-functions-and-variables"></a>시스템 함수 및 변수

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')`에서 값 8을 반환합니다. 이 속성은 SQL Managed Instance를 고유하게 식별합니다. [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- SQL Server에 대한 인스턴스 개념은 SQL Managed Instance에 적용되지 않으므로 `SERVERPROPERTY('InstanceName')`에서 NULL을 반환합니다. [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`은 전체 DNS "연결 가능한" 이름을 반환합니다(예: my-managed-instance.wcus17662feb9ce98.database.windows.net). [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql)을 참조하세요. 
- `SYS.SERVERS`는 "name" 및 "data_source" 속성에 대한 전체 DNS "연결 가능한" 이름을 반환합니다(예: `myinstance.domain.database.windows.net`). [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- SQL Server에 대한 서비스 개념은 SQL Managed Instance에 적용되지 않으므로 `@@SERVICENAME`에서 NULL을 반환합니다. [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.
- `SUSER_ID`이 지원됩니다. Azure AD 로그인이 sys. syslogins에 없으면 NULL을 반환합니다. [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요. 
- `SUSER_SID`는 지원되지 않습니다. 잘못된 데이터가 반환되며 이는 알려진 일시적 문제입니다. [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 

## <a name="environment-constraints"></a><a name="Environment"></a>환경 제약 조건

### <a name="subnet"></a>서브넷
-  SQL Managed Instance를 배포한 서브넷에 다른 리소스(예: 가상 머신)를 배치할 수 없습니다. 다른 서브넷을 사용하여 이러한 리소스를 배포합니다.
- 서브넷에 사용 가능한 [IP 주소](connectivity-architecture-overview.md#network-requirements) 수가 충분해야 합니다. 최소한 32개의 IP 주소를 서브넷에 포함해야 합니다.
- 지역에 배포할 수 있는 vCore 및 인스턴스 유형 수에는 몇 가지 [제약 조건 및 제한](resource-limits.md#regional-resource-limitations)이 있습니다.
- 서브넷에 적용해야 하는 [네트워킹 구성](connectivity-architecture-overview.md#network-requirements)이 있습니다.

### <a name="vnet"></a>VNET
- VNet은 리소스 모델을 사용하여 배포할 수 있으며 VNet에 대한 클래식 모델은 지원되지 않습니다.
- SQL Managed Instance를 만든 후에 SQL Managed Instance 또는 VNet을 다른 리소스 그룹이나 구독으로 이동하는 기능은 지원되지 않습니다.
- 2020년 9월 22일 이전에 만든 가상 클러스터에서 호스트되는 SQL Managed Instances의 경우에는 [글로벌 피어링](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)이 지원되지 않습니다. VNet 게이트웨이를 사용하여 ExpressRoute 또는 VNet 간을 통해 이러한 리소스에 연결할 수 있습니다.

### <a name="failover-groups"></a>장애 조치(failover) 그룹
시스템 데이터베이스는 장애 조치(failover) 그룹의 보조 인스턴스에 복제되지 않습니다. 따라서 시스템 데이터베이스의 개체를 사용하는 시나리오는 개체가 보조 인스턴스에서 수동으로 생성되지 않는 한 보조 인스턴스에서는 불가능합니다.

### <a name="tempdb"></a>TEMPDB
- `tempdb`의 최대 파일 크기는 범용 계층에서 코어당 24GB보다 클 수 없습니다. 중요 비즈니스용 계층의 최대 `tempdb` 크기는 SQL Managed Instance 스토리지 크기에 의해 제한됩니다. `Tempdb` 로그 파일 크기는 범용 계층에서 120GB로 제한됩니다. 일부 쿼리는 `tempdb`에서 코어당 24GB보다 많이 필요하거나 120GB보다 많이 로그 데이터를 생성하는 경우 오류를 반환할 수 있습니다.
- `Tempdb`는 항상 12개의 데이터 파일(마스터 파일이라고도 하는 주 데이터 파일 1개, 주 데이터 파일 이외의 파일 11개)로 분할됩니다. 파일 구조는 변경할 수 없으며 새 파일을 `tempdb`에 추가할 수 없습니다. 
- 새로운 SQL Server 2019 메모리 내 데이터베이스 기능인 [메모리 최적화`tempdb` 메타데이터](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata)는 지원되지 않습니다.
- 모델 데이터베이스에서 생성된 개체는 `tempdb`가 모델 데이터베이스에서 초기 개체 목록을 가져오지 않기 때문에 다시 시작 또는 장애 복구(failover) 후에 `tempdb`에서 자동으로 만들 수 없습니다. 각 다시 시작 또는 장애 조치(failover) 후에는 `tempdb`에서 수동으로 개체를 만들어야 합니다.

### <a name="msdb"></a>MSDB

SQL Managed Instance의 다음 MSDB 스키마는 각각의 미리 정의된 역할이 소유해야 합니다.

- 일반 역할
  - TargetServersRole
- [고정 데이터베이스 역할](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail 역할](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile):
  - DatabaseMailUserRole
- [Integration Services 역할](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true):
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 고객이 미리 정의된 역할 이름, 스키마 이름 및 스키마 소유자를 변경하면 서비스의 정상적인 작동에 영향을 줍니다. 이러한 변경 내용은 정상적인 서비스 작업을 보장하기 위해 검색되는 즉시 또는 다음 최신 서비스 업데이트 시 다시 되돌려집니다.

### <a name="error-logs"></a>오류 로그

SQL Managed Instance는 자세한 정보를 오류 로그에 배치합니다. 내부 시스템 이벤트는 오류 로그에 많이 기록됩니다. 관련이 없는 항목을 필터링하는 사용자 지정 프로시저를 사용하여 오류 로그를 읽습니다. 자세한 내용은 Azure Data Studio의 경우 [ 확장(미리 보기)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 또는 [ – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 기능 및 비교 목록은 [ Azure SQL Managed Instance 기능](../database/features-comparison.md)을 참조하세요.
- 릴리스 업데이트는 [새로운 것을 참조하세요.](doc-changes-updates-release-notes-whats-new.md)
- 문제, 해결 방법 및 해결 방법을 보려면 [알려진 문제를](doc-changes-updates-known-issues.md)참조하세요.
- 새 SQL Managed Instance를 만드는 방법을 보여 주는 빠른 시작은 [ 만들기](instance-create-quickstart.md)를 참조하세요.