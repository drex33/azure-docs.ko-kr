---
title: NV 시리즈 사용 중지
description: 2021년 9월 1일부터 NV 시리즈 사용 중지
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538302"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>2022년 8월 31일까지 NV 및 NV_Promo 시리즈 가상 머신 마이그레이션
데이터 센터 기술의 최신 혁신 기능을 사용하여 최적화된 최신 가상 머신 인스턴스를 Azure에 계속 도입함에 따라 하드웨어 에이징의 사용을 중지하는 방식을 신중하게 계획하고 있습니다.
이를 고려하여 2022년 9월 1일에 NV 시리즈 Azure 가상 머신 크기를 사용 중지할 예정입니다.

## <a name="how-does-the-nv-series-migration-affect-me"></a>NV 시리즈 마이그레이션은 어떤 영향을 주나요?  

2022년 9월 1일 이후 구독에 남아 있는 모든 NV 및 NV_Promo 크기 가상 머신은 할당 취소됨 상태로 설정됩니다. 해당 가상 머신은 호스트에서 중지되고 제거됩니다. 이러한 가상 머신은 할당 취소됨 상태에서 더 이상 요금이 청구되지 않습니다. 

현재 VM 크기 사용 중지는 [NV 시리즈](nv-series.md)의 VM 크기에만 영향을 줍니다. [NVv3](nvv3-series.md) 및 [NVv4](nvv4-series.md) 시리즈 가상 머신에는 영향을 주지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?  

NV 가상 머신의 크기를 조정하거나 할당을 취소해야 합니다. GPU 시각화/그래픽 워크로드를 다른 [GPU 가속 가상 머신 크기](sizes-gpu.md)로 이동하는 것이 좋습니다.

워크로드를 다른 GPU Azure 가상 머신 크기로 마이그레이션하는 방법에 관해 [자세히 알아봅니다](nv-series-migration-guide.md). 

질문이 있는 경우 고객 지원을 통해 문의하세요.
