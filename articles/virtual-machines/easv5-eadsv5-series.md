---
title: Easv5 및 Eadsv5 시리즈-Azure Virtual Machines
description: Easv5 및 Eadsv5 시리즈 Vm에 대 한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/8/2021
ms.openlocfilehash: 4628f6c6d368f5d62a8c7e5a2dd07adab9d0a3b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482968"
---
# <a name="easv5-and-eadsv5-series"></a>Easv5 및 Eadsv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Easv5 시리즈 및 Eadsv5 시리즈는 최대 256 MB L3 캐시가 포함 된 다중 스레드 구성에서 AMD의 3 세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용 하 여 대부분의 메모리 액세스에 최적화 된 워크 로드를 실행 하는 고객 옵션을 늘립니다. 이러한 가상 머신은 관계형 데이터베이스 서버 및 메모리 내 분석 워크 로드와 같이 메모리를 많이 사용 하는 대부분의 엔터프라이즈 응용 프로그램과 관련 된 요구 사항을 충족 하기 위해 vCPUs와 메모리를 조합 하 여 제공 합니다.

## <a name="easv5-series"></a>Easv5 시리즈

Easv5 시리즈는 17ghz의 승격 된 최대 주파수를 달성할 수 있는 AMD의 3 세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용 합니다. Easv5 시리즈 크기는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 한 vCPU와 메모리의 조합을 제공 합니다. 로컬 디스크가 없는 새 Vm은 로컬 임시 디스크를 요구 하지 않는 워크 로드에 대해 더 나은 가치를 제공 합니다.

> [!NOTE]
> 질문과 대답은 [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

Easv5 시리즈 가상 머신은 표준 SSD, 표준 HDD 및 프리미엄 SSD 디스크 유형을 지원 합니다. 지역별 가용성에 따라 Ultra Disk storage를 연결할 수도 있습니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원됨 <br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨 <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨 <br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨 <br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대 <br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br><br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2as_v5              | 2  | 16  | 원격 스토리지 전용 | 4  | 3750/82    | 10000/600  | 2 | 12500  |
| Standard_E4as_v5<sup>2</sup>  | 4  | 32  | 원격 스토리지 전용 | 8  | 6400/144   | 20000/600  | 2 | 12500  |
| Standard_E8as_v5<sup>2</sup>  | 8  | 64  | 원격 스토리지 전용 | 16 | 12800/200  | 20000/600  | 4 | 12500  |
| Standard_E16as_v5<sup>2</sup> | 16 | 128 | 원격 스토리지 전용 | 32 | 25600/384  | 40000/800  | 8 | 12500 |
| Standard_E20as_v5             | 20 | 160 | 원격 스토리지 전용 | 32 | 32000/480  | 64000/1000 | 8 | 12500 |
| Standard_E32as_v5<sup>2</sup> | 32 | 256 | 원격 스토리지 전용 | 32 | 51200/768  | 80000/1600 | 8 | 16000 |
| Standard_E48as_v5             | 48 | 384 | 원격 스토리지 전용 | 32 | 76800/1152 | 80000/2000 | 8 | 24000 |
| Standard_E64as_v5<sup>2</sup> | 64 | 512 | 원격 스토리지 전용 | 32 | 80000/1200 | 80000/2000 | 8 | 32000 |
| Standard_E96as_v5<sup>2</sup> | 96 | 672 | 원격 스토리지 전용 | 32 | 80000/1600 | 80000/2000 | 8 | 40,000 |

<sup>1</sup> Easv5 시리즈 vm은 디스크 성능을 [버스트](disk-bursting.md) 하 고 한 번에 최대 30 분 동안 최대 버스트를 얻을 수 있습니다.<br>
<sup>2</sup> [제한 된 코어 크기 사용 가능](constrained-vcpu.md)



## <a name="eadsv5-series"></a>Eadsv5 시리즈

Eadsv5 시리즈는 17ghz의 승격 된 최대 주파수를 달성할 수 있는 AMD의 3 세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용 합니다. Eadsv5 시리즈 크기는 메모리 집약적 엔터프라이즈 응용 프로그램에 적합 한 vCPU, 메모리 및 임시 저장소의 조합을 제공 합니다. 새 Vm에는 50% 더 큰 로컬 저장소가 있으며, [Gen2](generation-2.md) vm을 사용 하는 [Eav4/Easv4](eav4-easv4-series.md) 크기와 비교 하 여 읽기 및 쓰기 둘 다에 대 한 로컬 디스크 IOPS도 향상 되었습니다.

Eadsv5 시리즈 가상 머신은 표준 SSD, 표준 HDD 및 프리미엄 SSD 디스크 유형을 지원 합니다. 지역별 가용성에 따라 Ultra Disk storage를 연결할 수도 있습니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원됨 <br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨 <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨 <br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨 <br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대 <br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br><br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수 | 최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2ads_v5              | 2  | 16  | 75   | 4  | 9000/125    | 3750/82      | 10000/600  | 2 | 12500  |
| Standard_E4ads_v5<sup>2</sup>  | 4  | 32  | 150  | 8  | 19000/250   | 6400/144     | 20000/600  | 2 | 12500  |
| Standard_E8ads_v5<sup>2</sup>  | 8  | 64  | 300  | 16 | 38000/500   | 12800/200    | 20000/600  | 4 | 12500  |
| Standard_E16ads_v5<sup>2</sup> | 16 | 128 | 600  | 32 | 75000/1000  | 25600/384    | 40000/800  | 8 | 12500 |
| Standard_E20ads_v5             | 20 | 160 | 750  | 32 | 94000/1250  | 32000/480    | 64000/1000 | 8 | 12500 |
| Standard_E32ads_v5<sup>2</sup> | 32 | 256 | 1200 | 32 | 15만/2000 | 51200/768    | 80000/1600 | 8 | 16000 |
| Standard_E48ads_v5             | 48 | 384 | 1800 | 32 | 225000 / 3000 | 76800/1152   | 80000/2000 | 8 | 24000 |
| Standard_E64ads_v5<sup>2</sup> | 64 | 512 | 2400 | 32 | 300000 / 4000 | 80000/1200   | 80000/2000 | 8 | 32000 |
| Standard_E96ads_v5<sup>2</sup> | 96 | 672 | 3600 | 32 | 450000 / 4000 | 80000/1600   | 80000/2000 | 8 | 40,000 |

* 이러한IOPs 값은 Gen2 VM을 사용하여 달성할 수 있습니다.<br>
Eadsv5 시리즈 VM <sup>1개는</sup> 디스크 성능을 [버스트하고](disk-bursting.md) 한 번에 최대 30분 동안 최대 버스트 최대값을 얻을 수 있습니다.<br>
<sup>2</sup> [사용 가능한 코어 크기 제한](constrained-vcpu.md).


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