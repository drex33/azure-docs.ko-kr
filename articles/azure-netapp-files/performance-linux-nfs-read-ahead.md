---
title: Azure NetApp Files에 대한 Linux NFS 미리 읽기 모범 사례 - 세션 슬롯 및 슬롯 테이블 항목 | Microsoft Docs
description: Azure NetApp Files에 대한 파일 시스템 캐시 및 Linux NFS 미리 읽기 모범 사례를 설명합니다.
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
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233515"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Linux NFS 미리 읽기 모범 사례

이 문서는 Azure NetApp Files의 파일 시스템 캐시 모범 사례를 이해하는 데 도움이 됩니다.  

NFS 미리 읽기는 애플리케이션에 의한 I/O 요청에 앞서 파일의 차단을 예측적으로 요청합니다. 클라이언트의 순차적 읽기 처리량을 향상시키도록 설계되었습니다.  최근까지 모든 최신 Linux 배포는 미리 읽기 값을 탑재된 파일 시스템 `rsize`의 15배에 맞도록 설정합니다.  

다음 표에서는 지정된 각 `rsize` 탑재 옵션에 대한 기본 미리 읽기 값을 보여 줍니다.

| 탑재된 파일 시스템 `rsize` | 미리 읽기 차단 |
|-|-|
| 64KiB | 960KiB |
| 256KiB | 3,840KiB |
| 1024KiB | 15,360KiB |

RHEL 8.3 및 Ubuntu 18.04는 클라이언트의 순차적 읽기 성능에 부정적인 영향을 줄 수 있는 변경 사항을 도입했습니다.  이전 릴리스와 달리 이러한 배포는 사용된 `rsize` 탑재 옵션과 관계 없이 미리 읽기를 기본값인 128KiB로 설정합니다. 더 큰 미리 읽기 값을 사용하는 릴리스를 128-KiB 기본값을 사용하는 릴리스로 업그레이드하면 순차적 읽기 성능이 저하됩니다. 그러나 미리 읽기 값은 동적으로나 영구적으로 상향 튜닝이 가능합니다.  예를 들어 SAS GRID를 사용하여 테스트하는 경우 3,840KiB, 960KiB 및 128KiB와 비교하여 15,360KiB 읽기 값이 최적으로 검색되었습니다.  15,360KiB 이상으로 실행된 테스트가 부족하여 긍정적인 영향 또는 부정적 영향을 확인할 수 없습니다.

다음 표에서는 현재 사용 가능한 각 배포에 대한 기본 미리 읽기 값을 보여 줍니다.

|     분포    |     Release    |     미리 읽기 차단    |
|-|-|-|
|     RHEL    |     8.3    |     128KiB    |
|     RHEL    |     7.X, 8.0, 8.1, 8.2    |     15 X `rsize`    |
|     SLES    |     12.X – 최소 15SP2    |     15 X `rsize`    |
|     Ubuntu    |     18.04 – 최소 20.04    |     128KiB    |
|     Ubuntu    |     16.04    |     15 X `rsize`    |
|     Debian    |     최대 10    |     15 x `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>NFS 파일 시스템별 미리 읽기 작동 방식   

NFS 미리 읽기는 NFS 파일 시스템의 탑재 지점에서 정의됩니다. 기본 설정은 동적으로나 영구적으로 표시하고 설정할 수 있습니다.  편의를 위해 Red Hat에서 작성한 다음과 같은 bash 스크립트가 탑재된 NFS 파일 시스템의 미리 읽기를 보거나 동적으로 설정할 수 있도록 제공되었습니다.

미리 읽기는 다음 스크립트를 사용하거나 NFS 탑재별로 동적으로 정의하거나, 이 섹션에 표시된 대로 `udev` 규칙을 사용하여 영구적으로 정의할 수 있습니다.  탑재된 NFS 파일 시스템에 대한 미리 읽기를 표시하거나 설정하려면 다음 스크립트를 bash 파일로 저장하고 파일의 사용 권한을 수정하여 실행 파일(`chmod 544 readahead.sh`)로 설정하고 표시된 대로 실행합니다. 

## <a name="how-to-show-or-set-read-ahead-values"></a>미리 읽기 값을 표시하거나 설정하는 방법   

현재 미리 읽기 값(반환된 값이 KiB에 있는 경우)을 표시하려면 다음 명령을 실행합니다.  

`$ ./readahead.sh  show <mount-point>`   

미리 읽기에 대해 새 값을 설정하려면 다음 명령을 실행합니다.   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>예   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>NFS 탑재의 미리 읽기를 영구적으로 설정하는 방법

NFS 탑재에 대해 미리 읽기를 영구적으로 설정하려면 다음과 같이 `udev` 규칙을 작성할 수 있습니다.    

1. `/etc/udev/rules.d/99-nfs.rules`를 만들고 테스트합니다.

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. `udev` 규칙을 적용합니다.   

    `$udevadm control --reload`

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례](performance-linux-direct-io.md)
* [Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례](performance-linux-filesystem-cache.md)
* [Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)
* [Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)
* [Azure 가상 머신 SKU 모범 사례](performance-virtual-machine-sku.md) 
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
