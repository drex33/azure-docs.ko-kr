---
title: DCsv2 시리즈 - Azure Virtual Machines
description: DCsv2 시리즈 VM의 사양입니다.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 474478c03586866f28d02c4e1cb219bd6df65dea
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530127"
---
# <a name="dcsv2-series"></a>DCsv2 시리즈


DCsv2 시리즈 가상 머신을 사용하면 퍼블릭 클라우드에서 처리되는 동안 데이터 및 코드의 기밀성 및 무결성을 보호할 수 있습니다. DCsv2 시리즈는 Intel® Software Guard Extensions를 활용하므로, 고객이 보호를 위해 보안 Enclave를 사용할 수 있습니다.

해당 머신은 SGX 기술을 사용하는 3.7GHz Intel® Xeon E-2288G(Coffee Lake)로 백업됩니다. Intel® Turbo Boost Max Technology 3.0을 사용하면 이 머신은 최대 5.0GHz까지 사용할 수 있습니다. 

사용 사례로는 기밀 단체 데이터 공유, 사기 감지, 기밀 데이터베이스, 자금 세탁 방지, 블록체인, 기밀 사용 분석, 인텔리전스 분석, 기밀 기계 학습이 있습니다.

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

| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 NIC 수 / 예상 네트워크 대역폭(MBps) | EPC 메모리(MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |


## <a name="get-started"></a>시작

- [Azure Portal](./linux/quick-create-portal.md) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)를 사용하여 DCsv2 VM 만들기
- DCsv2 시리즈 VM은 [2세대 VM](./generation-2.md#creating-a-generation-2-vm)이며 `Gen2` 이미지만 지원합니다.
- 현재 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에 나열된 지역에서만 사용할 수 있습니다.
- 차세대 DC 시리즈 VM: [미리 보기 프로그램에 참가](https://aka.ms/intelgen3)

## <a name="more-sizes-and-information"></a>추가 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)
- [요금 계산기](https://azure.microsoft.com/pricing/calculator/).
- [디스크 유형에 관한 자세한 내용](./disks-types.md#ultra-disk)

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.