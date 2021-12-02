---
title: Azure NetApp Files 단일 볼륨의 Oracle 데이터베이스 성능 | Microsoft Docs
description: Oracle 데이터베이스에서 Azure NetApp Files 단일 볼륨의 성능 테스트 결과에 대해 설명합니다.
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
ms.date: 09/30/2020
ms.author: b-hchen
ms.openlocfilehash: de25120dcfc1c6eeae4b5e852d567f3a30787252
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481293"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp Files 단일 볼륨의 Oracle 데이터베이스 성능

이 문서에서는 클라우드의 Oracle에 대한 다음 항목을 다룹니다. 해당 항목은 데이터베이스 관리자, 클라우드 설계자 또는 스토리지 설계자와 특히 관련이 있을 수 있습니다.   

* OLTP(온라인 트랜잭션 처리) 워크로드(대부분 임의 I/O) 또는 OLAP(온라인 분석 처리) 워크로드(대부분 순차 I/O)를 구동하는 경우 성능은 어떤가요?   
* 일반 Linux kNFS(커널 NFS) 클라이언트와 Oracle 고유의 Direct NFS 클라이언트 간에 어떤 성능 차이가 있나요?
* 대역폭과 관련해서 단일 Azure NetApp Files 볼륨의 성능이 충분한가요?

## <a name="testing-environment-and-components"></a>환경 및 구성 요소 테스트

다음 다이어그램은 테스트에 사용되는 환경을 보여 줍니다. 일관되고 간편한 배포를 위해 Ansible 플레이북을 사용하여 테스트 환경의 모든 요소를 배포했습니다.

![Oracle 테스트 환경](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>가상 머신 구성

테스트에서 사용한 가상 머신 설정은 다음과 같습니다.
* 운영 체제:   
    RedHat Enterprise Linux 7.8(wle-ora01)
* 인스턴스 유형:   
    D32s_v3 및 D64s_v3의 두 가지 모델이 테스트에 사용됨
* 네트워크 인터페이스 수:   
    서브넷 3에 1개가 배치됨  
* 디스크:   
    Oracle 이진 파일 및 OS가 단일 프리미엄 디스크에 배치됨

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files 구성
테스트에서 사용한 Azure NetApp Files 구성은 다음과 같습니다.   

* 용량 풀 크기:  
    4TiB, 8TiB, 16TiB, 32TiB 등 다양한 크기의 풀이 구성됨 
* 서비스 수준:  
    Ultra(할당된 볼륨 용량 1TiB당 128MiB/초 대역폭)
* 볼륨:  
    한두 개의 볼륨 테스트가 평가됨

### <a name="workload-generator"></a>워크로드 생성기 

테스트에서는 SLOB 2.5.4.2에서 생성된 워크로드를 사용했습니다. SLOB(Silly Little Oracle Benchmark)는 SGA로 버퍼링된 물리적 I/O 워크로드를 사용하여 SGA 하위 시스템에 스트레스를 주고 테스트하도록 설계된, Oracle 영역에서 잘 알려진 워크로드 생성기입니다.  

SLOB 2.5.4.2는 PDB(플러그형 데이터베이스)를 지원하지 않습니다. 따라서 PDB 지원을 추가하기 위해 `setup.sh` 및 `runit.sh` 스크립트에 변경 내용이 추가되었습니다.  

테스트에 사용된 SLOB 변수는 다음 섹션에 설명되어 있습니다.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>워크로드 80% SELECT, 20% UPDATE | 임의 I/O – `slob.conf` 변수   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>워크로드 100% SELECT | 순차 I/O – `slob.conf` 변수

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>데이터베이스

테스트에 사용된 Oracle 버전은 Oracle Database Enterprise Edition 19.3.0.0입니다.

Oracle 매개 변수는 다음과 같습니다.  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

SLOB 데이터베이스에 대한 PDB를 만들었습니다.

다음 다이어그램은 SLOB 사용자 스키마 4개를 호스트하기 위해 생성된 600GB 크기(각각 30GB인 데이터 파일 20개)의 PERFIO라는 테이블스페이스를 보여 줍니다. 각 사용자 스키마의 크기는 125GB입니다.

![Oracle 데이터베이스](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>성능 메트릭

목표는 애플리케이션에서 경험한 대로 IO 성능을 보고하는 것이었습니다. 따라서 이 문서의 모든 다이어그램은 AWR(자동 워크로드 리포지토리) 보고서를 통해 Oracle 데이터베이스에서 보고된 메트릭을 사용합니다. 다이어그램에서 사용된 메트릭은 다음과 같습니다.   

* **평균 IO 요청/초**   
    부하 프로필 섹션의 평균 읽기 IO 요청/초 및 평균 쓰기 IO 요청/초의 합계에 해당합니다.
* **평균 IO MB/초**   
    부하 프로필 섹션의 평균 읽기 IO MB/초 및 평균 쓰기 IO MB/초의 합계에 해당합니다.
* **평균 읽기 대기 시간**   
    Oracle 대기 이벤트 “db 파일 순차 읽기”의 평균 대기 시간(마이크로초)에 해당합니다.
* **스레드 수/스키마**   
    사용자 스키마당 SLOB 스레드 수에 해당합니다.

## <a name="performance-measurement-results"></a>성능 측정 결과  

이 섹션에서는 성능 측정 결과에 대해 설명합니다.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS 클라이언트 대 Oracle Direct NFS

이 시나리오는 Azure VM Standard_D32s_v3(Intel E5-2673 v4 @ 2.30 GHz)에서 실행되었습니다. 워크로드는 75% SELECT 및 25% UPDATE로, 대부분 임의 I/O이며 데이터베이스 버퍼 적중은 ~7.5%입니다. 

다음 다이어그램과 같이 Oracle DNFS 클라이언트는 일반 Linux kNFS 클라이언트보다 최대 2.8배 높은 처리량을 제공했습니다.  

![Linux kNFS 클라이언트 및 Oracle Direct NFS 비교](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

다음 다이어그램은 읽기 작업의 대기 시간 곡선을 보여 줍니다. 이 컨텍스트에서 kNFS 클라이언트의 병목 상태는 클라이언트와 NFS 서버(Azure NetApp Files 볼륨) 간에 설정된 단일 NFS TCP 소켓 연결입니다.  

![Linux kNFS 클라이언트 및 Oracle Direct NFS 대기 시간 곡선 비교](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS 클라이언트는 수백 개의 TCP 소켓 연결을 만들 수 있기 때문에 더 많은 IO 요청/초를 푸시하여 병렬 처리를 활용할 수 있었습니다. [Azure NetApp Files 구성](#anf_config)에 설명된 대로 각 TiB 용량을 추가로 할당할 때마다 128MiB/초의 대역폭을 더 사용할 수 있습니다. DNFS의 최대 처리량은 8TiB 용량 선택 시 적용되는 한도인 1GiB/초였습니다. 용량을 더 많이 지정했다면 처리량이 더 증가했을 것입니다.

처리량은 고려 사항 중 하나일 뿐입니다. 또 다른 고려 사항은 사용자 환경에 주로 영향을 주는 대기 시간입니다. 다음 다이어그램에서 볼 수 있듯이 DNFS보다 kNFS에서 대기 시간이 훨씬 빨리 증가할 것으로 예상할 수 있습니다. 

![Linux kNFS 클라이언트 및 Oracle Direct NFS 읽기 대기 시간 비교](../media/azure-netapp-files/performance-oracle-read-latency.png)  

히스토그램은 데이터베이스 대기 시간에 대한 뛰어난 인사이트를 제공합니다. 다음 다이어그램은 가장 높은 동시성 데이터 요소(32개 스레드/스키마)에서 DNFS를 사용하는 동안 기록된 “db 파일 순차 읽기”의 관점에서 전체 뷰를 제공합니다. 다음 다이어그램에서 볼 수 있듯이 모든 읽기 작업의 47%는 512마이크로초와 1000마이크로초 사이에서 적용되었으며, 모든 읽기 작업의 90%는 2ms 미만의 대기 시간으로 처리되었습니다.

![Linux kNFS 클라이언트 및 Oracle Direct NFS 히스토그램 비교](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

결론적으로, NFS에서 Oracle 데이터베이스 인스턴스의 성능을 향상시킬 때 DNFS가 필수인 것은 분명합니다.

### <a name="single-volume-performance-limits"></a>단일 볼륨 성능 한도

이 섹션에서는 임의 I/O 및 순차 I/O를 사용하는 단일 볼륨의 성능 한도에 대해 설명합니다. 

#### <a name="random-io"></a>임의 I/O

DNFS는 8TB의 Azure NetApp Files 성능 할당량에서 제공하는 것보다 훨씬 더 많은 대역폭을 사용할 수 있습니다. Azure NetApp Files 볼륨 용량을 16TiB으로 늘리면 즉시 변경되며 볼륨 대역폭 양이 1024MiB/초에서 두 배인 2048MiB/초로 증가합니다. 

다음 다이어그램은 데이터베이스 버퍼 적중률이 8%인 80% SELECT 및 20% UPDATE 워크로드의 구성을 보여 줍니다. SLOB는 단일 볼륨을 200,000 NFS I/O 요청/초까지 구동할 수 있었습니다. 각 작업의 크기가 8KiB라는 것을 감안할 때 테스트 중인 시스템은 ~200,000 IO 요청/초 또는 1600MiB/초를 제공할 수 있었습니다.
 
![Oracle DNFS 처리량](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

다음 읽기 대기 시간 곡선 다이어그램은 읽기 처리량이 증가함에 따라 1ms 선 아래에서는 대기 시간이 부드럽게 증가하다가 평균 읽기 대기 시간인 ~1.3ms에서 ~165,000 평균 읽기 IO 요청/초로 곡선의 무릎을 적중합니다.  이 값은 Azure 클라우드의 다른 모든 기술로는 거의 불가능한 놀라운 I/O 대비 대기 시간 값입니다. 

![Oracle DNFS 대기 시간 곡선](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>순차 I/O  

다음 다이어그램에서 볼 수 있듯이 모든 I/O가 임의로 수행되는 것은 아닙니다. 예를 들어 RMAN 백업 또는 전체 테이블 검사의 경우 워크로드는 사용할 수 있는 모든 대역폭을 필요로 합니다.  앞에서 설명한 것과 동일한 구성을 사용하되 볼륨 크기를 32TiB로 조정한 다음 다이어그램은 단일 Oracle DB 인스턴스가 3,900MB/초의 처리량을 초과하여 32TB의 Azure NetApp Files 볼륨 성능 할당량(128MB/s * 32 = 4096MB/s)에 근접할 수 있음을 보여 줍니다.

![Oracle DNFS I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

요약하면, Azure NetApp Files는 Oracle 데이터베이스를 클라우드로 이동하는 데 도움이 됩니다. 데이터베이스에서 요청 시 성능을 제공합니다. 언제든지 볼륨 할당량의 크기를 동적으로 중단 없이 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files의 성능 벤치마크 테스트 추천 사항](azure-netapp-files-performance-metrics-volumes.md)
- [Linux용 성능 벤치마크](performance-benchmarks-linux.md)