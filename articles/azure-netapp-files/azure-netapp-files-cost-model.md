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
ms.date: 11/08/2021
ms.author: b-juche
ms.openlocfilehash: 56091fb0c6ddfa205fc60f6aef06397a0314369e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053163"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp Files의 비용 모델을 이해하면 서비스 비용을 관리하는 데 도움이 됩니다. 

지역 간 복제와 관련된 비용 모델은 [지역 간 복제의 비용 모델](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)을 참조하세요.

## <a name="calculation-of-capacity-consumption"></a>사용량 계산

Azure NetApp Files는 프로 비전 된 저장소 용량에 대해 요금이 청구 되며 용량 풀을 만들어 할당 합니다. 용량 풀은 시간당 할당 된 GiB 설정 된 비용을 기준으로 매월 청구 됩니다. 용량 풀 할당은 시간별로 측정 됩니다.  

용량 풀은 4 TiB 이상 이어야 하며 TiB 간격으로 늘리거나 줄일 수 있습니다. 용량 풀에는 100 GiB 이상에서 최대 100 TiB 까지의 크기를 포함 하는 볼륨이 포함 되어 있습니다. 볼륨에는 용량 풀의 프로 비전 된 크기에서 차감 된 할당량이 할당 됩니다. 활성 볼륨의 경우 할당량에 대 한 용량 소비량은 활성 파일 시스템 데이터 또는 스냅숏 데이터에 해당 하는 논리적 (유효) 용량을 기반으로 합니다. 자세한 내용은 [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)을 참조하세요. 

### <a name="pricing-examples"></a>가격 책정 예제

이 섹션에서는 Azure NetApp Files 비용 모델을 이해 하는 데 도움이 되는 예를 보여 줍니다.

#### <a name="example-1-one-month-cost-with-static-versus-dynamic-capacity-pool-provisioning"></a>예 1: 정적 및 동적 용량 풀을 프로 비전 하는 1 개월 비용 

용량 풀 크기 요구 사항 (예: 용량 또는 성능 요구 사항)이 변동 경우 [볼륨 및 용량 풀의 크기를 동적으로 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md) 하 여 용량 및 성능 요구 사항에 따라 비용을 분산 하는 것이 좋습니다.

예를 들어 24 TiB의 24 시간 (1 일) 96에 Premium 용량 24 시간 (1 일), 24 5 TiB, 480 시간 (20 일), 6 TiB, 월의 남은 시간을 0 TiB에 사용 합니다. 동적 클라우드 소비 배포 프로필은 기존의 정적 온-프레미스 사용량 프로필과 다르게 보입니다. 

[![동적 및 정적 용량 풀 프로 비전을 보여 주는 가로 막대형 차트입니다. ](../media/azure-netapp-files/cost-model-example-one-capacity.png) ](../media/azure-netapp-files/cost-model-example-one-capacity.png#lightbox)

비용은 GiB/시간당 $0.000403 ([지역에 따라 가격 책정](https://azure.microsoft.com/pricing/details/netapp/))로 청구 되는 경우 월별 비용 분석은 다음과 같습니다.

*Premium에 대 한 정적 프로 비전 (최대 용량/성능)*

* 24 TiB x 720 시간 x $0.000403 GiB/시간당 = $7130.97 (하루 $237.70) 

*볼륨 및 용량 풀 크기 조정을 사용 하 여 동적 프로 비전* 

* 10 TiB x 24 시간 x $0.000403/시간 = $99.04
* 24 TiB x 96 시간 x $0.000403 GiB/시간 = $950.80
* 6 TiB x 480 시간 x $0.000403 GiB/시간 = $1188.50
* 합계 = **$2238.33**

[![정적 및 동적 서비스 수준 비용 모델을 보여 주는 가로 막대형 차트입니다. ](../media/azure-netapp-files/cost-model-example-one-pricing.png) ](../media/azure-netapp-files/cost-model-example-one-pricing.png#lightbox)

이 시나리오는 고정 프로 비전에 비해 $4892.64의 월별 절감 액을 구성 합니다.

#### <a name="example-2-one-month-cost-with-and-without-dynamic-service-level-change"></a>예 2: 동적 서비스 수준 변경을 사용 하거나 사용 하지 않는 1 개월 비용

용량 풀 크기 요구 사항이 동일 하 게 유지 되지만 성능 요구 사항이 변동 [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)하는 것이 좋습니다. 해당 월에 다양 한 형식의 용량 풀을 프로 비전 하 고 프로 비전 해제 하 여 just-in-time 성능을 제공 하 고 성능이 필요 하지 않은 기간 동안 비용을 절감할 수 있습니다. 

용량 요구 사항이 일정 24 TiB 시나리오를 고려해 보세요. 그러나 성능 요구 사항은 384 시간 (16 일)의 표준 서비스 수준, 120 시간 (5 일) Premium 서비스 수준, 168 시간 (7 일), 초소형 서비스 수준, 표준 서비스 수준 성능의 48 시간 (2 일) 사이에 변동 됩니다. 이 시나리오에서 동적 클라우드 소비 배포 프로필은 기존의 정적 온-프레미스 소비 프로필에 비해 다르게 보입니다. 

[![동적 서비스 수준 변경을 사용 하거나 사용 하지 않고 프로 비전을 보여 주는 가로 막대형 차트입니다. ](../media/azure-netapp-files/cost-model-example-two-capacity.png) ](../media/azure-netapp-files/cost-model-example-two-capacity.png#lightbox)

이 경우 비용이 GiB/hour (표준), $0.000403 GiB/hour (Premium) 및 $0.000538 per GiB/hour ([지역에 따라 가격 책정](https://azure.microsoft.com/pricing/details/netapp/))로 $0.000202 청구 되는 경우 월별 비용 분석은 다음과 같습니다. 

*Ultra service 수준에서 정적 프로 비전 (최대 성능)*

* 24 TiB x 720 시간 x $0.000538 GiB/시간당 = $9519.76 (하루 $317.33) 
 
*동적 서비스 수준 변경을 사용한 동적 프로 비전*

* 24 TiB x 384 시간 x $0.000202 GiB/시간 = $1901.31  
* 24 TiB x 120 시간 x $0.000403 GiB/시간 = $1188.50  
* 24 TiB x 168 시간 x $0.000538 GiB/시간 = $2221.28  
* 24 TiB x 48 시간 x $0.000202 GiB/시간 = $238.29 
* 합계 = **$5554.37** 

[![정적 및 동적 서비스 수준 변경 비용 모델을 보여 주는 가로 막대형 차트입니다. ](../media/azure-netapp-files/cost-model-example-two-pricing.png) ](../media/azure-netapp-files/cost-model-example-two-pricing.png#lightbox)

이 시나리오는 고정 프로 비전에 비해 $3965.39의 월별 절감 액을 구성 합니다.

## <a name="capacity-consumption-of-snapshots"></a>스냅샷의 사용량 

Azure NetApp Files의 스냅샷 사용량은 부모 볼륨의 할당량 대비 요금이 청구됩니다.  따라서 볼륨이 속한 용량 풀과 동일한 청구 요금을 공유합니다.  그러나 활성 볼륨과 달리 스냅샷 사용량은 증분 사용량을 기준으로 측정됩니다.  Azure NetApp Files 스냅샷은 특성상 차등 스냅샷입니다. 데이터 변경률에 따라 스냅샷은 활성 볼륨의 논리적 용량보다 훨씬 적은 용량을 사용하는 경우가 많습니다. 예를 들어 500GiB 볼륨 스냅샷에 10GiB 차등 데이터만 포함되어 있다고 가정합니다. 

활성 파일 시스템 및 스냅샷의 볼륨 할당량에 계산되는 용량 사용량은 1000GiB가 아닌 510GiB입니다. 일반적으로 스냅샷 빈도, 애플리케이션 일일 블록 수준 변경 비율에 따라, 용량의 20%가 한 주 분량의 스냅샷 데이터를 유지한다고 가정하는 것을 추천합니다. 

다음 다이어그램은 이 개념을 보여 줍니다. 

* 프로비저닝된 용량이 40TiB인 용량 풀이 있다고 가정합니다. 이 풀에는 세 볼륨이 포함되어 있습니다.    
    * 볼륨 1에는 20 TiB의 할당량이 할당 되 고 13 개의 TiB (12 TiB 활성, 1-TiB 스냅숏) 사용이 할당 됩니다.
    * 볼륨 2의 할당량은 1TiB이고 사용량은 450GiB입니다.
    * 볼륨 3에는 14 TiB의 할당량이 할당 되지만 8.8 TiB (8 TiB 활성, 800-GiB 스냅숏)이 할당 됩니다.   
* 용량 풀은 40TiB 용량(프로비전 용량)으로 요금이 청구됩니다. 22.25 TiB의 용량이 사용 됩니다 (TiB, 450 GiB 및 8.8 TiB 볼륨 1, 2, 3). 용량 풀에는 17.75TiB의 용량이 남아 있습니다.   

[![볼륨이 ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png) 3 개인 용량 풀을 보여 주는 다이어그램 ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png#lightbox)

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
* [Azure NetApp Files 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=netapp)
