---
title: Android 맵에 트래픽 데이터 표시 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용하여 맵에 트래픽 데이터를 표시하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 012fdf6e35b0b0c27f8ad9afe10b5f70709fcc64
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093565"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>맵에 트래픽 데이터 표시(Android SDK)

흐름 데이터 및 인시던트 데이터는 맵에 표시 될 수 있는 두 가지 트래픽 데이터 유형입니다. 이 가이드에서는 두 가지 유형의 트래픽 데이터를 표시하는 방법을 보여 줍니다. 인시던트 데이터는 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다. 흐름 데이터는 도로의 트래픽 흐름에 대한 메트릭을 표시합니다.

## <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료해야 합니다. 이 문서의 코드 블록은 맵 `onReady` 이벤트 처리기에 삽입할 수 있습니다.

## <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

Azure Maps에서 사용할 수 있는 트래픽 데이터에는 두 가지 유형이 있습니다.

- 인시던트 데이터 - 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다.
- 흐름 데이터 - 도로의 트래픽 흐름에 대한 메트릭을 제공합니다. 트래픽 흐름 데이터를 사용하여 도로 색을 조정하는 경우가 많습니다. 이 색은 속도 제한 또는 다른 메트릭을 기준으로 흐름을 저하시키는 트래픽 양을 기반으로 합니다. 맵의 트래픽 `flow` 옵션에 전달할 수 있는 4개의 값이 있습니다.

    |흐름 열거형 | 설명|
    | :-- | :-- |
    | `TrafficFlow.NONE` | 맵에 트래픽 데이터를 표시하지 않습니다. |
    | `TrafficFlow.RELATIVE` | 도로의 자유 흐름 속도에 비례하여 트래픽 데이터를 표시합니다. |
    | `TrafficFlow.RELATIVE_DELAY` | 평균 예상 지연 시간보다 느린 영역을 표시합니다. |
    | `TrafficFlow.ABSOLUTE` | 도로에 있는 모든 차량의 절대 속도를 보여 줍니다. |

다음 코드에서는 맵에 트래픽 데이터를 표시하는 방법을 보여줍니다.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

다음 스크린샷은 맵의 실시간 트래픽 정보를 렌더링하는 위의 코드를 보여 줍니다.

![실시간 트래픽 정보를 표시하는 맵](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>트래픽 인시던트 세부 정보 가져오기

트래픽 인시던트에 대한 세부 정보는 맵에 인시던트를 표시하는 데 사용되는 기능의 속성 내에서 사용할 수 있습니다. 트래픽 인시던트는 Azure Maps 트래픽 인시던트 벡터 타일 서비스를 사용하여 맵에 추가됩니다. [여기에 설명된](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) 경우, 벡터 타일에 있는 데이터의 형식입니다. 다음 코드는 맵에 클릭 이벤트를 추가하고 클릭한 트래픽 인시던트 기능을 검색하여 일부 세부 정보를 포함하는 알림 메시지를 표시합니다.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

다음 스크린샷은 인시던트 정보를 표시하는 알림 메시지가 포함된 맵의 실시간 트래픽 정보를 렌더링하는 위의 코드를 보여 줍니다.

![인시던트 정보를 표시하는 알림 메시지를 사용하여 실시간 트래픽 정보를 표시하는 맵](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="filter-traffic-incidents"></a>교통 사고 필터링

대부분의 주요 도시에서는 일반적으로 교통 사고가 압도적으로 많을 수 있지만 시나리오에 따라 이러한 사고의 하위 집합을 필터링하고 표시하는 것이 바람직할 수 있습니다. 트래픽 옵션을 설정할 때 사고 범주 또는 규모 열거자 또는 문자열 값의 배열을 취하는 `incidentCategoryFilter` 및 `incidentMagnitudeFilter` 옵션이 있습니다.

다음 표는 `incidentCategoryFilter` 옵션 내에서 사용할 수 있는 모든 교통 사고 범주를 보여 줍니다.

| 범주 열거형 | 문자열 값 | 설명 |
|--------------------|--------------|-------------|
| `IncidentCategory.UNKNOWN` | `"unknown"` | 정의된 범주에 맞지 않거나 아직 분류되지 않은 사고입니다. |
| `IncidentCategory.ACCIDENT` | `"accident"` | 교통 사고. |
| `IncidentCategory.FOG` | `"fog"` | 가시성을 감소시키고 교통 흐름을 줄이며 사고 위험을 증가시킬 수 있는 안개. |
| `IncidentCategory.DANGEROUS_CONDITIONS` | `"dangerousConditions"` | 도로 위의 물체와 같은 도로 위의 위험한 상황. |
| `IncidentCategory.RAIN` | `"rain"` | 가시성을 감소시키고 운전 조건을 어렵게 하며 사고 위험을 증가시킬 수 있는 폭우. |
| `IncidentCategory.ICE` | `"ice"` | 운전이 어렵거나 위험할 수 있는 빙판길. |
| `IncidentCategory.JAM` | `"jam"` | 교통 흐름을 느리게 하는 교통 체증. |
| `IncidentCategory.LANE_CLOSED` | `"laneClosed"` | 차선이 폐쇄됨. |
| `IncidentCategory.ROAD_CLOSED` | `"roadClosed"` | 도로가 폐쇄됨. |
| `IncidentCategory.ROAD_WORKS` | `"roadWorks"` | 이 지역의 도로 공사/건설. |
| `IncidentCategory.WIND` | `"wind"` | 측면 프로필이 크거나 무게 중심이 높은 차량의 운전을 어렵게 만들 수 있는 강풍. |
| `IncidentCategory.FLOODING` | `"flooding"` | 도로에 발생하는 홍수. |
| `IncidentCategory.DETOUR` | `"detour"` | 우회하라고 지시하는 교통 안내. |
| `IncidentCategory.CLUSTER` | `"cluster"` | 다양한 범주의 교통 사고 클러스터. 지도를 확대하면 클러스터가 개별 사고로 분리됩니다. |
| `IncidentCategory.BROKEN_DOWN_VEHICLE` | `"brokenDownVehicle"` | 도로 위 또는 옆에 고장난 차량. |

다음 표는 `incidentMagnitudeFilter` 옵션 내에서 사용할 수 있는 모든 교통 사고 규모를 보여 줍니다.

| 크기 열거형 | 문자열 값 | 설명 |
|--------------------|--------------|-------------|
| `IncidentMagnitude.UNKNOWN` | `"unknown"` | 규모가 아직 분류되지 않은 사고. |
| `IncidentMagnitude.MINOR` | `"minor"` | 종종 정보용으로만 사용되며 교통 흐름에 최소한의 영향을 미치는 사소한 교통 문제입니다. |
| `IncidentMagnitude.MODERATE` | `"moderate"` | 교통 흐름에 약간의 영향을 미치는 보통의 교통 문제입니다. |
| `IncidentMagnitude.MAJOR` | `"major"` |  교통 흐름에 상당한 영향을 미치는 주요 교통 문제입니다. |

다음은 중간 정도의 교통 체증과 위험한 상황의 사고만 지도에 표시되도록 교통 사고를 필터링합니다.

::: zone pivot="programming-language-java-android"

``` java
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(new String[] { IncidentMagnitude.MODERATE }),
    incidentCategoryFilter(new String[] { IncidentCategory.DANGEROUS_CONDITIONS, IncidentCategory.JAM })              
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    incidentMagnitudeFilter(*arrayOf(IncidentMagnitude.MODERATE)),
    incidentCategoryFilter(
        *arrayOf(
            IncidentCategory.DANGEROUS_CONDITIONS,
            IncidentCategory.JAM
        )
    )
)
```

::: zone-end

다음 스크린샷은 중간 정도의 교통 체증과 위험한 상황의 사고에 대한 지도를 보여 줍니다.

![중간 정도의 교통 체증 및 위험한 상황의 사고 지도.](media/how-to-show-traffic-android/android-traffic-incident-filters.jpg)

> [!NOTE]
> 일부 교통 사고에는 여러 범주가 할당될 수 있습니다. 사고에는 `incidentCategoryFilter`에 전달된 옵션과 일치하는 범주가 있는 경우 해당 범주가 표시됩니다. 기본 사고 범주는 필터에 지정된 범주와 다를 수 있으므로 다른 아이콘이 표시됩니다.

## <a name="next-steps"></a>다음 단계

맵에 데이터를 추가하는 방법에 대한 자세한 내용은 다음 가이드를 참조하세요.

> [!div class="nextstepaction"]
> [타일 레이어 추가](how-to-add-tile-layer-android-map.md)
