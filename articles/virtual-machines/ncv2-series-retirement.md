---
title: NCv2 시리즈 중지
description: 2022년 8월 31일까지 NCv2 시리즈 중지
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: 7ed24690ff287c01b5b9c0e404a5f71349a021f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634246"
---
# <a name="migrate-your-ncv2-series-virtual-machines-by-august-31-2022"></a>2022년 8월 31일까지 NCv2 시리즈 가상 머신 마이그레이션
데이터 센터 기술의 최신 혁신을 활용하여 최신의 최적화된 가상 머신 인스턴스를 Azure에 계속 도입함에 따라 노후화된 하드웨어를 사용이용하는 방법을 신중하게 계획합니다. 이 점을 염두에 두고 2022년 8월 31일에 NVIDIA Tesla P100 GPU를 기반으로 하는 NC(v2) GPU VM 크기를 사용 중지합니다. 

## <a name="how-does-the-ncv2-series-migration-affect-me"></a>NCv2 시리즈 마이그레이션은 어떤 영향을 미치나요?  

2022년 8월 31일 이후 구독에 남아 있는 나머지 NCv2 크기의 가상 머신은 할당 취소된 상태로 설정됩니다. 해당 가상 머신은 호스트에서 중지되고 제거됩니다. 이러한 가상 머신은 할당 취소됨 상태에서 더 이상 요금이 청구되지 않습니다. 

이 VM 크기 중지는 [NCv2 시리즈](ncv2-series.md)의 VM 크기에만 영향을 미칩니다. 최신 [NCv3,](ncv3-series.md) [NC T4 v3](nct4-v3-series.md)및 [ND v2](ndv2-series.md) 시리즈 가상 머신에는 영향을 주지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?  
NC 가상 머신의 크기를 조정하거나 할당을 조정해야 합니다. GPU 워크로드를 다른 GPU Virtual Machine 크기로 이동하는 것이 좋습니다. 워크로드를 다른 [GPU 가속 Virtual Machine 크기로](sizes-gpu.md)마이그레이션하는 방법에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

워크로드를 다른 GPU Azure 가상 머신 크기로 마이그레이션하는 방법에 관해 [자세히 알아봅니다](n-series-migration.md). 

질문이 있는 경우 고객 지원을 통해 문의하세요.
