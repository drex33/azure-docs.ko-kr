---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 2bc30ff8b610b48973c6d72b86fb897ad70f7db9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705626"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="record-calls"></a>통화 녹음
> [!WARNING]
> ACS 통화 iOS SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isRecordingActive`가 `Call` 개체의 일부이고 `didChangeRecordingState`는 `CallDelegate` 대리자의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 iOS SDK의 ‘베타’ 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```swift
let callRecordingFeature = call.api(RecordingFeature.self)
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingFeature`의 `isRecordingActive` 속성을 검사합니다. `Bool`를 반환합니다.

```swift
let isRecordingActive = callRecordingFeature.isRecordingActive;
```

이벤트 `didChangeRecordingState`를 사용하여 클래스에 `RecordingFeatureDelegate` 대리자를 구현하여 변경 내용 기록을 구독할 수도 있습니다.

```swift
callRecordingFeature.delegate = self

// didChangeRecordingState is a member of RecordingFeatureDelegate
public func recordingFeature(_ recordingFeature: RecordingFeature, didChangeRecordingState args: PropertyChangedEventArgs) {
    let isRecordingActive = recordingFeature.isRecordingActive
}
```

애플리케이션에서 녹음/녹화를 시작하려면 먼저 [통화 녹음/녹화 개요](../../../../concepts/voice-video-calling/call-recording.md)에 따라 통화 녹음/녹화를 설정하는 단계를 따르세요.

서버에 통화 녹음/녹화를 설정하고 나면 iOS 애플리케이션에서 통화에서 `ServerCallId` 값을 얻은 다음, 이를 서버로 보내 녹음/녹화 프로세스를 시작해야 합니다. `ServerCallId` 값은 `getInfo()`를 사용하여 클래스 개체에서 찾을 수 있는 `CallInfo` 클래스의 `getServerCallId()`를 사용하여 찾을 수 있습니다.

```swift
// Send serverCallId to your recording server to start the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

서버에서 녹음/녹화가 시작되면 `didChangeRecordingState` 이벤트가 트리거되고 `recordingFeature.isRecordingActive`의 값은 `true`가 됩니다.

통화 녹음/녹화를 시작하는 것과 마찬가지로 통화 녹음/녹화를 중지하려면 `ServerCallId`를 가져와 녹음/녹화 서버로 보내 통화 녹음/녹화를 중지해야 합니다.

```swift
// Send serverCallId to your recording server to stop the call recording.
let serverCallId = call.info.getServerCallId(){ (serverId, error) in }
```

서버에서 녹음/녹화가 중지되면 `didChangeRecordingState` 이벤트가 트리거되고 `recordingFeature.isRecordingActive` 값은 `false`가 됩니다.