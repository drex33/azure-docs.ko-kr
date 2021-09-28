---
title: Azure NetApp Files | 백업 정책 관리 Microsoft Docs
description: Azure NetApp Files 볼륨에 대한 백업 정책을 수정하거나 일시 중단하는 방법을 설명합니다.
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
ms.openlocfilehash: c3f440899e53125b6a7e6ef7986d74e4f1d0977d
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095808"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Azure NetApp Files 백업 정책 관리 

Azure NetApp Files [정책 기반 백업이](backup-configure-policy-based.md) 볼륨을 자동으로 백업하려면 스냅샷 정책 구성이 필요합니다. 백업 정책을 사용하여 Azure NetApp Files 백업을 설정한 후 필요에 따라 백업 정책을 수정하거나 일시 중단할 수 있습니다.  

수동 백업은 백업 정책 변경의 영향을 받지 않습니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정   

필요에 따라 기존 Azure NetApp Files 백업 정책을 수정하여 Azure NetApp Files 볼륨에 대한 적절한 백업 적용 범위를 유지할 수 있습니다.  예를 들어 서비스에서 보호되는 보존된 백업 수를 변경해야 하는 경우 볼륨에 대한 Azure NetApp Files 백업 정책을 수정하여 유지할 복원 수를 수정할 수 있습니다. 

백업 정책 설정을 수정하려면 다음을 수행합니다.   

1. 백업 으로 **이동합니다.**  

2. **백업 정책을** 선택하고 백업 정책의 오른쪽에 있는 세 개의 점()을 클릭한 다음 `…` **편집을** 클릭합니다.

    ![백업 정책의 상황에 맞는 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/backup-policies-edit.png)

3. 백업 정책 수정 창에서 매일, 매주 및 매월 백업에 유지할 복원 수를 업데이트합니다. 백업 정책 이름을 입력하여 작업을 확인합니다. **저장** 을 클릭합니다.  

    ![백업 정책 수정 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > 백업을 사용하도록 설정하여 예약된 빈도에 적용한 후에는 백업 보존 횟수를 로 변경할 수 `0` 없습니다. 백업 정책에는 최소 보존 수가 `1` 필요합니다. 자세한 내용은 [Azure NetApp Files 리소스 제한을](azure-netapp-files-resource-limits.md) 참조하세요.  

## <a name="suspend-a-backup-policy"></a>백업 정책 일시 중단  

연결된 볼륨에 대해 새 백업 작업을 수행하지 않도록 백업 정책을 일시 중단할 수 있습니다. 이 작업을 사용하면 기존 백업을 유지 관리해야 하지만 버전 관리로 인해 사용 중지되지 않는 경우 일시적으로 백업을 일시 중단할 수 있습니다.   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>정책과 연결된 모든 볼륨에 대한 백업 정책 일시 중단

1. 백업 으로 **이동합니다.**

2. **백업 정책을** 선택하고 백업 정책의 오른쪽에 있는 세 개의 점()을 클릭한 다음 `…` **편집을** 클릭합니다. 

3. **정책 상태를** 사용 **안** 함으로 전환하고 확인할 정책 이름을 입력한 다음 **저장을** 클릭합니다. 

    ![정책 상태가 비활성화된 백업 정책 수정 창을 보여주는 스크린샷.](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>특정 볼륨에 대한 백업 정책 일시 중단 

1. **볼륨으로 이동합니다.** 
2. 백업을 일시 중단하려는 특정 볼륨을 선택합니다.
3. **구성** 을 선택합니다.
4. 백업 구성 페이지에서 **정책 상태를** **일시 중단으로** 전환하고 확인할 볼륨 이름을 입력한 다음 **확인을** 클릭합니다.   

    ![정책 일시 중단 상태가 있는 백업 구성 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



