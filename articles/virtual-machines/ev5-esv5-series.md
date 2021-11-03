---
title: Ev5 및 Esv5 시리즈 - Azure Virtual Machines
description: Ev5 및 Esv5 시리즈 VM에 대한 사양입니다.
author: styli365
ms.author: sttsinar
ms.reviewer: joelpell
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 1eff0184010df3b2c36c9f4f16b093c18a92ebee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483103"
---
# <a name="ev5-and-esv5-series"></a>Ev5 및 Esv5 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Ev5 및 Esv5 시리즈는 하이퍼 스레드 구성의 3세대 Intel &reg; Xeon &reg; 8370C(Ice Lake) 프로세서에서 [실행될](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) Virtual Machines 있으며, 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 이 새로운 프로세서는 [Intel Turbo Boost &reg; Technology, Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)Advanced-Vector [ &reg; Extensions 512(Intel AVX-512) 및 Intel Deep Learning Boost가 있는 &reg; 3.5GHz의](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) 모든 코어 turbo 클록 속도를 제공합니다. [ &reg; ](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) 최대 672 GiB RAM을 갖춘 이러한 가상 머신은 메모리 집약적 엔터프라이즈 애플리케이션, 관계형 데이터베이스 서버 및 메모리 내 분석 워크로드에 적합합니다. Ev5 및 Esv5 시리즈는 로컬 임시 디스크가 필요하지 않은 워크로드에 더 나은 가치 제안을 제공합니다.

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 Azure VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

## <a name="ev5-series"></a>Ev5 시리즈

Ev5 시리즈 가상 머신은 3세대 Intel® Xeon® 8370C(Ice Lake) 프로세서에서 실행하여 최대 3.5GHz의 모든 코어 turbo 클록 속도에 도달합니다.  이러한 가상 머신은 최대 104개 vCPU와 672 GiB RAM을 제공합니다. Ev5 시리즈 가상 머신에는 임시 스토리지가 없으므로 진입 가격이 낮아질 수 있습니다.

Ev5 시리즈는 표준 SSD 및 표준 HDD 디스크 유형을 지원합니다. Premium SSD 또는 Ultra Disk 스토리지를 사용하려면 Esv5 시리즈 가상 머신을 선택합니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)

[Premium Storage](premium-storage-performance.md): 지원되지 않음<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹:](../virtual-network/create-vm-accelerated-networking-cli.md)필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v5<sup>1,2</sup>  | 2   | 16  | 원격 스토리지 전용 | 4  | 2 | 12500 |
| Standard_E4_v5                | 4   | 32  | 원격 스토리지 전용 | 8  | 2 | 12500 |
| Standard_E8_v5                | 8   | 64  | 원격 스토리지 전용 | 16 | 4 | 12500 |
| Standard_E16_v5               | 16  | 128 | 원격 스토리지 전용 | 32 | 8 | 12500 |
| Standard_E20_v5               | 20  | 160 | 원격 스토리지 전용 | 32 | 8 | 12500  |
| Standard_E32_v5               | 32  | 256 | 원격 스토리지 전용 | 32 | 8 | 16000  |
| Standard_E48_v5               | 48  | 384 | 원격 스토리지 전용 | 32 | 8 | 24000  |
| Standard_E64_v5               | 64  | 512 | 원격 스토리지 전용 | 32 | 8 | 30000  |
| Standard_E96_v5               | 96  | 672 | 원격 스토리지 전용 | 32 | 8 | 30000  |
| Standard_E104i_v5<sup>3</sup> | 104 | 672 | 원격 스토리지 전용 | 64 | 8 | 100000 |

<sup>1</sup> 가속화된 네트워킹이 필요하며 모든 Ev5 가상 머신에서 기본적으로 켜져 있습니다.<br>
<sup>2</sup> 가속화된 네트워킹은 두 개의 NIC에 적용할 수 있습니다.<br>
<sup>3</sup> 인스턴스는 단일 고객 전용 하드웨어로 [격리됩니다.](../security/fundamentals/isolation-choices.md#compute-isolation)<br>

## <a name="esv5-series"></a>Esv5 시리즈

Esv5 시리즈 가상 머신은 3세대 Intel® Xeon® 8370C(Ice Lake) 프로세서에서 실행하여 최대 3.5GHz의 모든 코어 turbo 클록 속도에 도달합니다.  이러한 가상 머신은 최대 104개 vCPU와 672 GiB RAM을 제공합니다. Esv5 시리즈 가상 머신에는 임시 스토리지가 없으므로 진입 가격이 낮아질 수 있습니다.

Esv5 시리즈는 표준 SSD, 표준 HDD 및 Premium SSD 디스크 유형을 지원합니다. 지역별 가용성에 따라 Ultra Disk 스토리지를 연결할 수도 있습니다. 디스크 스토리지는 가상 머신과 별도로 청구됩니다. [디스크에 대한 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/managed-disks/)

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹:](../virtual-network/create-vm-accelerated-networking-cli.md)필수 <br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 캐시되지 않은 최대 버스트 디스크 처리량: IOPS/MBps<sup>5</sup> | 최대 NIC 수 | 최대 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v5<sup>1,2</sup>  | 2   | 16  | 원격 스토리지 전용 | 4  | 3750/85     | 10000/1200 | 2 | 12500 |
| Standard_E4s_v5                | 4   | 32  | 원격 스토리지 전용 | 8  | 6400/145    | 20000/1200 | 2 | 12500 |
| Standard_E8s_v5                | 8   | 64  | 원격 스토리지 전용 | 16 | 12800/290   | 20000/1200 | 4 | 12500 |
| Standard_E16s_v5               | 16  | 128 | 원격 스토리지 전용 | 32 | 25600/600   | 40000/1200 | 8 | 12500 |
| Standard_E20s_v5               | 20  | 160 | 원격 스토리지 전용 | 32 | 32000/750   | 64000/1600 | 8 | 12500  |
| Standard_E32s_v5               | 32  | 256 | 원격 스토리지 전용 | 32 | 51200/865   | 80000/2000 | 8 | 16000  |
| Standard_E48s_v5               | 48  | 384 | 원격 스토리지 전용 | 32 | 76800/1315  | 80000/3000 | 8 | 24000  |
| Standard_E64s_v5               | 64  | 512 | 원격 스토리지 전용 | 32 | 80000/1735  | 80000/3000 | 8 | 30000  |
| Standard_E96s_v5<sup>3</sup>   | 96  | 672 | 원격 스토리지 전용 | 32 | 80000/2600  | 80000/4000 | 8 | 35000  |
| Standard_E104is_v5<sup>4</sup> | 104 | 672 | 원격 스토리지 전용 | 64 | 120000/4000 | 80000/4000 | 8 | 100000 |

<sup>1</sup> 가속화 된 네트워킹은 모든 Esv5 가상 머신에서 기본적으로 설정 되 고 설정 됩니다.<br>
<sup>2 2</sup> 개의 nic에 가속화 된 네트워킹을 적용할 수 있습니다.<br>
<sup>3</sup> [제한 된 코어](constrained-vcpu.md) 크기를 사용할 수 있습니다.<br>
<sup>4</sup> 인스턴스는 단일 고객 전용의 하드웨어에 [격리](../security/fundamentals/isolation-choices.md#compute-isolation) 되어 있습니다.<br>
<sup>5</sup> Esv5 시리즈 vm은 디스크 성능을 증가 [시킬 수 있으며](disk-bursting.md) 한 번에 최대 30 분 동안 버스트 max를 얻을 수 있습니다.

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
