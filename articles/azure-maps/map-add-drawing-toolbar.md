---
title: 맵에 그리기 도구 모음 추가 | Microsoft Azure Maps
description: Azure Maps Web SDK를 사용하여 맵에 그리기 도구 모음을 추가하는 방법
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 0a548d516213b65406a2f47c27c9a1a82cfb6f4b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567399"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>맵에 그리기 도구 모음 추가

이 문서에서는 그리기 도구 모듈을 사용하고 맵에 그리기 도구 모음을 표시하는 방법을 보여 줍니다. [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) 컨트롤로 맵에 그리기 도구 모음을 추가할 수 있습니다. 하나의 모든 그리기 도구로 맵을 만드는 방법과 그리기 관리자에서 그리기 도형의 렌더링을 사용자 지정하는 방법을 알아보겠습니다.

## <a name="add-drawing-toolbar"></a>그리기 도구 모음 추가

다음 코드는 그리기 관리자의 인스턴스를 만들고 해당 도구 모음을 맵에 표시합니다.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 모음 추가" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>그리기 도구 모음 추가</a> 펜을 참조하세요.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>표시된 도구 모음 옵션 제한

다음 코드는 그리기 관리자의 인스턴스를 만들고 맵에 다각형 그리기 도구만 있는 도구 모음을 표시합니다. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기 도구 추가" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>다각형 그리기 도구 추가</a> 펜을 참조하세요.
</iframe>


## <a name="change-drawing-rendering-style"></a>그리기 렌더링 스타일 변경

그리는 도형의 스타일은 `drawingManager.getLayers()` 및 `drawingManager.getPreviewLayers()` 함수를 사용하여 그리기 관리자의 기본 레이어를 검색한 다음 개별 레이어의 옵션을 설정하여 사용자 지정할 수 있습니다. 도형을 편집할 때 좌표에 대해 나타나는 끌기 핸들은 HTML 표식입니다. 그리기 관리자의 `dragHandleStyle` 및 `secondaryDragHandleStyle` 옵션에 HTML 표식 옵션을 전달하여 끌기 핸들의 스타일을 사용자 지정할 수 있습니다.  

다음 코드는 그리기 관리자에서 렌더링 레이어를 가져오고 그리기의 렌더링 스타일을 변경하는 옵션을 수정합니다. 이 경우 점은 파란색 표식 아이콘으로 렌더링됩니다. 선은 빨강색이며 너비는 4픽셀입니다. 다각형의 윤곽은 주황색이고 초록색으로 채워집니다. 그런 다음 끌기 핸들의 스타일을 정사각형 아이콘으로 변경합니다. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});


//Get preview rendering layers from the drawing manager and modify line styles to be dashed.
var previewLayers = drawingManager.getPreviewLayers();
previewLayers.lineLayer.setOptions({ strokeColor: 'red', strokeWidth: 4, strokeDashArray: [3,3] });
previewLayers.polygonOutlineLayer.setOptions({ strokeColor: 'orange', strokeDashArray: [3, 3] });

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 렌더링 스타일 변경" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>그리기 렌더링 스타일 변경</a> 펜을 참조하세요.
</iframe>

> [!NOTE]
> 편집 모드에서 모양을 회전할 수 있습니다. 회전은 MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon 및 Rectangle 도형에서 지원됩니다. 점 및 원 도형은 회전할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [그리기 도구 모음](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [그리기 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)