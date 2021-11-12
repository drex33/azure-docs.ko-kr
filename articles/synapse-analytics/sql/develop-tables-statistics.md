---
title: Azure Synapse SQL 리소스를 사용한 통계 제작 및 업데이트
description: Synapse SQL에서 쿼리 최적화 통계를 만들고 업데이트하기 위한 권장 사항과 예제입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 26d2b6123e40f163d261009ff1c2f706f7597825
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309608"
---
# <a name="statistics-in-synapse-sql"></a>Synapse SQL의 통계

이 문서에서는 Synapse SQL 리소스인 SQL 풀과 서버리스 SQL 풀을 사용해 쿼리 최적화 통계를 만들고 업데이트하기 위한 권장 사항과 예제를 보게 됩니다.

## <a name="statistics-in-dedicated-sql-pool"></a>전용 SQL 풀의 통계

### <a name="why-use-statistics"></a>통계를 사용하는 이유

전용 SQL 풀에서 데이터에 대해 많이 알수록 쿼리를 빠르게 실행할 수 있습니다. 전용 SQL 풀로 데이터를 로드한 후에 쿼리 최적화를 위해 수행할 수 있는 가장 중요한 작업 중 하나는 데이터에 대한 통계 수집입니다.  

전용 SQL 풀 쿼리 최적화 프로그램은 비용 기반 최적화 프로그램입니다. 다양한 쿼리 계획의 비용을 비교한 다음, 비용이 가장 낮은 계획을 선택합니다. 선택된 계획은 대부분의 경우 가장 빠르게 실행되는 계획입니다.

예를 들어 최적화 프로그램이 쿼리에서 필터링하는 날짜가 하나의 행을 반환한다고 예측하는 경우에는 특정 계획을 선택할 수 있고, 선택한 날짜가 1백만 개의 행을 반환한다고 예측하는 경우에는 또 다른 계획을 선택할 수 있습니다.

### <a name="automatic-creation-of-statistics"></a>통계 자동 생성

전용 SQL 풀 엔진은 데이터베이스 AUTO_CREATE_STATISTICS 옵션이 `ON`으로 설정된 경우 수신 사용자 쿼리를 분석하여 누락된 통계가 있는지 확인합니다.  통계가 누락된 경우 쿼리 최적화 프로그램은 쿼리 조건자 또는 조인 조건의 개별 열에 대해 통계를 만듭니다. 

이 기능은 쿼리 계획의 카디널리티 추정을 개선하는 데 사용됩니다.

> [!IMPORTANT]
> 통계 자동 생성은 현재 기본적으로 설정됩니다.

다음 명령을 실행하여 데이터 웨어하우스에 AUTO_CREATE_STATISTICS가 구성되어 있는지 확인할 수 있습니다.

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

데이터 웨어하우스에 AUTO_CREATE_STATISTICS가 설정되어 있지 않은 경우 다음 명령을 실행하여 이 속성을 사용하도록 설정하는 것이 좋습니다.

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

다음 문은 통계의 자동 생성을 트리거합니다.

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- Delete
- EXPLAIN - 조인을 포함하거나 조건자의 존재가 감지되는 경우

> [!NOTE]
> 임시 테이블이나 외부 테이블에서는 통계 자동 생성이 생성되지 않습니다.

통계 자동 생성은 동기식으로 수행됩니다. 따라서 열에 통계가 없는 경우 쿼리 성능이 약간 저하될 수 있습니다. 단일 열에 대한 통계를 만드는 데 걸리는 시간은 테이블 크기에 따라 달라집니다.

체감될 정도의 성능 저하를 방지하려면 시스템을 프로파일링하기 전에 벤치마크 워크로드를 실행하여 통계가 생성되도록 해야 합니다.

> [!NOTE]
> 통계 생성은 다른 사용자 컨텍스트의 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)에 로깅됩니다.

생성되는 자동 통계의 형식은 _WA_Sys_<16진수 8자리 열 ID>_<16진수 8자리 테이블 ID>입니다. 이미 생성된 통계는 [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) 명령을 실행하여 볼 수 있습니다.

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name은 표시할 통계를 포함하는 테이블의 이름으로, 이 테이블은 외부 테이블일 수 없습니다. target은 통계 정보를 표시할 대상 인덱스, 통계 또는 열의 이름입니다.

### <a name="update-statistics"></a>통계 업데이트

모범 사례 중 하나는 새로운 날짜가 추가되는 날마다 날짜 열에 통계를 업데이트하는 것입니다. 새 행이 데이터 웨어하우스에 로드될 때마다 새 부하 날짜나 트랜잭션 날짜가 추가됩니다. 이로 인해 데이터 분포가 변경되며 통계가 최신 상태가 아니게 됩니다.

값 분포는 일반적으로 변경되지 않기 때문에 고객 테이블의 국가 또는 지역 열에 대한 통계는 업데이트할 필요가 없을 수 있습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다.

그러나 데이터 웨어하우스에 하나의 국가 또는 지역만 포함되어 있고 새 국가 또는 지역에서 데이터를 가져오는 경우, 국가 또는 지역 열에 대한 통계를 업데이트해야 합니다.

통계를 업데이트하는 권장 사항은 다음과 같습니다.

|유형|권장|
|-|-|
| **통계 업데이트의 빈도**  | 일반: 매일 </br> 데이터 로드 또는 변환 후 |
| **샘플링** |  10억 개 미만의 행인 경우 기본 샘플링(20%)을 사용합니다. </br> 10억 개 이상의 행인 경우 2%의 샘플링을 사용합니다. |

### <a name="determine-last-statistics-update"></a>마지막 통계 업데이트 확인

쿼리 문제를 해결할 때 가장 먼저 묻는 질문 중 하나는 **"통계가 최신 상태입니까?"** 입니다.

이 질문은 데이터의 보존 기간을 기준으로 답할 수 있는 질문이 아닙니다. 기본 데이터에 중대한 변경이 없는 경우 최신 통계 개체가 오래되었을 수 있습니다. 행 수가 크게 변경되었거나 열에 대한 값 분포에 중대한 변경이 발생했다면 바로 이 시점에 통계를 업데이트해야 합니다.

통계가 마지막으로 업데이트된 후에 테이블의 데이터가 변경되었는지 확인하는 데 사용할 수 있는 동적 관리 뷰가 없으므로 통계의 보존 기간을 알면 얼마간의 도움이 될 수 있습니다. 

다음 쿼리를 사용하여 각 테이블에서 통계가 마지막으로 업데이트된 시간을 확인할 수 있습니다.

> [!NOTE]
> 열에 대한 값 분포에 중대한 변경이 있는 경우 데이터가 마지막으로 업데이트된 시간과 관계없이 통계를 업데이트해야 합니다.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

예를 들어, 일반적으로 데이터 웨어하우스의 **날짜 열** 은 자주 통계 업데이트가 필요합니다. 새 행이 데이터 웨어하우스에 로드될 때마다 새 부하 날짜나 트랜잭션 날짜가 추가됩니다. 이로 인해 데이터 분포가 변경되며 통계가 최신 상태가 아니게 됩니다.

고객 테이블의 성별 열에 대한 통계는 업데이트할 필요가 없을 수 있습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다.

그러나 데이터 웨어하우스에 성별이 하나만 있고 새로운 요구 사항으로 인해 성별이 여러 개가 되는 경우, 성별 열에 대한 통계를 업데이트해야 합니다. 

자세한 내용은 [통계](/sql/relational-databases/statistics/statistics) 문서를 참조하세요.

### <a name="implement-statistics-management"></a>통계 관리 구현

로드가 끝날 때 통계가 업데이트되도록 데이터 로드 프로세스를 확장하는 것이 좋습니다. 데이터 로드는 테이블이 값의 크기 및/또는 분포를 자주 변경하는 경우입니다. 따라서 로드 프로세스는 일부 관리 프로세스를 구현할 논리적인 위치가 됩니다.

로드 프로세스 중에 통계를 업데이트하기 위해 제공되는 지침 원칙은 다음과 같습니다.

- 로드된 각 테이블에 하나 이상의 업데이트된 통계 개체가 있는지 확인합니다. 이 프로세스는 통계 업데이트의 일부로 테이블 크기(행 수 및 페이지 수) 정보를 업데이트합니다.
- JOIN, GROUP BY, ORDER BY 및 DISTINCT 절에 참여하는 열에 집중합니다.
- 이러한 값은 통계 히스토그램에 포함되지 않으므로 트랜잭션 날짜와 같은 “오름차순 키” 열을 더 자주 업데이트하는 것이 좋습니다.
- 정적 배포 열은 자주 업데이트하지 않는 것이 좋습니다.
- 각 통계 개체는 순서대로 업데이트됩니다. `UPDATE STATISTICS <TABLE_NAME>`을 구현하는 것만이 항상 이상적인 것은 아닙니다(특히 통계 개체가 많이 있는 광범위한 테이블의 경우).

자세한 내용은 [카디널리티 예측](/sql/relational-databases/performance/cardinality-estimation-sql-server)을 참조합니다.

### <a name="examples-create-statistics"></a>예제: 통계 만들기

이 예제는 통계를 만들기 위한 다양한 옵션을 사용하는 방법을 보여줍니다. 각 열에 대해 사용하는 옵션은 데이터의 특징 및 열이 쿼리에서 사용되는 방법에 따라 다릅니다.

#### <a name="create-single-column-statistics-with-default-options"></a>기본 옵션으로 단일 열 통계 만들기

열에 대해 통계를 만들려면 통계 개체의 이름과 열 이름을 지정합니다.
이 구문은 모든 기본 옵션을 사용합니다. SQL 풀은 통계를 만들 때 기본적으로 테이블의 **20%** 를 샘플링합니다.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

다음은 그 예입니다.

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>모든 행을 검사하여 단일 열 통계 만들기

20%의 기본 샘플링 속도는 대부분의 상황에 충분합니다. 그러나 샘플링 비율을 조정할 수 있습니다. 전체 테이블을 샘플링하려면 이 구문을 사용합니다.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

다음은 그 예입니다.

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>샘플 크기를 지정하여 단일 열 통계 만들기

또 다른 옵션은 샘플 크기를 백분율로 지정하는 것입니다.

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>일부 행에 대해서만 단일 열 통계 만들기

테이블의 일부 행에 관한 통계, 즉 필터링된 통계를 만들 수 있습니다.

예를 들어 크게 분할된 테이블의 특정 파티션을 쿼리할 때 필터링된 통계를 사용할 수 있습니다. 파티션 값에 관해서만 통계를 만들면 통계의 정확도가 향상되므로 쿼리 성능도 향상됩니다.

이 예에서는 값의 범위에서 통계를 만듭니다. 값은 파티션의 값 범위와 일치하도록 쉽게 정의할 수 있습니다.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 분산된 쿼리 계획을 선택하는 경우 필터링된 통계 사용을 고려하는 쿼리 최적화 프로그램의 경우, 쿼리는 통계 개체의 정의 내에서 적합해야 합니다. 앞의 예제를 사용하는 경우 쿼리의 WHERE 절은 2000101과 20001231 사이의 col1 값을 지정해야 합니다.

#### <a name="create-single-column-statistics-with-all-the-options"></a>모든 옵션으로 단일 열 통계 만들기

옵션을 함께 결합할 수도 있습니다. 다음 예제에서는 사용자 지정 샘플 크기의 필터링된 통계 개체를 만듭니다.

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE 50 PERCENT;
```

전체 참조의 경우 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

#### <a name="create-multi-column-statistics"></a>여러 열 통계 만들기

여러 열 통계 개체를 만들려면 앞에 나온 예제를 사용하되 더 많은 열을 지정하면 됩니다.

> [!NOTE]
> 쿼리 결과에서 행 수를 예측하는 데 사용되는 히스토그램은 통계 개체 정의에 나열된 첫 번째 열에 대해서만 사용할 수 있습니다.

이 예에서 히스토그램은 *product\_category* 에 있습니다. 열 간 통계는 *product\_category* 및 *product\_sub_category* 에서 계산됩니다.

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE 50 PERCENT;
```

*product\_category* 와 *product<\_sub\_category* 사이에 상관 관계가 있으므로, 이러한 열을 동시에 액세스하는 경우 다중 열 통계 개체가 유용할 수 있습니다. 이 테이블을 쿼리할 때 다중 열 통계는 조인, GROUP BY 집계, 고유 개수 및 WHERE 필터에 대한 카디널리티 예상치를 향상시킵니다(기본 통계 열이 필터의 일부인 경우).

#### <a name="create-statistics-on-all-columns-in-a-table"></a>테이블의 모든 열에 대한 통계 만들기

통계를 만드는 한 가지 방법은 테이블을 만든 후 CREATE STATISTICS 명령을 실행하는 것입니다.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>저장된 프로시저를 사용하여 데이터베이스의 모든 열에서 통계를 만듭니다.

SQL 풀에는 SQL Server의 sp_create_stats에 해당하는 시스템 저장 프로시저가 없습니다. 이 저장된 프로시저는 아직 통계가 없는 데이터베이스의 모든 열에 단일 열 통계 개체를 만듭니다.

다음 예제는 데이터베이스 설계를 시작하는 데 도움이 됩니다. 사용자의 요구에 맞게 자유롭게 적용합니다.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

기본값을 사용하여 테이블의 모든 열에 대해 통계를 만들려면 저장 프로시저를 실행합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

전체 검색을 사용하여 테이블의 모든 열에서 통계를 만들려면 다음 프로시저를 호출합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

테이블의 모든 열에서 샘플링된 통계를 만들려면 3을 입력하고 샘플 퍼센트를 입력합니다. 아래 프로시저에서는 20%의 샘플 속도를 사용합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>예제: 통계 업데이트

통계를 업데이트하려면 다음을 수행할 수 있습니다.

- 하나의 통계 개체를 업데이트합니다. 업데이트하려는 통계 개체의 이름을 지정합니다.
- 테이블에 있는 모든 통계 개체를 업데이트합니다. 하나의 특정 통계 개체 대신 테이블의 이름을 지정합니다.

#### <a name="update-one-specific-statistics-object"></a>하나의 통계 개체 업데이트

특정 통계 개체를 업데이트하려면 다음 구문을 사용합니다.

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

다음은 그 예입니다.

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

특정 통계 개체를 업데이트하여 통계를 관리하는 데 필요한 리소스와 시간을 최소화할 수 있습니다. 이 작업에서는 업데이트할 최상의 통계 개체를 신중히 선택해야 합니다.

#### <a name="update-all-statistics-on-a-table"></a>테이블에 대한 모든 통계 업데이트

다음은 테이블의 모든 통계 개체를 업데이트하는 간단한 방법을 보여 줍니다.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

다음은 그 예입니다.

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS 문은 쉽게 사용할 수 있습니다. 테이블에 대한 모든 통계를 업데이트하므로 필요한 것보다 더 많은 작업을 수행할 수 있다는 사실만 기억하세요. 

성능이 문제가 되지 않는다면 통계가 최신 상태임을 보증하는 가장 쉽고 완벽한 방법입니다.

> [!NOTE]
> 테이블에 대한 모든 통계를 업데이트하는 경우 전용 SQL 풀은 각 통계 개체에 대한 테이블을 검사하여 샘플링합니다. 테이블이 크고 많은 열과 통계가 있는 경우 필요에 따라 개별 통계를 업데이트하는 것이 더 효율적일 수 있습니다.

`UPDATE STATISTICS` 프로시저의 구현은 [임시 테이블](develop-tables-temporary.md)을 참조하세요. 구현 방법은 앞의 `CREATE STATISTICS` 프로시저와 약간 다르지만 그 결과는 동일합니다.
전체 구문은 [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

### <a name="statistics-metadata"></a>통계 메타데이터

통계에 대한 정보를 찾는 데 사용할 수 있는 몇 가지 시스템 뷰 및 함수가 있습니다. 예를 들어 STATS_DATE() 함수를 사용하여 통계 개체가 오래되었는지 확인할 수 있습니다. STATS_DATE()를 사용하면 통계가 마지막으로 만들어졌거나 업데이트된 시기를 확인할 수 있습니다.

#### <a name="catalog-views-for-statistics"></a>통계에 대한 카탈로그 뷰

이 시스템 뷰는 통계에 대한 정보를 제공합니다.

| 카탈로그 뷰 | Description |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |각 열에 대해 한 행입니다. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |데이터베이스의 각 개체에 대해 한 행입니다. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) |데이터베이스의 각 스키마에 대해 한 행입니다. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) |각 통계 개체에 대해 한 행입니다. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) |통계 개체의 각 열에 대해 한 행입니다. sys.columns에 다시 연결합니다. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) |각 테이블에 대해 한 행입니다(외부 테이블 포함). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) |각 데이터 유형에 대해 한 행입니다. |

#### <a name="system-functions-for-statistics"></a>통계에 대한 시스템 함수

이 시스템 함수는 통계를 작업할 때 유용합니다.

| 시스템 함수 | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) |통계 개체가 마지막으로 업데이트된 날짜입니다. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) |통계 개체에서 인식되는 값의 분포에 대한 요약 수준 및 세부 정보 |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>통계 열 및 함수를 하나의 보기로 결합

이 뷰는 통계와 관련된 열 및 STATS_DATE() 함수의 결과를 모두 제공합니다.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() 예

DBCC SHOW_STATISTICS()는 통계 개체 내에 있는 데이터를 보여줍니다. 이 데이터는 다음 세 부분으로 제공됩니다.

- 헤더
- 밀도 벡터
- 히스토그램

헤더는 통계의 메타데이터입니다. 히스토그램은 통계 개체의 첫 번째 키 열에 값의 분포를 표시합니다. 

밀도 벡터는 열 간 상관 관계를 측정합니다. 전용 SQL 풀은 통계 개체에 있는 임의의 데이터를 사용하여 카디널리티 추정치를 계산합니다.

#### <a name="show-header-density-and-histogram"></a>헤더, 밀도 및 히스토그램 표시

이 간단한 예제에서는 통계 개체의 세 부분을 모두 보여 줍니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

다음은 그 예입니다.

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>하나 이상의 DBCC SHOW_STATISTICS() 부분 표시

특정 부분을 보는 데만 관심이 있는 경우 `WITH` 절을 사용하고 보려는 부분을 지정합니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

다음은 그 예입니다.

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() 차이점

`DBCC SHOW_STATISTICS()`는 SQL Server와 비교했을 때 전용 SQL 풀에서 다음과 같이 더 엄격하게 구현됩니다.

- 문서화되지 않은 기능은 지원되지 않습니다.
- Stats_stream은 사용할 수 없습니다.
- 통계 데이터의 특정 하위 집합에 대해 결과를 조인할 수 없습니다. 예: STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS에 메시지 제거를 설정할 수 없습니다.
- 통계 이름을 대괄호로 묶을 수 없습니다.
- 열 이름을 사용하여 통계 개체를 식별할 수 없습니다.
- 사용자 지정 오류 2767은 지원되지 않습니다.


## <a name="statistics-in-serverless-sql-pool"></a>서버리스 SQL 풀의 통계

통계는 특정 데이터 세트(스토리지 경로)의 특정 열에 대해 생성됩니다.

> [!NOTE]
> LOB 열에 대한 통계는 만들 수 없습니다.

### <a name="why-use-statistics"></a>통계를 사용하는 이유

서버리스 SQL 풀에서 데이터에 대해 많이 알수록 쿼리를 빠르게 실행할 수 있습니다. 데이터에 대해 통계를 수집하는 것은 쿼리 최적화를 위해 할 수 있는 가장 중요한 작업입니다. 

서버리스 SQL 풀 쿼리 최적화 프로그램은 비용 기반 최적화 프로그램입니다. 다양한 쿼리 계획의 비용을 비교한 다음, 비용이 가장 낮은 계획을 선택합니다. 선택된 계획은 대부분의 경우 가장 빠르게 실행되는 계획입니다. 

예를 들어 최적화 프로그램이 쿼리에서 필터링하는 날짜가 하나의 행을 반환한다고 예측하는 경우에는 특정 계획을 선택할 수 있고, 선택한 날짜가 1백만 개의 행을 반환한다고 예측하는 경우에는 또 다른 계획을 선택할 수 있습니다.

### <a name="automatic-creation-of-statistics"></a>통계 자동 생성

서버리스 SQL 풀은 누락된 통계에 대한 수신 사용자 쿼리를 분석합니다. 누락된 통계가 있는 경우 쿼리 최적화 프로그램이 쿼리 조건자 또는 조인 조건의 개별 열에 대한 통계를 만들어서 쿼리 계획의 카디널리티 추정값을 개선합니다.

SELECT 문은 통계의 자동 생성을 트리거합니다.

> [!NOTE]
> Parquet 파일의 경우 통계 자동 생성이 설정되어 있습니다. CSV 파일의 경우 CSV 파일 통계의 자동 생성이 지원될 때까지 통계를 수동으로 만들어야 합니다.

통계 자동 생성은 동기식으로 수행되므로 열에 통계가 없는 경우 쿼리 성능이 약간 저하될 수 있습니다. 단일 열에 대한 통계를 만드는 데 걸리는 시간은 대상으로 지정된 파일의 크기에 따라 달라집니다.

### <a name="manual-creation-of-statistics"></a>통계 수동 생성

서버리스 SQL 풀로 통계를 수동으로 만들 수 있습니다. CSV 파일의 경우에는 CSV 파일에 대한 통계 자동 생성이 설정되어 있지 않으므로 통계를 수동으로 만들어야 합니다. 

통계를 수동으로 만드는 방법은 아래 예제를 참조하세요.

### <a name="update-statistics"></a>통계 업데이트

파일의 데이터가 변경되거나 파일이 삭제 또는 추가되면 데이터 분포가 변경되어 통계가 최신 상태가 아니게 됩니다. 이 경우 통계를 업데이트해야 합니다.

데이터가 대폭 변경된 경우에는 서버리스 SQL 풀이 자동으로 통계를 다시 생성합니다. 통계가 자동으로 생성될 때마다 데이터 세트의 현재 상태(파일 경로, 크기, 마지막 수정 날짜)도 저장됩니다.

통계가 오래되면 새로운 통계가 만들어집니다. 알고리즘은 데이터를 살펴보며 데이터 세트의 현재 상태와 비교합니다. 변경의 규모가 지정된 임계값보다 큰 경우 기존 통계가 삭제되고 새로운 데이터 세트에 대해 다시 만들어집니다.

수동 통계는 어떤 경우에도 오래된 것으로 선언되지 않습니다.

> [!NOTE]
> Parquet 파일의 경우 통계 자동 재생성이 설정되어 있습니다. CSV 파일의 경우 CSV 파일 통계의 자동 생성이 지원될 때까지 통계를 삭제한 후 수동으로 만들어야 합니다. 통계를 삭제하고 만드는 방법은 아래 예제를 참조하세요.

쿼리 문제를 해결할 때 가장 먼저 묻는 질문 중 하나는 **"통계가 최신 상태입니까?"** 입니다.

행 수가 크게 변경되었거나 열에 대한 값 분포에 중대한 변경이 발생했다면 바로 이 시점에 통계를 업데이트해야 합니다.

> [!NOTE]
> 열에 대한 값 분포에 중대한 변경이 있는 경우 데이터가 마지막으로 업데이트된 시간과 관계없이 통계를 업데이트해야 합니다.

### <a name="implement-statistics-management"></a>통계 관리 구현

파일의 추가, 삭제 또는 변경을 통해 데이터가 크게 변경된 경우 통계가 업데이트되도록 데이터 파이프라인을 확장하는 것이 좋습니다.

통계를 업데이트하기 위해 제공되는 지침 원칙은 다음과 같습니다.

- 데이터 세트에 하나 이상의 업데이트된 통계 개체가 있는지 확인합니다. 이렇게 하면 통계 업데이트의 일부로 크기(행 수 및 페이지 수) 정보가 업데이트됩니다.
- WHERE, JOIN, GROUP BY, ORDER BY 및 DISTINCT 절에 참여하는 열에 중점을 둡니다.
- 이러한 값은 통계 히스토그램에 포함되지 않으므로 트랜잭션 날짜와 같은 “오름차순 키” 열을 더 자주 업데이트하고
- 통계 분포 열은 덜 자주 업데이트하세요.

자세한 내용은 [카디널리티 예측](/sql/relational-databases/performance/cardinality-estimation-sql-server)을 참조합니다.

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>예제: OPENROWSET 경로에 있는 열에 대해 통계 만들기

다음 예제에서는 다양한 옵션을 사용하여 통계를 만드는 방법을 보여 줍니다. 각 열에 대해 사용하는 옵션은 데이터의 특징 및 열이 쿼리에서 사용되는 방법에 따라 다릅니다.

> [!NOTE]
> 지금은 단일 열 통계만 만들 수 있습니다.
>
> sp_create_openrowset_statistics와 sp_drop_openrowset_statistics를 실행하려면 ADMINISTER BULK OPERATIONS 혹은 ADMINISTER DATABASE BULK OPERATIONS 두 가지 권한이 필요합니다.

다음 저장 프로시저는 통계를 만드는 데 사용됩니다.

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

인수: [ @stmt = ] N'statement_text' - 통계에 사용될 열 값을 반환하는 Transact-SQL 문을 지정합니다. TABLESAMPLE을 사용하여 사용될 데이터 샘플을 지정할 수 있습니다. TABLESAMPLE이 지정되지 않은 경우 FULLSCAN이 사용됩니다.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> 지금은 CSV 샘플링은 사용할 수 없으며, CSV에서는 FULLSCAN만 지원됩니다.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>모든 행을 검사하여 단일 열 통계 만들기

열에 대해 통계를 만들려면 통계가 필요한 열을 반환하는 쿼리를 제공합니다.

기본적으로 달리 지정하지 않는 한 서버리스 SQL 풀은 통계를 만들 때 데이터 집합에 제공된 데이터를 100% 사용합니다.

예를 들어 다음은 population.csv 파일을 기반으로 데이터 세트의 연도 열에 대해 기본 옵션(FULLSCAN)을 사용하여 통계를 만듭니다.

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>샘플 크기를 지정하여 단일 열 통계 만들기

샘플 크기를 백분율로 지정할 수도 있습니다.

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>예제: 통계 업데이트

통계를 업데이트하려면 통계를 삭제한 후 만들어야 합니다. 다음 저장 프로시저는 통계를 삭제하는 데 사용됩니다.

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> sp_create_openrowset_statistics와 sp_drop_openrowset_statistics를 실행하려면 ADMINISTER BULK OPERATIONS 혹은 ADMINISTER DATABASE BULK OPERATIONS 두 가지 권한이 필요합니다.

인수: [ @stmt = ] N'statement_text' - 통계를 만들 때 사용된 것과 통일한 Transact-SQL 문을 지정합니다.

population.csv 파일을 기반으로 데이터 세트의 연도 열에 대해 통계를 업데이트하려면 통계를 삭제한 후에 만들어야 합니다.

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>예제: 외부 테이블 열에 대해 통계 만들기

다음 예제에서는 다양한 옵션을 사용하여 통계를 만드는 방법을 보여 줍니다. 각 열에 대해 사용하는 옵션은 데이터의 특징 및 열이 쿼리에서 사용되는 방법에 따라 다릅니다.

> [!NOTE]
> 지금은 단일 열 통계만 만들 수 있습니다.

열에 대해 통계를 만들려면 통계 개체의 이름과 열 이름을 지정합니다.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

인수: external_table 통계를 만들 외부 테이블을 지정합니다.

FULLSCAN은 모든 행을 검사하여 통계를 계산합니다. FULLSCAN과 SAMPLE 100 PERCENT의 결과는 같습니다. FULLSCAN은 SAMPLE 옵션과 함께 사용할 수 없습니다.

SAMPLE number PERCENT는 쿼리 최적화 프로그램에서 통계를 만들 때 사용할 테이블이나 인덱싱된 뷰에 있는 행의 비율이나 개수를 대략적으로 지정합니다. number는 0~100 사이일 수 있습니다.

SAMPLE은 FULLSCAN 옵션과 함께 사용할 수 없습니다.

> [!NOTE]
> 지금은 CSV 샘플링은 사용할 수 없으며, CSV에서는 FULLSCAN만 지원됩니다.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>모든 행을 검사하여 단일 열 통계 만들기

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>샘플 크기를 지정하여 단일 열 통계 만들기

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>예제: 통계 업데이트

통계를 업데이트하려면 통계를 삭제한 후 만들어야 합니다. 먼저 통계를 삭제합니다.

```sql
DROP STATISTICS census_external_table.sState
```

그런 다음 통계를 만듭니다.

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>통계 메타데이터

통계에 대한 정보를 찾는 데 사용할 수 있는 몇 가지 시스템 뷰 및 함수가 있습니다. 예를 들어 STATS_DATE() 함수를 사용하여 통계 개체가 오래되었는지 확인할 수 있습니다. STATS_DATE()를 사용하면 통계가 마지막으로 만들어졌거나 업데이트된 시기를 확인할 수 있습니다.

> [!NOTE]
> 통계 메타데이터는 외부 테이블 열에만 사용할 수 있습니다. OPENROWSET 열에는 통계 메타데이터를 사용할 수 없습니다.

#### <a name="catalog-views-for-statistics"></a>통계에 대한 카탈로그 뷰

이 시스템 뷰는 통계에 대한 정보를 제공합니다.

| 카탈로그 뷰                                                 | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 각 열에 대해 한 행입니다.                                     |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 데이터베이스의 각 개체에 대해 한 행입니다.                     |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 데이터베이스의 각 스키마에 대해 한 행입니다.                     |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 각 통계 개체에 대해 한 행입니다.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 통계 개체의 각 열에 대해 한 행입니다. sys.columns에 다시 연결합니다. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 각 테이블에 대해 한 행입니다(외부 테이블 포함).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 각 데이터 유형에 대해 한 행입니다.                                  |

#### <a name="system-functions-for-statistics"></a>통계에 대한 시스템 함수

이 시스템 함수는 통계를 작업할 때 유용합니다.

| 시스템 함수                                              | Description                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 통계 개체가 마지막으로 업데이트된 날짜입니다. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>통계 열 및 함수를 하나의 보기로 결합

이 뷰는 통계와 관련된 열 및 STATS_DATE() 함수의 결과를 모두 제공합니다.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>다음 단계

전용 SQL 풀의 쿼리 성능을 더욱 향상시키려면 [사용자 워크로드 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context) 및 [전용 SQL 풀에 대한 모범 사례](best-practices-dedicated-sql-pool.md#maintain-statistics)를 참조하세요.

서버리스 SQL 풀의 쿼리 성능을 더욱 향상시키려면 [서버리스 SQL 풀의 모범 사례](best-practices-serverless-sql-pool.md)를 참조하세요.
