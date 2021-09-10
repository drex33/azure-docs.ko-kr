---
title: 'Application Insights: 언어, 플랫폼 및 통합 | Microsoft Docs'
description: Application Insights에 사용할 수 있는 언어, 플랫폼 및 통합
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d388914badbd9ac8870a9d5e23370cd5b0319eea
ms.sourcegitcommit: 8154d7f8642d783f637cf6d857b4abbe28033f53
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113616400"
---
# <a name="supported-languages"></a>지원되는 언어

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>지원되는 플랫폼 및 프레임워크

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>이미 배포된 애플리케이션에 대한 계측(코드리스, 에이전트 기반)
* [Azure VM 및 Azure 가상 머신 확장 집합](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET - IIS로 호스트되는 웹앱용](./status-monitor-v2-overview.md)
* 웹 역할과 작업자 역할을 모두 포함하는 [Azure Cloud Services](./cloudservices.md)
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>코드를 통한 계측(SDK)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md)(App Center)
* [iOS](../app/mobile-center-quickstart.md)(App Center)
* [Java EE](./java-in-process-agent.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [유니버설 Windows 앱](../app/mobile-center-quickstart.md)(App Center)
* [Windows 데스크톱 애플리케이션, 서비스 및 작업자 역할](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>로깅 프레임워크
* [ILogger](./ilogger.md)
* [Log4Net, NLog 또는 System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J 또는 Logback](java-2x-trace-logs.md)
* [LogStash 플러그 인](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>내보내기 및 데이터 분석
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>지원되지 않는 SDK
다른 커뮤니티 지원 SDK가 있다는 사실을 알고 있습니다. 하지만 Azure Monitor는 이 페이지에 나열된 지원되는 SDK를 사용하는 경우에만 지원을 제공합니다. 다른 언어에 대한 지원을 확장할 기회를 지속적으로 평가하고 있으므로 [GitHub 발표](https://github.com/microsoft/ApplicationInsights-Announcements/issues) 페이지를 자주 확인하여 최신 SDK 뉴스를 받으세요. 

