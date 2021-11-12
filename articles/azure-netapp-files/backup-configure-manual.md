---
title: Azure NetApp Files에 대 한 수동 백업 구성 | Microsoft Docs
description: Azure NetApp Files 볼륨의 수동 백업을 구성 하는 방법을 설명 합니다.
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
ms.date: 11/10/2021
ms.author: b-juche
ms.openlocfilehash: 5b5d2cafbbd70e63e2b3a039e94f1fc66106203c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319601"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 수동 백업 구성 

Azure NetApp Files 백업에서는 볼륨 수준에서 *정책 기반* (예약 됨) 백업 및 *수동* (주문형) 백업을 지원 합니다. 동일한 볼륨에서 두 가지 유형의 백업을 모두 사용할 수 있습니다. 구성 프로세스 중에는 정책 기반 백업 또는 수동 백업을 수행할 수 있기 전에 Azure NetApp Files 볼륨에 대 한 백업 기능을 사용 하도록 설정 합니다. 

이 문서에서는 수동 백업을 구성 하는 방법을 보여 줍니다. 정책 기반 백업 구성에 대 한 자세한 내용은 [정책 기반 백업 구성](backup-configure-policy-based.md)을 참조 하세요.  

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공 됩니다. **[Azure NetApp Files 백업 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. Azure NetApp Files 팀에서 Azure NetApp Files 백업 기능을 사용 하기 전에 공식 확인 전자 메일을 기다리십시오.

## <a name="about-manual-backups"></a>수동 백업 정보  

모든 Azure NetApp Files 볼륨에 백업 기능을 사용 하도록 설정한 후 백업 (정책 기반 또는 수동)을 수행할 수 있어야 합니다.   

백업 기능을 사용 하도록 설정한 후에는 수동으로 볼륨을 백업 하는 옵션을 사용할 수 있습니다. 수동 백업은 활성 파일 시스템의 특정 시점 스냅숏을 만들고 해당 스냅숏을 Azure 저장소 계정에 백업 합니다.

다음 목록은 수동 백업 동작을 요약 한 것입니다.  

* 볼륨이 이미 백업 가능 하 고 백업 정책을 사용 하 여 구성 된 경우에도 볼륨에서 수동 백업을 만들 수 있습니다.  그러나 볼륨에 대해 처리 중인 수동 백업 요청은 하나만 있을 수 있습니다. 백업 정책을 할당 하 고 기준 전송이 아직 진행 중인 경우 기준 전송이 완료 될 때까지 수동 백업 생성이 차단 됩니다.

* 백업에 사용할 기존 스냅숏을 지정 하지 않으면 수동 백업을 만들 때 볼륨에 스냅숏이 자동으로 생성 됩니다. 그러면 스냅숏이 Azure storage로 전송 됩니다. 볼륨에 생성 된 스냅숏은 다음 수동 백업을 만들 때까지 유지 됩니다. 이후 수동 백업 작업을 수행 하는 동안 이전 스냅숏이 정리 됩니다. 최신 수동 백업에 대해 생성 된 스냅숏은 삭제할 수 없습니다. 

## <a name="enable-backup-functionality"></a>백업 기능 사용

아직 수행 하지 않은 경우 수동 백업을 만들기 전에 볼륨의 백업 기능을 사용 하도록 설정 합니다. 

1. **볼륨** 으로 이동 하 고 백업을 사용 하도록 설정할 특정 볼륨을 선택 합니다.
2. **구성** 을 선택합니다.
3. 백업 구성 페이지에서 **사용** 설정을 **켜기** 로 전환 합니다.   
    자격 증명 모음 필드는 미리 채워져 있습니다. 
4. **확인** 을 클릭합니다.   

![백업 구성 창의 사용 설정을 보여 주는 스크린샷](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>볼륨에 대 한 수동 백업 만들기

1. **볼륨** 으로 이동 하 여 수동 백업을 만들려는 볼륨을 선택 합니다.
2. **백업 추가** 를 선택 합니다.
3. 표시 되는 새 백업 창에서 다음을 수행 합니다.   

    1. **이름** 필드에 백업 이름을 지정 합니다.   
    
        * 수동 백업 이름은 3 ~ 256 자 길이 여야 합니다.   
        * 실제 백업 이름 앞에 다음 형식의 접두사를 추가 하는 것이 가장 좋습니다. 이렇게 하면 볼륨이 삭제 된 경우 (백업이 보존 된 상태에서) 수동 백업을 식별할 수 있습니다.   

            `NetAppAccountName-CapacityPoolName-VolumeName`   

            예를 들어 NetApp 계정이이 고 `account1` 용량 풀이 인 경우 `pool1` 볼륨 이름은 `vol1` 입니다. 수동 백업의 이름은 다음과 같이 지정할 수 있습니다.    

            `account1-pool1-vol1-backup1`   

            백업 이름에 더 짧은 형식을 사용 하는 경우 백업 목록에 표시할 NetApp 계정, 용량 풀 및 볼륨 이름을 식별 하는 정보가 계속 포함 되어 있는지 확인 합니다.
            
    2. 백업에 기존 스냅숏을 사용 하려면 **기존 스냅숏 사용** 옵션을 선택 합니다.  이 옵션을 사용 하는 경우 이름 필드가 백업에 사용 되는 기존 스냅숏 이름과 일치 하는지 확인 합니다. 

4. **만들기** 를 클릭합니다. 

    수동 백업을 만들 때 백업에 대해 지정한 것과 동일한 이름을 사용 하 여 볼륨에 스냅숏이 생성 됩니다. 이 스냅숏은 활성 파일 시스템의 현재 상태를 나타냅니다. Azure storage에 전송 됩니다. 백업이 완료 되 면 볼륨의 백업 목록에 수동 백업 항목이 표시 됩니다.

![새 백업 창을 보여 주는 스크린샷](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)


