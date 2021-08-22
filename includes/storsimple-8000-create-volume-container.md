---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 4ad58078207a90966134ec4d6cf8815f614e4341
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449878"
---
#### <a name="to-create-a-volume-container"></a>볼륨 컨테이너를 만들려면

1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스** 를 클릭합니다. 테이블 형식 디바이스 목록에서 디바이스를 선택하여 클릭합니다. 

    ![볼륨 컨테이너 블레이드](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. 디바이스 대시보드에서 **+ 볼륨 컨테이너 추가** 를 클릭합니다.

    ![볼륨 컨테이너 블레이드 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. **볼륨 컨테이너 추가** 블레이드에서:
   
   1. 디바이스가 자동으로 선택됩니다.
   2. 볼륨 컨테이너의 **이름** 을 입력합니다. 이름은 3~32자 길이여야 합니다. 볼륨 컨테이너를 만든 후에는 이름을 바꿀 수 없습니다.
   3. **클라우드 스토리지 암호화 사용** 을 선택하여 디바이스에서 클라우드로 보낸 데이터의 암호화를 사용합니다.
   4. **클라우드 저장소 암호화 키** 를 8~32자 사이로 입력하고 확인을 위해 다시 한 번 입력합니다. 이 키는 암호화된 데이터에 액세스하는 디바이스가 사용합니다.
   5. 이 볼륨 컨테이너에 연결할 **저장소 계정** 을 선택합니다. 서비스를 만들 때 생성되는 기본 계정 또는 기존 스토리지 계정을 선택할 수 있습니다. **새로 추가** 옵션을 사용하여 이 서비스 구독에 연결되지 않은 저장소 계정을 지정할 수도 있습니다.
   6. 사용 가능한 대역폭을 모두 사용하려면 **대역폭 지정** 드롭다운 목록에서 **제한 없음** 을 선택합니다.
   
      대역폭 사용 정보를 사용할 수 있는 경우 **대역폭 템플릿 선택** 을 지정하여 일정에 따라 대역폭을 할당할 수 있습니다. 단계별 절차의 경우 [대역폭 템플릿 추가](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)로 이동합니다.

      ![볼륨 컨테이너 블레이드 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. **만들기** 를 클릭합니다.

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       볼륨 컨테이너가 성공적으로 만들어지면 알림이 표시됩니다.

       ![볼륨 컨테이너 만들기 알림](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   새로 만들어진 볼륨 컨테이너가 디바이스에 대한 볼륨 컨테이너 목록에 나열됩니다.

   ![볼륨 컨테이너 추가 블레이드](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
