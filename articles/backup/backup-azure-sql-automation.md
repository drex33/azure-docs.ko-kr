---
title: PowerShell을 통한 Azure VM의 SQL DB 백업 및 복원
description: Azure Backup과 PowerShell을 사용하여 Azure VM의 SQL Database를 백업 및 복원합니다.
ms.topic: conceptual
ms.date: 06/30/2021
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be335dc70290c61f35608b8adf55483bf2a61ca8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449455"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>PowerShell을 사용하여 Azure VM의 SQL 데이터베이스를 백업 및 복원

이 문서에서는 Azure PowerShell를 사용하여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 사용하는 Azure VM 내에서 SQL DB를 백업하고 복원하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정하고 Azure Recovery Services 공급자를 등록합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure VM 내에서 SQL DB에 대한 백업을 구성합니다.
> * 백업 작업을 실행합니다.
> * 백업된 SQL DB를 복원합니다.
> * 백업 및 복원 작업을 모니터링합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md).
* [Azure VM 내에서 SQL DB를 백업](backup-azure-sql-database.md#before-you-start)하기 위한 기능에 대해 알아보세요.
* Recovery Services에 대한 PowerShell 개체 계층 구조를 검토합니다.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리의 **Az.RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices)에 대해 검토합니다.

### <a name="set-up-and-install"></a>설정 및 설치

다음과 같이 PowerShell을 설정합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.5.0입니다.

2. 다음 명령을 사용하여 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup 및 Recovery Services 자격 증명 모음의 별칭과 cmdlet을 검토합니다. 볼 수 있는 항목의 예는 다음과 같습니다. 이 목록은 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount** 으로 Azure 계정에 로그인합니다.
5. 표시되는 웹 페이지에 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

    * 또는 **Connect-AzAccount** cmdlet에서 **-Credential** 로 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 사용자가 테넌트를 위해 작업 중인 CSP 파트너인 경우에는 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

6. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록되었는지 확인합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 **RegistrationState** 가 **Registered** 로 변경되는지 확인합니다. 변경되지 않은 경우 **Register-AzResourceProvider** cmdlet을 다시 실행합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

1. 자격 증명 모음은 리소스 그룹에 배치됩니다. 기존 리소스 그룹이 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)로 새 그룹을 만듭니다. 이 예제에서는 미국 서부 지역의 새 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet을 사용하여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 자격 증명 모음 스토리지에 사용할 중복 유형을 지정합니다.

    * [로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage), [지역 중복 스토리지](../storage/common/storage-redundancy.md#geo-redundant-storage), [영역 중복 스토리지](../storage/common/storage-redundancy.md#zone-redundant-storage)를 사용할 수 있습니다.
    * 다음 예제에서는 [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd의 **-BackupStorageRedundancy** 옵션을 설정하여 **testvault** 를 **GeoRedundant** 로 설정합니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

다음과 유사하게 출력됩니다. 연결된 리소스 그룹과 위치가 제공됩니다.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

자격 증명 모음 개체를 변수에 저장하고 자격 증명 모음 컨텍스트를 설정합니다.

* 많은 Azure Backup cmdlet에 입력으로 Recovery Services 자격 증명 모음 개체가 필요하므로 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)로 설정합니다. 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다.

다음 예제에서는 **testvault** 에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID 가져오기

Azure PowerShell 지침과 관련하여 자격 증명 모음 컨텍스트 설정을 사용 중단할 계획입니다. 대신 자격 증명 모음 ID를 보관하거나 가져오고 다음과 같이 관련 명령으로 전달할 수 있습니다.

```powershell
$testVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$testVault.ID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정과 백업 복구 지점을 얼마나 오래 보관해야 하는지 지정합니다.

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)를 사용하여 기본 백업 정책 보존을 확인합니다.
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)를 사용하여 기본 백업 정책 일정을 확인합니다.
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) cmdlet을 사용하여 새로운 백업 정책을 만듭니다. 일정과 보존 정책 개체를 입력합니다.

기본값으로 시작 시간은 일정 정책 개체에 정의되어 있습니다. 다음 예제를 사용하여 시작 시간을 원하는 시간으로 변경합니다. 원하는 시작 시간은 UTC로도 지정해야 합니다. 다음 예제에서는 원하는 일일 백업 시간을 오전 01:00 UTC로 가정합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30분의 배수로 시작 시간을 제공해야 합니다. 위 예제에서 시작 시간은 "01:00:00"이나 "02:30:00"으로만 지정할 수 있습니다. 시작 시간은 "01:15:00"일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수를 새 정책의 매개 변수로 사용합니다(**NewSQLPolicy**). **NewSQLPolicy** 일일 "전체" 백업을 수행하며 180일 동안 보존하고 2시간마다 로그 백업을 수행

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

다음과 유사하게 출력됩니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>백업 사용

### <a name="registering-the-sql-vm"></a>SQL VM 등록

Azure VM 백업 및 Azure 파일 공유의 경우 Backup 서비스를 Azure Resource Manager 리소스에 연결하여 관련 세부 정보를 가져올 수 있습니다. SQL은 Azure VM 내의 애플리케이션이기 때문에 Backup 서비스에는 애플리케이션에 액세스하고 필요한 세부 정보를 가져올 권한이 있어야 합니다. 이를 위해 Recovery Services 자격 증명 모음을 사용하여 SQL 애플리케이션이 포함된 Azure VM을 *'등록'* 해야 합니다. 자격 증명 모음으로 SQL VM을 등록하면 SQL DB를 해당 자격 증명 모음으로만 보호할 수 있습니다. [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) PowerShell cmdlet을 사용하여 VM을 등록합니다.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID -Force
```

이 명령을 사용하면 리소스의 '백업 컨테이너'를 반환하고 상태는 '등록됨'으로 변경됩니다.

> [!NOTE]
> Force 매개 변수가 주어지지 않으면 '이 컨테이너의 보호를 사용하지 않도록 설정하시겠습니까'와 같이 확인을 묻는 메시지가 나타납니다. 메시지를 무시하고 "Y"로 답변해 확인합니다. 이것은 알려진 문제이며 이 메시지와 Force 매개 변수의 요구 사항을 제거하기 위해 노력하고 있습니다.

### <a name="fetching-sql-dbs"></a>SQL DB 가져오기

등록이 완료되면 Backup 서비스에 VM 내의 사용 가능한 모든 SQL 구성 요소를 나열할 수 있습니다. 이 자격 증명 모음에 아직 백업되지 않은 모든 SQL 구성 요소를 보려면 [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용하십시오.

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $testVault.ID
```

이 자격 증명 모음이 등록된 모든 SQL VM에서 보호되지 않은 모든 SQL 구성 요소와 ItemType 및 ServerName이 출력됩니다. '-Container' 매개 변수를 전달하거나 ItemType 플래그와 'Name' 및 'ServerName'을 조합하여 추가적으로 필터링함으로써 특정 SQL VM에 필터링하여 고유한 SQL 항목에 도달할 수 있습니다.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $testVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>백업 구성

이제 필수 SQL DB와 백업해야 하는 정책이 있으니 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) cmdlet을 사용하여 이 SQL DB에 대한 백업을 구성할 수 있습니다.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

이 명령을 사용하면 구성 백업이 완료될 때까지 대기하고 다음과 같은 출력을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>새 SQL DB 가져오기

컴퓨터를 등록하면 Backup 서비스가 사용 가능한 DB의 세부 정보를 가져옵니다. 이후에 등록된 컴퓨터에 SQL DB나 SQL 인스턴스가 추가되면 수동으로 백업 서비스를 트리거하여 다시 보호되지 않은 **모든** DB(새로 추가된 DB 포함)에 대한 새로운 조회를 수행해야 합니다. SQL VM에서 [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용하여 새로 조회합니다. 이 명령을 실행하면 작업이 완료될 때까지 대기합니다. 이후 [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용하여 보호되지 않은 SQL 구성 요소의 최신 목록을 가져옵니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $testVault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $testVault.ID
```

보호할 수 있는 관련 항목을 가져오면 [위 섹션](#configuring-backup)에 안내된 대로 백업을 사용하도록 설정합니다.
새로운 DB를 수동으로 검색하고 싶지 않을 경우 [아래](#enable-autoprotection) 설명과 같이 자동 보호를 사용할 수 있습니다.

## <a name="enable-autoprotection"></a>자동 보호 사용하도록 설정

나중에 추가된 모든 DB가 특정 정책을 통해 보호되도록 백업을 구성할 수 있습니다. 자동 보호를 사용하도록 설정하려면 [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet을 사용합니다.

향후 모든 DB를 백업하도록 하는 지침이기 때문에 SQLInstance 수준에서 작업이 완료됩니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $testVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $testVault.ID
```

자동 보호 의도가 주어지면 8시간마다 새로 추가된 DB를 컴퓨터로 가져오는 조회가 예약된 백그라운드 작업으로 진행됩니다.

## <a name="restore-sql-dbs"></a>SQL DB 복원

Azure Backup은 다음과 같은 방법으로 Azure VM에서 실행되는 SQL Server 데이터베이스를 복원할 수 있습니다.

* 트랜잭션 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 해당 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.
* 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

SQL DB를 복원하기 전에 [여기](restore-sql-database-azure-vm.md#restore-prerequisites)에 언급된 사전 요구 사항을 확인합니다.

> [!WARNING]
> RBAC와 관련된 보안 문제로 인해 PowerShell을 통해 SQL DB에 대한 복원 명령의 주요 변경 내용이 도입되었습니다. PowerShell을 통해 적절한 복원 명령을 제출하려면 Az 6.0.0 버전 이상으로 업그레이드하세요. 최신 PS 명령은 아래에 제공됩니다.

먼저 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용하여 관련성 있으며 백업된 SQL DB를 가져옵니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>관련 복원 시간 가져오기

위에서 설명한 것처럼 백업된 SQL DB를 전체/차등 복사본 **또는** 로그 지정 시간으로 복원할 수 있습니다.

#### <a name="fetch-distinct-recovery-points"></a>특정 복구 지점 가져오기

[Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint)를 사용하여 백업된 SQL DB에 대한 특정(전체/차등) 복구 지점을 가져옵니다.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $testVault.ID -StartDate $startdate -EndDate $endDate
```

다음 예제와 유사하게 출력됩니다.

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

'RecoveryPointId' 필터나 정렬 필터를 사용하여 관련 복구 지점을 가져옵니다.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $testVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>지정 시간 복구 지점 가져오기

DB를 특정 지정 시간으로 복원하려면 [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell cmdlet을 사용합니다. 이 cmdlet을 사용하면 해당 SQL 백업 항목의 손상되지 않은 연속적인 로그 체인의 시작 및 종료 시간을 나타내는 날짜 목록이 반환됩니다. 원하는 지정 시간이 이 범위 내에 있을 가능성이 높습니다.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $testVault.ID
```

출력은 다음 예제와 비슷합니다.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

위의 출력 결과는 표시된 시작 시간과 종료 시간 사이의 어떤 지정 시간으로나 복원할 수 있음을 의미합니다. 시간은 UTC 기준입니다. PowerShell에서 위에 표시된 범위 내에 있는 지정 시간을 생성합니다.

> [!NOTE]
> 복원할 로그 지정 시간을 선택했으면 DB가 복원된 전체 백업의 시작 지점을 지정하지 않아도 됩니다. Azure Backup 서비스는 전체 복구 계획, 즉 어떤 전체 백업을 선택할지, 어떤 로그 백업을 적용할지 등을 처리합니다.

### <a name="determine-recovery-configuration"></a>복구 구성 확인

SQL DB 복원의 경우 다음과 같은 복원 시나리오가 지원됩니다.

* 백업된 SQL DB를 다른 복구 지점의 데이터로 재정의 - OriginalWorkloadRestore
* SQL DB를 동일한 SQL 인스턴스의 새로운 DB로 복원 - AlternateWorkloadRestore
* SQL DB를 다른 SQL VM에 있는 다른 SQL 인스턴스의 새로운 DB로 복원 - AlternateWorkloadRestore
* SQL DB를 .bak 파일로 복원 - RestoreAsFiles

관련 복구 지점(고유 또는 로그 지정 시간)을 가져온 후 [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet을 사용하여 원하는 복구 계획에 따라 복구 구성 개체를 가져옵니다.

#### <a name="original-workload-restore"></a>원본 워크로드 복원

백업된 DB를 복구 지점 데이터로 재정의하려면 다음 예제와 같이 정확한 플래그와 관련 복구 지점을 지정하기만 하면 됩니다.

##### <a name="original-restore-with-distinct-recovery-point"></a>고유 복구 지점으로 원본 복원

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $testVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>로그 지정 시간으로 원본 복원

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $testVault.ID
```

#### <a name="alternate-workload-restore"></a>대체 워크로드 복원

> [!IMPORTANT]
> 백업된 SQL DB는 이 자격 증명 모음에 '등록된' Azure VM에서 다른 SQLInstance으로만 새로운 DB로 복원할 수 있습니다.

위에서 설명한 것처럼 대상 SQLInstance가 다른 Azure VM에 있는 경우 [이 자격 증명 모음에 등록되었으며](#registering-the-sql-vm) 관련 SQLInstance가 보호할 수 있는 항목으로 표시되는지 확인하십시오. 이 문서에서는 대상 SQLInstance 이름이 다른 VM "Contoso2" 내의 MSSQLSERVER라고 가정해 보겠습니다.

```powershell
$TargetContainer =  Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -Status Registered  -VaultId $testVault.ID -FriendlyName "Contoso2"
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "MSSQLSERVER" -ServerName "Contoso2" -VaultId $testVault.ID
```

그런 다음, 해당 복구 지점, 아래와 같이 올바른 플래그가 있는 대상 SQL 인스턴스 및 대상 SQL 인스턴스가 존재하는 대상 컨테이너를 전달하면 됩니다.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>고유 복구 지점으로 대체 복원

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $testVault.ID -TargetContainer $TargetContainer
```

##### <a name="alternate-restore-with-log-point-in-time"></a>로그 지정 시간으로 대체 복원

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $testVault.ID -TargetContainer $TargetContainer
```

##### <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스 대신 .bak 파일로 복원하려면 **파일로 복원** 옵션을 선택하십시오. 백업된 SQL DB는 이 자격 증명 모음에 등록된 모든 대상 VM으로 복원할 수 있습니다.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>고유 복구 지점을 사용하여 파일로 복원

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>최신 전체 로그 지정 시간에서 파일로 복원

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>특정 전체 로그 지정 시간에서 파일로 복원

복원에 사용해야 하는 특정 전체를 제시하고자 하는 경우 다음 명령을 사용하십시오.

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

[Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet을 통해 얻은 최종 복구 지점 구성 개체에는 아래와 같이 복원에 필요한 모든 관련 정보가 있습니다.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

복원된 DB 이름, OverwriteWLIfpresent, NoRecoveryMode, targetPhysicalPath 필드를 편집할 수 있습니다. 아래와 같은 대상 파일 경로에 대한 자세한 내용을 확인하세요.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

아래와 같이 관련 PowerShell 속성을 문자열 값으로 설정합니다.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> 복원 작업은 구성 개체를 기반으로 이루어지므로 최종 복구 구성 개체에 필수적이며 관련성 있는 모든 값이 있는지 확인하십시오.

#### <a name="alternate-workload-restore-to-a-vault-in-secondary-region"></a>보조 지역의 자격 증명 모음에 대한 대체 워크로드 복원

> [!IMPORTANT]
> PowerShell에서 SQL의 보조 지역 복원에 대한 지원은 Az 6.0.0에서 사용할 수 있습니다.

지역 간 복원을 사용하도록 설정한 경우 복구 지점도 쌍을 이루는 보조 지역으로 복제됩니다. 그런 다음, 해당 복구 지점을 가져와서 쌍을 이루는 해당 지역에 있는 머신에 대한 복원을 트리거할 수 있습니다. 일반 복원과 마찬가지로, 대상 머신은 보조 지역의 대상 자격 증명 모음에 등록되어야 합니다. 다음 단계 시퀀스는 엔드투엔드 프로세스를 명확히 해야 합니다.

* 보조 지역에 복제되는 백업 항목을 가져옵니다.
* 이러한 항목의 경우 보조 지역에 복제되는 복구 지점(고유 및/또는 로그)을 가져옵니다.
* 그런 다음, 쌍을 이루는 보조 지역 내의 자격 증명 모음에 등록된 대상 서버를 선택합니다.
* 해당 서버에 대한 복원을 트리거하고 JobId를 사용하여 추적합니다.

#### <a name="fetch-backup-items-from-secondary-region"></a>보조 지역에서 백업 항목 가져오기

일반적인 명령을 사용하여 보조 지역에서 모든 SQL 백업 항목을 가져오지만 보조 지역에서 이러한 항목을 가져와야 한다는 것을 나타내는 추가 매개 변수를 사용합니다.

```powershell
$secondaryBkpItems = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload  -WorkloadType MSSQL  -VaultId $testVault.ID -UseSecondaryRegion
```

##### <a name="fetch-distinct-recovery-points-from-secondary-region"></a>보조 지역에서 고유 복구 지점 가져오기

[Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint)를 사용하여 백업된 SQL DB에 대한 고유(전체/차등) 복구 지점을 가져오고 보조 지역에서 가져온 복구 지점임을 나타내는 매개 변수를 추가합니다.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $secondaryBkpItems[0] -VaultId $testVault.ID -StartDate $startdate -EndDate $endDate -UseSecondaryRegion
```

다음 예제와 유사하게 출력됩니다.

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

'RecoveryPointId' 필터나 정렬 필터를 사용하여 관련 복구 지점을 가져옵니다.

```powershell
$FullRPFromSec = Get-AzRecoveryServicesBackupRecoveryPoint -Item $secondaryBkpItems[0] -VaultId $testVault.ID -RecoveryPointId "6660368097802" -UseSecondaryRegion
```

##### <a name="fetch-log-recovery-points-from-secondary-region"></a>보조 지역에서 로그 복구 지점 가져오기

보조 지역에서 해당 SQL 백업 항목에 대한 끊어지지 않은 연속 로그 체인의 시작 시간과 종료 시간을 반환하는 ' *-UseSecondaryRegion*' 매개변수와 함께 [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell cmdlet을 사용합니다. 원하는 지정 시간이 이 범위 내에 있을 가능성이 높습니다.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $secondaryBkpItems[0] -VaultId $testVault.ID -UseSecondaryRegion
```

출력은 다음 예제와 비슷합니다.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

위의 출력 결과는 표시된 시작 시간과 종료 시간 사이의 어떤 지정 시간으로나 복원할 수 있음을 의미합니다. 시간은 UTC 기준입니다. PowerShell에서 위에 표시된 범위 내에 있는 지정 시간을 생성합니다.

#### <a name="fetch-target-server-from-secondary-region"></a>보조 지역에서 대상 서버 가져오기

보조 지역에서는 자격 증명 모음과 해당 자격 증명 모음에 등록된 대상 서버가 필요합니다. 보조 지역 대상 컨테이너와 SQL 인스턴스가 있으면 기존 cmdlet을 다시 사용하여 복원 워크로드 구성을 생성할 수 있습니다. 이 문서에서는 VM 이름이 "secondaryVM"이고 해당 VM 내의 인스턴스 이름이 "MSSQLInstance"라고 가정해 보겠습니다.

먼저 보조 지역에 있는 관련 자격 증명 모음을 가져온 다음, 해당 자격 증명 모음 내에 등록된 컨테이너를 가져옵니다.

```powershell
$PairedRegionVault = Get-AzRecoveryServicesVault -ResourceGroupName SecondaryRG -Name PairedVault
$secContainer =  Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -Status Registered  -VaultId $PairedRegionVault.ID -FriendlyName "secondaryVM"
```

등록된 컨테이너를 선택한 후에는 DB를 복원해야 하는 컨테이너 내의 SQL 인스턴스를 가져옵니다. 여기서는 "secondaryVM" 내에 SQL 인스턴스가 1개 있고 해당 인스턴스를 가져온다고 가정합니다.

```powershell
$secSQLInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -VaultId $PairedRegionVault.ID -Container $secContainer
```

#### <a name="prepare-the-recovery-configuration"></a>복구 구성 준비

일반적인 SQL 복원에 대해 [위에서](#determine-recovery-configuration) 설명한 것처럼 동일한 명령을 다시 사용하여 관련 복구 구성을 생성할 수 있습니다.

##### <a name="for-full-restores-from-secondary-region"></a>보조 지역에서 전체 복원의 경우

```powershell
Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRPFromSec[0] -TargetItem $secSQLInstance -AlternateWorkloadRestore -VaultId $vault.ID -TargetContainer $secContainer
```

##### <a name="for-log-point-in-time-restores-from-secondary-region"></a>보조 지역에서 로그 특정 시점 복원의 경우

```powershell
Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $secondaryBkpItems[0] -TargetItem $secSQLInstance  -AlternateWorkloadRestore -VaultId $vault.ID -TargetContainer $secContainer
```

주 지역 복원 또는 보조 지역 복원에 대한 관련 구성을 얻은 후에는 동일한 복원 명령을 사용하여 복원을 트리거하고 나중에 jobID를 사용하여 추적할 수 있습니다.

### <a name="restore-with-relevant-configuration"></a>관련 구성으로 복원

관련 복구 구성 개체가 주어지고 확인되었으면 [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용하여 복원 프로세스를 시작합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $testVault.ID
```

복원 작업은 추적할 작업을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL 백업 관리

### <a name="on-demand-backup"></a>주문형 백업

DB에 대해 백업을 사용하도록 설정했다면 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용하여 DB에 대한 주문형 백업을 트리거할 수도 있습니다. 다음 예제에서는 압축을 사용하도록 설정되었으며 전체 백업을 60일 간 보존해야 하는 SQL DB 전체 백업을 트리거합니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $testVault.ID -ExpiryDateTimeUTC $endDate
```

주문형 백업 명령은 추적할 작업을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

출력이 손실되거나 관련 작업 ID를 얻고자 하는 경우 Azure Backup 서비스에서 [작업 목록을 가져온 다음](#track-azure-backup-jobs) 작업과 세부 정보를 추적합니다.

### <a name="change-policy-for-backup-items"></a>백업 항목에 대한 정책 변경

백업된 항목의 정책을 *정책1* 에서 *정책2* 로 변경할 수 있습니다. 백업된 항목의 정책을 변경하려면 관련 정책과 백업 항목을 가져와 매개 변수로 백업 항목의 [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 명령을 사용합니다.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

이 명령을 사용하면 구성 백업이 완료될 때까지 대기하고 다음과 같은 출력을 반환합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>기존 백업 정책 편집

기존 정책을 편집하려면 [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 명령을 사용합니다.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

오류를 추적하려면 잠시 후 백업 작업을 확인합니다. 실패가 있는 경우 문제를 해결해야 합니다. 그 다음 **FixForInconsistentItems** 매개 변수로 편집 정책 명령을 다시 실행하여 이전에 실패한 모든 작업에 대한 모든 백업 항목의 정책을 편집합니다.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>SQL VM 다시 등록

> [!WARNING]
> 다시 등록하려 하기 전에 오류 증상과 원인을 이해하려면 이 [문서](backup-sql-server-azure-troubleshoot.md#re-registration-failures)를 확인하십시오.

SQL VM의 재등록을 트리거하려면 관련 백업 컨테이너를 가져오고 등록 cmdlet으로 전달합니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

보호를 중지하려면 [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용하면 됩니다. 이렇게 하면 예약된 백업이 중지되지만 지금까지 백업된 데이터는 영구적으로 보존됩니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $testVault.ID
```

#### <a name="delete-backup-data"></a>백업 데이터 삭제

자격 증명 모음에 저장된 백업 데이터를 완전히 제거하려면 [보호 '사용하지 않도록 설정' 명령에](#retain-data) '-RemoveRecoveryPoints' 플래그/스위치만 추가하면 됩니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $testVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>자동 보호 사용하지 않도록 설정

SQLInstance에 대해 자동 보호가 구성된 경우 [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet을 사용하여 사용하지 않도록 설정할 수 있습니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $testVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID
```

#### <a name="unregister-sql-vm"></a>SQL VM 등록 해제

SQL 서버의 모든 DB가 [더 이상 보호되지 않고 백업 데이터가 없으면](#delete-backup-data) 이 자격 증명 모음에서 SQL VM을 등록 취소할 수 있습니다. 그 다음에만 다른 자격 증명 모음에 DB를 보호할 수 있습니다. SQL VM을 등록 취소하려면 [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) PowerShell cmdlet을 사용하십시오.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $testVault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure Backup 작업 추적

Azure Backup은 SQL 백업의 사용자 트리거 작업만 추적한다는 점에 유의해야 합니다. 예약된 백업(로그 백업 포함)은 포털이나 PowerShell에 나타나지 않습니다. 하지만 예약된 작업이 실패할 경우 [백업 경고](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)가 생성되고 포털에 표시됩니다. [Azure Monitor를 사용](backup-azure-monitoring-use-azuremonitor.md)하여 예약된 모든 작업과 다른 관련 정보를 추적하십시오.

사용자는 백업과 같은 비동기 작업의 [출력](#on-demand-backup)에서 반환된 JobID를 사용하여 주문형/사용자가 트리거한 작업을 추적할 수 있습니다. 작업과 세부 정보를 추적하려면 [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell cmdlet을 사용하십시오.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $testVault.ID
```

Azure Backup 서비스에서 주문형 작업 및 상태 목록을 가져오려면 [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell cmdlet을 사용하십시오. 다음 예제에서는 진행 중인 모든 SQL 작업을 반환합니다.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

진행 중인 작업을 취소하려면 [Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) PowerShell cmdlet을 이용합니다.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹 관리

SQL Always On 가용성 그룹의 경우 가용성 그룹(AG)의 [모든 노드를 등록](#registering-the-sql-vm)해야 합니다. 모든 노드의 등록이 완료되면 보호할 수 있는 항목에 따라 SQL 가용성 그룹 개체가 논리적으로 만들어집니다. SQL AG 아래의 데이터베이스는 'SQLDatabase'로 나열됩니다. 노드는 독립 실행형 인스턴스로 표시되며 기본 SQL 데이터베이스는 SQL 데이터베이스에도 나열됩니다.

예를 들어 SQL AG에 *sql-server-0* 및 *sql-server-1* 두 개의 노드와 1개의 SQL AG DB가 있다고 가정하겠습니다. 두 개의 노드가 모두 등록되면 [보호할 수 있는 항목을 나열](#fetching-sql-dbs)했을 때 다음과 같은 구성 요소가 나열됩니다.

* SQL AG 개체 - SQLAvailabilityGroup으로 보호할 수 있는 항목 종류
* SQL AG DB - SQLDatabase로 보호할 수 있는 항목 종류
* sql-server-0 - SQLInstance로 보호할 수 있는 항목 종류
* sql-server-1 - SQLInstance로 보호할 수 있는 항목 종류
* sql-server-0 아래의 모든 기본 SQL DB(마스터, 모델, msdb) - SQLDatabase로 보호할 수 있는 항목 종류
* sql-server-1 아래의 모든 기본 SQL DB(마스터, 모델, msdb) - SQLDatabase로 보호할 수 있는 항목 종류

sql-server-0, sql-server-1은 [백업 컨테이너를 나열](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)하면 "AzureVMAppContainer"로도 나열됩니다.

관련 데이터베이스를 가져와 [백업을 사용하도록 설정](#configuring-backup)하면 [주문형 백업](#on-demand-backup)과 [복원 PowerShell cmdlet](#restore-sql-dbs)이 동일해집니다.
