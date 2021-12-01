---
title: Azure 지도 Power BI 시각적 개체에 타일 계층 추가
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure 지도 Power BI 시각적 개체에서 타일 계층을 사용하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
manager: eriklind
ms.date: 11/29/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 15c15e0b07b9818a7681b06b6c049346d2388fae
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133369076"
---
# <a name="add-a-tile-layer"></a>타일 레이어 추가

참조 레이어 기능과 같은 타일 레이어 기능을 사용하면 지도에 추가 데이터를 중첩하여 더 많은 컨텍스트를 제공할 수 있습니다. 타일 레이어를 사용하면 Azure Maps 기본 지도 타일 위에 이미지를 겹칠 수 있습니다. 이 방법은 드론 이미지 또는 수백만 개의 데이터 행과 같은 크고 복잡 한 데이터 집합을 오버레이하는 좋은 방법입니다.

> [!div class="mx-imgBorder"]
> ![Azure Maps에서 현재 적외선 날씨 데이터를 표시하는 타일 레이어 위에 거품 레이어를 표시 하는 지도](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

타일 레이어가 서버에서 타일을 로드합니다. 이러한 이미지는 미리 렌더링 되거나 동적으로 렌더링될 수 있습니다. 미리 렌더링된 이미지는 타일 레이어에서 인식하는 명명 규칙을 사용하여 서버의 다른 이미지와 마찬가지로 저장됩니다. 동적으로 렌더링된 이미지는 서비스를 사용하여 가까운 이미지를 실시간으로 로드합니다. 타일 레이어는 지도에서 큰 데이터 집합을 시각화할 수 있는 좋은 방법입니다. 이미지에서 타일 레이어를 생성할 있을 뿐 아니라 벡터 데이터 또한 타일 레이어로 렌더링할 수 있습니다.

타일 서비스가 가능한 경계 상자 및 확대/축소 범위는 타일이 요청되는 위치를 제한하는 설정으로 전달될 수 있습니다. 이는 시각적 개체와 타일 서비스 모두에 대한 성능 향상입니다. 다음은 **타일 레이어** 섹션에서 사용 가능한 **형식** 창에서 사용할 수 있는 모든 설정에 대한 개요입니다.

| 설정        | Description   |
|----------------|---------------|
| Url            | 타일 서비스를 가리키는 형식 지정된 URL입니다.  |
| 타일 크기      | 타일의 너비 및 높이 치수를 모두 지정하는 정수 값입니다.   |
| 북부 경계    | 타일을 사용할 수 있는 경계 상자의 북부 위도입니다. |
| 남부 경계    | 타일을 사용할 수 있는 경계 상자의 남부 위도입니다. |
| 동부 경계     | 타일을 사용할 수 있는 경계 상자의 동부 경도입니다.  |
| 서부 경계     | 타일을 사용할 수 있는 경계 상자의 서부 경도입니다.   |
| 투명성   | 타일 레이어의 투명도   |
| TMS         | 타일 지도 서비스는 타일 레이어의 Y 좌표 축에 반대하는 사양입니다. |
| 최소 확대/축소       | 최소 확대/축소 수준 타일을 사용할 수 있습니다. |
| 최대 확대/축소       | 최대 확대/축소 수준 타일을 사용할 수 있습니다.  |
| 레이어 위치 | 기타 지도 레이어를 기준으로 레이어의 위치를 지정합니다. |

## <a name="tile-url-formatting"></a>타일 URL 서식 지정

Azure 지도 Power BI 시각적 개체에서 지원하는 세 가지 타일 서비스 명명 규칙이 있습니다.

* **X, Y, 확대/축소 표기법** - 타일 그리드에서 X는 열이고 Y는 타일의 행 위치이며 확대/축소 표기법은 확대/축소 수준을 기반으로 하는 값입니다.
* **쿼드키 표기법** - x, y 및 확대/축소 정보를 단일 문자열 값으로 결합합니다. 이 문자열 값은 단일 타일에 대한 고유 식별자가 됩니다.
* **경계 상자** - 경계 상자 좌표 형식 `{west},{south},{east},{north}`으로 이미지를 지정합니다. 이 형식은 일반적으로 [WMS(웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에서 사용됩니다.

타일 URL은 다음 매개 변수를 사용하는 타일 URL 템플릿에 대한 https URL입니다.

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing 지도 타일 시스템 명명 규칙을 기반으로 하는 타일 `quadkey`식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.

예를 들어 다음은 Azure Maps에서 [날씨 레이더 타일 서비스](/rest/api/maps/render-v2/get-map-tile)에 대한 서식이 지정된 타일 URL입니다. `[subscription-key]`은 Azure Maps 구독 키에 대한 자리 표시자입니다.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}`

Azure Maps 타일링 배열 시스템에 대한 자세한 정보는 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

맵에 컨텍스트를 더 추가합니다.

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
