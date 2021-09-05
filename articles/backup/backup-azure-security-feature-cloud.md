---
title: Azure Backup의 일시 삭제
description: Azure Backup의 보안 기능을 사용하여 백업을 좀 더 안전하게 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ba21b9e7fd864b70ad2916de2053530677438c5
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113304213"
---
# <a name="soft-delete-for-azure-backup"></a>Azure Backup의 일시 삭제

맬웨어, 랜섬웨어 및 침입과 같은 보안 문제에 대한 우려는 증가하고 있습니다. 이러한 보안 문제는 돈과 데이터 측면 모두에서 비용이 많이 들 수 있습니다. 이러한 공격을 방지하기 위해 Azure Backup은 이제 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다.

이러한 기능 중 하나는 일시 삭제입니다. 일시 삭제를 사용하면 악의적인 행위자가 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 추가로 14일 동안 보관되므로 데이터 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대한 추가 보존 기간은 14일이며 비용이 발생하지 않습니다.

다음 서비스에 대해 일시 삭제 보호를 사용할 수 있습니다.

- [Azure 가상 머신의 일시 삭제](soft-delete-virtual-machines.md)
- [Azure VM의 SQL Server 일시 삭제 및 Azure VM 워크로드의 SAP HANA 일시 삭제](soft-delete-sql-saphana-in-azure-vm.md)

이 순서도는 일시 삭제를 사용하도록 설정하는 경우 백업 항목의 여러 단계와 상태를 보여 줍니다.

![일시 삭제된 백업 항목의 수명 주기](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>일시 삭제 사용 및 사용 안 함

새로 만든 자격 증명 모음에서는 기본적으로 일시 삭제가 사용하도록 설정되어 실수로 인한 삭제 또는 악의적인 삭제로부터 백업 데이터를 보호합니다.  이 기능은 사용하지 않도록 설정하지 않는 것이 좋습니다. 일시 삭제를 사용하지 않도록 설정해야 하는 유일한 상황은 보호된 항목을 새 자격 증명 모음으로 이동할 계획이고 삭제 및 다시 보호하기 전에 필요한 14일을 기다릴 수 없는(예: 테스트 환경) 경우입니다.

자격 증명 모음에서 일시 삭제를 사용하지 않도록 설정하려면 해당 자격 증명 모음에 대한 백업 기여자 역할이 있어야 합니다(자격 증명 모음에서 Microsoft.RecoveryServices/Vaults/backupconfig/write를 수행할 수 있는 권한이 있어야 함). 이 기능을 사용하지 않도록 설정하면 나중에 보호된 항목을 삭제할 때마다 복원 기능 없이 즉시 제거됩니다. 이 기능을 사용하지 않도록 설정하기 전에 일시 삭제 상태의 백업 데이터는 14일 동안 일시 삭제 상태로 유지됩니다. 이러한 항목을 즉시 영구적으로 삭제하려면 삭제를 취소했다가 다시 삭제하여 영구적으로 삭제해야 합니다.

일시 삭제를 사용하지 않도록 설정하면 모든 유형의 작업에 대해 해당 기능을 사용할 수 없다는 점에 유의해야 합니다. 예를 들어, 동일한 자격 증명 모음의 가상 머신에 대해 일시 삭제를 사용하도록 설정하면서 SQL Server 또는 SAP HANA DB에 대해서만 일시 삭제를 사용하지 않도록 설정할 수는 없습니다. 세부적인 제어를 위해 별도의 자격 증명 모음을 만들 수 있습니다.

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure Portal를 사용하여 일시 삭제 사용 안 함

일시 삭제를 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 자격 증명 모음으로 이동하고 **설정** -> **속성** 으로 이동합니다.
2. 속성 창에서 **보안 설정** -> **업데이트** 를 선택합니다.  
3. 보안 설정 창의 **일시 삭제** 아래에서 **사용 안 함** 을 선택합니다.

![일시 삭제 사용 안 함](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure PowerShell를 사용하여 일시 삭제 사용 안 함

> [!IMPORTANT]
> Azure PowerShell를 사용하여 일시 삭제를 사용하는 데 필요한 Az.RecoveryServices 최소 버전은 2.2.0입니다. 최신 버전을 받으려면 ```Install-Module -Name Az.RecoveryServices -Force```를 사용합니다.

사용하지 않도록 설정하려면 [Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) PowerShell cmdlet을 사용합니다.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API를 사용하여 일시 삭제 사용 안 함

REST API를 사용하여 일시 삭제 기능을 사용하지 않도록 설정하려면 [여기](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)에 설명된 단계를 참조하세요.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>일시 삭제된 백업 항목을 영구적으로 삭제

이 기능을 사용하지 않도록 설정하기 전에 일시 삭제 상태였던 백업 데이터는 일시 삭제된 상태를 유지합니다. 이러한 항목을 즉시 영구적으로 삭제하려면 삭제를 취소했다가 다시 삭제하여 영구적으로 삭제합니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

다음 단계를 수행합니다.

1. [일시 삭제를 사용하지 않도록 설정](#enabling-and-disabling-soft-delete)하려면 이 단계를 따릅니다.

2. Azure Portal에서 자격 증명 모음으로 이동하고, **백업 항목** 으로 이동한 후 일시 삭제된 항목을 선택합니다.

   ![일시 삭제된 항목 선택](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. **삭제 취소** 옵션을 선택합니다.

   ![삭제 취소 선택](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. 창이 나타납니다. **삭제 취소** 를 선택합니다.

   ![삭제 취소 선택](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. **백업 데이터 삭제** 를 선택하여 백업 데이터를 영구적으로 삭제합니다.

   ![백업 데이터 삭제 선택](./media/backup-azure-manage-vms/delete-backup-button.png)

6. 백업 항목의 이름을 입력하여 복구 지점을 삭제한다고 확인합니다.

   ![백업 항목의 이름 입력](./media/backup-azure-manage-vms/delete-backup-data.png)

7. 해당 항목에 대한 백업 데이터를 삭제하려면 **삭제** 를 선택합니다. 알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

일시 삭제를 사용하지 않도록 설정하기 전에 항목을 삭제한 경우 일시 삭제 상태를 유지합니다. 즉시 삭제하려면 삭제 작업을 되돌린 후 다시 수행해야 합니다.

일시 삭제 상태에 있는 항목을 식별합니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

일시 삭제를 사용하도록 설정했을 때 수행된 삭제 작업을 되돌립니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

이제 일시 삭제를 사용할 수 없으므로 삭제 작업으로 인해 백업 데이터가 즉시 제거됩니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API 사용

일시 삭제를 사용하지 않도록 설정하기 전에 항목을 삭제한 경우 일시 삭제 상태를 유지합니다. 즉시 삭제하려면 삭제 작업을 되돌린 후 다시 수행해야 합니다.

1. 먼저 [여기](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)에 설명된 단계를 사용하여 삭제 작업을 실행 취소합니다.
2. 그런 후 [여기](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)에 설명된 단계를 사용하여 REST API를 통해 일시 삭제 기능을 사용하지 않도록 설정합니다.
3. [여기](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)에 언급된 REST API를 사용하는 백업을 삭제합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>모든 자격 증명 모음에서 일시 삭제 기능을 사용하도록 설정해야 하나요?

아니요, 이 기능은 기본 제공되며 기본적으로 모든 Recovery Services 자격 증명 모음에 대해 사용하도록 설정되어 있습니다.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>삭제 작업이 완료된 후 내 데이터가 일시 삭제 상태로 유지되는 일 수를 구성할 수 있나요?

아니요, 삭제 작업 후 14일의 추가 보존 기간으로 고정됩니다.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>이 14일의 추가 보존을 위해 비용을 지불해야 하나요?

아니요, 이 14일의 추가 보존은 일시 삭제 기능의 일부로 무료로 제공됩니다.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>내 데이터가 일시 삭제 상태일 때 복원 작업을 수행할 수 있나요?

아니요, 복원하려면 일시 삭제된 리소스를 삭제 취소해야 합니다. 삭제 취소 작업을 수행하면 리소스가 특정 시점으로 복원될 수 있는 **데이터 보존을 사용하는 보호 중지** 로 전환됩니다. 가비지 수집기는 이 상태에서 일시 중지 상태를 유지합니다.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>내 스냅샷은 자격 증명 모음의 복구 지점과 동일한 수명 주기를 따르나요?

예.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>일시 삭제된 리소스에 대해 예약된 백업을 다시 트리거하려면 어떻게 하나요?

다시 시작 작업을 수행한 후 삭제 취소를 수행하면 리소스가 다시 보호됩니다. 다시 시작 작업은 예약 된 백업을 트리거하는 백업 정책을 선택한 보존 기간에 연결합니다. 또한 가비지 수집기는 다시 시작 작업이 완료되는 즉시 실행됩니다. 만료 날짜를 지난 복구 지점에서 복원을 수행하려는 경우 다시 시작 작업을 트리거하기 전에 수행하는 것이 좋습니다.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>자격 증명 모음에 일시 삭제된 항목이 있는 경우 자격 증명 모음을 삭제할 수 있나요?

자격 증명 모음에 일시 삭제된 상태의 백업 항목이 있으므로 Recovery Services 자격 증명 모음을 삭제할 수 없습니다. 일시 삭제된 항목은 삭제 작업이 있고 14일 후에 영구적으로 삭제됩니다. 14일 동안 기다릴 수 없는 경우 [일시 삭제를 사용하지 않도록 설정](#enabling-and-disabling-soft-delete)하고 일시 삭제된 항목의 삭제를 취소한 다음, 삭제된 항목을 다시 삭제하여 영구적으로 삭제합니다. 보호된 항목이 없고 일시 삭제된 항목이 없는 경우 자격 증명 모음을 삭제할 수 있습니다.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>삭제 후 14일의 일시 삭제 기간보다 이전 데이터를 삭제할 수 있나요?

아니요. 일시 삭제된 항목은 강제로 삭제할 수 없습니다. 14일 후 자동으로 삭제됩니다. 이 보안 기능은 백업된 데이터를 실수 또는 악의적인 삭제로부터 보호하기 위해 사용됩니다.  항목에 대해 다른 작업을 수행하기 전에 14일 동안 기다려야 합니다.  일시 삭제된 항목에는 요금이 부과되지 않습니다.  새 자격 증명 모음에서 14일 이내에 일시 삭제로 표시된 항목을 다시 보호해야 하는 경우 Microsoft 지원 서비스에 문의하세요.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell 또는 CLI에서 일시 삭제 작업을 수행할 수 있나요?

일시 삭제 작업은 PowerShell을 사용하여 수행할 수 있습니다. 현재 CLI는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 보안 기능 개요](security-overview.md)
