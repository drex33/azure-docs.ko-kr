---
title: Azure Monitor의 로그 경고
description: 지정하는 로그 쿼리 조건을 충족하는 경우, 이메일, 알림, 웹 사이트 URL 호출(웹후크) 또는 자동화를 트리거합니다
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2479e5b269d6cc6a4d0aaadd6b3616da17670c2d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070036"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고

## <a name="overview"></a>개요

로그 경고는 [Azure 경고](./alerts-overview.md)에서 지원되는 경고 유형 중 하나입니다. 사용자는 로그를 통해 설정된 빈도마다 [Log Analytics](../logs/log-analytics-tutorial.md) 쿼리를 사용하여 리소스 로그를 평가하고 결과에 따라 경고를 실행할 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용하여 하나 이상의 작업을 트리거할 수 있습니다.

> [!NOTE]
> [Log Analytics 작업 영역](../logs/log-analytics-tutorial.md)의 로그 데이터를 Azure Monitor 메트릭 저장소로 보낼 수 있습니다. 메트릭 경고에는 사용 중인 데이터에 따라 적합할 수 있는 [다른 동작](alerts-metric-overview.md)이 있습니다. 로그를 메트릭에 라우팅하는 대상 및 방법에 대한 자세한 내용은 [로그에 대한 메트릭 경고](alerts-metric-logs.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

로그 경고는 Log Analytics 데이터에 대해 쿼리를 실행합니다. 먼저 [로그 데이터 수집](../essentials/resource-logs.md)을 시작하고 문제에 대한 로그 데이터를 쿼리합니다. Log Analytics의 [경고 쿼리 예시 문서](../logs/queries.md)를 사용하여 발견한 문제를 이해하거나 [사용자 고유 쿼리를 작성하여 시작](../logs/log-analytics-tutorial.md)할 수 있습니다.

[Azure 모니터링 기여자](../roles-permissions-security.md)는 로그 경고를 만들고, 수정하고, 업데이트하는 데 필요한 일반적인 역할입니다. 리소스 로그에 대한 액세스 권한 및 쿼리 실행 권한도 필요합니다. 리소스 로그에 대한 부분 액세스는 쿼리에 실패하거나 부분적인 결과를 반환할 수 있습니다. [Azure에서 로그 경고를 구성하는 방법에 대해 자세히 알아보세요](./alerts-log.md).

> [!NOTE]
> Log Analytics에 대한 로그 경고는 레거시 [Log Analytics 경고 API](./api-alerts.md)를 사용하여 관리되어 왔습니다. [최신 ScheduledQueryRules API로 전환하는 방법에 대해 자세히 알아보세요](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>쿼리 평가 정의

로그 검색 규칙 조건 정의는 다음으로부터 시작됩니다.

- 실행할 쿼리
- 결과를 사용하는 방법

다음 섹션에서는 위의 논리를 설정하는 데 사용할 수 있는 다양한 매개 변수를 설명합니다.

### <a name="log-query"></a>로그 쿼리
규칙을 평가하는 데 사용되는 [Log Analytics](../logs/log-analytics-tutorial.md) 쿼리입니다. 이 쿼리에서 반환된 결과는 경고를 트리거할지 여부를 결정하는 데 사용됩니다. 쿼리 범위를 다음으로 지정할 수 있습니다.

- 가상 머신과 같은 특정 리소스.
- 구독 또는 리소스 그룹과 같은 대규모 리소스.
- [리소스 간 쿼리](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)를 사용하는 다중 리소스. 
 
> [!IMPORTANT]
> 경고 쿼리에는 최적의 성능 및 결과의 관련성을 보장하는 제약 조건이 있습니다. [여기를 참조하세요](./alerts-log-query.md).

> [!IMPORTANT]
> 리소스 중심 및 [리소스 간 쿼리](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)는 최신 scheduledQueryRules API를 사용하는 경우에만 지원됩니다. 레거시 [Log Analytics Alert API](./api-alerts.md)를 사용하는 경우 전환해야 합니다. [전환에 대해 자세히 알아보세요](./alerts-log-api-switch.md).

#### <a name="query-time-range"></a>쿼리 시간 범위

시간 범위는 규칙 조건 정의에서 설정됩니다. 작업 영역과 Application Insights에서는 **기간** 이라고 합니다. 다른 모든 리소스 종류에서는 **쿼리 시간 범위 재정의** 라고 합니다.

로그 분석과 마찬가지로, 시간 범위는 쿼리 데이터를 지정된 범위로 제한합니다. **ago** 명령이 쿼리에 사용되더라도 시간 범위가 적용됩니다.

예를 들어, 쿼리는 텍스트에 **ago(1d)** 가 포함되어 있더라도 시간 범위가 60분인 경우 60분을 검색합니다. 시간 범위와 쿼리 시간 필터링이 일치해야 합니다. 예시의 경우, **기간** / **재정의 쿼리 시간 범위** 를 1일로 변경하면 예상대로 작동합니다.

### <a name="measure"></a>측정값

로그 경고는 로그를 평가할 수 있는 숫자 값으로 바꿉니다. 다음 두 가지 사항을 측정할 수 있습니다.

#### <a name="count-of-the-results-table-rows"></a>결과 테이블 행 수

결과 수가 기본 측정값입니다. Windows 이벤트 로그, syslog, 애플리케이션 예외와 같은 이벤트 작업에 적합합니다. 로그 레코드가 발생하거나 또는 로그 레코드가 평가된 시간대에서 발생하지 않을 때 트리거됩니다.

로그 경고는 로그에서 데이터를 검색하려고 할 때 가장 적합합니다. 로그의 데이터 부족을 감지하려고 할 때는 잘 작동하지 않습니다. 예를 들어, 가상 머신 하트비트에 대한 경고입니다.

작업 영역 및 Application Insights의 경우에는 이를 선택한 **결과 수** **기준** 이라고 합니다. 다른 모든 리소스 종류에서는 이를 선택한 **테이블 행** 의 **측정값** 이라고 합니다.

> [!NOTE]
> 로그는 반 구조화된 데이터이기 때문에, 기본적으로 메트릭보다 더 잠재적입니다. 로그에서 데이터 부족을 감지하려고 할 때 잘못 실행될 수 있으므로 [메트릭 경고](alerts-metric-overview.md)를 사용하는 것이 좋습니다. [로그에 대한 메트릭 경고](alerts-metric-logs.md)를 사용하여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

##### <a name="example-of-results-table-rows-count-use-case"></a>결과 테이블 행 수 사용 사례의 예시

애플리케이션이 언제 오류 코드 500(내부 서버 오류)을 표시하면서 응답했는지 확인하려고 합니다. 다음 세부 정보로 경고 규칙을 만들 수 있습니다.

- **쿼리:** 

```Kusto
requests
| where resultCode == "500"
```

- **기간 / 집계 세분성:** 15분
- **경고 빈도:** 15분
- **임계값:** 0 초과

그러면 경고 규칙이 오류 코드 500으로 끝나는 모든 요청을 모니터링합니다. 쿼리는 지난 15분에 대해 15분마다 실행됩니다. 이러한 레코드가 하나라도 있으면 경고가 발생하고, 구성된 작업이 트리거됩니다.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>숫자 열을 기준으로 하는 측정값 계산(예: CPU 카운터 값)

작업 영역 및 Application Insights의 경우에는 이를 선택한 **메트릭 측정** **기준** 이라고 합니다. 다른 모든 리소스 유형에서 임의의 숫자 열 이름 선택 **측정값** 이라고 합니다.

### <a name="aggregation-type"></a>집계 유형

단일 숫자 값으로 집계하기 위해 여러 레코드에서 수행되는 계산입니다. 예를 들면 다음과 같습니다.
- **Count** 는 쿼리의 레코드 수를 반환합니다
- **Average** 는 정의된 측정값 열 [**집계 세분성**](#aggregation-granularity)의 평균을 반환합니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 의 측정값 유형에서만 지원됩니다. 쿼리 결과에는 사용자 정의 집계 다음에 숫자 값을 제공하는 AggregatedValue 라는 열이 포함되어 있어야 합니다. 다른 모든 리소스 종류의 경우에는 해당 이름의 필드에서 **집계 유형** 이 선택됩니다.

### <a name="aggregation-granularity"></a>집계 세분성

여러 레코드를 하나의 숫자 값으로 집계하는 데 사용되는 간격을 결정합니다. 예를 들어, **5분** 을 지정한 경우 레코드는 지정된 **집계 유형** 을 사용하여 5분 간격으로 그룹화됩니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 의 측정값 유형에서만 지원됩니다. 쿼리 결과에는 쿼리 결과의 간격을 설정하는 [bin()이](/azure/kusto/query/binfunction) 포함되어야 합니다. 다른 모든 리소스 종류에서 이 설정을 제어하는 필드를 **집계 세분성** 이라고 합니다.

> [!NOTE]
> [bin()](/azure/kusto/query/binfunction)에서 동일하지 않은 시간 간격이 발생할 수 있으므로, 경고 서비스는 런타임 시 적절한 시간을 사용하여 [bin()](/azure/kusto/query/binfunction) 함수를 [bin_at()](/azure/kusto/query/binatfunction) 함수로 자동 변환함으로써 고정 시점이 포함된 결과를 보장합니다.

### <a name="split-by-alert-dimensions"></a>경고 차원별 분할

고유한 조합으로 그룹화하여 경고를 숫자 또는 문자열 열별로 별도의 경고로 분할합니다. 대규모 리소스 중심 경고(구독 또는 리소스 그룹 범위)를 만들 때 Azure 리소스 ID 열로 분할할 수 있습니다. Azure 리소스 ID 열을 분할하면 경고 대상이 지정된 리소스로 변경됩니다.

여러 Azure 리소스에서 동일한 조건을 모니터링하려는 경우, Azure 리소스 ID 열로 분할하는 것이 좋습니다. 예를 들어, CPU 사용량 80% 이상의 모든 가상 머신을 모니터링합니다. 범위의 여러 리소스에 대한 조건을 원하는 경우 분할하지 않을 수도 있습니다. 리소스 그룹 범위에서 5개 이상의 컴퓨터가 80%를 넘는 CPU 사용량을 갖는지 모니터링하는 것을 예로 들 수 있습니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 의 측정값 유형에서만 지원됩니다. 이 필드를 **다음에 대한 집계:** 라고 합니다. 이는 세 개의 열로 제한됩니다. 쿼리에 열별로 그룹이 4개 이상 있으면 예기치 않은 결과가 발생할 수 있습니다. 다른 모든 리소스 종류에서는 조건의 **차원별 분할** 섹션에 구성됩니다(6개의 분할로 제한됨).

#### <a name="example-of-splitting-by-alert-dimensions"></a>경고 차원별 분할 예시

예를 들어, 특정 리소스 그룹에서 웹 사이트/앱을 실행하는 여러 가상 머신의 오류를 모니터링하려고 합니다. 다음과 같이 로그 경고 규칙을 사용하여 이 작업을 수행할 수 있습니다.

- **쿼리:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    **메트릭 측정** 경고 논리를 사용하여 작업 영역 및 Application Insights를 사용할 경우 이 줄을 쿼리 텍스트에 추가해야 합니다.

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **리소스 ID 열:**  _ResourceId(경고 규칙의 리소스 ID 열별 분할은 현재 구독 및 리소스 그룹에만 사용할 수 있음)
- **차원/다음에 대한 집계:**
  - Computer = VM1, VM2(현재 작업 영역 및 Application Insights에 대한 경고 규칙 정의의 필터링 값을 사용할 수 없습니다. 쿼리 텍스트를 필터링합니다.)
- **기간 / 집계 세분성:** 15분
- **경고 빈도:** 15분
- **임계값:** 0 초과

이 규칙은 지난 15분 동안 가상 머신에 오류 이벤트가 있는지 여부를 모니터링합니다. 각 가상 머신은 별도로 모니터링되고 작업을 개별적으로 트리거합니다.

> [!NOTE]
> 차원별 경고 분할은 현재 scheduledQueryRules API에 대해서만 사용할 수 있습니다. 레거시 [Log Analytics Alert API](./api-alerts.md)를 사용하는 경우 전환해야 합니다. [샘플링에 대해 자세히 알아보세요](./alerts-log-api-switch.md). 리소스 중심의 대규모 경고는 API 버전 `2020-08-01` 이상에서만 지원됩니다.

## <a name="alert-logic-definition"></a>경고 논리 정의

실행할 쿼리와 결과의 평가를 정의한 후에는 경고 논리 및 작업 발생 시기를 정의해야 합니다. 다음 섹션에서는 사용할 수 있는 다양한 매개 변수에 대해 설명합니다.

### <a name="threshold-and-operator"></a>임계값 및 연산자

쿼리 결과는 임계값 및 연산자와 비교되는 숫자로 변환됩니다.

### <a name="frequency"></a>빈도

> [!NOTE]
> 현재 1분 빈도의 로그 경고 미리 보기에 대한 추가 요금은 없습니다. 미리 보기 단계인 기능에 대한 가격 책정은 나중에 발표될 예정이며 청구를 시작하기 전에 공지합니다. 알림 기간 후 1분 빈도의 로그 경고를 계속 사용하도록 선택하는 경우 해당 요율에 따라 요금이 청구됩니다.

간격은 쿼리가 실행되는 간격입니다. 1분에서 1일까지 설정할 수 있습니다. 로그 레코드를 누락하지 않으려면 [쿼리 시간 범위](#query-time-range)보다 짧거나 같아야 합니다.

예를 들어, 기간을 30분으로 설정하고 빈도를 1시간으로 설정합니다.  쿼리가 00:00에서 실행되는 경우 23:30에서 00:00 사이의 레코드를 반환합니다. 다음에 쿼리를 실행할 시간은 01:00이며 00:30과 01:00 사이의 레코드를 반환합니다. 00:00 및 00:30 사이에 생성된 레코드는 평가되지 않습니다.

### <a name="number-of-violations-to-trigger-alert"></a>경고를 트리거할 위반 수

경고 평가 기간과 경고를 트리거하는 데 필요한 오류 수를 지정할 수 있습니다. 경고를 트리거하는 영향 시간을 더 효율적으로 정의할 수 있습니다. 

예를 들어 규칙 [**집계 세분성**](#aggregation-granularity)이 '5분'으로 정의된 경우, 지난 1시간 동안 세 번의 오류(15분)가 발생한 경우에만 경고를 트리거할 수 있습니다. 이 설정은 애플리케이션 비즈니스 정책에 의해 정의됩니다.

## <a name="state-and-resolving-alerts"></a>상태 및 경고 해결

로그 경고는 상태 비저장 또는 상태 저장(현재 미리 보기 상태)일 수 있습니다.

상태 비저장 경고는 이전에 발생한 경우에도 조건이 충족할 때마다 발생합니다. 경고 인스턴스가 해결되면 [경고를 닫힌 것으로 표시](../alerts/alerts-managing-alert-states.md)할 수 있습니다. 경고 규칙이 실행된 후 기간 동안 트리거되지 않도록 작업의 알림을 차단할 수도 있습니다. Log Analytics 작업 영역과 Application Insights에서, 이는 **경고 중지** 라고 합니다. 다른 모든 리소스 종류에서는 **작업 알림 차단** 이라고 합니다. 

다음 경고 상태 비저장 평가 예시를 참조하세요.

| 시간    | 로그 조건 평가 | 결과 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | 경고가 발생하지 않습니다. 호출된 작업이 없습니다.
| 00:10 | TRUE  | 경고가 실행되고 작업 그룹이 호출됩니다. 새 경고 ACTIVE 상태.
| 00:15 | TRUE  | 경고가 실행되고 작업 그룹이 호출됩니다. 새 경고 ACTIVE 상태.
| 00:20 | FALSE | 경고가 발생하지 않습니다. 호출된 작업이 없습니다. 이전 경고 상태는 활성 상태로 유지됩니다.

상태 저장 경고는 인시던트마다 한 번씩 발생하고 해결됩니다. 경고 규칙은 특정 평가 기간(로그 수집 지연 고려)에 대해 경고 조건이 30분 동안 충족되지 않을 때 해결되며 플래핑 조건이 있는 경우 노이즈를 줄이기 위해 3회 연속 평가됩니다. 예를 들어 빈도가 5분이면 경고가 40분 후에 해결되거나 빈도가 1분이면 경고가 32분 후에 해결됩니다. 해결된 알림은 웹 후크 또는 이메일을 통해 전송되며 Azure Portal의 경고 인스턴스 상태(모니터 상태라고 함)도 해결됨으로 설정됩니다.

상태 저장 경고 기능은 현재 Azure 공용 클라우드에서 미리 보기로 제공됩니다. 경고 세부 정보 섹션에서 **경고 자동 해결** 을 사용하여 이를 설정할 수 있습니다.

## <a name="location-selection-in-log-alerts"></a>로그 경고의 위치 선택

로그 경고를 사용하여 경고 규칙의 위치를 설정할 수 있습니다. Log Analytics 작업 영역에서 규칙 위치는 작업 영역 위치와 일치해야 합니다. 다른 모든 리소스에서 [Log Analytics 지원 지역 목록](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)에 맞게 정렬된 지원되는 위치를 선택할 수 있습니다.

위치는 경고 규칙이 평가되는 지역에 영향을 줍니다. 쿼리는 선택한 지역의 로그 데이터에서 실행됩니다. 즉, 경고 서비스 엔드투엔드는 전역입니다. 경고 규칙 정의, 발생한 경고, 알림 및 작업이 경고 규칙의 위치에 바인딩되지 않음을 의미합니다. Azure Monitor 경고 서비스는 [비 지역 서비스](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=non-regional)이므로 데이터는 집합 영역에서 전송됩니다.

## <a name="pricing-and-billing-of-log-alerts"></a>로그 경고 가격 책정 및 청구

가격 책정 정보는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 있습니다. 로그 경고는 다음을 포함하는 리소스 공급자 `microsoft.insights/scheduledqueryrules` 아래에 나열됩니다.

- Application Insights에 대한 로그 경고는 리소스 그룹 및 경고 속성과 함께 정확한 리소스 이름으로 표시됩니다.
- [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)를 사용하여 생성된 경우, Log Analytics에 대한 로그 경고는 리소스 그룹 및 경고 속성과 함께 정확한 리소스 이름으로 표시됩니다.
- [레거시 Log Analytics API](./api-alerts.md)에서 생성된 로그 경고는 [Azure 리소스](../../azure-resource-manager/management/overview.md)를 추적하지 않으며 강제로 지정된 고유 리소스 이름이 없습니다. 이러한 경고는 리소스 명명 구조`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`를 가진 숨겨진 리소스로 `microsoft.insights/scheduledqueryrules`에 계속 생성됩니다. 레거시 API에 대한 로그 경고는 리소스 그룹 및 경고 속성과 함께 숨겨진 리소스 이름으로 표시됩니다.

> [!NOTE]
> `<, >, %, &, \, ?, /`과 같은 지원되지 않는 리소스 문자는 숨겨진 리소스 이름에서 `_`로 바뀌고 청구 정보에도 반영됩니다.

> [!NOTE]
> Log Analytics에 대한 로그 경고는 레거시 [Log Analytics 경고 API](./api-alerts.md) 및 [Log Analytics 저장된 검색 및 경고](../insights/solutions.md)의 레거시 템플릿을 사용하여 관리할 수도 있습니다. [최신 ScheduledQueryRules API로 전환하는 방법에 대해 자세히 알아보세요](../alerts/alerts-log-api-switch.md). 전환하기로 결정할 때까지는 [레거시 Log Analytics API](./api-alerts.md)를 사용하여 모든 경고 규칙 관리를 수행해야 하며, 숨겨진 리소스는 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 로그 경고에서 만들기](./alerts-log.md)에 대해 알아봅니다.
* [Azure의 로그 경고에서 웹후크](../alerts/alerts-log-webhook.md)를 이해합니다.
* [Azure Alerts](./alerts-overview.md)에 대해 알아봅니다.
* [Log Analytics](../logs/log-query-overview.md)에 대해 자세히 알아보기
