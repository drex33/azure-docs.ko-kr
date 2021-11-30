---
title: IOS SDK의 클러스터링 지점 데이터
titleSuffix: Microsoft Azure Maps
description: 맵에서 요소 데이터를 클러스터링하는 방법에 대해 알아봅니다. Azure Maps iOS SDK를 사용 하 여 데이터를 클러스터링 하 고, 클러스터 마우스 이벤트에 대응 하 고, 클러스터 집계를 표시 하는 방법을 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 10fe277634c0e973a4235d4ebbda82767e771cf6
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218989"
---
# <a name="clustering-point-data-in-the-ios-sdk-preview"></a>IOS SDK의 클러스터링 지점 데이터 (미리 보기)

맵에서 많은 데이터 요소를 시각화하면 데이터 요소가 서로 겹칠 수 있습니다. 겹치는 경우 맵을 읽을 수 없게 되고 사용하기 어려울 수 있습니다. 지점 데이터 클러스터링은 서로 가까이 있는 지점 데이터를 결합하여 맵에 단일 클러스터형 데이터 요소로 표시하는 프로세스입니다. 사용자가 맵을 확대하면 클러스터는 개별 데이터 요소로 분리됩니다. 많은 수의 데이터 요소로 작업할 때 클러스터링 프로세스를 사용하여 사용자 환경을 개선합니다.

[Azure Maps의 클러스터링 지점 데이터 사물 인터넷 표시](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny)

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은의 함수에 삽입할 수 있습니다  `viewDidLoad` `ViewController` .

## <a name="enabling-clustering-on-a-data-source"></a>데이터 원본에서 클러스터링 사용

`cluster` 옵션을 `true`로 설정하여 `DataSource` 클래스에서 클러스터링을 사용하도록 설정합니다. `clusterRadius`주변 데이터 요소를 선택 하 고 클러스터에 결합 하도록 설정 합니다. 의 값 `clusterRadius` 이 요소입니다. `clusterMaxZoom`을 사용하여 클러스터링 논리를 사용하지 않도록 설정할 확대/축소 수준을 지정합니다. 데이터 원본에서 클러스터링을 사용하도록 설정하는 방법의 예는 다음과 같습니다.

```swift
// Create a data source and enable clustering.
let source = DataSource(options: [
    //Tell the data source to cluster point data.
    .cluster(true),

    //The radius in points to cluster data points together.
    .clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    .clusterMaxZoom(15)
])
```

> [!Caution]
> 클러스터링은 `Point` 기능에서만 작동합니다. 데이터 원본에 `Polyline` 또는 `Polygon`과 같은 다른 도형 유형의 기능이 포함되어 있으면 오류가 발생합니다.

> [!TIP]
> 두 데이터 요소를 함께 사용하는 경우 확대로 인해 데이터 요소가 얼마나 가까워지든, 클러스터는 절대 분리되지 않습니다. 이를 해결하기 위해 `clusterMaxZoom` 옵션을 설정하여 클러스터링 논리를 사용하지 않도록 설정하고 모든 항목을 표시하기만 합니다.

`DataSource` 클래스는 클러스터링과 관련하여 다음 메서드를 제공합니다.

| 방법 | 반환 형식 | Description |
|--------|-------------|-------------|
| `children(of cluster: Feature)` | `[Feature]` | 다음 확대/축소 수준에서 지정된 클러스터의 자식 요소를 검색합니다. 이러한 자식은 기능과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치하는 속성이 있는 기능입니다. |
| `zoomLevel(forExpanding cluster: Feature)` | `Double` | 클러스터가 확장 또는 분리를 시작하는 확대/축소 수준을 계산합니다. |
| `leaves(of cluster: Feature, offset: UInt, limit: UInt)` | `[Feature]` | 클러스터의 모든 지점을 검색합니다. `limit`를 설정하여 지점의 하위 세트를 반환하고, `offset`을 사용하여 지점을 통해 페이지를 이동합니다. |

## <a name="display-clusters-using-a-bubble-layer"></a>거품형 계층을 사용하여 클러스터 표시

거품형 계층은 클러스터형 요소를 렌더링하는 좋은 방법입니다. 식을 사용하여 반지름의 크기를 조정하고 클러스터의 요소 수에 따라 색을 변경합니다. 거품형 계층을 사용하여 클러스터를 표시하는 경우에는 별도의 계층을 사용하여 비클러스터형 데이터 요소를 렌더링해야 합니다.

클러스터의 크기를 거품 위에 표시하려면 텍스트와 함께 기호 계층을 사용하고 아이콘은 사용하지 마세요.

다음 코드는 거품형 레이어를 사용하여 클러스터된 포인트를 표시하고 기호 레이어를 사용하여 각 클러스터의 포인트 수를 표시합니다. 두 번째 기호 레이어는 클러스터 내에 있지 않은 개별 포인트를 표시하는 데 사용됩니다.

```swift
// Create a data source and enable clustering.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true),

    // The radius in points to cluster data points together.
    .clusterRadius(45),

    // The maximum zoom level in which clustering occurs.
    // If you zoom in more than this, all points are rendered as symbols.
    .clusterMaxZoom(15)
])

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a bubble layer for rendering clustered data points.
map.layers.addLayer(
    BubbleLayer(
        source: source,
        options: [
            // Scale the size of the clustered bubble based on the number of points in the cluster.
            .bubbleRadius(
                from: NSExpression(
                    forAZMStepping: NSExpression(forKeyPath: "point_count"),
                    // Default of 20 point radius.
                    from: NSExpression(forConstantValue: 20),
                    stops: NSExpression(forConstantValue: [

                        // If point_count >= 100, radius is 30 points.
                        100: 30,

                        // If point_count >= 750, radius is 40 points.
                        750: 40
                    ])
                )
            ),

            // Change the color of the cluster based on the value on the point_count property of the cluster.
            .bubbleColor(
                from: NSExpression(
                    forAZMStepping: NSExpression(forKeyPath: "point_count"),
                    // Default to green.
                    from: NSExpression(forConstantValue: UIColor.green),
                    stops: NSExpression(forConstantValue: [

                        // If the point_count >= 100, color is yellow.
                        100: UIColor.yellow,

                        // If the point_count >= 100, color is red.
                        750: UIColor.red
                    ])
                )
            ),
            .bubbleStrokeWidth(0),

            // Only rendered data points which have a point_count property, which clusters do.
            .filter(from: NSPredicate(format: "point_count != NIL"))
        ]
    )
)

// Create a symbol layer to render the count of locations in a cluster.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Hide the icon image.
            .iconImage(nil),

            // Display the point count as text.
            .textField(from: NSExpression(forKeyPath: "point_count")),
            .textOffset(CGVector(dx: 0, dy: 0.4)),
            .textAllowOverlap(true),

            // Allow clustered points in this layer.
            .filter(from: NSPredicate(format: "point_count != NIL"))
        ]
    )
)

// Create a layer to render the individual locations.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Filter out clustered points from this layer.
            .filter(from: NSPredicate(format: "point_count = NIL"))
        ]
    )
)
```

다음 이미지는 클러스터의 포인트 수에 따라 크기가 조정되고 색상이 지정된 거품형 레이어의 클러스터된 포인트 기능을 표시하는 위의 코드를 보여 줍니다. 클러스터되지 않은 포인트는 기호 레이어를 사용하여 렌더링됩니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-bubble-layer.gif" alt-text="에서 지도를 확대/축소 하는 동안 클러스터형 위치를 분리 합니다.":::

## <a name="display-clusters-using-a-symbol-layer"></a>기호 계층을 사용하여 클러스터 표시

데이터 요소를 시각화할 때 기호 계층은 보다 명확한 사용자 인터페이스를 위해 서로 겹치는 기호를 자동으로 숨깁니다. 맵에 데이터 요소 밀도를 표시하려는 경우 이 기본 동작은 바람직하지 않을 수 있습니다. 그러나 이러한 설정은 변경할 수 있습니다. 모든 기호를 표시하려면 기호 계층의 `iconAllowOverlap` 옵션을 `true`로 설정합니다.

클러스터링을 사용하여 선명한 사용자 인터페이스를 유지하면서 데이터 요소 밀도를 표시합니다. 아래 샘플에서는 기호 계층을 사용하여 사용자 지정 기호를 추가하고 클러스터 및 개별 데이터 요소를 나타내는 방법을 보여 줍니다.

```swift
// Load all the custom image icons into the map resources.
map.images.add(UIImage(named: "earthquake_icon")!, withID: "earthquake_icon")
map.images.add(UIImage(named: "warning-triangle-icon")!, withID: "warning-triangle-icon")

// Create a data source and add it to the map.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true)
])

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a layer to render the individual locations.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            .iconImage("earthquake_icon"),

            // Filter out clustered points from this layer.
            .filter(from: NSPredicate(format: "point_count = NIL"))
        ]
    )
)

// Create a symbol layer to render the clusters.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            .iconImage("warning-triangle-icon"),
            .textField(from: NSExpression(forKeyPath: "point_count")),
            .textOffset(CGVector(dx: 0, dy: -0.4)),

            // Allow clustered points in this layer.
            .filter(from: NSPredicate(format: "point_count != NIL"))
        ]
    )
)
```

이 샘플에서 다음 이미지는 앱의 자산 폴더에 로드 됩니다.

| ![지진 아이콘 이미지](./media/ios-sdk/cluster-point-data-ios-sdk/earthquake-icon.png) | ![소나기의 날씨 아이콘 이미지](./media/ios-sdk/cluster-point-data-ios-sdk/warning-triangle-icon.png) |
|:-----------------------:|:--------------------------:|
| earthquake-icon.png     | warning-triangle-icon.png  |

다음 이미지는 사용자 지정 아이콘을 사용하여 클러스터링된 포인트 기능과 클러스터링되지 않은 포인트 기능을 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-symbol-layer.gif" alt-text="기호 계층을 사용 하 여 렌더링 된 클러스터형 점의 맵입니다.":::

## <a name="clustering-and-the-heat-map-layer"></a>클러스터링 및 열 지도 계층

열 지도는 지도에 데이터 밀도를 표시하는 좋은 방법입니다. 이 시각화 메서드는 자체적으로 많은 수의 데이터 요소를 처리할 수 있습니다. 데이터 요소가 클러스터링되며 클러스터 크기가 열 지도의 가중치로 사용되면 열 지도에서 더 많은 데이터를 처리할 수 있습니다. 이 옵션을 구현하려면 열 지도 계층의 `heatmapWeight` 옵션을 `NSExpression(forKeyPath: "point_count")`로 설정합니다. 클러스터 반지름이 작은 경우 열 지도는 비클러스터형 데이터 요소를 사용하여 열 지도와 거의 동일하게 보이지만 훨씬 더 나은 성능을 나타냅니다. 그러나 클러스터 반지름이 작을수록 열 지도가 더 정확하게 나타나지만 성능상의 이점이 줄어듭니다.

```swift
// Create a data source and enable clustering.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true),

    // The radius in points to cluster points together.
    .clusterRadius(10)
])

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a heat map and add it to the map.
map.layers.insertLayer(
    HeatMapLayer(
        source: source,
        options: [
            // Set the weight to the point_count property of the data points.
            .heatmapWeight(from: NSExpression(forKeyPath: "point_count")),

            // Optionally adjust the radius of each heat point.
            .heatmapRadius(20)
        ]
    ),
    below: "labels"
)
```

다음 이미지는 위의 코드가 클러스터된 포인트 기능과 히트 맵의 가중치로 클러스터 수를 사용하여 최적화된 히트 맵을 표시하는 것을 보여 줍니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-heat-map.gif" alt-text="클러스터형 요소를 가중치로 사용 하 여 최적화 된 열 지도를 매핑합니다.":::

## <a name="tap-events-on-clustered-data-points"></a>클러스터 된 데이터 요소에서 이벤트를 누릅니다.

클러스터 된 데이터 요소를 포함 하는 계층에서 탭 이벤트가 발생 하면 클러스터형 데이터 지점은 이벤트를 GeoJSON point 기능 개체로 반환 합니다. 이 요소 기능에는 다음과 같은 속성이 있습니다.

| 속성 이름             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 기능이 클러스터를 표시하는지 여부를 나타냅니다. |
| `point_count`             | number  | 클러스터에 포함된 지점의 수입니다.  |
| `point_count_abbreviated` | 문자열  | 길이가 긴 경우 `point_count` 값을 줄여서 표시하는 문자열입니다. (예: 4,000은 4K)  |

이 예제에서는 클러스터 요소를 렌더링 하 고 탭 이벤트를 추가 하는 거품형 계층을 사용 합니다. 탭 이벤트를 트리거하는 경우이 코드는 맵을 계산 하 고 다음 확대/축소 수준으로 축소 하 여 클러스터를 분리 합니다. 이 기능은 클래스의 메서드를 사용 하 여 구현 됩니다 `zoomLevel(forExpanding:)` `DataSource` .

```swift
// Create a data source and enable clustering.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true),

    // The radius in points to cluster data points together.
    .clusterRadius(45),

    // The maximum zoom level in which clustering occurs.
    // If you zoom in more than this, all points are rendered as symbols.
    .clusterMaxZoom(15)
])

// Set data source to the class property to use in events handling later.
self.source = source

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a bubble layer for rendering clustered data points.
let clusterBubbleLayer = BubbleLayer(
    source: source,
    options: [
        // Scale the size of the clustered bubble based on the number of points in the cluster.
        .bubbleRadius(
            from: NSExpression(
                forAZMStepping: NSExpression(forKeyPath: "point_count"),
                // Default of 20 point radius.
                from: NSExpression(forConstantValue: 20),
                stops: NSExpression(forConstantValue: [
                    // If point_count >= 100, radius is 30 points.
                    100: 30,

                    // If point_count >= 750, radius is 40 points.
                    750: 40
                ])
            )
        ),

        // Change the color of the cluster based on the value on the point_count property of the cluster.
        .bubbleColor(
            from: NSExpression(
                forAZMStepping: NSExpression(forKeyPath: "point_count"),
                // Default to green.
                from: NSExpression(forConstantValue: UIColor.green),
                stops: NSExpression(forConstantValue: [
                    // If the point_count >= 100, color is yellow.
                    100: UIColor.yellow,

                    // If the point_count >= 100, color is red.
                    750: UIColor.red
                ])
            )
        ),
        .bubbleStrokeWidth(0),

        // Only rendered data points which have a point_count property, which clusters do.
        .filter(from: NSPredicate(format: "point_count != NIL"))
    ]
)

// Add the clusterBubbleLayer to the map.
map.layers.addLayer(clusterBubbleLayer)

// Create a symbol layer to render the count of locations in a cluster.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Hide the icon image.
            .iconImage(nil),

            // Display the point count as text.
            .textField(from: NSExpression(forKeyPath: "point_count_abbreviated")),

            // Offset the text position so that it's centered nicely.
            .textOffset(CGVector(dx: 0, dy: 0.4)),

            // Allow text overlapping so text is visible anyway
            .textAllowOverlap(true),

            // Allow clustered points in this layer.
            .filter(from: NSPredicate(format: "point_count != NIL"))
        ]
    )
)

// Create a layer to render the individual locations.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Filter out clustered points from this layer.
            .filter(from: NSPredicate(format: "point_count = NIL"))
        ]
    )
)

// Add the delegate to handle taps on the clusterBubbleLayer only.
map.events.addDelegate(self, for: [clusterBubbleLayer.id])
```

```swift
func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
    guard let source = source, let cluster = features.first else {
        // Data source have been released or no features provided
        return
    }

    // Get the cluster expansion zoom level. This is the zoom level at which the cluster starts to break apart.
    let expansionZoom = source.zoomLevel(forExpanding: cluster)

    // Update the map camera to be centered over the cluster.
    map.setCameraOptions([
        // Center the map over the cluster points location.
        .center(cluster.coordinate),

        // Zoom to the clusters expansion zoom level.
        .zoom(expansionZoom),

        // Animate the movement of the camera to the new position.
        .animationType(.ease),
        .animationDuration(200)
    ])
}
```

다음 그림은 위의 코드를 표시 하 고, 한 번에 클러스터가 분리 되어 확장 될 때까지 다음 확대/축소 수준을 확대 합니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-expansion.gif" alt-text="클러스터링 된 기능의 지도를 확대 하 고 탭 할 때 분리 합니다.":::

## <a name="display-cluster-area"></a>클러스터 영역 표시

클러스터가 나타내는 요소 데이터는 영역에 분산됩니다. 이 샘플에서는 클러스터를 탭 할 때 두 가지 주요 동작이 발생 합니다. 먼저 클러스터에 포함된 개별 데이터 요소를 사용하여 볼록 집합(convex hull)을 계산합니다. 그러면 볼록 집합이 맵에 표시되면서 영역을 표시합니다.  볼록 집합은 탄력적 밴드와 같은 일련의 요소를 래핑하고 `convexHull(from:)` 메서드를 사용하여 계산할 수 있는 다각형입니다. 클러스터에 포함된 모든 요소는 `leaves(of:offset:limit:)` 메서드를 사용하여 데이터 원본에서 검색할 수 있습니다.

```swift
// Create a data source and enable clustering.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true)
])

// Set data source to the class property to use in events handling later.
self.source = source

// Import the geojson data and add it to the data source.
let url = URL(string: "https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a data source for the convex hull polygon.
// Since this will be updated frequently it is more efficient to separate this into its own data source.
let polygonDataSource = DataSource()

// Set polygon data source to the class property to use in events handling later.
self.polygonDataSource = polygonDataSource

// Add data source to the map.
map.sources.add(polygonDataSource)

// Add a polygon layer and a line layer to display the convex hull.
map.layers.addLayer(PolygonLayer(source: polygonDataSource))
map.layers.addLayer(LineLayer(source: polygonDataSource))

// Load an icon into the image sprite of the map.
map.images.add(.azm_markerRed, withID: "marker-red")

// Create a symbol layer to render the clusters.
let clusterLayer = SymbolLayer(
    source: source,
    options: [
        .iconImage("marker-red"),
        .textField(from: NSExpression(forKeyPath: "point_count_abbreviated")),
        .textOffset(CGVector(dx: 0, dy: -1.2)),
        .textColor(.white),
        .textSize(14),

        // Only rendered data points which have a point_count property, which clusters do.
        .filter(from: NSPredicate(format: "point_count != NIL"))
    ]
)

// Add the clusterLayer to the map.
map.layers.addLayer(clusterLayer)

// Create a layer to render the individual locations.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Filter out clustered points from this layer.
            .filter(from: NSPredicate(format: "point_count = NIL"))
        ]
    )
)

// Add the delegate to handle taps on the clusterLayer only
// and then calculate the convex hull of all the points within a cluster.
map.events.addDelegate(self, for: [clusterLayer.id])
```

```swift
func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
    guard let source = source, let polygonDataSource = polygonDataSource, let cluster = features.first else {
        // Data source have been released or no features provided
        return
    }

    // Get all points in the cluster. Set the offset to 0 and the max int value to return all points.
    let featureLeaves = source.leaves(of: cluster, offset: 0, limit: .max)

    // When only two points in a cluster. Render a line.
    if featureLeaves.count == 2 {

        // Extract the locations from the feature leaves.
        let locations = featureLeaves.map(\.coordinate)

        // Create a line from the points.
        polygonDataSource.set(geometry: Polyline(locations))

        return
    }

    // When more than two points in a cluster. Render a polygon.
    if let hullPolygon = Math.convexHull(from: featureLeaves) {

        // Overwrite all data in the polygon data source with the newly calculated convex hull polygon.
        polygonDataSource.set(geometry: hullPolygon)
    }
}
```

다음 이미지는 위의 코드를 통해 탭 클러스터 내 모든 지점의 영역을 표시 합니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-leaves-convex-hull.gif" alt-text="탭 클러스터 내 모든 지점의 볼록 선체 다각형을 보여 주는 맵입니다.":::

## <a name="aggregating-data-in-clusters"></a>클러스터의 데이터 집계

클러스터는 종종 클러스터에 포함된 요소 수와 함께 기호를 사용하여 표시됩니다. 그러나 경우에 따라 추가 메트릭을 사용하여 클러스터 스타일을 사용자 지정하는 것이 좋습니다. 클러스터 속성을 사용하면 클러스터가 있는 각 포인트 내의 속성을 기반으로 한 계산과 동일한 사용자 지정 속성을 만들 수 있습니다. 클러스터 속성은 `DataSource`의 `clusterProperties` 옵션에서 정의할 수 있습니다.

다음 코드는 클러스터에 있는 각 데이터 요소의 엔터티 형식 속성을 기준으로 개수를 계산합니다. 사용자가 클러스터에서 탭 하면 클러스터에 대 한 추가 정보를 포함 하는 팝업이 표시 됩니다.

```swift
// Create a popup and add it to the map.
let popup = Popup()
map.popups.add(popup)

// Set popup to the class property to use in events handling later.
self.popup = popup

// Close the popup initially.
popup.close()

// Create a data source and enable clustering.
let source = DataSource(options: [
    // Tell the data source to cluster point data.
    .cluster(true),

    // The radius in points to cluster data points together.
    .clusterRadius(50),

    // Calculate counts for each entity type in a cluster as custom aggregate properties.
    .clusterProperties(self.entityTypes.map { entityType in
        ClusterProperty(
            name: entityType,
            operator: NSExpression(
                forFunction: "sum:",
                arguments: [
                    NSExpression.featureAccumulatedAZMVariable,
                    NSExpression(forKeyPath: entityType)
                ]
            ),
            map: NSExpression(
                forConditional: NSPredicate(format: "EntityType = '\(entityType)'"),
                trueExpression: NSExpression(forConstantValue: 1),
                falseExpression: NSExpression(forConstantValue: 0)
            )
        )
    })
])

// Import the geojson data and add it to the data source.
let url = URL(string: "https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/SamplePoiDataSet.json")!
source.importData(fromURL: url)

// Add data source to the map.
map.sources.add(source)

// Create a bubble layer for rendering clustered data points.
let clusterBubbleLayer = BubbleLayer(
    source: source,
    options: [
        .bubbleRadius(20),
        .bubbleColor(.purple),
        .bubbleStrokeWidth(0),

        // Only rendered data points which have a point_count property, which clusters do.
        .filter(from: NSPredicate(format: "point_count != NIL"))
    ]
)

// Add the clusterBubbleLayer to the map.
map.layers.addLayer(clusterBubbleLayer)

// Create a symbol layer to render the count of locations in a cluster.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Hide the icon image.
            .iconImage(nil),

            // Display the 'point_count_abbreviated' property value.
            .textField(from: NSExpression(forKeyPath: "point_count_abbreviated")),

            .textColor(.white),
            .textOffset(CGVector(dx: 0, dy: 0.4)),

            // Allow text overlapping so text is visible anyway
            .textAllowOverlap(true),

            // Only rendered data points which have a point_count property, which clusters do.
            .filter(from: NSPredicate(format: "point_count != NIL"))
        ]
    )
)

// Create a layer to render the individual locations.
map.layers.addLayer(
    SymbolLayer(
        source: source,
        options: [
            // Filter out clustered points from this layer.
            SymbolLayerOptions.filter(from: NSPredicate(format: "point_count = NIL"))
        ]
    )
)

// Add the delegate to handle taps on the clusterBubbleLayer only
// and display the aggregate details of the cluster.
map.events.addDelegate(self, for: [clusterBubbleLayer.id])
```

```swift
func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
    guard let popup = popup, let cluster = features.first else {
        // Popup has been released or no features provided
        return
    }

    // Create a number formatter that removes decimal places.
    let nf = NumberFormatter()
    nf.maximumFractionDigits = 0

    // Create the popup's content.
    var text = ""

    let pointCount = cluster.properties["point_count"] as! Int
    let pointCountString = nf.string(from: pointCount as NSNumber)!

    text.append("Cluster size: \(pointCountString) entities\n")

    entityTypes.forEach { entityType in
        text.append("\n")
        text.append("\(entityType): ")

        // Get the aggregated entity type count from the properties of the cluster by name.
        let aggregatedCount = cluster.properties[entityType] as! Int
        let aggregatedCountString = nf.string(from: aggregatedCount as NSNumber)!

        text.append(aggregatedCountString)
    }

    // Create the custom view for the popup.
    let customView = PopupTextView()

    // Set the text to the custom view.
    customView.setText(text)

    // Get the position of the cluster.
    let position = Math.positions(from: cluster).first!

    // Set the options on the popup.
    popup.setOptions([
        // Set the popups position.
        .position(position),

        // Set the anchor point of the popup content.
        .anchor(.bottom),

        // Set the content of the popup.
        .content(customView)
    ])

    // Open the popup.
    popup.open()
}
```

팝업은 [팝업 표시](Display-feature-information-ios-sdk.md) 문서에 설명된 단계를 따릅니다.

다음 이미지는 위의 코드에서 탭된 클러스터형 지점의 모든 지점에 대해 각 엔터티 값 형식의 집계된 개수가 있는 팝업을 표시합니다.

:::image type="content" source="./media/ios-sdk/cluster-point-data-ios-sdk/ios-cluster-aggregates.gif" alt-text="클러스터에 있는 모든 요소의 엔터티 형식에 대해 집계된 개수의 팝업을 보여주는 맵입니다.":::

## <a name="additional-information"></a>추가 정보

원본에 더 많은 데이터 추가:

- [데이터 원본 만들기](create-data-source-ios-sdk.md)
- [기호 계층 추가](add-symbol-layer-ios.md)
- [거품형 계층 추가](add-bubble-layer-map-ios.md)
