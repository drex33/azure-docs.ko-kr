---
title: Ddv5 및 Ddsv5 시리즈 - Azure Virtual Machines
description: Ddv5 및 Ddsv5 시리즈 VM에 대한 사양입니다.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: f2f4cb3a9d0af5bdf5dd873151b7adc899a24295
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401994"
---
# <a name="ddv5-and-ddsv5-series"></a>Ddv5 및 Ddsv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Ddv5 및 Ddsv5 시리즈는 하이퍼 스레드 구성의 3세대 Intel &reg; Xeon &reg; 8370C(Ice Lake) 프로세서에서 [실행될](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) Virtual Machines 있으며, 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 이 새로운 프로세서는 [Intel Turbo Boost &reg; Technology, Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)Advanced-Vector [ &reg; Extensions 512(Intel AVX-512) 및 Intel Deep Learning Boost가 있는 &reg; 3.5GHz의](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) 모든 코어 turbo 클록 속도를 제공합니다. [ &reg; ](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) 이러한 가상 머신은 중소 규모의 데이터베이스, 중소형 트래픽 웹 서버, 애플리케이션 서버 등 대부분의 엔터프라이즈 워크로드와 관련된 요구 사항을 충족할 수 있는 vC CPU, 메모리 및 임시 스토리지의 조합을 제공합니다.


## <a name="ddv5-series"></a>Ddv5 시리즈
Ddv5 시리즈 가상 머신은 3세대 Intel® Xeon® 8370C(Ice Lake) 프로세서에서 실행되어 최대 3.5GHz의 모든 코어 turbo 클록 속도에 도달합니다. 이러한 가상 머신은 최대 96개의 vCPU 및 384 GiB RAM뿐만 아니라 최대 3,600 GiB의 빠른 로컬 SSD 스토리지를 제공합니다. Ddv5 시리즈 가상 머신은 이전 세대에 비해 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다(예: 확장성 향상 및 업그레이드된 CPU 클래스). 또한 이러한 가상 머신은 빠르고 큰 로컬 SSD 스토리지(최대 3,600 GiB)를 제공합니다.

Ddv5 시리즈 가상 머신은 표준 SSD 및 표준 HDD 디스크 유형을 지원합니다. Premium SSD 또는 Ultra Disk 스토리지를 사용하려면 Ddsv5 시리즈 가상 머신을 선택합니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)


[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹:](../virtual-network/create-vm-accelerated-networking-cli.md)필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)지원됨 <br>
<br> 

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시된 최대 및 임시 스토리지 처리량: IOPS/MBps<sup>*</sup> | 최대 NIC 수|최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_D4d_v5               | 4  | 16  | 150  | 8  | 19000/250   | 2 | 12500 |
| Standard_D8d_v5               | 8  | 32  | 300  | 16 | 38000/500   | 4 | 12500 |
| Standard_D16d_v5              | 16 | 64  | 600  | 32 | 75000/1000  | 8 | 12500 |
| Standard_D32d_v5              | 32 | 128 | 1200 | 32 | 150000/2000 | 8 | 16000 |
| Standard_D48d_v5              | 48 | 192 | 1800 | 32 | 225000/3000 | 8 | 24000 |
| Standard_D64d_v5              | 64 | 256 | 2400 | 32 | 300000/4000 | 8 | 30000 |
| Standard_D96d_v5              | 96 | 384 | 3600 | 32 | 450000/4000 | 8 | 35000 |

<sup>*</sup> [Gen2 VM](generation-2.md)을 사용하여 이러한 IOP 값을 보장할 수 있습니다.<br>
<sup>1</sup> 가속화된 네트워킹이 필요하며 모든 Ddv5 가상 머신에서 기본적으로 켜져 있습니다.<br>
<sup>2</sup> 가속화된 네트워킹은 두 개의 NIC에 적용할 수 있습니다.

## <a name="ddsv5-series"></a>Ddsv5 시리즈

Ddsv5 시리즈 가상 머신은 3세대 Intel® Xeon® 8370C(Ice Lake) 프로세서에서 실행되어 최대 3.5GHz의 모든 코어 turbo 클록 속도에 도달합니다.  이러한 가상 머신은 최대 96개의 vCPU 및 384 GiB RAM뿐만 아니라 최대 3,600 GiB의 빠른 로컬 SSD 스토리지를 제공합니다. Ddsv5 시리즈 가상 머신은 이전 세대에 비해 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다(예: 확장성 향상 및 업그레이드된 CPU 클래스).

Ddsv5 시리즈 가상 머신은 표준 SSD, 표준 HDD 및 Premium SSD 디스크 유형을 지원합니다. 지역별 가용성에 따라 Ultra Disk 스토리지를 연결할 수도 있습니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹:](../virtual-network/create-vm-accelerated-networking-cli.md)필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)지원됨 <br>
<br> 


| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시된 최대 및 임시 스토리지 처리량: IOPS/MBps<sup>*</sup> | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 캐시되지 않은 최대 버스트 디스크 처리량: IOPS/MBps<sup>3</sup> | 최대 NIC 수 | 최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v5<sup>1,2</sup> | 2  | 8   | 75   | 4  | 9000/125    | 3750/85     | 10000/1200 | 2 | 12500 |
| Standard_D4ds_v5               | 4  | 16  | 150  | 8  | 19000/250   | 6400/145    | 20000/1200 | 2 | 12500 |
| Standard_D8ds_v5               | 8  | 32  | 300  | 16 | 38000/500   | 12800/290   | 20000/1200 | 4 | 12500 |
| Standard_D16ds_v5              | 16 | 64  | 600  | 32 | 75000/1000  | 25600/600   | 40000/1200 | 8 | 12500 |
| Standard_D32ds_v5              | 32 | 128 | 1200 | 32 | 150000/2000 | 51200/865   | 80000/2000 | 8 | 16000 |
| Standard_D48ds_v5              | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1315  | 80000/3000 | 8 | 24000 |
| Standard_D64ds_v5              | 64 | 256 | 2400 | 32 | 375000/4000 | 80000/1735  | 80000/3000 | 8 | 30000 |
| Standard_D96ds_v5              | 96 | 256 | 3600 | 32 | 450000/4000 | 80000/2600  | 80000/4000 | 8 | 35000 |

<sup>*</sup> [Gen2 VM](generation-2.md)을 사용하여 이러한 IOP 값을 보장할 수 있습니다.<br>
<sup>1</sup> 가속화된 네트워킹이 필요하며 모든 Ddsv5 가상 머신에서 기본적으로 켜져 있습니다.<br>
<sup>2</sup> 가속화된 네트워킹은 두 개의 NIC에 적용할 수 있습니다.<br>
<sup>3</sup> Ddsv5 시리즈 가상 머신은 디스크 성능을 [버스트하고](disk-bursting.md) 한 번에 최대 30분 동안 최대 버스트 최대값을 얻을 수 있습니다.

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
