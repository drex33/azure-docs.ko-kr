---
title: 권장되는 성능 벤치 마크 테스트 - Azure NetApp Files
description: Azure NetApp Files를 사용하는 볼륨 성능 및 메트릭에 대한 벤치마크 테스트 권장 사항을 알아봅니다.
author: b-hchen
ms.author: b-hchen
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: d345aa054eeae57a3993c67f60778ecee8e5cfd7
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483558"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 벤치마크 테스트 추천 사항

이 문서에서는 Azure NetApp Files를 사용하는 볼륨 성능 및 메트릭에 대한 벤치마크 테스트 권장 사항을 제공합니다.

## <a name="overview"></a>개요

Azure NetApp Files 볼륨의 성능 특성을 이해하려면 오픈 소스 도구 [FIO를](https://github.com/axboe/fio) 사용하여 다양한 워크로드를 시뮬레이션하는 일련의 벤치마크를 실행할 수 있습니다. FIO는 Linux 및 Windows 기반 운영 체제 모두에 설치할 수 있습니다.  이 도구는 볼륨의 IOPS 및 처리량 모두에 대한 스냅샷을 빠르게 얻기 위한 훌륭한 도구입니다.

> [!IMPORTANT]
> Azure NetApp Files 유틸리티를  기준 벤치마킹 도구로 사용하지 않는 것이 `dd` 좋습니다. 최적의 인프라 성능을 설정하고 분석하려면 실제 애플리케이션 워크로드, 워크로드 시뮬레이션, 벤치마킹 및 분석 도구(예: Oracle을 사용하는 Oracle AWR 또는 DB2에 해당하는 IBM)를 사용해야 합니다. FIO, vdbench 및 iometer와 같은 도구는 스토리지 제한에 대한 가상 머신을 결정하고 테스트의 매개 변수를 실제 애플리케이션 워크로드 혼합과 일치시켜 가장 유용한 결과를 제공합니다. 그러나 항상 실제 애플리케이션을 사용하여 테스트하는 것이 가장 좋습니다.  
### <a name="vm-instance-sizing"></a>VM 인스턴스 크기

최상의 결과를 위해서는 테스트를 수행하는 데 적절한 크기의 VM(가상 머신) 인스턴스를 사용해야 합니다. 다음 예제에서는 Standard_D32s_v3 인스턴스를 사용합니다. VM 인스턴스 크기에 대한 자세한 내용은 Windows 기반 VM의 경우 [Azure의 Windows 가상 머신 크기](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 Linux 기반 VM의 경우 [Azure의 Linux 가상 머신 크기](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files 볼륨 크기

예상되는 성능 수준에 적합한 서비스 수준 및 볼륨 할당량 크기를 선택해야 확인합니다. 자세한 내용은 [Azure NetApp Files 서비스 수준을](azure-netapp-files-service-levels.md)참조하세요.

### <a name="virtual-network-vnet-recommendations"></a>VNet(가상 네트워크) 권장 사항

벤치마크 테스트를 Azure NetApp Files와 동일한 VNet에서 수행해야 합니다. 아래 예제에서는 권장 사항을 보여 줍니다.

![VNet 권장 사항](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="performance-benchmarking-tools"></a>성능 벤치마킹 도구

이 섹션에서는 몇 가지 벤치마킹 도구에 대해 자세히 설명합니다. 

### <a name="ssb"></a>SSB

SSB(SQL Storage Benchmark)는 Python으로 작성된 오픈 소스 벤치마크 도구입니다. 스토리지 하위 시스템 성능을 측정하는 방식으로 데이터베이스 상호 작용을 에뮬레이트하는 "실제" 워크로드를 생성하도록 설계되었습니다. 

SSB의 목적은 조직과 개인이 SQL 데이터베이스 워크로드의 스트레스에서 스토리지 하위 시스템 성능을 측정할 수 있도록 하는 것입니다.

#### <a name="installation-of-ssb"></a>SSB 설치 

SSB 추가 정보 파일의 [시작](https://github.com/NetApp/SQL_Storage_Benchmark/blob/main/README.md#getting-started) 섹션에 따라 선택한 플랫폼에 설치합니다.

### <a name="fio"></a>FIO 

유연한 I/O 테스터(FIO)는 벤치마크 및 스트레스/하드웨어 확인 모두에 사용되는 무료 오픈 소스 디스크 I/O 도구입니다. 

FIO는 Linux 및 Windows 모두에서 이진 형식으로 사용할 수 있습니다. 

#### <a name="installation-of-fio"></a>FIO 설치

[FIO 추가 정보 파일의](https://github.com/axboe/fio#readme) 이진 패키지 섹션에 따라 선택한 플랫폼에 설치합니다.

#### <a name="fio-examples-for-iops"></a>FIO의 IOPS 예제 

이 섹션의 FIO 예제에서는 다음과 같은 설정을 사용합니다.
* VM 인스턴스 크기: D32s_v3
* 용량 풀 서비스 수준 및 크기: 프리미엄/50TiB
* 볼륨 할당량 크기: 48TiB

다음 예제에서는 FIO 임의 읽기 및 쓰기를 보여 줍니다.

##### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k 블록 크기 100% 임의 읽기

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

##### <a name="output-68k-read-iops-displayed"></a>출력: 68k 읽기 IOPS 표시됨

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

##### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k 블록 크기 100% 임의 쓰기

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

##### <a name="output-73k-write-iops-displayed"></a>출력: 73k 쓰기 IOPS 표시됨

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

#### <a name="fio-examples-for-bandwidth"></a>FIO의 대역폭 예제

이 섹션의 예제에서는 FIO 순차 읽기 및 쓰기를 보여 줍니다.

##### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k 블록 크기 100% 순차 읽기

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

##### <a name="output-118-gbits-throughput-displayed"></a>출력: 11.8Gbit/s 처리량 표시됨

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

##### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k 블록 크기 100% 순차 쓰기

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

##### <a name="output-122-gbits-throughput-displayed"></a>출력: 12.2Gbit/s 처리량 표시됨

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>볼륨 메트릭

Azure NetApp Files 성능 데이터는 Azure Monitor 카운터를 통해 사용할 수 있습니다. 카운터는 Azure Portal 및 REST API GET 요청을 통해 사용할 수 있습니다. 

다음 정보에 대한 과거 데이터를 볼 수 있습니다.
* 평균 읽기 대기 시간 
* 평균 쓰기 대기 시간 
* 읽기 IOPS(평균)
* 쓰기 IOPS(평균)
* 볼륨 논리적 크기(평균)
* 볼륨 스냅샷 크기(평균)

### <a name="using-azure-monitor"></a>Azure Monitor 사용 

아래와 같이 메트릭 페이지에서 볼륨별로 Azure NetApp Files 카운터에 액세스할 수 있습니다.

![Azure Monitor 메트릭](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

메트릭 페이지로 이동하고, NetApp으로 필터링하고, 관심 있는 볼륨 카운터를 지정하여 Azure Monitor에서 Azure NetApp Files용 대시보드를 만들 수도 있습니다. 

![Azure Monitor 대시보드](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API 액세스

REST API 호출을 사용하여 Azure NetApp Files 카운터에 액세스할 수 있습니다. 용량 풀 및 볼륨에 대한 카운터는 [Azure Monitor에서 지원되는 메트릭: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes)를 참조하세요.

다음 예제에서는 논리 볼륨 크기를 확인하기 위한 GET URL을 보여 줍니다.

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Linux용 성능 벤치마크](performance-benchmarks-linux.md)
