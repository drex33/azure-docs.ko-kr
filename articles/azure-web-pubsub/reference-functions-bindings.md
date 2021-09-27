---
title: 참조 - Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩
description: 이 참조서에서는 Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 43fcc70bf88b7e037167a936ebd655b8877b394d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820366"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Azure Functions에 대한 Azure Web PubSub 트리거 및 바인딩

이 참조에서는 Azure Functions에서 Web PubSub 이벤트를 처리하는 방법을 설명합니다.

Web PubSub는 개발자가 실시간 기능 및 게시-구독 패턴을 사용하여 웹 애플리케이션을 쉽게 빌드할 수 있도록 도와주는 Azure 관리형 서비스입니다.

| 작업 | 형식 |
|---------|---------|
| 서비스에서 메시지가 제공되는 경우 함수 실행 | [트리거 바인딩](#trigger-binding) |
| 서비스 엔드포인트 URL 및 액세스 토큰 반환 | [입력 바인딩](#input-binding)
| Web PubSub 메시지 보내기 |[출력 바인딩](#output-binding) |

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
| C# 스크립트, JavaScript, Python, PowerShell       | [명시적으로 확장 설치]                    | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                                   | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

지정된 패키지 및 버전을 사용하여 [NuGet](https://www.nuget.org/)에서 클라이언트 라이브러리를 설치합니다.

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[명시적으로 확장 설치]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[확장 업데이트]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>주요 개념

![함수 앱으로 작업하는 Azure Web PubSub 서비스의 워크플로를 보여 주는 다이어그램](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) 클라이언트 연결을 생성하기 위한 HttpTrigger를 사용한 `WebPubSubConnection` 입력 바인딩입니다.

(3)-(4) 서비스 요청을 처리하기 위한 HttpTrigger를 사용한 `WebPubSubTrigger` 트리거 바인딩 또는 `WebPubSubRequest` 입력 바인딩입니다.

(5)-(6) 서비스에 작업을 요청하기 위한 `WebPubSub` 출력 바인딩입니다.

## <a name="trigger-binding"></a>트리거 바인딩

함수 트리거를 사용하여 Azure Web PubSub 서비스의 요청을 처리합니다. 

`WebPubSubTrigger`는 서비스 쪽에서 요청을 처리해야 할 때 사용됩니다. 트리거 엔드포인트 패턴은 아래와 같으며 Web PubSub 서비스 쪽에서 설정해야 합니다(Portal: 설정 -> 이벤트 처리기 -> URL 템플릿). 엔드포인트 패턴에서 쿼리 부분 `code=<API_KEY>`는 [보안](../azure-functions/security-concepts.md#system-key)상의 이유로 Azure 함수 앱을 사용할 때 **필수** 입니다. 키는 **Azure Portal** 에서 찾을 수 있습니다. 함수 앱 리소스를 찾고, Azure에 함수 앱을 배포한 후에 **함수** -> **앱 키** -> **시스템 키** -> **webpubsub_extension** 으로 이동합니다. 그러나 로컬 함수를 사용할 때는 이 키가 필요하지 않습니다.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>예제


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

또한 `WebPubSubTrigger` 바인딩은 서버가 클라이언트 요청을 확인 및 거부하거나 요청 클라이언트에 직접 메시지를 보낼 수 있는 경우 `Connect`, `Message` 이벤트와 같은 일부 시나리오의 반환 값도 지원합니다. `Connect` 이벤트는 `ConnectResponse` 및 `ErrorResponse`를 준수하고, `Message` 이벤트는 `MessageResponse` 및 `ErrorResponse`을 준수하며, 현재 시나리오와 일치하지 않는 rest 유형은 무시됩니다. 또한 `ErrorResponse`가 반환되면 서비스에서 클라이언트 연결을 삭제합니다.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
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
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

`WebPubSubTrigger` 바인딩은 일부 시나리오(예: `Connect`, `Message` 이벤트)의 반환 값도 지원합니다. 서버가 클라이언트 요청을 확인 및 거부할 수 있거나 요청 클라이언트에 직접 메시지를 보낼 수 있는 경우 JavaScript의 형식 없는 언어에서는 개체 키와 관련하여 역직렬화됩니다. 그리고 `ErrorResponse`는 rest 개체에 비해 우선 순위가 가장 높으며 `code`가 반환 결과에 있는 경우 `ErrorResponse`로 구문 분석되고 클라이언트 연결이 삭제됩니다.

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
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
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
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
| **eventName** | EventName | 필수 - 함수가 트리거되려면 이 값을 반드시 메시지의 이벤트로 설정해야 합니다. </br> `system` 이벤트 유형의 경우 이벤트 이름은 `connect`, `connected` 또는 `disconnect`에 있어야 합니다. </br> 시스템 지원 하위 프로토콜 `json.webpubsub.azure.v1.`의 경우 이벤트 이름은 사용자 정의 이벤트 이름입니다. </br> 사용자 정의 하위 프로토콜의 경우 이벤트 이름은 `message`입니다. |

### <a name="usages"></a>사용

C#에서 `ConnectionContext`는 형식 인식 바인딩 매개 변수이고, rest 매개 변수는 매개 변수 이름으로 바인딩됩니다. 아래 표에서 사용 가능한 매개 변수 및 형식을 확인합니다.

JavaScript와 같은 형식 없는 언어에서 `function.json`의 `name`은 아래 매핑 테이블과 관련된 트리거 개체를 바인딩하는 데 사용됩니다. 또한 `name`이 트리거 입력에 대한 바인딩 개체로서 `message`로 설정되면 그에 따라 `function.json`에서 `dataType`을 준수하여 메시지를 변환합니다. 모든 매개 변수는 `context.bindingData.<BindingName>`에서 읽을 수 있으며 `JObject`가 변환됩니다. 

| 바인딩 이름 | 바인딩 유형 | 설명 | 속성 |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|일반 요청 정보| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| 클라이언트에서 메시지 요청 | -|
|dataType|`MessageDataType`| 요청 메시지 dataType은 `binary`, `text`, `json`을 지원합니다. | -|
|claims|`IDictionary<string, string[]>`|`connect` 요청의 사용자 클레임 | -|
|Query|`IDictionary<string, string[]>`|`connect` 요청의 사용자 쿼리 | -|
|subprotocols|`string[]`|`connect` 요청에서 사용 가능한 하위 프로토콜 | -|
|clientCertificates|`ClientCertificate[]`|`connect` 요청에서 클라이언트의 인증서 지문 목록|-|
|reason|`string`|연결 끊기 요청의 이유|-|

### <a name="return-response"></a> 반환 응답

`WebPubSubTrigger`는 `connect`의 동기 이벤트 및 사용자 이벤트 `message`에 대해 고객이 반환한 응답을 준수합니다. 일치하는 응답만 서비스로 다시 전송되고, 일치하지 않으면 무시됩니다. 

| 반환 형식 | 설명 | 속성 |
|---------|---------|---------|
|`ConnectResponse`| `connect` 이벤트에 대한 응답 | Groups, Roles, UserId, Subprotocol |
|`MessageResponse`| 사용자 이벤트에 대한 응답 | DataType, Message |
|`ErrorResponse`| 동기화 이벤트에 대한 오류 응답 | Code, ErrorMessage |
|`ServiceResponse`| 확실하지 않은 반환 사례에 사용되는 지원 응답의 기본 응답 형식 | - |

## <a name="input-binding"></a>입력 바인딩

확장은 서로 다른 요구 사항을 대상으로 하는 2개의 입력 바인딩을 제공합니다.

- `WebPubSubConnection`

  클라이언트가 Azure Web PubSub Service에 연결할 수 있게 하려면 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 알아야 합니다. `WebPubSubConnection` 입력 바인딩은 필요한 정보를 생성하므로 클라이언트는 이 토큰 생성 자체를 처리할 필요가 없습니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유하지 않도록 합니다. 이 입력 바인딩을 사용하는 HTTP 트리거를 클라이언트에서 연결 정보를 검색하는 데 사용할 수 있습니다.

- `WebPubSubRequest`

  Static Web Apps를 사용하는 경우 `HttpTrigger`는 지원되는 유일한 트리거이며 Web PubSub 시나리오에서는 사용자가 Web PubSub 프로토콜 아래의 서비스 쪽에서 업스트림 http 요청을 역직렬화하도록 지원하는 `WebPubSubRequest` 입력 바인딩을 제공합니다. 따라서 고객은 함수에서의 쉬운 처리 측면에서 `WebPubSubTrigger`와 비교할 때 유사한 결과를 얻을 수 있습니다. 아래의 [예제](#example---webpubsubrequest)를 참조하세요.
  `HttpTrigger`와 함께 사용하는 경우 고객은 업스트림에서 HttpTrigger 노출 URL을 구성해야 합니다.

### <a name="example---webpubsubconnection"></a>예제 - `WebPubSubConnection`

다음 예제에서는 입력 바인딩을 사용하여 Web PubSub 연결 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 C# 함수를 보여 줍니다.

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

### <a name="example---webpubsubrequest"></a>예제 - `WebPubSubRequest`

다음 예제에서는 연결 이벤트 유형 아래의 입력 바인딩을 사용하여 Web PubSub 요청 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 C# 함수를 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
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
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
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
| **hub** | 허브 | 함수가 트리거되려면 이 값을 반드시 Web PubSub 허브 이름으로 설정해야 합니다. 특성의 값을 더 높은 우선 순위로 설정하거나 앱 설정에서 전역 값으로 설정할 수 있습니다. |
| **userId** | UserId | 선택 사항 - 액세스 키 토큰에서 설정될 사용자 식별자 클레임의 값입니다. |
| **connectionStringSetting** | ConnectionStringSetting | Web PubSub Service 연결 문자열(기본값 "WebPubSubConnectionString")을 포함하는 앱 설정의 이름입니다. |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

다음 테이블에서는 function.json 파일 및 `WebPubSubRequest` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 | `webPubSubRequest`로 설정해야 합니다. |
| **direction** | 해당 없음 | `in`로 설정해야 합니다. |
| **name** | 해당 없음 | 입력 Web PubSub 요청에 대한 함수 코드에 사용되는 변수 이름입니다. |

### <a name="usage"></a>사용량

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection`은 아래 속성을 제공합니다.

바인딩 이름 | 바인딩 유형 | Description
---------|---------|---------
BaseUrl | 문자열 | Web PubSub 클라이언트 연결 URL
Url | 문자열 | Web PubSub 연결의 절대 URI는 요청의 `AccessToken` 생성 기반을 포함합니다.
AccessToken | 문자열 | 요청 UserId 및 서비스 정보를 기준으로 생성된 `AccessToken`입니다.

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest`는 아래 속성을 제공합니다.

바인딩 이름 | 바인딩 유형 | 설명 | 속성
---------|---------|---------|---------
connectionContext | `ConnectionContext` | 일반 요청 정보| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature
request | `ServiceRequest` | 클라이언트의 요청, 자세한 내용은 아래 표를 참조하세요. | IsValidationRequest, Valid, Unauthorized, BadRequest, ErrorMessage, Name 등
응답 | `HttpResponseMessage` | 주로 `AbuseProtection` 및 오류 사례에 대한 확장 빌드 응답 | -

`ServiceRequest`의 경우 요청 시나리오에 대한 다양한 정보를 제공하는 다른 클래스로 역직렬화됩니다. `ValidationRequest` 또는 `InvalidRequest`의 경우 시스템 빌드 응답 `WebPubSubRequest.Response`를 직접 반환하는 것이 좋습니다. 또는 고객이 필요에 따라 오류를 기록할 수 있습니다. 다른 시나리오에서 고객은 아래와 같이 요청 속성을 읽을 수 있습니다.

파생 클래스 | 설명 | 속성
--|--|--
`ValidationRequest` | `IsValidationRequest`가 **true** 일 때 `AbuseProtection`에서 사용합니다. | -
`ConnectEventRequest` | `Connect` 이벤트 유형에서 사용합니다. | Claims, Query, Subprotocols, ClientCertificates
`ConnectedEventRequest` | `Connected` 이벤트 유형에서 사용합니다. | -
`MessageEventRequest` | 사용자 이벤트 유형에 사용 | Message, DataType
`DisconnectedEventRequest` | `Disconnected` 이벤트 유형에서 사용합니다. | 이유
`InvalidRequest` | 요청이 올바르지 않을 때 사용합니다. | -

## <a name="output-binding"></a>출력 바인딩

Azure Web PubSub Service를 사용하여 하나 이상의 메시지를 보내려면 Web PubSub 출력 바인딩을 사용합니다. 다음으로 메시지를 브로드캐스트할 수 있습니다.

* 연결된 모든 클라이언트
* 특정 사용자에게 인증된 연결된 클라이언트
* 특정 그룹에 조인된 연결된 클라이언트

출력 바인딩을 사용하여 그룹을 관리하고, 그룹을 사용하여 특정 connectionId를 대상으로 하는 권한을 부여/취소할 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 개요를 참조하세요.

### <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
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
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

`index.js`에서 함수를 정의합니다.

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation`은 출력 바인딩의 기본 추상 형식입니다. 파생 형식은 서비스에서 호출할 작업 서버를 나타냅니다. `javascript`와 같은 형식 없는 언어에서 `OperationKind`는 형식을 확인하기 위한 키 매개 변수입니다. 또한 `csharp`와 같은 강력한 형식 언어에서 사용자는 대상 작업 형식을 직접 만들 수 있으며, 고객 할당 `OperationKind` 값은 무시됩니다.

파생 클래스|속성
--|--
`SendToAll`|Message, DataType, Excluded
`SendToGroup`|Group, Message, DataType, Excluded
`SendToUser`|UserId, Message, DataType
`SendToConnection`|ConnectionId, Message, DataType
`AddUserToGroup`|UserId, Group
`RemoveUserFromGroup`|UserId, Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId, Group
`RemoveConnectionFromGroup`|ConnectionId, Group
`CloseClientConnection`|ConnectionId, Reason
`GrantGroupPermission`|ConnectionId, Group, Permission, TargetName
`RevokeGroupPermission`|ConnectionId, Group, Permission, TargetName

### <a name="configuration"></a>구성

#### <a name="webpubsub"></a>WebPubSub

다음 표에서는 function.json 파일 및 `WebPubSub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| function.json 속성 | 특성 속성 | Description |
|---------|---------|---------|
| **type** | 해당 없음 | `webPubSub`로 설정해야 합니다. |
| **direction** | 해당 없음 | `out`로 설정해야 합니다. |
| **name** | 해당 없음 | 출력 바인딩 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
| **hub** | 허브 | 함수가 트리거되려면 이 값을 반드시 Web PubSub 허브 이름으로 설정해야 합니다. 특성의 값을 더 높은 우선 순위로 설정하거나 앱 설정에서 전역 값으로 설정할 수 있습니다. |
| **connectionStringSetting** | ConnectionStringSetting | Web PubSub Service 연결 문자열(기본값 "WebPubSubConnectionString")을 포함하는 앱 설정의 이름입니다. |

## <a name="troubleshooting"></a>문제 해결

### <a name="setting-up-console-logging"></a>콘솔 로깅 설정
서비스에 대해 수행하는 요청을 더 자세히 알아보려면 쉽게 [콘솔 로깅을 사용하도록 설정](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)할 수도 있습니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]