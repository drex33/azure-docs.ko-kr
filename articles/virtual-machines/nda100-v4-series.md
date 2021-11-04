---
title: ND A100 v4 시리즈
description: ND A100 v4 시리즈 VM의 사양입니다.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: d18172751bb58d6a21115dd482024bdfcc47bea1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060443"
---
# <a name="nd-a100-v4-series"></a>ND A100 v4 시리즈

ND A100 v4 시리즈 가상 머신은 고급 Deep Learning 학습 및 긴밀하게 결합된 HPC 워크로드 스케일 업 및 스케일 아웃을 위해 설계된 Azure GPU 제품군에 새로 추가된 주력 가상 머신입니다. 

ND A100 v4 시리즈는 단일 VM(가상 머신) 및 8개의 NVIDIA Ampere A100 Tensor Core GPU로 시작합니다. ND A100 v4 기반 배포는 VM당 1.6Tb/초의 상호 연결 대역폭을 사용하여 수천 개의 GPU로 스케일 업할 수 있습니다. VM 내의 각 GPU에는 고유한 전용 토폴로지 독립적 200Gb/초 NVIDIA Mellanox HDR InfiniBand 연결이 제공됩니다. 이러한 연결은 동일한 가상 머신 확장 집합을 차지하는 VM 간에 자동으로 구성되며 GPUDirect RDMA를 지원합니다.

각 GPU는 VM 내의 통신을 위한 NVLINK 3.0 연결을 제공하며, 인스턴스는 96 물리적 2세대 AMD Epyc™ CPU 코어에서도 지원됩니다.

이러한 인스턴스는 GPU 가속화를 '기본으로' 지원하는 많은 AI, ML, 분석 도구(예: TensorFlow, Pytorch, Caffe, RAPIDS, 다른 프레임워크)에 탁월한 성능을 제공합니다. 또한 스케일 아웃 InfiniBand 상호 연결은 GPU의 원활한 클러스터링을 위해 NVIDIA의 NCCL2 통신 라이브러리를 기반으로 하는 대규모 기존 AI 및 HPC 도구 집합에서 지원됩니다.

> [!IMPORTANT]
> ND A100 v4 VM을 시작하려면 [HPC 워크로드 구성 및 최적화](./workloads/hpc/configure.md)에서 드라이버 및 네트워크 구성 등의 단계를 참조하세요.
> GPU 메모리 I/O 공간의 증가로 인해 ND A100 v4는 [2세대 VM](./generation-2.md) 및 마켓플레이스 이미지를 사용해야 합니다. [Azure HPC 이미지](./workloads/hpc/configure.md)를 적극 권장합니다. Azure HPC Ubuntu 18.04, 20.04 및 Azure HPC CentOS 7.9 이미지가 지원됩니다.
> 

<br>

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disks): 지원됨(가용성, 사용량 및 성능에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원되지 않음<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨<br>
InfiniBand: 지원됨, GPUDirect RDMA, 8 x 200 Gigabit HDR<br>
Nvidia NVLink Interconnect: 지원됨<br>
<br>
ND A100 v4 시리즈는 다음과 같은 커널 버전을 지원합니다. <br>
CentOS 7.9 HPC: 3.10.0-1160.24.1.el7.x86_64 <br>
Ubuntu 18.04: 5.4.0-1043-azure <br>
Ubuntu 20.04: 5.4.0-1046-azure <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 네트워크 대역폭 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND96asr_v4 | 96 | 900 | 6000 | 8 A100 40 GB GPU(NVLink 3.0) | 40 | 32 | 80,000 / 800 | 24,000Mbps | 8 |

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

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
