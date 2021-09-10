---
title: Azure VM 크기 - 스토리지 | Microsoft Docs
description: Azure의 가상 머신에 사용할 수 있는 다양한 스토리지 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a6210583dc450fa31cd0e76c158cdab40e1c578b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695743"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>스토리지 최적화 가상 컴퓨터 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

스토리지 최적화 VM 크기는 높은 디스크 처리량 및 IO를 제공하며 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대형 트랜잭션 데이터베이스에 이상적입니다.  예를 들어 Cassandra, MongoDB, Cloudera, Redis가 있습니다. 이 문서에서는 각 최적화 크기에 대한 로컬 스토리지 처리량 및 네트워크 대역폭뿐 아니라 vCPU, 데이터 디스크, NIC 수에 대한 정보를 제공합니다.

[Lsv2 시리즈](lsv2-series.md)는 처리량이 많고, 대기 시간이 짧으며, 모든 코어 부스트가 2.55GHz이고 최대 부스트가 3.0GHz인 [AMD EPYC<sup>TM</sup> 7551 프로세서](https://www.amd.com/en/products/epyc-7000-series)에서 실행되는 직접 매핑된 로컬 NVMe 스토리지가 특징입니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

[Windows](windows/storage-performance.md) 또는 [Linux](linux/storage-performance.md)용 Lsv2 시리즈 가상 머신에서 성능을 최적화하는 방법에 대해 알아봅니다.

Azure가 VM의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 가상 머신 크기 명명 규칙](./vm-naming-conventions.md)을 참조하세요.