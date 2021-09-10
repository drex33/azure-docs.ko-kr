---
title: HBv3 시리즈 VM 크기 성능 및 확장성
description: Azure에서 HBv3 시리즈 VM 크기의 성능 및 확장성에 대해 알아봅니다.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bb32a407813ba7e85f55fc39790fba81ca452c50
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693401"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 시리즈 가상 머신 성능

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

일반적인 HPC 마이크로 벤치마크를 사용하는 성능 기대치는 다음과 같습니다.

| 작업                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350GB/s(NUMA당 ~82-86GB/s)                                     |
| HPL(고성능 LInpack)                  | 4TF(Rpeak, FP64), 8TF(Rpeak, FP32), 120-코어 VM 크기               |
| RDMA 대기 시간 및 대역폭                        | 1.2 마이크로초(1바이트), 192Gb/초(단방향)                                        |
| 로컬 NVMe Ssd(RAID0)의 FIO                  | 7GB/s 읽기, 3Gb/초 쓰기, 186k IOPS 읽기, 201k IOPS 쓰기 |

## <a name="process-pinning"></a>프로세스 고정

[프로세스 고정](compiling-scaling-applications.md#process-pinning)은 기본 실리콘을 있는 그대로 게스트 VM에 노출하므로 HBv3 시리즈 VM에서 작동합니다. 최적의 성능과 일관성을 위해 이를 고정하는 것이 좋습니다.

## <a name="mpi-latency"></a>MPI 대기 시간

OSU 마이크로 벤치마크 제품군의 MPI 대기 시간 테스트는 다음에 따라 실행할 수 있습니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI 대역폭
OSU 마이크로 벤치마크 제품군의 MPI 대역폭 테스트는 다음에 따라 실행할 수 있습니다. 샘플 스크립트는 [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)에 있습니다.
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
[Mellanox Perftest 패키지](https://community.mellanox.com/s/article/perftest-package)에는 대기 시간(ib_send_lat)과 대역폭(ib_send_bw)과 같은 많은 InfiniBand 테스트가 있습니다. 예제 명령은 다음과 같습니다.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>다음 단계
- [MPI 애플리케이션 스케일링](compiling-scaling-applications.md)에 대해 알아보세요.
- [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843)의 HBv3 VM에서 HPC 애플리케이션의 성능 및 확장성 결과를 검토합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
