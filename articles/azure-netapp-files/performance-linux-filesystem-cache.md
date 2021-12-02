---
title: Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례 | Microsoft Docs
description: Azure NetApp Files에 대해 따라야 할 Linux 파일 시스템 캐시 모범 사례를 설명합니다.
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
ms.date: 07/02/2021
ms.author: b-hchen
ms.openlocfilehash: d886028084552c84f16a83e79a353349aaf441e9
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485500"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례

이 문서는 Azure NetApp Files의 파일 시스템 캐시 모범 사례를 이해하는 데 도움이 됩니다.  

## <a name="filesystem-cache-tunables"></a>파일 시스템 캐시 튜닝 가능 항목

파일 시스템 캐시 튜닝 가능 항목에 대한 다음 요소를 이해해야 합니다.  

* 더티 버퍼를 플러시하면 메모리 압력이 제거될 때까지 데이터가 향후 읽기에 사용할 수 있는 깨끗한 상태로 유지됩니다.  
* 비동기 플러시 작업에는 세 가지 트리거가 있습니다.
    * 시간 기반: 버퍼가 이 튜닝 가능 항목에 의해 정의된 수명에 도달하면 정리(즉, 플러시 또는 스토리지에 쓰기)를 위해 표시되어야 합니다.
    * 메모리 압력: 자세한 내용은 [`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes)를 참조하세요.
    * 닫기: 파일 핸들이 닫히면 모든 더티 버퍼가 비동기적으로 스토리지에 플러시됩니다.

이러한 요인은 4개의 튜닝 가능 항목에 의해 제어됩니다. 각 튜닝 가능 항목은 `/etc/sysctl.conf` 파일에서 `tuned` 또는 `sysctl`를 사용하여 동적이고 지속적으로 튜닝할 수 있습니다. 이러한 변수를 튜닝하면 애플리케이션의 성능이 향상됩니다.  

> [!NOTE]
> 이 문서에서 설명하는 정보는 SAS 그리드 및 SAS Viya 유효성 검사 연습에서 발견되었습니다. 따라서 튜닝 가능 항목은 유효성 검사 연습에서 얻은 개선 사항을 기반으로 합니다. 많은 애플리케이션에서 이러한 매개 변수를 튜닝하면 유사한 이점을 얻을 수 있습니다.

### `vm.dirty_ratio | vm.dirty_bytes` 

이러한 두 튜닝 가능 항목은 수정된 데이터에 사용할 수 있는 RAM의 양을 정의하지만 안정적인 스토리지에는 아직 작성되지 않습니다.  어떤 튜닝 가능 항목이 설정되든 다른 튜닝 가능 항목은 0으로 자동 설정됩니다. RedHat은 두 튜닝 가능 항목 중 하나를 수동으로 0으로 설정하지 않을 것을 권장합니다.  `vm.dirty_ratio` 옵션(둘 중 기본값)은 OS에 따라 Redhat에 의해 실제 메모리의 20% 또는 30%로 설정되며, 이는 최신 시스템의 메모리 공간을 고려할 때 상당한 양입니다. 메모리 크기에 관계없이 더욱 일관된 경험을 위해 `vm.dirty_ratio` 대신 `vm.dirty_bytes`을 설정하는 것을 고려해야 합니다.  예를 들어 SAS 그리드를 사용하여 진행 중인 작업은 30MiB가 최상의 전체 혼합 워크로드 성능을 위한 적절한 설정으로 확인되었습니다. 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

이러한 튜닝 가능 항목은 Linux 쓰기 저장 메커니즘이 더티 블록을 안정적인 스토리지로 플러시하기 시작하는 지점을 정의합니다. Redhat은 기본적으로 실제 메모리의 10%로 설정되어 있으며, 이는 대용량 메모리 시스템에서 플러시를 시작하는 데 상당한 양의 데이터입니다. 예를 들어 SAS 그리드를 사용하면 시간별로 `vm.dirty_background`를 `vm.dirty_ratio` 또는 `vm.dirty_bytes`의 1/5 크기로 설정하는 것이 좋습니다. SAS 그리드에 대해 `vm.dirty_bytes` 설정이 적극적으로 설정되는 방식을 고려하면 여기에 특정 값이 설정되지 않습니다.  

### `vm.dirty_expire_centisecs` 

이 튜닝 가능 항목은 더티 버퍼를 비동기적으로 쓰기 위해 태그를 지정해야 하기 전까지 걸리는 시간을 정의합니다. SAS Viya의 CAS 워크로드를 예로 들어 보겠습니다. 임시 쓰기 관련 워크로드는 이 값을 300센티초(3초)로 설정하고 기본값은 3,000센티초(30초)인 것이 가장 최적인 것으로 나타났습니다.  

SAS Viya는 CAS 데이터를 각각 몇 메가바이트의 여러 개의 작은 청크로 공유합니다.  각 분할에 데이터를 쓴 후 이러한 파일 핸들을 닫지 않고도 핸들은 열린 상태로 유지되고 내부의 버퍼는 애플리케이션에서 메모리를 매핑합니다.  핸들을 닫지 않으면 메모리 압력 또는 30초가 지날 때까지 플러시가 발생하지 않습니다. 메모리 압력을 대기하는 것은 긴 타이머가 만료되기를 기다리는 것처럼 차선책으로 판명되었습니다. 최고의 전체 처리량을 찾는 SAS 그리드와는 달리 SAS Viya는 쓰기 대역폭의 최적화를 고려했습니다.  

### `vm.dirty_writeback_centisecs` 

커널 플러시 스레드는 각 플러시 스레드의 대기 간에 더티 버퍼를 비동기적으로 플러시하는 역할을 합니다.  이 튜닝 가능 항목은 플러시 간의 절전에 소요되는 시간을 정의합니다.  SAS Viya에서 사용하는 3초 `vm.dirty_expire_centisecs` 값을 고려하여 SAS는 이 튜닝 가능 항목을 500센티초(5초) 기본값이 아닌 100센티초(1초)로 설정하여 최상의 전체 성능을 찾습니다.

## <a name="impact-of-an-untuned-filesystem-cache"></a>튜닝되지 않은 파일 시스템 캐시의 영향

기본 가상 메모리 튜닝 가능 항목과 최신 시스템의 RAM 양을 고려하여 쓰기 저장은 이 혼합 워크로드를 구동하는 특정 클라이언트의 관점에서 잠재적으로 다른 스토리지 바인딩 작업의 속도를 늦춥니다.  다음과 같은 증상은 튜닝되지 않고 쓰기 집약적이며 캐시가 많은 Linux 컴퓨터에서 발생할 수 있습니다.  

* 디렉터리 목록 `ls`가 응답하지 않는 것으로 나타날 만큼 시간이 오래 걸립니다.
* 파일 시스템에 대한 읽기 처리량은 쓰기 처리량보다 크게 감소합니다.
* `nfsiostat` 보고서 쓰기 대기 시간은 **초 이상** 입니다.

이 동작은 쓰기가 많은 혼합 워크로드를 수행하는 *Linux 컴퓨터* 에서만 발생할 수 있습니다.  또한 단일 스토리지 엔드포인트에 탑재된 모든 NFS 볼륨에 대한 환경이 저하됩니다.  둘 이상의 엔드포인트를 통해 탑재되는 경우 엔드포인트를 공유하는 볼륨만 이 동작이 발생합니다.

이 섹션에 설명된 대로 파일 시스템 캐시 매개 변수를 설정하면 문제를 해결하는 것으로 나타났습니다.

## <a name="monitoring-virtual-memory"></a>가상 메모리 모니터링

가상 메모리와 쓰기 저장에 수행되는 작업을 이해하려면 다음 코드 조각과 출력을 고려하세요.  *더티* 는 시스템의 더티 메모리 양을 나타내고 *쓰기 저장* 는 스토리지에 활발하게 기록되고 있는 메모리 양을 나타냅니다.  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

다음 출력은 `vm.dirty_ratio` 및 `vm.dirty_background` 비율이 각각 실제 메모리의 2%와 1%로 설정된 실험에서 나온 것입니다.  이 경우 플러시는 384GiB 메모리 시스템의 1%인 3.8GiB에서 시작되었습니다.  쓰기 저장은 NFS에 대한 쓰기 처리량과 매우 유사했습니다. 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례](performance-linux-direct-io.md)
* [Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)
* [Azure NetApp Files를 위한 Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)
* [Linux NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)
* [Azure 가상 머신 SKU 모범 사례](performance-virtual-machine-sku.md) 
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
