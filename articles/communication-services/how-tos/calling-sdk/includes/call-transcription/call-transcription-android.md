---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: b880f8a8f84247c14a0f1e81577d9e252b2b78c8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585191"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

> [!WARNING]
> ACS 호출 Android SDK의 버전 1.1.0 및 베타 릴리스 버전 1.1.0-beta.1까지는 `isTranscriptionActive` 및 `addOnIsTranscriptionActiveChangedListener`가 `Call` 개체의 일부입니다. 새 베타 릴리스의 경우 해당 API는 아래 설명과 같이 `Call`의 확장 기능으로 이동되었습니다.

> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 Android SDK의 '베타' 릴리스를 사용하세요.

통화 대화 내용 기록은 핵심 `Call` API의 확장 기능입니다. 먼저 대화 내용 기록 기능 API 개체를 가져와야 합니다.

```java
TranscriptionCallFeature callTranscriptionFeature = call.api(Features.TRANSCRIPTION);
```

그런 다음, 통화 대화 내용이 기록되고 있는지 확인하려면 `callTranscriptionFeature`의 `isTranscriptionActive` 속성을 검사합니다. `boolean`를 반환합니다.

```java
boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
```

또한 대화 내용 기록의 변경 사항을 구독할 수 있습니다.

```java
private void handleCallOnIsTranscriptionChanged(PropertyChangedEvent args) {
    boolean isTranscriptionActive = callTranscriptionFeature.isTranscriptionActive();
}

callTranscriptionFeature.addOnIsTranscriptionActiveChangedListener(handleCallOnIsTranscriptionChanged);
```
