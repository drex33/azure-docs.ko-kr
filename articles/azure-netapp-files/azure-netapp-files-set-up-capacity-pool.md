---
title: Azure NetApp Files | 용량 풀 만들기 Microsoft Docs
description: 용량 풀 내에 볼륨을 만들 수 있도록 용량 풀을 만드는 방법을 설명합니다.
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
ms.date: 11/4/2021
ms.author: b-juche
ms.openlocfilehash: 90867546e0866d0d899bc990a9eb5225fbaf4c1e
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027720"
---
# <a name="create-a-capacity-pool-for-azure-netapp-files"></a>Azure NetApp Files 용량 풀 만들기

용량 풀을 만들면 용량 풀 내에 볼륨을 만들 수 있습니다.  

## <a name="before-you-begin"></a>시작하기 전에 

NetApp 계정을 만들어야 합니다.   

[NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>단계 

1. NetApp 계정의 관리 블레이드로 이동한 다음, 탐색 창에서 **용량 풀** 을 클릭합니다.  
    
    ![용량 풀로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. **+ 풀 추가** 를 클릭하여 새 용량 풀을 만듭니다.   
    새 용량 풀 창이 나타납니다.

3. 새 용량 풀에 대한 다음과 같은 정보를 제공합니다.  
   * **이름**  
     용량 풀의 이름을 지정합니다.  
     용량 풀 이름은 각 NetApp 계정에 대해 고유해야 합니다.

   * **서비스 수준**   
     이 필드는 용량 풀에 대한 대상 성능을 보여줍니다.  
     용량 풀에 대한 서비스 수준 지정: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium) 또는 [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **크기**     
     구입하려는 용량 풀의 크기를 지정합니다.        
     최소 용량 풀 크기는 4TiB입니다. 용량 풀의 크기를 1-TiB 단위로 변경할 수 있습니다.

   * **QoS**   
     용량 풀에서 **수동** 또는 **자동** QoS 유형을 사용해야 하는지 여부를 지정합니다.  

     QoS 유형을 이해하려면 [스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 및 [성능 고려 사항](azure-netapp-files-performance-considerations.md)을 참조하세요.  

     > [!IMPORTANT] 
     > **QoS 유형** 을 **수동** 으로의 설정은 영구적입니다. 자동 QoS를 사용하도록 수동 QoS 용량 풀을 변환할 수 없습니다. 하지만 수동 QoS를 사용하도록 자동 QoS 용량 풀을 변환할 수 있습니다. [용량 풀을 변경하여 수동 QoS 사용](manage-manual-qos-capacity-pool.md#change-to-qos)을 참조하세요.   

    ![새 용량 풀](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. **만들기** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계 

- [스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 
- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
- [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
