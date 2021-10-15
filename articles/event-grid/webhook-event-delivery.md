---
title: WebHook 이벤트 전달
description: 이 문서에서는 웹후크를 사용할 때의 웹후크 이벤트 전달 및 엔드포인트 유효성 검사에 대해 설명합니다.
ms.topic: conceptual
ms.date: 10/13/2021
ms.openlocfilehash: 35b088f18b4261760d7908a8e779dbc1bda56501
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062997"
---
# <a name="webhook-event-delivery"></a>웹후크 이벤트 전달
웹후크는 Azure Event Grid에서 이벤트를 수신하는 여러 가지 방법 중 하나입니다. 새 이벤트가 준비되면 Event Grid 서비스는 요청 본문에 이벤트가 포함되어 구성된 엔드포인트로 HTTP 요청을 게시합니다.

웹후크를 지원하는 여러 다른 서비스와 마찬가지로, Event Grid를 사용하려면 해당 엔드포인트로 이벤트 제공을 시작하기 전에 웹후크 엔드포인트에 대한 소유권을 증명해야 합니다. 이 요구 사항으로 인해 악의적인 사용자가 엔드포인트에서 과도한 이벤트를 발생하는 일이 방지됩니다. 아래 나열된 세 가지 Azure 서비스를 사용하는 경우 Azure 인프라는 자동으로 이 유효성 검사를 처리합니다.

- [Event Grid 커넥터](/connectors/azureeventgrid/)를 포함하는 Azure Logic Apps
- [웹후크](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)를 통한 Azure Automation
- [Event Grid Trigger](../azure-functions/functions-bindings-event-grid.md)를 포함하는 Azure Functions

## <a name="endpoint-validation-with-event-grid-events"></a>Event Grid 이벤트를 사용한 엔드포인트 유효성 검사

HTTP 트리거 기반 Azure 함수와 같은 엔드포인트의 다른 형식을 사용하는 경우, 엔드포인트 코드가 Event Grid를 통해 핸드셰이크 유효성 검사에 참여해야 합니다. Event Grid는 두 가지 방법의 구독 유효성 검사를 지원합니다.

- **동기 핸드셰이크**: 이벤트 구독 생성 시 Event Grid는 엔드포인트에 구독 유효성 검사 이벤트를 전송합니다. 이 이벤트의 스키마는 다른 Event Grid 이벤트와 비슷합니다. 이 이벤트의 데이터 부분에는 `validationCode` 속성이 포함됩니다. 애플리케이션은 예상된 이벤트 구독에 대해 유효성 검사 요청이 수행된 것인지 확인하고 응답에 유효성 검사 코드를 동기식으로 반환합니다. 이 핸드셰이크 메커니즘은 모든 Event Grid 버전에서 지원됩니다.

- **비동기 핸드셰이크**: 유효성 검사 코드를 응답에 동기식으로 반환할 수 없는 경우가 있습니다. 예를 들어, 타사 서비스를 사용하는 경우(예: [`Zapier`](https://zapier.com) 또는 [IFTTT](https://ifttt.com/)) 유효성 검사 코드를 통해 프로그래밍 방식으로 응답할 수 없습니다.

   버전 2018-05-01-미리 보기부터 Event Grid는 수동 유효성 검사 핸드셰이크를 지원합니다. API 버전 2018-05-01-미리 보기 이상을 사용하는 SDK 또는 도구에서 이벤트 구독을 만드는 경우 Event Grid는 구독 유효성 검사 이벤트의 데이터 부분에 `validationUrl` 속성을 전송합니다. 핸드셰이크를 완료하려면 이벤트 데이터에서 해당 URL을 찾은 후 GET 요청을 전송합니다. REST 클라이언트 또는 웹 브라우저를 사용할 수 있습니다.

   제공된 URL은 **5분** 동안 유효합니다. 이 시간 동안 이벤트 구독의 프로비전 상태가 `AwaitingManualAction`입니다. 5분 안에 수동 유효성 검사를 완료하지 않은 경우 프로비저닝 상태가 `Failed`로 설정됩니다. 수동 유효성 검사를 시작하기 전에 이벤트 구독을 다시 작성해야 합니다.

   이 인증 메커니즘에서는 수동 유효성 감시 모드로 들어가기 전에 유효성 검사 이벤트의 POST가 수락되었는지 알 수 있도록 웹후크 엔드포인트가 HTTP 상태 코드 200을 반환하도록 요구합니다. 즉, 엔드포인트가 200을 반환하지만 동기식으로 유효성 검사 응답을 반환하지 않는 경우 모드가 수동 유효성 검사 모드로 전환됩니다. 5분 안에 유효성 검사 URL에 대한 GET이 수신되는 경우, 유효성 검사 핸드셰이크가 성공했다고 간주됩니다.

> [!NOTE]
> 유효성 검사에 자체 서명된 인증서를 사용하는 것은 지원되지 않습니다. 대신 상용 CA(인증 기관)의 서명된 인증서를 사용하세요.

### <a name="validation-details"></a>유효성 검사 세부 정보

- 이벤트 구독 생성/업데이트 시 Event Grid는 대상 엔드포인트에 구독 유효성 검사 이벤트를 게시합니다.
- 이벤트에는 헤더 값 이 `aeg-event-type: SubscriptionValidation` 포함됩니다.
- 이벤트 본문에는 다른 Event Grid 이벤트와 동일한 스키마가 있습니다.
- `eventType`이벤트의 속성은 `Microsoft.EventGrid.SubscriptionValidationEvent` 입니다.
- `data`의 속성을 이벤트 임의로 생성 된 문자열을 가진 속성을 포함 `validationCode` 합니다. 예: `validationCode: acb13…`.
- 이벤트 데이터는 구독에 대해 수동으로 유효성 검사를 수행하기 위해 URL에 `validationUrl` 속성도 포함되어 있습니다.
- 배열에는 유효성 검사 이벤트만 포함됩니다. 다른 이벤트는 유효성 검사 코드를 에코 백한 후 별도의 요청으로 전송됩니다.
- EventGrid 데이터 평면 SDK에는 구독 유효성 검사 이벤트 데이터 및 구독 유효성 검사 응답에 해당하는 클래스가 있습니다.

SubscriptionValidationEvent 예가 다음 예제에 나와 있습니다.

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/myeventsub/validate?id=0000000000-0000-0000-0000-00000000000000&t=2021-09-01T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2021-00-01T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

엔드포인트 소유권을 증명하려면 다음 예제와 같이 속성의 유효성 검사 코드를 다시 `validationResponse` 에코합니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

**HTTP 200 OK** 응답 상태 코드를 반환해야 합니다. **HTTP 202 수락됨은** 유효한 Event Grid 구독 유효성 검사 응답으로 인식되지 않습니다. HTTP 요청은 30초 이내에 완료되어야 합니다. 작업이 30초 이내에 완료되지 않으면 작업이 취소되며, 5초 뒤에 다시 시도될 수 있습니다. 모든 시도가 실패하면 유효성 검사 핸드셰이크 오류로 간주됩니다.

또는 유효성 검사 URL에 GET 요청을 수동으로 전송하여 구독이 유효한지 수동으로 검사할 수 있습니다. 이벤트 구독은 유효성을 검사할 때까지 보류 상태로 유지됩니다. 유효성 검사 URL은 포트 553을 사용합니다. 방화벽 규칙이 포트 553을 차단하는 경우, 성공적인 수동 핸드셰이크를 위해서는 규칙을 업데이트해야 합니다.

구독 유효성 검사 핸드셰이크 처리 예제를 보려면 [C# 샘플](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)을 참조하세요.

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0을 사용한 엔드포인트 유효성 검사
CloudEvents v1.0은 **HTTP OPTIONS** 메서드를 사용하여 자체 남용 방지 [의미 체계를 구현합니다.](webhook-event-delivery.md) 해당 서비스에 대한 자세한 내용은 [여기](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)에서 확인할 수 있습니다. 출력에 CloudEvents 스키마를 사용하는 경우, Event Grid는 Event Grid 유효성 검사 이벤트 메커니즘 대신 CloudEvents v1.0 악용 방지를 사용합니다.

## <a name="event-schema-compatibility"></a>이벤트 스키마 호환성
토픽이 만들어지면 들어오는 이벤트 스키마가 정의됩니다. 또한 구독을 만들 때 나가는 이벤트 스키마가 정의됩니다. 다음 표에서는 구독을 만들 때 허용되는 호환성을 보여줍니다. 

| 들어오는 이벤트 스키마 | 나가는 이벤트 스키마 | 지원됨 |
| ---- | ---- | ---- |
| Event Grid 스키마 | Event Grid 스키마 | 예 |
| | 클라우드 이벤트 v1.0 스키마 | 예 |
| | 사용자 지정 입력 스키마 | 아니요 |
| 클라우드 이벤트 v1.0 스키마 | Event Grid 스키마 | 아니요 |
| | 클라우드 이벤트 v1.0 스키마 | 예 |
| | 사용자 지정 입력 스키마 | 아니요 |
| 사용자 지정 입력 스키마 | Event Grid 스키마 | 예 |
| | 클라우드 이벤트 v1.0 스키마 | 예 |
| | 사용자 지정 입력 스키마 | 예 |

## <a name="next-steps"></a>다음 단계
다음 문서를 참고하여 이벤트 구독 유효성 검사의 문제를 해결하는 방법을 알아봅니다. 

[이벤트 구독 유효성 검사 문제 해결](troubleshoot-subscription-validation.md)
