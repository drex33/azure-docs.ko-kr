---
title: Azure Functions의 Azure Event Grid 트리거
description: Azure Functions에서 Event Grid 이벤트가 디스패치될 때 코드를 실행하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 3786ac149847c61974fb079409d7d18beb16bdd8
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110536797"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Functions의 Azure Event Grid 트리거

함수 트리거를 사용하여 Event Grid 항목으로 전송된 이벤트에 응답합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-event-grid.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

HTTP 트리거 예제는 [HTTP 엔드포인트에서 이벤트 수신](../event-grid/receive-events.md)을 참조하세요.

### <a name="c-2x-and-higher"></a>C#(2.x 이상)

다음 예제에서는 `EventGridEvent`에 바인딩되는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

자세한 내용은 패키지, [특성](#attributes-and-annotations), [구성](#configuration) 및 [사용](#usage)을 참조하세요.

### <a name="version-1x"></a>버전 1.x

다음 예제에서는 `JObject`에 바인딩되는 Functions 1.x [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

### <a name="version-3x-preview"></a>버전 3.x(미리 보기)

다음 예제에서는 `CloudEvent`에 바인딩되는 Functions 3.x [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Azure.Messaging;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class CloudEventTriggerFunction
    {
        [FunctionName("CloudEventTriggerFunction")]
        public static void Run(
            ILogger logger,
            [EventGridTrigger] CloudEvent e)
        {
            logger.LogInformation("Event received {type} {subject}", e.Type, e.Subject);
        }
    }
}
```

다음 예제에서는 `EventGridEvent`에 바인딩되는 Functions 3.x [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Azure.Messaging.EventGrid;
using Microsoft.Extensions.Logging;

namespace Azure.Extensions.WebJobs.Sample
{
    public static class EventGridEventTriggerFunction
    {
        [FunctionName("EventGridEventTriggerFunction")]
        public static void Run(
            ILogger logger,
            [EventGridTrigger] EventGridEvent e)
        {
            logger.LogInformation("Event received {type} {subject}", e.EventType, e.Subject);
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>2\.x 이상 버전

`EventGridEvent`에 바인딩되는 예제는 다음과 같습니다.

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

자세한 내용은 패키지, [특성](#attributes-and-annotations), [구성](#configuration) 및 [사용](#usage)을 참조하세요.

### <a name="version-1x"></a>버전 1.x

`JObject`에 바인딩하는 Functions 1.x C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [Event Grid 트리거, 문자열 매개 변수](#event-grid-trigger-string-parameter)
* [Event Grid 트리거, POJO 매개 변수](#event-grid-trigger-pojo-parameter)

다음 예제에서는 바인딩을 사용하고 이벤트를 출력한 후 처음에는 이벤트를 `String`으로 수신하고, 두 번째로는 POJO로 수신하는 [Java](functions-reference-java.md) 함수의 트리거 바인딩을 보여 줍니다.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid 트리거, 문자열 매개 변수

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid 트리거, POJO 매개 변수

이 예제에서는 다음 POJO를 사용하여 Event Grid 이벤트의 최상위 속성을 나타냅니다.

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

도착 시, 이벤트의 JSON 페이로드가 함수에서 사용될 수 있게 ```EventSchema``` POJO로 역직렬화됩니다. 이 프로세스에서 함수는 개체 지향 방식으로 이벤트의 속성에 액세스할 수 있습니다.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 EventGrid에서 제공되는 매개 변수에 대한 `EventGridTrigger` 주석을 사용합니다. 이러한 주석을 사용하는 매개 변수로 인해 이벤트 도착 시 함수가 실행될 수 있습니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제는 *function.json* 파일의 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 예제에서는 *function.json* 파일에서 Event Grid 트리거 바인딩을 구성하는 방법을 보여 줍니다.

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

다음 PowerShell 예제에 표시된 것처럼 이름이 `eventGridEvent`인 매개 변수를 통해 함수에서 Event Grid 이벤트를 사용할 수 있습니다.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

다음은 Python 코드입니다.

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) 특성을 사용합니다.

다음은 메서드 서명의 `EventGridTrigger` 특성입니다.

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

전체 예제는 C# 예제를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) 주석을 사용하면 구성 값을 제공하여 Event Grid 바인딩을 선언적으로 구성할 수 있습니다. 자세한 내용은 [예제](#example) 및 [구성](#configuration) 섹션을 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. `EventGridTrigger` 특성에서 설정할 생성자 매개 변수 또는 속성은 없습니다.

|function.json 속성 |Description|
|---------|---------|
| **type** | 필수 - `eventGridTrigger`으로 설정해야 합니다. |
| **direction** | 필수 - `in`으로 설정해야 합니다. |
| **name** | 필수 - 이벤트 데이터를 수신하는 매개 변수에 대한 함수 코드에 사용되는 변수 이름입니다. |

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

Azure Functions 1.x의 경우 Event Grid 트리거에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `JObject`
* `string`

Azure Functions 2.x 이상의 경우 Event Grid 트리거에 대해 다음 매개 변수 형식을 사용하는 옵션도 있습니다.

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - 모든 이벤트 유형에 공통되는 필드의 속성을 정의합니다.

> [!NOTE]
> Functions v1에서 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`에 바인딩하려는 경우 컴파일러에서는 "사용되지 않음" 메시지를 표시하고 대신 `Microsoft.Azure.EventGrid.Models.EventGridEvent`를 사용하도록 권고합니다. 최신 형식을 사용하려면 [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet 패키지를 참조하고, `Microsoft.Azure.EventGrid.Models`를 접두사로 사용하여 `EventGridEvent` 형식 이름을 정규화합니다.

### <a name="additional-types"></a>추가 형식 
Event Grid 확장의 버전 3.0.0 이상을 사용하는 앱은 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 네임스페이스의 `EventGridEvent` 유형을 사용합니다. 또한 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 네임스페이스의 `CloudEvent` 유형에 바인딩할 수 있습니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Azure Functions 1.x의 경우 Event Grid 트리거에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `JObject`
* `string`

Azure Functions 2.x 이상의 경우 Event Grid 트리거에 대해 다음 매개 변수 형식을 사용하는 옵션도 있습니다.

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - 모든 이벤트 유형에 공통되는 필드의 속성을 정의합니다.

> [!NOTE]
> Functions v1에서 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`에 바인딩하려는 경우 컴파일러에서는 "사용되지 않음" 메시지를 표시하고 대신 `Microsoft.Azure.EventGrid.Models.EventGridEvent`를 사용하도록 권고합니다. 최신 형식을 사용하려면 [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet 패키지를 참조하고, `Microsoft.Azure.EventGrid.Models`를 접두사로 사용하여 `EventGridEvent` 형식 이름을 정규화합니다. C# 스크립트 함수에서 NuGet 패키지를 참조하는 방법에 대한 정보는 [NuGet 패키지 사용](functions-reference-csharp.md#using-nuget-packages)을 참조하세요.

### <a name="additional-types"></a>추가 형식 
Event Grid 확장의 버전 3.0.0 이상을 사용하는 앱은 [Azure.Messaging.EventGrid](/dotnet/api/azure.messaging.eventgrid.eventgridevent) 네임스페이스의 `EventGridEvent` 유형을 사용합니다. 또한 [Azure.Messaging](/dotnet/api/azure.messaging.cloudevent) 네임스페이스의 `CloudEvent` 유형에 바인딩할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

Event Grid 이벤트 인스턴스는 `EventSchema`로 형식화된 `EventGridTrigger` 특성에 연결된 매개 변수를 통해 사용할 수 있습니다. 자세한 내용은 [예제](#example)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Event Grid 인스턴스는 *function.json* 파일의 `name` 속성에 구성된 매개 변수를 통해 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Event Grid 인스턴스는 *function.json* 파일의 `name` 속성에 구성된 매개 변수를 통해 사용할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

Event Grid 인스턴스는 `func.EventGridEvent`로 형식화된 *function.json* 파일의 `name` 속성에 구성된 매개 변수를 통해 사용할 수 있습니다.

---

## <a name="event-schema"></a>이벤트 스키마

Event Grid 이벤트의 데이터는 HTTP 요청 본문에 JSON 개체로 수신됩니다. JSON은 다음 예제와 유사합니다.

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

표시된 예제는 한 요소의 배열입니다. Event Grid는 항상 배열을 전송하며, 배열에서 둘 이상의 이벤트를 전송할 수 있습니다. 런타임은 각 배열 요소에 대해 한 번씩 함수를 호출합니다.

이벤트 JSON 데이터의 최상위 속성은 모든 이벤트 유형에서 동일하지만, `data` 속성의 내용은 이벤트 유형마다 다릅니다. 표시된 예제는 Blob Storage 이벤트에 대한 것입니다.

공통 및 이벤트별 속성에 대한 설명을 보려면 Event Grid 설명서에서 [이벤트 속성](../event-grid/event-schema.md#event-properties)을 참조하세요.

`EventGridEvent` 형식은 최상위 속성만 정의하며, `Data` 속성은 `JObject`입니다.

## <a name="create-a-subscription"></a>구독 만들기

Event Grid HTTP 요청 수신을 시작하려면 함수를 호출하는 엔드포인트 URL을 지정하는 Event Grid 구독을 만듭니다.

### <a name="azure-portal"></a>Azure portal

Event Grid 트리거를 사용하여 Azure Portal에서 개발하는 함수의 경우 **통합** 을 선택하고, **Event Grid 트리거** 를 차례로 선택한 다음, **Event Grid 구독 만들기** 를 선택합니다.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="포털에서 새 이벤트 구독을 트리거에 연결합니다.":::

이 링크를 선택하면 포털에서 현재 트리거 엔드포인트가 이미 정의된 **이벤트 구독 만들기** 페이지가 열립니다.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="함수 엔드포인트가 이미 정의된 이벤트 구독 만들기" :::

Azure Portal을 사용하여 구독을 만드는 방법에 대한 자세한 내용은 Event Grid 설명서에서 [사용자 지정 이벤트 만들기 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)을 참조하세요.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 구독을 만들려면 [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)를 사용합니다.

이 명령에는 함수를 호출하는 엔드포인트 URL이 필요합니다. 다음 예제에서는 버전별 URL 패턴을 보여줍니다.

#### <a name="version-2x-and-higher-runtime"></a>버전 2.x 이상 런타임

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>버전 1.x 런타임

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

시스템 키는 Event Grid 트리거에 대한 엔드포인트 URL에 포함되어야 하는 인증 키입니다. 다음 섹션에서는 시스템 키를 가져오는 방법을 설명합니다.

다음은 Blob Storage 계정을 구독하는 예제입니다(시스템 키에 대한 자리 표시자 포함).

#### <a name="version-2x-and-higher-runtime"></a>버전 2.x 이상 런타임

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>버전 1.x 런타임

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

구독을 만드는 방법에 대한 자세한 내용은 [Blob Storage 빠른 시작](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) 또는 다른 Event Grid 빠른 시작을 참조하세요.

### <a name="get-the-system-key"></a>시스템 키 가져오기

다음 API(HTTP GET)를 사용하여 시스템 키를 가져올 수 있습니다.

#### <a name="version-2x-and-higher-runtime"></a>버전 2.x 이상 런타임

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>버전 1.x 런타임

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

이것은 관리자 API로, 사용자의 함수 앱 [마스터 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 입력해야 합니다. 시스템 키(Event Grid 트리거 함수 호출용)와 마스터 키(함수 앱의 관리 작업 수행용)를 혼동하지 마세요. Event Grid 토픽을 구독할 때는 시스템 키를 사용해야 합니다.

시스템 키를 제공하는 응답 예제는 다음과 같습니다.

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

포털의 **함수 앱 설정** 탭에서 함수 앱에 대한 마스터 키를 가져올 수 있습니다.

> [!IMPORTANT]
> 마스터 키는 함수 앱에 대한 관리자 액세스를 제공합니다. 이 키를 타사와 공유하거나 네이티브 클라이언트 애플리케이션에 배포하지 마십시오.

자세한 내용은 HTTP 트리거 참조 문서에서 [인증 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

또는 HTTP PUT을 전송하여 키 값을 직접 지정할 수 있습니다.

## <a name="local-testing-with-viewer-web-app"></a>뷰어 웹앱을 사용한 로컬 테스트

Event Grid 트리거를 로컬로 테스트하려면 클라우드의 원본에서 로컬 컴퓨터로 전달된 Event Grid HTTP 요청을 가져와야 합니다. 이 작업을 수행하는 한 가지 방법은 온라인으로 요청을 캡처하고 로컬 컴퓨터에서 수동으로 다시 전송하는 것입니다.

1. 이벤트 메시지를 캡처하는 [뷰어 웹앱을 만듭니다](#create-a-viewer-web-app).
1. 뷰어 앱에 이벤트를 보내는 [Event Grid 구독을 만듭니다](#create-an-event-grid-subscription).
1. [요청을 생성](#generate-a-request)하고 뷰어 앱에서 요청 본문을 복사합니다.
1. Event Grid 트리거 함수의 localhost URL에 [요청을 수동으로 게시](#manually-post-the-request)합니다.

테스트가 완료되면 엔드포인트를 업데이트하여 프로덕션 환경에도 동일한 구독을 사용할 수 있습니다. [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update) Azure CLI 명령을 사용합니다.

### <a name="create-a-viewer-web-app"></a>뷰어 웹앱 만들기

이벤트 메시지 캡처를 간소화하기 위해 이벤트 메시지를 표시하는, [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포할 수 있습니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

**Azure에 배포** 를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

참조하는 사이트에 이벤트가 아직 게시되지 않았습니다.

![새 사이트 보기](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기

테스트할 유형의 Event Grid 구독을 만들고 이벤트 알림의 엔드포인트로 웹앱의 URL을 지정합니다. 웹앱에 대한 엔드포인트는 접미사 `/api/updates/`를 포함해야 합니다. 따라서 전체 URL은 `https://<your-site-name>.azurewebsites.net/api/updates`입니다.

Azure Portal을 사용하여 구독을 만드는 방법에 대한 자세한 내용은 Event Grid 문서에서 [사용자 지정 이벤트 만들기 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)을 참조하세요.

### <a name="generate-a-request"></a>요청 생성

웹앱 엔드포인트에 대한 HTTP 트래픽을 생성하는 이벤트를 트리거합니다.  예를 들어, Blob Storage 구독을 만든 경우 Blob을 업로드하거나 삭제합니다. 웹앱에 요청이 표시되면 요청 본문을 복사합니다.

구독 유효성 검사 요청이 먼저 수신됩니다. 유효성 검사 요청은 모두 무시하고 이벤트 요청을 복사합니다.

![웹앱에서 요청 본문 복사](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>수동으로 요청 게시

Event Grid 함수를 로컬로 실행합니다. `Content-Type` 및 `aeg-event-type` 헤더는 수동으로 설정해야 하며, 다른 모든 값은 기본값으로 남겨둘 수 있습니다.

[Postman](https://www.getpostman.com/) 또는 [curl](https://curl.haxx.se/docs/httpscripting.html)과 같은 도구를 사용하여 HTTP POST 요청을 만듭니다.

* `Content-Type: application/json` 헤더를 설정합니다.
* `aeg-event-type: Notification` 헤더를 설정합니다.
* 요청 본문에 RequestBin 데이터를 붙여 넣습니다.
* Event Grid 트리거 함수의 URL에 게시합니다.
  * 2\.x 이상에서는 다음 패턴을 사용합니다.

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1\.x에서는 다음을 사용합니다.

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName` 매개 변수는 `FunctionName` 특성에 지정된 이름이어야 합니다.

다음 스크린샷에서는 Postman의 헤더 및 요청 본문을 보여 줍니다.

![Postman의 헤더](media/functions-bindings-event-grid/postman2.png)

![Postman의 요청 본문](media/functions-bindings-event-grid/postman.png)

Event Grid 트리거 함수가 실행되고, 다음 예제와 비슷한 결과를 표시합니다.

![샘플 Event Grid 트리거 함수 로그](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>다음 단계

* [Event Grid 이벤트 디스패치](./functions-bindings-event-grid-output.md)
