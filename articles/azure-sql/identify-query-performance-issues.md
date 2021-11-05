---
title: Azure SQL Database에서 쿼리 성능 문제 형식
description: 이 문서에서는 Azure SQL Database의 쿼리 성능 문제 형식에 관해 알아보고, 해당 문제를 사용하여 쿼리를 식별하고 해결하는 방법에 관해서도 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: NikaKinska
ms.author: nnikolic
ms.reviewer: mathoma, wiassaf
ms.date: 11/04/2021
ms.openlocfilehash: d4b9249ece6117af35466b0984c9dc5ceff2a224
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843639"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Database에서 검색 가능한 쿼리 성능 병목 상태 유형
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

성능 병목 상태를 해결하려는 경우, 우선 쿼리가 실행 중 상태 또는 대기 상태일 때 병목 현상이 발생하는지 확인합니다. 확인 내용에 따라 어떤 해결 방법이 적용할지가 달라집니다. 다음 다이어그램을 사용하면 실행 관련 문제 또는 대기 관련 문제를 일으킬 수 있는 요소를 이해하는 데 도움이 됩니다. 문제 및 각 문제 형식에 관련된 해결 방법은 이 문서에 설명되어 있습니다.

Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) 또는 SQL Server [DMV](database/monitoring-with-dmvs.md)를 사용하여 해당 형식의 성능 병목 상태를 검색할 수 있습니다.

![워크로드 상태](./media/identify-query-performance-issues/workload-states.png)

**실행 관련 문제**: 일반적으로 실행 관련 문제는 컴파일 문제와 관련이 있으며, 만족스럽지 못한 쿼리 계획 또는 부족하거나 남용된 리소스와 관련된 실행 문제를 일으킵니다.
**대기 관련 문제**: 대기 관련 문제는 일반적으로 다음과 관련이 있습니다.

- 잠금(차단)
- I/O
- TempDB 사용량과 관련된 경합
- 메모리 부여 대기

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>만족스럽지 못한 쿼리 계획을 발생시키는 컴파일 문제

SQL 쿼리 최적화 프로그램에서 생성된 만족스럽지 못한 계획은 쿼리의 성능을 저하할 수 있습니다. SQL 쿼리 최적화 프로그램은 누락된 인덱스, 부실 통계, 프로세스할 행 수에 대한 잘못된 예상치 또는 필요한 메모리에 대한 부정확한 예상치로 인해 만족스럽지 못한 계획을 생성할 수 있습니다. 이전 인스턴스 또는 다른 인스턴스에서 쿼리가 더 빠르게 실행된 경우, 실제 실행 계획을 비교하여 차이가 있는지 확인합니다.

- 다음 메서드 중 하나를 사용하여 누락된 인덱스를 식별합니다.

  - [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index) 사용.
  - 단일 데이터베이스 및 풀링된 데이터베이스를 위한 [Database Advisor](database/database-advisor-implement-performance-recommendations.md).
  - DMV. 이 예제에서는 누락된 인덱스의 영향, DMV를 사용하여 [누락된 인덱스](database/performance-guidance.md#identifying-and-adding-missing-indexes)를 검색하는 방법, 누락된 인덱스 권장 사항을 구현할 때의 영향을 보여 줍니다.
- 더 나은 계획을 얻으려면 [쿼리 힌트](/sql/t-sql/queries/hints-transact-sql-query)를 적용하거나, [통계를 업데이트](/sql/t-sql/statements/update-statistics-transact-sql)하거나, [인덱스를 다시 빌드](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)해 보세요. 이런 문제를 자동으로 완화할 수 있게 Azure SQL Database에서 [자동 계획 수정](../azure-sql/database/automatic-tuning-overview.md) 기능을 사용하세요.

  이 [예제](database/performance-guidance.md#query-tuning-and-hinting)에서는 매개 변수가 있는 쿼리로 초래된 만족스럽지 못한 쿼리 계획의 영향, 해당 조건을 검색하는 방법, 쿼리 힌트를 사용하여 해결하는 방법을 보여 줍니다.

- 데이터베이스 호환성 수준을 변경하고 지능형 쿼리 처리를 구현해 보세요. SQL 쿼리 최적화 프로그램은 데이터베이스의 호환성 수준에 따라 쿼리 계획을 다르게 생성할 수 있습니다. 호환성 수준이 높으면 보다 [인텔리전트 쿼리 처리 기능](/sql/relational-databases/performance/intelligent-query-processing)을 제공합니다.

  - 쿼리 처리 관련한 자세한 내용은 [쿼리 처리 아키텍처 가이드](/sql/relational-databases/query-processing-architecture-guide)를 참조하세요.
  - 데이터베이스 호환성 수준을 변경하고 호환성 수준 간의 차이점에 관한 자세한 내용을 알아보려면 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)를 참조하세요.
  - 카디널리티 예측에 관한 자세한 내용은 [카디널리티 예측](/sql/relational-databases/performance/cardinality-estimation-sql-server)을 참조하세요.

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>만족스럽지 못한 쿼리 실행 계획으로 쿼리 해결

다음 섹션에서는 만족스럽지 못한 쿼리 실행 계획을 사용하여 쿼리를 해결하는 방법을 설명합니다.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> PSP(매개 변수에 민감한 계획) 문제가 있는 쿼리

PSP(매개 변수에 민감한 계획) 문제는 쿼리 최적화 프로그램에서 특정 매개 변숫값(또는 값 집합)에만 최적인 쿼리 실행 계획을 생성하고, 캐시된 계획이 연속 실행 시 사용되는 매개 변숫값에 최적이 아니면 발생합니다. 최적이 아닌 계획은 쿼리 성능 문제를 초래하고, 전체 워크로드 처리량을 저하할 수 있습니다.

매개 변수 스니핑 및 쿼리 처리 관련한 자세한 내용은 [쿼리-처리 아키텍처 가이드](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)를 참조하세요.

PSP 문제를 완화하는 방법은 여러 가지가 있습니다. 해결 방법마다 장단점이 있습니다.

- 각 쿼리 실행 시 [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 해결 방법을 사용할 경우 품질은 향상되지만 컴파일 시간 및 CPU가 늘어납니다. 때에 따라 높은 처리량이 필요한 워크로드에는 `RECOMPILE` 옵션을 사용하지 못할 수 있습니다.
- 실제 매개 변숫값을 대부분의 매개 변숫값에 충분히 적합한 계획을 생성하는 일반적인 매개 변숫값으로 재정의하려면 [OPTION(OPTIMIZE FOR…)](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 옵션을 사용하려면 최적의 매개 변수 값 및 관련 계획 특성을 적절히 이해해야 합니다.
- 밀도 벡터 평균을 사용하는 대신, 실제 매개 변숫값을 재정의하려면 [OPTION(OPTIMIZE FOR UNKNOWN)](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 작업을 수행하는 또 다른 방법은 수신 매개 변숫값을 로컬 변수에 캡처하고, 매개 변수 자체를 사용하는 대신 조건자 내에서 해당 로컬 변수를 사용하는 것입니다. 이 경우에는 평균 밀도가 *충분* 해야 합니다.
- [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 매개 변수 검사를 완전히 사용하지 않도록 설정합니다.
- [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 캐시에서 다시 컴파일이 발생하지 않도록 합니다. 이 해결 방법은 충분히 좋은 일반 계획이 캐시에 이미 있다고 가정합니다. 자동 통계 업데이트를 사용하지 않도록 설정하여 적합한 계획이 제거될 가능성을 줄이고, 새로운 잘못된 계획을 컴파일할 수도 있습니다.
- 쿼리를 다시 작성하고 쿼리 텍스트에 힌트를 추가하여 [사용 계획](/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 명시적으로 사용함으로써 계획을 강제로 적용합니다. 또는 쿼리 저장소를 사용하거나 [자동 튜닝](../azure-sql/database/automatic-tuning-overview.md)을 사용하여 특정 계획을 설정합니다.
- 단일 프로시저를 각각을 조건부 논리 및 연결된 매개 변수 값을 기준으로 사용할 수 있는 중첩된 프로시저 세트로 바꿉니다.
- 정적 프로시저 정의를 대신하는 동적 문자열 실행을 만듭니다.

PSP 문제를 해결하는 방법 관련한 자세한 내용은 다음 블로그 게시물을 참조하세요.

- ["I smell a parameter!"(매개 변수를 찾았습니다!)](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamic SQL vs. procedures vs. plan quality for parameterized queries(매개 변수화된 쿼리를 위한 Conor 대 동적 SQL 대 프로시저 대 계획 품질)](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)

### <a name="compile-activity-caused-by-improper-parameterization"></a>잘못된 매개 변수화로 인한 컴파일 작업

쿼리에 리터럴이 있으면 데이터베이스 엔진이 자동으로 문을 매개 변수화하거나 사용자가 문을 명시적으로 매개 변수화하여 컴파일 수를 줄입니다. 패턴이 같지만 다른 리터럴 값을 사용하는 쿼리를 여러 번 컴파일하면 CPU 사용량이 많아질 수 있습니다. 마찬가지로, 리터럴을 계속 포함하는 쿼리를 부분적으로만 매개 변수화해도 데이터베이스 엔진이 추가로 쿼리를 매개 변수화하지는 않습니다.

다음은 부분적으로 매개 변수가 있는 쿼리 예제입니다.

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

이 예제에서 `t1.c1`은 `@p1`을 사용하지만 `t2.c2`는 계속해서 GUID를 리터럴로 사용합니다. 이 경우 `c2` 값을 변경하면 쿼리는 다른 쿼리로 처리되고 새 컴파일이 발생합니다. 이 예제에서 컴파일을 줄이려면 GUID도 매개 변수화합니다.

다음 쿼리는 쿼리가 적절히 매개 변수화되었는지를 확인하기 위한 쿼리 해시별 쿼리 수를 보여 줍니다.

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>쿼리 계획 변경에 영향을 주는 요소

쿼리 실행 계획을 다시 컴파일하면 원래 캐시된 계획과 다른 쿼리 계획이 생성될 수 있습니다. 다음과 같은 다양한 이유로 원래 계획이 자동으로 다시 컴파일될 수 있습니다.

- 스키마의 변경 내용을 쿼리에서 참조합니다.
- 테이블 데이터 변경 내용은 쿼리에서 참조합니다.
- 쿼리 컨텍스트 옵션이 변경되었습니다.

다음과 같은 다양한 이유로 컴파일된 계획이 캐시에서 분리될 수 있습니다.

- 인스턴스 다시 시작
- 데이터베이스 범위 구성 변경
- 메모리 부족
- 캐시를 지워달라는 명시적 요청

RECOMPILE 힌트를 사용하는 경우에는 계획이 캐시되지 않습니다.

다시 컴파일하면(또는 캐시 제거 후 새로 컴파일하면) 원래 계획과 동일한 쿼리 실행 계획이 여전히 생성될 수 있습니다. 계획이 이전 계획 또는 원래 계획에서 변경되었을 때는 다음과 같은 설명이 가능합니다.

- **변경된 실제 디자인**: 예를 들어 새로 만든 인덱스는 쿼리 요구 사항을 보다 효과적으로 다룹니다. 새 인덱스는 쿼리 최적화 프로그램에서 쿼리 실행의 첫 번째 버전을 위해 원래 선택한 데이터 구조를 사용하는 것보다 새 인덱스를 사용하는 것이 더 적합하다고 판단하는 경우 새 컴파일에서 사용될 수 있습니다. 참조된 개체를 물리적으로 변경하면 컴파일 시간에 새 계획을 선택할 수 있습니다.

- **서버 리소스 차이**: 한 시스템의 계획이 다른 시스템의 계획과 다를 경우, 리소스 가용성(예: 사용 가능한 프로세서 수)이 어떤 계획이 생성되느냐에 영향을 줄 수 있습니다. 예를 들어 한 시스템에 더 많은 프로세서가 있는 경우 병렬 계획을 선택할 수 있습니다. Azure SQL Database의 병렬 처리 관련한 자세한 내용은 [Azure SQL Database에서 최대 병렬 처리 수준 구성(MAXDOP)](database/configure-max-degree-of-parallelism.md)을 참조하세요.

- **다른 통계**: 참조된 개체와 연결된 통계가 변경되었거나 원래 시스템의 통계와 상당히 다를 수 있습니다. 통계가 변경되고 컴파일이 다시 발생하면 쿼리 최적화 프로그램은 변경 시점부터 통계를 사용하기 시작합니다. 수정된 통계의 데이터 배포 및 빈도는 원래 컴파일과는 다를 수 있습니다. 이 변경 내용은 카디널리티 예상치를 만드는 데 사용됩니다. (*카디널리티 예상치* 는 논리적 쿼리 트리를 통해 전달될 것으로 예상되는 행의 수입니다.) 카디널리티 예상치를 변경하면 다른 물리 연산자 및 관련 작업 순서를 선택할 수 있습니다. 통계를 약간만 변경해도 쿼리 실행 계획이 변경될 수 있습니다.

- **변경된 데이터베이스 호환성 수준 또는 카디널리티 예측 도구 버전**: 데이터베이스 호환성 수준이 변경되면 다른 쿼리 실행 계획을 만들 수 있는 새로운 전략과 기능을 사용할 수 있습니다. 데이터베이스 호환성 수준 외에도 사용하지 않거나 사용하도록 설정된 추적 플래그 4199 또는 데이터베이스 범위 구성 QUERY_OPTIMIZER_HOTFIXES의 변경된 상태는 컴파일 시간에 쿼리 실행 계획 선택에 영향을 줄 수 있습니다. 추적 플래그 9481(강제 레거시 CE) 및 2312(강제 기본 CE)도 계획에 영향을 줍니다.

## <a name="resource-limits-issues"></a>리소스 한도 문제

만족스럽지 못한 쿼리 계획 및 누락된 인덱스와 관련되지 않은 쿼리 성능 저하는 일반적으로 부족한 리소스 또는 남용된 리소스와 관련이 있습니다. 쿼리 계획이 최적이면 쿼리(및 데이터베이스)가 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스의 리소스 한도에 도달하는 것일 수 있습니다. 예를 들면 서비스 수준을 위한 과도한 로그 쓰기 처리량이 있을 수 있습니다.

- Azure Portal를 사용하여 리소스 문제 검색: 리소스 한도에 문제가 있는지 확인하려면 [SQL Database 리소스 모니터링](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)을 참조하세요. 단일 데이터베이스 및 탄력적 풀은 [Database Advisor 성능 권장 사항](database/database-advisor-implement-performance-recommendations.md) 및 [Query Performance Insight](database/query-performance-insight-use.md)를 참조하세요.
- [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)를 사용하여 리소스 제한 검색
- [DMV](database/monitoring-with-dmvs.md)를 사용하여 리소스 문제 검색:

  - [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV는 데이터베이스의 CPU, I/O, 메모리 소비를 반환합니다. 데이터베이스에서 작업이 없더라도 15초 간격으로 한 행이 있습니다. 기록 데이터는 한 시간 동안 유지됩니다.
  - [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV는 Azure SQL Database을 위한 CPU 사용량 및 스토리지 데이터를 반환합니다. 데이터는 5분 간격 이내로 수집 및 집계됩니다.
  - [점증적으로 CPU 사용량이 높은 여러 개별 쿼리](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

리소스 부족으로 인한 문제를 식별하는 경우, 리소스를 업그레이드하여 데이터베이스의 용량을 확대함으로써 CPU 요구 사항을 충족할 수 있습니다. 자세한 내용은 [Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정](database/single-database-scale.md) 및 [Azure SQL Database에서 탄력적 풀 리소스 크기 조정](database/elastic-pool-scale.md)을 참조하세요. 관리되는 인스턴스 크기 조정 관련한 자세한 내용은 [서비스 계층 리소스 제한](managed-instance/resource-limits.md#service-tier-characteristics)을 참조하세요.

## <a name="performance-problems-caused-by-increased-workload-volume"></a>워크로드 볼륨이 증가하여 발생하는 성능 문제

애플리케이션 트래픽과 워크로드 볼륨이 증가하면 CPU 사용량이 증가할 수 있습니다. 그러나 이 문제를 적절하게 진단하려면 주의를 기울여야 합니다. 높은 CPU 문제가 표시되면 다음 질문에 대답하여 워크로드 볼륨의 변경으로 증가했는지를 확인합니다.

- 애플리케이션의 쿼리가 높은 CPU 문제의 원인이 되나요?
- [식별할 수 있는 상위 CPU 소비 쿼리](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - 동일한 쿼리와 연결된 실행 계획이 여러 개인가요? 그렇다면 이유는 무엇인가요?
  - 실행 계획이 동일한 쿼리의 경우 실행 시간이 일관되었나요? 실행 횟수가 증가했나요? 그렇다면 워크로드 증가로 인해 성능 문제가 발생할 수 있습니다.

즉, 쿼리 실행 계획은 다르게 실행되지만, CPU 사용량은 실행 수에 따라 증가할 경우 성능 문제는 워크로드 증가와 관련이 있을 수 있습니다.

CPU 문제를 발생시키는 워크로드 볼륨 변경 사항을 식별하기가 항상 쉽지는 않습니다. 다음 항목을 고려합니다.

- **변경된 리소스 사용량**: 예를 들어, 장시간 동안 CPU 사용량이 80%까지 증가하는 시나리오를 고려해 보세요. CPU 사용량만으로는 워크로드 볼륨이 변경된 것을 의미하지 않습니다. 애플리케이션이 정확히 동일한 워크로드를 실행하더라도 쿼리 실행 계획 회귀 및 데이터 분포 변경 또한 리소스 사용량 증가에 영향을 줄 수 있습니다.

- **새 쿼리의 모양**: 애플리케이션에서 다른 시간에 새 쿼리 집합을 구동하는 때도 있습니다.

- **요청 수 증가 또는 감소**: 이 시나리오는 워크로드의 가장 확실한 측정값입니다. 쿼리 수가 많다고 해서 항상 리소스 사용률이 많은 것은 아닙니다. 그렇지만 이 메트릭은 다른 요소가 변경되지 않으면 중대한 신호가 됩니다.

Intelligent Insights를 사용하여 [워크로드 증가](database/intelligent-insights-troubleshoot-performance.md#workload-increase) 및 [계획 회귀](database/intelligent-insights-troubleshoot-performance.md#plan-regression)를 검색합니다.

- **병렬 처리**: 과도한 병렬 처리는 다른 CPU 및 작업자 스레드 리소스 쿼리의 리소스를 사용함으로써 다른 동시 워크로드 성능을 저해할 수 있습니다. Azure SQL Database의 병렬 처리 관련한 자세한 내용은 [Azure SQL Database에서 최대 병렬 처리 수준 구성(MAXDOP)](database/configure-max-degree-of-parallelism.md)을 참조하세요.

## <a name="waiting-related-problems"></a>대기 관련 문제

만족스럽지 못한 계획과 실행 문제와 관련된 *대기 관련* 문제를 제거하면 일반적으로 성능 문제는 쿼리가 일부 리소스를 대기하는 것일 수 있습니다. 대기 관련 문제는 다음과 같은 경우에 발생할 수 있습니다.

- **차단**:

  한 쿼리는 데이터베이스의 개체 잠금을 유지할 수 있지만, 다른 쿼리는 동일한 개체에 액세스하려고 합니다. [DMV](database/monitoring-with-dmvs.md#monitoring-blocked-queries) 또는 [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking)를 사용하여 차단 쿼리를 식별할 수 있습니다. 자세한 내용은 [Azure SQL 차단 문제 이해 및 해결](database/understand-resolve-blocking.md)을 참조하세요.
- **IO 문제**

  쿼리가 데이터 또는 로그 파일에 페이지가 기록될 때까지 대기하고 있을 수 있습니다. 이 경우 DMV에서 `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG` 또는 `PAGEIOLATCH_*` 대기 통계를 확인합니다. DMV를 사용하여 [IO 성능 문제 식별](database/monitoring-with-dmvs.md#identify-io-performance-issues)을 참조하세요.
- **TempDB 문제**

  워크로드에서 임시 테이블을 사용하거나 계획에 TempDB 스필이 있는 경우 쿼리에 TempDB 처리량 문제가 있을 수 있습니다. DMV를 사용하여 [TempDB 문제 식별](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)을 참조하세요.
- **메모리 관련 문제**

  워크로드에 충분한 메모리가 없는 경우 페이지 예상 수명이 줄어들거나 쿼리에 필요한 것보다 메모리가 작게 확보될 수 있습니다. 때에 따라 쿼리 최적화 프로그램에서 기본 제공되는 인텔리전스를 통해 메모리 관련 문제를 해결할 수 있습니다. DMV를 사용하여 [메모리 부여 문제 파악](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)을 참조하세요. 자세한 내용 및 샘플 쿼리는 [Azure SQL Database을 사용 하 여 메모리 부족 오류 해결](database/troubleshoot-memory-errors-issues.md)을 참조 하세요.

### <a name="methods-to-show-top-wait-categories"></a>상위 대기 범주를 표시하는 메서드

해당 메서드는 일반적으로 대기 형식의 상위 범주를 표시하는 데 사용됩니다.

- [대기 시간이 증가](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)하여 성능이 저하된 쿼리를 식별하는 Intelligent Insights 사용
- [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)를 사용하여 시간에 따른 각 쿼리의 대기 통계를 찾을 수 있습니다. 쿼리 저장소에서 대기 유형은 대기 범주로 결합됩니다. 대기 유형에 대한 대기 범주의 매핑은 [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)에서 찾을 수 있습니다.
- [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)를 사용하여 쿼리 작업 중에 실행된 스레드로 인해 발생한 모든 대기 관련 정보를 반환합니다. 이 집계 보기를 사용하여 Azure SQL Database와 특정 쿼리 및 일괄 처리의 성능 문제를 진단할 수도 있습니다. 쿼리는 리소스, 큐 대기 또는 외부 대기를 기다릴 수 있습니다.
- [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)를 사용하여 일부 리소스에서 대기 중인 작업의 큐 관련 정보를 반환합니다.

높은 CPU 시나리오에서는 다음과 같은 경우 쿼리 저장소 및 대기 통계가 CPU 사용량을 반영하지 않을 수 있습니다.

- 높은 CPU 소비 쿼리가 아직 실행되고 있습니다.
- 장애 조치(failover)가 발생했을 때 높은 CPU 소비 쿼리가 실행 중이었습니다.

쿼리 저장소 및 대기 통계를 추적하는 DMV는 성공적으로 완료되고 시간이 초과한 쿼리의 결과만 표시합니다. 문이 완료될 때까지 현재 실행 중인 문의 데이터를 표시하지 않습니다. 동적 관리 뷰 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)를 사용하여 현재 실행 중인 쿼리와 관련된 작업자 시간을 추적합니다.

> [!TIP]
> 추가 도구:
>
> - [TigerToolbox 대기 및 래치](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="see-also"></a>참고 항목

* [Azure SQL Database에서 최대 병렬 처리 수준(MAXDOP) 구성](database/configure-max-degree-of-parallelism.md)
* [Azure SQL Database에서 Azure SQL Database 차단 문제의 이해 및 해결](database/understand-resolve-blocking.md)

## <a name="next-steps"></a>다음 단계

* [SQL Database 모니터링 및 튜닝 개요](database/monitor-tune-overview.md)
