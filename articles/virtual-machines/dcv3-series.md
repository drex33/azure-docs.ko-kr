---
title: DCsv3 및 DCdsv3 시리즈-Azure Virtual Machines
description: DCsv3 및 DCdsv3 시리즈 Vm에 대 한 사양입니다.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09d4c88bec94c881fd9895557aa35264ea4ffab4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561217"
---
# <a name="dcsv3-and-dcdsv3-series"></a>DCsv3 및 DCdsv3 시리즈

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!NOTE] 
> DCsv3 및 DCdsv3는 2021 년 11 월 1 일에 공개 미리 보기로 제공 됩니다.

DCsv3 및 DCdsv3 시리즈 가상 머신은 공용 클라우드에서 처리 되는 동안 코드 및 데이터의 기밀성과 무결성을 보호 하는 데 도움이 됩니다. 고객은 Intel® Software Guard 확장 및 Intel® 총 메모리 암호화-다중 키를 활용 하 여 데이터를 항상 암호화 하 고 사용 하 여 보호 하도록 할 수 있습니다. 

이러한 컴퓨터는 최신 3 세대 Intel® Xeon 확장 가능한 프로세서로 제공 되며 Intel® 터보 부스트 최대 기술 3.0을 활용 하 여 3.5 GHz에 도달 합니다. 

이 세대에서 CPU 코어는 6x 증가 하 고 (최대 48 개의 실제 코어), EPC (암호화 된 메모리)는 1500x에서 256GB로 증가 하 고, 일반 메모리는 12x에서 384GB로 증가 했습니다. 이러한 모든 변경 사항은 세대의 성능을 대폭 향상 시키고 완전히 새로운 새 시나리오의 잠금을 해제 합니다. 

> [!NOTE]
> 추가 된 보안 상태에 대해 하이퍼 기능을 사용할 수 없습니다. 가격 책정은 DC 시리즈의 고유한 보안 기능 뿐만 아니라 물리적 vs 가상 코어의 뛰어난 성능을 기반으로 합니다.

Microsoft는 워크 로드가 로컬 디스크의 이점을 활용 하는지 여부에 따라 두 가지 변형을 제공 합니다. 로컬 디스크를 사용 하 여 VM을 선택 하는지 여부에 관계 없이 원격 영구 디스크 저장소를 모든 Vm에 연결할 수 있습니다. 원격 디스크 옵션 (예: VM 부팅 디스크의 경우)은 언제나 처럼 Vm과는 별도로 청구 됩니다. 

## <a name="configuration"></a>구성

CPU: 3 세대 Intel® Xeon 확장 가능 프로세서 8370C<br>
기본 All-Core 빈도: 2.8 g h z<br>
[터보 부스트 최대 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): 사용, 최대 빈도 3.5 GHz<br>
[하이퍼 스레딩](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): 지원되지 않음<br>
[총 메모리 암호화-다중 키](https://itpeernetwork.intel.com/memory-encryption/): 사용<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[디스크 저장소](disks-enable-ultra-ssd.md): 지원 됨<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원 됨 (CLI 프로 비전 또는 ARM 템플릿만 해당)<br>
[Azure Kubernetes Service](../aks/intro-kubernetes.md): 지원 됨 (처음에만 CLI 프로 비전)<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>
[전용 호스트](dedicated-hosts.md): 서비스 예정<br>

## <a name="dcsv3-series-technical-specifications"></a>DCsv3 시리즈 기술 사양

| 크기             | 실제 코어 | 메모리(GB) | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |  EPC 메모리 GB |
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

| 크기             | 실제 코어 | 메모리(GB) | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |  EPC 메모리 GB |
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

- [Azure Portal](./linux/quick-create-portal.md) 를 사용 하 여 DCsv3 및 DCdsv3 vm 만들기
- DCsv3 및 DCdsv3 Vm은 [2 세대 vm](./generation-2.md#creating-a-generation-2-vm) 이며 이미지만 지원 `Gen2` 합니다.
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
