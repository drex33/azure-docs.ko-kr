---
title: 태그를 사용하여 Azure NetApp Files 청구 관리 | Microsoft Docs
description: 태그를 사용하여 Azure NetApp Files 청구를 관리하는 방법을 설명합니다.
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
ms.date: 05/06/2021
ms.author: b-hchen
ms.openlocfilehash: d9af9638d0e0cc485d1452cd7b754136c140bd41
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481426"
---
# <a name="manage-billing-by-using-capacity-pool-billing-tags"></a>용량 풀 청구 태그를 사용하여 청구 관리

태그는 리소스를 분류하고 통합 청구를 볼 수 있는 이름 및 값 쌍입니다. 여러 리소스 및 리소스 그룹에 동일한 태그를 적용할 수 있습니다.  태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.  

태그를 사용하면 Azure NetApp Files 청구 및 비용을 관리할 수 있습니다. 예를 들어 회사에는 Azure 구독이 하나뿐이지만 Azure 리소스를 사용하고 비용이 발생하는 여러 부서가 있을 수 있습니다. 용량 풀 수준에서 부서 이름으로 리소스에 태그를 지정할 수 있습니다. 해당 태그는 각 부서에서 발생하는 비용을 볼 수 있도록 청구서에 표시됩니다.   

청구 태그는 볼륨 수준이 아닌 용량 풀 수준에서 할당됩니다.

## <a name="steps"></a>단계

1. 용량 풀에서 태그를 추가하거나 편집하려면 **용량 풀** 로 이동하여 **태그** 를 선택합니다.   

2. **이름** 및 **값** 쌍을 입력합니다.  **적용** 을 클릭합니다.

    > [!IMPORTANT] 
    > 태그 데이터는 전역적으로 복제됩니다. 따라서 리소스의 보안을 손상시킬 수 있는 태그 이름 또는 값을 사용하지 마십시오. 예를 들어 개인 정보나 중요한 정보가 포함된 태그 이름은 사용하지 마십시오. 

      ![용량 풀의 태그 창을 보여 주는 스냅샷입니다.](../media/azure-netapp-files/billing-tags-capacity-pool.png)

3. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) 포털을 사용하여 태그가 지정된 리소스에 대한 정보를 표시하고 다운로드할 수 있습니다. 
    1. **비용 분석** 을 클릭하고 **리소스별 비용** 보기를 선택합니다.    
      [ ![Azure Cost Management의 비용 분석을 보여 주는 스크린샷](../media/azure-netapp-files/cost-analysis.png) ](../media/azure-netapp-files/cost-analysis.png#lightbox)  

    2. 청구서를 다운로드하려면 **청구서** 를 선택한 다음, **다운로드** 단추를 선택합니다.   
      [ ![Azure Cost Management의 청구서를 보여 주는 스크린샷](../media/azure-netapp-files/azure-cost-invoices.png) ](../media/azure-netapp-files/azure-cost-invoices.png#lightbox)  

    1. 다운로드 창이 표시되면 사용량 세부 정보를 다운로드합니다. 다운로드한 `csv` 파일은 해당 리소스에 대한 용량 풀 청구 태그를 포함합니다.   
       ![Azure Cost Management의 다운로드 창을 보여 주는 스냅샷](../media/azure-netapp-files/invoice-download.png)   

       [ ![다운로드한 스프레드시트를 보여 주는 스크린샷](../media/azure-netapp-files/spreadsheet-download.png) ](../media/azure-netapp-files/spreadsheet-download.png#lightbox)

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md) 