---
title: 재해 복구 및 저장소 계정 장애 조치(failover)
titleSuffix: Azure Storage
description: Azure Storage는 지역 중복 스토리지 계정에 대해 계정 장애 조치를 지원합니다. 계정 장애 조치(failover)의 경우 기본 엔드포인트를 사용할 수 없는 경우 스토리지 계정에 대해 장애 조치(failover) 프로세스를 시작할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 60e4d7cc9d24ca166e30c42b0f5a8159c9babeff
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407554"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>재해 복구 및 저장소 계정 장애 조치(failover)

Microsoft는 Azure 서비스를 항상 사용할 수 있도록 하기 위해 노력합니다. 그러나 계획되지 않은 서비스 중단이 발생할 수 있습니다. 애플리케이션에 복원력이 필요한 경우 지역 중복 스토리지를 사용하여 데이터를 두 번째 지역에서 복사하는 것이 좋습니다. 또한 고객은 지역 서비스 중단을 처리하기 위해 재해 복구 계획을 설정하는 것이 좋습니다. 재해 복구 계획의 중요한 부분은 기본 엔드포인트를 사용할 수 없는 경우 보조 엔드포인트로의 장애 조치(failover)를 준비하는 것입니다.

Azure Storage는 지역 중복 스토리지 계정에 대해 계정 장애 조치를 지원합니다. 계정 장애 조치(failover)의 경우 기본 엔드포인트를 사용할 수 없는 경우 스토리지 계정에 대해 장애 조치(failover) 프로세스를 시작할 수 있습니다. 장애 조치(failover)는 스토리지 계정의 기본 엔드포인트가 되도록 보조 엔드포인트를 업데이트합니다. 장애 조치(failover)가 완료되면 클라이언트는 새 기본 엔드포인트에 쓰기 시작할 수 있습니다.

계정 장애 조치(failover)는 Azure Resource Manager 배포를 사용하는 범용 v1, 범용 v2 및 Blob 스토리지 계정 유형에 사용할 수 있습니다. 계정 장애 조치(failover)는 계층 구조 네임스페이스를 사용하도록 설정한 스토리지 계정에 대해 지원되지 않습니다.

이 문서에서는 계정 장애 조치(failover)와 관련된 개념 및 프로세스를 설명하고 고객에게 최소의 영향만 미치고 복구할 수 있게 스토리지 계정을 준비하는 방법을 논의합니다. Azure Portal 또는 PowerShell에서 계정 장애 조치(failover)를 시작하는 방법을 알아보려면 [계정 장애 조치(failover) 시작](storage-initiate-account-failover.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>적절한 중복 옵션 선택

Azure Storage는 내구성 및 고가용성을 보장하기 위해 스토리지 계정의 여러 복사본을 유지 관리합니다. 계정에 대해 선택하는 중복 옵션은 필요한 복원력 수준에 따라 달라집니다. 지역 가동 중단으로부터 보호하려는 경우 보조 지역에서 읽기 액세스 옵션을 제공하거나 제공하지 않는 지역 중복 스토리지에 대한 계정을 구성합니다.

**GRS(지역 중복 스토리지) 또는 GZRS(지역 영역 중복 스토리지)** 는 수백 킬로미터 떨어져 있는 두 지리적 지역에서 비동기적으로 데이터를 복사합니다. 주 지역에서 중단이 발생하는 경우 보조 지역이 데이터의 중복 원본으로 사용됩니다. 장애 조치(failover)를 시작하여 보조 엔드포인트를 기본 엔드포인트으로 변환할 수 있습니다.

**RA-GRS(읽기 액세스 지역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 중복 스토리지)** 는 보조 엔드포인트에 대해 추가적인 읽기 액세스 권한 혜택을 포함하는 지역 중복 스토리지를 제공합니다. 기본 엔드포인트에서 중단이 발생하는 경우 보조 엔드포인트에 대한 읽기 권한을 위해 구성되고 고가용성을 위해 설계된 애플리케이션이 보조 엔드포인트에서 계속 읽을 수 있습니다. Microsoft는 애플리케이션의 최대 가용성 및 내구성을 위해 RA-GZRS를 권장합니다.

Azure Storage의 중복성에 대한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

> [!WARNING]
> 지역 중복 스토리지를 사용하면 데이터가 손실될 위험이 있습니다. 데이터는 비동기적으로 보조 지역으로 복사됩니다. 즉, 주 지역에 기록된 데이터가 보조 지역에 기록되는 시간 사이에는 지연이 발생합니다. 중단이 발생할 경우 보조 엔드포인트에 아직 복사되지 않은 기본 엔드포인트에 대한 쓰기 작업은 손실됩니다.

## <a name="design-for-high-availability"></a>고가용성을 위한 디자인

처음부터 고가용성을 위해 애플리케이션을 디자인하는 것이 중요합니다. 애플리케이션을 디자인하고 재해 복구를 계획하기 위한 지침에 대해서는 다음 Azure 리소스를 참조하세요.

- [Azure용 복원 애플리케이션 디자인](/azure/architecture/framework/resiliency/app-design): Azure에서 고가용성 애플리케이션을 설계하기 위한 주요 개념을 간략하게 설명합니다.
- [복원력 검사 목록](/azure/architecture/checklist/resiliency-per-service): 애플리케이션이 고가용성을 위한 디자인 모범 사례를 구현하는지 확인하기 위한 검사 목록입니다.
- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md): 지역 중복 스토리지를 활용하는 애플리케이션을 빌드하기 위한 설계 지침입니다.
- [자습서: Blob Storage로 고가용성 애플리케이션 빌드](../blobs/storage-create-geo-redundant-storage.md): 실패 및 복구를 시뮬레이션할 때 엔드포인트 간을 자동으로 전환하는 고가용성 애플리케이션을 빌드하는 방법을 보여주는 자습서입니다.

또한 Azure Storage 데이터에 대해 고가용성을 유지하기 위한 다음 모범 사례도 참조하세요.

- **Disks:** [Azure Backup](https://azure.microsoft.com/services/backup/)을 사용하여 Azure Virtual Machines에서 사용하는 VM 디스크를 백업합니다. 또한 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 사용하여 지역 재해 발생 시 VM을 보호하는 것이 좋습니다.
- **블록 Blob:** [일시 삭제](../blobs/soft-delete-blob-overview.md)를 설정하여 개체 수준 삭제 및 덮어쓰기에 대해 보호하거나 [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/) 또는 [Azure 데이터 이동 라이브러리](storage-use-data-movement-library.md)를 사용하여 다른 지역에 있는 다른 스토리지 계정에 블록 Blob을 복사합니다.
- **파일:** [Azure Backup](../../backup/azure-file-share-backup-overview.md)을 사용하여 파일 공유를 백업합니다. 또한 실수로 인한 파일 공유 삭제로부터 보호하기 위해 [일시 삭제](../files/storage-files-prevent-file-share-deletion.md)를 사용하도록 설정합니다. GRS를 사용할 수 없을 때 지리적 중복성을 위해 [AzCopy](./storage-use-azcopy-v10.md) 또는 [Azure PowerShell](/powershell/module/az.storage/)을 사용하여 다른 지역의 다른 스토리지 계정에 파일을 복사합니다.
- **테이블:**[AzCopy](./storage-use-azcopy-v10.md)를 사용하여 다른 지역에 있는 다른 스토리지 계정으로 테이블 데이터를 내보냅니다.

## <a name="track-outages"></a>중단 추적

고객은 [Azure Service Health 대시보드](https://azure.microsoft.com/status/)를 구독하여 Azure Storage 및 다른 Azure 서비스의 상태를 추적할 수 있습니다.

쓰기 오류의 가능성에 대비하도록 애플리케이션을 디자인하는 것이 좋습니다. 애플리케이션은 주 지역의 가동 중단 가능성을 경고하는 방식으로 쓰기 오류를 공개해야 합니다.

## <a name="understand-the-account-failover-process"></a>계정 장애 조치(failover) 프로세스 이해

고객이 관리하는 계정 장애 조치(failover)를 사용하면 어떤 이유로든 주 지역을 사용할 수 없게 될 경우 전체 스토리지 계정을 보조 지역으로 장애 조치(failover)할 수 있습니다. 강제로 보조 지역으로 장애 조치(failover)하는 경우 클라이언트는 장애 조치(failover)가 완료된 후에 보조 엔드포인트에 데이터를 쓰기 시작할 수 있습니다. 장애 조치(failover)에는 일반적으로 약 1시간이 걸립니다.

> [!NOTE]
> 이 기능은 계층 구조 네임스페이스(Azure Data Lake Storage Gen2)가 있는 계정에서 아직 지원되지 않습니다. 자세히 알아보려면 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob 스토리지 기능](../blobs/storage-feature-support-in-storage-accounts.md)을 참조하세요.

### <a name="how-an-account-failover-works"></a>계정 장애 조치(failover)가 작동하는 방식

정상적인 상황에서 클라이언트는 주 지역의 Azure Storage 계정에 데이터를 쓰며, 해당 데이터는 보조 지역에 비동기적으로 복사됩니다. 다음 그림은 주 지역을 사용할 수 있는 경우의 시나리오를 보여 줍니다.

![클라이언트가 주 지역의 스토리지 계정에 데이터를 기록합니다.](media/storage-disaster-recovery-guidance/primary-available.png)

어떤 이유로든 기본 엔드포인트를 사용할 수 없는 경우 클라이언트는 스토리지 계정에 더 이상 쓸 수 없습니다. 다음 그림은 기본 엔드포인트를 사용할 수 없지만 복구가 아직 발생하지 않은 시나리오를 보여 줍니다.

![기본 엔드포인트를 사용할 수 없으므로 클라이언트가 데이터를 쓸 수 없습니다.](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

고객은 보조 엔드포인트에 대한 계정 장애 조치(failover)를 시작합니다. 장애 조치(failover) 프로세스는 Azure Storage에서 제공하는 DNS 항목을 업데이트하므로 다음 그림에 나와 있는 것처럼 보조 엔드포인트가 스토리지 계정의 새 기본 엔드포인트가 됩니다.

![고객은 보조 엔드포인트로의 계정 장애 조치(failover)를 시작합니다.](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

DNS 항목이 업데이트되면 지역 중복 계정의 쓰기 액세스 권한이 복원되고 요청이 새 기본 엔드포인트로 전달됩니다. Blob, 테이블, 큐 및 파일에 대한 기존 스토리지 서비스 엔드포인트는 장애 조치(failover) 후에도 동일하게 유지됩니다.

> [!IMPORTANT]
> 장애 조치(failover)를 완료한 후 스토리지 계정은 새 기본 엔드포인트에서 로컬로 중복되도록 구성됩니다. 새 보조 엔드포인트로의 복제를 재개하려면 지리적 중복성에 대한 계정을 다시 구성합니다.
>
> 지역 중복을 사용하도록 로컬 중복 스토리지 계정을 변환하려면 비용과 시간이 필요합니다. 자세한 내용은 [계정 장애 조치의 중요한 의미](storage-initiate-account-failover.md#important-implications-of-account-failover)를 참조하세요.

### <a name="anticipate-data-loss"></a>데이터 손실 예상

> [!CAUTION]
> 계정 장애 조치(Failover)를 수행하면 일반적으로 데이터가 일부 손실됩니다. 계정 장애 조치(failover)를 시작할 때 진행되는 과정을 이해하는 것이 중요합니다.

데이터는 비동기적으로 주 지역에서 보조 지역으로 기록되므로 주 지역에 쓴 데이터가 보조 지역으로 복사되기 전에 항상 지연이 발생합니다. 주 지역을 사용할 수 없는 경우 가장 최근 쓰기가 보조 지역에 아직 복사되지 않을 수 있습니다.

장애 조치(failover)를 강제로 적용하면 보조 지역이 새 주 지역이 되기 때문에 주 지역의 모든 데이터가 손실됩니다. 새 주 지역은 장애 조치(failover) 후 로컬 중복이 되도록 구성됩니다.

장애 조치(failover)가 발생하면 보조 지역으로 이미 복사된 모든 데이터가 유지됩니다. 그러나 보조 지역으로 아직 복사되지 않은 주 지역에 쓴 데이터는 영구히 손실됩니다.

**마지막 동기화 시간** 속성은 주 지역의 데이터가 보조 지역에 기록될 것으로 보장되는 가장 최근 시간을 나타냅니다. 마지막 동기화 시간 전에 기록된 모든 데이터는 보조 지역에서 사용할 수 있지만, 마지막 동기화 시간 이후에 기록된 데이터는 보조 지역에 기록되지 않았을 수 있으므로 손실될 수 있습니다. 작동 중단 시 이 속성을 사용하여 계정 장애 조치(failover) 시작으로 인해 발생할 수 있는 데이터 손실 크기를 예상합니다.

모범 사례로, 마지막 동기화 시간을 사용하여 예상되는 데이터 손실을 예측할 수 있도록 애플리케이션을 디자인합니다. 예를 들어, 모든 쓰기 작업을 기록하는 경우 마지막 쓰기 작업의 시간을 마지막 동기화 시간과 비교하여 보조 지역과 동기화되지 않은 쓰기를 확인할 수 있습니다.

**마지막 동기화 시간** 속성을 확인하는 방법에 대한 자세한 내용은 [스토리지 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조하세요.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>원래 주 지역으로 장애 복구(Failback)할 때 주의

주 지역에서 보조 지역으로 장애 조치(failover)한 후에 스토리지 계정이 새 주 지역에서 로컬로 중복되도록 구성됩니다. 그런 다음 지역 중복을 위해 새 주 지역에서 계정을 구성할 수 있습니다. 계정이 장애 조치(failover) 후 지역 중복을 위해 구성되면 새 주 지역은 원래 장애 조치(failover) 이전에 주 지역이었던 새 보조 지역으로 데이터를 즉시 복사하기 시작합니다. 그러나 새 주 지역의 기존 데이터가 새 보조 지역에 완전히 복사되기까지 다소 시간이 걸릴 수 있습니다.

저장소 계정이 지리적 중복성을 위해 다시 구성 된 후 새 주 데이터베이스에서 새 보조 데이터베이스로 장애 복구 (failback)를 시작할 수 있습니다. 이 경우 장애 조치(failover) 이전의 원래 주 지역은 다시 주 지역이 되며 원래 기본 구성이 GRS/RA-GRS였는지 GZRS/RA-GZRS였는지에 따라 로컬 중복 또는 영역 중복이 되도록 구성됩니다. 장애 조치(failover) 이후 주 지역(원래 보조 지역)의 모든 데이터는 장애 복구(failback) 중에 손실됩니다. 스토리지 계정의 데이터 대부분이 장애 복구(failback) 전에 새 보조 지역에 복사되지 않으면 주요 데이터가 손실될 수 있습니다.

주요 데이터 손실을 방지하려면 장애 복구(failback) 전에 **마지막 동기화 시간** 속성 값을 확인합니다. 마지막 동기화 시간을 데이터를 새 주 지역에 마지막으로 기록한 시간과 비교하여 데이터 손실을 예측합니다.

장애 복구(failback) 작업 후에는 새 주 지역이 지역 중복이 되도록 다시 구성할 수 있습니다. 원래 주 지역이 LRS에 대해 구성된 경우에는 GRS 또는 RA-GRS가 되도록 구성할 수 있습니다. 원래 주 지역이 ZRS에 대해 구성된 경우에는 GZRS 또는 RA-GZRS가 되도록 구성할 수 있습니다. 추가 옵션은 [스토리지 계정 복제 방법 변경](redundancy-migration.md)을 참조하세요.

## <a name="initiate-an-account-failover"></a>계정 장애 조치(failover) 시작

Azure Portal, PowerShell, Azure CLI 또는 Azure Storage 리소스 공급자 API에서 계정 장애 조치(failover)를 시작할 수 있습니다. 장애 조치(failover)를 시작하는 방법에 대한 자세한 내용은 [계정 장애 조치(failover) 시작](storage-initiate-account-failover.md)을 참조하세요.

## <a name="additional-considerations"></a>기타 고려 사항

강제 장애 조치(failover)를 수행할 경우 애플리케이션 및 서비스가 어떤 영향을 받을 수 있는지를 이해하려면 이 섹션에 설명된 추가 고려 사항을 검토합니다.

### <a name="storage-account-containing-archived-blobs"></a>보관된 BLOB을 포함하는 스토리지 계정

보관된 BLOB을 포함하는 스토리지 계정은 계정 장애 조치(failover)를 지원합니다. 장애 조치(failover)가 완료된 후에는 보관된 모든 BLOB을 온라인 계층으로 리하이드레이션해야만 지리적 중복성을 위해 계정을 구성할 수 있습니다.

### <a name="storage-resource-provider"></a>스토리지 리소스 공급자

장애 조치(failover)가 완료되면 클라이언트는 새 주 지역에서 Azure Storage 데이터를 다시 읽고 쓸 수 있습니다. 그러나 Azure Storage 리소스 공급자는 장애 조치(fail over)되지 않으므로 리소스 관리 작업은 주 지역에서 계속 수행되어야 합니다. 주 지역을 사용할 수 없는 경우 스토리지 계정에서 관리 작업을 수행할 수 없습니다.

Azure Storage 리소스 공급자가 장애 조치(failover)되지 않으므로 [위치](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) 속성은 장애 조치(failover)가 완료된 후 원래 기본 위치를 반환합니다.

### <a name="azure-virtual-machines"></a>Azure 가상 머신

Azure VM(Virtual Machines)은 계정 장애 조치(failover)의 일부로 장애 조치(failover)되지 않습니다. 주 지역을 사용할 수 없으며 보조 지역으로 장애 조치(failover)할 경우 장애 조치(failover) 후에 VM을 다시 만들어야 합니다. 또한 계정 장애 조치(failover)와 관련된 잠재적인 데이터 손실이 있습니다. Microsoft는 Azure의 가상 머신과 관련하여 다음과 같은 [고가용성](../../virtual-machines/availability.md) 및 [재해 복구](../../virtual-machines/backup-recovery.md) 지침을 권장합니다.

### <a name="azure-unmanaged-disks"></a>Azure 관리되지 않는 디스크

모범 사례로, 관리되지 않는 디스크를 Managed Disks로 변환하는 것이 좋습니다. 그러나 Azure VM에 연결된 관리되지 않는 디스크를 포함하는 계정을 장애 조치(failover)해야 할 경우 장애 조치(failover)를 시작하기 전에 VM을 종료해야 합니다.

관리되지 않는 디스크는 Azure Storage에 페이지 Blob으로 저장됩니다. VM을 Azure에서 실행 중인 경우 VM에 연결된 모든 관리되지 않는 디스크가 임대됩니다. Blob에 임대가 있으면 계정 장애 조치(failover)를 계속할 수 없습니다. 장애 조치(failover)를 수행하려면 다음 단계를 수행합니다.

1. 시작하기 전에 관리되지 않는 디스크, 해당 LUN(논리 단위 번호) 및 해당 디스크가 연결된 VM을 기록해 둡니다. 이렇게 하면 장애 조치(failover) 후에 디스크를 보다 쉽게 다시 연결할 수 있습니다.
2. VM을 종료합니다.
3. VM을 삭제하지만 관리되지 않는 디스크의 VHD 파일은 유지합니다. VM을 삭제한 시간을 기록해 둡니다.
4. VM을 삭제한 이후에 해당하는 **마지막 동기화 시간** 이 업데이트될 때까지 기다립니다. 장애 조치(failover)가 발생할 때 보조 엔드포인트가 VHD 파일로 완전히 업데이트되지 않은 경우 새 주 지역에서 VM이 제대로 작동하지 않을 수 있으므로 이 단계가 필요합니다.
5. 계정 장애 조치(failover)를 시작합니다.
6. 계정 장애 조치(failover)가 완료되고 보조 지역이 새 주 지역이 될 때까지 기다립니다.
7. 새 주 지역에서 VM을 만들고 VHD를 다시 연결합니다.
8. 새 VM을 시작합니다.

VM이 종료되면 임시 디스크에 저장된 데이터가 손실됩니다.

## <a name="unsupported-features-and-services"></a>지원되지 않는 기능 및 서비스

다음 기능 또는 서비스는 계정 장애 조치(failover)에서 지원되지 않습니다.

- Azure 파일 동기화는 스토리지 계정 장애 조치(Failover)를 지원하지 않습니다. Azure 파일 동기화에서 클라우드 엔드포인트로 사용되는 Azure 파일 공유를 포함하는 스토리지 계정은 장애 조치(failover)하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다.
- 계층 구조 네임스페이스를 사용하는 스토리지 계정(예: Data Lake Storage Gen2)은 현재 지원되지 않습니다.
- 프리미엄 블록 blob을 포함하는 스토리지 계정은 장애 조치(failover)할 수 없습니다. 프리미엄 블록 blob를 지원하는 스토리지 계정은 현재 지리적 중복을 지원하지 않습니다.
- [WORM 불변성 정책](../blobs/immutable-storage-overview.md)이 활성화된 컨테이너의 스토리지 계정은 장애 조치(failover)할 수 없습니다. 잠금 해제/잠금 시간 기반 보존 또는 법적 보류 정책은 규정 준수를 유지하기 위해 장애 조치(failover)를 방지합니다.

## <a name="copying-data-as-an-alternative-to-failover"></a>장애 조치(failover) 대신 데이터 복사

스토리지 계정이 보조 엔드포인트에 대한 읽기 권한으로 구성된 경우 보조 엔드포인트에서 읽도록 애플리케이션을 설계할 수 있습니다. 주 지역에서 중단이 발생할 경우 장애 조치(failover)하지 않으려는 경우 [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/) 또는 [Azure 데이터 이동 라이브러리](../common/storage-use-data-movement-library.md)와 같은 도구를 사용하여 보조 지역의 스토리지 계정에 있는 데이터를 영향을 받지 않는 지역의 다른 스토리지 계정으로 복사할 수 있습니다. 그런 다음, 읽기 및 쓰기 가용성을 위해 애플리케이션에서 해당 스토리지 계정을 가리키도록 할 수 있습니다.

> [!CAUTION]
> 계정 장애 조치(failover)는 데이터 마이그레이션 전략의 일부로 사용하면 안 됩니다.

## <a name="microsoft-managed-failover"></a>Microsoft에서 관리하는 장애 조치(failover)

중대한 재해로 인해 지역이 손실되는 극단적인 경우 Microsoft는 지역 장애 조치(failover)를 시작할 수 있습니다. 이 경우에 사용자의 조치가 필요하지 않습니다. Microsoft에서 관리하는 장애 조치(failover)가 완료될 때까지 스토리지 계정에 대한 쓰기 액세스 권한이 없습니다. 애플리케이션은 스토리지 계정이 RA-GRS 또는 RA-GZRS용으로 구성된 경우 보조 지역에서 읽을 수 있습니다.

## <a name="see-also"></a>참고 항목

- [지리적 중복성을 사용하여 고가용성 애플리케이션 설계](geo-redundant-design.md)
- [계정 장애 조치(failover) 시작](storage-initiate-account-failover.md)
- [저장소 계정에 대한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [자습서: Blob Storage에서 고가용성 애플리케이션 빌드](../blobs/storage-create-geo-redundant-storage.md)
