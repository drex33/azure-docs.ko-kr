---
title: HBv2 시리즈 VM 크기 성능
description: Azure에서 HBv2 시리즈 VM 크기에 대한 성능 테스트 결과에 대해 알아봅니다.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 81ac751ca209c7741e3bf55082f7b041ff13022d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318164"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 시리즈 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

[HBv2 시리즈](../../hbv2-series.md) 크기의 VM에서 여러 성능 테스트가 실행되었습니다. 다음은 이러한 성능 테스트의 결과 중 일부입니다.


| 작업                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM 3조                                    | 350GB/s(CCX당 21~23GB/s)                                     |
| HPL(고성능 LInpack)                  | 4테라플롭(Rpeak, FP64), 8테라플롭(Rpeak, FP32)               |
| RDMA 대기 시간 및 대역폭                        | 1.2마이크로초, 190GB/초                                        |
| 로컬 NVMe SSD의 FIO                           | 2.7GB/s 읽기, 1.1GB/s 쓰기, 102k IOPS 읽기, 115 IOPS 쓰기 |
| 8 * Azure Premium SSD(P40 관리 디스크, RAID0)의 IOR **  | 1.3GB/s 읽기, 2.5GB/ 쓰기 101k IOPS 읽기, 105k IOPS 쓰기 |


## <a name="mpi-latency"></a>MPI 대기 시간

OSU 마이크로벤치마크 제품군의 MPI 대기 시간 테스트가 실행됩니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
```

:::image type="content" source="./media/latency-hbv2.png" alt-text="Azure HB의 MPI 대기 시간":::

## <a name="mpi-bandwidth"></a>MPI 대역폭

OSU 마이크로벤치마크 제품군의 MPI 대역폭 테스트가 실행됩니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="Azure HB의 MPI 대역폭":::

## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 패키지](https://community.mellanox.com/s/article/perftest-package)에는 대기 시간(ib_send_lat)과 대역폭(ib_send_bw)과 같은 많은 InfiniBand 테스트가 있습니다. 예제 명령은 다음과 같습니다. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.