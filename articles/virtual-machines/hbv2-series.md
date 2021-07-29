---
title: HBv2 시리즈 - Azure Virtual Machines
description: HBv2 시리즈 VM의 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 70bc81edde843f70dfb4403b63dfeb55ec2036fe
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750890"
---
# <a name="hbv2-series"></a>HBv2 시리즈

HBv2 시리즈 VM은 유체 역학, 유한 요소 분석, 저수지 시뮬레이션 등 메모리 대역폭으로 구동되는 애플리케이션에 최적화되어 있습니다. HBv2 VM에는 120개의 AMD EPYC 7742 프로세서 코어, CPU 코어당 4GB RAM이 있으며 동시 다중 스레딩이 없습니다. 각 HBv2 VM은 최대 340GB/초 메모리 대역폭, 최대 4테라플롭 FP64 컴퓨팅을 제공합니다.

HBv2 시리즈 VM은 200Gb/초 Mellanox HDR InfiniBand를 제공합니다. 이러한 VM은 최적화되고 일관된 RDMA 성능을 위해 차단되지 않는 패트 트리에 연결됩니다. 이러한 VM은 적응형 라우팅 및 동적으로 연결된 전송(표준 RC 및 UD 전송 외에 DCT)을 지원합니다. 이러한 기능은 애플리케이션 성능, 확장성 및 일관성을 향상시키며 해당 사용이 권장됩니다.

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disk): 지원됨(가용성, 사용량 및 성능에 대한 [자세한 정보](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨(성능 및 잠재적인 문제에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965)) <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
<br>

| 크기 | vCPU | 프로세서 | 메모리(GiB) | 메모리 대역폭 GB/초 | 기본 CPU 빈도(GHz) | 모든 코어 빈도(GHz, 최고) | 단일 코어 빈도(GHz, 최고) | RDMA 성능(Gb/s) | MPI 지원 | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 이더넷 vNIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | 모두 | 480 + 960 | 8 | 8 |

추가 정보:
- [아키텍처 및 VM 토폴로지](./workloads/hpc/hbv2-series-overview.md)
- 지원되는 OS를 포함하는 지원되는 [소프트웨어 스택](./workloads/hpc/hbv2-series-overview.md#software-specifications)
- HBv2 시리즈 VM의 예상 [성능](./workloads/hpc/hbv2-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
