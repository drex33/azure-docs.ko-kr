---
title: Azure NetApp Files 볼륨 | 백업 기능 사용 안 함 Microsoft Docs
description: 더 이상 백업 보호가 필요하지 않은 볼륨에 대해 백업 기능을 사용하지 않도록 설정하는 방법을 설명합니다.
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
ms.openlocfilehash: 1555a1c42f6365202787750f149b345ccd0cd55c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224146"
---
# <a name="disable-backup-functionality-for-a-volume"></a>볼륨에 대한 백업 기능 사용 안 함 

백업 보호가 더 이상 필요하지 않은 경우 볼륨에 대한 백업 기능을 사용하지 않도록 설정할 수 있습니다. 

> [!IMPORTANT]
> 볼륨에 대한 백업을 사용하지 않도록 하면 해당 볼륨에 대한 Azure Storage에 저장된 모든 백업이 삭제됩니다.

볼륨이 삭제되었지만 볼륨을 삭제하기 전에 백업 정책을 사용하지 않도록 설정하지 않은 경우 볼륨과 관련된 모든 백업이 Azure Storage에 유지되고 연결된 NetApp 계정 아래에 나열됩니다. 

## <a name="steps"></a>단계

1. **볼륨을** 선택합니다.
2. 백업 기능을 사용하지 않도록 설정할 특정 볼륨을 선택합니다.
3. **구성** 을 선택합니다.
4. 백업 구성 페이지에서 **사용** 설정을 **끄기로 전환합니다.** 확인할 볼륨 이름을 입력하고 **확인을** 클릭합니다.

    ![백업을 사용하지 않도록 설정하여 백업 구성 창으로 복원을 보여주는 스크린샷.](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)

