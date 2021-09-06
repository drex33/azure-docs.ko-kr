---
title: 맵에 스냅 그리드 추가 | Microsoft Azure Maps
description: Azure Maps Web SDK를 사용하여 맵에 스냅 그리드를 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 0236128925362bf2736489f2dbf97ab936183c93
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670048"
---
# <a name="add-a-snap-grid-to-the-map"></a>맵에 스냅 그리드 추가

스냅 그리드를 사용하면 공유 에지와 노드가 있는 셰이프를 더 쉽게 그릴 수 있고 선이 더 곧을 수 있습니다. 그리드에 셰이프 맞추기는 맵에서 빌딩 윤곽선 또는 네트워크 경로를 그릴 때 유용합니다.

그리드 맞추기의 해상도는 픽셀 단위입니다. 그리드는 사각형이며 가장 가까운 정수 확대/축소 수준을 기준으로 합니다. 그리드 비율은 각 확대/축소 수준에서 실제 세계를 기준으로 하는 두 개의 요소를 기준으로 크기가 조정됩니다.

## <a name="use-a-snap-grid"></a>스냅 그리드 사용

`atlas.drawing.SnapGridManager` 클래스를 사용하여 스냅 그리드를 만들고 관리자를 연결하려는 맵에 참조를 전달합니다. 그리드를 표시하려면 `showGrid` 옵션을 `true`로 설정합니다. 셰이프를 그리드에 맞추려면 스냅 그리드 관리자 `snapShape` 기능에 전달합니다. 위치 배열을 맞추려면 `snapPositions` 함수에 전달합니다.

다음 예제에서는 끌 때 HTML 표식을 그리드에 맞춥니다. `drawingcomplete` 이벤트가 발생했을 때 그리기 도구를 사용하여 그리드에 그려진 셰이프를 맞춥니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="맞추기 그리드 사용" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href="https://codepen.io">CodePen</a>에서 Azure Maps(<a href="https://codepen.io/azuremaps">@azuremaps</a>)의 펜 <a href="https://codepen.io/azuremaps/pen/rNmzvXO"> 맞추기 그리드 사용</a>을 참조하세요.
</iframe>


## <a name="snap-grid-options"></a>스냅 그리드 옵션

다음 예제에서는 스냅 그리드 관리자에 사용할 수 있는 다양한 사용자 지정 옵션을 보여 줍니다. 눈금선 스타일은 스냅 그리드 관리자 `getGridLayer` 기능을 사용하여 기본 선 계층을 검색하여 사용자 지정할 수 있습니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="스냅 그리드 옵션" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href="https://codepen.io">CodePen</a>에서 Azure Maps(<a href="https://codepen.io/azuremaps">@azuremaps</a>)의 펜 <a href="https://codepen.io/azuremaps/pen/RwVZJry"> 스냅 그리드 옵션</a>을 참조하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 다른 기능을 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)