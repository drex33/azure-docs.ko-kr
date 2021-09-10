---
title: M 시리즈 - Azure Virtual Machines
description: M 시리즈 VM의 사양입니다.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 97691d3b9c2d94a42e3ff434a5bbaef1bd281975
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698838"
---
# <a name="m-series"></a>M 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

M 시리즈는 높은 vCPU 수(최대 128개의 vCPU)와 많은 양의 메모리(최대 3.8TiB)를 제공합니다. 높은 vCPU 수와 많은 양의 메모리를 활용하는 매우 큰 데이터베이스나 다른 애플리케이션에도 이상적입니다. M 시리즈 크기는 Intel&reg; Xeon&reg; CPU E7-8890 v3 @ 2.50GHz 및 Intel&reg; Xeon&reg; Platinum 8280M(Cascade Lake) 모두에서 지원됩니다.

M 시리즈 VM의 기능은 Intel&reg; Hyper-Threading 기술입니다.

[ACU](acu.md): 160-180<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[Write Accelerator](./how-to-enable-write-accelerator.md): 지원됨<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>3</sup>       | 8   | 218.75 | 256   | 8  | 10000/100(793)     | 5000/125   | 4|2000  |
| Standard_M16ms <sup>3</sup>      | 16  | 437.5  | 512   | 16 | 20000/200(1587)    | 10000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400(3174)    | 20000/500  | 8|8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400(3174)    | 20000/500  | 8|8000  |
| Standard_M32ms <sup>3</sup>      | 32  | 875    | 1024  | 32 | 40000/400(3174)    | 20000/500  | 8|8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80000/800(6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80000/800(6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1,3</sup>    | 64  | 1792   | 2048  | 64 | 80000/800(6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160000/1600(12696) | 80000/2000 | 8|30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600(12696) | 80000/2000 | 8|30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800(1228)    | 40000/1000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800(1228)    | 40000/1000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250000/1600(2456)  | 80000/2000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600(2456)  | 80000/2000 | 8|32000 |

<sup>1</sup> 64개를 초과하는 vCPU에는 지원되는 게스트 버전인 Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 및 Red Hat Enterprise Linux, LIS 4.2.1을 사용하는 CentOS 7.3 또는 Oracle Linux 7.3 중 하나가 필요합니다.

<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

<sup>3</sup> [사용 가능한 코어 크기 제한](./constrained-vcpu.md).

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
