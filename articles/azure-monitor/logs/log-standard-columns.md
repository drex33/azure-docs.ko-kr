---
title: Azure Monitor 로그 레코드의 표준 열 | Microsoft Docs
description: Azure Monitor 로그에서 여러 데이터 형식에 공통적인 열을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.openlocfilehash: 909c02c53f753579d6788933277bca8f75f53859
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539589"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure Monitor 로그의 표준 열
Azure Monitor 로그의 데이터는 각각 고유한 열 세트가 포함된 특정 데이터 형식을 사용하여 [Log Analytics 작업 영역 또는 Application Insights 애플리케이션에 레코드 세트로 저장](../logs/data-platform-logs.md)됩니다. 대부분 데이터 형식에는 여러 형식에 공통적인 표준 열이 있습니다. 이 문서에서는 해당 열에 관해 설명하고 쿼리에 속성을 사용하는 방법의 예제를 제공합니다.

Application Insights의 작업 영역 기반 애플리케이션은 Log Analytics 작업 영역에 데이터를 저장하고 작업 영역의 다른 테이블과 동일한 표준 열을 사용합니다. 클래식 애플리케이션은 데이터를 별도로 저장하고 이 문서에 지정된 대로 다양한 표준 열을 포함합니다.

> [!NOTE]
> 일부 표준 열은 Log Analytics의 스키마 뷰 또는 IntelliSense에 표시되지 않으며 출력에 열을 명시적으로 지정하지 않으면 쿼리 결과에 표시되지 않습니다.
> 

## <a name="tenantid"></a>TenantId
**TenantId** 열에는 Log Analytics 작업 영역의 작업 영역 ID가 포함됩니다.

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** 열은 데이터 원본에서 레코드가 생성된 시간과 날짜를 포함합니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](../logs/data-ingestion-time.md)을 참조하세요.

**TimeGenerated** 는 시간별 필터링 또는 요약에 사용할 공통 열을 제공합니다. Azure Portal에서 보기 또는 대시보드의 시간 범위를 선택하면 보기 또는 대시보드는 **TimeGenerated** 를 사용하여 결과를 필터링합니다. 

> [!NOTE]
> 클래식 Application Insights 리소스를 지원하는 테이블은 **TimeGenerated** 열 대신 **timestamp** 열을 사용합니다.

### <a name="examples"></a>예제

다음 쿼리는 이전 주의 각 날짜에 생성된 오류 이벤트 수를 반환합니다.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```
## <a name="_timereceived"></a>\_TimeReceived
**\_ TimeReceived** 열에는 Azure 클라우드의 Azure Monitor 수집 지점에서 레코드를 받은 날짜 및 시간이 포함됩니다. 이는 데이터 원본과 클라우드 간 대기 시간 문제를 식별하는 데 유용할 수 있습니다. 예를 들어, 네트워킹 문제로 인해 에이전트에서 데이터가 전송되는 데 지연이 발생할 수 있습니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](../logs/data-ingestion-time.md)을 참조하세요.

> [!NOTE]
> **\_ TimeReceived** 열은 사용될 때마다 계산됩니다. 이 프로세스는 리소스를 많이 사용합니다. 많은 레코드를 필터링하기 위해 이 프로세스를 사용하여 구체화합니다. 이 함수를 반복적으로 사용하면 쿼리 실행 기간이 증가할 수 있습니다.


다음 쿼리는 에이전트의 이벤트 레코드에 대한 시간별로 평균 대기 시간을 제공합니다. 여기에는 에이전트에서 클라우드까지 시간 및 로그 쿼리에 사용할 수 있는 레코드의 총 시간이 포함됩니다.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type"></a>형식
**Type** 열은 레코드가 검색된 테이블 이름을 포함하며 테이블 이름을 레코드 유형이라고 생각해도 됩니다. 이 열은 `search` 연산자를 사용하여 여러 형식의 레코드를 구분하는 쿼리처럼 여러 테이블의 레코드를 결합하는 쿼리에 유용합니다. 일부 쿼리에서는 **Type** 대신 **$table** 을 사용할 수 있습니다.

> [!NOTE]
> 클래식 Application Insights 리소스를 지원하는 테이블은 **Type** 열 대신 **itemType** 열을 사용합니다.

### <a name="examples"></a>예제
다음 쿼리는 지난 시간 동안 수집된 레코드 수를 유형별로 반환합니다.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
**\_ItemId** 열에는 레코드의 고유 식별자가 포함됩니다.


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** 열에는 레코드가 연결된 리소스의 고유 ID가 포함됩니다. 쿼리 범위를 특정 리소스의 레코드로만 제한하거나 여러 테이블의 관련 데이터를 조인하는 데 사용할 표준 열을 제공합니다.

Azure 리소스의 경우 **_ResourceId** 값은 [Azure 리소스 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)입니다. 열은 [Azure Arc](../../azure-arc/overview.md) 리소스를 비롯한 Azure 리소스 또는 수집 중에 리소스 ID를 나타낸 사용자 지정 로그로 제한됩니다.

> [!NOTE]
> 일부 데이터 형식은 Azure 리소스 ID 또는 적어도 그 일부(예: 구독 ID)를 포함하는 필드를 이미 갖고 있습니다. 이러한 필드는 이전 버전과의 호환성을 위해 유지되지만, 보다 일관적인 _ResourceId를 사용하여 교차 상관 관계를 수행하는 것이 좋습니다.

### <a name="examples"></a>예제
다음 쿼리는 각 컴퓨터에 대한 성능 및 이벤트 데이터를 조인합니다. 여기서는 ID가 _101_ 이고 프로세서 활용률이 50%를 초과하는 이벤트가 모두 표시됩니다.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

다음 쿼리는 _AzureActivity_ 레코드를 _SecurityEvent_ 레코드와 조인합니다. 이 컴퓨터에 로그인한 사용자가 포함된 활동 작업이 모두 표시됩니다.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

다음 쿼리는 **_ResourceId** 를 구문 분석하고 Azure 리소스 그룹당 청구된 데이터 볼륨을 집계합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다.

ResourceId 열\_을 구문 분석하여 해당 항목을 추출하는 것보다는 SubscriptionId 열\_을 사용하는 것이 항상 더 효율적입니다.

## <a name="_subscriptionid"></a>\_SubscriptionId
**\_SubscriptionId** 열에는 해당 레코드가 연결된 리소스의 구독 ID가 포함됩니다. 쿼리 범위를 특정 구독의 레코드만으로 지정하거나 다양한 구독을 비교하는 데 사용할 표준 열을 제공합니다.

Azure 리소스의 경우 **__SubscriptionId** 값은 [Azure 리소스 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)의 구독 부분입니다. 열은 [Azure Arc](../../azure-arc/overview.md) 리소스를 비롯한 Azure 리소스 또는 수집 중에 리소스 ID를 나타낸 사용자 지정 로그로 제한됩니다.

> [!NOTE]
> 일부 데이터 형식에는 이미 Azure 구독 ID를 포함하는 필드가 있습니다. 해당 필드는 이전 버전과의 호환성을 위해 유지되지만, 보다 일관적인 \_SubscriptionId를 사용하여 상호 상관 관계를 수행하는 것이 좋습니다.
### <a name="examples"></a>예제
다음 쿼리는 특정 구독의 컴퓨터에 대한 성능 데이터를 검사합니다. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "ebb79bc0-aa86-44a7-8111-cabbe0c43993"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

다음 쿼리는 **_ResourceId** 를 구문 분석하고 Azure 구독당 청구된 데이터 볼륨을 집계합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다.


## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** 열은 수집된 데이터의 청구 가능 여부를 지정합니다. **\_IsBillable** 이 `false`인 데이터는 무료로 수집되고 Azure 계정에 요금이 청구되지 않습니다.

### <a name="examples"></a>예제
비용이 청구되는 데이터 형식을 전송하는 컴퓨터 목록을 가져오려면 다음 쿼리를 사용합니다.

> [!NOTE]
> 다른 데이터 형식의 데이터 간을 검색할 경우 비용이 많이 들기 때문에 `union withsource = tt *`를 사용하는 쿼리는 자주 사용하지 않도록 합니다. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

비용이 청구되는 데이터 형식을 전송하는 컴퓨터의 시간당 수를 반환하도록 이 쿼리를 다음과 같이 확장할 수 있습니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
**\_BilledSize** 열은 **\_IsBillable** 이 true인 경우 Azure 계정에 요금이 청구되는 데이터의 크기(바이트)를 지정합니다.


### <a name="examples"></a>예제
컴퓨터당 수집된 청구 가능한 이벤트의 크기를 보려면 크기(바이트)를 제공하는 `_BilledSize` 열을 사용합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

구독당 수집된 청구 가능한 이벤트 크기를 보려면 다음 쿼리를 사용합니다.

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

리소스 그룹당 수집된 청구 가능한 이벤트 크기를 보려면 다음 쿼리를 사용합니다.

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


컴퓨터당 수집된 이벤트 수를 보려면 다음 쿼리를 사용합니다.

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

컴퓨터당 수집된 청구 가능한 이벤트 수를 보려면 다음 쿼리를 사용합니다. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

특정 컴퓨터에서 청구 가능한 데이터 형식 수를 보려면 다음 쿼리를 사용합니다.

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 데이터](./log-query-overview.md)가 저장되는 방법에 대해 자세히 알아보세요.
- [로그 쿼리 작성](./get-started-queries.md) 단원을 계속 진행하세요.
- [로그 쿼리에서 테이블 조인](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins) 단원을 계속 진행하세요.
