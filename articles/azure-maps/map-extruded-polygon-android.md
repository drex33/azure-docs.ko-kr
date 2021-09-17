---
title: Android 맵에 입체 계층 추가 | Microsoft Azure Maps
description: 다각형 입체 계층을 Microsoft Azure Maps Android SDK에 추가하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 85e8f7e2d4d6dad05484131cc7c0cf0932fee407
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432705"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>다각형 입체 계층을 맵에 추가(Android SDK)

이 문서에서는 다각형 입체 계층을 사용하여 `Polygon` 및 `MultiPolygon` 기능 기하 도형을 입체 도형으로 렌더링하는 방법을 보여 줍니다.

## <a name="use-a-polygon-extrusion-layer"></a>다각형 입체 계층 사용

다각형 입체 계층을 데이터 원본에 연결합니다. 그런 다음 맵에 로드합니다. 다각형 입체 계층은 `Polygon` 및 `MultiPolygon` 기능의 영역을 입체 도형으로 렌더링합니다. 다각형 입체 계층의 `height` 및 `base` 속성은 **미터** 단위로 지면에서 입체 도형까지의 기본 거리를 정의합니다. 다음 코드에서는 다각형을 만들어 데이터 원본에 추가하고 다각형 입체 계층 클래스를 사용하여 렌더링하는 방법을 보여 줍니다.

> [!Note]
> 다각형 입체 계층에 정의된 `base` 값은 `height`의 값보다 작거나 같아야 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.958383, 40.800279),
            Point.fromLngLat(-73.981547, 40.768459),
            Point.fromLngLat(-73.981246, 40.767761),
            Point.fromLngLat(-73.973618, 40.764616),
            Point.fromLngLat(-73.973060, 40.765128),
            Point.fromLngLat(-73.972599, 40.764908),
            Point.fromLngLat(-73.949446, 40.796584),
            Point.fromLngLat(-73.949661, 40.797088),
            Point.fromLngLat(-73.957815, 40.800523),
            Point.fromLngLat(-73.958383, 40.800279)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.958383, 40.800279),
                Point.fromLngLat(-73.981547, 40.768459),
                Point.fromLngLat(-73.981246, 40.767761),
                Point.fromLngLat(-73.973618, 40.764616),
                Point.fromLngLat(-73.973060, 40.765128),
                Point.fromLngLat(-73.972599, 40.764908),
                Point.fromLngLat(-73.949446, 40.796584),
                Point.fromLngLat(-73.949661, 40.797088),
                Point.fromLngLat(-73.957815, 40.800523),
                Point.fromLngLat(-73.958383, 40.800279)
            )
        )
    )
)

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
val layer = PolygonExtrusionLayer(
    source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
)

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷에서는 다각형 입체 계층을 사용하여 세로로 확장된 다각형을 렌더링하는 위의 코드를 보여 줍니다.

![다각형 입체 계층을 사용하여 세로로 확장된 맵](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>데이터 중심 다각형 추가

다각형 입체 계층을 사용하여 단계구분도 지도를 렌더링할 수 있습니다. 입체 계층의 `height` 및 `fillColor` 속성을 `Polygon` 및 `MultiPolygon` 기능 기하 도형의 통계 변수 측정값으로 설정합니다. 다음 코드 샘플은 상태별 인구 밀도의 측정값을 기반으로 하는 미국의 입체 단계구분도 지도를 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json");

//Add data source to the map.
map.sources.add(source);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json")

//Add data source to the map.
map.sources.add(source)

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
val layer = PolygonExtrusionLayer(
    source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
)

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels")
```

::: zone-end

다음 스크린샷에서는 인구 밀도를 기준으로 입체 다각형으로 색이 지정되고 세로로 확장된 미국 단계구분도 지도를 보여 줍니다.

![인구 밀도를 기준으로 입체 다각형으로 색이 지정되고 세로로 확장된 미국 단계구분도 지도를 보여 줍니다.](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
