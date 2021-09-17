---
title: 지도에 열 지도 계층 추가 | Microsoft Azure Maps
description: 열 지도를 만드는 방법을 알아봅니다. Azure Maps Web SDK를 사용하여 지도에 열 지도 계층을 추가하는 방법을 알아봅니다. 열 지도 계층을 사용자 지정하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 181cd714f85195157b767062cac99caadd32e817
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432759"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

지점 밀도 맵이라고도 하는 열 지도는 데이터 시각화의 한 형식입니다. 이는 색 범위를 사용하여 데이터의 밀도를 나타내고 지도에 데이터 “핫 스폿”을 표시하는 데 사용됩니다. 열 지도는 많은 수의 요소가 있는 데이터 세트를 렌더링하는 좋은 방법입니다.

수만 개의 요소를 기호로 렌더링하면 대부분의 지도 영역을 덮을 수 있습니다. 이 경우 많은 기호가 겹칠 수 있습니다. 그러면 데이터를 정확하게 이해하기가 어렵습니다. 그러나 열 지도와 동일한 데이터 세트를 시각화하면 각 데이터 요소의 밀도와 상대 밀도를 쉽게 확인할 수 있습니다.

다음을 비롯한 다양한 시나리오에서 열 지도를 사용할 수 있습니다.

- **온도 데이터**: 두 데이터 요소 사이 온도의 근사값을 제공합니다.
- **노이즈 센서 데이터**: 센서가 있는 곳의 노이즈 강도를 표시할 뿐만 아니라, 멀리 떨어진 곳에 대한 인사이트를 제공할 수도 있습니다. 한 사이트의 노이즈 수준이 높지 않을 수 있습니다. 여러 센서의 노이즈 검사 영역이 겹치는 경우 이 겹치는 영역이 더 높은 노이즈 수준을 경험할 수 있습니다. 이렇게 겹쳐진 영역이 열 지도에 표시됩니다.
- **GPS 추적**: 각 데이터 요소의 강도가 속도에 따라 다른 가중 높이 지도에서의 속도를 포함합니다. 예를 들어 이 기능은 차량의 속도를 확인할 수 있는 방법을 제공합니다.

> [!TIP]
> 기본적으로 열 지도 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 계층이 지점 기하 도형만 렌더링하도록 제한하려면 계층의 `filter` 속성을 `['==', ['geometry-type'], 'Point']`로 설정합니다. MultiPoint 기능도 포함하려면 계층의 `filter` 속성을 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`로 설정합니다.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

요소의 데이터 원본을 열 지도로 렌더링하려면 데이터 원본을 `HeatMapLayer` 클래스 인스턴스로 전달하여 맵에 추가합니다.

다음 코드에서 각 열 요소의 반경은 모든 확대/축소 수준에서 10픽셀입니다. 더 나은 사용자 환경을 보장하기 위해 열 지도는 레이블 계층 아래에 있습니다. 레이블이 명확하게 표시됩니다. 이 샘플의 데이터는 [USGS 지진 위험 프로그램](https://earthquake.usgs.gov/)에서 제공됩니다. 이는 지난 30일 동안 발생한 심각한 지진에 대한 것입니다.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

다음은 이전 코드의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

## <a name="customize-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 다음을 비롯하여 다양한 사용자 지정 옵션을 제공합니다.

* `radius`: 각 데이터 요소를 렌더링할 픽셀 반경을 정의합니다. 반경을 정수 또는 식으로 설정할 수 있습니다. 식을 사용하여 확대/축소 수준에 따라 반경을 조정하고 지도에서 일관적인 공간 영역을 나타낼 수 있습니다(예: 5마일 반경).
* `color`: 열 지도에 색이 지정되는 방식을 지정합니다. 색 그라데이션은 열 지도의 일반적인 기능입니다. `interpolate` 식을 사용하여 해당 효과를 구현할 수 있습니다. 열 지도에 색상을 적용하는 `step` 식을 사용하여 윤곽선 또는 레이더 스타일 지도와 유사한 범위로 밀도를 시각적으로 나눌 수도 있습니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다.

  열 지도에 대한 색 값을 `heatmap-density` 값에서 식으로 지정합니다. “보간” 식의 인덱스 0에 데이터가 정의되어 있지 않은 영역의 색 또는 “계단식” 식의 기본 색입니다. 배경색을 정의하는 데 이 값을 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

  색 식의 예는 다음과 같습니다.

  | 보간 색 식 | 계단식 색 식 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] |

- `opacity`: 열 지도 계층의 불투명도 또는 투명도를 지정합니다.
- `intensity`: 각 데이터 요소의 가중치에 승수를 적용하여 열 지도의 전체 강도를 높입니다. 이로 인해 데이터 요소의 가중치가 달라지므로 쉽게 시각화할 수 있습니다.
- `weight`: 기본적으로 모든 데이터 요소의 가중치는 1이므로, 가중치는 동일합니다. 가중치 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자를 가중치로 설정하는 경우 지도에 각 데이터 요소를 두 번씩 배치하는 것과 마찬가지입니다. 예를 들어 가중치가 2인 경우 밀도는 2배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다.

  그러나 식을 사용하는 경우 각 데이터 요소의 가중치는 각 데이터 요소의 속성에 따라 다를 수 있습니다. 예를 들어 각 데이터 요소가 지진을 나타내는 경우 진도 값은 각 지진 데이터 요소에서 중요한 메트릭입니다. 지진은 항상 발생하지만, 대부분 진도가 낮으며 알아차리지 못합니다. 식에서 진도 값을 사용하여 각 데이터 요소에 가중치를 할당합니다. 진도 값을 사용하여 가중치를 할당하면 열 지도 내에서 지진의 유의성을 보다 잘 표현할 수 있습니다.
- `source` 및 `source-layer`: 데이터 원본을 업데이트할 수 있습니다.

다른 열 지도 계층 옵션을 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>일관된 확대/축소가 가능한 열 지도

기본적으로 열 지도 계층에 렌더링되는 데이터 요소의 반경에는 모든 확대/축소 수준에 대한 고정 픽셀 반경이 있습니다. 지도를 확대/축소하면 데이터가 함께 집계되고 열 지도 계층이 다르게 보입니다.

각 데이터 요소가 지도의 동일한 실제 영역을 포함하도록 각 확대/축소 수준에 맞게 반경을 조정하는 데 `zoom` 식을 사용할 수 있습니다. 이 식은 열 지도 계층을 더 정적이고 일관되게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 비교했을 때 가로 및 세로로 두 배의 픽셀이 있습니다.

확대/축소 수준마다 두 배가 되도록 반경을 조정하면 모든 확대/축소 수준에서 일관되게 보이는 열 지도가 생성됩니다. 이와 같이 반경을 조정하려면 다음 샘플과 같이 픽셀 반경이 최소 확대/축소 수준으로 설정되고 조정된 반경이 최대 확대/축소 수준으로 설정되어 `2 * Math.pow(2, minZoom - maxZoom)`로 계산된 기본 2 `exponential interpolation` 식에 `zoom`을 사용합니다. 지도를 확대/축소하여 확대/축소 수준으로 열 지도가 조정되는 방식을 확인합니다.

<br/>

<iframe height="500" scrolling="no" title="일관된 확대/축소가 가능한 열 지도" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>일관된 확대/축소가 가능한 열 지도</a>를 참조하세요.
</iframe>

`zoom` 식은 `step` 및 `interpolate` 식에서만 사용할 수 있습니다. 다음 식을 사용하여 반경을 미터 단위로 근사할 수 있습니다. 이 식은 원하는 반경으로 바꿔야 하는 자리 표시자 `radiusMeters`를 사용합니다. 이 식은 확대/축소 수준 0 및 24의 적도에서 확대/축소 수준에 대한 대략적인 픽셀 반경을 계산하고, 지도의 타일링 시스템이 작동하는 것과 동일한 방식으로 이러한 값 사이의 배율을 조정하기 위해 `exponential interpolation` 식을 사용합니다.

```json
[
    `'interpolate', 
    ['exponential', 2],
    ['zoom'],
    0, ['*', radiusMeters, 0.000012776039596366526],
    24, [`'*', radiusMeters, 214.34637593279402]
]
```

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 인접한 요소는 클러스터된 요소로 그룹화됩니다. 각 클러스터의 요소 개수를 열 지도의 가중치 식으로 사용할 수 있습니다. 이렇게 하면 렌더링되는 요소의 수를 크게 줄일 수 있습니다. 한 클러스터의 요소 수는 요소 기능의 `point_count` 속성에 저장됩니다.
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 클러스터링 반경이 단 몇 개의 픽셀로 이루어진 경우에는 렌더링에 시각적 차이가 작습니다. 반경 그룹이 커지면 각 클러스터의 요소가 많아지며 열 지도의 성능이 향상됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [HeatMapLayer](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
