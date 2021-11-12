---
title: Azure 가상 머신의 선택적 디스크 백업 및 복원
description: 이 문서에서는 Azure 가상 머신 백업 솔루션을 사용한 선택적 디스크 백업 및 복원을 지원합니다.
ms.topic: conceptual
ms.date: 11/10/2021
ms.custom: references_regions , devx-track-azurecli, devx-track-azurepowershell3
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: f2b4eda015bca45e586d77302eeedef18b217fbc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312422"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Azure 가상 머신의 선택적 디스크 백업 및 복원

Azure Backup은 가상 머신 백업 솔루션을 함께 사용하여 VM의 모든 디스크(운영 체제 및 데이터) 백업 작업을 지원합니다. 이제 선택적 디스크 백업 및 복원 기능을 사용하여 VM에서 데이터 디스크의 하위 집합을 백업할 수 있습니다. 이는 사용자의 백업 및 복원 요구 사항을 충족하는 능률적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함되는 디스크만 포함됩니다. 나아가 복원 작업 중에 해당 복구 지점에서 복원된 디스크의 하위 집합을 확보할 수 있게 됩니다. 스냅샷에서의 복원과 자격 증명 모음에서의 복원 모두에 해당합니다.

## <a name="scenarios"></a>시나리오

이 솔루션은 다음과 같은 시나리오에서 특히 유용합니다.

1. 백업 스토리지 비용을 최소화하기 위해, 한 디스크 또는 디스크 하위 집합에서만 중요 데이터만 백업하고 VM에 연결된 나머지 디스크는 백업하지 않으려는 경우
2. VM 또는 데이터의 일부에 대해 다른 백업 솔루션이 있는 경우 가능한 최상의 기능을 사용하여 효율적이고 강력한 시스템을 구축하기 위해, 다른 워크로드 백업 솔루션을 사용하여 데이터베이스나 데이터를 백업하고 나머지 데이터나 디스크에 대해서만 Azure VM 수준 백업을 사용하려는 경우를 예로 들 수 있습니다.

PowerShell 또는 Azure CLI를 사용하여 Azure VM의 선택적 디스크 백업을 구성할 수 있습니다. 스크립트를 사용하여 데이터 디스크를 해당 LUN 번호로 포함하거나 제외할 수 있습니다. 현재 Azure Portal을 통해 선택적 디스크 백업을 구성하는 기능은 **OS 디스크만 백업** 옵션으로 제한됩니다. 따라서 OS 디스크에서의 Azure VM 백업을 구성하고, 연결된 모든 데이터 디스크는 제외할 수 있습니다.

>[!NOTE]
> OS 디스크는 기본적으로 VM 백업에 추가되며 제외할 수 없습니다.

## <a name="using-azure-cli"></a>Azure CLI 사용

Az CLI 버전 2.0.80 이상을 사용해야 합니다. CLI 버전은 다음 명령을 사용하여 구할 수 있습니다.

```azurecli
az --version
```

Recovery Services 자격 증명 모음 및 VM이 있는 구독 ID에 로그인합니다.

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>아래의 각 명령에는 자격 증명 모음에 해당하는 **resourcegroup** 이름(개체 아님)만 필요합니다.

### <a name="configure-backup-with-azure-cli"></a>Azure CLI를 사용하여 백업 구성

보호 구성 작업 중에는 디스크의 LUN 번호를 백업에서 포함 또는 제외하도록 **inclusion** / **exclusion** 매개 변수로 디스크 목록 설정을 지정해야 합니다.

>[!NOTE]
>보호 구성 작업은 이전 설정을 재정의하며 누적되지 않습니다.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

VM이 자격 증명 모음과 동일한 리소스 그룹에 있지 않으면 **ResourceGroup** 은 자격 증명 모음이 생성된 리소스 그룹을 참조합니다. VM 이름 대신 아래 표시된 대로 VM ID를 제공합니다.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Azure CLI를 사용하여 이미 백업된 VM에 대한 보호 수정

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Azure CLI를 사용하여 백업을 구성하는 동안 OS 디스크만 백업

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Azure CLI를 사용하여 보호를 수정하는 동안 OS 디스크만 백업

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Azure CLI를 사용하여 디스크 복원

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Azure CLI를 사용하여 OS 디스크만 복원

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Azure CLI를 사용하여 디스크 제외 세부 정보를 얻기 위해 보호된 항목 가져오기

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

아래와 같이 보호된 항목에 **diskExclusionProperties** 매개 변수가 더 추가되었습니다.

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Azure CLI를 사용하여 백업 작업 가져오기

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

이 명령은 아래와 같이 백업된 디스크 및 제외 된 디스크의 세부 정보를 가져올 수 있습니다.

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

_BackupJobID_ 는 백업 작업 이름입니다. 작업 이름을 가져오려면 다음 명령을 실행합니다.

```azurecli
az backup job list --resource-group {resourcegroup} --vault-name {vaultname}
```
### <a name="list-recovery-points-with-azure-cli"></a>Azure CLI를 사용하여 복구 지점 나열

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

이렇게 하면 VM에 연결 및 백업된 디스크 수에 대한 정보가 제공됩니다.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Azure CLI를 사용하여 복구 지점 가져오기

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

각 복구 지점에는 다음과 같이 포함된 디스크와 제외된 디스크 정보가 있습니다.

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Azure CLI를 사용하여 디스크 제외 설정을 제거하고 보호된 항목 가져오기

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup}
```

이 명령을 실행하면 `"diskExclusionProperties": null` 이 표시됩니다.

## <a name="using-powershell"></a>PowerShell 사용

Azure PowerShell 버전 3.7.0 이상을 사용해야 합니다.

보호 구성 작업 중에는 디스크의 LUN 번호를 백업에서 포함 또는 제외하도록 inclusion/exclusion 매개 변수로 디스크 목록 설정을 지정해야 합니다.

>[!NOTE]
>보호 구성 작업은 이전 설정을 재정의하며 누적되지 않습니다.

### <a name="enable-backup-with-powershell"></a>PowerShell을 통해 백업을 사용하도록 설정

예를 들면 다음과 같습니다.

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Set-AzRecoveryServicesVaultContext -Vault $targetVault
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>PowerShell을 사용하여 백업을 구성하는 동안 OS 디스크만 백업

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>PowerShell을 사용하여 보호 수정에서 전달할 백업 항목 개체 가져오기

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $targetVault.ID -FriendlyName "V2VM"
```

다음 cmdlet에서, 위에서 구한 **$item** 개체를 **–Item** 매개 변수에 전달해야 합니다.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>PowerShell을 사용하여 이미 백업된 VM에 대한 보호 수정

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>PowerShell을 사용하여 보호를 수정하는 동안 OS 디스크만 백업

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>PowerShell을 사용하여 디스크 제외 설정 초기화

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

> [!NOTE]
> 정책 매개 변수가 필요하다는 오류와 함께 명령이 실패하면 백업 항목의 보호 상태를 확인합니다. 보호가 중지되어 보호를 다시 시작하고 이전의 모든 디스크 제외 설정을 다시 설정하는 정책이 필요할 수 있습니다.

### <a name="restore-selective-disks-with-powershell"></a>PowerShell을 사용하여 선택적 디스크 복원

```azurepowershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [$disks]
```

### <a name="restore-only-os-disk-with-powershell"></a>PowerShell을 사용하여 OS 디스크만 복원

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Azure Portal 사용

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Azure Portal을 사용하여 VM 백업 세부 정보 창과 백업 작업 세부 정보 창에서 포함 및 제외된 디스크를 볼 수 있습니다.  복원 중에 복원을 시작할 복구 지점을 선택할 때 해당 복구 지점에서의 백업된 디스크를 볼 수 있습니다.

여기서는 VM 백업 세부 정보 창에서 포털의 가상 머신에 대해 포함 및 제외된 디스크를 확인할 수 있습니다.

![백업 세부 정보 창에서 포함 및 제외된 디스크 보기](./media/selective-disk-backup-restore/backup-details.png)

여기에서는 작업 세부 정보 창의 백업에 포함 및 제외된 디스크를 볼 수 있습니다.

![작업 세부 정보 창에서 포함 및 제외된 디스크 보기](./media/selective-disk-backup-restore/job-details.png)

여기에서는 복원을 시작할 복구 지점을 선택할 때 복원된 디스크를 볼 수 있습니다.

![복원 중 백업된 디스크 보기](./media/selective-disk-backup-restore/during-restore.png)

Azure Portal을 통해 VM에 대해 선택적 디스크 백업 환경을 구성하는 기능은 **OS 디스크만 백업** 옵션으로 제한됩니다. 이미 백업된 VM이나, VM의 특정 데이터 디스크에 대한 고급 포함 또는 제외에 선택적 디스크 백업을 사용하려면 PowerShell 또는 Azure CLI를 사용합니다.

>[!NOTE]
>데이터가 여러 디스크에 걸쳐 있는 경우 모든 종속 디스크가 백업에 포함되어야 합니다. 볼륨의 모든 종속 디스크를 백업하지 않으면 복원 중에 백업되지 않은 디스크로 구성된 볼륨이 만들어지지 않습니다.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Azure Portal에서 OS 디스크만 백업

Azure Portal을 사용하여 백업을 사용하도록 설정할 경우 **OS 디스크만 백업** 옵션을 선택할 수 있습니다. 따라서 OS 디스크에서의 Azure VM의 백업을 구성하고, 연결된 모든 데이터 디스크는 제외할 수 있습니다.

![OS 디스크만 백업 구성](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Azure REST API 사용

일부 선택한 디스크에서의 Azure VM Backup을 구성하거나, [여기](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)에서 설명한 대로 일부 디스크를 포함/제외하도록 기존 VM의 보호를 수정할 수 있습니다.

## <a name="selective-disk-restore"></a>선택적 디스크 복원

선택적 디스크 복원은 선택적 디스크 백업 기능을 사용하도록 설정할 때 제공되는 추가 기능입니다. 이 기능을 사용하면 복구 지점에서 백업된 모든 디스크로부터 선택적 디스크를 복원할 수 있습니다. 더 효율적이며, 복원이 필요한 디스크가 어떤 것인지 알고 있는 시나리오에서 시간을 절약할 수 있습니다.

- OS 디스크는 VM 백업 및 복원에 기본적으로 포함되며 제외할 수 없습니다.
- 선택적 디스크 복원은 디스크 제외 기능을 사용하도록 설정한 후 만들어진 복구 지점에 대해서만 지원됩니다.
- 디스크 제외 설정을 **사용** 하는 백업은 **디스크 복원** 옵션만 지원합니다. 이 경우 **VM 복원** 또는 **기존 항목 바꾸기** 복원 옵션은 지원되지 않습니다.

![VM을 복원하고 기존 항목을 바꾸는 옵션은 복원 작업 중에 사용할 수 없습니다.](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>제한 사항

선택적 디스크 백업 기능은 클래식 가상 머신 및 암호화된 가상 머신에 대해 지원되지 않습니다. 따라서 ADE(Azure Disk Encryption)로 암호화된 Azure VM은 Windows VM의 암호화에 BitLocker를 사용하며, Linux VM에 대한 dm-crypt 기능은 지원되지 않습니다.

선택적 디스크 백업 기능을 사용하도록 설정된 VM에 대해서는 **새 VM 만들기** 및 **기존 항목 바꾸기** 복원 옵션이 지원되지 않습니다.

현재 Azure VM 백업은 Ultra Disk 또는 공유 디스크가 연결되어 있는 VM을 지원하지 않습니다. 디스크를 제외하고 VM을 백업하는 경우에는 선택적 디스크 백업을 사용할 수 없습니다.

Azure VM을 백업하는 동안 디스크 제외 또는 선택적 디스크를 사용하는 경우 _[보호를 중지하고 백업 데이터를 보존](backup-azure-manage-vms.md#stop-protection-and-retain-backup-data)_ 합니다. 이 리소스에 대한 백업을 다시 시작할 때 디스크 제외 설정을 다시 설정해야 합니다.

## <a name="billing"></a>결제

Azure 가상 머신 백업은 기존 가격 책정 모델을 따르며 [여기](https://azure.microsoft.com/pricing/details/backup/)에서 상세히 설명합니다.

**OS 디스크만** 옵션을 사용하여 백업하도록 선택한 경우 **PI(보호된 인스턴스) 비용** 은 OS 디스크에 대해서만 계산됩니다.  백업을 구성하고 하나 이상의 데이터 디스크를 선택한 경우 PI 비용은 VM에 연결된 모든 디스크에 대해 계산됩니다. **백업 스토리지 비용** 은 포함된 디스크만을 기준으로 하므로 스토리지 비용을 절감할 수 있습니다. **스냅샷 비용** 은 항상 VM의 모든 디스크에 대해 계산됩니다(포함 및 제외된 디스크 모두).

CRR(교차 지역 복원) 기능을 선택한 경우 디스크 제외 후 [CRR 가격 책정](https://azure.microsoft.com/pricing/details/backup/)이 백업 스토리지 비용에 적용됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Windows 및 Linux의 OS 디스크만 백업에 대한 PI(보호된 인스턴스) 비용은 어떻게 계산하나요?

PI 비용은 VM의 실제(사용) 크기에 따라 계산됩니다.

- Windows의 경우: 사용 공간 계산은 운영 체제를 저장하는 드라이브(보통 C:)를 기준으로 합니다.
- Linux의 경우: 사용 공간 계산은 루트 파일 시스템(/)이 탑재된 디바이스를 기준으로 합니다.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>OS 디스크만 백업을 구성했습니다. 모든 디스크에 대해 스냅샷이 발생하는 이유는 무엇인가요?

선택적 디스크 백업 기능을 사용하면 백업의 일부인 포함된 디스크를 강화하여 백업 자격 증명 모음 스토리지 비용을 줄일 수 있습니다. 그러나 VM에 연결된 모든 디스크에 대해 스냅샷이 생성됩니다. 따라서 스냅샷 비용은 항상 VM의 모든 디스크에 대해 계산됩니다(포함 및 제외된 디스크 모두). 자세한 내용은 [청구](#billing)를 참조하세요.

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>VM에 연결된 Ultra Disk 또는 공유 디스크를 제외하여 Azure 가상 머신에 대한 백업을 구성할 수 없습니다.

선택적 디스크 백업 기능은 Azure 가상 머신 백업 솔루션을 바탕으로 제공되는 기능입니다. 현재 Azure VM 백업은 Ultra Disk 또는 공유 디스크가 연결되어 있는 VM을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure VM 백업의 지원 매트릭스](backup-support-matrix-iaas.md)
- [질문과 대답 - Azure VM 백업](backup-azure-vm-backup-faq.yml)
