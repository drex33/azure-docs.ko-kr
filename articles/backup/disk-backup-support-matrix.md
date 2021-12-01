---
title: Azure Disk Backup 지원 매트릭스
description: Azure Disk Backup 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 12/01/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 83ed12b2804a98fc51b34fd02395587580b930a0
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407630"
---
# <a name="azure-disk-backup-support-matrix"></a>Azure Disk Backup 지원 매트릭스

[Azure Backup](./backup-overview.md)을 사용하여 Azure Disk를 보호할 수 있습니다. 이 문서에는 지역 가용성, 지원되는 시나리오 및 제한 사항이 요약되어 있습니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Disk Backup은 모든 퍼블릭 클라우드 및 소버린 클라우드 지역에서 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

- Azure Disk Backup은 공유 디스크(공유 프리미엄 SSD)를 포함하여 Azure Managed Disks에서 지원됩니다. 비관리 디스크는 지원되지 않습니다. 현재 이 솔루션은 스냅샷 기능이 없기 때문에 공유 Ultra Disk를 포함하여 Ultra Disk를 지원하지 않습니다.

- Azure Disk Backup은 쓰기 가속기 디스크의 백업을 지원합니다. 그러나 복원 중에는 디스크가 일반 디스크로 복원됩니다. 디스크를 VM에 탑재하면 디스크에서 쓰기 가속화 캐시를 사용하도록 설정할 수 있습니다.

- Azure Backup은 하루에 여러 번 백업을 지원하는 Azure 관리 디스크의 작업(스냅샷) 계층 백업을 제공합니다. 백업은 백업 자격 증명 모음에 복사되지 않습니다.

- 현재, 백업을 수행한 기존 원본 디스크를 교체하여 복원할 수 있는 OLR(원래 위치 복구) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 백업이 수행된 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR(대체 위치 복구)라고 합니다.

- Managed Disks에 대한 Azure Backup은 디스크당 스냅샷 200개로 제한되는 증분 스냅샷을 사용합니다. 예약된 백업 외에도 요청 시 백업을 수행할 수 있도록 Backup 정책은 전체 백업을 180으로 제한합니다. 관리 디스크의 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md#restrictions)에 대해 자세히 알아보세요.

- Azure [구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits)은 구독당 지역별 총 디스크 스냅샷 수에 적용됩니다.

- 가상 머신에 연결된 여러 디스크의 특정 시점 스냅샷은 지원되지 않습니다.

- Backup 자격 증명 모음과 백업할 디스크는 동일하거나 다른 구독에 있을 수 있습니다. 그러나 백업할 백업 자격 증명 모음과 디스크는 모두 동일한 지역에 있어야 합니다.

- 백업할 디스크 및 스냅샷이 로컬에 저장되는 스냅샷 리소스 그룹은 동일한 구독에 있어야 합니다.

- 백업에서 동일하거나 다른 구독으로 디스크 백업하기가 지원됩니다. 그러나 복원된 디스크는 스냅샷과 동일한 지역에 만들어집니다.

- ADE 암호화된 디스크는 보호할 수 없습니다.

- 플랫폼 관리형 키 또는 고객 관리형 키로 암호화된 디스크가 지원됩니다. 그러나 디스크 암호화 집합 KeyVault 키가 사용하지 않도록 설정되거나 삭제된 경우, CMK(고객 관리형 키)를 사용해 암호화되는 디스크의 복원 지점이 복원되지 않습니다.

- 현재 Backup 정책은 수정할 수 없으며, 디스크의 백업을 구성할 때 백업 인스턴스에 할당되는 스냅샷 리소스 그룹은 변경할 수 없습니다.

- 현재 디스크의 백업을 구성하는 Azure Portal 환경은 동일한 구독의 최대 20개의 디스크로 제한됩니다.

- Azure Disk Backup은 PowerShell을 지원합니다. 현재 Azure CLI 지원되지 않습니다.

- 백업을 구성할 때 백업하도록 선택한 디스크와 스냅샷이 저장될 스냅샷 리소스 그룹은 동일한 구독의 일부여야 합니다. 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅샷을 만들 수 없습니다. 관리 디스크의 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md#restrictions)에 대해 자세히 알아보세요. 스냅샷 리소스 그룹을 선택하는 방법에 대한 자세한 내용은 [백업 구성](backup-managed-disks.md#configure-backup)을 참조하세요.

- 백업/복원 작업에 성공하려면 Backup 자격 증명 모음 관리 ID에 역할을 할당해야 합니다. 설명서에 제공된 역할 정의만 사용합니다. 소유자, 참가자 등의 다른 역할을 사용하는 기능은 지원되지 않습니다. 역할을 할당한 후 즉시 백업 또는 복원 작업을 구성하기 시작하면 권한 문제가 발생할 수 있습니다. 역할 할당을 적용하는 데 몇 분이 걸릴 수 있기 때문입니다.

- 관리 디스크를 사용하면 배포 시 또는 나중에 디스크 크기를 변경하지 않고 성능 계층을 변경할 수 있습니다. Azure Disk Backup 솔루션은 백업 중인 원본 디스크의 성능 계층 변경을 지원합니다. 복원하는 동안 복원된 디스크의 성능 계층은 백업 시점의 원본 디스크와 동일해집니다. 복원 작업 후 디스크의 성능 계층을 변경하려면 [여기에서](../virtual-machines/disks-performance-tiers-portal.md) 설명서를 따르세요.

- 관리 디스크에 대한 [Private Link](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) 지원을 사용하면 관리 디스크의 내보내기/가져오기를 제한하여 Azure 가상 네트워크 내에서만 발생하도록 할 수 있습니다. Azure Disk Backup은 프라이빗 엔드포인트가 사용하도록 설정된 디스크의 백업을 지원합니다. 여기에는 프라이빗 엔드포인트를 통해 액세스할 수 있는 백업 데이터 또는 스냅샷이 포함되지 않습니다.

- 백업 인스턴스를 삭제하면 백업이 중지되고 백업 데이터도 모두 삭제됩니다. **백업 중지 및 백업 데이터 보존** 옵션이 지원되지 않으므로, 현재 백업을 사용하지 않도록 설정할 수 없습니다.

- Azure Disk Backup 제한은 다음과 같습니다.
    
    | 설정 | 최대 제한 |
    | --- | --- |
    | Backup Vault당 백업 정책 수 | 5,000 |
    | Backup Vault당 백업 인스턴스 수 | 5,000 |
    | 백업 인스턴스당 하루에 허용되는 주문형 백업 수 | 10 |
    | 백업 인스턴스당 하루에 허용되는 복원 수 | 10 |

## <a name="next-steps"></a>다음 단계

- [Azure Managed Disks 백업](backup-managed-disks.md)