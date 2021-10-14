---
title: Power BI에 대 한 Azure Maps 시각적 개체에 열 지도 계층 추가 Microsoft Azure 지도
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 지도 시각적 개체의 열 지도 계층을 사용 하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.custom: ''
ms.openlocfilehash: 95d35c3f0ed7ab70ca64249323b22a11eca8f5a9
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008779"
---
# <a name="add-a-heat-map-layer-to-the-azure-maps-visual-for-power-bi"></a>Power BI에 대 한 Azure Maps 시각적 개체에 열 지도 계층을 추가 합니다.

이 문서에서는 Power BI에서 Azure Maps 시각적 개체에 열 지도 계층을 추가 하는 방법에 대해 설명 합니다.

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Power BI에 대 한 Azure Maps 시각적 개체의 열 지도 계층입니다.":::

밀도 맵이 라고도 하는 열 지도는 서로 다른 색을 사용 하 여 데이터 밀도를 나타내는 데 사용 되는 지도의 오버레이 유형입니다. 열 맵은 종종 맵에 데이터 "핫 스폿"을 표시 하는 데 사용 됩니다. 열 지도는 많은 수의 요소가 있는 데이터 세트를 렌더링하는 좋은 방법입니다. 지도에 많은 수의 데이터 요소를 표시 하면 성능이 저하 되 고 겹치는 기호를 사용 하 여 사용할 수 없게 될 수 있습니다. 데이터를 열 맵으로 렌더링 하면 성능이 향상 될 뿐만 아니라 각 데이터 요소의 상대적 밀도를 쉽게 확인할 수 있도록 하 여 데이터를 보다 잘 이해할 수 있습니다.

열 맵은 사용자가 방대한 비교 데이터를 시각화 하려는 경우 유용 합니다.

- 고객 만족도 요금을 비교 하거나 지역 또는 국가 간에 성능을 쇼핑 합니다.
- 고객이 다른 위치에서 쇼핑 malls을 방문 하는 빈도 측정.
- 방대한 통계 및 지리적 데이터 집합 시각화

## <a name="prerequisites"></a>사전 요구 사항

- [Power BI에 대 한 Azure Maps 시각적 개체 시작](/power-bi-visual-getting-started.md)
- [Power BI에 대 한 Azure Maps 시각적 개체의 레이어 이해](/power-bi-visual-understanding-layers.md)

## <a name="add-the-heat-map-layer"></a>열 지도 계층 추가

1. Power BI Desktop에서 사용자가 만든 Azure map을 선택 합니다.
1. **서식** 창에서 **열 지도** 를 **켜기** 로 전환 합니다.

이제 모든 열 지도 계층 설정을 보고서에 맞게 조정할 수 있습니다.

## <a name="heat-map-layer-settings"></a>열 지도 계층 설정

**서식** 창의 **열 지도** 섹션에서는 특정 요구 사항에 맞게 열 지도 시각화를 사용자 지정 하 고 디자인할 수 있는 유연성을 제공 합니다. **열 지도** 섹션에서 다음 작업을 수행할 수 있습니다.

- 픽셀 또는 미터를 측정 단위로 사용 하 여 각 데이터 요소의 반경을 구성 합니다.
- 열 지도 계층의 불투명도 및 강도를 사용자 지정 합니다.  
- 크기 필드의 값을 각 데이터 요소의 가중치로 사용 해야 하는지 여부를 지정 합니다.
- 색 선택기에서 다른 색을 선택 합니다.
- 표시할 열 지도 계층의 최소 및 최대 확대/축소 수준을 설정 합니다.
- 여러 계층 (예: 3D 가로 막대형 차트 계층 및 거품형 계층) 간에 열 지도 계층의 위치를 결정 합니다.

다음 표에서는 **서식** 창의 **열 지도** 섹션에서 사용할 수 있는 기본 설정을 보여 줍니다.

| 설정              | Description      |
|----------------------|------------------|
| 반지름 | 열 지도에 있는 각 데이터 요소의 반경입니다.<br /><br />Unit = ' 픽셀 ' 인 경우 유효한 값: 1-200. 기본값: **20**<br />Unit = ' 미터 ' 인 경우 유효한 값: 1-400만|
| 단위  | 반지름의 거리 단위입니다. 가능한 값은 다음과 같습니다.<br /><br />**픽셀**. 픽셀 (픽셀)로 설정 하면 확대/축소 수준에 관계 없이 각 데이터 요소의 크기가 항상 동일 하 게 됩니다.<br />**미터**. 미터로 설정 하면 데이터 요소의 크기가 확대/축소 수준에 따라 크기가 조정 되어 반지름이 공간적으로 정확 하 게 유지 됩니다.<br /><br /> 기본값: **픽셀**  |
| 불투명도 | 열 지도 계층의 불투명도를 설정 합니다. 기본값: **1**<br/>값은 0에서 1 사이의 10 진수 여야 합니다. |
| 농도가 | 각 열 지점의 강도입니다. 강도는 0에서 1 사이의 10 진수 값으로, 단일 데이터 요소를 "핫"로 지정 하는 방법을 지정 하는 데 사용 됩니다. 기본값: **0.5** |
| 크기를 가중치로 사용 | 크기 필드 값을 각 데이터 요소의 가중치로 사용할지 여부를 결정 하는 부울 값입니다. On으로 설정 하면 계층이 가중치가 적용 된 열 맵으로 렌더링 됩니다. 기본값: **해제** |
| 그라데이션 |낮음 (0%), 가운데 (50%) 및 높은 (100%) 그라데이션 색에 대해 3 가지 색을 선택할 수 있는 색을 선택 합니다. |
| 최소 확대/축소 |계층이 표시 되는 최소 확대/축소 수준입니다. 유효한 값은 1 ~ 22입니다. 기본값: **0** |
|최대 확대/축소 |레이어가 표시 되는 최대 확대/축소 수준입니다.  유효한 값은 1 ~ 22입니다. 기본값: **22**|
|레이어 위치 |다른 지도 계층에 상대적인 계층의 위치를 지정 합니다. 유효한 **값에는 레이블** 아래, **레이블 아래** 및도 **아래** 에 포함 됩니다. |

## <a name="next-steps"></a>다음 단계

지도에 데이터가 표시되는 방식 변경:

> [!div class="nextstepaction"]
> [가로 막대형 차트 레이어 추가](power-bi-visual-add-bar-chart-layer.md)

맵에 컨텍스트 추가:

> [!div class="nextstepaction"]
> [참조 레이어 추가](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [타일 레이어 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)

시각적 개체를 사용자 지정합니다.

> [!div class="nextstepaction"]
> [Power BI의 색 서식을 위한 팁과 힌트](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [시각화 제목, 배경 및 범례 사용자 지정](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
