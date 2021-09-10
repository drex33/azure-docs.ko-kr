---
title: Azure 데이터베이스 리소스, Azure 독일을 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 데이터베이스 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 방법을 설명합니다.
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 6fe6d653712e034c13f3b755e906de491b4dd49a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122528071"
---
# <a name="migrate-database-resources-to-global-azure"></a>데이터베이스 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 데이터베이스 리소스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 데 유용한 정보가 있습니다.

## <a name="sql-database"></a>SQL Database

작은 Azure SQL Database 워크로드를 마이그레이션하려면(마이그레이션된 데이터베이스를 온라인 상태로 유지하지 않고) 내보내기 함수를 사용하여 BACPAC 파일을 만듭니다. BACPAC 파일은 메타데이터와 SQL Server 데이터베이스 데이터를 포함하는 압축 파일입니다. BACPAC 파일을 만든 후에는 파일을 대상 환경에 복사하고(예: AzCopy 사용) 가져오기 함수를 사용하여 데이터베이스를 다시 빌드할 수 있습니다. 다음 고려 사항에 유의하시기 바랍니다.

- 내보내기에서 트랜잭션 일관성을 유지하려면 다음 조건 중 하나가 true인지 확인합니다.
  - 내보내는 동안 쓰기 활동이 발생하지 않습니다.
  - SQL 데이터베이스의 트랜잭션 일관성 복사본에서 내보냅니다.
- Azure Blob Storage로 내보내기 위해 BACPAC 파일 크기는 200GB로 제한됩니다. 더 큰 BACPAC 파일의 경우 로컬 스토리지로 내보냅니다.
- SQL Database에서 내보내기 작업이 20시간보다 오래 걸리면 작업이 취소될 수 있습니다. 성능을 향상시키는 방법에 대한 팁은 다음 문서를 참조하세요.

> [!NOTE]
> 내보내기 작업 도중 서버의 DNS 이름이 변경되므로 내보내기 작업 이후 연결 문자열이 변경됩니다.

추가 정보는 다음 항목을 참조하세요.

- [데이터베이스를 BACPAC 파일로 내보내는](../azure-sql/database/database-export.md) 방법을 알아봅니다.
- [BACPAC 파일을 데이터베이스로 가져오는](../azure-sql/database/database-import.md) 방법을 알아봅니다.
- [Azure SQL Database 설명서](/azure/sql-database/)를 검토하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>활성 지역 복제를 사용하여 SQL Database 마이그레이션

데이터베이스가 BACPAC 파일에 비해 너무 크거나 한 클라우드에서 다른 클라우드로 마이그레이션하면서 온라인 상태를 유지하고 가동 중지 시간을 최소화하려는 경우 Azure 독일에서 글로벌 Azure로 활성 지역 복제를 구성할 수 있습니다.

> [!IMPORTANT]
> 데이터베이스를 글로벌 Azure로 마이그레이션하도록 활성 지역 복제를 구성하는 것은 T-SQL(Transact-SQL)을 사용하는 경우에만 지원되며, 마이그레이션하기 전에 글로벌 Azure로 마이그레이션을 지원하려면 구독 활성화를 요청해야 합니다. 요청을 제출하려면 [이 지원 요청 링크](#requesting-access)를 사용해야 합니다. 

> [!Note]
> Azure 글로벌 클라우드 지역인 독일 중서부 및 독일 북부는 Azure 독일 클라우드를 통해 활성 지역 복제가 지원되는 지역입니다. 대체 글로벌 Azure 지역이 최종 데이터베이스 대상으로 필요한 경우에는 글로벌 Azure로 마이그레이션을 완료한 후 독일 중서부 또는 독일 북부에서 필요한 Azure 글로벌 클라우드 지역으로 추가 지역 복제 링크를 구성하는 것이 좋습니다.

활성 지역 복제 비용에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)의 **활성 지역 복제** 섹션을 참조하세요.

활성 지역 복제를 사용하여 데이터베이스를 마이그레이션하려면 글로벌 Azure에 Azure SQL 논리 서버가 필요합니다. 포털, Azure PowerShell, Azure CLI 등을 사용하여 서버를 만들 수 있지만 Azure 독일에서 글로벌 Azure로 마이그레이션하도록 활성 지역 복제를 구성하는 것은 T-SQL(Transact-SQL)을 사용해야만 지원됩니다.

> [!IMPORTANT]
> 클라우드 간에 마이그레이션하는 경우 주(Azure 독일) 서버와 보조(글로벌 Azure) 서버 이름 접두사가 달라야 합니다. 서버 이름이 같은 경우 ALTER DATABASE 문을 실행하면 성공하지만 마이그레이션은 실패합니다. 예를 들어 주 서버 이름의 접두사가 `myserver`(`myserver.database.cloudapi.de`)이면 글로벌 Azure에서 보조 서버 이름의 접두사는 `myserver`일 수 없습니다.


`ALTER DATABASE` 문을 사용하면 대상 측에서 정규화된 DNS 서버 이름을 사용하여 글로벌 Azure에서 대상 서버를 지정할 수 있습니다. 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- *`sourcedb`* 는 Azure 독일의 Azure SQL Server에 있는 데이터베이스 이름을 나타냅니다. 
- *`public-server.database.windows.net`* 은 데이터베이스를 마이그레이션해야 하는 글로벌 Azure에 존재하는 Azure SQL Server 이름을 나타냅니다. "database.windows.net" 네임스페이스가 필요합니다. *public-server* 를 글로벌 Azure의 논리적 SQL 서버 이름으로 대체합니다. 글로벌 Azure의 서버는 Azure 독일의 주 서버와 이름이 달라야 합니다.


명령은 마이그레이션할 로컬 데이터베이스를 호스트하는 Azure 독일 서버의 마스터 데이터베이스에서 실행됩니다. 
- T-SQL start-copy API는 서버의 마스터 데이터베이스에서 SQL 로그인/사용자 이름이 동일한 사용자를 찾아서 퍼블릭 클라우드 서버에 로그인한 사용자를 인증합니다. 이 방식은 클라우드 제약이 없습니다. 따라서 T-SQL API는 클라우드 간 복사를 시작하는 데 사용됩니다. 이 항목에 대한 권한 및 자세한 내용은 [활성 지역 복제 만들기 및 사용](../azure-sql/database/active-geo-replication-overview.md)과 [ALTER DATABASE(Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/)를 참조하세요.
- 글로벌 Azure의 Azure SQL 논리 서버를 나타내는 초기 T-SQL 명령 확장을 제외하고 나머지 활성 지역 복제 프로세스는 로컬 클라우드의 기존 실행과 동일합니다. 활성 지역 복제를 만드는 자세한 단계는 글로벌 Azure에서 만든 보조 논리 서버에서 보조 데이터베이스가 생성되는 경우를 제외하고 [활성 지역 복제 만들기 및 사용](../azure-sql/database/active-geo-replication-overview.md)을 참조하세요. 
- 보조 데이터베이스가 글로벌 Azure에 존재하면(Azure 독일 데이터베이스의 온라인 복사본으로) 고객은 ALTER DATABASE T-SQL 명령을 사용하여 이 데이터베이스에 대해 Azure 독일에서 글로벌 Azure로의 데이터베이스 장애 조치(failover)를 시작할 수 있습니다(아래 표 참조).
- 장애 조치(failover) 후 보조 데이터베이스가 글로벌 Azure의 주 데이터베이스가 되면 활성 지역 복제를 중지하고 Azure 독일 측에서 언제든지 보조 데이터베이스를 제거할 수 있습니다(아래 표 및 다이어그램에 있는 단계 참조). 
- 장애 조치(failover) 후 Azure 독일의 보조 데이터베이스는 삭제될 때까지 계속 비용이 발생합니다.
      
- `ALTER DATABASE` 명령을 사용하는 것은 활성 지역 복제를 설정하여 Azure 독일 데이터베이스를 글로벌 Azure로 마이그레이션하는 유일한 방법입니다. 
- 이 마이그레이션에 대한 활성 지역 복제를 구성하는 데 Azure Portal, Azure Resource Manager, PowerShell 또는 CLI는 사용할 수 없습니다. 

Azure 독일에서 글로벌 Azure로 데이터베이스를 마이그레이션하려면:   

1.  Azure 독일에서 사용자 데이터베이스(예: `azuregermanydb`)를 선택합니다.
2.  글로벌 Azure(퍼블릭 클라우드)에서 논리 서버(예: `globalazureserver`)를 만듭니다. FQDN(정규화된 도메인 이름)은 `globalazureserver.database.windows.net`입니다.
3.  Azure 독일의 서버에서 이 T-SQL 명령을 실행하여 Azure 독일에서 글로벌 Azure로 활성 지역 복제를 시작합니다. 정규화된 DNS 이름은 public 서버 `globalazureserver.database.windows.net`에 사용됩니다. 이것은 대상 서버가 Azure 독일이 아닌 글로벌 Azure에 있음을 나타냅니다.

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  복제가 읽기-쓰기 워크로드를 글로벌 Azure 서버로 이동할 준비가 되면 글로벌 Azure 서버에서 이 T-SQL 명령을 실행하여 글로벌 Azure로 계획된 장애 조치(failover)를 시작합니다.

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  활성 지역 복제 링크는 장애 조치(failover) 프로세스 전이나 후에 종료될 수 있습니다. 계획된 장애 조치(failover) 후 다음 T-SQL 명령을 실행하면 읽기-쓰기 복사본이 되는 글로벌 Azure의 데이터베이스와의 지역 복제 링크가 제거됩니다. 현재 지역 주 데이터베이스의 논리 서버(즉, 글로벌 Azure 서버)에서 실행해야 합니다. 그러면 마이그레이션 프로세스가 완료됩니다.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    계획된 장애 조치(failover) 전에 다음 T-SQL 명령을 실행해도 마이그레이션 프로세스가 중지되지만 이 경우에는 Azure 독일의 데이터베이스가 읽기-쓰기 복사본으로 유지됩니다. 이 T-SQL 명령은 현재 지역 주 데이터베이스의 논리 서버(이 경우 Azure 독일 서버)에서도 실행해야 합니다.

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

Azure 독일에서 글로벌 Azure로 Azure SQL 데이터베이스를 마이그레이션하는 이러한 단계는 활성 지역 복제를 사용하여 수행할 수도 있습니다.


자세한 내용은 아래 표에서 장애 조치(failover) 관리를 위한 T-SQL 명령을 참조하세요. Azure 독일과 글로벌 Azure 사이의 클라우드 간 활성 지역 복제에 대해 다음 명령이 지원됩니다. 
 
|명령 |설명|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작하려면 ADD SECONDARY ON SERVER 인수를 사용합니다.| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |장애 조치를 시작하기 위해 보조 데이터베이스를 기본 데이터베이스로 전환하려면 FAILOVER 또는 FORCE_FAILOVER_ALLOW_DATA_LOSS를 사용합니다.  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료하려면 REMOVE SECONDARY ON SERVER를 사용합니다.  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>활성 지역 복제 모니터링 시스템 보기 
 
|명령 |설명|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|Azure SQL Database 서버의 각 데이터베이스에 대한 모든 기존 복제 링크에 대한 정보를 반환합니다. |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |지정된 SQL Database의 복제 링크에 대한 마지막 복제 시간, 마지막 복제 지연 및 기타 정보를 가져옵니다. |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | 복제 링크의 상태를 비롯한 모든 데이터베이스 작업에 대한 상태를 표시합니다. | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | 커밋된 모든 트랜잭션이 복제되고 활성 보조 데이터베이스에서 확인될 때까지 애플리케이션이 대기하도록 합니다. |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>SQL Database 장기 보존 백업 마이그레이션

지역 복제 또는 BACPAC 파일을 사용하여 데이터베이스를 마이그레이션하면 Azure 독일에 있을 수 있는 장기 보존 백업이 복사되지 않습니다. 기존 장기 보존 백업을 대상 글로벌 Azure 지역으로 마이그레이션하려면 COPY 장기 보존 백업 절차를 사용할 수 있습니다. 

>[!Note]
>여기에 설명된 LTR 백업 복사 메서드는 Azure 독일에서 글로벌 Azure로 LTR 백업만 복사할 수 있습니다. 이러한 방법을 사용하여 PITR 백업을 복사하는 것은 지원되지 않습니다. 
>

### <a name="pre-requisites"></a>필수 구성 요소

1. 백업 복사를 시작하기 전에 글로벌 Azure에서 LTR 백업을 복사하는 대상 데이터베이스가 있어야 합니다. 먼저 [활성 지역 복제](#migrate-sql-database-using-active-geo-replication)를 사용하여 원본 데이터베이스를 마이그레이션한 다음, LTR 백업 복사를 시작하는 것이 좋습니다. 이렇게 하면 데이터베이스 백업이 올바른 대상 데이터베이스에 복사됩니다. 삭제된 데이터베이스의 LTR 백업을 통해 복사하는 경우에는 이 단계가 필요하지 않습니다. 삭제된 데이터베이스의 LTR 백업을 복사할 때 대상 지역에 더미 DatabaseID가 만들어집니다. 
2. 이 [PowerShell Az 모듈](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview)을 설치합니다.
3. 시작하기 전에 필요한 [Azure RBAC 역할](../azure-sql/database/long-term-backup-retention-configure.md#azure-roles-to-manage-long-term-retention)이 **구독** 또는 **리소스 그룹** 범위에서 부여되었는지 확인합니다. 참고: 삭제된 서버에 속한 LTR 백업에 액세스하려면 해당 서버의 구독 범위에서 권한이 부여되어야 합니다. . 


### <a name="limitations"></a>제한 사항  

- 장애 조치(failover) 그룹은 지원되지 않습니다. 즉, Azure 독일 데이터베이스를 마이그레이션하는 고객은 장애 조치(failover) 중에 연결 문자열을 직접 관리해야 합니다.
- Azure Portal, Azure Resource Manager API, PowerShell 또는 CLI가 지원되지 않습니다. 즉, 각 Azure 독일 마이그레이션은 T-SQL을 통해 활성 지역 복제 설정 및 장애 조치(failover)를 관리해야 합니다.
- 고객은 Azure 독일의 데이터베이스에 대해 글로벌 Azure에서 여러 지역 보조를 만들 수 없습니다.
- 지역 보조 만들기는 Azure 독일 지역에서 시작해야 합니다.
- 고객은 Azure 독일에서 글로벌 Azure로만 데이터베이스를 마이그레이션할 수 있습니다. 현재 다른 클라우드 간 마이그레이션은 지원되지 않습니다. 
- Azure 독일 사용자 데이터베이스의 Azure AD 사용자는 마이그레이션되지만 마이그레이션된 데이터베이스가 상주하는 새 Azure AD 테넌트에서 사용할 수 없습니다. 이러한 사용자를 활성화하려면 해당 사용자를 수동으로 삭제하고 새로 마이그레이션된 데이터베이스가 상주하는 새 Azure AD 테넌트에서 사용 가능한 현재 Azure AD 사용자를 사용하여 다시 만들어야 합니다.  


### <a name="copy-long-term-retention-backups-using-powershell"></a>PowerShell을 사용하여 장기 보존 백업 복사

Azure 독일에서 Azure 글로벌 지역으로 장기 보존 백업을 복사하는 데 사용할 수 있는 새로운 PowerShell 명령 **Copy-AzSqlDatabaseLongTermRetentionBackup** 이 도입되었습니다. 

1. **백업 이름을 사용하여 LTR 백업 복사** 다음 예제에서는 backupname을 사용하여 Azure 독일에서 Azure 글로벌 지역으로 LTR 백업을 복사하는 방법을 보여줍니다.  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. **백업 resourceID를 사용하여 LTR 백업 복사** 다음 예제에서는 resourceID를 사용하여 Azure 독일에서 Azure 글로벌 지역으로 LTR 백업을 복사하는 방법을 보여줍니다. 이 예는 삭제된 데이터베이스의 백업을 복사하는 데에도 사용할 수 있습니다. 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>제한 사항  

- [PITR(특정 시점 복원)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) 백업은 주 데이터베이스에서만 수행되며 의도된 기능입니다. Geo-DR을 사용하여 Azure 독일의 데이터베이스를 마이그레이션할 때 PITR 백업은 장애 조치(failover) 후 새로운 주 데이터베이스에서 발생하기 시작합니다. 하지만, 기존 PITR 백업(Azure 독일의 이전 주 데이터베이스)은 마이그레이션되지 않습니다. 특정 시점 복원 시나리오를 지원하기 위해 PITR 백업이 필요한 경우 Azure 독일의 PITR 백업에서 데이터베이스를 복원한 다음, 복구된 데이터베이스를 글로벌 Azure로 마이그레이션해야 합니다. 
- 장기 보존 정책은 데이터베이스로 마이그레이션되지 않습니다. Azure 독일의 데이터베이스에 [LTR(장기 보존)](../azure-sql/database/long-term-retention-overview.md) 정책이 있으면 마이그레이션 후 새 데이터베이스에서 LTR 정책을 수동으로 복사하고 다시 만들어야 합니다. 


### <a name="requesting-access"></a>액세스 권한 요청

지역 복제를 사용하여 Azure 독일에서 글로벌 Azure로 데이터베이스를 마이그레이션하려면 클라우드 간 마이그레이션을 성공적으로 구성하기 위해 *Azure* 독일에서 구독을 사용하도록 설정해야 합니다.

Azure 독일 구독을 사용하도록 설정하려면 다음 링크를 사용하여 마이그레이션 지원 요청을 만들어야 합니다.   

1. 다음 [마이그레이션 지원 요청](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D)으로 이동합니다.

2. 기본 탭에서 *Geo-DR migration* 을 **요약** 으로 입력한 후 **다음: 솔루션** 을 선택합니다.
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text="새 지원 요청 양식":::

3. **권장되는 단계** 를 검토한 후 **다음: 세부 정보** 를 선택합니다. 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="필요한 지원 요청 정보":::

4. 세부 정보 페이지에서 다음을 제공합니다.

   1. 설명 상자에 마이그레이션할 글로벌 Azure 구독 ID를 입력합니다. 데이터베이스를 둘 이상의 구독에 마이그레이션하려면 데이터베이스를 마이그레이션할 글로벌 Azure ID 목록을 추가합니다.
   1. 연락처 정보(이름, 회사 이름, 이메일 또는 전화번호)를 제공합니다.
   1. 양식을 작성한 후 **다음: 검토 + 만들기** 를 선택합니다.

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="지원 요청 세부 정보":::


5. 지원 요청을 검토한 다음, **만들기** 를 선택합니다. 


요청이 처리되면 연락을 받게 됩니다.



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB 데이터 마이그레이션 도구를 사용하여 Azure Cosmos DB로 데이터를 마이그레이션할 수 있습니다. Azure Cosmos DB 데이터 마이그레이션 도구는 JSON 파일, MongoDB, SQL Server, CSV 파일, Azure Table Storage, Amazon DynamoDB, HBase 및 Azure Cosmos 컨테이너를 비롯한 다양한 원본에서 Azure Cosmos DB로 데이터를 가져오는 오픈 소스 솔루션입니다.


Azure Cosmos DB 데이터 마이그레이션 도구는 그래픽 인터페이스 도구 또는 명령줄 도구로 사용할 수 있습니다. 소스 코드는 [Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub 리포지토리에서 사용할 수 있습니다. [컴파일된 버전의 도구](https://www.microsoft.com/download/details.aspx?id=46436)는 Microsoft 다운로드 센터에서 사용할 수 있습니다.

Azure Cosmos DB 리소스를 마이그레이션하려면 다음 단계를 완료하는 것이 좋습니다.

1. 애플리케이션 가동 시간 요구 사항 및 계정 구성을 검토하여 최상의 실행 계획을 결정합니다.
1. 데이터 마이그레이션 도구를 실행하여 Azure 독일에서 새 지역으로 계정 구성을 복제합니다.
1. 유지 관리 기간을 사용할 수 있는 경우 데이터 마이그레이션 도구를 실행하여 원본에서 대상으로 데이터를 복사합니다.
1. 유지 관리 기간을 사용할 수 없는 경우 도구를 실행하여 원본에서 대상으로 데이터를 복사한 후 다음 단계를 완료하세요.
   1. 구성 기반 접근 방식을 사용하여 애플리케이션에서 읽기/쓰기를 변경합니다.
   1. 최초 동기화를 완료합니다.
   1. 증분 동기화를 설정하고 변경 피드를 파악합니다.
   1. 새 계정에 대한 읽기를 가리키고 애플리케이션의 유효성을 검사합니다.
   1. 이전 계정에 대한 쓰기를 중지하고 변경 피드가 발생되었는지 확인한 다음, 새 계정에 대한 쓰기를 가리킵니다.
   1. 도구를 중지하고 이전 계정을 삭제합니다.
1. 도구를 실행하여 데이터가 이전 계정과 새 계정 간에 일관성이 있는지 확인합니다.

추가 정보는 다음 항목을 참조하세요.

- 데이터 마이그레이션 도구를 사용하는 방법을 알아보려면 [자습서: 데이터 마이그레이션 도구를 사용하여 Azure Cosmos DB로 데이터 마이그레이션](../cosmos-db/import-data.md)을 참조하세요.
- Cosmos DB에 대해 알아보려면 [Azure Cosmos DB 시작](../cosmos-db/introduction.md)을 참조하세요.


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Azure Cache for Redis 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하려는 경우 몇 가지 옵션이 있습니다. 선택하는 옵션은 요구 사항에 따라 다릅니다.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>옵션 1: 데이터 손실 허용, 새 인스턴스 만들기

이 방식은 다음 두 조건에 모두 해당하는 경우 가장 적합합니다.

- Azure Cache for Redis를 임시 데이터 캐시로 사용합니다.
- 애플리케이션이 캐시 데이터를 새 지역에 자동으로 다시 채웁니다.

데이터 손실을 허용하면서 마이그레이션하고 새 인스턴스를 만들려면 다음을 수행합니다.

1. 새 대상 지역에 새 Azure Cache for Redis 인스턴스를 만듭니다.
1. 새 지역에서 새 인스턴스를 사용하도록 애플리케이션을 업데이트합니다.
1. 원본 지역에서 이전 Azure Cache for Redis 인스턴스를 삭제합니다.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>옵션 2: 원본 인스턴스에서 대상 인스턴스로 데이터 복사

Azure Cache for Redis 팀의 멤버가 Azure Cache for Redis 인스턴스에서 다른 인스턴스로 데이터를 복사하는(가져오기 또는 내보내기 기능이 필요하지 않은) 오픈 소스 도구를 작성했습니다. 이 도구에 대한 자세한 내용은 다음 단계에서 4단계를 참조하세요.

원본 인스턴스에서 대상 인스턴스로 데이터를 복사하려면:

1. 원본 지역에서 VM을 만듭니다. Azure Cache for Redis의 데이터 세트가 큰 경우 복사 시간을 최소화하려면 비교적 강력한 VM 크기를 선택해야 합니다.
1. 대상 지역에 새 Azure Cache for Redis 인스턴스를 만듭니다.
1. **대상** 인스턴스에서 데이터를 플러시합니다. (**원본** 인스턴스에서 플러시하지 않도록 하세요. 플러시는 복사 도구가 대상 위치의 기존 키를 덮어쓰지 않기 때문에 필요합니다.
1. 다음 도구를 사용하여 원본 Azure Cache for Redis 인스턴스에서 대상 Azure Cache for Redis 인스턴스로 데이터를 자동으로 복사합니다. [도구 원본](https://github.com/deepakverma/redis-copy) 및 [도구 다운로드](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)

> [!NOTE]
> 이 프로세스는 데이터 세트의 크기에 따라 시간이 오래 걸릴 수 있습니다.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>옵션 3: 원본 인스턴스에서 내보내기, 대상 인스턴스로 가져오기

이 방식은 프리미엄 계층에서만 사용할 수 있는 기능을 활용합니다.

원본 인스턴스에서 내보내고 대상 인스턴스로 가져오려면:

1. 대상 지역에 새 프리미엄 계층 Azure Cache for Redis 인스턴스를 만듭니다. 원본 Azure Cache for Redis 인스턴스와 동일한 크기를 사용합니다.
1. [원본 캐시에서 데이터를 내보내](../azure-cache-for-redis/cache-how-to-import-export-data.md)거나 [Export-AzRedisCache PowerShell cmdlet](/powershell/module/az.rediscache/export-azrediscache)을 사용합니다.

   > [!NOTE]
   > 내보내기 Azure Storage 계정은 캐시 인스턴스와 동일한 지역에 있어야 합니다.

1. 내보낸 Blob을 대상 지역의 스토리지 계정에 복사합니다(예: AzCopy 사용).
1. [대상 캐시로 데이터를 가져오거](../azure-cache-for-redis/cache-how-to-import-export-data.md)나 [Import-AzRedisCAche PowerShell cmdlet](/powershell/module/az.rediscache/import-azrediscache)을 사용합니다.
1. 대상 Azure Cache for Redis 인스턴스를 사용하도록 애플리케이션을 다시 구성합니다.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>옵션 4: 두 개의 Azure Cache for Redis 인스턴스에 데이터 쓰기, 한 인스턴스에서 읽기

이 방식의 경우 애플리케이션을 수정해야 합니다. 애플리케이션은 데이터를 둘 이상의 캐시 인스턴스에 써야 하고 캐시 인스턴스 중 하나에서 읽어야 합니다. 이 방식은 Azure Cache for Redis에 저장된 데이터가 다음 조건을 충족하는 경우 적합합니다.
- 데이터가 정기적으로 새로 고쳐집니다. 
- 모든 데이터는 대상 Azure Cache for Redis 인스턴스에 기록됩니다.
- 모든 데이터를 새로 고칠 시간이 충분합니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure Cache for Redis 개요](../azure-cache-for-redis/cache-overview.md)를 검토하세요.

## <a name="postgresql-and-mysql"></a>PostgreSQL 및 MySQL

자세한 내용은 [PostgreSQL](../postgresql/index.yml) 및 [MySQL](../mysql/index.yml)의 "데이터 백업 및 마이그레이션" 섹션에 있는 문서를 참조하세요.

![PostgreSQL 및 MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)
