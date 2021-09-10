---
title: Azure SignalR Service에 대한 문제 해결 방법
description: 연결 및 메시지 전송 문제를 해결하는 방법을 알아봅니다.
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: a85e61536e04bd670cd49127f906c69727acc8e9
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113134982"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>연결 및 메시지 전송 문제를 해결하는 방법

이 지침에서는 자체 진단을 수행하여 근본 원인을 직접 찾거나 문제의 범위를 좁히는 데 도움이 되는 여러 가지 방법을 소개합니다. 자체 진단 결과는 추가 조사를 위해 당사에 보고할 때에도 유용합니다.

먼저, 어떤 [ServiceMode](./concept-service-mode.md)가 Azure SignalR Service(**ASRS** 라고도 함)로 구성되었는지 Azure Portal에서 확인해야 합니다.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* `Default` 모드는 [기본 모드 문제 해결](#default_mode_tsg)을 참조하세요.

* `Serverless` 모드는 [서버리스 모드 문제 해결](#serverless_mode_tsg)을 참조하세요.

* `Classic` 모드는 [클래식 모드 문제 해결](#classic_mode_tsg)을 참조하세요.

둘째, 문제를 해결하기 위해 서비스 추적을 캡처해야 합니다. 추적을 캡처하는 방법은 [서비스 추적을 캡처하는 방법](#how-to-capture-service-traces)을 참조하세요.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="how-to-capture-service-traces"></a>서비스 추적을 캡처하는 방법

문제 해결 프로세스를 단순화하기 위해 Azure SignalR 서비스는 **연결** 및 **메시지** 카테고리에 대한 서비스 추적을 노출하는 **라이브 추적 도구** 를 제공합니다. 추적에는 연결 연결/연결 해제 이벤트, 메시지 수신/남음 이벤트가 포함되지만 이에 국한되지 않습니다. **라이브 추적 도구** 를 사용하면 실시간 추적을 캡처, 보기, 정렬, 필터링 및 내보낼 수 있습니다. 자세한 내용은 [라이브 추적 도구 사용 방법](./signalr-howto-troubleshoot-live-trace.md)을 참조하세요.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>기본 모드 문제 해결

**ASRS** 가 *기본* 모드인 경우 *클라이언트*, *서버*, *서비스* 의 **세 가지** 역할이 있습니다.

* *클라이언트*: *클라이언트* 는 **ASRS** 에 연결된 클라이언트를 의미합니다. 클라이언트와 **ASRS** 를 연결하는 영구 연결을 이 지침에서는 *클라이언트 연결* 이라고 합니다.

* *서버*: *서버* 는 클라이언트 협상을 수행하고 SignalR `Hub` 논리를 호스트하는 서버를 의미합니다. *서버* 와 **ASRS** 간의 영구 연결을 이 지침에서는 *서버 연결* 이라고 합니다.

* *서비스*: *서비스* 는 이 지침에서 **ASRS** 의 약식 이름입니다.

전체 아키텍처 및 워크플로에 대한 자세한 소개는 [Azure SignalR Service 내부 정보](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)를 참조하세요.

문제의 범위를 좁히는 데 도움이되는 여러 가지 방법이 있습니다. 

* 문제가 일반적인 방식으로 발생하거나 재현할 수 있으면, 바로 앞으로 진행되는 트래픽을 확인합니다. 

* 문제가 재현하기 어려운 경우 추적 및 로그가 도움이 될 수 있습니다.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>트래픽을 확인하고 문제의 범위를 좁히는 방법

진행 중인 트래픽을 캡처하는 것은 문제의 범위를 좁히는 가장 간단한 방법입니다. 아래에 설명된 옵션을 사용하여 [네트워크 추적](/aspnet/core/signalr/diagnostics#network-traces)을 캡처할 수 있습니다.

* [Fiddler를 사용하여 네트워크 추적 수집](/aspnet/core/signalr/diagnostics#network-traces)

* [Tcpdump를 사용하여 네트워크 추적 수집](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [브라우저에서 네트워크 추적 수집](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>클라이언트 요청

SignalR 영구 연결의 경우, 먼저 `/negotiate`에서 호스트된 앱 서버에 연결하고 Azure SignalR Service로 리디렉션된 후 Azure SignalR Service에 대한 실제 영구 연결을 설정합니다. 자세한 단계는 [Azure SignalR Service의 내부 정보](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)를 참조하세요.

직접 클라이언트 쪽 네트워크 추적을 사용하여 어떤 상태 코드와 어떤 응답으로 요청에 실패하는지 확인하고, [문제 해결 가이드](./signalr-howto-troubleshoot-guide.md) 내에서 솔루션을 찾습니다.

#### <a name="server-requests"></a>서버 요청

SignalR *서버* 는 *서버* 와 *서비스* 간의 *서버 연결* 을 유지합니다. 앱 서버를 시작하면 Azure SignalR Service에 대한 **WebSocket** 연결이 시작됩니다. 모든 클라이언트 트래픽은 Azure SignalR Service를 통해 이러한 *서버 연결* 로 라우팅된 후 `Hub`로 디스패치됩니다. *서버 연결* 이 끊어지면, 이 *서버 연결* 로 라우팅되는 클라이언트에 영향을 줍니다. Microsoft Azure SignalR SDK에서는 영향을 최소화하기 위해 최대 1분의 지연 시간 동안 *서버 연결* 을 다시 연결하는 논리가 "항상 재시도"됩니다.

Azure SignalR Service의 네트워크 불안정이나 정기 유지 관리 또는 호스트된 앱 서버 업데이트/유지 관리 때문에 *서버 연결* 을 끊을 수 있습니다. 클라이언트 쪽에 연결 끊기/다시 연결 메커니즘이 있으면, 클라이언트 쪽에서 연결 끊기-다시 연결에 따른 영향이 최소화됩니다.

서버 쪽 네트워크 추적을 보고 *서비스* 에서 *서버 연결* 이 끊어지거나 거부되는 이유 및 오류 정보를 확인하고, [문제 해결 가이드](./signalr-howto-troubleshoot-guide.md)에서 근본 원인을 찾으세요.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>로그를 추가하는 방법

로그는 문제를 진단하고 실행 상태를 모니터링하는 데 유용합니다.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>클라이언트 쪽 로그를 사용하도록 설정하는 방법

클라이언트 쪽 로깅 환경은 자체 호스팅 SignalR를 사용하는 경우와 동일합니다.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>`ASP.NET Core SignalR`에 대한 클라이언트 쪽 로깅 사용

* [JavaScript 클라이언트 로깅](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET 클라이언트 로깅](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>`ASP.NET SignalR`에 대한 클라이언트 쪽 로깅 사용

* [.NET 클라이언트](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Windows Phone 8 클라이언트에서 추적 사용](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [JavaScript 클라이언트에서 추적 사용](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>서버 쪽 로그를 사용하도록 설정하는 방법

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>`ASP.NET Core SignalR`에 대해 서버 쪽 로깅을 사용하도록 설정

`ASP.NET Core SignalR`에 대한 서버 쪽 로깅은 `ASP.NET Core` 프레임 워크에서 제공된 `ILogger` 기반 [로깅](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&preserve-view=true&view=aspnetcore-2.1)과 통합됩니다. 다음과 같이 사용하는 샘플인 `ConfigureLogging`을 사용하여 서버 쪽 로깅을 사용하도록 설정할 수 있습니다.

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR에 대한 로거 범주는 항상 `Microsoft.Azure.SignalR`로 시작합니다. Azure SignalR에서 자세한 로그를 사용하도록 설정하려면, 아래와 같이 **appsettings.json** 파일에서 이전 접두사를 `Information` 수준으로 구성합니다.

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

비정상적인 경고/오류 로그가 기록되었는지 확인합니다. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>`ASP.NET SignalR`에 대해 서버 쪽 추적 사용

SDK 버전 `1.0.0` 이상을 사용하는 경우 다음을 `web.config`에 추가하여 추적을 사용하도록 설정할 수 있습니다([세부 정보](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102)).

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

비정상적인 경고/오류 로그가 기록되었는지 확인합니다. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Azure SignalR Service 내에서 로그를 사용하도록 설정하는 방법

Azure SignalR Service에 대한 [진단 로그를 사용하도록 설정](./signalr-howto-diagnostic-logs.md)할 수도 있습니다. 이러한 로그는 Azure SignalR Service에 연결된 모든 연결에 대한 자세한 정보를 제공합니다.

<a name="serverless_mode_tsg"></a>

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>서버리스 모드 문제 해결

**ASRS** 가 *서버리스* 모드에 있으면, **ASP.NET Core SignalR** 만 `Serverless` 모드를 지원하고, **ASP.NET SignalR** 는 이 모드를 지원하지 **않습니다**.

`Serverless` 모드에서 연결 문제를 진단하기 위한 가장 직접적인 방법은 [클라이언트 쪽 트래픽을 보는 것](#view_traffic_client)입니다. [클라이언트 쪽 로그](#add_logs_client)와 [서비스 쪽 로그](#add_logs_server)를 사용하는 것도 도움이 될 수 있습니다.

<a name="classic_mode_tsg"></a>

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>클래식 모드 문제 해결

`Classic` 모드는 사용되지 않으며, 사용하지 않는 것이 좋습니다. 이 모드에서 Azure SignalR 서비스는 연결된 *서버 연결* 을 사용하여 현재 서비스가 `default` 모드인지, `serverless` 모드인지 확인합니다. 네트워크 불안정 등으로 인해 연결된 모든 *서버 연결* 이 갑자기 끊어지면 Azure SignalR에서 현재 `serverless` 모드로 전환된 것으로 간주하기 때문에 이 경우 일부 중간 클라이언트 연결 문제가 발생할 수 있습니다. 이 기간에 연결된 클라이언트는 호스팅된 앱 서버로 라우팅되지 않습니다. [서비스 쪽 로그](#add_logs_server)를 사용하도록 설정하고, 앱 서버를 호스트하지만 일부 클라이언트가 앱 서버 쪽에 도달하지 않은 경우 `ServerlessModeEntered`로 기록된 클라이언트가 있는지 확인합니다. 이 경우 [클라이언트 연결을 중단](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API)하고 클라이언트를 다시 시작하는 것이 도움이 될 수 있습니다.

`classic` 모드 연결 및 메시지 전송 문제의 문제 해결은 [기본 모드 문제 해결](#default_mode_tsg)과 유사합니다.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>서비스 상태

상태 API에서 서비스 상태를 확인할 수 있습니다.

* 요청: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* 응답 상태 코드:
  * 200 = 정상
  * 503: 서비스가 비정상 상태입니다. 다음을 할 수 있습니다.
    * 자동 복구를 위해 몇 분 정도 기다립니다.
    * IP 주소가 포털의 IP와 동일한지 확인합니다.
    * 또는 인스턴스를 다시 시작합니다.
    * 위의 모든 옵션이 작동하지 않는 경우, Azure Portal에서 새로운 지원 요청을 추가하여 문의해 주세요.

[재해 복구](./signalr-concept-disaster-recovery.md)에 대한 자세한 정보.

[문제 해결에 대한 문제 또는 피드백이 있나요? 알려주세요.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 연결 및 메시지 전송 문제를 해결 하는 방법에 대해 알아보았습니다. 일반적인 문제를 처리하는 방법도 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [문제 해결 가이드](./signalr-howto-troubleshoot-guide.md)