---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 049e1e5b32953f5f72108602538a3096c077f2a2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019238"
---
Event Hubs 출력 바인딩을 사용하여 이벤트 스트림에 이벤트를 씁니다. 이벤트를 쓰려면 이벤트 허브에 대한 보내기 사용 권한이 있어야 합니다.

출력 바인딩을 구현하려고 하기 전에 필요한 패키지 참조가 준비되어 있는지 확인하세요.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 메서드 반환 값을 출력으로 사용하여 이벤트 허브로 메시지를 쓰는 [C# 함수](../articles/azure-functions/functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

다음 예제에서는 `IAsyncCollector` 인터페이스를 사용하여 일괄 처리 메시지를 보내는 방법을 보여줍니다. 이 시나리오는 하나의 Event Hub에서 들어오는 메시지를 처리하고 그 결과를 다른 Event Hub로 보내는 경우에 일반적입니다.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](../articles/azure-functions/functions-reference-csharp.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 Functions 2.x 이상에 해당하고 두 번째 예제는 Functions 1.x에 해당합니다. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

여러 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](../articles/azure-functions/functions-reference-node.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 Functions 2.x 이상에 해당하고 두 번째 예제는 Functions 1.x에 해당합니다. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 보내는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

여러 메시지를 보내는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](../articles/azure-functions/functions-reference-python.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 보내는 Python 코드는 다음과 같습니다.

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 Event Hub에 현재 시간을 포함하는 메시지를 작성하는 Java 함수를 보여줍니다.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에 게시되는 매개 변수에 대한 `@EventHubOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO 또는 원시 Java 형식입니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)에서 [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 특성을 사용합니다.

특성의 생성자는 이벤트 허브의 이름 및 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventHub` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에 게시되는 매개 변수에 대한 [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 주석을 사용합니다. 매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 `T`는 POJO 또는 원시 Java 형식입니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `EventHub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "eventHub"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**path** |**EventHubName** | Functions 1.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**eventHubName** |**EventHubName** | Functions 2.x 이상. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**connection** |**연결** | Event Hubs에 연결 하는 방법을 지정 하는 설정 컬렉션 또는 앱 설정의 이름입니다. [연결](#connections)을 참조하세요.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-event-hubs-connections](./functions-event-hubs-connections.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>기본값

Event Hub의 출력 바인딩에 다음 매개 변수 유형을 사용할 수 있습니다.

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData의 기본 속성은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에서 제공됩니다.

`out string paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out string` 대신 `ICollector<string>` 또는 `IAsyncCollector<string>`를 사용할 수 있습니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Event Hub 확장을 사용하는 앱은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에 있는 `EventData` 유형 대신 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Body` 유형 지원을 중단합니다.

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>기본값

Event Hub의 출력 바인딩에 다음 매개 변수 유형을 사용할 수 있습니다.

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData의 기본 속성은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에서 제공됩니다.

`out string paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json* 의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out string` 대신 `ICollector<string>` 또는 `IAsyncCollector<string>`를 사용할 수 있습니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Event Hub 확장을 사용하는 앱은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에 있는 `EventData` 유형 대신 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Body` 유형 지원을 중단합니다.

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`을 사용하여 출력 이벤트에 액세스합니다. 여기서 `<name>`은 *function.json* 의 `name` 속성에 지정된 값입니다.

# <a name="python"></a>[Python](#tab/python)

함수에서 Event Hub 메시지를 출력하는 두 가지 옵션이 있습니다.

- **반환 값**: *function.json* 의 `name` 속성을 `$return`으로 설정합니다. 이 구성을 사용하면 함수의 반환 값이 Event Hub 메시지로 유지됩니다.

- **명령형**: [출력](/python/api/azure-functions/azure.functions.out) 형식으로 선언된 매개 변수의 [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) 메서드에 값을 전달합니다. `set`에 전달되는 값은 Event Hub 메시지로 유지됩니다.

# <a name="java"></a>[Java](#tab/java)

[EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 주석을 사용하여 함수에서 Event Hub 메시지를 출력하는 두 가지 옵션이 있습니다.

- **반환 값**: 함수 자체에 주석을 적용하면 함수의 반환 값이 Event Hub 메시지로 유지됩니다.

- **명령형**: 메시지 값을 명시적으로 설정하려면 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) 형식의 특정 매개 변수에 주석을 적용합니다. 여기서 `T`는 POJO 또는 네이티브 Java 형식입니다. 이 구성을 사용하여 `setValue` 메서드에 값을 전달하면 값이 Event Hub 메시지로 유지됩니다.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참고 |
|---|---|
| 이벤트 허브 | [운영 가이드](/rest/api/eventhub/publisher-policy-operations) |