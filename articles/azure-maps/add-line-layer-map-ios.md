---
title: IOS 맵에 선 계층 추가
titleSuffix: Microsoft Azure Maps
description: 지도에 선을 추가 하는 방법에 대해 알아봅니다. Azure Maps iOS SDK를 사용 하 여 지도에 선 계층을 추가 하 고 기호 및 색 그라데이션을 사용 하 여 줄을 사용자 지정 하는 예제를 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eba1a8c1f42f2cb6b611dd0929d8ab0877b2925d
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218970"
---
# <a name="add-a-line-layer-to-the-map-in-the-ios-sdk-preview"></a>IOS SDK에서 지도에 선 계층 추가 (미리 보기)

선 계층은 맵에서 `LineString` 및 `MultiLineString` 피처를 경로로 렌더링하는 데 사용할 수 있습니다. 선 계층을 사용하여 `Polygon` 및 `MultiPolygon` 기능의 개요를 렌더링할 수도 있습니다. 데이터 원본은 선 계층에 연결되어 렌더링할 데이터를 제공합니다.

> [!TIP]
> 기본적으로 선 계층은 다각형의 좌표와 데이터 원본의 선을 렌더링합니다. LineString geometry 기능만 렌더링 하도록 계층을 제한 하려면 `filter` 계층의 옵션을로 설정 `NSPredicate(format: "%@ == \"LineString\"", NSExpression.geometryTypeAZMVariable)` 합니다. MultiLineString 기능도 포함 하려면를 사용 `NSCompoundPredicate` 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은의 함수에 삽입할 수 있습니다  `viewDidLoad` `ViewController` .

## <a name="add-a-line-layer"></a>선 계층 추가

다음 코드는 선을 만드는 방법을 보여 줍니다. 데이터 소스에 줄을 추가한 다음 클래스를 사용 하 여 선 계층으로 렌더링 `LineLayer` 합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a list of points.
let points = [
    CLLocationCoordinate2D(latitude: 40.74327, longitude: -73.97234),
    CLLocationCoordinate2D(latitude: 40.75680, longitude: -74.00442)
]

// Create a Polyline geometry and add it to the data source.
source.add(geometry: Polyline(points))

// Create a line layer and add it to the map.
let layer = LineLayer(source: source, options: [
    .strokeWidth(5),
    .strokeColor(.blue)
])
map.layers.addLayer(layer)
```

다음 스크린샷은 줄 계층에서 선을 렌더링 하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-line-layer-map-ios/simple.png" alt-text="지도에서 선 계층에 표시 되는 간단한 선입니다.":::

## <a name="data-driven-line-style"></a>데이터 기반 선 스타일

다음 코드는 두 개의 선 기능을 만들고 각 줄에 속성으로 속도 제한 값을 추가 합니다. 선 계층은 속도 제한 값을 기준으로 하는 선의 데이터 드라이브 스타일 식 색을 사용 합니다. 선 데이터가도로 오버레이 되므로 아래 코드는도로 레이블을 명확 하 게 읽을 수 있도록 레이블 계층 아래에 선 계층을 추가 합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a line feature.
let feature = Feature(Polyline([
    CLLocationCoordinate2D(latitude: 47.704033, longitude: -122.131821),
    CLLocationCoordinate2D(latitude: 47.703678, longitude: -122.099919)
]))

// Add a property to the feature.
feature.addProperty("speedLimitMph", value: 35)

// Add the feature to the data source.
source.add(feature: feature)

// Create a second line feature.
let feature2 = Feature(Polyline([
    CLLocationCoordinate2D(latitude: 47.708265, longitude: -122.126662),
    CLLocationCoordinate2D(latitude: 47.703980, longitude: -122.126877)
]))

// Add a property to the second feature.
feature2.addProperty("speedLimitMph", value: 15)

// Add the second feature to the data source.
source.add(feature: feature2)

// Create a line layer and add it to the map.
let stops: [Int: UIColor] = [
    0: .green,
    30: .yellow,
    60: .red
]
let colorExpression = NSExpression(
    forAZMInterpolating: NSExpression(forKeyPath: "speedLimitMph"),
    curveType: .linear,
    parameters: nil,
    stops: NSExpression(forConstantValue: stops)
)

let layer = LineLayer(source: source, options: [
    .strokeWidth(5),
    .strokeColor(from: colorExpression)
])

map.layers.insertLayer(layer, below: "labels")
```

다음 스크린샷에서는 줄 기능에서 속성을 기반으로 하는 데이터 기반 스타일 식에서 색이 검색 되는 위의 코드를 선 계층에서 렌더링 하는 방법을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-line-layer-map-ios/data-driven.png" alt-text="지도에 선 계층을 추가 합니다.":::

## <a name="add-a-stroke-gradient-to-a-line"></a>선에 스트로크 그라데이션 추가

선에 단일 스트로크 색을 적용할 수 있습니다. 색 그라데이션으로 선을 채워서 한 선분에서 다음 선분으로의 전환을 표시할 수도 있습니다. 예를 들어 선 그라데이션을 사용하여 시간 및 거리에 따른 변경이나 연결된 개체 선의 다른 온도를 나타낼 수 있습니다. 줄에이 기능을 적용 하려면 데이터 원본에 옵션이로 설정 되어 있어야 하 `lineMetrics` `true` 고 색 그라데이션 식이 줄의 옵션으로 전달 될 수 있습니다 `strokeGradient` . 스트로크 그라데이션 식은 계산된 선 메트릭을 식에 노출하는 `NSExpression.lineProgressAZMVariable` 데이터 식을 참조해야 합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource(options: [
    // Enable line metrics on the data source. This is needed to enable support for strokeGradient.
    .lineMetrics(true)
])
map.sources.add(source)

// Create a line and add it to the data source.
source.add(geometry: Polyline([
    CLLocationCoordinate2D(latitude: 47.63208, longitude: -122.18822),
    CLLocationCoordinate2D(latitude: 47.63196, longitude: -122.18204),
    CLLocationCoordinate2D(latitude: 47.62976, longitude: -122.17243),
    CLLocationCoordinate2D(latitude: 47.63023, longitude: -122.16419),
    CLLocationCoordinate2D(latitude: 47.62942, longitude: -122.15852),
    CLLocationCoordinate2D(latitude: 47.62988, longitude: -122.15183),
    CLLocationCoordinate2D(latitude: 47.63451, longitude: -122.14256),
    CLLocationCoordinate2D(latitude: 47.64041, longitude: -122.13483),
    CLLocationCoordinate2D(latitude: 47.64422, longitude: -122.13466),
    CLLocationCoordinate2D(latitude: 47.65440, longitude: -122.13844),
    CLLocationCoordinate2D(latitude: 47.66515, longitude: -122.13277),
    CLLocationCoordinate2D(latitude: 47.66712, longitude: -122.12779),
    CLLocationCoordinate2D(latitude: 47.66712, longitude: -122.11595),
    CLLocationCoordinate2D(latitude: 47.66735, longitude: -122.11063),
    CLLocationCoordinate2D(latitude: 47.67035, longitude: -122.10668),
    CLLocationCoordinate2D(latitude: 47.67498, longitude: -122.10565)
]))

// Create a line layer and add it to the map.
let stops: [Double: UIColor] = [
    0: .blue,
    0.1: UIColor(red: 0.25, green: 0.41, blue: 1, alpha: 1), // Royal Blue
    0.3: .cyan,
    0.5: UIColor(red: 0, green: 1, blue: 0, alpha: 1), // Lime
    0.7: .yellow,
    1: .red
]
let colorExpression = NSExpression(
    forAZMInterpolating: NSExpression.lineProgressAZMVariable,
    curveType: .linear,
    parameters: nil,
    stops: NSExpression(forConstantValue: stops)
)

map.layers.addLayer(LineLayer(source: source, options: [
    .strokeWidth(5),
    .strokeGradient(from: colorExpression)
]))
```

다음 스크린샷은 그라데이션 스트로크 색을 사용 하 여 렌더링 된 선을 표시 하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-line-layer-map-ios/gradient.png" alt-text="지도에 그라데이션 선 계층을 추가 합니다.":::

## <a name="add-symbols-along-a-line"></a>선을 따라 기호 추가

이 샘플에서는 맵의 선을 따라 화살표 아이콘을 추가하는 방법을 보여 줍니다. 기호 계층을 사용 하는 경우 `symbolPlacement` 옵션을로 설정 `.line` 합니다. 이 옵션은 선을 따라 기호를 렌더링하고 아이콘(0도 = 오른쪽)을 회전합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add(UIImage(named: "purple-arrow-right")!, withID: "arrow-icon")

// Create a line and add it to the data source.
source.add(geometry: Polyline([
    CLLocationCoordinate2D(latitude: 47.63208, longitude: -122.18822),
    CLLocationCoordinate2D(latitude: 47.63196, longitude: -122.18204),
    CLLocationCoordinate2D(latitude: 47.62976, longitude: -122.17243),
    CLLocationCoordinate2D(latitude: 47.63023, longitude: -122.16419),
    CLLocationCoordinate2D(latitude: 47.62942, longitude: -122.15852),
    CLLocationCoordinate2D(latitude: 47.62988, longitude: -122.15183),
    CLLocationCoordinate2D(latitude: 47.63451, longitude: -122.14256),
    CLLocationCoordinate2D(latitude: 47.64041, longitude: -122.13483),
    CLLocationCoordinate2D(latitude: 47.64422, longitude: -122.13466),
    CLLocationCoordinate2D(latitude: 47.65440, longitude: -122.13844),
    CLLocationCoordinate2D(latitude: 47.66515, longitude: -122.13277),
    CLLocationCoordinate2D(latitude: 47.66712, longitude: -122.12779),
    CLLocationCoordinate2D(latitude: 47.66712, longitude: -122.11595),
    CLLocationCoordinate2D(latitude: 47.66735, longitude: -122.11063),
    CLLocationCoordinate2D(latitude: 47.67035, longitude: -122.10668),
    CLLocationCoordinate2D(latitude: 47.67498, longitude: -122.10565)
]))

// Create a line layer and add it to the map.
map.layers.addLayer(LineLayer(source: source, options: [
    .strokeWidth(5),
    .strokeColor(.purple)
]))

// Create a symbol layer and add it to the map.
map.layers.addLayer(SymbolLayer(source: source, options: [
    // Space symbols out along line.
    .symbolPlacement(.line),

    // Spread the symbols out 100 points apart.
    .symbolSpacing(100),

    // Use the arrow icon as the symbol.
    .iconImage("arrow-icon"),

    // Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    .iconAllowOverlap(true),

    // Center the symbol icon.
    .iconAnchor(.center),

    // Scale the icon size.
    .iconSize(0.8)
]))
```

이 샘플에서는 다음 이미지가 응용 프로그램의 자산 폴더에 로드 되었습니다.

| ![purple_arrow_right](./media/ios-sdk/add-line-layer-map-ios/purple-arrow-right.png) |
|:------------------------------------------------------------------------------------:|
|                           `purple-arrow-right.png`                                   |

아래 스크린샷은 화살표 아이콘이 있는 선을 표시 하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-line-layer-map-ios/symbol.png" alt-text="오른쪽을 가리키는 자주색 화살표 아이콘이 표시 된 선입니다.":::

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

* [데이터 원본 만들기](create-data-source-ios-sdk.md)
* [데이터 기반 스타일 식 사용](data-driven-style-expressions-ios-sdk.md)
* [다각형 계층 추가](add-polygon-layer-map-ios.md)
