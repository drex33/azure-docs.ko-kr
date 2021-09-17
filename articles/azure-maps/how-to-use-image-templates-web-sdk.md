---
title: Azure Maps 웹 SDK의 이미지 템플릿 | Microsoft Azure Maps
description: Azure Maps 웹 SDK를 사용하여 지도에 이미지 아이콘 및 패턴이 채워진 다각형을 추가하는 방법에 대해 알아봅니다. 사용 가능한 이미지 및 채우기 패턴 템플릿을 봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 520a8681ff72f6cbf0587baa501b5ece3d5369e7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437853"
---
# <a name="how-to-use-image-templates"></a>이미지 템플릿을 사용하는 방법

Azure Maps 웹 SDK 내에서 HTML 마커 및 다양한 계층에 이미지를 사용할 수 있습니다.

 - 기호 계층은 이미지 아이콘이 있는 지도에서 포인트를 렌더링할 수 있습니다. 줄 경로를 따라 기호를 렌더링할 수도 있습니다.
 - 다각형 계층은 채우기 패턴 이미지를 사용하여 렌더링할 수 있습니다. 
 - HTML 마커는 이미지 및 기타 HTML 요소를 사용하여 포인트를 렌더링할 수 있습니다.

계층을 사용할 때 좋은 성능을 보장하려면 렌더링하기 전에 이미지를 지도 이미지 스프라이트 리소스에 로드합니다. SymbolLayer의 [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)는 기본값으로 몇 가지 색의 마커 이미지를 지도 이미지 스프라이트에 미리 로드합니다. 이러한 마커 이미지는 SVG 템플릿으로 사용할 수 있습니다. 사용자 지정 배율을 사용하여 이미지를 만들거나 고객의 기본 및 보조 색상으로 사용할 수 있습니다. 총 42개의 이미지 템플릿, 27개의 기호 아이콘 및 15개의 다각형 채우기 패턴이 제공됩니다

이미지 템플릿은 `map.imageSprite.createFromTemplate` 함수를 사용하여 지도 이미지 스프라이트 리소스에 추가할 수 있습니다. 이 함수를 사용하면 최대 5개의 매개 변수를 전달할 수 있습니다.

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id`는 사용자가 만드는 고유 식별자입니다. `id`는 맵 이미지 스프라이트에 추가될 때 이미지에 할당됩니다. 계층에 이 식별자를 사용하여 렌더링할 이미지 리소스를 지정합니다. `templateName`은 사용할 이미지 템플릿을 지정합니다. `color` 옵션은 이미지의 기본 색을 설정하고 `secondaryColor` 옵션은 이미지의 보조 색을 설정합니다. `scale` 옵션은 이미지 스프라이트에 적용하기 전에 이미지 템플릿의 크기를 조정합니다. 이미지가 이미지 스프라이트에 적용되면 PNG로 변환됩니다. 선명한 렌더링을 위해 이미지 템플릿을 레이어에서 확장하는 것보다 스프라이트에 추가하기 전에 이미지 템플릿을 확장하는 것이 좋습니다.

이 함수는 이미지를 이미지 스프라이트에 비동기적으로 로드합니다. 따라서 이 함수가 완료되기를 기다릴 수 있는 프라미스를 반환합니다.

다음 코드에서는 기본 제공 템플릿 중 하나에서 이미지를 만들고 기호 계층과 함께 사용하는 방법을 보여줍니다.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>기호 계층이 있는 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 이미지 리소스 ID를 `iconOptions`의 `image` 옵션에서 참조하여 기호 계층에서 기호로 렌더링할 수 있습니다.

다음 샘플에서는 청록색 기본 색과 흰색 보조 색이 있는 `marker-flat` 이미지 템플릿을 사용하여 기호 계층을 렌더링합니다. 

<br/>

<iframe height="500" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 기호 계층" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>기본 제공 아이콘 템플릿이 있는 기호 계층</a>을 참조합니다.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>선 경로를 따라 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 데이터 원본에 LineString을 추가하고 `lineSpacing` 옵션을 추가하고 이미지 리소스의 ID를 `iconOptions`의 `image` 옵션에서 참조한 기호 계층을 사용해 선의 경로를 따라 렌더링할 수 있습니다. 

다음 샘플에서는 맵에 분홍색 선을 렌더링하고, `car` 이미지 템플릿을 사용하여 진한 파란색 기본 색과 흰색 보조 색으로 기호 계층을 사용합니다. 

<br/>

<iframe height="500" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 선 계층" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>기본 제공 아이콘 템플릿이 있는 선 계층</a>을 참조합니다.
</iframe>

> [!TIP]
> 이미지 템플릿이 가리키는 경우, 선과 동일한 방향을 가리키도록 하려면 기호 계층의 `rotation` 아이콘 옵션을 90으로 설정합니다.

## <a name="use-an-image-template-with-a-polygon-layer"></a>다각형 계층에서 이미지 템플릿 사용

이미지 템플릿이 맵 이미지 스프라이트에 로드되면 계층의 `fillPattern` 옵션에서 이미지 리소스 ID를 참조하여 다각형 계층에서 채우기 패턴으로 렌더링할 수 있습니다.

다음 샘플에서는 빨간색 기본 색과 투명한 보조 색이 있는 `dot` 이미지 템플릿을 사용하여 다각형 계층을 렌더링합니다.  

<br/>

<iframe height="500" scrolling="no" title="기본 제공 아이콘 템플릿으로 다각형 채우기" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>기본 제공 아이콘 템플릿으로 다각형 채우기</a>를 참조합니다.
</iframe>

> [!TIP]
> 채우기 패턴의 보조 색을 설정하면 기본 맵이 여전히 주 패턴을 제공하는 것을 쉽게 확인할 수 있습니다. 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML 마커와 함께 이미지 템플릿 사용

이미지 템플릿은 `altas.getImageTemplate` 함수를 사용하여 검색하고 HTML 마커의 콘텐츠로 사용할 수 있습니다. 템플릿은 마커의 `htmlContent` 옵션으로 전달된 다음, `color`, `secondaryColor`, `text` 옵션으로 사용자 지정할 수 있습니다.

다음 샘플에서는 기본 빨간색, 보조 분홍색 색 및 텍스트 값 "00"으로 `marker-arrow` 템플릿을 사용합니다.

<br/>

<iframe height="500" scrolling="no" title="기본 제공 아이콘 템플릿이 있는 HTML 마커" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>기본 제공 아이콘 템플릿이 있는 HTML 마커</a>를 참조합니다.
</iframe>


> [!TIP]
> 이미지 템플릿은 맵 외부에서도 사용할 수 있습니다. getImageTemplate 기능은 자리 표시자 `{color}`, `{secondaryColor}`, `{scale}`, `{text}`를 갖춘 SVG 문자열을 반환합니다. 유효한 SVG 문자열을 만들려면 이러한 자리 표시자 값을 바꿉니다. 그런 다음 HTML DOM에 직접 SVG 문자열을 추가하거나 데이터 URI로 변환하여 이미지 태그에 삽입할 수 있습니다. 예를 들면 다음과 같습니다.
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>재사용 가능한 사용자 지정 템플릿 만들기

애플리케이션에서 아이콘이 다른 아이콘을 사용하거나 추가 이미지 템플릿을 추가하는 모듈을 만드는 경우 Azure Maps 웹 SDK에서 이러한 아이콘을 쉽게 추가하고 검색할 수 있습니다. `atlas` 네임스페이스에서 다음 고정적인 함수를 사용합니다.

| 이름 | 반환 형식 | 설명 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Atlas 네임스페이스에 사용자 지정 SVG 이미지 템플릿을 추가합니다. |
|  `getImageTemplate(templateName: string, scale?: number)`| 문자열 | 이름으로 SVG 템플릿을 검색합니다. |
| `getAllImageTemplateNames()` | string[] |  이름으로 SVG 템플릿을 검색합니다. |

SVG 이미지 템플릿은 다음 자리 표시자 값을 지원합니다.

| 자리 표시자 | Description |
|-|-|
| `{color}` | 기본 색입니다. | 
| `{secondaryColor}` | 보조 색입니다. | 
| `{scale}` | 지도 이미지 스프라이트에 추가되면 SVG 이미지가 PNG 이미지로 변환됩니다. 이 자리 표시자를 사용하여 템플릿이 변환되기 전에 크기를 조정하여 명확하게 렌더링되도록 할 수 있습니다. | 
| `{text}` | HTML 표식과 함께 사용할 때 텍스트를 렌더링할 위치입니다. |

다음 예제에서는 SVG 템플릿을 사용하여 Azure Maps 웹 SDK에 재사용 가능한 아이콘 템플릿으로 추가하는 방법을 보여줍니다. 

<br/>

<iframe height="500" scrolling="no" title="Atlas 네임스페이스에 사용자 지정 아이콘 템플릿 추가" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Atlas 네임스페이스에 사용자 지정 아이콘 템플릿 추가</a>를 참조합니다.
</iframe>

## <a name="list-of-image-templates"></a>이미지 템플릿 목록

이 표에는 Azure Maps 웹 SDK 내에서 현재 사용할 수 있는 모든 이미지 템플릿이 나열되어 있습니다. 템플릿 이름은 각 이미지 위에 있습니다. 기본값으로 기본 색은 파란색이고 보조 색은 흰색입니다. 흰색 배경에서 보조 색을 더 쉽게 볼 수 있도록 다음 이미지들은 보조 색이 검은색으로 설정되어 있습니다.

**기호 아이콘 템플릿**

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      marker-thick
   :::column-end:::
   :::column span="":::
      marker-circle
   :::column-end:::
   :::column span="":::
      marker-flat
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식 아이콘](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![표식 굵은 아이콘](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![표식 원 아이콘](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![표식 평면 아이콘](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marker-square
   :::column-end:::
   :::column span="":::
      marker-square-cluster
   :::column-end:::
   :::column span="":::
      marker-arrow
   :::column-end:::
   :::column span="":::
      marker-ball-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식-사각형 아이콘](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![표식-사각형-클러스터 아이콘](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![표식-화살표 아이콘](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![표식-공-핀 아이콘](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marker-square-rounded
   :::column-end:::
   :::column span="":::
      marker-square-rounded-cluster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      flag-triangle
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![표식-둥근 사각형 아이콘](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![표식-둥근 사각형-클러스터 아이콘](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![플래그 아이콘](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![플래그-삼각형 아이콘](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      삼각형
   :::column-end:::
   :::column span="":::
      triangle-thick
   :::column-end:::
   :::column span="":::
      triangle-arrow-up
   :::column-end:::
   :::column span="":::
      triangle-arrow-left
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![삼각형 아이콘](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-굵은 아이콘](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-화살표-위쪽 아이콘](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![삼각형-화살표-왼쪽 아이콘](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      육각형
   :::column-end:::
   :::column span="":::
      육각형-굵은
   :::column-end:::
   :::column span="":::
      육각형-둥근
   :::column-end:::
   :::column span="":::
      육각형-둥근-굵은
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![육각형 아이콘](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![육각형-굵은 아이콘](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![육각형-둥근 아이콘](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![육각형-둥근-굵은 아이콘](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      고정
   :::column-end:::
   :::column span="":::
      pin-round
   :::column-end:::
   :::column span="":::
      둥근-사각형
   :::column-end:::
   :::column span="":::
      rounded-square-thick
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![고정 아이콘](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![고정-원형 아이콘](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![둥근-사각형 아이콘](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![둥근-사각형-굵은 아이콘](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      위쪽-화살표
   :::column-end:::
   :::column span="":::
      arrow-up-thin
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![화살표-위쪽 아이콘](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![화살표-위쪽-얇은 아이콘](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![자동차 아이콘](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**다각형 채우기 패턴 템플릿**

:::row:::
   :::column span="":::
      검사기
   :::column-end:::
   :::column span="":::
      검사기-회전
   :::column-end:::
   :::column span="":::
      원
   :::column-end:::
   :::column span="":::
      circles-spaced
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![검사기 아이콘](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![검사기-회전 아이콘](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![원 아이콘](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![원-간격 아이콘](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonal-lines-up
   :::column-end:::
   :::column span="":::
      diagonal-lines-down
   :::column-end:::
   :::column span="":::
      diagonal-stripes-up
   :::column-end:::
   :::column span="":::
      diagonal-stripes-down
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![대각-선-위쪽 아이콘](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![대각-선-아래쪽 아이콘](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![대각선-줄무늬-위쪽 아이콘](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![대각선-줄무늬-아래쪽 아이콘](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      grid-lines
   :::column-end:::
   :::column span="":::
      rotated-grid-lines
   :::column-end:::
   :::column span="":::
      rotated-grid-stripes
   :::column-end:::
   :::column span="":::
      x-fill
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![눈금-선 아이콘](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![회전-눈금-선 아이콘](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![회전-눈금-줄무늬 아이콘](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x-채우기 아이콘](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      지그-재그
   :::column-end:::
   :::column span="":::
      zig-zag-vertical
   :::column-end:::
   :::column span="":::
      점
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![지그-재그 아이콘](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![지그-재그-수직 아이콘](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![점 아이콘](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**미리 로드된 이미지 아이콘**

맵은 `marker`, `pin`, `pin-round` 템플릿을 사용하여 맵 이미지 스프라이트에 아이콘 집합을 미리 로드합니다. 이러한 아이콘 이름 및 해당 색 값은 아래 표에 나열되어 있습니다.

| 아이콘 이름 | 색 | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>지금 시도 도구

다음 도구를 사용하여 다양한 기본 제공 이미지 템플릿을 여러 방식으로 렌더링하고 기본 및 보조 색과 배율에 맞게 사용자 지정할 수 있습니다.

<br/>

<iframe height="500" scrolling="no" title="아이콘 템플릿 옵션" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>아이콘 템플릿 옵션</a>을 참조합니다.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas 네임스페이스](/javascript/api/azure-maps-control/atlas#functions
)

이미지 템플릿을 사용할 수 있는 더 많은 코드 샘플은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](map-add-bubble-layer.md)