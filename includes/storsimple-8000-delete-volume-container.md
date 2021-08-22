---
title: 포함 파일
description: 포함 파일
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 6e505158c80c0b9a831af17ca2f2d6062dc56f6a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449857"
---
볼륨 컨테이너를 삭제하려면
 - 볼륨 컨테이너에서 볼륨을 삭제해야 합니다. 볼륨 컨테이너가 볼륨에 연결된 경우 해당 볼륨을 먼저 오프라인으로 전환합니다. [볼륨을 오프라인으로 전환](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)단계를 따릅니다. 볼륨이 오프라인이 되면 삭제할 수 있습니다. 
 - 연결된 백업 정책 및 클라우드 스냅샷을 삭제합니다. 볼륨 컨테이너에 연결된 백업 정책 및 클라우드 스냅샷이 있는지 확인합니다. 있는 경우 [백업 정책을 삭제](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy)합니다. 그러면 클라우드 스냅샷도 삭제됩니다. 
 
볼륨 컨테이너에 연결된 볼륨, 백업 정책 및 클라우드 스냅샷이 있는 경우 삭제할 수 있습니다. 볼륨 컨테이너를 삭제하려면 다음 절차를 수행합니다.

#### <a name="to-delete-a-volume-container"></a>볼륨 컨테이너를 삭제하려면

1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스** 를 클릭합니다. 디바이스를 선택하여 클릭하고 **설정 &gt; 관리 &gt; 볼륨 컨테이너** 로 이동합니다.

    ![볼륨 컨테이너 블레이드](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. 테이블 형식의 볼륨 컨테이너 목록에서 삭제하려는 볼륨 컨테이너를 선택하고 **...** 를 마우스 오른쪽 단추로 클릭한 후 **삭제** 를 선택합니다.

    ![볼륨 컨테이너 삭제](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. 볼륨 컨테이너에 연결된 볼륨, 백업 정책 및 클라우드 스냅샷이 있는 경우 삭제할 수 있습니다. 확인 대화 상자가 나타나면 볼륨 컨테이너를 삭제한 결과를 설명하는 확인란을 검토 및 선택합니다. **삭제** 를 클릭하면 볼륨 컨테이너가 삭제됩니다.

    ![삭제 확인](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)<!--Added missing border.-->

삭제된 볼륨 컨테이너를 반영하도록 볼륨 컨테이너 목록이 업데이트됩니다.

![볼륨 컨테이너 페이지의 스크린샷. 볼륨 컨테이너의 테이블 형식 목록에는 삭제된 컨테이너가 더 이상 포함되지 않습니다.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
