---
title: 지속성 함수의 단일 항목 - Azure
description: Azure Functions의 Durable Functions 확장에서 싱글톤을 사용하는 방법을 설명합니다.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 82b3db8d65093f6cbb4d4a3013c396e5b6caf795
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038157"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>지속성 함수의 단일 항목 오케스트레이터(Azure Functions)

백그라운드 작업의 경우 한 번에 하나의 특정 오케스트레이터 인스턴스만 실행되도록 해야 합니다. 오케스트레이터를 만들 때 특정 인스턴스 ID를 할당하여 [Durable Functions](durable-functions-overview.md)에서 이 종류의 싱글톤 동작을 보장할 수 있습니다.

## <a name="singleton-example"></a>단일 항목 예제

다음 예제에서는 싱글톤 백그라운드 작업 오케스트레이션을 만드는 HTTP 트리거 함수를 보여 줍니다. 이 코드는 지정한 인스턴스 ID에 대해 인스턴스가 하나만 존재하는지 확인합니다.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance.runtime_status in [df.OrchestrationRuntimeStatus.Completed, df.OrchestrationRuntimeStatus.Failed, df.OrchestrationRuntimeStatus.Terminated, None]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${existing_instance.instance_id}' already exists"
        }

```

---

기본적으로 인스턴스 ID는 임의로 GUID에서 생성됩니다. 그러나 이전 예제에서는 인스턴스 ID가 URL의 경로 데이터에 전달됩니다. 이 코드는 `GetStatusAsync`(C#), `getStatus`(JavaScript) 또는 `get_status`(Python)를 호출하여 지정된 ID를 가진 인스턴스가 이미 실행되고 있는지 확인합니다. 해당 인스턴스가 실행되고 있지 않으면 이 ID를 사용하여 새 인스턴스가 생성됩니다.

> [!NOTE]
> 이 샘플에는 잠재적 경합 상태가 있습니다. **HttpStartSingle** 의 두 인스턴스가 동시에 실행되면 두 함수의 호출 모두에서 성공을 보고하지만 실제로는 하나의 오케스트레이션 인스턴스만 시작됩니다. 요구 사항에 따라 바람직하지 않은 부작용이 있을 수 있습니다. 이러한 이유로 두 요청이 이 트리거 함수를 동시에 실행할 수 없도록 하는 것이 중요합니다.

오케스트레이터 함수의 구현 세부 정보는 실제로 중요하지 않습니다. 시작하고 완료하는 일반 오케스트레이터 함수일 수 있거나 무기한 실행하는 오케스트레이터 함수일 수 있습니다(즉, [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)). 중요한 점은 한 번에 하나의 인스턴스만 실행된다는 점입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오케스트레이션의 네이티브 HTTP 기능에 대한 자세한 정보](durable-functions-http-features.md)
