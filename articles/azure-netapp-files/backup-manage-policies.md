---
title: Azure NetApp Files에 대 한 백업 정책 관리 | Microsoft Docs
description: Azure NetApp Files 볼륨에 대 한 백업 정책을 수정 하거나 일시 중단 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 8431d9db26e14e9ddf1c40e377744b3e084dcb68
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485652"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 백업 정책 관리 

스냅숏 정책 구성은 Azure NetApp Files [정책 기반 백업](backup-configure-policy-based.md) 에서 볼륨을 자동으로 백업 하는 데 필요 합니다. 백업 정책을 사용 하 여 Azure NetApp Files 백업을 설정한 후 필요에 따라 백업 정책을 수정 하거나 일시 중단할 수 있습니다.  

수동 백업은 백업 정책 변경의 영향을 받지 않습니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정   

필요에 따라 기존 Azure NetApp Files 백업 정책을 수정 하 여 Azure NetApp Files 볼륨에 대 한 적절 한 백업 검사가 수행 되도록 할 수 있습니다.  예를 들어 서비스에 의해 보호 되는 보존 된 백업 수를 변경 해야 하는 경우 볼륨에 대 한 Azure NetApp Files 백업 정책을 수정 하 여 유지할 복원 수를 수정할 수 있습니다. 

백업 정책 설정을 수정 하려면:   

1. **백업** 으로 이동 합니다.  

2. **백업** 정책을 선택 하 고 백업 정책 오른쪽에 있는 세 개의 점 ()을 클릭 `…` 한 다음 **편집** 을 클릭 합니다.

    ![백업 정책에 대 한 상황에 맞는 메뉴를 보여 주는 스크린샷](../media/azure-netapp-files/backup-policies-edit.png)

3. 백업 정책 수정 창에서 매일, 매주, 매월 백업 하는 데 사용할 복원 수를 업데이트 합니다. 백업 정책 이름을 입력 하 여 작업을 확인 합니다. **Save** 을 클릭합니다.  

    ![백업 정책 수정 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > 백업을 사용 하도록 설정 하 고 예약 된 빈도에 적용 한 후에는 백업 보존 횟수를로 변경할 수 없습니다 `0` . 백업 정책에는 최소 수의 `1` 보존이 필요 합니다. 자세한 내용은 [Azure NetApp Files에 대 한 리소스 제한](azure-netapp-files-resource-limits.md) 을 참조 하세요.  

## <a name="suspend-a-backup-policy"></a>백업 정책 일시 중단  

백업 정책은 연결 된 볼륨에 대해 새로운 백업 작업을 수행 하지 않도록 일시 중단 될 수 있습니다. 이 작업을 사용 하면 기존 백업을 유지 관리 해야 하지만 버전 관리로 인해 사용이 중지 되지 않는 경우 백업을 일시적으로 중단할 수 있습니다.   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>정책과 연결 된 모든 볼륨에 대 한 백업 정책 일시 중단

1. **백업** 으로 이동 합니다.

2. **백업** 정책을 선택 하 고 백업 정책 오른쪽에 있는 세 개의 점 ()을 클릭 `…` 한 다음 **편집** 을 클릭 합니다. 

3. **정책 상태** 를 **사용 안 함** 으로 설정 하 고, 확인할 정책 이름을 입력 하 고, **저장** 을 클릭 합니다. 

    ![정책 상태가 비활성화 된 백업 정책 수정 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>특정 볼륨에 대 한 백업 정책 일시 중단 

1. **볼륨** 으로 이동 합니다. 
2. 백업을 일시 중단 하려는 특정 볼륨을 선택 합니다.
3. **구성** 을 선택합니다.
4. 백업 구성 페이지에서 **정책 상태** 를 **일시 중지** 로 전환 하 고, 확인할 볼륨 이름을 입력 하 고 **확인** 을 클릭 합니다.   

    ![일시 중단 정책 상태의 백업 구성 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)



