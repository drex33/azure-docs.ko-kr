---
title: Azure Maps Power BI 시각적 개체의 계층 이해
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure 지도 Power BI 시각적 개체에서 사용할 수 있는 다양 한 계층에 대해 설명 합니다.
author: stevemunk
ms.author: v-munksteve
manager: erikland
ms.date: 11/29/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 9b3cebe2cd8735cfd007dbe13c52c9be70b01b90
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361884"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Azure Maps Power BI 시각적 개체의 계층 이해

Azure Maps Power BI 시각적 개체에는 두 가지 유형의 계층을 사용할 수 있습니다. 첫 번째 형식은 시각적 개체의 **필드** 창에 전달되는 데이터의 렌더링과 주로 관련이 있으며 다음 계층으로 구성됩니다. 이러한 데이터 렌더링을 레이어라고 지칭하겠습니다.

:::row:::
    :::column span="":::
        **거품형 계층**

        지도에 점을 스케일링된 원으로 렌더링합니다.

        ![지도의 거품형 계층](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **가로 막대형 차트 계층**

        지도에서 점을 3D 막대로 렌더링합니다.
        
        ![지도의 가로 막대형 차트 계층](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

계층의 두 번째 형식은 데이터의 외부 원본을 지도에 연결하여 더 많은 컨텍스트를 제공하며 다음 계층으로 구성됩니다.

:::row:::
    :::column span="":::
        **참조 계층**

        업로드된 GeoJSON 파일을 지도 위에 오버레이합니다.

        ![지도의 참조 계층](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **타일 계층**

        지도 위에 사용자 지정 타일 계층을 오버레이합니다.
        
        ![지도의 타일 계층](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **트래픽 계층**

        지도에 실시간 트래픽 정보를 오버레이합니다.
        
        ![지도의 트래픽 계층](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

모든 데이터 렌더링 계층 및 **타일 계층** 은 이러한 계층이 표시되는 확대/축소 수준 범위를 지정하는 데 사용되는 최소 및 최대 확대/축소 수준 옵션을 포함합니다. 이를 통해 하나의 확대/축소 수준에서 한 가지 유형의 렌더링 계층을 사용하고 다른 확대/축소 수준의 다른 렌더링 계층으로 전환할 수 있습니다.

이러한 계층에는 지도의 다른 계층을 기준으로 위치를 지정하는 옵션도 있습니다. 여러 데이터 렌더링 계층을 사용하는 경우 **계층 위치** 값이 같을 때 지도에 추가되는 순서에 따라 상대적인 계층화 순서가 결정됩니다.

## <a name="general-layer-settings"></a>일반 계층 설정

**서식** 창의 일반 계층 섹션은 **필드** 창(거품형 계층, 가로 막대형 차트)의 Power BI 데이터 세트에 연결된 계층에 적용되는 공통 설정입니다.

| 설정     | Description   |
|-------------|---------------|
| 선택하지 않은 투명도 | 하나 이상의 도형을 선택하는 경우 선택되지 않은 도형의 투명도입니다.  |
| 0 표시              | 최소 반지름을 사용하여 지도에 크기 값이 0인 점을 표시해야 하는지 여부를 지정합니다. |
| 음수 표시          | 음수 크기 값의 절대값을 그려야 하는지 여부를 지정합니다.   |
| 최소 데이터 값          | 크기를 조정할 입력 데이터의 최소값입니다. 이상값 클리핑에 적합합니다.  |
| 최대 데이터 값          | 크기를 조정할 입력 데이터의 최대값입니다. 이상값 클리핑에 적합합니다.  |

## <a name="next-steps"></a>다음 단계

지도에 데이터가 표시되는 방식 변경:

> [!div class="nextstepaction"]
> [거품형 계층 추가](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [가로 막대형 차트 레이어 추가](power-bi-visual-add-bar-chart-layer.md)

맵에 컨텍스트 추가:

> [!div class="nextstepaction"]
> [참조 레이어 추가](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [타일 레이어 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
