---
title: Azure NetApp Files 지역 간 복제를 사용하여 재해 복구 관리 | Microsoft Docs
description: Azure NetApp Files 지역 간 복제를 사용하여 재해 복구를 관리하는 방법을 설명합니다.
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
ms.date: 09/29/2021
ms.author: b-hchen
ms.openlocfilehash: 86eabaf1cf705f3097964e3ae745c13071dd9d4e
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133544688"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>지역 간 복제를 사용하여 재해 복구 관리 

원본 볼륨과 대상 볼륨 간에 진행 중인 복제([볼륨 복제 만들기](cross-region-replication-create-peering.md) 참조)는 재해 복구 이벤트를 준비합니다. 

이러한 이벤트가 발생하는 경우 [대상 볼륨으로 장애 조치(failover)](#fail-over-to-destination-volume)하여 클라이언트가 대상 볼륨을 읽고 쓰도록 할 수 있습니다. 

재해 복구 후 [다시 동기화](#resync-replication) 작업을 수행하여 원본 볼륨으로 장애 복구할 수 있습니다. 그런 다음, [원본-대상 복제를 다시 설정](#reestablish-source-to-destination-replication)하고 클라이언트가 액세스할 원본 볼륨을 다시 탑재합니다. 

자세한 내용은 아래에 설명되어 있습니다. 

## <a name="fail-over-to-destination-volume"></a>대상 볼륨으로 장애 조치

대상 볼륨을 활성화해야 하는 경우(예: 대상 지역으로 장애 조치하려는 경우) 복제 피어링을 중단하고 대상 볼륨을 탑재해야 합니다.  

1. 복제 피어링을 중단하려면 대상 볼륨을 선택합니다. 스토리지 서비스에서 **복제** 를 클릭합니다.  

2.  계속하려면 먼저 다음 필드를 확인합니다.  
    * 미러 상태가 ***미러링됨*** 으로 표시되는지 확인합니다.   
        미러 상태가 *초기화되지 않음* 으로 표시되는 경우 복제 피어링을 중단하지 않도록 합니다.
    * 관계 상태가 ***유휴*** 로 표시하는지 확인합니다.   
        관계 상태가 *전송 중* 으로 표시되는 경우 복제 피어링을 중단하지 않도록 합니다.   

    [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)를 참조하세요. 

3.  **피어링 분리** 를 클릭합니다.  

4.  메시지가 표시되면 **예** 를 입력하고 **분리** 단추를 클릭합니다. 

    ![복제 피어링 분리](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)의 단계를 수행하여 대상 볼륨을 탑재합니다.   
    이 단계를 통해 클라이언트는 대상 볼륨에 액세스할 수 있습니다.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>재해 복구 후 볼륨 다시 동기화

재해 복구 후 다시 동기화 작업을 수행하여 원본 볼륨을 다시 활성화할 수 있습니다.  다시 동기화 작업은 복제 프로세스를 되돌리고 대상 볼륨에서 원본 볼륨으로 데이터를 동기화합니다.  

> [!IMPORTANT] 
> 다시 동기화 작업은 원본 볼륨과 대상 볼륨을 동기화 하 여 원본 볼륨과 대상 볼륨의 최신 업데이트를 사용 하 여 마지막으로 사용 가능한 일반 스냅숏을 기반으로 합니다. 이 작업을 수행 하면 대부분의 경우에 전체 볼륨을 동기화 하지 않아도 됩니다. 대부분의 경우에는 가장 최근의 일반적인 스냅숏 *이후의* 대상 볼륨에 대 한 변경 내용만 원본 볼륨에 복제 됩니다.  
> 
> 다시 동기화 작업은 원본 볼륨에서 가장 일반적인 스냅숏 보다 최신 데이터를 업데이트 된 대상 볼륨 데이터로 덮어씁니다. UI는 데이터 손실 가능성을 경고합니다. 작업이 시작되기 전에 다시 동기화 작업을 확인하라는 메시지가 표시됩니다.  
> 
> 원본 볼륨이 재해가 발생 하지 않아 공통 스냅숏이 존재 하지 않는 경우 대상의 모든 데이터가 새로 생성 된 원본 볼륨으로 다시 동기화 됩니다.


1. 복제를 다시 동기화하려면 *원본* 볼륨을 선택합니다. 스토리지 서비스에서 **복제** 를 클릭합니다. 그런 다음, **다시 동기화** 를 클릭합니다.  

2. 메시지가 표시되면 **예** 를 입력하고 **다시 동기화** 를 클릭합니다. 
 
    ![복제 다시 동기화](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. [복제 관계 상태 표시](cross-region-replication-display-health-status.md)의 단계에 따라 원본 볼륨 상태를 모니터링합니다.   
    원본 볼륨 상태에 다음 값이 표시되면 다시 동기화 작업이 완료되고 대상 볼륨의 변경 내용이 이제 원본 볼륨에 캡처됩니다.   

    * 미러된 상태: *미러링됨*  
    * 전송 상태: *유휴*  

## <a name="reestablish-source-to-destination-replication"></a>원본-대상 복제 다시 설정

대상에서 원본으로 다시 동기화 작업을 완료한 후에는 복제 피어링을 다시 중단하여 원본-대상 복제를 다시 설정해야 합니다. 또한 클라이언트에서 액세스할 수 있도록 원본 볼륨을 다시 탑재해야 합니다.  

1. 다음과 같이 복제 피어링을 분리합니다.  
    a. *대상* 볼륨을 선택합니다. 스토리지 서비스에서 **복제** 를 클릭합니다.  
    b. 계속하려면 먼저 다음 필드를 확인합니다.   
    * 미러 상태가 ***미러링됨*** 으로 표시되는지 확인합니다.   
    미러 상태가 *초기화되지 않음* 으로 표시되는 경우 복제 피어링을 분리하지 않도록 합니다.  
    * 관계 상태가 ***유휴*** 로 표시하는지 확인합니다.   
    관계 상태가 *전송 중* 으로 표시되는 경우 복제 피어링을 분리하지 않도록 합니다.    

        [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)를 참조하세요. 

    다. **피어링 분리** 를 클릭합니다.   
    d. 메시지가 표시되면 **예** 를 입력하고 **분리** 단추를 클릭합니다.  

2. 원본 볼륨을 대상 볼륨으로 다시 동기화합니다.  
    a. *대상* 볼륨을 선택합니다. 스토리지 서비스에서 **복제** 를 클릭합니다. 그런 다음, **다시 동기화** 를 클릭합니다.   
    b. 메시지가 표시되면 **예** 를 입력하고 **다시 동기화** 단추를 클릭합니다.

3. [Windows 또는 Linux 가상 머신에 대 한 볼륨 탑재](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)의 단계를 수행 하 여 원본 볼륨을 다시 탑재 합니다.  
    이 단계를 통해 클라이언트는 원본 볼륨에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [지역 간 복제 대상 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [볼륨 복제 또는 볼륨 삭제](cross-region-replication-delete.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

