---
title: Azure Functions의 RabbitMQ 트리거
description: RabbitMQ 메시지를 만들 때 Azure 함수를 실행하는 방법을 알아봅니다.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 671fa1a8c9e0a9c29cfd5d2f93acd1aa7f3a8c4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658959"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Azure Functions의 RabbitMQ 트리거 개요

> [!NOTE]
> RabbitMQ 바인딩은 **Premium 및 Dedicated** 플랜에서만 완전하게 지원됩니다. 사용은 지원되지 않습니다.

RabbitMQ 트리거를 사용하여 RabbitMQ 큐의 메시지에 응답하세요.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-rabbitmq.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제는 RabbitMQ 메시지를 [RabbitMQ 이벤트](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)로 읽고 로그하는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

다음 예제는 메시지를 POCO로 읽는 방법을 보여 줍니다.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

Json 개체와 마찬가지로 메시지의 형식이 C# 개체로 올바르게 지정되지 않은 경우 오류가 발생합니다. 올바르게 지정되면 필요한 모든 항목에 사용할 수 있는 pocObj 변수에 바인딩됩니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예제는 *function.json* 파일의 RabbitMQ 트리거 바인딩과 이 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 이 함수는 RabbitMQ 메시지를 읽고 로그합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

C# 스크립트 코드는 다음과 같습니다.

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제는 *function.json* 파일의 RabbitMQ 트리거 바인딩과 이 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 이 함수는 RabbitMQ 메시지를 읽고 로그합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제는 트리거를 통해 RabbitMQ 큐 메시지를 읽는 방법을 보여 줍니다.

RabbitMQ 바인딩은 *type* 이 `RabbitMQTrigger`로 설정된 *function.json* 에서 정의됩니다.

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

다음 Java 함수는 [Java RabbitMQ 형식](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq)의 `@RabbitMQTrigger` 주석을 사용하여 RabbitMQ 큐 트리거의 구성을 설명합니다. 이 함수는 큐에 배치된 메시지를 가져와 로그에 추가합니다.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) 특성을 사용하세요.

다음은 메서드 서명의 `RabbitMQTrigger` 특성입니다.

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

전체 예제는 C# [예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQTrigger` 주석을 사용하면 RabbitMQ 메시지를 만들 때 실행되는 함수를 만들 수 있습니다. 사용 가능한 구성 옵션에는 큐 이름 및 연결 문자열 이름이 포함됩니다. 추가 매개 변수 세부 정보를 보려면 [RabbitMQTrigger Java 주석](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)을 방문하세요.

자세한 내용은 트리거 [예제](#example)를 참조하세요.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `RabbitMQTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | ‘RabbitMQTrigger’로 설정해야 합니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다.|
|**name** | 해당 없음 | 함수 코드에서 큐를 나타내는 변수의 이름입니다. |
|**queueName**|**QueueName**| 메시지를 받을 큐의 이름입니다. |
|**hostName**|**HostName**|(ConnectStringSetting을 사용하는 경우 무시됨) <br>큐의 호스트 이름(예: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ConnectionStringSetting을 사용하는 경우 무시됨) <br>큐에 액세스하는 사용자 이름이 포함된 앱 설정의 이름입니다. 예: UserNameSetting: "%< UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ConnectionStringSetting을 사용하는 경우 무시됨) <br>큐에 액세스하기 위한 암호가 포함된 앱 설정의 이름입니다. 예: PasswordSetting: "%< PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|RabbitMQ 메시지 큐 연결 문자열이 포함된 앱 설정의 이름입니다. local.settings.json의 앱 설정을 통하지 않고 직접 연결 문자열을 지정하는 경우에는 트리거가 작동하지 않습니다. (예: *function.json*: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**포트**|(ConnectionStringSetting을 사용하는 경우 무시됨) 사용되는 포트를 가져오거나 설정합니다. 기본값은 RabbitMQ 클라이언트의 기본 포트 설정인 5672를 가리키는 0입니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

기본 메시지 유형은 [RabbitMQ 이벤트](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)이며, RabbitMQ 이벤트의 `Body` 속성은 아래 나열된 유형으로 읽을 수 있습니다.

* `An object serializable as JSON` - 메시지가 유효한 JSON 문자열로 전달됩니다.
* `string`
* `byte[]`
* `POCO` - 메시지의 형식이 C# 개체로 지정됩니다. 전체 예제는 C# [예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

기본 메시지 유형은 [RabbitMQ 이벤트](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)이며, RabbitMQ 이벤트의 `Body` 속성은 아래 나열된 유형으로 읽을 수 있습니다.

* `An object serializable as JSON` - 메시지가 유효한 JSON 문자열로 전달됩니다.
* `string`
* `byte[]`
* `POCO` - 메시지의 형식이 C# 개체로 지정됩니다. 전체 예제는 C# 스크립트 [예제](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

큐 메시지는 context.bindings를 통해 사용 가능합니다.\<NAME\> 여기서 \<NAME\>은 function.json에 정의된 이름과 일치합니다. 페이로드가 JSON인 경우 값은 개체로 역직렬화됩니다.

# <a name="python"></a>[Python](#tab/python)

Python [예제](#example)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

Java [특성 및 주석](#attributes-and-annotations)을 참조하세요.

---

## <a name="dead-letter-queues"></a>배달 못한 편지 큐
배달 못한 편지 큐 및 교환은 RabbitMQ 트리거에서 제어하거나 구성할 수 없습니다.  배달 못한 편지 큐를 사용하려면 RabbitMQ의 트리거가 사용하는 큐를 미리 구성합니다. [RabbitMQ 설명서](https://www.rabbitmq.com/dlx.html)를 참조하세요.

## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------|
|prefetchCount|30|메시지 수신자가 동시에 요청할 수 있고 캐시되는 메시지 수를 가져오거나 설정합니다.|
|queueName|해당 없음| 메시지를 받을 큐의 이름입니다.|
|connectionString|해당 없음|RabbitMQ 메시지 큐 연결 문자열입니다. 연결 문자열은 앱 설정을 통하지 않고 여기서 직접 지정됩니다.|
|포트|0|(connectionString을 사용하는 경우 무시됨) 사용되는 포트를 가져오거나 설정합니다. 기본값은 rabbitmq 클라이언트의 기본 포트 설정인 5672을 가리키는 0입니다.|

## <a name="local-testing"></a>로컬 테스트

> [!NOTE]
> connectionString이 ‘hostName’, ‘userName’, ‘password’보다 우선적으로 적용됩니다. 이 모두가 설정되어 있으면 connectionString이 다른 둘을 재정의합니다.

연결 문자열 없이 로컬로 테스트하는 경우 *host.json* 의 ‘rabbitMQ’ 섹션에서 해당되는 경우 ‘hostName’ 설정과 ‘userName’ 및 ‘password’를 설정해야 합니다.

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------|
|hostName|해당 없음|(connectionString을 사용하는 경우 무시됨) <br>큐의 호스트 이름(예: 10.26.45.210)|
|userName|해당 없음|(connectionString을 사용하는 경우 무시됨) <br>큐에 액세스하기 위한 이름 |
|password|해당 없음|(connectionString을 사용하는 경우 무시됨) <br>큐에 액세스하기 위한 암호|


## <a name="enable-runtime-scaling"></a>런타임 스케일링 사용

RabbitMQ 트리거가 여러 인스턴스로 확장되려면 **런타임 스케일링 모니터링** 설정을 사용하도록 설정해야 합니다. 

포털에서 이 설정은 함수 앱의 **구성** > **함수 런타임 설정** 에서 찾을 수 있습니다.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

CLI에서 다음 명령을 사용하여 **런타임 스케일링 모니터링** 을 사용하도록 설정할 수 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>RabbitMQ 엔드포인트 모니터링
특정 RabbitMQ 엔드포인트의 큐 및 교환을 모니터링하는 방법은 다음과 같습니다.

* [RabbitMQ 관리 플러그 인](https://www.rabbitmq.com/management.html)을 사용하도록 설정합니다.
* http://{node-hostname}:15672로 이동하고 사용자 이름과 암호를 사용하여 로그인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Functions에서 RabbitMQ 메시지 보내기(출력 바인딩)](./functions-bindings-rabbitmq-output.md)
