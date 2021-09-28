---
title: Dav4 및 Dasv4 시리즈
description: Dav4 및 Dasv4 시리즈 VM에 대한 사양입니다.
author: mamccrea
ms.author: mamccrea
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.reviewer: jushiman
ms.openlocfilehash: 43602810d9526257c5980d6a113e6bc45c3ead77
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153428"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 및 Dasv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Dav4 및 Dasv4 시리즈는 최대 256MB L3 캐시의 다중 스레드 구성으로 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하는 새로운 크기로, 모든 8개 코어에 해당 L3 캐시 중 8MB를 전용으로 지정하여 범용 워크로드 실행을 위한 고객 옵션을 늘립니다. Dav4 시리즈와 Dasv4 시리즈는 D 및 Dsv3 시리즈와 동일한 메모리 및 디스크 구성을 가집니다.

## <a name="dav4-series"></a>Dav4 시리즈

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>


Dav4 시리즈 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며 최대 주파수 3.35GHz를 달성할 수 있습니다. Dav4 시리즈 크기는 vCPU, 메모리 및 임시 스토리지를 결합하여 대부분의 프로덕션 워크로드에 제공합니다. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. 프리미엄 SSD를 사용하려면 Dasv4 크기를 사용합니다. Dasv4 크기에 대한 가격 및 청구 미터는 Dav4 시리즈와 동일합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC 수 | 예상 네트워크 대역폭(Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4<sup>1</sup> |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 | 2000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 | 4000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 | 8000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 | 10000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000 / 1000 / 500 | 8 | 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000 / 1000 / 500 | 8 | 32000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000 / 1000 / 500 | 8 | 40,000 |

<sup>1</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 

## <a name="dasv4-series"></a>Dasv4 시리즈

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

Dasv4 시리즈 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며 최대 주파수 3.35GHz를 달성할 수 있고 프리미엄 SSD를 사용합니다. Dasv4 시리즈 크기는 vCPU, 메모리 및 임시 스토리지를 결합하여 대부분의 프로덕션 워크로드에 제공합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 버스트 캐시 및 임시 스토리지 처리량: IOPS/MBps | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps |  최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 예상 네트워크 대역폭(Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4<sup>2</sup>|2|8|16|4|4000 / 32 (50)| 4000/100  |3200 / 48| 4000/200 | 2 | 2000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)| 8000/200 |6400 / 96| 8000/200 |2 | 4000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)| 16000/400 |12800 / 192| 16000/400 |4 | 8000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)| 32000/800 |25600 / 384| 32000/800 |8 | 10000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)| 64000/1600 |51200 / 768| 64000/1600 |8 | 16000 |
| Standard_D48as_v4|48|192|384|32|96000 / 1020 (1200)| 96000/2000 |76800 / 1148| 80000/2000 |8 | 24000 |
| Standard_D64as_v4|64|256|512|32|128000 / 1020 (1600)| 128000/2000 |80000 / 1200| 80000/2000 |8 | 32000 | 
| Standard_D96as_v4|96|384|768|32|192000 / 1020 (2400)| 192000/2000 |80000 / 1200| 80000/2000 |8 | 40,000 |

<sup>1</sup> Dasv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.
<sup>2</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
