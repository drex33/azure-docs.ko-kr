---
title: 'Application Insights: 언어, 플랫폼 및 통합 | Microsoft Docs'
description: Application Insights에 사용할 수 있는 언어, 플랫폼 및 통합
ms.topic: conceptual
ms.date: 10/29/2021
ms.reviewer: olegan
ms.openlocfilehash: 44c08d1008691826299d1fa41507df26064f6274
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462029"
---
# <a name="supported-languages"></a>지원되는 언어

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>지원되는 플랫폼 및 프레임워크

### <a name="azure-service-integration-portal-enablement-arm-deployments"></a>Azure 서비스 통합 (포털 활성화, ARM 배포)
* [Azure VM 및 Azure 가상 머신 확장 집합](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [Azure Functions](../../azure-functions/functions-monitoring.md)
* 웹 역할과 작업자 역할을 모두 포함하는 [Azure Cloud Services](./cloudservices.md)

### <a name="auto-instrumentation-enable-without-code-changes"></a>자동 계측 (코드 변경 없이 사용)
* [ASP.NET - IIS로 호스트되는 웹앱용](./status-monitor-v2-overview.md)
* [Java](./java-in-process-agent.md)

### <a name="manual-instrumentation--sdk-some-code-changes-required"></a>수동 계측/SDK (일부 코드 변경 필요)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript-웹](./javascript.md)
  * [React](./javascript-react-plugin.md)
  * [React Native](./javascript-react-native-plugin.md)
  * [Angular](./javascript-angular-plugin.md)
* [Windows 데스크톱 애플리케이션, 서비스 및 작업자 역할](./windows-desktop.md)
* [유니버설 Windows 앱](../app/mobile-center-quickstart.md)(App Center)
* [Android](../app/mobile-center-quickstart.md)(App Center)
* [iOS](../app/mobile-center-quickstart.md)(App Center)

> [!NOTE]
> OpenTelemetry 기반 계측은 [c #, Node.js 및 Python](opentelemetry-enable.md)에 대 한 미리 보기 상태로 제공 됩니다. 각 언어의 공식 설명서의 시작 부분에서 설명한 제한 사항을 검토 하세요. 모든 기능을 사용 해야 하는 사용자는 기존 Application Insights sdk를 사용 해야 합니다.

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
다른 여러 커뮤니티 지원 Application Insights sdk가 있습니다. 그러나 Azure Monitor은이 페이지에 나열 된 지원 되는 계측 옵션을 사용할 때만 지원 합니다. 다른 언어에 대 한 지원을 확장할 수 있는 기회를 지속적으로 평가 하 고 있습니다. 최신 SDK 뉴스에 대 한 [Application Insights Azure 업데이트](https://azure.microsoft.com/updates/?query=application%20insights) 를 따르세요.
