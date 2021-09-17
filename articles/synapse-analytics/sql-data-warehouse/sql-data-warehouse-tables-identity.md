---
title: IDENTITY를 사용하여 서로게이트 키 만들기
description: IDENTITY 속성을 사용하여 전용 SQL 풀의 테이블에 서로게이트 키를 만드는 방법에 대한 권장 사항 및 예입니다.
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f4ae68478bf1e964fe2539f25e11ed27645f7a62
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541930"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>IDENTITY를 사용하여 Azure Synapse Analytics에서 전용 SQL 풀을 통해 서로게이트 키 만들기

이 문서에는 IDENTITY 속성을 사용하여 전용 SQL 풀의 테이블에 서로게이트 키를 만드는 방법에 대한 권장 사항 및 예가 나와 있습니다.

## <a name="what-is-a-surrogate-key"></a>서로게이트 키란?

테이블의 서로게이트 키는 각 행에 대해 고유 식별자가 있는 열입니다. 이 키는 테이블 데이터에서 생성되지 않습니다. 데이터 웨어하우스 모델을 설계하는 경우 데이터 모델러는 해당 테이블에 서로게이트 키를 만들려고 합니다. 로드 성능에 영향을 주지 않고 간단하고 효과적으로 이 목표를 달성하기 위해 IDENTITY 속성을 사용할 수 있습니다.
> [!NOTE]
> Azure Synapse Analytics에서 IDENTITY 값은 각 배포에서 고유하게 늘어나고 다른 배포의 IDENTITY 값과 겹치지 않습니다.  사용자가 “SET IDENTITY_INSERT ON”으로 중복 값을 명시적으로 삽입하거나 IDENTITY를 다시 시드하는 경우 Synapse의 IDENTITY 값이 고유하도록 보장하지 않습니다. 자세한 내용은 [CREATE TABLE(Transact-SQL) IDENTITY(속성)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true)를 참조하세요. 


## <a name="creating-a-table-with-an-identity-column"></a>IDENTITY 열이 있는 테이블 만들기

IDENTITY 속성은 로드 성능에 영향을 주지 않고 전용 SQL 풀의 모든 배포에 스케일 아웃하도록 설계되었습니다. 따라서 IDENTITY를 구현하여 이러한 목표를 달성합니다.

다음 문과 유사한 구문을 사용하여 테이블을 처음 만드는 경우 테이블이 IDENTITY 속성을 가졌다고 정의할 수 있습니다.

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

테이블을 채우는 데 `INSERT..SELECT`를 사용할 수 있습니다.

이 섹션의 나머지 부분에는 보다 완전하게 이해할 수 있도록 구현의 미묘한 차이를 강조 표시합니다.  

### <a name="allocation-of-values"></a>값 할당

IDENTITY 속성은 데이터 웨어하우스의 분산 아키텍처로 인해 서로게이트 값이 할당되는 순서를 보장하지 않습니다. IDENTITY 속성은 로드 성능에 영향을 주지 않고 전용 SQL 풀의 모든 배포에 스케일 아웃하도록 설계되었습니다. 

다음 예제는 그림입니다.

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

앞의 예제에서 두 개의 행이 배포 1에서 연결되었습니다. 첫 번째 행에는 열 `C1`의 서로게이트 값 1이 있고 두 번째 행에는 서로게이트 값 61이 있습니다. 이 값은 모두 IDENTITY 속성에 의해 생성되었습니다. 그러나 값은 인접하게 할당되지 않습니다. 이 동작은 의도된 것입니다.

### <a name="skewed-data"></a>불균형 데이터

데이터 형식 값의 범위는 배포에 균등하게 분산되어 있습니다. 배포된 테이블에 불균형 데이터가 발생한 경우 데이터 형식으로 사용할 수 있는 값의 범위는 중간에 소진될 수 있습니다. 예를 들어 모든 데이터가 단일 분포에서 끝난 경우 테이블은 데이터 형식 값의 1/6에만 효율적으로 액세스할 수 있습니다. 이러한 이유로 IDENTITY 속성은 `INT` 및 `BIGINT` 데이터 형식으로 제한됩니다.

### <a name="selectinto"></a>SELECT..INTO

다음 조건 중 하나가 true가 아닌 경우 새 테이블에서 기존 IDENTITY 열을 선택하면 새 열은 IDENTITY 속성을 상속합니다.

- SELECT 문은 조인을 포함합니다.
- UNION을 사용하여 여러 SELECT 문을 조인합니다.
- IDENTITY 열이 SELECT 목록에 한 번 이상 나열됩니다.
- IDENTITY 열이 식의 일부입니다.

위의 조건 중 만족하는 것이 있으면 열은 IDENTITY 속성을 상속하지 않고 NOT NULL로 만들어집니다.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CTAS(CREATE TABLE AS SELECT)의 경우 SELECT..INTO에서 설명한 동일한 SQL Server 동작을 따릅니다. 그러나 문의 `CREATE TABLE` 부분에 있는 열 정의에서 IDENTITY 속성을 지정할 수 없습니다. CTAS의 `SELECT` 부분에서 IDENTITY 함수를 사용할 수 없습니다. 테이블을 채우려면 `CREATE TABLE`를 사용하여 `INSERT..SELECT` 다음의 테이블을 정의해야 합니다.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>IDENTITY 열에 값을 명시적으로 삽입

전용 SQL 풀은 `SET IDENTITY_INSERT <your table> ON|OFF` 구문을 지원합니다. 이 구문을 사용하여 명시적으로 값을 IDENTITY 열에 삽입할 수 있습니다.

많은 데이터 모델러는 해당 차원에 있는 특정 행에 미리 정의된 음수 값을 사용하려고 합니다. 예를 들어 -1 또는 "알 수 없는 멤버" 행입니다.

다음 스크립트에서는 SET IDENTITY_INSERT를 사용하여 이 행을 명시적으로 추가하는 방법을 보여줍니다.

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>데이터 로드 중

IDENTITY 속성이 존재한다는 것은 데이터 로딩 코드와 몇 가지 관련이 있습니다. 이 섹션에서는 IDENTITY를 사용하여 테이블로 데이터를 로드하는 몇 가지 기본 패턴을 강조 표시합니다.

IDENTITY를 사용하여 테이블에 데이터를 로드하고 서로게이트 키를 생성하려면 테이블을 만든 다음 INSERT..SELECT 또는 INSERT..VALUES를 사용하여 로드합니다.

다음 예제에서는 기본 패턴을 강조 표시합니다.

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> 현재 IDENTITY 열이 있는 테이블에 데이터를 로드 하는 경우 `CREATE TABLE AS SELECT`를 사용할 수 없습니다.
>

데이터 로드에 대한 자세한 내용은 [전용 SQL 풀에 대한 ELT(추출, 로드 및 변환) 설계](design-elt-data-loading.md) 및 [로드 모범 사례](../sql/data-loading-best-practices.md)를 참조하세요.

## <a name="system-views"></a>시스템 뷰

[sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 카탈로그 뷰를 사용하여 IDENTITY 속성이 있는 열을 식별할 수 있습니다.

이 예제에서는 데이터베이스 스키마를 보다 잘 이해할 수 있도록 다른 시스템 카탈로그 뷰와 sys.identity_column`을 통합하는 방법을 보여줍니다.

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>제한 사항

IDENTITY 속성을 사용할 수 없는 경우:

- 열 데이터 형식이 INT 또는 BIGINT가 아닌 경우
- 열이 배포 키인 경우
- 테이블이 외부 테이블인 경우

다음 관련 함수는 전용 SQL 풀에서 지원되지 않습니다.

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="common-tasks"></a>일반 작업

이 섹션에서는 IDENTITY 열을 사용하여 작업할 때 일반적인 작업을 수행하는 데 사용할 수 있는 몇 가지 샘플 코드를 제공합니다.

열 C1은 다음과 같은 모든 작업의 IDENTITY입니다.

### <a name="find-the-highest-allocated-value-for-a-table"></a>테이블에 가장 높게 할당된 값을 확인합니다.

`MAX()` 함수를 사용하여 배포된 테이블에 할당된 가장 높은 값을 확인합니다.

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>IDENTITY 속성에 대한 초기값 및 증분을 찾습니다.

다음 쿼리를 사용하여 테이블에 대한 ID 증분 및 초기 구성 값을 검색하기 위해 카탈로그 뷰를 사용할 수 있습니다.

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>다음 단계

- [테이블 개요](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE(Transact-SQL) IDENTITY (Property)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC CHECKIDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
