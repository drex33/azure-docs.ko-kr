---
title: Azure VM을 Managed Disks로 마이그레이션
description: 스토리지 계정에서 관리되지 않는 디스크를 사용하여 만든 Azure 가상 머신을 마이그레이션하여 Managed Disks를 사용합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9fc976ae7854ef3f787492c012877c9203e7712a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690165"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM을 Azure의 Managed Disks로 마이그레이션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

Azure Managed Disks는 스토리지 계정을 별도로 관리하지 않아도 되기 때문에 스토리지 관리를 간소화합니다.  또한 기존 Azure VM을 Managed Disks에 마이그레이션하여 가용성 집합에서 VM의 안정성을 향상시킬 수 있습니다. 가용성 집합에서 여러 VM의 디스크는 단일 실패 지점을 방지하기 위해 충분히 서로 격리되어야 합니다. 서로 다른 스토리지 배율 단위(스탬프)인 자동 가용성 집합에서 다른 VM의 디스크를 자동으로 배치합니다. 그러면 하드웨어 및 소프트웨어 오류로 인해 발생한 단일 스토리지 배율 단위 오류의 영향을 제한합니다.
필요에 따라 4가지 유형의 스토리지 옵션 중에 하나를 선택할 수 있습니다. 사용 가능한 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](../disks-types.md) 문서를 참조하세요.

## <a name="migration-scenarios"></a>마이그레이션 시나리오

다음과 같은 시나리오에서 Managed Disks를 마이그레이션할 수 있습니다.

|시나리오  |아티클  |
|---------|---------|
|가용성 집합의 VM 및 독립 실행형 VM을 관리 디스크로 변환     |[관리 디스크를 사용하도록 VM 변환](convert-unmanaged-to-managed-disks.md)         |
|Managed Disks에서 단일 VM을 클래식에서 Resource Manager로 변환     |[클래식 VHD에서 VM 만들기](create-vm-specialized-portal.md)         |
|Managed Disks에서 VNet에 있는 모든 VM을 클래식에서 Resource Manager로 변환     |[클래식에서 Resource Manager로 IaaS 리소스를 마이그레이션](../migration-classic-resource-manager-ps.md)한 다음 [관리되지 않는 디스크에서 Managed Disks로 VM 변환](convert-unmanaged-to-managed-disks.md)         |
|표준 비관리 디스크를 사용하는 VM을 관리 프리미엄 디스크를 사용하는 VM으로 업그레이드     | 먼저, [비관리 디스크에서 관리 디스크로 Windows 가상 머신을 변환](convert-unmanaged-to-managed-disks.md)합니다. 그런 다음 [관리 디스크의 스토리지 형식을 업데이트](convert-disk-storage.md)합니다.         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>다음 단계

- [Managed Disks](../managed-disks-overview.md)에 대한 자세한 정보
- [Managed Disks에 대한 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 검토합니다.