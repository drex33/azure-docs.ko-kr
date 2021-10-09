---
title: HB 시리즈 사용 중지
description: HB 시리즈 중지는 2021년 9월 1일부터 시작됩니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: f582aad0b5af8053ff7accfac2917d4d5191ce5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712145"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>2024년 8월 31일까지 HB 시리즈 가상 머신 마이그레이션

Microsoft Azure HPC(고성능 컴퓨팅)를 위한 HBv2 및 HBv3 시리즈 VM(가상 머신)을 도입했습니다. 따라서 워크로드를 원래 HB 시리즈 VM에서 최신 제품으로 마이그레이션하는 것이 좋습니다.

Azure [HBv2](hbv2-series.md) 및 [HBv3](hbv3-series.md) VM에는 더 큰 메모리 대역폭, 향상된 RDMA(원격 직접 메모리 액세스) 네트워킹 기능, 더 크고 빠른 로컬 반도체 드라이브, 다양한 HPC 워크로드의 비용 및 성능이 향상됩니다. 따라서 2024년 8월 31일에 HB 시리즈 Azure VM 크기가 사용 중지됩니다.

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB 시리즈 마이그레이션은 어떤 영향을 주나요?

2024년 8월 31일 이후에는 나머지 HB 크기 VM 구독이 할당 취소된 상태로 설정됩니다. 작동이 중지되고 더 이상 청구 요금이 발생하지 않습니다.

> [!NOTE]
> 이 VM 크기 중지는 HB 시리즈의 VM 크기에만 영향을 미칩니다. 이 중지 공지는 최신 HBv2, HBv3 및 HC 시리즈 VM에는 적용되지 않습니다.

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?

H 시리즈 VM의 크기를 조정하거나 할당을 조정해야 합니다. 워크로드를 원래 H 시리즈 VM 및 H 시리즈 프로모션 VM에서 최신 제품으로 마이그레이션하는 것이 좋습니다.

[HBv2](hbv2-series.md) 및 [HBv3](hbv3-series.md) VM은 다음과 같은 이유로 상당히 높은 수준의 HPC 워크로드 성능 및 비용 효율성을 제공합니다.

- CPU 코어 아키텍처가 크게 향상되었습니다.
- 더 높은 메모리 대역폭.
- 더 큰 L3 캐시.
- HB 시리즈에 비해 InfiniBand 네트워킹이 향상되었습니다.

결과적으로 HBv2 및 HBv3 시리즈는 일반적으로 비용 단위당 훨씬 더 나은 성능(고정 지출에 대한 성능 최대화) 및 성능당 비용(고정 성능에 대한 비용 최소화)을 제공합니다.

HB 시리즈 VM을 포함하는 모든 지역에는 HBv2 및 HBv3 시리즈 VM이 포함됩니다. HB 시리즈 VM에서 실행되는 기존 워크로드는 지리적 배치나 해당 지역의 더 많은 서비스에 대한 액세스에 대한 걱정 없이 마이그레이션할 수 있습니다.

[HB 시리즈](hb-series.md) VM은 2024년 9월까지 사용 중지되지 않습니다. 마이그레이션을 평가, 계획 및 실행할 수 있는 긴 기간을 제공하기 위해 이 가이드를 미리 제공하고 있습니다.

### <a name="recommendations-for-workload-migration-from-hb-series-vms"></a>HB 시리즈 VM에서 워크로드 마이그레이션을 위한 권장 사항

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이점  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM  <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-45rs |표준 HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> 표준_HB120-32rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM  <br>  메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-30rs |표준_HB120-32rs_v3 <br> 표준_HB120-16rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-15rs |표준_HB120-16rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |

### <a name="migration-steps"></a>마이그레이션 단계

1. 마이그레이션할 시리즈와 크기를 선택합니다.
1. 대상 VM 시리즈에 대한 할당량을 얻습니다.
1. 현재 HB 시리즈 VM 크기를 대상 크기로 조정합니다.

### <a name="get-a-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량을 얻습니다.

가이드에 따라 [VM 제품군에 의한 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다.

### <a name="resize-the-current-vm"></a>현재 VM 크기 조정

가상 [머신 의 크기를 변경할](resize-vm.md)수 있습니다.
