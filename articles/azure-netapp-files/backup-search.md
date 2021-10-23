---
title: Azure NetApp Files 볼륨의 백업 검색 | Microsoft Docs
description: 볼륨 수준 및 NetApp 계정 수준에서 Azure NetApp Files 볼륨의 백업을 표시 하 고 검색 하는 방법을 설명 합니다.
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
ms.topic: how-to
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 0ed10fbe492a49f61a989e9159bed1cf4b60e513
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256308"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨의 백업 검색

백업을 표시 하 고 볼륨 수준 또는 NetApp 계정 수준에서 특정 백업을 검색할 수 있습니다.

스냅숏에 사용 되는 이름은 스냅숏이 백업 될 때 유지 됩니다. 스냅숏 이름에는 접두사 `daily` , 또는가 포함 됩니다 `weekly` `monthly` . 또한 스냅숏을 만들 때의 타임 스탬프를 포함 합니다. 백업 기능을 사용 하는 경우 첫 번째 스냅숏을 *기준 스냅숏* 이라고 하며, 이름에는 접두사가 포함 됩니다 `snapmirror` .

볼륨을 삭제 해도 해당 백업은 계속 유지 됩니다. 백업은 연결 된 NetApp 계정 아래의 백업 섹션에 나열 됩니다. 이 목록에는 해당 지역의 구독 내 모든 백업 (NetApp 계정에서)이 포함 됩니다. 동일한 구독에서 다른 NetApp 계정에 있는 볼륨으로 백업을 복원 하는 데 사용할 수 있습니다.

## <a name="search-backups-at-volume-level"></a>볼륨 수준에서 백업 검색    

볼륨 수준에서 백업을 표시 하 고 검색할 수 있습니다.

1. **볼륨** 을 선택 합니다.

2. **백업** 으로 이동 하 여 볼륨에 대 한 백업을 표시 합니다.   
    **유형** 열은 백업이 *예약* 된 (정책 기반) 또는 *수동* 백업에 의해 생성 되는지 여부를 표시 합니다.

3. 백업 **검색** 상자에 검색할 백업 이름을 입력 합니다.  

    부분 검색을 지원 합니다. 전체 백업 이름을 지정할 필요는 없습니다. 검색은 검색 문자열에 따라 백업을 필터링 합니다.

    ![볼륨에 대 한 백업 목록을 보여 주는 스크린샷](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>NetApp 계정 수준에서 백업 검색 

구독 지역의 모든 NetApp 계정에서 구독과 연결 된 백업을 표시 하 고 검색할 수 있습니다.   

1. **Netapp 계정** 을 선택 합니다.

2. **백업** 으로 이동 합니다.
    **유형** 열은 백업이 *예약* 된 (정책 기반) 또는 *수동* 백업에 의해 생성 되는지 여부를 표시 합니다. 

3. 백업 **검색** 상자에 검색할 백업 이름을 입력 합니다.

    * 백업 목록에는 구독의 모든 NetApp 계정에 속하는 모든 볼륨 (활성 및 삭제 됨)의 모든 백업이 포함 됩니다.

    * 백업 목록은 새 볼륨에 백업을 복원 하는 데 사용할 수 있으며 백업을 삭제 하는 데에도 사용할 수 있습니다. 백업 복원 워크플로는 [볼륨 수준 백업 복원과](backup-restore-new-volume.md)동일 합니다. 백업을 삭제 하는 워크플로는 [볼륨 수준 백업 삭제](backup-delete.md)와 동일 합니다.

    * [수동 백업](backup-configure-manual.md) 명명 규칙에 권장 되는 모범 사례는 백업이 속한 볼륨을 식별 하는 데 도움이 됩니다. 이 방법은 백업이 삭제 된 볼륨 (백업이 보존 된 상태)에 속하는 경우에 특히 유용 합니다.

    ![NetApp 계정에 대 한 백업 목록을 보여 주는 스크린샷](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)
