---
title: 참조 - Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩
description: 이 참조서에서는 Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 40904b087d58ee6a07ca7acecdd2b2927348799e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493251"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩

이 참조에서는 Azure Functions에서 Web PubSub 이벤트를 처리하는 방법을 설명합니다.

Web PubSub는 개발자가 실시간 기능 및 게시-구독 패턴을 사용하여 웹 애플리케이션을 쉽게 빌드할 수 있도록 도와주는 Azure 관리형 서비스입니다.

| 작업 | 형식 |
|---------|---------|
| 서비스에서 메시지가 제공되는 경우 함수 실행 | [트리거 바인딩](#trigger-binding) |
| 협상 및 업스트림 요청에 대한 Http 트리거에서 대상 개체에 요청 바인딩 | [입력 바인딩](#input-binding)
| 서비스 수행 작업 호출 | [출력 바인딩](#output-binding) |

[소스 코드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[패키지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[API 참조 설명서](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[제품 설명서](./index.yml) |
[샘플][samples_ref]

## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지], 시험판 버전 설치 | |
| C# 스크립트, JavaScript, Python, PowerShell       | [명시적으로 확장 설치], [확장 번들 사용] | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                                   | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

> [!NOTE]
> 지정된 패키지 및 버전을 사용하여 [NuGet](https://www.nuget.org/)에서 클라이언트 라이브러리를 설치합니다.
> 
> ```bash
> func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
> ```

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[확장 번들 사용]: ../azure-functions/functions-bindings-register.md#extension-bundles
[명시적으로 확장 설치]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[확장 업데이트]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>주요 개념

![함수 앱으로 작업하는 Azure Web PubSub 서비스의 워크플로를 보여 주는 다이어그램](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) 클라이언트 연결을 생성하기 위한 HttpTrigger를 사용한 `WebPubSubConnection` 입력 바인딩입니다.

(3)-(4) 서비스 요청을 처리하기 위한 HttpTrigger를 사용한 `WebPubSubTrigger` 트리거 바인딩 또는 `WebPubSubContext` 입력 바인딩입니다.

(5)-(6) 서비스에 작업을 요청하기 위한 `WebPubSub` 출력 바인딩입니다.

## <a name="trigger-binding"></a>트리거 바인딩

함수 트리거를 사용하여 Azure Web PubSub 서비스의 요청을 처리합니다. 

`WebPubSubTrigger`는 서비스 쪽에서 요청을 처리해야 할 때 사용됩니다. 트리거 엔드포인트 패턴은 아래와 같으며 Web PubSub 서비스 쪽에서 설정해야 합니다(Portal: 설정 -> 이벤트 처리기 -> URL 템플릿). 엔드포인트 패턴에서 쿼리 부분 `code=<API_KEY>`는 [보안](../azure-functions/security-concepts.md#system-key)상의 이유로 Azure 함수 앱을 사용할 때 **필수** 입니다. 키는 **Azure Portal** 에서 찾을 수 있습니다. 함수 앱 리소스를 찾고 **함수**  ->  **앱을**  ->  Azure에 배포한 후 webpubsub_extension 함수 앱 키 시스템 **키로**  ->   이동합니다. 그러나 로컬 함수를 사용할 때는 이 키가 필요하지 않습니다.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="함수 시스템 키 가져오기의 스크린샷":::

### <a name="example"></a>예제


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    WebPubSubConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message data: {data}");
    Console.WriteLine($"Request message dataType: {dataType}");
}
```

`WebPubSubTrigger` 또한 바인딩은 `Connect` 서버가 클라이언트 요청을 확인 및 거부하거나 호출자에게 직접 메시지를 보낼 수 있는 경우 시스템 및 사용자 이벤트와 같은 동기화 시나리오에서 반환 값을 지원합니다. `Connect` 이벤트는 및 를 `ConnectEventResponse` `EventErrorResponse` 준수하고, 사용자 이벤트는 `UserEventResponse` 및 를 `EventErrorResponse` 준수하며, 현재 시나리오와 일치하지 않는 rest 형식은 무시됩니다. 또한 `EventErrorResponse`가 반환되면 서비스에서 클라이언트 연결을 삭제합니다.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    ConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    return new UserEventResponse
    {
        Data = BinaryData.FromString("ack"),
        DataType = WebPubSubDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json`에서 트리거 바인딩을 정의합니다.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "data",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = function (context, data) {
  console.log('Request from: ', context.bindingData.request.connectionContext.userId);
  console.log('Request message data: ', data);
  console.log('Request message dataType: ', context.bindingData.request.dataType);
}
```

`WebPubSubTrigger` 또한 바인딩은 `Connect` 서버가 클라이언트 요청을 확인 및 거부하거나 요청 클라이언트에 직접 메시지를 보낼 수 있는 경우 시스템 및 사용자 이벤트와 같은 동기화 시나리오에서 반환 값을 지원합니다. JavaScript 약한 형식의 언어에서는 개체 키와 관련하여 deserialized됩니다. 그리고 `EventErrorResponse`는 rest 개체에 비해 우선 순위가 가장 높으며 `code`가 반환 결과에 있는 경우 `EventErrorResponse`로 구문 분석되고 클라이언트 연결이 삭제됩니다.

```js
module.exports = async function (context) {
  return { 
    "data": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>특성 및 주석

[C# 클래스 라이브러리](../azure-functions/functions-dotnet-class-library.md)에서 `WebPubSubTrigger` 특성을 사용합니다.

다음은 메서드 서명의 `WebPubSubTrigger` 특성입니다.

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<event-Name>", <WebPubSubEventType>)] 
WebPubSubConnectionContext context, ILogger log)
{
    ...
}
```

전체 예제는 C# 예제를 참조하세요.

### <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 |필수 - `webPubSubTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음 | 필수 - `in`으로 설정해야 합니다. |
| **name** | 해당 없음 | 필수 - 이벤트 데이터를 수신하는 매개 변수에 대한 함수 코드에 사용되는 변수 이름입니다. |
| **hub** | 허브 | 필수 - 함수가 트리거되려면 이 값을 반드시 Web PubSub 허브 이름으로 설정해야 합니다. 특성의 값을 더 높은 우선 순위로 설정하거나 앱 설정에서 전역 값으로 설정할 수 있습니다. |
| **eventType** | EventType | 필수 - 함수가 트리거되려면 이 값을 반드시 메시지의 이벤트 유형으로 설정해야 합니다. 값은 `user` 또는 `system`이어야 합니다. |
| **eventName** | EventName | 필수 - 함수가 트리거되려면 이 값을 반드시 메시지의 이벤트로 설정해야 합니다. </br> `system` 이벤트 유형의 경우 이벤트 이름은 `connect`, `connected` 또는 `disconnected`에 있어야 합니다. </br> 사용자 정의 하위 프로토콜의 경우 이벤트 이름은 `message`입니다. </br> 시스템 지원 하위 프로토콜 `json.webpubsub.azure.v1.`의 경우 이벤트 이름은 사용자 정의 이벤트 이름입니다. |
| **connection** | 연결 | 선택 사항 - 업스트림 Azure Web PubSub 서비스를 지정하는 앱 설정 또는 설정 컬렉션의 이름입니다. 값은 서명 유효성 검사에 사용됩니다. 그리고 기본적으로 앱 설정 "WebPubSubConnectionString"으로 값이 자동으로 확인됩니다. 또한 `null` 유효성 검사가 필요하지 않으며 항상 성공한다는 의미입니다. |

### <a name="usages"></a>사용

C#에서 `WebPubSubEventRequest`는 형식 인식 바인딩 매개 변수이고, rest 매개 변수는 매개 변수 이름으로 바인딩됩니다. 아래 표에서 사용 가능한 매개 변수 및 형식을 확인합니다.

JavaScript와 같은 약한 형식의 언어에서 `name` 의는 `function.json` 아래 매핑 테이블과 관련 된 트리거 개체를 바인딩하는 데 사용 됩니다. 또한 `name`이 트리거 입력에 대한 바인딩 개체로서 `data`로 설정되면 그에 따라 `function.json`에서 `dataType`을 준수하여 메시지를 변환합니다. 모든 매개 변수는 `context.bindingData.<BindingName>`에서 읽을 수 있으며 `JObject`가 변환됩니다. 

| 바인딩 이름 | 바인딩 유형 | 설명 | 속성 |
|---------|---------|---------|---------|
|request|`WebPubSubEventRequest`|업스트림 요청을 설명 합니다.|속성이 파생 클래스, 및를 비롯 한 다양 한 이벤트 형식에 따라 다릅니다. `ConnectEventRequest` `ConnectedEventRequest` `UserEventRequest``DisconnectedEventRequest` |
|connectionContext|`WebPubSubConnectionContext`|일반 요청 정보| EventType, EventName, Hub, ConnectionId, UserId, 헤더, 원본, 서명, 상태 |
|데이터|`BinaryData`,`string`,`Stream`,`byte[]`| 사용자 이벤트에서 클라이언트의 메시지 데이터 요청 `message` | -|
|dataType|`WebPubSubDataType`| 요청 메시지 dataType은 `binary`, `text`, `json`을 지원합니다. | -|
|claims|`IDictionary<string, string[]>`|시스템 요청의 사용자 클레임 `connect` | -|
|Query|`IDictionary<string, string[]>`|시스템 요청의 사용자 쿼리 `connect` | -|
|subprotocols|`IList<string>`|시스템 요청에서 사용 가능한 하위 프로토콜 `connect` | -|
|clientCertificates|`IList<ClientCertificate>`|시스템 요청에서 클라이언트의 인증서 지문 목록 `connect`|-|
|reason|`string`|시스템 요청에 대 한 이유 `disconnected`|-|

### <a name="return-response"></a> 반환 응답

`WebPubSubTrigger` 는의 동기 이벤트와 사용자 이벤트에 대해 고객에 게 반환 된 응답을 준수 합니다 `connect` . 일치하는 응답만 서비스로 다시 전송되고, 일치하지 않으면 무시됩니다. 뿐만 아니라 `WebPubSubTrigger` 반환 개체는 사용자가 `SetState()` `ClearStates()` 연결에 대 한 메타 데이터를 관리할 수 있도록 지원 합니다. 그리고 확장은 반환 값의 결과를 요청의 원래 값과 병합 합니다 `WebPubSubConnectionContext.States` . 기존 키의 값이 덮어쓰여집니다. 새 키의 값이 추가 됩니다.

| 반환 형식 | 설명 | 속성 |
|---------|---------|---------|
|`ConnectEventResponse`| `connect` 이벤트에 대한 응답 | Groups, Roles, UserId, Subprotocol |
|`UserEventResponse`| 사용자 이벤트에 대한 응답 | 데이터 형식, 데이터 |
|`EventErrorResponse`| 동기화 이벤트에 대한 오류 응답 | Code, ErrorMessage |
|`*WebPubSubEventResponse`| 확실하지 않은 반환 사례에 사용되는 지원 응답의 기본 응답 형식 | - |

## <a name="input-binding"></a>입력 바인딩

확장은 서로 다른 요구 사항을 대상으로 하는 2개의 입력 바인딩을 제공합니다.

- `WebPubSubConnection`

  클라이언트가 Azure Web PubSub Service에 연결할 수 있게 하려면 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 알아야 합니다. `WebPubSubConnection` 입력 바인딩은 필요한 정보를 생성하므로 클라이언트는 이 토큰 생성 자체를 처리할 필요가 없습니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유하지 않도록 합니다. 이 입력 바인딩을 사용하는 HTTP 트리거를 클라이언트에서 연결 정보를 검색하는 데 사용할 수 있습니다.

- `WebPubSubContext`

  Static Web Apps를 사용하는 경우 `HttpTrigger`는 지원되는 유일한 트리거이며 Web PubSub 시나리오에서는 사용자가 Web PubSub 프로토콜 아래의 서비스 쪽에서 업스트림 http 요청을 역직렬화하도록 지원하는 `WebPubSubContext` 입력 바인딩을 제공합니다. 따라서 고객은와 비교 하 여 `WebPubSubTrigger` 함수에서 쉽게 처리할 수 있는 유사한 결과를 얻을 수 있습니다. 아래의 [예제](#example---webpubsubcontext)를 참조하세요.
  와 함께 사용 하는 경우 `HttpTrigger` 고객은 이벤트 처리기에서 HttpTrigger 노출 url을 적절 하 게 구성 해야 합니다.

### <a name="example---webpubsubconnection"></a>예제 - `WebPubSubConnection`

다음 예제에서는 입력 바인딩을 사용하여 Web PubSub 연결 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 C# 함수를 보여 줍니다. 아래 예제에서는 `UserId` 와 같은 클라이언트 요청 쿼리 부분을 통해 전달 됩니다 `?userid={User-A}` .

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json`에서 입력 바인딩을 정의합니다.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>인증된 **토큰**

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. App Service Authentication을 사용하여 함수 앱에 인증을 쉽게 추가할 수 있습니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다.

바인딩 식 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}`을 사용하여 바인딩의 UserId 속성을 헤더 중 하나의 값으로 설정할 수 있습니다.

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubcontext"></a>예제 - `WebPubSubContext`

다음 예제에서는 이벤트 형식에서 입력 바인딩을 사용 하 여 웹 PubSub 업스트림 요청 정보를 획득 하 고 HTTP를 통해 반환 하는 c # 함수를 보여 줍니다 `connect` .

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubContextInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubContext] WebPubSubContext wpsContext)
{
    if (wpsContext.IsPreflight || !wpsContext.HasError)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectEventResponse
    {
        UserId = wpsContext.Request.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json`에서 입력 바인딩을 정의합니다.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubContext",
      "name": "wpsContext",
      "direction": "in"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = async function (context, req, wpsContext) {
  if (!wpsContext.hasError || wpsContext.isPreflight)
  {
    console.log(`invalid request: ${wpsContext.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${wpsContext.connectionContext.userId} is connecting.`);
  return { body: {"userId": wpsContext.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>구성

#### <a name="webpubsubconnection"></a>WebPubSubConnection

다음 표에서는 function.json 파일 및 `WebPubSubConnection` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 | `webPubSubConnection`로 설정해야 합니다. |
| **direction** | 해당 없음 | `in`로 설정해야 합니다. |
| **name** | 해당 없음 | 입력 연결 바인딩 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
| **hub** | 허브 | 필수-트리거되는 함수에 대 한 웹 PubSub 허브의 이름으로 값을 설정 해야 합니다. 특성의 값을 더 높은 우선 순위로 설정하거나 앱 설정에서 전역 값으로 설정할 수 있습니다. |
| **userId** | UserId | 선택 사항 - 액세스 키 토큰에서 설정될 사용자 식별자 클레임의 값입니다. |
| **connection** | 연결 | 필수-웹 PubSub 서비스 연결 문자열을 포함 하는 앱 설정의 이름입니다 (기본값은 "WebPubSubConnectionString"). |

#### <a name="webpubsubcontext"></a>WebPubSubContext

다음 테이블에서는 function.json 파일 및 `WebPubSubContext` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 | `webPubSubContext`로 설정해야 합니다. |
| **direction** | 해당 없음 | `in`로 설정해야 합니다. |
| **name** | 해당 없음 | 입력 Web PubSub 요청에 대한 함수 코드에 사용되는 변수 이름입니다. |
| **connection** | 연결 | 선택 사항-앱 설정의 이름 또는 업스트림 Azure 웹 PubSub 서비스를 지정 하는 컬렉션을 설정 합니다. 값은 [남용 방지](https://github.com/cloudevents/spec/blob/v1.0.1/http-webhook.md#4-abuse-protection) 및 서명 유효성 검사에 사용 됩니다. 값은 기본적으로 "WebPubSubConnectionString"을 사용 하 여 자동으로 해결 됩니다. 및은 `null` 유효성 검사가 필요 하지 않으며 항상 성공 함을 의미 합니다. |

### <a name="usage"></a>사용량

#### <a name="webpubsubconnection"></a>WebPubSubConnection

# <a name="c"></a>[C#](#tab/csharp)

`WebPubSubConnection`는 아래 속성을 제공합니다.

| 바인딩 이름 | 바인딩 유형 | Description |
|---------|---------|---------|
| BaseUri | URI | 웹 PubSub 클라이언트 연결 uri입니다. |
| URI | URI | 웹 PubSub 연결의 절대 Uri로, `AccessToken` 요청에 생성 된 기본을 포함 합니다. |
| AccessToken | 문자열 | `AccessToken`요청 UserId 및 서비스 정보를 기반으로 생성 됩니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`WebPubSubConnection`는 아래 속성을 제공합니다.

| 바인딩 이름 | Description |
|---------|---------|
| baseUrl | 웹 PubSub 클라이언트 연결 uri입니다. |
| url | 웹 PubSub 연결의 절대 Uri로, `AccessToken` 요청에 생성 된 기본을 포함 합니다. |
| accessToken | `AccessToken`요청 UserId 및 서비스 정보를 기반으로 생성 됩니다. |

---

#### <a name="webpubsubcontext"></a>WebPubSubContext

`WebPubSubContext`는 아래 속성을 제공합니다.

| 바인딩 이름 | 바인딩 유형 | 설명 | 속성 |
|---------|---------|---------|---------|
| request | `WebPubSubEventRequest` | 클라이언트의 요청에 대 한 자세한 내용은 아래 표를 참조 하세요. | `WebPubSubConnectionContext` 요청 헤더 및 요청 본문에서 deserialize 된 기타 속성에서 요청을 설명 합니다 (예:) `Reason` `DisconnectedEventRequest` . |
| 응답 | `HttpResponseMessage` | 확장은 주로 및 오류 사례에 대 한 응답을 작성 `AbuseProtection` 합니다. | - |
| errorMessage | 문자열 | 업스트림 요청을 처리할 때 오류 세부 정보를 설명 합니다. | - |
| System.windows.controls.validation.haserror | bool | 유효한 웹 PubSub 업스트림 요청 인지 여부를 나타내는 플래그입니다. | - |
| isPreflight | bool | 의 실행 전 요청 인지 여부를 나타내는 플래그입니다 `AbuseProtection` . | - |

의 경우 `WebPubSubEventRequest` 요청 시나리오에 대 한 다양 한 정보를 제공 하는 다른 클래스로 deserialize 됩니다. `PreflightRequest`유효 하지 않은 경우에는 사용자가 플래그를 확인 `IsPreflight` 하 고 확인할 수 있습니다 `HasError` . 시스템 빌드 응답을 직접 반환 하는 것이 좋습니다 `WebPubSubContext.Response` . 또는 고객은 요청 시 오류를 기록할 수 있습니다. 다른 시나리오에서 고객은 아래와 같이 요청 속성을 읽을 수 있습니다.

| 파생 클래스 | 설명 | 속성 |
| -- | -- | -- |
| `PreflightRequest` | `AbuseProtection`가 true 일 때에 사용 `IsPreflight`  됩니다. | - |
| `ConnectEventRequest` | 시스템 `Connect` 이벤트 유형에 사용 됨 | Claims, Query, Subprotocols, ClientCertificates |
| `ConnectedEventRequest` | 시스템 `Connected` 이벤트 유형에 사용 됨 | - |
| `UserEventRequest` | 사용자 이벤트 유형에 사용 됨 | 데이터, 데이터 형식 |
| `DisconnectedEventRequest` | 시스템 `Disconnected` 이벤트 유형에 사용 됨 | 이유 |

> [!NOTE]
> 는 `WebPubSubContext` 입력 바인딩에서와 비교 하 여 유사한 요청 deserialize 방식을 제공 하지만 `HttpTrigger` `WebPubSubTrigger` 연결 상태 사후 병합은 지원 되지 않습니다. 반환 응답은 서비스 쪽에서 계속 적용 되지만 사용자는 직접 응답을 작성 해야 합니다. 사용자가 이벤트 응답을 설정 해야 하는 경우 `HttpResponseMessage` `ConnectEventResponse` 사용자 이벤트에 대 한 포함 또는 메시지를 **응답 본문** 으로 반환 하 고 `ce-connectionstate` **응답 헤더** 에 키를 사용 하 여 연결 상태를 입력 해야 합니다.

## <a name="output-binding"></a>출력 바인딩

웹 PubSub 출력 바인딩을 사용 하 여 Azure 웹 PubSub 서비스를 호출 하 고 작업을 수행 합니다. 다음으로 메시지를 브로드캐스트할 수 있습니다.

* 연결된 모든 클라이언트
* 특정 사용자에게 인증된 연결된 클라이언트
* 특정 그룹에 조인된 연결된 클라이언트
* 특정 클라이언트 연결

또한 출력 바인딩을 사용 하면 클라이언트 및 그룹을 관리할 수 있을 뿐만 아니라 그룹을 사용 하 여 특정 connectionId을 대상으로 하는 권한을 부여/취소할 수 있습니다.

* 그룹에 연결 추가
* 그룹에 사용자 추가
* 그룹에서 연결 제거
* 그룹에서 사용자 제거
* 모든 그룹에서 사용자 제거
* 모든 클라이언트 연결 닫기
* 특정 클라이언트 연결을 닫습니다.
* 그룹의 연결 닫기
* 연결 권한 부여
* 연결 권한 취소

설정 및 구성 세부 정보에 관한 내용은 개요를 참조하세요.

### <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubAction> actions)
{
    await actions.AddAsync(WebPubSubAction.CreateSendToAllAction("Hello Web PubSub!", WebPubSubDataType.Text));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`functions.json`에서 바인딩을 정의합니다.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "actions",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = async function (context) {
  context.bindings.actions = {
    "actionName": "sendToAll",
    "data": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsubaction"></a>WebPubSubAction 

`WebPubSubAction`은 출력 바인딩의 기본 추상 형식입니다. 파생 형식은 서비스에서 호출하려는 작업 서버를 나타냅니다. 

# <a name="c"></a>[C#](#tab/csharp)

C# 언어에서는 에서 사용 가능한 작업을 검색하는 데 도움이 되는 몇 가지 정적 `WebPubSubAction` 메서드를 제공합니다. 예를 들어 사용자는 를 호출하여 를 만들 수 `SendToAllAction` `WebPubSubAction.CreateSendToAllAction()` 있습니다.

| 파생 클래스 | 속성 |
| -- | -- |
| `SendToAllAction`|Data, DataType, Excluded |
| `SendToGroupAction`|Group, Data, DataType, Excluded |
| `SendToUserAction`|UserId, Data, DataType |
| `SendToConnectionAction`|ConnectionId, Data, DataType |
| `AddUserToGroupAction`|UserId, Group |
| `RemoveUserFromGroupAction`|UserId, Group |
| `RemoveUserFromAllGroupsAction`|UserId |
| `AddConnectionToGroupAction`|ConnectionId, Group |
| `RemoveConnectionFromGroupAction`|ConnectionId, Group |
| `CloseAllConnectionsAction`|Excluded, Reason |
| `CloseClientConnectionAction`|ConnectionId, Reason |
| `CloseGroupConnectionsAction`|Group, Excluded, Reason |
| `GrantPermissionAction`|ConnectionId, Permission, TargetName |
| `RevokePermissionAction`|ConnectionId, Permission, TargetName |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

와 같이 약한 형식의 언어에서 `javascript` **`actionName`** 는 형식을 해결하기 위한 키 매개 변수이며 사용 가능한 작업은 다음과 같습니다.

| ActionName | 속성 |
| -- | -- |
| `SendToAll`|Data, DataType, Excluded |
| `SendToGroup`|Group, Data, DataType, Excluded |
| `SendToUser`|UserId, Data, DataType |
| `SendToConnection`|ConnectionId, Data, DataType |
| `AddUserToGroup`|UserId, Group |
| `RemoveUserFromGroup`|UserId, Group |
| `RemoveUserFromAllGroups`|UserId |
| `AddConnectionToGroup`|ConnectionId, Group |
| `RemoveConnectionFromGroup`|ConnectionId, Group |
| `CloseAllConnections`|Excluded, Reason |
| `CloseClientConnection`|ConnectionId, Reason |
| `CloseGroupConnections`|Group, Excluded, Reason |
| `GrantPermission`|ConnectionId, Permission, TargetName |
| `RevokePermission`|ConnectionId, Permission, TargetName |

> [!IMPORTANT]
> 메시지 보내기 관련 작업의 메시지 데이터 속성은 `string` 데이터 형식이 로 설정된 경우 `json` 이거나 `text` 데이터 변환 모호성을 방지하기 위해 여야 합니다. 를 `JSON.stringify()` 사용하여 필요한 json 개체를 변환하세요. 이는 메시지 속성을 사용하는 모든 위치(예: `UserEventResponse.Data` 작업)에 적용됩니다. `WebPubSubTrigger` 
> 
> 데이터 형식이 로 설정된 `binary` 경우 에서 구성된 대로 자연스럽게 지원되는 바인딩을 활용할 수 있습니다. 자세한 내용은 트리거 및 바인딩 정의를 `dataType` `binary` `function.json` [참조하세요.](/azure/azure-functions/functions-triggers-bindings?tabs=csharp#trigger-and-binding-definitions)

---

### <a name="configuration"></a>구성

#### <a name="webpubsub"></a>WebPubSub

다음 표에서는 function.json 파일 및 `WebPubSub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 | `webPubSub`로 설정해야 합니다. |
| **direction** | 해당 없음 | `out`로 설정해야 합니다. |
| **name** | 해당 없음 | 출력 바인딩 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
| **hub** | 허브 | 함수가 트리거되려면 이 값을 반드시 Web PubSub 허브 이름으로 설정해야 합니다. 특성의 값을 더 높은 우선 순위로 설정하거나 앱 설정에서 전역 값으로 설정할 수 있습니다. |
| **connection** | 연결 | Web PubSub 서비스 연결 문자열을 포함하는 앱 설정의 이름입니다(기본값은 "WebPubSubConnectionString"). |

## <a name="troubleshooting"></a>문제 해결

### <a name="setting-up-console-logging"></a>콘솔 로깅 설정
서비스에 대해 수행하는 요청을 더 자세히 알아보려면 쉽게 [콘솔 로깅을 사용하도록 설정](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)할 수도 있습니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]