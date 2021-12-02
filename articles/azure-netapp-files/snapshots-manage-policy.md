---
title: Azure NetApp Files | 스냅샷 정책 관리 Microsoft Docs
description: Azure NetApp Files 사용하여 스냅샷 정책을 만들고, 관리하고, 수정하고, 삭제하는 방법을 설명합니다.
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
ms.openlocfilehash: 0aa6a641d93d72a9c8b8929396b6bffff4a3dda0
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484869"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>Azure NetApp Files 스냅샷 정책 관리

[스냅샷을](snapshots-introduction.md) 사용하면 볼륨을 적시에 복구할 수 있습니다. 스냅샷 정책을 사용하여 [볼륨 스냅샷을](snapshots-introduction.md) 자동으로 만들도록 예약할 수 있습니다. 필요에 따라 스냅샷 정책을 수정하거나 더 이상 필요하지 않은 스냅샷 정책을 삭제할 수도 있습니다.  

## <a name="create-a-snapshot-policy"></a>스냅샷 정책 만들기 

스냅샷 정책을 사용하면 스냅샷 만들기 빈도를 매시간, 매일, 매주 또는 매월 주기로 지정할 수 있습니다. 또한 볼륨에 대해 보관할 최대 스냅샷 수를 지정해야 합니다.  

1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

    ![스냅샷 정책으로 이동하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  스냅샷 정책 창에서 정책 상태를 **사용** 으로 설정합니다. 

3.  **매시간,** **매일,** **매주** 또는 **매월** 탭을 클릭하여 매시간, 매일, 매주 또는 매월 스냅샷 정책을 만듭니다. **보관할 스냅샷 수** 를 지정합니다.  

    볼륨에 허용되는 최대 스냅샷 수에 대해서는 [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)을 참조하세요. 

    다음 예제에서는 매시간 스냅샷 정책 구성을 보여 줍니다. 

    ![시간별 스냅샷 정책을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-hourly.png)

    다음 예제에서는 매일 스냅샷 정책 구성을 보여 줍니다.

    ![일일 스냅샷 정책을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-daily.png)

    다음 예제에서는 매주 스냅샷 정책 구성을 보여 줍니다.

    ![주간 스냅샷 정책을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-weekly.png)

    다음 예제에서는 매월 스냅샷 정책 구성을 보여 줍니다.

    ![월별 스냅샷 정책을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **저장** 을 클릭합니다.  

추가 스냅샷 정책을 만들어야 하는 경우 3단계를 반복합니다.
만든 정책이 스냅샷 정책 페이지에 표시됩니다.

볼륨에서 스냅샷 정책을 사용하려면 [볼륨에 정책을 적용](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume)해야 합니다. 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>볼륨에 스냅샷 정책 적용

볼륨에서 만든 스냅샷 정책을 사용하려면 볼륨에 정책을 적용해야 합니다. 

지역 간 복제에서는 대상 볼륨에 스냅샷 정책을 적용할 수 없습니다.  

1.  **볼륨** 페이지로 이동하여 스냅샷 정책을 적용할 볼륨을 마우스 오른쪽 단추로 클릭하고 **편집** 을 선택합니다.

    ![볼륨 오른쪽 클릭 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  편집 창의 **스냅샷 정책** 에서 볼륨에 사용할 정책을 선택합니다.  **확인** 을 클릭하여 정책을 적용합니다.  

    ![스냅샷 정책 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>스냅샷 정책 수정 

기존 스냅샷 정책을 수정하여 정책 상태, 스냅샷 빈도(매시간, 매일, 매주 또는 매월) 또는 보존할 스냅샷 수를 변경할 수 있습니다.  
 
1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

2.  수정하려는 스냅샷 정책을 마우스 오른쪽 단추로 클릭한 다음, **편집** 을 선택합니다.

    ![스냅샷 정책 오른쪽 클릭 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  나타나는 스냅샷 정책 창에서 변경을 수행한 후 **저장** 을 클릭합니다. 

## <a name="delete-a-snapshot-policy"></a>스냅샷 정책 삭제 

더 이상 보존하지 않으려는 스냅샷 정책을 삭제할 수 있습니다.   

1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

2.  수정하려는 스냅샷 정책을 마우스 오른쪽 단추로 클릭한 다음, **삭제** 를 선택합니다.

    ![삭제 메뉴 항목을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **예** 를 클릭하여 스냅샷 정책을 삭제할 것임을 확인합니다.   

    ![스냅샷 정책 삭제 확인을 보여 주는 스크린샷.](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>다음 단계

* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [스냅샷에 대한 자세한 정보](snapshots-introduction.md)