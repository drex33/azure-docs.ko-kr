---
title: Azure DCasv5 및 DCadsv5 시리즈 기밀 가상 머신(미리 보기)
description: Azure 기밀 컴퓨팅의 DCasv5 및 DCadsv5 시리즈 기밀 가상 머신에 대한 사양입니다.
author: runcai
ms.author: runcai
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/15/2021
ms.openlocfilehash: 40c4e7ac2f911d3a0987cd2523386c7cbf83b9c1
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943458"
---
# <a name="dcasv5-and-dcadsv5-series-confidential-vms-preview"></a>DCasv5 및 DCadsv5 시리즈 기밀 VM(미리 보기)

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

> [!IMPORTANT]
> Azure 기밀 컴퓨팅의 기밀 가상 머신(기밀 VM)은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

DCasv5 시리즈 및 DCadsv5 시리즈는 기밀 컴퓨팅에 사용할 [기밀 VM입니다.](../confidential-computing/confidential-vm-overview.md) 

이러한 기밀 VM은 최대 256MB L3 캐시가 있는 다중 스레드 구성에서 AMD의 3세대 EPYC<sup>TM</sup> 7763v 프로세서를 사용합니다. 이러한 프로세서는 최대 3.5GHz의 상승된 빈도를 달성할 수 있습니다. 두 시리즈 모두 SEV-Virtualization-Secure(Secure Encrypted Virtualization-Secure 중첩 페이징)를 제공합니다. SEV-THE는 다른 VM, 하이퍼바이저 및 호스트 관리 코드로부터 데이터를 보호하는 하드웨어 격리 VM을 제공합니다. 기밀 VM은 하드웨어 기반 VM 메모리 암호화를 제공합니다. 또한 이러한 시리즈는 다른 키 관리 솔루션을 통해 VM을 프로비전하기 전에 OS 디스크 사전 암호화를 제공합니다. 

## <a name="dcasv5-series"></a>DCasv5 시리즈

DCasv5 시리즈 VM은 대부분의 프로덕션 워크로드에 vCPU와 메모리의 조합을 제공합니다. 로컬 디스크가 없는 이러한 VM은 로컬 임시 디스크가 필요하지 않은 워크로드에 더 나은 가치 제안을 제공합니다. 자세한 내용은 로컬 [임시 디스크가 없는 Azure VM 크기에 대한 FAQ를 참조하세요.](azure-vms-no-temp-disk.yml) 

이 시리즈는 SSD 디스크 유형에 표준 SSD, 표준 HDD 및 Premium 지원합니다. 디스크 스토리지 및 VM에 대한 청구는 별개입니다. 비용을 예측하려면 가격 [계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

> [!NOTE]
> 기밀 VM에 대한 [암호화 설정에 따라](../confidential-computing/confidential-vm-overview.md#encryption-pricing-differences) 몇 가지 가격 책정 차이가 있습니다.

### <a name="dcasv5-series-feature-support"></a>DCasv5 시리즈 기능 지원

DCasv5 시리즈 VM에서 *지원되는* 기능:

- [Premium Storage](premium-storage-performance.md)
- [Premium Storage 캐싱](premium-storage-performance.md)
- [VM 2세대](generation-2.md)

*DCasv5* 시리즈 VM에서 지원되지 않는 기능:

- [실시간 마이그레이션](maintenance-and-updates.md)
- [메모리 보존 업데이트](maintenance-and-updates.md)
- [가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md)
- [임시 OS 디스크](ephemeral-os-disks.md)

### <a name="dcasv5-series-products"></a>DCasv5 시리즈 제품

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
|---|---|---|---|---|---|---|
| Standard_DC2as_v5  | 2  | 8   | 원격 스토리지 전용 | 4  | 3750/82    | 2 |
| Standard_DC4as_v5  | 4  | 16  | 원격 스토리지 전용 | 8  | 6400/144   | 2 |
| Standard_DC8as_v5  | 8  | 32  | 원격 스토리지 전용 | 16 | 12800/200  | 4 |
| Standard_DC16as_v5 | 16 | 64  | 원격 스토리지 전용 | 32 | 25600/384  | 4 |
| Standard_DC32as_v5 | 32 | 128 | 원격 스토리지 전용 | 32 | 51200/768  | 8 |
| Standard_DC48as_v5 | 48 | 192 | 원격 스토리지 전용 | 32 | 76800/1152 | 8 |
| Standard_DC64as_v5 | 64 | 256 | 원격 스토리지 전용 | 32 | 80000/1200 | 8 |
| Standard_DC96as_v5 | 96 | 384 | 원격 스토리지 전용 | 32 | 80000/1600 | 8 |

## <a name="dcadsv5-series"></a>DCadsv5 시리즈

DCadsv5 시리즈는 대부분의 프로덕션 워크로드에 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

이 시리즈는 SSD 디스크 유형에 표준 SSD, 표준 HDD 및 Premium 지원합니다. 디스크 스토리지 및 VM에 대한 청구는 별개입니다. 비용을 예측하려면 가격 [계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

> [!NOTE]
> 기밀 Vm의 [암호화 설정에 따라 몇 가지 가격 책정 차이가](../confidential-computing/confidential-vm-overview.md#encryption-pricing-differences) 있습니다.

### <a name="dcadsv5-series-feature-support"></a>DCadsv5 시리즈 기능 지원

DCadsv5 시리즈 Vm에서 *지원 되* 는 기능:

- [Premium Storage](premium-storage-performance.md)
- [Premium Storage 캐싱](premium-storage-performance.md)
- [VM 생성 2](generation-2.md)

DCadsv5 시리즈 Vm에서 *지원 되지 않는* 기능:

- [실시간 마이그레이션](maintenance-and-updates.md)
- [메모리 보존 업데이트](maintenance-and-updates.md)
- [가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md)
- [임시 OS 디스크](ephemeral-os-disks.md)

### <a name="dcadsv5-series-products"></a>DCadsv5 시리즈 제품

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량: IOPS/MBps | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|
| Standard_DC2ads_v5  | 2  | 8   | 75   | 4  | 9000/125    | 3750/82    | 2 |
| Standard_DC4ads_v5  | 4  | 16  | 150  | 8  | 19000/250   | 6400/144   | 2 |
| Standard_DC8ads_v5  | 8  | 32  | 300  | 16 | 38000/500   | 12800/200  | 4 |
| Standard_DC16ads_v5 | 16 | 64  | 600  | 32 | 75000/1000  | 25600/384  | 4 |
| Standard_DC32ads_v5 | 32 | 128 | 1200 | 32 | 15만/2000 | 51200/768  | 8 |
| Standard_DC48ads_v5 | 48 | 192 | 1800 | 32 | 225000/3000 | 76800/1152 | 8 |
| Standard_DC64ads_v5 | 64 | 256 | 2400 | 32 | 30만/4000 | 80000/1200 | 8 |
| Standard_DC96ads_v5 | 96 | 384 | 3600 | 32 | 45만/4000 | 80000/1600 | 8 |

> [!NOTE]
> 이러한 IOPs를 얻으려면 [Gen2 vm](generation-2.md)을 사용 합니다.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [AMD 프로세서의 기밀 가상 머신 옵션](../confidential-computing/virtual-machine-solutions-amd.md)
