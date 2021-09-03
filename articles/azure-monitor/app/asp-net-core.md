---
title: ASP.NET Core 애플리케이션용 Azure Application Insights | Microsoft Docs
description: ASP.NET Core 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: f12385d5514d999785980622530861cd1f714ac3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528922"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 애플리케이션용 Application Insights

이 문서에서는 Application Insights를 [ASP.NET Core](/aspnet/core) 애플리케이션에 사용하도록 설정하는 방법에 대해 설명합니다. 이 문서의 지침을 완료하면 Application Insights에서 ASP.NET Core 애플리케이션으로부터 요청, 종속성, 예외, 성능 카운터, 하트비트 및 로그를 수집합니다.

여기서 사용하는 예제는 `netcoreapp3.0`을 대상으로 하는 [MVC 애플리케이션](/aspnet/core/tutorials/first-mvc-app)입니다. 이러한 지침은 모든 ASP.NET Core 애플리케이션에 적용할 수 있습니다. [Worker Service](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)를 사용하는 경우 [여기](./worker-service.md)의 지침을 사용하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

[ASP.NET Core용 Application Insights SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)는 실행되는 위치 또는 방법에 관계없이 애플리케이션을 모니터링할 수 있습니다. 애플리케이션이 실행 중이고 Azure에 네트워크로 연결되어 있는 경우 원격 분석을 수집할 수 있습니다. Application Insights 모니터링은 .NET Core를 지원하는 모든 위치에서 지원됩니다. 지원에서는 다음을 다룹니다.
* **운영 체제**: Windows, Linux 또는 Mac
* **호스팅 메서드**: In process 또는 out of process
* **배포 방법**: 프레임워크 종속 또는 자체 포함
* **웹 서버**: IIS(Internet Information Server) 또는 Kestrel
* **호스팅 플랫폼**: Azure App Service, Azure VM, Docker, AKS(Azure Kubernetes Service) 등의 Web Apps 기능
* **.NET Core 버전**: 공식적으로 [지원되는 모든 .NET Core 버전](https://dotnet.microsoft.com/download/dotnet-core)이며 미리 보기에는 없습니다.
* **IDE**: Visual Studio, Visual Studio Code 또는 명령줄

> [!NOTE]
> ASP.NET Core 3.1에는 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 이상이 필요합니다.

## <a name="prerequisites"></a>전제 조건

- 작동하는 ASP.NET Core 애플리케이션. ASP.NET Core 애플리케이션을 만들어야 하는 경우 이 [ASP.NET Core 자습서](/aspnet/core/getting-started/)를 따르세요.
- 유효한 Application Insights 계측 키. 이 키는 원격 분석을 Application Insights에 보내는 데 필요합니다. 계측 키를 가져오기 위해 새 Application Insights 리소스를 만들어야 하는 경우 [Application Insights 리소스 만들기](./create-new-resource.md)를 참조하세요.

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md?tabs=net)이 권장됩니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 **사용해야 합니다**. 연결 문자열은 원격 분석 데이터를 연결할 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.


## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Application Insights 서버 쪽 원격 분석 사용(Visual Studio 사용)

Mac용 Visual Studio의 경우 [수동 지침](#enable-application-insights-server-side-telemetry-no-visual-studio)을 사용합니다. 이 절차는 Windows 버전의 Visual Studio에서만 지원됩니다.

1. Visual Studio에서 새 프로젝트를 엽니다.

    > [!TIP]
    > Application Insights의 모든 변경 사항을 추적하려면 프로젝트에 대한 소스 제어를 설정할 수 있습니다. 설정하려면 **파일** > **소스 제어에 추가** 를 선택합니다.

2. **프로젝트** > **Application Insights 원격 분석 추가** 를 선택합니다.

3. **시작** 을 선택합니다. Visual Studio 버전에 따라 이 단추의 이름이 달라질 수 있습니다. 일부 이전 버전에서는 이 단추의 이름이 **무료 시작** 입니다.

4. 구독을 선택한 다음, **리소스** > **등록** 을 선택합니다.

5. Application Insights가 프로젝트에 추가되면 안정적인 최신 릴리스의 SDK를 사용하고 있는지 확인합니다. **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore** 로 차례로 이동합니다. 필요한 경우 **업데이트** 를 선택합니다.

     ![업데이트할 Application Insights 패키지를 선택할 수 있는 위치를 보여 주는 스크린샷](./media/asp-net-core/update-nuget-package.png)

6. 프로젝트를 소스 제어에 추가한 경우 **보기** > **팀 탐색기** > **변경 내용** 으로 이동합니다. Application Insights 원격 분석에서 변경한 내용에 대한 차이 보기를 확인하려면 각 파일을 선택할 수 있습니다.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Application Insights 서버 쪽 원격 분석 사용(Visual Studio 사용 안 함)

1. [ASP.NET Core용 Application Insights SDK NuGet 패키지](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)를 설치합니다. 항상 안정적인 최신 버전을 사용하는 것이 좋습니다. [오픈 소스 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)에서 SDK에 대한 전체 릴리스 정보를 확인합니다.

    다음 코드 샘플에서는 프로젝트의 `.csproj` 파일에 추가되는 변경 내용을 보여 줍니다.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.16.0" />
        </ItemGroup>
    ```

2. 다음 예제와 같이 `services.AddApplicationInsightsTelemetry();`를 `Startup` 클래스의 `ConfigureServices()` 메서드에 추가합니다.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 계측 키를 설정합니다.

    계측 키를 `AddApplicationInsightsTelemetry`에 대한 인수로 제공할 수 있지만 구성에서 계측 키를 지정하는 것이 좋습니다. 다음 코드 샘플에서는 `appsettings.json`에서 계측 키를 지정하는 방법을 보여 줍니다. 게시하는 동안 `appsettings.json`이 애플리케이션 루트 폴더에 복사되었는지 확인합니다.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    또는 다음 환경 변수 중 하나에서 계측 키를 지정합니다.

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    예를 들면 다음과 같습니다.

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY`는 일반적으로 [Azure Web Apps](./azure-web-apps.md?tabs=net)에서 사용되지만, 이 SDK를 지원하는 모든 위치에서도 사용할 수 있습니다. (코드리스 웹앱 모니터링을 수행하는 경우 연결 문자열을 사용하지 않으면 이 형식이 필요합니다.)

    이제 계측 키를 설정하는 대신 [연결 문자열](./sdk-connection-string.md?tabs=net)을 사용할 수도 있습니다.

    > [!NOTE]
    > 코드에 지정된 계측 키는 다른 옵션보다 우선 적용되는 `APPINSIGHTS_INSTRUMENTATIONKEY` 환경 변수보다 우선 적용됩니다.

### <a name="user-secrets-and-other-configuration-providers"></a>사용자 비밀 및 기타 구성 공급자

계측 키를 ASP.NET Core 사용자 비밀에 저장하거나 다른 구성 공급자에서 검색하려는 경우 `Microsoft.Extensions.Configuration.IConfiguration` 매개 변수를 사용하여 오버로드를 사용할 수 있습니다. 예들 들어 `services.AddApplicationInsightsTelemetry(Configuration);`입니다.
Microsoft.ApplicationInsights.AspNetCore 버전 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)부터 `services.AddApplicationInsightsTelemetry()`를 호출하면 애플리케이션의 `Microsoft.Extensions.Configuration.IConfiguration`에서 계측 키를 자동으로 읽습니다. `IConfiguration`을 명시적으로 제공할 필요는 없습니다.

## <a name="run-your-application"></a>애플리케이션 실행

애플리케이션을 실행하고 이에 대한 요청을 수행합니다. 이제 원격 분석이 Application Insights로 이동합니다. Application Insights SDK는 다음 원격 분석과 함께 애플리케이션에 대한 들어오는 웹 요청을 자동으로 수집합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭](./live-stream.md)은 Application Insights 모니터링이 올바르게 구성되었는지 여부를 빠르게 확인하는 데 사용할 수 있습니다. 원격 분석이 포털 및 분석에 표시되는 데 몇 분 정도 걸릴 수 있지만 라이브 메트릭은 실행 중인 프로세스의 CPU 사용량을 거의 실시간으로 표시합니다. 또한 요청, 종속성, 추적 등의 다른 원격 분석 데이터도 표시할 수 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

기본 구성은 `ILogger` `Warning` 로그 및 더 심각한 로그를 수집합니다. [이 구성을 사용자 지정](#how-do-i-customize-ilogger-logs-collection)할 수 있습니다.

### <a name="dependencies"></a>종속성

종속성 수집은 기본적으로 사용하도록 설정됩니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서는 자동으로 수집되는 종속성에 대해 설명하고, 수동 추적을 수행하는 단계도 포함하고 있습니다.

### <a name="performance-counters"></a>성능 카운터

ASP.NET Core의 [성능 카운터](./performance-counters.md)는 제한적으로 지원됩니다.

* SDK 버전 2.4.1 이상에서는 애플리케이션이 Azure Web Apps(Windows)에서 실행되는 경우 성능 카운터를 수집합니다.
* SDK 버전 2.7.1 이상에서는 애플리케이션이 Windows에서 실행되고 `NETSTANDARD2.0` 이상을 대상으로 하는 경우 성능 카운터를 수집합니다.
* .NET Framework를 대상으로 하는 애플리케이션의 경우 모든 버전의 SDK에서 성능 카운터를 지원합니다.
* SDK 버전 2.8.0 이상에서는 Linux의 cpu/메모리 카운터를 지원합니다. 다른 카운터는 Linux에서 지원되지 않습니다. Linux 및 기타 비 Windows 환경에서 시스템 카운터를 가져오는 권장 방법은 [EventCounters](#eventcounter)를 사용하는 것입니다.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`은 기본적으로 사용하도록 설정됩니다. 수집할 카운터 목록을 구성하는 방법을 알아보려면 [EventCounters 소개](eventcounters.md)를 참조하세요.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 애플리케이션에 대한 클라이언트 쪽 원격 분석 사용

위의 단계는 서버 쪽 원격 분석 수집을 시작하는 데 도움이 됩니다. 클라이언트 쪽 구성 요소가 애플리케이션에 있는 경우 다음 단계에 따라 [사용량 원격 분석](./usage-overview.md) 수집을 시작합니다.

1. `_ViewImports.cshtml`에서 다음 injection을 추가합니다.

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. `_Layout.cshtml`에서 `HtmlHelper`를 `<head>` 섹션 끝이지만 다른 스크립트 앞에 삽입합니다. 페이지에서 사용자 지정 JavaScript 원격 분석을 보고하려는 경우 다음 코드 조각 뒤에 삽입합니다.

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

`FullScript`를 사용하는 대신 ASP.NET Core용 Application Insights SDK 버전 2.14부터 `ScriptBody`를 사용할 수 있습니다. 콘텐츠 보안 정책을 설정하기 위해 `<script>` 태그를 제어해야 하는 경우 다음 예제를 사용합니다.

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

앞에서 참조한 `.cshtml` 파일 이름은 기본 MVC 애플리케이션 템플릿에서 가져온 것입니다. 궁극적으로 애플리케이션에 대한 클라이언트 쪽 모니터링을 적절하게 사용하려면, 모니터링할 애플리케이션에 대한 각 페이지의 `<head>` 섹션에 JavaScript 코드 조각이 표시되어야 합니다. 이 애플리케이션 템플릿에서 이를 수행하려면 JavaScript 코드 조각을 `_Layout.cshtml`에 추가합니다. 

`_Layout.cshtml`이 프로젝트에 포함되지 않은 경우에도 [클라이언트 쪽 모니터링](./website-monitoring.md)을 추가할 수 있습니다. 이렇게 하려면 앱 내 모든 페이지의 `<head>`를 제어하는 동일한 파일에 JavaScript 코드 조각을 추가합니다. 또는 코드 조각을 여러 페이지에 추가할 수 있지만, 이 솔루션은 유지 관리하기 어렵고 일반적으로 권장하지 않습니다.

> [!NOTE]
> JavaScript 삽입은 기본 구성 환경을 제공합니다. 계측 키 설정 이외의 [구성](./javascript.md#configuration)이 필요한 경우 위에 설명된 대로 자동 삽입을 제거하고 [JavaScript SDK](./javascript.md#adding-the-javascript-sdk)를 수동으로 추가해야 합니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 구성

ASP.NET Core용 Application Insights SDK를 사용자 지정하여 기본값 구성을 변경할 수 있습니다. Application Insights ASP.NET SDK 사용자는 `ApplicationInsights.config`를 사용하거나 `TelemetryConfiguration.Active`를 수정하여 구성을 변경하는 데 익숙할 수 있습니다. ASP.NET Core에서는 달리 지시하는 경우를 제외하고 `Startup.cs` 클래스의 `ConfigureServices()` 메서드에서 거의 모든 구성 변경을 수행합니다. 자세한 내용은 다음 섹션에서 설명합니다.

> [!NOTE]
> ASP.NET Core 애플리케이션에서 `TelemetryConfiguration.Active`를 수정하여 구성을 변경하는 것은 지원되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions 사용

몇 가지 일반적인 설정은 다음 예제와 같이 `ApplicationInsightsServiceOptions`를 `AddApplicationInsightsTelemetry`에 전달하여 수정할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

다음 표에는 `ApplicationInsightsServiceOptions` 설정의 전체 목록이 있습니다.

|설정 | 설명 | 기본값
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | `PerformanceCounterCollectionModule` 사용/사용 안 함 | true
|EnableRequestTrackingTelemetryModule   | `RequestTrackingTelemetryModule` 사용/사용 안 함 | true
|EnableEventCounterCollectionModule   | `EventCounterCollectionModule` 사용/사용 안 함 | true
|EnableDependencyTrackingTelemetryModule   | `DependencyTrackingTelemetryModule` 사용/사용 안 함 | true
|EnableAppServicesHeartbeatTelemetryModule  |  `AppServicesHeartbeatTelemetryModule` 사용/사용 안 함 | true
|EnableAzureInstanceMetadataTelemetryModule   |  `AzureInstanceMetadataTelemetryModule` 사용/사용 안 함 | true
|EnableQuickPulseMetricStream | LiveMetrics 기능 사용/사용 안 함 | true
|EnableAdaptiveSampling | 적응 샘플링 사용/사용 안 함 | true
|EnableHeartbeat | 하트비트 기능 사용/사용 안 함. 이는 런타임 관련 정보(예: .NET 버전, Azure 환경 정보, 해당하는 경우 기타 정보 등)와 함께 'HeartbeatState'라는 사용자 지정 메트릭을 주기적으로(기본값 15분) 보냅니다. | true
|AddAutoCollectedMetricExtractor | AutoCollectedMetrics 추출기 사용/사용 안 함. 이는 샘플링을 수행하기 전에 요청/종속성에 대해 미리 집계된 메트릭을 보내는 TelemetryProcessor입니다. | true
|RequestCollectionOptions.TrackExceptions | Request 수집 모듈에서 처리되지 않은 예외 추적에 대한 보고 사용/사용 안 함. | NETSTANDARD2.0인 경우 false이고(Exceptions가 ApplicationInsightsLoggerProvider를 통해 추적되기 때문), 그렇지 않은 경우 true입니다.
|EnableDiagnosticsTelemetryModule | `DiagnosticsTelemetryModule` 사용/사용 안 함. 이를 사용하지 않도록 설정하면 `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` 설정이 무시됩니다. | true

최신 목록은 [`ApplicationInsightsServiceOptions`의 구성 가능한 설정](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)을 참조하세요.

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150-and-later"></a>Microsoft.ApplicationInsights.AspNetCore SDK 2.15.0 이상에 대한 구성 권장 사항

Microsoft.ApplicationInsights.AspNetCore SDK 버전 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) 이상에서는 애플리케이션의 `IConfiguration` 인스턴스를 사용하는 **InstrumentationKey** 를 포함하여 `ApplicationInsightsServiceOptions`에서 사용 가능한 모든 설정을 구성하는 것이 좋습니다. 이러한 설정은 다음 예제와 같이 "ApplicationInsights" 섹션 아래에 있어야 합니다. appsettings.json의 다음 섹션에서는 계측 키를 구성하고, 적응 샘플링 및 성능 카운터 수집을 사용하지 않도록 설정합니다.

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

`services.AddApplicationInsightsTelemetry(aiOptions)`를 사용하는 경우 `Microsoft.Extensions.Configuration.IConfiguration`의 설정을 재정의합니다.

### <a name="sampling"></a>샘플링

ASP.NET Core용 Application Insights SDK는 고정 비율 및 적응 샘플링을 모두 지원합니다. 기본적으로 적응 샘플링이 사용됩니다.

자세한 내용은 [ASP.NET Core 애플리케이션에 대한 적응 샘플링 구성](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)을 참조하세요.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializer 추가

추가 정보로 원격 분석을 보강하려는 경우 [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)를 사용합니다.

다음 코드와 같이 새 `TelemetryInitializer`를 `DependencyInjection` 컨테이너에 추가합니다. SDK에서 `DependencyInjection` 컨테이너에 추가된 모든 `TelemetryInitializer`를 자동으로 선택합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();`는 간단한 이니셜라이저에서 작동합니다. 다른 경우에는 `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`이 필요합니다.
    
### <a name="removing-telemetryinitializers"></a>TelemetryInitializer 제거

원격 분석 이니셜라이저는 기본적으로 제공됩니다. 모든 또는 특정 원격 분석 이니셜라이저를 제거하려면 `AddApplicationInsightsTelemetry()`를 호출한 *후* 에 다음 샘플 코드를 사용합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

`IServiceCollection`에서 `AddApplicationInsightsTelemetryProcessor` 확장 메서드를 사용하여 사용자 지정 원격 분석 프로세서를 `TelemetryConfiguration`에 추가할 수 있습니다. 원격 분석 프로세서는 [고급 필터링 시나리오](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)에서 사용합니다. 다음 예제를 사용합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 TelemetryModule 구성 또는 제거

Application Insights는 원격 분석 모듈을 사용하여 사용자가 수동으로 추적할 필요 없이 특정 워크로드에 대한 유용한 원격 분석을 자동으로 수집합니다.

다음 자동 수집 모듈은 기본적으로 사용하도록 설정되어 있습니다. 이러한 자동 수집 모듈은 원격 분석을 자동으로 수집해야 합니다. 자동 수집 모듈을 사용하지 않도록 설정하거나 해당 기본 동작을 변경하도록 구성할 수 있습니다.

* `RequestTrackingTelemetryModule` - 들어오는 웹 요청에서 RequestTelemetry를 수집합니다.
* `DependencyTrackingTelemetryModule` - 나가는 http 호출 및 sql 호출에서 [DependencyTelemetry](./asp-net-dependencies.md)를 수집합니다.
* `PerformanceCollectorModule` - Windows PerformanceCounters를 수집합니다.
* `QuickPulseTelemetryModule` - 라이브 메트릭 포털에 표시할 원격 분석을 수집합니다.
* `AppServicesHeartbeatTelemetryModule` - 애플리케이션이 호스트되는 Azure App Service 환경에 대한 하트비트(사용자 지정 메트릭으로 보냄)를 수집합니다.
* `AzureInstanceMetadataTelemetryModule` - 애플리케이션이 호스트되는 Azure VM 환경에 대한 하트비트(사용자 지정 메트릭으로 전송됨)를 수집합니다.
* `EventCounterCollectionModule` - [EventCounters](eventcounters.md)를 수집합니다. 이 모듈은 새로운 기능이며, SDK 버전 2.8.0 이상에서 사용할 수 있습니다.

기본 `TelemetryModule`을 구성하려면 다음 예제와 같이 `IServiceCollection`에서 `ConfigureTelemetryModule<T>` 확장 메서드를 사용합니다.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

버전 2.12.2 이상에서 [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions)에는 기본 모듈을 비활성화하는 손쉬운 옵션이 포함되어 있습니다.

### <a name="configuring-a-telemetry-channel"></a>원격 분석 채널 구성

기본 [원격 분석 채널](./telemetry-channels.md)은 `ServerTelemetryChannel`입니다. 다음 예제에서는 이를 재정의하는 방법을 설명합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>동적으로 원격 분석 사용 안 함

원격 분석을 조건부로, 동적으로 사용하지 않도록 설정하려는 경우 코드의 어느 위치에서나 ASP.NET Core 종속성 삽입 컨테이너를 사용하여 `TelemetryConfiguration` 인스턴스를 확인하고 이 인스턴스에 `DisableTelemetry` 플래그를 설정할 수 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

앞의 코드 샘플은 Application Insights에 원격 분석을 보내는 것을 방지합니다. 자동 수집 모듈이 원격 분석을 수집하는 것을 방지하지는 않습니다. 특정 자동 수집 모듈을 제거하려는 경우 [원격 분석 모듈 제거](#configuring-or-removing-default-telemetrymodules)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights에서 ASP.NET Core 3.X를 지원하나요?

예. [ASP.NET Core용 Application Insights SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.8.0 이상으로 업데이트합니다. 이전 버전의 SDK는 ASP.NET Core 3.X를 지원하지 않습니다.

또한 [Visual Studio 기반 서버 쪽 원격 분석을 사용하도록 설정](#enable-application-insights-server-side-telemetry-visual-studio)하려는 경우 최신 버전의 Visual Studio 2019(16.3.0)로 업데이트하여 온보딩합니다. 이전 버전의 Visual Studio는 ASP.NET Core 3.X 앱에 대한 자동 온보딩을 지원하지 않습니다.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집되지 않는 원격 분석을 추적하려면 어떻게 해야 하나요?

생성자 삽입을 사용하여 `TelemetryClient`의 인스턴스를 가져오고, 이 인스턴스에 필요한 `TrackXXX()` 메서드를 호출합니다. ASP.NET Core 애플리케이션에서 새 `TelemetryClient` 또는 `TelemetryConfiguration` 인스턴스를 만들지 않는 것이 좋습니다. `TelemetryClient`의 싱글톤 인스턴스는 나머지 원격 분석과 `TelemetryConfiguration`을 공유하는 `DependencyInjection` 컨테이너에 이미 등록되어 있습니다. 새 `TelemetryClient` 인스턴스를 만드는 것은 나머지 원격 분석과 분리된 구성이 필요한 경우에만 권장됩니다.

다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적하는 방법을 보여 줍니다.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Application Insights의 사용자 지정 데이터 보고에 대한 자세한 내용은 [Application Insights 사용자 지정 메트릭 API 참조](./api-custom-events-metrics.md)를 참조하세요. [GetMetric API](./get-metric.md)를 사용하여 사용자 지정 메트릭을 Application Insights에 보내는 데 비슷한 방법을 사용할 수 있습니다.

### <a name="how-do-i-customize-ilogger-logs-collection"></a>ILogger 로그 수집을 사용자 지정하려면 어떻게 할까요?

기본적으로 `Warning` 로그 및 더 심각한 로그만 자동으로 캡처됩니다. 이 동작을 변경하려면 아래와 같이 `ApplicationInsights` 공급자에 대한 로깅 구성을 명시적으로 재정의합니다.
다음 구성을 사용하면 ApplicationInsights에서 `Information` 로그 및 더 심각한 로그를 모두 캡처할 수 있습니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

다음으로 인해 ApplicationInsights 공급자가 `Information` 로그를 캡처하지는 않는다는 것에 주의해야 합니다. 캡처하지 않는 이유는 SDK가 `ApplicationInsights`에 `Warning` 로그 및 더 심각한 로그만 캡처하도록 지시하는 기본 로깅 필터를 추가하기 때문입니다. ApplicationInsights는 명시적 재정의를 요구합니다.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

자세한 내용은 [ILogger 구성](ilogger.md#logging-level)을 참조하세요.

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>일부 Visual Studio 템플릿은 IWebHostBuilder에서 UseApplicationInsights() 확장 메서드를 사용하여 Application Insights를 사용하도록 설정했습니다. 이 사용법이 여전히 유효한가요?

`UseApplicationInsights()` 확장 메서드는 계속 지원되지만 Application Insights SDK 버전 2.8.0 이상에서는 더 이상 사용되지 않는 것으로 표시됩니다. 이는 SDK의 다음 주 버전에서 제거됩니다. Application Insights 원격 분석을 사용하도록 설정하려면, 일부 구성을 제어하는 오버로드를 제공하므로 `AddApplicationInsightsTelemetry()`를 사용하는 것이 좋습니다. 또한 ASP.NET Core 3.X 앱에서 `services.AddApplicationInsightsTelemetry()`는 Application Insights를 사용하도록 설정하는 유일한 방법입니다.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>내 ASP.NET Core 애플리케이션을 Web Apps에 배포하고 있습니다. Web Apps에서 Application Insights 확장을 계속 사용하도록 설정해야 하나요?

이 문서에서 보여 주듯이 SDK가 빌드 시간에 설치되는 경우 App Service 포털에서 [Application Insights 확장](./azure-web-apps.md)을 사용하도록 설정할 필요가 없습니다. 확장 버전이 설치되는 경우에도 SDK가 애플리케이션에 이미 추가된 것을 감지하면 해당 확장이 백오프됩니다. 확장에서 Application Insights를 사용하도록 설정하는 경우 SDK를 설치하고 업데이트할 필요가 없습니다. 그러나 이 문서의 지침에 따라 Application Insights를 사용하도록 설정하면 다음과 같은 이유로 유연성이 향상됩니다.

   * Application Insights 원격 분석이 다음에서 계속 작동합니다.
       * Windows, Linux 및 Mac을 포함한 모든 운영 체제
       * 자체 포함 또는 프레임워크 종속성을 포함한 모든 게시 모드
       * 전체 .NET Framework를 포함한 모든 대상 프레임워크
       * Web Apps, VM, Linux, 컨테이너, Azure Kubernetes Service 및 비 Azure 호스팅을 포함한 모든 호스팅 옵션
       * 미리 보기 버전을 포함한 모든 .NET Core 버전
   * Visual Studio에서 디버그할 때 원격 분석이 로컬로 표시됩니다.
   * `TrackXXX()` API를 사용하여 추가 사용자 지정 원격 분석을 추적할 수 있습니다.
   * 구성을 완전히 제어할 수 있습니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-azure-monitor-application-insights-agent-formerly-status-monitor-v2"></a>Azure Monitor Application Insights Agent(이전의 상태 모니터 v2)와 같은 도구를 사용하여 Application Insights 모니터링을 사용하도록 설정할 수 있나요?

아니요, [Azure Monitor Application Insights Agent](./status-monitor-v2-overview.md)는 현재 ASP.NET 4.x만 지원합니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>내 애플리케이션을 Linux에서 실행하는 경우 모든 기능이 지원되나요?

예. SDK에 대한 기능 지원은 다음과 같은 경우를 제외하고 모든 플랫폼에서 동일합니다.

* [성능 카운터](./performance-counters.md)가 Windows에서만 지원되므로 SDK는 Linux에서 [이벤트 카운터](./eventcounters.md)를 수집합니다. 대부분의 메트릭은 동일합니다.
* `ServerTelemetryChannel`을 기본적으로 사용하도록 설정하는 경우에도 애플리케이션이 Linux 또는 macOS에서 실행될 때 네트워크 문제가 있으면 채널에서 원격 분석을 일시적으로 유지하기 위해 로컬 스토리지 폴더를 자동으로 만들지 않습니다. 이 제한으로 인해 일시적인 네트워크 또는 서버 문제가 있으면 원격 분석이 손실됩니다. 이 문제를 해결하려면 다음과 같이 채널에 대한 로컬 폴더를 구성합니다.

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
        services.AddApplicationInsightsTelemetry();
    }
```

이 제한은 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) 이상 버전에서 적용되지 않습니다.

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>이 SDK는 새 .NET Core 3.X Worker Service 템플릿 애플리케이션에 지원되나요?

이 SDK에는 `HttpContext`가 필요합니다. 따라서 .NET Core 3.X Worker Service 애플리케이션을 포함하여 HTTP 이외의 애플리케이션에서 작동하지 않습니다. 새로 출시된 Microsoft.ApplicationInsights.WorkerService SDK를 사용하여 이러한 애플리케이션에서 Application Insights를 사용하도록 설정하려면 [Worker Service 애플리케이션(비 HTTP 앱)에 대한 Application Insights](worker-service.md)를 참조하세요.

## <a name="open-source-sdk"></a>오픈 소스 SDK

* [코드를 읽고 기여합니다](https://github.com/microsoft/ApplicationInsights-dotnet).

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보](./release-notes.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [사용자 흐름을 검색](./usage-flows.md)하여 사용자가 앱을 탐색하는 방법을 이해합니다.
* 예외가 throw되는 시점의 소스 코드 및 변수 상태를 확인할 수 있도록 [스냅샷 컬렉션을 구성](./snapshot-debugger.md)합니다.
* 앱의 성능 및 사용량을 자세히 확인할 수 있도록 [API를 사용](./api-custom-events-metrics.md)하여 사용자 고유의 이벤트 및 메트릭을 보냅니다.
* [가용성 테스트](./monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
* [ASP.NET Core의 종속성 주입](/aspnet/core/fundamentals/dependency-injection)
