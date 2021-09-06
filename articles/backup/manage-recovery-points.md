---
title: 복구 지점 관리
description: Azure Backup 서비스가 가상 머신의 복구 지점을 관리하는 방법을 알아봅니다
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 2dd33b430d5aa117f35f74ff92bb3655a9002803
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300672"
---
# <a name="manage-recovery-points"></a>복구 지점 관리

이 문서에서는 가상 머신에 대한 보존이 작동하는 방식을 설명합니다. 백업이 발생할 때마다 복원 작업을 수행할 수 있는 복구 지점이 생성됩니다.

가상 머신의 경우 초기 백업은 전체 백업이며 후속 백업은 증분 백업입니다.

## <a name="recovery-points-and-retention"></a>복구 지점 및 보존

### <a name="scheduled-initial-and-incremental-backup"></a>예약된 초기 및 증분 백업

블록 1, 블록 2, 블록 3, 블록 4의 4개 블록으로 구성된 데이터 디스크가 있는 가상 머신 *V1* 의 간소화된 예를 살펴봅니다. 각 블록의 크기는 16KB입니다.

![4개의 블록이 있는 가상 머신](./media/manage-recovery-points/four-blocks.png)

**1단계 -초기 백업:** 초기 백업은 전체 백업입니다. 후속 증분 백업이 적용되는 기준선 역할을 합니다. 원본 VM의 블록 1 및 블록 2에 기록된 데이터가 있다고 가정합니다. 동일한 데이터가 Recovery Services 자격 증명 모음 스토리지에서 D1 및 D2로 복제됩니다.

![초기 백업이 복제됩니다](./media/manage-recovery-points/initial-backup.png)

**2단계 -증분 백업 1:** VM의 블록 3에 새 데이터가 추가된 것을 고려합니다. 동일한 데이터가 다음 증분 백업에서 복제되고, 변경된 블록만 D3으로 저장됩니다.  각 단계에서 블록의 1KB가 변경되더라도 전체 16KB 블록이 복구 지점에 업로드됩니다.

![첫 번째 증분 백업](./media/manage-recovery-points/first-incremental-backup.png)

**3단계 -증분 백업 2:** 이제 원본 VM의 블록 3 및 블록 2에 데이터 변경이 있는 것을 고려합니다. 이러한 변경은 다음 증분 백업에서 D3' 및 D2'로 복제됩니다.

![두 번째 증분 백업](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>주문형 백업

보호를 설정한 후 언제든지 VM의 주문형 백업을 실행하도록 선택할 수 있습니다.

- 주문형 백업은 첫 번째 예약된 초기 백업 이전에 트리거된 경우 전체 백업이 됩니다.
- 초기 백업이 완료된 후 주문형 백업이 트리거되면 증분 백업입니다.
- 주문형 백업에 대해 생성된 복구 지점의 보존 시간은 백업을 트리거할 때 지정하는 보존 기간입니다.

### <a name="storage-cost"></a>스토리지 비용

초기 백업에 대해 생성된 **복구 지점** 에는 데이터를 포함하는 모든 블록이 포함됩니다. 후속 증분 복구 지점은 데이터를 변경하는 블록으로만 구성됩니다. 스토리지 비용은 모든 복구 지점이 걸쳐 있는 모든 블록의 합계에 해당합니다.

위의 예시를 사용하여 각 단계 후의 스토리지 비용을 이해해 봅니다.

|단계  |백업 유형  |변경된 블록  |스토리지 유형 |
|------|---------|---------|---------|
|1     |     초기 백업    | 블록 1, 블록 2        |    복구 지점 1(D1 + D2)에 해당     |
|2     |  증분 백업 1       |  블록 3       |   복구 지점 1(D1 + D2) + 복구 지점 2(D3)에 해당      |
|3     |    증분 백업 2     |    블록 2, 블록 3     |   복구 지점 1(D1 + D2) + 복구 지점 2(D3) + 복구 지점 3(D2 ' + D3 ')에 해당합니다      |

### <a name="recovery-point-expiration"></a>복구 지점 만료

각 복구 지점에는 백업 정책에 지정된 보존 기간이 있습니다. 정리는 일정한 간격으로 수행되며 만료된 모든 복구 지점은 정리됩니다.

복구 지점이 만료되면 삭제되거나 병합됩니다.

### <a name="case-1-initial-recovery-point-expires"></a>사례 1: 초기 복구 지점 만료

초기 복구 지점이 만료되면 다음 증분 복구 지점과 병합됩니다. 증분 복구 지점에서 덮어쓴 모든 데이터 블록이 삭제되고 나머지는 병합됩니다. 그러면 증분 백업이 초기 전체 백업이 됩니다. 예시와 함께 검토해 보겠습니다.

- 초기 백업 중에 생성된 *복구 지점 1* 에는 VM의 전체 백업이 있습니다.
- *복구 지점 1* 이 만료되면 *복구 지점 2* 가 다음 전체 백업입니다.
- 블록 D1은 *복구 지점 2* 와 병합되고 D2는 *복구 지점 2* 에서 블록 2의 데이터를 덮어쓰므로 삭제됩니다. 이 변경 내용은 블록 D2'로 캡처됩니다.
- 블록 D1은 다음 백업 전에 변경 내용이 있을 때까지 연속 복구 지점에 그대로 유지됩니다.

![첫 번째 사례](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>사례 2: 중간 증분 복구 지점 만료

- *복구 지점 2* 가 *복구 지점 1* 이전에 만료되면, *복구 지점 2* 의 데이터가 사용 가능한 다음 복구 지점인 *복구 지점 3* 과 병합됩니다. 따라서 블록 D3은 *복구 지점 3* 과 병합됩니다.
- *복구 지점 1* 은 여전히 블록 D1 및 D2가 있는 전체 백업입니다.

![두 번째 사례](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>사례 3: 주문형 복구 지점 만료

이 예시에서는 일정(일별 백업) 정책이 *n일* 보존 기간으로 실행하도록 예약되었습니다.  다음 예약된 백업 4일 전에 주문형 백업이 트리거되고 보존 기간이 10일로 지정된 경우에도 증분 백업이 됩니다. 복구 지점(*주문형 RP1*)은 *복구 지점 3* 이후와 *복구 지점 4* 이전에 생성됩니다.  14일이 끝나면, 주문형 복구 지점(*주문형 RP1*)이 만료되고 사용 가능한 다음 복구 지점과 병합됩니다. 서버에 여전히 있는 데이터 블록은 병합되지만 변경된 데이터 블록(덮어쓰기 또는 삭제)은 만료된 복구 지점에서 삭제됩니다.

![세 번째 사례](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>복구 지점에 대한 정책 변경의 영향

정책이 수정되면 새 복구 지점과 기존 복구 지점 모두에 적용됩니다. 자세한 내용은 [복구 지점의 정책 변경의 영향](backup-architecture.md#impact-of-policy-change-on-recovery-points)을 참조하세요.

### <a name="impact-of-stop-protection-on-recovery-points"></a>복구 지점의 보호 중지의 영향

VM 보호를 중지하는 방법은 다음 두 가지가 있습니다.

- **보호를 중지하고 백업 데이터 삭제.** 이 옵션을 선택하면 이후의 모든 백업 작업이 VM을 보호하지 않으며, 모든 복구 지점이 삭제됩니다. [일시 삭제](backup-azure-security-feature-cloud.md)를 사용하는 경우 삭제된 데이터는 14일 동안 보존됩니다. 일시 삭제된 상태의 항목에 대한 요금은 발생하지 않습니다. 데이터는 14일 기간 내에 삭제 취소할 수 있습니다. 일시 삭제를 사용하도록 설정하지 않으면 데이터가 즉시 정리되며 VM을 복원하거나 **백업 다시 시작** 옵션을 사용할 수 없습니다.
- **보호를 중지하고 백업 데이터 보존.** 이 옵션은 VM을 보호하는 이후의 모든 백업 작업을 중지합니다. 그러나 Azure Backup 서비스는 백업된 복구 지점을 영구적으로 유지합니다. 자격 증명 모음에서 복구 지점이 유지되도록 요금을 지불해야 합니다(자세한 내용은 [Azure Backup 가격](https://azure.microsoft.com/pricing/details/backup/) 참조). 필요한 경우 VM을 복원할 수 있습니다. VM 보호를 다시 시작하기로 결정 한 경우, **백업 다시 시작** 옵션을 사용할 수 있습니다. 백업을 다시 시작한 후에는 보존 규칙이 만료 지점에 적용됩니다. **백업 데이터 삭제** 옵션을 사용하여 백업된 데이터를 삭제할 수도 있습니다.

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>보호를 중지하지 않고 VM을 삭제할 경우의 영향

보호를 중지하지 않고 VM을 삭제하면 복구 지점이 영향을 받게 되며, 이는 바람직하지 않은 시나리오입니다. 가상 머신을 삭제하기 전에 이상적으로 백업을 중지해야 합니다. 리소스가 존재하지 않으므로 예약된 백업은 [VMNotFoundV2 오류](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)로 인해 실패합니다. 복구 지점은 보존 정책에 따라 주기적으로 정리되지만, 가상 머신의 마지막 복사본은 영구적으로 유지되고 그에 따라 요금이 청구됩니다. 시나리오에 따라 다음 두 방법 중 하나를 사용할 수 있습니다.

- **옵션 1:** 복구 위치 중 하나를 사용하여 VM을 복원합니다. 삭제된 VM을 복구하려면 동일한 이름을 사용하고 및 동일한 리소스 그룹에서 복원합니다. 복원된 VM을 동일한 자격 증명 모음으로 보호하는 경우, 기존 복구 지점이 자동으로 연결됩니다.
- **옵션 2:** Recovery Services 자격 증명 모음으로 이동하여 데이터 삭제를 통해 보호를 중지합니다.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>일시 삭제된 상태의 항목에 대해 만료된 복구 지점의 영향

Recovery services 자격 증명 모음에 대해 [일시 삭제](backup-azure-security-feature-cloud.md)를 사용하도록 설정하면, 만료된 복구 지점은 일시 삭제된 상태로 유지되고 정리되지 않습니다. 복구 지점이 일시 삭제된 상태인 경우에는 요금이 부과되지 않습니다.

### <a name="impact-of-churn-on-backup-performance"></a>백업 성능에 대한 변동의 영향

VM의 총 스토리지가 8TB이고 변동이 5% 라고 가정합니다. 그런 다음 해당 증분 백업 스토리지는 8TB의 5%인 0.4TB가 됩니다. 변동률이 높을수록 후속 증분 백업의 경우 더 높은 백 엔드 스토리지에 해당합니다. 변동은 백업 성능에 영향을 미칩니다. 변동이 많을수록 백업 프로세스가 느려지고 백 엔드 스토리지 사용량이 증가합니다.

변동이 백업 성능에 미치는 영향을 이해하려면 다음 시나리오를 살펴보세요.

|가상 머신  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|데이터 디스크 수    | 4(A1, A2, A3, A4)        | 4(B1, B2, B3, B4)        |  4(C1, C2, C3, C4)       |
|각 디스크의 크기   |      4 TB   | 4 TB        |  4 TB       |
|백업 데이터 변동    |   A1- 4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

백업 성능은 VM2>VM3>VM1 순서가 됩니다. 그 이유는 변동된 데이터가 다양한 디스크에 분산되기 때문입니다. 디스크 백업이 병렬로 발생하기 때문에 VM2는 최상의 성능을 보여줍니다.

## <a name="frequently-asked-question"></a>질문과 대답

### <a name="how-can-i-find-the-retention-period-of-an-on-demand-backup"></a>주문형 백업의 보존 기간을 확인하려면 어떻게 해야 하나요?

주문형 백업의 백업 작업에 있는 **복구 지점 만료 시간(UTC)** 필드에 복구 지점의 보존 기간이 표시됩니다. 자세한 내용은 [주문형 백업 작업 실행](backup-azure-manage-vms.md#run-an-on-demand-backup)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Backup 아키텍처 및 구성 요소](backup-architecture.md)
