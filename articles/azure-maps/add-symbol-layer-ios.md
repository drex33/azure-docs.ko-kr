---
title: IOS 맵에 기호 계층 추가
titleSuffix: Microsoft Azure Maps
description: 맵에 마커를 추가하는 방법을 알아봅니다. Azure Maps iOS SDK를 사용 하 여 데이터 원본의 지점 기반 데이터를 포함 하는 기호 계층을 추가 하는 예제를 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 383a865b155320ab65a264584276bc54e01eb785
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219043"
---
# <a name="add-a-symbol-layer-in-the-ios-sdk-preview"></a>IOS SDK에서 기호 계층 추가 (미리 보기)

이 문서에서는 Azure Maps iOS SDK를 사용 하 여 데이터 원본의 지점 데이터를 맵의 기호 계층으로 렌더링 하는 방법을 보여 줍니다. 기호 계층은 점을 맵의 이미지 및 텍스트로 렌더링합니다.

> [!TIP]
> 기본적으로 기호 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기하 도형 기능만 렌더링하도록 계층을 제한하려면 계층의 `filter` 옵션을 `NSPredicate(format: "%@ == \"Point\"", NSExpression.geometryTypeAZMVariable)`로 설정합니다. MultiPoint 기능도 포함 하려면를 사용 `NSCompoundPredicate` 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은의 함수에 삽입할 수 있습니다  `viewDidLoad` `ViewController` .

## <a name="add-a-symbol-layer"></a>기호 레이어 추가

맵에 기호 계층을 추가하려면 몇 가지 단계를 수행해야 합니다. 먼저 데이터 원본을 만들어 맵에 추가합니다. 기호 계층을 만듭니다. 그런 다음, 데이터 원본을 기호 계층에 전달하여 데이터 원본에서 데이터를 검색합니다. 마지막으로, 렌더링할 수 있도록 데이터를 데이터 원본에 추가합니다.

아래의 코드는 로드된 후 맵에 추가해야 하는 내용을 보여 줍니다. 이 샘플에서는 기호 계층을 사용하여 맵에 단일 점을 렌더링합니다.

```swift
//Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(geometry: Point(CLLocationCoordinate2D(latitude: 0, longitude: 0)))

//Create a symbol layer to render icons and/or text at points on the map.
let layer = SymbolLayer(source: source)

//Add the layer to the map.
map.layers.addLayer(layer)
```

맵에 추가할 수 있는 점 데이터 유형은 다음의 세 가지가 있습니다.

- GeoJSON 점 기하 도형 - 이 개체는 점의 좌표만 포함하고, 그 외에는 아무것도 포함하지 않습니다. `Point`Init 메서드를 사용 하 여 이러한 개체를 쉽게 만들 수 있습니다.
- GeoJSON MultiPoint 기하 도형 - 이 개체는 여러 점의 좌표를 포함하고, 그 외에는 아무것도 포함하지 않습니다. 좌표 배열을 클래스에 전달 `PointCollection` 하 여 이러한 개체를 만듭니다.
- GeoJSON 기능 - 이 개체는 모든 GeoJSON 기하 도형 및 기하 도형에 연결된 메타데이터를 포함하는 속성 집합으로 구성됩니다.

자세한 정보는 데이터 만들기 및 맵에 추가하기에 대한 [데이터 원본 만들기](create-data-source-ios-sdk.md) 문서를 참조하세요.

다음 샘플 코드는 GeoJSON 점 기하 도형을 만들고 GeoJSON 기능에 전달하며, 해당 속성에 `title` 값이 추가됩니다. `title`속성은 지도의 기호 아이콘 아래에 텍스트로 표시 됩니다.

```swift
// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a point feature.
let feature = Feature(Point(CLLocationCoordinate2D(latitude: 0, longitude: 0)))

// Add a property to the feature.
feature.addProperty("title", value: "Hello World!")

// Add the feature to the data source.
source.add(feature: feature)

// Create a symbol layer to render icons and/or text at points on the map.
let layer = SymbolLayer(
    source: source,
    options: [
        // Get the title property of the feature and display it on the map.
        .textField(from: NSExpression(forKeyPath: "title")),

        // Place the text below so it doesn't overlap the icon.
        .textAnchor(.top)
    ]
)

// Add the layer to the map.
map.layers.addLayer(layer)
```

다음 스크린샷은 기호 계층의 아이콘과 텍스트 레이블을 사용하여 점 형상을 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-symbol-layer-ios/ios-map-pin.png" alt-text="지도에 추가 된 확대/축소 컨트롤을 보여 주는 스크린샷":::

> [!TIP]
> 기본적으로 기호 계층은 겹치는 기호를 숨겨서 기호 렌더링을 최적화합니다. 확대하면 숨겨진 기호가 표시됩니다. 이 기능을 사용하지 않고 모든 기호를 항상 렌더링하려면 `iconAllowOverlap` 및 `textAllowOverlap` 옵션을 `true`로 설정합니다.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>기호 계층에 사용자 지정 아이콘 추가

기호 레이어는 OpenGL을 사용 하 여 렌더링 됩니다. 따라서 아이콘 이미지와 같은 모든 리소스를 OpenGL 컨텍스트에 로드 해야 합니다. 이 샘플에서는 맵 리소스에 사용자 지정 아이콘을 추가하는 방법을 보여 줍니다. 이 아이콘은 맵에서 사용자 지정 기호를 사용하여 점 데이터를 렌더링하는 데 사용됩니다. 기호 계층의 `textField` 속성에는 식을 지정해야 합니다. 이 경우, 온도 속성을 렌더링하려고 합니다. 또한 추가 하 고 싶습니다 `"°F"` . 식을 사용 하 여이 연결을 수행할 수 있습니다.

```swift
NSExpression(
    forAZMFunctionJoin: [
        NSExpression(forKeyPath: "temperature"),
        NSExpression(forConstantValue: "°F")
    ]
)
```

```swift
// Load a custom icon image into the image sprite of the map.
map.images.add(UIImage(named: "showers")!, withID: "my-custom-icon")

// Create a data source and add it to the map.
let source = DataSource()
map.sources.add(source)

// Create a point feature.
let feature = Feature(Point(CLLocationCoordinate2D(latitude: 40.75773, longitude: -73.985708)))

// Add a property to the feature.
feature.addProperty("temperature", value: 64)

// Add the feature to the data source.
source.add(feature: feature)

// Create a symbol layer to render icons and/or text at points on the map.
let layer = SymbolLayer(
    source: source,
    options: [
        .iconImage("my-custom-icon"),
        .iconSize(0.5),
        // Get the title property of the feature and display it on the map.
        .textField(
            from: NSExpression(
                forAZMFunctionJoin: [
                    NSExpression(forKeyPath: "temperature"),
                    NSExpression(forConstantValue: "°F")
                ]
            )
        ),
        .textOffset(CGVector(dx: 0, dy: -1.5))
    ]
)

// Add the layer to the map.
map.layers.addLayer(layer)
```

이 샘플에서 다음 이미지는 앱의 자산 폴더에 로드 됩니다.

| :::image type="content" source="./media/ios-sdk/add-symbol-layer-ios/showers.png" alt-text="비 표시를 보여 주는 날씨 아이콘을 보여 주는 스크린샷" border="false"::: |
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

다음 스크린샷은 기호 계층과, 사용자 지정 아이콘 및 서식 있는 텍스트 레이블을 사용하여 점 형상을 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-symbol-layer-ios/ios-custom-symbol-layer.png" alt-text="지점 기능에 대해 사용자 지정 아이콘 및 서식 있는 텍스트 레이블을 표시 하는 기호 계층을 사용 하 여 점이 렌더링 된 지도를 보여 주는 스크린샷":::

> [!TIP]
> 기호 계층이 있는 텍스트만 렌더링하려는 경우 아이콘 옵션의 `iconImage` 속성을 `nil`으로 선택하여 아이콘을 숨길 수 있습니다.

## <a name="predefined-symbol-marker-icons"></a>미리 정의 된 기호 표식 아이콘

처음에 map에는 맵의 이미지 스프라이트에 이미 로드 되어 있는 기본 제공 기본 표식 아이콘이 있습니다. 옵션으로 설정 되지 않은 경우 기본적으로 사용 됩니다 `iconImage` . 수동으로 수행 해야 하 `"marker-default"` 는 경우 옵션으로 설정 `iconImage` 합니다.

```swift
let layer = SymbolLayer(source: source, options: [.iconImage("marker-default")])
```

또한 Azure Maps iOS SDK는 기본 (진한 파란색) 표식 아이콘의 미리 정의 된 색 변형 집합을 제공 합니다. 이러한 표식 아이콘에 액세스 하려면 클래스에서 정적 변수를 사용 `UIImage` 합니다 (예:) `UIImage.azm_markerRed` .

미리 정의 **되지 않은 기본** 표식 이미지를 사용 하려면 지도의 이미지 스프라이트에 추가 해야 합니다.

```swift
// Load a non-default predefined icon into the image sprite of the map.
map.images.add(.azm_markerRed, withID: "marker-red")

// Create a symbol layer to render icons and/or text at points on the map.
let layer = SymbolLayer(source: source, options: [.iconImage("marker-red")])
```

아래 코드에서는 클래스의 정적 변수로 사용할 수 있는 모든 기본 제공 아이콘 이미지를 나열 합니다 `UIImage` .

```swift
UIImage.azm_markerDefault // Dark blue
UIImage.azm_markerBlack
UIImage.azm_markerBlue
UIImage.azm_markerRed
UIImage.azm_markerYellow
```

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

- [데이터 원본 만들기](Create-data-source-ios-sdk.md)
- [클러스터 지점 데이터](clustering-point-data-ios-sdk.md)
- [거품형 계층 추가](add-bubble-layer-map-ios.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-ios-sdk.md)
- [기능 정보 표시](display-feature-information-ios-sdk.md)
