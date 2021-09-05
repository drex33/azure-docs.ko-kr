---
title: 그리기 도구 이벤트 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Web SDK를 사용하여 맵에 그리기 도구 모음을 추가하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: edb3b0bdcd5d8e9f80493267cad3ccbfa07a576f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665213"
---
# <a name="drawing-tool-events"></a>그리기 도구 이벤트

맵에서 그리기 도구를 사용하는 경우 사용자가 맵에 그릴 때 특정 이벤트에 유용하게 대응할 수 있습니다. 다음 표에서는 `DrawingManager` 클래스에서 지원하는 모든 이벤트를 나열합니다.

| 이벤트 | Description |
|-------|-------------|
| `drawingchanged` | 셰이프의 모든 좌표가 추가되거나 변경되었을 때 발생합니다. | 
| `drawingchanging` | 셰이프의 미리 보기 좌표가 표시될 때 발생합니다. 예를 들어 이 이벤트는 좌표를 끌 때 여러 번 발생합니다. | 
| `drawingcomplete` | 셰이프 그리기를 완료했거나 편집 모드를 벗어났을 때 발생합니다. |
| `drawingerased` | `erase-geometry` 모드에 있을 때 그리기 관리자에서 셰이프가 지워지면 발생합니다. |
| `drawingmodechanged` | 그리기 모드가 변경될 때 발생합니다. 새 그리기 모드가 이벤트 처리기에 전달됩니다. |
| `drawingstarted` | 사용자가 도형 그리기를 시작하거나 셰이프를 편집 모드로 전환할 때 발생합니다.  |

다음 코드에서는 그리기 도구 모듈의 이벤트가 작동하는 방식을 보여 줍니다. 맵에 셰이프를 그리고 이벤트가 발생할 때 감시합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 이벤트" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>그리기 도구 이벤트</a>을 참조하세요.
</iframe>

<br/>

## <a name="examples"></a>예제

그리기 도구 이벤트를 사용하는 몇 가지 일반적인 시나리오를 살펴보겠습니다.

### <a name="select-points-in-polygon-area"></a>다각형 영역에서 요소 선택

이 코드는 사용자 그리기 셰이프의 이벤트를 모니터링하는 방법을 보여 줍니다. 이 예제에서 코드는 다각형, 사각형 및 원의 셰이프를 모니터링합니다. 그런 다음, 맵에서 그린 영역 내에 있는 데이터 요소를 결정합니다. `drawingcomplete` 이벤트는 선택 논리를 트리거하는 데 사용됩니다. 선택 논리에서 코드는 맵의 모든 데이터 요소를 반복합니다. 요소와 그린 셰이프의 영역이 교차하는지 확인합니다. 이 예제에서는 오픈 소스 [Turf.js](https://turfjs.org/) 라이브러리를 사용하여 공간 교차점 계산을 수행합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그린 다각형 영역에서 데이터 선택" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/XWJdeja'>그린 다각형 영역에서 데이터 선택</a>을 참조하세요.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>다각형 영역에서 그리기 및 검색

이 코드는 사용자가 셰이프 그리기를 마친 후 셰이프의 영역 내에서 관심 지점을 검색합니다. 프레임의 오른쪽 위 모서리에 있는 '코드에서 편집 펜'을 클릭하여 코드를 수정하고 실행할 수 있습니다. `drawingcomplete` 이벤트는 검색 논리를 트리거하는 데 사용됩니다. 사용자가 사각형 또는 다각형을 그리면 기하 도형 내에서 검색이 수행됩니다. 원을 그리면 반지름과 중심 위치를 사용하여 관심 지점을 검색합니다. `drawingmodechanged` 이벤트는 사용자가 그리기 모드로 전환하는 시기를 결정하는 데 사용되며, 이 이벤트는 그리기 캔버스를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 영역에서 그리기 및 검색" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>다각형 영역에서 그리기 및 검색</a>을 참조하세요.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>측정 도구 만들기

아래 코드에서는 그리기 이벤트를 사용하여 측정 도구를 만드는 방법을 보여 줍니다. `drawingchanging`은 셰이프를 그릴 때 셰이프를 모니터링하는 데 사용됩니다. 사용자가 마우스를 움직이면 셰이프의 크기가 계산됩니다. `drawingcomplete` 이벤트는 셰이프가 그려진 후에 셰이프에 대해 최종 계산을 수행하는 데 사용됩니다. `drawingmodechanged` 이벤트는 사용자가 그리기 모드로 전환하는 시기를 결정하는 데 사용됩니다. 또한 `drawingmodechanged` 이벤트는 그리기 캔버스를 지우고 이전 측정 정보를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="측정 도구" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>측정 도구</a>를 참조하세요.
</iframe>

<br/>

## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

서비스 모듈에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [서비스 모듈](how-to-use-services-module.md)

추가 코드 샘플을 확인해 보세요.

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
