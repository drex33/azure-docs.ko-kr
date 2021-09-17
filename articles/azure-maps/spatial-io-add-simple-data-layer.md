---
title: 단순 데이터 레이어 추가 | Microsoft Azure Maps
description: Azure Maps Web SDK에서 제공하는 공간 IO 모듈을 사용하여 단순 데이터 레이어를 추가하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 6f1855474454cc69489b76e03b8ce17dd97ccf32
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437662"
---
# <a name="add-a-simple-data-layer"></a>단순 데이터 레이어 추가

공간 IO 모듈은 `SimpleDataLayer` 클래스를 제공합니다. 이 클래스를 사용하면 지도에서 스타일이 지정된 기능을 쉽게 렌더링할 수 있습니다. 스타일 속성을 가진 데이터 세트와 혼합 기하학 유형을 포함하는 데이터 세트를 렌더링할 수도 있습니다. 단순 데이터 레이어는 여러 렌더링 레이어를 래핑하고 스타일 식을 사용하여 이 기능을 수행합니다. 스타일 식은 이러한 래핑된 레이어 내에서 기능의 공통 스타일 속성을 검색합니다. `atlas.io.read` 함수 및 `atlas.io.write` 함수는 이러한 속성을 사용하여 스타일을 읽고 지원되는 파일 형식으로 씁니다. 지원되는 파일 형식에 속성을 추가한 후에는 다양한 용도로 파일을 사용할 수 있습니다. 예를 들어 파일을 사용하여 지도에 스타일이 지정된 기능을 표시할 수 있습니다.

스타일링 기능 외에도 `SimpleDataLayer`는 팝업 템플릿을 사용하여 기본 제공 팝업 기능을 제공합니다. 기능을 클릭하면 팝업이 표시됩니다. 원하는 경우 기본 팝업 기능을 사용하지 않도록 설정할 수 있습니다. 이 레이어는 클러스터형 데이터도 지원합니다. 클러스터를 클릭하면 맵이 클러스터를 확대하여 개별 포인트 및 하위 클러스터로 확장됩니다.

`SimpleDataLayer` 클래스는 여러 기하학 유형과 기능에 적용되는 많은 스타일이 포함된 대량 데이터 세트에서 사용하기 위한 것입니다. 이 클래스를 사용하는 경우 스타일 식이 포함된 6개 레이어의 오버헤드를 추가합니다. 따라서 핵심 렌더링 레이어를 사용하는 것이 더 효율적일 수 있습니다. 예를 들어 핵심 레이어를 사용하여 기능에 몇 가지 기하학 유형 및 스타일을 렌더링합니다.

## <a name="use-a-simple-data-layer"></a>단순 데이터 레이어 사용

`SimpleDataLayer` 클래스는 다른 렌더링 레이어가 사용되는 것과 같은 방식으로 사용됩니다. 아래 코드는 맵에서 단순 데이터 레이어를 사용하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

데이터 원본에 기능 추가 그런 다음, 단순 데이터 레이어를 통해 기능을 렌더링하는 최상의 방법을 파악할 수 있습니다. 개별 기능에 대한 스타일을 기능 속성으로 설정할 수 있습니다. 다음 코드에서는 `color` 속성이 `red`로 설정된 GeoJSON 포인트 기능을 보여 줍니다. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

다음 코드에서는 단순 데이터 레이어를 사용하여 위의 포인트 기능을 렌더링합니다. 

<br/>

<iframe height="500" scrolling="no" title="단순 데이터 레이어 사용" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>단순 데이터 레이어 사용</a>을 참조하세요.
</iframe>

단순 데이터 레이어의 실제 강력한 기능은 다음과 같은 경우에 나타납니다.

- 데이터 원본에 여러 가지 유형의 기능이 있는 경우
- 데이터 세트의 기능에 개별적으로 설정된 여러 가지 스타일 속성이 있는 경우
- 데이터 세트에 정확하게 포함된 내용이 확실하지 않을 경우

예를 들어 XML 데이터 피드를 구문 분석할 때 기능의 정확한 스타일 및 기하학 유형을 모를 수 있습니다. 다음 샘플에서는 KML 파일의 기능을 렌더링하여 단순 데이터 레이어의 강력한 기능을 보여 줍니다. 또한 단순 데이터 레이어 클래스에서 제공하는 다양한 옵션을 보여 줍니다.

<br/>

<iframe height="700" scrolling="no" title="단순 데이터 레이어 옵션" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> <a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>단순 데이터 레이어 옵션</a>을 참조하세요.
</iframe>


> [!NOTE]
> 이 단순 데이터 레이어는 [팝업 템플릿](map-add-popup.md#add-popup-templates-to-the-map) 클래스를 사용하여 KML 풍선을 표시하거나 기능 속성을 테이블로 표시합니다. 기본적으로 팝업에 렌더링된 모든 콘텐츠는 보안 기능으로 iframe 내에서 샌드박스가 적용됩니다. 그러나 제한 사항이 있습니다.
>
> - 모든 스크립트, 양식, 포인터 잠금 및 상위 탐색 기능을 사용할 수 없습니다. 링크를 클릭하면 새 탭에서 링크를 열 수 있습니다. 
> - iframe에서 `srcdoc` 매개 변수를 지원하지 않는 이전 브라우저는 적은 양의 콘텐츠를 렌더링하도록 제한됩니다.
> 
> 팝업에 로드되는 데이터를 신뢰하고 이러한 스크립트를 팝업에 로드하여 애플리케이션에 액세스할 수 있도록 하려면 팝업 템플릿 `sandboxContent` 옵션을 false로 설정하여 이를 사용하지 않도록 설정할 수 있습니다. 

## <a name="default-supported-style-properties"></a>지원되는 기본 스타일 속성

앞서 설명한 대로 단순 데이터 레이어는 거품형, 기호, 선, 다각형 및 돌출 다각형의 몇 가지 핵심 렌더링 레이어를 래핑합니다. 그런 다음, 식을 사용하여 개별 기능에 대해 유효한 스타일 속성을 검색합니다.

Azure Maps 및 GitHub 스타일 속성은 지원되는 속성 이름의 두 가지 주요 세트입니다. 다양한 Azure 맵 레이어 옵션의 속성 이름 대부분은 단순 데이터 레이어에서 기능의 스타일 속성으로 지원됩니다. GitHub에서 일반적으로 사용되는 스타일 속성 이름을 지원하기 위해 일부 레이어 옵션에 식이 추가되었습니다. 이러한 속성 이름은 [GitHub의 GeoJSON 맵 지원](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)으로 정의되며 플랫폼 내에서 저장되고 렌더링되는 GeoJSON 파일의 스타일을 지정하는 데 사용됩니다. `marker-symbol` 스타일링 속성을 제외한 모든 GitHub의 스타일링 속성은 단순 데이터 레이어에서 지원됩니다.

판독기가 덜 일반적인 스타일 속성을 발견하는 경우 가장 가까운 Azure Maps 스타일 속성으로 변환합니다. 또한 단순 데이터 레이어의 `getLayers` 함수를 사용하여 레이어의 옵션을 업데이트하면 기본 스타일 식이 재정의될 수 있습니다.

다음 섹션에서는 단순 데이터 레이어에서 지원되는 기본 스타일 속성에 대해 자세히 설명합니다. 지원되는 속성 이름의 순서도 속성의 우선 순위입니다. 같은 레이어 옵션에 대해 두 개의 스타일 속성이 정의된 경우 목록의 첫 번째 속성이 우선 순위가 높습니다. 색은 HEX, RGB, RGBA, HSL, HSLA 또는 명명된 색 값 등 CSS3 색 값이 될 수 있습니다.

### <a name="bubble-layer-style-properties"></a>거품형 레이어 스타일 속성

기능이 `Point` 또는 `MultiPoint`이고 기능에 포인트를 기호로 렌더링하는 사용자 지정 아이콘으로 사용되는 `image` 속성이 없는 경우, 기능이 `BubbleLayer`와 함께 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` 및 `scale` 값은 스칼라 값으로 간주되며 `8`을 곱합니다.

\[2\] GitHub `marker-size` 옵션을 지정하면 다음 값이 반경에 사용됩니다.

| 표식 크기 | 반지름 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

클러스터도 거품형 레이어를 사용하여 렌더링됩니다. 기본적으로 클러스터의 반경은 `16`으로 설정됩니다. 아래 정의된 대로 클러스터의 색은 클러스터의 포인트 수에 따라 달라집니다.

| 포인트 수 | 색    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>기호 스타일 속성

기능이 `Point` 또는 `MultiPoint`이고 기능에 포인트를 기호로 렌더링하는 사용자 지정 아이콘으로 사용되는 `image` 속성이 없는 경우, 기능이 `SymbolLayer`와 함께 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] GitHub `marker-size` 옵션을 지정하면 다음 값이 아이콘 크기 옵션에 사용됩니다.

| 표식 크기 | 기호 크기 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

포인트 기능이 클러스터인 경우 `point_count_abbreviated` 속성은 텍스트 레이블로 렌더링됩니다. 이미지가 렌더링되지 않습니다.

### <a name="line-style-properties"></a>선 스타일 속성

기능이 `LineString`, `MultiLineString`, `Polygon` 또는 `MultiPolygon`인 경우 기능이 `LineLayer`로 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>다각형 스타일 속성

기능이 `Polygon` 또는 `MultiPolygon`이고 기능에 `height` 속성이 없거나 `height` 속성이 0인 경우 이 기능은 `PolygonLayer`를 사용하여 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>돌출 다각형 스타일 속성

기능이 `Polygon` 또는 `MultiPolygon`이고 0보다 큰 값을 갖는 `height` 속성이 있는 경우 이 기능은 `PolygonExtrusionLayer`를 사용하여 렌더링됩니다.

| 레이어 옵션 | 지원되는 속성 이름 | 기본값 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC 지도 계층 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)