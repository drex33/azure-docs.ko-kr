---
title: Azure Monitor의 로그 쿼리 최적화
description: Azure Monitor 로그 쿼리를 최적화하는 모범 사례입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 6612d365250a48d9532c0ef331d011c719b95666
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321651"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리 최적화
Azure Monitor 로그는 [ADX(Azure Data Explorer)](/azure/data-explorer/)를 사용하여 로그 데이터를 저장하고 해당 데이터를 분석하기 위한 쿼리를 실행합니다. 여기에서는 ADX 클러스터가 생성, 관리 및 유지 관리되고 로그 분석 워크로드에 맞게 최적화됩니다. 쿼리를 실행하면 최적화되고 작업 영역 데이터를 저장하는 적절한 ADX 클러스터로 라우팅됩니다. Azure Monitor 로그와 Azure Data Explorer 둘 다 많은 자동 쿼리 최적화 메커니즘을 사용합니다. 자동 최적화는 상당한 성능 향상을 제공하지만 쿼리 성능을 크게 향상시킬 수 있는 경우도 있습니다. 이 문서에서는 성능 고려 사항 및 이를 해결하기 위한 몇 가지 기술에 대해 설명합니다.

대부분의 기술은 Azure Data Explorer 및 Azure Monitor Logs에서 직접 실행되는 쿼리에 일반적지만 여기에서 설명하는 몇 가지 고유한 Azure Monitor Logs 고려 사항이 있습니다. Azure Data Explorer 최적화 팁에 대한 자세한 내용은 [쿼리 모범 사례](/azure/kusto/query/best-practices)를 참조하세요.

쿼리를 최적화하면:

- 실행 속도가 빨라지고 쿼리를 실행하는 전체 시간이 줄어듭니다.
- 제한되거나 거부될 가능성이 줄어듭니다.

대시보드, 경고, Logic Apps 및 Power BI와 같은 되풀이 및 버스트 처리에 사용되는 쿼리에 특히 주의해야 합니다. 이러한 경우 비효율적인 쿼리의 영향은 상당히 큽니다.

다음은 쿼리 최적화에 대한 자세한 동영상 설명입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4NUH0]

## <a name="query-performance-pane"></a>쿼리 성능 창
Log Analytics에서 쿼리를 실행한 후 쿼리 결과 위의 아래쪽 화살표를 클릭하면 쿼리에 대한 여러 성능 지표의 결과를 보여주는 쿼리 성능 창을 볼 수 있습니다. 이러한 성능 지표 각각은 다음 섹션에 설명되어 있습니다.

![쿼리 성능 창](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>쿼리 성능 지표

다음 쿼리 성능 지표는 실행되는 모든 쿼리에 제공됩니다.

- [총 CPU](#total-cpu): 모든 컴퓨팅 노드에서 쿼리를 처리하는 데 사용되는 전체 컴퓨팅입니다. 컴퓨팅, 구문 분석 및 데이터 가져오기에 사용된 시간을 나타냅니다. 

- [처리된 쿼리에 사용되는 데이터](#data-used-for-processed-query): 쿼리를 처리하기 위해 액세스한 전체 데이터입니다. 대상 테이블의 크기, 사용된 시간 범위, 적용된 필터 및 참조되는 열 수의 영향을 받습니다.

- [처리된 쿼리의 시간 범위](#time-span-of-the-processed-query): 쿼리를 처리하기 위해 액세스한 최신 데이터와 가장 오래된 데이터 사이의 간격입니다. 쿼리에 대해 지정한 명시적 시간 범위의 영향을 받습니다.

- [처리된 데이터의 사용 기간](#age-of-processed-data): 현재와 쿼리를 처리하기 위해 액세스한 가장 오래된 데이터 사이의 간격입니다. 데이터 가져오기의 효율성에 큰 영향을 미칩니다.

- [작업 영역 수](#number-of-workspaces): 암시적 또는 명시적 선택으로 인해 쿼리 처리 중에 액세스된 작업 영역의 수입니다.

- [지역 수](#number-of-regions): 작업 영역을 암시적 또는 명시적으로 선택하여 쿼리를 처리하는 동안 액세스한 지역 수입니다. 다중 지역 쿼리는 훨씬 덜 효율적이며 성능 지표는 부분적으로 적용됩니다.

- [병렬 처리](#parallelism): 시스템이 여러 노드에서 쿼리를 얼마나 실행할 수 있었는지를 나타냅니다. CPU 사용량이 많은 쿼리에만 해당됩니다. 특정 함수 및 연산자의 사용에 영향을 받습니다.


## <a name="total-cpu"></a>총 CPU
모든 쿼리 처리 노드에서 이 쿼리를 처리하는 데 운용된 실제 컴퓨팅 CPU입니다. 대부분의 쿼리는 많은 수의 노드에서 실행되기 때문에 일반적으로 쿼리가 실제로 실행되는 데 걸린 기간 보다 훨씬 더 큽니다. 

100초를 초과하여 CPU를 활용하는 쿼리는 리소스를 과도하게 사용하는 쿼리로 간주됩니다. 1,000초를 초과하여 CPU를 활용하는 쿼리는 악의적인 쿼리로 간주되어 제한될 수 있습니다.

쿼리 처리 시간은 다음에 소요됩니다.
- 데이터 검색 – 오래된 데이터를 검색하는 것이 최근 데이터를 검색하는 것보다 시간이 더 많이 걸립니다.
- 데이터 처리 – 데이터의 논리 및 평가입니다. 

쿼리 처리 노드에서 소요되는 시간 외에, Azure Monitor Logs는 사용자를 인증하고 이 데이터에 액세스할 수 있는지 확인하고, 데이터 저장소를 찾고, 쿼리를 구문 분석하고, 쿼리 처리 노드를 할당하는 데 추가 시간을 소비합니다. 이 시간은 쿼리 총 CPU 시간에 포함되지 않습니다.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>높은 CPU 함수를 사용하기 전 초기에 레코드 필터링

일부 쿼리 명령 및 함수는 CPU 사용량이 많습니다. JSON 및 XML을 구문 분석하거나 복잡한 정규식을 추출하는 명령의 경우 특히 그렇습니다. 이러한 구문 분석은 [parse_json()](/azure/kusto/query/parsejsonfunction) 또는 [parse_xml()](/azure/kusto/query/parse-xmlfunction) 함수를 통해 명시적으로 수행되거나 동적 열을 참조할 때 암시적으로 발생할 수 있습니다.

이러한 함수는 처리 중인 행 수에 비례하여 CPU를 사용합니다. 가장 효율적인 최적화는 CPU를 많이 사용하는 함수가 실행되기 전에 가능한 한 많은 레코드를 필터링하여 제외할 수 있는 쿼리 초기에 Where 조건을 추가하는 것입니다.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리는 구문 분석 전 [where](/azure/kusto/query/whereoperator) 조건이 많은 레코드를 제외하기 때문에 가장 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>평가된 where 절 사용을 피하세요.

데이터 세트에 물리적으로 존재하는 열이 아닌 평가된 열에 [where](/azure/kusto/query/whereoperator) 절이 포함된 쿼리는 효율성이 떨어집니다. 평가된 열을 필터링하면 대규모 데이터 세트가 처리될 때 일부 시스템 최적화를 방해합니다.
예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리는 [where](/azure/kusto/query/whereoperator) 조건이 기본 제공 열을 참조하기 때문에 더 효율적입니다.

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

필터링이 필드에서만 수행되는 것은 아니기 때문에 평가된 열이 쿼리 처리 엔진에 의해 암시적으로 생성되는 경우가 있습니다.
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>summarize 및 join에 효과적인 집계 명령 및 차원 사용

[max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction), [avg()](/azure/kusto/query/avg-aggfunction)와 같은 일부 집계 명령은 해당 논리로 인해 CPU 영향은 낮지만, 다른 명령은 더 복잡하며 효율적으로 실행될 수 있도록 추론 및 추정을 포함합니다. 예를 들어, [dcount()](/azure/kusto/query/dcount-aggfunction)는 HyperLogLog 알고리즘을 사용하여 각 값을 실제로 계산하지 않고 큰 데이터 세트의 고유 개수에 대한 근접 추정을 제공합니다. 백분위수 함수는 가장 가까운 순위 백분위수 알고리즘을 사용하여 유사한 근사법을 수행합니다. 몇 가지 명령에는 영향을 줄이기 위한 선택적 매개 변수가 포함됩니다. 예를 들어 [makeset()](/azure/kusto/query/makeset-aggfunction) 함수에는 CPU 및 메모리에 큰 영향을 미치는 최대 집합 크기를 정의하는 선택적 매개 변수가 있습니다.

[join](/azure/kusto/query/joinoperator?pivots=azuremonitor) 및 [summarize](/azure/kusto/query/summarizeoperator) 명령은 대량의 데이터를 처리할 때 CPU 사용률을 높일 수 있습니다. 복잡성은 summarize에서 `by`로 사용하거나 join 특성으로 사용하는 열의 가능한 값(카디널리티라고 함)의 수와 직접적인 관련이 있습니다. join 및 summarize에 대한 설명과 최적화는 해당 설명서 문서 및 최적화 팁을 참조하세요.

예를 들어 **CounterPath** 는 항상 **CounterName** 및 **ObjectName** 에 일대일로 매핑되기 때문에 다음 쿼리는 정확히 동일한 결과를 생성합니다. 두 번째 항목은 집계 차원이 더 작기 때문에 더 효율적입니다.

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU 사용량은 집약적 컴퓨팅이 필요한 확장 열 또는 where 조건의 영향을 받을 수 있습니다. [equal ==](/azure/kusto/query/datatypes-string-operators) 및 [startswith](/azure/kusto/query/datatypes-string-operators)와 같은 모든 간단한 문자열 비교는 거의 동일한 CPU 영향을 주는 반면 고급 텍스트 일치는 더 큰 영향을 미칩니다. 특히, [has](/azure/kusto/query/datatypes-string-operators) 연산자는 [contains](/azure/kusto/query/datatypes-string-operators) 연산자보다 더 효율적입니다. 문자열 처리 기법 때문에 짧은 문자열보다는 4자를 초과하는 문자열을 찾는 것이 더 효율적입니다.

예를 들어 다음 쿼리는 컴퓨터 명명 정책에 따라 비슷한 결과를 생성하지만 두 번째 쿼리가 더 효율적입니다.

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> 이 지표는 직접 클러스터의 CPU만 표시합니다. 다중 지역 쿼리에서는 지역 중 하나만 나타냅니다. 다중 작업 영역 쿼리에는 모든 작업 영역이 포함되지 않을 수 있습니다.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>문자열 구문 분석이 작동할 때 전체 XML 및 JSON 구문 분석을 피하세요.
XML 또는 JSON 개체를 전체 구문 분석하면 CPU 및 메모리 리소스 사용량이 많을 수 있습니다. 대부분의 경우 매개 변수가 하나 또는 두 개만 필요하고 XML 또는 JSON 개체가 간단한 경우에는 [구문 분석 연산자](/azure/kusto/query/parseoperator) 또는 기타 [텍스트 구문 분석 기술](./parse-text.md)을 사용하여 문자열로 구문 분석하는 것이 더 쉽습니다. 성능 향상은 XML 또는 JSON 개체의 레코드 수가 증가할수록 더 중요합니다. 레코드 수가 수천만에 도달하면 반드시 필요합니다.

예를 들어 다음 쿼리는 전체 XML 구문 분석을 수행하지 않고 위의 쿼리와 정확히 동일한 결과를 반환합니다. XML 파일 구조에 대해 몇 가지 가정을 합니다. 예를 들어 FileHash 뒤에 FilePath 요소가 오고 이들 모두 특성이 없습니다. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>처리된 쿼리에 사용되는 데이터

쿼리 처리에서 중요한 요소는 쿼리 처리를 위해 검사되고 사용되는 데이터의 볼륨입니다. Azure Data Explorer 다른 데이터 플랫폼에 비해 데이터 볼륨을 획기적으로 줄이는 적극적인 최적화를 사용합니다. 그래도 쿼리에는 사용되는 데이터 볼륨에 영향을 줄 수 있는 중요한 요소가 있습니다.

2,000KB를 초과하는 데이터를 처리하는 쿼리는 리소스를 과도하게 사용하는 쿼리로 간주됩니다. 20,000KB를 초과하는 데이터를 처리하는 쿼리는 악의적인 쿼리로 간주되어 제한될 수 있습니다.

Azure Monitor 로그에서 **TimeGenerated** 열은 데이터를 인덱싱하는 방법으로 사용됩니다. **TimeGenerated** 값의 범위를 최대한 좁게 제한하면 처리해야 하는 데이터의 양이 크게 제한되어 쿼리 성능이 크게 향상됩니다.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>불필요한 search 및 union 연산자 사용을 피하세요.

처리되는 데이터를 늘리는 또 다른 요인은 많은 수의 테이블을 사용하는 것입니다. 이런 경우는 일반적으로 `search *` 및 `union *` 명령이 사용될 때 발생합니다. 이러한 명령은 시스템이 작업 영역에 있는 모든 테이블의 데이터를 평가하고 검사하도록 합니다. 경우에 따라 작업 영역에 수백 개의 테이블이 있을 수도 있습니다. 특정 테이블로 범위를 지정하지 않고 "search *" 또는 다른 검색을 사용하는 것을 최대한 피하세요.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 마지막 쿼리가 단연 가장 효율적입니다.

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>쿼리에 초기 필터 추가

데이터 볼륨을 줄이는 또 다른 방법은 쿼리의 초기에 [where](/azure/kusto/query/whereoperator) 조건을 사용하는 것입니다. Azure Data Explorer 플랫폼에는 특정 where 조건과 관련된 데이터가 포함된 파티션을 알 수 있는 캐시가 포함되어 있습니다. 예를 들어 쿼리에 `where EventID == 4624`가 포함된 경우 일치하는 이벤트가 있는 파티션을 처리하는 노드에만 쿼리를 배포합니다.

다음 예제 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리가 더 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>조건부 aggregation 함수 및 materialize 함수를 사용하여 동일한 원본 데이터를 여러 번 검사하지 마세요.
하나의 쿼리에 join 또는 union 연산자를 사용하여 병합된 하위 쿼리가 여러 개 있으면 각각의 하위 쿼리가 전체 원본을 개별적으로 검사한 다음, 결과를 병합합니다. 이렇게 되면 데이터를 검사하는 횟수가 몇 배가 되며, 대규모 데이터 세트에서 상당한 영향을 미칩니다.

이런 문제를 방지하는 기법은 조건부 집계 함수를 사용하는 것입니다. 요약 연산자에서 사용되는 대부분의 [집계 함수](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions)에는 조건화된 버전이 있어서 여러 조건에 단일 요약 연산자를 사용할 수 있습니다. 

예를 들어 다음 쿼리는 각 계정에 대한 로그인 이벤트 수와 프로세스 실행 이벤트 수를 보여줍니다. 둘 다 동일한 결과를 반환하지만 첫 번째는 데이터를 두 번 검사하고 두 번째는 데이터를 한 번만 검사합니다.

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

하위 쿼리가 불필요한 또 다른 경우는 특정 패턴과 일치하는 레코드만 처리하도록 [구문 분석 연산자](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor)를 사전 필터링하는 경우입니다. 이 경우는 패턴이 일치하지 않을 경우 구문 분석 연산자 및 기타 유사한 연산자가 빈 결과를 반환하기 때문에 필요하지 않습니다. 다음은 정확히 동일한 결과를 반환하는 두 개의 쿼리이며, 그 중 두 번째 쿼리는 데이터를 한 번만 검사합니다. 두 번째 쿼리에서 각 구문 분석 명령은 해당 이벤트에만 해당됩니다. 나중에 나오는 extend 연산자는 빈 데이터 상황을 참조하는 방법을 보여줍니다.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

위의 방법으로 하위 쿼리 사용을 피할 수 없는 경우 또 다른 기법은 [materialize() 함수](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor)를 사용하여 각 하위 쿼리에 단일 원본 데이터가 사용되고 있음을 쿼리 엔진에 힌트로 알리는 것입니다. 이 방법은 원본 데이터가 쿼리 내에서 여러 번 사용되는 함수에서 오는 경우에 유용합니다. materialize는 하위 쿼리의 출력이 입력보다 훨씬 작은 경우 효과적입니다. 쿼리 엔진은 모든 경우에 출력을 캐시하고 다시 사용합니다.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>검색되는 열 수 줄이기

Azure Data Explorer는 열 형식 데이터 저장소이므로 모든 열 검색은 다른 열과 독립적입니다. 검색되는 열의 수는 전체 데이터 볼륨에 직접적인 영향을 줍니다. 결과를 [요약](/azure/kusto/query/summarizeoperator)하거나 특정 열을 [예상](/azure/kusto/query/projectoperator)하는 데 필요한 열만 출력에 포함해야 합니다. Azure Data Explorer에는 검색된 열 수를 줄이기 위한 몇 가지 최적화 기능이 있습니다. 열이 필요하지 않다고 판단되면(예: [summarize](/azure/kusto/query/summarizeoperator) 명령에서 참조되지 않는 경우) 검색하지 않습니다.

예를 들어, 두 번째 쿼리는 하나의 열이 아닌 세 개의 열을 가져와야 하기 때문에 세 배 더 많은 데이터를 처리할 수 있습니다.

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>처리된 쿼리의 시간 범위

Azure Monitor 로그의 모든 로그는 **TimeGenerated** 열에 따라 분할됩니다. 액세스되는 파티션의 수는 시간 범위와 직접적인 관련이 있습니다. 시간 범위를 줄이는 것이 신속한 쿼리 실행을 보장하는 가장 효율적인 방법입니다.

시간 범위가 15일을 초과하는 쿼리는 과도한 리소스를 사용하는 쿼리로 간주됩니다. 시간 범위가 90일을 초과하는 쿼리는 악성 쿼리로 간주되어 제한될 수 있습니다.

시간 범위는 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](./scope.md#time-range)에 설명된 대로 Log Analytics 화면의 시간 범위 선택기를 사용하여 설정할 수 있습니다. 선택한 시간 범위가 쿼리 메타데이터를 사용하여 백 엔드로 전달되기 때문에 권장되는 방법입니다. 

다른 방법은 쿼리의 **TimeGenerated** 에 대해 [where](/azure/kusto/query/whereoperator) 조건을 명시적으로 포함하는 것입니다. 그러면 쿼리가 다른 인터페이스에서 사용되는 경우에도 시간 범위가 고정되기 때문에 이 방법을 사용해야 합니다.
쿼리의 모든 부분에 **TimeGenerated** 필터가 있는지 확인해야 합니다. 쿼리에 여러 테이블 또는 동일한 테이블에서 데이터를 가져오는 하위 쿼리가 있는 경우 각각 자체적으로 [where](/azure/kusto/query/whereoperator) 조건을 포함해야 합니다.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>모든 하위 쿼리에 TimeGenerated 필터 포함

예를 들어 다음 쿼리의 **Perf** 테이블에서는 마지막 날만 검사하지만 **Heartbeat** 테이블에서는 모든 기록(최대 2년)을 검사합니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

이러한 실수가 발생하는 일반적인 경우는 [arg_max()](/azure/kusto/query/arg-max-aggfunction)를 사용하여 가장 최근에 발생한 항목을 찾는 경우입니다. 예를 들면 다음과 같습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

이런 문제는 내부 쿼리에 시간 필터를 추가하여 쉽게 수정할 수 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

이러한 실수의 또 다른 예는 여러 테이블에 대한 [union](/azure/kusto/query/unionoperator?pivots=azuremonitor) 직후 시간 범위 필터링을 수행하는 경우입니다. union을 수행할 때는 각 하위 쿼리마다 범위를 지정해야 합니다. [let](/azure/kusto/query/letstatement) 문을 사용하여 범위 일관성을 보장할 수 있습니다.

예를 들어 다음 쿼리는 *Heartbeat* 및 *Perf* 테이블에서 지난 1일만이 아닌 모든 데이터를 검사합니다.

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

이 쿼리는 다음과 같이 수정해야 합니다.

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>시간 범위 측정값 제한

측정값은 항상 지정된 실제 시간보다 큽니다. 예를 들어 쿼리의 필터가 7일인 경우 시스템은 7.5일 또는 8.1일을 검사할 수 있습니다. 시스템이 데이터를 변수 크기의 청크로 분할하기 때문입니다. 모든 관련 레코드를 검사하기 위해 몇 시간, 심지어 하루 넘게 포함할 수 있는 전체 파티션을 검색합니다.

시스템이 시간 범위의 정확한 측정을 제공할 수 없는 경우가 몇 가지 있습니다. 이 문제는 쿼리의 범위가 하루 미만이거나 다중 작업 영역 쿼리에서 발생하는 대부분의 경우에 발생합니다.


> [!IMPORTANT]
> 이 지표는 직접 클러스터에서 처리된 데이터만 표시합니다. 다중 지역 쿼리에서는 지역 중 하나만 나타냅니다. 다중 작업 영역 쿼리에는 모든 작업 영역이 포함되지 않을 수 있습니다.

## <a name="age-of-processed-data"></a>처리된 데이터의 사용 기간
Azure Data Explorer는 여러 스토리지 계층(메모리 내, 로컬 SSD 디스크 및 훨씬 느린 Azure Blob)을 사용합니다. 데이터가 최신일수록 대기 시간이 더 짧은 성능 계층에 저장되어 쿼리 기간 및 CPU가 줄어들 가능성이 높습니다. 데이터 자체 외에 시스템에는 메타데이터용 캐시도 있습니다. 데이터가 오래될수록 해당 메타데이터가 캐시에 있을 가능성이 줄어듭니다.

14일 넘게 지난 데이터를 처리하는 쿼리는 과도한 리소스를 사용하는 쿼리로 간주됩니다.


오래된 데이터를 사용해야 하는 쿼리가 있는 반면 오래된 데이터를 실수로 사용하는 경우가 있습니다. 메타데이터에 시간 범위를 제공하지 않고 쿼리가 실행되고 일부 테이블 참조에 **TimeGenerated** 열에 대한 필터가 포함되지 않을 경우 발생합니다. 이런 경우 시스템은 해당 테이블에 저장된 모든 데이터를 검사합니다. 데이터 보존 기간이 길면 긴 시간 범위를 포함할 수 있으므로 데이터 보존 기간만큼 오래된 데이터를 포함할 수 있습니다.

이러한 경우의 예는 다음과 같습니다.

- 제한되지 않는 하위 쿼리로 Log Analytics에서 시간 범위를 설정하지 않는 경우. 위 예제를 참조하세요.
- 시간 범위 선택적 매개 변수 없이 API를 사용하는 경우
- 시간 범위를 강제 적용하지 않는 클라이언트(예: Power BI 커넥터)를 사용하는 경우

이전 섹션의 예제 및 메모도 이 경우와 관련이 있으니 참조하세요.

## <a name="number-of-regions"></a>지역 수
여러 지역에서 단일 쿼리가 실행될 수 있는 몇 가지 상황이 있습니다.

- 여러 작업 영역이 명시적으로 나열되고 서로 다른 지역에 있는 경우
- 리소스 범위 쿼리가 데이터를 가져오고 데이터가 서로 다른 지역에 있는 여러 작업 영역에 저장되어 있는 경우

지역 간 쿼리를 실행하려면 시스템을 직렬화하고 백엔드에서 일반적으로 쿼리 최종 결과보다 훨씬 큰 대량의 중간 데이터 청크를 전송해야 합니다. 또한 최적화 및 추론을 수행하고 캐시를 활용하는 시스템의 기능을 제한합니다.
이러한 지역을 모두 검사할 실질적인 이유가 없다면 더 적은 지역을 포함하도록 범위를 조정해야 합니다. 리소스 범위를 최소화했지만 여전히 많은 지역을 사용하는 경우에는 구성 오류가 원인일 수 있습니다. 예를 들어 감사 로그 및 진단 설정이 다른 지역의 다른 작업 영역으로 전송되거나 여러 진단 설정 구성이 있을 수 있습니다. 

3개가 넘는 지역에 걸쳐 있는 쿼리는 과도한 리소스를 사용하는 쿼리로 간주됩니다. 6개가 넘는 지역에 걸쳐 있는 쿼리는 악의적인 쿼리로 간주되어 제한될 수 있습니다.

> [!IMPORTANT]
> 여러 지역에서 쿼리를 실행하면 CPU 및 데이터 측정이 정확하지 않으며 지역 중 하나의 측정값만 나타냅니다.

## <a name="number-of-workspaces"></a>작업 영역 수
작업 영역은 로그 데이터를 분리하고 관리하는 데 사용되는 논리적 컨테이너입니다. 백 엔드는 선택한 지역 내에서 실제 클러스터의 작업 영역 배치를 최적화합니다.

여러 작업 영역을 사용하는 원인은 다음과 같습니다. 

- 여러 작업 영역이 명시적으로 나열된 경우
- 리소스 범위 쿼리가 데이터를 가져오고 데이터가 여러 작업 영역에 저장되어 있는 경우
 
지역 간 쿼리 또는 클러스터 간 쿼리를 실행하려면 시스템을 직렬화하고 백엔드에서 일반적으로 쿼리 최종 결과보다 훨씬 큰 대량의 중간 데이터 청크를 전송해야 합니다. 또한 최적화, 추론 및 캐시 활용을 수행하는 시스템 기능을 제한합니다.

5개가 넘는 작업 영역에 걸쳐 있는 쿼리는 과도한 리소스를 사용하는 쿼리로 간주됩니다. 쿼리는 100개를 초과하는 작업 영역으로 확장될 수 없습니다.

> [!IMPORTANT]
> 일부 다중 작업 영역 시나리오에서는 CPU 및 데이터 측정값이 정확하지 않으며 일부 작업 영역에만 측정값을 나타냅니다.

## <a name="parallelism"></a>병렬 처리
Azure Monitor 로그는 Azure Data Explorer의 대규모 클러스터를 사용하여 쿼리를 실행하며 이러한 클러스터는 규모가 다양하고 잠재적으로 최대 수십 개의 컴퓨팅 노드를 확보할 수 있습니다. 시스템은 작업 영역 배치 논리 및 용량에 따라 클러스터의 크기를 자동으로 조정합니다.

쿼리를 효율적으로 실행하기 위해 처리에 필요한 데이터를 기반으로 쿼리를 분할하고 컴퓨팅 노드에 배포합니다. 이런 작업을 시스템에서 효율적으로 수행할 수 없는 경우도 있습니다. 그러면 쿼리 기간이 길어질 수 있습니다. 

병렬 처리를 줄일 수 있는 쿼리 동작은 다음과 같습니다.

- [serialize 연산자](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction), [row](/azure/kusto/query/rowcumsumfunction) 함수와 같은 직렬화 및 기간 함수 사용하는 경우. 이러한 경우에 시계열 및 사용자 분석 함수를 사용할 수 있습니다. 비효율적인 직렬화는 쿼리 끝이 아닌 곳에 [range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator) 연산자를 사용하는 경우에도 발생할 수 있습니다.
-    [dcount()](/azure/kusto/query/dcount-aggfunction) 집계 함수를 사용하면 시스템이 고유한 값의 중앙 복사본을 갖게 됩니다. 데이터 규모가 큰 경우에는 dcount 함수 선택적 매개 변수를 사용하여 정확도를 낮추는 것이 좋습니다.
-    많은 경우 [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) 연산자는 전반적인 병렬 처리를 감소시킵니다. 성능에 문제가 있는 경우 대안으로 셔플 조인을 고려할 수 있습니다.
-    리소스 범위 쿼리에서 Azure 역할 할당이 매우 많은 경우 사전 실행 Kubernetes RBAC 또는 Azure RBAC 검사가 오래 지속될 수 있습니다. 이로 인해 검사가 길어져서 병렬 처리가 줄어들 수 있습니다. 예를 들어 수천 개의 리소스가 있는 구독에서 쿼리가 실행되고 각 리소스에는 구독 또는 리소스 그룹이 아닌 리소스 수준의 역할 할당이 많이 있습니다.
-    쿼리가 작은 데이터 청크를 처리하는 경우 시스템이 데이터를 여러 컴퓨팅 노드에 분산하지 않기 때문에 병렬 처리 속도가 낮습니다.



## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대한 참조 문서](/azure/kusto/query/)
