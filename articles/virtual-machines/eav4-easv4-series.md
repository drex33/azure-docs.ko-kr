---
title: Eav4 시리즈 및 Easv4 시리즈
description: Eav4 및 Easv4 시리즈 VM에 대한 사양입니다.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: ayshak
ms.reviewer: jushiman
ms.openlocfilehash: 5cefbdb7cdb3d44c08c61889d3a6420d31729989
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471747"
---
# <a name="eav4-and-easv4-series"></a>Eav4 및 Easv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Eav4 시리즈와 Easv4 시리즈는 최대 256MB의 L3 캐시를 갖춘 다중 스레드 구성에서 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하여 대부분의 메모리 최적화 워크로드 실행 옵션을 늘립니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 및 Esv3 시리즈와 동일한 메모리 및 디스크 구성을 가집니다.

## <a name="eav4-series"></a>Eav4 시리즈

[ACU](acu.md): 230 - 260<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

Eav4 시리즈 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며 최대 주파수 3.35GHz를 달성할 수 있습니다. Eav4 시리즈 크기는 메모리 집약적 엔터프라이즈 응용 프로그램에 적합합니다. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. 프리미엄 SSD를 사용하려면 Easv4 시리즈 크기를 사용합니다. Easv4 크기에 대한 가격 및 청구 미터는 Eav3 시리즈와 동일합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4<sup>1</sup>|2|16|50|4|3000 / 46 / 23|2 | 800 |
| 표준\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 | 1600 |
| 표준\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 | 3200 |
| 표준\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 | 6400 |
| 표준\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 | 8000 |
| 표준\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 | 12800 |
| 표준\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 | 19200 |
| 표준\_E64a\_v4|64|512|1600|32|96000 / 1000 (500)|8 | 25600 |
| 표준\_E96a\_v4|96|672|2400|32|96000 / 1000 (500)|8 | 32000 |

<sup>1</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 


## <a name="easv4-series"></a>Easv4 시리즈

[ACU](acu.md): 230 - 260<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

Easv4 시리즈 크기는 2.35Ghz AMD EPYC<sup>TM</sup> 7452 프로세서를 기반으로 하며 최대 주파수 3.35GHz를 달성할 수 있고 프리미엄 SSD를 사용합니다. Easv4 시리즈 크기는 메모리 집약적 엔터프라이즈 응용 프로그램에 적합합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 버스트 캐시 및 임시 스토리지 처리량: IOPS/MBps<sup>1</sup> | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 예상 네트워크 대역폭(Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4<sup>3</sup>|2|16|32|4|4000 / 32 (50)| 4000/100 |3200 / 48| 4000/200 |2 | 800 |
| Standard_E4as_v4 <sup>2</sup>|4|32|64|8|8000 / 64 (100)| 8000/200 |6400 / 96| 8000/200 |2 | 1600 |
| Standard_E8as_v4 <sup>2</sup>|8|64|128|16|16000 / 128 (200)| 16000/400 |12800 / 192| 16000/400 |4 | 3200 |
| Standard_E16as_v4 <sup>2</sup>|16|128|256|32|32000 / 255 (400)| 32000/800 |25600 / 384| 32000/800 |8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)| 40000/1000 |32000 / 480| 40000/1000 |8 | 8000 |
| Standard_E32as_v4<sup>2</sup>|32|256|512|32|64000 / 510 (800)| 64000/1600 |51200 / 768| 64000/1600 |8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)| 96000/2000 |76800 / 1148| 80000/2000 |8 | 19200 |
| Standard_E64as_v4<sup>2</sup>|64|512|1024|32|128000 / 1020 (1600)| 128000/2000 |80000 / 1200| 80000/2000 |8 | 25600 |
| Standard_E96as_v4 <sup>2</sup>|96|672|1344|32|192000 / 1020 (2400)| 192000/2000 |80000 / 1200| 80000/2000 |8 | 32000 |

<sup>1</sup> Easv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다. <br>
<sup>2</sup> [사용 가능한 코어 크기 제한](./constrained-vcpu.md). <br>
<sup>3</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 


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
