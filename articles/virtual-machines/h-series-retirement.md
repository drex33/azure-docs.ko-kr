---
title: H 시리즈 사용 중지
description: 2021년 9월 1일부터 H 시리즈 사용 중지
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 98465066f79f93777255a3072c472d342b557bdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600231"
---
# <a name="migrate-your-h-and-h_promo-series-virtual-machines-by-august-31-2022"></a>2022년 8월 31일까지 H 및 H_Promo 시리즈 가상 머신 마이그레이션
Microsoft Azure에서 최신 세대의 HPC(고성능 컴퓨팅), 범용 및 메모리 최적화 가상 머신을 도입함에 따라 원래 H 시리즈(H 시리즈 프로모션 포함) 가상 머신에서 최신 제품으로 워크로드를 마이그레이션하는 것이 좋습니다.

Azure [HC](hc-series.md), [HBv2](hbv2-series.md), [HBv3](hbv3-series.md), [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md), [Ev4](ev4-esv4-series.md) 및 [Eav4](eav4-easv4-series.md) 가상 머신은 광범위한 HPC 워크로드에서 더 큰 메모리 대역폭, 향상된 네트워킹 기능 및 향상된 비용 대비 성능을 제공합니다. 따라서 2022년 8월 31일에 H 시리즈 Azure Virtual Machine 크기(H8, H8m, H16, H16r, H16m, H16mr, H8 Promo, H16 Promo, H16r Promo, H16m Promo 및 H16mr Promo)의 사용이 중단될 예정입니다.

## <a name="how-does-the-h-series-migration-affect-me"></a>H 시리즈 마이그레이션은 어떤 영향을 주나요?  

2022년 8월 31일 이후에 위에 언급된 나머지 H 시리즈 가상 머신 구독은 할당 취소된 상태로 설정되고, 작동이 중지되며, 더 이상 청구 요금이 발생하지 않습니다. 

현재 VM 크기 사용 중지는 H 시리즈 Promo를 비롯한 [H 시리즈](h-series.md)의 VM 크기에만 영향을 줍니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?  

H 시리즈 가상 머신의 크기를 조정하거나 할당을 철회해야 합니다. 원래 H 시리즈(H 시리즈 프로모션 포함) 가상 머신에서 최신 제품으로 워크로드를 마이그레이션하는 것이 좋습니다.

HPC 워크로드의 경우 [HC](hc-series.md), [HBv2](hbv2-series.md) 및 [HBv3](hbv3-series.md) VM은 H 시리즈와 비교하여 CPU 코어 아키텍처의 향상된 기능, 더 높은 메모리 대역폭, 더 큰 L3 캐시 및 향상된 InfiniBand 네트워킹 하드웨어 및 소프트웨어 지원으로 인해 더 높은 수준의 HPC 워크로드 성능 및 비용 효율성을 제공합니다. 결과적으로 HC, HBv2 및 HBv3 시리즈는 비용 단위(고정된 지출 금액에 대한 성능 극대화) 뿐만 아니라 성능에 따른 비용(고정된 성능 양에 대한 비용 최소화)에 대해서도 훨씬 더 나은 성능을 제공합니다. 

범용 워크로드의 경우 [Dv4](dv4-dsv4-series.md), [Dav4](dav4-dasv4-series.md) 및 Dv5 VM은 동일하거나 더 큰 코어 수에서 동일하거나 더 나은 CPU 성능, 실제 CPU 코어당 비슷한 양의 메모리, 더 나은 Azure 네트워킹 기능 및 더 낮은 전반적인 비용을 제공합니다. 

메모리 최적화 워크로드의 경우 [Ev4](ev4-esv4-series.md), [Eav4](eav4-easv4-series.md) 및 Ev5 VM은 동일하거나 더 큰 코어 수에서 동일하거나 더 나은 CPU 성능, 실제 CPU 코어당 비슷한 양의 메모리, 더 나은 Azure 네트워킹 기능 및 더 낮은 전반적인 비용을 제공합니다. 

[H 시리즈](h-series.md) VM(H 시리즈 Promo 포함)은 2022년 9월에 사용이 중지되므로 사용자가 오랜 기간 마이그레이션을 평가, 계획 및 실행하도록 이 가이드를 미리 제공합니다. 


### <a name="migration-steps"></a>마이그레이션 단계 
1. 마이그레이션을 위한 시리즈 및 크기 선택 
2. 대상 VM 시리즈에 대한 할당량 얻기 
3. 현재 H 시리즈 VM 크기를 대상 크기로 조정 


### <a name="breaking-changes"></a>주요 변경 내용 
InfiniBand 네트워킹 인터페이스를 노출하는 H 시리즈 VM 크기(예: VM 크기 이름에 'r'이 있는 크기)를 사용하고 새 VM 크기가 InfiniBand 네트워킹도 지원하게 하려면 더 이상 기본 제공 InfiniBand 드라이버 지원(CentOS 7.4 및 이전, Windows Server 2012)에서 레거시 OS 이미지를 사용할 수 없습니다. 대신, 최신 운영 체제(CentOS 7.5 이상, Windows Server 2016 이상) 및 표준 OFED 드라이버를 지원하는 Azure Marketplace의 이미지와 같은 최신 OS 이미지를 사용합니다. 해당 VM 크기에 대한 지원되는 OS를 포함하여 [지원되는 소프트웨어 스택](hbv3-series.md#get-started)을 참조하세요. 


### <a name="get-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량 얻기 

가이드에 따라 [VM 제품군별로 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다.


### <a name="resize-the-current-virtual-machine"></a>현재 가상 머신 크기 조정
[가상 컴퓨터의 크기를 조정할](resize-vm.md)수 있습니다.
