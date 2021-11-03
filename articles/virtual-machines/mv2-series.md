---
title: Mv2 시리즈 - Azure Virtual Machines
description: Mv2 시리즈 VM의 사양입니다.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 0f311645f29f5daaaf65474a60031ee8cceff688
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063673"
---
# <a name="mv2-series"></a>Mv2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Mv2 시리즈는 하이퍼 스레드 Intel® Xeon® 플래티넘 8180M 2.5GHz(Skylake) 프로세서에서 실행되는, 높은 처리량과 낮은 대기 시간을 가진 플랫폼을 갖추고 있으며 2.5GHz의 전체 코어 기본 주파수와 3.8GHz의 최대 터보 주파수를 자랑합니다. 모든 Mv2 시리즈 가상 머신 크기에서 표준 및 프리미엄 영구적 디스크를 모두 사용할 수 있습니다. Mv2 시리즈 인스턴스는 메모리에 최적화된 VM 크기로, 탁월한 컴퓨팅 성능을 바탕으로 대규모 메모리 내 데이터베이스 및 워크로드를 지원하며, 메모리 대 CPU 비율이 높아 관계형 데이터베이스 서버, 대규모 캐시, 메모리 내 분석에 적합합니다.

Mv2 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[쓰기 가속기](./how-to-enable-write-accelerator.md): 지원됨<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
<br>

|크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 예상 네트워크 대역폭(Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800(7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800(7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1,2</sup> | 416 | 11400 | 8192 | 64 | 250000/1600(14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1,2</sup> | 416 | 5700 | 8192 | 64 | 250000/1600(14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> Mv2 시리즈 VM은 2세대 전용으로, 2세대에서 지원하는 이미지의 하위 집합을 지원합니다. Mv2 시리즈에 대해 지원되는 이미지의 전체 목록은 아래를 참조하세요. Linux를 사용하는 경우 이미지를 찾고 선택하는 방법에 대한 지침은 [Azure의 2세대 VM 지원](./generation-2.md)을 참조하세요. Windows를 사용하는 경우 이미지를 찾고 선택하는 방법에 대한 지침은 [Azure의 2세대 VM 지원](./generation-2.md)을 참조하세요. 

- Windows Server 2019 이상
- SUSE Linux Enterprise Server 12 SP4 이상 또는 SUSE Linux Enterprise Server 15 SP1 이상
- Red Hat Enterprise Linux 7.6 이상 및 8.1 이상
- Oracle Enterprise Linux 7.7 이상 및 8.1 이상
- Ubuntu 18.04 및 5.4.0-azure 커널 이상

<sup>2</sup> [사용 가능한 코어 크기 제한](./constrained-vcpu.md).


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
