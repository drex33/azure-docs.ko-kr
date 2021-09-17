---
title: Android 맵에서 맵 이벤트 처리 | Microsoft Azure 맵
description: 사용자가 맵과 상호 작용할 때 발생하는 이벤트에 대해 알아봅니다. 지원되는 모든 맵 이벤트 목록을 봅니다. Azure Maps Android SDK를 사용하여 이벤트를 처리하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 49a06fdd2c8025515fd42124aa7d00f27d8c5978
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425719"
---
# <a name="interact-with-the-map-android-sdk"></a>맵과 상호 작용(Android SDK)

이 문서에서는 맵 이벤트 관리자를 사용하는 방법을 보여 줍니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

맵은 `events` 속성을 통해 모든 이벤트를 관리합니다. 다음 표에서는 지원되는 모든 맵 클래스 이벤트를 보여 줍니다.

| 이벤트                  | 이벤트 처리기 형식 | Description |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>맵이 "유휴" 상태가 되기 전에 렌더링된 마지막 프레임 후에 발생합니다.<ul><li>진행 중인 카메라 전환이 없습니다.</li><li>현재 요청된 모든 타일이 로드되었습니다.</li><li>모든 페이드/전환 애니메이션이 완료되었습니다.</li></ul></p> |
| `OnCameraMove`         | `()`                 | 사용자 상호 작용이나 메서드의 결과로 한 보기에서 다른 보기로 애니메이션 전환이 수행되는 동안 반복적으로 발생합니다. |
| `OnCameraMoveCanceled` | `()`                 | 카메라에 대한 이동 요청이 취소된 경우에 발생합니다. |
| `OnCameraMoveStarted`  | `(int reason)`       | 사용자 상호 작용이나 메서드의 결과로 맵이 한 보기에서 다른 보기로의 전환을 시작하기 전에 발생합니다. 이벤트 수신기의 `reason` 인수는 카메라 이동이 시작된 방법에 대한 세부 정보를 제공하는 정수 값을 반환합니다. 다음 목록에서는 가능한 이유를 간략하게 설명합니다.<ul><li>1: 제스처</li><li>2: 개발자 애니메이션</li><li>3: API 애니메이션</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | 맵의 포인팅 디바이스를 눌렀다가 동일한 지점에서 놓을 때 발생합니다. 이 이벤트 처리기는 이벤트를 사용하거나 다른 이벤트 수신기에 추가로 전달해야 하는지 여부를 나타내는 부울 값을 반환합니다. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | 기능에서 포인팅 디바이스를 눌렀다가 동일한 지점에서 놓을 때 발생합니다. 이 이벤트 처리기는 이벤트를 사용하거나 다른 이벤트 수신기에 추가로 전달해야 하는지 여부를 나타내는 부울 값을 반환합니다. |
| `OnLayerAdded` | `(Layer layer)` | 맵에 계층이 추가될 때 발생합니다. |
| `OnLayerRemoved` | `(Layer layer)` | 맵에서 계층이 제거될 때 발생합니다. |
| `OnLoaded` | `()` | 필요한 모든 리소스를 다운로드하고 맵의 시각적으로 완전한 최초 렌더링이 발생한 후 즉시 발생합니다. |
| `OnLongClick`          | `(double lat, double lon): boolean` | 맵을 누르고 잠시 동안 기다린 다음 지도의 동일한 지점에서 해제할 때 발생합니다. 이 이벤트 처리기는 이벤트를 사용하거나 다른 이벤트 수신기에 추가로 전달해야 하는지 여부를 나타내는 부울 값을 반환합니다. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | 맵을 누르고 잠시 동안 기다린 다음 기능의 동일한 지점에서 해제할 때 발생합니다. 이 이벤트 처리기는 이벤트를 사용하거나 다른 이벤트 수신기에 추가로 전달해야 하는지 여부를 나타내는 부울 값을 반환합니다. |
| `OnReady`              | `(AzureMap map)`     | 처음에 맵이 로드될 때 발생하거나 앱 방향이 변경되고 필요한 최소 맵 리소스가 로드된 다음 맵을 프로그래밍 방식으로 상호 작용할 준비가 될 때 발생합니다. |
| `OnSourceAdded` | `(Source source)` | `DataSource` 또는 `VectorTileSource`가 맵에 추가될 때 발생합니다. |
| `OnSourceRemoved` | `(Source source)` | 맵에서 `DataSource` 또는 `VectorTileSource`가 제거될 때 발생합니다. |
| `OnStyleChange` | `()` | 맵의 스타일이 로드되거나 변경될 때 발생합니다. |

다음 코드에서는 `OnClick` , `OnFeatureClick` 및 `OnCameraMove` 이벤트를 맵에 추가하는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

자세한 내용은 맵 및 트리거 이벤트와 상호 작용하는 방법에 대한 [맵 탐색](how-to-use-android-map-control-library.md#navigating-the-map) 설명서를 참조하세요.

## <a name="scope-feature-events-to-layer"></a>계층에 대한 기능 이벤트 범위

맵에 `OnFeatureClick` 또는 `OnLongFeatureClick` 이벤트를 추가할 때 계층 인스턴스나 계층 ID를 두 번째 매개 변수로 전달할 수 있습니다. 계층이 전달될 때 해당 계층에서 이벤트가 발생하는 경우에만 이벤트가 시작됩니다. 계층으로 범위가 지정된 이벤트는 기호, 거품형, 선형 및 다각형 계층이 지원됩니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>다음 단계

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵 탐색](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
