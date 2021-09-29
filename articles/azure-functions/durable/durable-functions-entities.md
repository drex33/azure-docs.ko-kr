---
title: 지속성 엔터티 - Azure Functions
description: 지속성 엔터티에 대한 기본 사항 및 Azure Functions용 Durable Functions 확장에서 이를 사용하는 방법을 알아봅니다.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: bdc5ba0c21bc55c04c8407e218fd09eecd726be2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603603"
---
# <a name="entity-functions"></a>엔터티 함수

엔터티 함수는 *지속성 엔터티* 라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거* 를 사용하는 함수입니다. 오케스트레이터 함수와 달리 엔터티 함수는 제어 흐름을 통해 엔터티의 상태를 암시적으로 표시하는 것이 아니라 명시적으로 관리합니다.
엔터티는 각각 적당한 크기의 상태인 여러 엔터티 간에 작업을 분산하여 애플리케이션을 확장할 수 있는 방법을 제공합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 [Durable Functions 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) 이상에서만 사용할 수 있습니다. 현재 .NET 및 JavaScript 및 Python에서 지원됩니다.

## <a name="general-concepts"></a>일반 개념

엔터티는 메시지를 통해 통신하는 작은 서비스처럼 작동합니다. 각 엔터티에는 고유한 ID와 내부 상태가 있습니다(있는 경우). 서비스 또는 개체와 마찬가지로 엔터티도 작업을 수행하라는 메시지가 표시되면 해당 작업을 수행합니다. 작업이 실행되면 작업에서 엔터티의 내부 상태를 업데이트할 수 있습니다. 또한 외부 서비스를 호출하고 응답을 기다릴 수도 있습니다. 엔터티는 신뢰할 수 있는 큐를 통해 암시적으로 보내는 메시지를 사용하여 다른 엔터티, 오케스트레이션 및 클라이언트와 통신합니다. 

충돌을 방지하기 위해 단일 엔터티에 대한 모든 작업이 연속적으로, 즉 순차적으로 실행되도록 합니다.

> [!NOTE]
> 엔터티를 호출하는 경우 해당 페이로드를 완료될 때까지 처리한 다음, 후속 입력이 도착할 때 활성화되도록 새 실행을 예약합니다. 따라서 엔터티 실행 로그에는 각 엔터티 호출 후에 추가 실행이 표시될 수 있습니다. 이것은 예상된 동작입니다.

### <a name="entity-id"></a>엔터티 ID
엔터티는 고유 식별자인 *엔터티 ID* 를 통해 액세스됩니다. 엔터티 ID는 단순히 엔터티 인스턴스를 고유하게 식별하는 문자열 쌍입니다. 이 ID는 다음으로 구성됩니다.

* **엔터티 이름**: 엔터티 형식을 식별하는 이름입니다. 예를 들면 "Counter"와 같습니다. 이 이름은 엔터티를 구현하는 엔터티 함수의 이름과 일치해야 합니다. 대/소문자를 구분하지 않습니다.
* **엔터티 키**: 동일한 이름의 다른 모든 엔터티 중에서 해당 엔터티를 고유하게 식별하는 문자열입니다. 예를 들면 GUID와 같습니다.

예를 들어 `Counter` 엔터티 함수는 온라인 게임에서 점수를 유지하는 데 사용할 수 있습니다. 게임의 각 인스턴스에는 `@Counter@Game1` 및 `@Counter@Game2`와 같은 고유한 엔터티 ID가 있습니다. 특정 엔터티를 대상으로 하는 모든 작업에는 엔터티 ID를 매개 변수로 지정해야 합니다.

### <a name="entity-operations"></a>엔터티 작업 ###

엔터티에 대한 작업을 호출하려면 다음을 지정합니다.

* 대상 엔터티의 **엔터티 ID**
* **작업 이름**: 수행할 작업을 지정하는 문자열입니다. 예를 들어 `Counter` 엔터티는 `add`, `get` 또는 `reset` 작업을 지원할 수 있습니다.
* **작업 입력**: 작업에 대한 선택적 입력 매개 변수입니다. 예를 들어, add 작업은 정수 값을 입력으로 사용할 수 있습니다.
* 작업의 제공 시간을 지정하기 위한 선택적 매개 변수인 **예약 시간** 입니다. 예를 들어 향후 며칠 동안 작업이 안정적으로 실행되도록 예약할 수 있습니다.

작업은 결과 값 또는 오류 결과(예: JavaScript 오류 또는 .NET 예외)를 반환할 수 있습니다. 이 결과 또는 오류는 작업을 호출한 오케스트레이션에서 관찰할 수 있습니다.

엔터티 작업은 엔터티의 상태를 만들고, 읽고, 업데이트하고, 삭제할 수도 있습니다. 엔터티의 상태는 항상 스토리지에 지속적으로 유지됩니다.

## <a name="define-entities"></a>엔터티 정의

현재, 엔터티를 정의하기 위한 두 가지 고유한 API는 다음과 같습니다.

**함수 기반 구문**: 엔터티가 함수로 표시되고 애플리케이션에서 작업을 명시적으로 디스패치합니다. 이 구문은 간단한 상태, 몇 가지 작업 또는 동적 작업 세트(예: 애플리케이션 프레임워크)가 있는 엔터티에서 효율적으로 작동합니다. 이 구문은 컴파일 타임에 형식 오류를 catch하지 않으므로 유지 관리하는 데 번거로울 수 있습니다.

**클래스 기반 구문(.NET 전용)** : 엔터티와 작업이 클래스와 메서드로 표시됩니다. 이 구문은 더 쉽게 읽을 수 있는 코드를 생성하며, 형식이 안전한 방식으로 작업을 호출할 수 있도록 합니다. 클래스 기반 구문은 함수 기반 구문 위에 있는 씬(thin) 계층이므로 두 변형을 모두 동일한 애플리케이션에서 교대로 사용할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>예제: 함수 기반 구문 - C#

다음 코드는 지속성 함수로 구현된 간단한 `Counter` 엔터티의 예제입니다. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

함수 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [함수 기반 구문](durable-functions-dotnet-entities.md#function-based-syntax)을 참조하세요.

### <a name="example-class-based-syntax---c"></a>예제: 클래스 기반 구문 - C#

다음 예제에서는 클래스와 메서드를 사용하여 `Counter` 엔터티를 동일하게 구현합니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

이 엔터티의 상태는 카운터의 현재 값을 저장하는 필드가 포함된 `Counter` 형식의 개체입니다. 이 개체를 스토리지에 유지하기 위해 [Json.NET](https://www.newtonsoft.com/json) 라이브러리에서 직렬화 및 역직렬화합니다. 

클래스 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [엔터티 클래스 정의](durable-functions-dotnet-entities.md#defining-entity-classes)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>예제: JavaScript 엔터티

지속성 엔터티는 `durable-functions` npm 패키지의 버전 **1.3.0** 부터 JavaScript에서 사용할 수 있습니다. 다음 코드는 JavaScript로 작성된 지속성 함수로 구현된 `Counter` 엔터티입니다.

**Counter/function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Counter/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>예: Python 엔터티

다음 코드는 Python으로 작성된 지속성 함수로 구현된 `Counter` 엔터티입니다.

**Counter/function.json**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>액세스 엔터티

엔터티는 단방향 또는 양방향 통신을 사용하여 액세스할 수 있습니다. 다음은 두 가지 형태의 통신을 구분하는 용어입니다. 

* 엔터티 **호출** 에서는 양방향(라운드트립) 통신을 사용합니다. 작업 메시지를 엔터티로 보낸 다음, 계속하기 전에 응답 메시지를 기다립니다. 응답 메시지는 결과 값 또는 오류 결과(예: JavaScript 오류 또는 .NET 예외)를 제공할 수 있습니다. 이 결과 또는 오류는 호출자에서 관찰합니다.
* 엔터티에 **신호 보내기** 에서는 단방향(자체 유도) 통신을 사용합니다. 작업 메시지를 전송하지만 응답을 기다리지는 않습니다. 메시지가 최종적으로 배달되도록 보장하는 동안 발신자는 배달되는 시기를 인식하지 못하며 결과 값 또는 오류를 관찰할 수 없습니다.

엔터티는 클라이언트 함수, 오케스트레이터 함수 또는 엔터티 함수 내에서 액세스할 수 있습니다. 다음과 같이 일부 통신 형식만 모든 컨텍스트에서 지원합니다.

* 클라이언트 내에서 엔터티에 신호를 보내고 엔터티 상태를 읽을 수 있습니다.
* 오케스트레이션 내에서 엔터티에 신호를 보내고 엔터티를 호출할 수 있습니다.
* 엔터티 내에서 엔터티에 신호를 보낼 수 있습니다.

다음 예제에서는 엔터티에 액세스하는 다양한 방법을 보여 줍니다.

### <a name="example-client-signals-an-entity"></a>예제: 클라이언트에서 엔터티에 신호 보내기

일반 Azure 함수(클라이언트 함수라고도 함)에서 엔터티에 액세스하려면 [엔터티 클라이언트 바인딩](durable-functions-bindings.md#entity-client)을 사용합니다. 다음 예제에서는 이 바인딩을 사용하여 엔터티에 신호를 보내는 큐 트리거 함수를 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> 간단히 하기 위해 다음 예제에서는 엔터티에 액세스하기 위한 느슨하게 형식화된 구문을 보여 줍니다. 일반적으로 더 많은 형식 검사를 제공하므로 [인터페이스를 통해 엔터티에 액세스](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)하는 것이 좋습니다.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

*신호* 라는 용어는 엔터티 API 호출이 단방향이며 비동기적임을 의미합니다. 클라이언트 함수는 엔터티에서 작업을 처리한 시기를 인식할 수 없습니다. 또한 클라이언트 함수는 결과 값 또는 예외를 관찰할 수 없습니다. 

### <a name="example-client-reads-an-entity-state"></a>예제: 클라이언트에서 엔터티 상태 읽기

클라이언트 함수는 다음 예제와 같이 엔터티의 상태를 쿼리할 수도 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func

async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    entity_state_result = await client.read_entity_state(entityId)
    entity_state = "No state found"
    if entity_state_result.entity_exists:
      entity_state = str(entity_state_result.entity_state)
    return func.HttpResponse(entity_state)
```

---

엔터티 상태 쿼리는 지속성 추적 저장소로 보내지고 엔터티의 가장 최근 지속됨 상태를 반환합니다. 이 상태는 항상 "커밋됨” 상태입니다. 즉 작업을 실행하는 중간에 가정된 임시 중간 상태가 아닙니다. 그러나 이 상태는 엔터티의 메모리 내 상태에 비해 오래되었을 수 있습니다. 다음 섹션에서 설명한 대로 오케스트레이션만 엔터티의 메모리 내 상태를 읽을 수 있습니다.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>예제: 오케스트레이션에서 엔터티에 신호 보내기 및 엔터티 호출

오케스트레이터 함수는 [오케스트레이션 트리거 바인딩](durable-functions-bindings.md#orchestration-trigger)에서 API를 사용하여 엔터티에 액세스할 수 있습니다. 다음 예제 코드에서는 `Counter` 엔터티를 호출하고 신호를 보내는 오케스트레이터 함수를 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript는 현재 오케스트레이터에서 엔터티 신호를 지원하지 않습니다. 대신 `callEntity`를 사용하세요.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

오케스트레이션만 엔터티를 호출하고, 반환 값 또는 예외일 수 있는 응답을 받을 수 있습니다. [클라이언트 바인딩](durable-functions-bindings.md#entity-client)을 사용하는 클라이언트 함수는 엔터티에 대한 신호만 보낼 수 있습니다.

> [!NOTE]
> 오케스트레이터 함수에서 엔터티를 호출하는 것은 오케스트레이터 함수에서 [활동 함수](durable-functions-types-features-overview.md#activity-functions)를 호출하는 것과 비슷합니다. 주요 차이점은 엔터티 함수가 엔터티 ID에 해당하는 주소를 포함하는 지속성 개체하는 것입니다. 엔터티 함수는 작업 이름 지정하도록 지원합니다. 반면에 활동 함수는 상태 비저장이며 작업의 개념이 없습니다.

### <a name="example-entity-signals-an-entity"></a>예제: 엔터티에서 엔터티에 신호 보내기

엔터티 함수는 작업을 실행하는 동안 다른 엔터티(또는 자체)에 신호를 보낼 수 있습니다.
예를 들어 카운터 값이 100에 도달하면 "마일스톤에 도달했음” 신호를 일부 모니터 엔터티에 보내도록 이전 `Counter` 엔터티 예제를 수정할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python에서는 엔터티 간 신호를 아직 지원하지 않습니다. 대신, 엔터티 신호 전달에 orchestrator를 사용하세요.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>엔터티 조정(현재 .NET 전용)

여러 엔터티 간에 작업을 조정해야 하는 경우가 있을 수 있습니다. 예를 들어 은행 애플리케이션에서 개별 은행 계좌를 나타내는 엔터티가 있을 수 있습니다. 한 계정에서 다른 계정으로 자금을 이체하는 경우 원본 계정에 충분한 금액이 있는지 확인해야 합니다. 또한 원본 및 대상 계정에 대한 업데이트가 업무상 일관된 방식으로 수행되도록 해야 합니다.

### <a name="example-transfer-funds-c"></a>예제: 자금 이체(C#)

다음 예제 코드에서는 오케스트레이터 함수를 사용하여 두 계정 엔터티 간에 자금을 이체합니다. 엔터티 업데이트를 조정하려면 `LockAsync` 메서드를 사용하여 _임계 영역_ 을 오케스트레이션에 만들어야 합니다.

> [!NOTE]
> 간단히 하기 위해 이 예제에서는 앞에서 정의한 `Counter` 엔터티를 다시 사용합니다. 실제 애플리케이션에서는 더 자세한 `BankAccount` 엔터티를 정의하는 것이 좋습니다.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

.NET에서 `LockAsync`는 삭제될 때 임계 영역을 종료하는 `IDisposable`을 반환합니다. 이 `IDisposable` 결과를 `using` 블록과 함께 사용하여 임계 영역에 대한 구문 표현을 가져올 수 있습니다.

앞의 예제에서 오케스트레이터 함수는 자금을 원본 엔터티에서 대상 엔터티로 이체했습니다. `LockAsync` 메서드는 원본 및 대상 계정 엔터티를 모두 잠궜습니다. 이 잠금을 통해 오케스트레이션 논리에서 `using` 문 끝에 있는 임계 영역을 종료할 때까지 다른 클라이언트에서 두 계정의 상태를 쿼리하거나 수정할 수 없습니다. 이 동작으로 인해 원본 계정에서 초과 인출될 가능성이 효과적으로 차단되었습니다.

> [!NOTE] 
> 오케스트레이션이 종료되면(일반적으로 또는 오류로 인해) 진행 중인 모든 임계 영역이 암시적으로 종료되고 모든 잠금이 해제됩니다.

### <a name="critical-section-behavior"></a>임계 영역 동작

`LockAsync` 메서드는 임계 영역을 오케스트레이션에 만듭니다. 이러한 임계 영역은 다른 오케스트레이션에서 지정된 엔터티 세트를 중복하여 변경하지 못하도록 방지합니다. `LockAsync` API는 내부적으로 "잠금" 작업을 엔터티에 보내고, 이러한 동일한 엔터티 각각으로부터 "잠금 획득" 응답 메시지를 받으면 반환합니다. 잠금 및 잠금 해제는 둘 다 모든 엔터티에서 지원하는 기본 제공 작업입니다.

잠긴 상태에 있는 엔터티에는 다른 클라이언트의 작업이 허용되지 않습니다. 이 동작을 통해 한 번에 하나의 오케스트레이션 인스턴스만 엔터티를 잠글 수 있습니다. 호출자가 오케스트레이션에서 잠그고 있는 엔터티에 대한 작업을 호출하려고 하면 해당 작업이 보류 중인 작업 큐에 배치됩니다. 보류 중인 작업은 보류 중인 오케스트레이션에서 해당 잠금을 해제할 때까지 처리되지 않습니다.

> [!NOTE] 
> 이 동작은 C#의 `lock` 문과 같이 대부분의 프로그래밍 언어에서 사용되는 동기화 기본 형식과는 약간 다릅니다. 예를 들어 C#에서는 `lock` 문을 모든 스레드에서 사용하여 여러 스레드 간에 올바르게 동기화되도록 보장해야 합니다. 그러나 엔터티는 모든 호출자에서 엔터티를 명시적으로 잠그도록 요구하지는 않습니다. 호출자에서 엔터티를 잠그면 해당 엔터티에 대한 다른 모든 작업이 차단되고 해당 잠금 뒤에서 대기하게 됩니다.

엔터티에 대한 잠금은 지속적이므로 실행 프로세스를 재활용하더라도 계속 유지됩니다. 잠금은 엔터티의 지속성 상태 일부로 내부적으로 유지됩니다.

트랜잭션과 달리 임계 영역은 오류 발생 시 변경을 자동으로 롤백하지 않습니다. 대신, 오류 처리(롤백 또는 재시도)를 명시적으로 코딩해야 합니다. 예를 들어 오류 또는 예외를 catch합니다. 이 디자인 선택은 의도적입니다. 오케스트레이션에서 활동을 실행하고 롤백할 수 없는 외부 서비스를 호출할 수 있으므로 오케스트레이션의 모든 결과를 자동으로 롤백하는 것은 일반적으로 어렵거나 불가능합니다. 또한 롤백 시도 자체가 실패할 수 있고 추가적인 오류 처리가 필요합니다.

### <a name="critical-section-rules"></a>임계 영역 규칙

대부분의 프로그래밍 언어에서 낮은 수준의 잠금 기본 형식과 달리, 임계 영역은 *교착 상태가 되지 않도록 보장* 합니다. 교착 상태를 방지하기 위해 다음과 같은 제한 사항이 적용됩니다. 

* 임계 영역은 중첩할 수 없습니다.
* 임계 영역은 하위 오케스트레이션을 만들 수 없습니다.
* 임계 영역은 잠긴 엔터티만 호출할 수 있습니다.
* 임계 영역은 여러 개의 병렬 호출을 사용하여 동일한 엔터티를 호출할 수 없습니다.
* 임계 영역은 잠기지 않은 엔터티에 대한 신호만 보낼 수 있습니다.

이러한 규칙을 위반하면 중단된 규칙을 설명하는 메시지가 포함된 런타임 오류(예: .NET의 `LockingRulesViolationException`)가 발생합니다.

## <a name="comparison-with-virtual-actors"></a>가상 행위자와 비교

대부분의 지속성 엔터티 함수는 [행위자 모델](https://en.wikipedia.org/wiki/Actor_model)로부터 영향을 받습니다. 행위자에 대해 이미 익숙한 경우 이 문서에서 설명하는 여러 가지 개념을 인식할 수 있습니다. 지속성 엔터티는 특히 [Orleans 프로젝트](http://dotnet.github.io/orleans/)에서 일반화한 [가상 행위자](https://research.microsoft.com/projects/orleans/) 또는 그레인과 비슷합니다. 예를 들면 다음과 같습니다.

* 지속성 엔터티는 엔터티 ID를 통해 주소를 지정할 수 있습니다.
* 지속성 엔터티 작업은 경합 상태를 방지하기 위해 한 번에 하나씩 순차적으로 실행됩니다.
* 지속성 엔터티는 호출되거나 신호를 받으면 암시적으로 만들어집니다.
* 작업을 실행하지 않으면 지속성 엔터티가 메모리에서 자동으로 언로드됩니다.

다음과 같은 몇 가지 중요한 차이점이 있습니다.

* 지속성 엔터티는 대기 시간보다 지속성을 우선적으로 처리하므로 엄격한 대기 시간 요구 사항이 있는 애플리케이션에는 적합하지 않을 수 있습니다.
* 지속성 엔터티에는 메시지에 대한 기본 제공 시간 제한이 없습니다. Orleans에서는 모든 메시지가 구성 가능한 시간 후에 시간 초과됩니다. 기본값은 30초입니다.
* 엔터티 간에 보내는 메시지는 순서대로 안정적으로 전달됩니다. Orleans에서는 스트림을 통해 보내는 콘텐츠에 대해 안정적이거나 순차적인 배달을 지원하지만 그레인 간의 모든 메시지에 대해 보장되는 것은 아닙니다.
* 엔터티의 요청-응답 패턴은 오케스트레이션으로 제한됩니다. 원래 행위자 모델과 마찬가지로 엔터티 내에서 단방향 메시징(신호 보내기라고도 함)만 허용되며, Orleans의 그레인과는 다릅니다. 
* 지속성 엔터티는 교착 상태가 아닙니다. Orleans에서는 교착 상태가 발생할 수 있으며, 메시지가 시간 초과될 때까지 해결되지 않습니다.
* 지속성 엔터티는 지속성 오케스트레이션과 함께 사용할 수 있으며 분산 잠금 메커니즘을 지원합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [.NET의 지속성 엔터티에 대한 개발자 가이드 읽기](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [작업 허브에 대해 알아보기](durable-functions-task-hubs.md)
