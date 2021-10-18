---
title: Azure Functions의 Azure Service Bus 출력 바인딩
description: Azure Functions에서 Azure Service Bus 메시지를 보내는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0df83e6e335599b4942d53962add9083a2ecce12
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130138264"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions의 Azure Service Bus 출력 바인딩

Azure Service Bus 출력 바인딩을 사용하여 큐 또는 토픽 메시지를 보냅니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-service-bus.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 Service Bus 큐 메시지를 보내는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Service Bus 출력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 타이머 트리거를 사용하여 15초마다 큐 메시지를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

단일 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

여러 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 HTTP 요청으로 트리거될 때 `myqueue` Service Bus 큐에 메시지를 보내는 Java 함수를 보여줍니다.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Service Bus 큐에 기록될 함수 매개 변수에 대한 `@QueueOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO의 원시 Java 형식입니다.

Java 함수는 Service Bus 토픽에 쓸 수도 있습니다. 다음 예제에서는 `@ServiceBusTopicOutput` 주석을 사용하여 출력 바인딩의 구성을 설명합니다. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 Service Bus 출력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 타이머 트리거를 사용하여 15초마다 큐 메시지를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

단일 메시지를 만드는 JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

여러 메시지를 만드는 JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 예제에서는 바인딩을 사용하는 *function.json* 파일과 [PowerShell 함수](functions-reference-powershell.md)의 Service Bus 출력 바인딩을 보여줍니다. 

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

다음은 함수의 출력으로 메시지를 만드는 PowerShell입니다.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 Python에서 Service Bus 큐에 쓰는 방법을 보여줍니다.

Service Bus 바인딩은 *형식* 이 `serviceBus`로 설정된 *function.json* 에 정의됩니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

*_\_init_\_.py* 에서 `set` 메서드에 값을 전달하여 큐에 메시지를 쓸 수 있습니다.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)를 사용합니다.

특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 설정을 선택하는 방법은 [출력 - 구성](#configuration) 섹션에서 설명합니다. 함수의 반환 값에 적용된 특성을 보여주는 예제는 다음과 같습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

다음 예와 같이 `Connection` 속성을 설정하여 사용할 Service Bus 연결 문자열이 포함된 앱 설정 이름을 지정할 수 있습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

전체 예제는 [출력 - 예제](#example)를 참조하세요.

`ServiceBusAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 사용할 Service Bus 계정을 지정합니다.  자세한 내용은 [트리거 - 특성](functions-bindings-service-bus-trigger.md#attributes-and-annotations)을 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`및 `ServiceBusTopicOutput` 주석을 사용하여 메시지를 함수 출력으로 쓸 수 있습니다. 이러한 주석으로 장식된 매개 변수는 `T`가 메시지의 형식에 해당하는 형식인 `OutputBinding<T>`로 선언되어야 합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `ServiceBus` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBus"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽 메시지를 나타내는 변수의 이름입니다. "$return"으로 설정하여 함수 반환 값을 참조합니다. |
|**queueName**|**QueueName**|큐의 이름입니다.  토픽이 아닌 큐 메시지를 보내는 경우에만 설정합니다.
|**topicName**|**TopicName**|항목의 이름입니다. 큐가 아닌 토픽 메시지를 보내는 경우에만 설정합니다.|
|**connection**|**연결**|Service Bus에 연결 하는 방법을 지정 하는 설정 컬렉션 또는 앱 설정의 이름입니다. [연결](#connections)을 참조하세요.|
|**accessRights**(v1만 해당)|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x 이상에서는 최신 버전의 Service Bus SDK가 관리 작업을 지원하지 않으므로 이 속성을 사용할 수 없습니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-service-bus-connections](../../includes/functions-service-bus-connections.md)]

## <a name="usage"></a>사용량

Azure Functions 1.x에서 큐가 존재하지 않고 `accessRights`를 `manage`로 설정한 경우 런타임은 큐를 만듭니다. Functions 버전 2.x 이상에서는 큐 또는 토픽이 이미 있어야 합니다. 존재하지 않는 큐 또는 토픽을 지정하면 함수가 실패합니다. 

# <a name="c"></a>[C#](#tab/csharp)

출력 바인딩에 다음 매개 변수 형식을 사용합니다.

* `out T paramName` - `T`는 JSON 직렬화 가능 형식일 수 있습니다. 함수가 종료될 때 매개 변수 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `out string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out BrokeredMessage` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다(Functions 1.x의 경우).
* `out Message` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다(Functions 2.x 이상의 경우).
* `ICollector<T>` 또는 `IAsyncCollector<T>`(비동기 메서드의 경우) - 여러 메시지를 만드는 데 사용할 수 있습니다. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

C# 함수를 사용하는 경우:

* 비동기 함수는 `out` 매개 변수 대신 반환 값 또는 `IAsyncCollector`가 필요합니다.

* 세션 ID에 액세스하려면 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 형식에 바인딩하고 `sessionId` 속성을 사용합니다.

### <a name="additional-types"></a>추가 형식 

5\.0.0 이상 버전의 Service Bus 확장을 사용하는 앱은 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 네임스페이스에 있는 `ServiceBusMessage` 유형 대신 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusmessage)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Message` 유형 지원을 중단합니다.

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

출력 바인딩에 다음 매개 변수 형식을 사용합니다.

* `out T paramName` - `T`는 JSON 직렬화 가능 형식일 수 있습니다. 함수가 종료될 때 매개 변수 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `out string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out BrokeredMessage` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다(Functions 1.x의 경우).
* `out Message` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다(Functions 2.x 이상의 경우).
* `ICollector<T>` 또는 `IAsyncCollector<T>` - 여러 개의 메시지를 만들려는 경우. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

C# 함수를 사용하는 경우:

* 비동기 함수는 `out` 매개 변수 대신 반환 값 또는 `IAsyncCollector`가 필요합니다.

* 세션 ID에 액세스하려면 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 형식에 바인딩하고 `sessionId` 속성을 사용합니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Service Bus 확장을 사용하는 앱은 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 네임스페이스에 있는 형식 대신 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusmessage)에 있는 `ServiceBusMessage` 형식을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Message` 유형 지원을 중단합니다.

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="java"></a>[Java](#tab/java)

기본 제공 출력 바인딩 대신 [Azure Service Bus SDK](../service-bus-messaging/index.yml)를 사용합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>`을 사용하여 큐 또는 토픽에 액세스합니다. 문자열, 바이트 배열 또는 JavaScript 개체(JSON으로 역직렬화됨)를 `context.binding.<name>`에 할당할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Service Bus에 대한 출력은 *function.json* 파일에서 바인딩의 이름 매개 변수에 지정된 이름과 일치하는 인수를 전달하는 `Push-OutputBinding` cmdlet을 통해 사용할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

기본 제공 출력 바인딩 대신 [Azure Service Bus SDK](../service-bus-messaging/index.yml)를 사용합니다.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참고 |
|---|---|
| Service Bus | [Service Bus 오류 코드](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md) |

## <a name="next-steps"></a>다음 단계

- [Service Bus 큐 또는 토픽 메시지가 생성(트리거)될 때 함수 실행](./functions-bindings-service-bus-trigger.md)
