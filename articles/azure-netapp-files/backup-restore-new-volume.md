---
title: 새 Azure NetApp Files 볼륨 | 백업 복원 Microsoft Docs
description: 백업을 새 볼륨으로 복원하는 방법을 설명합니다.
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
ms.openlocfilehash: 1a0b2e59f2d11caf1db9985cfba937e2df0f44c7
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095803"
---
# <a name="restore-a-backup-to-a-new-volume"></a>새 볼륨으로 백업 복원

백업을 복원하면 동일한 프로토콜 형식의 새 볼륨이 만들어집니다. 이 문서에서는 복원 작업에 대해 설명합니다. 

## <a name="considerations"></a>고려 사항

* 복원 작업에서 만든 새 볼륨은 복원이 완료될 때까지 탑재할 수 없습니다. 

* 기준 백업이 없는 경우 복원 작업을 트리거해야 합니다. 그렇지 않으면 복원으로 데이터가 백업되는 Azure Blob 계정의 부하가 증가할 수 있습니다. 

## <a name="steps"></a>단계

1. **볼륨을** 선택합니다. 백업 으로 **이동합니다.**

    > [!NOTE]
    > 볼륨이 삭제되었지만 볼륨을 삭제하기 전에 백업 정책을 사용하지 않도록 설정하지 않은 경우 볼륨과 관련된 모든 백업이 Azure Storage에 유지되며 연결된 NetApp 계정에서 찾을 수 있습니다.  [NetApp 계정 수준에서 백업 검색을](backup-search.md#search-backups-at-netapp-account-level)참조하세요.


2. 백업 목록에서 복원할 백업을 선택합니다. 백업 오른쪽에 있는 세 개의 `…` 점()을 클릭한 다음 작업 메뉴에서 새 **볼륨으로 복원을** 클릭합니다.   

    ![백업을 새 볼륨으로 복원하는 옵션을 보여 주는 스크린샷](../media/azure-netapp-files/backup-restore-new-volume.png)

3. 표시되는 볼륨 만들기 페이지에서 해당하는 페이지의 필드에 대한 정보를 제공하고 검토 + **만들기를** 클릭하여 백업을 새 볼륨으로 복원하기 시작합니다.   

    * **프로토콜** 필드는 원래 볼륨에서 미리 채워져 있으며 변경할 수 없습니다.    
        그러나 NetApp 계정 수준의 백업 목록에서 볼륨을 복원하는 경우 프로토콜 필드를 지정해야 합니다. 프로토콜 필드는 원래 볼륨의 프로토콜과 일치해야 합니다. 그렇지 않으면 다음 오류로 복원 작업이 실패합니다.  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * **할당량** 값은 복원이 트리거되는 백업 크기(최소 100 GiB)보다 크거나 같아야 합니다.

    * 백업이 복원되는 **용량 풀에는** 복원된 새 볼륨을 호스트하는 데 사용되지 않는 용량이 충분히 있어야 합니다. 그렇지 않으면 복원 작업이 실패합니다.   

    ![볼륨 만들기 페이지를 보여 주는 스크린샷.](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



