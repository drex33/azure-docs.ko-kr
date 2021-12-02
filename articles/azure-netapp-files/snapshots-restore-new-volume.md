---
title: Azure NetApp Files |를 사용 하 여 새 볼륨으로 스냅숏 복원 Microsoft Docs
description: Azure NetApp Files를 사용 하 여 스냅숏에서 새 볼륨을 만드는 방법에 대해 설명 합니다.
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
ms.date: 09/16/2021
ms.author: b-hchen
ms.openlocfilehash: a63a2cebc3f9f0852cee1942b7cdc0a9121ad7b2
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486184"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>Azure NetApp Files를 사용 하 여 새 볼륨으로 스냅숏 복원

[스냅숏은](snapshots-introduction.md) 볼륨의 지정 시간 복구를 사용 하도록 설정 합니다. 현재 새 볼륨으로만 스냅샷을 복원할 수 있습니다. 

## <a name="steps"></a>단계

1. 볼륨 블레이드에서 **스냅샷** 을 선택하여 스냅샷 목록을 표시합니다. 
2. 복원할 스냅샷을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션에서 **새 볼륨으로 복원** 을 선택합니다.  

    ![새 볼륨 복원 메뉴를 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 볼륨 만들기 창에서 다음과 같은 새 볼륨 정보를 입력합니다.  
    * **Name**   
        만들고 있는 볼륨의 이름을 지정합니다.  
        
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **할당량**  
        볼륨에 할당하려는 논리 스토리지의 크기를 지정합니다.  

    ![볼륨 만들기 창을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **검토+만들기** 를 클릭합니다.  **만들기** 를 클릭합니다.   
    새 볼륨은 스냅샷에서 사용하는 것과 동일한 프로토콜을 사용합니다.   
    스냅샷이 복원된 새 볼륨은 볼륨 블레이드에 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [스냅숏에 대 한 자세한 정보](snapshots-introduction.md)
* [볼륨 및 스냅샷 메트릭 모니터링](azure-netapp-files-metrics.md#volumes)
* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
