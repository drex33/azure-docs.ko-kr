---
title: .NET을 사용한 Application Insights 로깅
description: .NET에서 ILogger 인터페이스와 함께 Application Insights를 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 0457656ae06d4a86c8a4151ce8b89d3e42978d74
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529951"
---
# <a name="application-insights-logging-with-net"></a>.NET을 사용한 Application Insights 로깅

이 문서에서는 여러 NuGet 패키지를 사용하여 .NET 앱에서 Application Insights를 사용하여 로그를 캡처하는 방법을 알아봅니다.

- **코어 패키지:**
  - [`Microsoft.Extensions.Logging.ApplicationInsights`][nuget-ai]
- **워크로드 패키지:**
  - [`Microsoft.ApplicationInsights.AspNetCore`][nuget-ai-anc]
  - [`Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`][nuget-ai-ws-tc]

[nuget-ai]: https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights
[nuget-ai-anc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore
[nuget-ai-ws]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService
[nuget-ai-ws-tc]: https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel

> [!TIP]
> [`Microsoft.ApplicationInsights.WorkerService`][nuget-ai-ws] NuGet 패키지는 이 문서의 범위를 벗어납니다. 이를 통해 Application Insights를 백그라운드 서비스에 대해 사용하도록 설정할 수 있습니다. 자세한 내용은 [작업자 서비스 앱에 대한 Application Insights](./worker-service.md)를 참조하세요.

사용하는 Application Insights 로깅 패키지에 따라 `ApplicationInsightsLoggerProvider`를 등록하는 다양한 방법이 있습니다. `ApplicationInsightsLoggerProvider`는 <xref:Microsoft.Extensions.Logging.ILogger> 및 <xref:Microsoft.Extensions.Logging.ILogger%601> 구현 제공을 담당하는 <xref:Microsoft.Extensions.Logging.ILoggerProvider>의 구현입니다.

## <a name="aspnet-core-applications"></a>ASP.NET Core 애플리케이션

Application Insights 원격 분석을 ASP.NET Core 애플리케이션에 추가하려면 `Microsoft.ApplicationInsights.AspNetCore` NuGet 패키지를 사용합니다. 이를 [연결된 서비스로서의 Visual Studio](/visualstudio/azure/azure-app-insights-add-connected-service)를 통해 또는 수동으로 구성할 수 있습니다.

기본적으로 ASP.NET Core 애플리케이션은 [코드](./asp-net-core.md) 방식 또는 [코드리스](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) 방식을 사용하여 구성할 때 Application Insights 로깅 공급자가 등록됩니다. 등록된 공급자는 심각도가 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 이상인 로그 이벤트를 자동으로 캡처하도록 구성됩니다. 심각도 및 범주를 사용자 지정할 수 있습니다. 자세한 내용은 [로깅 수준](#logging-level)을 참조하세요.

1. NuGet 패키지가 설치되었는지 확인합니다.

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.17.0" />
    </ItemGroup>
   ```

1. `Startup.ConfigureServices` 메서드가 `services.AddApplicationInsightsTelemetry`를 호출하는지 확인합니다.

    ```csharp
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Microsoft.Extensions.Configuration;
    
    namespace WebApplication
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddApplicationInsightsTelemetry(
                    Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                // An alternative overload, when not using appsettings.json or user secrets.
                // services.AddApplicationInsightsTelemetry();
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                // omitted for brevity
            }
        }
    }
    ```

NuGet 패키지가 설치되고 공급자가 종속성 주입에 등록되면 앱이 기록할 준비가 된 것입니다. 생성자 주입을 사용하려면 <xref:Microsoft.Extensions.Logging.ILogger> 또는 일반 유형 대체 <xref:Microsoft.Extensions.Logging.ILogger%601>가 필요합니다. 이러한 구현이 해결되면 `ApplicationInsightsLoggerProvider`에서 제공합니다. 로깅된 메시지 또는 예외는 Application Insights로 전송됩니다. 

다음 컨트롤러 예를 고려해 보겠습니다.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");

        return new string[] { "value1", "value2" };
    }
}
```

자세한 내용은 [ASP.NET Core 로그인](/aspnet/core/fundamentals/logging)(영문)을 참조하세요.

### <a name="capture-logs-within-aspnet-core-startup-code"></a>ASP.NET Core 시작 코드 내에서 로그 캡처

일부 시나리오에서는 요청-응답 파이프라인이 요청을 받을 준비가 되기 전에 앱 시작 루틴의 일부로 로그를 캡처해야 합니다. 그러나 *Program.cs* 및 *Startup.cs* 의 종속성 주입에서 `ILogger` 구현을 쉽게 사용할 수 없습니다. 자세한 내용은 [.NET 로그인: Main에서 로그 만들기](/dotnet/core/extensions/logging?tabs=command-line#create-logs-in-main)를 참조하세요.

*Program.cs* 및 *Startup.cs* 에서 로깅하는 경우 몇 가지 제한 사항은 다음과 같습니다.

* 원격 분석은 [InMemoryChannel](./telemetry-channels.md) 원격 분석 채널을 통해 전송됩니다.
* 원격 분석에는 [샘플링](./sampling.md)이 적용되지 않습니다.
* 표준 [원격 분석 이니셜라이저 또는 프로세서](./api-filtering-sampling.md)를 사용할 수 없습니다.

다음 예제에서는 *Program.cs* 및 *Startup.cs* 를 명시적으로 인스턴스화하고 구성하여 이를 보여 줍니다.

#### <a name="example-programcs"></a>예제 Program.cs

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;

namespace WebApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            logger.LogInformation("From Program, running the host now.");

            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                })
                .ConfigureLogging((context, builder) =>
                {
                    // Providing an instrumentation key is required if you're using the
                    // standalone Microsoft.Extensions.Logging.ApplicationInsights package,
                    // or when you need to capture logs during application startup, such as
                    // in Program.cs or Startup.cs itself.
                    builder.AddApplicationInsights(
                        context.Configuration["APPINSIGHTS_CONNECTIONSTRING"]);

                    // Capture all log-level entries from Program
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Program).FullName, LogLevel.Trace);

                    // Capture all log-level entries from Startup
                    builder.AddFilter<ApplicationInsightsLoggerProvider>(
                        typeof(Startup).FullName, LogLevel.Trace);
                });
    }
}
```

위의 코드에서 `ApplicationInsightsLoggerProvider`는 `"APPINSIGHTS_CONNECTIONSTRING"` 연결 문자열로 구성됩니다. 필터가 적용되어 로그 수준이 <xref:Microsoft.Extensions.Logging.LogLevel.Trace?displayProperty=nameWithType>으로 설정됩니다.

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md?tabs=net)을 사용하는 것이 좋습니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 *사용해야 합니다*. 
>
> 연결 문자열은 원격 분석 데이터와 연결할 리소스를 식별합니다. 또한 연결 문자열을 통해 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.

#### <a name="example-startupcs"></a>예제 Startup.cs

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace WebApplication
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddApplicationInsightsTelemetry(
                Configuration["APPINSIGHTS_CONNECTIONSTRING"]);
        }

        // The ILogger<Startup> is resolved by dependency injection
        // and available in Startup.Configure.
        public void Configure(
            IApplicationBuilder app, IWebHostEnvironment env, ILogger<Startup> logger)
        {
            logger.LogInformation(
                "Configuring for {Environment} environment",
                env.EnvironmentName);

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGet("/", async context =>
                {
                    await context.Response.WriteAsync("Hello World!");
                });
            });
        }
    }
}
```

## <a name="console-application"></a>콘솔 애플리케이션

설치된 패키지는 다음과 같습니다.

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="5.0.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.17.0"/>
</ItemGroup>
```

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new InMemoryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(config => config.TelemetryChannel = channel);
                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}

```

앞의 예제에서는 `Microsoft.Extensions.Logging.ApplicationInsights` 패키지를 사용합니다. 기본적으로 이 구성은 Application Insights: `InMemoryChannel`에 데이터를 전송하기 위해 "최소 기본 사항" `TelemetryConfiguration` 설정을 사용합니다. 샘플링도 없고 표준 `TelemetryInitializer` 인스턴스도 없습니다. 다음 예제와 같이 콘솔 애플리케이션의 경우 이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.17.0" />
```

다음 섹션에서는 <xref:Microsoft.Extensions.Options.ConfigureOptions%601.Configure(%600)> 메서드를 사용하여 기본 `TelemetryConfiguration` 설정을 재정의하는 방법을 보여 줍니다. 이 예제에서는 `ServerTelemetryChannel`과 샘플링을 설정합니다. 사용자 지정 `TelemetryInitializer`인스턴스를 `TelemetryConfiguration`에 추가합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.ApplicationInsights;
using System;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static async Task Main(string[] args)
        {
            using var channel = new ServerTelemetryChannel();

            try
            {
                IServiceCollection services = new ServiceCollection();
                services.Configure<TelemetryConfiguration>(
                    config =>
                    {
                        config.TelemetryChannel = channel;

                        // Optional: implement your own TelemetryInitializer instance and configure it here
                        // config.TelemetryInitializers.Add(new MyTelemetryInitializer());

                        config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
                        channel.Initialize(config);
                    });

                services.AddLogging(builder =>
                {
                    // Only Application Insights is registered as a logger provider
                    builder.AddApplicationInsights("<YourInstrumentationKey>");
                });

                IServiceProvider serviceProvider = services.BuildServiceProvider();
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                logger.LogInformation("Logger is working...");
            }
            finally
            {
                // Explicitly call Flush() followed by Delay, as required in console apps.
                // This ensures that even if the application terminates, telemetry is sent to the back end.
                channel.Flush();

                await Task.Delay(TimeSpan.FromMilliseconds(1000));
            }
        }
    }
}
```

## <a name="logging-level"></a>로깅 수준

`ILogger` 구현에는 [로그 필터링](/dotnet/core/extensions/logging#how-filtering-rules-are-applied)을 적용하는 기본 제공 메커니즘이 있습니다. 이 필터링을 통해 Application Insights 공급자를 포함하여 등록된 각 공급자로 전송되는 로그를 제어할 수 있습니다. 구성에서(예를 들어 *appsettings.json* 파일 사용) 또는 코드에서 필터링을 사용할 수 있습니다.

다음 예에서는 `ApplicationInsightsLoggerProvider`에 필터 규칙을 적용하는 방법을 보여 줍니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>appsettings.json을 사용하여 구성에서 필터 규칙 만들기

`ApplicationInsightsLoggerProvider`의 별칭이 "ApplicationInsights"로 지정됩니다. *appsettings.json* 의 다음 섹션은 Application Insights의 기본 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 로그 수준을 재정의하여 수준 <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 이상에서 "Microsoft"로 시작하는 범주를 로깅합니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>코드에서 필터 규칙 만들기

다음 코드 조각은 이러한 항목에 대해 로그가 `ApplicationInsightsLoggerProvider`로 전송되도록 구성합니다.

- 모든 범주에서 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 이상
- "Microsoft"로 시작하는 범주에서 <xref:Microsoft.Extensions.Logging.LogLevel.Error?displayProperty=nameWithType> 이상

```csharp
Host.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(builder =>
    {
        builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Warning);
        builder.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
    });
```


## <a name="logging-scopes"></a>로깅 범위

`ApplicationInsightsLoggingProvider`는 [로그 범위](/dotnet/core/extensions/logging#log-scopes)를 지원합니다. 범위는 기본적으로 사용하도록 설정되어 있습니다. 

범위가 `IReadOnlyCollection<KeyValuePair<string,object>>` 형식이면 컬렉션의 각 키/값 쌍이 Application Insights 원격 분석에 사용자 지정 속성으로 추가됩니다. 다음 예제에서는 로그가 `TraceTelemetry`로 캡처되고 로그의 속성에 `("MyKey", "MyValue")`가 포함됩니다.

```csharp
using (_logger.BeginScope(new Dictionary<string, object> { ["MyKey"] = "MyValue" }))
{
    _logger.LogError("An example of an Error level message");
}
```

다른 형식이 범위로 사용되는 경우에는 로그가 Application Insights 원격 분석의 `Scope` 속성 아래에 저장됩니다. 다음 예제에서 `TraceTelemetry`에는 범위를 포함하는 `Scope`라는 속성이 있습니다.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider의 구 버전과 새 버전은 무엇인가요?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)에는 `ILoggerFactory` 확장 메서드를 통해 사용하도록 설정된 기본 제공 `ApplicationInsightsLoggerProvider`(`Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider`)가 포함되어 있습니다. 이 공급자는 버전 2.7.1부터 사용되지 않는 것으로 표시됩니다. 다음 주 버전 변경에서는 완전히 제거될 예정입니다. 

[Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 패키지 자체가 더 이상 사용되지 않는 것은 아닙니다. 요청, 종속성 등의 항목 모니터링을 사용하도록 설정하는 데 필요합니다.

새로운 독립 실행형 패키지 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)를 사용하는 것이 좋습니다. 여기에는 향상된 `ApplicationInsightsLoggerProvider` 인스턴스(`Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider`)와 이 인스턴스를 사용하도록 설정하기 위한 `ILoggerBuilder`에 대한 확장 메서드가 포함됩니다.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.1은 새 패키지에 대한 종속성을 사용하고 `ILogger` 캡처를 자동으로 사용하도록 설정합니다.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Application Insights에서 일부 ILogger 로그가 두 번 표시되는 이유는 무엇인가요?

`ILoggerFactory`에서 `AddApplicationInsights`를 호출하여 이전(사용되지 않는) 버전의 `ApplicationInsightsLoggerProvider`를 사용하도록 설정한 경우 중복이 발생할 수 있습니다. `Configure` 메서드에 다음 코드가 있는지 확인하고 제거합니다.

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Visual Studio에서 디버그할 때 이중 로깅을 경험하는 경우 다음과 같이 Application Insights를 사용하도록 설정하는 코드에서 `EnableDebugLogger`를 `false`로 설정합니다. 이 중복 및 픽스는 애플리케이션을 디버깅하는 경우에만 관련이 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions
    {
        EnableDebugLogger = false
    }
    services.AddApplicationInsightsTelemetry(options);
    // ...
}
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Microsoft.ApplicationInsights.AspNet SDK 버전 2.7.1로 업데이트했는데 ILogger의 로그가 자동으로 캡처됩니다. 이 기능을 완전히 해제하려면 어떻게 해야 하나요?

[로깅 수준](#logging-level) 섹션에서 일반적인 로그 필터링 방법을 참조하세요. `ApplicationInsightsLoggerProvider`를 해제하려면 로깅을 구성하기 위해 호출에 `LogLevel.None`을 사용합니다. 다음 명령에서 `builder`는 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>입니다.

```csharp
builder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.None);
```

*appsettings.json* 파일의 변경 내용은 다음과 같습니다.

```json
{
    "Logging": {
        "ApplicationInsights": {
            "LogLevel": {
                "Default": "None"
            }
        }
    }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>일부 ILogger 로그가 다른 속성과 같은 속성을 포함하지 않는 이유는 무엇인가요?

Application Insights는 다른 모든 원격 분석에 사용되는 것과 동일한 `TelemetryConfiguration` 정보를 사용하여 `ILogger` 로그를 캡처하고 전송합니다. 그러나 예외가 있습니다. 기본적으로, *Program.cs* 또는 *Startup.cs* 에서 로그하는 경우에는 `TelemetryConfiguration`이 완전하게 설정되지 않습니다. 이러한 위치의 로그에는 기본 구성이 없으므로 모든 `TelemetryInitializer`인스턴스와 `TelemetryProcessor` 인스턴스를 실행하지는 않습니다.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights를 사용하고 있고, 수동으로 일부 사용자 지정 원격 분석을 추가적으로 로그하고 싶습니다. 방법을 알려주세요.

독립 실행형 패키지를 사용하는 경우`TelemetryClient`는 DI(종속성 주입) 컨테이너에 삽입되지 않습니다. 다음 코드에 나와 있는 것처럼 `TelemetryClient`의 새 인스턴스를 만들고 로거 공급자에서 사용하는 것과 동일한 구성을 사용해야 합니다. 이렇게 하면 모든 사용자 지정 원격 분석 및 `ILogger`의 원격 분석에 동일한 구성이 사용됩니다.

```csharp
public class MyController : ApiController
{
   // This TelemetryClient instance can be used to track additional telemetry through the TrackXXX() API.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> `Microsoft.ApplicationInsights.AspNetCore` 패키지를 사용하여 Application Insights를 사용하도록 설정하는 경우 생성자에서 직접 `TelemetryClient`를 가져오도록 이 코드를 수정합니다. 예제는 [이 FAQ](./asp-net-core.md#frequently-asked-questions)를 참조하세요.

### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 로그에서는 어떤 Application Insights 원격 분석 유형을 생성하나요? Application Insights의 어디에서 ILogger 로그를 볼 수 있나요?

`ApplicationInsightsLoggerProvider`는 `ILogger` 로그를 캡처하여 `TraceTelemetry`를 만듭니다. `Exception` 개체가 `ILogger`의 `Log` 메서드에 전달되는 경우 `TraceTelemetry` 대신 `ExceptionTelemetry`가 생성됩니다. 

이러한 원격 분석 항목은 Azure Portal, 분석 또는 Visual Studio 로컬 디버거를 비롯하여 Application Insights의 다른 `TraceTelemetry` 또는 `ExceptionTelemetry` 항목이 있는 동일한 위치에서 찾을 수 있습니다.

항상 `TraceTelemetry`를 보내려면 다음 코드 조각을 사용합니다.

```csharp
builder.AddApplicationInsights(
    options => options.TrackExceptionsAsExceptionTelemetry = false);
```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>SDK가 설치되어 있지 않고, Azure Web Apps 확장을 사용하여 ASP.NET Core 애플리케이션에 Application Insights를 사용하도록 설정하고 있습니다. 새 공급자는 어떻게 사용하나요? 

Azure Web Apps의 Application Insights 확장은 새 공급자를 사용합니다. 사용 중인 애플리케이션에 대한 *appsettings.json* 파일의 필터링 규칙을 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [.NET의 로깅](/dotnet/core/extensions/logging)
* [ASP.NET Core에 로그인](/aspnet/core/fundamentals/logging)
* [Application Insights의 .NET 추적 로그](./asp-net-trace-logs.md)
