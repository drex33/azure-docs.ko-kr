---
title: Azure Application Insights의 원격 분석 샘플링 | Microsoft Docs
description: 제어에서 원격 분석의 양을 유지하는 방법입니다.
ms.topic: conceptual
ms.date: 08/26/2021
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 9db589de9bd62a00b7de89b2b558a3bac1e1785a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039981"
---
# <a name="sampling-in-application-insights"></a>Application Insights의 샘플링

샘플링은 [Azure Application Insights](./app-insights-overview.md)의 기능입니다. 애플리케이션 데이터의 올바른 분석을 통계적으로 유지하는 동안 원격 분석 트래픽, 데이터 비용 및 스토리지 비용을 줄일 수 있는 좋은 방법입니다. 샘플링을 사용하면 Application Insights에 의한 원격 분석 제한을 방지할 수도 있습니다. 샘플링 필터는 관련된 항목을 선택하여 진단 조사를 수행할 때 항목 간을 탐색할 수 있도록 합니다.

메트릭 수가 포털에 표시되면 샘플링을 고려하여 다시 정규화됩니다. 이렇게 하면 통계에 미치는 영향이 최소화됩니다.

## <a name="brief-summary"></a>간단한 요약

* 샘플링에는 적응 샘플링, 고정 비율 샘플링, 수집 샘플링의 세 가지 유형이 있습니다.
* 적응 샘플링은 Application Insights ASP.NET 및 ASP.NET Core SDK(소프트웨어 개발 키트)의 모든 최신 버전에서 기본적으로 사용하도록 설정됩니다. [Azure Functions](../../azure-functions/functions-overview.md)에도 사용됩니다.
* 고정 비율 샘플링은 ASP.NET, ASP.NET Core, Java(에이전트 및 SDK 모두) 및 Python용 Application Insights SDK의 최신 버전에서 사용할 수 있습니다.
* Java에서는 샘플링 재정의를 사용할 수 있으며 선택한 종속성, 요청, 상태 확인에 다른 샘플링 주기를 적용해야 하는 경우에 유용합니다. 예를 들어 모든 중요한 오류가 100%로 유지되는 동안 [샘플링 재정의](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-sampling-overrides)를 사용하여 일부 노이즈 의존성을 튜닝합니다. 이는 원격 분석을 세밀하게 제어할 수 있는 고정 샘플링의 한 형태입니다.
* 수집 샘플링은 Application Insights 서비스 엔드포인트에서 작동합니다. 다른 샘플링이 작동하지 않는 경우에만 적용됩니다. SDK에서 원격 분석을 샘플링하는 경우 수집 샘플링이 비활성화됩니다.
* 웹 애플리케이션에서는 사용자 지정 이벤트를 기록하고 이벤트 집합을 함께 유지 또는 삭제해야 하는 경우 이벤트의 `OperationId` 값이 동일해야 합니다.
* 분석 쿼리를 작성하는 경우 [샘플링을 고려](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)해야 합니다. 특히, 레코드를 단순히 세는 대신 `summarize sum(itemCount)`를 사용해야 합니다.
* 성능 메트릭 및 사용자 지정 메트릭을 비롯한 일부 원격 분석 유형은 샘플링 사용 여부에 관계없이 항상 유지됩니다.

다음 표에는 각 SDK 및 애플리케이션 유형에 사용할 수 있는 샘플링 유형이 요약되어 있습니다.

| Application Insights SDK | 적응 샘플링 지원 | 고정 비율 샘플링 지원 | 수집 샘플링 지원 |
|-|-|-|-|
| ASP.NET | [예(기본적으로 설정됨)](#configuring-adaptive-sampling-for-aspnet-applications) | [예](#configuring-fixed-rate-sampling-for-aspnet-applications) | 다른 샘플링이 작동하지 않는 경우에만 |
| ASP.NET Core | [예(기본적으로 설정됨)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [예](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 다른 샘플링이 작동하지 않는 경우에만 |
| Azure Functions | [예(기본적으로 설정됨)](#configuring-adaptive-sampling-for-azure-functions) | 예 | 다른 샘플링이 작동하지 않는 경우에만 |
| Java | 예 | [예](#configuring-sampling-overrides-and-fixed-rate-sampling-for-java-applications) | 다른 샘플링이 작동하지 않는 경우에만 |
| Node.JS | 예 | [예](./nodejs.md#sampling) | 다른 샘플링이 작동하지 않는 경우에만
| Python | 예 | [예](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 다른 샘플링이 작동하지 않는 경우에만 |
| 나머지 | 예 | 아니요 | [예](#ingestion-sampling) |

> [!NOTE]
> 이 페이지의 정보는 대부분 현재 버전의 Application Insights SDK에 적용됩니다. 이전 버전의 SDK에 대한 자세한 내용은 [아래 섹션을 참조](#older-sdk-versions)하세요.

## <a name="types-of-sampling"></a>샘플링 유형

다음은 세 가지 대체 샘플링 방법입니다.

* **적응 샘플링** 은 ASP.NET/ASP.NET Core 앱의 SDK 또는 Azure Functions에서 전송되는 원격 분석의 양을 자동으로 조정합니다. ASP.NET 또는 ASP.NET Core SDK를 사용하는 경우에 기본 샘플링입니다. 적응 샘플링은 현재 ASP.NET 서버 쪽 원격 분석 및 Azure Functions에만 사용할 수 있습니다.

* **고정 비율 샘플링** 은 ASP.NET/ASP.NET Core, Java 서버 또는 사용자 브라우저에서 전송되는 원격 분석의 양을 줄입니다. 비율은 사용자가 설정합니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

* **수집 샘플링** 은 Application Insights 서비스 엔드포인트에서 작동합니다. 설정한 샘플링 주기에 따라 앱에서 보낸 원격 분석 중 일부를 삭제합니다. 앱에서 보낸 원격 분석 트래픽을 줄이지는 않지만 월별 할당량 내로 유지하는 데 도움이 됩니다. 수집 샘플링의 주요 이점은 앱을 다시 배포하지 않고도 샘플링 비율을 설정할 수 있다는 것입니다. 수집 샘플링은 모든 서버 및 클라이언트에 균일하게 작동하지만 다른 유형의 샘플링이 작동하는 경우에는 적용되지 않습니다.

> [!IMPORTANT]
> 원격 분석 유형에 적응 또는 고정 비율 샘플링 방법을 사용하는 경우 해당 원격 분석에는 수집 샘플링이 사용되지 않습니다. 그러나 SDK 수준에서 샘플링에서 제외된 원격 분석 유형은 여전히 포털에 설정된 비율로 수집 샘플링이 적용됩니다.

## <a name="adaptive-sampling"></a>적응 샘플링

적응 샘플링은 웹 서버 앱에서 Application Insights 서비스 엔드포인트로 보내는 원격 분석의 양에 영향을 줍니다.

> [!TIP]
> 적응 샘플링은 ASP.NET SDK 또는 ASP.NET Core SDK를 사용하는 경우 기본적으로 사용하도록 설정되며, Azure Functions에 대해서도 기본적으로 사용하도록 설정됩니다.

볼륨은 지정된 최대 트래픽 속도 내에서 유지되도록 자동으로 조정되고 `MaxTelemetryItemsPerSecond` 설정을 통해 제어됩니다. 디버그할 때 또는 사용량이 낮은 경우와 같이 애플리케이션이 소량의 원격 분석을 생성하는 경우, 이 양이 `MaxTelemetryItemsPerSecond`보다 낮은 수준으로 유지되는 한 항목이 샘플링 프로세서에 의해 삭제되지 않습니다. 원격 분석 볼륨이 늘어나면 샘플링 비율은 목표량을 달성할 수 있도록 조정됩니다. 조정은 정기적으로 다시 계산되고 발신 전송 속도의 이동 평균에 기반합니다.

목표량을 달성하기 위해 생성된 원격 분석 중 일부가 삭제됩니다. 그러나 다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다.

요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 샘플링 주기에 맞게 보정되도록 조정되므로 메트릭 탐색기에서 거의 정확한 값으로 표시됩니다.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET 애플리케이션에 대한 적응 샘플링 구성

> [!NOTE]
> 이 섹션은 ASP.NET 애플리케이션에 적용되고 ASP.NET Core 애플리케이션에는 적용되지 않습니다. [이 문서의 뒷부분에 나오는 ASP.NET Core 애플리케이션에 적응 샘플링을 구성하는 방법을 알아봅니다.](#configuring-adaptive-sampling-for-aspnet-core-applications)

[`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md)에서는 `AdaptiveSamplingTelemetryProcessor` 노드에서 여러 매개 변수를 조정할 수 있습니다. 표시된 수치는 기본값입니다.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    적응 알고리즘이 **각 서버 호스트** 에서 수집하려는 [논리 작업](./correlation.md#data-model-for-telemetry-correlation)의 목표 비율입니다. 여러 호스트에서 웹앱을 실행하는 경우 Application Insights 포털에서 트래픽을 대상 비율 범위 내에서 유지하기 위해 이 값을 줄입니다.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    현재 원격 분석 비율이 다시 평가되는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮출 수 있는 시간 제한입니다.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    샘플링 비율 값을 변경한 후 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 높일 수 있는 시간 제한입니다.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최솟값입니다.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최댓값입니다.

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    이동 평균 계산에서는 가장 최근 값에 할당되는 가중치를 지정합니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    앱이 처음 시작될 때 샘플링할 원격 분석의 양입니다. 디버그하는 동안에는 이 값을 줄이지 마세요.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    샘플링하지 않으려는 유형의 세미콜론으로 구분된 목록입니다. 인식할 수 있는 유형은 다음과 같습니다. `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. 지정된 유형의 원격 분석은 모두 전송되고 지정되지 않은 유형은 샘플링됩니다.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    샘플링하려는 유형의 세미콜론으로 구분된 목록입니다. 인식할 수 있는 유형은 다음과 같습니다. `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. 지정된 유형은 샘플링되고 다른 유형의 원격 분석은 모두 항상 전송됩니다.

**적응 샘플링을 끄려면** `ApplicationInsights.config`에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거합니다.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>대체: 코드에서 적응 샘플링 구성

`.config` 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다.

1. `.config` 파일에서 모든 `AdaptiveSamplingTelemetryProcessor` 노드를 제거합니다.
2. 다음 코드 조각을 사용하여 적응 샘플링을 구성합니다.

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([원격 분석 프로세서에 대해 알아봅니다](./api-filtering-sampling.md#filtering).)

각 원격 분석 유형마다 개별적으로 샘플링 비율을 조정하거나 특정 유형이 샘플링되지 않도록 제외할 수도 있습니다.

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET Core 애플리케이션에 대한 적응 샘플링 구성

ASP.NET Core 애플리케이션용 `ApplicationInsights.config`는 없으므로 모든 구성은 코드를 통해 수행합니다.
적응 샘플링은 모든 ASP.NET Core 애플리케이션에 기본적으로 사용됩니다. 샘플링 동작을 사용하지 않도록 설정하거나 사용자 지정할 수 있습니다.

#### <a name="turning-off-adaptive-sampling"></a>적응 샘플링 끄기

`Startup.cs` 파일 내에서 `ConfigureServices` 메서드의 `ApplicationInsightsServiceOptions`를 사용하여 Application Insights 서비스를 추가하는 동안 기본 샘플링 기능을 사용하지 않도록 설정할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

위의 코드는 적응 샘플링을 사용하지 않도록 설정합니다. 더 많은 사용자 지정 옵션을 사용하여 샘플링을 추가하려면 다음 단계를 수행합니다.

#### <a name="configure-sampling-settings"></a>샘플링 설정 구성

아래와 같이 `TelemetryProcessorChainBuilder`의 확장 메서드를 사용하여 샘플링 동작을 사용자 지정합니다.

> [!IMPORTANT]
> 이 메서드를 사용하여 샘플링을 구성하는 경우 `AddApplicationInsightsTelemetry()`를 호출할 때 `aiOptions.EnableAdaptiveSampling` 속성을 `false`로 설정해야 합니다. 이렇게 변경한 후 사용자 지정이 적용된 적응형 샘플링을 다시 사용하려면 아래 코드 블록의 지침을 **정확히** 따라야 합니다. 이에 실패하면 데이터 수집이 과도하게 발생할 수 있습니다. 변경 후 샘플링 설정을 항상 테스트하고 비용 관리에 도움이 되도록 적절한 [일일 데이터 한도](pricing.md#set-the-daily-cap)를 설정합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Azure Functions에 대한 적응 샘플링 구성

[이 페이지](../../azure-functions/configure-monitoring.md#configure-sampling)의 지침에 따라 Azure Functions에서 실행되는 앱에 대한 적응 샘플링을 구성합니다.

## <a name="fixed-rate-sampling"></a>고정 비율 샘플링

고정 비율 샘플링은 웹 서버와 웹 브라우저에서 전송되는 트래픽을 줄입니다. 적응 샘플링과 달리 사용자가 결정한 고정 비율로 원격 분석을 줄어듭니다. 고정 비율 샘플링은 ASP.NET, ASP.NET Core, Java 및 Python 애플리케이션에 사용할 수 있습니다.

다른 샘플링 기술과 마찬가지로 관련 항목도 유지됩니다. 또한 클라이언트와 서버 샘플링을 동기화하여 관련 항목이 유지되도록 합니다. 예를 들어 검색의 페이지 보기에서 관련 서버 요청을 찾을 수 있습니다. 

메트릭 탐색기에서 요청 및 예외 수와 같은 빈도는 거의 정확한 값이 되도록 계수가 곱해져 샘플링 주기에 맞게 보정됩니다.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>ASP.NET 애플리케이션에 대한 고정 비율 샘플링 구성

1. **적응 샘플링을 사용하지 않도록 설정**: [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md)에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거하거나 주석으로 처리합니다.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 다음 코드 조각을 [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md)에 추가합니다.
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      또는 `ApplicationInsights.config` 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다.

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([원격 분석 프로세서에 대해 알아봅니다](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>ASP.NET Core 애플리케이션에 대한 고정 비율 샘플링 구성

1. **적응 샘플링을 사용하지 않도록 설정**: `ConfigureServices` 메서드에서 `ApplicationInsightsServiceOptions`를 사용하여 변경 작업을 수행할 수 있습니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 아래 코드 조각과 같이 `Configure` 메서드에서 변경 작업을 수행할 수 있습니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-sampling-overrides-and-fixed-rate-sampling-for-java-applications"></a>Java 애플리케이션에 대한 샘플링 재정의 및 고정 비율 샘플링 구성

Java 자동 계측 및 SDK에서는 기본값으로 샘플링이 사용되지 않습니다. 현재 Java 자동 계측, [샘플링 재정의](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-sampling-overrides) 및 고정 비율 샘플링이 지원됩니다. 적응 샘플링은 Java에서 지원되지 않습니다.

#### <a name="configuring-java-auto-instrumentation"></a>Java 자동 계측 구성

* 샘플링 재정의를 구성하여 기본 샘플링 주기를 재정의하고 선택한 요청 및 종속성에 다른 샘플링 주기를 적용하려면 [샘플링 재정의 가이드](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-sampling-overrides#getting-started)를 사용합니다.
* 모든 원격 분석에 적용되는 고정 비율 샘플링을 구성하려면 [고정 비율 샘플링 가이드](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config#sampling)를 사용합니다.

#### <a name="configuring-java-2x-sdk"></a>Java 2.x SDK 구성

1. 최신 [Application Insights Java SDK](./java-2x-get-started.md)를 사용하여 웹 애플리케이션을 다운로드하고 구성합니다.

2. 다음 코드 조각을 `ApplicationInsights.xml` 파일에 추가하여 **고정 비율 샘플링 모듈을 사용하도록 설정** 합니다.

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. `Processor` 태그의 `FixedRateSamplingTelemetryProcessor` 내부에 다음 태그를 사용하여 샘플링에서 특정 유형의 원격 분석을 포함하거나 제외할 수 있습니다.
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

샘플링에서 포함 또는 제외할 수 있는 원격 분석 유형은 다음과 같습니다. `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`.

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>OpenCensus Python 애플리케이션에 대한 고정 요금 샘플링 구성

최신 [OpenCensus Azure Monitor 내보내기](./opencensus-python.md)를 사용하여 애플리케이션을 계측합니다.

> [!NOTE]
> 메트릭 내보내기에는 고정 비율 샘플링을 사용할 수 없습니다. 즉, 사용자 지정 메트릭은 샘플링을 구성할 수 없는 유일한 원격 분석 유형입니다. 메트릭 내보내기는 추적하는 모든 원격 분석을 전송합니다.

#### <a name="fixed-rate-sampling-for-tracing"></a>추적을 위한 고정 비율 샘플링 ####
`Tracer` 구성의 일부로 `sampler`를 지정할 수 있습니다. 명시적 샘플러가 제공되지 않으면 기본적으로 `ProbabilitySampler`가 사용됩니다. `ProbabilitySampler`는 기본적으로 1/10,000의 비율을 사용합니다. 즉, 요청 10,000개당 하나가 Application Insights로 전송됩니다. 샘플링 속도를 지정하려면 아래를 참조하세요.

샘플링 비율을 지정하려면 `Tracer`가 0.0~1.0 사이의 샘플링 비율로 샘플러를 지정하는지 확인합니다. 1\.0의 샘플링 비율은 100%를 나타내며 모든 요청이 원격 분석으로 Application Insights에 전송됩니다.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>로그에 대한 고정 비율 샘플링 ####
선택적 인수 `logging_sampling_rate`를 수정하여 `AzureLogHandler`에 대한 고정 비율 샘플링을 구성할 수 있습니다. 이 인수를 제공하지 않으면 1.0의 샘플링 비율이 사용됩니다. 1\.0의 샘플링 비율은 100%를 나타내며 모든 요청이 원격 분석으로 Application Insights에 전송됩니다.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>JavaScript를 사용하여 웹 페이지에 대한 고정 비율 샘플링 구성

Application Insights를 사용하도록 JavaScript 기반 웹 페이지를 구성할 수 있습니다. 원격 분석은 사용자의 브라우저에서 실행되는 클라이언트 애플리케이션에서 전송되며 페이지는 어떤 서버에서도 호스트될 수 있습니다.

[JavaScript 기반 웹 페이지에서 Application Insights를 구성](javascript.md)할 때 Application Insights 포털에서 얻은 JavaScript 코드 조각을 수정합니다.

> [!TIP]
> JavaScript가 포함된 ASP.NET 앱에서 이 코드 조각은 일반적으로 `_Layout.cshtml`에 배치됩니다.

계측 키 앞에 `samplingPercentage: 10,`과 같은 줄을 삽입합니다.

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>서버 쪽 및 클라이언트 쪽 샘플링 조정

클라이언트 쪽(JavaScript) SDK는 서버 쪽 SDK와 함께 고정 비율 샘플링에 사용됩니다. 계측된 페이지는 서버 쪽 SDK가 샘플링에 포함하기로 결정한 동일한 사용자의 클라이언트 쪽 원격 분석만을 보냅니다. 이 논리는 전체 클라이언트 쪽 및 서버 쪽 애플리케이션에 대한 사용자 세션의 무결성을 유지하도록 설계되었습니다. 따라서 Application Insights의 특정 원격 분석 항목에서 이 사용자 또는 세션에 대한 다른 모든 원격 분석 항목을 찾고 검색에서 관련 페이지 보기와 요청 간을 탐색할 수 있습니다.

클라이언트 쪽 및 서버 쪽 원격 분석이 조정된 샘플을 표시하지 않는 경우

* 서버 및 클라이언트 모두에서 고정 비율 샘플링이 사용되는지 확인합니다.
* 클라이언트와 서버 모두에서 동일한 샘플링 비율을 설정하도록 합니다.
* SDK 버전이 2.0 이상이어야 합니다.

## <a name="ingestion-sampling"></a>수집 샘플링

수집 샘플링은 웹 서버, 브라우저 및 디바이스의 원격 분석이 Application Insights 서비스 엔드포인트에 도달하는 지점에서 작동합니다. 앱에서 전송되는 원격 분석 트래픽을 줄이지는 않지만 Application Insights에서 처리 및 보존(및 청구)되는 양을 줄입니다.

앱이 월간 할당량을 자주 초과하지만 SDK 기반의 샘플링 유형 중 하나를 사용할 옵션이 없는 경우 이 샘플링 유형을 사용합니다. 

사용량 및 예상 비용 페이지에서 샘플링 주기를 설정합니다.

![애플리케이션의 개요 블레이드에서 설정, 할당량, 샘플을 차례로 클릭한 다음, 샘플링 주기를 선택하고 업데이트를 클릭합니다.](./media/sampling/data-sampling.png)

다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다. 요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 올바르게 유지됩니다.

샘플링에서 무시된 데이터 요소는 [연속 내보내기](./export-telemetry.md)와 같은 Application Insights 기능에서 사용할 수 없습니다.

적응 또는 고정 비율 샘플링이 작동하는 동안에는 수집 샘플링이 작동하지 않습니다. 적응 샘플링은 ASP.NET SDK 또는 ASP.NET Core SDK를 사용하는 경우 또는 [Azure App Service](azure-web-apps.md)에서 Application Insights를 사용하는 경우 또는 상태 모니터를 사용하면 기본적으로 사용하도록 설정됩니다. Application Insights 서비스 엔드포인트는 원격 분석을 수신하면 원격 분석을 검사하고 샘플링 비율이 100% 미만인 것으로 보고되는 경우(원격 분석이 샘플링됨을 나타냄) 사용자가 설정한 수집 샘플링 비율은 무시됩니다.

> [!WARNING]
> 포털 타일에 표시된 값은 수집 샘플링에 대해 설정한 값을 나타냅니다. SDK 샘플링(적응 또는 고정 비율 샘플링)이 작동 중인 경우에는 실제 샘플링 비율을 나타내지 않습니다.

## <a name="when-to-use-sampling"></a>샘플링을 사용해야 하는 경우

일반적으로 중소 규모의 애플리케이션은 대부분에서는 샘플링이 필요하지 않습니다. 가장 유용한 진단 정보 및 가장 정확한 통계는 모든 사용자 활동에서 데이터를 수집하여 구합니다. 

샘플링의 주요 장점은 다음과 같습니다.

* 앱이 짧은 시간 간격으로 매우 높은 비율의 원격 분석을 전송할 때 Application Insights 서비스는 데이터 요소를 삭제('제한')합니다. 샘플링은 애플리케이션 제한이 발생할 가능성을 줄입니다.
* 가격 책정 계층에 대한 데이터 요소의 [할당량](pricing.md) 을 유지합니다. 
* 원격 분석의 컬렉션에서 네트워크 트래픽을 줄입니다. 

### <a name="which-type-of-sampling-should-i-use"></a>어떤 유형의 샘플링을 사용해야 합니까?

**다음과 같은 경우 수집 샘플링을 사용합니다.**

* 원격 분석의 월간 할당량을 자주 소진하는 경우
* 사용자의 웹 브라우저에서 너무 많은 원격 분석이 전송되는 경우
* 샘플링을 지원하지 않는 SDK 버전(예: ASP.NET 버전 2 이전)을 사용하는 경우

**다음과 같은 경우 고정 비율 샘플링을 사용합니다.**

* 사용자가 클라이언트 및 서버 간의 동기화된 샘플링을 원하는 경우 [검색](./diagnostic-search.md)에서 이벤트를 조사하고 있을 때 클라이언트 및 서버에서 페이지 보기 및 HTTP 요청과 같은 관련 이벤트 사이를 탐색할 수 있습니다.
* 사용자가 앱에 대한 적절한 샘플링 비율을 확신하는 경우입니다. 샘플링 비율은 정확한 메트릭을 가져오기 위해 충분히 높아야 하지만 가격 책정 할당량 및 조정 제한을 초과하는 비율보다 낮아야 합니다.

**다음과 같은 경우 적응 샘플링을 사용합니다.**

다른 형태의 샘플링을 사용하는 조건이 적용되지 않으면 적응 샘플링을 사용하는 것이 좋습니다. 이 설정은 ASP.NET/ASP.NET Core SDK에서 기본적으로 설정되어 있습니다. 특정 최소 비율에 도달할 때까지 트래픽이 감소되지 않으므로 사용 빈도가 낮은 사이트는 샘플링될 가능성이 전혀 없습니다.

## <a name="knowing-whether-sampling-is-in-operation"></a>샘플링이 작동 중인지 여부 확인

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](../logs/log-query-overview.md) 를 사용합니다.

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

`RetainedPercentage`가 100미만인 유형이 있는 경우 해당 유형의 원격 분석이 샘플링됩니다.

> [!IMPORTANT]
> Application Insights는 모든 샘플링 방법에서 세션, 메트릭(사용자 지정 메트릭 포함) 또는 성능 카운터 원격 분석 유형을 샘플링하지 않습니다. 이러한 원격 분석 유형에서는 정확도 감소가 아주 바람직하지 않으므로 이러한 유형은 항상 샘플링에서 제외됩니다.

## <a name="how-sampling-works"></a>샘플링 작동 방식

샘플링 알고리즘은 어떤 원격 분석 항목을 삭제하고 어떤 항목을 유지할지 결정합니다. 이는 SDK에서 샘플링을 하든 Application Insights 서비스에서 샘플링을 하든 마찬가지입니다. 샘플링 의사 결정은 상호 관련된 데이터 요소를 그대로 유지하려는 목표의 여러 규칙에 기반하며 이는 감소된 데이터 집합을 사용하더라도 실행할 수 있고 신뢰할 수 있는 Application Insights에서 진단 환경을 유지 관리합니다. 예를 들어 앱에서 실패한 요청이 샘플에 포함되는 경우 추가 원격 분석 항목(예: 이 요청에 대해 기록된 예외 및 추적)이 유지됩니다. 샘플링은 전체를 유지하거나 삭제합니다. 결과적으로 Application Insights에서 요청 세부 정보를 볼 때 항상 연결된 원격 분석 항목과 함께 요청을 확인할 수 있습니다.

샘플링 결정은 요청의 작업 ID를 기준으로 하므로 특정 작업에 속하는 모든 원격 분석 항목이 유지되거나 삭제됩니다. 작업 ID가 없는 원격 분석 항목의 경우(예: HTTP 컨텍스트가 없는 비동기 스레드에서 보고된 원격 분석 항목) 샘플링은 단순히 각 형식의 원격 분석 항목 백분율을 캡처합니다.

원격 분석을 다시 표시하는 경우 Application Insights 서비스는 누락된 데이터 요소를 보완하기 위해 컬렉션의 시간에 사용된 동일한 샘플링 백분율로 메트릭을 조정합니다. 따라서 Application Insights에서 원격 분석을 보면 사용자는 실제 수에 가까운 통계적으로 올바른 근사치를 확인할 수 있습니다.

근사치의 정확도는 주로 구성된 샘플링 비율에 따라 다릅니다. 또한 아주 많은 사용자에게서 많은 양의 일반적으로 비슷한 요청을 처리하는 애플리케이션에 대해 정확도가 증가합니다. 반면에 상당한 부하가 있을 때 작동하지 않는 애플리케이션의 경우 이러한 애플리케이션이 일반적으로 할당량 내에 있으면서 제한에서 데이터 손실이 발생하지 않고 해당 원격 분석을 모두 보낼 수 있기에 샘플링은 필요하지 않습니다. 

## <a name="frequently-asked-questions"></a>질문과 대답

*ASP.NET 및 ASP.NET Core SDK의 기본 샘플링 동작은 무엇인가요?*

* 최신 버전의 SDK 중 하나를 사용하는 경우 적응 샘플링은 기본적으로 활성화되며 초당 5개의 원격 분석 항목을 샘플링하도록 설정됩니다.
  기본적으로 두 개의 `AdaptiveSamplingTelemetryProcessor` 노드가 추가되어, 하나는 샘플링에 `Event` 유형을 포함하고 다른 하나는 샘플링에서 `Event`유형을 제외합니다. 이 구성은 SDK가 원격 분석 항목을 `Event` 유형의 5개 원격 분석 항목으로 제한하고 다른 모든 유형의 5개 원격 분석 항목을 결합하여 다른 원격 분석 유형과 별도로 `Events`가 샘플링되도록 한다는 것을 의미합니다. 이벤트는 일반적으로 비즈니스 원격 분석에 사용되며, 대부분의 경우 진단 원격 분석 볼륨의 영향을 받지 않습니다.
  
  다음은 생성되는 기본 `ApplicationInsights.config` 파일입니다. ASP.NET Core도 코드에서 동일한 기본 동작이 활성화됩니다. [이 페이지의 이전 섹션에 있는 예제](#configuring-adaptive-sampling-for-aspnet-core-applications)를 사용하여 이 기본 동작을 변경할 수 있습니다.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*원격 분석을 두 번 이상 샘플링할 수 있나요?*

* 아니요. SamplingTelemetryProcessor는 샘플링 고려 사항에서 이미 샘플링된 항목을 무시합니다. 수집 샘플링의 경우에도 마찬가지입니다. SDK 자체에서 이미 샘플링된 항목에는 샘플링을 적용하지 않습니다.

*샘플링은 왜 간단히 "각 원격 분석 형식의 X 퍼센트를 수집"하지 않습니까?*

* 이 샘플링 방법이 메트릭 근사치에 매우 높은 정확도를 제공하지만 사용자, 세션 및 요청 단위 당 진단 데이터를 연결하는 기능을 중단시키며 이는 진단에 있어 매우 중요합니다. 따라서 샘플링은 '앱 사용자의 X 퍼센트에 대해 모든 원격 분석 항목 수집' 또는 '앱 요청의 X퍼센트에 대한 모든 원격 분석 수집'과 같은 정책에서 더 잘 작동합니다. 요청과 연결되지 않은 원격 분석 항목의 경우(예: 배경 비동기 처리) '각 원격 분석 형식에 대해 모든 항목의 X퍼센트 수집'으로 대체됩니다. 

*샘플링 비율은 시간이 지나면서 달라질 수 있습니까?*

* 예, 적응 샘플링은 원격 분석의 현재 관찰된 양에 따라 샘플링 비율을 점차적으로 변경합니다.

*고정 비율 샘플링을 사용하는 경우 내 앱에 가장 적합한 샘플링 비율을 어떻게 알 수 있습니까?*

* 한 가지 방법은 적응 샘플링으로 시작하고 안정적인 비율(위 질문 참조)을 찾은 다음 해당 비율을 사용하여 고정 비율 샘플링으로 전환하는 것입니다. 
  
    그렇지 않으면 추측해야 합니다. Application Insights에서 현재 원격 분석 사용을 분석하고, 발생하는 모든 제한을 관찰하며, 수집된 원격 분석의 양을 추정합니다. 선택된 가격 책정 계층과 함께 이러한 세 가지 입력은 수집된 원격 분석의 볼륨을 얼마나 줄여야 할지 제안합니다. 그러나 사용자 수가 증가하거나 원격 분석의 양이 약간 다르게 변화되면 추정치가 무효화될 수도 있습니다.

*샘플링 비율을 너무 낮게 구성하는 경우 어떻게 되나요?*

* 샘플링 비율이 지나치게 낮으면 지나치게 공격적인 샘플링을 유발하여 Application Insights가 데이터의 볼륨을 축소하기 위해 데이터의 시각화를 보완하려고 할 때 근사치의 정확도가 줄어듭니다. 또한 드물게 실패하거나 느린 요청이 샘플링될 수 있기 때문에 진단 환경이 부정적인 영향을 받을 수 있습니다.

*샘플링 비율을 너무 높게 구성하는 경우 어떻게 되나요?*

* 샘플링 비율을 너무 높게 구성하면(충분히 공격적이지 않음) 수집된 원격 분석의 볼륨이 충분히 감소되지 않습니다. 여전히 제한에 관련된 원격 분석 데이터 손실이 발생할 수 있고 Application Insights를 사용하는 비용은 초과 요금때문에 계획된 것 보다 클 수 있습니다.

*샘플링을 어떤 플랫폼에서 사용할 수 있습니까?*

* SDK가 샘플링을 수행하지 않는 경우 특정 볼륨을 초과하는 모든 원격 분석에 대해 자동으로 수집 샘플링이 발생할 수 있습니다. 예를 들어 앱이 이전 버전의 ASP.NET SDK 또는 Java SDK를 사용하는 경우 이 구성이 작동할 수 있습니다.
* 현재 버전의 ASP.NET 또는 ASP.NET Core SDK(Azure 또는 자체 서버에 호스트됨)를 사용하는 경우 기본적으로 적응 샘플링이지만 위에서 설명한 것처럼 고정 비율 샘플링으로 전환할 수 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다. 
* 현재 버전의 Java 에이전트를 사용하는 경우 `applicationinsights.json`(Java SDK의 경우 `ApplicationInsights.xml`)을 구성하여 고정 비율 샘플링을 설정할 수 있습니다. 샘플링은 기본적으로 꺼져 있습니다. 고정 비율 샘플링을 사용하면 브라우저 SDK와 서버는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다.

*항상 보고 싶은 확실히 드문 이벤트가 있습니다. 이전의 샘플링 모듈에서 그 이벤트를 어떻게 가져올 수 있습니까?*

* 이를 위한 최선의 방법은 아래와 같이 유지하려는 원격 분석 항목에서 `SamplingPercentage`를 100으로 설정하는 사용자 지정 [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)를 작성하는 것입니다. 이니셜라이저가 원격 분석 프로세서(샘플링 포함)보다 먼저 실행되도록 보장되므로 모든 샘플링 기술이 샘플링 고려 사항에서 이 항목을 무시하게 됩니다. ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK 및 Java SDK에서 사용자 지정 원격 분석 이니셜라이저를 사용할 수 있습니다. 예를 들어 ASP.NET SDK를 사용하여 원격 분석 이니셜라이저를 구성할 수 있습니다.

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>이전 SDK 버전

적응 샘플링은 Application Insights SDK for ASP.NET v2.0.0-beta3 이상 또는 Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1에 사용할 수 있으며 기본적으로 사용하도록 설정됩니다.

고정 비율 샘플링은 ASP.NET 버전 2.0.0 이상 및 Java SDK 버전 2.0.1 이상 SDK의 기능입니다.

ASP.NET SDK v2.5.0-beta2 및 ASP.NET Core SDK v2.2.0-beta3 이전에는 '사용자'를 정의하는 애플리케이션(즉, 가장 일반적인 웹 애플리케이션)의 사용자 ID 해시를 기준으로 샘플링 결정이 이루어졌습니다. 사용자를 정의하지 않는 애플리케이션 유형의 경우(예: 웹 서비스) 샘플링 의사 결정은 요청의 작업 ID를 기반으로 했습니다. 최신 버전의 ASP.NET 및 ASP.NET Core SDK는 샘플링 결정에 작업 ID를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [필터링](./api-filtering-sampling.md) 은 SDK에서 보내는 항목을 더 엄격하게 제어할 수 있습니다.
* 개발자 네트워크 문서 [Application Insights를 사용하여 원격 분석 최적화](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)를 읽어 보세요.

