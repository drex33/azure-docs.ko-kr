---
title: CREATE TABLE AS SELECT (CTAS)
description: 솔루션 개발용 Synapse SQL의 CTAS(CREATE TABLE AS SELECT) 문에 대한 설명 및 예제입니다.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: a2d752ff97e51514830acc2f53f562c071d6e11d
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288736"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

이 문서에서는 솔루션 개발용 Synapse SQL의 CTAS(CREATE TABLE AS SELECT) T-SQL 문을 설명합니다. 코드 예제도 제공합니다.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(CREATE TABLE AS SELECT) 문은 현재 제공되고 있는 가장 중요한 T-SQL 기능 중 하나로, SELECT 문의 출력을 기반으로 새 테이블을 만드는 병렬 연산입니다. CTAS는 단일 명령을 사용하여 데이터를 만들고 테이블에 삽입하는 가장 간단하고 빠른 방법입니다.

## <a name="selectinto-vs-ctas"></a>SELECT...INTO와 CTAS

CTAS는 [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 문의 한 버전으로, 보다 다양한 사용자 지정이 가능합니다.

다음은 간단한 SELECT..INTO 문의 예제입니다.

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT...INTO를 사용할 때는 작업의 일환으로 배포 방법이나 인덱스 유형을 변경할 수 없습니다. 기본 배포 유형인 ROUND_ROBIN과 기본 테이블 구조인 CLUSTERED COLUMNSTORE INDEX를 사용하여 `[dbo].[FactInternetSales_new]`를 생성합니다.

반면, CTAS를 사용할 때는 테이블 데이터 배포와 테이블 구조 유형을 모두 지정할 수 있습니다. 이전 예제를 CTAS로 변환하려면 다음을 수행합니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> CTAS 작업에서 인덱스만 변경하려고 하고 원본 테이블이 해시로 배포되는 경우 동일한 배포 열과 데이터 형식을 유지합니다. 이렇게 하면 보다 효율적으로 작업하는 동안 배포 간 데이터 이동을 방지할 수 있습니다.

## <a name="use-ctas-to-copy-a-table"></a>CTAS를 사용하여 테이블 복사

CTAS의 가장 일반적인 용도 중 하나는 DDL을 변경하기 위해 테이블의 복사본을 만드는 경우일 것입니다. 테이블을 원래는 `ROUND_ROBIN`으로 만들었는데 지금은 이를 열에 배포된 테이블로 변경하려 한다고 가정해 보겠습니다. CTAS를 사용하여 배포 열을 변경할 수 있습니다. CTAS를 사용하여 분할, 인덱싱 또는 열 형식을 변경할 수도 있습니다.

`CREATE TABLE`에서 배포 열을 지정하지 않고 `ROUND_ROBIN`의 기본 배포 유형을 사용하여 이 테이블을 만들었다고 가정해 보겠습니다.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

이제 클러스터형 Columnstore 테이블의 성능을 이용할 수 있도록 `Clustered Columnstore Index`로 이 테이블의 새 복사본을 만들려고 합니다. 또한 이 열에 대한 조인을 예상하고 `ProductKey`에 대한 조인 중에 데이터 이동을 방지하려고 하므로 `ProductKey`에 대해 이 테이블을 배포하려고 합니다. 마지막으로 이전 파티션을 삭제하여 이전 데이터를 빨리 삭제할 수 있도록 `OrderDateKey`에 대한 분할을 추가하려고 합니다. 다음은 이전 테이블을 새 테이블에 복사하는 CTAS 문입니다.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

끝으로 새 테이블에서 바꿀 테이블 이름을 변경한 다음, 이전 테이블을 삭제할 수 있습니다.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS를 사용하여 지원되지 않는 기능 해결

CTAS를 사용하면 아래에 나와 있는 여러 지원되지 않는 기능과 관련된 문제도 해결할 수 있습니다. 이 방법은 종종 유용할 수 있는데, 사용자의 코드가 규격을 갖추게 될 뿐 아니라 Synapse SQL에서 실행 속도가 빨라지는 경우가 많기 때문입니다. 이러한 성능 향상은 완전히 병렬화된 디자인의 결과입니다. 다음과 같은 시나리오가 있습니다.

* UPDATE에 대한 ANSI JOINS
* DELETE에 대한 ANSI JOIN
* MERGE 문

> [!TIP]
> “CTAS를 먼저” 생각해 보세요. 결과적으로 더 많은 데이터를 작성하게 되더라도 CTAS를 사용하여 문제를 해결하는 것이 좋습니다.

## <a name="ansi-join-replacement-for-update-statements"></a>update 문에 대한 ANSI 조인 대체

업데이트가 복잡한 경우가 있을 수 있습니다. 업데이트를 실행하면 ANSI 조인 구문을 사용하여 UPDATE 또는 DELETE를 수행하므로 두 개 이상의 테이블이 함께 조인됩니다.

이 테이블을 업데이트해야 했다고 가정해 보십시오.

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

원래 쿼리는 다음 예제와 유사했을 것입니다.

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL은 `UPDATE` 문의 `FROM` 절에서 ANSI 조인을 지원하지 않으므로 이전 예제를 수정하지 않고는 사용할 수 없습니다.

CTAS와 암시적 조인의 조합을 사용하여 이전 예제를 대체할 수 있습니다.

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>MERGE에 대한 ANSI 조인 대체 

Azure Synapse Analytics에서 [MERGE](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)(미리 보기)를 NOT MATCHED BY TARGET과 함께 사용하려면 대상이 해시로 배포된 테이블이어야 합니다.  사용자는 ANSI JOIN을 [UPDATE](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) 또는 [DELETE](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true)와 함께 사용하여 다른 테이블과 조인한 결과에 따라 대상 테이블 데이터를 수정할 수 있습니다.  다음은 예제입니다.

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>데이터 형식 및 출력의 null 허용 여부를 명시적으로 지정

코드를 마이그레이션하는 경우 이 유형의 코딩 패턴을 볼 수 있습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

직관적으로 이 코드를 CTAS로 마이그레이션하는 것이 옳을 것 같다는 생각이 들 것입니다. 그러나 여기에는 숨겨진 문제가 있습니다.

다음 코드는 동일한 결과를 산출하지 않습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

참고로 "결과" 열은 식의 데이터 형식 및 NULL 허용 여부 값을 이월합니다. 데이터 형식을 이월할 경우 주의하지 않으면 값에 미묘한 차이가 발생할 수 있습니다.

다음 예제를 실행해 보세요.

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

결과에 저장된 값이 다릅니다. 결과 열에 보관된 값이 다른 식에 사용되면 오류가 훨씬 더 심각해집니다.

![CTAS 결과 스크린샷](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

이는 데이터 마이그레이션의 경우 중요합니다. 두 번째 쿼리는 틀림없이 더 정확하지만 문제가 있습니다. 데이터가 원본 시스템과 다르며 마이그레이션에서 무결성 문제가 발생합니다. 이는 "오답"이 실제로는 정답인, 드문 경우 중 하나입니다!

두 결과 간에 차이가 나타나는 이유는 암시적 형식 캐스팅 때문입니다. 첫 번째 예제에서 테이블은 열 정의를 정의합니다. 행이 삽입되면 암시적 형식 변환이 일어납니다. 두 번째 예제에서는 식이 열의 데이터 형식을 정의하기 때문에 암시적 형식 변환이 없습니다.

또한 두 번째 예제의 열은 NULL을 허용하는 열로 정의되었지만, 첫 번째 예제의 열은 NULL을 허용하는 열로 정의되지 않았다는 점에 주목하세요. 첫 번째 예제에서 테이블이 만들어질 때 열의 NULL 허용 여부를 명시적으로 정의했습니다. 두 번째 예제에서는 NULL 허용 여부가 식에 따라서 결정되기 때문에 기본적으로 이로 인해 NULL 정의가 있는 셈입니다.

이러한 문제를 해결하려면 CTAS 문의 SELECT 부분에서 형식 변환 및 Null 허용 여부를 명시적으로 설정해야 합니다. ‘CREATE TABLE’에서는 해당 속성을 설정할 수 없습니다.
다음 예제에서는 코드를 수정하는 방법을 보여 줍니다.

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

다음 사항에 유의하세요.

* CAST 또는 CONVERT를 사용할 수 있습니다.
* NULL 허용 여부를 강제 설정하려면 COALESCE가 아닌 ISNULL을 사용합니다. 다음이 참고를 확인하세요.
* ISNULL은 맨 바깥쪽의 함수입니다.
* ISNULL의 두 번째 부분은 상수(0)입니다.

> [!NOTE]
> Null 허용 여부를 올바르게 설정하려면 COALESCE가 아닌 ISNULL을 사용해야 합니다. COALESCE는 결정 함수가 아니므로 식의 결과는 언제나 Null을 허용합니다. ISNULL은 다릅니다. ISNULL은 결정적 함수입니다. 그러므로 ISNULL 함수의 두 번째 부분이 상수이거나 리터럴이면 결과 값은 NOT NULL이 됩니다.

테이블 파티션 전환에도 계산의 무결성을 유지하는 것이 중요 합니다. 이 테이블을 팩트 테이블로 정의했다고 생각해 보세요.

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

그러나 amount 필드는 계산된 식이며 원본 데이터의 일부가 아닙니다.

분할된 데이터 세트를 만들려면 다음 코드를 사용하는 것이 좋습니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

쿼리는 아주 잘 실행될 것입니다. 문제는 파티션 전환을 시도할 때 나타납니다. 즉, 테이블 정의가 일치하지 않는 것입니다. 테이블 정의가 일치하도록 하려면 CTAS를 수정하여 열의 Null 허용 여부 특성이 유지되도록 `ISNULL` 함수를 추가합니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

형식 일관성과 CTAS에 대한 NULL 허용 여부 속성을 유지하는 것이 엔지니어링 모범 사례임을 알 수 있습니다. 계산에서 무결성을 유지하고 파티션 전환도 가능하도록 하는 것이 좋습니다.

CTAS는 Synapse SQL에서 가장 중요한 문 중 하나이므로, 완전하게 이해해야 합니다. [CTAS 설명서](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.