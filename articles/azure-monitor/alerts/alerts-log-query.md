---
title: 로그 경고 쿼리 최적화 | Microsoft Docs
description: 효율적인 경고 쿼리 작성을 위한 권장 사항입니다
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 82f40d33d0465a7981105472ccd0487e7e24da80
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635131"
---
# <a name="optimizing-log-alert-queries"></a>경고 쿼리 최적화
이 문서에서는 최적의 성능을 얻기 위해 [로그 경고](./alerts-unified-log.md) 쿼리를 작성하고 변환하는 방법을 설명합니다. 최적화된 쿼리는 자주 실행되는 경고의 대기 시간 및 부하를 줄입니다.

## <a name="how-to-start-writing-an-alert-log-query"></a>경고 로그 쿼리 작성을 시작하는 방법

경고 쿼리는 [Log Analytics에서 문제를 나타내는 로그 데이터를 쿼리하는 것](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)부터 시작합니다. 항목 중 [경고 쿼리 예제 토픽](../logs/queries.md)을 사용하여 검색 가능한 항목을 찾을 수 있습니다. 또한 [사용자 고유의 쿼리를 작성하기 시작](../logs/log-analytics-tutorial.md)할 수도 있습니다. 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>경고가 아닌 문제를 나타내는 쿼리

경고 흐름은 경고에 대한 문제를 나타내는 결과를 변환하기 위해 작성되었습니다. 예를 들어 다음과 같은 쿼리의 경우

``` Kusto
SecurityEvent
| where EventID == 4624
```

사용자가 의도가 경고하는 것이라면, 이러한 이벤트 유형이 발생할 때 경고 논리가 쿼리에 `count`를 추가합니다. 실행할 쿼리는 다음과 같습니다.

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

쿼리에 경고 논리를 추가할 필요가 없으며, 오히려 그렇게 하면 문제가 발생할 수도 있습니다. 위의 예제에서 쿼리에 `count`를 포함하면 항상 값 1이 발생합니다. 이는 경고 서비스가 `count`를 `count`에서 수행하기 때문입니다.

### <a name="avoid-limit-and-take-operators"></a>방지해야 하는 `limit` 및 `take` 연산자

쿼리에서 `limit` 및 `take`를 사용하면 시간이 지남에 따라 결과가 일관되지 않으므로 대기 시간 및 경고 로드가 증가할 수 있습니다. 필요한 경우에만 사용하는 것이 좋습니다.

## <a name="log-query-constraints"></a>로그 쿼리 제약 조건
[Azure Monitor 로그 쿼리](../logs/log-query-overview.md)는 테이블, [`search`](/azure/kusto/query/searchoperator), 또는 [`union`](/azure/kusto/query/unionoperator) 연산자에서 시작합니다.

명확한 범위를 정의하려면 Azure의 로그 경고 규칙에 대한 쿼리는 항상 테이블로 시작되어야 합니다. 이를 통해 쿼리 성능과 결과의 관련성을 모두 개선할 수 있습니다. 경고 규칙의 쿼리는 자주 실행되므로 `search` 및 `union`의 사용은 여러 테이블에서의 검색으로 이어지게 되며, 과도한 오버헤드로 인해 경고에 대기 시간이 추가됩니다. 이러한 연산자는 또한 쿼리를 최적화하는 경고 서비스의 기능을 축소합니다.

`search` `union` 리소스 간 쿼리를 제외 하 고 또는 연산자를 사용 하는 로그 경고 규칙을 만들거나 수정 하는 것은 지원 되지 않습니다.

예를 들어 다음 경고 쿼리는 _SecurityEvent_ 테이블로 범위가 지정되며 특정 이벤트 ID를 검색합니다. 쿼리가 반드시 처리해야 하는 유일한 테이블입니다.

``` Kusto
SecurityEvent
| where EventID == 4624
```

로그 경고 규칙이 [상호 리소스 쿼리](../logs/cross-workspace-query.md)를 사용한다면 쿼리 범위를 특정 리소스로 제한하는 `union` 형식을 사용하기 때문에 이러한 변경의 영향을 받지 않습니다. 다음 예는 유효한 로그 경고 쿼리입니다.

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> 로그 경고의 [상호 리소스 쿼리](../logs/cross-workspace-query.md)는 새로운 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)에서 지원됩니다. 로그 경고를 만드는 데 [레거시 Log Analytics 경고 API](./api-alerts.md)를 계속 사용하는 경우 [여기](../alerts/alerts-log-api-switch.md)에서 전환하는 방법을 배울 수 있습니다.

## <a name="examples"></a>예
다음 예제는 `search` 및 `union`를 사용하는 로그 쿼리를 포함하고, 경고 규칙에 사용할 목적으로 이러한 쿼리를 수정하는 데 사용할 수 있는 절차를 안내합니다.

### <a name="example-1"></a>예제 1
성능 정보를 검색하는 데 `search`를 사용하는 다음 쿼리를 이용해 로그 경고 규칙을 만들고자 합니다. 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

이 쿼리의 결과에는 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다.

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>예 2
성능 정보를 검색하는 데 `search`를 사용하는 다음 쿼리를 이용해 로그 경고 규칙을 만들고자 합니다. 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

이 쿼리의 결과에는 _ObjectName_ 및 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다.

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>예제 3

성능 정보를 검색하는 데 `search` 및 `union` 양쪽 모두 사용하는 다음 쿼리를 사용하여 로그 경고 규칙을 만들고자 합니다. 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 쿼리의 첫 부분에 있는 속성이 속한 테이블을 식별합니다. 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

이 쿼리의 결과에는 이러한 모든 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이제 `withsource` 명령에서 `union`을 사용하여 각 행을 제공한 원본 테이블을 식별합니다.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

이 쿼리의 결과에는 이러한 속성 역시 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이러한 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다. 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>예제 4
다음 쿼리를 사용하여 두 `search` 쿼리의 결과를 조인하는 로그 경고 규칙을 만들고자 합니다.

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

쿼리를 수정하려면 먼저 다음 쿼리를 사용하여 조인 왼쪽의 속성이 포함된 테이블을 식별합니다. 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

결과에는 조인 왼쪽의 속성이 _SecurityEvent_ 테이블에 속한다는 내용이 표시됩니다. 

이제 다음 쿼리를 사용하여 조인 오른쪽의 속성이 포함된 테이블을 식별합니다. 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
결과에는 조인 오른쪽의 속성이 _하트비트_ 테이블에 속한다는 내용이 표시됩니다.

이러한 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다. 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>다음 단계
- Azure Monitor의 [로그 경고](alerts-log.md)에 대해 알아봅니다.
- [로그 쿼리](../logs/log-query-overview.md)에 대해 알아봅니다.
