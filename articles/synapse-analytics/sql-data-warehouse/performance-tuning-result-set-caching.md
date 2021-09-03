---
title: 결과 세트 캐싱을 사용한 성능 조정
description: Azure Synapse Analytics의 전용 SQL 풀에 대한 결과 집합 캐싱 기능 개요
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: edd131be48b75ccbedf0e537f92b69de214076ee
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005351"
---
# <a name="performance-tuning-with-result-set-caching"></a>결과 세트 캐싱을 사용한 성능 조정

결과 집합 캐싱을 사용하도록 설정한 경우 전용 SQL 풀은 반복 사용을 위해 사용자 데이터베이스에 쿼리 결과를 자동으로 캐시합니다.  이렇게 하면 후속 쿼리 실행 시 지속형 캐시에서 직접 결과를 가져올 수 있으므로 재계산이 필요하지 않습니다.   결과 세트 캐싱은 쿼리 성능을 향상시키고 컴퓨팅 리소스 사용량을 줄입니다.  또한 캐시된 결과 세트를 사용하는 쿼리는 동시성 슬롯을 사용하지 않으므로 기존 동시성 제한이 적용되지 않습니다. 보안을 위해 캐시된 결과를 만든 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시된 결과에 액세스할 수 있습니다.  결과 집합 캐싱은 기본적으로 데이터베이스 및 세션 수준에서 끄기입니다. 

## <a name="key-commands"></a>주요 명령

[사용자 데이터베이스에 대한 결과 세트 캐싱 설정/해제](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[세션에 대한 결과 세트 캐싱 설정/해제](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[캐시된 결과 세트의 크기 확인](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

[캐시 정리](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="whats-not-cached"></a>캐시되지 않는 내용  

데이터베이스에 대해 결과 집합 캐싱이 켜진 경우, 다음 쿼리를 제외하고 캐시가 가득 찰 때까지 모든 쿼리에 대해 결과가 캐시됩니다.

- 기본 테이블의 데이터 또는 쿼리가 변경되지 않은 경우에도 비결정적인 기본 제공 함수 또는 런타임 식으로 쿼리합니다. 예: DateTime.Now(), GetDate()와 같습니다
- 사용자 정의 함수를 사용하여 쿼리
- 행 수준 보안 또는 열 수준 보안이 설정된 테이블을 사용하는 쿼리
- 행 크기가 64KB보다 큰 데이터를 반환하는 쿼리
- 크기가 큰 데이터(10GB 초과)를 반환하는 쿼리 
>[!NOTE]
> - 일부 비결정적 함수 및 런타임 식은 동일한 데이터에 대한 반복 쿼리에 결정적일 수 있습니다. 예: ROW_NUMBER ()  
> - 쿼리 결과 집합의 행 순서/시퀀스가 애플리케이션 논리에 중요한 경우 쿼리에 ORDER BY를 사용합니다.
> - ORDER BY 열의 데이터가 고유하지 않은 경우 결과 집합 캐싱의 사용 여부에 관계없이 ORDER BY 열의 값이 동일한 행에는 행 순서가 보장되지 않습니다.

> [!IMPORTANT]
> 결과 집합 캐시를 만들고 캐시에서 데이터를 검색하는 작업은 전용 SQL 풀 인스턴스의 제어 노드에서 수행됩니다.
> 결과 세트 캐싱이 켜져 있을 때 큰 결과 세트(예: 1GB 초과)를 반환하는 쿼리를 실행하면 제어 노드에서 제한이 증가하고 인스턴스의 전체 쿼리 응답 속도가 느려질 수 있습니다.  이러한 쿼리는 일반적으로 데이터 탐색 또는 ETL 작업 중에 사용됩니다. 컨트롤 노드의 스트레스를 방지하고 성능 문제가 발생하는 것을 방지하려면 사용자는 해당 유형의 쿼리를 실행하기 전에 데이터베이스에서 결과 집합 캐싱을 해제해야 합니다.  

쿼리에 대한 결과 세트 캐싱 작업에 소요된 시간 동안 다음 쿼리를 실행합니다.

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

다음은 결과 세트 캐싱을 사용하지 않도록 설정한 상태로 실행되는 쿼리의 예제 출력입니다.

![위치 유형 및 명령을 포함하여 쿼리 결과를 보여 주는 스크린샷](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

다음은 결과 세트 캐싱을 사용하도록 설정한 상태로 실행되는 쿼리의 예제 출력입니다.

![\* [D W ResultCache D b] dot d b o에서 선택된 명령을 호출한 쿼리 결과를 보여주는 스크린샷](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>캐시된 결과가 사용되는 경우

캐시된 결과 집합은 다음 요구 사항이 모두 충족되는 경우 쿼리에 다시 사용됩니다.

- 쿼리를 실행하는 사용자는 쿼리에서 참조된 모든 테이블에 액세스할 수 있습니다.
- 새 쿼리와 결과 집합 캐시를 생성한 이전 쿼리가 정확히 일치합니다.
- 캐시된 결과 집합이 생성된 테이블에는 데이터 또는 스키마 변경 내용이 없습니다.

이 명령을 실행하여 결과 캐시 적중 또는 누락으로 쿼리를 실행했는지 확인합니다. result_cache_hit 열은 캐시 적중 시 1을 반환하고, 캐시 누락 시 0을 반환하고, 결과 집합 캐싱이 사용되지 않은 이유에 대해 음수 값을 반환합니다. 자세한 내용은 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>캐시된 결과 관리

결과 세트 캐시의 최대 크기는 데이터베이스당 1TB입니다.  기본 쿼리 데이터가 변경되면 캐시된 결과가 자동으로 무효화됩니다.  

캐시 제거는 다음 일정에 따라 전용 SQL 풀에서 자동으로 관리됩니다.

- 결과 세트가 사용되지 않았거나 무효화된 경우 48시간 간격으로
- 결과 세트 캐시가 최대 크기에 가까워질 때

다음 옵션 중 하나를 사용하여 전체 결과 세트 캐시를 수동으로 비울 수 있습니다.

- 데이터베이스에 대한 결과 집합 캐시 기능을 해제
- 데이터베이스에 연결된 상태에서 DBCC DROPRESULTSETCACHE 실행

데이터베이스를 일시 중지해도 캐시된 결과 세트가 비워지지 않습니다.  

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
