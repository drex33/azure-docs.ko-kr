---
title: Azure Cosmos DB Cassandra API에 대 한 서버 진단
description: 이 문서에서는 Azure Cosmos DB Cassandra API의 몇 가지 일반적인 오류 코드와을 사용 하 여 문제를 해결 하는 방법을 설명 Log Analytics
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: troubleshooting
ms.date: 10/12/2021
ms.custom: template-how-to
ms.openlocfilehash: bbef336e16623113c45929710e49101bddfdf64e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103151"
---
# <a name="server-diagnostics-for-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API에 대 한 서버 진단
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Log Analytics은 Cassandra API 계정에서 서버 진단을 실행 하는 데 도움이 되는 Azure Portal 도구입니다. Azure Monitor 로그에 의해 수집 된 데이터에서 로그 쿼리를 실행 하 고 결과를 대화형으로 분석 합니다. Log Analytics 쿼리에서 검색 되는 레코드는 데이터에 대 한 다양 한 정보를 제공 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Log Analytics 작업 영역](../../azure-monitor/logs/quick-create-workspace.md)을 만듭니다.
- [진단 설정](../cosmosdb-monitor-resource-logs.md)를 만듭니다.
- Cassandra API 계정에서 [log analytics](../../azure-monitor/logs/log-analytics-overview.md) 를 시작 합니다.

## <a name="use-log-analytics"></a>Log Analytics 사용
Log analytics 설정을 완료 한 후에는 로그를 탐색 하 여 더 많은 정보를 얻을 수 있습니다.

### <a name="explore-data-plane-operations"></a>데이터 평면 작업 살펴보기
CDBCassandraRequests 테이블을 사용 하 여 Cassandra API 계정에 대해 특별히 데이터 평면 작업을 확인할 수 있습니다. TopN (10) 사용 요청을 확인 하 고 각 요청에 대 한 자세한 정보를 가져오는 샘플 쿼리입니다.

```Kusto
CDBCassandraRequests
| where RequestCharge  > 0
| project DatabaseName, CollectionName, DurationMs, OperationName, ActivityId, ErrorCode, RequestCharge, PIICommandText 
| order by RequestCharge
| take 10
```

#### <a name="error-codes-and-possible-solutions"></a>오류 코드 및 가능한 해결 방법
|상태 코드 | 오류 코드           | Description  |
|------------|----------------------|--------------|
| 200 | -1 | 성공 |
| 400 | 8704 | 쿼리가 올바르지만 잘못 된 구문입니다. |
| 400 | 8192 | 전송 된 쿼리에 구문 오류가 있습니다. 쿼리를 검토 합니다. |
| 400 | 8960 | 일부 구성 문제로 인해 쿼리가 잘못 되었습니다. |
| 401 |8448 | 기록 된 사용자에 게 쿼리를 수행할 수 있는 적절 한 권한이 없습니다. |
| 403 | 8448 | 사용자에 게 요청을 수행 하는 데 필요한 권한이 없을 수 있으므로 사용할 수 없는 응답입니다. |
| 404 | 5376 | 응답을 찾을 수 없기 때문에 쓰기 요청 중에 제한 시간이 아닌 예외가 발생 했습니다. |
| 405 | 0 | 서버 쪽 Cassandra 오류입니다. 오류가 드물게 발생 하는 경우 지원 티켓을 엽니다. |
| 408 | 4608 | 읽기 요청 중 시간이 초과 되었습니다. |
| 408 | 4352 | 쓰기 serviceRequest 동안 시간 초과 예외가 발생 했습니다. |
| 409 | 9216 | 이미 존재 하는 keyspace 또는 테이블을 만드는 중입니다. |
| 412 | 5376 | 사전 조건 오류입니다. 데이터 무결성을 보장 하기 위해 읽기 응답을 기반으로 하는 쓰기 요청이 참 인지 확인 합니다. 시간 제한 없는 쓰기 요청 예외가 반환 됩니다. |
| 413 | 5376 | 쓰기 요청 중에이 시간 제한 없는 예외는 페이로드가 너무 클 수 있기 때문입니다. 현재 행 당 2MB 제한이 있습니다. |
| 417 | 9472 | 준비 된 문이 서버 노드에 캐시 되지 않은 경우 예외가 throw 됩니다. 일시적인/비차단 이어야 합니다. |
| 423 | 5376 | 현재 처리 중인 쓰기 요청 때문에 잠금이 있습니다. |
| 429 | 4097| 오버 로드 예외는 (r) 또는 높은 요청 률의 결과로 발생 합니다. 더 높은 볼륨 요청을 처리 하려면 더 많은 추가가 필요할 수 있습니다. 에서 네이티브 Cassandra이는 CPU가 충분 하지 않은 Vm 중 하나로 해석 될 수 있습니다. 현재 데이터 모델을 검토 하 여 핫 파티션을 일으킬 수 있는 과도 한 기울어집니다이 없는지 확인 하는 것이 좋습니다. |
| 449 | 5376 | 동시 실행 예외입니다. 이는 지정 된 행에 대해 한 번에 하나의 쓰기 업데이트만 보장 하기 위해 발생 합니다. |
| 500 | 0 | 서버 cassandraError: 예기치 않은 문제가 발생 했습니다. 서버 쪽 버그를 나타냅니다. |
| 503 | 4096 | 서비스를 사용할 수 없습니다. |
|   | 256 | 연결 자격 증명이 잘못 되었기 때문일 수 있습니다. 연결 자격 증명을 확인 하세요. |
|   | 10 | 클라이언트 메시지가 프로토콜 위반을 트리거 했습니다. 예는 시작 하기 전에 보낸 쿼리 메시지입니다. |

### <a name="troubleshoot-query-consumption"></a>쿼리 사용 문제 해결
Cdb분할 키를 사용 하는 테이블에는 각 실제 파티션 내에서 각 지역의 논리적 키에 대 한 (요청 단위) 사용에 대 한 세부 정보가 포함 되어 있습니다.

```Kusto
CDBPartitionKeyRUConsumption 
| summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
| render columnchart
 ```

### <a name="explore-control-plane-operations"></a>제어 평면 작업 살펴보기
CBDControlPlaneRequests 테이블에는 특히 Cassandra API 계정에 대 한 제어 평면 작업에 대 한 세부 정보가 포함 되어 있습니다. 

```Kusto
CDBControlPlaneRequests
| where TimeGenerated > now(-6h)
| where  ApiKind == "Cassandra"
| where OperationName in ("Create", "Upsert", "Delete", "Execute")
| summarize by OperationName
 ```

## <a name="next-steps"></a>다음 단계

- [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)에 대해 자세히 알아보기
- [기본 Apache Cassandra에서 Azure Cosmos DB Cassandra API](migrate-data-databricks.md)로 마이그레이션하는 방법에 대해 알아봅니다.