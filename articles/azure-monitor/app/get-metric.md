---
title: Azure Monitor Application Insights의 Get-Metric
description: GetMetric() 호출을 효과적으로 사용하여 Azure Monitor Application Insights로 .NET 및 .NET Core 애플리케이션에 대해 로컬로 사전 집계된 메트릭을 캡처하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0df826e90cff575e2e7ee3c5a55b8b2c3a6affb2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269887"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 및 .NET Core의 사용자 지정 메트릭 컬렉션

Azure Monitor Application Insights .NET 및 .NET Core SDK에는 사용자 지정 메트릭을 수집하는 두 가지 메서드로 `TrackMetric()`과 `GetMetric()`이 있습니다. 이러한 두 방법의 주요 차이점은 로컬 집계입니다. `GetMetric()`에는 사전 집계가 있는 반면 `TrackMetric()`에는 사전 집계가 없습니다. 권장되는 방법은 집계를 사용하는 것입니다. 따라서 `TrackMetric()`은 더 이상 사용자 지정 메트릭을 수집하는 기본 방법이 아닙니다. 이 문서에서는 GetMetric() 메서드를 사용하는 방법과 그 작동 원리에 대해 설명합니다.

## <a name="pre-aggregating-vs-non-pre-aggregating-api"></a>사전 집계 API와 비 집계 API 비교

`TrackMetric()`은 메트릭을 나타내는 원시 원격 분석 데이터를 보냅니다. 각 값에 대해 단일 원격 분석 데이터를 보내는 것은 비효율적입니다. `TrackMetric()`은 모든 `TrackMetric(item)`이 원격 분석 이니셜라이저와 프로세서의 전체 SDK 파이프라인을 거치므로 성능 측면에서도 비효율적입니다. `TrackMetric()`과 달리 `GetMetric()`은 로컬 사전 집계를 처리한 다음, 1분의 고정 간격으로 집계된 요약 메트릭만 제출합니다. 따라서 1분마다 모니터링하는 스토리지 및 네트워크 트래픽 비용만으로 초 또는 밀리초 수준에서 일부 사용자 지정 메트릭을 면밀하게 모니터링할 수 있습니다. 또한 집계된 메트릭에 대해 전송되어야 하는 총 원격 분석 항목 수가 크게 감소하기 때문에 제한 발생 위험이 크게 줄어듭니다.

Application Insights에서 `TrackMetric()` 및 `GetMetric()`을 통해 수집된 사용자 지정 메트릭은 [샘플링](./sampling.md) 대상이 아닙니다. 중요한 메트릭을 샘플링하면 해당 메트릭을 기반으로 구축했을 수 있는 경고가 불안정해지는 시나리오가 발생할 수 있습니다. 사용자 지정 메트릭을 샘플링하지 않으면 경고 임계값 위반 시 경고가 발생함을 확신할 수 있습니다.  그러나 사용자 지정 메트릭이 샘플링되지 않으므로 몇 가지 잠재적인 문제가 있습니다.

1초마다 또는 더 세분화된 간격으로 메트릭의 추세를 추적해야 하는 경우 다음과 같은 결과가 나타날 수 있습니다.

- 데이터 스토리지 비용 증가. Azure Monitor로 보내는 데이터 양과 관련된 비용이 있습니다. 더 많은 데이터를 보낼수록 전체 모니터링 비용이 증가합니다.
- 네트워크 트래픽/성능 오버헤드 증가. 일부 시나리오에서는 여기에 금전적 비용과 애플리케이션 성능 비용이 모두 포함될 수 있습니다.
- 수집 제한의 위험. 앱이 짧은 시간 간격으로 매우 높은 비율의 원격 분석을 전송할 때 Azure Monitor 서비스는 데이터 요소를 삭제("제한")합니다.

샘플링과 마찬가지로 경고를 트리거하는 조건이 로컬에서 발생한 다음, 너무 많은 데이터 전송으로 인해 수집 엔드포인트에서 삭제될 수 있기 때문에 제한이 누락된 경고로 이어질 수 있다는 점에서 제한은 특히 중요합니다. 바로 이러한 이유로 자체 로컬 집계 논리를 구현하지 않은 경우 .NET 및 .NET Core에 `TrackMetric()`을 사용하지 않는 것이 좋습니다. 지정된 기간 동안 이벤트가 발생하는 모든 인스턴스를 추적하려는 경우 [`TrackEvent()`](./api-custom-events-metrics.md#trackevent)가 더 적합할 수 있습니다. 사용자 지정 메트릭과 달리 사용자 지정 이벤트는 샘플링 대상입니다. 물론 로컬 사전 집계를 직접 작성하지 않고도 `TrackMetric()`을 계속 사용할 수 있습니다. 그러나 이러한 경우 문제에 대해 알고 있어야 합니다.

요약하자면 `GetMetric()`은 사전 집계를 수행하기 때문에 권장되는 방법입니다. 모든 Track() 호출의 값을 누적하고 1분마다 요약/집계를 보냅니다. 이렇게 하면 모든 관련 정보를 수집하는 동안 더 적은 데이터 요소를 보냄으로써 비용 및 성능 오버헤드를 상당히 줄일 수 있습니다.

> [!NOTE]
> .NET 및 .NET Core SDK만 GetMetric() 메서드를 포함합니다. Java를 사용하는 경우 [Micrometer를 사용하여 사용자 지정 메트릭 보내기](./java-in-process-agent.md#send-custom-metrics-by-using-micrometer)를 참조하세요. JavaScript 및 Node.js의 경우 `TrackMetric()`을 계속 사용할 수 있지만 이전 섹션에서 설명한 주의 사항을 염두에 두어야 합니다. Python의 경우 [OpenCensus.stats](./opencensus-python.md#metrics)를 사용하여 사용자 지정 메트릭을 보낼 수 있지만 메트릭 구현은 다릅니다.

## <a name="getting-started-with-getmetric"></a>GetMetric 시작

이 예에서는 기본 .NET Core 3.1 작업자 서비스 애플리케이션을 사용합니다. 이러한 예에 사용된 테스트 환경을 정확하게 복제하려면 [작업자 서비스 모니터링 문서](./worker-service.md#net-core-30-worker-service-application)의 1~6단계에 따라 기본 작업자 서비스 프로젝트 템플릿에 Application Insights를 추가합니다. 이러한 개념은 웹 앱 및 콘솔 앱을 포함하여 SDK를 사용할 수 있는 모든 일반 애플리케이션에 적용됩니다.

### <a name="sending-metrics"></a>메트릭 보내기

`worker.cs` 파일의 내용을 다음 코드로 바꿉니다.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

위의 코드를 실행하고 Visual Studio 출력 창이나 Telerik의 Fiddler와 같은 도구를 통해 전송되는 원격 분석 데이터를 살펴보면 원격 분석이 전송되지 않고 while 루프가 반복적으로 실행됨을 확인할 수 있습니다. 그런 다음, 단일 원격 분석 항목이 60초 표시 근처에서 전송됩니다. 테스트의 경우 다음과 같습니다.

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

이 단일 원격 분석 항목은 41개의 고유 메트릭 측정의 집계값을 나타냅니다. 같은 값을 반복해서 보냈기 때문에 동일한 *최대(max)* 값과 *최소(min)* 값이 동일하며 *표준 편차(stDev)* 는 0입니다. *value* 속성은 집계된 모든 개별 값의 합계를 나타냅니다.

> [!NOTE]
> GetMetric은 마지막 값(예: "gauge") 추적 또는 히스토그램/분포 추적을 지원하지 않습니다.

로그(분석) 환경에서 Application Insights 리소스를 검사하는 경우 이 개별 원격 분석 항목은 다음과 같이 표시됩니다.

![Log Analytics 쿼리 보기](./media/get-metric/log-analytics.png)

> [!NOTE]
> 원시 원격 측정 항목에는 일단 수집된 명시적 합계 속성/필드가 포함되어 있지 않아 사용자를 위해 하나 만듭니다. 이 경우 `value` 및 `valueSum` 속성이 모두 동일한 작업을 나타냅니다.

포털의 [_메트릭_](../essentials/metrics-charts.md) 섹션에서 사용자 지정 메트릭 원격 분석에 액세스할 수도 있습니다. 둘 다 [로그 기반 및 사용자 지정 메트릭](pre-aggregated-metrics-log-metrics.md)으로 액세스할 수 있습니다. 아래 스크린샷은 로그 기반의 예입니다. ![메트릭 탐색기 보기](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>높은 처리량 사용을 위한 캐싱 메트릭 참조

경우에 따라 메트릭 값은 매우 자주 관찰됩니다. 예를 들어 초당 500개의 요청을 처리하는 고 처리량 서비스는 각 요청에 대해 20개의 원격 측정 메트릭을 내보낼 수 있습니다. 즉, 초당 1만 개의 값을 추적합니다. 이렇게 처리량이 높은 시나리오에서는 사용자가 일부 조회를 피하여 SDK를 지원해야 할 수 있습니다.

예를 들어 이 경우 위의 예에서는 메트릭 "ComputersSold"에 대한 핸들 조회를 수행한 후 관찰된 값 42를 추적했습니다. 대신 여러 추적 호출에 대해 핸들이 캐싱될 수 있습니다.

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

메트릭 핸들을 캐싱하는 것 외에도 위의 예에서는 루프가 더 자주 실행되어 772개의 `TrackValue()` 호출이 발생하도록 `Task.Delay`를 50밀리초로 줄였습니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭

이전 섹션의 예는 0차원 메트릭을 보여줍니다. 메트릭은 다차원일 수도 있습니다. 현재 최대 10개의 차원을 지원합니다.

 다음은 1차원 메트릭을 만드는 방법의 예입니다.

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

이 코드를 60초 이상 실행하면 각각 3개의 폼 팩터 중 하나의 집계를 나타내는 3개의 고유 원격 분석 항목이 Azure로 전송됩니다. 이전과 마찬가지로 로그(분석) 보기에서 다음을 확인할 수 있습니다.

![다차원 메트릭의 로그 분석 보기](./media/get-metric/log-analytics-multi-dimensional.png)

메트릭 탐색기 환경에서도 마찬가지입니다.

![사용자 지정 메트릭](./media/get-metric/custom-metrics.png)

그러나 새 사용자 지정 차원에 따라 메트릭을 분할하거나 메트릭 보기를 사용하여 사용자 지정 차원을 볼 수 없습니다.

![분할 지원](./media/get-metric/splitting-support.png)

기본적으로 메트릭 탐색기 환경 내의 다차원 메트릭은 Application Insights 리소스에서 설정되지 않습니다.

### <a name="enable-multi-dimensional-metrics"></a>다차원 메트릭 사용

Application Insights 리소스에 대해 다차원 메트릭을 사용하려면 **사용량 및 예상 비용** > **사용자 지정 메트릭** > **사용자 지정 메트릭 차원의 경고를 사용하도록 설정** > **확인** 을 선택합니다. 이에 대한 자세한 내용은 [여기](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)에서 확인할 수 있습니다.

이러한 변경을 수행하고 새 다차원 원격 분석 데이터를 보내면 **분할을 적용** 할 수 있습니다.

> [!NOTE]
> 포털에서 기능이 설정된 후 새로 전송된 메트릭만 차원이 저장됩니다.

![분할 적용](./media/get-metric/apply-splitting.png)

그리고 각 _FormFactor_ 차원에 대한 메트릭 집계를 확인합니다.

![폼 팩터](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>4개 이상의 차원이 있는 경우 MetricIdentifier를 사용하는 방법

현재 10개의 차원이 지원되지만 4개 이상의 차원이 있는 경우 `MetricIdentifier`를 사용해야 합니다.

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>사용자 지정 메트릭 구성

메트릭 구성을 변경하려면 메트릭이 초기화되는 위치에서 이 작업을 수행해야 합니다.

### <a name="special-dimension-names"></a>특수 차원 이름

메트릭은 액세스에 사용되는 `TelemetryClient`의 원격 분석 컨텍스트를 사용하지 않습니다. `MetricDimensionNames` 클래스의 상수로 사용할 수 있는 특수 차원 이름이 이 제한에 대한 최상의 해결 방법입니다.

아래 "특수 작업 요청 크기" 메트릭에 의해 전송된 메트릭 집계의 `Context.Operation.Name`은 "특수 작업"으로 설정되지 **않습니다**. `TrackMetric()` 또는 다른 TrackXXX()의 `OperationName`은 "특수 작업"으로 올바르게 설정됩니다.

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

이 경우 `TelemetryContext` 값을 지정하기 위해 `MetricDimensionNames` 클스에 나열된 특수 차원 이름을 사용합니다.

예를 들어 다음 문에서 생성된 메트릭 집계가 Application Insights 클라우드 엔드포인트로 전송되면 해당 `Context.Operation.Name` 데이터 필드가 "특수 작업"으로 설정됩니다.

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

이 특수 차원의 값은 `TelemetryContext`에 복사되고 'normal' 차원으로 사용되지 않습니다. 표준 메트릭 탐색을 위해 작업 차원도 유지하려는 경우 해당 용도로 별도의 차원을 만들어야 합니다.

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>차원 및 시계열 제한

 원격 분석 하위 시스템이 실수로 리소스를 다 사용하지 않도록 메트릭당 최대 데이터 계열 수를 제어할 수 있습니다. 기본 한도는 메트릭당 데이터 계열 총 1,000개 이하이며 차원당 값 100개 이하입니다.

 차원 및 시계열 제한과 관련하여 `Metric.TrackValue(..)`로 한도가 준수되는지 확인합니다. 한도에 이미 도달한 경우 `Metric.TrackValue(..)`는 "False"를 반환하고 값은 추적되지 않습니다. 그렇지 않은 경우 "True"를 반환합니다. 이는 메트릭 데이터가 사용자 입력에서 시작되는 경우 유용합니다.

`MetricConfiguration` 생성자는 각 메트릭 내에서 서로 다른 계열을 관리하는 방법에 대한 몇 가지 옵션과 메트릭의 각 계열에 대한 집계 동작을 지정하는 `IMetricSeriesConfiguration`을 구현하는 클래스의 개체를 사용합니다.

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit`은 메트릭에 포함할 수 있는 최대 데이터 시계열 수입니다. 이 한도에 도달하면 일반적으로 새 시리즈를 생성하는 `TrackValue()`에 대한 호출은 false를 반환합니다.
* `valuesPerDimensionLimit`은 비슷한 방식으로 차원당 고유 값 수를 제한합니다.
* `restrictToUInt32Values`는 음이 아닌 정수 값만 추적해야 하는지 여부를 결정합니다.

다음은 한도를 초과했는지 확인하기 위해 메시지를 보내는 방법의 예입니다.

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>다음 단계

* 작업자 서비스 애플리케이션 모니터링에 대해 [자세히 알아보세요](./worker-service.md).
* [로그 기반 및 사전 집계 메트릭](./pre-aggregated-metrics-log-metrics.md)에 대한 자세한 내용
* [메트릭 탐색기](../essentials/metrics-getting-started.md)
* [ASP.NET Core 애플리케이션](asp-net-core.md)에 Application Insights를 사용하도록 설정하는 방법
* [ASP.NET 애플리케이션](asp-net.md)에 Application Insights를 사용하도록 설정하는 방법
