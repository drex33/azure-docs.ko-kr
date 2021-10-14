---
title: Azure NetApp Files에 대 한 정책 기반 백업 구성 | Microsoft Docs
description: Azure NetApp Files 볼륨에 대해 정책 기반 (예약 됨) 백업을 구성 하는 방법을 설명 합니다.
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
ms.date: 10/13/2021
ms.author: b-juche
ms.openlocfilehash: 06d7e334205de142b60f73d1e98f0c4167c65b7d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993153"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 정책 기반 백업 구성 

Azure NetApp Files 백업에서는 볼륨 수준에서 *정책 기반* (예약 됨) 백업 및 *수동* (주문형) 백업을 지원 합니다. 동일한 볼륨에서 두 가지 유형의 백업을 모두 사용할 수 있습니다. 구성 프로세스 중에는 정책 기반 백업 또는 수동 백업을 수행할 수 있기 전에 Azure NetApp Files 볼륨에 대 한 백업 기능을 사용 하도록 설정 합니다. 

이 문서에서는 정책 기반 백업을 구성 하는 방법을 보여 줍니다.  수동 백업 구성에 대 한 자세한 내용은 [수동 백업 구성](backup-configure-manual.md)을 참조 하세요.  

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공 됩니다. **[Azure NetApp Files 백업 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. Azure NetApp Files 팀에서 Azure NetApp Files 백업 기능을 사용 하기 전에 공식 확인 전자 메일을 기다리십시오.

## <a name="about-policy-based-backups"></a>정책 기반 백업 정보  

볼륨에는 정책 기반 백업 기능이 작동 하려면 다음과 같은 두 가지 유형의 정책이 있어야 합니다.  

* 볼륨에 대 한 스냅숏이 생성 되는 시기를 제어 하는 *스냅숏 정책* 입니다.
* Azure storage에 백업 하는 데 사용할 스냅숏을 제어 하는 *백업 정책* 입니다.

스냅숏 정책은 볼륨의 스냅숏을 만드는 과정을 처리 합니다. 백업 기능에서 Azure storage에 스냅숏을 백업 하는 데 사용 됩니다.   

백업은 장기 실행 작업입니다. 시스템은 기본 작업 (높은 우선 순위를 지정)을 기반으로 백업을 예약 하 고 백그라운드에서 백업을 실행 합니다. 백업 중인 볼륨의 크기에 따라 몇 시간 동안 백그라운드에서 백업을 실행할 수 있습니다. 백업에 대 한 시작 시간을 선택할 수 있는 옵션은 없습니다. 서비스는 내부 예약 및 최적화 논리에 따라 백업을 수행 합니다. 

정책을 할당 하면 볼륨의 현재 상태에 해당 하는 기본 스냅숏이 만들어지고 스냅숏이 Azure storage로 전송 됩니다. 로 시작 하는 이름으로 기본 스냅숏이 생성 됩니다 `snapmirror` . 이 기본 스냅숏은 정책을 기반으로 하 여 첫 번째 예약 된 백업이 완료 되 면 자동으로 삭제 됩니다. 백업 정책이 볼륨에 연결 된 경우 기본 스냅숏이 전송 될 때까지 백업 목록이 비어 있게 됩니다. 백업이 완료 되 면 볼륨의 백업 목록에 기준 백업 항목이 표시 됩니다. 기준 전송 후에는 정책을 기준으로 매일 업데이트 됩니다. 빈 백업 목록은 기준 백업이 진행 중임을 나타냅니다. 백업 정책을 할당 하기 전에 볼륨에 기존 수동 백업이 이미 있는 경우 기준 스냅숏은 생성 되지 않습니다. 기준 스냅숏은 볼륨에 이전 백업이 없는 경우에만 만들어집니다.

## <a name="configure-and-apply-a-snapshot-policy"></a>스냅숏 정책 구성 및 적용  

스냅숏 정책을 만들고 백업 하려는 볼륨에 스냅숏 정책을 연결 해야 합니다. 단일 스냅숏 정책을 여러 볼륨에 연결할 수 있습니다. 스냅숏 정책의 변경 내용은 볼륨의 백업 기능에 영향을 줄 수 있습니다. 

1. Azure Portal에 로그인 하 여 **Azure NetApp Files** 으로 이동 합니다.    
2. Azure NetApp Files 계정을 선택 합니다.   
3. **스냅숏 정책** 을 선택 합니다.   

    ![스냅숏 정책 옵션으로 이동 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  **스냅숏 정책 추가** 를 클릭 합니다.
5.  표시 되는 스냅숏 정책 페이지에서 보관할 스냅숏 수와 볼륨에 대 한 스냅숏 만들기 일정을 지정 합니다. **저장** 을 클릭합니다.  

    현재 백업 기능은 매일, 매주 및 매월 스냅숏으로 백업할 수 있습니다. (매시간 백업은 지원 되지 않습니다.)   

    * 일별 스냅숏 구성의 경우 스냅숏을 만들 시간을 지정 합니다. 
    * 주별 스냅숏 구성의 경우 스냅숏을 만들 요일 및 시간을 지정 합니다. 
    * 월별 스냅숏 구성의 경우 스냅숏을 만들 날짜 및 시간을 지정 합니다. 
    * 각 스냅숏 구성에 대해 유지 하려는 스냅숏의 수를 지정 합니다.

    예를 들어 매일 백업을 수행 하려는 경우 일별 스냅숏 일정 및 스냅숏 수를 사용 하 여 스냅숏 정책을 구성 하 고 해당 일별 스냅숏 정책을 볼륨에 적용 해야 합니다. 스냅숏 정책을 변경 하거나 일별 스냅숏 구성을 삭제 하면 새로운 매일 스냅숏이 생성 되지 않으므로 매일 백업이 수행 되지 않습니다. 매주 및 매월 백업에 적용 되는 프로세스와 동작이 동일 합니다.  

    각 스냅숏에 고유한 스냅숏 일정 구성이 있는지 확인 합니다. 기본적으로 Azure NetApp Files는 최신 백업을 삭제 하지 못하게 합니다. 여러 스냅숏의 시간이 동일한 경우 (예: 동일한 일별 및 주간 일정 구성) Azure NetApp Files 최신 스냅숏으로 간주 하 고 해당 백업을 삭제 하지 못합니다.  

    다음 예제에서는 일별 스냅숏 정책 구성을 보여 줍니다. 

    ![일별 스냅숏 정책 구성을 보여 주는 스크린샷](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  백업 하려는 볼륨에 스냅숏 정책을 적용 합니다.  

    1. **볼륨** 페이지로 이동 하 여 스냅숏 정책을 적용 하려는 볼륨을 마우스 오른쪽 단추로 클릭 하 고 **편집** 을 선택 합니다.   
        ![볼륨 편집 메뉴를 표시 하는 스크린샷](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. 편집 창의 **스냅숏 정책** 에서 적용할 정책을 선택 합니다. **확인** 을 클릭합니다.   
        ![스냅숏 정책 풀 다운을 포함 하는 편집 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 정기적으로 예약 된 간격으로 볼륨을 보호할 수 있도록 합니다.  

백업 정책을 만들고 백업 하려는 볼륨에 백업 정책을 연결 해야 합니다. 단일 백업 정책을 여러 볼륨에 연결할 수 있습니다. 정책을 사용 하지 않도록 설정 하거나 볼륨 수준에서 백업을 사용 하지 않도록 설정 하 여 백업을 일시적으로 중단할 수 있습니다. 볼륨 수준에서 백업을 완전히 사용 하지 않도록 설정 하 여 Azure storage에 연결 된 모든 데이터를 정리할 수도 있습니다. 볼륨에 연결 되어 있는 경우에는 백업 정책을 삭제할 수 없습니다.

정책 기반 (예약 된) 백업을 사용 하도록 설정 하려면: 

1. Azure Portal에 로그인 하 여 **Azure NetApp Files** 으로 이동 합니다. 
2. Azure NetApp Files 계정을 선택 합니다.
3. **백업** 을 선택 합니다. 

    ![백업 옵션을 탐색 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/backup-navigate.png)

4. **백업 정책** 을 선택 합니다.
5. **추가** 를 선택합니다. 
6. **백업 정책** 페이지에서 백업 정책 이름을 지정 합니다.  매일, 매주 및 매월 백업을 위해 유지할 백업 수를 입력 합니다. **저장** 을 클릭합니다.

    ![백업 정책 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-policy-window-daily.png)

    * 스냅숏 정책을 연결 하지 않고 백업 정책을 구성 하 고 볼륨에 연결 하면 백업이 제대로 작동 하지 않습니다. Azure 저장소로 전송 되는 기준 스냅숏은 있습니다. 
    * 구성 하는 각 백업 정책 (예: 일별 백업)에 대해 해당 하는 스냅숏 정책 구성 (예: 일별 스냅숏)이 있는지 확인 합니다.
    * 백업 정책이 스냅숏 정책에 종속 되어 있습니다. 아직 스냅숏 정책을 만들지 않은 경우에는 백업 정책 창에서 **스냅숏 정책 만들기** 확인란을 선택 하 여 두 정책을 동시에 구성할 수 있습니다.   

        ![스냅숏 정책이 선택 된 백업 정책 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>유효한 구성의 예

다음 예제 구성에서는 볼륨에서 5 개의 최근 일별 스냅숏, 4 개의 최근 주간 스냅숏 및 3 개의 최근 월간 스냅숏을 사용 하 여 볼륨에서 데이터 보호 정책을 구성 하는 방법을 보여 줍니다. 이 구성을 통해 15 개의 최신 일별 스냅숏, 6 개의 최근 주간 스냅숏 및 4 개의 최근 월별 스냅숏이 백업 됩니다.

* 스냅숏 정책:   
    매일 `Number of Snapshots to Keep = 5`   
    단위의 `Number of Snapshots to Keep = 4`   
    월별 `Number of Snapshots to Keep = 3`
* 백업 정책:   
    매일 `Daily Backups to Keep = 15`   
    단위의 `Weekly Backups to Keep = 6`   
    월별 `Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>잘못 된 구성의 예

다음 예제 구성에는 매일 백업에 대해 구성 된 백업 정책이 있지만 스냅숏 정책에 해당 구성이 없습니다. 결과적으로 백업 정책에 따라 백업 되는 매일 스냅숏이 생성 되지 않습니다. 이 구성은 주별 및 월별 스냅샷만 백업합니다.

* 스냅샷 정책:   
    주간: `Number of Snapshots to Keep = 4`   
    월별: `Number of Snapshots to Keep = 3`   
* 백업 정책:   
    매일: `Daily Backups to Keep = 15`   
    주간: `Weekly Backups to Keep = 6`   
    월별: `Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>볼륨에 백업 기능을 사용하도록 설정하고 백업 정책 할당

모든 Azure NetApp Files 볼륨은 백업(정책 기반 또는 수동)을 수행하려면 먼저 백업 기능을 사용하도록 설정해야 합니다. 

백업 기능을 사용하도록 설정한 후 정책 기반 백업이 적용되도록 볼륨에 백업 정책을 할당해야 합니다. 수동 백업의 경우 백업 정책은 선택 사항입니다.

볼륨에 백업 기능을 사용하도록 설정하려면 다음을 수행합니다.  

1. **볼륨으로** 이동하여 백업을 사용하도록 설정할 볼륨을 선택합니다.
2. **구성** 을 선택합니다.
3. 백업 구성 페이지에서 **사용** 설정을 **켜기로 전환합니다.**
4. **스냅샷 정책** 드롭다운 메뉴에서 볼륨에 사용할 스냅샷 정책을 할당합니다. 
5. 백업 **정책** 드롭다운 메뉴에서 볼륨에 사용할 백업 정책을 할당합니다. **확인** 을 클릭합니다.

    자격 증명 모음 정보가 미리 채워져 있습니다.  

    ![백업 구성 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-configure-window.png)


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
* [Azure NetApp Files 백업 FAQ](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


