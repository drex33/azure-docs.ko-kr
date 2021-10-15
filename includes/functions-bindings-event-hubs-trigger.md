---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: c8d3e988edd48d33c1bbf7e1680b9a777725727d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019334"
---
함수 트리거를 사용하여 이벤트 허브 이벤트 스트림으로 보내진 이벤트에 응답합니다. 트리거를 설정하려면 기본 이벤트 허브에 대한 읽기 액세스 권한이 있어야 합니다. 함수가 트리거되면 함수에 전달된 메시지가 문자열로 입력됩니다.

## <a name="scaling"></a>확장

이벤트 트리거 함수의 각 인스턴스는 단일 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스에서 지원됩니다. 트리거(Event Hubs 구동)는 하나의 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스만 지정된 파티션에서 임대를 받을 수 있도록 합니다.

예를 들어, 다음과 같은 Event Hub를 고려합니다.

* 10개 파티션
* 모든 파티션에 균등하게 분산된 1,000개 이벤트(각 파티션에 100개 메시지 포함)

함수를 처음 사용하는 경우 함수 인스턴스가 하나만 있습니다. 첫 번째 함수 인스턴스인 `Function_0`을 호출해 보겠습니다. `Function_0` 함수에는 10개 파티션 모두에 대한 임대를 보유하는 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)의 단일 인스턴스가 있습니다. 이 인스턴스는 파티션 0-9에서 이벤트를 읽습니다. 이 지점부터 다음 중 하나가 발생합니다.

* **새 함수 인스턴스가 필요하지 않음**: `Function_0`은 Functions 크기 조정 논리가 적용되기 전에 1,000개 이벤트를 모두 처리할 수 있습니다. 이 경우 1,000개 메시지는 모두 `Function_0`에서 처리됩니다.

* **추가 함수 인스턴스가 추가됨**: Functions 크기 조정 논리에서 처리할 수 있는 것보다 많은 메시지가 `Function_0`에 있다고 확인되면 새 함수 앱 인스턴스(`Function_1`)가 만들어집니다. 이 새 함수에도 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor)의 연결된 인스턴스가 있습니다. 기본 Event Hubs에서 새 호스트 인스턴스가 메시지 읽기를 시도하고 있음을 감지하면 호스트 인스턴스 간에 파티션의 부하를 분산합니다. 예를 들어, 파티션 0-4는 `Function_0`에, 파티션 5-9는 `Function_1`에 할당할 수 있습니다.

* **N 이상 함수 인스턴스 추가됨**: Functions 크기 조정 논리에서 처리할 수 있는 것보다 많은 메시지가 `Function_0` 및 `Function_1` 모두에 있다고 확인되면 새 함수 앱 인스턴스(`Functions_N`)가 만들어집니다.  앱은 `N`이 이벤트 허브 파티션 수보다 큰 지점에 만들어집니다. 이 예에서 Event Hubs는 다시 파티션을 부하 분산합니다(이 경우, 인스턴스 `Function_0`...`Functions_9`로).

크기 조정이 수행되면 `N` 인스턴스는 이벤트 허브 파티션 수보다 큰 숫자입니다. 이 패턴을 사용하여 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스가 다른 인스턴스에서 사용할 수 있게 될 때 파티션에서 잠금을 얻는 데 사용할 수 있는지 확인할 수 있습니다. 함수 인스턴스가 실행될 때 사용되는 리소스에 대해서만 요금이 청구됩니다. 즉, 이러한 과도한 프로비저닝에 대한 요금은 청구되지 않습니다.

모든 함수 실행이 오류 없이 완료되면 연결된 스토리지 계정에 검사점이 추가됩니다. 검사점이 성공적으로 설정되면 1,000개 메시지 모두가 다시 검색되지 않습니다.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 이벤트 허브 트리거의 메시지 본문을 기록하는 [C# 함수](../articles/azure-functions/functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

함수 코드에서 [이벤트 메타데이터](#event-metadata)에 대한 액세스를 얻으려면 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체에 바인딩합니다(`Microsoft.Azure.EventHubs`에 using 문이 필요함). 메서드 시그니처의 바인딩 식을 사용하여 동일한 속성에 액세스할 수도 있습니다.  다음 예제에서는 동일한 데이터를 가져오는 두 가지 방법을 모두 보여줍니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

일괄 처리에서 이벤트를 수신하려면 `string` 또는 `EventData` 배열을 만듭니다.  

> [!NOTE]
> 일괄로 이벤트를 수신하면 `DateTime enqueuedTimeUtc`를 사용하여 위 예제와 같이 메서드 매개 변수에 바인딩할 수 없으며 각 `EventData` 개체에서 이러한 매개 변수를 수신해야 합니다.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](../articles/azure-functions/functions-reference-csharp.md)를 보여줍니다. 함수는 이벤트 허브 트리거의 메시지 본문을 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

### <a name="version-2x-and-higher"></a>2\.x 이상 버전

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

함수 코드에서 [이벤트 메타데이터](#event-metadata)에 대한 액세스를 얻으려면 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체에 바인딩합니다(`Microsoft.Azure.EventHubs`에 using 문이 필요함). 메서드 시그니처의 바인딩 식을 사용하여 동일한 속성에 액세스할 수도 있습니다.  다음 예제에서는 동일한 데이터를 가져오는 두 가지 방법을 모두 보여줍니다.

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

일괄 처리에서 이벤트를 수신하려면 `string` 또는 `EventData` 배열을 만듭니다.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](../articles/azure-functions/functions-reference-node.md)를 보여줍니다. 함수는 [이벤트 메타데이터](#event-metadata)를 읽고 메시지를 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

### <a name="version-2x-and-higher"></a>2\.x 이상 버전

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

일괄 처리에서 이벤트를 수신하려면 다음 예제에 표시된 대로 *function.json* 파일에서 `cardinality`를 `many`로 설정합니다.

### <a name="version-2x-and-higher"></a>2\.x 이상 버전

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](../articles/azure-functions/functions-reference-python.md)를 보여줍니다. 함수는 [이벤트 메타데이터](#event-metadata)를 읽고 메시지를 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info(f'Function triggered to process a message: {event.get_body().decode()}')
    logging.info(f'  EnqueuedTimeUtc = {event.enqueued_time}')
    logging.info(f'  SequenceNumber = {event.sequence_number}')
    logging.info(f'  Offset = {event.offset}')

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = {event.metadata[key]}')
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 Event Hub 트리거의 메시지 본문을 기록하는 Event Hub 트리거 바인딩을 보여 줍니다.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에서 제공되는 매개 변수에 대한 `EventHubTrigger` 주석을 사용합니다. 이러한 주석을 사용하는 매개 변수로 인해 이벤트 도착 시 함수가 실행될 수 있습니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

 ---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)에서 [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 이벤트 허브의 이름, 소비자 그룹의 이름 및 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [트리거 구성 섹션](#configuration)을 참조하세요. `EventHubTriggerAttribute` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

전체 예제는 [트리거 - C# 예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

Java [함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에서 제공되는 매개 변수에 대해 [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) 주석을 사용합니다. 이러한 주석을 사용하는 매개 변수로 인해 이벤트 도착 시 함수가 실행될 수 있습니다. `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `EventHubTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `eventHubTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. |
|**path** |**EventHubName** | Functions 1.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**eventHubName** |**EventHubName** | Functions 2.x 이상. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. `%eventHubName%` [앱 설정](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)을 통해 참조할 수 있습니다. |
|**consumerGroup** |**ConsumerGroup** | 허브에서 이벤트를 구독하는 데 사용되는 [소비자 그룹](../articles/event-hubs/event-hubs-features.md#event-consumers)을 설정하는 선택적 속성입니다. 생략한 경우 `$Default` 소비자 그룹이 사용됩니다. |
|**cardinality** | 해당 없음 | C#이 아닌 모든 언어에 사용됩니다. `many`로 설정하여 일괄 처리할 수 있도록 합니다.  생략하거나 `one`으로 설정하면 단일 메시지가 함수에 전달됩니다.<br><br>C#의 경우 형식에 대한 배열이 트리거에 있을 때마다 이 속성이 자동으로 할당됩니다.|
|**connection** |**연결** | Event Hubs 연결하는 방법을 지정하는 앱 설정 또는 설정 컬렉션의 이름입니다. [연결](#connections)을 참조하세요.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-event-hubs-connections](./functions-event-hubs-connections.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>기본값

Event Hub를 트리거하는 데 대한 다음 매개 변수 형식을 사용할 수 있습니다.

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData의 기본 속성은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에서 제공됩니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Event Hub 확장을 사용하는 앱은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에 있는 `EventData` 유형 대신 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Body` 유형 지원을 중단합니다.

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>기본값

Event Hub를 트리거하는 데 대한 다음 매개 변수 형식을 사용할 수 있습니다.

* `string`
* `byte[]`
* `POCO`
* `EventData` - EventData의 기본 속성은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에서 제공됩니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Event Hub 확장을 사용하는 앱은 [Microsoft.Azure.EventHubs 네임스페이스](/dotnet/api/microsoft.azure.eventhubs.eventdata)에 있는 `EventData` 유형 대신 [Azure.Messaging.EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Body` 유형 지원을 중단합니다.

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody)

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 Java [트리거 예제](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

자세한 내용은 JavaScript [트리거 예제를](#example) 참조하세요.

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 Python [트리거 예제](#example)를 참조하세요.


---


## <a name="event-metadata"></a>이벤트 메타데이터

Event Hubs 트리거는 몇 가지 [메타데이터 속성](../articles/azure-functions/./functions-bindings-expressions-patterns.md)을 제공합니다. 메타데이터 속성은 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 속성은 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 클래스에서 제공됩니다.

|속성|형식|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 인스턴스입니다.|
|`EnqueuedTimeUtc`|`DateTime`|큐에 대기된 시간(UTC)입니다.|
|`Offset`|`string`|Event Hub 파티션 스트림을 기준으로 데이터의 오프셋 오프셋은 Event Hubs 스트림 내의 이벤트에 대한 표식 또는 식별자입니다. 식별자는 Event Hubs 스트림의 파티션 내에서 고유합니다.|
|`PartitionKey`|`string`|이벤트 데이터를 전송해야 하는 파티션|
|`Properties`|`IDictionary<String,Object>`|이벤트 데이터의 사용자 속성|
|`SequenceNumber`|`Int64`|이벤트의 논리적 시퀀스 번호|
|`SystemProperties`|`IDictionary<String,Object>`|이벤트 데이터를 비롯한 시스템 속성|

이 아티클의 앞부분에서 이러한 속성을 사용하는 [코드 예제](#example)를 참조하세요.
