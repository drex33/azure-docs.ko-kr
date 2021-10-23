---
title: Azure NetApp Files 볼륨의 백업 삭제 | Microsoft Docs
description: 볼륨에 대해 더 이상 유지 하지 않아도 되는 개별 백업을 삭제 하는 방법을 설명 합니다.
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
ms.openlocfilehash: e1d59d336d331a41bf5f82e650d80d7843ab3977
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250581"
---
# <a name="delete-backups-of-a-volume"></a>볼륨의 백업 삭제 

볼륨에 대해 더 이상 유지 하지 않아도 되는 개별 백업을 삭제할 수 있습니다. 백업을 삭제 하면 Azure Storage 계정의 연결 된 개체가 삭제 되어 공간이 절약 됩니다.  

최신 백업을 삭제할 수 없습니다.  

## <a name="steps"></a>단계

1. **볼륨** 을 선택 합니다.
2. **백업** 으로 이동 합니다.
3. 백업 목록에서 삭제할 백업을 선택 합니다. 백업 오른쪽에 있는 세 개의 점 ()을 클릭 `…` 한 다음 작업 메뉴에서 **삭제** 를 클릭 합니다.

    ![백업에 대 한 삭제 메뉴를 보여 주는 스크린샷](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)
