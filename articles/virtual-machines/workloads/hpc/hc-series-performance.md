---
title: HC-시리즈 VM 크기 성능
description: Azure에서 HC-시리즈 VM 크기의 성능 테스트 결과에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 40cdd0ffeafe9e55444eb6ac4836a335aacbacc1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687500"
---
# <a name="hc-series-virtual-machine-sizes"></a>HC-시리즈 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

몇 가지 성능 테스트가 HC-시리즈 크기에 대해 실행되었습니다. 다음은 이 성능 테스트의 결과 중 일부입니다.

| 작업                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM 3조                                    | 190 GB/s (Intel MLC AVX-512)  |
| 고성능 LInpack (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| RDMA 대기 시간 및 대역폭                        | 1.05마이크로초, 96.8Gb/s   |
| 로컬 NVMe SSD의 FIO                           | 1.3 GB/s 읽기, 900 MB/s 쓰기 |  
| 4 Azure Premium SSD의 IOR(P30 관리 디스크, RAID0)**  | 780 MB/s 읽기, 780 MB/쓰기 |

## <a name="mpi-latency"></a>MPI 대기 시간

OSU 마이크로 벤치마크 제품군의 MPI 대기 시간 테스트가 실행됩니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Azure HC의 MPI 대기 시간":::

## <a name="mpi-bandwidth"></a>MPI 대역폭

OSU 마이크로 벤치마크 제품군의 MPI 대역폭 테스트가 실행됩니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Azure HC의 MPI 대역폭":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Mellanox Perftest 패키지](https://community.mellanox.com/s/article/perftest-package)에는 대기 시간(ib_send_lat)과 대역폭(ib_send_bw)과 같은 많은 InfiniBand 테스트가 있습니다. 예제 명령은 다음과 같습니다.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
