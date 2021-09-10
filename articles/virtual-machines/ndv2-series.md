---
title: NDv2 시리즈
description: NDv2 시리즈 VM의 사양입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 1009a63f1ffc323073deb00e7c628d3752af2408
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690217"
---
# <a name="updated-ndv2-series"></a>업데이트된 NDv2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

NDv2 시리즈 가상 머신은 가장 까다로운 GPU 가속 AI, 기계 학습, 시뮬레이션 및 HPC 워크로드의 요구 사항에 맞게 설계된 GPU 제품군에 새롭게 추가되었습니다.

NDv2는 NVIDIA Tesla V100 NVLINK에 연결된 8개의 GPU로 구동되며, 각 GPU의 메모리는 32GB입니다. 또한 각 NDv2 VM은 40개의 non-HyperThreaded Intel Xeon Platinum 8168 (Skylake) 코어 및 672GiB의 시스템 메모리를 갖추고 있습니다.

NDv2 인스턴스는 CUDA GPU에 최적화된 계산 커널 및 GPU 가속화를 지원하는 많은 AI, ML, 분석 도구(예: TensorFlow, Pytorch, Caffe, RAPIDS, 다른 프레임워크)를 활용하여 HPC 및 AI 워크로드에 탁월한 성능을 제공합니다.

특히, NDv2는 계산 집약적인 스케일 업(VM당 8개의 GPU 활용) 워크로드와 스케일 아웃(여러 VM을 함께 활용) 워크로드에 모두 적합하게 빌드되었습니다. 현재 NDv2 시리즈는 HPC VM의 HB 시리즈와 유사한 100기가비트 InfiniBand EDR 백엔드 네트워킹을 지원하여 AI 및 ML에 대한 분산 학습을 포함한 병렬 시나리오에 대한 고성능 클러스터링이 가능합니다. 해당 백엔드 네트워크는 NVIDIA의 NCCL2 라이브러리에서 사용되는 프로토콜을 포함하여 모든 주요 InfiniBand 프로토콜을 지원하여 GPU가 원활하게 클러스터링할 수 있습니다.

> [!IMPORTANT]
> ND40rs_v2 VM에서 [InfiniBand 사용](./workloads/hpc/enable-infiniband.md)을 설정할 때는 4.7-1.0.0.1 Mellanox OFED 드라이버를 사용하세요.
>
> GPU 메모리의 증가로 인해 새로운 ND40rs_v2 VM은 [2세대 VM](./generation-2.md) 및 마켓플레이스 이미지를 사용해야 합니다. 
>
> 참고: GPU당 16GB의 메모리가 포함된 ND40s_v2는 더 이상 미리 보기에 사용할 수 없으며 업데이트된 ND40rs_v2로 대체 되었습니다.

<br>

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disk): 지원됨(가용성, 사용량 및 성능에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
InfiniBand: 지원됨<br>
Nvidia NVLink Interconnect: 지원됨<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 네트워크 대역폭 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32GB(NVLink) | 32 | 32 | 80000 / 800 | 24,000Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-linux.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

NVIDIA GPU 드라이버를 수동으로 설치하기로 한 경우, [Linux용 N 시리즈 GPU 드라이버 설정](./linux/n-series-driver-setup.md)을 참조하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
