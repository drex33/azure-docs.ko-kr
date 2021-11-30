---
title: IOS maps에서 맵 이벤트 처리
titleSuffix: Microsoft Azure Maps
description: 사용자가 맵과 상호 작용할 때 발생하는 이벤트에 대해 알아봅니다. 지원되는 모든 맵 이벤트 목록을 봅니다. Azure Maps iOS SDK를 사용 하 여 이벤트를 처리 하는 방법을 참조 하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 095e4da80c847caf20348f7fced17f7067b76af9
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133219062"
---
# <a name="interact-with-the-map-in-the-ios-sdk-preview"></a>IOS SDK (미리 보기)의 맵과 상호 작용

이 문서에서는 맵 이벤트 관리자를 사용하는 방법을 보여 줍니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

맵은 `events` 프로토콜을 따르는 대리자를 허용 하는 속성을 통해 모든 이벤트를 관리 합니다 `AzureMapDelegate` . 다음 표에서는 프로토콜의 메서드로 표시 되는 모든 지원 되는 맵 이벤트를 보여 줍니다 `AzureMapDelegate` .

| 메서드                 | Description |
|------------------------|-------------|
| `azureMapCameraIsIdle(_ map: AzureMap)` | <p>맵이 "유휴" 상태가 되기 전에 마지막 프레임이 렌더링 된 후에 발생 합니다.<ul><li>진행 중인 카메라 전환이 없습니다.</li><li>현재 요청된 모든 타일이 로드되었습니다.</li><li>모든 페이드/전환 애니메이션이 완료되었습니다.</li></ul></p> |
| `azureMapCameraIsMoving(_ map: AzureMap)` | 한 뷰에서 다른 뷰로 애니메이션 전환 하는 동안 반복적으로 발생 합니다. 이는 사용자 상호 작용 또는 방법의 결과일 수 있습니다. |
| `azureMapCameraMoveWasCanceled(_ map: AzureMap)` | 카메라에 대 한 이동 요청을 취소할 때 발생 합니다. |
| `azureMap(_ map: AzureMap, cameraMoveIsStarted reason: CameraChangeReason)` | 맵이 한 뷰에서 다른 뷰로 전환 되기 시작 하기 직전에 발생 합니다. 이는 프로그래밍 방식으로 또는 사용자 상호 작용의 결과로 발생할 수 있습니다. `reason`매개 변수는 카메라 이동이 시작 된 방법에 대 한 세부 정보를 제공 하는 옵션 집합입니다. 다음 목록에서는 가능한 이유를 간략하게 설명합니다.<ul><li>Unknown</li><li>프로그래밍 방식</li><li>북쪽 다시 설정</li><li>이동 제스처</li><li>손가락으로 제스처</li><li>회전 제스처</li><li>Zoom-In 제스처</li><li>Zoom-Out 제스처</li><li>원-손가락-확대/축소 제스처 </li><li>기울기 제스처</li><li>전환이 취소 됨</li></ul>   |
| `azureMap(_ map: AzureMap, didTapAt location: CLLocationCoordinate2D)` | 지도의 동일한 지점에서 지도를 눌렀다 놓았을 때 발생 합니다. |
| `azureMap(_ map: AzureMap, didTapOn features: [Feature])` | 지도를 눌렀다가 동일한 지점에서 해제할 때 발생 합니다. |
| `azureMap(_ map: AzureMap, didAddLayer layer: Layer)` | 지도에 계층이 추가 될 때 발생 합니다. |
| `azureMap(_ map: AzureMap, didRemoveLayer layer: Layer)` | 지도에서 레이어가 제거 될 때 발생 합니다. |
| `azureMapWillLoad(_ map: AzureMap)` | 렌더링에 필요한 리소스를 다운로드 하기 전에 발생 합니다. |
| `azureMapDidLoad(_ map: AzureMap)` | 리소스가 다운로드 되 고 맵의 첫 번째 시각적 렌더링이 완료 된 후에 발생 합니다. |
| `azureMap(_ map: AzureMap, didLongPressAt location: CLLocationCoordinate2D)` | 지도를 누른 후 잠시 동안 보관 한 다음 지도의 동일한 지점에서 해제할 때 발생 합니다. |
| `azureMap(_ map: AzureMap, didLongPressOn features: [Feature])` | 지도를 누른 후 잠시 동안 유지 한 다음 기능의 동일한 지점에서 해제할 때 발생 합니다. |
| `azureMapIsReady(_ map: AzureMap)` | 다음 조건이 충족 될 때 발생 합니다.<ul><li>처음에 로드 되는 맵 </li><li>앱 방향 변경 </li><li>필요한 최소 맵 리소스 로드 완료 </li><li>지도를 프로그래밍 방식으로와 상호 작용할 준비가 되었습니다.</li></ul> |
| `azureMap(_ map: AzureMap, didAddSource source: Source)` | `DataSource`또는 `VectorTileSource` 가 맵에 추가 될 때 발생 합니다. |
| `azureMap(_ map: AzureMap, didRemoveSource source: Source)` | `DataSource`또는 `VectorTileSource` 가 맵에서 제거 될 때 발생 합니다. |
| `azureMapStyleDidChange(_ map: AzureMap)` | 지도의 스타일이 로드 되거나 변경 될 때 발생 합니다. |

다음 코드에서는 `azureMap(_ map: AzureMap, didTapAt location: CLLocationCoordinate2D)` , `azureMap(_ map: AzureMap, didTapOn features: [Feature])` 및 `azureMapCameraIsMoving(_ map: AzureMap)` 이벤트를 맵에 추가하는 방법을 보여 줍니다.

```swift
class ShowSimpleEventsHandlingViewController: UIViewController, AzureMapDelegate {

    // Other Setup...

    func setupMapControl() {
        mapControl.onReady { map in

            // Add the delegate to the map to respond to events.
            map.events.addDelegate(self)
        }
    }

    func azureMap(_ map: AzureMap, didTapAt location: CLLocationCoordinate2D) {
        // Map clicked.
    }

    func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
        // Feature clicked.
    }

    func azureMapCameraIsMoving(_ map: AzureMap) {
        // Map camera moved.
    }
}
```

자세한 내용은 맵 및 트리거 이벤트와 상호 작용 하는 방법에 대 한 [맵 탐색](how-to-use-ios-map-control-library.md) 문서를 참조 하세요.

## <a name="scope-feature-events-to-layer"></a>계층에 대한 기능 이벤트 범위

맵에 대리자를 추가 하는 경우 계층 Id를 두 번째 매개 변수로 전달할 수 있습니다. 계층이 전달 되 면 해당 계층에서 이벤트가 발생 하는 경우에만 이벤트가 발생 합니다. 계층으로 범위가 지정된 이벤트는 기호, 거품형, 선형 및 다각형 계층이 지원됩니다.

```swift
class ShowScopedEventsHandlingViewController: UIViewController, AzureMapDelegate {
    
    // Other Setup...

    func setupMapControl() {
        mapControl.onReady { map in

            // Create a data source.
            let source = DataSource()
            map.sources.add(source)

            // Add data to the data source.
            source.add(geometry: Point(CLLocationCoordinate2D(latitude: 0, longitude: 0)))

            // Create a layer and add it to the map.
            let layer = BubbleLayer(source: source)
            map.layers.addLayer(layer)

            // Add the delegate to the map to respond to events.
            map.events.addDelegate(self, for: [layer.id])
        }
    }

    func azureMap(_ map: AzureMap, didTapOn features: [Feature]) {
        // One or more features tapped.
    }

    func azureMap(_ map: AzureMap, didLongPressOn features: [Feature]) {
        // One or more features long pressed.
    }
}
```

## <a name="additional-information"></a>추가 정보

전체 코드 예제는 다음 문서를 참조하세요.

- [맵 탐색](how-to-use-ios-map-control-library.md)
- [기호 계층 추가](add-symbol-layer-ios.md)
- [거품형 계층 추가](add-bubble-layer-map-ios.md)
- [선 계층 추가](add-line-layer-map-ios.md)
- [다각형 계층 추가](add-polygon-layer-map-ios.md)
