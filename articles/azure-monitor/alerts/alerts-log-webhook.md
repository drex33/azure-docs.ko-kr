---
title: Azure Alerts의 로그 경고에 대한 웹후크 작업
description: 웹후크 작업 및 사용 가능한 사용자 지정을 사용하여 로그 경고 푸시를 구성하는 방법을 설명합니다.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 27e92c0434dcc78600f6b5a28ab6bb346c1ac87d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012324"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업

[로그 경고](alerts-log.md)는 [웹후크 작업 그룹 구성](./action-groups.md#webhook)을 지원합니다. 이 문서에서는 사용할 수 있는 속성에 대해 설명합니다. 웹후크 작업을 사용하면 단일 HTTP POST 요청을 호출할 수 있습니다. 호출되는 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 알고 있어야 합니다.

> [!NOTE]
> 웹후크 통합에 [일반 경고 스키마](../alerts/alerts-common-schema.md)를 사용하는 것이 좋습니다. 일반 경고 스키마는 Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공합니다. 사용자 지정 JSON 페이로드가 정의된 로그 경고 규칙의 경우 일반 경고 스키마를 사용하도록 설정하면 페이로드 스키마가 [여기](../alerts/alerts-common-schema-definitions.md#log-alerts)에 설명된 스키마로 되돌아갑니다. 즉, 사용자 지정 JSON 페이로드를 정의하려면 웹후크에서 일반 경고 스키마를 사용할 수 없습니다. 일반 스키마가 사용하도록 설정된 경고의 크기 상한은 경고당 256KB이며 더 큰 경고에는 검색 결과가 포함되지 않습니다. 검색 결과가 포함되지 않은 경우 `LinkToFilteredSearchResultsAPI` 또는 `LinkToSearchResultsAPI`를 사용하여 Log Analytics API를 통해 쿼리 결과에 액세스해야 합니다.

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 로그 경고의 웹후크에 대한 샘플 페이로드를 보여 줍니다. 샘플 페이로드에는 페이로드가 표준이며 사용자 지정인 경우의 예제가 포함됩니다.

### <a name="log-alert-for-all-resources-logs-from-api-version-2020-08-01"></a>모든 리소스 로그에 대한 로그 경고(API `2020-08-01` 버전)

다음 샘플 페이로드는 리소스 로그를 기반으로 하는 로그 경고에 사용되는 표준 webhook에 대한 것입니다.

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-for-log-analytics-up-to-api-version-2018-04-16"></a>Log Analytics에 대한 로그 경고(API `2018-04-16` 버전까지)
다음 샘플 페이로드는 Log Analytics를 기반으로 하는 경고에 사용되는 표준 웹후크 작업에 대한 것입니다.

> [!NOTE]
> “심각도” 필드 값은 [레거시 Log Analytics 경고 API](./api-alerts.md)에서 [현재 scheduledQueryRules API로 전환](../alerts/alerts-log-api-switch.md)한 경우 변경됩니다.

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights-up-to-api-version-2018-04-16"></a>애플리케이션 Insights 대한 로그 경고(API `2018-04-16` 버전까지)
다음 샘플 페이로드는 Application Insights 리소스를 기반으로 하는 로그 경고에 사용되는 표준 웹후크에 대한 것입니다.
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-with-a-custom-json-payload-up-to-api-version-2018-04-16"></a>사용자 지정 JSON 페이로드를 사용하여 로그 경고(API `2018-04-16` 버전까지)

> [!NOTE]
> 사용자 지정 JSON 기반 webhook는 API 버전 에서 지원되지 `2021-08-01` 않습니다.

기본 웹후크 작업 속성 및 사용자 지정 JSON 매개 변수 이름:

| 매개 변수 | 변수 | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |경고 규칙의 이름입니다. |
| *심각도* |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| *AlertThresholdOperator* |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다. |
| *AlertThresholdValue* |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| *LinkToSearchResults* |#linktosearchresults |경고를 만든 쿼리에서 레코드를 반환하는 Analytics 포털에 대한 링크입니다. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |경고를 만든 쿼리에서 레코드를 반환하는 Analytics API에 대한 링크입니다. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |경고를 만든 차원 값 조합으로 필터링된 쿼리에서 레코드를 반환하는 Analytics 포털에 대한 링크입니다. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |경고를 만든 차원 값 조합으로 필터링된 쿼리에서 레코드를 반환하는 Analytics API에 대한 링크입니다. |
| *ResultCount* |#searchresultcount |검색 결과의 레코드 수입니다. |
| *검색 간격 종료 시간* |#searchintervalendtimeutc |UTC 형식의 쿼리 종료 시간(mm/dd/yyyy HH:mm:ss AM/PM 형식)입니다. |
| *검색 간격* |#searchinterval |경고 규칙의 시간으로, HH:mm:ss 형식입니다. |
| *검색 간격 시작 시간* |#searchintervalstarttimeutc |UTC 형식의 쿼리 시작 시간(mm/dd/yyyy HH:mm: ss AM/PM 형식)입니다. 
| *SearchQuery* |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| *SearchResults* |"IncludeSearchResults": true|쿼리를 통해 JSON 테이블로 반환된 레코드입니다(처음 1000개 레코드로 제한). "IncludeSearchResults": true는 사용자 지정 JSON 웹후크 정의에 최상위 속성으로 추가됩니다. |
| *차원* |"IncludeDimensions": true|JSON 섹션으로 해당 경고를 트리거한 차원 값 조합입니다. "IncludeDimensions": true는 사용자 지정 JSON 웹후크 정의에 최상위 속성으로 추가됩니다. |
| *경고 유형*| #alerttype | [메트릭 측정 또는 결과 수](./alerts-unified-log.md#measure)로 구성된 로그 경고 규칙 유형입니다.|
| *WorkspaceID* |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| *애플리케이션 ID* |#applicationid |Application Insights 앱의 ID입니다. |
| *구독 ID* |#subscriptionid |사용되는 Azure 구독의 ID입니다. | 

**webhook에 사용자 지정 JSON 페이로드 포함을** 사용하여 매개 변수를 사용하여 사용자 지정 JSON 페이로드를 얻을 수 있습니다. 추가 속성을 생성할 수도 있습니다.
예를 들어 *text* 라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다. 이 웹후크가 호출하는 서비스에는 다음 매개 변수가 필요합니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 웹후크에 보낼 때 다음과 같은 내용으로 확인될 수 있습니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
사용자 지정 웹후크의 변수는 JSON 인클로저 내에서 지정해야 합니다. 예를 들어 webhook 예제에서 "#searchresultcount"을 참조하면 경고 결과에 따라 출력됩니다.

검색 결과를 포함하려면 사용자 지정 JSON에서 최상위 속성으로 **IncludeSearchResults** 를 추가합니다. 검색 결과는 JSON 구조로 포함되므로 사용자 지정 정의된 필드에서 결과를 참조할 수 없습니다. 

> [!NOTE]
> **웹후크용 사용자 지정 JSON 페이로드 포함** 옵션 옆에 있는 **웹후크 보기** 단추는 제공된 항목의 미리 보기를 표시합니다. 실제 데이터는 포함되지 않지만 사용될 JSON 스키마를 나타냅니다.

예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음 구성을 사용합니다. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

다음은 로그 경고에 대한 사용자 지정 웹후크 작업의 샘플 페이로드입니다.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>다음 단계
- [Azure Alerts의 로그 경고](./alerts-unified-log.md)에 대해 알아보기
- [Azure에서 로그 경고를 관리](alerts-log.md)하는 방법을 이해합니다.
- [Azure에서 작업 그룹](./action-groups.md) 만들기 및 관리
- [Application Insights](../logs/log-query-overview.md)에 대해 자세히 알아봅니다.
- [로그 쿼리](../logs/log-query-overview.md)에 대해 자세히 알아봅니다.
