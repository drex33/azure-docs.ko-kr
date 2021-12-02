---
title: Azure NetApp Files 지역 간 복제를 위한 볼륨 복제 또는 삭제 | Microsoft Docs
description: 원본 볼륨과 대상 볼륨 사이에 더 이상 불필요한 복제 연결을 삭제하는 방법을 설명합니다.
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
ms.date: 11/18/2020
ms.author: b-hchen
ms.openlocfilehash: efc8089727466a7dedd779442d4edcfb9a378ada
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483463"
---
# <a name="delete-volume-replications-or-volumes"></a>볼륨 복제 또는 볼륨 삭제

이 문서에서는 볼륨 복제를 삭제하는 방법을 설명합니다. 또한 원본 또는 대상 볼륨을 삭제하는 방법을 설명합니다.

## <a name="delete-volume-replications"></a>볼륨 복제 삭제

볼륨 복제를 삭제하여 원본 볼륨과 대상 볼륨 간의 복제 연결을 종료할 수 있습니다. 대상 볼륨에서 복제를 삭제해야 합니다. 삭제 작업은 복제에 대한 권한 부여만 제거합니다. 원본 또는 대상 볼륨은 제거되지 않습니다. 

1. 볼륨 복제를 삭제하기 전에 복제 피어링이 손상되었는지 확인하세요. 복제 피어링을 중단하려면: 

    1. *대상* 볼륨을 선택합니다. 스토리지 서비스에서 **복제** 를 클릭합니다.  

    2.  계속하려면 먼저 다음 필드를 확인합니다.  
        * 미러 상태가 ***미러링됨*** 으로 표시되는지 확인합니다.   
            미러 상태가 *초기화되지 않음* 으로 표시되는 경우 복제 피어링을 중단하지 않도록 합니다.
        * 관계 상태가 ***유휴*** 로 표시하는지 확인합니다.   
            관계 상태가 *전송 중* 으로 표시되는 경우 복제 피어링을 중단하지 않도록 합니다.   

        [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)를 참조하세요. 

    3.  **피어링 분리** 를 클릭합니다.  

    4.  메시지가 표시되면 **Yes** 를 입력하고 **중단** 을 클릭합니다. 

        ![복제 피어링 중단](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. 볼륨 복제를 삭제하려면 원본 또는 대상 볼륨에서 **복제** 를 선택합니다.  

2. **삭제** 를 클릭합니다.    

3. **Yes** 를 입력하고 **삭제** 를 클릭하여 삭제를 확인합니다.   

    ![복제 삭제](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>원본 또는 대상 볼륨 삭제

원본 또는 대상 볼륨을 삭제하려면 설명된 순서대로 다음 단계를 수행해야 합니다. 그렇지 않으면 `Volume with replication cannot be deleted` 오류가 발생합니다.  

1. 대상 볼륨에서 [볼륨 복제를 삭제](#delete-volume-replications)합니다.   

2. 필요에 따라 볼륨 이름을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택하여 대상 또는 원본 볼륨을 삭제합니다.   

    ![볼륨의 오른쪽 클릭 메뉴를 표시하는 스크린샷.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

