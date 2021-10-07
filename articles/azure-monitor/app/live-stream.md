---
title: 라이브 메트릭 스트림을 사용하여 진단 - Azure Application Insights
description: 사용자 지정 메트릭으로 웹앱을 실시간으로 모니터링하고 오류, 추적 및 이벤트의 라이브 피드를 통해 문제를 진단할 수 있습니다.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 3e19d424f8aa56f37b12ab776c9ff85ca78f6738
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614957"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>라이브 메트릭 스트림: 1초 대기 시간으로 모니터링 및 진단

[Application Insights](./app-insights-overview.md)에서 라이브 메트릭 스트림(QuickPulse라고도 함)을 사용하여 실시간 프로덕션 웹 애플리케이션을 모니터링합니다. 메트릭과 성능 카운터를 선택 및 필터링하여 서비스에 지장 없이 실시간으로 확인합니다. 실패한 요청 및 예외 샘플에서 스택 추적을 검사합니다. [프로파일러](./profiler.md) 및 [스냅샷 디버거](./snapshot-debugger.md)와 함께 라이브 메트릭 스트림은 라이브 웹 사이트에 강력하고 비침해적인 진단 도구를 제공합니다.

라이브 메트릭 스트림을 사용하여 다음을 수행할 수 있습니다.

* 릴리스되는 동안 성능 및 실패 수를 확인하여 수정된 부분의 유효성을 검사합니다.
* 테스트 로드의 영향을 확인하고 문제를 실시간으로 진단합니다.
* 확인하려는 메트릭을 선택 및 필터링하여 특정 테스트 세션에 중점을 두거나 알려진 문제를 필터링합니다.
* 예외 추적이 발생하면 가져옵니다.
* 필터를 사용하여 가장 관련성이 높은 KPI를 찾아 봅니다.
* 모든 Windows 성능 카운터를 실시간 모니터링합니다.
* 문제가 있는 서버를 쉽게 식별하고 해당 서버로의 모든 KPI/라이브 피드만 필터링합니다.

![라이브 메트릭 탭](./media/live-stream/live-metric.png)

라이브 메트릭은 현재 ASP.NET, ASP.NET Core, Azure Functions, Java 및 Node.js 앱에 대해 지원됩니다.

## <a name="get-started"></a>시작

1. 언어별 지침에 따라 라이브 메트릭을 사용하도록 설정합니다.
   * [ASP.NET](./asp-net.md) - 라이브 메트릭은 기본적으로 사용하도록 설정되어 있습니다.
   * [ASP.NET Core](./asp-net-core.md) - 라이브 메트릭은 기본적으로 사용하도록 설정되어 있습니다.
   * [.Net/.Net Core 콘솔/작업자](./worker-service.md) - 라이브 메트릭은 기본적으로 사용하도록 설정되어 있습니다.
   * [.Net 애플리케이션 - 코드를 사용하여 사용하도록 설정](#enable-livemetrics-using-code-for-any-net-application)합니다.
    * [Java](./java-in-process-agent.md) - 라이브 메트릭은 기본적으로 사용하도록 설정되어 있습니다.
   * [Node.JS](./nodejs.md#live-metrics)

2. [Azure Portal](https://portal.azure.com)에서 앱에 대한 Application Insights 리소스를 연 다음 라이브 스트림을 엽니다.

3. 필터에 고객 이름과 같은 중요한 데이터를 사용할 경우 [컨트롤 채널을 보호](#secure-the-control-channel)합니다.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>모든 .NET 애플리케이션에 대해 코드를 사용하여 LiveMetrics 사용

LiveMetrics는 .NET 애플리케이션에 권장되는 지침을 사용하여 온보딩할 때 기본적으로 사용하도록 설정되지만 다음에서는 라이브 메트릭을 수동으로 설정하는 방법을 보여 줍니다.

1. NuGet 패키지 [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)를 설치합니다.
2. 다음 샘플 콘솔 앱 코드는 라이브 메트릭을 설정하는 방법을 보여 줍니다.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

위의 샘플은 콘솔 앱에 대한 것이지만 모든 .NET 애플리케이션에서 동일한 코드를 사용할 수 있습니다. 원격 분석을 자동으로 수집하는 다른 TelemetryModule을 사용하도록 설정한 경우 해당 모듈을 초기화하는 데 사용되는 것과 동일한 구성이 라이브 메트릭 모듈에도 사용되는지 확인하는 것이 중요합니다.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>라이브 메트릭 스트림과 메트릭 탐색기 및 분석과의 차이점

| |라이브 스트림 | 메트릭 탐색기 및 분석 |
|---|---|---|
|**대기 시간**|데이터가 1초 내에 표시됨|몇 분에 걸쳐 집계됨|
|**보존 없음**|데이터가 차트에 있는 동안 지속된 후 삭제됨|[데이터가 90일 동안 유지됨](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**요청 시**|라이브 메트릭 창이 열려 있는 동안에만 데이터가 스트리밍됩니다. |SDK가 설치되고 사용될 때마다 데이터가 전송됨|
|**Free**|라이브 스트림 데이터 무료|[가격 책정](./pricing.md)에 따라 다름
|**샘플링**|선택한 모든 메트릭 및 카운터가 전송되고 오류 및 스택 추적이 샘플링되며 |이벤트가 [샘플링](./api-filtering-sampling.md)될 수 있음|
|**컨트롤 채널**|필터 제어 신호가 SDK로 전송되며 이 채널을 보호하는 것이 좋습니다.|통신은 포털로, 단방향으로 진행됩니다.|

## <a name="select-and-filter-your-metrics"></a>메트릭 선택 및 필터링

(ASP.NET, ASP.NET Core 및 Azure Functions(v2)와 함께 사용할 수 있습니다.)

포털에서 Application Insights 원격 분석에 임의 필터를 적용하여 사용자 지정 KPI를 라이브로 모니터링할 수 있습니다. 차트 위로 마우스를 가져가면 표시되는 필터 컨트롤을 클릭합니다. 다음 차트는 URL 및 기간 특성에 필터를 적용하여 사용자 지정 요청 수 KPI를 그래프로 나타냅니다. 언제든지 지정한 조건과 일치하는 원격 분석의 라이브 피드를 표시하는 스트림 미리 보기 섹션을 사용하여 필터의 유효성을 검사합니다.

![필터 요청 속도](./media/live-stream/filter-request.png)

Count와 다른 값을 모니터링할 수 있습니다. 옵션은 Application Insights 원격 분석이 될 수 있는 스트림 유형에 따라 달라집니다. 요청, 종속성, 예외, 추적, 이벤트 또는 메트릭 등이 옵션으로 제공됩니다. 이것은 사용자 고유의 [사용자 지정 측정](./api-custom-events-metrics.md#properties)일 수 있습니다.

![사용자 지정 메트릭을 사용한 요청 속도에 대한 쿼리 작성기](./media/live-stream/query-builder-request.png)

Application Insights 원격 분석 외에, 스트림 옵션 중에서 선택하고 성능 카운터의 이름을 제공하여 Windows 성능 카운터도 모니터링할 수도 있습니다.

라이브 메트릭은 각 서버에서 로컬로 집계된 후 모든 서버에서 집계됩니다. 해당 드롭다운 목록에서 다른 옵션을 선택하여 기본값을 변경할 수 있습니다.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>샘플 원격 분석: 사용자 지정 라이브 진단 이벤트
기본적으로 이벤트의 라이브 피드는 실패한 요청 및 종속성 호출, 예외, 이벤트 및 추적의 샘플을 보여 줍니다. 필터 아이콘을 클릭하면 특정 시점에 적용된 조건을 확인할 수 있습니다.

![필터 단추](./media/live-stream/filter.png)

메트릭과 마찬가지로, Application Insights 원격 분석 유형 중 하나에 대해 임의 조건을 지정할 수 있습니다. 이 예제에서는 특정 요청 실패 및 이벤트를 선택합니다.

![쿼리 작성기](./media/live-stream/query-builder.png)

> [!NOTE]
> 현재 예외 메시지 기반 조건의 경우 가장 바깥쪽 예외 메시지를 사용하세요. 앞에 나온 예제에서 안쪽 예외 메시지("<--" 구분 기호 다음에 나옴) "클라이언트 연결이 끊어졌습니다."를 포함하는 심각하지 않은 예외를 필터링하기 위해서는 "요청 콘텐츠를 읽는 동안 오류 발생" 조건을 포함하지 않는 메시지를 사용합니다.

라이브 피드 항목을 클릭하여 세부 정보를 확인합니다. **일시 중지** 를 클릭하거나, 아래로 스크롤하거나, 항목을 클릭하여 피드를 일시 중지할 수 있습니다. 맨 위로 다시 스크롤하거나 일시 중지된 상태에서 수집된 항목의 카운터를 클릭하면 라이브 피드가 계속됩니다.

![예외가 선택되어 있고 창 맨 아래에 예외 세부 정보를 표시하는 샘플 원격 분석 창을 보여 주는 스크린샷](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>서버 인스턴스별 필터링

특정 서버 역할 인스턴스를 모니터링하려는 경우 서버별로 필터링할 수 있습니다. 필터링하려면 *서버* 에서 서버 이름을 선택합니다.

![샘플링된 라이브 실패](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>컨트롤 채널 보호

> [!NOTE]
> 현재는 코드 베이스 모니터링을 사용하여 인증된 채널만 설정할 수 있으며 코드 없는 첨부를 사용하여 서버를 인증할 수 없습니다.

라이브 메트릭 포털에서 지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 계측 키 외에도 비밀 API 키를 사용해서 채널 보안을 유지할 수 있습니다.

### <a name="create-an-api-key"></a>API 키 만들기

![API 키 > API 키 만들기](./media/live-stream/api-key.png)
![API 키 만들기 탭. "SDK 컨트롤 채널 인증"을 선택한 다음, "키 생성"을 선택합니다.](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>구성에 API 키 추가

### <a name="aspnet"></a>ASP.NET

applicationinsights.config 파일에서 QuickPulseTelemetryModule에 AuthenticationApiKey를 추가합니다.

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

[ASP.NET Core](./asp-net-core.md) 애플리케이션의 경우 아래 지침을 따르세요.

다음과 같이 Startup.cs 파일의 `ConfigureServices`를 수정합니다.

다음 네임스페이스를 추가합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

그런 다음, 아래와 같이 `ConfigureServices` 메서드를 수정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

ASP.NET Core 애플리케이션 구성에 대한 자세한 내용은 [ASP.NET Core에서 원격 분석 모듈 구성](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)에 대한 지침에서 찾을 수 있습니다.

### <a name="workerservice"></a>WorkerService

[WorkerService](./worker-service.md) 애플리케이션의 경우 아래 지침을 따릅니다.

다음 네임스페이스를 추가합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

다음으로, `services.AddApplicationInsightsTelemetryWorkerService` 호출 전에 다음 줄을 추가합니다.

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

WorkerService 애플리케이션 구성에 대한 자세한 내용은 [WorkerServices에서 원격 분석 모듈 구성](./worker-service.md#configuring-or-removing-default-telemetrymodules)에 대한 지침에서 찾을 수 있습니다.

### <a name="azure-function-apps"></a>Azure 함수 앱

Azure 함수 앱(v2)의 경우 환경 변수를 사용하여 API 키로 채널 보안을 수행할 수 있습니다.

Application Insights 리소스 내에서 API 키를 만들고 함수 앱에 대한 **설정 > 구성** 으로 이동합니다. **새 애플리케이션 설정** 을 선택하고 `APPINSIGHTS_QUICKPULSEAUTHAPIKEY`의 이름 및 API 키에 해당하는 값을 입력합니다.

그러나 연결된 모든 서버를 인식하고 신뢰하는 경우 인증된 채널을 사용하지 않고 사용자 지정 필터를 시도할 수 있습니다. 이 옵션은 6개월 동안 사용할 수 있습니다. 이 재정의는 새 세션마다 한 번씩 또는 새 서버가 온라인 상태가 될 때마다 필요합니다.

![라이브 메트릭 인증 옵션](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>필터 조건에 CustomerID와 같은 잠재적으로 중요한 정보를 입력하기 전에 인증된 채널을 설정하는 것이 좋습니다.
>

## <a name="supported-features-table"></a>지원되는 기능 표

| 언어                         | 기본 메트릭       | 성능 메트릭 | 사용자 지정 필터링    | 샘플 원격 분석    | 프로세스별 CPU 분할 |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | 지원됨(V2.7.2 이상) | 지원됨(V2.7.2 이상) | 지원됨(V2.7.2 이상) | 지원됨(V2.7.2 이상) | 지원됨(V2.7.2 이상)  |
| .NET Core(대상=.NET Framework)| 지원됨(V2.4.1 이상) | 지원됨(V2.4.1 이상) | 지원됨(V2.4.1 이상) | 지원됨(V2.4.1 이상) | 지원됨(V2.4.1 이상)  |
| .NET Core(대상=.NET Core)     | 지원됨(V2.4.1 이상) | 지원됨*          | 지원됨(V2.4.1 이상) | 지원됨(V2.4.1 이상) | **지원 안 됨**    |
| Azure Functions v2               | 지원됨           | 지원됨           | 지원됨           | 지원됨           | **지원 안 됨**    |
| Java                             | 지원됨(V2.0.0 이상) | 지원됨(V2.0.0 이상) | **지원 안 됨**   | 지원 됨 (V 3.2.0 +) | **지원 안 됨**    |
| Node.js                          | 지원됨 (V1.3.0 이상) | 지원됨 (V1.3.0 이상) | **지원 안 됨**   | 지원됨 (V1.3.0 이상) | **지원 안 됨**    |

기본 메트릭에는 요청, 종속성 및 예외 비율이 포함됩니다. 성능 메트릭(성능 카운터)에는 메모리 및 CPU가 포함됩니다. 샘플 원격 분석은 실패한 요청 및 종속성, 예외, 이벤트 및 추적에 대한 자세한 정보 스트림을 표시합니다.

 \* PerfCounters 지원은 .NET Framework를 대상으로 하지 않는 .NET Core의 여러 버전에서 약간씩 다릅니다.

- PerfCounters 메트릭은 Windows용 Azure App Service에서 실행되는 경우 지원됩니다. (AspNetCore SDK 버전 2.4.1 이상)
- PerfCounters는 앱이 임의 Windows 머신(VM 또는 클라우드 서비스나 온-프레미스 등)(AspNetCore SDK 버전 2.7.1 이상)에서 실행되지만 .NET Core 2.0 이상을 대상으로 하는 경우에 지원됩니다.
- PerfCounters는 앱이 최신 버전(예: AspNetCore SDK 버전 2.8.0 이상)의 임의 위치(예: Linux, Windows, Linux용 앱 서비스, 컨테이너 등)에서 실행되지만 .NET Core 2.0 이상을 대상으로 하는 경우 지원됩니다.

## <a name="troubleshooting"></a>문제 해결

라이브 메트릭 스트림은 다른 Application Insights 원격 분석과 다른 IP 주소를 사용합니다. 방화벽에서 [해당 포트](./ip-addresses.md)가 열려 있는지 확인합니다. [라이브 메트릭 스트림의 나가는 포트](./ip-addresses.md#outgoing-ports)가 서버의 방화벽에서 열려 있는지도 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용하여 사용량 모니터링](./usage-overview.md)
* [진단 검색 사용](./diagnostic-search.md)
* [프로파일러](./profiler.md)
* [스냅샷 디버거](./snapshot-debugger.md)