---
title: Android maps에 선 계층 추가 | Microsoft Azure 지도
description: 지도에 선을 추가 하는 방법에 대해 알아봅니다. Azure Maps Android SDK를 사용 하 여 지도에 선 계층을 추가 하 고 기호 및 색 그라데이션을 사용 하 여 줄을 사용자 지정 하는 예제를 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e9e677ce5ecd767147e18b2dd85608ec11d20263
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218302"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>지도에 선 계층 추가 (Android SDK)

선 계층은 맵에서 `LineString` 및 `MultiLineString` 피처를 경로로 렌더링하는 데 사용할 수 있습니다. 선 계층을 사용하여 `Polygon` 및 `MultiPolygon` 기능의 개요를 렌더링할 수도 있습니다. 데이터 원본은 선 계층에 연결되어 렌더링할 데이터를 제공합니다.

> [!TIP]
> 기본적으로 선 계층은 다각형의 좌표와 데이터 원본의 선을 렌더링합니다. LineString geometry 기능만 렌더링 하도록 계층을 제한 하려면 `filter` 계층의 옵션을로 설정 `eq(geometryType(), "LineString")` 합니다. MultiLineString 기능도 포함 하려면 `filter` 계층의 옵션을로 설정 `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 맵 `onReady` 이벤트 처리기에 삽입할 수 있습니다.

## <a name="add-a-line-layer"></a>선 계층 추가

다음 코드는 선을 만드는 방법을 보여 줍니다. 데이터 소스에 줄을 추가한 다음 클래스를 사용 하 여 선 계층으로 렌더링 `LineLayer` 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.97234, 40.74327),
    Point.fromLngLat(-74.00442, 40.75680));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a list of points.
val points = asList(
    Point.fromLngLat(-73.97234, 40.74327),
    Point.fromLngLat(-74.00442, 40.75680)
)

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points))

//Create a line layer and add it to the map.
val layer = LineLayer(
    source,
    strokeColor("blue"),
    strokeWidth(5f)
)

map.layers.add(layer)
```

::: zone-end

다음 스크린샷은 줄 계층에서 선을 렌더링 하는 위의 코드를 보여 줍니다.

![선 계층을 사용 하 여 렌더링 된 선으로 맵](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-driven-line-style"></a>데이터 기반 선 스타일

다음 코드는 두 개의 선 기능을 만들고 각 줄에 속성으로 속도 제한 값을 추가 합니다. 선 계층은 속도 제한 값을 기준으로 하는 선의 데이터 드라이브 스타일 식 색을 사용 합니다. 선 데이터가도로 오버레이 되므로 아래 코드는도로 레이블을 명확 하 게 읽을 수 있도록 레이블 계층 아래에 선 계층을 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a line feature.
val feature = Feature.fromGeometry(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.131821, 47.704033),
            Point.fromLngLat(-122.099919, 47.703678)
        )
    )
)

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35)

//Add the feature to the data source.
source.add(feature)

//Create a second line feature.
val feature2 = Feature.fromGeometry(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.126662, 47.708265),
            Point.fromLngLat(-122.126877, 47.703980)
        )
    )
)

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15)

//Add the second feature to the data source.
source.add(feature2)

//Create a line layer and add it to the map.
val layer = LineLayer(
    source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
)

map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷에서는 줄 기능에서 속성을 기반으로 하는 데이터 기반 스타일 식에서 색이 검색 되는 위의 코드를 선 계층에서 렌더링 하는 방법을 보여 줍니다.

![선 계층에서 렌더링 된 데이터 드라이브 스타일 선으로 매핑](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-a-stroke-gradient-to-a-line"></a>선에 스트로크 그라데이션 추가

선에 단일 스트로크 색을 적용할 수 있습니다. 색 그라데이션으로 선을 채워서 한 선분에서 다음 선분으로의 전환을 표시할 수도 있습니다. 예를 들어 선 그라데이션을 사용하여 시간 및 거리에 따른 변경이나 연결된 개체 선의 다른 온도를 나타낼 수 있습니다. 줄에이 기능을 적용 하려면 데이터 원본에 옵션이로 설정 되어 있어야 하 `lineMetrics` `true` 고 색 그라데이션 식이 줄의 옵션으로 전달 될 수 있습니다 `strokeColor` . 스트로크 그라데이션 식은 계산된 선 메트릭을 식에 노출하는 `lineProgress` 데이터 식을 참조해야 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
);
map.sources.add(source);

//Create a line and add it to the data source.
source.add(LineString.fromLngLats(
    Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498)
    )
));

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(new LineLayer(source,
    strokeWidth(6f),

    //Pass an interpolate or step expression that represents a gradient.
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource(
    //Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    withLineMetrics(true)
)
map.sources.add(source)

//Create a line and add it to the data source.
source.add(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.18822, 47.63208),
            Point.fromLngLat(-122.18204, 47.63196),
            Point.fromLngLat(-122.17243, 47.62976),
            Point.fromLngLat(-122.16419, 47.63023),
            Point.fromLngLat(-122.15852, 47.62942),
            Point.fromLngLat(-122.15183, 47.62988),
            Point.fromLngLat(-122.14256, 47.63451),
            Point.fromLngLat(-122.13483, 47.64041),
            Point.fromLngLat(-122.13466, 47.64422),
            Point.fromLngLat(-122.13844, 47.65440),
            Point.fromLngLat(-122.13277, 47.66515),
            Point.fromLngLat(-122.12779, 47.66712),
            Point.fromLngLat(-122.11595, 47.66712),
            Point.fromLngLat(-122.11063, 47.66735),
            Point.fromLngLat(-122.10668, 47.67035),
            Point.fromLngLat(-122.10565, 47.67498)
        )
    )
)

//Create a line layer and pass in a gradient expression for the strokeGradient property.
map.layers.add(
    LineLayer(
        source,
        strokeWidth(6f),  

        //Pass an interpolate or step expression that represents a gradient.
        strokeGradient(
            interpolate(
                linear(),
                lineProgress(),
                stop(0, color(Color.BLUE)),
                stop(0.1, color(Color.argb(255, 65, 105, 225))),  //Royal Blue
                stop(0.3, color(Color.CYAN)),
                stop(0.5, color(Color.argb(255, 0, 255, 0))),  //Lime
                stop(0.7, color(Color.YELLOW)),
                stop(1, color(Color.RED))
            )
        )
    )
)
```

::: zone-end

다음 스크린샷은 그라데이션 스트로크 색을 사용 하 여 렌더링 된 선을 표시 하는 위의 코드를 보여 줍니다.

![선 계층에서 그라데이션 패스로 렌더링 된 선으로 맵](media/android-map-add-line-layer/android-line-layer-gradient.jpg)

## <a name="add-symbols-along-a-line"></a>선을 따라 기호 추가

이 샘플에서는 맵의 선을 따라 화살표 아이콘을 추가하는 방법을 보여 줍니다. 기호 계층을 사용 하는 경우 `symbolPlacement` 옵션을로 설정 `SymbolPlacement.LINE` 합니다. 이 옵션은 선을 따라 기호를 렌더링하고 아이콘(0도 = 오른쪽)을 회전합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple_arrow_right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
    Point.fromLngLat(-122.18822, 47.63208),
    Point.fromLngLat(-122.18204, 47.63196),
    Point.fromLngLat(-122.17243, 47.62976),
    Point.fromLngLat(-122.16419, 47.63023),
    Point.fromLngLat(-122.15852, 47.62942),
    Point.fromLngLat(-122.15183, 47.62988),
    Point.fromLngLat(-122.14256, 47.63451),
    Point.fromLngLat(-122.13483, 47.64041),
    Point.fromLngLat(-122.13466, 47.64422),
    Point.fromLngLat(-122.13844, 47.65440),
    Point.fromLngLat(-122.13277, 47.66515),
    Point.fromLngLat(-122.12779, 47.66712),
    Point.fromLngLat(-122.11595, 47.66712),
    Point.fromLngLat(-122.11063, 47.66735),
    Point.fromLngLat(-122.10668, 47.67035),
    Point.fromLngLat(-122.10565, 47.67498)))
);

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple_arrow_right)

//Create and add a line to the data source.
//Create and add a line to the data source.
source.add(
    LineString.fromLngLats(
        Arrays.asList(
            Point.fromLngLat(-122.18822, 47.63208),
            Point.fromLngLat(-122.18204, 47.63196),
            Point.fromLngLat(-122.17243, 47.62976),
            Point.fromLngLat(-122.16419, 47.63023),
            Point.fromLngLat(-122.15852, 47.62942),
            Point.fromLngLat(-122.15183, 47.62988),
            Point.fromLngLat(-122.14256, 47.63451),
            Point.fromLngLat(-122.13483, 47.64041),
            Point.fromLngLat(-122.13466, 47.64422),
            Point.fromLngLat(-122.13844, 47.65440),
            Point.fromLngLat(-122.13277, 47.66515),
            Point.fromLngLat(-122.12779, 47.66712),
            Point.fromLngLat(-122.11595, 47.66712),
            Point.fromLngLat(-122.11063, 47.66735),
            Point.fromLngLat(-122.10668, 47.67035),
            Point.fromLngLat(-122.10565, 47.67498)
        )
    )
)

//Create a line layer and add it to the map.
map.layers.add(
    LineLayer(
        source,
        strokeColor("DarkOrchid"),
        strokeWidth(5f)
    )
)

//Create a symbol layer and add it to the map.
map.layers.add(
    SymbolLayer(
        source,  //Space symbols out along line.
        symbolPlacement(SymbolPlacement.LINE),  //Spread the symbols out 100 pixels apart.
        symbolSpacing(100f),  //Use the arrow icon as the symbol.
        iconImage("arrow-icon"),  //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
        iconAllowOverlap(true),  //Center the symbol icon.
        iconAnchor(AnchorType.CENTER),  //Scale the icon size.
        iconSize(0.8f)
    )
)
```

::: zone-end

이 샘플에서는 다음 이미지가 응용 프로그램의 그릴 폴더에 로드 되었습니다.

| ![자주색 화살표 아이콘 이미지](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                           `purple-arrow-right.png`                       |

아래 스크린샷은 화살표 아이콘이 있는 선을 표시 하는 위의 코드를 보여 줍니다.

![줄 계층에서 화살표가 렌더링 되는 데이터 드라이브 스타일 선으로 매핑](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
