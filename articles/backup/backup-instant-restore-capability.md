---
title: Azure 인스턴트 복원 기능
description: VM 백업 스택, Resource Manager 배포 모델에 대한 Azure 인스턴트 복원 기능 및 FAQ
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a70e2780cb2ceeb37379557ad9cb4bb931e9b6f1
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252223"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup 인스턴트 복원 기능을 사용하여 향상된 백업 및 복원 성능 얻기

> [!NOTE]
> 사용자의 피드백에 따라 Azure Stack 기능과의 혼동을 줄이기 위해 **VM 백업 스택 V2** 의 이름을 **즉시 복원** 으로 바꾸었습니다.
> 모든 Azure Backup 사용자는 이제 **즉시 복원** 으로 업그레이드되었습니다.

인스턴트 복원의 새 모델은 다음과 같은 기능 개선 사항을 제공합니다.

* 자격 증명 모음으로의 데이터 전송이 완료될 때까지 기다리지 않고 백업 작업의 일부로 만든 스냅샷을 복구에 사용할 수 있습니다. 이에 따라, 복원을 트리거하기 전에 스냅샷이 저장소에 복사되는 대기 시간이 줄었습니다.
* 기본적으로 스냅샷을 로컬에 2일 동안 보존하여 백업 및 복원 시간을 단축합니다. 이 기본 스냅샷 보존 값은 1일에서 5일 사이의 값으로 구성할 수 있습니다.
* 최대 32TB까지 디스크 크기를 지원합니다. Azure Backup에서는 디스크 크기를 조정하지 않는 것이 좋습니다.
* 표준 HDD 디스크 및 프리미엄 SSD 디스크와 함께 표준 SSD 디스크를 지원합니다.
* 복원할 때 비관리형 VM의 원래 스토리지 계정(디스크당)을 사용할 수 있습니다. 이 기능은 VM의 디스크가 스토리지 계정에 분산된 경우에도 지원됩니다. 이는 다양한 VM 구성에 대한 복원 작업을 가속화합니다.
* 즉시 복원으로 스토리지의 비관리 프리미엄 디스크를 사용하는 VM 백업의 경우 할당된 총 스토리지 공간의 *50%* 를 사용 가능한 공간으로 할당하는 것이 좋으며, 이는 **오직** 첫 번째 백업에서만 요구되는 사항입니다. 첫 번째 백업이 완료된 후에는 백업에 50%의 사용 가능한 공간이 필요하지 않습니다.

## <a name="whats-new-in-this-feature"></a>이 기능의 새로운 사항

현재 백업 작업은 다음과 같은 두 단계로 구성됩니다.

1. VM 스냅샷 만들기.
2. VM 스냅샷을 Azure Recovery Services 자격 증명 모음으로 전송.

복구 지점은 1단계 및 2단계가 완료된 후에만 만들어지는 것으로 간주됩니다. 이 업그레이드의 일부로, 스냅샷이 완료되는 즉시 복구 지점이 생성되며 스냅샷 유형의 이 복구 지점을 사용하여 동일한 복원 흐름으로 복원을 수행할 수 있습니다. “스냅샷”을 복구 지점 유형으로 사용하여 Azure Portal에서 이 복구 지점을 식별할 수 있으며, 스냅샷이 자격 증명 모음으로 전송된 후 복구 지점 유형이 “스냅샷 및 자격 증명 모음”으로 바뀝니다.

![VM 백업 스택 Resource Manager 배포 모델의 백업 작업 - 스토리지 및 자격 증명 모음](./media/backup-azure-vms/instant-rp-flow.png)

기본적으로 스냅샷은 이틀 동안 보존됩니다. 이 기능을 사용하면 복원 시간을 줄여 스냅샷에서 복원 작업을 수행할 수 있습니다. 자격 증명 모음에서 데이터를 다시 변환하고 복사하는 데 필요한 시간을 줄일 수 있습니다.

## <a name="feature-considerations"></a>기능 고려 사항

* 스냅샷이 디스크와 함께 저장되어 복구 지점 생성을 강화하고 복원 작업을 가속화합니다. 따라서 이 기간에 만든 스냅샷에 해당하는 스토리지 비용이 발생합니다.
* 증분 스냅샷은 페이지 Blob으로 저장됩니다. 비관리 디스크를 사용하는 모든 사용자의 경우 로컬 스토리지 계정에 저장된 스냅샷에 대한 요금이 청구됩니다. 관리형 VM 백업에 사용되는 복원 지점 컬렉션은 기본 스토리지 수준에서 Blob 스냅샷을 사용하므로, 관리 디스크의 경우 Blob 스냅샷 가격에 해당하는 비용이 표시되며 증분됩니다.
* Premium Storage 계정의 경우, 빠른 복구 지점에 대해 만든 스냅샷이 10TB의 할당된 공간 한도에 포함됩니다.
* 복원 요구 사항을 기준으로 스냅샷 보존을 구성할 수 있습니다. 요구 사항에 따라 아래 설명된 대로 백업 정책 창에서 스냅샷 보존을 최소 하루로 설정할 수 있습니다. 이렇게 하면 복원을 자주 수행하지 않는 경우 스냅샷 보존 비용 절감에 도움이 됩니다.
* 단일 방향 업그레이드입니다. 즉시 복원으로 업그레이드한 후에는 되돌릴 수 없습니다.

>[!NOTE]
>이 즉시 복원 업그레이드를 사용하면 모든 고객(**새 고객과 기존 고객 모두 포함**)의 스냅샷 보존 기간이 기본값인 2일로 설정됩니다. 그러나 요구 사항에 따라 기간을 1~5일 사이의 임의 값으로 설정할 수 있습니다.

## <a name="cost-impact"></a>비용 영향

즉시 복구에 사용되는 증분 스냅샷은 VM의 스토리지 계정에 저장됩니다. 증분 스냅샷은 스냅샷이 사용하는 공간이 스냅샷이 생성된 후 작성된 페이지에서 사용하는 공간과 같음을 의미합니다. 청구는 계속해서 스냅샷이 사용하는 공간 GB당 부과되며, GB당 가격은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)에 언급된 내용과 동일합니다. 비관리 디스크를 사용하는 VM의 경우 각 디스크의 VHD 파일 메뉴에서 스냅샷을 볼 수 있습니다. 관리 디스크의 경우 스냅샷은 지정된 리소스 그룹의 복원 지점 수집 리소스에 저장되며 스냅샷 자체는 직접 표시되지 않습니다.

>[!NOTE]
> 주간 정책의 경우 스냅샷 보존은 5일로 고정됩니다.

## <a name="configure-snapshot-retention"></a>스냅샷 보존 구성

### <a name="using-azure-portal"></a>Azure Portal 사용

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Azure Portal의 **VM 백업 정책** 창에서, **즉시 복원** 섹션 아래에 필드가 추가된 것을 확인할 수 있습니다. 특정 백업 정책과 연결된 모든 VM에 대해 **VM 백업 정책** 창에서 스냅샷 보존 기간을 변경할 수 있습니다.

![즉시 복원 기능](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell 사용

>[!NOTE]
> Az PowerShell 버전 1.6.0 이상에서는 PowerShell을 사용하여 정책의 즉시 복원 스냅샷 보존 기간을 업데이트 가능

```powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

각 정책의 기본 스냅샷 보존은 2일로 설정됩니다. 값을 최소 1일에서 최대 5일로 변경할 수 있습니다. 주간 정책의 경우 스냅샷 보존이 5일로 고정됩니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="what-are-the-cost-implications-of-instant-restore"></a>인스턴트 복원은 비용에 어떤 영향을 주나요?

스냅샷이 디스크와 함께 저장되어 복구 지점 생성 및 복원 작업을 가속화합니다. 따라서 VM 백업 정책의 일부로 선택된 스냅샷 보존에 해당하는 스토리지 비용이 표시됩니다.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage 계정에서 인스턴트 복구 지점에 대해 만든 스냅샷이 10TB 스냅샷 한도에 포함되나요?

예, Premium Storage 계정의 경우, 즉시 복구 지점에 대해 만든 스냅샷이 10TB의 할당된 스냅샷 공간에 포함됩니다.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>스냅샷 보존은 5일 동안 어떻게 작동하나요?

매일 새 스냅샷이 생성되며, 5개의 개별 증분 스냅샷이 있습니다. 스냅샷 크기는 데이터 변동에 따라 달라지며, 대부분의 경우 약 2~7%입니다.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>인스턴트 복원 스냅샷은 증분 스냅샷인가요, 아니면 전체 스냅샷인가요?

인스턴트 복원 기능의 일부로 생성되는 스냅샷은 증분 스냅샷입니다.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>인스턴트 복원 기능으로 인한 대략적인 비용 증가는 어떻게 계산할 수 있나요?

VM 변동에 따라 다릅니다. 안정된 상태에서는 비용 증가를 스냅샷 보존 기간 * VM당 일일 변동 * GB당 스토리지 비용으로 가정할 수 있습니다.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>복원 지점의 복구 유형이 “스냅샷 및 자격 증명 모음”이고 복원 작업을 수행하는 경우 어떤 복구 유형이 사용되나요?

복구 유형이 “스냅샷 및 자격 증명 모음”인 경우 로컬 스냅샷에서 자동으로 복원이 수행되며, 자격 증명 모음에서 수행되는 복원에 비해 훨씬 더 빠릅니다.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>스냅샷(계층 1) 보존 기간보다 짧은 복원 지점(계층 2)의 보존 기간을 선택하면 어떻게 되나요?

새 모델에서는 스냅샷(계층 1)을 삭제하지 않은 경우 복원 지점(계층 2) 삭제를 허용하지 않습니다. 복원 지점(계층 2) 보존 기간을 스냅샷 보존 기간 이상으로 예약하는 것이 좋습니다.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>백업 정책에 설정된 보존 기간 이후에도 스냅샷이 여전히 존재하는 무엇인가요?

복구 지점은 스냅샷을 포함하여 사용 가능한 최신 복구 지점인 경우 다음번 백업이 완료될 때까지 유지됩니다. 이는 지정된 “가비지 수집”(GC) 정책에 따른 것입니다. VM의 문제로 모든 후속 백업이 실패할 경우에 대비해, 항상 최소한 하나 이상의 최신 복구 지점이 있어야 합니다. 일반적인 시나리오에서 복구 지점은 만료된 후 최대 24시간 이내에 정리됩니다. 드문 경우이긴 하지만 GC(가비지 수집기)의 로드가 높을 때, 하나 또는 두 개의 추가 스냅샷이 존재할 수 있습니다.

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>보존 정책보다 더 많은 스냅샷이 표시되는 이유는 무엇인가요?

보존 정책이 “1”로 설정된 시나리오에서 두 개의 스냅샷이 발견될 수 있습니다. VM의 문제로 모든 후속 백업이 실패할 경우에 대비해, 항상 최소한 하나 이상의 최신 복구 지점이 있어야 합니다. 이로 인해 두 개의 스냅샷이 발생할 수 있습니다.<br></br>따라서 “n” 스냅샷 정책의 경우, 가끔 “n+1” 스냅샷이 발견될 수 있습니다. 또한 가비지 수집이 지연되는 경우, “n+1+2” 스냅샷이 발견되기도 합니다. 이는 다음과 같은 경우에 드물게 발생할 수 있습니다.
- 보존 기간이 지난 스냅샷을 정리합니다.
- 백엔드에 있는 가비지 수집기(GC)의 로드가 높습니다.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>즉시 복원 기능이 필요하지 않습니다. 사용하지 않도록 설정할 수 있나요?

즉시 복원 기능은 모든 사용자가 사용하도록 설정되어 있으며 사용하지 않도록 설정할 수 없습니다. 스냅샷 보존 기간을 최소 하루로 줄일 수는 있습니다.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>(몇 시간이 걸릴 수 있는)전송 프로세스 중에 VM을 다시 시작해도 안전하나요? VM을 다시 시작하면 전송이 중단되거나 속도가 느려지나요?

네, 안전하며 데이터 전송 속도에는 전혀 영향을 주지 않습니다.

