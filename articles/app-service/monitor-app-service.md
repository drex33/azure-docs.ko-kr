---
title: Azure Monitor를 사용하여 App Service 모니터링
description: App Service를 모니터링하는 방법을 알아보려면 여기서 시작합니다.
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 537173792c4e14ac1dfdb142d8ab15e3e68e5649
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230589"
---
# <a name="monitoring-app-service"></a>App Service 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 App Service에서 생성되어 [Azure Monitor](../azure-monitor/overview.md)에 제공되는 모니터링 데이터에 대해 설명합니다. 사용자는 App Service 앱을 디버그하는 데 도움이 되는 [기본 제공 진단으로 리소스를 모니터링](troubleshoot-diagnostic-logs.md)할 수도 있습니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.

> [!NOTE]
> Azure Monitor와 App Service의 통합은 [미리 보기](https://aka.ms/appsvcblog-azmon)에 있습니다.
>

## <a name="monitoring-data"></a>데이터 모니터링 

App Service는 [Azure 리소스에서 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

[*App Service* 모니터링 데이터 참조](monitor-app-service-reference.md)에서 App Service에서 생성된 메트릭 및 로그 메트릭에 관한 자세한 정보를 확인하세요.

또한 App Service는 앱을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 기본 제공 로그 사용에 관한 자세한 내용은 [진단 로깅 사용](troubleshoot-diagnostic-logs.md)을 참조하세요. App Service 인스턴스를 모니터링하려면 [상태 검사를 사용하여 App Service 인스턴스 모니터링](monitor-instances-health-check.md)을 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. *App Service* 의 범주는 [App Service 모니터링 데이터 참조](monitor-app-service-reference.md#resource-logs)에 나열되어 있습니다.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 *App Service* 에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

App Service에 대해 수집되는 플랫폼 메트릭의 목록은 [App Service 모니터링 데이터 참조 메트릭](monitor-app-service-reference.md#metrics)을 참조하세요.  

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)에서 설명합니다.

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준 이벤트에 인사이트를 제공하는 플랫폼 로그의 유형입니다. 독립적으로 보거나, Azure Monitor 로그로 라우팅할 수 있습니다. Azure Monitor 로그로 라우팅하면 Log Analytics를 사용하여 복잡한 쿼리를 실행할 수 있습니다.

App Service에 대해 수집되는 리소스 로그 유형 목록은 [App Service 모니터링 데이터 참조](monitor-app-service-reference.md#resource-logs)를 확인하세요.  

Azure Monitor 로그 및 Log Analytics에서 사용되는 쿼리할 수 있는 테이블 목록은 [App Service 모니터링 데이터 참조](monitor-app-service-reference.md#azure-monitor-logs-tables)를 확인하세요.  

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> App Service 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 리소스로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 [리소스]의 데이터 또는 다른 Azure 리소스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

다음 샘플 쿼리는 `AppServiceAppLogs`를 사용하여 앱 로그를 모니터링하는 데 도움이 될 수 있습니다.

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

다음 샘플 쿼리는 `HTTP response code`가 `500` 이상인 `AppServiceHTTPLogs`를 사용하여 HTTP 로그를 모니터링하는 데 유용할 수 있습니다.

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

다음 샘플 쿼리는 `AppServiceConsoleLogs` 및 `AppserviceHTTPLogs`를 조인하여 HTTP 500 오류를 모니터링하는 데 도움이 될 수 있습니다.

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

더 많은 샘플 쿼리는 [App Service를 위한 Azure Monitor 쿼리](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries)를 참조하세요.

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다.

App Service에서 애플리케이션을 실행하는 경우 [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights)는 추가 유형의 경고를 제공할 수 있습니다.

다음 표에는 App Service에 대한 일반적인 경고 규칙과 권장되는 경고 규칙이 나와 있습니다.

| 경고 유형 | 조건 | 예제  |
|:---|:---|:---|
| 메트릭 | 평균 연결| 연결 수가 설정된 값을 초과하는 경우|
| 메트릭 | HTTP 404| HTTP 404 응답이 설정된 값을 초과하는 경우|
| 메트릭 | HTTP 서버 오류| HTTP 5xx 오류가 설정된 값을 초과하는 경우|
| 활동 로그 | 웹앱 만들기 또는 업데이트 | 앱이 만들어지거나 업데이트되는 경우|
| 활동 로그 | 웹앱 삭제 | 앱이 삭제되는 경우|
| 활동 로그 | 웹앱 다시 시작| 앱이 다시 시작되는 경우|
| 활동 로그 | 웹 앱 중지| 앱이 중지되는 경우|

## <a name="next-steps"></a>다음 단계

- App Service로 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조로서 [App Service 모니터링 데이터 참조](monitor-app-service-reference.md)를 확인하세요.

- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.