---
title: Azure NetApp Files 볼륨의 백업 검색 | Microsoft Docs
description: 볼륨 수준 및 NetApp 계정 수준에서 Azure NetApp Files 볼륨의 백업을 표시하고 검색하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/27/2021
ms.author: b-hchen
ms.openlocfilehash: 7a31218440dd51ddf365355e7953f7f455c37ae1
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486260"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨의 백업 검색

볼륨 수준 또는 NetApp 계정 수준에서 백업을 표시하고 특정 백업을 검색할 수 있습니다.

스냅샷에 사용되는 이름은 스냅샷이 백업될 때 유지됩니다. 스냅샷 이름에는 , 또는 접두사 `daily` 가 포함됩니다. `weekly` `monthly` 스냅샷을 만들 때의 타임스탬프도 포함됩니다. 백업 기능을 사용하는 첫 번째 스냅샷을 *기준 스냅샷이라고* 하며 이름에는 접두사 가 포함됩니다. `snapmirror`

볼륨이 삭제되면 해당 백업은 계속 유지됩니다. 백업은 연결된 NetApp 계정의 백업 섹션 아래에 나열됩니다. 이 목록에는 해당 지역의 구독 내 모든 백업(NetApp 계정 간)이 포함됩니다. 동일한 구독에 있는 다른 NetApp 계정의 볼륨에 백업을 복원하는 데 사용할 수 있습니다.

## <a name="search-backups-at-volume-level"></a>볼륨 수준에서 백업 검색    

볼륨 수준에서 백업을 표시하고 검색할 수 있습니다.

1. **볼륨을** 선택합니다.

2. **백업으로** 이동하여 볼륨에 대한 백업을 표시합니다.   
    **유형** 열에는 *예약된* 백업(정책 기반) 또는 *수동* 백업에서 백업을 생성하는지 여부가 표시됩니다.

3. 백업 **검색** 상자에 검색할 백업 이름을 입력합니다.  

    부분 검색이 지원됩니다. 전체 백업 이름을 지정할 필요는 없습니다. 검색은 검색 문자열을 기반으로 백업을 필터링합니다.

    ![볼륨에 대한 백업 목록을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>NetApp 계정 수준에서 백업 검색 

구독 지역에 있는 모든 NetApp 계정에서 구독과 연결된 백업을 표시하고 검색할 수 있습니다.   

1. **NetApp 계정 을** 선택합니다.

2. 백업 으로 **이동합니다.**
    **유형** 열에는 *예약된* 백업(정책 기반) 또는 *수동* 백업에서 백업을 생성하는지 여부가 표시됩니다. 

3. 백업 **검색** 상자에 검색할 백업 이름을 입력합니다.

    * 백업 목록에는 구독의 모든 NetApp 계정에 속하는 모든 볼륨(활성 및 삭제)의 모든 백업이 포함됩니다.

    * 백업 목록을 사용하여 백업을 새 볼륨으로 복원하고 백업을 삭제할 수도 있습니다. 백업 복원 워크플로는 볼륨 수준 [백업 복원과](backup-restore-new-volume.md)동일합니다. 백업을 삭제하는 워크플로는 볼륨 [수준 백업 삭제와](backup-delete.md)동일합니다.

    * 수동 백업 명명 규칙에 제안된 모범 사례는 [백업이](backup-configure-manual.md) 속한 볼륨을 식별하는 데 도움이 됩니다. 이 방법은 백업이 삭제된 볼륨에 속하는 경우(백업이 보존된 경우) 특히 유용합니다.

    ![NetApp 계정에 대한 백업 목록을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)
