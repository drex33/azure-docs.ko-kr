---
title: Azure 지도 실내 맵 모듈을 사용하여 Microsoft Creator 서비스를 사용하여 iOS 애플리케이션 개발
description: iOS SDK용 Microsoft Azure 지도 실내 맵 모듈을 사용하여 모듈의 JavaScript 라이브러리를 포함시켜 맵을 렌더링하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: a46ac1a160ccb391ce51f6f1ef0c0b9fb9f3c35b
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817886"
---
# <a name="using-indoor-maps-in-the-ios-sdk"></a>iOS SDK에서 실내 맵 사용

Azure 지도 iOS SDK를 사용하면 Azure 지도 Creator 서비스에서 만든 실내 맵을 렌더링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [빠른 시작: iOS 앱 만들기의](quick-ios-app.md)단계를 완료해야 합니다. 이 문서의 코드 블록은 의 함수에 삽입할 수  `viewDidLoad` `ViewController` 있습니다.
1. [Creator 리소스를 만듭니다](how-to-manage-creator.md).
1. 실내 `tilesetId` [맵을 만드는 자습서를](tutorial-creator-indoor-maps.md)완료하여 를 얻습니다. 이 식별자를 사용하여 Azure 지도 iOS SDK로 실내 맵을 렌더링해야 합니다.

## <a name="instantiate-the-indoor-manager"></a>실내 관리자 인스턴스화

타일의 실내 타일 세트 및 지도 스타일을 로드하려면 를 `IndoorManager` 인스턴스화하고 이에 대한 강력한 참조를 유지해야 합니다.

```swift
guard let indoor = try? IndoorManager(azureMap: map, options: [.tilesetID({Your-tilesetID})]) else { return }
self.indoorManager = indoor
```

> [!IMPORTANT]
> 이 가이드에서는 작성자 서비스가 미국 만들어진 것으로 가정합니다. Creator 서비스가 서유럽에서 만들어진 경우 다음 코드를 추가합니다.
>
> ```swift
> self.indoorManager.setOptions([.geography(.eu)])
> ```

## <a name="indoor-level-picker-control"></a>실내 수준 선택 컨트롤

*Indoor Level Picker* 컨트롤을 사용하여 렌더링된 맵의 수준을 변경할 수 있습니다. 필요에 따라 `IndoorControl` 를 초기화하고 다음 코드와 같이 에서 적절한 옵션으로 설정할 수 있습니다. `IndoorManager`

```swift
let levelControl = IndoorControl(options: [.controlPosition(.topRight)])
self.indoorManager.setOptions([.levelControl(levelControl)])
```

> [!TIP]
> 기능을 탭하면 *수준 선택기가* 나타납니다.

## <a name="indoor-events"></a>실내 이벤트

`IndoorManager`실내 맵 이벤트를 수신 대기하는 대리자를 에 추가합니다.

```swift
self.indoorManager.addDelegate(self)
```

`IndoorManagerDelegate` 에는 시설 또는 층이 변경될 때 호출되는 메서드가 하나 있습니다.

```swift
func indoorManager(
    _ manager: IndoorManager,
    didSelectFacility selectedFacility: IndoorFacilitySelection,
    previousSelection: IndoorFacilitySelection
) {
    // code that you want to run after a facility or floor has been changed
    print("New selected facility's ID:", selectedFacility.facilityID)
    print("New selected floor:", selectedFacility.levelsOrdinal)
}
```

## <a name="example"></a>예제

아래 스크린샷은 실내 맵을 표시하는 위의 코드를 보여줍니다.

![위의 샘플 코드를 사용하여 만든 실내 지도를 표시하는 스크린샷.](./media/ios-sdk/indoor-maps/indoor.png)

## <a name="additional-information"></a>추가 정보

- [실내 맵용 Creator](creator-indoor-maps.md)
- [드로잉 패키지 요구 사항](drawing-requirements.md)
