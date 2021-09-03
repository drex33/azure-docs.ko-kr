---
title: 진단 쿼리 관련 문제 해결
titleSuffix: Azure Cosmos DB
description: Azure Cosmos DB에 저장된 데이터와 관련된 문제를 해결하기 위해 진단 로그를 쿼리하는 방법에 대해 알아봅니다.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/12/2021
ms.author: esarroyo
ms.openlocfilehash: 9f863760130b0748405cb30d4257864482b679f0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106242"
---
# <a name="troubleshoot-issues-with-diagnostics-queries"></a>진단 쿼리 관련 문제 해결
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 **AzureDiagnostics(레거시)** 및 **리소스 관련(미리 보기)** 테이블로 전송된 진단 로그를 사용하여 Azure Cosmos DB 계정 관련 문제 해결에 도움이 되는 간단한 쿼리를 작성하는 방법을 설명합니다.

Azure Diagnostics 테이블의 경우 모든 데이터가 하나의 단일 테이블에 기록되며 사용자는 쿼리할 범주를 지정해야 합니다.

리소스별 테이블의 경우 데이터는 리소스의 각 범주에 대한 개별 테이블에 기록됩니다(테이블 API에는 사용할 수 없음). 데이터를 훨씬 쉽게 다룰 수 있고 스키마를 더 정확하게 검색할 수 있으며 수집 대기 시간과 쿼리 시간 모두에서 성능이 향상되므로 이 모드를 사용하는 것이 좋습니다.

## <a name="azurediagnostics-queries"></a><a id="azure-diagnostics-queries"></a> AzureDiagnostics 쿼리

- 실행에 3밀리초보다 오래 걸리는 작업을 쿼리하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

- 작업을 실행하는 사용자 에이전트를 쿼리하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

- 장기 실행 작업을 쿼리하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
- 데이터베이스 계정의 상위 3개 파티션 간의 왜도를 평가하기 위해 파티션 키 통계를 가져오는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

- 비용이 많이 드는 쿼리에 대한 요청 요금을 어떻게 가져오나요?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- 초당 RU의 대부분을 차지하는 작업을 어떻게 찾나요?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

- **DataPlaneRequests** 및 **QueryRunTimeStatistics** 를 통해 데이터에 조인 시, 초당 100RU를 초과하여 사용하는 모든 쿼리를 가져오는 방법.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- 요청 요금 및 쿼리 실행 시간을 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```

- 여러 작업에 대한 분포를 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

- 파티션이 사용한 최대 처리량이 어떻게 되나요?
   
   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

- 파티션 키의 초당 RU 사용량에 대한 정보를 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 특정 파티션 키에 대한 요청 요금을 가져오는 방법

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

- 특정 기간에 초당 RU를 가장 많이 사용하는 상위 파티션 키를 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
   ```

- 스토리지 크기가 8GB보다 큰 파티션 키에 대한 로그를 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

- 작업, 요청 요금 또는 응답 길이에 대한 P99 또는 P50 복제 대기 시간을 어떻게 가져오나요?

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s), percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s), percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s), count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
- ControlPlane 로그를 어떻게 가져오나요?
 
   [키 기반 메타데이터 쓰기 권한 사용 안 함](audit-control-plane-logs.md#disable-key-based-metadata-write-access) 문서에 설명된 대로 플래그를 사용하고, Azure PowerShell, Azure CLI 또는 Azure Resource Manager를 사용하여 작업을 실행해야 합니다.
 
   ```kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="resource-specific-queries"></a><a id="resource-specific-queries"></a> 리소스 관련 쿼리

- 실행에 3밀리초보다 오래 걸리는 작업을 쿼리하는 방법:

    ```kusto
    CDBDataPlaneRequests 
    | where toint(DurationMs) > 3
    | summarize count() by ClientIpAddress, TimeGenerated
    ```

- 작업을 실행하는 사용자 에이전트를 쿼리하는 방법:

   ```kusto
    CDBDataPlaneRequests
    | summarize count() by OperationName, UserAgent
   ```

- 장기 실행 작업을 쿼리하는 방법:

   ```kusto
    CDBDataPlaneRequests
    | project TimeGenerated , DurationMs 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
   ```
    
- 데이터베이스 계정의 상위 3개 파티션 간의 왜도를 평가하기 위해 파티션 키 통계를 가져오는 방법:

   ```kusto
    CDBPartitionKeyStatistics
    | project RegionName, DatabaseName, CollectionName, PartitionKey, SizeKb
   ```

- 비용이 많이 드는 쿼리에 대한 요청 요금을 어떻게 가져오나요?

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 10.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- 초당 RU의 대부분을 차지하는 작업을 어떻게 찾나요?

    ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize max(ResponseLength), max(RequestLength), max(RequestCharge), count = count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```

- **DataPlaneRequests** 및 **QueryRunTimeStatistics** 를 통해 데이터에 조인 시, 초당 100RU를 초과하여 사용하는 모든 쿼리를 가져오는 방법.

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 100.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- 요청 요금 및 쿼리 실행 시간을 어떻게 가져오나요?

   ```kusto
    CDBQueryRuntimeStatistics
    | join kind= inner (
    CDBDataPlaneRequests
    ) on $left.ActivityId == $right.ActivityId
    | project DatabaseName, CollectionName, OperationName , QueryText, RequestCharge, DurationMs, bin(TimeGenerated, 1min)
   ```

- 여러 작업에 대한 분포를 어떻게 가져오나요?

   ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize count = count()  by OperationName, RequestResourceType, bin(TimeGenerated, 1h)
   ```

- 파티션이 사용한 최대 처리량이 어떻게 되나요?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2h) 
   | summarize max(RequestCharge) by bin(TimeGenerated, 1h), PartitionId
   ```

- 파티션 키의 초당 RU 사용량에 대한 정보를 어떻게 가져오나요?

   ```kusto
   CDBPartitionKeyRUConsumption 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 특정 파티션 키에 대한 요청 요금을 어떻게 가져오나요?

   ```kusto
   CDBPartitionKeyRUConsumption  
   | where parse_json(PartitionKey)[0] == "2" 
   ```

- 특정 기간에 초당 RU를 가장 많이 사용하는 상위 파티션 키를 어떻게 가져오나요? 

   ```kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= datetime("02/12/2021, 11:20:00.000 PM") and TimeGenerated <= datetime("05/12/2021, 11:30:00.000 PM") 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey 
   | order by total desc
   ```

- 스토리지 크기가 8GB보다 큰 파티션 키에 대한 로그를 어떻게 가져오나요?

   ```kusto
   CDBPartitionKeyStatistics
   | where todouble(SizeKb) > 800000
   ``` 

- 작업, 요청 요금 또는 응답 길이에 대한 P99 또는 P50 복제 대기 시간을 어떻게 가져오나요?

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(ResponseLength), 50), percentile(todouble(ResponseLength), 99), max(ResponseLength), percentile(todouble(RequestCharge), 50), percentile(todouble(RequestCharge), 99), max(RequestCharge), percentile(todouble(DurationMs), 50), percentile(todouble(DurationMs), 99), max(DurationMs),count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```
 
- ControlPlane 로그를 어떻게 가져오나요?
 
   [키 기반 메타데이터 쓰기 권한 사용 안 함](audit-control-plane-logs.md#disable-key-based-metadata-write-access) 문서에 설명된 대로 플래그를 사용하고, Azure PowerShell, Azure CLI 또는 Azure Resource Manager를 사용하여 작업을 실행해야 합니다.
 
   ```kusto  
   CDBControlPlaneRequests
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>다음 단계 
* Cosmos DB용 진단 설정을 만드는 방법에 대한 자세한 내용은 [진단 설정 만들기](cosmosdb-monitor-resource-logs.md) 문서를 참조하세요.

* Azure Portal, CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.