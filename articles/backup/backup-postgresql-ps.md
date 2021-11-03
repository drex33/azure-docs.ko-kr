---
title: Azure PowerShell 사용하여 장기 보존으로 Azure Database for PostgreSQL 백업
description: Azure PowerShell 사용하여 Azure Database for PostgreSQL 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47beb64c518bbd55090c9cf6cd50068635310d25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103723"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell 사용하여 Azure PostgreSQL 데이터베이스 백업

이 문서에서는 Azure PowerShell 사용하여 [Azure PostgreSQL 데이터베이스를](../postgresql/overview.md#azure-database-for-postgresql---single-server) 백업하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Backup 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure PostgreSQL 데이터베이스의 백업 구성

- 주문형 백업 작업 실행

Azure PostgreSQL 데이터베이스 지원 시나리오 및 제한에 대한 자세한 내용은 [지원 매트릭스를 참조하세요.](backup-azure-database-postgresql-overview.md#support-matrix)

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

Backup 자격 증명 모음은 Azure Database for PostgreSQL 서버, Azure 디스크 및 Azure Blob과 같이 Azure Backup 지원하는 다양한 새 워크로드에 대한 백업 데이터를 저장하는 Azure의 스토리지 엔터티입니다. 백업 자격 증명 모음은 관리 오버헤드를 최소화하면서 백업 데이터를 구성하는 데 도움이 됩니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

Backup 자격 증명 모음을 구성하기 전에 자격 증명 모음 내에서 데이터의 스토리지 중복성을 선택합니다. 그런 다음 해당 스토리지 중복도 및 위치를 사용하여 백업 자격 증명 모음을 만듭니다.

이 문서에서는 리소스 그룹 *testBkpVaultRG* 아래에 *westus* 지역에 Backup 자격 증명 모음 *TestBkpVault* 를 만듭니다. [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 Backup 자격 증명 모음을 만듭니다. [백업 자격 증명 모음 만들기](./backup-vault-overview.md#create-a-backup-vault)에 대해 자세히 알아보세요.

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

자격 증명 모음을 만든 후 Azure PostgreSQL 데이터베이스를 보호하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

### <a name="understand-postgresql-backup-policy"></a>PostgreSQL 백업 정책 이해

디스크 백업은 하루에 여러 백업을 제공하고 Blob 백업은 트리거 없이 *연속* 백업이지만 PostgreSQL 백업은 보관 보호를 제공합니다. 자격 증명 모음으로 처음 전송되는 백업 데이터는 정의된 규칙 또는 *수명 주기* 에 따라 *보관* 계층으로 이동할 수 있습니다. 이 컨텍스트에서 PostgreSQL에 대한 백업 정책 개체를 이해해 보겠습니다.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType(이 경우 전체 데이터베이스 백업)
         -  초기 데이터 저장소(백업이 처음에 있는 위치)
         -  트리거(백업이 트리거되는 방법)
            -  일정 기반
            -  기본 태그 지정 조건(예약된 모든 백업에 대한 기본 '태그'입니다. 이 태그는 백업을 보존 규칙에 연결합니다.
   -  기본 보존 규칙(기본적으로 초기 데이터 저장소의 모든 백업에 적용되는 규칙)

따라서 이 개체는 트리거되는 백업 유형, 트리거되는 방법(일정을 통해), 태그가 지정된 백업, 백업 위치(데이터 저장소) 및 데이터 저장소에 있는 백업 데이터의 수명 주기를 정의합니다. PostgreSQL에 대한 기본 PowerShell 개체는 매주 *전체* 백업을 트리거한다고 하며, 자격 증명 모음에 도달하여 3개월 동안 저장됩니다.

*보관* 계층을 정책에 추가하려는 경우 데이터를 자격 증명 모음에서 보관으로 이동할 시기, 데이터가 보관에 유지되는 기간 및 _예약된_ 백업 중 보관 가능한 로 *태그를* 지정해야 하는 시점을 결정해야 합니다. 따라서 백업 데이터의 수명 주기를 자격 증명 모음 데이터 저장소에서 보관 데이터 저장소로 정의하고 보관 데이터 저장소에 보관할  기간을 지정하는 *보존 규칙* 를 추가해야 합니다. 그런 다음 예약된 백업을 보관할 수 있는 것으로 표시하는 *태그를* _추가해야 합니다._

결과 PowerShell 개체는 다음과 같습니다.


-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType(이 경우 전체 데이터베이스 백업)
         -  초기 데이터 저장소(백업이 처음에 있는 위치)
         -  트리거(백업이 트리거되는 방법)
            -  일정 기반
            -  기본 태그 지정 조건(예약된 모든 백업에 대한 기본 '태그'입니다. 이 태그는 백업을 보존 규칙에 연결합니다.
            -  이름이 'X'인 새 보존 규칙에 대한 새 태그 지정 조건
   -  기본 보존 규칙(기본적으로 초기 데이터 저장소의 모든 백업에 적용되는 규칙)
   -  'X'로 명명된 새 보존 규칙
      -  수명 주기
         -  원본 데이터 저장소
         -  원본 데이터 저장소에서 기간 후 삭제
         -  대상 데이터 저장소에 복사

### <a name="retrieving-the-policy-template"></a>정책 템플릿 검색

Azure PostgreSQL 데이터베이스 백업에 대한 백업 정책의 내부 구성 요소를 이해하려면 [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 정책 템플릿을 검색합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDatabaseForPostgreSQL
$policyDefn | fl


DatasourceType : {Microsoft.DBforPostgreSQL/servers/databases}
ObjectType     : BackupPolicy
PolicyRule     : {BackupWeekly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : VaultStore
Name                      : BackupWeekly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.ScheduleBasedTriggerCo
                            ntext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210701.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure PostgreSQL 데이터베이스 백업에서 트리거의 기본값은 예약된 매주 트리거(7일마다 백업 1개)이며 3개월 동안 각 백업을 유지합니다.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2021-08-22T02:00:00+00:00/P1W}
ScheduleTimeZone              : UTC
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P3M
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : VaultStore
TargetDataStoreCopySetting : {}
```

### <a name="modify-the-policy-template"></a>정책 템플릿 수정

#### <a name="modify-the-schedule"></a>일정 수정

기본 정책 템플릿은 매주 한 번씩 백업을 제공합니다. 백업 일정이 매주 며칠씩 발생하도록 수정할 수 있습니다. 일정을 변경하려면 [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용합니다.

다음 예제에서는 매주 매주 일요일, 수요일 및 금요일마다 백업하도록 주별 백업을 수정합니다. 일정 날짜 배열은 날짜를 언급하고 해당 날짜의 요일은 요일로 간주합니다. 또한 이러한 일정이 매주 반복하도록 지정해야 합니다. 따라서 일정 간격은 "1"이고 간격 유형은 "매주"입니다.

```azurepowershell-interactive
$schDates = @(
    (
        (Get-Date -Year 2021 -Month 08 -Day 15 -Hour 22 -Minute 0 -Second 0)
    ), 
    (
        (Get-Date -Year 2021 -Month 08 -Day 18 -Hour 22 -Minute 0 -Second 0)
    ),
  (
        (Get-Date -Year 2021 -Month 08 -Day 20 -Hour 22 -Minute 0 -Second 0)
    )
)
$trigger = New-AzDataProtectionPolicyTriggerScheduleClientObject -ScheduleDays $schDates -IntervalType Weekly -IntervalCount 1 
Edit-AzDataProtectionPolicyTriggerClientObject -Schedule $trigger -Policy $policyDefn   
```

#### <a name="add-a-new-retention-rule"></a>새 보존 규칙 추가

따라서 _보관_ 보호를 추가하려면 아래와 같이 정책 템플릿을 수정해야 합니다.

기본 템플릿에는 기본 보존 규칙에 따라 초기 데이터 저장소에 대한 수명 주기가 있습니다. 이 시나리오에서 규칙은 3개월 후에 백업 데이터를 삭제한다고 표시합니다. 데이터가 *보관* 데이터 *저장소로 이동되는* 시기를 정의하는 새 보존 규칙을 추가해야 합니다. 즉, 백업 데이터가 먼저 보관 데이터 저장소에 복사된 다음 자격 증명 모음 데이터 저장소에서 삭제됩니다. 또한 규칙은 데이터가 *보관* 데이터 저장소에 보관되는 기간도 정의해야 합니다. [New-AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용하여 새 수명 주기를 만들고 [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용하여 새 규칙 또는 기존 규칙에 연결합니다.

다음 예제에서는 매월 첫 번째 성공한 백업을 6개월 동안 자격 증명 모음에 보관하고 보관 계층으로 이동한 후 보관 계층 24개월에 보관해야 하는 *매월이라는* 새 보존 규칙을 만듭니다.

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>태그 및 관련 조건 추가

보존 규칙이 만들어지면 백업 정책의 *트리거* 속성에 해당 *태그를* 만들어야 합니다. [New-AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용하여 새 태그 지정 조건을 만들고 [Edit-AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용하여 기존 태그를 업데이트하거나 새 태그를 만듭니다.

다음 예제에서는 적용할 해당 보존 규칙과 동일한 이름의 조건(월의 첫 번째 성공적인 백업)과 함께 새 *태그를* 만듭니다.

이 예제에서는 태그 조건의 이름을 *매월* 로 지정해야 합니다.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

일정이 매주 여러 백업(위 예제에 지정된 대로 매주 일요일, 수요일, 목요일)이고 일요일 및 금요일 백업을 보관하려는 경우 태그 지정 기준을 다음과 같이 변경할 수 있습니다.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>새 PostgreSQL 백업 정책 만들기

요구 사항에 따라 템플릿이 수정되면 [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true) 명령을 사용하여 수정된 템플릿을 사용하여 정책을 만듭니다.

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책이 만들어지면 Azure PostgreSQL 데이터베이스를 보호하기 위해 고려해야 하는 세 가지 중요한 지점이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="postgresql-database-to-be-protected"></a>보호할 PostgreSQL 데이터베이스

보호할 PostgreSQL의 Azure Resource Manager ID(ARM ID)를 가져옵니다. 데이터베이스의 식별자로 사용됩니다. 다른 구독의 리소스 그룹 **ossrg에** 있는 PostgreSQL 서버 **testposgresql** 아래에 **empdb11이라는** 데이터베이스의 예를 사용합니다.

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup 서비스는 PostgreSQL 데이터베이스에 연결하기 위한 사용자 이름과 암호를 저장하지 않습니다. 대신, 백업 관리자는 *키를 키* 자격 증명 모음에 시드한 다음, 백업 서비스가 키 자격 증명 모음에 액세스하고, 키를 읽고, 데이터베이스에 액세스합니다. 관련 키의 비밀 식별자를 확인합니다.

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

PostgreSQL 서버에 백업 자격 증명 모음을 연결한 다음 키 자격 증명 모음에 있는 키를 통해 데이터베이스에 액세스 해야 합니다. 따라서 PostgGreSQL 서버 및 주요 자격 증명 모음에 대 한 액세스 권한이 필요 합니다. 백업 자격 증명 모음 MSI에 액세스 권한이 부여 됩니다.

PostgreSQL 서버에서 백업 자격 증명 모음 MSI에 부여 해야 하는 [적절 한 사용 권한과](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup) 데이터베이스에 대 한 키가 저장 되는 Azure key vault에 대해 알아보세요.

### <a name="prepare-the-request"></a>요청 준비

모든 관련 권한이 설정 되 면 백업의 구성이 두 단계로 수행 됩니다.

1. [AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 관련 자격 증명 모음, 정책, PostgreSQL 데이터베이스를 사용 하 여 관련 요청을 준비 합니다.
1. [AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 데이터베이스를 보호 하기 위한 요청을 제출 합니다.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

[AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 백업을 트리거하는 데 필요한 관련 백업 인스턴스를 가져옵니다.

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

백업을 트리거하는 동안 보존 규칙을 지정할 수 있습니다. 정책에서 보존 규칙을 보려면 보존 규칙의 정책 개체를 검색합니다. 다음 예에서는 이름이 *default* 인 규칙이 표시 됩니다. 주문형 백업에 대해이 규칙을 사용 합니다.

```azurepowershell-interactive
$ossPol.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

주문형 백업을 트리거하려면 [AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) 명령을 사용 합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az.ResourceGraph_ 를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 백업 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell를 사용 하 여 Azure PostgreSQL database 복원](restore-managed-disks-ps.md)
