---
title: iOS 지도에 다각형 계층 추가
titleSuffix: Microsoft Azure Maps
description: 맵에 다각형 또는 원을 추가하는 방법을 알아봅니다. Azure 지도 iOS SDK를 사용하여 기하 도형을 사용자 지정하고 쉽게 업데이트하고 유지 관리하는 방법을 참조하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 09db829d485c08c349d0d5187d1c5332d65e8e1e
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219000"
---
# <a name="add-a-polygon-layer-to-the-map-in-the-ios-sdk-preview"></a>iOS SDK의 맵에 다각형 계층 추가(미리 보기)

이 문서에서는 다각형 계층을 사용하여 맵에 `Polygon` 및 `MultiPolygon` 기능 기하 도형의 영역을 렌더링하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 의 함수에 삽입할 수  `viewDidLoad` `ViewController` 있습니다.

## <a name="use-a-polygon-layer"></a>다각형 계층 사용

다각형 계층이 데이터 원본에 연결되고 맵에 로드되면 `Polygon` 및 `MultiPolygon` 기능을 사용하여 영역을 렌더링합니다. 다각형을 만들려면 데이터 원본에 추가하고 클래스를 사용하여 다각형 계층으로 `PolygonLayer` 렌더링합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a rectangular polygon.
source.add(geometry: Polygon([
    CLLocationCoordinate2D(latitude: 40.76799, longitude: -73.98235),
    CLLocationCoordinate2D(latitude: 40.80044, longitude: -73.95785),
    CLLocationCoordinate2D(latitude: 40.79680, longitude: -73.94928),
    CLLocationCoordinate2D(latitude: 40.76437, longitude: -73.97317),
    CLLocationCoordinate2D(latitude: 40.76799, longitude: -73.98235)
]))

// Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.insertLayer(
    PolygonLayer(source: source, options: [
        .fillColor(.red),
        .fillOpacity(0.7)
    ]),
    below: "labels"
)
```

다음 스크린샷에서는 다각형 계층을 사용하여 다각형 영역을 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-polygon-layer-map-ios/polygon.png" alt-text="다각형 계층을 사용하는 다각형을 보여 줍니다." lightbox="./media/ios-sdk/add-polygon-layer-map-ios/polygon.png":::

## <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 계층을 함께 사용

선 계층은 다각형의 윤곽선을 렌더링하는 데 사용됩니다. 다음 코드 샘플에서는 이전 예와 같이 다각형을 렌더링하지만, 이제는 선 계층을 추가합니다. 이 선 계층은 데이터 원본에 연결된 두 번째 계층입니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a rectangular polygon.
source.add(geometry: Polygon([
    CLLocationCoordinate2D(latitude: 40.76799, longitude: -73.98235),
    CLLocationCoordinate2D(latitude: 40.80044, longitude: -73.95785),
    CLLocationCoordinate2D(latitude: 40.79680, longitude: -73.94928),
    CLLocationCoordinate2D(latitude: 40.76437, longitude: -73.97317),
    CLLocationCoordinate2D(latitude: 40.76799, longitude: -73.98235)
]))

// Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.insertLayer(
    PolygonLayer(source: source, options: [
        .fillColor(UIColor(red: 0, green: 0.78, blue: 0.78, alpha: 0.5))
    ]),
    below: "labels"
)

// Create and add a line layer to render the outline of the polygon.
map.layers.addLayer(LineLayer(source: source, options: [
    .strokeColor(.red),
    .strokeWidth(2)
]))
```

다음 스크린샷에서는 선 계층을 사용하여 윤곽선이 렌더링된 다각형을 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-polygon-layer-map-ios/polygon-and-line.png" alt-text="선 계층을 사용하여 렌더링된 윤곽선이 있는 다각형을 보여주는 이미지." lightbox="./media/ios-sdk/add-polygon-layer-map-ios/polygon-and-line.png":::

> [!TIP]
> 선 계층을 사용하여 다각형을 개략화할 때 각 점 배열의 시작점과 끝점이 같도록 다각형의 모든 링을 닫아야 합니다. 이 작업을 수행하지 않으면 선 계층이 다각형의 마지막 점을 첫 번째 지점에 연결하지 못할 수 있습니다.

## <a name="fill-a-polygon-with-a-pattern"></a>패턴을 사용하여 다각형 채우기

색을 사용한 다각형 채우기 외에도 이미지 패턴을 사용하여 다각형을 채울 수 있습니다. 이미지 패턴을 맵 이미지 스프라이트 리소스에 로드한 다음 다각형 계층의 옵션을 사용하여 이 이미지를 `fillPattern` 참조합니다.

```swift
// Load an image pattern into the map image sprite.
map.images.add(UIImage(named: "fill-checker-red")!, withID: "fill-checker-red")

// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a polygon.
source.add(geometry: Polygon([
    CLLocationCoordinate2D(latitude: -20, longitude: -50),
    CLLocationCoordinate2D(latitude: 40, longitude: 0),
    CLLocationCoordinate2D(latitude: -20, longitude: 50),
    CLLocationCoordinate2D(latitude: -20, longitude: -50)
]))

// Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.insertLayer(
    PolygonLayer(source: source, options: [
        .fillPattern("fill-checker-red"),
        .fillOpacity(0.5)
    ]),
    below: "labels"
)
```

이 샘플의 경우 다음 이미지가 앱의 assets 폴더에 로드되었습니다.

| :::image type="content" source="./media/ios-sdk/add-polygon-layer-map-ios/fill-checker-red.png" alt-text="빨간색 검사기 채우기 패턴이 있는 다각형을 보여주는 이미지.":::
|:-----------------------------------------------------------------------:|
| `fill-checker-red.png`                                                  |

다음은 맵에서 채우기 패턴으로 다각형을 렌더링하는 위 코드의 스크린샷입니다.

:::image type="content" source="./media/ios-sdk/add-polygon-layer-map-ios/pattern.png" alt-text="맵에서 채우기 패턴으로 다각형을 렌더링하는 위의 코드를 보여주는 이미지입니다." lightbox="./media/ios-sdk/add-polygon-layer-map-ios/pattern.png":::

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

- [데이터 원본 만들기](create-data-source-ios-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-ios-sdk.md)
- [선 계층 추가](add-line-layer-map-ios.md)
- [다각형 입체 면 레이어 추가](add-polygon-extrusion-layer-map-ios.md)
