---
title: ND 시리즈 사용 중지
description: 2022 년 8 월 31 일까 지 ND 시리즈 사용 중지
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7111af4f275f97ff35e4a0cf9846eb6b68a807f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705851"
---
# <a name="migrate-your-nd-series-virtual-machines-by-august-31-2022"></a>2022 년 8 월 31 일까 지 ND 시리즈 virtual machines 마이그레이션
데이터 센터 기술의 최신 혁신을 활용 하 여 최신의 최적화 된 가상 머신 인스턴스를 Azure에 계속 제공 하는 것은 하드웨어 에이징의 사용을 중지 하는 방법을 신중 계획입니다. 이 점을 염두에 두면 2022 년 8 월 31 일에 NVIDIA Tesla P40 Gpu에 의해 제공 되는 ND GPU VM 크기를 사용 중지 합니다. 

## <a name="how-does-the-nd-series-migration-affect-me"></a>ND 시리즈 마이그레이션은 어떻게 영향을 미칩니까?  

2022 년 8 월 31 일 이후 구독에 남아 있는 나머지 ND 크기의 가상 머신은 할당 취소 됨 상태로 설정 됩니다. 해당 가상 머신은 호스트에서 중지되고 제거됩니다. 이러한 가상 머신은 할당 취소됨 상태에서 더 이상 요금이 청구되지 않습니다. 

이 VM 크기는 [ND 시리즈](nd-series.md)의 vm 크기에만 영향을 줍니다. 최신 [NCv3](ncv3-series.md), [NC T4 v3](nct4-v3-series.md)및 [ND v2](ndv2-series.md) 시리즈 virtual machines에는 영향을 주지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?  
NC 가상 머신의 크기를 조정 하거나 할당을 취소 해야 합니다. GPU 워크 로드를 다른 GPU 가상 머신 크기로 이동 하는 것이 좋습니다. 워크 로드를 다른 [GPU 가속 가상 머신 크기로](sizes-gpu.md)마이그레이션하는 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
워크로드를 다른 GPU Azure 가상 머신 크기로 마이그레이션하는 방법에 관해 [자세히 알아봅니다](n-series-migration.md). 

질문이 있는 경우 고객 지원을 통해 문의하세요.
