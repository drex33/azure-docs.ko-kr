---
title: Azure Maps 웹 지도 컨트롤의 스타일 변경
description: 지도의 스타일 및 옵션을 변경하는 방법에 대해 알아봅니다. 사용자가 다른 스타일 사이를 전환할 수 있도록 Azure Maps에서 지도에 스타일 선택 컨트롤을 추가하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 7a301f9882f8b35ad7df25dd1ea8bdfdd22577f2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432849"
---
# <a name="change-the-style-of-the-map"></a>맵 스타일 변경

지도 컨트롤은 다양한 지도 [스타일 옵션](/javascript/api/azure-maps-control/atlas.styleoptions) 및 [기본 지도 스타일](supported-map-styles.md)을 지원합니다. 지도 컨트롤이 초기화될 때 모든 스타일을 설정할 수 있습니다. 또는 지도 컨트롤의 `setStyle` 함수를 사용하여 스타일을 설정할 수 있습니다. 이 문서에서는 이러한 스타일 옵션을 사용하여 지도의 모양을 사용자 지정하는 방법을 보여 줍니다. 또한 지도에서 스타일 선택 컨트롤을 구현하는 방법을 배웁니다. 스타일 선택 컨트롤을 사용하면 사용자가 다른 여러 기본 스타일 사이에 전환할 수 있습니다.

## <a name="set-map-style-options"></a>지도 스타일 옵션 설정

웹 컨트롤 초기화 중 스타일 옵션을 설정할 수 있습니다. 또는 지도 컨트롤의 `setStyle` 함수를 호출하여 스타일 옵션을 업데이트할 수 있습니다. 사용 가능한 모든 스타일 옵션을 보려면 [스타일 옵션](/javascript/api/azure-maps-control/atlas.styleoptions)을 참조하세요.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

다음 도구는 여러 스타일 옵션이 지도의 렌더링 방식을 변경하는 방법을 보여 줍니다. 3D 건물을 보려면 주요 도시를 가까이 확대합니다.

<br/>

<iframe height="700" scrolling="no" title="지도 스타일 옵션" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>지도 스타일 옵션</a>을 참조하세요.
</iframe>

## <a name="set-a-base-map-style"></a>기본 지도 스타일 설정

웹 SDK에서 사용할 수 있는 [기본 지도 스타일](supported-map-styles.md) 중 하나로 지도 컨트롤을 초기화할 수도 있습니다. 그런 다음 `setStyle` 함수를 사용하여 다른 지도 스타일로 기본 스타일을 업데이트할 수 있습니다.

### <a name="set-a-base-map-style-on-initialization"></a>초기화 시 기본 지도 스타일 설정

초기화하는 동안 지도 컨트롤의 기본 스타일을 설정할 수 있습니다. 다음 코드에서 지도 컨트롤의 `style` 옵션은 [`grayscale_dark` 기본 지도 스타일](supported-map-styles.md#grayscale_dark)로 설정됩니다.  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='지도 로드 시 스타일 설정' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>지도 로드 시 스타일 설정</a>을 참조하세요.
</iframe>

### <a name="update-the-base-map-style"></a>기본 지도 스타일 업데이트

기본 지도 스타일은 `setStyle` 함수를 사용하고 `style` 옵션을 설정하여 다른 기본 지도 스타일로 변경하거나 스타일 옵션을 더 추가하여 업데이트할 수 있습니다.

```javascript
map.setStyle({ style: 'satellite' });
```

다음 코드에서는 지도 인스턴스가 로드된 후 [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) 함수를 사용하여 지도 스타일이 `grayscale_dark`에서 `satellite`로 업데이트됩니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 업데이트' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>스타일 업데이트</a>를 참조하세요.
</iframe>

## <a name="add-the-style-picker-control"></a>스타일 선택 컨트롤 추가

스타일 선택 컨트롤은 최종 사용자가 기본 스타일을 전환하는 데 사용할 수 있는 플라이아웃 패널과 사용하기 쉬운 단추를 제공합니다.

스타일 선택에는 `icon` 및 `list`의 두 가지 레이아웃 옵션이 있습니다. 또한 스타일 선택을 사용하면 두 가지 스타일 선택 제어 `style` 옵션(`light` 및 `dark`)을 선택할 수 있습니다. 이 예에서 스타일 선택은 `icon` 레이아웃을 사용하고 아이콘 형식으로 기본 지도 스타일의 선택 목록을 표시합니다. 스타일 컨트롤 선택에는 다음과 같은 기본 스타일 세트가 포함되어 있습니다. `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]`. 스타일 선택 컨트롤 옵션에 대한 자세한 내용은 [스타일 컨트롤 옵션](/javascript/api/azure-maps-control/atlas.stylecontroloptions)을 참조하세요.

아래 이미지는 `icon` 레이아웃에 표시된 스타일 선택 컨트롤을 보여 줍니다.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="스타일 선택 아이콘 레이아웃":::

아래 이미지는 `list` 레이아웃에 표시된 스타일 선택 컨트롤을 보여 줍니다.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="스타일 선택 목록 레이아웃":::

> [!IMPORTANT]
> 기본적으로 스타일 선택 컨트롤은 Azure Maps S0 가격 책정 계층에서 사용할 수 있는 모든 스타일을 나열합니다. 이 목록의 스타일 수를 줄이려면 목록에 표시할 스타일의 배열을 스타일 선택의 `mapStyle` 옵션에 전달합니다. Gen 1(S1) 또는 Gen 2 가격 책정 계층을 사용 중이고 사용 가능한 모든 스타일을 표시하려면 스타일 선택의 `mapStyles` 옵션을 `"all"`로 설정하세요.

다음 코드는 기본값인 `mapStyles` 기본 스타일 목록을 재정의하는 방법을 보여 줍니다. 이 예에서는 `mapStyles` 옵션을 설정하여 스타일 선택 컨트롤에 의해 표시될 기본 스타일을 나열합니다.

<br/>

<iframe height='500' scrolling='no' title='스타일 선택 추가' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>스타일 선택 추가</a>를 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아려보면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)
