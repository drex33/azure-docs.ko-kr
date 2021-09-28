---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705628"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>통화 녹음
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```js
const callRecordingApi = call.api(Features.Recording);
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingApi`의 `isRecordingActive` 속성을 검사합니다. `Boolean`를 반환합니다.

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

레코딩 변화를 구독할 수도 있습니다.

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```