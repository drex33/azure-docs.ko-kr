---
title: HC 시리즈 - Azure Virtual Machines
description: HC 시리즈 VM의 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 6cd934f185a9317f031cde1b70fd0fbca71d6c99
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072353"
---
# <a name="hc-series"></a>HC 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

HC 시리즈 VM은 암시적 유한 요소 분석, 분자 역학, 계산 화학 등 조밀한 컴퓨팅으로 구동되는 애플리케이션에 최적화되어 있습니다. HC VM은 Intel Xeon Platinum 8168 프로세서 코어 44개, CPU 코어당 8GB RAM을 제공하며 하이퍼스레딩은 제공하지 않습니다. Intel Xeon Platinum 플랫폼은 Intel Math Kernel Library 및 AVX-512와 같은 고급 벡터 처리 기능과 같은 다양한 소프트웨어 도구 에코시스템을 지원합니다.

HC 시리즈 VM은 100Gb/초 Mellanox EDR InfiniBand를 제공합니다. 해당 VM은 최적화되고 일관된 RDMA 성능을 위해 차단되지 않는 패트 트리에 연결됩니다. 해당 VM은 적응형 라우팅 및 동적으로 연결된 전송(표준 RC 및 UD 전송 외에 DCT)을 지원합니다. 해당 기능은 애플리케이션 성능, 확장성, 일관성을 향상하며 해당 사용이 권장됩니다. 

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disks): 지원됨(가용성, 사용량 및 성능에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨(성능 및 잠재적인 문제에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965))<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
<br>

| 크기 | vCPU | 프로세서 | 메모리(GiB) | 메모리 대역폭 GB/초 | 기본 CPU 빈도(GHz) | 모든 코어 빈도(GHz, 최고) | 단일 코어 빈도(GHz, 최고) | RDMA 성능(Gb/s) | MPI 지원 | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 이더넷 vNIC |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | 모두 | 700 | 4 | 8 |

추가 정보:
- [아키텍처 및 VM 토폴로지](./workloads/hpc/hc-series-overview.md)
- 지원되는 OS를 포함하는 지원되는 [소프트웨어 스택](./workloads/hpc/hc-series-overview.md#software-specifications)
- HC 시리즈 VM의 예상 [성능](./workloads/hpc/hc-series-performance.md)

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
