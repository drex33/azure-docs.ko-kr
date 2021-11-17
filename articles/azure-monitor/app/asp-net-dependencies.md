---
title: Azure Application Insights의 종속성 추적 | Microsoft Docs
description: Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 애플리케이션의 종속성 호출을 모니터링합니다.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a2052b4b4d5822d583101d27e873bf19ceeca49c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486127"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure Application Insights에서 종속성 추적 

*종속성* 은 애플리케이션에서 호출하는 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. [Application Insights](./app-insights-overview.md)는 실패 여부와 상관없이 종속성 호출의 시간 및 속성의 이름과 같은 추가 정보를 측정합니다. 특정 종속성 호출을 조사하고 요청 및 예외와의 상관 관계를 지정할 수 있습니다.

## <a name="automatically-tracked-dependencies"></a>자동으로 추적되는 종속성

.NET 및 .NET Core용 Application Insights SDK는 종속성을 자동으로 수집하는 원격 분석 모듈인 `DependencyTrackingTelemetryModule`과 함께 제공됩니다. 이 종속성 컬렉션은 연결된 공식 문서에 따라 구성된 경우 [ASP.NET](./asp-net.md) 및 [ASP.NET Core](./asp-net-core.md) 애플리케이션에 대해 자동으로 사용하도록 설정됩니다. `DependencyTrackingTelemetryModule`은 [이](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet 패키지로 제공되며 NuGet 패키지(`Microsoft.ApplicationInsights.Web` 또는 `Microsoft.ApplicationInsights.AspNetCore`) 중 하나를 사용하는 경우 자동으로 가져옵니다.

 `DependencyTrackingTelemetryModule`은 현재 다음 종속성을 자동으로 추적합니다.

|종속성 |세부 정보|
|---------------|-------|
|Http/Https | 로컬 또는 원격 http/https 호출 |
|WCF 호출| Http 기반 바인딩을 사용하는 경우에만 자동으로 추적됩니다.|
|SQL | `SqlClient`를 사용한 호출입니다. SQL 쿼리 캡처는 [이](#advanced-sql-tracking-to-get-full-sql-query) 항목을 참조하세요.  |
|[Azure Storage(Blob, 테이블, 큐)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure Storage 클라이언트를 사용한 호출입니다. |
|[EventHub 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 버전 이상. |
|[ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 버전 이상. |
|Azure Cosmos DB | HTTP/HTTPS를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다. |

종속성이 누락되었거나 다른 SDK를 사용하는 경우 [자동 수집된 종속성](./auto-collect-dependencies.md) 목록에 들어 있는지 확인합니다. 종속성이 아래 목록에 없으면 [추적 종속성 호출](./api-custom-events-metrics.md#trackdependency)을 사용하여 수동으로 추적할 수 있습니다.

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>콘솔 앱에서 자동 종속성 추적 설정

.NET 콘솔 앱에서 종속성을 자동으로 추적하려면 NuGet 패키지 `Microsoft.ApplicationInsights.DependencyCollector`를 설치하고 다음과 같이 `DependencyTrackingTelemetryModule`을 초기화합니다.

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

.NET Core 콘솔 앱의 경우 TelemetryConfiguration.Active가 사용되지 않습니다. [작업자 서비스 설명서](./worker-service.md) 및 [ASP.NET Core 모니터링 설명서](./asp-net-core.md)의 지침을 참조하세요.

### <a name="how-automatic-dependency-monitoring-works"></a>자동 종속성 모니터링은 어떻게 작동하나요?

종속성은 다음 방법 중 하나를 사용하여 자동으로 수집됩니다.

* 선택 메서드 주위에서는 바이트 코드 계측을 사용합니다. (StatusMonitor 또는 Azure 웹앱 확장에서의 InstrumentationEngine)
* EventSource 콜백
* DiagnosticSource 콜백(최신의 .NET/.NET Core SDK)

## <a name="manually-tracking-dependencies"></a>수동으로 종속성 추적

다음은 자동으로 수집되지 않으므로 수동으로 추적해야 하는 종속성의 몇 가지 예입니다.

* Azure Cosmos DB는 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking)를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다.
* Redis

SDK에서 자동으로 수집되지 않는 종속성의 경우 표준 자동 컬렉션 모듈에서 사용되는 [TrackDependency API](api-custom-events-metrics.md#trackdependency)를 사용하여 수동으로 추적할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

또는 `TelemetryClient`는 [다음](custom-operations-tracking.md#outgoing-dependencies-tracking)과 같이 종속성을 수동으로 추적하는 데 사용할 수 있는 확장 메서드 `StartOperation` 및 `StopOperation`을 제공합니다.

표준 종속성 추적 모듈을 해제하려면 ASP.NET 애플리케이션에 대한 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서 DependencyTrackingTelemetryModule에 대한 참조를 제거합니다. ASP.NET Core 애플리케이션의 경우 [여기](asp-net-core.md#configuring-or-removing-default-telemetrymodules)에 있는 지침을 따르세요.

## <a name="tracking-ajax-calls-from-web-pages"></a>웹 페이지에서 AJAX 호출 추적

웹 페이지의 경우 Application Insights JavaScript SDK는 AJAX 호출을 종속성으로 자동 수집합니다.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>전체 SQL 쿼리를 가져오기 위한 고급 SQL 추적

> [!NOTE]
> Azure Functions에는 SQL 텍스트 컬렉션을 사용하도록 설정하는 별도의 설정이 필요합니다. [host.json](../../azure-functions/functions-host-json.md#applicationinsights)에서는 `"EnableDependencyTracking": true,`이 설정되며 `applicationInsights`에서는 `"DependencyTrackingOptions": { "enableSqlCommandTextInstrumentation": true }`가 설정됩니다.

SQL 호출의 경우 서버 및 데이터베이스의 이름은 항상 수집되고 수집된 `DependencyTelemetry`의 이름으로 저장됩니다. 전체 SQL 쿼리 텍스트를 포함할 수 있는 'data'라는 추가 필드가 있습니다.

ASP.NET Core 애플리케이션의 경우 이제 다음을 사용하여 SQL 텍스트 컬렉션을 옵트인(opt in)해야 합니다.
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

ASP.NET 애플리케이션의 경우, 계측 엔진을 사용하거나 System.Data.SqlClient 라이브러리 대신 [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 패키지를 사용해야 하는 바이트 코드 계측을 통해 전체 SQL 쿼리 텍스트가 수집됩니다. 전체 SQL 쿼리 컬렉션을 사용하도록 설정하는 플랫폼별 단계는 아래에 설명되어 있습니다.

| 플랫폼 | 전체 SQL 쿼리를 가져오는 데 필요한 단계 |
| --- | --- |
| Azure 웹앱 |웹앱 제어판에서 [Application Insights 블레이드를 열고](../../azure-monitor/app/azure-web-apps.md) .NET에서 SQL 명령을 사용하도록 설정합니다. |
| IIS 서버(Azure VM, 온-프레미스 등) | [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 패키지를 사용하거나 상태 모니터 PowerShell 모듈을 사용하여 [계측 엔진을 설치](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine)하고 IIS를 다시 시작합니다. |
| Azure 클라우드 서비스 | [StatusMonitor를 설치하기 위한 시작 작업](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) 추가 <br> 애플리케이션은 [ASP.NET](./asp-net.md) 또는 [ASP.NET Core 애플리케이션](./asp-net-core.md)에 대한 NuGet 패키지를 설치하여 빌드 시에 ApplicationInsights SDK로 온보딩됩니다. |
| IIS Express | [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 패키지를 사용합니다.
| Azure 웹 작업 | [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 패키지를 사용합니다.

위의 플랫폼별 단계 외에도 applicationInsights.config 파일을 다음과 같이 수정하여 **SQL 명령 컬렉션을 사용하도록 명시적으로 옵트인해야 합니다**.

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

위의 경우, 계측 엔진의 유효성을 검사하는 올바른 방법은 수집된 `DependencyTelemetry`의 SDK 버전이 'rddp'인지 확인하는 것입니다. 'rdddsd' 또는 'rddf'는 종속성이 DiagnosticSource 또는 EventSource 콜백을 통해 수집되므로 전체 SQL 쿼리가 캡처되지 않습니다.

## <a name="where-to-find-dependency-data"></a>종속성 데이터를 찾을 수 있는 위치

* [애플리케이션 맵](app-map.md)은 앱과 인접 구성 요소 간의 종속성을 시각화합니다.
* [트랜잭션 진단](transaction-diagnostics.md)은 상관 관계가 지정된 통합 서버 데이터를 표시합니다.
* [브라우저 탭](javascript.md): 사용자 브라우저에서의 AJAX 호출을 표시합니다.
* 느리거나 실패한 요청 클릭: 해당 종속성 호출을 확인합니다.
* [분석](#logs-analytics): 종속성 데이터를 쿼리하는 데 사용됩니다.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> 느린 요청 진단

각 요청 이벤트는 종속성 호출, 예외 및 기타 앱에서 요청을 처리하는 동안 추적된 이벤트와 연결됩니다. 따라서 일부 요청이 잘못 수행되는 경우 종속성의 응답 속도가 느리기 때문인지 여부를 확인할 수 있습니다.

### <a name="tracing-from-requests-to-dependencies"></a>요청에서 종속성까지 추적

**성능** 탭을 열고 작업 옆의 위쪽에 있는 **종속성** 탭으로 이동합니다.

아래에서 **종속성 이름** 을 클릭합니다. 종속성을 선택한 후에는 해당 종속성의 기간 분포 그래프가 오른쪽에 표시됩니다.

![성능 탭의 위쪽에서 종속성 탭을 클릭하고 차트에서 종속성 이름을 클릭합니다.](./media/asp-net-dependencies/2-perf-dependencies.png)

오른쪽 아래에 있는 파란색 **샘플** 단추를 클릭하고 샘플에서 엔드투엔드 트랜잭션 세부 정보를 확인합니다.

![샘플을 클릭하여 엔드투엔드 트랜잭션 세부 정보를 확인합니다.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>라이브 사이트 프로파일링

시간에 따른 위치를 알 수 없나요? [Application Insights Profiler](../../azure-monitor/app/profiler.md)는 라이브 사이트에 대한 HTTP 호출을 추적하고 가장 오래 걸린 코드의 함수를 표시합니다.

## <a name="failed-requests"></a>실패한 요청

실패한 요청은 종속성에 대한 실패한 호출과 연관이 있을 수도 있습니다.

왼쪽의 **오류** 탭으로 이동한 다음 위쪽의 **종속성** 탭을 클릭할 수 있습니다.

![실패한 요청 차트 클릭](./media/asp-net-dependencies/4-fail.png)

여기에서 실패한 종속성 수를 확인할 수 있습니다. 실패한 발생에 대한 자세한 정보를 보려면 아래쪽 테이블에서 종속성 이름을 클릭합니다. 오른쪽 아래에 있는 파란색 **종속성** 단추를 클릭하여 엔드투엔드 트랜잭션 세부 정보를 가져올 수 있습니다.

## <a name="logs-analytics"></a>로그(분석)

[Kusto 쿼리 언어](/azure/kusto/query/)에서 종속성을 추적할 수 있습니다. 다음은 몇 가지 예제입니다.

* 실패한 종속성 호출을 찾습니다.

``` Kusto

    dependencies | where success != "True" | take 10
```

* AJAX 호출을 찾습니다.

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* 요청과 연관된 종속성 호출을 찾습니다.

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 페이지 보기와 관련된 AJAX 호출을 찾습니다.

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*자동 종속성 수집기가 종속성에 대한 실패한 호출을 보고하는 방법*

* 실패한 종속성 호출의 'success' 필드는 False로 설정됩니다. `DependencyTrackingTelemetryModule`은 `ExceptionTelemetry`를 보고하지 않습니다. 종속성에 대한 전체 데이터 모델은 [여기](data-model-dependency-telemetry.md)에 설명되어 있습니다.

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*종속성 원격 분석에 대한 수집 대기 시간은 어떻게 계산하나요?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*종속성 호출이 시작된 시간은 어떻게 확인하나요?*

Log Analytics 쿼리 뷰 `timestamp`에서는 종속성 호출 응답이 수신된 직후에 발생하는 TrackDependency() 호출이 시작된 순간을 나타냅니다. 종속성 호출이 시작된 시간을 계산하려면 `timestamp`에서 종속성 호출의 기록된 `duration`을 뺍니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
모든 Application Insights SDK와 마찬가지로 종속성 컬렉션 모듈 또한 오픈 소스입니다. [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet)에서 코드를 읽고 참여하거나 문제를 보고합니다.

## <a name="next-steps"></a>다음 단계

* [예외](./asp-net-exceptions.md)
* [사용자 및 페이지 데이터](./javascript.md)
* [가용성](./monitor-web-app-availability.md)

