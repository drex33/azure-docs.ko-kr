---
title: 지속성 함수의 바인딩 - Azure
description: Azure Functions의 Durable Functions 확장에 트리거 및 바인딩을 사용하는 방법입니다.
ms.topic: conceptual
ms.date: 08/03/2021
ms.author: azfuncdf
ms.openlocfilehash: 097527dbbf4363365e609a1f5aac1d851eb5dc60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567031"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>지속성 함수의 바인딩(Azure Functions)

[Durable Functions](durable-functions-overview.md) 확장은 오케스트레이터, 엔터티 및 작업 함수의 실행을 제어하는 3개의 트리거 바인딩을 도입합니다. 또한 지속성 함수 런타임에 대한 클라이언트 역할을 하는 출력 바인딩도 도입하고 있습니다.

## <a name="orchestration-trigger"></a>오케스트레이션 트리거

오케스트레이션 트리거를 사용하여 [지속성 오케스트레이터 함수](durable-functions-types-features-overview.md#orchestrator-functions)를 작성할 수 있습니다. 이 트리거는 새 오케스트레이션 인스턴스가 예약되었을 때 그리고 기존 오케스트레이션 인스턴스가 이벤트를 수신할 때 실행됩니다. 오케스트레이터 함수를 트리거할 수 있는 이벤트 예로는 지속성 타이머 만료, 작업 함수 응답, 외부 클라이언트에 의해 발생하는 이벤트가 포함됩니다.

.NET에서 함수를 작성할 때 오케스트레이션 트리거는 [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET 특성을 사용하여 구성됩니다.

JavaScript, Python 또는 PowerShell과 같은 스크립팅 언어에서 오케스트레이터 함수를 작성할 때 오케스트레이션 트리거는 *function.json* 파일의 `bindings` 배열에서 다음 JSON 객체로 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`은 이 오케스트레이터 함수의 새 인스턴스를 시작하려고 할 때 클라이언트에서 사용해야 하는 오케스트레이션 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 오케스트레이션 시작 이벤트, 지속성 타이머 만료 이벤트, 작업 함수 응답 이벤트 및 다른 함수에 의해 발생한 외부 이벤트와 같은 새 오케스트레이션 이벤트에 대해 구성된 지속성 저장소를 폴링합니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 오케스트레이션 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드** - 단일 디스패처 스레드는 단일 호스트 인스턴스에서 모든 오케스트레이터 함수를 실행하는 데 사용됩니다. 이러한 이유로 오케스트레이터 함수 코드가 효율적이고 모든 I/O를 수행하지 않도록 하는 것이 중요합니다. 또한 이 스레드에서 지속성 함수 관련 작업 종류를 기다리는 경우를 제외하고는 비동기 작업을 수행하지 않도록 하는 것이 중요합니다.
* **포이즌 메시지 처리** - 오케스트레이션 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 오케스트레이션 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다. 이러한 반환 값은 나중에 설명할 오케스트레이션 클라이언트 바인딩을 통해 쿼리할 수 있습니다.

> [!WARNING]
> 오케스트레이터 함수는 오케스트레이션 트리거 바인딩 이외의 입력 또는 출력 바인딩을 절대로 사용하면 안됩니다. 이렇게 하면 그러한 바인딩에서 단일 스레딩 및 I/O 규칙을 따르지 않을 수 있으므로 지속성 작업 확장에서 문제가 발생할 수 있습니다. 다른 바인딩을 사용하려면, 이를 Orchestrator 함수에서 호출한 작업 함수에 추가합니다. 오케스트레이터 함수의 코딩 제약 조건에 대한 자세한 내용은 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md) 설명서를 참조하세요.

> [!WARNING]
> JavaScript 및 Python 오케스트레이터 함수는 `async`로 선언되지 않아야 합니다.

### <a name="trigger-usage"></a>트리거 사용

오케스트레이션 트리거 바인딩은 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - 오케스트레이션 트리거는 컨텍스트 입력 개체를 통해 액세스되는 입력을 사용하여 호출될 수 있습니다. 모든 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 오케스트레이션 트리거는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다.

### <a name="trigger-sample"></a>트리거 샘플

다음은 가장 간단한 "Hello World" 오케스트레이터 함수 예제입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    // ... do some work ...
    return $"Hello {name}!";
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    // ... do some work ...
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `durable-functions` 라이브러리는 생성기 함수가 종료될 때 `context.done` 메서드 호출을 처리합니다.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    # Do some work
    return f"Hello {name}!"

main = df.Orchestrator.create(orchestrator_function)
```

---

대부분의 오케스트레이터 함수에서 작업 함수를 호출하므로 작업 함수를 호출하는 방법을 보여 주는 "Hello World" 예제가 여기에 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    result = yield context.call_activity('SayHello', name)
    return result

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="activity-trigger"></a>작업 트리거

작업 트리거를 사용하면 [작업 함수](durable-functions-types-features-overview.md#activity-functions)라고 하는 오케스트레이터 함수에서 호출하는 함수를 작성할 수 있습니다.

.NET에서 함수를 작성하는 경우 [ActivityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.activitytriggerattribute) .NET 특성을 사용하여 작업 트리거가 구성됩니다.

JavaScript, Python 또는 PowerShell을 사용하는 경우 작업 트리거는 *function.json* 의 `bindings` 배열에서 다음 JSON 개체에 의해 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`은 작업의 이름입니다. 이 값은 오케스트레이터 함수에서 이 작업 함수를 호출하는 데 사용하는 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 새 작업 실행 이벤트에 대해 구성된 지속성 저장소를 폴링합니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 작업 트리거에 대한 몇 가지 참고 사항입니다.

* **스레딩** - 오케스트레이션 트리거와 달리 작업 트리거에는 스레딩 또는 I/O에 대한 제한이 없습니다. 일반 함수처럼 처리될 수 있습니다.
* **포이즌 메시지 처리** - 작업 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 작업 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값이 JSON으로 직렬화되고 구성된 지속성 저장소에 저장됩니다.

### <a name="trigger-usage"></a>트리거 사용

작업 트리거 바인딩은 오케스트레이션 트리거와 마찬가지로 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - 작업 트리거는 오케스트레이터 함수의 입력으로 호출될 수 있습니다. 모든 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 작업 함수는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다.
* **메타데이터** - .NET 작업 함수는 `string instanceId` 매개변수에 바인딩되어 호출 오케스트레이션의 인스턴스 ID를 가져올 수 있습니다.

### <a name="trigger-sample"></a>트리거 샘플

다음 예시 코드는 간단한 `SayHello` 작업 함수가 어떻게 표시될 수 있는지를 보여줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET `ActivityTriggerAttribute` 바인딩의 기본 매개변수 유형은 [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext)(또는 Durable Functions v1의 경우 [DurableActivityContext](/dotnet/api/microsoft.azure.webjobs.durableactivitycontext?view=azure-dotnet-legacy&preserve-view=true))입니다. 그러나 .NET 작업 트리거는 JSON 직렬화 가능 형식(기본 형식 포함)에 대한 직접 바인딩도 지원하므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 바인딩도 추가 매개 변수로 전달될 수 있으므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
def main(name: str) -> str:
    return f"Hello {name}!"
```

---

### <a name="using-input-and-output-bindings"></a>입력 및 출력 바인딩 사용

작업 트리거 바인딩 외에도 일반 입력 및 출력 바인딩을 사용할 수 있습니다. 예를 들어, 작업 바인딩에 대한 입력을 가져오고 EventHub 출력 바인딩을 사용하여 EventHub로 메시지를 보낼 수 있습니다.

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>오케스트레이션 클라이언트

오케스트레이션 클라이언트 바인딩을 사용하면 오케스트레이터 함수와 상호 작용하는 함수를 작성할 수 있습니다. 이러한 함수를 종종 [클라이언트 함수](durable-functions-types-features-overview.md#client-functions)라고 부릅니다. 예를 들어 오케스트레이션 인스턴스에서 다음과 같은 방법으로 작동할 수 있습니다.

* 인스턴스를 시작합니다.
* 해당 상태를 쿼리합니다.
* 인스턴스를 종료합니다.
* 실행하는 동안 이벤트를 보냅니다.
* 인스턴스 기록을 제거합니다.

.NET을 사용하는 경우 [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) 특성(Durable Functions v1.x의 [OrchestrationClientAttribute](/dotnet/api/microsoft.azure.webjobs.orchestrationclientattribute?view=azure-dotnet-legacy&preserve-view=true))을 사용하여 오케스트레이션 클라이언트에 바인딩할 수 있습니다.

JavaScript, Python 또는 PowerShell과 같은 스크립팅 언어를 사용하는 경우 지속성 클라이언트 트리거는 *function.json* 의 `bindings` 배열에서 다음 JSON 개체에 의해 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 오케스트레이터 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 스토리지 계정은 대상 오케스트레이터 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 이러한 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

### <a name="client-usage"></a>클라이언트 사용

.NET 함수에서는 일반적으로 [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient)(Durable Functions v1.x의 [DurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclient?view=azure-dotnet-legacy&preserve-view=true))에 바인딩합니다. 그러면 Durable Functions에서 지원되는 모든 오케스트레이션 클라이언트 API에 대해 모든 액세스 권한을 부여합니다. 다른 언어에서는 클라이언트 개체에 액세스하기 위해 해당 언어별 SDK를 사용해야 합니다.

다음은 "HelloWorld" 오케스트레이션을 시작하는 큐 트리거 함수 예제입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**__init__.py**
```python
import json
import azure.functions as func
import azure.durable_functions as df

async def main(msg: func.QueueMessage, starter: str) -> None:
    client = df.DurableOrchestrationClient(starter)
    payload = msg.get_body().decode('utf-8')
    instance_id = await client.start_new("HelloWorld", client_input=payload)
```

---

인스턴스 시작에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)에서 찾을 수 있습니다.

## <a name="entity-trigger"></a>엔터티 트리거

엔터티 트리거를 사용하면 [엔터티 함수](durable-functions-entities.md)를 작성할 수 있습니다. 이 트리거는 특정 엔터티 인스턴스에 대한 이벤트 처리를 지원합니다.

> [!NOTE]
> 엔터티 트리거는 Durable Functions 2.x부터 사용할 수 있습니다.

내부적으로 이 트리거 바인딩은 실행해야 할 새 엔터티 작업에 대해 구성된 지속성 저장소를 폴링합니다.

.NET에서 함수를 작성하는 경우 [EntityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.entitytriggerattribute) .NET 특성을 사용하여 엔터티 트리거가 구성됩니다.

JavaScript, Python 또는 PowerShell을 사용하는 경우 엔터티 트리거는 *function.json* 의 `bindings` 배열에서 다음 JSON 개체에 의해 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "entityName": "<Optional - name of the entity>",
    "type": "entityTrigger",
    "direction": "in"
}
```

기본적으로 엔터티의 이름은 함수의 이름입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 엔터티 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드**: 단일 디스패처 스레드를 사용하여 특정 엔터티에 대한 작업을 처리합니다. 여러 메시지를 단일 엔터티로 동시에 보내는 경우, 작업은 한 번에 하나씩 처리됩니다.
* **포이즌 메시지 처리** - 엔터티 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 구성 가능한 기간 동안 엔터티 트리거 메시지가 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 엔터티 함수는 반환 값을 지원하지 않습니다. 상태를 저장하거나 값을 다시 오케스트레이션에 전달하는 데 사용할 수 있는 특정 API가 있습니다.

실행 중에 엔터티 상태를 변경하면 실행이 완료된 후 자동으로 유지 됩니다.

엔터티 트리거를 정의하고 상호 작용하는 방법과 예제는 [지속성 엔터티](durable-functions-entities.md) 설명서를 참조하세요.

## <a name="entity-client"></a>엔터티 클라이언트

엔터티 클라이언트 바인딩을 사용하면 [엔터티 함수](#entity-trigger)를 비동기적으로 트리거할 수 있습니다. 해당 함수를 [클라이언트 함수](durable-functions-types-features-overview.md#client-functions)라고도 합니다.

.NET 미리 컴파일된 함수를 사용하는 경우 [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) .NET 특성을 사용하여 엔터티 클라이언트에 바인딩할 수 있습니다.

> [!NOTE]
> `[DurableClientAttribute]`를 사용하여 [오케스트레이션 클라이언트](#orchestration-client)에 바인딩할 수도 있습니다.

개발을 위해 스크립팅 언어(예: C# 스크립팅, JavaScript 또는 Python)를 사용하는 경우 엔터티 트리거는 *function.json* 의 `bindings` 배열에 있는 다음 JSON 개체에 의해 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 엔터티 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 스토리지 계정은 대상 엔터티 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 선택적 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

클라이언트로 엔터티와 상호 작용하는 방법과 예제는 [지속성 엔터티](durable-functions-entities.md#access-entities) 설명서를 참조하세요.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리에 대한 기본 제공 HTTP API 참조](durable-functions-http-api.md)
