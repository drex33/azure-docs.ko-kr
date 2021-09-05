---
title: Azure Event Grid로부터 HTTP 엔드포인트로 이벤트 수신
description: HTTP 엔드포인트의 유효성을 검사한 다음, Azure Event Grid로부터 이벤트를 수신하고 역직렬화하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f655982203c81ef6ff32a01566e37c346cf78138
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730282"
---
# <a name="receive-events-to-an-http-endpoint"></a>HTTP 엔드포인트에서 이벤트 수신

이 아티클에서는 이벤트 구독으로부터 이벤트를 수신하기 위해 [HTTP 엔드포인트의 유효성을 검사](webhook-event-delivery.md)한 다음, 이벤트를 수신하고 역직렬화하는 방법을 설명합니다. 이 문서에서는 데모용으로 Azure Function을 사용하나 애플리케이션이 어디서 호스팅되느냐와 관계없이 동일한 개념이 적용됩니다.

> [!NOTE]
> Event Grid로 Azure Function을 트리거할 때는 [Event Grid 트리거](../azure-functions/functions-bindings-event-grid.md)를 사용하는 것이 좋습니다. Event Grid와 Azure Functions를 보다 쉽고 빠르게 통합할 수 있습니다. 그러나 Azure Functions Event Grid 트리거는 호스트된 코드가 Event Grid에 반환된 HTTP 상태 코드를 제어해야 하는 시나리오를 지원하지 않습니다. 이러한 제한으로 인해 Azure Function에서 실행되는 코드는 5XX 오류를 반환하여 Event Grid의 이벤트 전달 재시도를 시작할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

HTTP 트리거 함수가 있는 함수 앱이 필요합니다.

## <a name="add-dependencies"></a>종속성 추가

.NET에서 개발할 때는 함수에 `Azure.Messaging.EventGrid` [NuGet 패키지](https://www.nuget.org/packages/Azure.Messaging.EventGrid)의 [종속성을 추가](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies)합니다.

다른 언어에 대한 SDK는 [SDK 게시](./sdk-overview.md#data-plane-sdks) 참조에서 제공합니다. 이 패키지에는 `EventGridEvent`, `StorageBlobCreatedEventData`, `EventHubCaptureFileCreatedEventData` 같은 원시 이벤트 형식의 모델이 있습니다.

## <a name="endpoint-validation"></a>엔드포인트 유효성 검사

먼저 수행할 작업은 `Microsoft.EventGrid.SubscriptionValidationEvent` 이벤트를 처리하는 것입니다. 누군가가 이벤트를 구독할 때마다 Event Grid는 유효성 검사 이벤트를 데이터 페이로드에 `validationCode`가 있는 엔드포인트에 보냅니다. 엔드포인트는 응답 본문에서 [엔드포인트가 유효하며 자신의 소유임을 증명하기 위해](webhook-event-delivery.md) 이를 다시 에코하는 데 필요합니다. WebHook 트리거 함수가 아닌 [Event Grid 트리거](../azure-functions/functions-bindings-event-grid.md)를 사용할 경우 엔드포인트 유효성 검사가 자동으로 처리됩니다. 타사 API 서비스를 사용하는 경우(예: [Zapier](https://zapier.com/home) 또는 [IFTTT](https://ifttt.com/)) 프로그래밍 방식으로 유효성 검사 코드를 에코하지 못할 수 있습니다. 이러한 서비스의 경우 구독 유효성 검사 이벤트에 전송된 유효성 검사 URL을 사용하여 수동으로 구독의 유효성을 검사할 수 있습니다. `validationUrl` 속성에 해당 URL을 복사하고 REST 클라이언트 또는 웹 브라우저를 통해 GET 요청을 보냅니다.

C#에서 `ParseMany()` 메서드는 하나 이상의 이벤트를 포함하는 `BinaryData` 인스턴스를 `EventGridEvent` 배열로 역직렬화하는 데 사용됩니다. 단일 이벤트만 역직렬화하는 것을 미리 알고 있는 경우 `Parse` 메서드를 대신 사용할 수 있습니다.

프로그래밍 방식으로 유효성 검사 코드를 에코하려면 다음 코드를 사용합니다.

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>유효성 검사 응답 테스트

샘플 이벤트를 함수에 대한 테스트 필드에 붙여넣어 유효성 검사 응답 함수를 테스트합니다.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

실행을 클릭하면 출력 본문에 200 OK 및 `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}`이 있어야 합니다.

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="유효성 검사 요청":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="유효성 검사 출력":::

## <a name="handle-blob-storage-events"></a>Blob Storage 이벤트 처리

이제 함수를 확장하여 `Microsoft.Storage.BlobCreated` 시스템 이벤트를 처리해 보겠습니다.

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>만든 Blob의 이벤트 처리 테스트

[Blob Storage 이벤트](./event-schema-blob-storage.md#example-event)를 테스트 필드에 놓고 실행하여 함수의 새 기능을 테스트할 수 있습니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

함수 로그에 Blob URL 출력이 있어야 합니다.

![출력 로그](./media/receive-events/blob-event-response.png)

Blob Storage 계정이나 GPv2(General Purpose V2) 스토리지 계정을 만들고 [이벤트 구독을 추가한 다음](../storage/blobs/storage-blob-event-quickstart.md) 엔드포인트를 함수 URL로 설정해서 테스트할 수도 있습니다.

![함수 URL](./media/receive-events/function-url.png)

## <a name="handle-custom-events&quot;></a>사용자 지정 이벤트 처리

마지막으로, 사용자 지정 이벤트를 처리할 수 있도록 함수를 더 확장할 수 있습니다. 

이벤트 `Contoso.Items.ItemReceived`에 대한 검사를 추가합니다. 최종 코드는 다음과 같이 표시됩니다.

```c#
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System;
using Azure.Messaging.EventGrid;
using Azure.Messaging.EventGrid.SystemEvents;

namespace Function1
{
    public static class Function1
    {
        [FunctionName(&quot;Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            BinaryData events = await BinaryData.FromStreamAsync(req.Body);
            log.LogInformation($"Received events: {events}");

            EventGridEvent[] eventGridEvents = EventGridEvent.ParseMany(events);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                // Handle system events
                if (eventGridEvent.TryGetSystemEventData(out object eventData))
                {
                    // Handle the subscription validation event
                    if (eventData is SubscriptionValidationEventData subscriptionValidationEventData)
                    {
                        log.LogInformation($"Got SubscriptionValidation event data, validation code: {subscriptionValidationEventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                        // Do any additional validation (as required) and then return back the below response

                        var responseData = new SubscriptionValidationResponse()
                        {
                            ValidationResponse = subscriptionValidationEventData.ValidationCode
                        };
                        return new OkObjectResult(responseData);
                    }
                    // Handle the storage blob created event
                    else if (eventData is StorageBlobCreatedEventData storageBlobCreatedEventData)
                    {
                        log.LogInformation($"Got BlobCreated event data, blob URI {storageBlobCreatedEventData.Url}");
                    }
                }
                // Handle the custom contoso event
                else if (eventGridEvent.EventType == "Contoso.Items.ItemReceived")
                {
                    var contosoEventData = eventGridEvent.Data.ToObjectFromJson<ContosoItemReceivedEventData>();
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {contosoEventData.ItemSku}");
                }
            }
            return new OkObjectResult(response);
        }
    }
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>사용자 지정 이벤트 처리 테스트

마지막으로 함수가 이제 사용자 지정 이벤트 형식을 처리할 수 있는지 테스트합니다.

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

[포털의 CURL로 사용자 지정 이벤트를 보내거나](./custom-event-quickstart-portal.md), [Postman](https://www.getpostman.com/)처럼 엔드포인트에 POST할 수 있는 서비스나 애플리케이션을 사용하여 [사용자 지정 토픽에 게시하여](./post-to-custom-topic.md) 이 기능을 라이브로 테스트할 수도 있습니다. 함수 URL로 설정된 엔드포인트를 통해 사용자 지정 토픽과 이벤트 구독을 만듭니다.

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Event Grid 관리 및 SDK 게시](./sdk-overview.md)를 살펴봅니다.
* [사용자 지정 토픽에 게시](./post-to-custom-topic.md) 방법 알아보기
* [Blob Storage에 업로드된 이미지 크기 조정](resize-images-on-storage-blob-upload-event.md) 등, 심층적인 Event Grid 및 Functions 시도해 보기
