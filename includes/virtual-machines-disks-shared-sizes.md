---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 78721aa407977262747c43be90001da2343322aa
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484551"
---
지금은 ultra disks, premium Ssd 및 standard Ssd만 공유 디스크를 사용 하도록 설정할 수 있습니다. 디스크 크기에 따라 `maxShares` 제한이 다를 수 있으므로 `maxShares` 값을 설정할 때 초과할 수 없습니다. 프리미엄 SSD의 경우 디스크 공유를 지원하는 디스크 크기는 P15 이상입니다.

각 디스크에 대해 디스크를 동시에 공유할 수 있는 최대 노드 수를 나타내는 `maxShares` 값을 정의할 수 있습니다. 예를 들어 2 노드 장애 조치(failover) 클러스터를 설정하려는 경우 `maxShares=2`를 설정합니다. 최댓값이 상한입니다. 노드 수가 지정된 `maxShares` 값보다 적으면 노드가 클러스터에 조인하거나 탈퇴할 수 있습니다(디스크를 탑재 또는 분리).

> [!NOTE]
> `maxShares` 값은 디스크가 모든 노드에서 분리된 경우에만 설정하거나 편집할 수 있습니다.

### <a name="premium-ssd-ranges"></a>프리미엄 SSD 범위

다음 표에서는 프리미엄 SSD 크기별로 `maxShares`에 허용되는 최댓값을 보여줍니다.

|디스크 크기  |maxShares 제한  |
|---------|---------|
|P1,P2,P3,P4,P6,P10,P15,P20     |3         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

디스크의 IOPS 및 대역폭 제한은 `maxShares` 값에 의해 영향을 받지 않습니다. 예를 들어 P15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1이든 상관없이 1100입니다.

### <a name="standard-ssd-ranges"></a>표준 SSD 범위

다음 표에서는 표준 SSD 크기별로 `maxShares`에 허용되는 최댓값을 보여줍니다.

|디스크 크기  |maxShares 제한  |
|---------|---------|
|E1,E2,E3,E4,E6,E10,E15,E20     |3         |
|E30, E40, E50     |5         |
|E60, E70, E80     |10         |

디스크의 IOPS 및 대역폭 제한은 `maxShares` 값에 의해 영향을 받지 않습니다. 예를 들어 E15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1이든 상관없이 500입니다.

### <a name="ultra-disk-ranges"></a>울트라 디스크 범위

최소 `maxShares` 값은 1이고 최대 `maxShares` 값은 5입니다. 울트라 디스크에는 크기 제한이 없으며 모든 크기의 울트라 디스크는 최댓값을 포함하여 `maxShares`에 대한 모든 값을 사용할 수 있습니다.