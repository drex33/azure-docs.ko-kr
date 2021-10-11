---
title: Azure NetApp Files의 스토리지 계층 구조 | Microsoft Docs
description: Azure NetApp Files 계정, 용량 풀 및 볼륨을 포함하는 스토리지 계층 구조를 설명합니다.
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
ms.topic: overview
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 41ccd31a5e42b2765ffa778dc347cc848090ffee
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407516"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files의 스토리지 계층 구조

Azure NetApp Files에서 볼륨을 만들기 전에 프로비전된 용량에 대한 풀을 구매하고 설정해야 합니다.  용량 풀을 설정하려면 NetApp 계정이 있어야 합니다. 스토리지 계층 구조를 이해하는 것은 Azure NetApp Files 리소스를 설정하고 관리하는 데 도움이 됩니다.

> [!IMPORTANT] 
> Azure NetApp Files는 현재 구독 간의 리소스 마이그레이션을 지원하지 않습니다.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp 계정

- NetApp 계정은 구성 용량 풀의 관리 그룹으로 사용됩니다.  
- NetApp 계정은 일반 Azure Storage 계정과 동일하지 않습니다. 
- NetApp 계정은 범위에서 지역적입니다.   
- 지역에서 여러 NetApp 계정을 가질 수 있지만 각 NetApp 계정은 단일 지역에만 연결됩니다.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>용량 풀

용량 풀의 작동 방식을 이해하면 스토리지 요구 사항에 적합한 용량 풀 유형을 선택할 수 있습니다. 

### <a name="general-rules-of-capacity-pools"></a>용량 풀의 일반 규칙

- 용량 풀은 해당 프로비전된 용량에 의해 측정됩니다.   
    추가 정보는 [QoS 유형](#qos_types)을 참조하세요.  
- 용량은 구입한 고정 SKU(예: 4TiB 용량)를 통해 프로비전됩니다.
- 용량 풀은 하나의 서비스 수준만 가질 수 있습니다.  
- 각 용량 풀은 하나의 NetApp 계정에만 속할 수 있습니다. 그러나 NetApp 계정에는 여러 개의 용량 풀이 있을 수 있습니다.  
- 용량 풀은 NetApp 계정 간에 이동할 수 없습니다.   
  예를 들어 아래 [스토리지 계층 구조의 개념 다이어그램](#conceptual_diagram_of_storage_hierarchy)에서 용량 풀 1은 미국 동부 NetApp 계정에서 미국 서부 2 NetApp 계정으로 이동할 수 없습니다.  
- 용량 풀은 용량 풀 내 모든 볼륨이 삭제되기 전까지 삭제할 수 없습니다.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>용량 풀의 QoS(서비스 품질) 유형

QoS 유형은 용량 풀의 특성입니다. Azure NetApp Files는 용량 풀의 두 가지 QoS 유형을 제공합니다. 

- *자동(또는 자동)* QoS 유형  

    용량 풀을 만들 때 기본 QoS 유형은 자동입니다.

    자동 QoS 용량 풀에서 처리량은 볼륨에 할당된 크기 할당량에 비례하여 풀의 볼륨에 자동으로 할당됩니다. 

    볼륨에 할당된 최대 처리량은 용량 풀의 서비스 수준과 볼륨의 크기 할당량에 따라 달라집니다. 예를 들어 [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)을 참조하세요.

- <a name="manual_qos_type"></a>*수동* QoS 유형  

    용량 풀에 수동 QoS 유형을 사용하는 옵션이 있습니다.

    수동 QoS 용량 풀에서는 볼륨에 대한 용량 및 처리량을 독립적으로 할당할 수 있습니다. 수동 QoS 용량 풀로 만든 모든 볼륨의 총 처리량은 풀의 총 처리량으로 제한됩니다.  이는 풀 크기와 서비스 수준 처리량의 조합에 따라 결정됩니다. 

    예를 들어, Ultra 서비스 수준인 4TiB 용량 풀에는 해당 볼륨에 사용할 수 있는 총 512MiB/s(4TiB x 128MiB/s/TiB)의 처리량 용량이 있습니다.


## <a name="volumes"></a><a name="volumes"></a>볼륨

- 볼륨은 논리적 용량 사용량으로 측정되며 확장 가능합니다. 
- 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.
- 볼륨의 처리량 사용은 풀의 사용 가능한 처리량에 대해 계산됩니다. [수동 QoS 유형](#manual_qos_type)을 참조하세요.
- 각 볼륨은 하나의 풀에만 속하지만 풀은 여러 볼륨을 포함할 수 있습니다. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>스토리지 계층 구조의 개념 다이어그램 
다음 예제에서는 Azure 구독, NetApp 계정, 용량 풀 및 볼륨의 관계를 보여줍니다.   

![스토리지 계층 구조의 개념 다이어그램](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md)
- [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
