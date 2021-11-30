---
title: iOS 지도 | 지도 스타일 설정 Microsoft Azure 지도
description: 지도의 스타일을 설정하는 두 가지 방법을 알아봅니다. 레이아웃 파일 또는 작업 클래스에서 Azure 지도 iOS SDK를 사용하여 스타일을 조정하는 방법을 참조하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/22/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 562336a14d499d58c01633173d77a1f6930095a5
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219051"
---
# <a name="set-map-style-in-the-ios-sdk-preview"></a>iOS SDK에서 지도 스타일 설정(미리 보기)

이 문서에서는 Azure 지도 iOS SDK를 사용하여 맵 스타일을 설정하는 두 가지 방법을 보여줍니다. Azure Maps에서는 여섯 가지 다른 지도 스타일 중에서 선택할 수 있습니다. 지원되는 지도 스타일에 대한 자세한 내용은 [Azure Maps에서 지원되는 지도 스타일](supported-map-styles.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: iOS 앱 만들기](quick-ios-app.md) 문서의 단계를 완료해야 합니다.

> [!IMPORTANT]
> 이 섹션의 절차에는 Gen 1 또는 Gen 2 가격 책정 계층의 Azure Maps 계정이 필요합니다. 가격 책정 계층에 대한 자세한 내용은 [Azure Maps에서 적절한 가격 책정 계층 선택](choose-pricing-tier.md)을 참조하세요.

## <a name="set-map-style-in-the-map-control-init"></a>지도 컨트롤 init에서 지도 스타일 설정

지도 컨트롤 init에서 지도 스타일을 설정할 수 있습니다. 다음 코드는 중심 위치, 확대/축소 수준 및 지도 스타일을 설정합니다.

```swift
MapControl(options: [
    CameraOption.center(lat: 47.602806, lng: -122.329330),
    CameraOption.zoom(12),
    StyleOption.style(.grayscaleDark)
])
```

다음 스크린샷은 도로 지도를 회색조 어두운 스타일로 표시하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/set-map-style-ios/ios-grayscale-dark.png" alt-text="회색조 어두운 로드맵 스타일이 있는 지도의 스크린샷.":::

## <a name="set-map-style-via-setstyleoptions-method"></a>메서드를 통해 맵 스타일 설정 `setStyleOptions`

지도의 `setStyleOptions` 메서드를 사용하여 코드에서 프로그래밍 방식으로 지도 스타일을 설정할 수 있습니다. 다음 코드는 지도 `setCameraOptions` 메서드와 지도 스타일을 사용하여 중심 위치 및 확대/축소 수준을 `.satelliteRoadLabels`(으)로 설정합니다.

```swift
mapControl.onReady { map in

    //Set the camera of the map.
    map.setCameraOptions([
        .center(lat: 47.64, lng: -122.33),
        .zoom(14)
    ])

    //Set the style of the map.
    map.setStyleOptions([.style(.satelliteRoadLabels)])
}
```

다음 스크린샷은 위성 도로 레이블 스타일로 지도를 표시하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/set-map-style-ios/ios-satellite-road-labels.png" alt-text="위성 도로 레이블 스타일이 있는 지도의 스크린샷.":::

## <a name="set-the-map-camera"></a>지도 카메라 설정

지도 카메라는 지도 뷰포트에 세계의 어느 부분이 표시되는지 제어합니다. 맵의 위치를 설정하는 두 가지 주요 메서드가 있습니다. 가운데 및 확대/축소를 사용하거나 경계 상자를 전달합니다. 다음 코드에서는 `center` 및 `zoom`을(를) 사용할 때 모든 선택적 카메라 옵션을 설정하는 방법을 보여 줍니다.

```swift
//Set the camera of the map using center and zoom.
map.setCameraOptions([
    .center(lat: 47.64, lng: -122.33),
    .zoom(14),
    .pitch(45),
    .bearing(90),
    .minZoom(10),
    .maxZoom(14)
])
```

데이터 집합보다 지도에 초점을 맞추는 것이 바람직한 경우가 종종 있습니다. 경계 상자는 `BoundingBox.fromData(_:)` 메서드를 사용하여 기능에서 계산될 수 있고 지도 카메라의 `bounds` 옵션으로 전달될 수 있습니다. 경계 상자를 기반으로 맵 보기를 설정하는 경우 `padding` 거품 또는 기호로 렌더링되는 데이터 포인트의 지점 크기를 고려하여 값을 지정하는 것이 유용한 경우가 많습니다. 다음 코드에서는 경계 상자를 사용하여 카메라의 위치를 설정하는 경우 모든 선택적 카메라 옵션을 설정하는 방법을 보여 줍니다.

```swift
//Set the camera of the map using a bounding box.
map.setCameraBoundsOptions([
    .bounds(
        BoundingBox(
            sw: CLLocationCoordinate2D(latitude: 47.4333, longitude: -122.4594),
            ne: CLLocationCoordinate2D(latitude: 47.75758, longitude: -122.21866)
        )
    ),
    .padding(20),
    .maxZoom(14)
])
```

경계 상자의 가로 세로 비율은 지도의 가로 세로 비율과 동일하지 않을 수 있습니다. 이와 같이 지도는 전체 경계 상자 영역을 표시하지만 세로 또는 가로로만 밀집되는 경우가 많습니다.

### <a name="animate-map-view"></a>맵 보기 애니메이션

맵의 카메라 옵션을 설정하는 경우 애니메이션 옵션을 사용하여 현재 맵 보기와 다음 맵 보기 간의 전환을 만들 수도 있습니다. 이 옵션은 애니메이션 유형과 카메라를 이동하는 데 걸리는 시간을 지정합니다.

| 옵션 | 설명 |
|--------|-------------|
| `animationDuration(_ duration: Double)` | 카메라가 보기 간에 애니메이션을 적용할 시간(밀리초)을 지정합니다. |
| `animationType(_ animationType: AnimationType)` | 수행할 애니메이션 전환 유형을 지정합니다.<br/><br/> - `.jump` - 즉각적인 변경.<br/> - `.ease` - 카메라 설정의 점진적 변경.<br/> - `.fly` - 비행과 유사한 arc에 따라 카메라 설정을 점진적으로 변경합니다. |

다음 코드에서는 3초 동안 `.fly` 애니메이션을 사용하여 맵 보기에 애니메이션을 적용하는 방법을 보여줍니다.

```swift
map.setCameraOptions([
    .animationType(.fly),
    .animationDuration(3000)
    .center(lat: 47.6, lng: -122.33),
    .zoom(12),
])
```

다음은 뉴욕에서 시애틀까지의 맵 보기에 애니메이션을 적용하는 위의 코드를 보여줍니다.

:::image type="content" source="./media/ios-sdk/set-map-style-ios/ios-animate-camera.gif" alt-text="카메라 애니메이션을 New York에서 Seattle로 매핑합니다.":::

## <a name="additional-information"></a>추가 정보

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

- [기호 계층 추가](add-symbol-layer-ios.md)
- [거품형 계층 추가](add-bubble-layer-map-ios.md)
