---
title: Android 지도에 거품형 계층 추가 | Microsoft Azure Maps
description: 맵의 지점을 고정된 크기의 원으로 렌더링하는 방법을 알아봅니다. Azure Maps Android SDK를 사용하여 거품형 계층을 추가하고 사용자 지정하는 방법을 확인합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: c3f4dc33347a7e4397a2cd53ecc56d87ed155378
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093520"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>지도에 거품형 계층 추가(Android SDK)

이 문서에서는 맵에 거품형 계층으로 데이터 원본에서 지점 데이터를 렌더링할 수 있는 방법을 보여줍니다. 거품형 계층은 고정된 픽셀 반경을 사용하여 지점을 맵에 원형으로 렌더링합니다.

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 계층이 점 기하 도형 기능만 렌더링하도록 제한하려면 계층의 `filter` 옵션을 `eq(geometryType(), "Point")`로 설정합니다. MultiPoint 기능도 포함하려면 계층의 `filter` 옵션을 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))`로 설정합니다.

## <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 맵 `onReady` 이벤트 처리기에 삽입할 수 있습니다.

## <a name="add-a-bubble-layer"></a>거품형 레이어 추가

다음 코드는 지점 배열을 데이터 원본에 로드합니다. 그러면 데이터 요소가 거품형 계층에 연결됩니다. 거품형 계층은 5픽셀과 채우기 색이 흰색인 각 거품의 반경을 렌더링합니다. 파란색 스트로크 색과 6픽셀의 스트로크 너비입니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

다음 스크린샷에서는 위 코드가 거품형 계층에서 점을 렌더링하는 것을 보여 줍니다.

![거품형 계층을 사용하여 렌더링된 점이 있는 지도](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>거품형 계층을 사용하여 레이블 표시

이 코드는 거품형 계층을 사용하여 맵에 지점을 렌더링하는 방법을 보여줍니다. 기호 계층을 사용하여 레이블을 렌더링하는 방법도 있습니다. 기호 계층의 아이콘을 숨기려면 `iconImage` 옵션을 `"none"`으로 설정합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

다음 스크린샷에서는 거품형 계층에서 점을 렌더링하고 기호 계층을 사용하여 점의 텍스트 레이블을 렌더링하는 위 코드를 보여 줍니다.

![거품형 계층을 사용하여 렌더링된 점과 기호 계층을 사용하여 렌더링된 텍스트 레이블이 있는 지도](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [클러스터 지점 데이터](clustering-point-data-android-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)
