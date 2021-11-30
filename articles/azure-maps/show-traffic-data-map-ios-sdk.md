---
title: iOS 맵에 트래픽 데이터 표시
titleSuffix: Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure 지도 iOS SDK를 사용하여 맵에 트래픽 데이터를 표시하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: d0826406ac8afd550950510214e0d248fef58608
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218979"
---
# <a name="show-traffic-data-on-the-map-in-the-ios-sdk-preview"></a>iOS SDK에서 맵에 트래픽 데이터 표시(미리 보기)

흐름 데이터 및 인시던트 데이터는 맵에 표시 될 수 있는 두 가지 트래픽 데이터 유형입니다. 이 가이드에서는 두 가지 유형의 트래픽 데이터를 표시하는 방법을 보여 줍니다. 인시던트 데이터는 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다. 흐름 데이터는 도로의 트래픽 흐름에 대한 메트릭을 표시합니다.

## <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 의 함수에 삽입할 수  `viewDidLoad` `ViewController` 있습니다.

## <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

Azure Maps에서 사용할 수 있는 트래픽 데이터에는 두 가지 유형이 있습니다.

- 인시던트 데이터 - 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다.
- 흐름 데이터 - 도로의 트래픽 흐름에 대한 메트릭을 제공합니다. 트래픽 흐름 데이터를 사용하여 도로 색을 조정하는 경우가 많습니다. 이 색은 속도 제한 또는 다른 메트릭을 기준으로 흐름을 저하시키는 트래픽 양을 기반으로 합니다. 맵의 트래픽 `flow` 옵션에 전달할 수 있는 4개의 값이 있습니다.

    |흐름 열거형 | 설명|
    | :-- | :-- |
    | `TrafficFlow.none` | 맵에 트래픽 데이터를 표시하지 않습니다. |
    | `TrafficFlow.relative` | 도로의 자유 흐름 속도에 비례하여 트래픽 데이터를 표시합니다. |
    | `TrafficFlow.relativeDelay` | 평균 예상 지연 시간보다 느린 영역을 표시합니다. |
    | `TrafficFlow.absolute` | 도로에 있는 모든 차량의 절대 속도를 보여 줍니다. |

다음 코드에서는 맵에 트래픽 데이터를 표시하는 방법을 보여줍니다.

```swift
// Show traffic on the map using the traffic options.
map.setTrafficOptions([
    .incidents(true),
    .flow(.relative)
])
```

다음 스크린샷은 맵의 실시간 트래픽 정보를 렌더링하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/show-traffic-data-map-ios/ios-show-traffic.png" alt-text="실시간 트래픽 정보를 보여주는 맵입니다.":::

## <a name="get-traffic-incident-details"></a>트래픽 인시던트 세부 정보 가져오기

트래픽 인시던트에 대한 세부 정보는 맵에 인시던트를 표시하는 데 사용되는 기능의 속성 내에서 사용할 수 있습니다. 트래픽 인시던트는 Azure Maps 트래픽 인시던트 벡터 타일 서비스를 사용하여 맵에 추가됩니다. 이러한 벡터 타일의 데이터 형식은 TomTom 사이트의 [벡터 인시던트 타일](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) 문서에서 찾을 수 있습니다. 다음 코드는 클릭 이벤트를 처리하는 대리자를 맵에 추가하고, 클릭한 트래픽 인시던트 기능을 검색하고, 일부 세부 정보가 포함된 경고를 표시합니다.

```swift
// Show traffic information on the map.
map.setTrafficOptions([
    .incidents(true),
    .flow(.relative)
])

// Add the delegate to handle taps on traffic incidents only.
map.events.addDelegate(self, for: [.trafficIncidentLayerId])
```

```swift
extension ShowDetailedTrafficViewController: AzureMapDelegate {
    func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
        guard let feature = features.first else {
            // No features provided.
            return
        }

        // Get the incident's type based on icon_category_[idx] property.
        let iconCategoryKeys = ["icon_category"] + (0...9).map { "icon_category_\($0)" }
        let title = iconCategoryKeys
            .compactMap { feature.properties[$0] as? Int }
            .last
            .map { IncidentCategory(categoryNumber: $0).description }

        // Get the incident's description based on description_[idx] property.
        let descriptionKeys = ["description"] + (0...9).map { "description_\($0)" }
        let message = descriptionKeys
            .compactMap { feature.properties[$0] as? String }
            .last
            .map { $0.capitalized }

        // Show an alert with the details.
        let alert = UIAlertController(title: title ?? "Traffic Incident", message: message, preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "OK", style: .default) { _ in
            alert.dismiss(animated: true)
        })
        present(alert, animated: true)
    }
}
```

다음 스크린샷은 인시던트 세부 정보를 표시하는 경고와 함께 맵에서 실시간 트래픽 정보를 렌더링하는 위의 코드를 보여줍니다.

:::image type="content" source="./media/ios-sdk/show-traffic-data-map-ios/ios-traffic-details.png" alt-text="인시던트 세부 정보를 표시하는 알림 메시지와 함께 실시간 트래픽 정보를 보여주는 맵입니다.":::

## <a name="filter-traffic-incidents"></a>교통 사고 필터링

대부분의 주요 도시에서는 일반적으로 교통 사고가 압도적으로 많을 수 있지만 시나리오에 따라 이러한 사고의 하위 집합을 필터링하고 표시하는 것이 바람직할 수 있습니다. 트래픽 옵션을 설정하는 경우 `incidentCategoryFilter` `incidentMagnitudeFilter` 인시던트 범주 또는 크기 열거자의 배열에서 받는 및 옵션이 있습니다.

다음 표는 `incidentCategoryFilter` 옵션 내에서 사용할 수 있는 모든 교통 사고 범주를 보여 줍니다.

| 범주 열거형 | 설명 |
|--------------------|-------------|
| `IncidentCategory.unknown` | 정의된 범주에 맞지 않거나 아직 분류되지 않은 사고입니다. |
| `IncidentCategory.accident` | 교통 사고. |
| `IncidentCategory.fog` | 가시성을 감소시키고 교통 흐름을 줄이며 사고 위험을 증가시킬 수 있는 안개. |
| `IncidentCategory.dangerousConditions` | 도로 위의 물체와 같은 도로 위의 위험한 상황. |
| `IncidentCategory.rain` | 가시성을 감소시키고 운전 조건을 어렵게 하며 사고 위험을 증가시킬 수 있는 폭우. |
| `IncidentCategory.ice` | 운전이 어렵거나 위험할 수 있는 빙판길. |
| `IncidentCategory.jam` | 교통 흐름을 느리게 하는 교통 체증. |
| `IncidentCategory.laneClosed` | 차선이 폐쇄됨. |
| `IncidentCategory.roadClosed` | 도로가 폐쇄됨. |
| `IncidentCategory.roadWorks` | 이 지역의 도로 공사/건설. |
| `IncidentCategory.wind` | 측면 프로필이 크거나 무게 중심이 높은 차량의 운전을 어렵게 만들 수 있는 강풍. |
| `IncidentCategory.flooding` | 도로에 발생하는 홍수. |
| `IncidentCategory.detour` | 우회하라고 지시하는 교통 안내. |
| `IncidentCategory.cluster` | 다양한 범주의 교통 사고 클러스터. 지도를 확대하면 클러스터가 개별 사고로 분리됩니다. |

다음 표는 `incidentMagnitudeFilter` 옵션 내에서 사용할 수 있는 모든 교통 사고 규모를 보여 줍니다.

| 크기 열거형 | 설명 |
|--------------------|-------------|
| `IncidentMagnitude.unknown` | 규모가 아직 분류되지 않은 사고. |
| `IncidentMagnitude.minor` | 종종 정보용으로만 사용되며 교통 흐름에 최소한의 영향을 미치는 사소한 교통 문제입니다. |
| `IncidentMagnitude.moderate` | 교통 흐름에 약간의 영향을 미치는 보통의 교통 문제입니다. |
| `IncidentMagnitude.major` |  교통 흐름에 상당한 영향을 미치는 주요 교통 문제입니다. |

다음은 중간 정도의 교통 체증과 위험한 상황의 사고만 지도에 표시되도록 교통 사고를 필터링합니다.

```swift
map.setTrafficOptions([
    .incidents(true),
    .incidentMagnitudeFilter([.moderate]),
    .incidentCategoryFilter([.jam, .dangerousConditions])
])
```

다음 스크린샷은 중간 정도의 교통 체증과 위험한 상황의 사고에 대한 지도를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/show-traffic-data-map-ios/ios-traffic-incident-filters.png" alt-text="중간 정도의 교통 체증 및 위험한 상황의 사고 지도.":::

> [!NOTE]
> 일부 교통 사고에는 여러 범주가 할당될 수 있습니다. 사고에는 `incidentCategoryFilter`에 전달된 옵션과 일치하는 범주가 있는 경우 해당 범주가 표시됩니다. 기본 사고 범주는 필터에 지정된 범주와 다를 수 있으므로 다른 아이콘이 표시됩니다.

## <a name="additional-information"></a>추가 정보

맵에 데이터를 추가하는 방법에 대한 자세한 내용은 다음 가이드를 참조하세요.

* [타일 레이어 추가](add-tile-layer-map-ios.md)
* [기호 계층 추가](add-symbol-layer-ios.md)
* [거품형 계층 추가](add-bubble-layer-map-ios.md)
* [선 계층 추가](add-line-layer-map-ios.md)
* [다각형 계층 추가](add-polygon-layer-map-ios.md)
