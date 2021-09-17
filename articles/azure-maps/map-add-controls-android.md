---
title: Android 지도에 컨트롤 추가 | Microsoft Azure Maps
description: Microsoft Azure Maps Android SDK에서 지도에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5599efb61c02c05ea3908d660ca0f1c11bea1ca7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425305"
---
# <a name="add-controls-to-a-map-android-sdk"></a>지도에 컨트롤 추가(Android SDK)

이 문서에서는 지도에 UI 컨트롤을 추가하는 방법을 보여 줍니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 지도를 확대/축소하는 단추를 추가합니다. 다음 코드 샘플은 `ZoomControl` 클래스의 인스턴스를 만들고 지도에 추가합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

아래 스크린샷은 지도에 로드된 확대/축소 컨트롤입니다.

![지도에 추가된 확대/축소 컨트롤](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 수평을 기준으로 지도에 피치를 기울이기 위한 단추를 추가합니다. 다음 코드 샘플에서는 `PitchControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

아래 스크린샷은 지도에 로드된 피치 컨트롤입니다.

![지도에 추가된 피치 컨트롤](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>나침반 컨트롤 추가

나침반 컨트롤은 지도를 회전하는 단추를 추가합니다. 다음 코드 샘플에서는 `CompassControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

아래 스크린샷은 지도에 로드된 나침반 컨트롤입니다.

![지도에 추가된 나침반 컨트롤](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>트래픽 컨트롤 추가

트래픽 컨트롤은 지도에 트래픽 데이터를 표시/숨기는 단추를 추가합니다. 다음 코드 샘플에서는 `TrafficControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

아래 스크린샷은 지도에 로드된 트래픽 컨트롤입니다.

![지도에 추가된 트래픽 컨트롤](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 배치하고 한 번에 모두 지도에 추가하고 지도의 동일한 영역에 배치하여 개발을 간소화할 수 있습니다. 다음 코드에서는 이 방법을 사용하여 지도에 표준 탐색 컨트롤을 추가합니다.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

아래 스크린샷에서는 지도에 로드된 모든 컨트롤을 보여 줍니다. 지도에 추가되는 순서가 표시되는 순서입니다.

![지도에 추가된 모든 컨트롤](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
