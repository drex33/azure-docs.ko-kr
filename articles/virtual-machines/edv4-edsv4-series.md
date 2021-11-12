---
title: Edv4 및 Edsv4 시리즈
description: Ev4, Edv4, Esv4 및 Edsv4 시리즈 VM의 사양.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 3f1f1cfa0feb13b03abd5129098ab20c7b755b76
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401025"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 및 Edsv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM: heavy_check_mark: Windows VM: heavy_check_mark: 유연한 확장 집합: heavy_check_mark: 단일 확장 집합

Edv4 및 Edsv4 시리즈는 하이퍼 스레드 구성에서 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서로 실행되고 다양한 메모리 집약적 엔터프라이즈 애플리케이션에 이상적입니다. RAM 504GiB, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)가 제공됩니다. 또한 [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원합니다. 이 새로운 VM 크기는 [Gen2 VM](./generation-2.md)이 포함된 [Ev3/Esv3](./ev3-esv3-series.md)에 비해 로컬 스토리지가 50% 확장되고, 읽기 쓰기 로컬 디스크 IOPS가 향상됩니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다. 

## <a name="edv4-series"></a>Edv4 시리즈

Edv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서에서 실행됩니다. Edv4 가상 머신 크기는 대용량 고속 로컬 SSD 스토리지(최대 2,400GiB) 외에 RAM 504GiB가 탑재됩니다. 이 가상 머신은 메모리 집약적인 엔터프라이즈 애플리케이션과 지연이 낮은 고속 로컬 스토리지가 필요한 애플리케이션에 이상적입니다. 표준 SSD 및 표준 HDD 디스크 스토리지를 Edv4 VM에 연결할 수 있습니다. 

[ACU](acu.md): 195 - 210<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<sup>1</sup> <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)지원됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/MBps<sup>*</sup>  | 최대 NIC 수|최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4<sup>1</sup>  | 2  | 16  | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_E4d_v4              | 4  | 32  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_E8d_v4              | 8  | 64  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_E16d_v4             | 16 | 128 | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_E20d_v4             | 20 | 160 | 750  | 32 | 94000/1250  | 8 | 10000 |
| Standard_E32d_v4             | 32 | 256 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_E48d_v4             | 48 | 384 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_E64d_v4             | 64 | 504 | 2400 | 32 | 300000/4000 | 8 | 30000 |

<sup>*</sup>이러한IOPs 값은 [Gen2 VM](generation-2.md) 
 <sup>1</sup> 가속화된 네트워킹을 사용하여 단일 NIC에만 적용할 수 있습니다. <br>

## <a name="edsv4-series"></a>Edsv4 시리즈

Edsv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 프로세서에서 실행됩니다. Edsv4 가상 머신 크기는 대용량 고속 로컬 SSD 스토리지(최대 2,400GiB) 외에 RAM 504GiB가 탑재됩니다. 이 가상 머신은 메모리 집약적인 엔터프라이즈 애플리케이션과 지연이 낮은 고속 로컬 스토리지가 필요한 애플리케이션에 이상적입니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩 된 가상화](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): 지원 됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량: IOPS/MBps<sup>*</sup> | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수|최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4<sup>4</sup>    | 2  | 16  | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_E4ds_v4                | 4  | 32  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_E8ds_v4                | 8  | 64  | 300  | 16 | 38000/500   | 12800/192  | 16000/400  | 4 | 4000  |
| Standard_E16ds_v4               | 16 | 128 | 600  | 32 | 75000/1000   | 25600/384  | 32000/800  | 8 | 8000  |
| Standard_E20ds_v4               | 20 | 160 | 750  | 32 | 94000/1250  | 32000/480  | 40000/1000 | 8 | 10000 |
| Standard_E32ds_v4               | 32 | 256 | 1200 | 32 | 150000/2000 | 51200/768  | 64000/1600 | 8 | 16000 |
| Standard_E48ds_v4               | 48 | 384 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_E64ds_v4 <sup>2</sup>  | 64 | 504 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |
| Standard_E80ids_v4 <sup>3</sup> | 80 | 504 | 2400 | 64 | 375000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> [Gen2 VM](generation-2.md)을 사용하여 이러한 IOP 값을 보장할 수 있습니다.<br>
<sup>1</sup> Edsv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.<br>
<sup>2</sup> [사용 가능한 코어 크기 제한](./constrained-vcpu.md).<br>
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
