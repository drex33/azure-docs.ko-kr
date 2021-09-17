---
title: 지도에 타일 계층 추가 | Microsoft Azure Maps
description: 지도에 이미지를 중첩하는 방법을 알아봅니다. Azure Maps 웹 SDK를 사용하여 날씨 방사형 오버레이가 포함된 타일 계층을 지도에 추가하는 예제를 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9b645ccc5347e55f46666563dc9bab565b8ba631
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439599"
---
# <a name="add-a-tile-layer-to-a-map"></a>맵에 타일 레이어 추가

이 문서에는 지도에서 타일 계층을 오버레이하는 방법이 나와 있습니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. Azure Maps 바둑판식 배열 시스템에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조하세요.

타일 계층이 서버에서 타일에 로드됩니다. 이러한 이미지는 사전 렌더링되거나 동적으로 렌더링될 수 있습니다. 미리 렌더링된 이미지는 타일 레이어에서 인식하는 명명 규칙을 사용하여 서버의 다른 이미지와 마찬가지로 저장됩니다. 동적으로 렌더링된 이미지는 서비스를 사용하여 실시간에 가깝게 이미지를 로드합니다. Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 클래스에서 지원되는 세 가지 다른 타일 서비스 명명 규칙이 있습니다.

* X, Y, 확대/축소 표기법 - X는 열이고, Y는 타일 그리드 내 타일의 행 위치이며, 확대/축소 표기법은 확대/축소 수준을 기반으로 한 값입니다.
* Quadkey 표기법 - x, y 및 확대/축소 정보를 단일 문자열 값으로 결합합니다. 이 문자열 값은 단일 타일에 대한 고유 식별자가 됩니다.
* 경계 상자 - 경계 상자 좌표 형식(`{west},{south},{east},{north}`)으로 이미지를 지정합니다. 이 형식은 일반적으로 [WMS(웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에서 사용됩니다.

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)는 맵에서 큰 데이터 세트를 시각화하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 수 있을 뿐만 아니라, 타일 계층으로 벡터 데이터도 렌더링할 수 있습니다. 맵 컨트롤은 타일 계층으로 벡터 데이터를 렌더링하여 맵 컨트롤이 표현하는 벡터 데이터보다 파일 크기가 더 작은 타일만 로드해야 합니다. 이 기술은 일반적으로 맵에서 수백만 개의 데이터 행을 렌더링하는 데 사용됩니다.

타일 계층으로 전달된 타일 URL은 다음 매개 변수를 사용하는 타일 URL 템플릿이거나 TileJSON 리소스에 대한 http 또는 https URL이어야 합니다.

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}` - 하위 도메인 값의 자리 표시자(지정된 경우) `subdomain`이(가) 추가됩니다.
* `{azMapsDomain}` - 맵에서 사용되는 것과 동일한 값을 사용하여 타일 요청의 인증 및 도메인을 정렬하는 자리 표시자입니다.

## <a name="add-a-tile-layer"></a>타일 레이어 추가

 이 샘플은 타일 세트를 가리키는 타일 계층을 만드는 방법을 보여줍니다. 이 샘플에서는 x, y, 확대/축소 바둑판식 배열 시스템을 사용합니다. 이 타일 계층의 원본은 [OpenSeaMap 프로젝트](https://openseamap.org/index.php)로, 여기에는 크라우드소싱 항해 차트가 포함되어 있습니다. 방사형 데이터를 볼 때 이상적으로 사용자는 지도를 탐색할 때 도시의 레이블을 명확하게 볼 수 있습니다. 이 동작은 `labels` 계층 아래에 타일 계층을 삽입하여 구현할 수 있습니다.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='X, Y 및 Z를 사용하는 타일 계층' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>X, Y 및 Z를 사용하는 펜 타일 계층</a>을 참조하세요.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC WMS(웹 매핑 서비스) 추가

WMTS(웹 매핑 서비스)는 지도 데이터의 이미지를 제공하는 OGC(Open Geospatial Consortium) 표준입니다. Azure Maps에서 사용 가능한 이 형식으로 사용할 수 있는 개방형 데이터 세트가 많습니다. 서비스가 `EPSG:3857` CRS(좌표 참조 시스템)를 지원하는 경우 이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMS 서비스를 사용할 때 너비/높이 매개 변수를 서비스에서 지원하는 것과 동일한 값으로 설정하고, 이 동일한 값을 `tileSize` 옵션에 설정해야 합니다. 형식이 지정된 URL에서 `BBOX` 서비스 매개 변수를 `{bbox-epsg-3857}` 자리 표시자로 설정합니다.

다음 스크린샷은 지도 위, 레이블 아래에 있는 [USGS(미국 지질 조사)](https://mrdata.usgs.gov/)의 지질 데이터 웹 매핑 서비스를 오버레이하는 위의 코드를 보여 줍니다.

<br/>

<iframe height="500" scrolling="no" title="WMS 타일 계층" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/BapjZqr'>WMS 타일 계층</a>을 참조하세요.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC WMTS(웹 매핑 타일 서비스) 추가

WMTS(웹 매핑 타일 서비스)는 지도에 타일 기반 오버레이를 제공하기 위한 OGC(Open Geospatial Consortium) 표준입니다. Azure Maps에서 사용 가능한 이 형식으로 사용할 수 있는 개방형 데이터 세트가 많습니다. 서비스가 `EPSG:3857` 또는 `GoogleMapsCompatible` CRS(좌표 참조 시스템)를 지원하는 경우 이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMTS 서비스를 사용할 때 너비/높이 매개 변수를 서비스에서 지원하는 것과 동일한 값으로 설정하고, 이 동일한 값을 `tileSize` 옵션에 설정해야 합니다. 형식이 지정된 URL에서 다음 자리 표시자를 적절하게 바꿉니다.

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

다음 스크린샷은 지도 위, 도로와 레이블 아래에 있는 [미국 지질 조사(USGS) 국가 지도](https://viewer.nationalmap.gov/services/)의 이미지 웹 매핑 타일 서비스를 오버레이하는 위의 코드를 보여 줍니다.

<br/>

<iframe height="500" scrolling="no" title="WMTS 타일 계층" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/BapjZVY'>WMTS 타일 계층</a>을 참조하세요.
</iframe>

## <a name="customize-a-tile-layer"></a>타일 계층 사용자 지정

타일 계층 클래스에는 다양한 스타일링 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='타일 계층 옵션' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>타일 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [이미지 레이어 추가](./map-add-image-layer.md)
