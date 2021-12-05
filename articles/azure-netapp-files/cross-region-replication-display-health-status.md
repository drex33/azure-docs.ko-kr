---
title: Azure NetApp Files 복제 관계의 상태 표시 | Microsoft Docs
description: 원본 볼륨 또는 Azure NetApp Files의 대상 볼륨에서 복제 상태를 보는 방법을 설명합니다.
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
ms.date: 12/03/2021
ms.author: b-hchen
ms.openlocfilehash: 4c488a1711399487c4e109c30431f69a3cfda33c
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133571108"
---
# <a name="display-health-status-of-replication-relationship"></a>복제 관계의 상태 표시 

원본 볼륨 또는 대상 볼륨에서 복제 상태를 볼 수 있습니다.  

## <a name="display-replication-status"></a>복제 상태 표시

1. 원본 볼륨 또는 대상 볼륨에서 볼륨에 대한 스토리지 서비스 아래의 **복제** 를 클릭합니다.

    다음 복제 상태 및 상태 정보가 표시됩니다.  
    * **엔드포인트 유형** – 볼륨이 복제의 원본인지 또는 대상인지를 식별합니다.
    * **상태** – 복제 관계의 상태를 표시합니다.
    * **미러 상태** – 다음 값 중 하나를 표시합니다.
        * *초기화되지 않음*:  
            피어링 관계를 만들 때의 초기 기본 상태입니다. 초기화가 성공적으로 완료될 때까지 상태는 초기화되지 않은 상태를 유지합니다.
        * *미러링됨*:   
            대상 볼륨이 초기화되었으며 미러링 업데이트를 받을 준비가 되었습니다.
        * *분리됨*:   
            피어링 관계를 분리한 후의 상태입니다. 대상 볼륨이 `‘RW’`이고 스냅샷이 있습니다.
    * **관계 상태** – 다음 값 중 하나를 표시합니다. 
        * *유휴 상태*:  
            전송 작업이 진행 중이 아니며 후속 전송은 사용하지 않도록 설정됩니다.
        * *전송 중*:  
            전송 작업이 진행 중이며 후속 전송은 사용하지 않도록 설정됩니다.
    * **복제 일정** – 초기화(기준 복사)가 완료되면 증분 미러링 업데이트가 수행되는 빈도를 표시합니다.

    * **총 진행률** -관계의 수명 동안 전송 된 누적 바이트의 총 크기를 표시 합니다. 이 크기는 전송 된 실제 바이트 이며 원본 및 대상 볼륨이 보고 하는 논리적 공간과 다를 수 있습니다.  

    ![복제 상태](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 이전 복제 작업이 완료되지 않은 경우 복제 관계는 *비정상* 상태를 표시합니다. 이 상태에서는 낮은 전송 기간에 걸쳐 대용량 볼륨이 전송됩니다(예: 대용량 볼륨의 경우 전송 시간이 10분 소요됨). 이 경우 관계 상태가 *전송 중* 으로 표시되고 상태가 *비정상* 으로 표시됩니다.

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [지역 간 복제 대상 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [볼륨 복제 또는 볼륨 삭제](cross-region-replication-delete.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

