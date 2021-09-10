---
title: 가상 머신 및 디스크 성능
description: 가상 머신 및 연결된 디스크를 함께 사용하여 성능을 향상하는 방법에 대한 자세한 정보를 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 2e3bf032cad39887250c0d0a3cf148e24dc2397a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695853"
---
# <a name="virtual-machine-and-disk-performance"></a>가상 머신 및 디스크 성능

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>가상 머신 캐시되지 않은 제한 및 캐시된 제한
프리미엄 스토리지 및 프리미엄 스토리지 캐싱에 모두 사용하도록 설정된 가상 머신에는 두 가지 스토리지 대역폭 제한이 있습니다. 한 가지 예로 Standard_D8s_v3 가상 머신을 살펴봅니다. [Dsv3-시리즈](dv3-dsv3-series.md) 및 Standard_D8s_v3에 대한 설명서는 다음과 같습니다.

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

