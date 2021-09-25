---
title: Azure NetApp Files를 사용 하 여 주문형 스냅숏 만들기 Microsoft Docs
description: Azure NetApp Files를 사용 하 여 주문형 스냅숏을 만드는 방법을 설명 합니다.
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
ms.date: 08/12/2021
ms.author: b-juche
ms.openlocfilehash: 2a17ed67830dfcfc3f1c5c3cbd6cc06ceedc8028
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614185"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅샷 만들기

Azure NetApp Files는 주문형 [스냅숏](snapshots-introduction.md) 만들기 및 스냅숏 정책 사용을 지원 하 여 자동 스냅숏 생성을 예약 합니다. [스냅숏을 새 볼륨으로](snapshots-restore-new-volume.md)복원 하거나, [클라이언트를 사용 하 여 단일 파일을 복원](snapshots-restore-file-client.md)하거나, [스냅숏을 사용 하 여 기존 볼륨을 되돌릴](snapshots-revert-volume.md)수도 있습니다. 이 문서에서는 볼륨에 대 한 주문형 스냅숏을 만드는 방법을 설명 합니다. 

> [!NOTE] 
> 지역 간 복제의 스냅샷 관리에 대한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)을 참조하세요.
 
## <a name="steps"></a>단계

1.  스냅샷을 만들 볼륨으로 이동합니다. **스냅샷** 을 클릭합니다.

    ![스냅숏 블레이드로 이동 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅샷 추가** 를 클릭하여 볼륨에 대한 주문형 스냅샷을 만듭니다.

    ![스냅숏을 추가 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅샷 창에 만들고 있는 새 스냅샷의 이름을 제공합니다.   

    ![새 스냅숏 창을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인** 을 클릭합니다. 

## <a name="next-steps"></a>다음 단계

* [스냅숏에 대 한 자세한 정보](snapshots-introduction.md)
* [Azure NetApp Files를 사용 하 여 스냅숏 정책 관리](snapshots-manage-policy.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Azure Application Consistent Snapshot Tool이란?](azacsnap-introduction.md)
