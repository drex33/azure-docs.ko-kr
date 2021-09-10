---
title: Azure Disk Storage에서 애플리케이션 벤치마크
description: Azure에서 애플리케이션을 벤치마킹하는 프로세스를 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: c57047cdaf848bc27805d8819be515636d492676
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687554"
---
# <a name="benchmark-a-disk"></a>디스크 벤치마크

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

벤치마킹은 애플리케이션에서 다양한 작업을 시뮬레이션하고 각 작업에 대한 애플리케이션 성능을 측정하는 과정입니다. [고성능을 위한 디자인 문서](premium-storage-performance.md)에 설명된 단계를 사용하여 애플리케이션 성능 요구 사항을 수집했습니다. 애플리케이션을 호스팅하는 VM에서 벤치마킹 도구를 실행하여 프리미엄 SSD를 통해 애플리케이션이 얻을 수 있는 성능 수준을 확인할 수 있습니다. 이 문서에서는 Azure 프리미엄 SSD로 프로비전된 Standard_D8ds_v4 VM을 벤치마킹하는 예제를 제공합니다.

Windows 및 Linux용으로 각각 일반 벤치마킹 도구 DiskSpd 및 FIO를 사용했습니다. 이러한 도구는 작업과 같은 프로덕션을 시뮬레이션하는 여러 스레드를 생성하고 시스템 성능을 측정합니다. 도구를 사용하여 일반적으로 애플리케이션에 대해 변경할 수 없는 블록 크기 및 큐 크기와 같은 매개 변수를 구성할 수도 있습니다. 애플리케이션 작업의 다양한 유형에 대해 프리미엄 SSD로 프로비전된 높은 확장성의 VM에 최대 성능을 구동하도록 유연성을 제공합니다. 각 벤치마킹 도구에 대해 자세히 알아보려면 [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) 및 [FIO](http://freecode.com/projects/fio)를 방문하세요.

아래 예제를 수행하려면 Standard_D8ds_v4를 만들고 프리미엄 SSD 4개를 VM에 연결합니다. 4개의 디스크 중 3개를 호스트 캐싱이 “없음”으로 구성하고 NoCacheWrites라는 볼륨에 스트라이프합니다. 나머지 디스크에 “ReadOnly”로 호스트 캐싱을 구성하고 이 디스크를 사용하여 CacheReads라는 볼륨을 만듭니다. 이 설정을 사용하면 Standard_D8ds_v4 VM의 최대 읽기 및 쓰기 성능을 확인할 수 있습니다. 프리미엄 SSD를 사용하여 Standard_D8ds_v4를 만드는 방법에 대한 자세한 단계는 [고성능을 위한 디자인](premium-storage-performance.md)을 참조하세요.

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>다음 단계

[고성능을 위한 디자인](premium-storage-performance.md)에 대한 문서를 계속 진행하세요.

이 문서에서는 프로토타입을 위한 기존 애플리케이션과 비슷한 검사 목록을 만듭니다. 벤치마킹 도구를 사용하여 작업을 시뮬레이션하고 프로토타입 애플리케이션의 성능을 측정할 수 있습니다. 이렇게 하면 애플리케이션 성능 요구 사항과 일치하거나 능가할 수 있는 디스크 제품을 확인할 수 있습니다. 그런 다음, 프로덕션 애플리케이션에 대해 동일한 지침을 구현할 수 있습니다.
