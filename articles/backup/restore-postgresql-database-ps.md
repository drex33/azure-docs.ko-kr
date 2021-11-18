---
title: Azure PowerShell를 통해 Azure PostgreSQL 데이터베이스 복원
description: Azure PowerShell를 사용 하 여 Azure PostgreSQL 데이터베이스를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/26/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c5a17d1e0969dda584c19f344e226dff666b6bea
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716717"
---
# <a name="restore-azure-postgresql-databases-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure PostgreSQL 데이터베이스 복원

이 문서에서는 Azure Backup 여 백업 된 Azure PostgreSQL 서버에 [Azure PostgreSQL 데이터베이스](../postgresql/overview.md#azure-database-for-postgresql---single-server) 를 복원 하는 방법을 설명 합니다.

PaaS 데이터베이스의 경우 백업이 수행 된 위치에서 기존 데이터베이스를 대체 하 여 복원 하는 OLR (Original-Location 복구) 옵션이 지원 되지 않습니다. 복구 지점에서 복원 하 여 동일한 Azure PostgreSQL 서버 또는 다른 PostgreSQL 서버에 새 데이터베이스를 만들 수 있습니다. 이를 ALR (Alternate-Location 복구) 라고 하며,이를 통해 원본 데이터베이스와 복원 된 (새 데이터베이스) 데이터베이스를 모두 유지할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 PostgreSQL 데이터베이스를 만들기 위해 복원

- 복원 작업 상태 추적

예제에서는 리소스 그룹 _testBkpVaultRG_ 에서 기존 백업 자격 증명 모음 _TestBkpVault_ 을 참조 합니다.

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-postgresql-database"></a>새 PostgreSQL 데이터베이스를 만들기 위해 복원

### <a name="set-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 Id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 백업에서 복원 하려면 백업 자격 증명 모음의 관리 되는 id에 데이터베이스를 복원 해야 하는 Azure PostgreSQL 서버에 대 한 권한 집합이 필요 합니다.

대상 PostgreSQL 서버에서 자격 증명 모음의 시스템 할당 관리 id에 대 한 관련 사용 권한을 할당 하려면 [Azure PostgreSQL database를 백업 하는 데 필요한 권한 집합](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)을 참조 하세요.

복구 지점을 저장소 계정에 파일로 복원 하려면 [백업 자격 증명 모음의 시스템 할당 관리 id에 대상 저장소 계정에 대 한 액세스 권한이 있어야](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)합니다.

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 모든 인스턴스를 가져오고 관련 인스턴스를 식별합니다.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

**Az. Resourcegraph** 및 [AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 여러 자격 증명 모음 및 구독에서 여러 인스턴스의 복구 위치를 검색할 수도 있습니다.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured
```

검색 조건을 필터링 하려면 아래와 같이 PowerShell 클라이언트 검색 기능을 사용 합니다.

```azurepowershell-interactive
Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -ProtectionStatus ProtectionConfigured | Where-Object { $_.BackupInstanceName -match "empdb11"}
```

인스턴스가 식별되면 관련 복구 지점을 가져옵니다.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

보관 계층에서 복구 지점을 인출 해야 하는 경우 다음과 같이 클라이언트 필터를 추가 합니다.

```azurepowershell-interactive
Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName | Where-Object {$_.Property.RecoveryPointDataStoresDetail[0].Type -match "Archive" }
```

### <a name="prepare-the-restore-request"></a>복원 요청 준비

PostgreSQL 데이터베이스에 대 한 다양 한 복원 옵션이 있습니다. 복구 지점을 다른 데이터베이스로 복원 하거나 파일로 복원할 수 있습니다. 복구 지점은 보관 계층에도 있을 수 있습니다.

#### <a name="restore-as-database"></a>데이터베이스로 복원

만들 새 PostgreSQL 데이터베이스의 Azure 리소스 관리 ID (ARM ID)를 생성 합니다 ( [위에서](#set-up-permissions)설명한 대로 사용 권한이 할당 된 대상 PostgreSQL 서버 및 필수 PostgreSQL 데이터베이스 이름). 예를 들어, 다른 구독을 사용 하 여 리소스 그룹 **targetrg** 의 대상 PostgreSQL 서버 **targetossserver** 아래에서 PostgreSQL 데이터베이스의 이름을 **emprestored21** 으로 지정할 수 있습니다.

```azurepowershell-interactive
$targetOssId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21
```

[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

```azurepowershell-interactive
$OssRestoreReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault
```

아카이브 기반 복구 지점의 경우 다음을 수행 해야 합니다.

1. 보관 데이터 저장소에서 자격 증명 모음 저장소로 리하이드레이션.
1. 원본 데이터 저장소를 수정 합니다.
1. 다른 매개 변수를 추가 하 여 리하이드레이션 우선 순위를 지정 합니다.
1. 자격 증명 모음 데이터 저장소에서 담당 하는 복구 지점을 보존할 기간을 지정 합니다.
1. 이 복구 지점의 데이터베이스로 복원 합니다.

다음 명령을 사용 하 여 위에서 언급 한 모든 작업에 대 한 요청을 한 번에 준비할 수 있습니다.

```azurepowershell-interactive
$OssRestoreFromArchiveReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore ArchiveStore -RestoreLocation $TestBkpVault.Location -RestoreType AlternateLocation -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetResourceId $targetOssId -SecretStoreURI "https://restoreoss-test.vault.azure.net/secrets/dbauth3" -SecretStoreType AzureKeyVault -RehydrationDuration 12 -RehydrationPriority Standard
```

#### <a name="restore-as-files"></a>파일로 복원

[위에서](#set-up-permissions)설명한 대로 사용 권한이 할당 된 저장소 계정 내에서 컨테이너의 URI를 가져옵니다. 예를 들어 다른 구독이 있는 **testossstorageaccount** 저장소 계정 아래에 **testcontainerrestore** 라는 컨테이너가 있습니다.

```azurepowershell-interactive
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

[Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 명령을 사용하여 관련된 모든 세부 정보와 함께 복원 요청을 준비합니다.

```azurepowershell-interactive
$OssRestoreAsFilesReq = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDatabaseForPostgreSQL -SourceDataStore VaultStore -RestoreLocation $TestBkpVault.Location -RestoreType RestoreAsFiles -RecoveryPoint $rps[0].Property.RecoveryPointId -TargetContainerURI $contURI -FileNamePrefix "empdb11_postgresql-westus_1628853549768" 
```

보관 기반 복구 지점의 경우 원본 데이터 저장소를 수정 하 고 아래 설명 된 대로, 리하이드레이션 된 복구 지점의 보존 기간 (일)을 추가 합니다.

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

*Az. ResourceGraph* 를 사용 하 여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 명령을 사용 하 여 모든 백업 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDatabaseForPostgreSQL -Operation OnDemandBackup
```

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL Backup 개요](backup-azure-database-postgresql-overview.md)