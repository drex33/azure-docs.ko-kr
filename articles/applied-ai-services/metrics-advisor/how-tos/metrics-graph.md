---
title: Metrics Advisor 메트릭 그래프
titleSuffix: Azure Cognitive Services
description: 메트릭 그래프를 구성하고 데이터에서 관련 변칙을 시각화하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342024"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>방법: 메트릭 그래프를 작성하여 관련 메트릭 분석

Metrics Advisor의 각 시계열은 과거 데이터에서 학습하여 미래의 추세를 예측하는 모델에 의해 개별적으로 모니터링됩니다. 데이터 요소가 기록 패턴을 벗어난 경우 변칙이 검색됩니다. 그러나 경우에 따라서는 여러 메트릭이 서로 관련될 수 있으며 여러 메트릭에서 변칙을 분석해야 합니다. **메트릭 그래프** 는 이 작업에 도움이 되는 도구일 뿐입니다. 

예를 들어 여러 관점에서 비즈니스를 모니터링하는 메트릭이 여러 개 있는 경우 변칙 검색이 각각 적용됩니다. 그러나 실제 비즈니스 사례에서 여러 메트릭에 대해 검색된 변칙은 서로 관련되어 있을 수 있습니다. 이러한 관계를 검색하고 근본 원인 기반을 분석하는 것은 실제 문제를 해결하는 데 도움이 됩니다. 메트릭 그래프는 관련 메트릭에 대해 검색된 변칙의 상관 관계를 자동으로 설정하여 문제 해결 프로세스를 가속화합니다. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>첫 번째 노드를 그래프에 표시할 메트릭 선택

탐색 모음에서 **메트릭 그래프** 탭을 클릭합니다. 메트릭 그래프를 작성하는 첫 번째 단계는 그래프에 노드를 추가하는 것입니다. 페이지 맨 위에서 데이터 피드 및 메트릭을 선택합니다. 아래쪽 패널에 노드가 나타납니다. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="메트릭 선택":::

## <a name="add-a-noderelation-on-existing-node"></a>기존 노드에 노드/관계 추가

다음엔 다른 노드를 추가하고 기존 노드와의 관계를 지정해야 합니다. 기존 노드를 선택하고 마우스 오른쪽 단추를 클릭합니다. 상황에 맞는 메뉴가 여러 가지 옵션과 함께 나타납니다. 

**관계 추가** 를 선택하면 다른 메트릭을 선택하고 두 노드 간에 관계 유형을 지정할 수 있습니다. 특정 차원 필터를 적용할 수도 있습니다. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="노드 및 관계 추가":::

위의 단계를 반복하면 모든 관련 메트릭 간의 관계를 설명하는 메트릭 그래프가 표시됩니다.

그래프에서 수행할 수 있는 다른 작업은 다음과 같습니다. 
1.  노드 삭제
2.  메트릭으로 이동
3.  인시던트 허브로 이동
4.  Expand
5.  관계 삭제

## <a name="legend-of-metrics-graph"></a>메트릭 그래프의 범례

그래프의 각 노드는 메트릭을 나타냅니다. 메트릭 그래프에는 다음과 같은 네 가지 종류의 노드가 있습니다.

-  **녹색 노드**: 현재 메트릭 인시던트 심각도를 나타내는 노드가 부족합니다.
- **주황색 노드**: 현재 메트릭 인시던트 심각도를 나타내는 노드가 보통입니다.
- **빨간색 노드**: 현재 메트릭 인시던트 심각도를 나타내는 노드가 높습니다.
- **파란색 노드**: 변칙 심각도가 없는 노드입니다.


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>인시던트 허브에서 관련 메트릭 변칙 상태 보기

메트릭 그래프가 작성되면 그래프 내의 메트릭에서 변칙이 검색될 때마다 관련 변칙 상태를 보고 인시던트에 대한 높은 수준의 보기를 얻을 수 있습니다. 

그래프에서 인시던트를 클릭하고 진단 정보 아래에 있는 **교차 메트릭 분석** 까지 아래로 스크롤합니다.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="관련 메트릭 및 변칙 보기":::

## <a name="next-steps"></a>다음 단계

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트 진단](diagnose-an-incident.md)
- [메트릭을 구성하고 검색 구성 미세 조정](configure-metrics.md)