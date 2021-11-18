---
title: 여러 Azure Monitor Application Insights 리소스 통합 | Microsoft Docs
description: 이 문서에서는 Azure Monitor Logs의 함수를 사용해 여러 Application Insights 리소스를 쿼리하고 해당 데이터를 표시하는 방법을 자세히 설명합니다.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c50a653d4c30324c7deb86be49e1328da842ecd9
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132763509"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>여러 Azure Monitor Application Insights 리소스 통합 
이 문서에서는 모든 Application Insights 로그 데이터가 각기 다른 Azure 구독에 있더라도 한 곳에서 쿼리하고 보는 방법을 설명합니다. 사용이 중단된 Application Insights 커넥터 대신 이 방법을 사용할 수 있습니다. 단일 쿼리에 포함할 수 있는 Application Insights 리소스의 수는 100개로 제한됩니다.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>여러 Application Insights 리소스를 쿼리하는 권장 방식 
여러 Application Insights 리소스의 목록을 쿼리에 표시하는 방식은 번거로우며 유지 관리하기가 어려울 수 있습니다. 이러한 방식 대신 함수를 사용해 애플리케이션 범위에서 쿼리 논리를 분리할 수 있습니다.  

이 예제에서는 여러 Application Insights 리소스를 모니터링하고 애플리케이션 이름을 기준으로 실패한 요청의 수를 시각화하는 방법을 보여 줍니다.

애플리케이션 목록에 합집합 연산자를 사용하는 함수를 만든 다음, *applicationsScoping* 이라는 별칭으로 작업 영역의 쿼리를 함수로 저장합니다. 

언제든지 포털에서 작업 영역의 쿼리 탐색기로 이동한 후 편집할 함수를 선택하고 저장하여, 또는 `SavedSearch` PowerShell cmdlet을 사용하여 나열된 애플리케이션을 수정할 수 있습니다. 

>[!NOTE]
>작업 영역 및 애플리케이션을 포함한 경고 규칙 리소스의 액세스 유효성 검사는 경고 생성 시 수행되므로, 이 메서드는 로그 경고와 함께 사용할 수 없습니다. 경고를 만든 후 함수에 새 리소스를 추가하는 것은 지원되지 않습니다. 로그 경고에 리소스 범위를 지정하는 함수를 사용하려면 포털에서 경고 규칙을 편집하거나 Resource Manager 템플릿으로 범위가 지정된 리소스를 업데이트해야 합니다. 또는 로그 경고 쿼리에 리소스 목록을 포함할 수 있습니다.

`withsource= SourceApp` 명령은 로그를 전송한 애플리케이션을 지정하는 열을 결과에 추가합니다. 이 예제에서 구문 분석 연산자는 선택적 항목으로, SourceApp 속성에서 애플리케이션 이름을 추출하기 위해 사용됩니다. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

이제 리소스 간 쿼리에 applicationsScoping 함수를 사용할 수 있습니다.  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

ApplicationsScoping 함수는 Application Insights 데이터 구조를 반환하므로 쿼리는 작업 영역에서 실행되지만 Application Insights 스키마를 사용합니다. 함수 별칭은 정의된 모든 애플리케이션에서 요청의 합집합을 반환합니다. 그런 다음, 쿼리는 실패한 요청에 대해 필터링하고 애플리케이션별로 추세를 시각화합니다.

![쿼리 간 결과 예제](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>로그 경고의 [리소스 간 쿼리](../logs/cross-workspace-query.md)는 현재 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrule-2018-04-16/scheduled-query-rules)에서만 지원됩니다. 레거시 Log Analytics 경고 API를 사용하는 경우 [현재 API로 전환](../alerts/alerts-log-api-switch.md)해야 합니다. [예제 템플릿을 참조하세요](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights 및 Log Analytics 작업 영역 스키마의 차이점
다음 표에는 Log Analytics 및 Application Insights의 스키마 간 차이점이 나와 있습니다.  

| Log Analytics 작업 영역 속성| Application Insights 리소스 속성|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | 위치 |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| 브라우저 | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| 국가 | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | 성공 | 
| ResponseCode | resultCode | 
| 역할 | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>다음 단계

Application Insights 앱에 대한 자세한 정보를 보려면 [로그 검색](../logs/log-query-overview.md)을 사용합니다.

