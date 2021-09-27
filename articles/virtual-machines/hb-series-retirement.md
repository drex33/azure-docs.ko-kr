---
title: HB-series-retirement
description: 2021년 9월 1일부터 HB 시리즈 사용 중지
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 2d14b9b3adb9905cc87408db33018746e1479a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680094"
---
# <a name="migrate-your-hb-series-virtual-machines-by-august-31-2024"></a>2024년 8월 31일까지 HB 시리즈 가상 머신 마이그레이션
Microsoft Azure에서 HPC(고성능 컴퓨팅)를 위한 HBv2 및 HBv3 시리즈 가상 머신을 도입함에 따라 원래 HB 시리즈 가상 머신에서 최신 제품으로 워크로드를 마이그레이션하는 것이 좋습니다.  

Azure [HBv2](hbv2-series.md) 및 [HBv3](hbv3-series.md) 가상 머신은 메모리 대역폭, 향상된 RDMA(원격 직접 메모리 액세스) 네트워킹 기능, 더 크고 더 빠른 로컬 반도체 드라이브 및 광범위한 HPC 워크로드에 대한 더 향상된 비용/성능을 제공합니다. 따라서 2024년 8월 31일에 HB 시리즈 Azure Virtual Machine 크기의 사용이 중지됩니다.

## <a name="how-does-the-hb-series-migration-affect-me"></a>HB 시리즈 마이그레이션은 어떤 영향을 주나요?  

2024년 8월 31일 이후에 나머지 HB 크기의 가상 머신 구독은 할당 취소된 상태로 설정되고, 작동이 중지되며, 더 이상 청구 요금이 발생하지 않습니다.  
> [!NOTE]
> 이 VM 크기 사용 중지는 HB 시리즈의 VM 크기에만 영향을 줍니다. 이 사용 중지 알림은 최신 HBv2, HBv3 및 HC 시리즈 가상 머신에는 적용되지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?  

H 시리즈 가상 머신의 크기를 조정하거나 할당을 철회해야 합니다. 원래 H 시리즈(H 시리즈 프로모션 포함) 가상 머신에서 최신 제품으로 워크로드를 마이그레이션하는 것이 좋습니다.

[HBv2](hbv2-series.md) 및 [HBv3](hbv3-series.md) VM은 HB 시리즈와 비교하여 CPU 코어 아키텍처의 향상된 기능, 더 높은 메모리 대역폭, 더 큰 L3 캐시 및 향상된 InfiniBand 네트워킹으로 인해 더 높은 수준의 HPC 워크로드 성능 및 비용 효율성을 제공합니다. 결과적으로 HBv2 및 HBv3 시리즈는 비용 단위(즉, 고정된 지출 금액에 대한 성능 극대화) 뿐만 아니라 성능에 따른 비용(즉, 고정된 성능 양에 대한 비용 최소화)에 대해서도 훨씬 더 나은 성능을 제공합니다.

HB 시리즈 VM을 포함하는 모든 지역에는 HBv2 및 HBv3 시리즈 가상 머신이 포함되어 있으므로 HB 시리즈 VM에서 실행되는 기존 워크로드는 지리적 배치나 해당 지역의 추가 서비스 액세스에 대한 걱정 없이 마이그레이션될 수 있습니다. 

[HB 시리즈](hb-series.md) VM은 2024년 9월에 사용이 중지되므로 사용자가 오랜 기간 마이그레이션을 평가, 계획 및 실행하도록 이 가이드를 미리 제공합니다. 

### <a name="recommendations-for-workload-migration-from-hb-series-virtual-machines"></a>HB 시리즈 Virtual Machines에서 워크로드 마이그레이션에 대한 권장 사항 

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이  |
|---|---|---|
|Standard_HB60rs |Standard_HB120rs_v2 <br> Standard_HB120rs_v3 <br> Standard_HB120-64rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM  <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-45rs |표준 HB120-96rs_v3 <br> Standard_HB120-64rs_v3 <br> 표준_HB120-32rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM  <br>  메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-30rs |표준_HB120-32rs_v3 <br> 표준_HB120-16rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |
|Standard_HB60-15rs |표준_HB120-16rs_v3 |새 CPU: AMD Rome 및 MiIan(+20-30% IPC) <br> 메모리: 최대 2x 이상 RAM <br> 메모리 대역폭: 최대 30% 더 많은 메모리 대역폭 <br> InfiniBand: 200Gb HDR(2x 더 높은 대역폭) <br> 최대 데이터 디스크: 최대 32(+8x) |


### <a name="migration-steps"></a>마이그레이션 단계 
1. 마이그레이션을 위한 시리즈 및 크기 선택 
2. 대상 VM 시리즈에 대한 할당량 얻기 
3. 현재 HB 시리즈 VM 크기를 대상 크기로 조정 


### <a name="get-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량 얻기 

가이드에 따라 [VM 제품군별로 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다.


### <a name="resize-the-current-virtual-machine"></a>현재 가상 머신 크기 조정
가상 [머신 의 크기를 변경할](resize-vm.md)수 있습니다.
