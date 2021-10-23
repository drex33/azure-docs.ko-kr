---
title: Azure NetApp Files 백업에 대 한 요구 사항 및 고려 사항 | Microsoft Docs
description: Azure NetApp Files 백업을 사용 하기 전에 알아 두어야 하는 요구 사항 및 고려 사항을 설명 합니다.
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
ms.openlocfilehash: 5e622f86b7b0b9d757dc32089db86c7bfca7fe63
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239755"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항 

이 문서에서는 Azure NetApp Files 백업을 사용 하기 전에 알아야 하는 요구 사항 및 고려 사항을 설명 합니다.

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

Azure NetApp Files 백업을 사용 하기 전에 몇 가지 요구 사항 및 고려 사항을 알고 있어야 합니다. 

* Azure NetApp Files 백업은 Azure NetApp Files 구독과 연결 된 지역에서 사용할 수 있습니다. 지역에서 Azure NetApp Files 백업은 동일한 지역에 있는 Azure NetApp Files 볼륨만 보호할 수 있습니다. 예를 들어 미국 서 부 2에 위치한 볼륨에 대 한 미국 서 부 2의 서비스에서 만든 백업은 미국 서 부 2에 있는 Azure storage로 전송 됩니다. Azure NetApp Files는 다른 지역으로의 백업 또는 백업 복제를 지원 하지 않습니다.  

* 백업이 실제로 완료 된 후 백업을 표시 하는 데 최대 5 분이 걸릴 수 있습니다.

* 현재 Azure NetApp Files 백업 기능은 연결 된 스냅숏 정책에서 만든 매일, 매주 및 매월 로컬 스냅숏을 Azure storage에 백업 하도록 지원 합니다. 매시간 백업은 현재 지원 되지 않습니다.

* Azure NetApp Files 백업에서는 LRS ( [로컬 중복 Storage](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) ) 저장소만 지원 되는 아래에 나열 된 영역을 제외 하 고는 지역에서 3 개의 Azure 가용성 영역에 걸쳐 동기적으로 데이터를 복제 하는 ZRS ( [영역 중복 저장소](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) ) 계정을 사용 합니다.   

    * 미국 서부   

    LRS는 서버 랙 및 드라이브 오류 로부터 복구할 수 있습니다. 그러나 데이터 센터 내에서 화재 또는 홍수와 같은 재해가 발생 하면 LRS를 사용 하는 저장소 계정의 모든 복제본이 손실 되거나 복구할 수 없을 수 있습니다. 

* 정책 기반(예약됨) Azure NetApp Files 백업을 사용하려면 스냅샷 정책을 구성하고 사용하도록 설정해야 합니다. [Azure NetApp Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)를 참조하세요.   
    백업 해야 하는 볼륨에는 스냅숏을 만들기 위한 구성 된 스냅숏 정책이 필요 합니다. 구성 된 백업 수는 Azure 저장소에 저장 됩니다. 

* 문제가 발생 하 여 (예: 볼륨에 남아 있는 공간이 부족 한 경우) 스냅숏 정책이 새 스냅숏 만들기를 중지 하도록 하면 백업 기능에 백업할 새 스냅숏이 없습니다. 

* 지역 간 복제 설정에서 Azure NetApp Files 백업은 원본 볼륨에 대해서만 구성할 수 있습니다. 지역 간 복제 *대상* 볼륨에서는 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)
* [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)
