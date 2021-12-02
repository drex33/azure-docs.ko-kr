---
title: Azure 지도 Power BI 시각적 개체에 참조 계층 추가
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Azure 지도 Power BI 시각적 개체에서 참조 계층을 사용하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
manager: jokebeck
ms.date: 11/29/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 05d3a13b97dfc0dce030e677dfa752afc19d3143
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480913"
---
# <a name="add-a-reference-layer"></a>참조 레이어 추가

참조 계층 기능을 사용하면 보조 공간 데이터 세트를 시각적 개체에 업로드하고 지도에 중첩하여 추가 컨텍스트를 제공할 수 있습니다. 이 데이터 세트는 Power BI에서 호스트되며 `.json` 또는 `.geojson` 파일 확장명을 갖는 [GeoJSON 파일](https://wikipedia.org/wiki/GeoJSON)이어야 합니다.

**GeoJSON** 파일을 참조 계층으로 추가하려면 **서식** 창으로 이동하여 **참조 계층** 섹션을 확장하고 **+ 로컬 파일 추가** 단추를 누릅니다.

GeoJSON 파일이 참조 계층에 추가되면 **X** 가 옆에 있는 **+ 로컬 파일 추가** 단추 대신 파일 이름이 표시됩니다. **X** 단추를 눌러 시각적 개체에서 데이터를 제거하고 Power BI에서 GeoJSON 파일을 삭제합니다.

다음 지도에는 [콜로라도의 2016년 인구 조사](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)(모집단별로 색 지정)가 표시됩니다.

> [!div class="mx-imgBorder"]
> ![콜로라도의 2016년 인구 조사를 표시하고 모집단별로 색을 지정하여 참조 계층으로 보여 주는 지도](media/power-bi-visual/reference-layer-CO-census-tract.png)

:::image type="content" source="./media/power-bi-visual/reference-layer-CO-census-tract.png" alt-text="인구별로 참조 계층으로 색이 지정되어 있는 2016년 인구 조사 지역을 표시하는 지도입니다.":::

다음은 **참조 계층** 섹션에서 사용할 수 있는 **서식** 창의 모든 설정입니다.

| 설정              | 설명   |
|----------------------|---------------|
| 참조 계층 데이터 | 지도 내에서 추가 계층으로 시각적 개체에 업로드할 데이터 GeoJSON 파일입니다. **+ 로컬 파일 추가** 단추는 사용자가 `.json` 또는 `.geojson` 파일 확장명을 갖는 GeoJSON 파일을 선택하는 데 사용할 수 있는 파일 대화 상자를 엽니다. |

> [!NOTE]
> Azure 지도 Power BI 시각적 개체의 이 미리 보기에서 참조 계층은 처음 5,000개의 도형 기능만 맵에 로드합니다. 이 제한은 향후 업데이트에서 늘어날 예정입니다.

## <a name="styling-data-in-a-reference-layer"></a>참조 계층의 데이터 스타일 지정

GeoJSON 파일 내의 각 기능에 속성을 추가하여 지도에서 데이터 스타일 방식을 사용자 지정할 수 있습니다. 이 기능은 Azure Maps Web SDK의 단순 데이터 계층 기능을 사용합니다. 자세한 내용은 [지원되는 스타일 속성](spatial-io-add-simple-data-layer.md#default-supported-style-properties)에 대한 이 문서를 참조하세요. 사용자 지정 아이콘 이미지는 보안 예방 조치로 Azure 지도 Power BI 시각적 개체 내에서 지원되지 않습니다.

다음은 표시된 색을 빨강으로 설정하는 GeoJSON 점 기능의 예입니다.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```

## <a name="next-steps"></a>다음 단계

지도에 컨텍스트 추가:

> [!div class="nextstepaction"]
> [타일 레이어 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
