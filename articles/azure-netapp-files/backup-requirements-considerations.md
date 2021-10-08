---
title: Azure NetApp Files 백업 | 대한 요구 사항 및 고려 사항 Microsoft Docs
description: Azure NetApp Files 백업을 사용하기 전에 알아야 할 요구 사항 및 고려 사항에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: b-juche
ms.openlocfilehash: f396904b3832403337a6fa4553e2ae4a4b8c86b4
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667956"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항 

이 문서에서는 Azure NetApp Files 백업을 사용하기 전에 알아야 할 요구 사항 및 고려 사항에 대해 설명합니다.

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

Azure NetApp Files 백업을 사용하기 전에 몇 가지 요구 사항 및 고려 사항을 알고 있어야 합니다. 

* Azure NetApp Files 백업은 Azure NetApp Files 구독과 연결된 지역에서 사용할 수 있습니다. 지역의 Azure NetApp Files 백업은 동일한 지역에 있는 Azure NetApp Files 볼륨만 보호할 수 있습니다. 예를 들어 미국 서부 2에 있는 볼륨에 대해 미국 서부 2의 서비스에서 만든 백업은 미국 서부 2에도 있는 Azure Storage로 전송됩니다. Azure NetApp Files 다른 지역에 대한 백업 또는 백업 복제를 지원하지 않습니다.  

* 백업이 실제로 완료된 후 백업을 표시하는 데 최대 5분이 지연될 수 있습니다.

* 현재 Azure NetApp Files 백업 기능은 연결된 스냅샷 정책에서 만든 일별, 주별 및 월별 로컬 스냅샷을 Azure Storage에 백업하도록 지원합니다. 시간별 백업은 현재 지원되지 않습니다.

* Azure NetApp Files 백업은 LRS(로컬 [중복 Storage) 스토리지만](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) 지원되는 아래 나열된 지역을 제외하고 지역의 세 Azure 가용성 영역에서 데이터를 동기적으로 복제하는 ZRS(영역 [중복](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) 스토리지) 계정을 사용합니다.   

    * 미국 서부   

    LRS는 서버 랙 및 드라이브 오류에서 복구할 수 있습니다. 그러나 데이터 센터 내에서 화재 또는 정전과 같은 재해가 발생하면 LRS를 사용하는 스토리지 계정의 모든 복제본이 손실되거나 복구할 수 없게 될 수 있습니다. 

* 정책 기반(예약됨) Azure NetApp Files 백업을 사용하려면 스냅샷 정책을 구성하고 사용하도록 설정해야 합니다. [Azure NetApp Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)를 참조하세요.   
    백업해야 하는 볼륨에는 스냅샷을 만들기 위해 구성된 스냅샷 정책이 필요합니다. 구성된 백업 수는 Azure Storage에 저장됩니다. 

* 문제가 발생하고(예: 볼륨에 충분한 공간이 남아 있지 않음) 스냅샷 정책으로 인해 새 스냅샷 만들기가 중지되면 백업 기능에 백업할 새 스냅샷이 없습니다. 

* 지역 간 복제 설정에서 Azure NetApp Files 백업은 원본 볼륨에서만 구성할 수 있습니다. 지역 간 복제 *대상* 볼륨에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)
