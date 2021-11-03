---
title: 보관 계층 사용
description: Azure Backup 보관 계층 지원을 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/23/2021
ms.custom: devx-track-azurepowershell-azurecli
zone_pivot_groups: backup-client-powershelltier-clitier-portaltier
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9b35cd9269ef75f7c97d576c85004a4fe0be6a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052983"
---
# <a name="use-archive-tier-support"></a>보관 계층 지원 사용


::: zone pivot="client-powershelltier"

이 문서에서는 PowerShell을 사용하여 보관 계층 및 스냅샷 및 표준 계층의 장기 보존 지점을 백업하는 절차를 제공합니다.

## <a name="supported-workloads"></a>지원되는 워크로드

| 워크로드 | 작업 |
| --- | --- |
| Azure Virtual Machines(미리 보기)   <br><br>  Azure 가상 컴퓨터의 SQL Server   | <ul><li>보관 가능한 복구 지점 보기    </li><li>권장 복구 지점 보기(Virtual Machines 경우에만)  </li><li>보관 가능한 복구 지점 이동   </li><li>권장 복구 지점 이동(Azure Virtual Machines 경우에만)   </li><li>보관된 복구 지점 보기   </li><li>보관된 복구 지점에서 복원   </li></ul> |

## <a name="get-started"></a>시작

1. GitHub에서 [최신](https://github.com/PowerShell/PowerShell/releases) 버전의 PowerShell을 다운로드합니다.

1. PowerShell에서 다음 cmdlet을 실행합니다.
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure에 연결합니다.

1. 구독에 로그인합니다.

   ```azurepowershell
   Set-AzContext -Subscription "SubscriptionName"
   ```

1. 자격 증명 모음을 가져옵니다.

    ```azurepowershell
    $vault = Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"
    ```

1. 백업 항목의 목록을 가져옵니다.

   - **For Azure Virtual Machines**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"
       ```

   - **Azure Virtual Machines SQL Server**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"
       ```

1. 백업 항목을 가져옵니다.

   - **For Azure Virtual Machines**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}
     ```

   - **Azure Virtual Machines SQL Server**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}
     ```

1. (선택 사항) 복구 지점을 보려는 날짜 범위를 추가합니다. 예를 들어 지난 120일 동안의 복구 지점을 보려면 다음 cmdlet을 사용합니다.

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-120)
    $endDate = (Get-Date).AddDays(0) 
   ```
   >[!NOTE]
   >다른 시간 범위에 대한 복구 지점을 표시하려면 시작 날짜와 종료 날짜를 적절하게 수정합니다. <br><br> 기본적으로 지난 30일 동안 사용됩니다.

## <a name="check-the-archivable-status-of-all-recovery-points"></a>모든 복구 지점의 보관 가능 상태 확인

이제 다음 cmdlet을 사용하여 백업 항목의 모든 복구 지점의 보관 가능한 상태를 확인할 수 있습니다.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

## <a name="check-archivable-recovery-points"></a>보관 가능 복구 지점 확인

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

그러면 보관으로 이동할 준비가 된 특정 백업 항목과 연결된 모든 복구 지점이 나열됩니다(시작 날짜에서 종료 날짜까지). 시작 날짜와 종료 날짜를 수정할 수도 있습니다.

## <a name="check-why-a-recovery-point-cant-be-moved-to-archive"></a>복구 지점을 보관으로 이동할 수 없는 이유 확인

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

여기서 `$rp[0]`은(는) 보관할 수 없는 이유를 확인하려는 복구 지점입니다.

**샘플 출력**

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>권장되는 보관 가능 지점의 집합 선택(Azure VM만 해당)

가상 머신과 연결된 복구 지점은 증분됩니다. 특정 복구 지점을 보관으로 이동하면 전체 백업으로 변환된 다음 보관으로 이동됩니다. 따라서 보관으로 이동과 관련된 비용 절감은 데이터 원본의 변동에 따라 달라집니다.

따라서 Azure Backup 함께 이동하는 경우 비용을 절감할 수 있는 권장 복구 지점 집합을 제공합니다.

>[!NOTE]
>- 비용 절감은 다양한 이유에 따라 달라지며 모든 인스턴스에 대해 동일하지 않을 수 있습니다.
>- 비용 절감은 권장 사항 집합에 포함된 모든 복구 지점을 자격 증명 모음 보관 계층으로 이동하는 경우에만 보장됩니다.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

## <a name="move-to-archive"></a>보관으로 이동

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

여기서 `$rp[0]`는 목록의 첫 번째 복구 지점입니다. 다른 복구 지점을 이동하려면 `$rp[1]`, `$rp[2]` 등을 사용합니다.

이 cmdlet은 보관 가능한 복구 지점을 보관으로 이동합니다. 포털에서 PowerShell을 사용하여 이동 작업을 추적하는 데 사용할 수 있는 작업을 반환합니다.

## <a name="view-archived-recovery-points"></a>보관된 복구 지점 보기

이 cmdlet은 보관된 모든 복구 지점을 반환합니다.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-with-powershell"></a>PowerShell로 복원

보관의 복구 지점에 대해 Azure Backup에서는 통합 복원 방법론을 제공합니다.
통합 복원은 2단계 프로세스로 이루어집니다.

1. 보관에 저장된 복구 지점을 리하우딩하는 작업이 포함됩니다.
2. 10~30일 범위의 기간(리하일레이션 기간이라고도 함)에 대한 자격 증명 모음 표준 계층에 임시로 저장합니다. 기본값은 15일입니다. 리하이드레이션에는 두 가지 우선 순위(표준/높은 우선 순위)가 있습니다. [리하이드레이션 우선 순위](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority)에 대해 자세히 알아보세요.

>[!NOTE]
>
>- 선택한 후 리하일레이션 기간은 변경할 수 없으며 리하위드 복구 지점은 리하일레이션 기간 동안 표준 계층에 유지됩니다.
>- 추가 리하이드레이션 단계를 진행하면 비용이 발생합니다.

Azure Virtual Machines의 다양한 복원 방법에 대한 자세한 내용은 [PowerShell로 Azure VM 복원](backup-azure-vms-automation.md#restore-an-azure-vm)을 참조하세요.

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server를 복원하려면 [다음 단계](backup-azure-sql-automation.md#restore-sql-dbs)를 수행합니다. `Restore-AzRecoveryServicesBackupItem`cmdlet에는 및 의 두 가지 추가 매개 변수가 `RehydrationDuration` `RehydrationPriority` 필요합니다.

## <a name="view-jobs"></a>작업 보기

이동 및 복원 작업을 보려면 다음 PowerShell cmdlet을 사용합니다.

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="move-recovery-points-to-archive-tier-at-scale"></a>대규모로 복구 지점을 보관 계층으로 이동

이제 샘플 스크립트를 사용하여 대규모 작업을 수행할 수 있습니다. 샘플 스크립트를 실행하는 방법에 대해 [자세히 알아봅니다.](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md) 여기에서 스크립트를 다운로드할 수 [있습니다.](https://github.com/hiaga/Az.RecoveryServices)

Azure Backup 제공하는 샘플 스크립트를 사용하여 다음 작업을 수행할 수 있습니다.

- Azure VM의 SQL 서버에 대한 특정 데이터베이스/모든 데이터베이스에 적합한 모든 복구 지점을 보관 계층으로 이동합니다.
- 특정 Azure Virtual Machine에 대한 모든 권장 복구 지점을 보관 계층으로 이동합니다.
 
요구 사항에 따라 스크립트를 작성하거나 위의 샘플 스크립트를 수정하여 필요한 백업 항목을 가져올 수도 있습니다.

::: zone-end



::: zone pivot="client-clitier"

이 문서에서는 CLI(명령줄 인터페이스)를 사용하여 보관 계층 및 스냅샷 및 표준 계층의 장기 보존 지점을 백업하는 절차를 제공합니다.

## <a name="supported-workloads"></a>지원되는 워크로드

| 워크로드 | 작업 |
| --- | --- |
| Azure Virtual Machines(미리 보기)   <br><br>  Azure 가상 컴퓨터의 SQL Server   <br><br>   SAP HANA in Azure Virtual Machines | <ul><li> 보관 가능한 복구 지점 보기       </li><li>권장 복구 지점 보기(Virtual Machines 경우에만)    </li><li>보관 가능한 복구 지점 이동    </li><li>권장 복구 지점 이동(Azure Virtual Machines 경우에만)    </li><li>보관된 복구 지점 보기    </li><li>보관된 복구 지점에서 복원 </li></ul> |


## <a name="get-started"></a>시작

1. AZ CLI 버전을 2.26.0 이상으로 다운로드/업그레이드 

   1. [지침에](/cli/azure/install-azure-cli) 따라 CLI를 처음으로 설치합니다.
   1. az --upgrade를 실행하여 이미 설치된 버전을 업그레이드합니다.

2. 다음 명령을 사용하여 로그인합니다.

   ```azurecli
   az login
   ```

3. 구독 컨텍스트 설정:

   ```azurecli
   az account set –s <subscriptionId>
   ```
## <a name="view-archivable-recovery-points"></a>보관 가능한 복구 지점 보기

다음 명령을 사용하여 보관 가능한 복구 지점을 자격 증명 모음 보관 계층으로 이동할 수 있습니다. 자격 기준에 대해 [자세히 알아보세요.](archive-tier-support.md#supported-workloads)

- **For Azure Virtual Machines**

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureIaasVM} --workload-type {VM}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Azure Virtual Machines SQL Server

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Azure Virtual Machines의 SAP HANA

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

## <a name="check-why-a-recovery-point-isnt-archivable"></a>복구 지점이 보관할 수 않는 이유를 확인 합니다.

다음 명령 실행:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload / AzureIaasVM} --workload-type {MSSQL / SAPHANA / VM}  --query [].{Name:name,move_ready:properties.recoveryPointMoveReadinessInfo.ArchivedRP.isReadyForMove,additional_details: properties.recoveryPointMoveReadinessInfo.ArchivedRP.additionalInfo
```

보관할 수 여부 및 보관할 수 되지 않은 경우의 이유에 대 한 모든 복구 지점의 목록을 가져옵니다.

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>권장되는 보관 가능 지점의 집합 선택(Azure VM만 해당)

다음 명령 실행:

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --recommended-for-archive
```

권장 사항 집합에 [대해 자세히 알아보세요](archive-tier-support.md#archive-recommendations-only-for-azure-virtual-machines) .

>[!Note]
>- 비용 절감은 다양 한 원인에 따라 다르며, 모든 인스턴스에 대해 동일 하지 않을 수 있습니다.
>- 권장 구성에 포함 된 모든 복구 지점이 자격 증명 모음-보관 계층으로 이동 하는 경우에만 비용을 절감할 수 있습니다.

## <a name="move-to-archive"></a>보관으로 이동

다음 명령을 사용 하 여 보관할 수 복구 지점은 자격 증명 모음-보관 계층으로 이동할 수 있습니다. 명령의 name 매개 변수에는 보관할 수 복구 지점의 이름이 포함 되어야 합니다.

- **Azure 가상 컴퓨터**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Azure 가상 컴퓨터의 SQL Server**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Azure 가상 컴퓨터의 SAP HANA**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```

## <a name="view-archived-recovery-points"></a>보관된 복구 지점 보기

다음 명령을 사용합니다.

- **Azure Virtual Machines**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload } --workload-type {VM} -- tier {VaultArchive}
    ```
- **Azure Virtual Machines의 SQL Server**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} -- tier {VaultArchive}
    ```
- **Azure Virtual Machines의 SAP HANA**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} -- tier {VaultArchive}
    ```

## <a name="restore"></a>복원 

다음 명령을 실행합니다.

- **Azure Virtual Machines**

    ```azurecli
    az backup restore restore-disks -g {rg} -v {vault} -c {container} -i {item} --rp-name {rp} --storage-account {storage_account} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

- **azure vm의 SQL Server 및 azure vm의 SAP HANA**

    ```azurecli
    az backup recoveryconfig show --resource-group saphanaResourceGroup \
        --vault-name saphanaVault \
        --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
        --item-name saphanadatabase;hxe;hxe \
        --restore-mode AlternateWorkloadRestore \
        --rp-name 7660777527047692711 \
        --target-item-name restored_database \
        --target-server-name hxehost \
        --target-server-type HANAInstance \
        --workload-type SAPHANA \
        --output json


    az backup restore restore-azurewl -g {rg} -v {vault} --recovery-config {recov_config} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

::: zone-end



::: zone pivot="client-portaltier"

이 문서에서는 Azure Portal를 사용 하 여 보관 계층의 장기 보존 지점과 스냅숏 및 표준 계층을 백업 하는 절차를 제공 합니다.

## <a name="supported-workloads"></a>지원되는 워크로드

| 워크로드 | 작업 |
| --- | --- |
| Azure Virtual Machine | <ul><li>보관 된 복구 지점의 보기    </li><li>보관 된 복구 지점의 복원   </li><li>보관 이동 및 복원 작업 보기 </li></ul> |
| Azure 가상 컴퓨터의 SQL Server/ <br> Azure Virtual Machines에서 SAP HANA | <ul><li>보관 된 복구 지점의 보기    </li><li>모든 보관할 수 recovery를 archive로 이동   </li><li>보관 된 복구 지점의 복원   </li><li>보관 이동 및 복원 작업 보기</li></ul> |

## <a name="view-archived-recovery-points"></a>보관된 복구 지점 보기

이제 보관으로 이동 된 모든 복구 지점은 볼 수 있습니다.

:::image type="content" source="./media/use-archive-tier-support/view-recovery-points-list-inline.png" alt-text="복구 지점의 목록을 보여 주는 스크린샷" lightbox="./media/use-archive-tier-support/view-recovery-points-list-expanded.png":::


## <a name="move-archivable-recovery-points-for-a-particular-sqlsap-hana-database"></a>특정 SQL/sap HANA 데이터베이스에 대 한 보관할 수 복구 지점은 이동 합니다.

이제 한 번에 특정 SQL/sap HANA 데이터베이스에 대 한 모든 복구 지점을 이동할 수 있습니다.

다음 단계를 수행합니다.

1. 자격 증명 모음-보관 계층으로 이동 하려는 복구 지점이 있는 백업 항목 (SQL Server의 데이터베이스 또는 Azure VM의 SAP HANA)을 선택 합니다.

2. 7 일 보다 오래 된 복구 위치를 보려면 **여기를 클릭** 하세요 .를 선택 합니다.

   :::image type="content" source="./media/use-archive-tier-support/view-old-recovery-points-inline.png" alt-text="7 일 보다 오래 된 복구 지점의 수를 확인 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/use-archive-tier-support/view-old-recovery-points-expanded.png":::

3. 보관으로 이동할 적격 보관할 수 점수를 모두 보려면 _장기 보존 점수를 보관으로 이동할 수 있습니다 .를 선택 합니다. 모든 ' 적격 복구 시점 '을 보관 계층으로 이동 하려면 여기를 클릭_ 하세요.

   :::image type="content" source="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-inline.png" alt-text="보관으로 이동할 적격 보관할 수 points를 모두 표시 하는 프로세스를 보여 주는 스크린샷" lightbox="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-expanded.png":::

   모든 보관할 수 복구 지점이 표시 됩니다.


   자격 조건에 대 한 [자세한](archive-tier-support.md#supported-workloads) 정보.

3. **복구 지점이 보관 위치로 이동** 을 클릭 하 여 모든 복구 지점이 자격 증명 모음-보관 계층으로 이동 합니다.

   :::image type="content" source="./media/use-archive-tier-support/move-all-recovery-points-to-vault-inline.png" alt-text="자격 증명 모음-보관 계층에 대 한 모든 복구 지점의 이동 프로세스를 시작 하는 옵션을 보여 주는 스크린샷" lightbox="./media/use-archive-tier-support/move-all-recovery-points-to-vault-expanded.png":::

   >[!Note]
   >이 옵션을 선택 하면 모든 보관할 수 복구 지점이 자격 증명 모음-보관으로 이동 됩니다.

백업 작업의 진행 상태를 모니터링할 수 있습니다.

## <a name="restore"></a>복원

보관으로 이동 된 복구 지점은 복원 하려면 리하이드레이션 duration 및 리하이드레이션 priority에 필요한 매개 변수를 추가 해야 합니다.

:::image type="content" source="./media/use-archive-tier-support/restore-in-portal.png" alt-text="포털에서 복구 지점이 복원 되는 프로세스를 보여 주는 스크린샷":::

## <a name="view-jobs"></a>작업 보기

:::image type="content" source="./media/use-archive-tier-support/view-jobs-portal.png" alt-text="포털에서 작업을 확인 하는 프로세스를 보여 주는 스크린샷":::

## <a name="view-archive-usage-in-vault-dashboard"></a>자격 증명 모음 대시보드에서 보관 사용 현황 보기

자격 증명 모음 대시보드에서 보관 사용 현황을 볼 수도 있습니다.

:::image type="content" source="./media/use-archive-tier-support/view-archive-usage-in-vault-dashboard.png" alt-text="자격 증명 모음 대시보드의 보관 사용 현황을 보여 주는 스크린샷":::


::: zone-end

## <a name="next-steps"></a>다음 단계

- [보관 계층 오류 문제 해결](troubleshoot-archive-tier.md)
