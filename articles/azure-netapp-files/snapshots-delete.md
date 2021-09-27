---
title: Azure NetApp Files | 사용하여 스냅샷 삭제 Microsoft Docs
description: Azure NetApp Files 사용하여 스냅샷을 삭제하는 방법을 설명합니다.
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
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634311"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>Azure NetApp Files 사용하여 스냅샷 삭제 

더 이상 보존하지 않아도 되는 스냅샷은 삭제할 수 있습니다. 

> [!IMPORTANT]
> 스냅샷 삭제 작업은 실행 취소할 수 없습니다. 삭제한 스냅샷은 복구할 수 없습니다. 

## <a name="steps"></a>단계

1. 볼륨의 **스냅샷** 메뉴로 이동합니다. 삭제할 스냅샷을 마우스 오른쪽 단추로 클릭합니다. **삭제** 를 선택합니다.

    ![스냅샷의 오른쪽 클릭 메뉴를 설명하는 스크린샷](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 스냅샷 삭제 창에서 **예** 를 클릭하여 스냅샷을 삭제할 것임을 확인합니다. 

    ![스냅샷 삭제를 확인하는 스크린샷](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>다음 단계

* [스냅샷에 대한 자세한 정보](snapshots-introduction.md)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)