---
title: Azure NetApp Files에 대한 용량 풀 문제 해결 | Microsoft Docs
description: 용량 풀을 관리하는 경우 발생할 수 있는 잠재적인 문제에 대해 설명하고 문제 해결 방법을 제공합니다.
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 7ca7f042aaa97967d47dd9f35afaa647e0d37361
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407269"
---
# <a name="troubleshoot-capacity-pool-issues"></a>용량 풀 문제 해결

이 문서에서는 풀 변경 작업을 포함하여 용량 풀을 관리할 때 발생할 수 있는 문제에 대한 해결 방법을 설명합니다. 

## <a name="issues-managing-a-capacity-pool"></a>용량 풀 관리 문제 

|     오류 조건    |     해결 방법    |
|-|-|
| 용량 풀 만들기 문제 |  용량 풀 수가 제한을 초과하지 않는지 확인합니다. [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)을 참조하세요.  수가 제한보다 적고 여전히 문제가 발생하는 경우 지원 티켓을 제출하고 용량 풀 이름을 지정합니다. |
| 용량 풀 삭제 문제  |  용량 풀을 삭제하려고 하는 구독에서 모든 Azure NetApp Files 볼륨 및 스냅샷을 제거해야 합니다. <br> 모든 볼륨 및 스냅샷을 이미 제거했는데도 용량 풀을 삭제할 수 없는 경우 포털에 표시되지 않고 리소스에 대한 참조가 여전히 존재할 수 있습니다. 이 경우 지원 티켓을 제출하고 위의 권장 단계를 수행했음을 지정합니다. |
| `Requested throughput not available` 오류가 발생하여 볼륨 만들기 또는 수정이 실패함 | 볼륨에 사용할 수 있는 처리량은 해당 용량 풀의 크기와 서비스 수준에 따라 결정됩니다. 처리량이 충분하지 않으면 풀 크기를 늘리거나 기존 볼륨 처리량을 조정해야 합니다. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>볼륨의 용량 풀을 변경할 때 발생하는 문제 

|     오류 조건    |     해결 방법    |
|-|-|
| 볼륨의 용량 풀 변경이 허용되지 않습니다. | 이 기능을 사용하기 위한 권한이 아직 부여되지 않았을 수 있습니다. <br> 볼륨을 다른 용량 풀로 이동하는 기능이 현재 미리 보기 상태입니다. 이 기능을 처음 사용하는 경우 먼저 이 기능을 등록하고 `-FeatureName ANFTierChange`를 설정해야 합니다. [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)에서 등록 단계를 참조하세요. |
| 총 볼륨 크기에 비해 용량 풀 크기가 너무 작습니다. |  이 오류는 이동 중인 볼륨에 사용할 수 있는 용량이 대상 용량 풀에 없기 때문에 발생합니다.  <br> 대상 풀의 크기를 늘리거나 용량이 더 큰 다른 풀을 선택합니다.  [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)을 참조하세요.   |
|  호출한 볼륨이 `'{source pool name}'`대상 풀에 이미 있기`'{target pool name}'` 때문에 풀 변경을 완료할 수 없습니다. | 이 오류는 동일한 이름의 볼륨이 대상 용량 풀에 이미 있기 때문에 발생합니다.  동일한 이름의 볼륨이 없는 다른 용량 풀을 선택합니다.   | 

## <a name="next-steps"></a>다음 단계  

* [용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)
* [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
* [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)
