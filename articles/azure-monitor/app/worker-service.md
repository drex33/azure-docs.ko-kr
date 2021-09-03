---
title: Worker Service 앱(비 HTTP 앱)용 Application Insights
description: Azure Monitor Application Insights를 사용하여 .NET Core/.NET Framework 비 HTTP 앱을 모니터링합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: 8ed4670c1eda0a6a6b1b32ca69cc8a2010ae486e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528855"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Worker Service 애플리케이션(비 HTTP 앱)에 대한 Application Insights

[Application Insights SDK For Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)는 메시지, 백그라운드 작업, 콘솔 애플리케이션 등과 같은 비 HTTP 작업에 가장 적합한 새로운 SDK입니다. 해당 유형의 애플리케이션에는 기존의 ASP.NET/ASP.NET Core 웹 애플리케이션과 같은 인커밍 HTTP 요청 개념이 없으므로, [ASP.NET](asp-net.md) 또는 [ASP.NET Core](asp-net-core.md) 애플리케이션에 대한 Application Insights 패키지 사용이 지원되지 않습니다.

새 SDK는 자체적으로 원격 분석 컬렉션을 수행하지 않습니다. 대신 [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 등과 같이 잘 알려진 다른 Application Insights 자동 수집기를 제공합니다. 해당 SDK는 원격 분석 컬렉션을 사용하고 구성하기 위해 `IServiceCollection`에 확장 메서드를 노출합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[Worker Service용 Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)는 애플리케이션이 실행되는 위치나 방식과 관계없이 비 HTTP 애플리케이션에 가장 적합합니다. 애플리케이션이 실행 중이고 Azure에 네트워크로 연결되어 있는 경우, 원격 분석을 수집할 수 있습니다. Application Insights 모니터링은 .NET Core를 지원하는 모든 위치에서 지원됩니다. 해당 패키지는 새로 도입된 [.NET Core 3.0 Worker 서비스](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [ASP.NET Core 2.1/2.2의 백그라운드 작업](/aspnet/core/fundamentals/host/hosted-services), 콘솔 앱(.NET Core/.NET Framework) 등에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

유효한 Application Insights 계측 키. 이 키는 원격 분석을 Application Insights에 보내는 데 필요합니다. 계측 키를 가져오기 위해 새 Application Insights 리소스를 만들어야 하는 경우, [Application Insights 리소스 만들기](./create-new-resource.md)를 참조하세요.

## <a name="using-application-insights-sdk-for-worker-services"></a>Worker Service용 Application Insights SDK 사용

1. 애플리케이션에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.
   다음 코드 조각에서는 프로젝트의 `.csproj` 파일에 추가해야 하는 변경 내용을 보여 줍니다.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 계측 키를 제공하여 `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)`확장 메서드`IServiceCollection`를 호출합니다. 해당 메서드는 애플리케이션 시작 시 호출되어야 합니다. 정확한 위치는 애플리케이션의 유형에 따라 달라집니다.

1. `ILogger` 인스턴스 또는 `TelemetryClient`DI(종속성 삽입) 컨테이너의 인스턴스를 호출`serviceProvider.GetRequiredService<TelemetryClient>();`하거나 Constructor Injection을 사용하여 검색합니다. 이 단계에서는 `TelemetryConfiguration` 및 자동 수집 모듈을 설정하는 작업을 트리거합니다.

각 애플리케이션 유형에 대한 구체적인 지침은 다음 섹션에서 설명합니다.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 Worker Service 애플리케이션

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerService)를 참조하세요.

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) 다운로드 및 설치
2. Microsoft Visual Studio에서 새 프로젝트 템플릿 또는 명령줄 `dotnet new worker`을 사용하여 새 Worker Service 프로젝트를 만듭니다.
3. 애플리케이션에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.

4. 다음 예제와 같이 `services.AddApplicationInsightsTelemetryWorkerService();`을 `Program.cs` 클래스의 `CreateHostBuilder()` 메서드에 추가합니다.

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. 아래 예제에 따라 `Worker.cs`을 수정합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. 계측 키를 설정합니다.

    계측 키를 `AddApplicationInsightsTelemetryWorkerService`에 대한 인수로 제공할 수 있지만 구성에서 계측 키를 지정하는 것이 좋습니다. 다음 코드 샘플에서는 `appsettings.json`에서 계측 키를 지정하는 방법을 보여 줍니다. 게시 과정에서 애플리케이션 루트 폴더에 `appsettings.json`이 복사되었는지 확인합니다.

```json
    {
        "ApplicationInsights":
        {
            "InstrumentationKey": "putinstrumentationkeyhere"
        },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

또는 다음 환경 변수 중 하나에서 계측 키를 지정합니다.
`APPINSIGHTS_INSTRUMENTATIONKEY` 또는 `ApplicationInsights:InstrumentationKey`

예: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
또는 `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

일반적으로 `APPINSIGHTS_INSTRUMENTATIONKEY`은 Web Apps에 Web Jobs로 배포된 애플리케이션에 대한 계측 키를 지정합니다.

> [!NOTE]
> 코드에 지정된 계측 키는 다른 옵션에 우선하는 환경 변수`APPINSIGHTS_INSTRUMENTATIONKEY`에 우선합니다.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>호스트된 서비스를 활용한 ASP.NET Core 백그라운드 작업

[이](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio) 문서에서는 ASP.NET Core 2.1/2.2 애플리케이션에서 백그라운드 작업을 만드는 방법을 설명합니다.

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/BackgroundTasksWithHostedService)를 참조하세요.

1. 애플리케이션에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.
2. 다음 예제와 같이 `services.AddApplicationInsightsTelemetryWorkerService();`을 `ConfigureServices()` 메서드에 추가합니다.

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

다음은 백그라운드 작업 로직이 존재하는 `TimedHostedService`의 코드입니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 계측 키를 설정합니다.
   위의 .Net Core 3.0 Worker Service 예제에서 동일한 `appsettings.json`을 사용합니다.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework Console 애플리케이션

이 문서의 시작 부분에서 설명한 것처럼 일반 콘솔 애플리케이션에서도 새 패키지를 사용하여 Application Insights 원격 분석을 사용하도록 설정할 수 있습니다. 해당 패키지는 [`NetStandard2.0`](/dotnet/standard/net-standard)을 대상으로 하므로 .NET Core 2.0 이상의 콘솔 앱 및 .NET Framework 4.7.2 이상의 앱에서 사용할 수 있습니다.

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ConsoleApp)를 참조하세요.

1. 애플리케이션에 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치합니다.

2. 아래 예제와 같이 Program.cs를 수정합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

또한 해당 콘솔 애플리케이션은 동일한 기본값 `TelemetryConfiguration`을 사용하며, 이전 섹션의 예제와 동일한 방식으로 사용자 지정될 수 있습니다.

## <a name="run-your-application"></a>애플리케이션 실행

애플리케이션을 실행합니다. 위의 모든 예제의 작업자는 bing.com에 대해 매초 http 호출을 수행하고 `ILogger`을 사용하여 몇 개의 로그를 내보냅니다. 해당 선은 작업을 만드는 데 사용되는 `TelemetryClient`의 `StartOperation` 호출 내에 래핑됩니다(이 예제에서는 `RequestTelemetry`을 ‘작업’이라 합니다). Application Insights는 해당 ILogger 로그(기본값으로 경고 또는 그 이상) 및 종속성을 수집하고 부모-자식 관계에 있는 `RequestTelemetry`과 상관관계를 가집니다. 상관관계는 프로세스/네트워크 경계에 걸쳐 작동합니다. 예를 들어, 모니터링되는 다른 구성 요소에 대한 호출을 수행하는 경우, 해당 구성 요소의 부모와도 상관관계가 지정됩니다.

`RequestTelemetry`의 사용자 지정 작업은 일반적인 Web Application에서 들어오는 웹 요청과 동등한 것으로 간주할 수 있습니다. 작업을 사용할 필요는 없지만, [Application Insights 상관관계 데이터 모델](./correlation.md)과 부모로서 작업을 수행하는 `RequestTelemetry`에 가장 적합하며, 작업자 반복 내에 생성된 모든 원격 분석은 논리적으로 동일한 작업에 속하는 것으로 처리됩니다. 해당 방법을 사용하면 생성된 모든 원격 분석(자동 및 수동)도 동일하게 `operation_id`을 포함합니다. 샘플링은 `operation_id`을 기반으로 하며 샘플링 알고리즘은 단일 반복에서 모든 원격 분석을 유지하거나 삭제합니다.

다음은 Application Insights가 자동으로 수집하는 원격 분석을 전체를 나열합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭](./live-stream.md)은 Application Insights 모니터링이 올바르게 구성되었는지 여부를 빠르게 확인하는 데 사용할 수 있습니다. 포털 및 분석에서는 원격 분석 데이터가 표시되기 시작하는 데 몇 분 정도 시간이 걸릴 수 있지만, 라이브 메트릭은 근 실시간으로 실행 중인 프로세스의 CPU 사용량을 표시합니다. 또한 요청, 종속성, 추적 등의 다른 원격 분석 데이터도 표시할 수 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

`ILogger`의 심각도 `Warning` 또는 그 이상을 통해 보내진 로그는 자동으로 캡처됩니다. [ILogger 문서](ilogger.md#logging-level)에 따라 Application Insights에서 캡처한 로그 수준을 사용자 지정합니다.

### <a name="dependencies"></a>종속성

종속성 수집은 기본적으로 사용하도록 설정됩니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서는 자동으로 수집되는 종속성에 대해 설명하고 수동 추적을 수행하는 단계를 포함합니다.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`은 기본적으로 사용하도록 설정되며, .NET Core 3.0 앱에서 카운터의 기본 집합을 수집합니다. [EventCounter](eventcounters.md) 자습서는 수집된 카운터의 기본 세트를 나열됩니다. 또한 목록을 사용자 지정하는 방법에 대한 지침도 포함되어 있습니다.

### <a name="manually-tracking-additional-telemetry"></a>추가 원격 분석 데이터 수동 추적

SDK는 위에서 설명한 대로 원격 분석 데이터를 자동으로 수집할 때, 대부분의 경우 사용자는 Application Insights 서비스에 추가 원격 분석 데이터를 전송해야 합니다. 추가 원격 분석 데이터를 추적하는 데 있어 권장되는 방법은 종속성 삽입에서 `TelemetryClient`의 인스턴스를 가져온 다음 지원되는 `TrackXXX()` [API](api-custom-events-metrics.md) 메서드 중 하나를 호출하는 것입니다. 또 다른 일반적 사용 사례는 [작업의 사용자 지정 추적](custom-operations-tracking.md)입니다. 해당 방법은 위의 Worker 예제에 설명되어 있습니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 구성

Worker Service SDK에서 사용하는 기본값 `TelemetryConfiguration`은 ASP.NET 또는 ASP.NET Core 애플리케이션에서 사용되는 자동 구성과 유사하며, `HttpContext`의 원격 분석 데이터를 보강하는 데 사용되는 TelemetryInitializers를 제외합니다.

Worker Service에 대한 Application Insights SDK를 사용자 지정하여 기본값 구성을 변경할 수 있습니다. Application Insights ASP.NET Core SDK의 사용자라면 [종속성 삽입](/aspnet/core/fundamentals/dependency-injection)에 기본 제공된 ASP.NET Core를 사용하여 구성을 변경하는 방법을 이미 숙지하고 있을 것입니다. 또한 WorkerService SDK는 유사한 원칙을 기반으로 합니다. 아래 설명된 대로 `IServiceCollection`에서 적절한 메서드를 호출하여 `ConfigureServices()` 섹션의 거의 모든 구성 변경을 수행합니다.

> [!NOTE]
> 해당 SDK를 사용하는 동안 `TelemetryConfiguration.Active`을 수정하여 구성을 변경하는 것은 지원되지 않으며 변경 내용은 반영되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions 사용

다음 예제와 같이 `ApplicationInsightsServiceOptions`을 `AddApplicationInsightsTelemetryWorkerService`로 전달하여 몇 가지 일반적인 설정을 수정할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

해당 SDK의 `ApplicationInsightsServiceOptions`은 ASP.NET Core SDK의 `Microsoft.ApplicationInsights.AspNetCore.Extensions`가 아닌 네임스페이스 `Microsoft.ApplicationInsights.WorkerService`에 있습니다.

`ApplicationInsightsServiceOptions`에서 자주 쓰이는 설정

|설정 | 설명 | 기본값
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics 기능 사용/사용 안 함 | true
|EnableAdaptiveSampling | 적응 샘플링 사용/사용 안 함 | true
|EnableHeartbeat | 주기적으로(15분 기본값) ‘HeartBeatState’라는 사용자 지정 메트릭을 .NET 버전, Azure 환경 정보 등의 런타임 정보로 (해당하는 경우) 보내는 하트비트 기능 사용/사용하지 않음. | true
|AddAutoCollectedMetricExtractor | AutoCollectedMetrics 추출기 사용/사용 안 함. 이는 샘플링을 수행하기 전에 요청/종속성에 대해 미리 집계된 메트릭을 보내는 TelemetryProcessor입니다. | true
|EnableDiagnosticsTelemetryModule | `DiagnosticsTelemetryModule` 사용/사용 안 함. 이를 사용하지 않도록 설정하면 `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` 설정이 무시됩니다. | true

최신 목록은 의 [구성 가능한 설정`ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)을 참조하세요.

### <a name="sampling"></a>샘플링

Application Insights SDK for Worker Service는 고정 비율과 적응 샘플링을 모두 지원합니다. 적응 샘플링은 기본적으로 사용하도록 설정됩니다. [ApplicationInsightsServiceOptions](#using-applicationinsightsserviceoptions)의 `EnableAdaptiveSampling` 옵션을 사용하여 샘플링 기능을 사용하지 않도록 설정할 수 있습니다.

추가 샘플링 설정을 구성하기 위해, 다음 예제를 사용할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

자세한 내용은 [샘플링](#sampling) 설명서에 있습니다.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

모든 원격 분석 데이터와 함께 전송되는 속성을 정의하려는 경우, [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)를 사용합니다.

`DependencyInjection` 컨테이너에 새 `TelemetryInitializer`을 추가하면 SDK가 자동으로 `TelemetryConfiguration`에 추가합니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

원격 분석 이니셜라이저는 기본적으로 제공됩니다. 모든 또는 특정 원격 분석 이니셜라이저를 제거하려면 `AddApplicationInsightsTelemetryWorkerService()`를 호출한 *후* 에 다음 샘플 코드를 사용합니다.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서 추가

`IServiceCollection`에서 확장 메서드 `AddApplicationInsightsTelemetryProcessor`를 사용하여 사용자 지정 원격 분석 프로세서를 `TelemetryConfiguration`에 추가할 수 있습니다. [고급 필터링 시나리오](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)에서 원격 분석 프로세서를 사용하여, Application Insights 서비스로 보내는 원격 분석 데이터에서 포함되거나 제외되는 항목을 보다 직접적으로 제어할 수 있습니다. 다음 예제를 참고하세요.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 TelemetryModules 구성 또는 제거

Application Insights는 원격 분석 모듈을 사용하여 수동으로 추적할 필요 없이 특정 워크로드에 대한 원격 분석 데이터를 자동으로 수집합니다.

다음 자동 수집 모듈은 기본적으로 사용하도록 설정되어 있습니다. 이러한 자동 수집 모듈은 원격 분석을 자동으로 수집해야 합니다. 자동 수집 모듈을 사용하지 않도록 설정하거나 기본 동작을 변경하도록 구성할 수 있습니다.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` - (현재 해당 원격 분석 모듈과 관련하여 문제가 발생했습니다. 임시 해결 방법은 [GitHub Issue 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
)를 참조하세요.)
* `AzureInstanceMetadataTelemetryModule`

기본값 `TelemetryModule`을 구성하려면 다음 예제와 같이 `IServiceCollection`에 확장 메서드 `ConfigureTelemetryModule<T>`를 사용합니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>원격 분석 채널 구성

기본 채널은 `ServerTelemetryChannel`입니다. 다음 예제와 같이 기본 채널을 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>동적으로 원격 분석 사용 안 함

원격 분석을 조건부로 또는 동적으로 사용하지 않도록 설정하려는 경우, 코드 어느 곳에서나 ASP.NET Core 종속성 삽입 컨테이너를 사용하여 `TelemetryConfiguration` 인스턴스를 확인하고 `DisableTelemetry` 플래그를 설정할 수 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집되지 않는 원격 분석 데이터를 추적하려면 어떻게 해야 하나요?

생성자 삽입을 사용하여 `TelemetryClient`의 인스턴스를 가져온 다음 필요한 `TrackXXX()` 메서드를 호출합니다. 새로 `TelemetryClient` 인스턴스를 만드는 것은 권장하지 않습니다. `TelemetryClient`의 싱글톤 인스턴스는 나머지 원격 분석 데이터와 `TelemetryConfiguration`을 공유하는 `DependencyInjection` 컨테이너에 이미 등록되어 있습니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 원격 분석 데이터의 나머지 부분과 분리된 별도 구성이 필요한 경우에만 권장됩니다.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Visual Studio IDE를 사용하여 Worker Service 프로젝트에 Application Insights를 온보딩할 수 있나요?

Visual Studio IDE의 온보딩은 현재 ASP.NET/ASP.NET Core 애플리케이션에 대해서만 지원하고 있습니다. 이 문서는 Visual Studio에서 Worker Service 애플리케이션에 대한 온보딩을 지원할 때 업데이트될 예정입니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-azure-monitor-application-insights-agent-formerly-status-monitor-v2"></a>Azure Monitor Application Insights Agent(이전의 상태 모니터 v2)와 같은 도구를 사용하여 Application Insights 모니터링을 사용하도록 설정할 수 있나요?

아니요, [Azure Monitor Application Insights Agent](./status-monitor-v2-overview.md)는 현재 ASP.NET 4.x만 지원합니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>내 애플리케이션을 Linux에서 실행하는 경우 모든 기능이 지원되나요?

예. 해당 SDK에 대한 기능 지원은 모든 플랫폼에서 동일하지만 다음과 같은 경우는 예외입니다.

* 성능 카운터는 라이브 메트릭에 표시된 프로세스 CPU/메모리를 제외한 Windows에서만 지원됩니다.
* `ServerTelemetryChannel`을 기본적으로 사용하도록 설정하는 경우에도 애플리케이션이 Linux 또는 macOS에서 실행될 때 네트워크 문제가 있으면 채널에서 원격 분석을 일시적으로 유지하기 위해 로컬 스토리지 폴더를 자동으로 만들지 않습니다. 이 제한으로 인해 일시적인 네트워크 또는 서버 문제가 있으면 원격 분석이 손실됩니다. 문제를 해결하려면 채널에 대한 로컬 폴더를 구성합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>샘플 애플리케이션

[.Net Core 콘솔 애플리케이션](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ConsoleApp) .NET Core(2.0 이상) 또는 .NET Framework(4.7.2 이상)로 작성된 콘솔 애플리케이션을 사용하는 경우, 해당 샘플을 사용합니다.

[HostedServices를 통한 ASP.NET Core 백그라운드 작업](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/BackgroundTasksWithHostedService) ASP.NET Core 2.1/2.2를 사용하여 [여기](/aspnet/core/fundamentals/host/hosted-services)의 공식 지침에 따라 백그라운드 작업을 만드는 경우 이 샘플을 사용합니다.

[.Net Core 3.0 작업자 서비스](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerService) [여기](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio#worker-service-template)의 공식 지침에 따른 .NET Core 3.0 작업자 서비스 애플리케이션이 있는 경우 이 샘플을 사용합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK

* [코드를 읽고 기여합니다](https://github.com/microsoft/ApplicationInsights-dotnet).

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](./release-notes.md)하세요.

## <a name="next-steps"></a>다음 단계

* [API를 사용](./api-custom-events-metrics.md)하여 앱의 성능 및 사용량에 대한 이벤트 및 메트릭을 보냅니다.
* [자동으로 추적되지 않는 추가 종속성을 추적합니다](./auto-collect-dependencies.md).
* [자동 수집된 원격 분석 데이터를 보강하거나 필터링합니다](./api-filtering-sampling.md).
* [ASP.NET Core에 종속성 삽입](/aspnet/core/fundamentals/dependency-injection).
