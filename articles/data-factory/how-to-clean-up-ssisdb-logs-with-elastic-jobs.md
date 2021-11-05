---
title: SSISDB 로그를 자동으로 정리하는 방법
description: 이 문서에서는 Azure Data Factory, Azure SQL Managed Instance 에이전트 또는 탄력적 데이터베이스 작업을 통해 관련 SSISDB 저장 프로시저를 자동으로 호출하여 SSISDB에 저장된 SSIS 프로젝트 배포 및 패키지 실행 로그를 정리하는 방법을 설명합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78b6c754fe2b49ceabf940aa76b86ddd90dfc91e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843662"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>SSISDB 로그를 자동으로 정리하는 방법

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF(Azure Data Factory)에서 Azure SSIS(SQL Server Integration Services) IR(통합 런타임)을 프로비저닝하면 다음 위치에 배포된 SSIS 패키지를 실행하는 데 사용할 수 있습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)

프로젝트 배포 모델에서는 Azure-SSIS IR이 SSIS 프로젝트를 SSISDB에 배포하고, SSISDB에서 실행할 SSIS 패키지를 가져오고, 패키지 실행 로그를 SSISDB에 다시 씁니다. SSISDB는 SSIS 작업 및 IR 작업 로그를 저장하는 데도 사용됩니다. 누적된 로그를 관리할 수 있도록 ADF, Azure SQL Managed Instance 에이전트 또는 탄력적 데이터베이스 작업을 통해 일정에 따라 자동으로 호출할 수 있는 관련 SSISDB 속성 및 저장 프로시저가 제공되었습니다.

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>SSISDB 로그 정리 속성 및 저장 프로시저
SSIS 패키지 실행 로그를 관리하려면 SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Database 서버/Managed Instance에 호스트되는 SSISDB에 연결하여 SSISDB 로그 정리 속성을 구성하면 됩니다. [SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database)을 참조하세요. 연결한 후에는 SSMS의 **개체 탐색기** 창에서 **Integration Services 카탈로그** 노드를 확장하고, **SSISDB** 하위 노드를 마우스 오른쪽 단추로 클릭하고, **속성** 메뉴 항목을 선택하여 **카탈로그 속성** 대화 상자를 열 수 있습니다. **카탈로그 속성** 대화 상자에서 다음과 같은 SSISDB 로그 정리 속성을 찾을 수 있습니다.

- **주기적으로 로그 정리**: 패키지 실행 로그를 정리합니다. 기본적으로 *True* 로 설정됩니다.
- **보존 기간(일)** : 보존되는 로그의 최대 보존 기간(일)을 지정합니다. 기본적으로 *365* 로 설정되며 관련 SSISDB 저장 프로시저를 호출하면 오래된 로그가 삭제됩니다.
- **주기적으로 이전 버전 제거**: 저장된 프로젝트 버전을 정리합니다. 기본적으로 *True* 로 설정됩니다.
- **프로젝트당 최대 버전 수**: 저장된 프로젝트 버전의 최대 수를 지정합니다. 기본적으로 *10* 으로 설정되며, 관련 SSISDB 저장 프로시저가 호출되면 이전 버전이 삭제됩니다.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png" alt-text="SSISDB 로그 정리 속성":::

SSISDB 로그 정리 속성이 구성되면 관련 SSISDB 저장 프로시저 `[internal].[cleanup_server_retention_window_exclusive]`를 호출하여 SSIS 패키지 실행 로그를 정리할 수 있습니다.

SSIS 작업 로그를 정리하려면 관련 SSISDB 저장 프로시저 `[internal].[cleanup_completed_jobs_exclusive]`를 호출하면 됩니다. 보존 기간은 기본적으로 60분으로 설정되고, 저장 프로시저가 호출될 때 이전 로그는 삭제됩니다.

SSIS IR 작업 로그를 정리하려면 관련 SSISDB 저장 프로시저 `[internal].[cleanup_expired_worker]`를 호출하면 됩니다. 보존 기간은 기본적으로 168시간으로 설정되고, 저장 프로시저가 호출될 때 이전 로그는 삭제됩니다.

이러한 SSISDB 저장 프로시저는 다음과 같은 다양한 SSISDB 테이블을 정리합니다.

| SSISDB 저장 프로시저 | 정리하는 SSISDB 테이블 |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[event_message_context_scaleout]`<br/>`[internal].[event_messages_scaleout]`<br/>`[internal].[executable_statistics]`<br/>`[internal].[execution_component_phases]`<br/>`[internal].[execution_data_statistics]`<br/>`[internal].[execution_data_taps]`<br/>`[internal].[execution_parameter_values]`<br/>`[internal].[execution_parameter_values_noncatalog]`<br/>`[internal].[execution_property_override_values]`<br/>`[internal].[execution_property_override_values_noncatalog]`<br/>`[internal].[executions]`<br/>`[internal].[executions_noncatalog]`<br/>`[internal].[extended_operation_info]`<br/>`[internal].[operation_messages]`<br/>`[internal].[operation_messages_scaleout]`<br/>`[internal].[operation_permissions]`<br/>`[internal].[operations]`<br/>`[internal].[validations]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[job_worker_agents]`<br/>`[internal].[jobs]`<br/>`[internal].[tasks]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |

이러한 SSISDB 저장 프로시저는 ADF, Azure SQL Managed Instance 에이전트 또는 탄력적 데이터베이스 작업을 통해 일정에 따라 자동으로 호출할 수도 있습니다.

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>ADF를 통해 자동으로 SSISDB 로그 정리
Azure SQL 데이터베이스 서버/Managed Instance를 사용하여 SSISDB를 호스트하는지 여부에 관계없이, 항상 ADF를 사용하여 일정에 따라 SSISDB 로그를 자동으로 정리할 수 있습니다. 이렇게 하려면 관련 SSISDB 저장 프로시저를 호출하는 단일 SQL 실행 태스크가 들어 있는 포함된 패키지를 사용하여 ADF 파이프라인에서 SSIS 패키지 실행 작업을 준비하면 됩니다. [Run Any SQL Anywhere in 3 Easy Steps with SSIS in Azure Data Factory](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244) 블로그의 예제 4)를 참조하세요.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png" alt-text="ADF를 통해 SSISDB 로그 정리":::

**SQLStatementSource** 매개 변수의 경우 `EXEC internal.cleanup_server_retention_window_exclusive`를 입력하여 SSIS 패키지 실행 로그를 정리할 수 있습니다. 

SSIS 작업 로그를 정리하려면 `EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]`를 추가하면 됩니다. 

SSIS IR 작업 로그를 정리하려면 `EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] `를 추가하면 됩니다.

ADF 파이프라인이 준비되면 일정 트리거를 연결하여 주기적으로 실행할 수 있습니다. [일정에 따라 ADF 파이프라인을 트리거하는 방법](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)을 참조하세요.

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance 에이전트를 통해 자동으로 SSISDB 로그 정리
Azure SQL Managed Instance를 사용하여 SSISDB를 호스트하는 경우에는 기본 제공 작업 오케스트레이터/스케줄러인 Azure SQL Managed Instance 에이전트를 사용하여 일정에 따라 자동으로 SSISDB 로그를 정리할 수도 있습니다. Azure SQL Managed Instance에서 최근에 SSISDB를 만든 분들을 위해, Azure SQL Managed Instance 에이전트 아래에 SSIS 패키지 실행 로그를 명확하게 정리할 수 있는 **SSIS 서버 유지 관리 작업** 이라는 T-SQL 작업을 만들어 두었습니다. 기본적으로 사용되지 않으며 매일 실행되는 일정으로 구성됩니다.  사용하도록 설정하거나 일정을 다시 구성하려면 SSMS를 사용하여 Azure SQL Managed Instance에 연결하면 됩니다. 연결되면 SSMS의 **개체 탐색기** 창에서 **SQL Server 에이전트** 노드를 확장하고, **작업** 하위 노드를 확장하고, **SSIS 서버 유지 관리 작업** 을 두 번 클릭하여 사용하도록 설정/다시 구성할 수 있습니다.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png" alt-text="Azure SQL Managed Instance 에이전트를 통해 SSISDB 로그 정리":::

Azure SQL Managed Instance 에이전트에서 아직 **SSIS 서버 유지 관리 작업** 을 만들지 않은 경우 Azure SQL Managed Instance에서 다음 T-SQL 스크립트를 실행하여 수동으로 추가할 수 있습니다.

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

관련 SSISDB 저장 프로시저를 호출하여 SSIS 작업/IR 작업 로그를 추가로 정리하도록 기존 **SSIS 서버 유지 관리 작업** 을 구성하거나 위의 T-SQL 스크립트를 수정할 수도 있습니다.

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>탄력적 데이터베이스 작업을 통해 SSISDB 로그 정리
Azure SQL Database 서버를 사용하여 SSISDB를 호스트하는 경우에는 기본 제공 작업 오케스트레이터/스케줄러가 없으므로 ADF와 같은 외부 구성 요소(위 참조) 또는 탄력적 데이터베이스 작업(이 섹션의 나머지 부분 참조)을 사용하여 일정에 따라 자동으로 SSISDB 로그를 정리해야 합니다.

탄력적 데이터베이스 작업은 데이터베이스 또는 데이터베이스 그룹에 대한 작업을 자동화하고 실행할 수 있는 Azure 서비스입니다. Azure Portal, Azure PowerShell, T-SQL 또는 REST API를 사용하여 이러한 작업을 예약하고, 실행하고, 모니터링할 수 있습니다. 탄력적 데이터베이스 작업을 사용하여 로그를 한 번만 또는 일정에 따라 정리하는 관련 SSISDB 저장 프로시저를 호출할 수 있습니다. 지나친 데이터베이스 부하를 방지하기 위해 SSISDB 리소스 사용량을 기준으로 일정 간격을 선택할 수 있습니다.

자세한 내용은 [Elastic Database 작업을 사용하여 데이터베이스 그룹 관리](../azure-sql/database/elastic-jobs-overview.md)를 참조하세요.

다음 섹션에서는 보존 기간을 벗어난 SSISDB 로그를 제거하는 관련 SSISDB 저장 프로시저 `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]`를 호출하는 방법을 설명합니다.

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>Azure PowerShell을 사용하여 탄력적 데이터베이스 작업 구성

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

다음 Azure PowerShell 스크립트는 고객이 선택한 SSISDB 로그 정리 저장 프로시저를 호출하는 새 탄력적 작업을 만듭니다. 자세한 내용은 [PowerShell을 사용하여 탄력적 작업 에이전트 만들기](../azure-sql/database/elastic-jobs-powershell-create.md)를 참조하세요.

#### <a name="create-parameters"></a>매개 변수 만들기

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>SSISDB 로그 정리 저장 프로시저 호출

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>T-SQL을 사용하여 탄력적 데이터베이스 작업 구성

다음 T-SQL 스크립트는 고객이 선택한 SSISDB 로그 정리 저장 프로시저를 호출하는 새 탄력적 작업을 만듭니다. 자세한 내용은 [T-SQL을 사용하여 탄력적 데이터베이스 작업 만들기 및 관리](../azure-sql/database/elastic-jobs-tsql-create-manage.md)를 참조하세요.

1. Azure SQL Database의 비어 있는 S0 이상 서비스 계층을 확인하거나 작업 데이터베이스에 사용할 새 서비스 계층을 만듭니다. 그런 다음, [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent)에서 탄력적 작업 에이전트를 만듭니다.

2. 작업 데이터베이스에서, 대상 서버의 SSISDB에 연결하기 위한 자격 증명을 만듭니다.

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. 정리할 SSISDB만 포함하는 대상 그룹을 정의합니다.

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. SSISDB의 로그인에서 SSISDB 로그 정리 사용자를 만들고 SSISDB 로그 정리 저장 프로시저를 호출할 수 있는 권한을 부여합니다. 자세한 지침은 [로그인 관리](../azure-sql/database/logins-create-manage.md)를 참조하세요.

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. 작업을 만들고 SSISDB 로그 정리 저장 프로시저를 호출하는 작업 단계를 추가합니다.

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. 계속하기 전에, 보존 기간을 적절하게 설정해야 합니다. 이 기간을 벗어난 SSISDB 로그는 삭제되며 복구할 수 없습니다. 그런 다음, 작업을 즉시 실행하여 SSISDB 로그 정리를 시작할 수 있습니다.

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. 원한다면 보존 기간을 벗어난 SSISDB 로그를 일정에 따라 삭제할 수 있습니다. 다음과 같이 작업 매개 변수를 구성합니다.

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>Azure Portal을 사용하여 SSISDB 로그 정리 작업 모니터링

Azure Portal에서 SSISDB 로그 정리 작업을 모니터링할 수 있습니다. 각 실행의 상태, 시작 시간 및 종료 시간을 볼 수 있습니다.

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png" alt-text="Azure Portal에서 SSISDB 로그 정리 작업 모니터링":::

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>T-SQL을 사용하여 SSISDB 로그 정리 작업 모니터링

T-SQL을 사용하여 SSISDB 로그 정리 작업의 실행 기록을 볼 수도 있습니다.

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR을 관리하고 모니터링하려면 다음 문서를 참조하세요.

- [Azure-SSIS 통합 런타임 다시 구성](manage-azure-ssis-integration-runtime.md)

- [Azure-SSIS Integration Services 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime)
