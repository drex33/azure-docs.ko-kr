---
title: 데이터베이스 복사
description: 동일한 서버 또는 다른 서버에서 Azure SQL Database의 기존 데이터베이스에 대한 트랜잭션 일치 복사본을 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: LitKnd
ms.author: kendralittle
ms.reviewer: mathoma
ms.date: 03/10/2021
ms.openlocfilehash: b62e9a059c2f60aec59a6277260d6deae171ecae
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133355286"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스에 대한 트랜잭션 일치 복사본 복사

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database는 동일한 서버 또는 다른 서버에서 기존 [데이터베이스](single-database-overview.md)의 복사본을 만들기 위한 여러 가지 방법을 제공합니다. Azure Portal, PowerShell, Azure CLI 또는 T-SQL을 사용하여 데이터베이스를 복사할 수 있습니다.

## <a name="overview"></a>개요

데이터베이스 복사본은 복사 요청이 시작된 후의 시점으로 원본 데이터베이스의 트랜잭션 일치 스냅샷입니다. 복사본에 대해 동일한 서버 또는 다른 서버를 선택할 수 있습니다. 또한 원본 데이터베이스의 백업 중복, 서비스 계층 및 컴퓨팅 크기를 유지하거나 동일한 또는 다른 서비스 계층 내에서 다른 백업 스토리지 중복 및/또는 컴퓨팅 크기를 사용하도록 선택할 수 있습니다. 복사가 완료되면 완전히 작동하는 독립 데이터베이스가 됩니다. 복사된 데이터베이스의 로그인, 사용자 및 사용 권한은 원본 데이터베이스와 독립적으로 관리됩니다. 이 복사본은 지역에서 복제 기술을 사용하여 만들어집니다. 복제본 시드가 완료되면 지역에서 복제 링크가 자동으로 종료됩니다. 지역에서 복제 사용에 대한 모든 요구 사항은 데이터베이스 복사 작업에 적용됩니다. 자세한 내용은 [활성 지역 복제 개요](active-geo-replication-overview.md)를 참조하세요.

> [!NOTE]
> Azure SQL Database 구성 가능한 백업 스토리지 중복은 현재 동남 아시아 Azure 지역에서만 일반 공급되며 브라질 남부에서는 공개 미리 보기로 이용할 수 있습니다. 미리 보기 상태에서는 로컬 중복 또는 영역 중복 백업 스토리지 중복을 사용하여 원본 데이터베이스를 만드는 경우 다른 Azure 지역의 서버에 데이터베이스를 복사하는 것은 지원되지 않습니다. 

## <a name="database-copy-for-azure-sql-hyperscale"></a>Azure SQL 하이퍼스케일용 데이터베이스 복사

Azure SQL 하이퍼스케일의 경우 대상 데이터베이스는 복사본이 빠른 복사인지 아니면 데이터 복사 크기인지를 결정합니다.

빠른 복사: 복사가 원본과 동일한 지역에서 수행되면 Blob의 스냅샷에서 복사본이 만들어집니다. 이 복사본은 데이터베이스 크기에 관계없이 빠른 작업입니다.

데이터 복사 크기: 대상 데이터베이스가 원본과 다른 지역에 있거나 대상의 데이터베이스 백업 스토리지 중복성(로컬, 영역, 지역)이 원본 데이터베이스와 다른 경우 복사 작업은 데이터 작업의 크기가 됩니다. 페이지 서버 Blob이 병렬로 복사되기 때문에 복사 시간은 크기에 직접 비례하지 않습니다.

## <a name="logins-in-the-database-copy"></a>데이터베이스 복사본에서 로그인

데이터베이스를 동일한 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하는 보안 주체는 새 데이터베이스의 데이터베이스 소유자가 됩니다.

다른 서버에 데이터베이스를 복사하는 경우 대상 서버에서 복사 작업을 시작한 보안 주체가 새 데이터베이스의 소유자가 됩니다.

대상 서버에 관계없이 모든 데이터베이스 사용자, 권한 및 SID(보안 식별자)가 데이터베이스 사본에 복사됩니다. 데이터 액세스에 대해 [포함된 데이터베이스 사용자](logins-create-manage.md)를 사용하면 복사된 데이터베이스에 동일한 사용자 자격 증명이 있으므로 복사를 완료한 후 동일한 자격 증명을 사용하여 해당 데이터베이스에 즉시 액세스할 수 있습니다.

데이터 액세스에 서버 수준 로그인을 사용하고 데이터베이스를 다른 서버에 복사하는 경우 로그인 기반 액세스가 작동하지 않을 수 있습니다. 이는 로그인이 대상 서버에 없거나 암호 및 SID(보안 식별자)가 다르기 때문에 발생할 수 있습니다. 다른 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)을 참조하세요. 다른 서버에 대한 복사 작업이 성공하고 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자나 서버 관리자와 연결된 로그인만 복사된 데이터베이스에 로그인할 수 있습니다. 복사 작업이 완료된 후 로그인을 확인하고 데이터 액세스를 설정하려면 [로그인 확인](#resolve-logins)을 참조하세요.

## <a name="copy-using-the-azure-portal"></a>Azure Portal을 사용하여 복사

Azure Portal을 사용하여 데이터베이스를 복사하려면 데이터베이스에 대한 페이지를 열고 **복사** 를 클릭합니다.

   ![데이터베이스 복사](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>PowerShell 또는 Azure CLI를 사용하여 복사

데이터베이스를 복사하려면 다음 예제를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell의 경우 [AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet을 사용합니다.

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 여전히 Azure SQL Database에서 지원되지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

데이터베이스 복사는 비동기 작업이지만 요청이 수락된 직후에 대상 데이터베이스가 생성됩니다. 진행 중인 동안 복사 작업을 취소해야 하는 경우 [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet을 사용하여 대상 데이터베이스를 삭제합니다.

전체 샘플 PowerShell 스크립트는 [새 서버에 데이터베이스 복사](scripts/copy-database-to-new-server-powershell.md)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

데이터베이스 복사는 비동기 작업이지만 요청이 수락된 직후에 대상 데이터베이스가 생성됩니다. 진행 중인 동안 복사 작업을 취소해야 하는 경우 [az sql db delete](/cli/azure/sql/db#az_sql_db_delete) cmdlet을 사용하여 대상 데이터베이스를 삭제합니다.

* * *

## <a name="copy-using-transact-sql"></a>Transact-SQL을 사용하여 복사

서버 관리자 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 로그인이 서버 관리자가 아닌 `dbmanager` 역할의 구성원이어야 합니다. 서버에 로그인 및 연결하는 방법에 대한 자세한 내용은 [로그인 관리](logins-create-manage.md)를 참조하세요.

[CREATE DATABASE ... AS COPY OF](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) 문으로 원본 데이터베이스 복사를 시작합니다. T-SQL 문은 데이터베이스 복사 작업이 완료될 때까지 계속 실행됩니다.

> [!NOTE]
> T-SQL 문을 종료해도 데이터베이스 복사 작업은 종료되지 않습니다. 작업을 종료하려면 대상 데이터베이스를 삭제합니다.
>
> 원본 및/또는 대상 서버가 [개인 끝점](private-endpoint-overview.md) 을 구성 하 고 [공용 네트워크 액세스가 거부](connectivity-settings.md#deny-public-network-access)된 경우에는 데이터베이스 복사가 지원 되지 않습니다. 개인 끝점이 구성 되었지만 공용 네트워크 액세스가 허용 되는 경우 공용 IP 주소에서 대상 서버에 연결 될 때 데이터베이스 복사를 시작 하는 것이 지원 됩니다. 복사 작업이 완료 되 면 공용 액세스를 거부할 수 있습니다.

> [!IMPORTANT]
> T-SQL CREATE DATABASE ... AS COPY OF 명령을 사용할 때 백업 스토리지 중복을 선택하는 것은 아직 지원되지 않습니다. 

### <a name="copy-to-the-same-server"></a>동일한 서버에 복사

서버 관리자 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 로그인이 서버 관리자가 아닌 `dbmanager` 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>탄력적 풀에 복사

서버 관리자 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 로그인이 서버 관리자가 아닌 `dbmanager` 역할의 구성원이어야 합니다.

이 명령은 pool1이라는 탄력적 풀에서 Database2라는 새 데이터베이스에 Database1을 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

Database1은 단일 또는 풀링된 데이터베이스가 될 수 있습니다. 다른 계층 풀 간 복사는 지원되지만 일부 교차 계층 복사본은 성공하지 않습니다. 예를 들어 단일 또는 탄력적 표준 db를 범용 풀로 복사할 수 있지만 탄력적 표준 db를 프리미엄 풀로 복사할 수는 없습니다. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>다른 서버에 복사

새 데이터베이스를 만들 대상 서버의 master 데이터베이스에 로그인합니다. 원본 서버에서 원본 데이터베이스의 데이터베이스 소유자와 동일한 이름과 암호를 가진 로그인을 사용합니다. 대상 서버의 로그인도 `dbmanager` 역할의 구성원이거나 서버 관리자 로그인이어야 합니다.

이 명령은 server1의 Database1을 server2의 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 두 서버 방화벽이 모두 T-SQL CREATE DATABASE ... AS COPY OF 명령을 실행하는 클라이언트의 IP에서 인바운드 연결을 허용하도록 구성해야 합니다. 현재 연결의 원본 IP 주소를 확인하려면 `SELECT client_net_address FROM sys.dm_exec_connections WHERE session_id = @@SPID;`를 실행합니다.

### <a name="copy-to-a-different-subscription"></a>다른 구독으로 복사

T-SQL을 사용하여 다른 구독에 있는 서버에 데이터베이스를 복사하려면 [다른 서버에 SQL Database 복사](#copy-to-a-different-server) 섹션의 단계를 수행하면 됩니다. 원본 데이터베이스의 데이터베이스 소유자와 동일한 이름과 암호를 가진 로그인을 사용해야 합니다. 또한 로그인은 원본 및 대상 서버 둘 다에서 `dbmanager` 역할 또는 서버 관리자의 구성원이어야 합니다.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell 및 Azure CLI는 다른 구독에 대한 데이터베이스 복사를 지원하지 않습니다.

> [!TIP]
> T-SQL을 사용한 데이터베이스 복사는 다른 Azure 테넌트의 구독에서 데이터베이스를 복사 하는 것을 지원합니다. 이는 SQL 인증 로그인을 사용하여 대상 서버에 로그인하는 경우에만 지원됩니다.
> 원본 또는 대상 논리 서버에서 [Azure Active Directory](https://techcommunity.microsoft.com/t5/azure-sql/support-for-azure-ad-user-creation-on-behalf-of-azure-ad/ba-p/2346849) 인증이 활성화(사용)된 경우 다른 Azure 테넌트의 논리 서버에 데이터베이스 복사본을 만드는 것은 지원되지 않습니다.

## <a name="monitor-the-progress-of-the-copying-operation"></a>복사 작업 진행률 모니터링

[sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database) 및 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 뷰를 쿼리하여 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **COPYING** 으로 설정됩니다.

* 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **SUSPECT** 로 설정됩니다. 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
* 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **ONLINE** 으로 설정됩니다. 복사가 완료되었으며 새 데이터베이스가 원본 데이터베이스와는 독립적으로 변경 가능한 일반 데이터베이스가 됩니다.

> [!NOTE]
> 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) 문을 실행합니다.

> [!IMPORTANT]
> 원본보다 훨씬 더 작은 서비스 목표를 가진 복사본을 만들어야 하는 경우, 대상 데이터베이스에 시드 프로세스를 완료하는 데 필요한 리소스가 부족하여 복사 작업이 실패할 수 있습니다. 이 시나리오에서는 지역 복원 요청을 사용하여 다른 서버 및/또는 다른 지역에 복사본을 만듭니다. 자세한 내용은 [데이터베이스 백업을 사용하여 Azure SQL Database 복구](recovery-using-backups.md#geo-restore)를 참조하세요.

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Azure RBAC 역할 및 데이터베이스 복사본 관리 권한

데이터베이스 복사본을 만들려면 다음 역할을 부여받아야 합니다.

* 구독 소유자 또는
* SQL Server 기여자 역할 또는
* 원본 및 대상 데이터베이스에 대한 다음 권한이 있는 사용자 지정 역할:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

데이터베이스 복사본을 취소하려면 다음 역할을 부여받아야 합니다.

* 구독 소유자 또는
* SQL Server 기여자 역할 또는
* 원본 및 대상 데이터베이스에 대한 다음 권한이 있는 사용자 지정 역할:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Azure Portal을 사용하여 데이터베이스 복사본을 관리하려면 다음 사용 권한도 필요합니다.

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

포털에서 리소스 그룹의 배포 아래에 있는 작업, SQL 작업을 비롯한 여러 리소스 공급자의 작업을 확인하려면 다음과 같은 추가 권한이 필요합니다.

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>로그인 확인

대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)을 참조하세요. [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 됩니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자만 새 데이터베이스에 로그인할 수 있습니다.

다른 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)을 참조하세요.

## <a name="database-copy-errors"></a>데이터베이스 복사 오류

Azure SQL Database에서 데이터베이스를 복사하는 동안 다음 오류가 발생할 수 있습니다. 자세한 내용은 [Azure SQL Database 복사](database-copy.md)를 참조하세요.

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 40635 |16 |IP 주소 '%.&#x2a;ls'을(를) 사용하는 클라이언트가 일시적으로 비활성화되었습니다. |
| 40637 |16 |데이터베이스 복사본 만들기를 현재 사용할 수 없습니다. |
| 40561 |16 |데이터베이스를 복사하지 못했습니다. 원본 또는 대상 데이터베이스가 존재하지 않습니다. |
| 40562 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스가 삭제되었습니다. |
| 40563 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스가 삭제되었습니다. |
| 40564 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40565 |16 |데이터베이스를 복사하지 못했습니다. 동일한 소스에서 1개의 동시 데이터베이스 복사본이 허용됩니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40566 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40567 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40568 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40569 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40570 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40571 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |

## <a name="next-steps"></a>다음 단계

* 로그인에 대한 자세한 내용은 [로그인 관리](logins-create-manage.md) 및 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](active-geo-replication-security-configure.md)을 참조하세요.
* 데이터베이스를 내보내려면 [데이터베이스를 BACPAC로 내보내기](database-export.md)를 참조하세요.
