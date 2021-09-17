---
title: 맵 이벤트 처리 | Microsoft Azure Maps
description: 사용자가 맵과 상호 작용할 때 발생하는 이벤트에 대해 알아봅니다. 지원되는 모든 맵 이벤트 목록을 봅니다. Azure Maps 웹 SDK를 사용하여 이벤트를 처리하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 0124d86a8ca0236fed8e2104ee38bd0863698307
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438141"
---
# <a name="interact-with-the-map"></a>맵과 상호 작용

이 문서에서는 [맵 이벤트 클래스](/javascript/api/azure-maps-control/atlas.map#events)를 사용하는 방법을 보여 줍니다. 속성은 맵과 맵의 여러 계층에 대한 이벤트를 강조 표시합니다. HTML 표식과 상호 작용할 때 이벤트를 강조 표시할 수도 있습니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

아래의 맵을 재생하고 오른쪽에 강조 표시된 해당 마우스 이벤트를 확인합니다. **JS 탭** 을 클릭하면 JavaScript 코드를 보고 편집할 수 있습니다. **CodePen에서 편집** 을 클릭하여 CodePen에서 코드를 수정할 수도 있습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 – 마우스 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>맵과 상호 작용 - 마우스 이벤트</a>를 참조하세요.
</iframe>

## <a name="interact-with-map-layers"></a>맵 계층과 상호 작용

다음 코드는 기호 계층과 상호 작용할 때 발생한 이벤트를 강조 표시합니다. 기호, 거품형, 선 및 다각형 계층은 모두 동일한 이벤트 세트를 지원합니다. 열 지도 및 타일 계층은 이러한 이벤트를 지원하지 않습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 - 계층 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>맵 - 계층 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

## <a name="interact-with-html-marker"></a>HTML 표식과 상호 작용

다음 코드는 HTML 표식에 JavaScript 맵 이벤트를 추가합니다. 또한 HTML 표식과 상호 작용할 때 발생한 이벤트의 이름을 강조 표시합니다.

<br/>

<iframe height='500' scrolling='no' title='맵 - HTML 표식 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>맵 - HTML 표식 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

다음 표에서는 지원되는 모든 맵 클래스 이벤트를 보여 줍니다.

| 이벤트               | Description |
|---------------------|-------------|
| `boxzoomend`        | "상자 확대/축소" 상호 작용이 종료될 때 발생합니다.|
| `boxzoomstart`      | "상자 확대/축소" 상호 작용이 시작될 때 발생합니다.|
| `click`             | 맵의 동일한 지점에서 포인팅 디바이스를 눌렀다가 놓을 때 발생합니다.|
| `close`             | 수동으로 또는 프로그래밍 방식으로 팝업을 닫을 때 발생합니다.|
| `contextmenu`       | 마우스 오른쪽 단추를 클릭할 때 발생합니다.|
| `data`              | 맵 데이터가 로드되거나 변경될 때 발생합니다. |
| `dataadded`         | 셰이프가 `DataSource`에 추가될 때 발생합니다.|
| `dataremoved`       | `DataSource`에서 셰이프를 제거할 때 발생합니다.|
| `datasourceupdated` | `DataSource` 개체가 업데이트될 때 발생합니다.|
| `dblclick`          | 맵의 서 동일한 지점에서 포인팅 디바이스를 두 번 클릭할 때 발생합니다.|
| `drag`              | 맵, 팝업 또는 HTML 표식에서 "끌어 이동"하는 동안 반복적으로 발생합니다.|
| `dragend`           | 맵, 팝업 또는 HTML 표식에서 "끌어 이동" 상호 작용이 종료될 때 발생합니다.|
| `dragstart`         | 맵, 팝업 또는 HTML 표식에서 "끌어 이동" 상호 작용이 시작될 때 발생합니다.|
| `error`             | 오류가 발생할 때 발생합니다.|
| `idle`              | <p>맵이 "유휴" 상태가 되기 전에 렌더링된 마지막 프레임 후에 발생합니다.<ul><li>진행 중인 카메라 전환이 없습니다.</li><li>현재 요청된 모든 타일이 로드되었습니다.</li><li>모든 페이드/전환 애니메이션이 완료되었습니다.</li></ul></p>|
| `keydown`           | 키를 누를 때 발생합니다.|
| `keypress`          | 입력 가능한 문자(ANSI 키)를 생성하는 키를 누를 때 발생합니다.|
| `keyup`             | 키를 놓을 때 발생합니다.|
| `layeradded`        | 맵에 계층이 추가될 때 발생합니다.|
| `layerremoved`      | 맵에서 계층이 제거될 때 발생합니다.|
| `load`              | 필요한 모든 리소스를 다운로드하고 맵의 시각적으로 완전한 최초 렌더링이 발생한 후 즉시 발생합니다.|
| `mousedown`         | 맵 내에서 포인팅 디바이스를 누르거나 디바이스가 요소 위에 있을 때 발생합니다.|
| `mouseenter`        | 포인팅 디바이스를 처음에 맵 또는 요소로 이동할 때 발생합니다. |
| `mouseleave`        | 포인팅 디바이스를 맵 또는 요소 밖으로 이동할 때 발생합니다. |
| `mousemove`         | 포인팅 디바이스를 맵 또는 요소 내에서 이동할 때 발생합니다.|
| `mouseout`          | 포인팅 디바이스가 맵의 캔버스를 벗어나거나 요소를 벗어날 때 발생합니다.|
| `mouseover`         | 포인팅 디바이스를 맵 또는 요소 위로 이동할 때 발생합니다.|
| `mouseup`           | 맵 내에서 포인팅 디바이스를 떼거나 디바이스가 요소 위에 있을 때 발생합니다.|
| `move`              | 사용자 상호 작용이나 메서드의 결과로 한 보기에서 다른 보기로 애니메이션 전환이 수행되는 동안 반복적으로 발생합니다.|
| `moveend`           | 사용자 상호 작용이나 메서드의 결과로 맵이 한 보기에서 다른 보기로의 전환을 완료한 후에 발생합니다.|
| `movestart`         | 사용자 상호 작용이나 메서드의 결과로 맵이 한 보기에서 다른 보기로의 전환을 시작하기 전에 발생합니다.|
| `open`              | 수동으로 또는 프로그래밍 방식으로 팝업을 열 때 발생합니다.|
| `pitch`             | 사용자 상호 작용이나 메서드의 결과로 맵의 피치(기울기)가 변경될 때마다 발생합니다.|
| `pitchend`          | 사용자 상호 작용이나 메서드의 결과로 맵의 피치(기울기)가 변경을 끝낸 직후에 발생합니다.|
| `pitchstart`        | 사용자 상호 작용이나 메서드의 결과로 맵의 피치(기울기)가 변경을 시작할 때마다 발생합니다.|
| `ready`             | 맵이 프로그래밍 방식으로 상호 작용할 수 있도록 준비되기 전에 필요한 최소 맵 리소스를 로드할 때 발생합니다.|
| `render`            | <p>다음의 결과로 맵을 화면에 그릴 때마다 발생합니다.<ul><li>맵의 위치, 확대/축소, 피치 또는 방위에 대한 변경 내용입니다.</li><li>맵 스타일에 대한 변경 내용입니다.</li><li>`DataSource` 원본에 대한 변경 내용입니다.</li><li>벡터 타일, GeoJSON 파일, 문자 모양 또는 스프라이트의 로드입니다.</li></ul></p>|
| `resize`            | 맵 크기가 조정된 직후에 발생합니다.|
| `rotate`            | "끌어 회전" 작업을 수행하는 동안 반복적으로 발생합니다.|
| `rotateend`         | "끌기 회전" 상호 작용이 종료될 때 발생합니다.|
| `rotatestart`       | "끌기 회전" 상호 작용이 시작될 때 발생합니다.|
| `shapechanged`      | 셰이프 개체 속성이 변경될 때 발생합니다.|
| `sourcedata`        | 원본에 속하는 타일이 로드되거나 변경되는 경우를 포함하여 맵의 원본 중 하나가 로드되거나 변경될 때 발생합니다. |
| `sourceadded`       | `DataSource` 또는 `VectorTileSource`가 맵에 추가될 때 발생합니다.|
| `sourceremoved`     | 맵에서 `DataSource` 또는 `VectorTileSource`가 제거될 때 발생합니다.|
| `styledata`         | 맵의 스타일이 로드되거나 변경될 때 발생합니다.|
| `styleimagemissing` | 존재하지 않는 이미지 스프라이트에서 계층이 이미지를 로드하려고 할 때 발생합니다. |
| `tokenacquired`     | Azure Active Directory 액세스 토큰을 가져올 때 발생합니다.|
| `touchcancel`       | 맵 내에서 `touchcancel` 이벤트가 발생할 때 발생합니다.|
| `touchend`          | 맵 내에서 `touchend` 이벤트가 발생할 때 발생합니다.|
| `touchmove`         | 맵 내에서 `touchmove` 이벤트가 발생할 때 발생합니다.|
| `touchstart`        | 맵 내에서 `touchstart` 이벤트가 발생할 때 발생합니다.|
| `wheel`             | 맵 내에서 마우스 휠 이벤트가 발생할 때 발생합니다.|
| `zoom`              | 사용자 상호 작용이나 메서드의 결과로 한 확대/축소 수준에서 다른 확대/축소 수준으로 애니메이션 전환이 수행되는 동안 반복적으로 발생합니다.|
| `zoomend`           | 사용자 상호 작용이나 메서드의 결과로 맵이 한 확대/축소 수준에서 다른 확대/축소 수준으로의 전환을 완료한 후에 발생합니다.|
| `zoomstart`         | 사용자 상호 작용이나 메서드의 결과로 맵이 한 확대/축소 수준에서 다른 확대/축소 수준으로의 전환을 시작하기 전에 발생합니다.|


## <a name="next-steps"></a>다음 단계

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps 서비스 모듈 사용](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [코드 샘플](/samples/browse/?products=azure-maps)