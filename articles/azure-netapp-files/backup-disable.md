---
title: Azure NetApp Files 볼륨의 백업 기능 사용 안 함 | Microsoft Docs
description: 더 이상 백업 보호가 필요 하지 않은 볼륨의 백업 기능을 사용 하지 않도록 설정 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 0ce5a3058381bce6843c31e712164624c9663d9e
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480474"
---
# <a name="disable-backup-functionality-for-a-volume"></a>볼륨에 대 한 백업 기능 사용 안 함 

백업 보호가 더 이상 필요 하지 않은 경우 볼륨의 백업 기능을 사용 하지 않도록 설정할 수 있습니다. 

> [!IMPORTANT]
> 볼륨에 대 한 백업을 사용 하지 않도록 설정 하면 해당 볼륨의 Azure 저장소에 저장 된 모든 백업이 삭제 됩니다.

볼륨을 삭제 하기 전에 볼륨을 삭제 했지만 백업 정책을 사용 하지 않도록 설정한 경우에는 볼륨과 관련 된 모든 백업이 Azure storage에 유지 되 고 연결 된 NetApp 계정 아래에 나열 됩니다. 

## <a name="steps"></a>단계

1. **볼륨** 을 선택 합니다.
2. 사용 하지 않도록 설정할 백업 기능을 가진 특정 볼륨을 선택 합니다.
3. **구성** 을 선택합니다.
4. 백업 구성 페이지에서 **사용** 설정을 **해제** 로 전환 합니다. 확인할 볼륨 이름을 입력 하 고 **확인** 을 클릭 합니다.

    ![백업을 사용 하지 않도록 설정 하 여 백업 구성 창으로 복원 하는 것을 보여 주는 스크린샷](../media/azure-netapp-files/backup-configure-backups-disable.png)

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

