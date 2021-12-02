---
title: Azure Virtual Machine 복원 프로세스 정보
description: Azure Backup 서비스에서 Azure 가상 머신을 복원하는 방법 알아보기
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 5e9a13ee2488c25bb7aa2d56be27fe45dc6dbdc4
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436993"
---
# <a name="about-azure-vm-restore"></a>Azure VM 복원 정보

이 문서에서는 [Azure Backup 서비스](./backup-overview.md)에서 Azure VM(가상 머신)을 복원하는 방법을 설명합니다. 다양한 복원 옵션이 있습니다. 지원되는 다양한 시나리오를 설명합니다.

## <a name="concepts"></a>개념

- **복구 지점**(**복원 지점** 이라고 함): 복구 지점은 백업 중인 원본 데이터의 복사본입니다.

- **계층(스냅샷과 자격 증명 모음 비교)** : Azure VM 백업은 다음과 같은 두 단계로 수행됩니다.

  - 1단계에서는 생성된 스냅샷이 디스크와 함께 저장됩니다. 이를 **스냅샷 계층** 이라고 합니다. 스냅샷 계층 복원은 복원을 트리거하기 전에 스냅샷이 자격 증명 모음에 복사될 때까지 기다리는 시간을 제거하므로 자격 증명 모음에서 복원하는 것보다 더 빠릅니다. 따라서 스냅샷 계층에서 복원을 [즉시 복원](./backup-instant-restore-capability.md)이라고 합니다.
  - 2단계에서는 스냅샷이 전송되어 Azure Backup 서비스에서 관리하는 자격 증명 모음에 저장됩니다. 이를 **자격 증명 모음 계층** 이라고 합니다.

- **OLR(원래 위치 복구)** : 복원 지점에서 백업이 수행된 원본 Azure VM으로 수행되는 복구이며 복구 지점에 저장된 상태로 바뀝니다. 이는 원본 VM의 OS 디스크와 데이터 디스크를 대체합니다.

- **ALR(대체 위치 복구)** : 복구 지점에서 백업이 수행된 원본 서버 외 다른 서버로 수행되는 복구입니다.

- **ILR(항목 수준 복원)** : 복구 지점에서 VM에 있는 개별 파일이나 폴더 복원

- **가용성(복제 유형)** : Azure Backup은 스토리지/데이터의 고가용성을 유지하는 두 가지 유형의 복제를 제공합니다.
  - [LRS(로컬 중복 스토리지)](../storage/common/storage-redundancy.md#locally-redundant-storage)는 데이터 센터의 스토리지 배율 단위로 데이터를 세 번 복제합니다(세 개의 데이터 복사본 생성). 모든 데이터 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류로부터 데이터를 보호하기 위한 저비용 옵션입니다.
  - [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md#geo-redundant-storage)는 기본값이며 권장하는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다. GRS는 LRS보다 더 많은 비용이 들지만, 지역 가동 중단이 발생하는 경우에도 높은 수준의 데이터 내구성을 제공합니다.
  - [ZRS(영역 중복 스토리지)](../storage/common/storage-redundancy.md#zone-redundant-storage)는 [가용성 영역](../availability-zones/az-overview.md#availability-zones)에서 데이터를 복제하여 동일한 지역에 데이터 상주 및 복원력을 보장합니다. ZRS에는 가동 중지 시간이 없습니다. 따라서 [데이터 상주](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)가 필요하고, 가동 중지 시간이 없어야 하는 중요한 워크로드를 ZRS에서 백업할 수 있습니다.

- **CRR(지역 간 복원)** : [복원 옵션](./backup-azure-arm-restore-vms.md#restore-options) 중 하나로, CRR(지역 간 복원)을 사용하면 [Azure 쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)인 보조 지역에서 Azure VM을 복원할 수 있습니다. 부분 또는 전체 중단 중에 언제든지 또는 선택한 다른 시간에 보조 지역의 데이터를 복원할 수 있습니다. 

## <a name="restore-scenarios"></a>복원 시나리오

![복원 시나리오 ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **시나리오**                                                 | **수행되는 작업**                                             | **사용하는 경우**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [복원하여 새 가상 머신 만들기](./backup-azure-arm-restore-vms.md) | 전체 VM을 OLR(원본 VM이 계속 있는 경우) 또는 ALR로 복원 | <li> 원본 VM이 손실되었거나 손상된 경우 전체 VM 복원 가능  <li> VM 복사본 생성 가능  <li> 감사 또는 규정 준수 복원 드릴 수행 가능  <li> Marketplace 이미지에서 생성된 Azure VM에는 이 옵션이 작동하지 않습니다(즉, 라이선스가 만료되어 사용할 수 없는 경우). |
| [VM 디스크 복원](./backup-azure-arm-restore-vms.md#restore-disks) | VM에 연결된 디스크 복원                             |  모든 디스크: 이 옵션은 템플릿을 만들고 디스크를 복원합니다. 이 템플릿을 요구 사항이 충족되도록 특수 구성(예: 가용성 집합)을 사용하여 수정한 다음, 템플릿을 둘 다 사용하고 디스크를 복원하여 VM을 다시 만들 수 있습니다. |
| [VM 내의 특정 파일 복원](./backup-azure-restore-files-from-vm.md) | 복원 지점을 선택하고 파일을 탐색 및 선택하고 백업한 VM과 동일한(또는 호환되는) OS로 복원합니다. |  복원할 특정 파일을 알고 있는 경우 전체 VM을 복원하는 대신 이 옵션을 사용합니다. |
| [암호화된 VM 복원](./backup-azure-vms-encryption.md) | 포털에서 디스크를 복원한 다음, PowerShell을 사용하여 VM 생성 | <li> [Azure Active Directory로 암호화된 VM](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Azure AD 없이 암호화된 VM](../virtual-machines/windows/disk-encryption-windows.md) <li> [*Azure AD 없이* 마이그레이션된 *Azure AD로* 암호화된 VM](../virtual-machines/windows/disk-encryption-faq.yml#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app-) |
| [지역 간 복원](./backup-azure-arm-restore-vms.md#cross-region-restore) | 새 VM 생성 또는 디스크를 보조 지역(Azure 쌍을 이루는 지역)으로 복원 | <li> **전체 중단**: 지역 간 복원 기능을 사용하면 보조 지역에서 데이터가 복구될 때까지 기다릴 필요가 없습니다. Azure에서 중단을 선언하기 전이라도 보조 지역에서 복원을 초기화할 수 있습니다. <li> **부분 중단**: Azure Backup이 백업 데이터를 저장하는 특정 스토리지 클러스터에서 또는 Azure Backup 및 백업 데이터와 연결된 스토리지 클러스터를 연결하는 네트워크에서도 가동 중지 시간이 발생할 수 있습니다. 지역 간 복원을 사용하면 보조 지역의 백업 데이터 복제본을 사용하여 보조 지역에서 복원을 수행할 수 있습니다. <li> **중단 없음**: 보조 지역 데이터를 사용하여 감사 또는 규정 준수 목적으로 BCDR(비즈니스 연속성 및 재해 복구) 드릴을 수행할 수 있습니다. 이를 통해 비즈니스 연속성 및 재해 복구 드릴의 주 지역에서 전체 또는 부분 중단이 발생하지 않더라도 보조 지역에서 백업 데이터 복원을 수행할 수 있습니다.  |

## <a name="next-steps"></a>다음 단계

- [VM 복원에 대한 질문과 대답](./backup-azure-vm-backup-faq.yml)
- [지원되는 복원 방법](./backup-support-matrix-iaas.md#supported-restore-methods)
- [복원 문제 해결](./backup-azure-vms-troubleshoot.md#restore)
