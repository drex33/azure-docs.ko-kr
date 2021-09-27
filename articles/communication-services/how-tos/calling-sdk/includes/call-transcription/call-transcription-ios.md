---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: fa436767a342c9854cf71004bb5a0754e5e9e775
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838794"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!WARNING]
> ACS 통화 iOS SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isTranscriptionActive`가 `Call` 개체의 일부이고 `didChangeTranscriptionState`는 `CallDelegate` 대리자의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 iOS SDK의 ‘베타’ 릴리스를 사용하세요.

통화 대화 내용 기록은 핵심 `Call` API의 확장 기능입니다. 먼저 대화 내용 기록 기능 API 개체를 가져와야 합니다.

```swift
let callTranscriptionFeature = call.api(TranscriptionFeature.self)
```

그런 다음, 통화 대화 내용이 기록되고 있는지 확인하기 위해 `callTranscriptionFeature`의 `isTranscriptionActive` 속성을 검사합니다. `Bool`를 반환합니다.

```swift
let isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive;
```

이벤트 `didChangeTranscriptionState`를 사용하여 클래스에 `TranscriptionFeatureDelegate` 대리자를 구현하여 전사 변경 내용을 구독할 수도 있습니다.

```swift
callTranscriptionFeature.delegate = self

// didChangeTranscriptionState is a member of TranscriptionFeatureDelegate
public func transcriptionFeature(_ transcriptionFeature: TranscriptionFeature, didChangeTranscriptionState args: PropertyChangedEventArgs) {
    let isTranscriptionActive = transcriptionFeature.isTranscriptionActive
}
```