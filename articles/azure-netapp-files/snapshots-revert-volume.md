---
title: 스냅샷을 사용하여 볼륨 되돌리기 Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files 사용하여 볼륨을 이전 상태로 되돌리는 방법을 설명합니다.
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705678"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>스냅샷을 사용하여 볼륨 되돌리기 Azure NetApp Files

[스냅샷](snapshots-introduction.md) 되돌리기 기능을 사용하면 볼륨을 특정 스냅샷이 생성되었을 때의 상태로 신속하게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리는 것이 스냅샷에서 활성 파일 시스템으로 개별 파일을 복원하는 것보다 훨씬 빠릅니다. 또한 스냅샷을 새 볼륨으로 복원하는 것에 비해 공간 효율성이 더 높습니다. 

볼륨의 스냅샷 메뉴에서 볼륨 되돌리기 옵션을 찾을 수 있습니다. 되돌릴 스냅샷을 선택하면 Azure NetApp Files는 선택한 스냅샷을 만들 때 포함했던 데이터 및 타임스탬프로 볼륨을 되돌립니다. 

> [!IMPORTANT]
> 선택한 스냅샷 이후에 수행된 Active Filesystem 데이터 및 스냅샷이 손실됩니다. 스냅샷 되돌리기 작업은 대상 볼륨의 모든 데이터를 선택한 스냅샷의 데이터로 바꿉니다. 스냅샷을 선택하는 경우 스냅샷 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅샷 되돌리기 작업은 실행 취소할 수 없습니다.

## <a name="steps"></a>단계

1. 볼륨의 **스냅샷** 메뉴로 이동합니다.  되돌리기 작업에 사용할 스냅샷을 마우스 오른쪽 단추로 클릭합니다. **볼륨 되돌리기** 를 선택합니다. 

    ![스냅샷의 오른쪽 클릭 메뉴를 설명하는 스크린샷.](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 볼륨을 스냅샷으로 되돌리기 창에서 볼륨의 이름을 입력하고 **되돌리기** 를 클릭합니다.   

    이제 볼륨이 선택한 스냅샷의 지정 시간으로 복원됩니다.

![볼륨을 스냅샷으로 되돌리기 창을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>다음 단계

* [스냅샷에 대한 자세한 정보](snapshots-introduction.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)