---
title: Azure NetApp Files | 사용하여 주문형 스냅샷 만들기 Microsoft Docs
description: Azure NetApp Files 사용하여 주문형 스냅샷을 만드는 방법을 설명합니다.
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
ms.date: 10/25/2021
ms.author: b-hchen
ms.openlocfilehash: 957e3cb5c5f1a3cdaeb3aa6a1ec868d0c0fdaa09
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481673"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅샷 만들기

Azure NetApp Files 주문형 스냅샷 만들기 및 스냅샷 정책을 사용하여 자동 스냅샷 생성 예약을 [지원합니다.](snapshots-introduction.md) [스냅샷을 새 볼륨으로](snapshots-restore-new-volume.md) [복원하거나, 클라이언트를 사용하여 단일 파일을 복원하거나,](snapshots-restore-file-client.md) [스냅샷을 사용하여 기존 볼륨을 되돌릴](snapshots-revert-volume.md)수도 있습니다. 이 문서에서는 볼륨에 대한 주문형 스냅샷을 만드는 방법을 설명합니다. 

> [!NOTE] 
> * 지역 간 복제의 스냅샷 관리에 대한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)을 참조하세요.
> * AzAcSnap(Azure 애플리케이션 일관성 스냅샷 도구)을 사용하여 타사 데이터베이스에 대한 데이터 보호를 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure 애플리케이션 일관성 있는 스냅샷 도구란?을 참조하세요.](azacsnap-introduction.md)
 
## <a name="steps"></a>단계

1.  스냅샷을 만들 볼륨으로 이동합니다. **스냅샷** 을 클릭합니다.

    ![스냅샷 블레이드로 이동하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅샷 추가** 를 클릭하여 볼륨에 대한 주문형 스냅샷을 만듭니다.

    ![스냅샷을 추가하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅샷 창에 만들고 있는 새 스냅샷의 이름을 제공합니다.   

    ![새 스냅샷 창을 보여 주는 스크린샷.](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인** 을 클릭합니다. 

## <a name="next-steps"></a>다음 단계

* [스냅샷에 대한 자세한 정보](snapshots-introduction.md)
* [Azure NetApp Files 통해 스냅샷 정책 관리](snapshots-manage-policy.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Azure Application Consistent Snapshot Tool이란?](azacsnap-introduction.md)
