---
title: Ev4 및 Esv4 시리즈 - Azure Virtual Machines
description: Ev4 및 Esv4 시리즈 VM의 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: e4278cb662d4e3ee518a52651462ca04d634c556
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688300"
---
# <a name="ev4-and-esv4-series"></a>Ev4 및 Esv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Ev4 및 Esv4 시리즈는 하이퍼 스레드 구성의 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되며, 다양한 메모리 집약적 엔터프라이즈 애플리케이션에 이상적이며, 최대 504GiB의 RAM을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다.

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

## <a name="ev4-series"></a>Ev4 시리즈

Ev4 시리즈 크기는 Intel Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Ev4 시리즈 인스턴스는 메모리 집약적 엔터프라이즈 애플리케이션에 적합합니다. Ev4 시리즈 VM은 Intel&reg; 하이퍼 스레딩 기술을 특징으로 합니다.

원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Esv4 크기를 사용합니다. Esv4 크기의 가격 및 요금 청구 기준은 Ev4 시리즈와 동일합니다.

[ACU](acu.md): 195 - 210<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4<sup>1</sup>  | 2 | 16   | 원격 스토리지 전용 | 4 | 2|5,000  |
| Standard_E4_v4  | 4 | 32  | 원격 스토리지 전용 | 8 | 2|10000  |
| Standard_E8_v4  | 8 | 64 | 원격 스토리지 전용 | 16 | 4|12500 |
| Standard_E16_v4 | 16 | 128 | 원격 스토리지 전용 | 32 | 8|12500 |
| Standard_E20_v4 | 20 | 160 | 원격 스토리지 전용 | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | 원격 스토리지 전용 | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | 원격 스토리지 전용 | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | 원격 스토리지 전용 | 32| 8|30000 |

<sup>1</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 


## <a name="esv4-series"></a>Esv4 시리즈

Esv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Esv4 시리즈 인스턴스는 메모리 집약적 엔터프라이즈 애플리케이션에 적합합니다. Evs4 시리즈 VM은 Intel&reg; 하이퍼 스레딩 기술을 특징으로 합니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>


| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> |최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4<sup>4</sup>  | 2 | 16  | 원격 스토리지 전용 | 4 | 3200/48 | 4000/200 | 2|5,000  |
| Standard_E4s_v4  | 4 | 32  | 원격 스토리지 전용 | 8 | 6400/96 | 8000/200 | 2|10000  |
| Standard_E8s_v4  | 8 | 64  | 원격 스토리지 전용 | 16 | 12800/192 | 16000/400 | 4|12500 |
| Standard_E16s_v4 | 16 | 128 | 원격 스토리지 전용 | 32 | 25600/384 | 32000/800 | 8|12500 |
| Standard_E20s_v4 | 20 | 160 | 원격 스토리지 전용 | 32 | 32000/480  | 40000/1000 | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | 원격 스토리지 전용 | 32 | 51200/768  | 64000/1600 | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | 원격 스토리지 전용 | 32 | 76800/1152 | 80000/2000 | 8|24000 |
| Standard_E64s_v4 <sup>2</sup> | 64 | 504| 원격 스토리지 전용 | 32 | 80000/1200 | 80000/2000 | 8|30000 |
| Standard_E80is_v4 <sup>3</sup> | 80 | 504 | 원격 스토리지 전용 | 32 | 80000/1200 | 80000/2000 | 8|30000 |

<sup>1</sup> Esv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.<br>
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

디스크 유형에 대한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
