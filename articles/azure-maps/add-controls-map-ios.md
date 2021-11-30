---
title: IOS 맵에 컨트롤 추가
titleSuffix: Microsoft Azure Maps
description: Microsoft Azure 지도 iOS SDK를 통해 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 맵에 추가 하는 방법입니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 337761dde1b3182060bbd0d98d1b14b1e3168f05
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219002"
---
# <a name="add-controls-to-a-map-in-the-ios-sdk-preview"></a>IOS SDK에서 지도에 컨트롤 추가 (미리 보기)

이 문서에서는 지도에 UI 컨트롤을 추가하는 방법을 보여 줍니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 지도를 확대/축소하는 단추를 추가합니다. 다음 코드 샘플은 `ZoomControl` 클래스의 인스턴스를 만들고 지도에 추가합니다.

```swift
// Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

아래 스크린샷은 지도에 로드된 확대/축소 컨트롤입니다.

:::image type="content" source="./media/ios-sdk/add-controls-to-map-ios/zoom.png" alt-text="지도의 확대/축소 컨트롤을 보여 주는 스크린샷" lightbox="./media/ios-sdk/add-controls-to-map-ios/zoom.png":::

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 수평을 기준으로 지도에 피치를 기울이기 위한 단추를 추가합니다. 다음 코드 샘플에서는 `PitchControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

```swift
// Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

아래 스크린샷은 지도에 로드된 피치 컨트롤입니다.

:::image type="content" source="./media/ios-sdk/add-controls-to-map-ios/pitch.png" alt-text="지도의 피치 컨트롤을 보여 주는 스크린샷" lightbox="./media/ios-sdk/add-controls-to-map-ios/pitch.png":::

## <a name="add-rotation-control"></a>회전 컨트롤 추가

회전 컨트롤은 지도를 회전 하는 단추를 추가 합니다. 다음 코드 샘플에서는 `RotationControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

```swift
// Construct a rotation control and add it to the map.
map.controls.add(RotationControl())
```

아래 스크린샷에서는 맵에 로드 된 회전 컨트롤이 있습니다.

:::image type="content" source="./media/ios-sdk/add-controls-to-map-ios/rotation.png" alt-text="지도의 회전 컨트롤을 보여 주는 스크린샷" lightbox="./media/ios-sdk/add-controls-to-map-ios/rotation.png":::

## <a name="add-traffic-control"></a>트래픽 컨트롤 추가

트래픽 컨트롤은 지도에 트래픽 데이터를 표시/숨기는 단추를 추가합니다. 다음 코드 샘플에서는 `TrafficControl` 클래스의 인스턴스를 만들어 지도에 추가합니다.

```swift
// Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

아래 스크린샷은 지도에 로드된 트래픽 컨트롤입니다.

:::image type="content" source="./media/ios-sdk/add-controls-to-map-ios/traffic.png" alt-text="지도의 트래픽 컨트롤을 보여 주는 스크린샷" lightbox="./media/ios-sdk/add-controls-to-map-ios/traffic.png":::

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 배치하고 한 번에 모두 지도에 추가하고 지도의 동일한 영역에 배치하여 개발을 간소화할 수 있습니다. 다음 코드에서는 이 방법을 사용하여 지도에 표준 탐색 컨트롤을 추가합니다.

```swift
map.controls.add([
    ZoomControl(),
    RotationControl(),
    PitchControl(),
    TrafficControl()
])
```

아래 스크린샷에서는 지도에 로드된 모든 컨트롤을 보여 줍니다. 지도에 추가되는 순서가 표시되는 순서입니다.

:::image type="content" source="./media/ios-sdk/add-controls-to-map-ios/all.png" alt-text="모든 컨트롤이 추가 된 맵을 보여 주는 스크린샷" lightbox="./media/ios-sdk/add-controls-to-map-ios/all.png":::

## <a name="additional-information"></a>추가 정보

다음 문서에서는 맵에 사용 가능한 다른 레이어를 추가 하는 방법을 보여 줍니다.

- [기호 계층 추가](add-symbol-layer-ios.md)
- [거품형 계층 추가](add-bubble-layer-map-ios.md)
- [선 계층 추가](add-line-layer-map-ios.md)
- [다각형 계층 추가](add-polygon-layer-map-ios.md)
