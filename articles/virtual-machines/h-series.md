---
title: H 시리즈 - Azure Virtual Machines
description: H 시리즈 VM의 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 09/11/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 75e3dc21c5cb5e8590b63c3382260331bb0bd844
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754652"
---
# <a name="h-series"></a>H 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

H 시리즈 VM은 높은 CPU 주파수 또는 많은 코어당 메모리 요구 사항으로 구동되는 애플리케이션에 최적화되어 있습니다. H 시리즈 VM은 8개 또는 16개의 Intel Xeon E5 2667 v3 프로세서 코어, CPU 코어당 최대 14GB의 RAM을 제공하며 하이퍼스레딩은 제공하지 않습니다. H 시리즈는 일관된 RDMA 성능을 위해 비차단 팻 트리 구성에서 56Gb/초 Mellanox FDR InfiniBand를 제공합니다. H 시리즈 VM은 현재 SR-IOV가 지원되지 않으며 Intel MPI 5.x 및 MS-MPI를 지원합니다.

[ACU](acu.md): 290-300<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원되지 않음<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 프로세서 | 메모리(GiB) | 메모리 대역폭 GB/초 | 기본 CPU 빈도(GHz) | 모든 코어 빈도(GHz, 최고) | 단일 코어 빈도(GHz, 최고) | RDMA 성능(Gb/s) | MPI 지원 | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 최대 이더넷 vNIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> MPI 애플리케이션의 경우 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

> [!NOTE]
> [RDMA 지원 VM](sizes-hpc.md#rdma-capable-instances)중에서 H 시리즈는 SR-IOV를 사용하도록 설정되어 있지 않습니다. 따라서 지원되는 [VM 이미지](./workloads/hpc/configure.md#vm-images), [InfiniBand 드라이버](./workloads/hpc/enable-infiniband.md) 요구 사항 및 지원되는 [MPI 라이브러리](./workloads/hpc/setup-mpi.md)는 SR-IOV를 사용하는 VM과 다릅니다.

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     |H 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 4800 코어(단일 가상 머신 확장 집합에 300개 VM, singlePlacementGroup=true)  |
| MPI 지원                 | Intel MPI 5.x, MS-MPI  |
| 비 SRIOV RDMA에 대한 OS 지원   | CentOS/RHEL 6.5-7.4, SLES 12 SP4+, WinServer 2012-2016  |
| Orchestrator 지원        | CycleCloud, Batch, AKS  |

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
