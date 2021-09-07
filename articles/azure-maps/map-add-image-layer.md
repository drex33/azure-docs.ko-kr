---
title: 지도에 이미지 계층 추가 | Microsoft Azure Maps
description: 지도에 이미지를 추가하는 방법을 알아봅니다. Azure Maps 웹 SDK를 사용하여 이미지 계층을 사용자 지정하고 고정된 좌표 집합에 이미지를 오버레이하는 방법을 참조하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 67a564b813014ffc25049f0694ef05977d65efe6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463056"
---
# <a name="add-an-image-layer-to-a-map"></a>맵에 이미지 계층 추가

이 문서에서는 이미지를 좌표의 고정 세트에 오버레이하는 방법을 보여 줍니다. 지도에 오버레이될 수 있는 다양한 이미지 유형의 몇 가지 예제는 다음과 같습니다.

* 드론에서 캡처한 이미지
* 빌딩 평면도
* 역사적 또는 기타 특수한 지도 이미지
* 작업 사이트의 청사진입니다.
* 날씨 레이더 이미지입니다.

> [!TIP]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)는 지도에 이미지를 오버레이할 수 있는 쉬운 방법입니다. 브라우저에서 대형 이미지를 로드하는 데 어려움이 있을 수 있습니다. 이 경우에는 이미지를 타일로 나눈 후 [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)로 지도에 로드해 보세요.

이미지 계층은 다음과 같은 이미지 형식을 지원합니다.

- JPEG
- PNG
- BMP
- GIF(애니메이션 없음)

## <a name="add-an-image-layer"></a>이미지 레이어 추가

다음 코드는 1922년 뉴저지, 뉴어크 지도 이미지를 지도에 오버레이합니다. `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`에서 4개의 모서리의 이미지 및 좌표에 대한 URL을 전달하면 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)가 만들어집니다.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

다음은 이전 코드의 전체 실행 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='단순 이미지 계층' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Pen Simple 이미지 계층</a>을 참조하세요.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML 파일을 지면 오버레이로 가져오기

이 샘플은 KML 지면 오버레이 정보를 지도에서 이미지 계층으로 추가하는 방법을 보여 줍니다. KML 지면 오버레이는 북쪽, 남쪽, 동쪽 및 서쪽 좌표와 시계 반대 방향 회전을 제공합니다. 그러나 이미지 계층에는 이미지의 각 구석에 대한 좌표가 필요합니다. 이 샘플의 KML 지면 오버레이는 샤르트르 대성당에 대한 것이며 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)에서 제공된 것입니다.

이 코드는 [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) 클래스의 정적 `getCoordinatesFromEdges` 함수를 사용합니다. 이 메서드는 KML 지면 오버레이의 북쪽, 남쪽, 동쪽, 서쪽 및 회전 정보를 사용하여 이미지의 네 모서리를 계산합니다.

<br/>

<iframe height='500' scrolling='no' title='이미지 계층으로 KML 지면 오버레이' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>이미지 계층으로 KML 지면 오버레이</a>를 참조하세요.
</iframe>

> [!TIP]
> 이미지 계층 클래스의 `getPixels` 및 `getPositions` 함수를 사용하여 배치된 이미지 계층의 지리적 좌표와 로컬 이미지 픽셀 좌표 간을 변환합니다.

## <a name="customize-an-image-layer"></a>이미지 계층 사용자 지정

이미지 계층에 여러 스타일 지정 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='이미지 계층 옵션' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>이미지 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [타일 레이어 추가](./map-add-tile-layer.md)
