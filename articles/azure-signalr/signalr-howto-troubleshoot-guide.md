---
title: Azure SignalR Service에 대한 문제 해결 가이드
description: 일반적인 문제를 해결하는 방법을 알아봅니다.
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 1e909273ae2413a67da6e3975e5a2bc50a68685d
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186824"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>일반적인 Azure SignalR Service 문제 해결 가이드

이 지침은 고객이 지난 몇 년 동안 마주치고 해결한 일반적인 문제를 기반으로 하는 유용한 문제 해결 가이드를 제공하기 위한 것입니다.

## <a name="access-token-too-long"></a>액세스 토큰이 너무 김

### <a name="possible-errors"></a>가능한 오류

* 클라이언트 쪽 `ERR_CONNECTION_`
* 414 URI가 너무 김
* 413 페이로드가 너무 큼
* 액세스 토큰은 4K보다 길지 않아야 합니다. 413 요청 엔터티가 너무 큼

### <a name="root-cause"></a>근본 원인

HTTP/2의 경우 단일 헤더의 최대 길이는 **4K** 이므로 브라우저를 사용하여 Azure 서비스에 액세스하는 경우 이 제한에 대한 `ERR_CONNECTION_` 오류가 발생합니다.

HTTP/1.1 또는 C# 클라이언트의 경우 최대 URI 길이는 **12K** 이고, 최대 헤더 길이는 **16K** 입니다.

SDK 버전 **1.0.6** 이상에서는 생성된 액세스 토큰이 **4K** 보다 큰 경우 `/negotiate`에서 `413 Payload Too Large`를 throw합니다.

### <a name="solution"></a>솔루션

기본적으로 **ASRS**(**A** zure **S** ignal **R** **S** ervice)에 대한 JWT 액세스 토큰을 생성할 때 `context.User.Claims`의 클레임이 포함되므로 클라이언트에서 `Hub`에 연결할 때 클레임이 보존되고 **ASRS** 에서 `Hub`로 전달될 수 있습니다.

경우에 따라 `context.User.Claims`는 앱 서버에 대한 많은 정보를 저장하는 데 사용되며, 대부분은 `Hub`가 아니라 다른 구성 요소에서 사용됩니다.

생성된 액세스 토큰은 네트워크를 통해 전달되며, WebSocket/SSE 연결의 경우 액세스 토큰은 쿼리 문자열을 통해 전달됩니다. 따라서 허브에 필요할 때 **필요한** 클레임만 **ASRS** 를 통해 클라이언트에서 앱 서버로 전달하는 것이 좋습니다.

액세스 토큰 내에서 **ASRS** 로 전달되는 클레임을 사용자 지정할 수 있는 `ClaimsProvider`가 있습니다.

ASP.NET Core의 경우:

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

ASP.NET의 경우:

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1.2 필요

### <a name="possible-errors"></a>가능한 오류

* ASP.NET "서버를 사용할 수 없음" 오류 [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "연결이 활성 상태가 아닙니다. 데이터를 서비스로 보낼 수 없습니다." 오류 [#324](https://github.com/Azure/azure-signalr/issues/324)
* "`https://<API endpoint>`에 대한 HTTP 요청을 만드는 동안 오류가 발생했습니다. 이것은 HTTPS 경우에 서버 인증서가 HTTP.SYS로 제대로 구성되지 않았기 때문일 수 있습니다. 또한 클라이언트와 서버 사이에 보안 바인딩이 불일치하기 때문일 수 있습니다."

### <a name="root-cause"></a>근본 원인

Azure Service는 보안 문제에 대한 TLS 1.2만 지원합니다. .NET 프레임워크를 사용하는 경우 TLS 1.2가 기본 프로토콜이 아닐 수 있습니다. 따라서 ASRS에 대한 서버 연결을 설정할 수 없습니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 이 오류를 로컬로 재현할 수 있는 경우 *내 코드만* 의 선택을 취소하고, 모든 CLR 예외를 throw하고, 앱 서버를 로컬로 디버그하여 throw되는 예외를 확인합니다.
    * *내 코드만* 선택 취소

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="내 코드만 선택 취소":::

    * CLR 예외 throw

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR 예외 throw":::

    * 앱 서버 쪽 코드를 디버그할 때 throw되는 예외 확인
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="예외 throw":::

2. ASP.NET의 경우 다음 코드를 `Startup.cs`에 추가하여 자세한 추적을 사용하도록 설정하고, 로그에서 오류를 확인할 수도 있습니다.

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>솔루션

다음 코드를 Startup에 추가합니다.

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400 클라이언트 요청에 대해 잘못된 요청이 반환됨

### <a name="root-cause"></a>근본 원인

여러 개의 `hub` 쿼리 문자열이 있는지 클라이언트 요청에 확인합니다. `hub`는 유지되는 쿼리 매개 변수이며, 서비스의 쿼리에서 둘 이상의 `hub`를 검색하면 400이 throw됩니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>401 권한이 없음 클라이언트 요청에 대해 반환됨

### <a name="root-cause"></a>근본 원인

현재 JWT 토큰 수명의 기본값은 1시간입니다.

ASP.NET Core SignalR의 경우 WebSocket 전송 형식을 사용하면 정상입니다.

ASP.NET Core SignalR의 다른 전송 형식인 SSE 및 긴 폴링의 경우 연결은 기본적으로 최대 1시간 동안 지속될 수 있습니다.

ASP.NET SignalR의 경우 클라이언트에서 `/ping` KeepAlive(연결 유지) 요청을 서비스에 전송하는 경우가 있습니다. `/ping`이 실패하면 클라이언트에서 연결을 **중단** 하고 다시 연결하지 않습니다. 즉, ASP.NET SignalR의 경우 기본 토큰 수명으로 인해 모든 전송 형식에 대해 연결이 **최대** 1시간 동안 지속됩니다.

### <a name="solution"></a>솔루션

보안 문제를 해결하기 위해 TTL을 확장하는 것은 권장되지 않습니다. 이러한 401이 발생하면 연결을 다시 시작하기 위해 클라이언트에서 다시 연결 논리를 추가하는 것이 좋습니다. 클라이언트에서 연결을 다시 시작하면 앱 서버와 협상하여 JWT 토큰을 다시 가져오고 갱신된 토큰을 받습니다.

클라이언트 연결을 다시 시작하는 방법은 [여기](#restart_connection)서 확인하세요.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>404 클라이언트 요청에 대해 반환됨

SignalR 영구 연결의 경우 먼저 Azure SignalR Service와 협상(`/negotiate`)한 다음, Azure SignalR Service에 대한 실제 연결을 설정합니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

* [보내는 요청을 보는 방법](#view_request)에 따라 요청을 클라이언트에서 서비스로 가져옵니다.
* 404가 발생하면 요청의 URL을 확인합니다. URL이 웹앱을 대상으로 하고 `{your_web_app}/hubs/{hubName}`과 비슷한 경우 클라이언트 `SkipNegotiation`이 `true`인지 확인합니다. Azure SignalR을 사용하는 경우 클라이언트는 앱 서버와 처음 협상할 때 리디렉션 URL을 받습니다. Azure SignalR을 사용하는 경우 클라이언트에서 협상을 건너뛰지 **않아야 합니다**.
* `/negotiate`가 호출된 후 **5** 초를 초과하여 연결 요청이 처리되면 다른 404가 발생할 수 있습니다. 클라이언트 요청의 타임스탬프를 확인하고, 서비스에 대한 요청의 응답이 느린 경우 문제를 제출합니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>ASP.NET SignalR의 다시 연결 요청에 대해 404가 반환됨

ASP.NET SignalR의 경우 [클라이언트 연결이 끊어지면](#client_connection_drop) 연결을 중지하기 전에 동일한 `connectionId`를 사용하여 세 번 다시 연결합니다. `/reconnect`에서 영구 연결을 성공적으로 다시 설정할 수 있는 네트워크 간헐적 문제로 인해 연결이 끊어진 경우 `/reconnect`가 도움이 될 수 있습니다. 예를 들어 라우팅된 서버 연결이 끊어져 클라이언트 연결이 끊어지거나, SignalR Service에 인스턴스 다시 시작/장애 조치(failover)/배포와 같은 일부 내부 오류가 발생하는 것과 같은 다른 상황에서는 연결이 더 이상 존재하지 않으므로 `/reconnect`에서 `404`를 반환합니다. 이는 `/reconnect`에 필요한 동작이며, 세 번 다시 시도하면 연결이 중지됩니다. 연결이 중지되면 [연결 다시 시작](#restart_connection) 논리를 사용하는 것이 좋습니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>클라이언트 요청에 대해 429(너무 많은 요청)가 반환됨

다음 두 가지 경우가 있습니다.

### <a name="concurrent-connection-count-exceeds-limit"></a>**동시** 연결 수가 제한을 초과함

**무료** 인스턴스의 경우 **동시** 연결 수 제한은 20입니다. **표준** 인스턴스의 경우 **단위당** **동시** 연결 수 제한은 1K입니다. 즉, 100개의 단위에서100K의 동시 연결을 허용합니다.

연결에는 클라이언트 및 서버 연결이 모두 포함됩니다 연결 수를 계산하는 방법은 [여기](./signalr-concept-messages-and-connections.md#how-connections-are-counted)서 확인하세요.

### <a name="too-many-negotiate-requests-at-the-same-time"></a>동시에 너무 많은 협상 요청

다시 연결하기 전에 임의로 지연하는 것이 좋습니다. 다시 시도 샘플은 [여기](#restart_connection)서 확인하세요.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>협상하는 동안 500 오류가 발생했습니다. Azure SignalR Service가 아직 연결되지 않았습니다. 나중에 다시 시도하세요.

### <a name="root-cause"></a>근본 원인

이 오류는 연결된 Azure SignalR Service에 대한 서버 연결이 없을 때 보고됩니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

서버에서 Azure SignalR Service에 연결하려고 할 때 오류 세부 정보를 확인할 수 있도록 서버 쪽 추적을 사용하도록 설정합니다.

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>ASP.NET Core SignalR에 대한 서버 쪽 로깅 사용

ASP.NET Core SignalR에 대한 서버 쪽 로깅은 ASP.NET Core 프레임워크에서 제공하는 `ILogger` 기반 [로깅](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true)과 통합됩니다. 다음과 같이 사용하는 샘플인 `ConfigureLogging`을 사용하여 서버 쪽 로깅을 사용하도록 설정할 수 있습니다.

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR에 대한 로거 범주는 항상 `Microsoft.Azure.SignalR`로 시작합니다. Azure SignalR에서 자세한 로그를 사용하도록 설정하려면 아래와 같이 **appsettings.json** 파일에서 이전 접두사를 `Debug` 수준으로 구성합니다.

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>ASP.NET Core SignalR에 대한 서버 쪽 추적 사용

SDK 버전 `1.0.0` 이상을 사용하는 경우 다음을 `web.config`에 추가하여 추적을 사용하도록 설정할 수 있습니다([자세한 정보](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102)).

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>클라이언트 연결 끊김

클라이언트가 Azure SignalR에 연결되는 경우 다른 이유로 인해 클라이언트와 Azure SignalR 사이의 영구 연결이 끊어질 수 있는 경우가 있습니다. 이 섹션에서는 이러한 연결 끊김이 발생할 수 있는 몇 가지 가능성에 대해 설명하고 근본 원인을 식별하는 방법에 대한 지침을 제공합니다.

### <a name="possible-errors-seen-from-the-client-side"></a>클라이언트 쪽에서 확인되는 가능한 오류

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>근본 원인

다음과 같은 다양한 상황에서 클라이언트 연결이 끊어질 수 있습니다.
* 들어오는 요청으로 인해 `Hub`에서 예외를 throw하는 경우
* 클라이언트에서 라우팅한 서버 연결이 끊어지는 경우([서버 연결 끊김](#server_connection_drop)에 대한 자세한 내용은 아래 섹션 참조)
* 클라이언트와 SignalR Service 간에 네트워크 연결 문제가 발생하는 경우
* SignalR Service에 인스턴스 다시 시작, 장애 조치(failover), 배포 등과 같은 내부 오류가 있는 경우

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 앱 서버 쪽 로그를 열어 비정상 작업이 발생했는지 확인합니다.
2. 앱 서버 쪽 이벤트 로그를 확인하여 앱 서버가 다시 시작되었는지 확인합니다.
3. 시간 프레임을 제공하는 문제를 만들고, 리소스 이름을 이메일로 보냅니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>클라이언트 연결이 지속적으로 증가함

클라이언트 연결의 잘못된 사용으로 인해 발생할 수 있습니다. 누군가가 SignalR 클라이언트를 중지/삭제하는 것을 잊은 경우 연결이 계속 열려 있습니다.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Azure Portal 리소스 메뉴의 [모니터링] 섹션에 있는 SignalR의 메트릭에서 확인되는 가능한 오류

클라이언트 연결이 Azure SignalR의 메트릭에서 오랫동안 지속적으로 증가합니다.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="지속적으로 증가하는 클라이언트 연결":::

### <a name="root-cause"></a>근본 원인

SignalR 클라이언트 연결의 `DisposeAsync`가 호출되지 않고 연결이 계속 열려 있습니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

SignalR 클라이언트가 **닫히지 않았는지** 확인합니다.

### <a name="solution"></a>솔루션

연결을 닫았는지 확인합니다. `HubConnection.DisposeAsync()`를 수동으로 호출하여 사용 후 연결을 중지합니다.

예를 들면 다음과 같습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>일반적인 잘못된 클라이언트 연결 사용

#### <a name="azure-function-example"></a>Azure Function 예 

누군가가 Function 클래스에 대한 정적 멤버로 만드는 대신 Azure Function 메서드에서 SignalR 클라이언트 연결을 설정할 때 이 문제가 발생하는 경우가 많습니다. 하나의 클라이언트 연결만 설정되는 것으로 예상할 수 있지만, Azure Portal 리소스 메뉴의 [모니터링] 섹션에 있는 [메트릭]에서 클라이언트 연결 수가 지속적으로 증가하는 것을 볼 수 있습니다. 이러한 모든 연결은 Azure Function 또는 Azure SignalR Service가 다시 시작된 후에만 끊어집니다. 이는 **각** 요청에 대해 Azure Function에서 **하나** 의 클라이언트 연결을 만들고, Function 메서드에서 클라이언트 연결을 중지하지 않으면 클라이언트에서 Azure SignalR Service에 대한 연결을 유지하기 때문입니다.

#### <a name="solution"></a>솔루션

* Azure 함수에서 SignalR 클라이언트를 사용하거나 SignalR 클라이언트를 싱글톤으로 사용하는 경우 클라이언트 연결을 닫아야 합니다.
* Azure 함수에서 SignalR 클라이언트를 사용하는 대신, 다른 곳에서 SignalR 클라이언트를 만들고, [Azure SignalR Service에 대한 Azure Functions 바인딩](https://github.com/Azure/azure-functions-signalrservice-extension)을 사용하여 클라이언트를 Azure SignalR과 [협상](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22)할 수 있습니다. 또한 바인딩을 활용하여 [메시지를 보낼](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40) 수도 있습니다. 클라이언트를 협상하고 메시지를 보내는 샘플은 [여기](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)서 찾을 수 있습니다. 자세한 내용은 [여기](https://github.com/Azure/azure-functions-signalrservice-extension)서 확인할 수 있습니다.
* Azure 함수에서 SignalR 클라이언트를 사용하는 경우 시나리오에 더 나은 아키텍처가 있을 수 있습니다. 적절한 서버리스 아키텍처를 설계하는지 확인합니다. [Azure Functions에서 SignalR Service 바인딩을 사용하는 실시간 서버리스 애플리케이션](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)을 참조할 수 있습니다.

<a name="server_connection_drop"></a>

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>서버 연결 끊김

앱 서버가 시작되면 Azure SDK가 백그라운드에서 원격 Azure SignalR에 대한 서버 연결을 시작합니다. [Azure SignalR Service의 내부](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)에서 설명한 대로 Azure SignalR은 들어오는 클라이언트 트래픽을 이러한 서버 연결로 라우팅합니다. 서버 연결이 끊어지면 해당 연결에서 제공하는 모든 클라이언트 연결도 닫힙니다.

앱 서버와 SignalR Service 간의 연결은 영구 연결이므로 네트워크 연결 문제가 발생할 수 있습니다. 서버 SDK에는 서버 연결에 대한 **항상 다시 연결** 전략이 있습니다. 또한 서버에 대한 대량 동시 요청을 방지하기 위해 사용자가 임의의 지연 시간을 사용하여 클라이언트에 지속적인 다시 연결 논리를 추가하는 것이 좋습니다.

정기적으로 Azure SignalR Service에 대한 새 버전 릴리스가 있으며, 때로는 Azure 전체 OS를 패치 또는 업그레이드하거나, 때로는 종속 서비스 중단이 발생합니다. 이로 인해 짧은 기간의 서비스 중단이 발생할 수 있지만, 클라이언트 쪽에서 연결을 끊고 다시 연결하는 메커니즘을 사용하는 한 클라이언트 쪽의 연결 끊기-다시 연결에 따른 영향은 최소화됩니다.

이 섹션에서는 서버 연결 끊김으로 이어질 수 있는 몇 가지 가능성에 대해 설명하고, 근본 원인을 식별하는 방법에 대한 몇 가지 지침을 제공합니다.

### <a name="possible-errors-seen-from-the-server-side"></a>서버 쪽에서 확인되는 가능한 오류

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>근본 원인

**ASRS**(**A** zure **S** ignal **R** **S** ervice)에서 서버-서비스 연결을 닫습니다.

ping 시간 제한의 경우 높은 CPU 사용량 또는 서버 쪽의 스레드 풀 결핍으로 인해 발생할 수 있습니다.

ASP.NET SignalR의 경우 알려진 문제가 SDK 1.6.0에서 수정되었습니다. SDK를 최신 버전으로 업그레이드합니다.

## <a name="thread-pool-starvation"></a>스레드 풀 결핍

서버가 결핍 상태에 있으면 메시지 처리에서 스레드가 작동하지 않는 것입니다. 모든 스레드가 특정 메서드에서 응답하지 않습니다.

일반적으로 이 시나리오는 동기화를 통한 비동기화(async over sync) 또는 비동기 메서드의 `Task.Result`/`Task.Wait()`에 의해 발생합니다.

[ASP.NET Core 성능 모범 사례](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls)를 참조하세요.

[스레드 풀 결핍](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)에 대해 자세히 알아보세요.

### <a name="how-to-detect-thread-pool-starvation"></a>스레드 풀 결핍을 검색하는 방법

스레드 수를 확인합니다. 해당 시간에 급증하지 않는 경우 다음 단계를 수행합니다.
* Azure App Service를 사용하는 경우 메트릭에서 스레드 수를 확인합니다. `Max` 집계를 확인합니다.
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Azure App Service의 최대 스레드 수 창에 대한 스크린샷":::

* .NET Framework를 사용하는 경우 서버 VM의 성능 모니터에서 [메트릭](/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters)을 확인할 수 있습니다.
* 컨테이너에서 .NET Core를 사용하는 경우 [컨테이너에서 진단 수집](/dotnet/core/diagnostics/diagnostics-in-containers)을 참조하세요.

또한 코드를 사용하여 스레드 풀 결핍을 검색할 수 있습니다.

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
서비스에 추가합니다.
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

그런 다음, ping 시간 제한으로 인해 서버 연결이 끊어지면 로그를 확인합니다.

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>스레드 풀 결핍의 근본 원인을 확인하는 방법

스레드 풀 결핍의 근본 원인을 확인하려면 다음을 수행합니다.

* 메모리를 덤프한 다음, 호출 스택을 분석합니다. 자세한 내용은 [메모리 덤프 수집 및 분석](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/)을 참조하세요.
* 스레드 풀 결핍이 검색되면 [clrmd](https://github.com/microsoft/clrmd)를 사용하여 메모리를 덤프합니다. 그런 다음, 호출 스택을 기록합니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 앱 서버 쪽 로그를 열어 비정상 작업이 발생했는지 확인합니다.
2. 앱 서버 쪽 이벤트 로그를 확인하여 앱 서버가 다시 시작되었는지 확인합니다.
3. 문제를 만듭니다. 시간 프레임을 제공하고, 리소스 이름을 이메일로 보냅니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>팁

<a name="view_request"></a>

### <a name="how-to-view-the-outgoing-request-from-the-client"></a>클라이언트에서 발신되는 요청을 어떻게 볼 수 있나요?

ASP.NET Core를 예로 사용합니다(ASP.NET 예도 비슷함).

* 브라우저에서: 크롬을 예로 들면, **F12** 키를 사용하여 콘솔 창을 열고, **네트워크** 탭으로 전환할 수 있습니다. 처음부터 네트워크를 캡처하려면 **F5** 키를 사용하여 페이지를 새로 고쳐야 할 수 있습니다.

    :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome 네트워크 보기":::

* C# 클라이언트에서:

    [Fiddler](https://www.telerik.com/fiddler)를 사용하여 로컬 웹 트래픽을 볼 수 있습니다. WebSocket 트래픽은 Fiddler 4.5부터 지원됩니다.

    :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler 네트워크 보기" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

### <a name="how-to-restart-client-connection"></a>클라이언트 연결을 다시 시작하는 방법은 어떻게 될까요?
    
*항상 다시 시도* 전략을 사용하여 연결 논리를 다시 시작하는 작업이 포함된 [샘플 코드](https://github.com/Azure/azure-signalr/tree/dev/samples)는 다음과 같습니다.

* [ASP.NET Core C# 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

* [ASP.NET Core JavaScript 클라이언트](https://github.com/Azure/azure-signalr/blob/dev/samples/ChatSample/ChatSample.Net50/wwwroot/index.html#L171)

* [ASP.NET C# 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

* [ASP.NET JavaScript 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 일반적인 문제를 처리하는 방법을 알아보았습니다. 일반적인 문제 해결 방법을 자세히 알아볼 수도 있습니다. 

> [!div class="nextstepaction"]
> [연결 및 메시지 전송 문제를 해결하는 방법](./signalr-howto-troubleshoot-method.md)
