---
title: iOS 지도에 열 지도 계층 추가
titleSuffix: Microsoft Azure Maps
description: 열 지도를 만드는 방법을 알아봅니다. Azure 지도 iOS SDK를 사용하여 지도에 열 지도 계층을 추가하는 방법을 참조하세요. 열 지도 계층을 사용자 지정하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0b4c06db10ea19e01cdf92b4526f00fee5dea8da
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218948"
---
# <a name="add-a-heat-map-layer-in-the-ios-sdk-preview"></a>iOS SDK에서 열 지도 계층 추가(미리 보기)

지점 밀도 맵이라고도 하는 열 지도는 데이터 시각화의 한 형식입니다. 이는 색 범위를 사용하여 데이터의 밀도를 나타내고 지도에 데이터 “핫 스폿”을 표시하는 데 사용됩니다. 열 지도는 많은 수의 요소가 있는 데이터 세트를 렌더링하는 좋은 방법입니다.

수만 개의 요소를 기호로 렌더링하면 대부분의 지도 영역을 덮을 수 있습니다. 이 경우 많은 기호가 겹칠 수 있습니다. 그러면 데이터를 정확하게 이해하기가 어렵습니다. 그러나 열 지도와 동일한 데이터 세트를 시각화하면 각 데이터 요소의 밀도와 상대 밀도를 쉽게 확인할 수 있습니다.

다음을 비롯한 다양한 시나리오에서 열 지도를 사용할 수 있습니다.

- **온도 데이터**: 두 데이터 요소 사이 온도의 근사값을 제공합니다.
- **노이즈 센서 데이터**: 센서가 있는 곳의 노이즈 강도를 표시할 뿐만 아니라, 멀리 떨어진 곳에 대한 인사이트를 제공할 수도 있습니다. 한 사이트의 노이즈 수준이 높지 않을 수 있습니다. 여러 센서의 노이즈 검사 영역이 겹치는 경우 이 겹치는 영역이 더 높은 노이즈 수준을 경험할 수 있습니다. 이렇게 겹쳐진 영역이 열 지도에 표시됩니다.
- **GPS 추적**: 각 데이터 요소의 강도가 속도에 따라 다른 가중 높이 지도에서의 속도를 포함합니다. 예를 들어 이 기능은 차량의 속도를 확인할 수 있는 방법을 제공합니다.

> [!TIP]
> 기본적으로 열 지도 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 점 기하 도형 기능만 렌더링하도록 계층을 제한하려면 계층의 `filter` 옵션을 `NSPredicate(format: "%@ == \"Point\"", NSExpression.geometryTypeAZMVariable)`로 설정합니다. MultiPoint 기능도 포함하려면 를 `NSCompoundPredicate` 사용합니다.

[사물 인터넷 표시 - Azure 지도 열 지도 및 이미지 오버레이](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny)

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 의 함수에 삽입할 수  `viewDidLoad` `ViewController` 있습니다.

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

요소의 데이터 원본을 열 지도로 렌더링하려면 데이터 원본을 `HeatMapLayer` 클래스 인스턴스로 전달하여 맵에 추가합니다.

다음 코드 샘플은 지난 주에 발생한 지진의 GeoJSON 피드를 로드하고 열 지도로 렌더링합니다. 각 데이터 포인트는 모든 확대/축소 수준에서 10포인트의 반지름으로 렌더링됩니다. 더 나은 사용자 환경을 보장하기 위해 열 지도가 레이블 계층 아래에 있어 레이블이 선명하게 표시됩니다. 이 샘플의 데이터는 [USGS 지진 위험 프로그램](https://earthquake.usgs.gov/)에서 제공됩니다.

```swift
// Create a data source.
let source = DataSource()

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a heat map layer.
let layer = HeatMapLayer(
    source: source,
    options: [
        .heatmapRadius(10),
        .heatmapOpacity(0.8)
    ]
)

// Add the layer to the map, below the labels.
map.layers.insertLayer(layer, below: "labels")
```

다음 스크린샷은 위의 코드를 사용하여 열 지도를 로드하는 맵을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-heat-map-layer-ios/ios-heat-map-layer.png" alt-text="최근 지진의 열 지도 계층으로 매핑합니다." lightbox="./media/ios-sdk/add-heat-map-layer-ios/ios-heat-map-layer.png":::

## <a name="customize-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 다음을 비롯하여 다양한 사용자 지정 옵션을 제공합니다.

- `heatmapRadius`: 각 데이터 포인트를 렌더링할 지점의 반경을 정의합니다. 반경을 정수 또는 식으로 설정할 수 있습니다. 식을 사용하여 확대/축소 수준에 따라 반경을 조정하고 지도에서 일관적인 공간 영역을 나타낼 수 있습니다(예: 5마일 반경).
- `heatmapColor`: 열 지도에 색이 지정되는 방식을 지정합니다. 색 그라데이션은 열 지도의 일반적인 기능입니다. `NSExpression(forAZMInterpolating:curveType:parameters:stops:)` 식을 사용하여 해당 효과를 구현할 수 있습니다. 열 지도에 색상을 적용하는 `NSExpression(forAZMStepping:from:stops:)` 식을 사용하여 윤곽선 또는 레이더 스타일 지도와 유사한 범위로 밀도를 시각적으로 나눌 수도 있습니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다.

  열 지도에 대한 색 값을 `NSExpression.heatmapDensityAZMVariable` 값에서 식으로 지정합니다. “보간” 식의 인덱스 0에 데이터가 정의되어 있지 않은 영역의 색 또는 “계단식” 식의 기본 색입니다. 배경색을 정의하는 데 이 값을 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

  색 식의 예는 다음과 같습니다.

```swift
// Interpolated color expression
NSExpression(
    forAZMInterpolating: .heatmapDensityAZMVariable,
    curveType: .linear,
    parameters: nil,
    stops: NSExpression(forConstantValue: [
        0: UIColor.magenta.withAlphaComponent(0),
        0.01: UIColor.magenta,
        0.5: UIColor(red: 251/255, green: 0, blue: 251/255, alpha: 1),
        1: UIColor(red: 0, green: 195/255, blue: 1, alpha: 1)
    ])
)
```

```swift
// Stepped color expression
NSExpression(
    forAZMStepping: .heatmapDensityAZMVariable,
    from: NSExpression(forConstantValue: UIColor.clear),
    stops: NSExpression(forConstantValue: [
        0.01: UIColor(red: 0, green: 0, blue: 128/255, alpha: 1),
        0.25: UIColor.cyan,
        0.5: UIColor.green,
        0.75: UIColor.yellow,
        1: UIColor.red
    ])
)
```

- `heatmapOpacity`: 열 지도 계층의 불투명도 또는 투명도를 지정합니다.
- `heatmapIntensity`: 각 데이터 요소의 가중치에 승수를 적용하여 열 지도의 전체 강도를 높입니다. 이로 인해 데이터 요소의 가중치가 달라지므로 쉽게 시각화할 수 있습니다.
- `heatmapWeight`: 기본적으로 모든 데이터 요소의 가중치는 1이므로, 가중치는 동일합니다. 가중치 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자를 가중치로 설정하는 경우 지도에 각 데이터 요소를 두 번씩 배치하는 것과 마찬가지입니다. 예를 들어 가중치가 `2`인 경우 밀도는 2배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다.

  그러나 식을 사용하는 경우 각 데이터 요소의 가중치는 각 데이터 요소의 속성에 따라 다를 수 있습니다. 예를 들어 각 데이터 요소가 지진을 나타내는 경우 진도 값은 각 지진 데이터 요소에서 중요한 메트릭입니다. 지진은 항상 발생하지만, 대부분 진도가 낮으며 알아차리지 못합니다. 식에서 진도 값을 사용하여 각 데이터 요소에 가중치를 할당합니다. 진도 값을 사용하여 가중치를 할당하면 열 지도 내에서 지진의 유의성을 보다 잘 표현할 수 있습니다.
- `minZoom` 및 `maxZoom`: 계층이 표시되어야 하는 확대/축소 수준 범위입니다.
- `filter`: 소스에서 검색되고 계층에서 렌더링되는 를 제한하는 데 사용되는 필터 사전입니다.
- `sourceLayer`: 계층에 연결된 데이터 원본이 벡터 타일 원본인 경우 벡터 타일 내의 원본 계층을 지정해야 합니다.
- `visible`: 계층을 숨기거나 표시합니다.

다음은 선형 보간 식을 사용하여 부드러운 색 그라데이션을 만드는 열 지도의 예제입니다. 데이터에 정의된 `mag` 속성은 지수 보간을 사용하여 각 데이터 요소의 가중치 또는 관련성을 설정하는 데 사용됩니다.

```swift
let layer = HeatMapLayer(source: source, options: [
    .heatmapRadius(10),

    // A linear interpolation is used to create a smooth color gradient based on the heat map density.
    .heatmapColor(
        from: NSExpression(
            forAZMInterpolating: .heatmapDensityAZMVariable,
            curveType: .linear,
            parameters: nil,
            stops: NSExpression(forConstantValue: [
                0: UIColor.black.withAlphaComponent(0),
                0.01: UIColor.black,
                0.25: UIColor.magenta,
                0.5: UIColor.red,
                0.75: UIColor.yellow,
                1: UIColor.white
            ])
        )
    ),

    // Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    .heatmapWeight(
        from: NSExpression(
            forAZMInterpolating: NSExpression(forKeyPath: "mag"),
            curveType: .exponential,
            parameters: NSExpression(forConstantValue: 2),
            stops: NSExpression(forConstantValue: [
                0: 0,
                // Any earthquake above a magnitude of 6 will have a weight of 1
                6: 1
            ])
        )
    )
])
```

다음 스크린샷에서는 이전 열 지도 예제와 동일한 데이터를 사용하는 위의 사용자 지정 열 지도 계층을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-heat-map-layer-ios/ios-custom-heat-map-layer.png" alt-text="최근 지진의 사용자 지정 열 지도 계층으로 매핑합니다." lightbox="./media/ios-sdk/add-heat-map-layer-ios/ios-custom-heat-map-layer.png":::

## <a name="consistent-zoomable-heat-map"></a>일관된 확대/축소가 가능한 열 지도

기본적으로 열 지도 계층에서 렌더링된 데이터 요소의 반지름에는 모든 확대/축소 수준에 대한 고정 지점 반경이 있습니다. 지도를 확대/축소하면 데이터가 함께 집계되고 열 지도 계층이 다르게 보입니다. 다음 비디오에서는 지도를 확대/축소할 때 점 반경을 유지하는 열 지도의 기본 동작을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-heat-map-layer-ios/ios-heat-map-layer-zoom.gif" alt-text="일관된 점 크기를 표시하는 열 지도 계층이 있는 지도 확대/축소를 보여주는 애니메이션입니다.":::

각 데이터 요소가 지도의 동일한 실제 영역을 포함하도록 각 확대/축소 수준에 맞게 반경을 조정하는 데 `zoom` 식을 사용할 수 있습니다. 이 식은 열 지도 계층을 더 정적이고 일관되게 보이게 합니다. 맵의 각 확대/축소 수준에는 이전 확대/축소 수준보다 세로 및 가로로 두 배 많은 지점이 있습니다.

확대/축소 수준마다 두 배가 되도록 반경을 조정하면 모든 확대/축소 수준에서 일관되게 보이는 열 지도가 생성됩니다. 이 크기 조정을 적용하려면 `NSExpression.zoomLevelAZMVariable` 다음 `exponential interpolation` 샘플과 같이 최소 확대/축소 수준에 대해 점 반경이 설정되고 최대 확대/축소 수준에 대해 크기가 조정된 반지름이 계산된 Base 2 식과 함께 를 `pow(2, maxZoom - minZoom) * radius` 사용합니다. 지도를 확대/축소하여 확대/축소 수준으로 열 지도가 조정되는 방식을 확인합니다.

```swift
let layer = HeatMapLayer(source: source, options: [
    .heatmapOpacity(0.75),
    .heatmapRadius(
        from: NSExpression(
            forAZMInterpolating: .zoomLevelAZMVariable,
            curveType: .exponential,
            parameters: NSExpression(forConstantValue: 2),
            stops: NSExpression(forConstantValue: [

                // For zoom level 1 set the radius to 2 points.
                1: 2,

                // Between zoom level 1 and 19, exponentially scale the radius from 2 points to 2 * 2^(maxZoom - minZoom) points.
                19: pow(2, 19 - 1) * 2
            ])
        )
    )
])
```

다음 비디오에서는 확대/축소 수준에서 일관된 열 지도 렌더링을 만들기 위해 지도를 확대/축소하는 동안 반경을 조정하는 위의 코드를 실행하는 맵을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/add-heat-map-layer-ios/ios-consistent-zoomable-heat-map-layer.gif" alt-text="일관된 지리 공간적 크기를 보여 줍니다. 열 지도 계층을 사용하여 지도 확대/축소를 보여주는 애니메이션입니다.":::

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 인접한 요소는 클러스터된 요소로 그룹화됩니다. 각 클러스터의 요소 개수를 열 지도의 가중치 식으로 사용할 수 있습니다. 이렇게 하면 렌더링되는 요소의 수를 크게 줄일 수 있습니다. 한 클러스터의 요소 수는 요소 기능의 `point_count` 속성에 저장됩니다.
>
> ```swift
> let layer = HeatMapLayer(source: source, options: [
>     .heatmapWeight(from: NSExpression(forKeyPath: "point_count"))
> ])
> ```
>
> 클러스터링 반경이 몇 가지 지점만 있는 경우 렌더링에 약간의 시각적 차이가 있습니다. 반경 그룹이 커지면 각 클러스터의 요소가 많아지며 열 지도의 성능이 향상됩니다.

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

- [데이터 원본 만들기](create-data-source-ios-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-ios-sdk.md)
