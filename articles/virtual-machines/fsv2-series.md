---
title: Fsv2 시리즈
description: Fsv2 시리즈 VM의 사양입니다.
author: brbell
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 4c652a9292caf8e7ac198ac6922b11b78a85781e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401424"
---
# <a name="fsv2-series"></a>Fsv2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Fsv2 시리즈는 Intel® Xeon® Platinum 8272CL (Cascade Lake) 프로세서 및 Intel® Xeon® Platinum 8168 (Skylake) 프로세서에서 실행됩니다. 모든 코어 터보 클럭 속도가 3.4GHz이고 최대 싱글 코어 터보 주파수는 3.7GHz입니다. Intel® AVX-512 지침은 Intel Scalable Processor에 새로 있습니다. 지침은 단일 및 이중 정밀 부동 소수점 작업에서 워크로드를 벡터 처리할 때의 성능을 최대 2배로 끌어올립니다. 즉, 계산 워크로드를 빠르게 처리합니다.

Fsv2 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)지원됨 <br>
<br>

| 크기 | vCPU 수 | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps |  최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> |최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2<sup>4</sup>  | 2  | 4   | 16  | 4  | 4000/31(32)       | 3200/47    | 4000/200 | 2| 5,000   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63(64)       | 6400/95    | 8000/200 | 2|10000  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 16000/400 | 4|12500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 32000/800 | 4|12500  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 64000/1600 | 8|16000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 80000/2000 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 80000/2000 | 8|28000 |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 80000/2000 | 8|30000 |

<sup>1</sup> Fsv2 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.

<sup>2</sup> 64개 이상의 vCPU를 사용하려면 다음의 지원되는 게스트 운영 체제 중 하나가 필요합니다.

- Windows Server 2016 이상
- Ubuntu 16.04 LTS 이상, Azure에서 조정된 커널(4.15커널 이상)
- SLES 12 SP2 이상
- Microsoft에서 제공하는 LIS package 4.3.1 이상이 설치된 RHEL 또는 CentOS 버전 6.7 ~ 6.10
- Microsoft에서 제공하는 LIS package 4.2.1 이상이 설치된 RHEL 또는 CentOS 버전 7.3
- RHEL 또는 CentOS 버전 7.6 이상
- UEK4 이상을 사용하는 Oracle Linux
- 백포트 커널이 있는 Debian 9, Debian 10 이상
- 4\.14 커널이 있는 CoreOS 이상

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.<br>
<sup>4</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다.

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
