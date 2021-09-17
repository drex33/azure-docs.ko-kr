---
title: 맵의 도형에서 데이터 가져오기 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 그려진 도형 데이터를 가져오는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: ae9683140eb1a3faf685bb3ffe2ee299313eabe3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435009"
---
# <a name="get-shape-data"></a>도형 데이터 가져오기

이 문서에서는 맵에 그려진 도형의 데이터를 가져오는 방법을 설명합니다. [드로잉 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--) 내의 **drawingManager.getSource()** 함수를 사용합니다. 그려진 도형의 geojson 데이터를 추출하고 다른 곳에서 사용하려는 경우의 시나리오는 다양합니다.  


## <a name="get-data-from-drawn-shape"></a>그려진 도형에서 데이터 가져오기

다음 함수는 그려진 도형의 원본 데이터를 가져와 화면에 출력합니다. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

다음은 도형을 그려 기능을 테스트할 수 있는 전체 실행 코드 샘플입니다.

<br/>

<iframe height="686" title="도형 데이터 가져오기" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"><a href='https://codepen.io'>CodePen</a>에서 Azure Maps로 펜 <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>도형 데이터 가져오기</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [그리기 관리자](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [그리기 도구 모음](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)