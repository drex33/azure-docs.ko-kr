---
title: Microsoft Azure Maps에서 지도에 대한 데이터 원본 만들기
description: 지도에 대한 데이터 원본을 만드는 방법을 알아봅니다. Azure Maps Web SDK에서 사용하는 데이터 원본 GeoJSON 원본 및 벡터 타일에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: bb5050d1305f88987ed7ac38a8217e6b4f9c724e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425773"
---
# <a name="create-a-data-source"></a>데이터 소스 만들기

Azure Maps Web SDK는 데이터 원본에 데이터를 저장합니다. 데이터 원본을 사용하면 쿼리 및 렌더링을 위해 데이터 작업이 최적화됩니다. 현재 다음 두 가지 유형의 데이터 원본이 지원됩니다.

* **GeoJSON 원본**: GeoJSON 형식의 원시 위치 데이터를 로컬로 관리합니다. 중소 규모의 데이터 세트에 적합합니다(수십만 개 이상의 형태).
* **벡터 타일 원본**: 지도 바둑판식 배열 시스템을 기반으로 현재 지도 보기의 벡터 타일 형식의 데이터를 로드합니다. 대규모 데이터 세트에 이상적입니다(수백만 또는 수십억 개의 형태).

## <a name="geojson-data-source"></a>GeoJSON 데이터 원본

GeoJSON 기반 데이터 원본은 `DataSource` 클래스를 사용하여 로컬로 데이터를 로드하고 저장합니다. GeoJSON 데이터는 수동으로 만들거나 [atlas.data](/javascript/api/azure-maps-control/atlas.data) 네임스페이스의 도우미 클래스를 사용하여 만들 수 있습니다. `DataSource` 클래스는 로컬 또는 원격 GeoJSON 파일을 가져오는 함수를 제공합니다. 원격 GeoJSON 파일은 COR 지원 엔드포인트에서 호스트해야 합니다. `DataSource` 클래스는 지점 데이터를 클러스터링하는 기능을 제공합니다. 또한 `DataSource` 클래스를 통해 데이터를 쉽게 추가, 제거 및 업데이트할 수 있습니다. 다음 코드는 Azure Maps에서 GeoJSON 데이터를 만드는 방법을 보여 줍니다.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

데이터 원본이 만들어지면 [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager)인 `map.sources` 속성을 통해 지도에 추가할 수 있습니다. 다음 코드는 `DataSource`를 만들어 지도에 추가하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

다음 코드는 GeoJSON 데이터를 `DataSource`에 추가할 수 있는 다양한 방법을 보여 줍니다.

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> `DataSource`의 모든 데이터를 덮어쓰려고 하는 경우라고 가정해 보겠습니다. `clear` 함수를 호출한 후 `add` 함수를 호출하면 지도가 두 번 다시 렌더링되어 약간의 지연이 발생할 수 있습니다. 대신 `setShapes` 함수를 사용합니다. 이 함수는 데이터 원본의 모든 데이터를 제거 및 바꾸고 지도의 단일 다시 렌더링만 트리거합니다.

## <a name="vector-tile-source"></a>벡터 타일 원본

벡터 타일 원본은 벡터 타일 레이어에 액세스하는 방법을 설명합니다. [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) 클래스를 사용하여 벡터 타일 원본을 인스턴스화합니다. 벡터 타일 레이어는 타일 레이어와 비슷하지만 동일하지는 않습니다. 타일 레이어는 래스터 이미지입니다. 벡터 타일 레이어는 **PBF** 형식의 압축 파일입니다. 이 압축 파일은 벡터 지도 데이터 및 하나 이상의 레이어를 포함합니다. 각 레이어의 스타일에 따라 클라이언트에서 파일을 렌더링하고 스타일을 지정할 수 있습니다. 벡터 타일의 데이터에는 점, 선 및 다각형 형식의 지리적 피처가 포함되어 있습니다. 래스터 타일 레이어 대신 벡터 타일 레이어를 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

* 벡터 타일의 파일 크기는 일반적으로 해당하는 래스터 타일보다 훨씬 작습니다. 따라서 더 적은 대역폭이 사용됩니다. 이것은 낮은 대기 시간, 더 빠른 지도 및 더 나은 사용자 환경을 의미합니다.
* 벡터 타일이 클라이언트에서 렌더링된 후에는 표시되는 디바이스의 해상도에 맞게 조정됩니다. 따라서 렌더링된 지도는 명확한 레이블을 포함하여 잘 정의된 것으로 나타납니다.
* 클라이언트에 새 스타일을 적용할 수 있으므로 벡터 지도에서 데이터의 스타일을 변경하려면 데이터를 다시 다운로드할 필요가 없습니다. 반면, 래스터 타일 레이어의 스타일을 변경하려면 일반적으로 서버에서 타일을 로드한 다음, 새 스타일을 적용해야 합니다.
* 데이터가 벡터 형식으로 전달되므로 데이터를 준비하는 데 필요한 서버 쪽 처리가 줄어듭니다. 따라서 최신 데이터를 더 빨리 사용할 수 있습니다.

Azure Maps는 [Mapbox Vector 타일 사양](https://github.com/mapbox/vector-tile-spec)(개방형 표준)을 준수합니다. Azure Maps는 플랫폼의 일부로 다음 벡터 타일 서비스를 제공합니다.

- 도로 타일 [설명서](/rest/api/maps/render-v2/get-map-tile) | [데이터 형식 세부 정보](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 트래픽 인시던트 [설명서](/rest/api/maps/traffic/gettrafficincidenttile) | [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 트래픽 흐름 [설명서](/rest/api/maps/traffic/gettrafficflowtile) | [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator를 사용하면 [Render V2-Get Map Tile API](/rest/api/maps/render-v2/get-map-tile)를 통해 사용자 지정 벡터 타일을 만들고 액세스할 수도 있습니다.

> [!TIP]
> Web SDK를 사용하여 Azure Maps 렌더링 서비스에서 벡터 또는 래스터 이미지 타일을 사용하는 경우 `atlas.microsoft.com`을 자리 표시자 `{azMapsDomain}`르로 바꿀 수 있습니다. 이 자리 표시자는 지도에서 사용하는 동일한 도메인으로 대체되고 동일한 인증 세부 정보도 자동으로 추가됩니다. 이를 통해 Azure Active Directory 인증을 사용할 때 렌더링 서비스에 대한 인증이 크게 간소화됩니다.

지도에 벡터 타일 원본의 데이터를 표시하려면 원본을 데이터 렌더링 레이어 중 하나에 연결합니다. 벡터 원본을 사용하는 모든 레이어는 옵션에서 `sourceLayer` 값을 지정해야 합니다. 다음 코드는 Azure Maps 트래픽 흐름 벡터 타일 서비스를 벡터 타일 원본으로 로드한 다음, 선 레이어를 사용하여 지도에 표시합니다. 이 벡터 타일 원본에는 "트래픽 흐름"이라는 원본 레이어에 단일 데이터 세트가 있습니다. 이 데이터 세트의 선 데이터에는 이 코드에서 선 색을 선택하고 스케일링하는 데 사용되는 `traffic_level`이라는 속성이 있습니다.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" scrolling="no" title="벡터 타일 선 레이어" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>벡터 타일 선 레이어</a>를 참조하세요.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>레이어에 데이터 원본 연결

데이터는 렌더링 레이어를 사용하여 지도에 렌더링됩니다. 단일 데이터 원본이 하나 이상의 렌더링 레이어에서 참조될 수 있습니다. 다음 렌더링 레이어에는 데이터 원본이 필요합니다.

* [거품형 레이어](map-add-bubble-layer.md) - 지도에서 점 데이터를 스케일링된 원으로 렌더링합니다.
* [기호 레이어](map-add-pin.md) - 점 데이터를 아이콘 또는 텍스트로 렌더링합니다.
* [열 지도 레이어](map-add-heat-map-layer.md) - 점 데이터를 고밀도 열 지도로 렌더링합니다.
* [선 레이어](map-add-shape.md) - 선을 렌더링하거나 다각형의 윤곽선을 렌더링합니다. 
* [다각형 레이어](map-add-shape.md) - 단색 또는 이미지 패턴으로 다각형 영역을 채웁니다.

다음 코드에서는 데이터 원본을 만들고 지도에 추가한 다음, 거품형 레이어에 연결하는 방법을 보여 줍니다. 그런 다음, 원격 위치에서 데이터 원본으로 GeoJSON 점 데이터를 가져옵니다. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

이러한 데이터 원본에 연결되지 않지만 렌더링을 위해 데이터를 직접 로드하는 추가 렌더링 레이어가 있습니다. 

* [이미지 레이어](map-add-image-layer.md) - 지도 위에 단일 이미지를 오버레이하고 해당 모서리를 지정된 좌표 세트에 바인딩합니다.
* [타일 레이어](map-add-tile-layer.md) - 지도 위에 래스터 타일 레이어를 겹쳐 놓습니다.

## <a name="one-data-source-with-multiple-layers"></a>여러 레이어가 있는 하나의 데이터 원본

여러 레이어를 단일 데이터 원본에 연결할 수 있습니다. 이 옵션이 유용할 수 있는 여러 가지 시나리오가 있습니다. 예를 들어, 사용자가 다각형을 그리는 시나리오를 생각해 보겠습니다. 사용자가 지도에 점이 추가할 때 다각형 영역을 렌더링하고 채워야 합니다. 스타일이 지정된 선을 추가하여 다각형의 윤곽을 만들면 사용자가 그릴 때 다각형의 가장자리를 더 쉽게 볼 수 있습니다. 다각형의 개별 위치를 편리하게 편집하기 위해 각 위치 위에 핀이나 표식 같은 핸들을 추가할 수 있습니다.

![단일 데이터 원본에서 데이터를 렌더링하는 여러 레이어를 보여 주는 지도](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

대부분의 매핑 플랫폼에서 다각형 개체, 선 개체 및 다각형의 각 위치에 대한 핀이 필요합니다. 다각형이 수정될 때 선과 핀을 수동으로 업데이트해야 하며, 이 작업은 빠르게 복잡해질 수 있습니다.

Azure Maps를 사용하는 경우 아래 코드와 같이 데이터 원본에 단일 다각형이 필요합니다.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> `map.layers.add` 함수를 사용하여 지도에 레이어를 추가하는 경우 기존 레이어의 ID 또는 인스턴스를 두 번째 매개 변수로 전달할 수 있습니다. 그러면 지도에 기존 레이어 아래에 새 레이어를 삽입하도록 지시됩니다. 레이어 ID를 전달하는 것 외에도 이 방법은 다음 값을 지원합니다.
>
> * `"labels"` - 지도 레이블 레이어 아래에 새 레이어를 삽입합니다.
> * `"transit"` - 지도 도로 및 교통 레이어 아래에 새 레이어를 삽입합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [코드 샘플](/samples/browse/?products=azure-maps)