---
title: Ddv4 및 Ddsv4 시리즈
description: Dv4, Ddv4, Dsv4 및 Ddsv4 시리즈 VM에 대한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 3fab06386a6f7030a9c3b6a78b5939883eb5d3e3
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370729"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 및 Ddsv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM: heavy_check_mark: Windows VM: heavy_check_mark: 유연한 확장 집합: heavy_check_mark: 단일 확장 집합

Ddv4 및 Ddsv4 시리즈는 하이퍼 스레드 구성의 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되어 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공하며, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 사용합니다. 또한 [Intel&reg; 딥 러닝 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원합니다. 이 새로운 VM 크기는 [2세대 VM](./generation-2.md)이 포함된 [Dv3/Dsv3](./dv3-dsv3-series.md)에 비해 로컬 스토리지가 50% 확장되고, 읽기 쓰기 로컬 디스크 IOPS가 향상됩니다.

D 시리즈 사용 사례로는 엔터프라이즈급 애플리케이션, 관계형 데이터베이스, 메모리 내 캐싱 및 분석 등이 있습니다.

## <a name="ddv4-series"></a>Ddv4 시리즈

Ddv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Ddv4 시리즈는 대부분의 프로덕션 워크로드에 대한 vCPU, 메모리 및 임시 디스크의 조합을 제공합니다.

새 Ddv4 VM 크기는 빠르고, 더 큰 로컬 SSD 스토리지(최대 2,400GiB)를 포함하며, 임시 스토리지에 대한 빠른 읽기/쓰기를 필요로 하는 애플리케이션이나 캐시 또는 임시 파일에 대한 임시 스토리지를 필요로 하는 애플리케이션처럼 짧은 대기 시간과 고속 로컬 스토리지를 활용하는 애플리케이션용으로 설계되었습니다. 표준 SSD 및 표준 HDD 스토리지를 Ddv4 VM에 연결할 수 있습니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md)지원됨 <br>
<br> 

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/MBps<sup>*</sup> | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4<sup>1</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 1000  |
| Standard_D4d_v4             | 4  | 16  | 150  | 8  | 19000/250   | 2 | 2000  |
| Standard_D8d_v4             | 8  | 32  | 300  | 16 | 38000/500   | 4 | 4000  |
| Standard_D16d_v4            | 16 | 64  | 600  | 32 | 75000/1000   | 8 | 8000  |
| Standard_D32d_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_D64d_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 8 | 30000 |




<sup>*</sup> 해당 IOP 값은 [Gen2 VM](generation-2.md)을 사용하여 얻을 수 있습니다.<br>
<sup>1</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 

## <a name="ddsv4-series"></a>Ddsv4 시리즈

Ddsv4 시리즈는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Ddsv4 시리즈는 대부분의 프로덕션 워크로드에 대한 vCPU, 메모리 및 임시 디스크의 조합을 제공합니다.

새 Ddsv4 VM 크기는 빠르고, 더 큰 로컬 SSD 스토리지(최대 2,400GiB)를 포함하며, 임시 스토리지에 대한 빠른 읽기/쓰기를 필요로 하는 애플리케이션이나 캐시 또는 임시 파일에 대한 임시 스토리지를 필요로 하는 애플리케이션처럼 짧은 대기 시간과 고속 로컬 스토리지를 활용하는 애플리케이션용으로 설계되었습니다. 

 > [!NOTE]
 >Ddsv4 크기의 가격 및 요금 청구 기준은 Ddv4 시리즈와 동일합니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md)지원됨 <br>
<br> 

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/MBps<sup>*</sup> | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps |  최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4<sup>2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3200/48    | 4000/200   | 2 | 1000  |
| Standard_D4ds_v4             | 4  | 16  | 150  | 8  | 19000/250   | 6400/96    | 8000/200   | 2 | 2000  |
| Standard_D8ds_v4             | 8  | 32  | 300  | 16 | 38000/500   | 12800/192  | 16000/400  | 4 | 4000  |
| Standard_D16ds_v4            | 16 | 64  | 600  | 32 | 85000/1000   | 25600/384  | 32000/800  | 8 | 8000  |
| Standard_D32ds_v4            | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/768  | 64000/1600 | 8 | 16000 |
| Standard_D48ds_v4            | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_D64ds_v4            | 64 | 256 | 2400 | 32 | 300000/4000 | 80000/1200 | 80000/2000 | 8 | 30000 |

<sup>*</sup> 해당 IOP 값은 [Gen2 VM](generation-2.md)을 사용하여 얻을 수 있습니다.<br>
<sup>1</sup> Ddsv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.<br>
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

디스크 유형에 대한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disks)


## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
