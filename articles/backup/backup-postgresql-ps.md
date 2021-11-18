---
title: Azure PowerShell를 사용 하 여 장기 보존으로 Azure Database for PostgreSQL 백업
description: Azure PowerShell를 사용 하 여 Azure Database for PostgreSQL를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.author: v-amallick
ms.date: 10/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d11bebf36de7c5ffd7fae8b774ee428ae3a652b2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714735"
---
# <a name="back-up-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure PostgreSQL 데이터베이스 백업

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure PostgreSQL 데이터베이스](../postgresql/overview.md#azure-database-for-postgresql---single-server) 를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Backup 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure PostgreSQL 데이터베이스의 백업 구성

- 주문형 백업 작업 실행

Azure PostgreSQL 데이터베이스 지원 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](backup-azure-database-postgresql-overview.md#support-matrix)를 참조 하세요.

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

백업 자격 증명 모음은 Azure Database for PostgreSQL 서버, Azure 디스크 및 Azure Blob과 같이 Azure Backup에서 지 원하는 다양 한 새 워크 로드에 대 한 백업 데이터를 저장 하는 Azure의 저장소 엔터티입니다. 백업 자격 증명 모음은 관리 오버 헤드를 최소화 하면서 백업 데이터를 구성 하는 데 도움이 됩니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

백업 자격 증명 모음을 만들기 전에 자격 증명 모음 내에서 데이터의 저장소 중복성을 선택 합니다. 그런 다음 해당 스토리지 중복도 및 위치를 사용하여 백업 자격 증명 모음을 만듭니다.

이 문서에서는 리소스 그룹 *testBkpVaultRG* 아래에 *Westus* region의 백업 자격 증명 모음 *TestBkpVault* 을 만듭니다. [AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 백업 자격 증명 모음을 만듭니다. [백업 자격 증명 모음 만들기](./backup-vault-overview.md#create-a-backup-vault)에 대해 자세히 알아보세요.

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

자격 증명 모음을 만든 후에 Azure PostgreSQL 데이터베이스를 보호 하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

### <a name="understand-postgresql-backup-policy"></a>PostgreSQL 백업 정책 이해

디스크 백업은 하루에 여러 백업을 제공 하 고 blob 백업은 트리거가 없는 *연속* 백업입니다. PostgreSQL Backup은 보관 보호를 제공 합니다. 자격 증명 모음으로 처음 전송 된 백업 데이터는 정의 된 규칙 또는 *수명 주기* 에 따라 *보관* 계층으로 이동할 수 있습니다. 이 컨텍스트에서는 PostgreSQL에 대 한 백업 정책 개체에 대해 알아보겠습니다.

-  PolicyRule
   -  BackupRule
      -  BackupParameter 변수
         -  BackupType (이 경우 전체 데이터베이스 백업)
         -  초기 데이터 저장소 (백업 위치는 처음에)
         -  트리거 (백업이 트리거되는 방법)
            -  일정 기반
            -  모든 예약 된 백업에 대 한 기본 태그 지정 조건 (기본 ' 태그 ')입니다. 이 태그는 백업을 보존 규칙에 연결 합니다.
   -  기본 보존 규칙 (기본적으로 초기 데이터 저장소에서 모든 백업에 적용 되는 규칙)

따라서이 개체는 트리거되는 백업 유형, 일정을 통해 트리거되는 방법, 태그가 지정 된 항목, 데이터 저장소 (데이터 저장소) 및 데이터 저장소의 백업 데이터에 대 한 수명 주기를 정의 합니다. PostgreSQL에 대 한 기본 PowerShell 개체는 매주 *전체* 백업을 트리거하기 때문에 3 개월 동안 저장 된 자격 증명 모음에 도달 합니다.

*보관* 계층을 정책에 추가 하려면 데이터를 자격 증명 모음에서 보관으로 이동할 시기, 데이터가 보관 저장소에 유지 되는 기간, 예약 된 백업에 _보관할 수_ 로 *태그를 지정* 해야 하는 시간을 결정 해야 합니다. 따라서 *보존 규칙* 을 추가 해야 합니다. 여기에서 백업 데이터의 수명 주기는 자격 증명 데이터 저장소에서 보관 데이터 저장소로, 보관 *데이터 저장소에* 유지 되는 기간을 정의 합니다. 그런 다음 예약 된 백업을 _보관할 자격이_ 있는 것으로 표시 하는 *태그* 를 추가 해야 합니다.

결과 PowerShell 개체는 다음과 같습니다.


-  PolicyRule
   -  BackupRule
      -  BackupParameter 변수
         -  BackupType (이 경우 전체 데이터베이스 백업)
         -  초기 데이터 저장소 (백업 위치는 처음에)
         -  트리거 (백업이 트리거되는 방법)
            -  일정 기반
            -  모든 예약 된 백업에 대 한 기본 태그 지정 조건 (기본 ' 태그 ')입니다. 이 태그는 백업을 보존 규칙에 연결 합니다.
            -  이름이 ' X ' 인 새 보존 규칙의 새 태그 지정 조건
   -  기본 보존 규칙 (기본적으로 초기 데이터 저장소에서 모든 백업에 적용 되는 규칙)
   -  ' X '로 이름이 지정 된 새 보존 규칙
      -  수명 주기
         -  원본 데이터 저장소
         -  원본 데이터 저장소의 기간 후 삭제
         -  대상 데이터 저장소에 복사

### <a name="retrieving-the-policy-template"></a>정책 템플릿 검색

Azure PostgreSQL 데이터베이스 백업에 대 한 백업 정책의 내부 구성 요소를 이해 하려면 [AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 정책 템플릿을 검색 합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

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

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure PostgreSQL 데이터베이스 백업에서 트리거의 기본값은 예약 된 주별 트리거 (7 일 마다 백업 1 개)이 고 각 백업이 3 개월 동안 유지 되는 것입니다.

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

기본 정책 템플릿은 매주 한 번 백업을 제공 합니다. 백업에 대 한 일정은 일주일에 한 번 수행 되도록 수정할 수 있습니다. 일정을 변경 하려면 [AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용 합니다.

다음 예에서는 매주 일요일, 수요일 및 금요일 마다 백업 하도록 주간 백업을 수정 합니다. 일정 날짜 배열은 날짜를 언급 하 고 해당 날짜의 요일은 요일로 사용 됩니다. 또한 매주이 일정을 반복 하도록 지정 해야 합니다. 따라서 일정 간격은 "1"이 고 간격 유형은 "매주"입니다.

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

따라서 _보관_ 보호를 추가 하려면 아래와 같이 정책 템플릿을 수정 해야 합니다.

기본 템플릿은 기본 보존 규칙의 초기 데이터 저장소에 대 한 수명 주기를 갖습니다. 이 시나리오에서 규칙은 3 개월 후 백업 데이터를 삭제 하는 것을 말합니다. 데이터를 *보관* 데이터 저장소로 *이동* 하는 경우를 정의 하는 새 보존 규칙을 추가 해야 합니다. 즉, 백업 데이터는 먼저 보관 데이터 저장소로 복사 된 후 자격 증명 저장소에서 삭제 됩니다. 또한이 규칙은 데이터가 *보관* 데이터 저장소에 보관 되는 기간에 대해 정의 해야 합니다. [AzDataProtectionRetentionLifeCycleClientObject](/powershell/module/az.dataprotection/new-azdataprotectionretentionlifecycleclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용 하 여 새 생명주 기를 만들고 [AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용 하 여 새 규칙 또는 기존 규칙과 연결 합니다.

다음 예에서는 *매월* 첫 번째 성공한 백업이 자격 증명 모음에서 6 개월 동안 유지 되 고 보관 계층으로 이동 하 여 보관 계층 24 개월 내에 보관 된 새 보존 규칙을 만듭니다.

```azurepowershell-interactive
$VaultToArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore VaultStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 6 -TargetDataStore ArchiveStore -CopyOption CopyOnExpiryOption

$OnArchiveLifeCycle = New-AzDataProtectionRetentionLifeCycleClientObject -SourceDataStore ArchiveStore -SourceRetentionDurationType Months -SourceRetentionDurationCount 24

Edit-AzDataProtectionPolicyRetentionRuleClientObject -Policy $policyDefn -Name Monthly -LifeCycles $VaultToArchiveLifeCycle, $OnArchiveLifeCycleLifeCycle -IsDefault $false
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>태그 및 관련 조건 추가

보존 규칙이 만들어지면 백업 정책의 *Trigger* 속성에 해당 *태그* 를 만들어야 합니다. [AzDataProtectionPolicyTagCriteriaClientObject](/powershell/module/az.dataprotection/new-azdataprotectionpolicytagcriteriaclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용 하 여 새 태그 지정 조건을 만들고 [AzDataProtectionPolicyTagClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytagclientobject?view=azps-6.5.0&preserve-view=true) 명령을 사용 하 여 기존 태그를 업데이트 하거나 새 태그를 만듭니다.

다음 예에서는 적용 되는 해당 보존 규칙과 동일한 이름으로 조건 (월의 첫 번째 백업)과 함께 새 *태그* 를 만듭니다.

이 예제에서는 태그 조건의 이름을 *매월* 로 지정 해야 합니다.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -AbsoluteCriteria FirstOfMonth
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

일정이 주별 백업 (위 예제에 지정 된 일요일, 수요일, 목요일 마다) 인 경우 일요일 및 금요일 백업을 보관 하려는 경우 다음과 같이 태깅 조건을 변경할 수 있습니다.

```azurepowershell-interactive
$tagCriteria = New-AzDataProtectionPolicyTagCriteriaClientObject -DaysOfWeek @("Sunday", "Friday")
Edit-AzDataProtectionPolicyTagClientObject -Policy $policyDefn -Name Monthly -Criteria $tagCriteria
```

### <a name="create-a-new-postgresql-backup-policy"></a>새 PostgreSQL 백업 정책 만들기

요구 사항에 따라 템플릿이 수정 되 면 [AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-6.5.0&preserve-view=true) 명령을 사용 하 여 수정 된 템플릿을 사용 하 여 정책을 만듭니다.

```azurepowershell-interactive
$polOss = New-AzDataProtectionBackupPolicy -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Name "TestOSSPolicy" -Policy $policyDefn
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책을 만든 후에는 Azure PostgreSQL 데이터베이스를 보호 하기 위해 고려해 야 하는 세 가지 중요 한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="postgresql-database-to-be-protected"></a>보호할 PostgreSQL 데이터베이스

보호할 PostgreSQL의 Azure Resource Manager ID (ARM ID)를 인출 합니다. 이는 데이터베이스의 식별자 역할을 합니다. PostgreSQL server **testposgresql** 아래에 **empdb11** 라는 데이터베이스의 예를 사용 합니다 .이 데이터베이스는 다른 구독에 있는 리소스 그룹 **ossrg** 에 있습니다.

```azurepowershell-interactive
$ossId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup 서비스는 PostgreSQL 데이터베이스에 연결 하기 위해 사용자 이름과 암호를 저장 하지 않습니다. 대신, 백업 관리자가 키 자격 증명 *모음에 키를 시드* 한 다음 backup 서비스는 키 자격 증명 모음에 액세스 하 고 키를 읽고 데이터베이스에 액세스 합니다. 관련 키의 비밀 식별자를 적어 둡니다.

```azurepowershell-interactive
$keyURI = "https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

Backup 자격 증명 모음을 PostgreSQL 서버에 연결한 다음 키 자격 증명 모음에 있는 키를 통해 데이터베이스에 액세스해야 합니다. 따라서 PostgGreSQL 서버 및 키 자격 증명 모음에 액세스해야 합니다. Backup 자격 증명 모음의 MSI에 대한 액세스 권한이 부여됩니다.

데이터베이스에 대한 키가 저장되는 PostgreSQL 서버 및 Azure Key Vault에서 Backup 자격 증명 모음의 MSI에 부여해야 하는 [적절한 사용 권한을 읽어보십시오.](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)

### <a name="prepare-the-request"></a>요청 준비

모든 관련 권한이 설정되면 백업 구성이 두 단계로 수행됩니다.

1. [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련 자격 증명 모음, 정책, PostgreSQL 데이터베이스를 사용하여 관련 요청을 준비합니다.
1. [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 데이터베이스를 보호하기 위한 요청을 제출합니다.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDatabaseForPostgreSQL -DatasourceLocation $TestBkpvault.Location -PolicyId $polOss[0].Id -DatasourceId $ossId -SecretStoreURI $keyURI -SecretStoreType AzureKeyVault
ConvertTo-Json -InputObject $instance -Depth 4 
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                        Type                                         BackupInstanceName
----                        ----                                          ------------------
ossrg-empdb11       Microsoft.DataProtection/backupVaults/backupInstances ossrg-empdb11
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 백업을 트리거해야 하는 관련 백업 인스턴스를 가져옵니다.

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

백업을 트리거하는 동안 보존 규칙을 지정할 수 있습니다. 정책에서 보존 규칙을 보려면 보존 규칙의 정책 개체를 검색합니다. 다음 예제에서는 이름이 *기본값인* 규칙이 표시됩니다. 주문형 백업에 해당 규칙을 사용합니다.

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

주문형 백업을 트리거하려면 [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) 명령을 사용합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="track-jobs"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az.ResourceGraph_ 를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 백업 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell 사용하여 Azure PostgreSQL 데이터베이스 복원](restore-managed-disks-ps.md)