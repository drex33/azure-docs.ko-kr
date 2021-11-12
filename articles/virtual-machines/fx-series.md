---
title: FX 시리즈
description: FX 시리즈 VM의 사양입니다.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: jushiman
ms.openlocfilehash: f287fddc2f7639f2ff61588c9d0fe8068cb675dc
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402165"
---
# <a name="fx-series"></a>FX 시리즈

FX 시리즈는 Intel® Xeon® Gold 6246R(Cascade Lake) 프로세서에서 실행됩니다. 4\.0GHz의 올코어 터보 주파수, vCPU당 21GB RAM, 최대 1TB의 총 RAM 및 로컬 임시 스토리지를 제공합니다. FX 시리즈는 높은 CPU 클록 속도와 높은 메모리 대 CPU 비율이 필요한 워크로드, 코어당 라이선스 비용이 높은 워크로드 및 높은 단일 코어 성능이 필요한 애플리케이션에 유용합니다. FX 시리즈의 일반적인 사용 사례는 EDA(전자 디자인 자동화) 워크로드입니다.

FX 시리즈 VM은 [Intel® Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel® Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel® Advanced Vector Extensions 512(Intel® AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 제공합니다.

[ACU](acu.md): 310 - 340<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)지원됨 <br>
<br>

| 크기 | vCPU 수 | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_FX4mds  | 4   | 84   | 168  | 8   | 40000/343     | 6700/104   | 2 | 4000  |
| Standard_FX12mds | 12  | 252  | 504  | 24  | 100000/1029   | 20000/314  | 4 | 8000  |
| Standard_FX24mds | 24  | 504  | 1008 | 32  | 200000/2057   | 40000/629  | 4 | 16000 |
| Standard_FX36mds | 36  | 756  | 1512 | 32  | 300000/3086   | 60000/944  | 8 | 24000 |
| Standard_FX48mds | 48  | 1008 | 2016 | 32  | 400000/3871   | 80000/1258 | 8 | 32000 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disks)


## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
