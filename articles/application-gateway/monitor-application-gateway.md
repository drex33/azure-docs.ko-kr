---
title: Azure Application Gateway 모니터링
description: Azure Application Gateway를 모니터링하는 방법을 알아보려면 여기에서 시작하세요.
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295983"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>Azure Application Gateway 모니터링
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 

이 문서에서는 Azure Application Gateway에서 생성된 모니터링 데이터에 대해 설명합니다. Azure Application Gateway는 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure Portal의 모니터링 개요 페이지
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

각 Application Gateway에 대한 Azure Portal의 **개요** 페이지에는 다음 메트릭이 포함되어 있습니다.

- 총 요청 수 합계
- 실패한 요청 합계
- HttpStatus별 응답 상태 합계
- 처리량 합계
- 동시 연결 수 합계
- BackendPool HttpSettings별 평균 정상 호스트 수
- BackendPool HttpSettings별 평균 비정상 호스트 수

이는 Application Gateway에 사용할 수 있는 메트릭의 하위 집합일 뿐입니다. 자세한 내용은 [Azure Application Gateway 데이터 참조 모니터링](monitor-application-gateway-reference.md)을 참조하세요.


## <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Azure의 일부 서비스에는 서비스 모니터링을 위한 시작점을 제공하는 Azure Portal에 특별히 집중적으로 미리 작성된 모니터링 대시보드가 있습니다. 이러한 특수 대시보드를 "인사이트"라고 합니다.

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

구성 없이 배포된 모든 네트워크 리소스(Application Gateway 포함)에 대한 상태 및 메트릭에 대한 포괄적인 뷰를 제공하는 Azure Monitor Network Insights입니다. 자세한 내용은 [Azure Monitor Network Insights](../azure-monitor/insights/network-insights-overview.md)를 참조하세요.

## <a name="monitoring-data"></a>데이터 모니터링 

<!-- REQUIRED. Please keep headings in this order -->
Azure Application Gateway는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대해서는 [Azure 리소스의 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. 

Azure Application Gateway에서 생성된 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Azure Application Gateway 데이터 참조 모니터링](monitor-application-gateway-reference.md)을 참조하세요.

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>수집 및 라우팅

<!-- REQUIRED. Please keep headings in this order -->

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Application Gateway의 범주는 [Azure Application Gateway 모니터링 데이터 참조](monitor-application-gateway-reference.md#resource-logs)에 나열되어 있습니다.

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Azure Application Gateway에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
Azure Application Gateway에 대해 수집된 플랫폼 메트릭 목록은 [Application Gateway 데이터 참조 메트릭 모니터링](monitor-application-gateway-reference.md#metrics)을 참조하세요.  


[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>로그 분석

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공통 스키마는 [Azure 리소스 로그에 대한 공통 및 서비스별 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에 설명되어 있습니다. 

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Azure Application Gateway에 대해 수집된 리소스 로그 유형 목록은 [Azure Application Gateway 데이터 참조 모니터링](monitor-application-gateway-reference.md#resource-logs)을 참조하세요.

Azure Monitor 로그에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [Azure Application Gateway 모니터링 데이터 참조](monitor-application-gateway-reference.md#azure-monitor-logs-tables)를 참조하세요.  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> Application Gateway 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 Application Gateway로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 Application Gateway의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/)를 참조하세요.

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

다음 쿼리를 사용하여 Application Gateway 리소스를 모니터링할 수 있습니다. 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>경고

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다

<!-- only include next line if applications run on your service and work with App Insights. --> 

Application Gateway [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights)를 사용하는 애플리케이션을 만들거나 실행하는 경우 추가 유형의 알림을 제공할 수 있습니다.
<!-- end -->

다음 표에는 Application Gateway에 대한 공통 및 권장 경고 규칙이 나열되어 있습니다.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

**Application Gateway v1**

| 경고 유형 | 조건 | 설명  |
|:---|:---|:---|
|메트릭|CPU 사용률이 80%를 넘습니다.|정상적인 조건에서 CPU 사용량은 Application Gateway 뒤에 호스트되는 웹 사이트에서 대기 시간이 발생하고 클라이언트 경험에 방해가 될 수 있으므로 일반적으로 90%를 초과해서는 안 됩니다.|
|메트릭|비정상 호스트 수가 임계값을 초과함|Application Gateway에서 제대로 프로브할 수 없는 백 엔드 서버 수를 나타냅니다. 해당 메트릭을 통해 Application Gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 해당 수가 백 엔드 용량의 20%를 초과하면 경고를 표시합니다.|
|메트릭|응답 상태(4xx, 5xx)가 임계값을 초과함|Application Gateway 응답 상태가 4xx 또는 5xx인 경우 일시적인 문제 때문에 가끔씩 4xx 또는 5xx 응답이 표시될 수 있습니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.|
|메트릭|실패한 요청이 임계값을 초과함|실패한 요청 지표가 임계값을 초과하는 경우. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.|


**Application Gateway v2**

| 경고 유형 | 조건 | 설명  |
|:---|:---|:---|
|메트릭|컴퓨팅 단위 사용률이 평균 사용량의 75%를 초과함|컴퓨팅 단위는 Application Gateway 컴퓨팅 사용률을 측정한 것입니다. 지난 한 달간의 평균 컴퓨팅 단위 사용량을 확인하고 75%를 초과하는 경우 경고를 설정합니다.|
|메트릭|용량 단위 사용률이 최고 사용량의 75%를 초과함|용량 단위는 처리량, 컴퓨팅 및 연결 수 면에서 전체 게이트웨이 사용률을 나타냅니다. 지난 한 달간의 최대 용량 단위 사용량을 확인하고 해당 사용량의 75%를 초과하는 경우 경고를 설정합니다.|
|메트릭|비정상 호스트 수가 임계값을 초과함|애플리케이션 게이트웨이에서 제대로 프로브할 수 없는 백 엔드 서버 수를 나타냅니다. 해당 메트릭을 통해 Application Gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 해당 수가 백 엔드 용량의 20%를 초과하면 경고를 표시합니다.|
|메트릭|응답 상태(4xx, 5xx)가 임계값을 초과함|Application Gateway 응답 상태가 4xx 또는 5xx인 경우 일시적인 문제 때문에 가끔씩 4xx 또는 5xx 응답이 표시될 수 있습니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.|
|메트릭|실패한 요청이 임계값을 초과함|실패한 요청 메트릭이 임계값을 초과하는 경우 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.|
|메트릭|백 엔드의 마지막 바이트 응답 시간이 임계값을 초과함|백 엔드 서버와 연결을 설정하기 시작한 시간과 응답 본문으로부터 마지막 바이트를 수신한 시간까지의 간격을 나타냅니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값보다 더 많은 경우 경고를 만듭니다.|
|메트릭|Application Gateway 총 시간이 임계값을 초과함|Application Gateway가 HTTP 요청의 첫 번째 바이트를 받은 시점부터 마지막 응답 바이트가 클라이언트로 전송된 시간까지의 간격입니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값보다 더 많은 경우 경고를 생성해야 합니다.|

## <a name="next-steps"></a>다음 단계

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- Application Gateway에서 생성된 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [모니터링 Application Gateway 데이터 참조](monitor-application-gateway-reference.md)를 참조하세요.

- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.