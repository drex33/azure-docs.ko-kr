---
title: Cassandra API에 대한 고급 진단 쿼리 문제 해결
titleSuffix: Azure Cosmos DB
description: Azure Cosmos DB for Cassandra API에 저장된 데이터와 관련된 문제를 해결하기 위해 진단 로그를 쿼리하는 방법에 대해 알아봅니다.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: 87fcbdff81f8885fd2c173193660aeb5aec2cf9c
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609660"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-cassandra-api"></a>Cassandra API에 대한 고급 진단 쿼리 문제 해결

[!INCLUDE[appliesto-all-apis-except-table](../includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL(Core) API](../cosmos-db-advanced-queries.md)
> * [MongoDB API](../mongodb/diagnostic-queries-mongodb.md)
> * [Cassandra API](diagnostic-queries-cassandra.md)
> * [Gremlin API](../queries-gremlin.md)


이 문서에서는 **Azure Diagnostics(레거시)** 및 **리소스 관련(미리 보기)** 테이블로 전송된 진단 로그를 사용하여 Azure Cosmos DB 계정 관련 문제 해결에 도움이 되는 고급 쿼리를 작성하는 방법을 설명합니다.

Azure Diagnostics 테이블의 경우 모든 데이터가 하나의 단일 테이블에 기록됩니다. 사용자는 쿼리할 범주를 지정합니다. 요청의 전체 텍스트 쿼리를 보려면 [Azure에서 진단 설정을 사용하여 Azure Cosmos DB 데이터 모니터링](../cosmosdb-monitor-resource-logs.md#full-text-query)을 참조하여 이 기능을 사용하는 방법을 알아봅니다.

[리소스별 테이블](../cosmosdb-monitor-resource-logs.md#create-setting-portal)의 경우 데이터는 리소스의 각 범주에 대한 개별 테이블에 기록됩니다. 다음과 같은 이유로 이 모드를 권장합니다.

- 데이터 작업을 훨씬 쉽게 만듭니다. 
- 스키마의 더 나은 검색 가능성을 제공합니다.
- 수집 대기 시간과 쿼리 시간 모두에서 성능이 향상됩니다.

## <a name="common-queries"></a>일반 쿼리
일반적인 쿼리는 리소스별 및 Azure Diagnostics 표에 나와 있습니다.

### <a name="top-n10-request-unit-ru-consuming-requests-or-queries-in-a-specific-time-frame"></a>특정 시간 프레임에서 요청 또는 쿼리를 소비하는 상위 N(10) 요청 단위(RU)

# <a name="resource-specific"></a>[리소스 관련(Resource-Specific)](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(1h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-in-a-specific-time-window"></a>특정 시간 창에서 조절된 요청(statusCode = 429) 

# <a name="resource-specific"></a>[리소스 관련(Resource-Specific)](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBCassandraRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-large-response-lengths-payload-size-of-the-server-response"></a>응답 길이가 큰 쿼리(서버 응답의 페이로드 크기)

# <a name="resource-specific"></a>[리소스 관련(Resource-Specific)](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBCassandraRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by PIICommandText
   | order by max_ResponseLength desc
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "CassandraRequests"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
   | order by max_responseLength_s1 desc
   ```    
---

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>실제 파티션별 RU 소비(복제본 세트의 모든 복제본에서)

# <a name="resource-specific"></a>[리소스 관련(Resource-Specific)](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>논리 파티션별 RU 소비(복제본 세트의 모든 복제본에서)

# <a name="resource-specific"></a>[리소스 관련(Resource-Specific)](#tab/resource-specific)
   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```

# <a name="azure-diagnostics"></a>[Azure Diagnostics](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>다음 단계 
* Azure Cosmos DB용 진단 설정을 만드는 방법에 대한 자세한 내용은 [진단 설정 만들기](../cosmosdb-monitor-resource-logs.md)를 참조하세요.
* Azure Portal, Azure CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.