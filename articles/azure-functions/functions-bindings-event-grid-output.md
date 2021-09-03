---
title: Azure Functions에 대한 Azure Event Grid 출력 바인딩
description: Azure Functions에서 Event Grid 이벤트를 보내는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: f29302efdf6d2a0c0b12ec15d897efda16cdc368
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539223"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions에 대한 Azure Event Grid 출력 바인딩

Event Grid 출력 바인딩을 사용하여 사용자 지정 항목에 이벤트를 씁니다. 유효한 [사용자 지정 항목에 대한 액세스 키](../event-grid/security-authenticate-publishing-clients.md)가 있어야 합니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요](./functions-bindings-event-grid.md)를 참조하세요.

> [!NOTE]
> Event Grid 출력 바인딩은 공유 액세스 서명(SAS 토큰)을 지원하지 않습니다. 항목의 액세스 키를 사용해야 합니다.

> [!IMPORTANT]
> Event Grid 출력 바인딩은 Functions 2.x 이상에서만 사용할 수 있습니다.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

### <a name="c-2x-and-higher"></a>C#(2.x 이상)

다음 예제에서는 메서드 반환 값을 출력으로 사용하여 Event Grid 사용자 지정 항목에 메시지를 쓰는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

다음 예제에서는 `IAsyncCollector` 인터페이스를 사용하여 일괄 처리 메시지를 보내는 방법을 보여줍니다.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

### <a name="version-3x-preview"></a>버전 3.x(미리 보기)

다음 예제에서는 `CloudEvent`에 바인딩되는 Functions 3.x [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using System.Threading.Tasks;
using Azure.Messaging;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Extensions.Http;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class CloudEventBindingFunction
    {
        [FunctionName("CloudEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<CloudEvent> eventCollector)
        {
            CloudEvent e = new CloudEvent("IncomingRequest", "IncomingRequest", await req.ReadAsStringAsync());
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

다음 예제에서는 `EventGridEvent`에 바인딩되는 Functions 3.x [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using System.Threading.Tasks;
using Azure.Messaging.EventGrid;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class EventGridEventBindingFunction
    {
        [FunctionName("EventGridEventBindingFunction")]
        public static async Task<IActionResult> RunAsync(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [EventGrid(TopicEndpointUri = "EventGridEndpoint", TopicKeySetting = "EventGridKey")] IAsyncCollector<EventGridEvent> eventCollector)
        {
            EventGridEvent e = new EventGridEvent(await req.ReadAsStringAsync(), "IncomingRequest", "IncomingRequest", "1.0.0");
            await eventCollector.AddAsync(e);
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예제에서는 *function.json* 파일에 있는 Event Grid 출력 바인딩 데이터를 보여줍니다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

단일 이벤트를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

여러 이벤트를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

다음 예는 Event Grid 사용자 지정 주제에 메시지를 작성하는 Java 함수를 보여 줍니다. 이 함수는 바인딩의 `setValue` 메서드를 사용하여 문자열을 출력합니다.

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);
        final String eventGridOutputDocument = "{\"id\": \"1807\", \"eventType\": \"recordInserted\", \"subject\": \"myapp/cars/java\", \"eventTime\":\"2017-08-10T21:03:07+00:00\", \"data\": {\"make\": \"Ducati\",\"model\": \"Monster\"}, \"dataVersion\": \"1.0\"}";
        outputEvent.setValue(eventGridOutputDocument);
    }
}
```

POJO 클래스를 사용하여 EventGrid 메시지를 보낼 수도 있습니다.

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<EventGridEvent> outputEvent,
            final ExecutionContext context) {
        context.getLogger().info("Java EventGrid trigger processed a request." + content);

        final EventGridEvent eventGridOutputDocument = new EventGridEvent();
        eventGridOutputDocument.setId("1807");
        eventGridOutputDocument.setEventType("recordInserted");
        eventGridOutputDocument.setEventTime("2017-08-10T21:03:07+00:00");
        eventGridOutputDocument.setDataVersion("1.0");
        eventGridOutputDocument.setSubject("myapp/cars/java");
        eventGridOutputDocument.setData("{\"make\": \"Ducati\",\"model\":\"monster\"");

        outputEvent.setValue(eventGridOutputDocument);
    }
}

class EventGridEvent {
    private String id;
    private String eventType;
    private String subject;
    private String eventTime;
    private String dataVersion;
    private String data;

    public String getId() {
        return id;
    }

    public String getData() {
        return data;
    }

    public void setData(String data) {
        this.data = data;
    }

    public String getDataVersion() {
        return dataVersion;
    }

    public void setDataVersion(String dataVersion) {
        this.dataVersion = dataVersion;
    }

    public String getEventTime() {
        return eventTime;
    }

    public void setEventTime(String eventTime) {
        this.eventTime = eventTime;
    }

    public String getSubject() {
        return subject;
    }

    public void setSubject(String subject) {
        this.subject = subject;
    }

    public String getEventType() {
        return eventType;
    }

    public void setEventType(String eventType) {
        this.eventType = eventType;
    }

    public void setId(String id) {
        this.id = id;
    }  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 *function.json* 파일에 있는 Event Grid 출력 바인딩 데이터를 보여줍니다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

단일 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

여러 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 예제에서는 Event Grid 이벤트 메시지를 출력하도록 함수를 구성하는 방법을 보여 줍니다. `type`이 `eventGrid`로 설정된 섹션은 Event Grid 출력 바인딩을 설정하는 데 필요한 값을 구성합니다.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

함수에서 `Push-OutputBinding`을 사용하여 Event Grid 출력 바인딩을 통해 사용자 지정 토픽에 이벤트를 보냅니다.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여줍니다. 그런 다음, `topicEndpointUri`에 지정된 대로 사용자 지정 토픽으로 이벤트를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

`EventGridOutputEvent`를 설정하여 사용자 지정 토픽으로 이벤트를 보내는 Python 샘플은 다음과 같습니다.

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)의 경우 [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 특성을 사용합니다.

특성의 생성자는 사용자 지정 항목 이름이 포함된 앱 설정 이름과 항목 키가 포함된 앱 설정 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventGrid` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제는 [예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java 클래스의 경우 [EventGridAttribute](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/EventGridOutput.java) 특성을 사용합니다.

특성의 생성자는 사용자 지정 항목 이름이 포함된 앱 설정 이름과 항목 키가 포함된 앱 설정 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventGridOutput` 특성 예제는 다음과 같습니다.

```java
public class Function {
    @FunctionName("EventGridTriggerTest")
    public void run(@EventGridTrigger(name = "event") String content,
            @EventGridOutput(name = "outputEvent", topicEndpointUri = "MyEventGridTopicUriSetting", topicKeySetting = "MyEventGridTopicKeySetting") OutputBinding<String> outputEvent, final ExecutionContext context) {
            ...
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `EventGrid` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "eventGrid"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**topicEndpointUri** |**TopicEndpointUri** | `MyTopicEndpointUri`와 같이 사용자 지정 항목에 대한 URI를 포함하는 앱 설정의 이름입니다. |
|**topicKeySetting** |**TopicKeySetting** | 사용자 지정 항목에 대한 액세스 키가 포함된 앱 설정의 이름입니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 구성 속성의 값을 사용자 지정 항목의 URI가 포함된 앱 설정 이름으로 설정했는지 확인합니다. 이 속성에서 직접 사용자 지정 항목의 URI를 지정하지 마세요.

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

`out EventGridEvent paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

### <a name="additional-types"></a>추가 형식 
Event Grid 확장의 버전 3.0.0 이상을 사용하는 앱은 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 네임스페이스의 `EventGridEvent` 유형을 사용합니다. 또한 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 네임스페이스의 `CloudEvent` 유형에 바인딩할 수 있습니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

`out EventGridEvent paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

### <a name="additional-types"></a>추가 형식 
Event Grid 확장의 버전 3.0.0 이상을 사용하는 앱은 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 네임스페이스의 `EventGridEvent` 유형을 사용합니다. 또한 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 네임스페이스의 `CloudEvent` 유형에 바인딩할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

`out EventGridOutput paramName`과 같은 메서드 매개 변수를 호출하여 개별 메시지를 보내고 `ICollector<EventGridOutput>`으로 여러 메시지를 작성합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`을 사용하여 출력 이벤트에 액세스합니다. 여기서 `<name>`은 *function.json* 의 `name` 속성에 지정된 값입니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

`Push-OutputBinding` commandlet을 사용하여 출력 이벤트에 액세스하고 Event Grid 출력 바인딩에 이벤트를 보냅니다.

# <a name="python"></a>[Python](#tab/python)

함수에서 Event Grid 메시지를 출력하는 두 가지 옵션이 있습니다.
- **반환 값**: *function.json* 의 `name` 속성을 `$return`으로 설정합니다. 이 구성을 사용하면 함수의 반환 값이 Event Grid 메시지로 유지됩니다.
- **명령형**: [출력](/python/api/azure-functions/azure.functions.out) 형식으로 선언된 매개 변수의 [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) 메서드에 값을 전달합니다. `set`에 전달되는 값은 Event Grid 메시지로 유지됩니다.

---

## <a name="next-steps"></a>다음 단계

* [Event Grid 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)
