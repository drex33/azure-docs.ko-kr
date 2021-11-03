---
title: Azure PowerShell 통해 Azure PostgreSQL 데이터베이스 복원
description: Azure PowerShell 사용하여 Azure PostgreSQL 데이터베이스를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 6b271f823d8c0547a0fd48861e463bfcc1513e97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103011"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell 사용하여 Azure PostgreSQL 데이터베이스 복원

이 문서에서는 Azure Backup 백업된 [Azure PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) 서버로 Azure PostgreSQL 데이터베이스를 복원하는 방법을 설명합니다.

PaaS 데이터베이스인 경우 백업이 수행된 기존 데이터베이스를 교체하여 복원하는 OLR(Original-Location Recovery) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 동일한 Azure PostgreSQL 서버 또는 다른 PostgreSQL 서버에 새 데이터베이스를 만들 수 있습니다. 이를 원본 데이터베이스와 복원된(새) 데이터베이스를 유지하는 데 도움이 되는 ALR(Alternate-Location Recovery)이라고 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 복원하여 새 PostgreSQL 데이터베이스 만들기

- 복원 작업 상태 추적

예제의 리소스 그룹 _testBkpVaultRG에서_ 기존 백업 자격 증명 모음 _TestBkpVault를_ 참조합니다.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>복원하여 새 PostgreSQL 데이터베이스 만들기

### <a name="set-up-permissions"></a>권한 설정

Backup 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 데이터베이스를 복원해야 하는 Azure PostgreSQL 서버에 대한 권한 집합이 필요합니다.

대상 PostgreSQL 서버에서 자격 증명 모음의 시스템 할당 관리 ID에 대한 관련 권한을 할당하려면 [Azure PostgreSQL 데이터베이스를 백업하는 데 필요한 권한 집합을 참조하세요.](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore)

복구 지점을 스토리지 계정에 파일로 복원하려면 Backup 자격 증명 [모음의 시스템 할당 관리 ID에 대상 스토리지 계정 에 대한 액세스 권한이 필요합니다.](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account)

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 인스턴스를 가져오고 관련 인스턴스를 식별합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az.Resourcegraph** 및 [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 여러 자격 증명 모음 및 구독의 인스턴스에서 복구 지점을 검색할 수도 있습니다.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

검색 조건을 필터링하려면 아래와 같이 PowerShell 클라이언트 검색 기능을 사용합니다.

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

인스턴스가 식별되면 관련 복구 지점을 가져옵니다.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

보관 계층에서 복구 지점을 가져와야 하는 경우 다음과 같이 클라이언트 필터를 추가합니다.

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>복원 요청 준비

PostgreSQL 데이터베이스에 대한 다양한 복원 옵션이 있습니다. 복구 지점을 다른 데이터베이스로 복원하거나 파일로 복원할 수 있습니다. 복구 지점은 보관 계층에도 있을 수 있습니다.

#### <a name="restore-as-database"></a>데이터베이스로 복원

만들 새 PostgreSQL 데이터베이스의 ARM ID(Azure 리소스 관리 ID)를 생성합니다(권한 [위에](#set-up-permissions)자세히 설명된 대로 할당된 대상 PostgreSQL 서버 및 필요한 PostgreSQL 데이터베이스 이름 사용). 예를 들어 다른 구독을 사용하는 리소스 그룹 **targetrg의** 대상 PostgreSQL 서버 **targetossserver에서** PostgreSQL 데이터베이스의 이름을 **emprestored21로** 지정할 수 있습니다.

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

보관 기반 복구 지점의 경우 다음을 수행해야 합니다.

1. 보관 데이터 저장소에서 자격 증명 모음 저장소로 리하이딩합니다.
1. 원본 데이터 저장소를 수정합니다.
1. 다른 매개 변수를 추가하여 리하일레이션 우선 순위를 지정합니다.
1. 리하위드 복구 지점을 자격 증명 모음 데이터 저장소에 보존할 기간을 지정합니다.
1. 이 복구 지점에서 데이터베이스로 복원합니다.

다음 명령을 사용하여 위에서 언급한 모든 작업에 대한 요청을 한 번에 준비합니다.

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>파일로 복원

[위에서](#set-up-permissions)설명한 대로 권한이 할당된 스토리지 계정 내에서 컨테이너의 URI를 가져옵니다. 예를 들어 다른 구독을 가진 스토리지 계정 **testossstorageaccount에** 있는 **testcontainerrestore라는** 컨테이너가 있습니다.

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

보관 기반 복구 지점의 경우 원본 데이터 저장소를 수정하고 아래 설명된 대로 리하위드 복구 지점의 리하이드레이션 우선 순위와 보존 기간(일)을 추가합니다.

```azurepowershell-interactive
$OssRestoreAsFilesFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" -RehydrationDuration "14" -RehydrationPriority Standard
```

### <a name="trigger-the-restore"></a>복원 트리거

[Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 위에서 준비한 요청으로 복원을 트리거합니다.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $OssRestoreReq
```

## <a name="tracking-job"></a>작업 추적

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

*Az.ResourceGraph를* 사용하여 모든 Backup 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 백업 자격 증명 모음에 있는 관련 작업을 얻을 수 있습니다.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL Backup 개요](backup-azure-database-postgresql-overview.md)
