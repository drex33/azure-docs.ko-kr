---
title: Azure NetApp Files에 대한 수동 QoS 용량 풀 관리 | Microsoft Docs
description: 수동 QoS 용량 풀을 설정하고 수동 QoS를 사용하도록 용량 풀을 변경하는 등 수동 QoS 유형을 사용하는 용량 풀을 관리하는 방법을 설명합니다.
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
ms.date: 06/14/2021
ms.author: b-hchen
ms.openlocfilehash: aeeec32105f8b07b29743db475647c7c502bb72b
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480894"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀 관리

이 문서에서는 수동 QoS 유형을 사용하는 용량 풀을 관리하는 방법을 설명합니다.  

QoS 유형에 대한 고려 사항을 이해하려면 [Azure NetApp Files의 스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 및 [Azure NetApp Files에 대한 성능 고려 사항](azure-netapp-files-performance-considerations.md)을 참조하세요.  

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>새로운 수동 QoS 용량 풀 설정 

수동 QoS 유형을 사용하여 새 용량 풀을 만들려면 다음을 수행합니다.

1. [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)의 단계를 수행 합니다.  

2. 새 용량 풀 창에서 **수동 QoS** 유형을 선택합니다.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>수동 QoS를 사용하도록 용량 풀 변경

현재 자동 QoS 유형을 사용하는 용량 풀을 변경하여 수동 QoS 유형을 사용할 수 있습니다.  

> [!IMPORTANT]
> 용량 유형을 수동 QoS로 설정하는 것은 영구적 변경입니다. 수동 QoS 유형 용량 풀을 자동 QoS 용량 풀로 변환할 수는 없습니다.  
> 변환 시 처리량 수준은 수동 QoS 유형의 볼륨에 대한 처리량 제한에 따라 제한될 수 있습니다. [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md#resource-limits)을 참조하세요.

1. NetApp 계정의 관리 블레이드에서 **용량 풀** 을 클릭하여 기존 용량 풀을 표시합니다.   
 
2.  수동 QoS를 사용하여 변경할 용량 풀을 클릭합니다.

3.  **QoS 유형 변경** 을 클릭합니다. 그런 다음, **새 QoS 유형** 을 **수동** 으로 설정합니다. **확인** 을 클릭합니다. 

![QoS 유형 변경](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀의 처리량 모니터링  

볼륨의 읽기 및 쓰기 처리량을 모니터링하는 데 도움이 되는 메트릭을 사용할 수 있습니다.  [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)을 참조하세요.  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>수동 QoS 볼륨의 할당된 처리량 수정 

볼륨이 수동 QoS 용량 풀에 포함되어 있는 경우 필요에 따라 할당된 볼륨 처리량을 수정할 수 있습니다.

1. **볼륨** 페이지에서 처리량을 수정할 볼륨을 선택합니다.   

2. **처리량 변경** 을 클릭합니다. 원하는 **처리량(MiB/S)** 을 지정합니다. **확인** 을 클릭합니다. 

    ![QoS 처리량 변경](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>다음 단계  

* [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)
* [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md)
* [용량 풀 문제 해결](troubleshoot-capacity-pools.md)
* [Azure NetApp Files의 스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
