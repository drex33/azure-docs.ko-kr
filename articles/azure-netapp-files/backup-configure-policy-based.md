---
title: Azure NetApp Files | 대한 정책 기반 백업 구성 Microsoft Docs
description: Azure NetApp Files 볼륨에 대한 정책 기반(예약된) 백업을 구성하는 방법에 대해 설명합니다.
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
ms.date: 10/13/2021
ms.author: b-hchen
ms.openlocfilehash: 9aeb3aaab34a393528f62d9044cb9ac58ab2f425
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133482832"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>Azure NetApp Files 대한 정책 기반 백업 구성 

Azure NetApp Files 백업은 볼륨 수준에서 정책 기반(예약된) 백업 및 *수동(주문형)* 백업을 지원합니다.  동일한 볼륨에서 두 가지 유형의 백업을 모두 사용할 수 있습니다. 구성 프로세스 중에 정책 기반 백업 또는 수동 백업을 수행하려면 Azure NetApp Files 볼륨에 백업 기능을 사용하도록 설정합니다. 

이 문서에서는 정책 기반 백업을 구성하는 방법을 보여줍니다.  수동 백업 구성은 [수동 백업 구성을 참조하세요.](backup-configure-manual.md)  

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공됩니다. **[Azure NetApp Files Backup 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. Azure NetApp Files 백업 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다립니다.

## <a name="about-policy-based-backups"></a>정책 기반 백업 정보  

볼륨에는 정책 기반 백업 기능이 작동하려면 다음 두 가지 유형의 정책이 있어야 합니다.  

* 볼륨에 대한 *스냅샷을* 만드는 시기를 제어하는 스냅샷 정책입니다.
* Azure Storage에 *백업하는* 데 사용할 스냅샷을 제어하는 백업 정책입니다.

스냅샷 정책은 볼륨에서 스냅샷 만들기를 처리합니다. 백업 기능에서 Azure Storage에 스냅샷을 백업하는 데 사용됩니다.   

백업은 장기 실행 작업입니다. 시스템은 기본 워크로드(우선 순위가 더 높음)를 기반으로 백업을 예약하고 백그라운드에서 백업을 실행합니다. 백업 중인 볼륨의 크기에 따라 백업이 백그라운드에서 몇 시간 동안 실행될 수 있습니다. 백업 시작 시간을 선택할 수 있는 옵션은 없습니다. 서비스는 내부 일정 및 최적화 논리에 따라 백업을 수행합니다. 

정책을 할당하면 볼륨의 현재 상태인 기준 스냅샷이 만들어지고 스냅샷이 Azure Storage로 전송됩니다. 기준 스냅샷은 로 시작하는 이름으로 `snapmirror` 만들어집니다. 이 기준 스냅샷은 첫 번째 예약된 백업이 완료되면 자동으로 삭제됩니다(정책에 따라). 백업 정책이 볼륨에 연결된 경우 기준 스냅샷이 전송될 때까지 백업 목록이 비어 있습니다. 백업이 완료되면 볼륨에 대한 백업 목록에 기준 백업 항목이 표시됩니다. 기준 전송 후 목록은 정책에 따라 매일 업데이트됩니다. 빈 백업 목록은 기준 백업이 진행 중임을 나타냅니다. 백업 정책을 할당하기 전에 볼륨에 기존 수동 백업이 이미 있는 경우 기준 스냅샷이 만들어지지 않습니다. 기준 스냅샷은 볼륨에 이전 백업이 없는 경우에만 만들어집니다.

## <a name="configure-and-apply-a-snapshot-policy"></a>스냅샷 정책 구성 및 적용  

스냅샷 정책을 만들고 백업하려는 볼륨에 스냅샷 정책을 연결해야 합니다. 단일 스냅샷 정책을 여러 볼륨에 연결할 수 있습니다. 스냅샷 정책의 변경 내용은 볼륨의 백업 기능에 영향을 줄 수 있습니다. 

1. Azure Portal 로그인하고 **Azure NetApp Files** 으로 이동합니다.    
2. Azure NetApp Files 계정을 선택합니다.   
3. **스냅샷 정책** 을 선택합니다.   

    ![스냅샷 정책 옵션으로 이동하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  **스냅샷 정책 추가를** 클릭합니다.
5.  표시되는 스냅샷 정책 페이지에서 유지할 스냅샷 수와 볼륨에 대한 스냅샷을 만들기 위한 일정을 지정합니다. **Save** 을 클릭합니다.  

    현재 백업 기능은 매일, 매주 및 매월 스냅샷만 백업할 수 있습니다. (시간별 백업은 지원되지 않습니다.)   

    * 일별 스냅샷 구성의 경우 스냅샷을 만들 시간을 지정합니다. 
    * 주간 스냅샷 구성의 경우 스냅샷을 만들 요일 및 시간을 지정합니다. 
    * 월별 스냅샷 구성의 경우 스냅샷을 만들 월 및 시간을 지정합니다. 
    * 각 스냅샷 구성에 대해 유지할 스냅샷 수를 지정합니다.

    예를 들어 매일 백업을 사용하려면 일별 스냅샷 일정 및 스냅샷 수를 사용하여 스냅샷 정책을 구성한 다음 해당 일별 스냅샷 정책을 볼륨에 적용해야 합니다. 스냅샷 정책을 변경하거나 일별 스냅샷 구성을 삭제하면 새 일일 스냅샷이 생성되지 않아 매일 백업이 발생하지 않습니다. 동일한 프로세스와 동작이 매주 및 월별 백업에 적용됩니다.  

    각 스냅샷에 고유한 스냅샷 일정 구성이 있는지 확인합니다. 의도적으로 Azure NetApp Files 최신 백업을 삭제할 수 없게 합니다. 여러 스냅샷이 동일한 시간(예: 동일한 매일 및 매주 일정 구성)을 갖는 경우 Azure NetApp Files 이를 최신 스냅샷으로 간주하고 해당 백업을 삭제할 수 없습니다.  

    다음 예제에서는 일별 스냅샷 정책 구성을 보여 줍니다. 

    ![일별 스냅샷 정책 구성을 보여 주는 스크린샷](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  백업하려는 볼륨에 스냅샷 정책을 적용합니다.  

    1. **볼륨** 페이지로 이동하여 스냅샷 정책을 적용할 볼륨을 마우스 오른쪽 단추로 클릭하고 **편집을** 선택합니다.   
        ![볼륨 편집 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. 편집 창의 **스냅샷 정책** 에서 적용할 정책을 선택합니다. **확인** 을 클릭합니다.   
        ![스냅샷 정책 풀다운이 있는 편집 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책을 사용하면 정기적으로 예약된 간격으로 볼륨을 보호할 수 있습니다.  

백업 정책을 만들고 백업 정책을 백업하려는 볼륨에 연결해야 합니다. 단일 백업 정책을 여러 볼륨에 연결할 수 있습니다. 정책을 사용하지 않도록 하거나 볼륨 수준에서 백업을 사용하지 않도록 하여 백업을 일시적으로 일시 중단할 수 있습니다. 볼륨 수준에서 백업을 완전히 사용하지 않도록 설정하여 Azure Storage에 연결된 모든 데이터를 정리할 수도 있습니다. 백업 정책이 볼륨에 연결되어 있으면 삭제할 수 없습니다.

정책 기반(예약된) 백업을 사용하도록 설정하려면 다음을 수행합니다. 

1. Azure Portal 로그인하고 **Azure NetApp Files** 으로 이동합니다. 
2. Azure NetApp Files 계정을 선택합니다.
3. **백업을** 선택합니다. 

    ![백업 옵션으로 이동하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/backup-navigate.png)

4. **백업 정책** 을 선택합니다.
5. **추가** 를 선택합니다. 
6. 백업 **정책** 페이지에서 백업 정책 이름을 지정합니다.  매일, 매주 및 매월 백업에 유지할 백업 수를 입력합니다. **Save** 을 클릭합니다.

    ![백업 정책 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-policy-window-daily.png)

    * 스냅샷 정책을 연결하지 않고 백업 정책을 구성하고 볼륨에 연결하는 경우 백업이 제대로 작동하지 않습니다. Azure Storage로 전송되는 기준 스냅샷만 있습니다. 
    * 구성하는 각 백업 정책(예: 매일 백업)에 대해 해당 스냅샷 정책 구성(예: 일일 스냅샷)이 있는지 확인합니다.
    * 백업 정책에는 스냅샷 정책에 대한 종속성이 있습니다. 스냅샷 정책을 아직 만들지 않은 경우 백업 정책 창에서 **스냅샷 정책 만들기** 확인란을 선택하여 두 정책을 동시에 구성할 수 있습니다.   

        ![스냅샷 정책이 선택된 백업 정책 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>유효한 구성의 예

다음 예제 구성에서는 볼륨에서 5개의 최신 일별 스냅샷, 4개의 최신 주별 스냅샷 및 3개의 최신 월별 스냅샷을 사용하여 볼륨에서 데이터 보호 정책을 구성하는 방법을 보여 줍니다. 이 구성은 15개의 최신 일일 스냅샷, 6개의 최신 주별 스냅샷 및 4개의 최신 월별 스냅샷을 백업합니다.

* 스냅샷 정책:   
    매일: `Number of Snapshots to Keep = 5`   
    주간: `Number of Snapshots to Keep = 4`   
    월별: `Number of Snapshots to Keep = 3`
* 백업 정책:   
    매일: `Daily Backups to Keep = 15`   
    주간: `Weekly Backups to Keep = 6`   
    월별: `Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>잘못된 구성의 예

다음 예제 구성에는 매일 백업에 대해 구성된 백업 정책이 있지만 스냅샷 정책에는 해당 구성이 없습니다. 따라서 백업 정책에 의해 백업할 일별 스냅샷이 만들어지지 않습니다. 이 구성은 매주 및 매월 스냅샷만 백업합니다.

* 스냅샷 정책:   
    주간: `Number of Snapshots to Keep = 4`   
    월별: `Number of Snapshots to Keep = 3`   
* 백업 정책:   
    매일: `Daily Backups to Keep = 15`   
    주간: `Weekly Backups to Keep = 6`   
    월별: `Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>볼륨에 백업 기능을 사용하도록 설정하고 백업 정책 할당

백업(정책 기반 또는 수동)을 수행하려면 모든 Azure NetApp Files 볼륨에서 백업 기능을 사용하도록 설정해야 합니다. 

백업 기능을 사용하도록 설정한 후 정책 기반 백업이 적용되도록 볼륨에 백업 정책을 할당해야 합니다. 수동 백업의 경우 백업 정책은 선택 사항입니다.

볼륨에 백업 기능을 사용하도록 설정하려면 다음을 수행합니다.  

1. **볼륨으로** 이동하여 백업을 사용하도록 설정할 볼륨을 선택합니다.
2. **구성** 을 선택합니다.
3. 백업 구성 페이지에서 **사용** 설정을 **켜기로 전환합니다.**
4. **스냅샷 정책** 드롭다운 메뉴에서 볼륨에 사용할 스냅샷 정책을 할당합니다. 
5. 백업 **정책** 드롭다운 메뉴에서 볼륨에 사용할 백업 정책을 할당합니다. **확인** 을 클릭합니다.

    자격 증명 모음 정보가 미리 채워져 있습니다.  

    ![백업 구성 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-configure-window.png)


## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)


