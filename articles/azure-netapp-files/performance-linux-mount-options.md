---
title: Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례 | Microsoft Docs
description: Azure NetApp Files와 함께 사용하는 탑재 옵션 및 모범 사례에 대해 설명합니다.
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
ms.date: 06/01/2021
ms.author: b-hchen
ms.openlocfilehash: 78535f0bb9d5a9b423054263b9bcba873396f8ed
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487040"
---
# <a name="linux-nfs-mount-options-best-practices-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례

이 문서는 Azure NetApp Files와 함께 사용하는 탑재 옵션 및 모범 사례를 이해하는 데 유용합니다.

## `Nconnect` 

`nconnect` 탑재 옵션을 사용하면 NFS 클라이언트와 NFS 엔드포인트 간에 설정해야 하는 연결 수(네트워크 흐름)를 최대 16개까지 지정할 수 있습니다. 일반적으로 NFS 클라이언트는 엔드포인트와 사이에 단일 연결을 사용합니다.  네트워크 흐름 수를 늘리면 I/O 및 처리량의 상한이 크게 증가합니다. 테스트 결과 `nconnect=8`이 가장 성능이 좋은 것으로 나타났습니다.  

프로덕션을 위해 다중 노드 SAS GRID 환경을 준비할 때 8시간에서 5.5시간으로 런타임에 반복 가능한 30% 단축이 일어날 수 있습니다. 

| 탑재 옵션 | 작업 실행 시간 |
|-|-|
| `nconnect` 아님 | 8시간 |
| `nconnect=8`  | 5.5시간 | 

두 테스트 세트 모두 미리 읽기가 15MiB로 설정된 RHEL8.3 및 E32-8_v4 가상 머신을 동일하게 사용했습니다.

`nconnect`를 사용하는 경우 다음 규칙을 염두에 두어야 합니다.

* `nconnect`는 모든 주요 Linux 배포판의 Azure NetApp Files에서 지원되지만 최신 릴리스에서만 지원됩니다.

    | Linux 릴리스 | NFSv3(최소 릴리스) | NFSv4.1(최소 릴리스) |
    |-|-|-|
    | RedHat Enterprise Linux | RHEL8.3 | RHEL8.3 |
    | SUSE | SLES12SP4 또는 SLES15SP1 | SLES15SP2 |
    | Ubuntu | Ubuntu18.04 |          |

    > [!NOTE]
    > SLES15SP2는 `nconnect`가 NFSv4.1용 Azure NetApp Files에서 지원되는 최소 SUSE 릴리스입니다.  지정된 다른 모든 릴리스는 `nconnect` 기능을 도입한 첫 번째 릴리스입니다.

* 단일 엔드포인트의 모든 탑재는 다음 시나리오와 같이 탑재된 첫 번째 내보내기의 `nconnect` 설정을 상속합니다. 

    시나리오 1: `nconnect`가 첫 번째 탑재에서 사용됩니다. 따라서 동일한 엔드포인트에 대한 모든 탑재는 `nconnect=8`을 사용합니다.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.10.10.10:/volume2 /mnt/volume2`
    * `mount 10.10.10.10:/volume3 /mnt/volume3`

    시나리오 2: `nconnect`가 첫 번째 탑재에서 사용되지 않습니다. 따라서 동일한 엔드포인트에 대한 탑재는 `nconnect`가 지정되어 있더라도 `nconnect`를 사용하지 않습니다.

    * `mount 10.10.10.10:/volume1 /mnt/volume1`
    * `mount 10.10.10.10:/volume2 /mnt/volume2 -o nconnect=8`
    * `mount 10.10.10.10:/volume3 /mnt/volume3 -o nconnect=8`

    시나리오 3: `nconnect` 설정이 별도의 스토리지 엔드포인트에 전파되지 않습니다.  `nconnect`가 `10.10.10.10`에서 오는 탑재에서 사용되지만 `10.12.12.12`에서 들어오는 탑재에는 사용되지 않습니다.

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.12.12.12:/volume2 /mnt/volume2`

* `nconnect`를 지정된 클라이언트에서 스토리지 동시성을 높이는 데 사용할 수 있습니다. 

자세한 내용은 [Azure NetApp Files를 위한 Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)를 참조하세요.

## <a name="rsize-and-wsize"></a>`Rsize` 및 `Wsize`
 
`rsize` 및 `wsize` 플래그는 NFS 작업의 최대 전송 크기를 설정합니다.  `rsize` 또는 `wsize`가 탑재에서 지정되지 않은 경우 클라이언트와 서버는 두 가지에서 지원하는 가장 큰 크기를 협상합니다.   현재 Azure NetApp Files 및 최신 Linux 배포판은 모두 1,048,576바이트(1MiB)의 읽기 및 쓰기 크기를 지원합니다.   그러나 최상의 전체 처리량 및 대기 시간을 위해 Azure NetApp Files는 `rsize` 및 `wsize`를 262,144바이트(256K) 이하로 설정하는 것을 권장합니다. 256KiB보다 큰 `rsize` 및 `wsize`를 사용하면 대기 시간이 증가하고 처리량이 감소하는 것을 확인할 수 있습니다. 

예를 들어 [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md#mount-the-azure-netapp-files-volumes)에는 다음과 같이 최대치의 256-KiB `rsize` 및 `wsize`가 나와 있습니다.

```
sudo vi /etc/fstab
# Add the following entries
10.23.1.5:/HN1-data-mnt00001 /hana/data/HN1/mnt00001  nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-data-mnt00002 /hana/data/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-log-mnt00001 /hana/log/HN1/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
```
 
또한 예를 들어 SAS Viya는 256KiB 읽기 및 쓰기 크기를 권장하며, [SAS GRID](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/606973/highlight/true#M17740)는 `r/wsize`를 64KiB로 제한하는 동시에, NFS 탑재에 대한 미리 읽기가 개선되어 읽기 성능을 향상시킵니다. 자세한 내용은 [Azure NetApp Files에 대한 NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)를 참조하세요.

`rsize` 및 `wsize` 사용에 적용되는 고려 사항은 다음과 같습니다.

* 임의 I/O 작업 크기는 `rsize` 및 `wsize` 탑재 옵션보다 작은 경우가 많습니다. 따라서 실제로 제한되지 않습니다.
* 파일 시스템 캐시를 사용하는 경우 파일 크기가 `rsize` 및 `wsize`보다 작지 않다면 순차 I/O는 `rsize` 및 `wsize` 탑재 옵션에 의해 가정된 크기로 발생합니다.
* 파일 시스템 캐시를 바이패스하는 작업은 여전히 `rsize` 및 `wsize` 탑재 옵션에 의해 제한되지만 `rsize` 또는 `wsize`에 지정된 최댓값만큼 큰 문제가 되지는 않습니다.  이 고려 사항은 `directio` 옵션이 있는 워크로드 생성기를 사용할 때 중요합니다.

*Azure NetApp Files에 대한 모범 사례로, 최상의 전체 처리량 및 대기 시간을 위해 `rsize` 및 `wsize`를 262,144바이트 이하로 설정하는 것이 가장 좋습니다.*

## <a name="close-to-open-consistency-and-cache-attribute-timers"></a>close-to-open 일관성 및 캐시 특성 타이머   

NFS는 느슨한 일관성 모델을 사용합니다. 애플리케이션이 공유 스토리지로 이동하여 데이터를 사용할 때마다 데이터를 가져올 필요가 없으므로 일관성이 느슨합니다. 이는 애플리케이션 성능에 엄청난 영향을 미칠 수 있는 시나리오입니다.  이 프로세스를 관리하는 두 가지 메커니즘에는 캐시 특성 타이머와 close-to-open 일관성이 있습니다.

*클라이언트가 데이터의 완전한 소유권을 갖는 경우, 즉 여러 노드 또는 시스템 간에 데이터가 공유되지 않는 경우 일관성이 보장됩니다.* 이 경우 close-to-open(`cto`) 일관성을 해제하고(탑재 옵션으로 `nocto`) 특성 캐시 관리에 대한 시간 제한을 설정하여(탑재 옵션으로 `actimeo=600`이 타이머를 기본값 `acregmin=3,acregmax=30,acdirmin=30,acdirmax=60`에서 10m로 변경함) 스토리지에 대한 `getattr` 액세스 작업을 줄이고 애플리케이션의 속도를 낮출 수 있습니다. 일부 테스트에서 `nocto`는 `getattr` 액세스 호출의 약 65~70%를 줄이며 `actimeo`를 조정하면 이러한 호출이 20~25% 감소합니다.

### <a name="how-attribute-cache-timers-work"></a>특성 캐시 타이머의 작동 방식  

특성 `acregmin`, `acregmax`, `acdirmin` 및 `acdirmax`는 캐시의 일관성을 제어합니다. 이전의 두 특성은 파일의 특성을 신뢰할 수 있는 기간을 제어합니다. 후자의 두 특성은 디렉터리 파일 자체의 특성을 신뢰할 수 있는 기간(디렉터리 크기, 디렉터리 소유권, 디렉터리 권한)을 제어합니다.  `min` 및 `max` 특성은 디렉터리 특성, 파일 특성 및 파일의 캐시 콘텐츠가 각각 신뢰할 수 있는 것으로 간주되는 최소 및 최대 기간을 정의합니다. `min` 및 `max` 사이에서 알고리즘은 캐시된 엔트리를 신뢰할 수 있는 기간을 정의하는 데 사용됩니다.

예를 들어 기본 `acregmin` 및 `acregmax` 값을 각각 3초와 30초로 고려합니다.  예를 들어 특성은 디렉터리에 있는 파일에 대해 반복적으로 평가됩니다.  3초 후에 NFS 서비스가 새로 고침에 대해 쿼리됩니다.  특성이 유효한 것으로 간주되는 경우 클라이언트는 신뢰할 수 있는 시간을 6초, 12초, 24초로 두 배로 하며, 최댓값은 30초, 30초로 설정됩니다.  이 시점부터 캐시된 특성이 만료된 것으로 간주될 때까지(주기가 시작되는 시점) 신뢰성은 `acregmax`에 지정된 값인 30초로 정의됩니다.

클라이언트가 데이터를 읽기 전용으로 사용하고 데이터 업데이트가 다른 경로를 통해 관리되는 경우와 같이 클라이언트의 완전한 소유권이 없는 경우에도 비슷한 탑재 옵션 세트를 활용할 수 있는 다른 경우가 있습니다.  EDA, 웹 호스팅 및 영화 렌더링과 같은 클라이언트 그리드를 사용하고 상대적으로 정적인 데이터 세트(EDA 도구 또는 라이브러리, 웹 콘텐츠, 질감 데이터)를 사용하는 애플리케이션의 경우 일반적인 동작은 데이터 세트가 클라이언트에 주로 캐시된다는 것입니다. 읽기가 거의 없고 쓰기도 없습니다. 스토리지로 다시 들어오는 `getattr`/access 호출이 많이 있습니다.  이러한 데이터 세트는 일반적으로 파일 시스템을 탑재하고 정기적으로 콘텐츠 업데이트를 푸시하는 다른 클라이언트를 통해 업데이트됩니다.

이러한 경우 새 콘텐츠를 선택하는 데 알려진 지연이 있으며 애플리케이션은 잠재적으로 오래된 데이터에서 계속 작동합니다.  이러한 경우 `nocto` 및 `actimeo`를 사용하여 데이터 만료 날짜를 관리할 수 있는 기간을 제어할 수 있습니다.  예를 들어 EDA 도구 및 라이브러리에서는 이 데이터가 일반적으로 자주 업데이트되지 않으므로 `actimeo=600`이 제대로 작동합니다.  클라이언트가 사이트를 편집할 때 데이터 업데이트를 적시에 확인해야 하는 소규모 웹 호스팅의 경우 `actimeo=10`이 허용될 수 있습니다. 여러 파일 시스템에 푸시된 콘텐츠가 있는 대규모 웹 사이트의 경우 `actimeo=60`이 허용될 수 있습니다.

이러한 탑재 옵션을 사용하면 이러한 경우 스토리지에 대한 워크로드가 크게 줄어듭니다. (예를 들어 최근 EDA 환경으로 도구 볼륨에 대한 IOP가 150K 이상에서 6K까지로 감소했습니다.) 애플리케이션은 메모리의 데이터를 신뢰할 수 있으므로 훨씬 더 빠르게 실행할 수 있습니다. (메모리 액세스 시간은 고속 네트워크의 `getattr`/access에 대해 나노초와 수백 마이크로초입니다.)

### <a name="close-to-open-consistency"></a>Close-to-open 일관성 

Close-to-open 일관성(`cto` 탑재 옵션)을 사용하면 캐시 상태에 관계없이 열려 있는 파일의 최신 데이터가 항상 애플리케이션에 표시됩니다.  

* 디렉터리를 크롤링할 때(예: `ls`, `ls -l`) 특정 PRC 호출 세트가 발급됩니다.  
    NFS 서버는 파일 시스템의 해당 보기를 공유합니다. 지정된 NFS 내보내기를 액세스하는 모든 NFS 클라이언트에서 `cto`를 사용하는 한, 모든 클라이언트에는 동일한 파일 및 디렉터리 목록이 표시됩니다.  디렉터리에 있는 파일의 특성 새로 고침은 [특성 캐시 타이머](#how-attribute-cache-timers-work)에 의해 제어됩니다.  즉, `cto`가 사용되는 한, 파일이 만들어지고 파일이 스토리지에 저장되는 즉시 원격 클라이언트에 파일이 표시됩니다.
* 파일을 열면 파일 콘텐츠가 NFS 서버의 관점에서 새로워집니다.  
    머신 2에서 파일을 열었을 때 콘텐츠가 머신 1에서 플러시를 완료하지 않은 경합 조건이 있는 경우 머신 2는 열 때 서버에 있는 데이터만 수신하게 됩니다. 이 경우 머신 2는 `acreg` 타이머에 도달할 때까지 파일에서 데이터를 더 검색하지 않으며, 머신 2는 서버에서 캐시 일관성을 다시 확인합니다.  이 시나리오는 머신 1에서 계속 파일을 작성 중일 때 머신 2의 타일 `-f`를 사용하여 확인할 수 있습니다.

### <a name="no-close-to-open-consistency"></a>Close-to-open 일관성 없음  

close-to-open 일관성이 사용되지 않는(`nocto`) 경우 클라이언트는 캐시 특성 타이머가 위반될 때까지 파일 및 디렉터리에 대한 현재 보기의 새로 고침을 신뢰합니다.  

* 디렉터리를 크롤링할 때(예: `ls`, `ls -l`) 특정 PRC 호출 세트가 발급됩니다.  
    클라이언트는 `acdir` 캐시 타이머 값이 위반된 경우 현재 파일 목록에 대한 서버 호출만 발급합니다.  이 경우 최근에 만든 파일 및 디렉터리도 나타나지 않고 최근에 제거된 파일 및 디렉터리가 계속 표시됩니다.  

* 파일이 열려 있는 경우 파일이 캐시에 있는 한 NFS 서버와의 일관성을 검사하지 않고 캐시된 콘텐츠(있는 경우)가 반환됩니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례](performance-linux-direct-io.md)
* [Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례](performance-linux-filesystem-cache.md)
* [Azure NetApp Files를 위한 Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)
* [Linux NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)
* [Azure 가상 머신 SKU 모범 사례](performance-virtual-machine-sku.md) 
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
