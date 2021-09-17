---
title: 맵에 기호 계층 추가 | Microsoft Azure Maps
description: 텍스트나 아이콘 같이 사용자 지정된 기호를 맵에 추가하는 방법을 알아봅니다. 이 목적을 위해 Azure Maps Web SDK에서 데이터 원본 및 기호 계층을 사용하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8c92fcceaf6cb95971f0c22d516731a18f731cd9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435045"
---
# <a name="add-a-symbol-layer-to-a-map"></a>맵에 기호 계층 추가

기호를 데이터 원본에 연결한 후 이를 사용해 지정된 점에서 아이콘 또는 텍스트를 렌더링합니다. 

기호 계층은 WebGL을 사용하여 렌더링됩니다. 기호 계층을 사용하여 맵에 대용량 점 컬렉션을 렌더링합니다. HTML 표식에 비해 기호 계층은 더 나은 성능으로 맵에 많은 수의 점 데이터를 렌더링합니다. 그러나 기호 계층은 스타일에 대한 기존 CSS/HTML 요소를 지원하지 않습니다.  

> [!TIP]
> 기본적으로 기호 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기하 도형만 렌더링하도록 레이어를 제한하려면 레이어의 `filter` 속성을 `['==', ['geometry-type'], 'Point']` 또는 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`로 설정하거나, 원하는 경우 MultiPoint 기능을 포함하면 됩니다.

맵 이미지 스프라이트 관리자는 기호 계층에서 사용하는 사용자 지정 이미지를 로드합니다. 지원되는 이미지 형식은 다음과 같습니다.

- JPEG
- PNG
- SVG
- BMP
- GIF(애니메이션 없음)

## <a name="add-a-symbol-layer"></a>기호 레이어 추가

맵에 기호 계층을 추가하려면 몇 가지 단계를 수행해야 합니다. 먼저 데이터 원본을 만들어 맵에 추가합니다. 기호 계층을 만듭니다. 그런 다음, 데이터 원본을 기호 계층에 전달하여 데이터 원본에서 데이터를 검색합니다. 마지막으로, 렌더링할 수 있도록 데이터를 데이터 원본에 추가합니다. 

아래의 코드는 로드된 후 맵에 추가해야 하는 내용을 보여 줍니다. 이 샘플에서는 기호 계층을 사용하여 맵에 단일 점을 렌더링합니다. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

맵에 추가할 수 있는 점 데이터 유형은 다음의 네 가지가 있습니다.

- GeoJSON 점 기하 도형 - 이 개체는 점의 좌표만 포함하고, 그 외에는 아무것도 포함하지 않습니다. `atlas.data.Point` 도우미 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON MultiPoint 기하 도형 - 이 개체는 여러 점의 좌표를 포함하고, 그 외에는 아무것도 포함하지 않습니다. `atlas.data.MultiPoint` 도우미 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON 기능 - 이 개체는 모든 GeoJSON 기하 도형 및 기하 도형에 연결된 메타데이터를 포함하는 속성 집합으로 구성됩니다. `atlas.data.Feature` 도우미 클래스를 사용하여 이러한 개체를 쉽게 만들 수 있습니다.
- `atlas.Shape` 클래스는 GeoJSON 기능과 비슷합니다. 둘 다 GeoJSON 기하 도형 및 기하 도형에 연결된 메타데이터를 포함하는 속성 집합으로 구성됩니다. GeoJSON 개체가 데이터 원본에 추가되는 경우, 계층에서 쉽게 렌더링할 수 있습니다. 그러나 해당 GeoJSON 개체의 좌표 속성이 업데이트되는 경우, 데이터 원본과 맵이 변경되지 않습니다. JSON 개체에 업데이트를 트리거하는 메커니즘이 없기 때문입니다. 도형 클래스는 포함된 데이터를 업데이트하는 함수를 제공합니다. 변경 내용이 적용되면 데이터 원본과 맵이 자동으로 알림을 받고 업데이트됩니다. 

다음 코드 샘플에서는 GeoJSON 점 기하 도형을 만들어 `atlas.Shape` 클래스에 전달함으로써 쉽게 업데이트할 수 있도록 합니다. 맵의 중심은 처음에 기호를 렌더링하는 데 사용됩니다. 클릭 이벤트가 맵에 추가됩니다. 이렇게 하면 마우스 좌표가 도형 `setCoordinates` 함수에서 사용됩니다. 마우스 좌표는 클릭 이벤트가 발생했을 때 기록됩니다. 그러면 `setCoordinates`에서 맵의 기호 위치를 업데이트합니다.

<br/>

<iframe height='500' scrolling='no' title='핀 고정 위치 전환' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>핀 고정 위치 전환</a>을 참조하세요.
</iframe>

> [!TIP]
> 기본적으로 기호 계층은 겹치는 기호를 숨겨서 기호 렌더링을 최적화합니다. 확대하면 숨겨진 기호가 표시됩니다. 이 기능을 사용하지 않도록 설정하고 모든 기호를 항상 렌더링하려면, `iconOptions` 옵션의 `allowOverlap` 속성을 `true`로 설정합니다.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>기호 계층에 사용자 지정 아이콘 추가

기호 계층은 WebGL을 사용하여 렌더링됩니다. 아이콘 이미지와 같은 이러한 모든 리소스는 WebGL 컨텍스트에 로드해야 하기 때문입니다. 이 샘플에서는 맵 리소스에 사용자 지정 아이콘을 추가하는 방법을 보여 줍니다. 이 아이콘은 맵에서 사용자 지정 기호를 사용하여 점 데이터를 렌더링하는 데 사용됩니다. 기호 계층의 `textField` 속성에는 식을 지정해야 합니다. 이 경우, 온도 속성을 렌더링하려고 합니다. 온도는 숫자이므로 문자열로 변환해야 합니다. 또한 "° F"를 추가하고자 합니다. 식을 사용하여 이 연결을 수행할 수 있습니다(`['concat', ['to-string', ['get', 'temperature']], '°F']`). 

<br/>

<iframe height='500' scrolling='no' title='사용자 지정 기호 이미지 아이콘' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>사용자 지정 기호 이미지 아이콘</a>을 참조하세요.
</iframe>

> [!TIP]
> Azure Maps Web SDK는 기호 계층으로 사용할 수 있는 몇 가지 사용자 지정 가능한 이미지 템플릿을 제공합니다. 자세한 내용은 [이미지 템플릿을 사용하는 방법](how-to-use-image-templates-web-sdk.md) 문서를 참조하세요.

## <a name="customize-a-symbol-layer"></a>기호 계층 사용자 지정 

기호 계층에는 사용할 수 있는 많은 스타일 지정 옵션이 있습니다. 이러한 다양한 스타일 지정 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='기호 계층 옵션' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/PxVXje/'>기호 계층 옵션</a>을 참조하세요.
</iframe>

> [!TIP]
> 기호 계층이 있는 텍스트만 렌더링하려는 경우 아이콘 옵션의 `image` 속성을 `'none'`으로 선택하여 아이콘을 숨길 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [이미지 템플릿을 사용하는 방법](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)