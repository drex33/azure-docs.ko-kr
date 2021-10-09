---
title: H 시리즈 사용 중지
description: H 시리즈 중지는 2021년 9월 1일부터 시작됩니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: amverma
ms.openlocfilehash: 1ad9a9384d00ec0961235c167379d9984ec417fe
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705243"
---
# <a name="migrate-your-h-and-h-series-promo-virtual-machines-by-august-31-2022"></a>2022년 8월 31일까지 H 및 H 시리즈 프로모션 가상 머신 마이그레이션

Microsoft Azure 최신 세대의 HPC(고성능 컴퓨팅), 범용 및 메모리 최적화 VM(가상 머신)이 도입되었습니다. 따라서 워크로드를 원래 H 시리즈 및 H 시리즈 프로모션 VM에서 최신 제품으로 마이그레이션하는 것이 좋습니다.

Azure [HC,](hc-series.md) [HBv2,](hbv2-series.md) [HBv3,](hbv3-series.md) [Dv4,](dv4-dsv4-series.md) [Dav4,](dav4-dasv4-series.md) [Ev4](ev4-esv4-series.md)및 [Eav4](eav4-easv4-series.md) VM에는 더 큰 메모리 대역폭, 향상된 네트워킹 기능 및 다양한 HPC 워크로드의 비용 및 성능이 향상됩니다. 2022년 8월 31일에 다음과 같은 H 시리즈 Azure VM 크기를 사용 중지합니다.

- H8
- H8m
- H16
- H16r
- H16m
- H16mr
- H8 프로모션
- H8m 프로모션
- H16 프로모션
- H16r 프로모션
- H16m 프로모션
- H16mr 프로모션

## <a name="how-does-the-h-series-migration-affect-me"></a>H 시리즈 마이그레이션은 어떤 영향을 주나요?

2022년 8월 31일 이후 이전 목록의 나머지 H 시리즈 VM 구독은 할당 취소된 상태로 설정됩니다. 작동이 중지되고 더 이상 청구 요금이 발생하지 않습니다.

현재 VM 크기 중지는 H 시리즈 프로모션을 포함하는 [H 시리즈의](h-series.md)VM 크기에만 영향을 주며,

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?

H 시리즈 VM의 크기를 조정하거나 할당을 조정해야 합니다. 워크로드를 원래 H 시리즈 VM 및 H 시리즈 프로모션 VM에서 최신 제품으로 마이그레이션하는 것이 좋습니다.

**HPC 워크로드:** [HC,](hc-series.md) [HBv2](hbv2-series.md)및 [HBv3](hbv3-series.md) VM은 다음과 같은 이유로 상당히 높은 수준의 HPC 워크로드 성능 및 비용 효율성을 제공합니다.

- CPU 코어 아키텍처가 크게 향상되었습니다.
- 더 높은 메모리 대역폭.
- 더 큰 L3 캐시.
- H 시리즈에 비해 InfiniBand 네트워킹 하드웨어 및 소프트웨어 지원이 향상되었습니다.

결과적으로 HC, HBv2 및 HBv3 시리즈는 일반적으로 비용 단위(고정 지출에 대한 성능 최대화) 및 성능당 비용(고정 성능에 대한 비용 최소화)당 훨씬 더 나은 성능을 제공합니다.

**범용 워크로드:** [Dv4,](dv4-dsv4-series.md) [Dav4](dav4-dasv4-series.md)및 Dv5 VM은 동일하거나 더 큰 코어 수, 실제 CPU 코어당 비슷한 양의 메모리, 더 나은 Azure 네트워킹 기능 및 낮은 전체 비용에서 동일하거나 더 나은 CPU 성능을 제공합니다.

**메모리 최적화 워크로드:** [Ev4,](ev4-esv4-series.md) [Eav4](eav4-easv4-series.md)및 Ev5 VM은 동일하거나 더 큰 코어 수에서 동일하거나 더 나은 CPU 성능을 제공하고, 실제 CPU 코어당 비슷한 양의 메모리를 제공하고, Azure 네트워킹 기능을 향상하고, 전체 비용을 낮춥니다.

[H 시리즈](h-series.md) 및 H 시리즈 프로모션 VM은 2022년 9월까지 사용 중지되지 않습니다. 마이그레이션을 평가, 계획 및 실행할 수 있는 긴 기간을 제공하기 위해 이 가이드를 미리 제공하고 있습니다.

### <a name="migration-steps"></a>마이그레이션 단계

1. 마이그레이션할 시리즈와 크기를 선택합니다.
1. 대상 VM 시리즈에 대한 할당량을 얻습니다.
1. 현재 H 시리즈 VM 크기를 대상 크기로 조정합니다.

### <a name="breaking-changes"></a>주요 변경 내용

VM 크기 이름에 "r"이 있는 크기와 같이 InfiniBand 네트워킹 인터페이스를 노출하는 H 시리즈 VM 크기를 사용하고 새 VM 크기가 InfiniBand 네트워킹도 지원하도록 하려면 더 이상 기본 제공 InfiniBand 드라이버 지원(CentOS 7.4 및 이전, Windows Server 2012)으로 레거시 OS 이미지를 사용할 수 없습니다.

대신 최신 운영 체제(CentOS 7.5 이상, Windows Server 2016 이상) 및 표준 OFED 드라이버를 지원하는 Azure Marketplace 사용할 수 있는 것과 같은 최신 OS 이미지를 사용합니다. 각 VM 크기에 [대해 지원되는](hbv3-series.md#get-started)OS를 포함하는 지원되는 소프트웨어 스택 을 참조하세요.

### <a name="get-a-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량을 얻습니다.

가이드에 따라 [VM 제품군에 의한 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다.

### <a name="resize-the-current-vm"></a>현재 VM 크기 조정

가상 [머신 의 크기를 변경할](resize-vm.md)수 있습니다.
