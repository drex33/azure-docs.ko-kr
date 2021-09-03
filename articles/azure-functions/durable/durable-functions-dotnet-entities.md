---
title: .NET의 지속형 엔터티에 대한 개발자 가이드 - Azure Functions
description: Azure Functions용 Durable Functions 확장을 사용하여 .NET에서 지속형 엔터티로 작업하는 방법입니다.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: azfuncdf
ms.openlocfilehash: 6927c0a276ffa58077ce258dc5fa4ce02eb8f43f
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108048"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET의 지속형 엔터티에 대한 개발자 가이드 읽기

이 문서에서는 예제 및 일반적인 조언을 포함하여 .NET에서 지속형 엔터티를 개발하는 데 사용할 수 있는 인터페이스에 대해 설명합니다. 

엔터티 함수는 서버리스 애플리케이션 개발자에게 애플리케이션 상태를 세분화된 엔터티의 컬렉션으로 구성하는 편리한 방법을 제공합니다. 기본 개념에 대한 자세한 내용은 [지속형 엔터티: 개념](durable-functions-entities.md) 문서를 참조하세요.

현재 엔터티를 정의하기 위해 다음 두 가지 고유한 API를 제공하고 있습니다.

- **클래스 기반 구문** 은 엔터티와 작업을 클래스와 메서드로 나타냅니다. 이 구문은 쉽게 읽을 수 있는 코드를 생성하고 인터페이스를 통해 형식이 검사된 방식으로 작업을 호출할 수 있도록 합니다. 

- **함수 기반 구문** 은 엔터티를 함수로 나타내는 하위 수준 인터페이스입니다. 엔터티 작업 디스패치 방법과 엔터티 상태 관리 방법을 정확하게 제어합니다.  

이 문서에서는 대부분의 애플리케이션에 더 적합한 것으로 생각되는 클래스 기반 구문을 중점적으로 살펴봅니다. 그러나 [함수 기반 구문](#function-based-syntax)은 엔터티 상태 및 작업에 대한 자체 추상화를 정의하거나 관리하려는 애플리케이션에 적합할 수 있습니다. 또한 클래스 기반 구문에서 현재 지원되지 않는 일반성이 필요한 라이브러리를 구현하는 데 적합할 수도 있습니다. 

> [!NOTE]
> 클래스 기반 구문은 함수 기반 구문 위에 있는 계층이므로 두 변형을 모두 동일한 애플리케이션에서 바꿔 사용할 수 있습니다. 
 
## <a name="defining-entity-classes"></a>엔터티 클래스 정의

다음 예제는 정수 형식의 단일 값을 저장하는 `Counter` 엔터티의 구현이며, 네 가지 작업 `Add`, `Reset`, `Get`, `Delete`을 제공합니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run` 함수는 클래스 기반 구문을 사용하는 데 필요한 상용구를 포함합니다. *정적* Azure 함수여야 합니다. 엔터티에 의해 처리되는 각 작업 메시지에 대해 한 번 실행됩니다. `DispatchAsync<T>`가 호출되었는데 엔터티가 아직 메모리에 없는 경우 유형 `T`의 개체를 생성하고 스토리지에 있는 마지막으로 지속된 JSON(있는 경우)에서 필드를 채웁니다. 그런 다음 일치하는 이름을 사용하여 메서드를 호출합니다.

> [!NOTE]
> 클래스 기반 엔터티의 상태는 엔터티가 작업을 처리하기 전에 **암시적으로 생성** 되며 `Entity.Current.DeleteState()`를 호출하여 작업에서 **명시적으로 삭제할** 수 있습니다.

### <a name="class-requirements"></a>클래스 요구 사항
 
엔터티 클래스는 특별한 슈퍼클래스, 인터페이스 또는 특성이 필요하지 않은 POCO(Plain Old CLR Object)입니다. 단,

- 클래스는 생성 가능해야 합니다([엔터티 생성](#entity-construction) 참조).
- 클래스는 JSON 직렬화되어야 합니다([엔터티 직렬화](#entity-serialization) 참조).

또한 작업으로 호출되는 모든 메서드는 추가 요구 사항을 충족해야 합니다.

- 작업에는 인수를 최대 하나만 사용할 수 있으며 오버로드 또는 제네릭 형식 인수가 없어야 합니다.
- 인터페이스를 사용하여 오케스트레이션에서 호출해야 하는 작업은 `Task` 또는 `Task<T>`를 반환해야 합니다.
- 인수 및 반환 값은 직렬화할 수 있는 값 또는 개체여야 합니다.

### <a name="what-can-operations-do"></a>작업은 어떤 기능을 수행하나요?

모든 엔터티 작업은 엔터티 상태를 읽고 업데이트할 수 있으며, 상태 변경 내용은 스토리지에 자동으로 유지됩니다. 또한 작업은 모든 Azure Functions에 공통된 일반 제한 내에서 외부 I/O 또는 기타 계산을 수행할 수 있습니다.

작업은 `Entity.Current` 컨텍스트에서 제공하는 기능에도 액세스할 수 있습니다.

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티의 ID입니다(이름 및 키 포함).
* `SignalEntity`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration`: 새 오케스트레이션을 시작합니다.
* `DeleteState`: 엔터티의 상태를 삭제합니다.

예를 들어 카운터가 100에 도달하여 엔터티 ID를 입력 인수로 전달할 때 오케스트레이션을 시작하도록 카운터 엔터티를 수정할 수 있습니다.

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>엔터티에 직접 액세스

클래스 기반 엔터티에는 엔터티의 명시적 문자열 이름 및 엔터티의 작업을 사용하여 직접 액세스할 수 있습니다. 아래에는 몇 가지 예제가 나와 있습니다. 기본 개념(예: 신호와 호출)에 대한 자세한 설명은 [엔터티에 액세스](durable-functions-entities.md#access-entities)에 대한 설명을 참조하세요. 

> [!NOTE]
> 더 많은 형식 검사를 제공하므로 가능한 경우 [인터페이스를 통해 엔터티에 액세스](#accessing-entities-through-interfaces)하는 것이 좋습니다.

### <a name="example-client-signals-entity"></a>예제: 클라이언트에서 엔터티에 신호 보내기

다음 Azure Http 함수는 REST 규칙을 사용하여 DELETE 작업을 구현합니다. 이 함수는 URL 경로에서 키가 전달되는 카운터 엔터티에 삭제 신호를 보냅니다.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>예제: 클라이언트에서 엔터티 상태 읽기

다음 Azure Http 함수는 REST 규칙을 사용하여 GET 작업을 구현합니다. URL 경로에서 키가 전달되는 카운터 엔터티의 현재 상태를 읽습니다.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> `ReadEntityStateAsync`에서 반환되는 개체는 단지 로컬 복사본, 즉 이전 특정 시점에서 엔터티 상태의 스냅숏일 뿐입니다. 특히 이 개체는 오래된 것일 수 있으며, 이 개체를 수정해도 실제 엔터티에는 영향을 주지 않습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>예: 오케스트레이션이 먼저 신호를 전달하고 엔터티 호출

다음 오케스트레이션은 카운터 엔터티에 값을 증분하라는 신호를 보낸 다음 최신 값을 읽도록 동일한 엔터티를 호출합니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>인터페이스를 통해 엔터티에 액세스

인터페이스는 생성된 프록시 개체를 통해 엔터티에 액세스하는 데 사용할 수 있습니다. 이 접근 방식을 사용하면 작업의 이름과 인수 형식이 구현된 항목과 일치합니다. 가능한 경우 엔터티에 액세스할 때 늘 인터페이스를 사용하는 것이 좋습니다.

예를 들어 카운터 예제를 다음과 같이 수정할 수 있습니다.

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

엔터티 클래스와 엔터티 인터페이스는 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)에 의해 널리 알려진 그레인(grain) 및 그레인 인터페이스와 유사합니다. 지속형 엔터티와 Orleans 간의 유사점 및 차이점에 대한 자세한 내용은 [Virtual Actor와 비교](durable-functions-entities.md#comparison-with-virtual-actors)를 참조하세요.

인터페이스는 형식 검사를 제공하는 것 외에도 애플리케이션 내에서 문제를 더 잘 분리하는 데 유용합니다. 예를 들어 엔터티가 여러 인터페이스를 구현할 수 있음으로 단일 엔터티가 여러 역할을 수행할 수 있습니다. 또한 인터페이스는 여러 엔터티로 구현할 수 있음으로 일반 통신 패턴을 재사용 가능한 라이브러리로 구현할 수 있습니다.

### <a name="example-client-signals-entity-through-interface"></a>예: 클라이언트에서 인터페이스를 통해 엔터티에 신호 보내기

클라이언트 코드는 `SignalEntityAsync<TEntityInterface>`를 사용하여 `TEntityInterface`를 구현하는 엔터티에 신호를 보낼 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

이 예제에서 `proxy` 매개 변수는 `ICounter`의 내부적으로 생성된 인스턴스로, 해당 호출을 `Delete` 신호로 내부적으로 변환합니다.

> [!NOTE]
> `SignalEntityAsync` API는 단방향 작업에만 사용할 수 있습니다. 작업이 `Task<T>`를 반환하는 경우에도 `T` 매개 변수의 값은 항상 null 이거나 `default`이지 실제 결과가 아닙니다.
예를 들어 값이 반환되지 않음으로 `Get` 작업에 신호를 보내는 것은 적절하지 않습니다. 대신, 클라이언트는 `ReadStateAsync`를 사용하여 카운터 상태에 직접 액세스하거나 `Get` 작업을 호출하는 오케스트레이터 함수를 시작할 수 있습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>예: 오케스트레이션이 엔터티에 먼저 신호를 전달하고 프록시를 통해 해당 엔터티 호출

오케스트레이션 내에서 엔터티를 호출하거나 엔터티에 신호를 보내기 위해 인터페이스 형식과 함께 `CreateEntityProxy`를 사용하여 엔터티에 대한 프록시를 생성할 수 있습니다. 그런 다음 생성한 프록시를 사용하여 작업을 호출하거나 작업에 신호를 보낼 수 있습니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

`void`를 반환하는 모든 작업에는 암시적으로 신호가 전달되고 `Task` 또는 `Task<T>`를 반환하는 모든 작업은 호출됩니다. 이 기본 동작은 변경할 수 있으며, 작업이 Task를 반환하더라도 `SignalEntity<IInterfaceType>` 메서드를 명시적으로 사용하여 작업에 신호를 보낼 수 있습니다.

### <a name="shorter-option-for-specifying-the-target"></a>대상을 지정하기 위한 짧은 옵션

인터페이스를 사용하여 엔터티를 호출하거나 엔터티에 신호를 보내는 경우 첫 번째 인수가 대상 엔터티를 지정해야 합니다. 엔터티 ID를 지정하여 대상을 지정하거나, 엔터티를 구현하는 클래스가 한 개만 있는 경우에는 엔터티 키만 지정할 수 있습니다.

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

엔터티 키만 지정하고 런타임에 고유한 구현을 찾을 수 없으면 `InvalidOperationException`이 throw됩니다. 

### <a name="restrictions-on-entity-interfaces"></a>엔터티 인터페이스에 대한 제한 사항

일반적으로 모든 매개 변수 및 반환 형식은 JSON 직렬화되어야 합니다. 그러지 않으면 런타임에 직렬화 예외가 throw됩니다.

또한 다음과 같은 몇 가지 추가 규칙이 적용됩니다.
* 엔터티 인터페이스는 엔터티 클래스와 동일한 어셈블리에 정의되어야 합니다.
* 엔터티 인터페이스는 메서드만 정의해야 합니다.
* 엔터티 인터페이스에 일반 매개 변수를 포함하면 안 됩니다.
* 엔터티 인터페이스 메서드에는 매개 변수가 둘 이상 있으면 안 됩니다.
* 엔터티 인터페이스 메서드는 `void`, `Task` 또는 `Task<T>`를 반환해야 합니다.

인터페이스가 `SignalEntity` 또는 `CreateProxy`에 대한 형식 인수로 사용될 때 이러한 규칙 중 하나라도 위반하면 런타임에 `InvalidOperationException`이 throw됩니다. 예외 메시지는 어떤 규칙이 위반되었는지 설명합니다.

> [!NOTE]
> `void`를 반환하는 인터페이스 메서드는 (단방향으로) 신호만 받을 수 있지 (양방향으로) 호출될 수는 없습니다. `Task` 또는 `Task<T>`를 반환하는 인터페이스 메서드는 호출되거나 신호를 받을 수 있습니다. 호출된 경우 작업의 결과를 반환하거나 작업에서 throw된 예외를 다시 throw합니다. 그러나 신호를 보낸 경우 작업에서 실제 결과 또는 예외를 반환하지 않고 기본값만 반환합니다.

## <a name="entity-serialization"></a>엔터티 직렬화

엔터티 상태는 지속되므로 엔터티 클래스를 직렬화해야 합니다. Durable Functions 런타임은 이 용도에 직렬화와 역직렬화 프로세스를 제어하는 다양한 정책 및 특성을 지원하는 [Json.NET](https://www.newtonsoft.com/json) 라이브러리를 사용합니다. 가장 일반적으로 사용되는 C# 데이터 형식(배열 및 컬렉션 형식 포함)은 이미 직렬화 가능하며 지속형 엔터티의 상태를 정의하는 데 쉽게 사용할 수 있습니다.

예를 들어, Json.NET은 다음 클래스를 쉽게 직렬화 및 역직렬화할 수 있습니다.

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>직렬화 특성

위의 예제에서는 기본 직렬화를 더 자세히 볼 수 있도록 여러 특성을 포함하도록 선택했습니다.
- 클래스를 직렬화해야 함을 알리고, JSON 속성으로 명시적으로 표시된 멤버만 지속하도록 `[JsonObject(MemberSerialization.OptIn)]`를 사용하여 클래스에 주석을 추가했습니다.
-  필드가 지속형 엔터티 상태의 일부임을 알리고 JSON 표현에 사용할 속성 이름을 지정하기 위해 `[JsonProperty("name")]`로 지속된다고 필드에 주석을 추가했습니다.

그러나 이러한 특성은 필요하지 않습니다. 다른 규칙 또는 특성은 Json.NET에서 작동하는 한 허용됩니다. 예를 들어 `[DataContract]` 특성을 사용하거나 특성을 전혀 사용하지 않을 수 있습니다.

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

기본적으로 클래스의 이름은 JSON 표현의 일부로 저장되지 *않습니다*. 즉, `TypeNameHandling.None`을 기본 설정으로 사용합니다. 이 기본 동작은 `JsonObject` 또는 `JsonProperty` 특성을 사용하여 재정의할 수 있습니다.

### <a name="making-changes-to-class-definitions"></a>클래스 정의 변경

저장된 JSON 개체가 새 클래스 정의와 더 이상 일치하지 않을 수 있기 때문에 애플리케이션을 실행한 후 클래스 정의를 변경하는 경우에는 주의해야 합니다. 그러나 `JsonConvert.PopulateObject`에서 사용하는 역직렬화 프로세스를 이해하는 한 데이터 형식을 변경하는 것이 적절한 경우가 종종 있습니다.

예를 들어 다음과 같은 몇 가지 변경의 예와 그 영향을 확인할 수 있습니다.

1. 저장된 JSON에 없는 새 속성을 추가하는 경우 기본값을 가정합니다.
1. 저장된 JSON에 있는 속성이 제거되면 이전 내용이 손실됩니다.
1. 속성의 이름을 바꾸면 마치 기존 속성을 제거하고 새 속성을 추가한 것과 같습니다.
1. 속성의 형식이 변경되어 저장된 JSON에서 더 이상 역직렬화할 수 없는 경우 예외가 throw됩니다.
1. 속성의 형식이 변경되었지만 저장된 JSON에서 계속 역직렬화할 수 있는 경우에도 예외가 throw됩니다.

Json.NET의 동작을 사용자 지정하는 데 사용할 수 있는 많은 옵션이 있습니다. 예를 들어 저장된 JSON에 클래스에 없는 필드가 포함된 경우 예외를 강제로 적용하려면 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` 특성을 지정합니다. 임의의 형식으로 저장된 JSON을 읽을 수 있는, 역직렬화를 위한 사용자 지정 코드를 작성할 수도 있습니다.

## <a name="entity-construction"></a>엔터티 생성

경우에 따라 엔터티 개체가 생성되는 방식을 보다 효과적으로 제어할 수 있습니다. 이제 엔터티 개체를 생성할 때 기본 동작을 변경하기 위한 몇 가지 옵션을 설명합니다. 

### <a name="custom-initialization-on-first-access"></a>처음 액세스할 때 사용자 지정 초기화

액세스한 적 없거나 삭제된 엔터티에 작업을 디스패치하기 전에 몇 가지 특수한 초기화를 수행해야 하는 경우도 있습니다. 이 동작을 지정하기 위해 `DispatchAsync`에 앞서 조건을 추가할 수 있습니다.

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>엔터티 클래스의 바인딩

일반 함수와 달리 엔터티 클래스 메서드는 입력 및 출력 바인딩에 직접 액세스할 수 없습니다. 대신, 진입점 함수 선언에서 바인딩 데이터를 캡처한 다음, `DispatchAsync<T>` 메서드에 전달해야 합니다. `DispatchAsync<T>`에 전달된 모든 개체는 자동으로 엔터티 클래스 생성자에 인수로 전달됩니다.

다음 예제에서는 [Blob 입력 바인딩](../functions-bindings-storage-blob-input.md)의 `CloudBlobContainer` 참조를 클래스 기반 엔터티에 사용할 수 있게 하는 방법을 보여 줍니다.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Azure Functions의 바인딩에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩](../functions-triggers-bindings.md) 설명서를 참조하세요.

### <a name="dependency-injection-in-entity-classes"></a>엔터티 클래스의 종속성 주입

엔터티 클래스는 [Azure Functions 종속성 주입](../functions-dotnet-dependency-injection.md)을 지원합니다. 다음 예제에서는 `IHttpClientFactory` 서비스를 클래스 기반 엔터티에 등록하는 방법을 보여 줍니다.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

다음 코드 조각에서는 주입된 서비스를 엔터티 클래스에 통합하는 방법을 보여 줍니다.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 직렬화 관련 문제를 방지하려면 직렬화에서 삽입된 값을 저장하는 필드를 제외해야 합니다.

> [!NOTE]
> 일반 .NET Azure Functions에서 생성자 주입을 사용하는 경우와 달리 클래스 기반 엔터티에 대한 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 함수 진입점을 선언하면 일반 Azure Functions 개체 이니셜라이저와 지속성 엔터티 개체 이니셜라이저 간에 충돌이 발생할 수 있습니다.

## <a name="function-based-syntax"></a>함수 기반 구문

지금까지 대부분의 애플리케이션에 더 적합한 것으로 생각되는 클래스 기반 구문을 중점적으로 살펴봤습니다. 그러나 함수 기반 구문은 엔터티 상태 및 작업에 대한 자체 추상화를 정의하거나 관리하려는 애플리케이션에 적합할 수 있습니다. 또한 클래스 기반 구문에서 현재 지원되지 않는 일반성이 필요한 라이브러리를 구현하는 데 적합할 수도 있습니다. 

함수 기반 구문을 사용하여 엔터티 함수는 작업 디스패치를 명시적으로 처리하고 엔터티의 상태를 명시적으로 관리합니다. 예를 들어 다음 코드에서는 함수 기반 구문을 사용하여 구현된 *카운터* 엔터티를 보여 줍니다.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>엔터티 컨텍스트 개체

`IDurableEntityContext` 형식의 컨텍스트 개체를 통해 엔터티 관련 기능에 액세스할 수 있습니다. 이 컨텍스트 개체는 비동기 로컬 속성인 `Entity.Current`를 통해 엔터티 함수에 대한 매개 변수로 사용할 수 있습니다.

다음 멤버는 현재 작업에 대한 정보를 제공하고 반환 값을 지정할 수 있도록 합니다. 

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티의 ID입니다(이름 및 키 포함).
* `OperationName`: 현재 작업의 이름입니다.
* `GetInput<TInput>()`: 현재 작업에 대한 입력을 가져옵니다.
* `Return(arg)`: 작업을 호출한 오케스트레이션에 값을 반환합니다.

다음 멤버는 엔터티(만들기, 읽기, 업데이트, 삭제)의 상태를 관리합니다. 

* `HasState`: 엔터티가 존재하는지 여부 즉, 엔터티에 상태가 있는지를 나타냅니다. 
* `GetState<TState>()`: 엔터티의 현재 상태를 가져옵니다. 엔터티가 아직 없는 경우 자동으로 생성됩니다.
* `SetState(arg)`: 엔터티의 상태를 만들거나 업데이트합니다.
* `DeleteState()`: 있는 경우 엔터티의 상태를 삭제합니다. 

`GetState`에서 반환하는 상태가 개체이면 애플리케이션 코드로 직접 수정할 수 있습니다. 종료 시에는 `SetState`를 다시 호출하지 않아도 됩니다. `GetState<TState>`가 여러 번 호출된 경우 동일한 형식을 사용해야 합니다.

마지막으로, 다음 멤버를 사용하여 다른 엔터티에 신호를 보내거나 새 오케스트레이션을 시작합니다.

* `SignalEntity(EntityId, operation, input)`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: 새 오케스트레이션을 시작합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 개념 알아보기](durable-functions-entities.md)
