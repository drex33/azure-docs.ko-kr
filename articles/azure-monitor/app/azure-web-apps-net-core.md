---
title: Azure app services 성능 .NET Core 모니터링 | Microsoft Docs
description: ASP.NET Core를 사용 하 여 Azure app services에 대 한 응용 프로그램 성능 모니터링. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: bf474ff535a9292fbadfd023830d15973bf39ad1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634241"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>Azure App Service 및 ASP.NET Core에 대 한 응용 프로그램 모니터링 

이제 [Azure 앱 서비스](../../app-service/index.yml) 에서 실행 되는 ASP.NET Core 기반 웹 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하는 것이 훨씬 쉬워졌습니다. 이전에는 수동으로 앱을 계측해야 했지만 이제 최신 확장/에이전트가 기본적으로 App Service 이미지에 기본 제공됩니다. 이 문서에서는 Azure Monitor Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> windows에서 자동 계측에 대해 지원 되는 ASP.NET Core 버전은 ASP.NET Core 3.1 및 5.0입니다. 버전 2.0, 2.1, 2.2, 3.0은 사용 중지되었으며 더 이상 지원되지 않습니다. 자동 계측을 사용하려면 .NET Core의 [지원되는 버전](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)으로 업그레이드하세요.

ASP.NET Core에서 전체 프레임워크를 대상으로 지정하는 것은 Windows에서 **지원되지 않습니다**. 대신 코드를 통한 [수동 계측](./asp-net-core.md)을 사용합니다.

Windows에서 [프레임 워크 종속 배포](/dotnet/core/deploying/#publish-framework-dependent) 만 지원 되며 [자체 포함 배포](/dotnet/core/deploying/#publish-self-contained) 는 지원 되지 않습니다.

App Service 리소스로 Application Insights 설정을 시작하려면 아래의 [모니터링 사용 섹션](#enable-monitoring )을 참조하세요. 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> Linux의 자동 계측에는 ASP.NET Core 6.0(미리 보기)만 지원됩니다.

> [!NOTE]
> Linux 자동 계측 App Services 포털 사용은 공개 미리 보기 상태입니다. 이러한 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

 Linux에서 [프레임 워크 종속 배포](/dotnet/core/deploying/#publish-framework-dependent) 및 [자체 포함 배포가](/dotnet/core/deploying/#publish-self-contained) 지원 됩니다. 

App Service 리소스로 Application Insights 설정을 시작하려면 아래의 [모니터링 사용 섹션](#enable-monitoring )을 참조하세요. 

---
 

### <a name="enable-monitoring"></a>모니터링 사용 

1. app service에 대 한 Azure 제어판에서 **Application Insights을 선택** 하 고 **사용** 을 선택 합니다.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="사용이 선택 된 Application Insights 탭의 스크린샷"::: 

2. 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다.

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="리소스 드롭다운 변경의 스크린샷"::: 

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. **권장 컬렉션** 을 제공 하거나 **사용 하지 않도록 설정할** ASP.NET Core.

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text=" 응용 프로그램 계측 섹션의 스크린샷"::: 


## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

클라이언트 쪽 모니터링은 앱 설정 ‘APPINSIGHTS_JAVASCRIPT_ENABLED’가 있는지 여부에 관계없이 **권장 수집** 을 통해 ASP.NET Core 앱에 대해 **기본적으로 사용하도록 설정** 됩니다.

어떤 이유로 클라이언트 쪽 모니터링을 사용하지 않으려는 경우:

* **설정** **>** **구성**
   * 애플리케이션 설정에서 **새 애플리케이션 설정** 을 만듭니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `false`

   * 설정을 **저장** 하고 앱을 **다시 시작** 합니다.


## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights에서 원격 분석 컬렉션을 사용 하도록 설정 하려면 응용 프로그램 설정만 설정 해야 합니다.

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Application Insights 설정을 사용 하는 App Service 응용 프로그램 설정의 스크린샷"::: 


### <a name="application-settings-definitions"></a>애플리케이션 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어하는 기본 확장입니다. | Windows의 경우 `~2` 또는 Linux의 경우 `~3` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에서는 최적의 성능을 보장 하기 위해 필수 기능만 사용할 수 있습니다. | `disabled` 또는 `recommended`. |
|XDT_MicrosoftApplicationInsights_PreemptSdk | ASP.NET Core 앱에만 해당합니다. Application Insights SDK와 상호 운용을 사용하도록 설정합니다. SDK를 사용하여 확장을 나란히 로드하고 이를 사용하여 원격 분석을 전송합니다(Application Insights SDK를 사용하지 않음). |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>모니터링 확장/에이전트 업그레이드 - .NET 

### <a name="upgrade-from-versions-289-and-up"></a>2.8.9 이상 버전에서 업그레이드

버전 2.8.9에서 업그레이드는 추가 작업 없이 자동으로 수행됩니다. 새 모니터링 비트는 백그라운드에서 대상 app service로 전달 되 고, 응용 프로그램을 다시 시작 하면 선택 됩니다.

실행 중인 확장의 버전을 확인하려면 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`로 이동합니다.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="실행 중인 확장의 버전을 확인 하는 URL 경로의 스크린샷" border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0~2.6.5에서 업그레이드

버전 2.8.9부터 미리 설치된 사이트 확장이 사용됩니다. 이전 버전을 사용 하는 경우 다음 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해 사용하도록 설정하여 업그레이드](#enable-agent-based-monitoring). Azure App Service의 Application Insights 확장이 설치된 경우에도 UI는 **사용** 단추만 표시합니다. 백그라운드에서 이전 프라이빗 사이트 확장이 제거됩니다.

* [PowerShell을 통해 업그레이드](#enable-through-powershell):

    1. 애플리케이션 설정을 설정하여 미리 설치된 사이트 확장 ApplicationInsightsAgent를 사용하도록 설정합니다. [PowerShell을 통한 사용](#enable-through-powershell)을 참조 하세요.
    2. Azure App Service의 Application Insights 확장이라는 프라이빗 사이트 확장을 수동으로 제거합니다.

2\.5.1 이전 버전에서 업그레이드한 경우 애플리케이션 bin 폴더에서 ApplicationInsigths dll이 제거되었는지 확인합니다([문제 해결 단계 참조](#troubleshooting)).

## <a name="troubleshooting"></a>문제 해결

다음은 Azure 앱 서비스에서 실행 되는 ASP.NET Core 기반 응용 프로그램에 대 한 확장/에이전트 기반 모니터링에 대 한 단계별 문제 해결 가이드입니다.

# <a name="windows"></a>[Windows](#tab/windows)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~2” 값으로 설정되어 있는지 확인합니다.
2. [https://www.microsoft.com]\(`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) 로 이동합니다.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="위 결과 페이지 링크의 스크린샷"border ="false"::: 
    
    - `Application Insights Extension Status`가 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`인지 확인합니다. 
    
         실행 되 고 있지 않으면 [Application Insights 모니터링 사용 지침](#enable-agent-based-monitoring)을 따릅니다.

    - 상태 원본이 존재하고 다음과 같이 표시되는지 확인합니다. `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         유사한 값이 없으면 응용 프로그램이 현재 실행 되 고 있지 않거나 지원 되지 않음을 의미 합니다. 애플리케이션이 실행되고 있는지 확인하려면 런타임 정보가 제공될 수 있도록 애플리케이션 URL/애플리케이션 엔드포인트를 수동으로 방문해 봅니다.

    - `IKeyExists`가 `true`인지 확인합니다. `false`인 경우 ikey guid를 사용하여 애플리케이션 설정에 `APPINSIGHTS_INSTRUMENTATIONKEY` 및 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 추가합니다.

    -  응용 프로그램이 Application Insights 패키지를 참조 하는 경우 (예: [ASP.NET Core SDK](./asp-net-core.md)를 사용 하 여 앱을 이전에 계측 하거나 계측 하려고 한 경우) App Service 통합을 사용 하도록 설정 하는 것은 적용 되지 않으며 데이터는 Application Insights에 표시 되지 않을 수 있습니다. 이 문제를 해결 하려면 포털에서 "Application Insights SDK를 사용 하 여 Interop"를 켜고 Application Insights 데이터를 확인 하기 시작 합니다. 
    - 
        > [!IMPORTANT]
        > 이 기능은 미리 보기 상태입니다. 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text=" Interop 설정의 스크린샷 사용"::: 
        
        Application Insights SDK를 원래 사용했거나 사용하려고 시도한 경우에도 이제 코드리스 접근 방식을 사용하여 데이터를 전송하려고 합니다.

        > [!IMPORTANT]
        > 애플리케이션이 Application Insights SDK를 사용하여 원격 분석을 보낸 경우 해당 원격 분석은 사용하도록 설정되지 않습니다. 즉, 사용자 지정 원격 분석(있는 경우, 예: Track*() 메서드) 및 사용자 지정 설정(예: 샘플링)이 사용하지 않도록 설정됩니다. 

# <a name="linux"></a>[Linux](#tab/linux)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~3” 값으로 설정되어 있는지 확인합니다.
2. */home\LogFiles\ApplicationInsights\status* 로 이동하여 *status_557de146e7fa_27_1.json* 을 엽니다.

    `AppAlreadyInstrumented`가 false로, `AiHostingStartupLoaded`가 true로, `IKeyExists`가 true로 설정되어 있는지 확인합니다.

    다음은 JSON 파일의 예입니다.

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    `AppAlreadyInstrumented`가 true이면 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다.

##### <a name="no-data"></a>데이터 없음

1. 앱을 호스팅하는 프로세스를 나열하고 식별합니다. 터미널로 이동하고 명령줄에 `ps ax`를 입력합니다. 
    
    출력은 다음과 비슷해야 합니다. 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. 그런 다음 앱 프로세스에서 환경 변수를 나열합니다. 명령줄에서 `cat /proc/27/environ | tr '\0' '\n`을 입력합니다.
    
    출력은 다음과 비슷해야 합니다. 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. , 및 가 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` `DOTNET_STARTUP_HOOKS` 설정되어 있는지 `APPLICATIONINSIGHTS_CONNECTION_STRING` 확인합니다.

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>웹앱으로 배포된 기본 웹 사이트에서 자동 클라이언트 쪽 모니터링을 지원하지 않음

Azure 앱 Services에서 런타임을 사용하여 웹앱을 만들 때 `ASP.NET Core` 단일 정적 HTML 페이지를 시작 웹 사이트로 배포합니다. 또한 정적 웹 페이지는 IIS에서 ASP.NET 관리형 웹 파트를 로드합니다. 이렇게 하면 코드 없는 서버 쪽 모니터링을 테스트할 수 있지만 자동 클라이언트 쪽 모니터링은 지원하지 않습니다.

Azure 앱 Services 웹앱에서 ASP.NET Core 대한 코드 없는 서버 및 클라이언트 쪽 모니터링을 테스트하려는 경우 ASP.NET Core [웹앱을 만들기](../../app-service/quickstart-dotnetcore.md)위한 공식 가이드를 따르는 것이 좋습니다. 그런 다음, 현재 문서의 지침을 사용하여 모니터링을 사용하도록 설정합니다.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP 및 WordPress가 지원되지 않음

PHP 및 WordPress 사이트는 지원되지 않습니다. 현재 이러한 워크로드의 서버 쪽 모니터링에 대해 공식적으로 지원되는 SDK/에이전트는 없습니다. 그러나 [JavaScript SDK](./javascript.md)를 사용하면 클라이언트 쪽 JavaScript를 웹 페이지에 추가하여 PHP 또는 WordPress 사이트에서 클라이언트 쪽 트랜잭션을 수동으로 계측할 수 있습니다.

아래 표에서는 해당 값의 의미, 근본 원인 및 권장 수정 사항을 더 자세히 설명합니다.

|문제 값 |설명 |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. 이는 , 또는 에 대한 참조 때문일 수 있습니다. `Microsoft.ApplicationInsights.AspNetCore``Microsoft.ApplicationInsights`  | 참조를 제거합니다. 해당 참조 중 일부는 특정 Visual Studio 템플릿에서 기본적으로 추가되며 이전 버전의 Visual Studio는 `Microsoft.ApplicationInsights`에 대한 참조를 추가할 수 있습니다. |
|`AppAlreadyInstrumented:true` | 애플리케이션이 ASP.NET Core 2.1 또는 2.2를 대상으로 하는 경우 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. | .NET Core 2.1, 2.2의 고객은 Microsoft.AspNetCore.App 메타 패키지를 대신 [사용하는 것이 좋습니다](https://github.com/aspnet/Announcements/issues/287). 또한 포털에서 “Application Insights SDK와 상호 운용”을 켭니다(위 지침 참조).|
|`AppAlreadyInstrumented:true` | 이 값은 이전 배포의 앱 폴더에 Microsoft.ApplicationsInsights dll이 있는 경우 발생할 수도 있습니다. | 앱 폴더를 정리하여 이 dll이 제거되도록 합니다. 로컬 앱의 bin 디렉터리와 App Service의 wwwroot 디렉터리를 둘 다 확인합니다. App Service 웹앱의 wwwroot 디렉터리를 확인하려면: 고급 도구(Kudu) > 디버그 콘솔 > CMD > home\site\wwwroot. |
|`IKeyExists:false`|이 값은 AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`에 계측 키가 없음을 나타냅니다. 가능한 원인: 값이 실수로 제거되거나 자동화 스크립트에서 값을 설정하지 않은 것일 수 있습니다. | 설정이 App Service 애플리케이션 설정에 있는지 확인합니다. |

## <a name="release-notes"></a>릴리스 정보

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](web-app-extension-release-notes.md)하세요.

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](./profiler.md).
* [Application Insights 를](monitor-functions.md)Azure Functions 모니터링합니다.
* [Azure 진단을 사용](../agents/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../alerts/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.
