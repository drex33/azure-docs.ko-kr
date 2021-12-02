---
title: Azure 지도 Power BI 시각적 개체에 막대형 차트 계층 추가
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Azure 지도 Power BI 시각적 개체에서 막대형 차트 계층을 사용하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
manager: jokebeck
ms.date: 11/29/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 104290f22ff447821c6e9c5d6b1f05cd007ae65a
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486545"
---
# <a name="add-a-bar-chart-layer"></a>가로 막대형 차트 레이어 추가

**막대형 차트 레이어는** 맵에서 위치 데이터의 시각화를 3D 막대나 원통 형태로 허용해 다음 차원으로 데이터를 가져오는 데 유용합니다. 거품형 레이어와 마찬가지로 막대형 차트는 추후 색과 상대 높이를 사용해 두 메트릭을 동시에 쉽게 시각화할 수 있습니다. 막대의 높이를 표시하려면 **크기** 버킷에 측정값을 추가해 **필드** 창에 추가해 합니다. 측정값이 제공되지 않은 경우에는 **막대 도형** 옵션에 따라 높이가 없는 막대가 플랫 사각형 또는 원으로 표시됩니다.

:::image type="content" source="./media/power-bi-visual/bar-chart-layer-styled.png" alt-text="막대형 차트 레이어를 사용해 점 데이터를 표시하는 맵":::

사용자는 맵을 기울이거나 회전시켜 여러 관점에서 데이터를 볼 수 있습니다. 다음 방법 중 하나를 사용하여 맵을 기울이거나 피치할 수 있습니다.

- **탐색 컨트롤** 옵션을 **맵 설정** 에서 켜 **서식** 창에서 실행합니다. 그러면 맵을 기울이는 단추가 추가됩니다.
- 마우스 오른쪽 단추를 클릭하고 마우스를 위나 아래로 끕니다.
- 터치 스크린을 사용하여 맵을 두 손가락으로 터치하고 위쪽 또는 아래쪽으로 함께 끕니다.
- 맵에 포커스가 있는 상태에서 **Shift** 키를 누른 채로 **위쪽** 또는 **아래쪽 화살표** 키를 누릅니다.

다음 방법 중 하나를 사용하여 맵을 회전시킬 수 있습니다.

- **탐색 컨트롤** 옵션을 **맵 설정** 에서 켜 **서식** 창에서 실행합니다. 맵을 회전시키는 단추가 추가됩니다.
- 마우스 오른쪽 단추를 누르고 마우스를 왼쪽이나 오른쪽으로 끕니다.
- 터치 스크린을 사용하여 두 손가락으로 맵을 터치하고 회전합니다.
- 맵에 포커스가 있는 상태에서 **Shift** 키를 누른 채 **왼쪽** 또는 **오른쪽 화살표** 키를 누릅니다.

다음은 **서식** 창의 모든 설정으로, **막대형 차트 레이어** 섹션에서 사용할 수 있습니다.

| 설정              | 설명      |
|----------------------|------------------|
| 막대 도형            | 3D 막대의 도형입니다.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• 상자 – 사각형 상자로 렌더링됩니다.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• 원통형 – 원통으로 렌더링된 막대형입니다. |
| 높이               | 항목의 높이입니다. 필드가 **크기** 버킷에 전달되어 **필드** 창에 전달되는 경우, 이 높이 값에 비례하여 막대 배율이 조정됩니다. |
| 확대/축소 시 높이 조정 | 막대의 높이를 확대/축소 수준에 비례하여 조정할지 여부를 지정합니다. |
| 너비                | 각 막대의 너비입니다.  |
| 확대/축소 시 너비 조정  | 막대의 너비를 확대/축소 수준에 비례하여 조정할지 여부를 지정합니다.  |
| 채우기 색           | 각 막대의 색입니다. 이 옵션은 **범례** 버킷에 필드가 전달되어 **필드** 창에 작성된 상태에서는 숨겨져 있으며 **데이터 색** 섹션이 **양식** 창에 나타납니다. |
| 투명성         | 각 막대의 투명도입니다. |
| 최소 확대/축소             | 최소 확대/축소 수준 타일을 사용할 수 있습니다. |
| 최대 확대/축소             | 최대 확대/축소 수준 타일을 사용할 수 있습니다. |
| 레이어 위치       | 다른 맵 레이어를 기준으로 레이어의 위치를 지정합니다. |

> [!NOTE]
> 막대가 작은 너비 값에 **확대/축소 시 너비 조정** 옵션을 사용하지 않도록 설정하면 렌더링된 너비가 픽셀보다 작게 축소될 때 사라질 수 있습니다. 그러나 **확대/축소 시 너비 조정** 옵션을 사용하는 경우 확대/축소 수준을 변경하면 큰 데이터 집합의 성능에 영향을 줄 수 있는 추가 계산이 수행됩니다.

## <a name="next-steps"></a>다음 단계

지도에 데이터가 표시되는 방식 변경:

> [!div class="nextstepaction"]
> [거품형 계층 추가](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](power-bi-visual-add-heat-map-layer.md)

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
