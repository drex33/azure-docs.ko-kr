---
title: Application Insights SDK에서 필터링 및 전처리 | Microsoft Docs
description: SDK용 원격 분석 프로세서 및 원격 분석 이니셜라이저를 작성하여 원격 분석이 Application Insights 포털에 전송되기 전에 데이터에 대한 속성을 필터링하거나 추가합니다.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 293de0f963829516e3fdb119e3bcbf592f9ad113
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063415"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK에서 원격 분석 필터링 및 전처리

Application Insights SDK에 대한 플러그 인을 작성하고 구성하여 원격 분석을 Application Insights 서비스에 전송하기 전에 보강하고 처리하는 방법을 사용자 지정할 수 있습니다.

* [샘플링](sampling.md) 통계를 왜곡하지 않고 원격 분석의 양을 줄입니다. 관련 데이터 요소를 함께 유지하여 문제를 진단할 때 데이터 요소 간을 탐색할 수 있습니다. 포털에서는 샘플링을 보완하기 위해 총 개수를 곱합니다.
* 원격 분석 프로세서로 필터링하면 원격 분석을 서버에 전송하기 전에 SDK에서 필터링할 수 있습니다. 예를 들어 로봇의 요청을 제외하여 원격 분석의 양을 줄일 수 있습니다. 필터링은 트래픽을 감소시키는 데 있어 샘플링보다 더 기본적인 방법입니다. 전송되는 내용을 더 많이 제어할 수 있지만 통계에 영향을 줍니다. 예를 들어 성공한 모든 요청을 필터링할 수 있습니다.
* [원격 분석 이니셜라이저](#add-properties)는 표준 모듈의 원격 분석을 비롯하여 앱에서 보낸 모든 원격 분석에 속성을 추가하거나 수정합니다. 예를 들어 계산된 값을 추가하거나 포털에서 데이터를 필터링하는 데 사용할 버전 번호를 추가할 수 있습니다.
* [SDK API](./api-custom-events-metrics.md) 사용자 지정 이벤트 및 메트릭을 보내는 데 사용됩니다.

시작하기 전에

* [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [비 HTTP/.NET 및 .NET Core용 Worker](worker-service.md), [JavaScript](javascript.md) 등 애플리케이션에 적합한 SDK를 설치합니다.

<a name="filtering"></a>

## <a name="filtering"></a>필터링

이 기술을 사용하면 원격 분석 스트림에 포함된 것과 제외된 것을 직접적으로 제어할 수 있습니다. 필터링을 사용하여 원격 분석 항목을 Application Insights에 전송되지 않도록 끊을 수 있습니다. 필터링을 샘플링과 함께 사용할 수도 있고 따로 사용할 수도 있습니다.

원격 분석을 필터링하려면 원격 분석 프로세서를 작성하고 `TelemetryConfiguration`을 사용하여 등록합니다. 모든 원격 분석은 프로세서를 통과합니다. 이를 스트림에서 끊거나 체인의 다음 프로세서에 제공할 수 있습니다. HTTP 요청 수집기, 종속성 수집기와 같은 표준 모듈의 원격 분석뿐만 아니라 직접 추적한 원격 분석까지 포함됩니다. 예를 들어 로봇 또는 성공적인 종속성 호출의 요청에 대한 원격 분석을 필터링할 수 있습니다.

> [!WARNING]
> SDK에서 보낸 원격 분석을 프로세서를 사용하여 필터링하는 작업은 포털에 표시되는 통계를 왜곡하고 관련 항목을 추적하기 어렵게 할 수 있습니다.
>
> 대신 [샘플링](./sampling.md)사용을 고려하세요.
>
>

### <a name="create-a-telemetry-processor-c"></a>원격 분석 프로세서 만들기(C#)

1. 필터 그룹을 만들려면 `ITelemetryProcessor`를 시행하세요.

    원격 분석 프로세서는 일련의 프로세싱을 구성합니다. 원격 분석 프로세서를 인스턴스화하면 체인의 다음 프로세서에 대한 참조가 제공됩니다. 원격 분석 데이터 요소가 전달되면 프로세스 메서드는 작업을 수행하고 체인의 다음 원격 분석 프로세서를 호출하거나 호출하지 않습니다.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. 프로세서를 추가합니다.

ASP.NET **앱**

ApplicationInsights.config에 다음 코드 조각을 삽입합니다.

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

클래스에서 명명된 공용 속성을 제공하여 .config 파일의 문자열 값을 전달할 수 있습니다.

> [!WARNING]
> .config 파일의 형식 이름 및 모든 속성 이름이 코드의 클래스 및 속성 이름과 일치하는지 주의하여 확인해야 합니다. .config 파일에서 존재하지 않는 형식 또는 속성을 참조하는 경우 SDK가 자동으로 원격 분석을 전송하는 데 실패할 수 있습니다.
>

또는 코드에서 필터를 초기화할 수 있습니다. `Global.asax.cs`의 AppStart와 같은 적합한 초기화 클래스에서 체인에 프로세서를 삽입합니다.

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

이 시점 이후에 생성된 원격 분석 클라이언트는 나의 프로세서를 사용합니다.

ASP.NET **Core/Worker 서비스 앱**

> [!NOTE]
> `ApplicationInsights.config` 또는 `TelemetryConfiguration.Active`를 사용하여 프로세서를 추가하는 것은 ASP.NET Core 애플리케이션 또는 Microsoft.ApplicationInsights.WorkerService SDK를 사용하는 경우에는 유효하지 않습니다.

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) 또는 [WorkerService](worker-service.md#adding-telemetry-processors)를 사용하여 작성한 앱의 경우, 표시된 것처럼 `IServiceCollection`에서 `AddApplicationInsightsTelemetryProcessor` 확장 메서드를 사용하여 새로운 원격 분석 프로세서를 추가합니다. 이 메서드는 `Startup.cs` 클래스의 `ConfigureServices` 메서드에서 호출됩니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>예제 필터

#### <a name="synthetic-requests"></a>가상 요청

보트 및 웹 테스트를 필터링합니다. 메트릭 탐색기가 가상 소스를 필터링하는 옵션을 제공하지만 이 옵션은 SDK 자체에서 필터링하여 트래픽과 수집 크기를 감소시킵니다.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>실패한 인증

"401" 응답을 사용하여 요청을 필터링합니다.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>빠른 원격 종속성 호출을 필터링합니다.

느린 호출만 진단하려는 경우 빠른 호출을 필터링합니다.

> [!NOTE]
> 이 필터링은 여러분이 포털에서 참조하는 통계를 왜곡시킵니다.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>종속성 문제 진단

[이 블로그](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) 에서는 종속성으로 정규 ping을 자동으로 전송하여 종속성 문제를 진단하는 프로젝트에 대해 설명합니다.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript 웹 애플리케이션

**ITelemetryInitializer를 사용하여 필터링**

1. 원격 분석 이니셜라이저 콜백 함수를 만듭니다. 콜백 함수는 처리 중인 이벤트인 `ITelemetryItem`을 매개 변수로 사용합니다. 이 콜백에서 `false`를 반환하면 원격 분석 항목이 필터링됩니다.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. 원격 분석 이니셜라이저 콜백을 추가합니다.

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>속성 추가/수정: ITelemetryInitializer

원격 분석 이니셜라이저를 사용하여 추가 정보로 원격 분석을 보강하거나 표준 원격 분석 모듈에 의해 설정된 원격 분석 속성을 재정의할 수 있습니다.

예를 들어, 웹 패키지에 대한 Application Insights는 HTTP 요청에 대한 원격 분석을 수집합니다. 기본적으로 응답 코드가 400 이상인 모든 요청을 실패한 것으로 플래그 지정합니다. 그러나 400을 성공으로 처리하고 싶다면 성공 속성을 설정하는 원격 분석 이니셜라이저를 제공할 수 있습니다.

원격 분석 이니셜라이저를 제공하면 Track*() 메서드가 호출될 때마다 호출됩니다. 표준 원격 분석 모듈에 의해 호출되는 `Track()` 메서드가 여기에 포함됩니다. 규칙에 따라 해당 모듈은 이니셜라이저가 이미 설정한 속성은 설정하지 않습니다. 원격 분석 이니셜라이저는 원격 분석 프로세서가 호출되기 이전에 호출됩니다. 따라서 이니셜라이저가 수행하는 모든 강화는 프로세서에 표시됩니다.

**이니셜라이저 정의**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **앱: 이니셜라이저 로드**

ApplicationInsights.config에서:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

또는, 코드에서 이니셜라이저를 인스턴스화할 수 있습니다(예: Global.aspx.cs).

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[이 샘플](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)에 대해 자세히 알아봅니다.

ASP.NET **Core/Worker 서비스 앱: 이니셜라이저 로드**

> [!NOTE]
> `ApplicationInsights.config` 또는 `TelemetryConfiguration.Active`를 사용하여 이니셜라이저를 추가하는 것은 ASP.NET Core 애플리케이션 또는 Microsoft.ApplicationInsights.WorkerService SDK를 사용하는 경우에는 유효하지 않습니다.

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 또는 [WorkerService](worker-service.md#adding-telemetryinitializers)를 사용하여 작성한 앱의 경우, 표시된 것처럼 종속성 수집 컨테이너에 추가함으로써 새로운 원격 분석 이니셜라이저를 추가합니다. 이 작업은 `Startup.ConfigureServices` 메서드에서 수행됩니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript 원격 분석 이니셜라이저
*JavaScript*

코드 조각 onInit 콜백을 사용 하 여 원격 분석 이니셜라이저를 삽입 합니다.

```html
<script type="text/javascript">
!function(T,l,y){<!-- Removed the Snippet code for brevity -->}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",
crossOrigin: "anonymous",
onInit: function (sdk) {
  sdk.addTelemetryInitializer(function (envelope) {
    envelope.data.someField = 'This item passed through my telemetry initializer';
  });
}, // Once the application insights instance has loaded and initialized this method will be called
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY"
}});
</script>
```

원격 분석 항목에서 사용할 수 있는 사용자 지정이 아닌 속성의 요약은 [Application Insights 데이터 모델 내보내기](./export-data-model.md)를 참조하세요.

이니셜라이저를 원하는 수만큼 추가할 수 있습니다. 추가된 순서대로 호출됩니다.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python 원격 분석 프로세서

OpenCensus Python의 원격 분석 프로세서는 내보내기 전에 단순히 원격 분석을 처리하기 위해 호출되는 콜백 함수입니다. 콜백 함수는 [봉투](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) 데이터 형식을 매개 변수로 허용해야 합니다. 원격 분석을 내보내지 않도록 필터링하려면 콜백 함수가 `False`를 반환하는지 확인합니다. [GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)의 봉투에서 Azure Monitor 데이터 형식에 대한 스키마를 볼 수 있습니다.

> [!NOTE]
> `tags` 필드의 `ai.cloud.role` 특성을 변경하여 `cloud_RoleName`을 수정할 수 있습니다.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
원하는 만큼 프로세서를 추가할 수 있습니다. 추가된 순서대로 호출됩니다. 한 프로세서가 예외를 throw해도 다음 프로세서에 영향을 주지 않습니다.

### <a name="example-telemetryinitializers"></a>TelemetryInitializers 예제

#### <a name="add-a-custom-property"></a>사용자 지정 속성 추가

다음 샘플 이니셜라이저는 추적된 모든 원격 분석에 사용자 지정 속성을 추가합니다.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>클라우드 역할 이름 추가

다음 샘플 이니셜라이저는 추적된 모든 원격 분석에 클라우드 역할 이름을 설정합니다.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>HttpContext에서 정보 추가

다음 샘플 이니셜라이저는 [`HttpContext`](/aspnet/core/fundamentals/http-context)에서 데이터를 읽고 `RequestTelemetry` 인스턴스에 추가합니다. `IHttpContextAccessor`는 생성자 종속성 주입을 통해 자동으로 제공됩니다.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 및 ITelemetryInitializer

원격 분석 프로세서 및 원격 분석 이니셜라이저 간의 차이는 무엇인가요?

* 두 가지로 수행할 수 있는 작업에는 약간의 중복이 있습니다. 두 가지 모두 원격 분석의 속성을 추가하거나 수정하는 데 사용할 수 있지만, 해당 목적이라면 이니셜라이저 사용을 권장합니다.
* 원격 분석 이니셜라이저는 원격 분석 프로세서보다 항상 먼저 실행됩니다.
* 원격 분석 이니셜라이저는 두 번 이상 호출될 수 있습니다. 규칙에 따라 이미 설정된 속성은 설정하지 않습니다.
* 원격 분석 프로세서를 사용하면 원격 분석 항목을 완전히 바꾸거나 버릴 수 있습니다.
* 등록된 모든 원격 분석 이니셜라이저는 모든 원격 분석 항목에 대한 호출이 보장됩니다. 원격 분석 프로세서의 경우 SDK가 첫 번째 원격 분석 프로세서 호출을 보장합니다. 나머지 프로세서가 호출되는지 여부는 이전 원격 분석 프로세서에 의해 결정됩니다.
* 추가 속성으로 원격 분석을 보강하거나 기존 속성을 재정의하려면 원격 분석 이니셜라이저를 사용합니다. 원격 분석을 필터링하려면 원격 분석 프로세서를 사용합니다.

> [!NOTE]
> JavaScript에는 [ITelemetryInitializer를 사용 하 여 이벤트를 필터링](#javascript-web-applications) 할 수 있는 원격 분석 이니셜라이저가 있습니다.

## <a name="troubleshoot-applicationinsightsconfig"></a>ApplicationInsights.config 문제 해결

* 정규화된 형식 이름 및 어셈블리 이름이 올바른지 확인합니다.
* applicationinsights.config 파일이 출력 디렉터리에 있고 최근 변경 내용을 포함하는지 확인합니다.

## <a name="reference-docs"></a>참조 문서

* [API 개요](./api-custom-events-metrics.md)
* [ASP.NET 참조](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK 코드

* [ASP.NET 핵심 SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>다음 단계
* [검색 이벤트 및 로그](./diagnostic-search.md)
* [샘플링](./sampling.md)
* [문제 해결](../faq.yml)

