---
title: IOS 맵에 거품형 계층 추가
titleSuffix: Microsoft Azure Maps
description: 맵의 지점을 고정된 크기의 원으로 렌더링하는 방법을 알아봅니다. 이 목적을 위해 Azure Maps iOS SDK를 사용 하 여 거품형 계층을 추가 하 고 사용자 지정 하는 방법을 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 6c505cdb927392b08375765f37f17acf2acbf499
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219063"
---
# <a name="add-a-bubble-layer-to-a-map-in-the-ios-sdk-preview"></a>IOS SDK에서 지도에 거품형 계층 추가 (미리 보기)

이 문서에서는 맵에 거품형 계층으로 데이터 원본에서 지점 데이터를 렌더링할 수 있는 방법을 보여줍니다. 거품형 레이어는 고정 점 반지름이 있는 지도에서 점을 원으로 렌더링 합니다.

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 계층이 점 기하 도형 기능만 렌더링하도록 제한하려면 계층의 `filter` 옵션을 `NSPredicate(format: "%@ == \"Point\"", NSExpression.geometryTypeAZMVariable)`로 설정합니다. MultiPoint 기능도 포함 하려면를 사용 `NSCompoundPredicate` 합니다.  

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은의 함수에 삽입할 수 있습니다  `viewDidLoad` `ViewController` .

## <a name="add-a-bubble-layer"></a>거품형 레이어 추가

다음 코드는 지점 배열을 데이터 원본에 로드합니다. 그러면 데이터 요소가 거품형 계층에 연결됩니다. 거품형 계층은 5 개의 점이 있는 각 거품의 반지름과 흰색의 채우기 색을 렌더링 합니다. 그리고, 파란색의 스트로크 색 및 6 점의 스트로크 너비입니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create point locations.
let points = [
    Point(CLLocationCoordinate2D(latitude: 40.75773, longitude: -73.985708)),
    Point(CLLocationCoordinate2D(latitude: 40.76542, longitude: -73.985600)),
    Point(CLLocationCoordinate2D(latitude: 40.77900, longitude: -73.985550)),
    Point(CLLocationCoordinate2D(latitude: 40.74859, longitude: -73.975550)),
    Point(CLLocationCoordinate2D(latitude: 40.78859, longitude: -73.968900))
]

// Add multiple points to the data source.
source.add(geometries: points)

// Create a bubble layer to render the filled in area of the circle, and add it to the map.
let layer = BubbleLayer(source: source, options: [
    .bubbleRadius(5),
    .bubbleColor(.white),
    .bubbleStrokeColor(.blue),
    .bubbleStrokeWidth(6)
])
map.layers.addLayer(layer)
```

다음 스크린샷에서는 위 코드가 거품형 계층에서 점을 렌더링하는 것을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-bubble-layer-map-ios/5-points.png" alt-text="지도에서 거품형 계층에 렌더링 된 5 개의 요소입니다.":::

## <a name="show-labels-with-a-bubble-layer"></a>거품형 계층을 사용하여 레이블 표시

이 코드는 거품형 계층을 사용하여 맵에 지점을 렌더링하는 방법을 보여줍니다. 기호 계층을 사용하여 레이블을 렌더링하는 방법도 있습니다. 기호 계층의 아이콘을 숨기려면 `iconImage` 옵션을 `nil`으로 설정합니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Add a data point to the map.
source.add(geometry: Point(CLLocationCoordinate2D(latitude: 47.627631, longitude: -122.336641)))

// Add a bubble layer.
map.layers.addLayer(
    BubbleLayer(source: source, options: [
        .bubbleRadius(5),
        .bubbleColor(.white),
        .bubbleStrokeColor(.blue),
        .bubbleStrokeWidth(6)
    ])
)

// Add a symbol layer to display text, hide the icon image.
map.layers.addLayer(
    SymbolLayer(source: source, options: [
        .iconImage(nil),
        .textField("Museum of History & Industry (MOHAI)"),
        .textColor(.blue),
        .textOffset(CGVector(dx: 0, dy: -2.2))
    ])
)
```

다음 스크린샷에서는 거품형 계층에서 점을 렌더링하고 기호 계층을 사용하여 점의 텍스트 레이블을 렌더링하는 위 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-bubble-layer-map-ios/text.png" alt-text="지도에 거품형 계층을 추가 합니다.":::

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

- [데이터 원본 만들기](create-data-source-ios-sdk.md)
- [클러스터 지점 데이터](clustering-point-data-ios-sdk.md)
- [기호 계층 추가](add-symbol-layer-ios.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-ios-sdk.md)
- [기능 정보 표시](display-feature-information-ios-sdk.md)
