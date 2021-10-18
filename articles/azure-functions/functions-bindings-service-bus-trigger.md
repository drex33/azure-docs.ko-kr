---
title: Azure Functions에 Azure Service Bus 트리거
description: Azure Service Bus 메시지가 생성될 때 Azure Function을 실행하는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 56f0a58d1713a2ddf47734686214846c7765baf5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137861"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions에 Azure Service Bus 트리거

Service Bus 트리거를 사용하여 Service Bus 큐 또는 토픽의 메시지에 응답합니다. 확장 버전 3.1.0부터 세션 사용 큐나 토픽에서 트리거할 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-service-bus.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="java"></a>[Java](#tab/java)

다음 Java 함수는 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)의 `@ServiceBusQueueTrigger` 주석을 사용하여 Service Bus 큐 트리거 구성을 설명합니다. 이 함수는 큐에 있는 메시지를 가져와 로그에 추가합니다.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

메시지가 Service Bus 토픽에 추가될 때 Java 함수도 트리거할 수 있습니다. 다음 예에서는 `@ServiceBusTopicTrigger` 주석을 사용하여 트리거 구성을 설명합니다.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 예에서는 바인딩을 사용하는 *function.json* 파일과 [PowerShell 함수](functions-reference-powershell.md)의 Service Bus 트리거 바인딩을 보여줍니다. 

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Service Bus 메시지를 보낼 때 실행되는 함수는 다음과 같습니다.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 트리거를 통해 Service Bus 큐 메시지를 읽는 방법을 보여줍니다.

Service Bus 바인딩은 *type* 이 `serviceBusTrigger`로 설정된 *function.json* 에서 정의됩니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

*_\_init_\_.py* 의 코드는 매개 변수를 `func.ServiceBusMessage`로 선언하므로 함수의 큐 메시지를 읽을 수 있습니다.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 Service Bus 트리거를 구성합니다.

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. Azure Functions 버전 1.x에서는 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 지정하지 않으면 기본값은 `Manage`입니다. 자세한 내용은 [트리거 - 구성](#configuration) 섹션을 참조하세요.

  문자열 매개 변수와 함께 사용되는 특성을 보여주는 예제는 다음과 같습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  `Connection` 속성이 정의되지 않았으므로 Functions는 Service Bus 연결 문자열의 기본 이름인 `AzureWebJobsServiceBus`라는 앱 설정을 찾습니다. 또한 다음 예와 같이 `Connection` 속성을 설정하여 사용할 Service Bus 연결 문자열이 포함된 애플리케이션 설정 이름을 지정할 수 있습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  전체 예는 [트리거 - 예](#example)를 참조하세요.

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  사용할 Service Bus 계정을 지정하는 다른 방법을 제공합니다. 생성자는 Service Bus 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

사용할 Service Bus 계정은 다음과 같은 순서로 결정됩니다.

* `ServiceBusTrigger` 특성의 `Connection` 속성
* `ServiceBusTrigger` 특성과 동일한 매개 변수에 적용된 `ServiceBusAccount` 특성
* 함수에 적용된 `ServiceBusAccount` 특성
* 클래스에 적용된 `ServiceBusAccount` 특성
* "AzureWebJobsServiceBus" 앱 설정입니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger` 주석을 사용하면 Service Bus 큐 메시지를 만들 때 실행되는 함수를 만들 수 있습니다. 사용 가능한 구성 옵션에는 큐 이름 및 연결 문자열 이름이 포함됩니다.

`ServiceBusTopicTrigger` 주석을 사용하면 토픽과 구독을 데이터가 함수를 트리거하는 대상으로 지정할 수 있습니다.

자세한 내용은 트리거 [예](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `ServiceBusTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBusTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽 메시지를 나타내는 변수의 이름입니다. |
|**queueName**|**QueueName**|모니터링할 큐의 이름입니다.  토픽이 아닌 큐를 모니터링하는 경우에만 설정합니다.
|**topicName**|**TopicName**|모니터링할 토픽의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**subscriptionName**|**SubscriptionName**|모니터링할 구독의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**connection**|**연결**| Service Bus에 연결 하는 방법을 지정 하는 설정 컬렉션 또는 앱 설정의 이름입니다. [연결](#connections)을 참조하세요.|
|**accessRights**|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x 이상에서는 최신 버전의 Service Bus SDK가 관리 작업을 지원하지 않으므로 이 속성을 사용할 수 없습니다.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` [세션 인식](../service-bus-messaging/message-sessions.md) 큐나 구독에 연결하는 경우 입니다. `false` 그렇지 않으면 기본값입니다.|
|**autoComplete**|**AutoComplete**|`true` 트리거가 처리 후 자동으로 complete를 호출해야 하는지 아니면 함수 코드가 수동으로 complete를 호출하는지 여부입니다.<br><br>`false`로 설정은 C#에서만 지원됩니다.<br><br>`true`로 설정한 경우 함수 실행이 성공적으로 완료되면 트리거가 자동으로 메시지를 완료하고 그렇지 않으면 메시지가 중단됩니다.<br><br>`false`로 설정한 경우 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) 메서드를 호출하여 메시지를 완료, 중단 또는 배달 못하도록 합니다. 예외가 발생하고 `MessageReceiver` 메서드가 호출되지 않으면 잠금이 유지됩니다. 잠금이 만료되면 `DeliveryCount`가 증가하여 메시지가 다시 큐에 추가되고 잠금이 자동으로 갱신됩니다.<br><br>C#이 아닌 함수에서 함수의 예외로 인해 백그라운드에서 런타임 호출 `abandonAsync`가 발생합니다. 예외가 발생하지 않으면 `completeAsync`가 백그라운드에서 호출됩니다. Azure Functions 2.x 이상에서만 이 속성을 사용할 수 있습니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-service-bus-connections](../../includes/functions-service-bus-connections.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

큐 또는 토픽 메시지에 사용할 수 있는 매개 변수 유형은 다음과 같습니다.

* `string` - 메시지가 텍스트인 경우
* `byte[]` - 이진 데이터에 유용합니다.
* 사용자 지정 형식 - 메시지에 JSON이 포함된 경우 Azure Functions는 JSON 데이터를 역직렬화하려고 합니다.
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용하는 역직렬화된 메시지가 표시됩니다.
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) - 메시지 컨테이너에서 메시지를 수신하고 승인하는 데 사용됩니다([`autoComplete`](functions-bindings-service-bus.md#hostjson-settings)이 `false`로 설정된 경우에는 필수).

이러한 매개 변수 유형은 Azure Functions 버전 1.x에서 사용되므로 2.x 이상에서는 `BrokeredMessage` 대신 [`Message`](/dotnet/api/microsoft.azure.servicebus.message)를 사용합니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Service Bus 확장을 사용하는 앱은 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 네임스페이스에 있는 `ServiceBusReceivedMessage` 유형 대신 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Message` 유형 지원을 중단합니다.

- [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

큐 또는 토픽 메시지에 사용할 수 있는 매개 변수 유형은 다음과 같습니다.

* `string` - 메시지가 텍스트인 경우
* `byte[]` - 이진 데이터에 유용합니다.
* 사용자 지정 형식 - 메시지에 JSON이 포함된 경우 Azure Functions는 JSON 데이터를 역직렬화하려고 합니다.
* `BrokeredMessage` - [BrokeredMessage.GetBody\<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용하는 역직렬화된 메시지가 표시됩니다.

이러한 매개 변수는 Azure Functions 버전 1.x에서 사용되므로 2.x 이상에서는 `BrokeredMessage` 대신 [`Message`](/dotnet/api/microsoft.azure.servicebus.message)를 사용합니다.

### <a name="additional-types"></a>추가 형식 
5\.0.0 이상 버전의 Service Bus 확장을 사용하는 앱은 [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message) 네임스페이스에 있는 `ServiceBusReceivedMessage` 유형 대신 [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)에 있는 유형을 사용합니다. 이 버전은 다음 유형을 위해 레거시 `Message` 유형 지원을 중단합니다.

- [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage)

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueMessage` 또는 `ServiceBusTopicMessage` 매개 변수를 통해 들어오는 Service Bus 메시지를 사용할 수 있습니다.

[자세한 내용은 다음 예를 참조하세요](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name from function.json>`를 사용하여 큐 또는 토픽 메시지에 액세스합니다. Service Bus 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* 파일의 이름 속성에 구성된 매개 변수를 통해 Service Bus 인스턴스를 사용할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

`func.ServiceBusMessage`로 형식화된 매개 변수를 통해 큐 메시지를 함수에서 사용할 수 있습니다. Service Bus 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

---

## <a name="poison-messages"></a>포이즌 메시지

포이즌 메시지 처리는 Azure Functions에서 제어되거나 구성될 수 없습니다. Service Bus는 스스로 포이즌 메시지를 처리합니다.

## <a name="peeklock-behavior"></a> PeekLock 동작

Functions 런타임은 [PeekLock 모드](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)로 메시지를 수신합니다. 함수가 성공적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 함수가 실행되면 잠금이 자동으로 갱신됩니다.

`maxAutoRenewDuration`은 [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration)에 매핑되는 *host.json* 에서 구성할 수 있습니다. 이 설정에 대해 허용되는 최대값음 Service Bus 설명서에 따라 5분입니다. 반면 함수 제한 시간 기본값은 5분에서 10분으로 늘릴 수 있습니다. Service Bus 함수의 경우 Service Bus 갱신 제한을 초과하기 때문에 이 작업을 하지 않는 것이 좋습니다.

## <a name="message-metadata"></a>메시지 메타데이터

Service Bus 트리거는 몇 가지 [메타데이터 속성](./functions-bindings-expressions-patterns.md#trigger-metadata)을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 속성은 [Message](/dotnet/api/microsoft.azure.servicebus.message) 클래스의 구성원입니다.

|속성|형식|Description|
|--------|----|-----------|
|`ContentType`|`string`|애플리케이션별 논리의 보낸 사람과 받는 사람이 활용하는 콘텐츠 형식 식별자입니다.|
|`CorrelationId`|`string`|상관관계 ID입니다.|
|`DeadLetterSource`|`string`|배달 못한 편지 원본입니다.|
|`DeliveryCount`|`Int32`|배달 수입니다.|
|`EnqueuedTimeUtc`|`DateTime`|큐에 대기된 시간(UTC)입니다.|
|`ExpiresAtUtc`|`DateTime`|만료 시간(UTC)입니다.|
|`Label`|`string`|애플리케이션별 레이블입니다.|
|`MessageId`|`string`|Service Bus에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|`MessageReceiver`|`MessageReceiver`|Service Bus 메시지 받는 사람입니다. 메시지를 중단, 완료 또는 배달 못하는 데 사용할 수 있습니다.|
|`MessageSession`|`MessageSession`|세션을 사용하는 큐와 토픽에 대한 메시지 받는 사람입니다.|
|`ReplyTo`|`string`|큐 주소에 대한 회신입니다.|
|`SequenceNumber`|`long`|Service Bus에 의해 메시지에 할당되는 고유 번호입니다.|
|`To`|`string`|주소로 보내기입니다.|
|`UserProperties`|`IDictionary<string, object>`|보낸 사람이 설정한 속성입니다. (버전 5.x 이상의 확장에서는 지원하지 않습니다. `ApplicationProperties`를 사용하세요.)|

이 아티클의 앞부분에서 이러한 속성을 사용하는 [코드 예제](#example)를 참조하세요.

### <a name="additional-message-metadata"></a>추가 메시지 메타데이터

다음 메타데이터 속성은 5.0.0 이상의 확장을 사용하는 앱에서 지원됩니다. 이러한 속성은 [ServiceBusReceivedMessage](/dotnet/api/azure.messaging.servicebus.servicebusreceivedmessage) 클래스의 구성원입니다.

|속성|형식|Description|
|--------|----|-----------|
|`ApplicationProperties`|`ApplicationProperties`|보낸 사람이 설정한 속성입니다. `UserProperties` 메타데이터 속성 대신 사용합니다.|
|`Subject`|`string`|`Label` 메타데이터 속성 대신 사용할 수 있는 애플리케이션별 레이블입니다.|
|`MessageActions`|`ServiceBusMessageActions`|`ServiceBusReceivedMessage`에서 수행할 수 있는 작업 집합입니다. `MessageReceiver` 메타데이터 속성 대신 사용할 수 있습니다.
|`SessionActions`|`ServiceBusSessionMessageActions`|세션 및 `ServiceBusReceivedMessage`에서 수행할 수 있는 작업 집합입니다. `MessageSession` 메타데이터 속성 대신 사용할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Functions에서 Azure Service Bus 메시지 전송(출력 바인딩)](./functions-bindings-service-bus-output.md)
