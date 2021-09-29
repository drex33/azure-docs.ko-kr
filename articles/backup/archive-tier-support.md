---
title: 보관 계층 지원
description: Azure Backup용 보관 계층 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5b16a308b7b30584bd8a44d2190bcdccbe191f2a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235519"
---
# <a name="archive-tier-support"></a>보관 계층 지원

고객은 조직의 준수 규칙에 따라 정의되는 보존 요구 사항이 있는 LTR(장기 보존) 백업 데이터 등의 백업 데이터를 저장하기 위해 Azure Backup을 사용합니다. 대부분의 경우 이전 백업 데이터는 거의 액세스되지 않고 준수 요구 사항에 한해 저장됩니다.

Azure Backup은 스냅숏과 표준 계층 외에도 보관 계층 내 장기 보존 지점의 백업을 지원합니다.

## <a name="scope"></a>범위

지원되는 워크로드:

- Azure 가상 머신
  - 월간/연간 복구 지점만 해당. 일간/주간 복구 지점은 지원되지 않습니다.
  - 사용 기간 >= 3개월(자격 증명 모음 표준 계층)
  - 남은 보존 기간 >= 6개월
  - 활성 상태의 일간/주간 종속성 없음
- Azure Virtual Machines의 SQL Server
  - 전체 복구 지점만 해당. 로그 및 차등은 지원되지 않습니다.
  - 사용 기간 >= 45일(자격 증명 모음 표준 계층)
  - 남은 보존 기간 >= 6개월
  - 종속성 없음

지원되는 클라이언트:

- 기능은 PowerShell을 사용하여 제공됩니다.

>[!Note]
>Azure VM의 SQL Server에 대한 보관 계층 지원이 이제 여러 지역에서 일반 공급됩니다. 지원되는 지역에 대한 자세한 목록은 [지원 매트릭스](#support-matrix)를 참조하세요.    <br><br>    Azure VM의 SQL Server에 대한 나머지 지역의 경우 보관 계층 지원은 제한된 공개 미리 보기로 제공됩니다. Azure Virtual Machines에 대한 보관 계층 지원도 제한된 공개 미리 보기로 제공됩니다. 제한된 공개 미리 보기에 등록하려면 이 [링크](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u)를 사용합니다.

## <a name="get-started-with-powershell"></a>PowerShell 시작

1. GitHub에서 [최신](https://github.com/PowerShell/PowerShell/releases) 버전의 PowerShell을 다운로드합니다.

1. PowerShell에서 다음 명령을 실행합니다.
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure에 연결합니다.
1. 구독에 로그인합니다.

   `Set-AzContext -Subscription "SubscriptionName"`

1. 자격 증명 모음을 가져옵니다.

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. 백업 항목의 목록을 가져옵니다.

    - Azure Virtual Machines:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - Azure Virtual Machines의 SQL Server:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. 백업 항목을 가져옵니다.

    - Azure Virtual Machines:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Azure Virtual Machines의 SQL Server:

        `$bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}`

1. 복구 지점을 표시할 날짜 범위를 추가합니다. 예를 들어 지난 124일에서 지난 95일까지의 복구 지점을 보려면 다음 명령을 사용합니다.

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-124)
    $endDate = (Get-Date).AddDays(-95) 

    ```
    >[!NOTE]
    >다른 시간 범위에 대한 복구 지점을 표시하려면 시작 날짜와 종료 날짜를 적절하게 수정합니다.
## <a name="use-powershell"></a>PowerShell 사용

### <a name="check-the-archivable-status-of-all-the-recovery-points"></a>모든 복구 지점의 보관할 수 상태를 확인 합니다.

이제 다음 cmdlet을 사용 하 여 백업 항목의 모든 복구 지점의 보관할 수 상태를 확인할 수 있습니다.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

### <a name="check-archivable-recovery-points"></a>보관 가능 복구 지점 확인

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

여기에는 보관으로 이동할 준비가 된 특정 백업 항목과 관련된 복구 지점이 모두 나열됩니다(시작 날짜부터 종료 날짜까지). 시작 날짜와 종료 날짜를 수정할 수도 있습니다.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>복구 지점을 보관으로 이동할 수 없는 이유 확인

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

여기서 `$rp[0]`은(는) 보관할 수 없는 이유를 확인하려는 복구 지점입니다.

샘플 출력:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>권장되는 보관 가능 지점의 집합 선택(Azure VM만 해당)

가상 머신과 관련된 복구 지점은 기본적으로 증분됩니다. 특정 복구 지점이 보관으로 이동되면 전체 백업으로 변환된 후 보관으로 이동됩니다. 따라서 보관으로 이동하는 일과 관련된 비용 절감 수준은 데이터 원본의 변동에 따라 달라집니다.

따라서 Azure Backup은 함께 이동하면 비용을 절감할 수 있는 권장 복구 지점의 집합을 제공합니다.

>[!NOTE]
>비용 절감은 다양한 원인에 따라 좌우되며, 두 인스턴스에 대해 동일하지 않을 수 있습니다.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>보관으로 이동

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

여기서 `$rp[0]`는 목록의 첫 번째 복구 지점입니다. 다른 복구 지점을 이동하려면 `$rp[1]`, `$rp[2]` 등을 사용합니다.

이 명령은 보관 가능 복구 지점을 보관으로 이동합니다. 포털 및 PowerShell에서 모두 이동 작업을 추적하는 데 사용될 수 있는 작업을 반환합니다.

### <a name="view-archived-recovery-points"></a>보관된 복구 지점 보기

이 명령은 보관된 복구 지점을 모두 반환합니다.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

### <a name="restore-with-powershell"></a>PowerShell로 복원

보관의 복구 지점에 대해 Azure Backup에서는 통합 복원 방법론을 제공합니다.

통합 복원은 2단계 프로세스로 이루어집니다. 첫 번째 단계는 보관에 저장된 복구 지점을 리하이드레이션하고 이를 10~30일의 기간(리하이드레이션 기간이라고도 함) 동안 자격 증명 모음 표준 계층에 임시로 저장하는 것입니다. 기본값은 15일입니다. 리하이드레이션에는 두 가지 우선 순위(표준/높은 우선 순위)가 있습니다. [리하이드레이션 우선 순위](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority)에 대해 자세히 알아보세요.

>[!NOTE]
>
>- 리하이드레이션 기간을 선택한 후에는 변경할 수 없으며, 리하이드레이션된 복구 지점은 리하이드레이션 기간 동안 표준 계층에 유지됩니다.
>- 추가 리하이드레이션 단계를 진행하면 비용이 발생합니다.

Azure Virtual Machines의 다양한 복원 방법에 대한 자세한 내용은 [PowerShell로 Azure VM 복원](backup-azure-vms-automation.md#restore-an-azure-vm)을 참조하세요.

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server를 복원하려면 [다음 단계](backup-azure-sql-automation.md#restore-sql-dbs)를 수행합니다. `Restore-AzRecoveryServicesBackupItem` 명령에는 **RehydrationDuration** 및 **RehydrationPriority** 의 두 가지 추가 매개 변수가 필요합니다.

### <a name="view-jobs-from-powershell"></a>PowerShell에서 작업 보기

이동 및 복원 작업을 보려면 다음 PowerShell cmdlet을 사용합니다.

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

### <a name="move-recovery-points-to-archive-tier-at-scale"></a>대규모 보관 계층으로 복구 지점 이동

이제 샘플 스크립트를 사용 하 여 크기 조정 작업을 수행할 수 있습니다. 샘플 스크립트를 실행 하는 방법에 [대해 자세히 알아보세요](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md) . [여기](https://github.com/hiaga/Az.RecoveryServices)에서 스크립트를 다운로드할 수 있습니다.

Azure Backup에서 제공 하는 샘플 스크립트를 사용 하 여 다음 작업을 수행할 수 있습니다.

- Azure VM의 SQL 서버에 대 한 특정 데이터베이스/모든 데이터베이스에 대 한 적합 한 모든 복구 지점은 보관 계층으로 이동 합니다.
- 특정 Azure Virtual Machine의 권장 복구 지점은 모두 보관 계층으로 이동 합니다.
 
요구 사항에 따라 스크립트를 작성 하거나 위의 샘플 스크립트를 수정 하 여 필요한 백업 항목을 가져올 수도 있습니다.

## <a name="use-the-portal"></a>포털 사용

### <a name="check-archived-recovery-point"></a>보관된 복구 지점 확인

이제 보관으로 이동된 복구 지점을 모두 볼 수 있습니다.

![모든 복구 지점.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>포털에서 복원

보관으로 이동된 복구 지점의 경우, 복원하려면 리하이드레이션 기간 및 리하이드레이션 우선 순위에 대한 매개 변수를 추가해야 합니다.

![포털에서 복원.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>포털에서 작업 보기

![포털에서 작업 보기.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>보호 수정

데이터 원본에 대한 보호를 수정할 수 있는 방법에는 다음의 두 가지가 있습니다.

- 기존 정책 수정
- 새 정책을 사용하여 데이터 원본 보호

두 경우 모두 새로운 정책이 표준 계층에 있는 모든 이전 복구 지점과, 보관 계층에 있는 모든 복구 시점에 적용됩니다. 따라서 정책이 변경된 경우 이전 복구 지점은 삭제될 수 있습니다.

복구 지점이 보관으로 이동되면 180일의 초기 삭제 기간이 적용됩니다. 요금은 비례 배분됩니다. 180일 동안 보관에 유지되지 않는 복구 지점을 삭제한 경우, 180에서 표준 계층에 소요된 기간(일)을 뺀 값에 해당하는 비용이 발생합니다.

최소 6개월간 보관에 유지되지 않은 복구 지점은 삭제 시 초기 삭제 비용이 발생합니다.

## <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호를 중지하고 데이터를 삭제하면 모든 복구 지점이 삭제됩니다. 보관 계층에서 180일간 유지되지 않은 보관의 복구 지점에서 복구 지점을 삭제하면 초기 삭제 비용이 발생합니다.

## <a name="support-matrix"></a>지원 매트릭스

| 워크로드 | 미리 보기 | 일반 공급 |
| --- | --- | --- |
| Azure VM의 SQL Server | None | 오스트레일리아 동부, 인도 중부, 북유럽, 동아시아, 동아시아, 오스트레일리아 남동부, 캐나다 중부, 브라질 남부, 캐나다 동부, 프랑스 중부, 프랑스 남부, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 인도 남부, 영국 서부, 영국 남부, 미국 중부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중서부, 미국 동부, 미국 중남부,  미국 중북부, 서유럽, US Gov 버지니아, US Gov 텍사스, US Gov 애리조나. |
| Azure Virtual Machines | 미국 동부, 미국 동부 2, 미국 중부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 중서부, 미국 중북부, 브라질 남부, 캐나다 동부, 캐나다 중부, 서유럽, 영국 남부, 영국 서부, 동아시아, 일본 동부, 인도 남부, 동아시아 오스트레일리아 동부, 인도 중부, 북유럽, 오스트레일리아 남동부, 프랑스 중부, 프랑스 남부, 일본 서부, 일본 서부  한국 중부, 한국 남부. | None |

## <a name="error-codes-and-troubleshooting-steps"></a>오류 코드 및 문제 해결 단계

복구 지점을 보관으로 이동할 수 없는 경우에 발생하는 오류 코드가 몇 가지 있습니다.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**오류 메시지** – 복구 지점 유형이 보관 이동에 적합하지 않습니다.

**설명** - 이 오류 코드는 선택한 복구 지점 유형을 보관으로 이동하기에 적합하지 않은 경우에 표시됩니다.

**권장 작업** – [여기](#scope)에서 복구 지점의 자격을 확인합니다.

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**오류 메시지** – 복원에 대한 활성 종속성이 있는 복구 지점은 보관 이동에 적합하지 않습니다.

**설명** – 선택한 복구 지점에 활성 종속성이 있으므로 보관으로 이동할 수 없습니다.

**권장 작업** – [여기](#scope)에서 복구 지점의 자격을 확인합니다.

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**오류 메시지** – 자격 증명 모음 표준 계층에 소요된 수명이 필요한 최솟값보다 작아서 복구 지점이 보관 이동에 적합하지 않습니다.

**설명** – 복구 지점은 Azure Virtual Machines의 경우 최소 3개월간, Azure Virtual Machines의 SQL Server의 경우 45일간 표준 계층에 있어야 합니다.

**권장 작업** – [여기](#scope)에서 복구 지점의 자격을 확인합니다.

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**오류 메시지** – 복구 지점의 남은 수명이 필요한 최소값보다 작습니다.

**설명** – 보관 이동 자격의 복구 지점에 필요한 최소 수명은 6개월입니다.

**권장 작업** – [여기](#scope)에서 복구 지점의 자격을 확인합니다.

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**오류 메시지** – 복구 지점이 보관 계층으로 이미 이동되었으므로 보관 이동에 적합하지 않습니다.

**설명** – 선택한 복구 지점이 이미 보관에 있습니다. 따라서 보관으로 이동하기에 적합하지 않습니다.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**오류 메시지** – 데이터 원본 유형이 권장 사항 API에 적합하지 않습니다.

**설명** – 권장 사항 API는 Azure Virtual Machines에만 적용됩니다. 선택한 데이터 원본 유형에는 적용되지 않습니다.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**오류 메시지** – 복구 지점이 이미 리하이드레이션되었습니다. 리하이드레이션은 이 RP에서 허용되지 않습니다.

**설명** – 선택한 복구 지점이 이미 리하이드레이션되었습니다.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**오류 메시지** – 복구 지점이 보관 이동에 적합하지 않습니다.

**설명** – 선택한 복구 지점은 보관 이동에 적합하지 않습니다.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**오류** **메시지** – 보관 복구 지점이 리하이드레이션되지 않았습니다. 보관 RP에서 리하이드레이션을 완료한 후 다시 복원해 보세요.

**설명** – 복구 지점이 리하이드레이션되지 않았습니다. 복구 지점을 리하이드레이션한 후 복원해 보세요.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**오류** **메시지** – 보관 복구 지점에 대한 리하이드레이션만 지원 - 보관 복구 지점에 대한 리하이드레이션만 지원됩니다.

**설명** – 선택한 복구 지점에 대한 리하이드레이션은 허용되지 않습니다.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**오류 메시지** – 보관 복구 지점에 대한 리하이드레이션이 이미 진행 중입니다.

**설명** – 선택한 복구 지점에 대한 리하이드레이션이 이미 진행 중입니다.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**오류 메시지** – 정책에 지정된 보존 기간이 충분하지 않아서 복구 지점을 보관 계층으로 이동할 수 없습니다.

**권장 작업** – 적절한 보존 설정으로 보호된 항목에 대한 정책을 업데이트하고 다시 시도하세요.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**오류 메시지** – 이 복구 지점을 이동할 수 있는지를 아직 확인하는 중입니다.

**설명** – 복구 지점의 이동 준비 상태를 아직 확인하지 않았습니다.

**권장 작업** – 일정 시간 동안 기다렸다가 다시 확인합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>보호를 중지하고 데이터를 보존하면 보관 복구 지점은 어떻게 되나요?

복구 지점이 영구적으로 보관 상태로 유지됩니다. 자세한 내용은 [복구 지점에 대한 보호 중지의 영향](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)을 참조하세요.

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>지역 간 복원이 보관 계층에서 지원되나요?

GRS 자격 증명 모음의 데이터를 표준 계층에서 보관 계층으로 이동하면 데이터가 GRS 보관으로 이동합니다. 지역 간 복원이 사용되는 경우에도 마찬가지입니다. 백업 데이터를 보관 계층으로 이동한 후에는 쌍으로 연결된 지역으로 데이터를 복원할 수 없습니다. 그러나 지역에서 장애가 발생하는 경우 보조 지역의 백업 데이터를 복원할 수 있게 됩니다. 

주 지역의 보관 계층에 있는 복구 지점에서 복원하는 동안 복구 지점은 표준 계층에 복사되고 주 지역과 보조 지역에서 모두 리하이드레이션 기간에 따라 보존됩니다. 이러한 리하이드레이션된 복구 지점에서 지역 간 복원을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 가격 책정](azure-backup-pricing.md)