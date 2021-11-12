---
title: Dv4 및 Dsv4 시리즈 - Azure Virtual Machines
description: Dv4 및 Dsv4 시리즈 VM 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: a9ea53c68f133ad07dc5edd9c12792ed14eba7f6
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369957"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 및 Dsv4 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Dv4 및 Dsv4 시리즈는 하이퍼 스레드 구성의 Intel &reg;Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되며 대부분의 범용 워크로드에 대해 더 나은 가치 제안을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공하며, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html), [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 사용합니다. 또한 [Intel&reg; 딥 러닝 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원합니다. 

> [!NOTE]
> 질문과 대답은 [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

## <a name="dv4-series"></a>Dv4 시리즈

Dv4 시리즈 크기는 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크로드에 대해 vCPU, 메모리 및 원격 스토리지 옵션의 조합을 제공합니다. Dv4 시리즈 VM은 [Intel&reg; 하이퍼 스레딩 기술](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)을 특징으로 합니다.


원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv4 크기를 사용합니다. Dsv4 크기의 가격 및 요금 청구 기준은 Dv4 시리즈와 동일합니다.

> [!NOTE]
> 다시 시작한 후 *Data_loss_warning.txt* 라는 파일이 C 드라이브(Azure Portal에서 연결된 첫 번째 데이터 디스크) 옆에 나타날 수 있습니다. 이 시나리오에서는 파일 이름에도 불구하고 디스크에서 데이터 손실이 발생하지 않았습니다. 일반적으로 *Data_loss_warning.txt* 파일은 일반적으로 임시 드라이브에 복사됩니다. 임시 드라이브가 없는 VM을 사용하는 경우 WindowsAzureGuestAgent는 파일을 첫 번째 드라이브 문자로 잘못 복사합니다. v4 VM에서 첫 번째 드라이브 문자는 데이터 디스크입니다.
>
> 이 문제에 대한 해결 방법은 VM 에이전트의 최신 버전(버전 2.7.41491.999)에서 적용되었습니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md)지원됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | 원격 스토리지 전용 | 4 | 2|5,000 |
| Standard_D4_v4 | 4 | 16  | 원격 스토리지 전용 | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | 원격 스토리지 전용 | 16 | 4|12500 |
| Standard_D16_v4 | 16 | 64 | 원격 스토리지 전용 | 32 | 8|12500 |
| Standard_D32_v4 | 32 | 128 | 원격 스토리지 전용 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 원격 스토리지 전용 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 원격 스토리지 전용 | 32 | 8|30000 |

<sup>1</sup> 가속화된 네트워킹은 단일 NIC에만 적용할 수 있습니다. 


## <a name="dsv4-series"></a>Dsv4 시리즈

Dsv4 시리즈 크기는 Intel &reg;Xeon&reg; Platinum 8272CL(Cascade Lake)에서 실행됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크로드에 대해 vCPU, 메모리 및 원격 스토리지 옵션의 조합을 제공합니다. Dsv4 시리즈 VM은 [Intel&reg; 하이퍼 스레딩 기술](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)을 특징으로 합니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
[중첩된 가상화:](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization.md)지원됨 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 버스트 캐시되지 않은 디스크 처리량: IOPS/MBps<sup>1</sup> | 최대 NIC 수|예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | 원격 스토리지 전용 | 4 | 3200/48 | 4000/200 |2|5,000 |
| Standard_D4s_v4 | 4 | 16 | 원격 스토리지 전용 | 8 | 6400/96 | 8000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | 원격 스토리지 전용 | 16 | 12800/192 | 16000/400 |4|12500 |
| Standard_D16s_v4 | 16 | 64  | 원격 스토리지 전용 | 32 | 25600/384 | 32000/800 |8|12500 |
| Standard_D32s_v4 | 32 | 128 | 원격 스토리지 전용 | 32 | 51200/768 | 64000/1600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | 원격 스토리지 전용 | 32 | 76800/1152 | 80000/2000 |8|24000 |
| Standard_D64s_v4 | 64 | 256 | 원격 스토리지 전용 | 32 | 80000/1200 | 80000/2000 |8|30000 |

<sup>1</sup> Dsv4 시리즈 VM은 디스크 성능을 [버스트](./disk-bursting.md)하고 한 번에 최대 30분 동안 최대 버스트를 얻을 수 있습니다.<br>
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

디스크 유형에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
