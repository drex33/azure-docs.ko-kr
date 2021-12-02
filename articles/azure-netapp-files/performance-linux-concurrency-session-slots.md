---
title: Azure NetApp Files에 대한 Linux 동시성 모범 사례 - 세션 슬롯 및 슬롯 테이블 항목 | Microsoft Docs
description: Azure NetApp Files NFS 프로토콜에 대한 세션 슬롯 및 슬롯 테이블 항목 관련 모범 사례를 설명합니다.
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
ms.date: 08/02/2021
ms.author: b-hchen
ms.openlocfilehash: 23b84644e56d7779f91d70870ec03b91ce84a05a
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133482604"
---
# <a name="linux-concurrency-best-practices-for-azure-netapp-files---session-slots-and-slot-table-entries"></a>Azure NetApp Files에 대한 Linux 동시성 모범 사례 - 세션 슬롯 및 슬롯 테이블 항목

이 문서는 Azure NetApp Files NFS 프로토콜의 세션 슬롯 및 슬롯 테이블 항목에 대한 동시성 모범 사례를 이해하는 데 도움이 됩니다. 

## <a name="nfsv3"></a>NFSv3

NFSv3에는 클라이언트와 서버 간의 동시성을 협상하는 메커니즘이 없습니다. 클라이언트와 서버는 상의 없이 각각의 한계를 정의합니다.  최상의 성능을 위해 클라이언트 측 `sunrpc` 슬롯 테이블 항목의 최대 수를 서버의 푸시백 없이 지원되는 최대 수와 맞춰야 합니다.  클라이언트가 서버 네트워크 스택의 워크로드 처리 능력에 과부하를 일으키면 서버는 연결 기간 크기를 줄여 응답하고, 이는 이상적인 성능 시나리오가 아닙니다.

기본적으로 최신 Linux 커널은 연결당 `sunrpc` 슬롯 테이블 항목 크기 `sunrpc.max_tcp_slot_table_entries`를 다음 표와 같이 65,536개의 미해결 작업을 지원하는 것으로 정의합니다. 

| Azure NetApp Files NFSv3 서버 <br> 연결당 최대 실행 컨텍스트 | Linux 클라이언트 <br> 연결당 기본 최대 `sunrpc` 슬롯 테이블 항목 |
|-|-|
| 128 | 65,536 |

이러한 슬롯 테이블 항목은 동시성의 제한을 정의합니다. 이 값이 높을 필요는 없습니다.  예를 들어 *Littles Law* 라는 큐 이론을 사용하면 I/O 속도가 동시성(미해결 I/O) 및 대기 시간에 따라 결정됨을 알 수 있습니다. 따라서 알고리즘은 이 65,536개의 슬롯이 매우 까다로운 워크로드를 구동하는 데 필요한 것보다 순서가 높음을 증명합니다.

`Littles Law: (concurrency = operation rate × latency in seconds)`

155의 낮은 동시성 수준으로 Oracle Direct NFS를 사용하여 초당 155,000개의 Oracle DB NFS 작업을 달성할 수 있으며, 이는 `nconnect` 탑재 옵션과 유사한 개념입니다.

* 0\.5ms의 대기 시간을 고려할 때 110,000 IOPS를 달성하려면 55의 동시성이 필요합니다.
* 1ms의 대기 시간을 고려할 때 155,000 IOPS를 달성하려면 155의 동시성이 필요합니다.

![Oracle DNFS 대기 시간 곡선](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

자세한 내용은 [Azure NetApp Files 단일 볼륨의 Oracle 데이터베이스 성능](performance-oracle-single-volumes.md)을 참조하세요.

`sunrpc.max_tcp_slot_table_entries` tunable은 연결 수준의 튜닝 매개 변수입니다.  ‘이 값을 연결당 128 이하로 설정하고, 환경 전체에서 10,000개 슬롯을 넘지 않도록 하는 것이 좋습니다.’

### <a name="examples-of-slot-count-based-on-concurrency-recommendation"></a>동시성 권장 사항을 기반으로 하는 슬롯 수의 예 

이 섹션의 예에서는 동시성 권장 사항에 따라 슬롯 수를 보여줍니다.  

#### <a name="example-1--one-nfs-client-65536-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128-based-on-the-server-side-limit-of-128"></a>예 1 – 1개의 NFS 클라이언트, `sunrpc.max_tcp_slot_table_entries` 65,536, 서버 측 한계 128을 기반으로 128의 최대 동시성에 대해 `nconnect` 없음

예 1은 기본 `sunrpc.max_tcp_slot_table_entry` 값이 65,536이고 단일 네트워크가 있는(따라서 `nconnect` 없음) 단일 클라이언트 워크로드를 기반으로 합니다.  이 경우 128의 동시성을 달성할 수 있습니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP`) 
        * 이론적으로 클라이언트는 연결당 서버로 이동하는 65,536개 미만의 요청을 발급할 수 있습니다.
        * 서버는 이 단일 연결로부터 이동하는 요청을 128개 미만으로 허용합니다.

#### <a name="example-2--one-nfs-client-128-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128"></a>예 2 – 1개의 NFS 클라이언트, `sunrpc.max_tcp_slot_table_entries` 128, 128의 최대 동시성에 대해 `nconnect` 없음

예 2는 `sunrpc.max_tcp_slot_table_entry` 값이 128이지만 `nconnect` 탑재 옵션이 없는 단일 클라이언트 워크로드를 기반으로 합니다.  이 설정을 사용하면 단일 네트워크 연결에서 128의 동시성을 달성할 수 있습니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * 클라이언트는 연결당 서버로 이동하는 요청을 128개 미만으로 발행합니다.
        * 서버는 이 단일 연결로부터 이동하는 요청을 128개 미만으로 허용합니다.

#### <a name="example-3--one-nfs-client-100-sunrpcmax_tcp_slot_table_entries-and-nconnect8-for-a-maximum-concurrency-of-800"></a>예 3 – 1개의 NFS 클라이언트, `sunrpc.max_tcp_slot_table_entries` 100, 800의 최대 동시성에 대해 `nconnect=8`

예 3은 `sunrpc.max_tcp_slot_table_entry` 값이 100인 단일 클라이언트 워크로드를 기반으로 합니다.  이번에는 `nconnect=8` 탑재 옵션을 사용하여 워크로드를 8개 연결에 분산합니다.  이 설정을 사용하면 8개 연결에 걸쳐 800의 동시성을 달성할 수 있습니다.  이 값은 400,000 IOPS를 달성하는 데 필요한 동시성입니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP), Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)… Connection 8 (10.10.10.10:2049, 10.10.10.11:7321,TCP)`
    * 연결 1
        * 클라이언트는 이 연결에서 서버로 이동하는 요청을 100개 미만으로 발행합니다.
        * 서버는 이 연결에 대해 클라이언트에서 128개 미만의 이동 중인 요청을 수락합니다.
    * 연결 2
        * 클라이언트는 이 연결에서 서버로 이동하는 요청을 100개 미만으로 발행합니다.
        * 서버는 이 연결에 대해 클라이언트에서 128개 미만의 이동 중인 요청을 수락합니다.
    * `…`
    * `…`
    * 연결 8
        * 클라이언트는 이 연결에서 서버로 이동하는 요청을 100개 미만으로 발행합니다.
        * 서버는 이 연결에 대해 클라이언트에서 128개 미만의 이동 중인 요청을 수락합니다.

#### <a name="example-4--250-nfs-clients-8-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-2000"></a>예 4 – 250개의 NFS 클라이언트, `sunrpc.max_tcp_slot_table_entries` 8, 2000의 최대 동시성에 대해 `nconnect` 없음

예 4에서는 컴퓨터 수가 250대인 EDA 환경에 대해 클라이언트당 낮아진 `sunrpc.max_tcp_slot_table_entry` 값 8을 사용합니다. 이 시나리오에서 백엔드 EDA 워크로드에서 4,000MiB/s를 달성하는 데 충분한 2000의 동시성이 환경에서 도달하게 됩니다.

* `NFS_Server=10.10.10.10, NFS_Client1=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * 클라이언트는 연결당 서버로 이동하는 요청을 8개 미만으로 발행합니다.
        * 서버는 이 단일 연결로부터 이동하는 요청을 128개 미만으로 허용합니다.
* `NFS_Server=10.10.10.10, NFS_Client2=10.10.10.12`
    * `Connection (10.10.10.10:2049, 10.10.10.12:7820,TCP) `
        * 클라이언트는 연결당 서버로 이동하는 요청을 8개 미만으로 발행합니다.
        * 서버는 이 단일 연결로부터 이동하는 요청을 128개 미만으로 허용합니다.
* `…`
* `…`
* `NFS_Server=10.10.10.10, NFS_Client250=10.10.11.13`
    * `Connection (10.10.10.10:2049, 10.10.11.13:4320,TCP) `
        * 클라이언트는 연결당 서버로 이동하는 요청을 8개 미만으로 발행합니다.
        * 서버는 이 단일 연결로부터 이동하는 요청을 128개 미만으로 허용합니다.

NFSv3를 사용하는 경우 ‘스토리지 엔드포인트 슬롯 수를 총 10,000개 이하로 유지해야 합니다’. 애플리케이션이 여러 네트워크 연결에 걸쳐 스케일 아웃하는 경우(일반적으로 `nconnect` 및 HPC, 특히 EDA) `sunrpc.max_tcp_slot_table_entries`에 대한 연결당 값을 128 미만으로 설정하는 것이 가장 좋습니다.  

### <a name="how-to-calculate-the-best-sunrpcmax_tcp_slot_table_entries"></a>최적의 `sunrpc.max_tcp_slot_table_entries` 계산 방법 

*Littles Law* 를 사용하여 필요한 슬롯 테이블 항목 수의 합을 계산할 수 있습니다. 일반적으로 다음 요소를 고려합니다.  

* 스케일 아웃 워크로드는 본질적으로 대규모 순차적인 경우가 많습니다.
* 데이터베이스 워크로드, 특히 OLTP는 본질적으로 임의적입니다. 

다음 표에서는 제공된 임의 대기 시간을 사용한 동시성 샘플 연구를 보여줍니다.

|     I/O 크기    |     대기 시간    |     I/O 또는 처리량    |     동시성    |
|-|-|-|-|
|     8KiB    |     0.5ms    |     110,000 IOPS \| 859MiB/s    |     55    |
|     8KiB    |     2ms    |     400,000 IOPS \| 3,125MiB/s    |     800    |
|     256KiB    |     2ms    |     16,000 IOPS \| 4,000MiB/s    |     32    |
|     256KiB    |     4ms    |     32,000 IOPS \| 8,000MiB/s    |     128    |

### <a name="how-to-calculate-concurrency-settings-by-connection-count"></a>연결 수에 따라 동시성 설정을 계산하는 방법

예를 들어 워크로드가 EDA 팜이고, 1,250개 클라이언트가 모두 워크로드를 동일한 스토리지 엔드포인트(스토리지 IP 주소)로 이동하는 경우 필요한 I/O 속도를 계산하고 팜 전체에 동시성을 나눠야 합니다.

워크로드가 256KiB 평균 작업 크기와 평균 10ms 대기 시간을 사용하여 4,000MiB/s인 것으로 가정합니다. 동시성을 계산하려면 다음 수식을 사용합니다.

`(concurrency = operation rate × latency in seconds)`

160의 동시성으로 변환하는 계산: 
 
`(160 = 16,000 × 0.010)`

1,250개의 클라이언트가 필요한 경우 `sunrpc.max_tcp_slot_table_entries`를 클라이언트당 2개로 설정하여 안전하게 4,000MiB/s에 도달할 수 있습니다.  하지만 클라이언트당 개수를 4 또는 8로 설정해서 추가 위쪽 공간을 빌드하여 권장 슬롯 실링인 10,000 아래로 유지할 수도 있습니다. 

### <a name="how-to-set-sunrpcmax_tcp_slot_table_entries-on-the-client"></a>클라이언트에서 `sunrpc.max_tcp_slot_table_entries`를 설정하는 방법

1.  `/etc/sysctl.conf` 구성 파일에 `sunrpc.max_tcp_slot_table_entries=<n>`를 추가합니다.   
    튜닝 도중 128 미만의 값이 적합한 경우 128을 해당 값으로 바꿉니다.
2.  다음 명령을 실행합니다.   
    `$ sysctl -p`
3.  튜닝 가능이 설정된 이후에 만든 탑재에만 튜닝 가능이 적용되므로 모든 NFS 파일 시스템을 탑재(또는 재탑재)합니다.

## <a name="nfsv41"></a>NFSv4.1 

NFSv4.1에서 세션은 클라이언트와 서버 간의 관계를 정의합니다. 탑재된 NFS 파일 시스템이 하나의 또는 여러 연결에 위치한지 여부와 무관하게(`nconnect` 사용) 세션 규칙이 적용됩니다. 세션 설정 시 클라이언트와 서버는 세션에 대한 최대 요청을 협상하여 지원되는 두 값 가운데 낮은 쪽으로 조정됩니다. Azure NetApp Files는 180개의 미해결 요청을 지원하고, Linux 클라이언트의 기본값은 64입니다. 다음 표에서는 세션 한계를 보여줍니다. 

| Azure NetApp Files NFSv4.1 서버 <br>  세션당 최대 명령 | Linux 클라이언트 <br>  세션당 기본 최대 명령    | 세션에 대해 협상된 최대 명령 |
|-|-|-|
|     180    |     64    |     64    |

Linux 클라이언트 기본값이 세션당 최대 64개의 요청이지만 `max_session_slots` 값은 튜닝이 가능합니다.  변경 사항을 적용하려면 재부팅이 필요합니다.  `systool -v -m nfs` 명령을 사용하여 현재 클라이언트에서 사용하는 최댓값을 확인합니다.  명령이 작동하려면 하나 이상의 NFSv4.1 탑재가 준비되어야 합니다.

```
$ systool -v -m nfs
{
Module = "nfs"
…
  Parameters:
…
    max_session_slots   = "64"   
…
}
```

`max_session_slots`를 튜닝하려면 `/etc/modprobe.d`에 구성 파일을 만듭니다.  파일의 줄에는 따옴표가 없어야 합니다. 그렇지 않으면 옵션이 적용되지 않습니다.

`$ echo “options nfs max_session_slots=180” > /etc/modprobe.d/nfsclient.conf`
`$ reboot`

Azure NetApp Files는 각 세션당 최대 180개 명령으로 제한합니다. 180을 현재 구성 가능한 최댓값으로 고려합니다.  Azure NetApp Files가 각 연결에 최대 128개의 NFS 명령으로 제한하므로 세션이 둘 이상의 연결에 걸쳐 분리되지 않는 한 클라이언트는 128을 초과하는 동시성을 달성할 수 없습니다.  둘 이상의 연결을 얻으려면 `nconnect` 탑재 옵션을 사용하는 것이 좋고 값이 2 이상이어야 합니다.

### <a name="examples-of-expected-concurrency-maximums"></a>예상 동시성 최댓값의 예

이 섹션의 예는 예상 동시성 최댓값을 보여줍니다.  

#### <a name="example-1--64-max_session_slots-and-no-nconnect"></a>예 1 – `max_session_slots` 64 및 `nconnect` 없음

예 1은 `max_session_slots` 기본 설정 64와 `nconnect` 없음을 기반으로 합니다. 이 설정을 사용하면 단일 네트워크 연결에서 64의 동시성을 달성할 수 있습니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * 클라이언트는 세션에 대해 서버로 이동하는 요청을 64개 미만으로 발행합니다.
        * 서버는 세션에 대해 클라이언트에서 이동하는 요청을 64개 미만으로 허용합니다. (64는 협상된 값입니다.)

#### <a name="example-2--64-max_session_slots-and-nconnect2"></a>예 2 – `max_session_slots` 64 및 `nconnect=2` 

예 2는 최대 `session_slots` 64와 `nconnect=2`의 탑재 옵션 추가를 기반으로 합니다.  64의 동시성을 달성할 수 있지만 두 연결에 걸쳐 분리됩니다.  이 시나리오에서 여러 개의 연결이 더 큰 동시성을 제공하지 않지만 연결된 큐 깊이가 감소하면 대기 시간에 긍정적인 영향을 미칩니다.

`max_session_slots`가 여전히 64이지만 `nconnect=2`인 경우 최대 요청 수는 연결에 걸쳐 분리됩니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 연결 1
        * 클라이언트는 이 연결에서 서버로 이동하는 요청을 32개 미만으로 발행합니다.
        * 서버는 이 연결에 대해 클라이언트에서 32개 미만의 이동 중인 요청을 수락합니다.
    * 연결 2
        * 클라이언트는 이 연결에서 서버로 이동하는 요청을 32개 미만으로 발행합니다.
        * 서버는 이 연결에 대해 클라이언트에서 32개 미만의 이동 중인 요청을 수락합니다.

#### <a name="example-3--180-max_session_slots-and-no-nconnect"></a>예 3 – 180 `max_session_slots` 및 `nconnect` 없음

예 3은 `nconnect` 탑재 옵션을 삭제하고 `max_session_slots` 값을 180으로 설정하여 서버의 최대 NFSv4.1 세션 동시성을 일치시킵니다.  이 시나리오에서는 단 하나의 연결과 Azure NetApp Files에서 NFS 연결당 최대 128개의 미해결 작업이 있는 경우 세션은 128개의 작업 이동으로 제한됩니다.

`max_session_slots`가 180으로 설정되어 있지만 단일 네트워크 연결은 다음과 같이 최대 128개 요청으로 제한됩니다. 

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * 클라이언트는 세션에 대해 서버로 이동하는 요청을 180개 미만으로 발행합니다.
        * 서버는 세션에 대해 클라이언트에서 이동하는 요청을 180개 미만으로 허용합니다.
        * *서버는 단일 연결에 대해 이동하는 요청을 128개 미만으로 허용합니다.*

#### <a name="example-4--180-max_session_slots-and-nconnect2-"></a>예 4 – 180 `max_session_slots` 및 `nconnect=2 ` 

예 4는 `nconnect=2` 탑재 옵션을 추가하고 180 `max_session_slots` 값을 다시 시도합니다. 전체 워크로드는 두 개의 연결로 분할되므로 180개의 미해결 작업을 달성할 수 있습니다.

세션은 두 개의 연결을 사용하여 전체 180개의 미해결 요청 할당을 지원합니다.

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 연결 1
        * 클라이언트는 연결된 서버로 이동하는 요청을 90개 미만으로 유지해야 합니다.
        * *서버는 세션 내에서 이 연결에 대해 클라이언트에서 90개 미만의 이동 중인 요청을 유지합니다.*
    * 연결 2
        * 클라이언트는 연결된 서버로 이동하는 요청을 90개 미만으로 유지해야 합니다.
        * *서버는 세션 내에서 이 연결에 대해 클라이언트에서 90개 미만의 이동 중인 요청을 유지합니다.*

> [!NOTE]
> 최대 동시성의 경우 `max_session_slots`를 현재 Azure NetApp Files에서 지원되는 최대 세션 수준 동시성과 동일한 180으로 설정합니다.

### <a name="how-to-check-for-the-maximum-requests-outstanding-for-the-session"></a>세션에 대한 미해결 최대 요청 수를 확인하는 방법 

클라이언트 및 서버에서 지원하는 `session_slot` 크기를 확인하려면 패킷 추적에서 mount 명령을 캡처합니다.  다음 예와 같이 `CREATE_SESSION` 호출과 `CREATE_SESSION` 회신을 찾습니다.  클라이언트에서 시작된 호출과 서버에서 시작된 회신.

다음 `tcpdump` 명령을 사용하여 mount 명령을 캡처합니다.

`$ tcpdump -i eth0 -s 900 -w /tmp/write.trc port 2049`

Wireshark를 사용하는 경우 중요 패킷은 다음과 같습니다.

![중요 패킷을 보여주는 스크린샷.](../media/azure-netapp-files/performance-packets-interest.png)  

이러한 두 패킷 내에서 추적 파일의 중간 섹션에 있는 `max_reqs` 필드를 확인합니다.

* 네트워크 파일 시스템
    * 작업
        * `Opcode`
            * `csa_fore_channel_attrs`
            * `max reqs`

패킷 12(클라이언트 최대 요청)에서 클라이언트의 `max_session_slots` 값이 64였음을 보여줍니다.  다음 섹션에서 서버는 세션에 대해 180의 동시성을 지원합니다.  세션은 제공된 두 값 중 더 낮은 값을 협상합니다.

![패킷 12에 대한 max session slots를 보여주는 스크린샷.](../media/azure-netapp-files/performance-max-session-packet-12.png)  

다음 예에서는 패킷 14(서버 최대 요청)를 보여줍니다.

![패킷 14에 대한 max session slots를 보여주는 스크린샷.](../media/azure-netapp-files/performance-max-session-packet-14.png) 

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례](performance-linux-direct-io.md)
* [Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례](performance-linux-filesystem-cache.md)
* [Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)
* [Linux NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)
* [Azure 가상 머신 SKU 모범 사례](performance-virtual-machine-sku.md) 
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
