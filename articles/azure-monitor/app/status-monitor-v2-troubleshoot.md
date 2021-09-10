---
title: Azure Application Insights Agent 문제 해결 및 알려진 문제 | Microsoft Docs
description: Application Insights Agent의 알려진 문제 및 문제 해결 예제입니다. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. VM 또는 Azure의 온-프레미스에 호스트되는 ASP.NET 웹앱으로 작업합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b353409ed1cacfabc8ac407e4ad17f4b1e167fe8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727448"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights Agent 문제 해결(이전 이름: 상태 모니터 v2)

모니터링을 사용하도록 설정하면 데이터 수집을 방해하는 문제가 발생할 수 있습니다.
이 문서에서는 모든 알려진 문제의 목록과 문제 해결 예제를 제공합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>앱의 bin 디렉터리에서 충돌하는 DLL

해당 DLL이 bin 디렉터리에 있는 경우 모니터링에 실패할 수 있습니다.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

해당 DLL 중 일부는 앱에서 사용하지 않는 경우에도 Visual Studio 기본 앱 템플릿에 포함되어 있습니다.
문제 해결 도구를 사용하여 증상 동작을 확인할 수 있습니다.

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 및 앱 로드(원격 분석 없음). Sysinternals(Handle.exe 및 ListDLLs.exe)를 사용하여 조사합니다.
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell 버전
이 제품은 PowerShell v5.1을 사용하여 작성하고 테스트했습니다.
이 모듈은 PowerShell 버전 6 또는 7과 호환되지 않습니다.
PowerShell v5.1은 최신 버전과 함께 사용하는 것이 좋습니다. 자세한 내용은 [PowerShell 7과 PowerShell 5.1 함께 사용](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51)을 참조하세요.

### <a name="conflict-with-iis-shared-configuration"></a>IIS 공유 구성과 충돌

웹 서버 클러스터가 있는 경우 [공유 구성](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)을 사용하고 있을 수 있습니다.
HttpModule은 해당 공유 구성에 삽입할 수 없습니다.
각 웹 서버에서 Enable 명령을 실행하여 각 서버의 GAC에 DLL을 설치합니다.

Enable 명령을 실행한 후 다음 단계를 완료합니다.
1. 공유 구성 디렉터리로 이동하여 applicationHost.config 파일을 찾습니다.
2. 구성의 모듈 섹션에 다음 줄을 추가합니다.
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 중첩 애플리케이션

버전 1.0에서 IIS의 중첩 애플리케이션을 계측하지 않습니다.

### <a name="advanced-sdk-configuration-isnt-available"></a>고급 SDK 구성은 사용할 수 없습니다.

SDK 구성은 버전 1.0에서 최종 사용자에게 노출되지 않습니다.

    
    
## <a name="troubleshooting"></a>문제 해결
    
### <a name="troubleshooting-powershell"></a>PowerShell 문제 해결

#### <a name="determine-which-modules-are-available"></a>사용할 수 있는 모듈 확인
`Get-Module -ListAvailable` 명령을 사용하여 설치된 모듈을 확인할 수 있습니다.

#### <a name="import-a-module-into-the-current-session"></a>모듈을 현재 세션으로 가져오기
모듈이 PowerShell 세션으로 로드되지 않은 경우 `Import-Module <path to psd1>` 명령을 사용하여 수동으로 로드할 수 있습니다.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Application Insights Agent 모듈 문제 해결

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Application Insights Agent 모듈에서 사용할 수 있는 명령을 나열합니다.
`Get-Command -Module Az.ApplicationMonitor` 명령을 실행하여 사용할 수 있는 명령을 가져옵니다.

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Application Insights Agent 모듈의 현재 버전 확인
`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 명령을 실행하여 모듈에 대한 다음 정보를 표시합니다.
   - PowerShell 모듈 버전
   - Application Insights SDK 버전
   - PowerShell 모듈의 파일 경로
    
해당 cmdlet을 사용하는 방법에 대한 자세한 내용은 [API 참조](status-monitor-v2-api-reference.md)를 참조하세요.


### <a name="troubleshooting-running-processes"></a>실행 중인 프로세스 문제 해결

계측된 컴퓨터에서 프로세스를 검사하여 모든 DLL을 로드했는지와 환경 변수를 설정했는지 확인할 수 있습니다.
모니터링이 작동하는 경우 12개 이상의 DLL이 로드되어야 합니다.

* `Get-ApplicationInsightsMonitoringStatus -InspectProcess` 명령을 사용하여 DLL을 확인합니다.
* `(Get-Process -id {PID}).StartInfo.EnvironmentVariables` 명령을 사용하여 환경 변수를 확인합니다. 다음은 작업자 프로세스 또는 dotnet core 프로세스에 설정된 환경 변수입니다.

```
COR_ENABLE_PROFILING=1
COR_PROFILER={324F817A-7420-4E6D-B3C1-143FBED6D855}
COR_PROFILER_PATH_32=Path to MicrosoftInstrumentationEngine_x86.dll
COR_PROFILER_PATH_64=Path to MicrosoftInstrumentationEngine_x64.dll
MicrosoftInstrumentationEngine_Host={CA487940-57D2-10BF-11B2-A3AD5A13CBC0}
MicrosoftInstrumentationEngine_HostPath_32=Path to Microsoft.ApplicationInsights.ExtensionsHost_x86.dll
MicrosoftInstrumentationEngine_HostPath_64=Path to Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
MicrosoftInstrumentationEngine_ConfigPath32_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftInstrumentationEngine_ConfigPath64_Private=Path to Microsoft.InstrumentationEngine.Extensions.config
MicrosoftAppInsights_ManagedHttpModulePath=Path to Microsoft.ApplicationInsights.RedfieldIISModule.dll
MicrosoftAppInsights_ManagedHttpModuleType=Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule
ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
DOTNET_STARTUP_HOOKS=Path to Microsoft.ApplicationInsights.StartupHook.dll
```

해당 cmdlet을 사용하는 방법에 대한 자세한 내용은 [API 참조](status-monitor-v2-api-reference.md)를 참조하세요.


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView를 사용하여 ETW 로그 수집

#### <a name="setup"></a>설치 프로그램

1. [GitHub](https://github.com/Microsoft/perfview/releases)에서 PerfView.exe 및 PerfView64.exe를 다운로드합니다.
2. PerfView64.exe를 시작합니다.
3. **고급 옵션** 을 확장합니다.
4. 다음 확인란의 선택을 취소합니다.
    - **Zip**
    - **병합**
    - **.NET 기호 컬렉션**
5. 다음과 같은 **추가 공급자** 를 설정합니다.`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,252e28f4-43f9-5771-197a-e8c7e750a984,f9c04365-1d1f-5177-1cdc-a0b0554b6903`


#### <a name="collecting-logs"></a>로그 수집

1. 관리자 권한이 있는 명령 콘솔에서 `iisreset /stop` 명령을 실행하여 IIS 및 모든 웹앱을 끕니다.
2. PerfView에서 **컬렉션 시작** 을 선택합니다.
3. 관리자 권한이 있는 명령 콘솔에서 `iisreset /start` 명령을 실행하여 IIS를 시작합니다.
4. 앱을 찾아봅니다.
5. 앱을 로드한 후 PerfView로 돌아가서 **수집 중지** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 누락되었을 수 있는 매개 변수에 대해 알아보려면 [API 참조](status-monitor-v2-overview.md#powershell-api-reference)를 참조하세요.
