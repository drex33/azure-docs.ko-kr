---
title: NCv2 시리즈 - Azure Virtual Machines
description: NCv2 시리즈 VM의 사양입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 043470a8db67454d660a2c1c2fa8e7dcde4278b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734927"
---
# <a name="ncv2-series"></a>NCv2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

NCv2 시리즈 VM은 NVIDIA Tesla P100 GPU로 구동됩니다. 이러한 GPU는 NC 시리즈보다 2배를 초과하는 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. GPU 외에 NCv2 시리즈 VM은 Intel Xeon E5-2690 v4(Broadwell) CPU로도 구동됩니다.

NC24rs v2 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disk): 지원됨(가용성, 사용량 및 성능에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원되지 않음<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
Nvidia NVLink Interconnect: 지원되지 않음

> [!IMPORTANT]
> 이 VM 시리즈의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 시리즈에 대한 [vCPU 할당량 증가를 요청](../azure-portal/supportability/regional-quota-requests.md)합니다.
>
| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

한 개의 GPU = 한 개의 P100 카드

*RDMA 지원



## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다.

[NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](./extensions/hpccompute-gpu-windows.md)를 참조하세요. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

NVIDIA GPU 드라이버를 수동으로 설치하려는 경우 지원되는 운영 체제, 드라이버, 설치 및 인증 단계에 대해서는 [Windows용 N 시리즈 GPU 드라이버 설정](./windows/n-series-driver-setup.md) 또는 [Linux용 N 시리즈 GPU 드라이버 설정](./linux/n-series-driver-setup.md)을 참조하세요.

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