---
title: Azure NetApp Files 볼륨의 지역 간 복제 | Microsoft Docs
description: Azure NetApp Files 지역 간 복제의 기능이 수행하는 작업, 지원되는 지역 쌍, 서비스 수준 목표, 데이터 내구성 및 비용 모델에 대해 설명합니다.
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
ms.date: 10/20/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 3335260460a9d1358aeeb301fbebb5b7d4d8f4cb
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133434447"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨의 지역 간 복제

Azure NetApp Files 복제 기능은 지역 간 볼륨 복제를 통해 데이터 보호 기능을 제공합니다. 한 지역의 Azure NetApp Files 볼륨(원본)에서 다른 지역의 다른 Azure NetApp Files 볼륨(대상)으로 데이터를 비동기식으로 복제할 수 있습니다.  이 기능을 사용하면 지역 전체의 가동 중단 또는 재해가 발생할 경우 중요한 애플리케이션을 장애 조치(failover)할 수 있습니다.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>지원되는 지역 간 복제 쌍

Azure NetApp Files 볼륨 복제는 다양한 [Azure 지역 쌍](../availability-zones/cross-region-replication-azure.md#azure-cross-region-replication-pairings-for-all-geographies)과 비쌍 간에 지원됩니다. Azure NetApp Files 볼륨 복제는 현재 다음 지역에서 사용할 수 있습니다.  

### <a name="azure-regional-pairs"></a>Azure 지역 쌍

| Geography | 지역 쌍 A | 지역 쌍 B  |
|:--- |:--- |:--- |
| 오스트레일리아 | 오스트레일리아 동부 | 오스트레일리아 남동부 |
| 아시아 태평양 | 동아시아 | 동남아시아 |
| Canada | 캐나다 중부 | 캐나다 동부 |
| 유럽 | 북유럽 | 서유럽 |
| 독일 | 독일 중서부 | 독일 북부 |
| 인도 | 인도 중부 |인도 남부 |
| 일본 | 일본 동부 | 일본 서부 |
| 북아메리카 | 미국 동부 | 미국 서부 |
| 북아메리카 | 미국 동부 2 | 미국 중부 |
| 북아메리카 | 미국 중북부 | 미국 중남부|
| 노르웨이 | 노르웨이 동부 | 노르웨이 서부 |
| 스위스 | 스위스 북부 | 스위스 서부 |
| 영국 | 영국 남부 | 영국 서부 |
| 아랍에미리트연합국 | 아랍에미리트 북부 | 아랍에미리트 중부 |
| US Government | US Gov 애리조나 | US Gov 텍사스 |
| US Government | US Gov 버지니아 | US Gov 텍사스 |

### <a name="azure-regional-non-standard-pairs"></a>Azure 지역 비표준 쌍

| Geography | 지역 쌍 A | 지역 쌍 B  |
|:--- |:--- |:--- |
| 오스트레일리아/동남 아시아 | 오스트레일리아 동부 | 동남 아시아 |
| 독일/영국 | 독일 중서부 | 영국 남부 |
| 독일/유럽 | 독일 중서부 | 서유럽 | 
| 독일/프랑스 | 독일 중서부 | 프랑스 중부 |
| 북아메리카 | 미국 동부 | 미국 동부 2 |
| 북아메리카 | 미국 동부 2| 미국 서부 2 |
| 북아메리카 | 미국 중남부 | 미국 동부 |
| 북아메리카 | 미국 중남부 | 미국 동부 2 |
| 북아메리카 | 미국 중남부 | 미국 중부 |
| 북아메리카 | 미국 서부 2 | 미국 동부 |
| US Government | US Gov 애리조나 | US Gov 버지니아 |

## <a name="service-level-objectives"></a>서비스 수준 목표

RPO(복구 지점 목표)는 데이터를 복구할 수 있는 시점을 나타냅니다. RPO 대상은 일반적으로 복제 일정의 두 배 미만이지만 달라질 수 있습니다. 경우에 따라 총 데이터 세트 크기, 변경률, 데이터 덮어쓰기 비율, 전송에 사용할 수 있는 복제 대역폭 등의 요소에 따라 대상 RPO를 초과할 수 있습니다.   

* 10분 복제 일정의 경우 일반적인 RPO는 20분 미만입니다.  
* 매시간 복제 일정의 경우 일반적인 RPO는 2시간 미만입니다.  
* 매일 복제 일정의 경우 일반적인 RPO는 2일 미만입니다.  

RTO(복구 시간 목표) 또는 허용 가능한 최대 비즈니스 애플리케이션 가동 중지 시간은 애플리케이션을 실행하고 두 번째 사이트의 데이터에 대한 액세스를 제공하는 요인에 따라 결정됩니다. 피어링 관계를 분리하여 대상 볼륨을 활성화하고 두 번째 사이트에서 읽기 및 쓰기 데이터 액세스를 제공하는 RTO의 스토리지 부분은 1분 내에 완료될 것으로 예상됩니다.

## <a name="cost-model-for-cross-region-replication"></a>지역 간 복제에 대한 비용 모델  

Azure NetApp Files 지역 간 복제를 사용하면 복제하는 데이터의 양에 대해서만 비용을 지불하게 됩니다. 설정 요금 또는 최소 사용 요금은 없습니다. 복제 가격은 초기 복제 구성 중에 선택한 *대상* 볼륨의 복제 빈도와 지역을 기준으로 합니다. 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 페이지를 참조하세요.  

일반 Azure NetApp Files 스토리지 용량 요금은 복제 대상 볼륨(*데이터 보호* 볼륨이라고도 함)에 적용됩니다. 

### <a name="pricing-examples"></a>가격 책정 예제

해당 월에 청구되는 지역 간 복제 요금은 해당 월에 지역 간 복제 기능을 통해 복제된 데이터의 양을 기준으로 합니다. 복제되는 데이터의 양은 GiB로 측정됩니다. 원본 볼륨에서 대상 볼륨으로, 대상 볼륨의 모든 정기적 복제와 대상 볼륨에서 원본 볼륨으로의 모든 다시 동기화 복제 중에 두 지역 간에 복제되는 데이터의 합계를 나타냅니다.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>예제 1: 월 1의 기준 복제 및 증분 복제

다음과 같은 상황을 가정해 보겠습니다.

* *원본* 볼륨은 Azure NetApp Files *Premium* 서비스 수준에서 가져온 것입니다. 볼륨 할당량 크기가 1000GiB이고, 1일부터 500GiB의 볼륨이 사용됩니다. 볼륨이 *미국 중남부* 지역에 있습니다.
* *대상* 볼륨은 Azure NetApp Files *Standard* 서비스 수준에서 가져온 것입니다. *미국 동부 2* 지역에 있습니다.
* 위의 두 볼륨 간에 *매시간* 기반 지역 간 복제를 구성했습니다. 따라서 복제 가격은 GiB당 $0.12입니다.
* 간단히 하기 위해 원본 볼륨은 1시간마다 0.5GiB 데이터가 변경되지만 사용된 총 볼륨 크기는 증가하지 않는 것으로 가정합니다(500GiB 유지). 

초기 설치 후에는 기준 복제가 즉시 수행됩니다.  

* 기준 복제 중에 복제된 데이터 양: `500 GiB`
* 기준 복제 요금: `500 GiB * $0.12 = $60`

기준 복제 후에는 변경된 블록만 복제됩니다. 따라서 0.5GiB 데이터는 후속 증분 복제에서 1시간마다 복제됩니다.

* 한달 30일 동안의 증분 복제를 통해 복제된 데이터 양 합계: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* 증분 복제 요금: `360 GiB * $0.12 = $43.2`

월 1의 말까지 총 지역 간 복제 요금은 다음과 같습니다.  

*  월 1의 총 지역 간 복제 요금: `$60 + $43.2 = $103.2`

일반 Azure NetApp Files 스토리지 용량 요금이 대상 볼륨에 적용됩니다. 그러나 대상 볼륨은 원본 볼륨 계층과 다른(더 저렴한) 스토리지 계층을 사용할 수 있습니다.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>예제 2: 월 2의 증분 복제 및 다시 동기화 복제  

예제 1의 설명에 따라 원본 볼륨, 대상 볼륨 및 두 설정 간의 복제 관계가 있다고 가정합니다. 월 2(30일로 구성)의 29일 동안 매시간 복제가 예상대로 발생합니다.

* 29일 동안의 증분 복제를 통해 복제된 데이터 양 합계: `0.5 GiB * 24 hours * 29 days = 348 GiB`

해당 월의 마지막 날에 원본 지역에서 계획되지 않은 중단이 발생하고 대상 볼륨으로 장애 조치(failover)되었다고 가정합니다. 2시간 후에 원본 지역이 복구되고 대상 볼륨에서 원본 볼륨으로 다시 동기화 복제가 수행되었습니다. 2시간 동안 0.8GiB는 대상 볼륨에서 데이터 변경이 발생했으며 원본과 재동기화 해야 합니다.

* 마지막 날의 22시간 동안 일반 복제를 통해 복제된 데이터 양 합계: `0.5 GiB * 22 hours = 11 GiB`
* 한 번의 다시 동기화 복제 중에 복제된 데이터 양: `0.8 GiB`

따라서 월 2의 말까지 지역 간 총 복제 요금은 다음과 같습니다.  

* 월 2의 총 지역 간 복제 요금: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

월 2에 대한 일반 Azure NetApp Files 스토리지 용량 요금이 대상 볼륨에 적용됩니다.

## <a name="next-steps"></a>다음 단계
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [볼륨 복제 만들기](cross-region-replication-create-peering.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [지역 간 복제 대상 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [볼륨 복제 또는 볼륨 삭제](cross-region-replication-delete.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)
