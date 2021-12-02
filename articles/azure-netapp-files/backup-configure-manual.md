---
title: Azure NetApp Files | 수동 백업 구성 Microsoft Docs
description: Azure NetApp Files 볼륨에 대한 수동 백업을 구성하는 방법에 대해 설명합니다.
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
ms.date: 11/10/2021
ms.author: b-hchen
ms.openlocfilehash: 17e7580464bfd839b19e9ce34c181f0064755e8b
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480493"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>Azure NetApp Files 수동 백업 구성 

Azure NetApp Files 백업은 볼륨 수준에서 정책 기반(예약된) 백업 및 *수동(주문형)* 백업을 지원합니다.  동일한 볼륨에서 두 가지 유형의 백업을 모두 사용할 수 있습니다. 구성 프로세스 중에 정책 기반 백업 또는 수동 백업을 수행하려면 Azure NetApp Files 볼륨에 백업 기능을 사용하도록 설정합니다. 

이 문서에서는 수동 백업을 구성하는 방법을 보여줍니다. 정책 기반 백업 구성은 [정책 기반 백업 구성을 참조하세요.](backup-configure-policy-based.md)  

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공됩니다. **[Azure NetApp Files Backup 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. Azure NetApp Files 백업 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다립니다.

## <a name="about-manual-backups"></a>수동 백업 정보  

백업(정책 기반 또는 수동)을 수행하려면 모든 Azure NetApp Files 볼륨에서 백업 기능을 사용하도록 설정해야 합니다.   

백업 기능을 사용하도록 설정하면 볼륨을 수동으로 백업할 수 있습니다. 수동 백업은 활성 파일 시스템의 지정 시간 스냅샷을 만들고 해당 스냅샷을 Azure Storage 계정에 백업합니다.

다음 목록에는 수동 백업 동작이 요약되어 있습니다.  

* 볼륨이 이미 백업을 사용하도록 설정되고 백업 정책을 사용하여 구성된 경우에도 볼륨에 수동 백업을 만들 수 있습니다.  그러나 볼륨에 대한 미해결 수동 백업 요청은 하나만 있을 수 있습니다. 백업 정책을 할당하고 기준 전송이 계속 진행 중인 경우 기준 전송이 완료될 때까지 수동 백업 만들기가 차단됩니다.

* 백업에 사용할 기존 스냅샷을 지정하지 않으면 수동 백업을 만들면 볼륨에 스냅샷이 자동으로 생성됩니다. 그러면 스냅샷이 Azure Storage로 전송됩니다. 볼륨에서 만든 스냅샷은 다음 수동 백업이 만들어질 때까지 유지됩니다. 후속 수동 백업 작업 중에 이전 스냅샷이 정리됩니다. 최신 수동 백업에 대해 생성된 스냅샷은 삭제할 수 없습니다. 

## <a name="enable-backup-functionality"></a>백업 기능 사용

아직 수행하지 않은 경우 수동 백업을 만들기 전에 볼륨에 대한 백업 기능을 사용하도록 설정합니다. 

1. **볼륨으로** 이동하여 백업을 사용하도록 설정할 특정 볼륨을 선택합니다.
2. **구성** 을 선택합니다.
3. 백업 구성 페이지에서 **사용** 설정을 **켜기로 전환합니다.**   
    자격 증명 모음 필드가 미리 미리 준비되었습니다. 
4. **확인** 을 클릭합니다.   

![백업 구성 창의 사용 설정을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>볼륨에 대한 수동 백업 만들기

1. **볼륨으로** 이동하여 수동 백업을 만들 볼륨을 선택합니다.
2. **백업 추가를** 선택합니다.
3. 표시되는 새 백업 창에서 다음을 수행합니다.   

    1. **이름** 필드에 백업 이름을 지정합니다.   
    
        * 수동 백업 이름은 3~256자여야 합니다.   
        * 실제 백업 이름 앞에 다음 형식의 접두사를 앞에 두는 것이 좋습니다. 이렇게 하면 볼륨이 삭제된 경우(백업이 유지된) 수동 백업을 식별할 수 있습니다.   

            `NetAppAccountName-CapacityPoolName-VolumeName`   

            예를 들어 NetApp 계정이 `account1` 이고, 용량 풀이 `pool1` 이고, 볼륨 이름은 `vol1` 입니다. 수동 백업의 이름은 다음과 같이 지정할 수 있습니다.    

            `account1-pool1-vol1-backup1`   

            백업 이름에 더 짧은 양식을 사용하는 경우 백업 목록에 표시할 NetApp 계정, 용량 풀 및 볼륨 이름을 식별하는 정보가 여전히 포함되어 있는지 확인합니다.
            
    2. 백업에 기존 스냅샷을 사용하려면 **기존 스냅샷 사용** 옵션을 선택합니다.  이 옵션을 사용하는 경우 이름 필드가 백업에 사용되는 기존 스냅샷 이름과 일치하는지 확인합니다. 

4. **만들기** 를 클릭합니다. 

    수동 백업을 만들 때 백업에 대해 지정한 것과 동일한 이름을 사용하여 볼륨에 스냅샷도 생성됩니다. 이 스냅샷은 활성 파일 시스템의 현재 상태를 나타냅니다. Azure Storage로 전송됩니다. 백업이 완료되면 볼륨에 대한 백업 목록에 수동 백업 항목이 표시됩니다.

![새 백업 창을 보여 주는 스크린샷.](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [Azure NetApp Files 백업에 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)


