---
title: 'Azure SQL Database: 장기 백업 보존 관리'
description: Azure Portal, Azure CLI 및 PowerShell을 사용하여 Azure Storage(최대 10년)에 Azure SQL Database 대한 자동화된 백업을 저장하고 복원하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 12/16/2020
ms.openlocfilehash: 72f92c1659497cda8e4f595df17899a4367d00b9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236247"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database 장기 백업 보존 관리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database를 사용하여 최장 10년간 여러 Azure Blob 스토리지 컨테이너에서 자동으로 백업을 보존하도록 LTR([장기 백업 보존)](long-term-retention-overview.md) 정책을 설정할 수 있습니다. 그런 다음 Azure Portal, Azure CLI 또는 PowerShell을 사용하여 이러한 백업을 사용하여 데이터베이스를 복구할 수 있습니다. 장기 보존 정책은 [Azure SQL 관리형 인스턴스](../managed-instance/long-term-backup-retention-configure.md)에도 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="portal"></a>[포털](#tab/portal)

활성화된 Azure 구독.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 대한 환경을 준비합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

**Get-AzSqlDatabaseLongTermRetentionBackup** 및 **Restore-AzSqlDatabase** 의 경우 다음 역할 중 하나가 필요합니다.

- 구독 소유자 역할 또는
- SQL Server 기여자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

**Remove-AzSqlDatabaseLongTermRetentionBackup** 의 경우 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> SQL Server 기여자 역할에는 LTR 백업을 삭제할 수 있는 권한이 없습니다.

Azure RBAC 권한은 *구독* 또는 *리소스 그룹* 범위에서 부여할 수 있습니다. 그러나 삭제된 서버에 속한 LTR 백업에 액세스하려면 해당 서버의 *구독* 범위에서 권한을 부여해야 합니다.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

---

## <a name="create-long-term-retention-policies"></a>장기 보존 정책 만들기

# <a name="portal"></a>[포털](#tab/portal)

서비스 계층의 보존 기간보다 긴 기간 동안 [자동화된 백업을 유지](long-term-retention-overview.md)하도록 SQL Database를 구성할 수 있습니다.

1. Azure Portal에서 내 서버로 이동하여 **백업** 을 선택합니다. **보존 정책** 탭을 선택하여 백업 보존 설정을 수정합니다.

   ![보존 정책 환경](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. 보존 정책 탭에서 장기 백업 보존 정책을 설정하거나 수정하려는 데이터베이스를 선택합니다. 선택하지 않은 데이터베이스는 영향을 받지 않습니다.

   ![백업 보존 정책을 구성할 데이터베이스 선택](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. **정책 구성** 창에서 원하는 보존 기간을 주간, 월간 또는 매년 백업으로 지정합니다. 장기 백업 보존을 설정하지 않는다는 의미로는 보존 기간에 ‘0’을 선택합니다.

   ![정책 구성 창](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. **적용** 을 선택하여 선택한 데이터베이스 전체에 선택한 보존 설정을 적용합니다.

> [!IMPORTANT]
> 장기 백업 보존 정책을 사용하도록 설정하면 첫 번째 백업이 표시되고 복원하는 데 최대 7일이 걸릴 수 있습니다. LTR 백업 주기에 대한 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az sql db ltr-policy set](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_set) 명령을 실행하여 LTR 정책을 만듭니다. 다음 예에서는 주간 백업에 대해 12주 동안 장기 보존 정책을 설정합니다.

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W"
```

이 예에서는 주 백업의 경우 12주, 연간 백업의 경우 5년, 매년 LTR 백업을 사용할 4월 15일 주에 보존 정책을 설정합니다.

```azurecli
az sql db ltr-policy set \
   --resource-group mygroup \
   --server myserver \
   --name mydb \
   --weekly-retention "P12W" \
   --yearly-retention "P5Y" \
   --week-of-year 16
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

---

## <a name="view-backups-and-restore-from-a-backup"></a>백업 보기 및 백업에서 복원

LTR 정책으로 특정 데이터베이스를 보존한 백업을 확인하고 해당 백업을 복원합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. Azure Portal에서 내 서버로 이동하여 **백업** 을 선택합니다. 특정 데이터베이스에 대하여 사용할 수 있는 LTR 백업을 확인하려면 사용할 수 있는 LTR 백업 열에서 **관리** 를 선택합니다. 선택한 데이터베이스에 대하여 사용할 수 있는 LTR 백업이 포함된 창이 표시됩니다.

   ![사용 가능한 백업 환경](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. 표시되는 **사용 가능한 LTR 백업** 창에서 사용 가능한 백업을 검토합니다. 복원하거나 삭제할 백업을 선택할 수 있습니다.

   ![사용 가능한 LTR 백업 보기](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. 사용 가능한 LTR 백업을 복원하려면, 복원하려는 백업을 선택한 다음 **복원** 을 선택합니다.

   ![사용 가능한 LTR 백업을 복원](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. 새로운 데이터베이스의 이름을 선택한 다음 **검토 + 만들기** 를 선택하여 복원 세부 정보를 검토합니다. **만들기** 를 선택하여 선택한 백업의 데이터 베이스를 복원합니다.

   ![복원 세부 정보 구성](./media/long-term-backup-retention-configure/restore-ltr.png)

1. 도구 모음에서 알림 아이콘을 선택하여 해당 복원 작업의 상태를 조회합니다.

   ![복원 작업 진행률](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 복원 작업이 완료되면 **SQL Database** 페이지를 열어 새로 복원된 데이터베이스를 봅니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-ltr-policies"></a>LTR 정책 보기

[az sql db ltr-policy show](/cli/azure/sql/db/ltr-policy#az_sql_db_ltr_policy_show) 명령을 실행하여 서버의 단일 데이터베이스에 대한 LTR 정책을 봅니다.

```azurecli
az sql db ltr-policy show \
    --resource-group mygroup \
    --server myserver \
    --name mydb
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

[az sql db ltr-backup list](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list) 명령을 사용하여 데이터베이스에 대한 LTR 백업을 나열합니다. 이 명령을 사용하여 다른 명령에서 사용할 매개 변수를 찾을 수 `name` 있습니다.

```azurecli
az sql db ltr-backup list \
   --location eastus2 \
   --server myserver \
   --database mydb
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

[az sql db ltr-backup delete](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_delete) 명령을 실행하여 LTR 백업을 제거합니다. [az sql db ltr-backup list를](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_list) 사용하여 백업 을 찾을 수 `name` 있습니다.

```azurecli
az sql db ltr-backup delete \
   --location eastus2 \
   --server myserver \
   --database mydb \
   --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000"
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 서버를 삭제한 후 LTR 백업을 삭제하려면 구독 범위 권한이 있어야 합니다. ‘장기 보존 백업 삭제’ 작업으로 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

[az sql db ltr-backup restore](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_restore) 명령을 실행하여 LTR 백업에서 데이터베이스를 복원합니다. [az sql db ltr-backup show를](/cli/azure/sql/db/ltr-backup#az_sql_db_ltr_backup_show) 실행하여 를 얻을 수 `backup-id` 있습니다.

1. 나중에 사용할 수 있는 `backup-id` 'az sql db ltr-backup show' 명령을 사용하여 에 대한 변수를 만듭니다.

   ```azurecli
   get_backup_id=$(az sql db ltr-backup show 
       --location eastus2 \
       --server myserver \
       --database mydb \
       --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000" \
       --query 'id' \
       --output tsv)
   ```

2. LTR 백업에서 데이터베이스를 복원합니다.

    ```azurecli
    az sql db ltr-backup restore \
       --dest-database targetdb \
       --dest-server myserver \
       --dest-resource-group mygroup \
       --backup-id $get_backup_id
    ```

> [!IMPORTANT]
> 서버나 리소스 그룹 삭제 후 LTR 백업을 복원하려면 해당 서버의 구독으로 범위가 지정된 권한을 갖춘 채로 해당 구독이 활성 상태여야 합니다. 또한 선택적인 -ResourceGroupName 매개 변수도 생략해야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio 사용하여 복원된 데이터베이스에 연결하여 데이터베이스 교환과 같은 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](recovery-using-backups.md#point-in-time-restore)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="view-ltr-policies"></a>LTR 정책 보기

이 예제에서는 서버 내에서 LTR 정책을 나열하는 방법을 보여 있습니다.

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>LTR 정책 지우기

이 예제에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여줍니다.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

이 예제에서는 서버 내의 LTR 백업을 나열하는 방법을 보여줍니다.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 서버를 삭제한 후 LTR 백업을 삭제하려면 구독 범위 권한이 있어야 합니다. ‘장기 보존 백업 삭제’ 작업으로 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 해당 인터페이스는 변경되지 않았지만 해당 리소스 ID 매개 변수에는 이제 LTR 백업 리소스 ID가 필요합니다.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> 서버나 리소스 그룹 삭제 후 LTR 백업을 복원하려면 해당 서버의 구독으로 범위가 지정된 권한을 갖춘 채로 해당 구독이 활성 상태여야 합니다. 또한 선택적인 -ResourceGroupName 매개 변수도 생략해야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](recovery-using-backups.md#point-in-time-restore)을 참조하세요.

---

## <a name="limitations"></a>제한 사항
- LTR 백업을 복원할 때, 읽기 확장 속성은 비활성화 됩니다. 복원한 데이터베이스의 읽기 확장 속성을 활성화하려면 해당 데이터베이스를 만든 다음 업데이트 하세요.
- 데이터베이스가 탄력적 풀에 있을 때 생성 된 LTR 백업에서 복원 하는 경우 대상 서비스 수준 목표를 지정 해야 합니다. 

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](automated-backups-overview.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](long-term-retention-overview.md) 참조
