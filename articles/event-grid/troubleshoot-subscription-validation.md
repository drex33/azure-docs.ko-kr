---
title: Azure Event Grid - 구독 유효성 검사 문제 해결
description: 이 문서에서는 구독 유효성 검사 문제를 해결하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 8db9d36b31c96187f985f98c4c2a1c79f4e58218
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230497"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid 구독 유효성 검사 문제 해결
이벤트 구독을 만드는 동안 `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`와 같은 오류 메시지가 표시되는 경우 유효성 검사 핸드셰이크에 오류가 있음을 나타냅니다. 이 오류를 해결하려면 다음과 같은 측면을 확인합니다.

- Postman, curl 또는 유사한 도구를 사용하여 [샘플 SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) 요청 본문을 통해 웹후크 URL에 HTTP POST를 수행합니다.
- 웹후크가 동기 유효성 검사 핸드셰이크 메커니즘을 구현하는 경우 ValidationCode가 응답의 일부로 반환되는지 확인합니다.
- 웹후크가 비동기 유효성 검사 핸드셰이크 메커니즘을 구현하는 경우 HTTP POST가 200 OK를 반환하는지 확인합니다.
- 웹후크가 응답에서 `403 (Forbidden)`을 반환하는 경우 웹후크가 Azure Application Gateway 또는 Web Application Firewall 뒤에 있는지 확인합니다. 그런 경우, 이러한 방화벽 규칙을 사용하지 않도록 설정하고 HTTP POST를 다시 수행해야 합니다.
    - 920300(수락 헤더가 누락된 요청)
    - 942430(제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12))
    - 920230(여러 URL 인코딩 감지)
    - 942130(SQL 삽입 공격: SQL Tautology 감지)
    - 931130(가능한 RFI(원격 파일 포함) 공격 = 오프-도메인 참조/링크)

> [!IMPORTANT]
> 웹후크의 엔드포인트 유효성 검사에 대한 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요.

다음 섹션에서는 Postman 및 Curl을 사용하여 이벤트 구독의 유효성을 검사하는 방법을 표시합니다.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Postman을 사용하여 Event Grid 이벤트 구독 유효성 검사
다음은 Event Grid 이벤트의 웹후크 구독 유효성을 검사하기 위해 Postman을 사용하는 예입니다. 

![Postman을 사용하여 이벤트 그리드 이벤트 구독 유효성 검사](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

다음은 샘플 **SubscriptionValidationEvent** JSON입니다.

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

성공적인 응답 예제는 다음과 같습니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

웹후크의 Event Grid 이벤트 유효성 검사에 대한 자세한 내용은 [Event Grid 이벤트를 사용하여 엔드포인트 유효성 검사](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)를 참조하세요.


## <a name="validate-event-grid-event-subscription-using-curl"></a>Curl을 사용하여 Event Grid 이벤트 구독 유효성 검사 
다음은 Event Grid 이벤트의 웹후크 구독 유효성을 검사하기 위한 샘플 Curl 명령입니다. 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Postman을 사용하여 클라우드 이벤트 구독 유효성 검사
다음은 클라우드 이벤트의 웹후크 구독 유효성을 검사하기 위해 Postman을 사용하는 예입니다. 

![Postman을 사용하여 클라우드 이벤트 구독 유효성 검사](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

클라우드 이벤트를 통한 유효성 검사에 **HTTP OPTIONS** 메서드를 사용합니다. 웹후크의 클라우드 이벤트 유효성 검사에 대한 자세한 내용은 [클라우드 이벤트를 사용하여 엔드포인트 유효성 검사](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)를 참조하세요.

## <a name="troubleshoot-event-subscription-validation"></a>이벤트 구독 유효성 문제 해결

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
