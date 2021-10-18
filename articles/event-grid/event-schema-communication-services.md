---
title: Event Grid 소스로 서의 Azure 통신 서비스-개요
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure Communication Services를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: mikben
ms.openlocfilehash: 590e0a53ca701a6b293dd52b2f7c30c5e873df30
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129494"
---
# <a name="event-handling-in-azure-communication-services"></a>Azure Communication Services에서 이벤트 처리

Azure Communication Services는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 안정적이고 확장 가능하며 안전한 방식으로 실시간 이벤트 알림을 제공합니다. 이 문서의 목적은 Communication Services 이벤트를 수신하도록 애플리케이션을 구성하는 데 도움을 주는 것입니다. 예를 들어, Communication Services 리소스와 연결된 전화 번호로 SMS 메시지를 받을 때마다 데이터베이스를 업데이트하고, 작업 항목을 만들고, 푸시 알림을 제공할 수 있습니다.

Azure Event Grid는 게시-구독 모델을 사용하는 완전 관리형 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../azure-functions/functions-overview.md)와 같은 Azure 서비스에 대한 지원이 기본적으로 제공됩니다. 웹후크를 사용하여 비 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](overview.md)를 참조하세요.

:::image type="content" source="./media/overview/functional-model.png" alt-text="Azure Event Grid의 이벤트 모델을 보여 주는 다이어그램.":::

> [!NOTE]
> 데이터 보존과 이벤트 처리의 관계에 대한 자세한 내용은 [데이터 보존 개념 설명서](../communication-services/concepts/privacy.md)를 참조하세요.

## <a name="events-types"></a>이벤트 유형

Event Grid는 [이벤트 구독](concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다.

Azure Communication Services에서 내보내는 이벤트 유형은 다음과 같습니다.

* [채팅 이벤트](./communication-services-chat-events.md)
* [전화 통신 및 SMS 이벤트](./communication-services-telephony-sms-events.md)
* [음성 및 비디오 호출 이벤트](./communication-services-voice-video-events.md)
* [현재 상태 이벤트](./communication-services-presence-events.md)

Azure Portal 또는 Azure CLI를 사용하여 Communication Services 리소스에서 내보낸 이벤트를 구독할 수 있습니다. 

## <a name="event-subjects"></a>이벤트 제목

모든 Communication Services 이벤트의 `subject` 필드는 이벤트의 대상으로 지정된 사용자, 전화 번호 또는 엔터티를 식별합니다. 일반적인 접두사는 단순한 [Event Grid 필터링](event-filtering.md)을 허용하는 데 사용됩니다.

| 제목 접두사                              | Communication Service 엔터티 |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN 전화 번호            |
| `user/`                                     | Communication Services 사용자  |
| `thread/`                                   | 채팅 스레드.                 |

다음 예제에서는 Communication Services 리소스에서 소유한 전체 555개 지역 전화 번호로 전송되는 모든 SMS 메시지 및 배달 보고서에 대한 필터를 보여 줍니다.

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](./overview.md)을 참조하세요.
* Azure Event Grid 개념에 대한 소개는 [Event Grid란?](./concepts.md)을 참조하세요.
* Azure Event Grid 시스템 토픽에 대한 소개는 [Azure Event Grid의 시스템 토픽](./system-topics.md)을 참조하세요.
