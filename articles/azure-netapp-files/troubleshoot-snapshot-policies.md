---
title: Azure NetApp Files에 대한 스냅샷 정책 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대한 스냅샷 정책 관리 문제를 해결하는 데 도움이 되는 오류 메시지 및 해결 방법을 설명합니다.
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: e86761ed15089ef8544ec80b23f3ab585522df7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613121"
---
# <a name="troubleshoot-snapshot-policies"></a>스냅샷 정책 문제 해결

이 문서에서는 Azure NetApp Files 스냅샷 정책을 관리할 때 발생할 수 있는 오류 시나리오를 설명합니다. 이슈를 해결하는 데 도움이 되는 솔루션도 제공합니다.

## <a name="error-conditions-and-resolutions"></a>오류 조건 및 해결 방법 

|     오류 조건    |     해결 방법    |
|-|-|
| 스냅샷 정책 이름이 잘못되어 스냅샷 정책을 만들지 못했습니다. | 스냅샷 정책 이름이 잘못된 경우 스냅샷 정책을 만드는 중 오류가 발생합니다. 스냅샷 정책 이름에는 다음 지침이 적용됩니다.  <ul><li> 스냅샷 정책 이름에는 ASCII가 아닌 문자 또는 특수 문자를 사용할 수 없습니다. </li> <li> 스냅샷 정책 이름은 문자 또는 숫자로 시작해야 하고 문자, 숫자, 밑줄('_') 및 하이픈('-')만 포함할 수 있습니다. </li> <li> 스냅샷 정책 이름은 1~64자여야 합니다.  </li></ul> 지침에 따라 스냅샷 정책 이름을 수정합니다.  |
| 값이 잘못되어 스냅샷 정책을 만들지 못했습니다. | `Number of snapshots to keep` 또는 `Minute on the hour to take snapshot` 등의 필드에 잘못된 값을 입력하면 Azure NetApp Files에서 스냅샷 정책을 만들지 못합니다. 유효한 값은 다음과 같습니다.  <ul><li>값이 유효한 숫자여야 합니다.</li> <li>값이 0에서 59 사이여야 합니다.</li></ul> 필드에 유효한 값을 입력했는지 확인합니다. | 
| `Total number of snapshots to keep exceeds 255` 오류가 발생하여 스냅샷 정책을 만들지 못했습니다. | 각 볼륨에는 [최대 255개의 스냅샷](azure-netapp-files-resource-limits.md)이 있을 수 있습니다. 최대값에는 모든 매시간, 매일, 매주, 매월 스냅샷의 합계가 포함됩니다. <br> `Snapshots to keep` 값을 줄인 후에 다시 시도하세요. |
| `Total snapshot policy is over the max '255'` 오류가 발생하여 볼륨에 정책을 할당하지 못했습니다. | 각 볼륨에는 [최대 255개의 스냅샷](azure-netapp-files-resource-limits.md)이 있을 수 있습니다. 모든 주문형, 매시간, 매일, 매주, 매월 스냅샷의 합계가 최대값을 초과하면 오류가 발생합니다. <br> `snapshots to keep` 값을 줄이거나 주문형 스냅샷을 일부 삭제한 후 다시 시도하세요. | 

## <a name="next-steps"></a>다음 단계  

* [스냅샷 정책 관리](snapshots-manage-policy.md)
