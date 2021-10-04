---
title: Azure NetApp Files의 수동 QoS 용량 풀 | Microsoft Docs
description: 수동 QoS 용량 풀을 소개하고 추가 정보를 위한 참조를 제공합니다.
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 3ff9ed36800c60d50e70b328ab37606ddea09508
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407573"
---
# <a name="manual-qos-capacity-pool"></a>수동 QoS 용량 풀

이 문서에서는 수동 QoS(서비스 품질) 용량 풀 기능에 대해 소개합니다.

## <a name="how-manual-qos-differs-from-auto-qos"></a>수동 QoS와 자동 QoS의 차이점

[QoS 유형](azure-netapp-files-understand-storage-hierarchy.md#qos_types)은 용량 풀의 특성입니다. Azure NetApp Files는 용량 풀의 QoS 유형으로 자동(기본값) 및 수동 두 가지 유형을 제공합니다.  

'수동' QoS 용량 풀에서는 볼륨의 용량과 처리량을 독립적으로 할당할 수 있습니다. 최소 및 최대 처리량 수준에 대해서는 [Azure NetApp Files의 리소스 제한](azure-netapp-files-resource-limits.md#resource-limits)을 참조하세요. 수동 QoS 용량 풀로 만든 모든 볼륨의 총 처리량은 풀의 총 처리량으로 제한됩니다. 이는 풀 크기와 서비스 수준 처리량의 조합에 따라 결정됩니다. 

'자동' QoS 용량 풀에서 처리량은 볼륨에 할당된 크기 할당량에 비례하여 풀의 볼륨에 자동으로 할당됩니다.  

QoS 유형에 대한 고려 사항은 [Azure NetApp Files의 스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 및 [Azure NetApp Files에 대한 성능 고려 사항](azure-netapp-files-performance-considerations.md)을 참조하세요.

## <a name="example-of-using-manual-qos"></a>수동 QoS 사용 예

예를 들어 SAP HANA 시스템, Oracle 데이터베이스 또는 기타 여러 볼륨이 필요한 워크로드에 수동 QoS 용량 풀을 사용하는 경우 용량 풀을 사용하여 이러한 애플리케이션 볼륨을 만들 수 있습니다.  각 볼륨은 애플리케이션 요구 사항을 충족하기 위해 개별 크기 및 처리량을 제공할 수 있습니다.  이점에 대한 자세한 내용은 [수동 QoS 용량 풀에 포함된 볼륨의 처리량 제한 예](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool)를 참조하세요.  

## <a name="how-to-specify-the-manual-qos-type"></a>수동 QoS 유형을 지정하는 방법

[용량 풀을 만들](azure-netapp-files-set-up-capacity-pool.md) 때 용량 풀이 수동 QoS 유형을 사용하도록 지정할 수 있습니다.  수동 QoS 유형을 사용하도록 [기존 용량 풀을 변경](manage-manual-qos-capacity-pool.md#change-to-qos)할 수도 있습니다. 

용량 유형을 수동 QoS로 설정하는 것은 영구적 변경입니다. 수동 QoS 유형 용량 풀을 자동 QoS 용량 풀로 변환할 수는 없습니다. 

## <a name="next-steps"></a>다음 단계

* [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
* [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
* [스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md)
* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)
* [용량 풀 문제 해결](troubleshoot-capacity-pools.md)
