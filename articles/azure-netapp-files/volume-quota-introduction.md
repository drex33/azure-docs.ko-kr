---
title: Azure NetApp Files에 대한 볼륨 할당량 이해 | Microsoft Docs
description: 볼륨 할당량에 대한 개요를 제공합니다. 또한 볼륨 및 풀 용량 모니터링 및 관리에 대한 참조를 제공합니다.
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
ms.date: 04/30/2021
ms.author: b-hchen
ms.openlocfilehash: 88ae027897dc1a718186da20632ce0c418cd4b68
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486906"
---
# <a name="understand-volume-quota"></a>볼륨 할당량 이해

이 문서는 Azure NetApp Files에 대한 볼륨 할당량에 대한 개요를 제공합니다. 또한 볼륨 또는 용량 풀의 용량을 모니터링하고 관리하는 데 참조할 수 있는 세부 정보를 제공합니다.  

## <a name="behaviors-of-volume-quota"></a>볼륨 할당량의 동작 

* Azure NetApp Files 볼륨의 저장소 용량은 볼륨의 집합 크기(할당량)로 제한됩니다. 

* 볼륨 소비가 최대를 초과하면 볼륨과 기본 용량 풀이 자동으로 증가하지 않습니다. 대신 볼륨이 "공간 부족" 상태로 됩니다. 그러나 필요에 따라 [용량 풀 또는 볼륨의 크기를 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)할 수 있습니다. 적극적으로 기본 용량 풀 및 [볼륨의 용량을 모니터링](monitor-volume-capacity.md)해야 합니다.

* 용량 풀 유형에 따라 Azure NetApp Files 볼륨의 크기(할당량)는 대역폭 성능 및 프로비저닝된 용량에 영향을 줍니다.  세부 정보는 [자동 QoS 풀 유형](azure-netapp-files-understand-storage-hierarchy.md#qos_types)을 참조하세요. 

* 볼륨 [스냅샷](snapshots-introduction.md)으로 소비된 용량은 볼륨의 프로비저닝된 공간에 포함됩니다. 

* 볼륨 할당량은 [복제 대상 볼륨](cross-region-replication-introduction.md)에 적용되지 않습니다.

* 용량 소비의 계산과 용량 소비 초과분에 대한 [Azure NetApp Files용 비용 모델](azure-netapp-files-cost-model.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [볼륨 용량 모니터링](monitor-volume-capacity.md)
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [용량 관리 FAQ](faq-capacity-management.md)