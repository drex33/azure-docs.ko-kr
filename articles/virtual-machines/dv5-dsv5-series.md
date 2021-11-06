---
title: Dv5 및 Dsv5 시리즈-Azure Virtual Machines
description: Dv5 및 Dsv5 시리즈 Vm에 대 한 사양입니다.
author: styli365
ms.author: joelpell
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 0bdd2c517a6bfef059ad89d7c3cbf1a825aba526
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866788"
---
# <a name="dv5-and-dsv5-series"></a>Dv5 및 Dsv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Dv5 및 Dsv5 시리즈 가상 머신은 하이퍼 스레드 구성에서 세 번째 세대 Intel &reg; Xeon &reg; 플래티넘 8370C (Ice Lake) 프로세서에서 실행 [](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 되므로 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 합니다. 이 새로운 프로세서는 [intel &reg; 터보 부스트 기술로](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)3.5 GHz의 모든 코어 터보 클록 속도, [intel &reg; Advanced-Vector 확장 512 (intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) 및 [intel &reg; Deep Learning 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 제공 합니다. 이러한 가상 머신은 중소 규모의 데이터베이스, 낮은-중간 트래픽 웹 서버, 응용 프로그램 서버 등 대부분의 엔터프라이즈 워크 로드와 관련 된 요구 사항을 충족 하기 위해 vCPUs와 메모리를 조합 하 여 제공 합니다. Dv5 및 Dsv5 시리즈는 로컬 임시 디스크가 필요 하지 않은 워크 로드에 대해 더 나은 가치를 제공 합니다. 로컬 디스크를 사용 하는 유사한 가상 컴퓨터에 대 한 자세한 내용은 [Ddv5 및 Ddsv5 시리즈 vm](ddv5-ddsv5-series.md)을 참조 하세요.

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 AZURE VM 크기](azure-vms-no-temp-disk.yml)를 참조 하세요.

## <a name="dv5-series"></a>Dv5 시리즈

Dv5 시리즈 가상 머신은 3 세대 Intel® Xeon® 플래티넘 8370C (Ice) 프로세서에서 실행 되며, 모든 코어 터보 클록 속도는 최대 3.5 g h z에 도달 합니다.  이러한 가상 머신은 최대 96 vCPU 및 384 GiB의 RAM을 제공 합니다.  Dv5 시리즈 가상 머신은 이전 세대 (예: 향상 된 확장성 및 업그레이드 된 CPU 클래스)와 비교 하 여 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 합니다.

Dv5 시리즈 가상 머신은 임시 저장소를 갖지 않으므로 항목의 가격은 절감 됩니다.  표준 Ssd와 표준 Hdd 디스크 저장소를 이러한 가상 컴퓨터에 연결할 수 있습니다. 프리미엄 SSD 또는 Ultra Disk storage를 사용 하려면 Dsv5 시리즈 virtual machines를 선택 합니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|최대 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v5<sup>1, 2</sup> | 2  | 8   | 원격 스토리지 전용 | 4  | 2 | 12500 |
| Standard_D4_v5                | 4  | 16  | 원격 스토리지 전용 | 8  | 2 | 12500 |
| Standard_D8_v5                | 8  | 32  | 원격 스토리지 전용 | 16 | 4 | 12500 |
| Standard_D16_v5               | 16 | 64  | 원격 스토리지 전용 | 32 | 8 | 12500 |
| Standard_D32_v5               | 32 | 128 | 원격 스토리지 전용 | 32 | 8 | 16000 |
| Standard_D48_v5               | 48 | 192 | 원격 스토리지 전용 | 32 | 8 | 24000 |
| Standard_D64_v5               | 64 | 256 | 원격 스토리지 전용 | 32 | 8 | 30000 |
| Standard_D96_v5               | 96 | 384 | 원격 스토리지 전용 | 32 | 8 | 35000 |

<sup>1</sup> 가속화 된 네트워킹은 모든 Dv5 가상 머신에서 기본적으로 설정 되 고 설정 됩니다.<br>
<sup>2 2</sup> 개의 nic에 가속화 된 네트워킹을 적용할 수 있습니다.

## <a name="dsv5-series"></a>Dsv5 시리즈

Dsv5 시리즈 가상 머신은 3 세대 Intel® Xeon® 플래티넘 8370C (Ice) 프로세서에서 실행 되며, 모든 코어 터보 클록 속도는 최대 3.5 g h z에 도달 합니다.  이러한 가상 머신은 최대 96 vCPU 및 384 GiB의 RAM을 제공 합니다.  Dsv5 시리즈 가상 머신은 이전 세대 (예: 향상 된 확장성 및 업그레이드 된 CPU 클래스)와 비교 하 여 대부분의 범용 워크 로드에 대해 더 나은 가치를 제공 합니다.

Dsv5 시리즈 가상 머신은 임시 저장소를 갖지 않으므로 항목의 가격은 절감 됩니다.  표준 ssd, 표준 hdd 및 Premium ssd 디스크 저장소를 이러한 가상 컴퓨터에 연결할 수 있습니다. 지역별 가용성에 따라 Ultra Disk storage를 연결할 수도 있습니다. 디스크 저장소는 가상 컴퓨터와 별도로 청구 됩니다. [디스크 가격을 참조 하세요](https://azure.microsoft.com/pricing/details/managed-disks/).

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹:](../virtual-network/create-vm-accelerated-networking-cli.md)필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 캐시되지 않은 최대 버스트 디스크 처리량: IOPS/MBps<sup>3</sup> | 최대 NIC 수 | 최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v5<sup>1,2</sup> | 2  | 8   | 원격 스토리지 전용 | 4  | 3750/85    | 10000/1200 | 2 | 12500 |
| Standard_D4s_v5               | 4  | 16  | 원격 스토리지 전용 | 8  | 6400/145   | 20000/1200 | 2 | 12500 |
| Standard_D8s_v5               | 8  | 32  | 원격 스토리지 전용 | 16 | 12800/290  | 20000/1200 | 4 | 12500 |
| Standard_D16s_v5              | 16 | 64  | 원격 스토리지 전용 | 32 | 25600/600  | 40000/1200 | 8 | 12500 |
| Standard_D32s_v5              | 32 | 128 | 원격 스토리지 전용 | 32 | 51200/865  | 80000/2000 | 8 | 16000 |
| Standard_D48s_v5              | 48 | 192 | 원격 스토리지 전용 | 32 | 76800/1315 | 80000/3000 | 8 | 24000 |
| Standard_D64s_v5              | 64 | 256 | 원격 스토리지 전용 | 32 | 80000/1735 | 80000/3000 | 8 | 30000 |
| Standard_D96s_v5              | 96 | 384 | 원격 스토리지 전용 | 32 | 80000/2600 | 80000/4000 | 8 | 35000 |

<sup>1</sup> 가속화된 네트워킹이 필요하며 모든 Dsv5 가상 머신에서 기본적으로 켜져 있습니다.<br>
<sup>2</sup> 가속화된 네트워킹은 두 개의 NIC에 적용할 수 있습니다.<br>
<sup>3</sup> Dsv5 시리즈 가상 머신은 디스크 성능을 [버스트하고](disk-bursting.md) 한 번에 최대 30분 동안 최대 버스트 최대값을 얻을 수 있습니다.

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
