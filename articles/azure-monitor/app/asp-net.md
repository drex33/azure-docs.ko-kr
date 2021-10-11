---
title: Azure Application Insights를 사용하여 ASP.NET에 대한 모니터링 구성 | Microsoft Docs
description: Azure 또는 온-프레미스에 호스트되는 ASP.NET 웹 사이트에 대한 성능, 가용성 및 사용자 동작 분석 도구를 구성합니다.
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: a0a262564306448fd587a699b24982595506df50
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740705"
---
# <a name="configure-application-insights-for-your-aspnet-website"></a>ASP.NET 웹 사이트용 Application Insights 구성

이 절차에서는 Azure Monitor 서비스의 [Azure Application Insights](./app-insights-overview.md) 기능으로 원격 분석을 보내도록 ASP.NET 웹앱을 구성합니다. 이 절차는 사용자 고유의 IIS 서버 온-프레미스 또는 클라우드에서 호스트되는 ASP.NET 앱에서 작동합니다. 

## <a name="prerequisites"></a>사전 요구 사항
Application Insights를 ASP.NET 웹 사이트에 추가하려면 다음을 수행해야 합니다.

- 다음 워크로드와 함께 최신 버전의 [Windows용 Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발

- Azure 구독이 아직 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.

- [Application Insights 작업 영역 기반 리소스](create-workspace-resource.md)를 만듭니다.

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md?tabs=net)을 사용하는 것이 좋습니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 *사용해야 합니다*.
>
> 연결 문자열은 원격 분석 데이터와 연결할 리소스를 식별합니다. 또한 연결 문자열을 통해 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.


## <a name="create-a-basic-aspnet-web-app"></a>기본 ASP.NET 웹앱 만들기

1. Visual Studio 2019를 엽니다.
2. **File** > **New** > **Project** 를 선택합니다.
3. **ASP.NET 웹 애플리케이션(.NET Framework) C#** 을 선택합니다.
4. 프로젝트 이름을 입력하고 **만들기** 를 선택합니다.
5. **MVC** > **만들기** 를 선택합니다. 

## <a name="add-application-insights-automatically"></a>자동으로 Application Insights 추가

이 섹션에서는 템플릿 기반 ASP.NET 웹앱에 Application Insights를 자동으로 추가하는 방법을 안내합니다. Visual Studio의 ASP.NET 웹앱 프로젝트 내에서:

1. **Project**  >  **추가 Application Insights 원격 분석**  >  **Application Insights Sdk (로컬)**  >  **다음**  >  **마침**  >  **닫기** 를 선택 합니다.
2. *ApplicationInsights.config* 파일을 엽니다. 
3. 닫는 `</ApplicationInsights>` 태그 앞에 Application Insights 리소스에 대한 계측 키가 포함된 줄을 추가합니다.  계측 키는 이 문서에서 필수 조건의 일부로 새로 만든 Application Insights 리소스의 개요 창에서 찾을 수 있습니다.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```
4. **프로젝트** > **NuGet 패키지 관리** > **업데이트** 를 선택합니다. 그런 다음, 각 `Microsoft.ApplicationInsights` NuGet 패키지를 안정적인 최신 릴리스로 업데이트합니다.   
5. **IIS Express** 를 선택하여 애플리케이션을 실행합니다. 기본 ASP.NET 앱이 열립니다. 사이트에서 페이지를 찾아보면 원격 분석이 Application Insights로 전송됩니다.

## <a name="add-application-insights-manually"></a>수동으로 Application Insights 추가

이 섹션에서는 템플릿 기반 ASP.NET 웹앱에 Application Insights를 수동으로 추가하는 방법을 안내합니다. 이 섹션에서는 ASP.NET Framework의 표준 MVC 웹앱 템플릿을 기반으로 하는 웹앱을 사용한다고 가정합니다.

1. 다음 NuGet 패키지 및 해당 종속성을 프로젝트에 추가합니다.

    - [`Microsoft.ApplicationInsights.WindowsServer`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer)
    - [`Microsoft.ApplicationInsights.Web`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
    - [`Microsoft.AspNet.TelemetryCorrelation`](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation)

2. 경우에 따라 *ApplicationInsights.config* 파일이 자동으로 만들어집니다. 파일이 이미 있는 경우 4단계로 건너뜁니다. 

   자동으로 만들어지지 않은 경우 직접 만들어야 합니다. *Global.asax* 파일과 동일한 프로젝트 수준에서 *ApplicationInsights.config* 라는 새 파일을 만듭니다.

3. 다음 XML 구성을 새로 만든 파일에 복사합니다.

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
      <TelemetryInitializers>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureRoleEnvironmentTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.Web.WebTestTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SyntheticUserAgentTelemetryInitializer, Microsoft.AI.Web">
          <!-- Extended list of bots:
                search|spider|crawl|Bot|Monitor|BrowserMob|BingPreview|PagePeeker|WebThumb|URL2PNG|ZooShot|GomezA|Google SketchUp|Read Later|KTXN|KHTE|Keynote|Pingdom|AlwaysOn|zao|borg|oegp|silk|Xenu|zeal|NING|htdig|lycos|slurp|teoma|voila|yahoo|Sogou|CiBra|Nutch|Java|JNLP|Daumoa|Genieo|ichiro|larbin|pompos|Scrapy|snappy|speedy|vortex|favicon|indexer|Riddler|scooter|scraper|scrubby|WhatWeb|WinHTTP|voyager|archiver|Icarus6j|mogimogi|Netvibes|altavista|charlotte|findlinks|Retreiver|TLSProber|WordPress|wsr-agent|http client|Python-urllib|AppEngine-Google|semanticdiscovery|facebookexternalhit|web/snippet|Google-HTTP-Java-Client-->
          <Filters>search|spider|crawl|Bot|Monitor|AlwaysOn</Filters>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ClientIpHeaderTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AzureAppServiceRoleNameFromHostNameHeaderInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationNameTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationCorrelationTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.UserTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SessionTelemetryInitializer, Microsoft.AI.Web" />
      </TelemetryInitializers>
      <TelemetryModules>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
          <ExcludeComponentCorrelationHttpHeadersOnDomains>
            <!-- 
            Requests to the following hostnames will not be modified by adding correlation headers.         
            Add entries here to exclude additional hostnames.
            NOTE: this configuration will be lost upon NuGet upgrade.
            -->
            <Add>core.windows.net</Add>
            <Add>core.chinacloudapi.cn</Add>
            <Add>core.cloudapi.de</Add>
            <Add>core.usgovcloudapi.net</Add>
          </ExcludeComponentCorrelationHttpHeadersOnDomains>
          <IncludeDiagnosticSourceActivities>
            <Add>Microsoft.Azure.EventHubs</Add>
            <Add>Microsoft.Azure.ServiceBus</Add>
          </IncludeDiagnosticSourceActivities>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <!--
          Use the following syntax here to collect additional performance counters:
          
          <Counters>
            <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
            ...
          </Counters>
          
          PerformanceCounter must be either \CategoryName(InstanceName)\CounterName or \CategoryName\CounterName
          
          NOTE: performance counters configuration will be lost upon NuGet upgrade.
          
          The following placeholders are supported as InstanceName:
            ??APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
            ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
            ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AppServicesHeartbeatTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureInstanceMetadataTelemetryModule, Microsoft.AI.WindowsServer">
          <!--
          Remove individual fields collected here by adding them to the ApplicationInsighs.HeartbeatProvider 
          with the following syntax:
          
          <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
            <ExcludedHeartbeatProperties>
              <Add>osType</Add>
              <Add>location</Add>
              <Add>name</Add>
              <Add>offer</Add>
              <Add>platformFaultDomain</Add>
              <Add>platformUpdateDomain</Add>
              <Add>publisher</Add>
              <Add>sku</Add>
              <Add>version</Add>
              <Add>vmId</Add>
              <Add>vmSize</Add>
              <Add>subscriptionId</Add>
              <Add>resourceGroupName</Add>
              <Add>placementGroupId</Add>
              <Add>tags</Add>
              <Add>vmScaleSetName</Add>
            </ExcludedHeartbeatProperties>
          </Add>
                
          NOTE: exclusions will be lost upon upgrade.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule, Microsoft.AI.WindowsServer">
          <!--</Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.FirstChanceExceptionStatisticsTelemetryModule, Microsoft.AI.WindowsServer">-->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule, Microsoft.AI.Web">
          <Handlers>
            <!-- 
            Add entries here to filter out additional handlers: 
            
            NOTE: handler configuration will be lost upon NuGet upgrade.
            -->
            <Add>Microsoft.VisualStudio.Web.PageInspector.Runtime.Tracing.RequestDataHttpHandler</Add>
            <Add>System.Web.StaticFileHandler</Add>
            <Add>System.Web.Handlers.AssemblyResourceLoader</Add>
            <Add>System.Web.Optimization.BundleHandler</Add>
            <Add>System.Web.Script.Services.ScriptHandlerFactory</Add>
            <Add>System.Web.Handlers.TraceHandler</Add>
            <Add>System.Web.Services.Discovery.DiscoveryRequestHandler</Add>
            <Add>System.Web.HttpDebugHandler</Add>
          </Handlers>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web" />
      </TelemetryModules>
      <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
      <TelemetrySinks>
        <Add Name="default">
          <TelemetryProcessors>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector" />
            <Add Type="Microsoft.ApplicationInsights.Extensibility.AutocollectedMetricsExtractor, Microsoft.ApplicationInsights" />
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <ExcludedTypes>Event</ExcludedTypes>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <IncludedTypes>Event</IncludedTypes>
            </Add>
          </TelemetryProcessors>
          <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel" />
        </Add>
      </TelemetrySinks>
      <!-- 
        Learn more about Application Insights configuration with ApplicationInsights.config here: 
        http://go.microsoft.com/fwlink/?LinkID=513840
      -->
      <InstrumentationKey>your-instrumentation-key-here</InstrumentationKey>
    </ApplicationInsights>
     ```

4. 닫는 `</ApplicationInsights>` 태그 앞에 Application Insights 리소스에 대한 계측 키를 추가합니다.  계측 키는 이 문서에서 필수 조건의 일부로 새로 만든 Application Insights 리소스의 개요 창에서 찾을 수 있습니다.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```

5. *ApplicationInsights.config* 파일과 동일한 프로젝트 수준에서 *AiHandleErrorAttribute.cs* 라는 새 C# 파일이 있는 *ErrorHandler* 라는 폴더를 만듭니다. 파일의 내용은 다음과 같습니다.

    ```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;
    
    namespace WebApplication10.ErrorHandler //namespace will vary based on your project name
    {
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
        public class AiHandleErrorAttribute : HandleErrorAttribute
        {
            public override void OnException(ExceptionContext filterContext)
            {
                if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
                {
                    //If customError is Off, then AI HTTPModule will report the exception
                    if (filterContext.HttpContext.IsCustomErrorEnabled)
                    {   
                        var ai = new TelemetryClient();
                        ai.TrackException(filterContext.Exception);
                    } 
                }
                base.OnException(filterContext);
            }
        }
    }
    
    ```

6. *App_Start* 폴더에서 *FilterConfig.cs* 파일을 열어 샘플과 일치하도록 변경합니다.

    ```csharp
    using System.Web;
    using System.Web.Mvc;
    
    namespace WebApplication10 //Namespace will vary based on project name
    {
        public class FilterConfig
        {
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());
            }
        }
    }
    ```

7. *Web.config* 가 이미 업데이트된 경우 이 단계를 건너뜁니다. 그러지 않으면 파일을 다음과 같이 업데이트합니다.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!--
      For more information on how to configure your ASP.NET application, please visit
      https://go.microsoft.com/fwlink/?LinkId=301880
      -->
    <configuration>
      <appSettings>
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.7.2" />
        <httpRuntime targetFramework="4.7.2" />
        <!-- Code added for Application Insights start -->
        <httpModules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" />
        </httpModules>
        <!-- Code added for Application Insights end -->
      </system.web>
      <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          <dependentAssembly>
            <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" />
            <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" />
            <bindingRedirect oldVersion="0.0.0.0-12.0.0.0" newVersion="12.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-5.2.7.0" newVersion="5.2.7.0" />
          </dependentAssembly>
          <!-- Code added for Application Insights start -->
          <dependentAssembly>
            <assemblyIdentity name="System.Memory" publicKeyToken="cc7b13ffcd2ddd51" culture="neutral" />
            <bindingRedirect oldVersion="0.0.0.0-4.0.1.1" newVersion="4.0.1.1" />
          </dependentAssembly>
          <!-- Code added for Application Insights end -->
        </assemblyBinding>
      </runtime>
      <system.codedom>
        <compilers>
          <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
          <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
        </compilers>
      </system.codedom>
      <system.webServer>
        <validation validateIntegratedModeConfiguration="false" />
        <!-- Code added for Application Insights start -->
        <modules>
          <remove name="TelemetryCorrelationHttpModule" />
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="managedHandler" />
          <remove name="ApplicationInsightsWebTracking" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
        </modules>
        <!-- Code added for Application Insights end -->
      </system.webServer>
    </configuration>
    
    ```

이제 서버 쪽 애플리케이션 모니터링을 성공적으로 구성했습니다. 웹앱을 실행하면 원격 분석이 Application Insights에 표시되기 시작합니다.

## <a name="add-client-side-monitoring"></a>클라이언트쪽 모니터링을 추가 합니다.

이전 섹션에서는 서버 쪽 모니터링을 자동 및 수동으로 구성하는 방법에 대한 지침을 제공했습니다. 클라이언트 쪽 모니터링을 추가하려면 [클라이언트 쪽 JavaScript SDK](javascript.md)를 사용합니다. 페이지 HTML의 닫는 `</head>` 태그 앞에 [JavaScript 코드 조각](javascript.md#snippet-based-setup)을 추가하여 웹 페이지의 클라이언트 쪽 트랜잭션을 모니터링할 수 있습니다. 

각 HTML 페이지의 헤더에 코드 조각을 수동으로 추가할 수 있지만 대신 주 페이지에 코드 조각을 추가하는 것이 좋습니다. 그러면 사이트의 모든 페이지에 코드 조각이 삽입됩니다. 

이 문서에서 템플릿 기반 ASP.NET MVC 앱의 경우 *_Layout.cshtml* 파일을 편집해야 합니다. 이 파일은 **보기** > **공유** 에서 찾을 수 있습니다. 클라이언트 쪽 모니터링을 추가하려면 *_Layout.cshtml* 을 열고 클라이언트 쪽 JavaScript SDK 구성에 대한 문서의 [코드 조각 기반 설정 지침](javascript.md#snippet-based-setup)을 따르세요.

## <a name="troubleshooting"></a>문제 해결

Visual Studio 2019의 현재 버전에 알려진 문제가 있습니다. .NET Framework 기반 앱에서 계측 키를 사용자 비밀에 저장할 수 없습니다. 이 버그를 해결하려면 키를 *applicationinsights.config* 파일에 하드 코드해야 합니다. 이 문서는 사용자 비밀을 사용하지 않고 이 문제를 완전히 방지하도록 설계되었습니다.  

## <a name="open-source-sdk"></a>오픈 소스 SDK

[코드를 읽고 기여합니다](https://github.com/microsoft/ApplicationInsights-dotnet).

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](./release-notes.md)하세요.

## <a name="next-steps"></a>다음 단계

* 가상 트랜잭션을 추가하여 [가용성 모니터링](monitor-web-app-availability.md)을 통해 전 세계에서 웹 사이트를 사용할 수 있는지 테스트합니다.
* [샘플링을 구성](sampling.md)하면 원격 분석 트래픽과 데이터 스토리지 비용을 줄이는 데 도움이 됩니다.


