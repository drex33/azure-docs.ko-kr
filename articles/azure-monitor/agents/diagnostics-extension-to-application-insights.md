---
title: Azure Diagnostics 데이터를 Application Insights에 보내기
description: Application Insights에 데이터를 보내도록 Azure Diagnostics 공용 구성을 업데이트 합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 8589caaa888e9a9b1156563bd38090c922ebfcc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020866"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Application Insights에 클라우드 서비스, Virtual Machine 또는 Service Fabric 데이터 보내기
클라우드 서비스, Virtual Machines, Virtual Machine Scale Sets 및 Service Fabric은 모두 Azure Diagnostics 확장을 사용하여 데이터를 수집합니다.  Azure 진단은 데이터를 Azure Storage 테이블에 보냅니다.  그러나 Azure Diagnostics 확장 1.5 이상을 사용하여 다른 위치에 데이터의 하위 세트나 전체를 파이핑할 수 있습니다.

이 문서에서는 Azure Diagnostics 확장에서 Application Insights에 데이터를 전송하는 방법을 설명합니다.

## <a name="diagnostics-configuration-explained"></a>설명한 진단 구성
Azure 진단 확장 1.5는 진단 데이터를 보낼 수 있는 추가 위치인 싱크에 소개되었습니다.

Application Insights에 대한 싱크 예제 구성:

```xml
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **싱크** *name* 특성은 싱크를 고유하게 식별하는 문자열 값입니다.

- **ApplicationInsights** 요소는 Azure 진단 데이터를 보낼 Application Insights 리소스의 계측 키를 지정합니다.
    - 기존 Application Insights 리소스가 없는 경우 리소스 만들기 및 계측 키 가져오기에 대한 자세한 내용은 [새 Application Insights 리소스 만들기](../app/create-new-resource.md)를 참조하세요.
    - Azure SDK 2.8 이상에서 클라우드 서비스를 개발하는 경우 이 계측 키는 자동으로 채워집니다. 클라우드 서비스 프로젝트를 패키징할 때 값은 **APPINSIGHTS_INSTRUMENTATIONKEY** 서비스 구성을 기반으로 합니다. [Cloud Services와 함께 Application Insights 사용](../app/cloudservices.md)을 참조하세요.

- **채널** 요소는 하나 이상의 **채널** 요소를 포함합니다.
    - *name* 특성은 고유하게 해당 채널을 의미합니다.
    - *loglevel* 특성을 사용하면 채널이 허용하는 로그 수준을 지정할 수 있습니다. 정보가 많은 순서대로 사용 가능한 로그 수준은 다음과 같습니다.
        - 자세히
        - 정보
        - 경고
        - Error
        - 위험

채널은 필터처럼 작동하고 채널을 사용하면 대상 싱크에 보내는 특정 로그 수준을 선택할 수 있습니다. 예를 들어 자세한 정보 표시 로그를 수집하고 스토리지에 보내지만 오류만을 싱크에 보낼 수 있습니다.

다음 그래프에서는 이 관계를 보여 줍니다.

![진단 공용 구성](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

다음 그래프에서는 구성 값 및 작동 방법을 요약합니다. 계층 구조에서 다양한 수준의 구성에 여러 싱크를 포함할 수 있습니다. 최상위 수준의 싱크는 전역 설정이며 개별 요소에 지정된 싱크는 전역 설정에 재정의와 같은 역할을 합니다.

![Application Insights를 사용한 진단 싱크 구성](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>전체 싱크 구성 예제
다음은 공용 구성 파일의 완전한 예제로, 공용 구성 파일이 다음과 같은 작업을 합니다.
1. 모든 오류를 Application Insights로 보냅니다(**DiagnosticMonitorConfiguration** 노드에서 지정).
2. 또한 애플리케이션 로그(**로그** 노드에서 지정함)에 대한 자세한 정보 표시 수준 로그를 보냅니다.

```xml
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
이전 구성에서 다음 줄은 다음과 같은 의미가 있습니다.

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Azure 진단에서 수집한 모든 데이터 보내기

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Application Insights 싱크로 오류 로그만 보내기

```xml
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

```json
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Application Insights에 자세한 정보 표시 애플리케이션 로그 보내기

```xml
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>제한 사항

- **성능 카운터가 아닌 로그 유형인 채널** 성능 카운터 요소를 사용하여 채널을 지정하는 경우 무시됩니다.
- **채널에 대한 로그 수준은 Azure 진단에서 수집되는 로그 수준을 초과할 수 없습니다.** 예를 들어 로그 요소에서 애플리케이션 로그 오류를 수집하고 Application Insight 싱크에 자세한 정보 표시 로그를 보내려고 시도할 수 없습니다. *scheduledTransferLogLevelFilter* 특성은 항상 싱크에 전송하려는 로그와 같거나 더 많은 로그를 수집해야 합니다.
- **Application Insights에 Azure 진단 확장에서 수집된 Blob 데이터를 보낼 수 없습니다.** 예를 들어 *디렉터리* 노드에 지정된 모든 항목입니다. 크래시 덤프의 경우 실제 크래시 덤프는 Blob Storage에 보내지고 크래시 덤프가 생성된 알림이 Application Insights에 전송됩니다.

## <a name="next-steps"></a>다음 단계
* Application Insights에서 [Azure 진단 정보를 보는 방법](../app/cloudservices.md)에 대해 알아봅니다.
* [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)을 사용하여 애플리케이션에 대한 Azure 진단 확장을 사용하도록 설정합니다.
* [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)를 사용하여 애플리케이션에 대한 Azure 진단 확장을 사용하도록 설정합니다.

