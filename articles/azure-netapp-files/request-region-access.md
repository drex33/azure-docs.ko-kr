---
title: Azure NetApp Files | 대한 지역 액세스 요청 Microsoft Docs
description: Azure NetApp Files 사용하기 위해 지역에 대한 액세스를 요청하는 방법을 설명합니다.
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
ms.date: 11/15/2021
ms.author: b-juche
ms.openlocfilehash: 9d610a74e63c85a66cd0be42cfdda3139c673f67
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520639"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Azure NetApp Files 대한 지역 액세스 요청

일부 특별한 상황에서는 지역에 대한 액세스를 명시적으로 요청해야 할 수 있습니다. 이 문서에서는 요청을 제출하는 방법을 설명합니다. 

## <a name="steps"></a>단계

1. **지원 + 문제 해결** 아래의 **새 지원 요청** 으로 이동합니다.   

2. 문제 **설명** 탭에서 필요한 정보를 제공합니다.
    1. **문제 유형에서** **서비스 및 구독 제한(할당량) 을** 선택합니다.
    2. **구독** 의 경우 사용자의 구독을 선택합니다. 
    3. **할당량 유형에서** **Storage: Azure NetApp Files 제한을 선택합니다.**

    ![문제 설명 탭을 보여 주는 스크린샷.](../media/azure-netapp-files/support-problem-descriptions.png)

3. **추가 세부 정보** 탭에서 요청 정보 필드에 **세부 정보 입력** 을 클릭합니다.  

    ![세부 정보 탭과 세부 정보 입력 필드를 보여주는 스크린샷.](../media/azure-netapp-files/quota-additional-details.png)

4. 지역 액세스를 요청하려면 표시되는 할당량 세부 정보 창에서 다음 정보를 제공합니다.   
    1. **할당량 유형에서** **지역 액세스를** 선택합니다.
    2. **요청된 지역에서** 해당 지역을 선택합니다.

    ![지역 액세스를 요청하기 위한 할당량 세부 정보 창을 보여 주는 스크린샷](../media/azure-netapp-files/quota-details-region-access.png)

5. **저장 후 계속** 을 클릭합니다. **검토 + 만들기를** 클릭하여 요청을 만듭니다.

## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
- [Azure NetApp Files 대한 지역별 용량 할당량](regional-capacity-quota.md)
- [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
