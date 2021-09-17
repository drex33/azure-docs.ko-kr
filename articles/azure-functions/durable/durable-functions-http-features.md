---
title: Durable Functions의 HTTP 기능 - Azure Functions
description: Azure Functions용 Durable Functions 확장의 통합 HTTP 기능에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 8f661ea1462ad00cdf0ddc5caa802b53d5d8fc20
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480447"
---
# <a name="http-features"></a>HTTP 기능

Durable Functions는 지속성 오케스트레이션 및 엔터티를 HTTP 워크플로에 쉽게 통합할 수 있는 몇 가지 기능을 제공합니다. 이 문서에서는 이러한 기능 중 일부에 대해 자세히 설명합니다.

## <a name="exposing-http-apis"></a>HTTP API 노출

오케스트레이션 및 엔터티는 HTTP 요청을 사용하여 호출하고 관리할 수 있습니다. Durable Functions 확장은 기본 제공 HTTP API를 노출합니다. 또한 HTTP 트리거 함수 내에서 오케스트레이션 및 엔터티와 상호 작용하기 위한 API를 제공합니다.

### <a name="built-in-http-apis"></a>기본 제공 HTTP API

Durable Functions 확장은 Azure Functions 호스트에 HTTP API 세트를 자동으로 추가합니다. 이러한 API를 사용하면 코드를 작성하지 않고도 오케스트레이션 및 엔터티와 상호 작용하고 관리할 수 있습니다.

지원되는 기본 제공 HTTP API는 다음과 같습니다.

* [새 오케스트레이션 시작](durable-functions-http-api.md#start-orchestration)
* [오케스트레이션 인스턴스 쿼리](durable-functions-http-api.md#get-instance-status)
* [오케스트레이션 인스턴스 종류](durable-functions-http-api.md#terminate-instance)
* [오케스트레이션에 외부 이벤트 보내기](durable-functions-http-api.md#raise-event)
* [오케스트레이션 기록 제거](durable-functions-http-api.md#purge-single-instance-history)
* [엔터티에 작업 이벤트 보내기](durable-functions-http-api.md#signal-entity)
* [엔터티 상태 가져오기](durable-functions-http-api.md#get-entity)
* [엔터티 목록 쿼리](durable-functions-http-api.md#list-entities)

Durable Functions 확장에서 노출하는 모든 기본 제공 HTTP API에 대한 자세한 설명은 [HTTP API 문서](durable-functions-http-api.md)를 참조하세요.

### <a name="http-api-url-discovery"></a>HTTP API URL 검색

[오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)은 편리한 HTTP 응답 페이로드를 생성할 수 있는 API를 노출합니다. 예를 들어 특정 오케스트레이션 인스턴스에 대한 관리 API의 링크가 포함된 응답을 만들 수 있습니다. 다음 예제에서는 이 API를 새 오케스트레이션 인스턴스에 사용하는 방법을 보여주는 HTTP 트리거 함수를 보여줍니다.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

**run.ps1**

```powershell
$FunctionName = $Request.Params.FunctionName
$InstanceId = Start-NewOrchestration -FunctionName $FunctionName
Write-Host "Started orchestration with ID = '$InstanceId'"

$Response = New-OrchestrationCheckStatusResponse -Request $Request -InstanceId $InstanceId
Push-OutputBinding -Name Response -Value $Response
```

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "Request",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{FunctionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```
---

앞에서 설명한 HTTP 트리거 함수를 사용하여 오케스트레이터 함수를 시작하려면 HTTP 클라이언트를 사용해야 합니다. 다음 cURL 명령은 `DoWork`라는 오케스트레이터 함수를 시작합니다.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

다음은 ID가 `abc123`인 오케스트레이션의 예제 응답입니다. 명확한 설명을 위해 일부 세부 정보가 제거되었습니다.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

이전 예제에서 `Uri`로 끝나는 각 필드는 기본 제공 HTTP API에 해당합니다. 이러한 API를 사용하여 대상 오케스트레이션 인스턴스를 관리할 수 있습니다.

> [!NOTE]
> webhook URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라집니다. 이전 예제의 호스트는 Azure Functions 2.0이었습니다.

모든 기본 제공 HTTP API에 대한 설명은 [HTTP API 참조](durable-functions-http-api.md)에서 확인할 수 있습니다.

### <a name="async-operation-tracking"></a>비동기 작업 추적

앞에서 언급한 HTTP 응답은 Durable Functions를 사용하여 장기 실행 HTTP 비동기 API를 구현하는 데 도움이 되도록 설계되었습니다. 이 패턴을 *폴링 소비자 패턴* 이라고도 합니다. 클라이언트/서버 흐름은 다음과 같이 작동합니다.

1. 클라이언트에서 장기 실행 프로세스(예: 오케스트레이터 함수)를 시작하기 위해 HTTP 요청을 발급합니다.
1. 대상 HTTP 트리거는 값이 "statusQueryGetUri"인 Location 헤더를 사용하여 HTTP 202 응답을 반환합니다.
1. 클라이언트에서 Location 헤더의 URL을 폴링합니다. Location 헤더가 있는 HTTP 202 응답이 클라이언트에 계속 표시됩니다.
1. 인스턴스가 완료되거나 실패하면 Location 헤더의 엔드포인트에서 HTTP 200을 반환합니다.

이 프로토콜을 사용하면 HTTP 엔드포인트를 폴링하고 Location 헤더를 추적할 수 있는 외부 클라이언트 또는 서비스에서 장기 실행 프로세스를 조정할 수 있습니다. 이 패턴의 클라이언트 및 서버 구현은 모두 Durable Functions HTTP API에 기본 제공됩니다.

> [!NOTE]
> 기본적으로 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)에서 제공하는 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 이 기능을 사용하면 Logic Apps 워크플로의 일부로 장기 실행 지속성 함수를 포함할 수 있습니다. Logic Apps의 비동기 HTTP 패턴 지원에 대한 자세한 내용은 [Azure Logic Apps 워크플로 작업 및 트리거 설명서](../../logic-apps/logic-apps-workflow-actions-triggers.md)에서 찾을 수 있습니다.

> [!NOTE]
> 오케스트레이션과의 상호 작용은 HTTP 트리거 함수뿐 아니라 모든 함수 형식에서 수행할 수 있습니다.

클라이언트 API를 사용하여 오케스트레이션 및 엔터티를 관리하는 방법에 대한 자세한 내용은 [인스턴스 관리 문서](durable-functions-instance-management.md)를 참조하세요.

## <a name="consuming-http-apis"></a>HTTP API 사용

[오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md)에 설명된 대로 오케스트레이터 함수는 I/O를 직접 수행할 수 없습니다. 대신 일반적으로 I/O 작업을 수행하는 [작업 함수](durable-functions-types-features-overview.md#activity-functions)를 호출합니다.

Durable Functions 2.0부터 오케스트레이션은 [오케스트레이션 트리거 바인딩](durable-functions-bindings.md#orchestration-trigger)을 사용하여 HTTP API를 기본적으로 사용할 수 있습니다.

다음 예제 코드는 아웃바운드 HTTP 요청을 만드는 오케스트레이터 함수를 보여줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 기능은 현재 PowerShell에서 지원됩니다.

---

"HTTP 호출" 작업을 사용하면 오케스트레이터 함수에서 다음 작업을 수행할 수 있습니다.

* 오케스트레이션 함수에서 직접 HTTP API를 호출합니다. 여기에는 몇 가지 제한 사항이 있는데, 나중에 설명하겠습니다.
* 클라이언트 쪽 HTTP 202 상태 폴링 패턴을 자동으로 지원합니다.
* [Azure 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 다른 Azure 엔드포인트에 대한 권한 있는 HTTP 호출을 수행합니다.

오케스트레이터 함수에서 직접 HTTP API를 사용하는 기능은 특정한 일반 시나리오에서 편의를 제공하기 위해 고안되었습니다. 작업 함수를 사용하여 이 모든 기능을 직접 구현할 수 있습니다. 대부분의 경우 작업 함수가 더 많은 유연성을 제공할 수 있습니다.

### <a name="http-202-handling"></a>HTTP 202 처리

"HTTP 호출" API는 클라이언트 쪽 폴링 소비자 패턴을 자동으로 구현할 수 있습니다. 호출된 API가 Location 헤더가 있는 HTTP 202 응답을 반환하는 경우 오케스트레이터 함수는 202 이외의 응답을 받을 때까지 Location 리소스를 자동으로 폴링합니다. 이 응답은 오케스트레이터 함수 코드에 반환되는 응답입니다.

> [!NOTE]
> 1. 또한 오케스트레이터 함수는 [비동기 작업 추적](#async-operation-tracking)에 설명된 대로 서버 쪽 폴링 소비자 패턴을 기본적으로 지원합니다. 이 지원은 한 함수 앱의 오케스트레이션이 다른 함수 앱의 오케스트레이터 함수를 쉽게 조정할 수 있다는 뜻입니다. [하위 오케스트레이션](durable-functions-sub-orchestrations.md) 개념과 비슷하지만 앱 간 통신을 지원합니다. 이 지원은 마이크로서비스 스타일 앱 개발에 특히 유용합니다.
> 2. 일시적인 제한으로 인해 기본 제공 HTTP 폴링 패턴은 현재 JavaScript/TypeScript 및 Python에서 사용할 수 없습니다.

### <a name="managed-identities"></a>관리 ID

Durable Functions는 권한 부여를 위해 Azure AD(Azure Active Directory) 토큰을 수락하는 API 호출을 기본적으로 지원합니다. 이 지원은 [Azure 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 이러한 토큰을 획득합니다.

다음 코드는 .NET 오케스트레이터 함수의 예입니다. 이 함수는 Azure Resource Manager [가상 머신 REST API](/rest/api/compute/virtualmachines)를 사용하여 가상 머신을 다시 시작하는 인증된 호출을 만듭니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell) 

이 기능은 현재 PowerShell에서 지원됩니다.

---

이전 예제에서 `tokenSource` 매개 변수는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대한 Azure AD 토큰을 획득하도록 구성됩니다. 토큰은 리소스 URI `https://management.core.windows.net/.default`로 식별됩니다. 이 예제에서는 현재 함수 앱이 로컬로 실행 중이거나 관리 ID를 사용하여 함수 앱으로 배포되었다고 가정합니다. 로컬 ID 또는 관리 ID에는 지정된 리소스 그룹 `myRG`의 VM을 관리할 수 있는 권한이 있다고 가정합니다.

구성된 토큰 원본은 런타임에 OAuth 2.0 액세스 토큰을 자동으로 반환합니다. 그 후 원본은 토큰을 나가는 요청의 권한 부여 헤더에 전달자 토큰으로 추가합니다. 다음과 같은 이유로 HTTP 요청에 권한 부여 헤더를 수동으로 추가하는 모델을 개선한 것이 이 모델입니다.

* 토큰 새로 고침이 자동으로 처리됩니다. 만료된 토큰에 대해 걱정할 필요가 없습니다.
* 토큰이 절대로 지속성 오케스트레이션 상태에 저장되지 않습니다.
* 토큰 획득을 관리하기 위해 코드를 작성할 필요가 없습니다.

[미리 컴파일된 C# RestartVM 샘플](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)에서 보다 완전한 예제를 찾을 수 있습니다.

관리 ID는 Azure 리소스 관리로 제한되지 않습니다. 관리 ID를 사용하여 Microsoft의 Azure 서비스와 파트너의 웹앱을 비롯한 Azure AD 전달자 토큰을 허용하는 모든 API에 액세스할 수 있습니다. 파트너의 웹앱은 다른 함수 앱일 수도 있습니다. Azure AD 인증을 지원하는 Microsoft의 Azure 서비스 목록은 [Azure AD 인증을 지원하는 Azure 서비스](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

### <a name="limitations"></a>제한 사항

HTTP API 호출 기본 지원은 편의를 위한 기능으로 일부 시나리오에는 적합하지 않습니다.

오케스트레이터 함수에서 보내는 HTTP 요청과 해당 응답은 Durable Functions 스토리지 공급자에 메시지로 [직렬화되고 유지](durable-functions-serialization-and-persistence.md)됩니다. 이 지속적인 큐 동작은 HTTP 호출이 [오케스트레이션을 재생하기에 안정적이고 안전하도록](durable-functions-orchestrations.md#reliability) 보장합니다. 그러나 지속적인 큐 동작에는 다음과 같은 제한 사항이 있습니다.

* 각 HTTP 요청은 네이티브 HTTP 클라이언트에 비해 대기 시간이 깁니다.
* [구성된 스토리지 공급자](durable-functions-storage-providers.md)에 따라 큰 요청 또는 응답 메시지가 오케스트레이션 성능을 크게 저하시킬 수 있습니다. 예를 들어 Azure Storage를 사용하는 경우 너무 커서 Azure Queue 메시지에 맞지 않는 HTTP 페이로드는 압축되어 Azure Blob 스토리지에 저장됩니다.
* 스트리밍, 청크 및 이진 페이로드는 지원되지 않습니다.
* HTTP 클라이언트의 동작을 사용자 지정하는 기능은 제한됩니다.

이러한 제한 사항이 사용 사례에 영향을 줄 수 있는 경우 작업 함수 및 언어별 HTTP 클라이언트 라이브러리를 대신 사용하여 아웃바운드 HTTP 호출을 수행하는 것이 좋습니다.

> [!NOTE]
> .NET 개발자인 경우 이 기능이 기본 제공 .NET **HttpRequestMessage** 및 **HttpResponseMessage** 형식 대신 **DurableHttpRequest** 및 **DurableHttpResponse** 형식을 사용하는 이유가 궁금할 수 있습니다.
>
> 이 디자인 선택은 의도적입니다. 주된 이유는 사용자 지정 형식을 사용하면 사용자가 내부 HTTP 클라이언트에서 지원되는 동작에 대해 잘못된 가정을 하지 않기 때문입니다. 또한 Durable Functions 관련 형식을 사용하면 API 디자인을 간소화할 수 있습니다. 뿐만 아니라 [관리 ID 통합](#managed-identities) 및 [폴링 소비자 패턴](#http-202-handling)과 같은 특수 기능을 보다 쉽게 사용할 수 있습니다. 

### <a name="extensibility-net-only"></a>확장성(.NET만 해당)

[Azure Functions .NET 종속성 주입](../functions-dotnet-dependency-injection.md)을 사용하여 오케스트레이션의 내부 HTTP 클라이언트 동작을 사용자 지정할 수 있습니다. 이 기능은 동작을 약간 변경할 때 유용하게 사용할 수 있습니다. 모의 개체를 삽입하여 HTTP 클라이언트를 단위 테스트할 때에도 유용하게 사용할 수 있습니다.

다음 예제에서는 종속성 주입을 사용하여 외부 HTTP 엔드포인트를 호출하는 오케스트레이터 함수에 대해 TLS/SSL 인증서 유효성 검사를 사용하지 않도록 설정하는 방법을 보여줍니다.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 엔터티에 대해 알아보기](durable-functions-entities.md)
