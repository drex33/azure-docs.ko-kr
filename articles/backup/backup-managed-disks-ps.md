---
title: Azure PowerShell을 사용하여 Azure Managed Disks 백업
description: Azure PowerShell을 사용하여 Azure Managed Disks를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aab90bdd5a8ee4ecafd0dbfec81052da6b39f69
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264601"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Managed Disks 백업

Azure PowerShell을 사용하여 [Azure Managed Disk](../virtual-machines/managed-disks-overview.md)를 백업하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Backup 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure Disk의 백업 구성

- 주문형 백업 작업 실행

Azure Disk Backup 지역 가용성, 지원되는 시나리오 및 제한 사항에 대한 정보는 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

Backup 자격 증명 모음은 PostgreSQL용 Azure Database 서버 및 Azure Disk와 같이 Azure Backup이 지원하는 다양한 최신 워크로드에 대한 백업 데이터를 보유하는 Azure의 스토리지 엔터티입니다. Backup 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

백업 자격 증명 모음을 만들기 전에 자격 증명 모음 내에서 데이터의 스토리지 중복도를 선택합니다. 그런 다음 해당 스토리지 중복도 및 위치를 사용하여 백업 자격 증명 모음을 만듭니다. 이 문서에서는 “testBkpVaultRG” 리소스 그룹의 “westus” 지역에 백업 자격 증명 모음 “TestBkpVault”를 만듭니다. [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 백업 자격 증명 모음을 만듭니다. [Backup 자격 증명 모음 만들기](./backup-vault-overview.md#create-a-backup-vault)에 관해 자세히 알아보세요.

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

자격 증명 모음을 만든 후에는 Azure 디스크를 보호하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

Azure 디스크 백업에 관한 백업 정책의 내부 구성 요소를 이해하려면 [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 정책 템플릿을 검색합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


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

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure 디스크 백업에서 트리거의 기본값은 4시간(PT4H)마다 예약된 매시간 트리거이며 각 백업을 7일 동안 유지합니다.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure Disk Backup은 하루에 여러 백업을 제공합니다. 더 자주 백업해야 하는 경우 **시간별** 백업 빈도를 선택하여 4, 6, 8 또는 12시간 간격으로 백업을 수행할 수 있습니다. 백업은 선택한 **시간** 간격을 기준으로 예약됩니다. 예를 들어 **4시간마다** 를 선택하면 대략 4시간 간격으로 백업이 수행되므로 백업이 하루 동안 균등하게 분산됩니다. 하루에 한 번 백업하면 **매일** 백업 빈도를 선택합니다. 매일 백업 빈도에서 백업을 수행할 시간을 지정할 수 있습니다. 시간은 백업 시작 시간을 나타내고 백업이 완료되는 시간이 아님에 유의해야 합니다. 백업 작업을 완료하는 데 필요한 시간은 디스크 크기 및 연속 백업 간의 변동 비율을 비롯한 다양한 요인에 따라 달라집니다. 그러나 Azure Disk Backup은 프로덕션 애플리케이션 성능에 영향을 주지 않는 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md)을 사용하는 에이전트 없는 백업입니다.

   >[!NOTE]
   > 선택한 자격 증명 모음에 전역 중복 설정이 있을 수 있지만, 현재 Azure Disk Backup은 스냅샷 데이터 저장소만 지원합니다. 모든 백업은 구독의 리소스 그룹에 저장되며 백업 자격 증명 모음 스토리지로 복사되지 않습니다.

정책 만들기에 관한 자세한 내용은 [Azure Disk Backup 정책](backup-managed-disks.md#create-backup-policy) 설명서를 참조하세요.

시간별 빈도 또는 보존 기간을 편집하려면 [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) 및/또는 [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) 명령을 사용합니다. 정책 개체에 원하는 값이 모두 있으면 [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)를 사용하여 정책 개체에서 새 정책을 만듭니다.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책을 만들면 Azure 디스크를 보호하기 위해 고려해야 할 세 가지 중요한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="disk-to-be-protected"></a>보호할 디스크

보호할 디스크의 ARM ID를 가져옵니다. 디스크의 식별자 역할을 합니다. 다른 구독의 리소스 그룹 “diskrg”에서 “PSTestDisk”라는 디스크의 예를 사용합니다.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>스냅샷 리소스 그룹

디스크 스냅샷은 구독 내의 리소스 그룹에 저장됩니다. 지침으로 Azure Backup 서비스에서 사용할 스냅샷 데이터 저장소로 전용 리소스 그룹을 만드는 것이 좋습니다. 전용 리소스 그룹을 사용하면 리소스 그룹에 대한 액세스 권한을 제한할 수 있기 때문에 안전하고 쉽게 백업 데이터를 관리할 수 있습니다. 디스크 스냅샷을 배치할 리소스 그룹의 ARM ID 기록해 두기

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

스냅샷을 트리거하고 수명 주기를 관리할 수 ​​있으려면 Backup 자격 증명 모음에 디스크 및 스냅샷 리소스 그룹에 관한 권한이 있어야 합니다. 자격 증명 모음의 시스템이 할당한 관리 ID는 해당 권한을 할당하는 데 사용됩니다. [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 복구 서비스 자격 증명 모음의 시스템이 할당한 관리 ID를 사용으로 설정합니다.

### <a name="assign-permissions"></a>권한 할당

사용자는 RBAC를 통해 자격 증명 모음(자격 증명 모음 MSI로 표시) 및 관련 디스크 및/또는 디스크 RG에 몇 가지 권한을 할당해야 합니다. 이 작업은 포털 또는 PowerShell을 통해 수행할 수 있습니다.

백업 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 관리 디스크의 백업을 구성 하려면 백업 자격 증명 모음의 관리 되는 id에 스냅숏이 만들어지고 관리 되는 원본 디스크 및 리소스 그룹에 대 한 권한 집합이 필요 합니다.

시스템 할당 관리 id는 리소스 당 하나로 제한 되며이 리소스의 수명 주기에 연결 됩니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 관리 ID에 대한 권한을 부여할 수 있습니다. 관리 ID는 Azure 리소스에서만 사용할 수 있는 특수 유형의 서비스 주체입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

관리 디스크의 백업을 구성 하려면 다음 필수 구성 요소를 확인 합니다.

- 백업 해야 하는 원본 디스크에서 백업 자격 증명 모음 관리 id에 **디스크 백업 읽기 권한자** 역할을 할당 합니다.

  1. 백업해야 하는 디스크로 이동합니다.
  1. **액세스 제어(IAM)** 로 이동하여 **역할 할당 추가** 를 선택합니다.
  1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **디스크 백업 판독기** 를 선택 합니다.
  1. 백업 자격 증명 모음에서 관리 되는 id를 선택 하 고 **저장** 을 클릭 합니다.
  
     >[!Tip]
     >백업 자격 증명 모음 이름을 입력 하 여 자격 증명 모음의 관리 되는 id를 선택 합니다.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-inline.png" alt-text="백업 해야 하는 원본 디스크에서 백업 자격 증명 모음 관리 id에 디스크 백업 읽기 권한자 역할을 할당 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-expanded.png":::

- 백업 자격 증명 모음에 있는 백업 자격 증명 모음 관리 id에 **디스크 스냅숏 참가자** 역할을 할당 합니다. 여기에서 백업은 Azure Backup 서비스에 의해 생성 되 고 관리 됩니다. 디스크 스냅샷은 구독 내의 리소스 그룹에 저장됩니다. Azure Backup 서비스에서 스냅숏을 만들고 저장 하 고 관리할 수 있도록 하려면 백업 자격 증명 모음에 대 한 사용 권한을 제공 해야 합니다.

  1. 리소스 그룹으로 이동합니다. 예를 들어 리소스 그룹은 백업할 디스크와 동일한 구독에 있는 _SnapshotRG_ 입니다.
  1. **액세스 제어(IAM)** 로 이동하여 **역할 할당 추가** 를 선택합니다.
  1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **디스크 스냅숏 참가자** 를 선택 합니다. 
  1. 백업 자격 증명 모음에서 관리 되는 id를 선택 하 고 **저장** 을 클릭 합니다.
  
     >[!Tip]
     >백업 자격 증명 모음 이름을 입력하여 자격 증명 모음의 관리 ID를 선택합니다.

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-inline.png" alt-text="리소스 그룹에서 백업 자격 증명 모음 관리 id에 디스크 스냅숏 참가자 역할을 할당 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-expanded.png":::

- 백업 자격 증명 모음 관리 ID에 스냅샷 데이터 저장소 역할을 하는 소스 디스크 및 리소스 그룹에 올바른 역할 할당 세트가 있는지 확인합니다.

  1. **백업 자격 증명 모음** id로 이동 하 여  ->   **Azure 역할 할당** 을 선택 합니다.
 
     :::image type="content" source="./media/backup-managed-disks-ps/select-azure-role-assignments-inline.png" alt-text="Azure 역할 할당의 선택 항목을 보여 주는 스크린샷" lightbox="./media/backup-managed-disks-ps/select-azure-role-assignments-expanded.png":::

  1. 역할, 리소스 이름 및 리소스 종류가 올바른지 확인 합니다.
 
     :::image type="content" source="./media/backup-managed-disks-ps/verify-role-assignment-details-inline.png" alt-text="역할, 리소스 이름 및 리소스 종류의 확인을 보여 주는 스크린샷" lightbox="./media/backup-managed-disks-ps/verify-role-assignment-details-expanded.png":::

>[!Note]
>포털에서 역할 할당이 올바르게 반영 되지만 백업 자격 증명 모음 관리 id에 사용 권한을 적용 하는 데는 약 15-30 분이 걸릴 수 있습니다.

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정하면 백업 구성은 2개의 단계로 수행됩니다. 먼저 [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련 자격 증명 모음, 정책, 디스크 및 스냅샷 리소스 그룹을 사용하여 관련 요청을 준비합니다. 그런 다음 [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 디스크를 보호하기 위한 요청을 제출합니다.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

사용자가 [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)를 사용하여 백업을 트리거하려는 관련 백업 인스턴스를 가져옴

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

백업을 트리거하는 동안 보존 규칙을 지정할 수 있습니다. 정책에서 보존 규칙을 보려면 보존 규칙의 정책 개체를 검색합니다. 아래 예에서는 이름이 ‘default’인 규칙이 표시되고 해당 규칙을 주문형 백업에 사용함

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


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

[Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 주문형 백업을 트리거합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

Az.ResourceGraph를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 백업 자격 증명 모음에서 관련 작업을 가져옵니다.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 Azure Managed Disks 복원](restore-managed-disks-ps.md)