---
title: Azure 지도 Power BI 시각적 개체에 열 지도 계층 추가
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Azure 지도 Power BI 시각적 개체에서 열 지도 계층을 사용하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
manager: jokebeck
ms.date: 11/29/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a49b4ae18c5f7cfd00f70a5ac537c671b63ef071
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484565"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

이 문서에서는 Azure 지도 Power BI 시각적 개체에 열 지도 계층을 추가하는 방법을 알아봅니다.

:::image type="content" source="media/power-bi-visual/heat-map.png" alt-text="Azure 지도 Power BI 시각적 개체의 열 지도 계층":::

밀도 맵이라고도 하는 열 지도는 다양한 색을 사용하여 데이터의 밀도를 나타내는 데 사용되는 맵의 오버레이 형식입니다. 열 지도는 맵에 데이터 "핫 스폿"을 표시하는 데 자주 사용됩니다. 열 지도는 많은 수의 요소가 있는 데이터 세트를 렌더링하는 좋은 방법입니다. 맵에 많은 수의 데이터 요소가 표시되면 성능이 저하되고 겹치는 기호로 덮어 사용할 수 없게 됩니다. 데이터를 열 지도 결과로 렌더링하면 성능이 향상되는 것뿐만 아니라 각 데이터 포인트의 상대적 밀도를 쉽게 볼 수 있도록 하여 데이터를 더 잘 이해할 수 있습니다.

열 지도는 사용자가 방대한 비교 데이터를 시각화하려는 경우에 유용합니다.

- 지역 또는 국가 간 고객 만족도 또는 상점 성과 비교
- 고객이 다른 위치에 있는 쇼핑 상점을 방문하는 빈도 측정
- 방대한 통계 및 지리적 데이터 집합 시각화

## <a name="prerequisites"></a>사전 요구 사항

- [Azure 지도 Power BI 시각적 개체를 시작합니다.](./power-bi-visual-get-started.md)
- [Azure 지도 Power BI 시각적 개체의 레이어를 이해합니다.](./power-bi-visual-understanding-layers.md)

## <a name="add-the-heat-map-layer"></a>열 지도 계층 추가

1. Power BI Desktop 만든 Azure 맵을 선택합니다.
1. **서식** 창에서 **열 지도** 토글을 **켜기로 전환합니다.**

이제 보고서에 맞게 모든 열 지도 계층 설정을 조정할 수 있습니다.

## <a name="heat-map-layer-settings"></a>열 지도 계층 설정

**서식** 창의 **열 지도** 섹션은 특정 요구 사항에 맞게 열 지도 시각화를 사용자 지정하고 디자인할 수 있는 유연성을 제공합니다. **열 지도** 섹션을 사용하여 다음을 수행할 수 있습니다.

- 픽셀 또는 미터를 측정 단위로 사용하여 각 데이터 포인트의 반경을 구성합니다.
- 열 지도 계층의 불투명도 및 강도를 사용자 지정합니다.  
- 크기 필드의 값을 각 데이터 포인트의 가중치로 사용할지 지정합니다.
- 색 선택기에서 다른 색을 선택합니다.
- 열 지도 계층이 표시될 최소 및 최대 확대/축소 수준을 설정합니다.
- 여러 계층(예: 3D 막대형 차트 계층 및 거품형 계층)에서 열 지도 계층 위치를 결정합니다.

다음 표에서는 **서식** 창의 **열 지도** 섹션에서 사용할 수 있는 기본 설정을 보여줍니다.

| Setting              | 설명      |
|----------------------|------------------|
| 반지름 | 열 지도에 있는 각 데이터 포인트의 반지름입니다.<br /><br />Unit = 'pixels'인 경우 유효한 값: 1 - 200 기본값: **20**<br />단위 = '미터'인 경우 유효한 값: 1 - 4,000,000|
| 단위  | 반지름의 거리 단위입니다. 가능한 값은 다음과 같습니다.<br /><br />**픽셀 입니다.** 픽셀로 설정하면 확대/축소 수준에 관계없이 각 데이터 포인트의 크기가 항상 동일합니다.<br />**미터**. 미터로 설정하면 데이터 포인트의 크기는 확대/축소 수준에 따라 크기가 조정되어 반경이 공간적으로 정확한지 확인합니다.<br /><br /> 기본값: **픽셀**  |
| 불투명도 | 열 지도 계층의 불투명도를 설정합니다. 기본값: **1**<br/>값은 0에서 1 사이의 10진수여야 합니다. |
| 강도 | 각 열 지점의 강도입니다. 강도는 단일 데이터 포인트의 "핫"을 지정하는 데 사용되는 0에서 1 사이의 10진수 값입니다. 기본값: **0.5** |
| 크기를 가중치로 사용 | 크기 필드 값을 각 데이터 포인트의 가중치로 사용해야 하는지 여부를 결정하는 부울 값입니다. 켜진 경우 계층이 가중 열 지도로 렌더링됩니다. 기본값: **끄기** |
| 그라데이션 |사용자가 낮음(0%), 가운데(50%) 및 높음(100%) 그라데이션 색에 대해 3가지 색을 선택할 수 있도록 색을 선택합니다. |
| 최소 확대/축소 |계층이 표시되는 최소 확대/축소 수준입니다. 유효한 값은 1~22입니다. 기본값: **0** |
|최대 확대/축소 |계층이 표시되는 최대 확대/축소 수준입니다.  유효한 값은 1~22입니다. 기본값: **22**|
|레이어 위치 |다른 지도 계층을 기준으로 계층의 위치를 지정합니다. 유효한 값에는 **위의 레이블,** 아래 레이블 및 **아래 도로가** 포함됩니다.  |

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
