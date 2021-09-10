---
title: VM을 위한 백업 옵션 개요
description: Linux Azure 가상 머신을 위한 백업 옵션 개요입니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: recovery
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: aadbc0fe98f85d96cb06c21b33c8b28be6e732ed
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694405"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Azure의 가상 머신을 위한 백업 및 복원 옵션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

정기적으로 백업을 수행하여 데이터를 보호할 수 있습니다. 사용 사례에 따라 VM에 사용 가능한 여러 백업 옵션이 있습니다.

## <a name="azure-backup"></a>Azure Backup

프로덕션 작업을 실행하는 Azure VM을 백업하려면 Azure Backup을 사용합니다. Azure Backup은 Windows와 Linux VM 모두에 대해 애플리케이션 일치 백업을 지원합니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 특정 파일만 복원할 수 있습니다. 

Azure VM용 Azure Backup에 대한 간단한 소개는 [Azure Backup 빠른 시작](../backup/quick-backup-vm-portal.md)을 참조하세요.

Azure Backup 작동 방법에 대한 자세한 내용은 [Azure에서 VM 백업 인프라 계획](../backup/backup-azure-vms-introduction.md)을 참조하세요.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery는 주요 자연 재해 또는 광범위한 서비스 중단으로 인해 전체 지역에서 중단이 발생하는 주요 재해 시나리오에서 VM을 보호합니다. 비교적 짧은 시간에 한 번의 클릭으로 애플리케이션을 복구할 수 있도록 VM에 Azure Site Recovery를 구성할 수 있습니다. 선택한 Azure 지역에 복제할 수 있고 쌍을 이루는 지역에만 제한되지 않습니다. 

프로덕션 작업 또는 진행 중인 복제에 영향을 주지 않고 주문형 테스트 장애 조치를 사용하여 재해 복구 훈련을 실행할 수 있습니다. 여러 VM에서 실행되는 전체 애플리케이션의 장애 조치 및 장애 복구를 오케스트레이션하는 복구 계획을 만듭니다. 복구 계획 기능은 Azure Automation Runbook과 통합됩니다.

[가상 머신을 복제](../site-recovery/azure-to-azure-quickstart.md)하여 시작할 수 있습니다. 

## <a name="managed-snapshots"></a>관리되는 스냅샷 

개발 및 테스트 환경에서 스냅샷은 Managed Disks를 사용하는 VM을 백업하기 위한 빠르고 간단한 옵션을 제공합니다. 관리되는 스냅샷은 관리 디스크에 대한 읽기 전용 전체 복사본입니다. 스냅샷은 원본 디스크와 별도로 존재하며, 새 관리 디스크를 만들어 VM을 다시 작성하는 데 사용될 수 있습니다. 디스크에서 사용되는 양에 따라 요금이 청구됩니다. 예를 들어 프로비전된 용량이 64GB이고 실제 사용된 데이터 크기가 10GB인 Managed Disks의 스냅샷을 만들 경우 사용된 10GB의 데이터 크기에 대해서만 스냅샷 요금이 청구됩니다.  

스냅샷 만들기에 대한 자세한 내용은 다음을 참조하세요.

* [Windows에서 스냅샷을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](./windows/snapshot-copy-managed-disk.md)
* [Linux에서 스냅샷을 사용하여 관리 디스크로 저장된 VHD 복사본 만들기](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>다음 단계
[Azure Backup 빠른 시작](../backup/quick-backup-vm-portal.md)을 수행하여 Azure Backup을 시도해 볼 수 있습니다.