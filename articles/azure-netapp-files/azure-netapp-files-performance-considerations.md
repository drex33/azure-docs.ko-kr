---
title: Azure NetApp Files의 성능 고려 사항 | Microsoft Docs
description: 할당량 및 처리량 한도의 관계와 볼륨 할당량을 동적으로 늘리거나 줄이는 방법을 포함하여 Azure NetApp Files의 성능에 대해 알아봅니다.
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
ms.date: 02/19/2021
ms.author: b-hchen
ms.openlocfilehash: 4ecf8e95c6e5ce19ab73284831fad714259217b1
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484907"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 고려 사항

자동 QoS가 포함된 볼륨의 [처리량 한도](azure-netapp-files-service-levels.md)는 볼륨 및 선택한 서비스 수준에 할당된 할당량의 조합에 따라 결정됩니다. 수동 QoS가 포함된 볼륨의 경우 처리량 한도는 개별적으로 정의할 수 있습니다. Azure NetApp Files에 대한 성능 계획을 세울 때 몇 가지 고려 사항을 이해해야 합니다. 

## <a name="quota-and-throughput"></a>할당량 및 처리량  

처리량 한도는 현실화될 실제 성능의 한 가지 결정 요인에 불과합니다.  

읽기 및 쓰기 조합, 전송 크기, 임의 또는 순차 패턴을 포함하는 일반적인 성능 고려 사항과 다른 많은 요인이 제공되는 전체 성능에 영향을 줍니다.  

테스트에서 관찰된 최대 경험적 처리량은 4500MiB/s입니다.  Premium Storage 계층에서 70.31TiB 자동 QoS 볼륨 할당량은 이 수준의 성능을 얻기에 충분한 처리량 제한을 프로비전합니다.  

자동 QoS 볼륨의 경우 70.31TiB 이상 볼륨 할당량을 할당하는 것을 고려하면 추가 데이터를 저장하기 위해 볼륨에 추가 할당량이 할당될 수 있습니다. 그러나 추가된 할당량으로 인해 실제 처리량이 증가하지 않습니다.  

수동 QoS를 사용하는 볼륨에는 동일한 경험적 처리량 상한이 적용됩니다. 볼륨에 할당할 수 있는 최대 처리량은 4500MiB/s입니다.

## <a name="automatic-qos-volume-quota-and-throughput"></a>자동 QoS 볼륨 할당량 및 처리량

이 섹션에서는 자동 QoS 유형을 사용하는 볼륨의 할당량 관리 및 처리량에 대해 설명합니다.

### <a name="overprovisioning-the-volume-quota"></a>볼륨 할당량 오버프로비전

워크로드의 성능이 처리량 제한에 구속되는 경우 자동 QoS 볼륨 할당량을 오버프로비전하여 더 높은 처리량 수준을 설정하고 더 높은 성능을 달성할 수 있습니다.  

예를 들어 Premium Storage 계층의 자동 QoS 볼륨에 500GiB의 데이터만 있지만 128MiB/s의 처리량이 필요한 경우 처리량 수준이 적절하게 설정되도록 할당량을 2TiB로 설정할 수 있습니다(TB당 64MiB/s * 2TiB = 128MiB/s).  

더 높은 처리량을 달성하기 위해 볼륨을 지속적으로 오버프로비전하는 경우 수동 QoS 볼륨을 사용하거나 더 높은 서비스 수준을 대신 사용하는 것이 좋습니다.  위의 예에서는 대신 Ultra 스토리지 계층을 사용하여 자동 QoS 볼륨 할당량의 절반으로 동일한 처리량 제한을 달성할 수 있습니다(TiB당 128MiB/s * 1TiB = 128MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>볼륨 할당량을 동적으로 늘리거나 줄이기

성능 요구 사항이 일시적이거나 고정된 기간 동안 성능 요구가 증가한 경우 볼륨 할당량을 동적으로 늘리거나 줄여서 처리량 제한을 즉시 조정할 수 있습니다.  다음 고려 사항에 유의하세요. 

* IO를 일시 중지할 필요 없이 볼륨 할당량을 늘리거나 줄일 수 있으며, 볼륨에 대한 액세스가 중단되거나 영향을 받지 않습니다.  

    볼륨에 대해 활성 I/O 트랜잭션 중에 할당량을 조정할 수 있습니다.  볼륨 할당량은 볼륨에 저장된 논리적 데이터의 양 이하로 감소할 수 없습니다.

* 볼륨 할당량이 변경되면 해당하는 처리량 제한 변경이 거의 즉시 이루어집니다. 

    변경 내용은 볼륨 액세스 또는 I/O를 중단하거나 영향을 주지 않습니다.  

* 볼륨 할당량을 조정하려면 용량 풀 크기를 변경해야 할 수 있습니다.  

    용량 풀 크기는 볼륨 가용성 또는 I/O에 영향을 주지 않고 동적으로 조정할 수 있습니다.

## <a name="manual-qos-volume-quota-and-throughput"></a>수동 QoS 볼륨 할당량 및 처리량 

수동 QoS 볼륨을 사용하는 경우 각 볼륨에 처리량을 독립적으로 할당할 수 있으므로 더 높은 처리량을 달성하기 위해 볼륨 할당량을 오버프로비전할 필요가 없습니다. 그러나 성능 요구 사항에 충분한 처리량으로 용량 풀이 미리 프로비전되어 있는지 확인해야 합니다. 용량 풀의 처리량은 크기 및 서비스 수준에 따라 프로비전됩니다. 자세한 내용은 [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files 성능 계산기](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Linux용 성능 벤치마크](performance-benchmarks-linux.md)
