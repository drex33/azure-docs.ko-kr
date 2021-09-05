---
title: Azure Application Insights 에이전트 개요 | Microsoft Docs
description: Application Insights 에이전트의 개요입니다. 웹 사이트 재배포 없이 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스트되는 ASP.NET 웹앱에 사용할 수 있습니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 1fcdca5aaedb9d3b0e86e02e931eb7e38c16cc25
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731859"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>온-프레미스 서버용 Azure Monitor Application Insights 에이전트 배포

> [!IMPORTANT]
> 이 지침은 Application Insights 에이전트의 온-프레미스 및 비 Azure 클라우드 배포에 권장됩니다. [Azure 가상 머신 및 가상 머신 확장 집합 배포](./azure-vm-vmss-apps.md)에 권장되는 방법은 여기에 나와 있습니다.

Application Insights 에이전트(이전 이름은 상태 모니터 V2)는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)에 게시된 PowerShell 모듈입니다.
Application Insights 에이전트는 상태 모니터를 대체합니다.
원격 분석은 앱을 [모니터링](./app-insights-overview.md)할 수 있는 Azure Portal로 전송됩니다.

> [!NOTE]
> 이 모듈은 현재 IIS로 호스트되는 .NET 및 .NET Core 웹앱의 코드 없는 계측을 지원합니다. SDK를 사용하여 Java 및 Node.js 애플리케이션을 계측합니다.

## <a name="powershell-gallery"></a>PowerShell 갤러리

Application Insights 에이전트는 여기(https://www.powershellgallery.com/packages/Az.ApplicationMonitor )에 있습니다.

![PowerShell 갤러리](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>지침
- 간결한 코드 샘플을 가져오려면 [시작 지침](status-monitor-v2-get-started.md)을 참조하세요.
- 시작하는 방법에 대한 심층 분석은 [자세한 지침](status-monitor-v2-detailed-instructions.md)을 참조하세요.

## <a name="powershell-api-reference"></a>PowerShell API 참조
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- Application Insights 에이전트가 프록시 설치를 지원하나요?

  *예*. Application Insights 에이전트를 다운로드하는 여러 방법이 있습니다. 컴퓨터에서 인터넷에 액세스할 수 있는 경우 `-Proxy` 매개 변수를 사용하여 PowerShell 갤러리에 온보딩할 수 있습니다.
모듈을 수동으로 다운로드하여 컴퓨터에 설치하거나 직접 사용할 수도 있습니다.
이러한 각 옵션은 [자세한 지침](status-monitor-v2-detailed-instructions.md)에 설명되어 있습니다.

- 상태 모니터 V2는 ASP.NET Core 애플리케이션을 지원하나요?

  *예*. [Application Insights Agent 2.0.0-beta1](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/2.0.0-beta1)부터 IIS에서 호스트되는 ASP.NET Core 애플리케이션이 지원됩니다.

- 사용이 성공했는지 어떻게 확인하나요?

  - [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) cmdlet을 사용하여 사용이 성공했는지 확인할 수 있습니다.
  - 앱에서 원격 분석을 전송하고 있는지 빠르게 확인하려면 [라이브 메트릭](./live-stream.md)을 사용하는 것이 좋습니다.

  - [Log Analytics](../logs/log-analytics-tutorial.md)를 사용하여 현재 원격 분석을 보내고 있는 모든 클라우드 역할을 나열할 수도 있습니다.
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>다음 단계

원격 분석 보기:

* [메트릭을 탐색하여](../essentials/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
* [이벤트 및 로그를 검색](./diagnostic-search.md)하여 문제를 진단합니다.
* [분석을 통해](../logs/log-query-overview.md) 고급 쿼리를 수행합니다.
* [대시보드를 만듭니다](./overview-dashboard.md).

원격 분석 더 추가:

* [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
* [웹 클라이언트 원격 분석을 추가](./javascript.md)하여 웹 페이지 코드에서 예외를 확인하고 추적 호출을 활성화합니다.
* [Application Insights SDK를 코드에 추가](./asp-net.md)하여 추적 및 로그 호출을 삽입할 수 있도록 합니다.
