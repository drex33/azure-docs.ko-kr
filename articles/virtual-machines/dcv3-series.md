---
title: DCsv3 및 DCdsv3 시리즈 - Azure Virtual Machines
description: DCsv3 및 DCdsv3 시리즈 VM에 대한 사양입니다.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: b28fb180ebbe5295e994d19a562955d834a064bd
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133435112"
---
# <a name="dcsv3-and-dcdsv3-series"></a>DCsv3 및 DCdsv3 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!IMPORTANT] 
> DCsv3 및 DCdsv3은 2021년 11월 1일부터 공개 미리 보기로 제공됩니다.

DCsv3 및 DCdsv3 시리즈 가상 머신은 퍼블릭 클라우드에서 처리되는 동안 코드 및 데이터의 기밀성과 무결성을 보호하는 데 도움이 됩니다. 고객은 Intel® Software Guard Extensions 및 Intel® Total Memory Encryption - 다중 키를 활용하여 데이터가 항상 암호화되고 사용 중으로 보호되도록 할 수 있습니다. 

이러한 머신은 최신 3세대 Intel® Xeon 확장 가능 프로세서로 구동되며 Intel® Turbo Boost Max Technology 3.0을 활용하여 3.5GHz에 도달합니다. 

이 세대에서 CPU 코어는 6배(최대 48개의 물리적 코어까지) 증가했으며, EPC(암호화된 메모리)는 1500x에서 256GB로 증가했으며, 일반 메모리는 12배에서 384GB로 증가했습니다. 이러한 모든 변경 내용은 Gen-On-Gen의 성능을 크게 향상시키고 완전히 새로운 시나리오의 잠금을 해제합니다. 

> [!NOTE]
> 추가 보안 태세에 대해 하이퍼 스레딩을 사용할 수 없습니다. 가격 책정은 DC 시리즈의 고유한 보안 기능뿐만 아니라 물리적 코어와 가상 코어의 뛰어난 성능을 기반으로 합니다.

워크로드가 로컬 디스크의 이점인지 여부에 따라 두 가지 변형을 제공합니다. 로컬 디스크가 있는 VM을 선택하든, 모든 VM에 원격 영구 디스크 스토리지를 연결할 수 있습니다. 원격 디스크 옵션(예: VM 부팅 디스크)은 항상 VM과 별도로 요금이 청구됩니다. 

## <a name="configuration"></a>구성

CPU: 3세대 Intel® Xeon 확장 가능 프로세서 8370C<br>
기본 All-Core 빈도: 2.8GHz<br>
[Turbo Boost Max 3.0:](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html)Enabled, Max Frequency 3.5GHz<br>
[하이퍼 스레딩](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): 지원되지 않음<br>
[총 메모리 암호화 - 다중 키:](https://itpeernetwork.intel.com/memory-encryption/)사용<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Ultra-Disk Storage:](disks-enable-ultra-ssd.md)지원됨<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[Azure Kubernetes Service:](../aks/intro-kubernetes.md)지원됨(처음에만 CLI 프로비전)<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[Dedicated Host:](dedicated-hosts.md)출시 예정<br>

## <a name="dcsv3-series-technical-specifications"></a>DCsv3 시리즈 기술 사양

| 크기             | 물리적 코어 | 메모리(GB) | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |  EPC 메모리 GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | 해당 없음                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | 해당 없음                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | 해당 없음                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | 해당 없음                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | 해당 없음                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | 해당 없음                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | 해당 없음                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | 해당 없음                    | 32             | 8     |  256               |

## <a name="dcdsv3-series-technical-specifications"></a>DCdsv3 시리즈 기술 사양

| 크기             | 물리적 코어 | 메모리(GB) | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |  EPC 메모리 GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2400                    | 32             | 8     |  256               |

## <a name="get-started"></a>시작

- [Azure Portal](./linux/quick-create-portal.md) 사용하여 DCsv3 및 DCdsv3 VM 만들기
- DCsv3 및 DCdsv3 VM은 [2세대 VM이며](./generation-2.md#creating-a-generation-2-vm) `Gen2` 이미지만 지원합니다.
- 현재 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에 나열된 지역에서만 사용할 수 있습니다.

## <a name="more-sizes-and-information"></a>추가 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)
- [요금 계산기](https://azure.microsoft.com/pricing/calculator/).

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
