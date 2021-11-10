---
title: Azure NetApp Files에 대 한 요청 영역 액세스 Microsoft Docs
description: Azure NetApp Files 사용을 위해 지역에 대 한 액세스를 요청 하는 방법을 설명 합니다.
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
ms.date: 11/09/2021
ms.author: b-juche
ms.openlocfilehash: 55a4d9c74e0f4e2ee54b3a7bc16e2ef7e4b30502
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132161055"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 요청 영역 액세스

이 문서에서는 지역에 대 한 액세스를 요청 하는 방법을 설명 합니다.

## <a name="steps"></a>단계

1. **지원 + 문제 해결** 아래의 **새 지원 요청** 으로 이동합니다.   

2. **문제 설명** 탭에서 필요한 정보를 제공 합니다.
    1. **문제 유형** 에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
    2. **구독** 의 경우 사용자의 구독을 선택합니다. 
    3. **할당량 유형** 에 대해 **Storage: Azure NetApp Files 제한** 을 선택 합니다.

    ![문제 설명 탭을 보여 주는 스크린샷](../media/azure-netapp-files/support-problem-descriptions.png)

3. **추가 세부 정보** 탭에서 요청 정보 필드에 **세부 정보 입력** 을 클릭합니다.  

    ![세부 정보 탭과 세부 정보 입력 필드를 보여주는 스크린샷.](../media/azure-netapp-files/quota-additional-details.png)

4. 영역 액세스를 요청 하려면 표시 되는 할당량 정보 창에서 다음 정보를 제공 합니다.   
    1. **할당량 유형** 에서 **지역 액세스** 를 선택 합니다.
    2. **요청 된 지역** 에서 해당 지역을 선택 합니다.

    ![영역 액세스 요청에 대 한 할당량 세부 정보 창을 보여 주는 스크린샷](../media/azure-netapp-files/quota-details-region-access.png)

5. **저장 후 계속** 을 클릭합니다. **검토 + 만들기** 를 클릭 하 여 요청을 만듭니다.

## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
- [Azure NetApp Files에 대 한 지역 용량 할당량](regional-capacity-quota.md)
- [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
