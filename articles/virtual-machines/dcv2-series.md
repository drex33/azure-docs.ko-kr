---
title: DCsv2 시리즈 - Azure Virtual Machines
description: DCsv2 시리즈 VM의 사양입니다.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: a92f368dc01aef5cf43ea93b94639fc1329ec7ea
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984386"
---
# <a name="dcsv2-series"></a>DCsv2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

DCsv2 시리즈 가상 머신을 사용하면 퍼블릭 클라우드에서 처리되는 동안 데이터 및 코드의 기밀성 및 무결성을 보호할 수 있습니다. DCsv2 시리즈는 Intel® Software Guard Extensions를 활용하므로, 고객이 보호를 위해 보안 Enclave를 사용할 수 있습니다.

해당 머신은 SGX 기술을 사용하는 3.7GHz Intel® Xeon E-2288G(Coffee Lake)로 백업됩니다. Intel® Turbo Boost Max Technology 3.0을 사용하면 이 머신은 최대 5.0GHz까지 사용할 수 있습니다. 

> [!NOTE]
> 추가 보안 태세에 대해 하이퍼 스레딩을 사용할 수 없습니다. 가격 책정은 DC 시리즈의 고유한 보안 기능뿐만 아니라 물리적 코어와 가상 코어의 뛰어난 성능을 기반으로 합니다.

기밀 사용 사례의 예로는 데이터베이스, 블록체인, 다중 파트 데이터 분석, 사기 감지, 자금 방지 사기, 사용량 분석, 인텔리전스 분석 및 기계 학습이 있습니다.

## <a name="configuration"></a>구성

[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): 지원됨(테넌트 VM은 3.7GHz를 지원하지만, 터보 속도에 도달함)<br>
[하이퍼 스레딩](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): 지원되지 않음<br>
[Premium Storage](premium-storage-performance.md): 지원됨(Standard_DC8_v2에 지원되지 않음)<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원되지 않음<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨 <br>

## <a name="technical-specifications"></a>기술 사양

| 크기             | 물리적 코어 | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 NIC 수 / 예상 네트워크 대역폭(MBps) | EPC 메모리(MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |


## <a name="get-started"></a>시작

- [Azure Portal](./linux/quick-create-portal.md) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)를 사용하여 DCsv2 VM 만들기
- DCsv2 시리즈 VM은 [2세대 VM](./generation-2.md#creating-a-generation-2-vm)이며 `Gen2` 이미지만 지원합니다.
- 현재 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에 나열된 지역에서만 사용할 수 있습니다.

## <a name="more-sizes-and-information"></a>추가 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)
- [요금 계산기](https://azure.microsoft.com/pricing/calculator/).
- [디스크 유형에 관한 자세한 내용](./disks-types.md#ultra-disk)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
