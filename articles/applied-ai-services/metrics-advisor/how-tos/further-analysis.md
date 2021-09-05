---
title: 인시던트를 추가로 분석하고 영향 평가
titleSuffix: Azure Cognitive Services
description: 분석 도구를 활용하여 인시던트를 추가로 분석하는 방법에 대해 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: 0aefc4207f064550b41f9341e946cae2f1611961
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342403"
---
# <a name="further-analyze-an-incident-and-evaluate-impact"></a>인시던트를 추가로 분석하고 영향 평가

## <a name="metrics-drill-down-by-dimensions"></a>차원에 따라 메트릭 드릴다운

인시던트 정보를 볼 때 다양한 차원과 타임 스탬프 등 더 상세한 정보가 필요할 수 있습니다. 데이터에 하나 이상의 차원이 있다면 드릴다운 기능을 통해 더 상세한 보기를 가져올 수 있습니다. 

드릴다운 기능을 사용하려면 **인시던트 허브** 에서 **메트릭 드릴다운** 탭을 클릭합니다. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="메트릭 드릴다운":::

**차원** 설정은 인시던트의 차원 목록으로, 각각에 대해 사용 가능한 다른 차원 값을 선택할 수 있습니다. 차원 값 변경 후 **타임스탬프** 설정을 통해 다른 시점에서의 현재 인시던트를 볼 수 있습니다.

### <a name="select-drilling-options-and-choose-a-dimension"></a>드릴다운 옵션 선택 및 차원 선택

**드릴다운** 및 **수평 비교** 라는 두 가지 유형의 드릴다운 옵션이 있습니다.

> [!Note]
> - 드릴다운의 경우 현재 선택한 차원을 제외한 다른 차원 값의 데이터를 살펴볼 수 있습니다. 
> - 수평 비교의 경우 종료된 차원을 제외한 다른 차원 값의 데이터를 살펴볼 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="차원 드릴다운":::

### <a name="value-comparison-for-different-dimension-values"></a>다른 차원 값에 대한 값 비교

드릴다운 탭의 두 번째 섹션은 다양한 차원 값에 대한 비교가 있는 테이블입니다. 값, 기준 값, 차이 값, 델타 값 및 변칙 여부를 포함합니다.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="비교 드릴다운" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>다른 차원 값에 대한 값 및 예상 값 비교

드릴다운 탭의 세 번째 섹션은 다양한 차원 값에 대한 값과 예상 값을 포함하는 히스토그램입니다. 이 히스토그램은 값과 예상 값의 차이를 기준으로 정렬됩니다. 가장 큰 영향을 주는 예외 값을 쉽게 찾을 수 있습니다. 예를 들어, 위 그림에서 종료 값을 제외하고 **US7** 이 변칙에 가장 기여하는 것을 확인할 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="테이블 드릴다운" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>원시 값 시각화
드릴다운 탭의 마지막 부분은 원시 값의 꺾은선형 차트입니다. 이 차트가 제공되면 세부 정보를 보기 위해 메트릭 페이지로 이동하지 않아도 됩니다.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="꺾은선형 차트 드릴다운" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="compare-time-series"></a>시계열 비교

경우에 따라 특정 시계열에서 변칙이 검색되면 단일 시각화 안에서 여러 다른 계열과 비교 하는 것이 유용합니다. **비교 도구** 탭을 클릭한 다음, 파란색 **+ 추가** 단추를 클릭합니다. 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="비교할 계열 추가" lightbox="../media/diagnostics/add-series.png":::

데이터 피드에서 계열을 선택합니다. 동일한 세분성 또는 다른 세분성을 선택할 수 있습니다. 대상 차원을 선택하고 계열 추세를 로드한 다음, **확인** 을 클릭하여 이전 계열과 비교 합니다. 계열이 하나의 시각화 안에 함께 배치됩니다. 계속해서 계열을 더 추가하여 비교하고 추가적인 인사이트를 얻을 수 있습니다. **비교 도구** 탭의 맨 위에 있는 드롭다운 메뉴를 클릭하여 시간 이동 기간의 시계열 데이터를 비교합니다.  

> [!Warning]
> 비교를 위해 시계열 데이터 분석을 데이터 포인트로 이동해야 할 수 있으므로 데이터의 세분성에서 이것이 지원되어야 합니다. 예를 들어 데이터가 주간인데 **일일** 비교를 사용한다면 결과를 얻지 못합니다. 이 예제에서는 대신 **월별** 비교를 사용합니다.

시간 이동 비교를 선택한 후에는 데이터 값, 델타 값 또는 백분율 델타를 비교할지 여부를 선택할 수 있습니다.

## <a name="view-similar-anomalies-using-time-series-clustering"></a>시계열 클러스터링을 사용하여 유사 변칙 보기

인시던트를 볼 때 **유사 시계열 클러스터링** 탭을 사용하여 연관 있는 다양한 계열을 볼 수 있습니다. 한 그룹의 계열은 함께 요약됩니다. 위의 그림에서는 둘 이상의 계열 그룹이 있음을 알 수 있습니다. 이 기능은 다음 요구 사항에 부합하는 경우에만 사용 가능합니다.

- 메트릭에 하나 이상의 차원 또는 차원 값이 있어야 합니다.
- 한 메트릭의 계열은 추세가 동일해야 합니다.

사용 가능한 차원은 탭 맨 위에 나열되며 선택하여 계열을 지정할 수 있습니다.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="계열 그룹":::