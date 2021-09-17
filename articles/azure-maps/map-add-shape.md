---
title: 맵에 다각형 계층 추가 | Microsoft Azure Maps
description: 맵에 다각형 또는 원을 추가하는 방법을 알아봅니다. Azure Maps Web SDK를 사용하여 기하학적 모양을 사용자 지정하고 쉽게 업데이트 및 유지 관리하는 방법을 확인하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 747a728edf68a1bc23982e3d3a4ffd7fa4496d56
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439653"
---
# <a name="add-a-polygon-layer-to-the-map"></a>맵에 다각형 계층 추가

이 문서에서는 다각형 계층을 사용하여 맵에 `Polygon` 및 `MultiPolygon` 기능 기하 도형의 영역을 렌더링하는 방법을 보여 줍니다. Azure Maps Web SDK는 [확장된 GeoJSON 스키마](extend-geojson.md#circle)에 정의된 원 기하 도형 만들기도 지원합니다. 이러한 원은 맵에서 렌더링될 때 다각형으로 변환됩니다. 모든 기능 기하 도형은 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) 클래스로 래핑된 경우 쉽게 업데이트할 수 있습니다.

## <a name="use-a-polygon-layer"></a>다각형 계층 사용 

다각형 계층이 데이터 원본에 연결되고 맵에 로드되면 `Polygon` 및 `MultiPolygon` 기능을 사용하여 영역을 렌더링합니다. 다각형을 만들려면 데이터 원본에 다각형을 추가하고 [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) 클래스를 사용하여 다각형 계층으로 렌더링합니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

다음은 위 코드를 실행하는 전체 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 계층을 함께 사용

선 계층은 다각형의 윤곽선을 렌더링하는 데 사용됩니다. 다음 코드 샘플에서는 이전 예와 같이 다각형을 렌더링하지만, 이제는 선 계층을 추가합니다. 이 선 계층은 데이터 원본에 연결된 두 번째 계층입니다.  

<br/>

<iframe height='500' scrolling='no' title='다각형을 추가하기 위한 다각형 및 선 계층' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>다각형을 추가하기 위한 다각형 및 선 계층</a>을 참조하세요.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>패턴을 사용하여 다각형 채우기

색을 사용한 다각형 채우기 외에도 이미지 패턴을 사용하여 다각형을 채울 수 있습니다. 맵 이미지 스프라이트 리소스에 이미지 패턴을 로드한 다음 다각형 계층의 `fillPattern` 속성을 사용하여 이 이미지를 참조합니다.

<br/>

<iframe height="500" scrolling="no" title="다각형 채우기 패턴" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>다각형 채우기 패턴</a>을 참조하세요.
</iframe>


> [!TIP]
> Azure Maps Web SDK는 채우기 패턴으로 사용할 수 있는 몇 가지 사용자 지정 가능한 이미지 템플릿을 제공합니다. 자세한 내용은 [이미지 템플릿을 사용하는 방법](how-to-use-image-templates-web-sdk.md) 문서를 참조하세요.

## <a name="customize-a-polygon-layer"></a>다각형 계층 사용자 지정

다각형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>를 참조하세요.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>맵에 원 추가

Azure Maps는 [여기](extend-geojson.md#circle) 설명된 대로 원에 대한 정의를 제공하는 GeoJSON 스키마의 확장된 버전을 사용합니다. 원은 `Point` 기능을 만들어 맵에 렌더링됩니다. 이 `Point`에는 값이 `"Circle"`인 `subType` 속성과 반지름을 나타내는 숫자(미터)가 포함된 `radius` 속성이 있습니다. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps Web SDK는 이러한 `Point` 기능을 `Polygon` 기능으로 변환합니다. 그런 다음, 이러한 기능은 다음 코드 샘플에 표시된 대로 다각형 및 선 계층을 사용하여 맵에서 렌더링됩니다.

<br/>

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>업데이트하기 쉬운 기하 도형 만들기

`Shape` 클래스는 [기하 도형](/javascript/api/azure-maps-control/atlas.data.geometry) 또는 [기능](/javascript/api/azure-maps-control/atlas.data.feature)을 래핑하여 이러한 기능을 쉽게 업데이트하고 유지 관리할 수 있도록 합니다. 도형 변수를 인스턴스화하려면 기하 도형 또는 속성 집합을 도형 생성자에 전달합니다.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

다음 코드 샘플에서는 원 GeoJSON 개체를 도형 클래스로 래핑하는 방법을 보여 줍니다. 도형의 반지름 값이 변경되면 원이 맵에서 자동으로 렌더링됩니다.

<br/>

<iframe height='500' scrolling='no' title='도형 속성 업데이트' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>도형 속성 업데이트</a>를 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

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

추가 리소스:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 사양 확장](extend-geojson.md#circle)