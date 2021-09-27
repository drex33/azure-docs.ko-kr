---
title: Azure Monitor에서 로그 경고 문제 해결 | Microsoft Docs
description: Azure의 로그 경고 규칙에 대한 일반적인 문제, 오류 및 해결 방법입니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aafd6f2e0d5c10b1e3e1698176734a83083ef72d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744510"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고 문제 해결  

이 문서에서는 Azure Monitor에서 로그 경고와 관련된 일반적인 문제를 해결하는 방법을 설명합니다. 또한 로그 경고의 기능 및 구성과 관련된 일반적인 문제에 대한 해결 방법을 제공합니다.

설정된 빈도마다 [Log Analytics](../logs/log-analytics-tutorial.md) 쿼리를 사용하여 리소스 로그를 평가하고 결과에 따라 경고를 실행하는 데 로그 경고를 사용할 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용하여 하나 이상의 작업을 트리거할 수 있습니다. 로그 경고의 기능 및 용어에 대해 자세히 알아보려면 [Azure Monitor의 로그 경고](alerts-unified-log.md)를 참조하세요.

> [!NOTE]
> 이 문서에서는 Azure Portal에서 경고 규칙이 트리거되었으나 알림이 수신되지 않는 경우를 고려하지 않습니다. 그러한 경우에는 [경고에 대한 작업 또는 알림이 예상대로 작동하지 않음](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)을 참조하세요.

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

### <a name="data-ingestion-time-for-logs"></a>로그에 대한 데이터 수집 시간

Azure Monitor는 전 세계에서 테라바이트 단위의 고객 로그를 처리하므로 [로그 수집 대기 시간](../logs/data-ingestion-time.md)이 발생할 수 있습니다.

로그는 반 구조화된 데이터이며 근본적으로 메트릭보다 더 잠재적입니다. 발생한 경고에 4분 넘게 지연이 발생하는 경우 [메트릭 경고](alerts-metric-overview.md)를 사용하는 것이 좋습니다. [로그에 대한 메트릭 경고](alerts-metric-logs.md)를 사용하여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

대기 시간을 줄이기 위해 시스템은 경고 평가를 여러 번 다시 시도합니다. 데이터가 도착한 후에는 대부분의 경우에 로그 레코드 시간과 같지 않은 경고가 발생합니다.

### <a name="incorrect-query-time-range-configured"></a>잘못 구성된 쿼리 시간 범위

쿼리 시간 범위는 규칙 조건 정의에서 설정됩니다. 작업 영역 및 Application Insights의 경우 이 필드를 **기간** 이라고 합니다. 다른 모든 리소스 종류에서는 **쿼리 시간 범위 재정의** 라고 합니다. 로그 분석과 마찬가지로, 시간 범위는 쿼리 데이터를 지정된 범위로 제한합니다. **ago** 명령이 쿼리에 사용되더라도 시간 범위가 적용됩니다. 

예를 들어 쿼리는 텍스트에 **ago(1d)** 가 포함되어 있더라도 시간 범위가 60분인 경우 60분을 검색합니다. 시간 범위와 쿼리 시간 필터링이 일치해야 합니다. 예시의 경우, **기간** / **쿼리 시간 범위 재정의** 를 1일로 변경하면 예상대로 작동합니다.

![기간](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>작업이 경고 규칙에서 음소거됨

로그 경고는 설정된 시간 동안 발생한 경고 작업을 음소거하는 옵션을 제공합니다. 작업 영역 및 Application Insights의 경우 이 필드를 **경고 표시 안 함** 이라고 합니다. 다른 모든 리소스 종류에서는 **작업 음소거** 라고 합니다. 

일반적인 문제는 경고가 규칙 구성에 의해 음소거되었음에도 불구하고 서비스 문제로 인해 작업을 실행하지 않았다고 생각하는 것입니다.

![알림 표시 안 함](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="alert-scope-resource-has-been-moved-renamed-or-deleted"></a>경고 범위 리소스가 이동되었거나, 이름이 바뀌었거나, 삭제됨

경고 규칙을 작성하면 Log Analytics에서 사용자 ID에 대한 사용 권한 스냅샷을 만듭니다. 이 스냅샷은 규칙에 저장되고 규칙 범위 리소스, Azure Resource Manager ID를 포함합니다. 규칙 범위 리소스를 이동하거나, 이름을 바꾸거나, 삭제하는 경우 해당 리소스를 참조하는 모든 로그 경고 규칙이 중단됩니다. 제대로 작동하려면 새 Azure Resource Manager ID를 사용하여 경고 규칙을 다시 만들어야 합니다.

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>레거시 Log Analytics API를 사용하여 분할하는 메트릭 측정 경고 규칙

[메트릭 측정](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)은 요약된 시계열 결과를 기반으로 하는 로그 경고의 한 유형입니다. 이러한 규칙을 사용하여 열을 기준으로 그룹화하여 [경고를 분할](alerts-unified-log.md#split-by-alert-dimensions)할 수 있습니다. 레거시 Log Analytics API를 사용하는 경우 그룹화를 지원하지 않으므로 분할이 예상대로 작동하지 않습니다.

현재 ScheduledQueryRules API를 사용하여 [메트릭 측정](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 규칙에서 예상대로 작동하는 **집계** 를 설정할 수 있습니다. 현재 ScheduledQueryRules API로의 전환에 대해 자세히 알아보려면 [레거시 Log Analytics 경고 API에서 현재 로그 경고 API로 업그레이드](../alerts/alerts-log-api-switch.md)를 참조하세요.

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

구성된 [Azure Monitor의 로그 경고 규칙](./alerts-log.md)이 예기치 않게 트리거될 수 있습니다. 다음 섹션에서는 몇 가지 일반적인 이유를 설명합니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

Azure Monitor는 전 세계에서 테라바이트 단위의 고객 로그를 처리하므로 [로그 수집 대기 시간](../logs/data-ingestion-time.md)이 발생할 수 있습니다.

로그는 반 구조화된 데이터이며 근본적으로 메트릭보다 더 잠재적입니다. 발생한 경고에 너무 많은 불발이 발생하는 경우 [메트릭 경고](alerts-metric-overview.md)를 사용하는 것이 좋습니다. [로그에 대한 메트릭 경고](alerts-metric-logs.md)를 사용하여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

로그 경고는 로그에서 데이터를 검색하려고 할 때 가장 적합합니다. 가상 머신 하트비트에 대한 경고와 같이 로그의 데이터 부족을 감지하려고 할 때에는 성능이 떨어집니다. 

거짓 경고를 방지하기 위한 기본 제공 기능이 있지만 매우 잠재적인 데이터(30분 이상) 및 대기 시간이 급증하는 데이터에서 계속 발생할 수 있습니다.

### <a name="query-optimization-issues"></a>쿼리 최적화 문제

경고 서비스는 낮은 부하 및 경고 대기 시간을 최적화하도록 쿼리를 변경합니다. 경고 흐름은 경고에 대한 문제를 나타내는 결과를 변환하기 위해 빌드되었습니다. 예를 들어 다음과 같은 쿼리의 경우

``` Kusto
SecurityEvent
| where EventID == 4624
```

사용자가 경고를 사용한다면 이러한 이벤트 유형이 발생할 때 경고 논리가 쿼리에 `count`를 추가합니다. 실행할 쿼리는 다음과 같습니다.

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

쿼리에 경고 논리를 추가할 필요가 없으며, 오히려 그렇게 하면 문제가 발생할 수도 있습니다. 앞의 예제에서 쿼리에 `count`를 포함하는 경우 경고 서비스에서 `count`의 `count`를 수행하므로 항상 값 **1** 이 반환됩니다.

로그 경고 서비스는 최적화된 쿼리를 실행합니다. Log Analytics [포털](../logs/log-query-overview.md) 또는 [API](/rest/api/loganalytics/)에서 수정된 쿼리를 실행할 수 있습니다.

작업 영역 및 Application Insights의 경우 조건 창의 **실행할 쿼리** 라고 합니다. 다른 모든 리소스 종류에서 **조건** 탭의 **최종 경고 쿼리 보기** 를 선택합니다.

![실행될 쿼리](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>로그 경고를 사용하지 않도록 설정함

다음 섹션에서는 Azure Monitor가 로그 경고 규칙을 사용하지 않도록 설정할 수 있는 몇 가지 이유를 나열합니다. 이러한 섹션 뒤에는 [규칙을 사용하지 않도록 설정할 때 전송되는 활동 로그의 예제](#activity-log-example-when-rule-is-disabled)가 있습니다.

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>경고 범위가 더 이상 존재하지 않거나 이동됨

경고 규칙의 범위 리소스가 더 이상 유효하지 않으면 규칙 실행이 실패하고 요금이 청구되지 않습니다.

로그 경고가 일주일 동안 계속 실패하는 경우 Azure Monitor에서 사용하지 않도록 설정합니다.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>로그 경고에 사용된 쿼리가 잘못됨

로그 경고 규칙을 만들면 올바른 구문에 대해 쿼리의 유효성이 검사됩니다. 그러나 경우에 따라 로그 경고 규칙에 제공된 쿼리가 실패할 수 있습니다. 몇 가지 일반적인 이유는 다음과 같습니다.

- API를 통해 규칙이 생성되었으며 사용자가 유효성 검사를 건너뛰었습니다.
- 쿼리가 [여러 리소스에서 실행](../logs/cross-workspace-query.md)되고 하나 이상의 리소스가 삭제되거나 이동되었습니다.
- 다음 이유로 인해 [쿼리가 실패](https://dev.loganalytics.io/documentation/Using-the-API/Errors)했습니다.
    - 로깅 솔루션이 [작업 영역에 배포](../insights/solutions.md#install-a-monitoring-solution)되지 않아 테이블이 생성되지 않습니다.
    - 쿼리에 있는 테이블에 대한 데이터 흐름이 30일 넘게 중지되었습니다.
    - 데이터 흐름이 시작되지 않아 [사용자 지정 로그 테이블](../agents/data-sources-custom-logs.md)이 아직 만들어지지 않았습니다.
- [쿼리 언어](/azure/kusto/query/)의 변경 내용에 명령 및 함수에 대한 수정된 형식이 포함되어 있어 이전에 제공된 쿼리가 더 이상 유효하지 않습니다.

[Azure Advisor](../../advisor/advisor-overview.md)에서는 이 동작에 대해 사용자에게 경고하고 영향을 받는 로그 경고 규칙에 대한 권장 사항을 추가합니다. 사용되는 범주는 중간 영향의 ‘고가용성’으로, ‘모니터링을 수행하도록 로그 경고 규칙 복구’로 설명됩니다.

## <a name="alert-rule-quota-was-reached"></a>경고 규칙 할당량에 도달함

구독당 로그 검색 경고 규칙 수 및 최대 리소스 제한에 대한 자세한 내용은 [Azure Monitor 서비스 제한](../service-limits.md)을 참조하세요.

### <a name="recommended-steps"></a>권장되는 단계
    
할당량 한도에 도달했다면 다음 단계를 통해 문제를 해결할 수 있습니다.

1. 더 이상 사용되지 않는 로그 검색 경고 규칙을 삭제하거나 사용하지 않도록 설정합니다.
1. [차원별 경고 분할](alerts-unified-log.md#split-by-alert-dimensions)을 사용하여 규칙 수를 줄입니다. 이러한 규칙은 다양한 리소스 및 검색 사례를 모니터링할 수 있습니다.
1. 할당량 한도를 늘려야 하는 경우 지원 요청을 열고 다음 정보를 제공하세요.

    - 할당량 제한을 늘려야 하는 구독 ID 및 리소스 ID
    - 할당량 증가 이유
    - 할당량을 늘릴 리소스 종류: **Log Analytics** 또는 **Application Insights**
    - 요청된 할당량 한도

### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>새 로그 경고 규칙의 현재 사용량을 확인하려면 다음을 수행합니다.
    
#### <a name="from-the-azure-portal"></a>Azure Portal에서

1. 경고 창에서 **경고 규칙 관리** 를 선택합니다.
1. **구독** 드롭다운 컨트롤에서 원하는 구독으로 필터링합니다. (특정 리소스 그룹, 리소스 유형 또는 리소스로 필터링하지 않도록 합니다.)
1. **신호 형식** 드롭다운 컨트롤에서 **로그 검색** 을 선택합니다.
1. **상태** 드롭다운 컨트롤이 **사용** 으로 설정되어 있는지 확인합니다.

로그 검색 경고 규칙의 총 수가 규칙 목록 위에 표시됩니다.

#### <a name="from-api"></a>API에서

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [구독별 목록](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>규칙을 사용하지 않도록 설정한 경우 활동 로그 예제

쿼리가 7일 동안 실패하면 Azure Monitor에서 로그 경고를 사용하지 않도록 설정하고 규칙의 청구를 중지합니다. Azure Monitor가 [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)에서 로그 경고를 사용하지 않도록 설정한 정확한 시간을 확인할 수 있습니다. 

이 예제를 참조하세요.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>다음 단계

- [Azure의 로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
- [로그 경고 구성](../logs/log-query-overview.md)에 대해 자세히 알아보세요.
- [로그 쿼리](../logs/log-query-overview.md)에 대해 자세히 알아봅니다.
