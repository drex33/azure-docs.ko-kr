---
title: 맵에 컨트롤 추가 | Microsoft Azure Maps
description: Microsoft Azure Maps에서 맵에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: e2029135497efb08bfa2c0d435690c112dea17ed
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438321"
---
# <a name="add-controls-to-a-map"></a>맵에 컨트롤 추가

이 문서에서는 맵에 컨트롤을 추가하는 방법을 보여 줍니다. 또한 모든 컨트롤 및 [스타일 선택기](./choose-map-style.md)를 사용하여 맵을 만드는 방법도 알아봅니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 맵을 확대/축소하는 단추를 추가합니다. 다음 코드 샘플은 [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 클래스의 인스턴스를 만들고 맵의 오른쪽 아래 모퉁이에 추가합니다.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='확대/축소 컨트롤 추가' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>확대/축소 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 피치를 기울여 수평선을 기준으로 매핑하는 단추를 추가합니다. 다음 코드 샘플은 [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 클래스의 인스턴스를 만듭니다. 맵의 오른쪽 위 모퉁이에 PitchControl을 추가합니다.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='피치 컨트롤 추가' src='//codepen.io/azuremaps/embed/xJrwaP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>피치 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-compass-control"></a>나침반 컨트롤 추가

나침반 컨트롤은 맵을 회전하는 단추를 추가합니다. 다음 코드 샘플은 [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 클래스의 인스턴스를 만들고 맵의 왼쪽 아래 모퉁이에 추가합니다.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='회전 컨트롤 추가' src='//codepen.io/azuremaps/embed/GBEoRb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>회전 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 배치하고 한 번에 모두 맵에 추가하고 맵의 동일한 영역에 배치하여 개발을 간소화할 수 있습니다. 다음은 이 방법을 사용하여 맵에 표준 탐색 컨트롤을 추가합니다.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

다음 코드 샘플은 맵의 오른쪽 위 모퉁이에 확대/축소, 나침반, 피치 및 스타일 선택기 컨트롤을 추가합니다. 자동으로 스택하는 방법을 확인합니다. 스크립트의 컨트롤 개체 순서에 따라 지도에서 해당 개체가 표시되는 순서가 결정됩니다. 맵의 컨트롤 순서를 변경하는 경우 배열에서 컨트롤의 순서를 변경하면 됩니다.

<br/>

<iframe height='500' scrolling='no' title='모든 컨트롤이 있는 지도' src='//codepen.io/azuremaps/embed/qyjbOM/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>모든 컨트롤이 있는 지도</a>를 참조하세요.
</iframe>

스타일 선택기 컨트롤은 [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 클래스에 의해 정의됩니다. 스타일 선택기 컨트롤을 사용하는 방법에 대한 자세한 내용은 [맵 스타일 선택](choose-map-style.md)을 참조하세요.

## <a name="customize-controls"></a>컨트롤 사용자 지정

컨트롤을 사용자 지정하는 다양한 옵션을 테스트하는 도구는 다음과 같습니다.

<br/>

<iframe height="700" scrolling="no" title="탐색 컨트롤 옵션" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>탐색 컨트롤 옵션</a>을 참조하세요.
</iframe>

사용자 지정된 탐색 컨트롤을 만들려면 `atlas.Control` 클래스에서 확장하는 클래스를 만들거나 HTML 요소를 만들고 맵 div 위에 배치합니다. 이 UI 컨트롤이 맵 `setCamera` 함수를 호출하여 맵을 이동하도록 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [나침반 컨트롤](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

전체 코드는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핀 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [팝업 추가](./map-add-popup.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)
