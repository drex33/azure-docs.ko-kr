---
title: 지속성 함수의 인스턴스 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 인스턴스를 관리하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 04c3e9f1a5c5a1a23a618f3274057a5e03a9f0e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567435"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure에서 Durable Functions의 인스턴스 관리

Durable Functions의 오케스트레이션은 기본 제공 관리 API를 사용하여 시작, 쿼리 및 종료할 수 있는 장기 실행 상태 저장 함수입니다. 외부 이벤트를 인스턴스로 보내기, 인스턴스 기록 제거 등과 같은 Durable Functions [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)을 통해 다른 여러 인스턴스 관리 API도 노출됩니다. 이 문서에서는 지원되는 모든 인스턴스 관리 작업에 대해 자세히 설명합니다.

## <a name="start-instances"></a>인스턴스 시작

[오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)의 `StartNewAsync`(.NET), `startNew`(JavaScript) 또는 `start_new`(Python) 메서드는 새 오케스트레이션 인스턴스를 시작합니다. 내부적으로 이 메서드는 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md)를 통해 메시지를 쓴 다음, 반환합니다. 이 메시지는 지정된 이름을 가진 [오케스트레이션 함수](durable-functions-types-features-overview.md#orchestrator-functions)의 시작을 비동기적으로 트리거합니다.

새 오케스트레이션 인스턴스를 시작 하는 데 사용할 수 있는 매개 변수는 다음과 같습니다.

* **Name**: 예약할 오케스트레이터 함수의 이름입니다.
* **Input**: 오케스트레이터 함수에 대한 입력으로 전달해야 하는 JSON 직렬화 가능 데이터입니다.
* **InstanceId**: (선택 사항) 인스턴스의 고유 ID입니다. 이 매개 변수를 지정 하지 않으면 메서드는 임의의 ID를 사용합니다.

> [!TIP]
> 가능하면 인스턴스 ID에 임의의 식별자를 사용하세요. 임의 인스턴스 ID는 여러 VM에서 오케스트레이터 함수를 크기 조정할 때 균등한 부하 분산을 보장하는 데 도움이 됩니다. ID가 외부 원본에서 제공되어야 하거나 [싱글톤 오케스트레이터](durable-functions-singletons.md) 패턴을 구현하는 경우에는 임의가 아닌 인스턴스 ID를 사용하는 것이 좋습니다.

다음 코드는 새 오케스트레이션 인스턴스를 시작하는 예제 함수입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>별도로 지정하지 않는 한 이 페이지의 예제에서는 다음과 같은 function.js와 함께 HTTP 트리거를 사용합니다.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 `durableClient` 대신 `orchestrationClient`를 사용합니다.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>별도로 지정하지 않는 한 이 페이지의 예제에서는 다음과 같은 function.js와 함께 HTTP 트리거를 사용합니다.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 `durableClient` 대신 `orchestrationClient`를 사용합니다.

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

다음 매개 변수를 사용하는 Core Tools의 [`func durable start-new` 명령](../functions-core-tools-reference.md#func-durable-start-new)을 사용하여 인스턴스를 직접 시작할 수도 있습니다.

* **`function-name`(필수)** : 시작할 함수의 이름입니다.
* **`input`(선택 사항)** : 인라인 또는 JSON 파일을 통한 함수 입력입니다. 파일의 경우 `@`를 사용해 파일 경로에 접두사를 추가합니다(예: `@path/to/file.json`).
* **`id`(선택 사항)** : 오케스트레이션 인스턴스의 ID입니다. 이 매개 변수를 지정하지 않으면 명령이 임의 GUID를 사용합니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 AzureWebJobsStorage입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 DurableFunctionsHub입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에서 값을 설정할 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 함수 앱의 루트 디렉터리에서 실행되는 것으로 가정합니다. `connection-string-setting` 및 `task-hub-name` 매개 변수를 명시적으로 제공하면 어느 디렉토리에서든 명령을 실행할 수 있습니다. 함수 앱 호스트를 실행하지 않고 이러한 명령을 실행할 수 있지만 호스트가 실행되고 있지 않으면 일부 효과를 확인할 수 없습니다. 예를 들어 `start-new` 명령은 시작 메시지를 대상 작업 허브의 큐에 넣지만 메시지를 처리할 수 있는 함수 앱 호스트 프로세스가 실행 중이지 않은 경우에는 오케스트레이션이 실제로 실행되지 않습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

다음 명령은 HelloWorld라는 함수를 시작하고 `counter-data.json` 파일의 콘텐츠를 전달합니다.

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>쿼리 인스턴스

새 오케스트레이션 인스턴스를 시작한 후에는 런타임 상태를 쿼리하여 실행 중인지, 완료되었는지 또는 실패했는지를 확인해야 합니다.

[오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)의 `GetStatusAsync`(.net), `getStatus`(JavaScript) 또는 `get_status`(Python) 메서드는 오케스트레이션 인스턴스의 상태를 쿼리합니다.

`instanceId`(필수), `showHistory`(선택 사항), `showHistoryOutput`(선택 사항) 및 `showInput`(선택 사항)을 매개 변수로 사용합니다.

* **`showHistory`** : `true`로 설정하는 경우 응답에는 실행 기록이 포함됩니다.
* **`showHistoryOutput`** : `true`로 설정하는 경우 실행 기록에 작업 출력이 포함됩니다.
* **`showInput`** : `false`로 설정하는 경우 응답에 함수의 입력이 포함됩니다. 기본값은 `true`입니다.

메서드는 다음과 같은 속성이 있는 개체를 반환합니다.

* **Name**: 오케스트레이터 함수의 이름입니다.
* **InstanceId**: 오케스트레이션의 인스턴스 ID입니다(`instanceId` 입력과 동일해야 함).
* **CreatedTime**: 오케스트레이터 함수가 실행되기 시작한 시간입니다.
* **LastUpdatedTime**: 오케스트레이션에서 마지막으로 검사점을 설정한 시간입니다.
* **Input**: JSON 값의 함수 입력입니다. `showInput`이 false인 경우 이 필드는 채워지지 않습니다.
* **CustomStatus**: JSON 형식의 사용자 지정 오케스트레이션 상태입니다.
* **Output**: JSON 값의 함수 출력입니다(함수가 완료된 경우). 오케스트레이터 함수가 실패하면 이 속성에 오류 세부 정보가 포함됩니다. 오케스트레이터 함수가 종료되면 이 속성에 종료 이유가 포함됩니다(있는 경우).
* **RuntimeStatus**: 다음 값 중 하나입니다.
  * **보류 중**: 인스턴스는 일정이 있지만 아직 실행을 시작하지 않았습니다.
  * **실행 중**: 인스턴스가 실행되기 시작했습니다.
  * **완료됨**: 인스턴스가 정상적으로 완료되었습니다.
  * **ContinuedAsNew(새 기록으로 계속됨)**: 인스턴스가 새 기록으로 다시 시작되었습니다. 이 상태는 임시 상태입니다.
  * **실패**: 인스턴스가 오류로 인해 실패했습니다.
  * **종료됨**: 인스턴스가 갑자기 중지되었습니다.
* **기록**: 오케스트레이션의 실행 기록입니다. 이 필드는 `showHistory`를 `true`로 설정한 경우에 채워집니다.

> [!NOTE]
> 오케스트레이터는 예약된 모든 작업이 _완료되고_ 오케스트레이터가 반환될 때까지 `Completed`로 표시되지 않습니다. 즉, 오케스트레이터가 `Completed`로 표시되도록 `return` 문에 도달하는 것으로는 충분하지 않습니다. `WhenAny`가 사용되는 경우에 특히 관련됩니다. 이러한 오케 스트레이터는 예약 된 모든 작업이 실행되기 전에 종종 `return`합니다.

인스턴스가 없으면 이 메서드가 `null`(.NET), `undefined`(JavaScript) 또는 `None`(Python)을 반환합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools의 [`func durable get-runtime-status` 명령](../functions-core-tools-reference.md#func-durable-get-runtime-status)을 사용하여 오케스트레이션 인스턴스의 상태를 직접 가져올 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable get-runtime-status` 명령은 다음 매개 변수를 사용합니다.

* **`id`(필수)** : 오케스트레이션 인스턴스의 ID입니다.
* **`show-input`(선택 사항)** : `true`로 설정하는 경우 응답에 함수의 입력이 포함됩니다. 기본값은 `false`입니다.
* **`show-output`(선택 사항)** : `true`로 설정하는 경우 응답에 함수의 출력이 포함됩니다. 기본값은 `false`입니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에 설정할 수도 있습니다.

다음 명령은 오케스트레이션 인스턴스 ID가 0ab8c55a66644d68a3a8b220b12d209c인 인스턴스의 상태(입력 및 출력 포함)를 검색합니다. 함수 앱의 루트 디렉터리에서 `func` 명령을 실행하는 것으로 가정합니다.

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` 명령을 사용하여 오케스트레이션 인스턴스의 기록을 검색할 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.

* **`id`(필수)** : 오케스트레이션 인스턴스의 ID입니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 host.json에 설정할 수도 있습니다.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>모든 인스턴스 쿼리

[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync)(.NET), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--)(JavaScript) 또는 `get_status_all`(Python) 메서드를 사용하여 [작업 허브](durable-functions-task-hubs.md)에 있는 모든 오케스트레이션 인스턴스의 상태를 쿼리할 수 있습니다. 이 메서드는 쿼리 매개 변수와 일치한 오케스트레이션 인스턴스를 나타내는 개체 목록을 반환합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools에서 [`func durable get-instances` 명령](../functions-core-tools-reference.md#func-durable-get-instances)을 사용하여 인스턴스를 직접 쿼리할 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable get-instances` 명령은 다음 매개 변수를 사용합니다.

* **`top`(선택 사항)** : 이 명령은 페이징을 지원합니다. 이 매개 변수는 요청당 검색된 인스턴스의 수에 해당합니다. 기본값은 10입니다.
* **`continuation-token`(선택 사항)** : 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에 설정할 수도 있습니다.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>필터로 인스턴스 쿼리

표준 인스턴스 쿼리에서 제공할 수 있는 모든 정보가 필요하지 않은 경우에는 어떻게 해야 하나요? 예를 들어 오케스트레이션 생성 시간 또는 오케스트레이션 런타임 상태만 검색하는 경우는 어떻게 해야 하나요? 필터를 적용하여 쿼리 범위를 좁힐 수 있습니다.

[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_)(.net) 또는 [getstatusby](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---)(JavaScript) 메서드를 사용하여 미리 정의된 필터 집합과 일치하는 오케스트레이션 인스턴스 목록을 가져옵니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2021, 3, 10, 10, 1, 0),
        new Date(2021, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2021, 3, 10, 10, 1, 0),
        datetime(2021, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Azure Functions Core Tools에서 `durable get-instances` 명령을 필터와 함께 사용할 수도 있습니다. 앞에서 언급한 `top`, `continuation-token`, `connection-string-setting`, `task-hub-name` 매개 변수와 함께 세 개의 필터 매개 변수(`created-after`, `created-before`, `runtime-status`)를 사용할 수 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

다음은 `durable get-instances` 명령에 대한 매개 변수입니다.

* **`created-after`(선택 사항)** : 이 날짜/시간(UTC) 이후에 생성된 인스턴스를 검색합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`created-before`(선택 사항)** : 이 날짜/시간(UTC) 전에 생성된 인스턴스를 검색합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`runtime-status`(선택 사항)** : 특정 상태(예: 실행 중 또는 완료됨)를 사용하여 인스턴스를 검색합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* **`top`(선택 사항)** : 요청당 검색된 인스턴스 수입니다. 기본값은 10입니다.
* **`continuation-token`(선택 사항)** : 검색할 인스턴스의 페이지 또는 섹션을 나타내는 토큰입니다. `get-instances` 실행할 때마다 다음 인스턴스 집합에 토큰이 반환됩니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에 설정할 수도 있습니다.

필터(`created-after`, `created-before` 또는 `runtime-status`)를 제공하지 않으면 이 명령은 런타임 상태나 생성 시간에 관계없이 `top` 인스턴스를 검색합니다.

```bash
func durable get-instances --created-after 2021-03-10T13:57:31Z --created-before  2021-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>인스턴스 종료

오케스트레이션 인스턴스를 실행하는 데 시간이 너무 오래 걸리거나 어떤 이유로든 완료되기 전에 중지해야 하는 경우에는 종료할 수 있습니다.

[오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)의 `TerminateAsync`(.net), `terminate`(JavaScript) 또는 `terminate`(Python) 메서드를 사용하여 인스턴스를 종료할 수 있습니다. 두 가지 매개 변수는 `instanceId` 및 `reason` 문자열이며 로그 및 인스턴스 상태에 기록됩니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "Found a bug";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Found a bug";
    return client.terminate(instanceId, reason);
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Found a bug"
    return client.terminate(instance_id, reason)
```

---

종료된 인스턴스는 최종적으로는 `Terminated` 상태로 전환됩니다. 그러나 이 전환이 즉시 수행되지는 않습니다. 대신 종료 작업은 해당 인스턴스에 대한 다른 작업과 함께 작업 허브에서 큐에 대기됩니다. [인스턴스 쿼리](#query-instances) API를 사용하면 종료된 인스턴스가 실제로 `Terminated` 상태에 도달한 때를 알 수 있습니다.

> [!NOTE]
> 현재 인스턴스 종료는 전파되지 않습니다. 작업 함수 및 하위 오케스트레이션은 호출된 오케스트레이션 인스턴스가 종료되었는지 여부에 관계없이 완료될 때까지 실행됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools에서 [`func durable terminate` 명령](../functions-core-tools-reference.md#func-durable-terminate)을 사용하여 오케스트레이션 인스턴스를 직접 종료할 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable terminate` 명령은 다음 매개 변수를 사용합니다.

* **`id`(필수)** : 종료할 오케스트레이션 인스턴스의 ID입니다.
* **`reason`(선택 사항)** : 종료 이유입니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에 설정할 수도 있습니다.

다음 명령은 ID가 0ab8c55a66644d68a3a8b220b12d209c인 오케스트레이션 인스턴스를 종료합니다.

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Found a bug"
```

## <a name="send-events-to-instances"></a>인스턴스에 이벤트 보내기

일부 시나리오에서 오케스트레이터 함수는 외부 이벤트를 대기 및 수신 대기해야 합니다. 이것이 유용한 예제 시나리오에는 [모니터링](durable-functions-overview.md#monitoring) 및 [인간 상호 작용](durable-functions-overview.md#human) 시나리오가 포함됩니다.

[오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client)의 `RaiseEventAsync`(.NET), `raiseEvent`(JavaScript) 또는 `raise_event`(Python) 메서드를 사용하여 실행 중인 인스턴스에 이벤트 알림을 보낼 수 있습니다. 이러한 이벤트를 처리할 수 있는 인스턴스는 `WaitForExternalEvent`(.NET) 호출을 기다리거나 `waitForExternalEvent`(JavaScript) 작업 또는 `wait_for_external_event`(Python) 작업을 일시 중단하는 인스턴스입니다.

`RaiseEventAsync`(.Net) 및 `raiseEvent`(JavaScript)에 대한 매개 변수는 다음과 같습니다.

* **InstanceId**: 인스턴스의 고유 ID입니다.
* **EventName**: 보낼 이벤트의 이름입니다.
* **EventData**: 인스턴스에 보낼 JSON 직렬화 가능 페이로드입니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> 지정된 인스턴스 ID가 있는 오케스트레이션 인스턴스가 없으면 이벤트 메시지가 삭제됩니다. 인스턴스가 있지만 이벤트를 아직 기다리고 있지 않은 경우 이벤트는 수신 및 처리할 준비가 될 때까지 인스턴스 상태로 저장됩니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools의 [`func durable raise-event` 명령](../functions-core-tools-reference.md#func-durable-raise-event)을 사용하여 오케스트레이션 인스턴스에 직접 이벤트를 발생시킬 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable raise-event` 명령은 다음 매개 변수를 사용합니다.

* **`id`(필수)** : 오케스트레이션 인스턴스의 ID입니다.
* **`event-name`** : 발생할 이벤트의 이름입니다.
* **`event-data`(선택 사항)** : 오케스트레이션 인스턴스로 전송할 데이터입니다. JSON 파일의 경로일 수도 있고 명령줄에서 직접 데이터를 제공할 수도 있습니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본값은 `DurableFunctionsHub`입니다. durableTask:HubName을 사용하여 [host.json](durable-functions-bindings.md#host-json)에 설정할 수도 있습니다.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>오케스트레이션 완료 대기

장기 실행되는 오케스트레이션에서는 오케스트레이션 결과를 기다려야 할 수 있습니다. 이 경우 오케스트레이션에서 제한 시간을 정의하는 것도 유용합니다. 제한 시간을 초과하면 결과 대신 오케스트레이션 상태가 반환되어야 합니다.

`WaitForCompletionOrCreateCheckStatusResponseAsync`(.NET), `waitForCompletionOrCreateCheckStatusResponse`(JavaScript) 또는 `wait_for_completion_or_create_check_status_response`(Python) 메서드를 사용하여 오케스트레이션 인스턴스에서 실제 출력을 동기적으로 가져올 수 있습니다. 기본적으로 이러한 메서드는 `timeout`에서 10초를 기본값으로 사용하고 `retryInterval`에서 1초를 기본값으로 사용합니다.  

다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

다음 줄을 사용하여 함수를 호출합니다. 제한 시간에 2초를 사용하고 다시 시도 간격으로 0.5초를 사용합니다.

```bash
curl -X POST "http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5"
```

오케스트레이션 인스턴스에 대한 응답을 가져오는 데 필요한 시간에 따라 두 가지 경우가 있습니다.

* 오케스트레이션 인스턴스는 정의된 제한 시간(이 경우에 2초) 내에 완료되며 응답은 동기적으로 배달된 실제 오케스트레이션 인스턴스 출력입니다.

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:14:29 GMT
Transfer-Encoding: chunked

[
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
]
```

* 오케스트레이션 인스턴스는 정의된 제한 시간 내에 완료될 수 없으며 응답은 [HTTP API URL 검색](durable-functions-http-api.md)에 설명된 기본값입니다.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Date: Thu, 14 Dec 2021 06:13:51 GMT
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
Retry-After: 10
Transfer-Encoding: chunked

{
    "id": "d3b72dddefce4e758d92f4d411567177",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
}
```

> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 앞의 예제는 Azure Functions 3.0 호스트에 대한 것입니다.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP 관리 웹후크 URL 검색

외부 시스템을 사용하여 오케스트레이션에 대한 이벤트를 모니터링하거나 발생시킬 수 있습니다. 외부 시스템은 [HTTP API URL 검색](durable-functions-http-features.md#http-api-url-discovery)에 설명된 기본 응답의 일부인 웹후크 URL을 통해 Durable Functions와 통신할 수 있습니다. [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)을 사용하여 프로그래밍 방식으로 웹후크 URL에 액세스할 수도 있습니다. `CreateHttpManagementPayload`(.Net) 또는 `createHttpManagementPayload`(JavaScript) 메서드를 사용하여 이러한 웹후크 URL이 포함된 직렬화 가능 개체를 가져올 수 있습니다.

`CreateHttpManagementPayload`(.Net) 및 `createHttpManagementPayload`(JavaScript) 메서드에는 다음과 같은 매개 변수가 하나 있습니다.

* **instanceId**: 인스턴스의 고유 ID입니다.

메서드는 다음과 같은 문자열 속성을 포함하는 개체를 반환합니다.

* **Id**: 오케스트레이션의 인스턴스 ID입니다(`InstanceId` 입력과 동일해야 함).
* **StatusQueryGetUri**: 오케스트레이션 인스턴스의 상태 URL입니다.
* **SendEventPostUri**: 오케스트레이션 인스턴스의 "이벤트 발생" URL입니다.
* **TerminatePostUri**: 오케스트레이션 인스턴스의 "종료" URL입니다.
* **PurgeHistoryDeleteUri**: 오케스트레이션 인스턴스의 "기록 제거" URL입니다.

다음 예제와 같이 함수는 이러한 개체의 인스턴스를 외부 시스템으로 보내 해당 오케스트레이션에 대한 이벤트를 모니터링하거나 발생시킬 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. 1\.x Durable Functions의 경우 사용자는 `IDurableActivityContext` 대신 `DurableActivityContext`, `DurableClient` 특성 대신 `OrchestrationClient` 특성, `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 관한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>인스턴스 되감기(미리 보기)

예기치 않은 이유로 인해 오케스트레이션 오류가 발생한 경우 해당 목적으로 생성된 API를 사용하여 인스턴스를 이전의 정상 상태로 *되감을* 수 있습니다.

> [!NOTE]
> 이 API로 적절한 오류 처리 및 재시도 정책을 대체할 수 없습니다. 예기치 않은 이유로 오케스트레이션 인스턴스가 실패하는 경우에만 사용할 수 있습니다. 오류 처리 및 재시도 정책에 대한 자세한 내용은 [오류 처리](durable-functions-error-handling.md) 문서를 참조하세요.

[오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)의 `RewindAsync`(.NET) 또는 `rewind`(JavaScript) 메서드를 사용하여 오케스트레이션을 *실행 중* 상태로 되돌릴 수 있습니다. 이 메서드는 오케스트레이션 오류를 유발한 작업 또는 하위 오케스트레이션 실행 실패도 다시 실행합니다.

예를 들어 일련의 [인적 승인](durable-functions-overview.md#human)이 필요한 워크플로가 있다고 가정해 보겠습니다. 누군가에게 승인이 필요하다고 알리며 실시간 응답을 기다리는 일련의 작업 함수가 있다고 가정하겠습니다. 모든 승인 작업이 응답을 수신하거나 시간이 초과된 후에는 애플리케이션의 잘못된 구성(예: 잘못된 데이터베이스 연결 문자열)으로 인해 다른 작업이 실패한다고 가정하겠습니다. 결과적으로 워크플로에 대한 오케스트레이션 실패가 발생합니다. `RewindAsync`(.NET) 또는 `rewind`(JavaScript) API를 사용하면 애플리케이션 관리자가 구성 오류를 수정하고 실패한 오케스트레이션을 실패 직전의 상태로 되감을 수 있습니다. 인간 상호 작용 단계를 다시 승인할 필요가 없기 때문에 오케스트레이션을 성공적으로 완료할 수 있습니다.

> [!NOTE]
> *되감기* 기능은 지속형 타이머를 사용하는 오케스트레이션 인스턴스 되감기를 지원하지 않습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> 현재 이 기능은 Python에서 지원되지 않습니다.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools에서 [`func durable rewind` 명령](../functions-core-tools-reference.md#func-durable-rewind)을 사용하여 오케스트레이션 인스턴스를 직접 되감을 수도 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable rewind` 명령은 다음 매개 변수를 사용합니다.

* **`id`(필수)** : 오케스트레이션 인스턴스의 ID입니다.
* **`reason`(선택 사항)** : 오케스트레이션 인스턴스를 되감는 이유입니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>인스턴스 기록 제거

오케스트레이션과 연관된 모든 데이터를 제거하려면 인스턴스 기록을 제거하면 됩니다. 예를 들어 완료된 인스턴스와 연관된 모든 Azure Table 행 및 큰 메시지 BLOB을 삭제하고자 할 수 있습니다. 이 작업을 수행하려면 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 개체의 `PurgeInstanceHistoryAsync`(.NET), `purgeInstanceHistory`(JavaScript) 또는 `purge_instance_history`(Python) 메서드를 사용합니다.

이 메서드에는 두 가지 오버로드가 있습니다. 첫 번째 오버로드는 오케스트레이션 인스턴스의 ID로 기록을 제거합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

function.json 구성을 위한 [인스턴스 시작](#javascript-function-json)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

다음 예제에서는 지정된 시간 간격 이후에 완료된 모든 오케스트레이션 인스턴스에 대한 기록을 제거하는 타이머 트리거 함수를 보여 줍니다. 이 경우에는 30일 이상 전에 완료된 모든 인스턴스의 데이터를 제거합니다. 이 예제 함수는 오후 12:00 UTC에 하루에 한 번 실행되도록 예약되어 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")] TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy` 메서드를 사용하여 여러 인스턴스에 대한 인스턴스 기록을 조건부로 제거할 수 있습니다.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 이 예제는 Durable Functions 버전 2.x를 대상으로 합니다. 버전 1.x에서는 `durableClient` 대신 `orchestrationClient`를 사용합니다.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> 기록 제거 작업이 성공하려면 대상 인스턴스의 런타임 상태가 **완료**, **종료** 또는 **실패** 여야 합니다.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Core Tools에서 [`func durable purge-history` 명령](../functions-core-tools-reference.md#func-durable-purge-history)을 사용하여 오케스트레이션 인스턴스의 기록을 삭제할 수 있습니다. 이전 섹션의 두 번째 C# 예제와 마찬가지로, 지정된 시간 간격 동안 생성된 모든 오케스트레이션 인스턴스에 대한 기록이 제거됩니다. 제거된 인스턴스를 런타임 상태별로 추가로 필터링할 수 있습니다.

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable purge-history` 명령에는 다음과 같은 여러 매개 변수가 있습니다.

* **`created-after`(선택 사항)** : 이 날짜/시간(UTC) 이후에 생성된 인스턴스의 기록을 제거합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`created-before`(선택 사항)** : 이 날짜/시간(UTC) 전에 생성된 인스턴스의 기록을 제거합니다. ISO 8601 형식으로 된 날짜/시간이 허용됩니다.
* **`runtime-status`(선택 사항)** : 특정 상태(예: 실행 중 또는 완료됨)를 사용하여 인스턴스 기록을 제거합니다. 여러 상태를 공백으로 구분하여 제공할 수 있습니다.
* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

다음 명령은 2021년 11월 14일 오후 7:35(UTC) 전에 작성된 모든 실패한 인스턴스의 기록을 삭제합니다.

```bash
func durable purge-history --created-before 2021-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>작업 허브 삭제

Core Tools의 [`func durable delete-task-hub` 명령](../functions-core-tools-reference.md#func-durable-delete-task-hub)을 사용하여 Azure Storage 테이블, 큐, BLOB을 포함한 특정 작업 허브와 연관된 모든 스토리지 아티팩트를 삭제할 수 있습니다. 

> [!NOTE]
> Core Tools 명령은 현재 런타임 상태를 유지하는 데 기본 [Azure Storage 공급자](durable-functions-storage-providers.md)를 사용하는 경우에만 지원됩니다.

`durable delete-task-hub` 명령에는 다음과 같은 두 개의 매개 변수가 있습니다.

* **`connection-string-setting`(선택 사항)** : 사용할 스토리지 연결 문자열이 포함된 애플리케이션 설정 이름입니다. 기본값은 `AzureWebJobsStorage`입니다.
* **`task-hub-name`(선택 사항)** : 사용할 Durable Functions 작업 허브의 이름입니다. 기본적으로 [host.js](durable-functions-bindings.md#host-json) 파일의 작업 허브 이름이 사용됩니다.

다음 명령은 `UserTest` 작업 허브와 연관된 모든 Azure Storage 데이터를 삭제합니다.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [인스턴스 관리에 대한 기본 제공 HTTP API 참조](durable-functions-http-api.md)
