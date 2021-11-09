---
title: Teams 회의 참가
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services sdk를 사용 하 여 Teams 회의에 참여 합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b2469def75cc54efc9efced2c25587b466f4cf0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063630"
---
# <a name="join-a-teams-meeting"></a>팀 참여 모임

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Azure Communication Services sdk를 사용 하면 사용자가 일반 Microsoft Teams 모임에 참여할 수 있습니다. 방법은 다음과 같습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작을 완료 하 여 [응용 프로그램에 음성 통화를 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md) 합니다.

> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

Teams 회의에 참가하려면 `join` 메서드를 사용하고 회의 링크 또는 회의 좌표를 전달합니다.

모임 링크를 사용하여 참가:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

모임 좌표를 사용하여 조인:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

모임 id를 사용 하 여 조인 (현재 제한 된 미리 보기 상태):

```js
const locator = { meetingId: '<MEETING_ID>'}
const call = callAgent.join(locator);
```

## <a name="next-steps"></a>다음 단계
- [호출을 관리 하는 방법 알아보기](./manage-calls.md)
- [비디오를 관리 하는 방법 알아보기](./manage-video.md)
- [호출을 전송 하는 방법 알아보기](./transfer-calls.md)
