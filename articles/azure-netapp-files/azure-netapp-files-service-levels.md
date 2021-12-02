---
title: Azure NetApp Files의 서비스 수준 | Microsoft Docs
description: Azure NetApp Files의 서비스 수준에 대한 처리량 성능을 설명합니다.
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
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: b-hchen
ms.openlocfilehash: a8fc2b8a588d9f980b9484c4004a9488107f4bd1
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484204"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
서비스 수준은 용량 풀의 특성입니다. 서비스 수준은 볼륨에 할당된 할당량에 따라 용량 풀의 볼륨에 허용되는 최대 처리량으로 정의되고 구별됩니다.

## <a name="supported-service-levels"></a>지원되는 서비스 수준

Azure NetApp Files는 *Ultra*, *Premium*, *Standard* 의 세 가지 서비스 수준을 지원합니다. 

* <a name="Ultra"></a>Ultra Storage

    Ultra Storage 계층은 프로비전된 용량 1TiB당 최대 128MiB/s의 처리량을 제공합니다. 

* <a name="Premium"></a>Premium Storage

    Premium Storage 계층은 프로비전된 용량 1TiB당 최대 64MiB/s의 처리량을 제공합니다. 

* <a name="Standard"></a>Standard Storage

    Standard Storage 계층은 프로비전된 용량 1TiB당 최대 16MiB/s의 처리량을 제공합니다.

## <a name="throughput-limits"></a>처리량 한도

볼륨 처리량 제한은 다음 요소의 조합에 따라 결정됩니다.
* 볼륨이 속한 용량 풀의 서비스 수준
* 볼륨에 할당된 할당량  
* 용량 풀의 QoS 유형(*자동* 또는 *수동*)  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>자동 QoS 용량 풀의 볼륨 처리량 제한 예제

다음 다이어그램은 자동 QoS 용량 풀에 있는 볼륨의 처리량 제한 예제를 보여 줍니다.

![서비스 수준 그림](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 예제 1에서는 2TiB의 할당량이 할당된 Premium Storage 계층이 있는 자동 QoS 용량 풀의 볼륨에 128MiB/s(2TiB * 64MiB/s)의 처리량 제한이 할당됩니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 소비량에 관계없이 적용됩니다.

* 예제 2에서는 할당량 100GiB가 할당된 Premium Storage 계층이 있는 자동 QoS 용량 풀의 볼륨에 6.25MiB/s(0.09765625TiB * 64MiB/s)의 처리량 제한이 할당됩니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 소비량에 관계없이 적용됩니다.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀의 볼륨 처리량 제한 예제 

수동 QoS 용량 풀을 사용하는 경우 볼륨의 용량과 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀에서 볼륨을 만들 때 처리량(MiB/S) 값을 지정할 수 있습니다. 수동 QoS 용량 풀의 볼륨에 할당되는 총 처리량은 풀의 크기와 서비스 수준에 따라 달라집니다. (TiB x 서비스 수준 처리량/TiB의 용량 풀 크기)에 따라 제한됩니다. 예를 들어, Ultra 서비스 수준인 10TiB 용량 풀에는 해당 볼륨에 사용할 수 있는 총 1280MiB/s(10TiB x 128MiB/s/TiB)의 처리량 용량이 있습니다.

예컨대 SAP HANA 시스템의 경우 이 용량 풀을 사용하여 다음 볼륨을 만들 수 있습니다. 각 볼륨은 애플리케이션 요구 사항을 충족하기 위한 개별 크기와 처리량을 제공합니다.

* SAP HANA 데이터 볼륨: 크기 4TiB, 최대 704MiB/s
* SAP HANA 로그 볼륨: 크기 0.5TiB, 최대 256MiB/s
* SAP HANA 공유 볼륨: 크기 1TiB, 최대 64MiB/s
* SAP HANA 백업 볼륨: 크기 4.5TiB, 최대 256MiB/s

다음 다이어그램은 SAP HANA 볼륨의 시나리오를 보여 줍니다.

![QoS SAP HANA 볼륨 시나리오](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md) 
- [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files에 대한 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 
- [지역 간 복제에 대한 서비스 수준 목표](cross-region-replication-introduction.md#service-level-objectives)
