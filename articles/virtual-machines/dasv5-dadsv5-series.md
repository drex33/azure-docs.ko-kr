---
title: Dasv5 및 Dadsv5 시리즈 - Azure Virtual Machines
description: Dasv5 및 Dadsv5 시리즈 VM에 대한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 2a4a48a66def1633aea5b85d9e51bdcf8bc1378e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483050"
---
# <a name="dasv5-and-dadsv5-series"></a>Dasv5 및 Dadsv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Dasv5 시리즈 및 Dadsv5 시리즈는 최대 256MB L3 캐시가 있는 다중 스레드 구성에서 AMD의 3세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용하여 범용 워크로드를 실행하기 위한 고객 옵션을 증가합니다. 이러한 가상 머신은 중소 규모의 데이터베이스, 중소 규모의 트래픽 웹 서버, 애플리케이션 서버 등 대부분의 엔터프라이즈 워크로드와 관련된 요구 사항을 충족하기 위해 vC CPU와 메모리의 조합을 제공합니다.

## <a name="dasv5-series"></a>Dasv5 시리즈

Dasv5 시리즈 VM은 최대 3.7GHz의 상승된 빈도를 달성할 수 있는 AMD의 3세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용합니다. Dasv5 시리즈 크기는 대부분의 프로덕션 워크로드에 vCPU와 메모리의 조합을 제공합니다. 로컬 디스크가 없는 새 VM은 로컬 임시 디스크가 필요하지 않은 워크로드에 더 나은 가치 제안을 제공합니다.

> [!NOTE]
> 질문과 대답은 [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

Dasv5 시리즈 가상 머신은 표준 SSD, 표준 HDD 및 Premium SSD 디스크 유형을 지원합니다. 지역별 가용성에 따라 Ultra Disk 스토리지를 연결할 수도 있습니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)

[Premium Storage](premium-storage-performance.md): 지원됨 <br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨 <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨 <br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨 <br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대 <br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br><br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2as_v5  | 2  | 8   | 원격 스토리지 전용 | 4  | 3750/82    | 10000/600   | 2 | 12500  |
| Standard_D4as_v5  | 4  | 16  | 원격 스토리지 전용 | 8  | 6400/144   | 20000/600   | 2 | 12500  |
| Standard_D8as_v5  | 8  | 32  | 원격 스토리지 전용 | 16 | 12800/200  | 20000/600   | 4 | 12500  |
| Standard_D16as_v5 | 16 | 64  | 원격 스토리지 전용 | 32 | 25600/384  | 40000/800   | 8 | 12500 |
| Standard_D32as_v5 | 32 | 128 | 원격 스토리지 전용 | 32 | 51200/768  | 80000/1600  | 8 | 16000 |
| Standard_D48as_v5 | 48 | 192 | 원격 스토리지 전용 | 32 | 76800/1152 | 80000/2000  | 8 | 24000 |
| Standard_D64as_v5 | 64 | 256 | 원격 스토리지 전용 | 32 | 80000/1200 | 80000/2000  | 8 | 32000 |
| Standard_D96as_v5 | 96 | 384 | 원격 스토리지 전용 | 32 | 80000/1600 | 80000/2000  | 8 | 40,000 |


<sup>1</sup> Dasv5 시리즈 VM은 디스크 성능을 [버스트하고](disk-bursting.md) 한 번에 최대 30분 동안 최대 버스트 최대값을 얻을 수 있습니다.


## <a name="dadsv5-series"></a>Dadsv5 시리즈

Dadsv5 시리즈는 최대 3.7GHz의 상승된 빈도를 달성할 수 있는 AMD의 3세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용합니다. 이 5 시리즈 크기는 대부분의 프로덕션 워크로드에 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다. 새 VM에는 [Gen2](generation-2.md) VM이 있는 [Dav4/Dasv4](dav4-dasv4-series.md) 크기에 비해 읽기 및 쓰기 둘 다에 대해 더 나은 로컬 디스크 IOPS뿐만 아니라 50% 더 큰 로컬 스토리지가 있습니다.

표준 SSD, 표준 HDD 및 SSD 디스크 유형 Premium 지원합니다. 지역별 가용성에 따라 Ultra Disk 스토리지를 연결할 수도 있습니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)


[Premium Storage](premium-storage-performance.md): 지원됨 <br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨 <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨 <br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨 <br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대 <br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br><br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2ads_v5  | 2  | 8   | 75   | 4  | 9000 / 125    | 3750/82    | 10000/600  | 2 | 12500  |
| Standard_D4ads_v5  | 4  | 16  | 150  | 8  | 19000 / 250   | 6400/144   | 20000/600  | 2 | 12500  |
| Standard_D8ads_v5  | 8  | 32  | 300  | 16 | 38000 / 500   | 12800/200  | 20000/600  | 4 | 12500  |
| Standard_D16ads_v5 | 16 | 64  | 600  | 32 | 75000 / 1000  | 25600/384  | 40000/800  | 8 | 12500 |
| Standard_D32ads_v5 | 32 | 128 | 1200 | 32 | 150000 / 2000 | 51200/768  | 80000/1000 | 8 | 16000 |
| Standard_D48ads_v5 | 48 | 192 | 1800 | 32 | 225000 / 3000 | 76800/1152 | 80000/200 | 8 | 24000 |
| Standard_D64ads_v5 | 64 | 256 | 2400 | 32 | 300000 / 4000 | 80000/1200 | 80000/2000 | 8 | 32000 |
| Standard_D96ads_v5 | 96 | 384 | 3600 | 32 | 45만/4000 | 80000/1600 | 80000/2000 | 8 | 40,000 |

* 이러한 IOPs 값은 Gen2 Vm을 사용 하 여 달성할 수 있습니다.<br>
<sup>1</sup> Dadsv5 시리즈 vm은 디스크 성능을 [버스트](disk-bursting.md) 하 고 한 번에 최대 30 분 동안 최대 버스트를 얻을 수 있습니다.


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.