---
title: Edv5 및 Edsv5 시리즈-Azure Virtual Machines
description: Edv5 및 Edsv5 시리즈 Vm에 대 한 사양입니다.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 8a2fac1ded1cd17d9e47500eda06e58ca21fc6e7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850055"
---
# <a name="edv5-and-edsv5-series"></a>Edv5 및 Edsv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Edv5 및 Edsv5 Virtual Machines 시리즈는 &reg; &reg; [하이퍼 스레드](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 구성에서 3 세대 Intel Xeon Platinum 8370c (Ice Lake) 프로세서에서 실행 되므로 대부분의 일반 용도의 작업에 대해 더 나은 가치를 제공 합니다. 이 새로운 프로세서는 [intel &reg; 터보 부스트 기술로](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)3.5 GHz의 모든 코어 터보 클록 속도, [intel &reg; Advanced-Vector 확장 512 (intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) 및 [intel &reg; Deep Learning 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 제공 합니다. 최대 672 GiB의 RAM을 사용 하는 이러한 가상 머신은 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램, 관계형 데이터베이스 서버 및 메모리 내 분석 워크 로드에 이상적입니다. 이러한 Vm은 신속 하 고 규모가 많은 로컬 SSD 저장소 (최대 3900 GiB)를 기능도 합니다.

## <a name="edv5-series"></a>Edv5 시리즈

Edv5 시리즈 가상 머신은 3 세대 Intel® Xeon® 플래티넘 8370C (Ice) 프로세서에서 실행 되며, 모든 코어 터보 클록 속도는 최대 3.5 g h z에 도달 합니다.  이러한 가상 머신은 최대 104 vCPU 및 672 GiB의 RAM과 최대 3800 GiB의 로컬 SSD 저장소를 제공 합니다. Edv5 시리즈 가상 머신은 낮은 대기 시간, 고속 로컬 저장소를 활용 하는 메모리 집약적 엔터프라이즈 응용 프로그램 및 응용 프로그램에 적합 합니다.

Edv5 시리즈 가상 머신은 표준 SSD 및 표준 HDD 디스크 유형을 지원 합니다. 프리미엄 SSD 또는 Ultra Disk storage를 사용 하려면 Edsv5 시리즈 virtual machines를 선택 합니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps<sup>*</sup>  | 최대 NIC 수|최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v5<sup>1, 2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 2 | 12500 |
| Standard_E4d_v5                | 4   | 32  | 150  | 8  | 19000/250   | 2 | 12500 |
| Standard_E8d_v5                | 8   | 64  | 300  | 16 | 38000/500   | 4 | 12500 |
| Standard_E16d_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 8 | 12500 |
| Standard_E20d_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 8 | 12500  |
| Standard_E32d_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 8 | 16000  |
| Standard_E48d_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 8 | 24000  |
| Standard_E64d_v5               | 64  | 512 | 2400 | 32 | 300000/4000 | 8 | 30000  |
| Standard_E96d_v5               | 96  | 672 | 3600 | 32 | 450000/4000 | 8 | 35000  |
| Standard_E104id_v5<sup>3</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 8 | 100000 |

<sup>*</sup> [Gen2 VM](generation-2.md)을 사용하여 이러한 IOP 값을 보장할 수 있습니다.<br>
<sup>1</sup> 가속화 된 네트워킹은 모든 Edv5 가상 머신에서 기본적으로 설정 되 고 설정 됩니다.<br>
<sup>2 2</sup> 개의 nic에 가속화 된 네트워킹을 적용할 수 있습니다.<br>
<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 [격리](../security/fundamentals/isolation-choices.md#compute-isolation) 되어 있습니다.


## <a name="edsv5-series"></a>Edsv5 시리즈

Edsv5 시리즈 가상 머신은 3 세대 Intel® Xeon® 플래티넘 8370C (Ice) 프로세서에서 실행 되며, 모든 코어 터보 클록 속도는 최대 3.5 g h z에 도달 합니다.  이러한 가상 머신은 최대 104 vCPU 및 672 GiB의 RAM과 최대 3800 GiB의 로컬 SSD 저장소를 제공 합니다. Edsv5 시리즈 가상 머신은 낮은 대기 시간, 고속 로컬 저장소를 활용 하는 메모리 집약적 엔터프라이즈 응용 프로그램 및 응용 프로그램에 적합 합니다.

Edsv5 시리즈 가상 머신은 표준 SSD 및 표준 HDD 디스크 유형을 지원 합니다. 표준 ssd, 표준 hdd 및 Premium ssd 디스크 저장소를 이러한 vm에 연결할 수 있습니다. 지역별 가용성에 따라 Ultra Disk storage를 연결할 수도 있습니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps<sup>*</sup> | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시 되지 않은 디스크 처리량: IOPS/MBps<sup>5</sup> | 최대 NIC 수 | 최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v5<sup>1, 2</sup>  | 2   | 16  | 75   | 4  | 9000/125    | 3750/85      | 10000/1200 | 2 | 12500 |
| Standard_E4ds_v5                | 4   | 32  | 150  | 8  | 19000/250   | 6400/145     | 20000/1200 | 2 | 12500 |
| Standard_E8ds_v5                | 8   | 64  | 300  | 16 | 38000/500   | 12800/290    | 20000/1200 | 4 | 12500 |
| Standard_E16ds_v5               | 16  | 128 | 600  | 32 | 75000/1000  | 25600/600    | 40000/1200 | 8 | 12500 |
| Standard_E20ds_v5               | 20  | 160 | 750  | 32 | 94000/1250  | 32000/750    | 64000/1600 | 8 | 12500  |
| Standard_E32ds_v5               | 32  | 256 | 1200 | 32 | 150000/2000 | 51200/865    | 80000/2000 | 8 | 16000  |
| Standard_E48ds_v5               | 48  | 384 | 1800 | 32 | 225000/3000 | 76800/1315   | 80000/3000 | 8 | 24000  |
| Standard_E64ds_v5               | 64  | 512 | 2400 | 32 | 375000/4000 | 80000/1735   | 80000/3000 | 8 | 30000  |
| Standard_E96ds_v5<sup>3</sup>   | 96  | 672 | 3600 | 32 | 450000/4000 | 80000/2600   | 80000/4000 | 8 | 35000  |
| Standard_E104ids_v5<sup>4</sup> | 104 | 672 | 3800 | 64 | 450000/4000 | 120000/4000  | 120000/4000 | 8 | 100000 |

<sup>*</sup> [Gen2 VM](generation-2.md)을 사용하여 이러한 IOP 값을 보장할 수 있습니다.<br>
<sup>1</sup> 가속화 된 네트워킹은 모든 Edsv5 가상 머신에서 기본적으로 설정 되 고 설정 됩니다.<br>
<sup>2 2</sup> 개의 nic에 가속화 된 네트워킹을 적용할 수 있습니다.<br>
<sup>3</sup> [제한 된 코어](constrained-vcpu.md) 크기를 사용할 수 있습니다.<br>
<sup>4</sup> 인스턴스는 단일 고객 전용의 하드웨어에 [격리](../security/fundamentals/isolation-choices.md#compute-isolation) 되어 있습니다.<br>
<sup>5 개의</sup> Edsv5 시리즈 가상 머신은 디스크 성능을 [버스트](disk-bursting.md) 하 고 최대 30 분 동안 최대 버스트를 받을 수 있습니다.

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
