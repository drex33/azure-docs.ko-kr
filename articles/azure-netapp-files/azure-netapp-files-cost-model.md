---
title: Azure NetApp Files의 비용 모델 | Microsoft Docs
description: 서비스 비용을 관리하기 위한 Azure NetApp Files의 비용 모델에 대해 설명합니다.
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
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 8b2fa319237c31db2e40cc602c17691e60384d13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265153"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp Files의 비용 모델을 이해하면 서비스 비용을 관리하는 데 도움이 됩니다. 

지역 간 복제와 관련된 비용 모델은 [지역 간 복제의 비용 모델](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)을 참조하세요.

## <a name="calculation-of-capacity-consumption"></a>사용량 계산

Azure NetApp Files는 프로비저닝된 스토리지 용량에 대해 요금이 청구됩니다.  프로비저닝된 용량은 용량 풀을 만들어 할당됩니다.  용량 풀은 시간당 증분 단위로 월 $/provisioned-GiB/의 요금이 청구됩니다. 단일 용량 풀의 최소 크기는 4TiB이며 나중에 1TiB 증분으로 용량 풀을 확장할 수 있습니다. 볼륨은 용량 풀 안에 생성됩니다.  각 볼륨에 할당된 할당량만큼 풀에 프로비저닝된 용량에서 차감됩니다. 볼륨에 할당할 수 있는 할당량은 최소 100GiB에서 최대 100TiB 사이입니다.  

활성 볼륨의 경우 할당량에 대한 용량 소비는 활성 파일 시스템 또는 스냅샷 데이터의 논리적(유효) 용량을 기반으로 합니다. 볼륨에는 설정된 크기(할당량)만큼의 데이터만 포함될 수 있습니다.

프로비전된 용량 대비 용량 풀에서 사용된 총 용량은 풀 내 모든 볼륨의 실제 사용량 합계입니다. 

   ![사용되는 전체 용량 계산을 나타내는 식입니다.](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

## <a name="capacity-consumption-of-snapshots"></a>스냅샷의 사용량 

Azure NetApp Files의 스냅샷 사용량은 부모 볼륨의 할당량 대비 요금이 청구됩니다.  따라서 볼륨이 속한 용량 풀과 동일한 청구 요금을 공유합니다.  그러나 활성 볼륨과 달리 스냅샷 사용량은 증분 사용량을 기준으로 측정됩니다.  Azure NetApp Files 스냅샷은 특성상 차등 스냅샷입니다. 데이터 변경률에 따라 스냅샷은 활성 볼륨의 논리적 용량보다 훨씬 적은 용량을 사용하는 경우가 많습니다. 예를 들어 500GiB 볼륨 스냅샷에 10GiB 차등 데이터만 포함되어 있다고 가정합니다. 활성 파일 시스템 및 스냅샷의 볼륨 할당량에 계산되는 용량 사용량은 1000GiB가 아닌 510GiB입니다. 일반적으로 스냅샷 빈도, 애플리케이션 일일 블록 수준 변경 비율에 따라, 용량의 20%가 한 주 분량의 스냅샷 데이터를 유지한다고 가정하는 것을 추천합니다. 

다음 다이어그램은 이 개념을 보여 줍니다. 

* 프로비저닝된 용량이 40TiB인 용량 풀이 있다고 가정합니다. 이 풀에는 세 볼륨이 포함되어 있습니다.    
    * 볼륨 1의 할당량은 20TiB이고 사용량은 13TiB(12TiB 활성, 1TiB 스냅샷)입니다.
    * 볼륨 2의 할당량은 1TiB이고 사용량은 450GiB입니다.
    * 볼륨 3의 할당량은 14TiB이나 사용량은 8.8TiB(8TiB 활성, 800GiB 스냅샷)입니다.   
* 용량 풀은 40TiB 용량(프로비전 용량)으로 요금이 청구됩니다. 22.25TiB의 용량이 소비됩니다(볼륨 1, 2 및 3에서 13TiB, 450GiB 및 8.8TiB 할당량). 용량 풀에는 17.75TiB의 용량이 남아 있습니다.   

![3개의 볼륨이 있는 용량 풀을 보여 주는 다이어그램](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [지역 간 복제에 대한 비용 모델](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [볼륨 할당량 이해](volume-quota-introduction.md)
* [볼륨 용량 모니터링](monitor-volume-capacity.md)
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [태그를 사용하여 청구 관리](manage-billing-tags.md)
* [용량 관리 FAQ](faq-capacity-management.md)
