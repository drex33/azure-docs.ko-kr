---
title: Azure App Services 성능 ASP.NET | 모니터링 Microsoft Docs
description: ASP.NET 사용하여 Azure App Services에 대한 애플리케이션 성능 모니터링 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 5e85a12d017f5fc812ac4f910ce9517d73cc41c7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154600"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>Azure App Service 및 ASP.NET 대한 애플리케이션 모니터링 

Azure 앱 [Services에서](../../app-service/index.yml) 실행되는 ASP.NET 기반 웹 애플리케이션에서 모니터링을 사용하도록 설정하는 것이 그 어느 때보다 쉬워졌습니다. 이전에는 수동으로 앱을 계측해야 했지만 이제 최신 확장/에이전트가 기본적으로 App Service 이미지에 기본 제공됩니다. 이 문서에서는 Azure Monitor Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.

> [!NOTE]
> **개발 도구** 확장을 통해 Application Insights 사이트 확장을 수동으로 추가하는 것은 더 이상  >   사용되지 않습니다. 이 확장 설치 방법은 각 새 버전의 수동 업데이트에 따라 달랐습니다. 확장의 안정적인 최신 릴리스는 이제 App Service 이미지의 일부로  [미리 설치](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)됩니다. 파일은 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`에 있으며 각 안정적인 릴리스로 자동 업데이트됩니다. 에이전트 기반 지침에 따라 아래 모니터링을 사용하는 경우 사용되지 않는 확장이 자동으로 제거됩니다.

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 둘 다 검색된 경우에는 수동 계측 설정만 적용됩니다. 이를 통해 중복 데이터가 전송되는 것을 방지합니다. 이 기능에 관한 자세한 내용은 아래 [문제 해결 섹션](#troubleshooting)을 확인하세요.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED 및 urlCompression의 조합은 지원되지 않습니다. 자세한 내용은 [문제 해결 섹션](#appinsights_javascript_enabled-and-urlcompression-is-not-supported)의 설명을 참조하세요.

1. App Service에 대한 Azure 제어판에서 **Application Insights** 선택한 다음, **사용을** 선택합니다.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="사용이 선택된 Application Insights 탭의 스크린샷."::: 

2. 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다. 

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="리소스 변경 드롭다운의 스크린샷."::: 

3. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. ASP.NET 앱 모니터링은 기본적으로 두 가지 수준의 수집을 사용하여 설정됩니다.

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="스크린샷에는 새 리소스 만들기가 선택된 Application Insights 사이트 확장 페이지가 표시됩니다."::: 
 
     다음은 각 경로에 대해 수집된 데이터의 요약입니다.
            
    | 데이터 | ASP.NET 기본 수집 | ASP.NET 권장 수집 |
    | --- | --- | --- |
    | CPU, 메모리 및 I/O 사용량 추세를 추가합니다. |예 |예 |
    | 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다. | 예 |예 |
    | 호스트 프로세스에서 처리되지 않은 예외를 수집합니다. | 예 |예 |
    | 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다. | 예 |예 |
    | 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다. | 아니요(단일 인스턴스 APM 기능만 해당) |예 |

4. 이전에 applicationinsights.config 파일을 통해 제어할 수 있었던 샘플링을 구성하려면 이제 해당 접두사 를 사용하여 애플리케이션 설정을 통해 상호 작용할 수 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor` 있습니다. 

    - 예를 들어, 초기 샘플링 비율을 변경하기 위해 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`와 `100` 값으로 된 애플리케이션 설정을 만들 수 있습니다.
    - 샘플링을 사용하지 않도록 설정하려면 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` 를 값으로 `100` 설정합니다.
    - 지원되는 설정은 다음과 같습니다.
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - 지원되는 적응 샘플링 원격 분석 프로세서 설정 및 정의 목록은 [코드](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) 및 [샘플링 설명서를](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications)참조하세요.


## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

클라이언트 쪽 모니터링은 ASP.NET에 대해 옵트인(opt in)됩니다. 클라이언트 쪽 모니터링을 사용하려면 다음을 수행합니다.

* **설정** **>** **구성**
   * 애플리케이션 설정에서 **새 애플리케이션 설정** 을 만듭니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `true`

   * 설정을 **저장** 하고 앱을 **다시 시작** 합니다.

클라이언트 쪽 모니터링을 사용하지 않으려면 애플리케이션 설정에서 연결된 키 값 쌍을 제거하거나 값을 false로 설정합니다.

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights를 통해 원격 분석 컬렉션을 사용하도록 설정하려면 애플리케이션 설정만 설정해야 합니다.

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="Application Insights 설정을 App Service 애플리케이션 설정의 스크린샷."::: 

### <a name="application-settings-definitions"></a>애플리케이션 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어하는 기본 확장입니다. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에서는 최적 성능을 보장하기 위해 필수 기능만 사용됩니다. | `default` 또는 `recommended` |
|InstrumentationEngine_EXTENSION_VERSION | 이진 다시 쓰기 엔진 `InstrumentationEngine`을 켤지 여부를 제어합니다. 이 설정은 성능에 영향을 주며 콜드 부팅/시작 시간에 영향을 줍니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL 및 Azure 테이블 텍스트를 종속성 호출과 함께 캡처할지 여부를 제어합니다. 성능 경고: 애플리케이션 콜드 부팅 작동 시간이 영향을 받습니다. 이 설정에는 `InstrumentationEngine`이 필요합니다. | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>모니터링 확장/에이전트 업그레이드 - .NET 

### <a name="upgrade-from-versions-289-and-up"></a>버전 2.8.9 이상에서 업그레이드

버전 2.8.9에서 업그레이드는 추가 작업 없이 자동으로 수행됩니다. 새 모니터링 비트는 백그라운드에서 대상 앱 서비스로 전달되고 애플리케이션이 다시 시작되면 해당 비트가 선택됩니다.

실행 중인 확장의 버전을 확인하려면 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`로 이동합니다.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="실행 중인 확장의 버전을 확인하는 URL 경로의 스크린샷." border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0~2.6.5에서 업그레이드

버전 2.8.9부터 미리 설치된 사이트 확장이 사용됩니다. 이전 버전인 경우 다음 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해 사용하도록 설정하여 업그레이드](#enable-agent-based-monitoring). Azure App Service의 Application Insights 확장이 설치된 경우에도 UI는 **사용** 단추만 표시합니다. 백그라운드에서 이전 프라이빗 사이트 확장이 제거됩니다.

* [PowerShell을 통해 업그레이드](#enable-through-powershell):

    1. 애플리케이션 설정을 설정하여 미리 설치된 사이트 확장 ApplicationInsightsAgent를 사용하도록 설정합니다. [PowerShell을 통해 사용을](#enable-through-powershell)참조하세요.
    2. Azure App Service의 Application Insights 확장이라는 프라이빗 사이트 확장을 수동으로 제거합니다.

2\.5.1 이전 버전에서 업그레이드한 경우 애플리케이션 bin 폴더에서 ApplicationInsigths dll이 제거되었는지 확인합니다([문제 해결 단계 참조](#troubleshooting)).

## <a name="troubleshooting"></a>문제 해결

다음은 Azure 앱 Services에서 실행되는 ASP.NET 기반 애플리케이션에 대한 확장/에이전트 기반 모니터링에 대한 단계별 문제 해결 가이드입니다.

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~2” 값으로 설정되어 있는지 확인합니다.
2. [https://www.microsoft.com]\(`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) 로 이동합니다.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="위의 결과 페이지 링크 스크린샷."border ="false"::: 
    
    - `Application Insights Extension Status`가 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`인지 확인합니다. 
    
         실행되고 있지 않으면 [Application Insights 모니터링 사용 지침](#enable-agent-based-monitoring)을 따릅니다.

    - 상태 원본이 존재하고 다음과 같이 표시되는지 확인합니다. `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         유사한 값이 없는 경우에는 애플리케이션이 현재 실행되고 있지 않거나 지원되지 않음을 의미합니다. 애플리케이션이 실행되고 있는지 확인하려면 런타임 정보가 제공될 수 있도록 애플리케이션 URL/애플리케이션 엔드포인트를 수동으로 방문해 봅니다.

    - `IKeyExists`가 `true`인지 확인합니다. `false`인 경우 ikey guid를 사용하여 애플리케이션 설정에 `APPINSIGHTS_INSTRUMENTATIONKEY` 및 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 추가합니다.

    - `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, `AppContainsAspNetTelemetryCorrelationAssembly`에 해당하는 항목이 없는지 확인합니다.

         해당 항목이 있는 경우 애플리케이션에서 `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` 패키지를 제거합니다.

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>웹앱으로 배포된 기본 웹 사이트에서 자동 클라이언트 쪽 모니터링을 지원하지 않음

Azure 앱 Services에서 런타임을 사용하여 웹앱을 만들 때 `ASP.NET` 단일 정적 HTML 페이지를 시작 웹 사이트로 배포합니다. 또한 정적 웹 페이지는 IIS에서 ASP.NET 관리형 웹 파트를 로드합니다. 이를 통해 코드리스 서버 쪽 모니터링을 테스트할 수 있지만 자동 클라이언트 쪽 모니터링은 지원하지 않습니다.

Azure 앱 Services 웹앱에서 ASP.NET 대한 코드 없는 서버 및 클라이언트 쪽 모니터링을 테스트하려는 경우 [ASP.NET Framework 웹앱을 만들기](../../app-service/quickstart-dotnetcore.md?tabs=netframework48) 위한 공식 가이드를 따르고 현재 문서의 지침을 사용하여 모니터링을 사용하도록 설정하는 것이 좋습니다.


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 및 urlCompression이 지원되지 않음

콘텐츠가 인코딩된 경우 APPINSIGHTS_JAVASCRIPT_ENABLED=true를 사용하면 다음과 같은 오류가 발생할 수 있습니다.

- 500 URL 다시 쓰기 오류
- HTTP 응답의 콘텐츠를 인코딩할 때 메시지 아웃바운드 다시 쓰기 규칙과 관련된 500.53 URL 다시 쓰기 모듈 오류를 적용할 수 없습니다(‘gzip’).

원인은 APPINSIGHTS_JAVASCRIPT_ENABLED 애플리케이션 설정이 true로 설정되고 콘텐츠 인코딩이 동시에 제공되기 때문입니다. 이 시나리오는 아직 지원되지 않습니다. 해결 방법은 애플리케이션 설정에서 APPINSIGHTS_JAVASCRIPT_ENABLED를 제거하는 것입니다. 안타깝게도 이는 클라이언트/브라우저 쪽 JavaScript 계측이 계속 필요한 경우 웹 페이지에 대한 수동 SDK 참조가 필요함을 의미합니다. JavaScript SDK를 사용한 수동 계측에 관한 [지침](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)을 따릅니다.

Application Insights 에이전트/확장에 관한 최신 정보는 [릴리스 정보](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)를 확인하세요.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP 및 WordPress가 지원되지 않음

PHP 및 WordPress 사이트가 지원되지 않습니다. 현재 이 워크로드의 서버 쪽 모니터링에 대해 공식적으로 지원되는 SDK/에이전트는 없습니다. 그러나 [JavaScript SDK](./javascript.md)를 사용하면 클라이언트 쪽 JavaScript를 웹 페이지에 추가하여 PHP 또는 WordPress 사이트에서 클라이언트 쪽 트랜잭션을 수동으로 계측할 수 있습니다.

아래 표에서는 해당 값의 의미, 근본 원인 및 권장 수정 사항을 더 자세히 설명합니다.

|문제 값|설명|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` 또는 `Microsoft.ApplicationInsights`에 대한 참조가 원인일 수 있습니다.  | 참조를 제거합니다. 해당 참조 중 일부는 특정 Visual Studio 템플릿에서 기본적으로 추가되며 이전 버전의 Visual Studio는 `Microsoft.ApplicationInsights`에 대한 참조를 추가할 수 있습니다.
|`AppAlreadyInstrumented:true` | 애플리케이션이 ASP.NET Core 2.1 또는 2.2를 대상으로 하는 경우 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. | .NET Core 2.1, 2.2의 고객은 Microsoft.AspNetCore.App 메타 패키지를 대신 [사용하는 것이 좋습니다](https://github.com/aspnet/Announcements/issues/287). 또한 포털에서 “Application Insights SDK와 상호 운용”을 켭니다(위 지침 참조).|
|`AppAlreadyInstrumented:true` | 이전 배포의 앱 폴더에 위 dll이 있기 때문에 이 값이 발생할 수도 있습니다. | 앱 폴더를 정리하여 이 dll이 제거되도록 합니다. 로컬 앱의 bin 디렉터리와 App Service의 wwwroot 디렉터리를 둘 다 확인합니다. App Service 웹앱의 wwwroot 디렉터리를 확인하려면: 고급 도구(Kudu) > 디버그 콘솔 > CMD > home\site\wwwroot.
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 이 값은 확장에서 애플리케이션의 `Microsoft.AspNet.TelemetryCorrelation`에 대한 참조를 검색했고 백오프될 것임을 나타냅니다. | 참조를 제거합니다.
|`AppContainsDiagnosticSourceAssembly**:true`|이 값은 확장에서 애플리케이션의 `System.Diagnostics.DiagnosticSource`에 대한 참조를 검색했고 백오프될 것임을 나타냅니다.| ASP.NET의 경우 참조를 제거합니다. 
|`IKeyExists:false`|이 값은 AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`에 계측 키가 없음을 나타냅니다. 가능한 원인: 값이 실수로 제거되거나 자동화 스크립트에서 값을 설정하지 않은 것일 수 있습니다. | 설정이 App Service 애플리케이션 설정에 있는지 확인합니다.

## <a name="release-notes"></a>릴리스 정보

최신 업데이트 및 버그 수정은 [릴리스 정보를 참조하세요](web-app-extension-release-notes.md).

## <a name="next-steps"></a>다음 단계

* [라이브 앱에서 프로파일러를 실행합니다](./profiler.md).
* [Application Insights 를 Azure Functions 모니터링합니다.](monitor-functions.md)
* [Azure 진단을 사용](../agents/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../alerts/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.