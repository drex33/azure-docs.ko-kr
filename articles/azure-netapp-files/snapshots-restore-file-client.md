---
title: Azure NetApp Files에서 클라이언트를 사용 하 여 스냅숏에서 파일 복원 Microsoft Docs
description: Azure NetApp Files를 사용 하 여 탑재 된 볼륨의 클라이언트를 사용 하 여 스냅숏에서 파일을 복원 하는 방법을 설명 합니다.
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
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634306"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>Azure NetApp Files 있는 클라이언트를 사용 하 여 스냅숏에서 파일 복원

[스냅숏은](snapshots-introduction.md) 볼륨의 지정 시간 복구를 사용 하도록 설정 합니다. [전체 스냅샷을 볼륨으로 복원](snapshots-restore-new-volume.md)하지 않으려면 볼륨이 탑재된 클라이언트를 사용하여 스냅샷에서 파일을 복원할 수 있습니다.  

탑재된 볼륨에는 스냅샷 디렉터리 `.snapshot`(NFS 클라이언트) 또는 클라이언트에서 액세스할 수 있는 `~snapshot`(SMB 클라이언트)이 포함되어 있습니다. 스냅샷 디렉터리에는 볼륨의 스냅샷에 해당하는 하위 디렉터리가 포함되어 있습니다. 각 하위 디렉터리에는 스냅샷의 파일이 있습니다. 실수로 파일을 삭제하거나 덮어쓰는 경우 스냅샷 하위 디렉터리에서 읽기-쓰기 디렉터리로 파일을 복사하여 파일을 부모 읽기-쓰기 디렉터리로 복원할 수 있습니다. 

[스냅샷 경로 숨기기 옵션](snapshots-edit-hide-path.md)을 사용하여 스냅샷 디렉터리에 대한 액세스를 제어할 수 있습니다. 이 옵션은 디렉터리를 클라이언트에서 숨길지 여부를 제어합니다. 따라서 이 옵션으로 스냅샷의 파일 및 폴더에 대한 액세스도 제어할 수 있습니다.  

NFSv4.1에는 `.snapshot` 디렉터리(`ls -la`)가 표시되지 않습니다. 그러나 스냅샷 경로 숨기기 옵션을 설정하지 않은 경우에도 클라이언트 명령줄에서 `.snapshot` 명령을 사용하여 NFSv4.1을 통해 `cd <snapshot-path>` 디렉터리에 계속 액세스할 수 있습니다. 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Linux NFS 클라이언트를 사용하여 파일 복원 

1. `ls` Linux 명령을 사용하여 `.snapshot` 디렉터리에서 복원하려는 파일을 나열합니다. 

    예를 들면 다음과 같습니다.

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. `cp` 명령을 사용하여 부모 디렉터리에 파일을 복사합니다.  

    예를 들면 다음과 같습니다. 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>Windows 클라이언트를 사용하여 파일 복원 

1. 볼륨의 `~snapshot`디렉터리가 숨겨진 경우 부모 디렉터리에서 [숨겨진 항목을 표시](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10)하여 `~snapshot`을 표시합니다.

    ![디렉터리의 숨겨진 항목을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. `~snapshot` 내의 하위 디렉터리로 이동하여 복원하려는 파일을 찾습니다.  파일을 마우스 오른쪽 단추로 클릭합니다. **복사** 를 선택합니다.  

    ![복원할 파일을 복사 하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 부모 디렉터리로 돌아갑니다. 부모 디렉터리를 마우스 오른쪽 단추로 클릭하고 `Paste`를 선택하여 파일을 디렉터리에 붙여넣습니다.

    ![복원에 파일을 붙여 넣는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 부모 디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택한 다음, **이전 버전** 탭을 클릭하여 스냅샷 목록을 확인하고 **복원** 을 선택하여 파일을 복원할 수도 있습니다.  

    ![이전 버전의 속성을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>다음 단계

* [스냅숏에 대 한 자세한 정보](snapshots-introduction.md) 
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)