---
title: Azure SQL Managed Instance 장기 백업 보존
description: Azure Portal 및 PowerShell을 사용하여 별도의 Azure SQL Managed Instance용 저장소 컨테이너에 자동화된 백업을 저장하고 복원하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 09/12/2021
ms.openlocfilehash: 25040c5017a4d0a990d9b1fddb342cab94c97e85
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239381"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention"></a>Azure SQL Managed Instance 장기 백업 보존 관리
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance에서는 [장기 백업 보존](../database/long-term-retention-overview.md) 정책(LTR)을 공개 미리 보기 기능으로 구성할 수 있습니다. 이를 통해 최대 10년 동안 별도의 Azure Blob Storage 컨테이너에 데이터베이스 백업을 자동으로 보존할 수 있습니다. 그런 다음 Azure Portal과 PowerShell에서 이러한 백업을 사용하여 데이터베이스를 복구할 수 있습니다.

   > [!IMPORTANT]
   > 관리되는 인스턴스에 대한 LTR은 현재 Azure 공용 지역의 공개 미리 보기에서 사용할 수 있습니다. 

다음 섹션에서는 Azure Portal, PowerShell 및 Azure CLI를 사용 하 여 장기 백업 보존을 구성 하 고, azure SQL 저장소에서 백업을 보고, azure SQL 저장소의 백업에서 복원 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="portal"></a>[포털](#tab/portal)

활성화된 Azure 구독.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 대 한 환경을 준비 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후 모든 개발은 Az.Sql 모듈에서 수행됩니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

**Get-AzSqlInstanceDatabaseLongTermRetentionBackup** 및 **Restore-AzSqlInstanceDatabase** 의 경우, 다음 역할 중 하나가 필요합니다.

- 구독 소유자 역할 또는
- Managed Instance Contributor 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

**Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** 의 경우, 다음 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Managed Instance 기여자 역할에는 LTR 백업을 삭제할 수 있는 권한이 없습니다.

Azure RBAC 권한은 *구독* 또는 *리소스 그룹* 범위에서 부여할 수 있습니다. 그러나 삭제된 인스턴스에 속한 LTR 백업에 액세스하려면 해당 인스턴스의 구독 범위에서 사용 권한을 부여해야 합니다.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

---

## <a name="create-long-term-retention-policies"></a>장기 보존 정책 만들기

서비스 계층의 보존 기간보다 긴 기간 동안 [자동화된 백업을 유지](../database/long-term-retention-overview.md)하도록 SQL Managed Instance를 구성할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

1. Azure Portal에서 관리되는 인스턴스를 선택한 다음, **백업** 을 클릭합니다. **보존 정책** 탭에서 장기 백업 보존 정책을 설정하거나 수정하려는 데이터베이스를 선택합니다. 선택하지 않은 데이터베이스에는 변경 내용이 적용되지 않습니다. 

   ![백업 관리 링크](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. **정책 구성** 창에서 원하는 보존 기간을 주간, 월간 또는 매년 백업으로 지정합니다. 장기 백업 보존을 설정하지 않는다는 의미로는 보존 기간에 ‘0’을 선택합니다.

   ![정책 구성](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. 완료되면 **적용** 을 클릭합니다.

> [!IMPORTANT]
> 장기 백업 보존 정책을 사용하도록 설정하면 첫 번째 백업이 표시되고 복원하는 데 최대 7일이 걸릴 수 있습니다. LTR 백업 흐름에 대 한 자세한 내용은 [장기 백업 보존](../database/long-term-retention-overview.md)을 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Az sql midb show](/cli/azure/sql/midb#az_sql_midb_show) 명령을 실행 하 여 Managed Instance 데이터베이스에 대 한 세부 정보를 가져옵니다.

    ```azurecli
    az sql midb show /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --subscription mysubscription
    ```

2. [Az sql midb ltr-policy set](/cli/azure/sql/midb/ltr-policy#az_sql_midb_ltr_policy_set) 명령을 실행 하 여 ltr 정책을 만듭니다. 다음 예에서는 주별 백업에 대해 12 주 동안 장기 보존 정책을 설정 합니다.

    ```azurecli
    az sql midb ltr-policy set /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --weekly-retention "P12W"
    ```

    이 예에서는 매주 백업에 대 한 12 주, 매년 백업에 대해 5 년, 매년 LTR 백업을 수행할 때 4 월 15 일에 대 한 보존 정책을 설정 합니다.

    ```azurecli
    az sql midb ltr-policy set /
    --resource-group mygroup /
    --managed-instance myinstance /
    --name mymanageddb /
    --weekly-retention "P12W" /
    --yearly-retention "P5Y" /
    --week-of-year 16
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

---

## <a name="view-backups-and-restore-from-a-backup"></a>백업 보기 및 백업에서 복원

# <a name="portal"></a>[포털](#tab/portal)

LTR 정책으로 특정 데이터베이스를 보존한 백업을 확인하고 해당 백업을 복원합니다.

1. Azure Portal에서 관리되는 인스턴스를 선택한 다음, **백업** 을 클릭합니다. **사용 가능한 백업** 탭에서 사용 가능한 백업을 보려는 데이터베이스를 선택합니다. **관리** 를 클릭합니다.

   ![데이터베이스 선택](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. **백업 관리** 창에서 사용 가능한 백업을 검토합니다.

   ![백업 보기](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. 복원할 백업을 선택하고 **복원** 을 클릭한 다음, 복원 페이지에서 새 데이터베이스 이름을 지정합니다. 백업 및 원본은 이 페이지에 미리 채워집니다. 

   ![복원을 위한 백업 선택](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![복원](./media/long-term-backup-retention-configure/ltr-restore.png)

1. **검토 + 만들기** 를 클릭하여 복원 세부 정보를 검토합니다. 그런 다음, **만들기** 를 클릭하여 선택한 백업에서 데이터베이스를 복원합니다.

1. 도구 모음에서 알림 아이콘을 클릭하여 복원 작업의 상태를 봅니다.

   ![복원 작업 진행률](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 복원 작업이 완료되면 **Managed Instance 개요** 페이지를 열어 새로 복원된 데이터베이스를 봅니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하여 [복원된 데이터베이스에서 일부 데이터를 추출하여 기존 데이터베이스로 복사 또는 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경](../database/recovery-using-backups.md#point-in-time-restore)하기와 같은 필요한 작업을 수행할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="view-ltr-policies"></a>LTR 정책 보기

[Az sql midb ltr-policy show](/cli/azure/sql/midb/ltr-policy#az_sql_midb_ltr_policy_show) 명령을 실행 하 여 인스턴스 내의 단일 데이터베이스에 대 한 ltr 정책을 볼 수 있습니다.

```azurecli
az sql midb ltr-policy show \
    --resource-group mygroup \
    --managed-instance myinstance \
    --name mymanageddb
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

[Az sql midb ltr-backup list](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_list) 명령을 사용 하 여 인스턴스 내에서 ltr 백업을 볼 수 있습니다.

```azurecli
az sql midb ltr-backup list \
    --resource-group mygroup \
    --location eastus2 \
    --managed-instance myinstance
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

[Az sql midb ltr-backup delete](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_delete) 명령을 실행 하 여 ltr 백업을 제거 합니다. [Az sql midb ltr-backup list](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_list) 를 실행 하 여 백업을 가져올 수 있습니다 `name` .

```azurecli
az sql midb ltr-backup delete \
    --location eastus2 \
    --managed-instance myinstance \
    --database mymanageddb \
    --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000"
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 인스턴스를 삭제한 후 LTR 백업을 삭제하려면 구독 범위 권한이 있어야 합니다. ‘장기 보존 백업 삭제’ 작업으로 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

[Az sql midb ltr-backup restore](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_restore) 명령을 실행 하 여 ltr 백업에서 데이터베이스를 복원 합니다. [Az sql midb ltr-backup show](/cli/azure/sql/midb/ltr-backup#az_sql_midb_ltr_backup_show) 를 실행 하 여을 가져올 수 있습니다 `backup-id` .

1. `backup-id`나중에 사용할 명령을 사용 하 여에 대 한 변수를 만듭니다 `az sql db ltr-backup show` .

   ```azurecli
    get_backup_id=$(az sql midb ltr-backup show
       --location eastus2 \
       --managed-instance myinstance \
       --database mydb \
       --name "3214b3fb-fba9-43e7-96a3-09e35ffcb336;132292152080000000" \
       --query 'id' \
       --output tsv)
   ```
2. LTR 백업에서 데이터베이스 복원

    ```azurecli
    az sql midb ltr-backup restore \
        --dest-database targetmidb \
        --dest-mi myinstance \
        --dest-resource-group mygroup \
        --backup-id $get_backup_id
    ```

> [!IMPORTANT]
> 인스턴스가 삭제된 후 LTR 백업에서 복원하려면 인스턴스의 구독으로 범위가 지정된 사용 권한이 있어야 하며 해당 구독은 활성 상태여야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](../database/recovery-using-backups.md#point-in-time-restore)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="view-ltr-policies"></a>LTR 정책 보기

이 예에서는 단일 데이터베이스에 대 한 인스턴스 내에서 LTR 정책을 나열 하는 방법을 보여 줍니다.

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

이 예에서는 인스턴스의 모든 데이터베이스에 대 한 LTR 정책을 나열 하는 방법을 보여 줍니다.

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>LTR 정책 지우기

이 예에서는 데이터베이스에서 LTR 정책을 지우는 방법을 보여 줍니다.

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>LTR 백업 보기

이 예제에서는 인스턴스 내의 LTR 백업을 나열하는 방법을 보여줍니다.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>LTR 백업 삭제

이 예제에서는 백업 목록에서 LTR 백업을 삭제하는 방법을 보여줍니다.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR 백업을 삭제하면 되돌릴 수 없습니다. 인스턴스를 삭제한 후 LTR 백업을 삭제하려면 구독 범위 권한이 있어야 합니다. ‘장기 보존 백업 삭제’ 작업으로 필터링하여 Azure Monitor에서 각 삭제에 대한 알림을 설정할 수 있습니다. 활동 로그에는 요청한 사람과 시기에 대한 정보가 포함되어 있습니다. 자세한 지침은 [활동 로그 경고 만들기](../../azure-monitor/alerts/alerts-activity-log.md)를 참조하세요.

### <a name="restore-from-ltr-backups"></a>LTR 백업에서 복원

이 예제에서는 LTR 백업에서 복원하는 방법을 보여줍니다. 해당 인터페이스는 변경되지 않았지만 해당 리소스 ID 매개 변수에는 이제 LTR 백업 리소스 ID가 필요합니다.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> 인스턴스가 삭제된 후 LTR 백업에서 복원하려면 인스턴스의 구독으로 범위가 지정된 사용 권한이 있어야 하며 해당 구독은 활성 상태여야 합니다. 또한 선택적인 -ResourceGroupName 매개 변수도 생략해야 합니다.

> [!NOTE]
> 여기에서 SQL Server Management Studio를 사용하여 복원된 데이터베이스에 연결하고, 이 데이터베이스에서 약간의 데이터를 추출하여 기존 데이터베이스에 복사하거나 기존 데이터베이스를 삭제하고 복원된 데이터베이스 이름을 기존 데이터베이스 이름으로 변경하는 등 필요한 작업을 수행할 수 있습니다. [특정 시점 복원](../database/recovery-using-backups.md#point-in-time-restore)을 참조하세요.

---

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](../database/automated-backups-overview.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](../database/long-term-retention-overview.md) 참조
