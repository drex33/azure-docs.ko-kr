---
title: 애플리케이션 및 데이터베이스의 성능 튜닝 지침
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에서 성능을 위해 데이터베이스 애플리케이션과 데이터베이스를 튜닝하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 07/26/2021
ms.openlocfilehash: 6446470baf321fa46eab4a68a13cc6e09fdd2e59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566946"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 성능을 위해 애플리케이션과 데이터베이스 튜닝
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 발생하는 성능 문제를 확인한 후 이 문서는 다음 작업에 도움이 되도록 작성되었습니다.

- 애플리케이션을 튜닝하고 성능을 향상시킬 수 있는 몇 가지 모범 사례를 적용합니다.
- 데이터에 대해 보다 효율적으로 작동하도록 인덱스 및 쿼리를 변경하여 데이터베이스를 튜닝합니다.

이 문서에서는 Azure SQL Database의 [Database Advisor 권장 사항](database-advisor-implement-performance-recommendations.md)과 Azure SQL Database [자동 튜닝 권장 사항](automatic-tuning-overview.md)을 이미 사용했다고 가정합니다. 또한 성능 문제 해결과 관련된 [모니터링 및 튜닝 개요](monitor-tune-overview.md) 및 관련 문서를 검토했다고 가정합니다. 또한 이 문서에서는 CPU 리소스가 없으며 데이터베이스에 더 많은 리소스를 제공하기 위해 컴퓨팅 크기 또는 서비스 계층을 늘려 해결할 수 있는 실행 관련 성능 문제가 없다고 가정합니다.

## <a name="tune-your-application"></a>애플리케이션 튜닝

기존 온-프레미스 SQL Server에서 초기 용량 계획 프로세스는 프로덕션 애플리케이션의 실행 프로세스에서 분리된 경우가 많았습니다. 하드웨어 및 제품 라이선스를 먼저 구입하고 성능 튜닝을 나중에 수행합니다. Azure SQL을 사용하는 경우 애플리케이션을 실행하고 튜닝하는 과정을 함께 사용하는 것이 좋습니다. 주문형 용량 지불 모델에서는 애플리케이션에 대해 어림짐작한 미래 성장 계획(정확하지 않은 경우가 많음)을 기준으로 과도한 프로비전을 하지 않고, 애플리케이션을 튜닝하여 현재 필요한 최소 리소스를 사용할 수 있습니다. 일부 고객은 애플리케이션을 튜닝하지 않고 하드웨어 리소스의 과도한 프로비전을 선택할 수 있습니다. 사용량이 많은 기간 중에 주요 애플리케이션을 변경하지 않으려면 이 방법이 좋은 생각일 수 있습니다. 하지만 애플리케이션을 튜닝하면 Azure SQL Database 및 Azure SQL Managed Instance에서 서비스 계층을 사용할 때 리소스 요구 사항을 최소화하고 월 청구 금액을 낮출 수 있습니다.

### <a name="application-characteristics"></a>애플리케이션의 특성

Azure SQL Database 및 Azure SQL Managed Instance 서비스 계층이 애플리케이션의 성능 안정성과 예측 가능성을 향상하도록 설계되었지만 몇 가지 모범 사례에 따라 애플리케이션을 튜닝하면 컴퓨팅 크기에서 리소스를 더욱 효율적으로 활용할 수 있습니다. 대부분의 애플리케이션이 단순히 높은 컴퓨팅 크기 또는 서비스 계층으로 전환하여 성능을 크게 향상할 수 있는 반면, 일부 애플리케이션은 더 높은 서비스 수준에서 이점을 얻으려면 추가 튜닝이 필요합니다. 성능 향상을 위해 이러한 특성을 가진 애플리케이션에 대한 추가 애플리케이션 튜닝을 고려하십시오.

- **"번잡한" 동작으로 인해 성능이 느려지는 애플리케이션**

  번잡한 애플리케이션은 네트워크 대기 시간에 민감한 과도한 데이터 액세스 작업을 만듭니다. 이러한 종류의 애플리케이션은 데이터베이스에 대한 데이터 액세스 작업 수를 줄이기 위한 수정이 필요할 수 있습니다. 예를 들어, 임시 쿼리를 일괄 처리하거나 쿼리를 저장 프로시저로 이동하는 등의 기법을 사용하여 애플리케이션 성능을 개선할 수 있습니다. 자세한 내용은 [쿼리 일괄 처리](#batch-queries)를 참조하세요.

- **전체 단일 시스템에서 지원할 수 없는 집중적인 워크로드를 가진 데이터베이스**

   가장 높은 프리미엄 컴퓨팅 크기의 리소스를 초과하는 데이터베이스는 워크로드를 확장하여 이점을 얻을 수 있습니다. 자세한 내용은 아래의 [교차-데이터베이스 분할](#cross-database-sharding) 및 [기능 분할](#functional-partitioning) 섹션을 참조하세요.

- **최적이 아닌 쿼리를 포함하는 애플리케이션**

  애플리케이션, 특히 쿼리가 적합하게 튜닝되지 않은 데이터 액세스 계층의 애플리케이션은 더 높은 컴퓨팅 크기에서 이점을 얻을 수 없습니다. 예를 들어 WHERE 절이 없거나 인덱스가 누락되거나 통계가 오래된 쿼리가 있습니다. 이러한 애플리케이션은 표준 쿼리 성능 튜닝 기술을 사용하는 것이 도움이 됩니다. 자세한 내용은 아래의 [인덱스 누락](#identifying-and-adding-missing-indexes) 및 [쿼리 튜닝 및 힌팅](#query-tuning-and-hinting) 섹션을 참조하세요.

- **최적이 아닌 데이터 액세스 디자인을 포함하는 애플리케이션**

   교착 상태와 같이 본질적인 데이터 액세스 동시성 문제가 있는 애플리케이션은 더 높은 컴퓨팅 크기에서 이점을 얻을 수 없습니다. Azure 캐싱 서비스나 다른 캐싱 기술로 클라이언트 쪽에서 데이터를 캐시하여 데이터베이스에 대한 왕복을 줄이는 것이 좋습니다. [애플리케이션 계층 캐싱](#application-tier-caching)을 참조하세요.

## <a name="tune-your-database"></a>데이터베이스 튜닝

이 섹션에서는 데이터베이스를 튜닝하여 애플리케이션에서 최고의 성능을 달성하고 최저 컴퓨팅 크기에서도 실행할 수 있는 몇 가지 기법에 대해 설명합니다. 이 기법 중 일부는 기존 SQL Server 튜닝의 모범 사례와 동일하지만 일부 기법은 Azure SQL Database 및 Azure SQL Managed Instance에만 해당합니다. 경우에 따라 데이터베이스에 사용된 리소스를 조사하고 추가 튜닝 영역을 찾으면 기존 SQL Server 기법을 확장하여 Azure SQL Database 및 Azure SQL Managed Instance에서도 사용할 수 있습니다.

### <a name="identifying-and-adding-missing-indexes"></a>누락된 인덱스 식별 및 추가

OLTP 데이터베이스 성능의 일반적인 문제는 물리적 데이터베이스 설계와 관련되어 있습니다. 데이터베이스 스키마를 (부하 또는 데이터 볼륨에서) 대규모로 테스트하지 않고 설계 및 배송하는 경우가 많습니다. 하지만 쿼리 계획의 성능이 소규모에서는 만족할 만한 수준인 경우에도 프로덕션 수준의 데이터 볼륨을 처리할 경우 크게 저하될 수 있습니다. 이 문제의 가장 일반적인 원인은 필터 또는 쿼리의 다른 한도를 충족할 수 있는 적절한 인덱스가 없기 때문입니다. 인덱스 누락으로 인해 인덱스 검색으로도 충분한 상황에서도 테이블 검색을 수행하는 경우가 많습니다.

이 예제에서는 검색이 충분한 경우 선택한 쿼리 계획에 스캔을 사용합니다.

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![인덱스가 누락된 쿼리 계획](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database 및 Azure SQL Managed Instance를 사용하여 일반적인 누락된 인덱스 조건을 찾고 수정할 수 있습니다. Azure SQL Database 및 Azure SQL Managed Instance에 기본 제공되는 DMV는 인덱스로 쿼리 실행의 예상 비용을 크게 줄일 수 있는 쿼리 컴파일을 확인합니다. 쿼리 실행 중에 데이터베이스 엔진은 각 쿼리 계획이 실행된 빈도를 추적하며, 실행 중인 쿼리 계획과 해당 인덱스가 있었던 예상 쿼리 계획 간에 예상되는 차이를 추적합니다. 이러한 DMV를 사용하여 물리적 데이터베이스 설계를 어떻게 변경해야 데이터베이스와 실제 워크로드의 전반적 워크로드 비용을 개선할 수 있을지 빠르게 추정할 수 있습니다.

이 쿼리를 사용하여 잠재적 인덱스 누락을 평가할 수 있습니다.

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

이 예제의 쿼리를 통해 얻은 권장 사항:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

쿼리를 만든 후 해당 동일한 SELECT 문에서 다른 계획(스캔 대신 검색을 사용)을 선택한 다음 계획을 더 효율적으로 실행합니다.

![수정된 인덱스가 있는 쿼리 계획](./media/performance-guidance/query_plan_corrected_indexes.png)

중요한 통찰력은 공유된 상용 시스템의 IO 용량은 전용 서버 시스템보다 제한적이라는 것입니다. 불필요한 IO를 최소화하여 서비스 계층에 대한 각 컴퓨팅 크기의 리소스에서 시스템을 최대한 활용하는 것이 좋습니다. 물리적 데이터베이스 설계를 적절히 선택할 경우 개별 쿼리의 대기 시간 및 규모 단위당 처리할 수 있는 동시 요청의 처리량을 크게 개선하고 쿼리를 충족하는 데 필요한 비용을 최소화할 수 있습니다. 누락된 인덱스 DMV에 대한 자세한 내용은 [sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql)를 참조하세요.

### <a name="query-tuning-and-hinting"></a>쿼리 튜닝 및 힌팅

Azure SQL Database 및 Azure SQL Managed Instance의 쿼리 최적화 프로그램은 기존 SQL Server 쿼리 최적화 프로그램과 유사합니다. 쿼리 튜닝과 쿼리 최적화 프로그램의 추론 모델 제한 이해에 대한 모범 사례의 대부분은 Azure SQL Database 및 Azure SQL Managed Instance에도 적용됩니다. Azure SQL Database 및 Azure SQL Managed Instance에서 쿼리를 튜닝하는 경우 집계 리소스 요구를 줄이는 추가적인 이점이 있습니다. 애플리케이션이 낮은 컴퓨팅 크기에서 실행될 수 있기 때문에 튜닝하지 않은 경우보다 더 낮은 비용으로 실행할 수 있습니다.

SQL Server에서 일반적으로 Azure SQL Database 및 Azure SQL Managed Instance에도 적용되는 예제는 쿼리 최적화 프로그램이 매개 변수를 “검색”하는 방법입니다. 컴파일하는 동안 쿼리 최적화 프로그램이 매개 변수의 현재 값을 평가하여 더 최적인 쿼리 계획을 생성할 수 있는지 여부를 결정합니다. 이 전략으로 쿼리가 알려진 매개 변수 값을 사용하지 않고 컴파일된 계획보다 훨씬 더 빨라질 수 있지만, 현재 이 전략은 SQL Server, Azure SQL Database, Azure SQL Managed Instance에서 모두 불완전하게 작동합니다. 매개 변수가 확인되지 않는 경우도 있고, 매개 변수가 확인되지만 생성된 계획이 워크로드의 전체 매개 변수 값 집합에 대해 최적이 아닌 경우도 있습니다. Microsoft는 더욱 의도적으로 지정하고 매개 변수 스니프의 기본 동작을 재정의할 수 있도록 쿼리 힌트(지침)를 포함합니다. 일반적으로 힌트를 사용하면 기본 SQL Server, Azure SQL Database 및 Azure SQL Managed Instance 동작이 특정 고객 워크로드에서 불완전한 사례를 해결할 수 있습니다.

다음 예제에서는 쿼리 프로세서가 성능 및 리소스 요구 사항에 대해 모두 최적이 아닌 계획을 생성하는 방법을 보여 줍니다. 또한 이 예제에서는 쿼리 힌트를 사용하는 경우 데이터베이스의 쿼리 실행 시간과 리소스 요구 사항을 줄일 수 있음을 보여 줍니다.

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

설정 코드는 기울어진 데이터 분포가 포함된 테이블을 만듭니다. 최적 쿼리 계획은 선택한 매개 변수에 따라 달라집니다. 아쉽게도 계획 캐싱 동작이 가장 일반적인 매개 변수 값을 기준으로 쿼리를 다시 컴파일하지 않는 경우도 있습니다. 따라서 다른 계획을 선택하는 것이 평균적으로 더 나을 수 있는 경우에도 최적이 아닌 계획이 많은 값에 대해 캐싱 및 사용될 수 있습니다. 이러한 경우 쿼리 계획은 하나의 특별 쿼리 힌트가 포함된 프로시저를 제외하고 두 개의 동일한 저장된 프로시저를 만듭니다.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

결과 원격 분석 데이터에서 결과가 구분되도록 예의 2부를 시작하기 전에 최소 10분 기다리는 것이 좋습니다.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

이 예의 각 부분은 (테스트 데이터 집합으로 사용하기에 충분한 부하를 생성하기 위해) 매개 변수가 있는 insert 문의 실행을 1000회 시도합니다. 저장된 프로시저를 실행할 경우 쿼리 프로세서는 첫 번째 컴파일 중 프로시저로 전달된 매개 변수 값을 검사합니다(매개 변수 "스니프"라고 함). 프로세서는 매개 변수 값이 다른 경우에도 결과 계획을 캐싱하고 나중의 호출에 사용합니다. 최적의 계획이 사용되지 않는 경우가 있을 수 있습니다. 쿼리가 최초로 컴파일되었을 때 구체적인 케이스보다 평균적인 케이스에 더 적합한 계획을 선택하도록 경우에 따라 최적화 프로그램을 조정해야 합니다. 이 예에서 초기 계획은 모든 행을 읽어서 매개 변수와 일치하는 각 값을 찾는 "스캔" 계획을 생성합니다.

![스캔 계획을 사용하여 쿼리 튜닝](./media/performance-guidance/query_tuning_1.png)

여기서는 값 1을 사용하여 프로시저를 실행했기 때문에 결과 계획은 값 1에 대해 최적이지만 테이블의 나머지 값에 대해서는 최적이 아닙니다. 그 결과 계획이 더 느리게 수행되고 더 많은 리소스를 사용하기 때문에 각 계획을 무작위로 선택한 경우 원하던 것과 다를 수 있습니다.

`SET STATISTICS IO`을 `ON`로 설정하고 테스트를 실행하면 이 예의 논리적 스캔 작업은 백그라운드에서 수행됩니다. 계획에 의해 1,148개의 읽기가 수행된 것을 알 수 있습니다(평균 케이스가 단 한 개의 행만 반환하려는 경우 부족함).

![논리적 스캔을 사용하여 쿼리 튜닝](./media/performance-guidance/query_tuning_2.png)

예제의 두 번째 부분은 쿼리 힌트를 사용하여 최적화 프로그램이 컴파일 프로세스 중 특정 값을 사용하도록 합니다. 이 경우 강제로 쿼리 프로세서가 매개 변수로 전달된 값을 무시하는 대신 `UNKNOWN`을 가정하게 합니다. 이는 테이블에서 평균적인 빈도가 포함된 값을 나타냅니다(기울이기 무시). 결과 계획은 더욱 빠른 검색 기반 계획으로, 예의 1부 계획보다 평균적으로 더 적은 리소스를 사용합니다.

![쿼리 힌트를 사용하여 쿼리 튜닝](./media/performance-guidance/query_tuning_3.png)

**sys.resource_stats** 테이블의 영향(테스트를 실행하는 시간부터 데이터가 테이블을 채울 때까지 지연이 발생함)을 확인할 수 있습니다. 이 예제에서 1부는 22:25:00 기간 중 실행되었으며 2부는 22:35:00에 실행되었습니다. 이전 기간에는 같은 시간에 (계획 효율성 개선으로 인해) 이후 기간보다 더 많은 리소스를 사용했습니다.

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![쿼리 튜닝 예의 결과](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> 이 예의 볼륨은 의도적으로 작게 만들었지만 최적이 아닌 매개 변수의 영향은 특히 큰 데이터베이스에서 크게 나타날 수 있습니다. 극한의 경우 그 차이는 빠른 케이스에서 몇 초, 느린 케이스에서 몇 시간이 될 수 있습니다.

**sys.resource_stats** 를 검사하여 특정 테스트의 리소스가 다른 테스트보다 리소스를 더 많이 또는 더 적게 사용했는지 확인할 수 있습니다. 데이터를 비교할 때에는 **sys.resource_stats** 뷰에서 두 테스트가 동일한 5분 기간에 겹치지 않도록 테스트 시간을 구분합니다. 이 연습의 목표는 최대 리소스를 최소화하는 것이 아니라 사용된 총 리소스 양을 최소화하는 것입니다. 일반적으로 대기 시간의 코드를 최적화할 경우 리소스 소비가 감소합니다. 애플리케이션을 변경해야 하는지, 그리고 변경 내용이 애플리케이션에서 쿼리 힌트를 사용 중인 고객 경험에 부정적 영향을 미치지 않는지 확인하십시오.

워크로드에 반복되는 쿼리 집합이 포함된 경우 데이터베이스를 호스트하는 데 필요한 최소 리소스 크기 단위가 결정되므로 선택한 계획의 최적성을 파악하고 확인하는 것이 좋은 경우가 많습니다. 유효성을 검사한 후 가끔 계획을 다시 검사하면 성능이 저하되지 않도록 하는 데 도움이 됩니다. [쿼리 힌트(Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query)에 대해 더 자세히 알아볼 수 있습니다.

### <a name="very-large-database-architectures"></a>초대형 데이터베이스 아키텍처

Azure SQL Database에서 단일 데이터베이스의 [하이퍼스케일](service-tier-hyperscale.md) 서비스 계층을 릴리스하기 전에 고객은 개별 데이터베이스의 용량 한도에 도달하는 경우가 많았습니다. 이 용량 한도는 Azure SQL Database 탄력적 풀의 풀링된 데이터베이스와 Azure SQL Managed Instance의 인스턴스 데이터베이스에 여전히 존재합니다. 다음 두 섹션에서는 하이퍼스케일 서비스 계층을 사용할 수 없을 때 Azure SQL Database 및 Azure SQL Managed Instance에서 초대형 데이터베이스에 관련된 문제를 해결하는 두 가지 옵션을 설명합니다.

### <a name="cross-database-sharding"></a>교차-데이터베이스 분할

Azure SQL Database 및 Azure SQL Managed Instance는 상용 하드웨어에서 실행되므로 기존 온-프레미스 SQL Server 설치보다 개별 데이터베이스의 용량 한도가 더 낮습니다. Azure SQL Database 및 Azure SQL Managed Instance의 개별 데이터베이스 한도 내에서는 작업을 수행할 수 없는 경우 분할 기법을 사용해 여러 데이터베이스로 데이터베이스 작업을 분산시키는 고객도 있습니다. 현재 Azure SQL Database 및 Azure SQL Managed Instance에서 분할 기법을 사용하는 대부분의 고객은 여러 데이터베이스에서 단일 규격으로 데이터를 분할합니다. 이 방식에서는 OLTP 애플리케이션이 스키마 내에서 하나의 행 또는 작은 행 그룹에만 적용되는 트랜잭션을 수행하는 경우가 많다는 점을 이해해야 합니다.

> [!NOTE]
> 이제 Azure SQL Database는 분할을 지원하기 위한 라이브러리를 제공합니다. 자세한 내용은 [Elastic Database 클라이언트 라이브러리 개요](elastic-database-client-library.md)를 참조하세요.

예를 들어 데이터베이스에 고객 이름, 주문, 주문 정보가 포함된 경우(SQL Server에 기본 제공된 기존 예제 Northwind 데이터베이스와 같이) 관련 주문 및 주문 정보가 있는 고객을 그룹화하여 이 데이터를 여러 데이터베이스로 분할할 수 있습니다. 그러면 고객 데이터를 개별 데이터베이스에 유지할 수 있습니다. 애플리케이션은 다양한 고객을 데이터베이스로 분할하여 부하를 여러 데이터베이스로 효과적으로 나눕니다. 분할을 통해 고객은 최대 데이터베이스 크기 한도에 도달하지 않을 뿐만 아니라, Azure SQL Database 및 Azure SQL Managed Instance는 개별 데이터베이스가 해당 서비스 계층 한도에 적합한 이상 다양한 컴퓨팅 크기의 한도보다 훨씬 큰 워크로드를 처리할 수 있습니다.

데이터베이스 분할을 사용해도 솔루션에 대한 집계 리소스 용량이 줄지 않지만, 여러 데이터베이스에 분담되는 매우 큰 솔루션을 지원할 때 매우 효과적입니다. 각 데이터베이스는 리소스 요구사항이 큰 매우 크고 "효과적인" 데이터베이스를 지원하기 위해 다른 컴퓨팅 크기에서 실행될 수 있습니다.

#### <a name="functional-partitioning"></a>기능 분할

사용자는 일반적으로 개별 데이터베이스 내에 여러 기능을 결합합니다. 예를 들어 애플리케이션에 매장 재고를 관리하는 논리가 포함되어 있을 경우 해당 데이터베이스에는 재고와 관련된 논리, 구매 주문서 추적, 저장된 프로시저, 그리고 월말 보고를 관리하는 인덱스가 되어 있거나 구체화된 뷰가 포함되어 있을 수 있습니다. 이 기법을 사용하면 데이터베이스에서 백업과 같은 작업을 쉽게 관리할 수 있지만 애플리케이션의 모든 기능에서 최고 부하를 처리할 수 있도록 하드웨어 규모를 늘려야 합니다.

Azure SQL Database 및 Azure SQL Managed Instance 내에서 스케일 아웃 아키텍처를 사용하는 경우 애플리케이션의 다양한 기능을 여러 데이터베이스로 분할하는 것이 좋습니다. 이 기법을 사용하면 각 애플리케이션은 독립적으로 확장됩니다. 애플리케이션 사용량이 많아지면(데이터베이스의 부하 증가) 관리자가 애플리케이션 내의 각 기능에 대해 독립적인 컴퓨팅 크기를 선택할 수 있습니다. 이 아키텍처에서 한도에 도달할 경우 여러 시스템으로 부하를 분산하여 단일 상용 시스템이 처리할 수 있는 것보다 크게 애플리케이션을 확장할 수 있습니다.

### <a name="batch-queries"></a>쿼리 일괄 처리

임시 쿼리를 대량으로 빈번하게 사용하여 데이터에 액세스하는 애플리케이션의 경우 애플리케이션 계층과 데이터베이스 계층 간 네트워크 통신에서 많은 응답 시간이 사용됩니다. 애플리케이션과 데이터베이스가 동일한 데이터 센터 내에 있는 경우에도 데이터 액세스 작업 수가 많으면 그 사이의 네트워크 대기 시간이 커질 수 있습니다. 데이터 액세스 작업의 네트워크 왕복을 줄이기 위해 임시 쿼리를 일괄 처리하거나 저장된 프로시저로 컴파일하는 옵션을 사용하는 것을 고려하십시오. 임시 쿼리를 일괄 처리할 경우 복수 쿼리를 하나의 큰 일괄 처리로 한 번에 데이터베이스에 보낼 수 있습니다. 임시 쿼리를 저장된 프로시저로 컴파일하면 일괄 처리와 동일한 결과를 얻을 수 있습니다. 저장 프로시저를 사용하면 데이터베이스에서 쿼리 계획을 캐시할 기회가 증가하므로 저장 프로시저를 다시 사용할 수 있다는 이점이 있습니다.

일부 애플리케이션은 쓰기를 많이 사용합니다. 쓰기를 일괄 처리하는 방법을 고려하여 데이터베이스에서 총 IO 부하를 줄일 수 있는 경우도 있습니다. 흔히 이렇게 하려면 저장된 프로시저 및 임시 배치 내에서 트랜잭션을 자동 커밋하는 대신 명시적 트랜잭션을 사용하기만 하면 됩니다. 사용 가능한 다양한 기법을 평가하려면 [Azure에서 데이터베이스 애플리케이션의 일괄 처리 기법](../performance-improve-use-batching.md)을 참조하세요. 자신의 고유한 워크로드를 가지고 실험하여 일괄 처리에 적합한 모델을 찾으세요. 모델의 트랜잭션 일관성 보증이 조금 다를 수 있다는 것을 이해해야 합니다. 리소스 사용을 최소화하는 올바른 워크로드를 찾으려면 일관성과 성능 사이의 올바른 조합을 찾아야 합니다.

### <a name="application-tier-caching"></a>애플리케이션 계층 캐싱

일부 데이터베이스 애플리케이션에는 읽기 작업이 많은 워크로드가 포함되어 잇습니다. 캐싱 계층을 통해 데이터베이스의 부하를 줄이고 Azure SQL Database 및 Azure SQL Managed Instance를 사용하여 데이터베이스를 지원하는 데 필요한 컴퓨팅 크기를 줄일 수 있습니다. [Azure Cache for Redis](https://azure.microsoft.com/services/cache/)를 사용하면 읽기 작업이 많은 워크로드에서 데이터를 한 번만(또는 구성 방식에 따라 애플리케이션 계층 머신당 한 번만) 읽고 해당 데이터를 데이터베이스 외부에 저장할 수 있습니다. 그러면 데이터베이스 부하(CPU 및 읽기 IO)가 감소하지만, 캐시에서 읽는 데이터가 데이터베이스에 있는 데이터와 동기화되지 않을 수 있어 트랜잭션 일관성에 영향을 미칠 수 있습니다. 많은 애플리케이션에서 비일관성이 어느 정도 허용되지만 모든 워크로드에 대해 허용되는 것은 아닙니다. 애플리케이션 계층 캐싱 전략을 구현할 경우 애플리케이션 요구 사항을 완전히 이해해야 합니다.

## <a name="get-configuration-and-design-tips"></a>구성 및 디자인 팁 가져오기

Azure SQL Database를 사용하는 경우 오픈 소스 T-SQL [스크립트](https://aka.ms/sqldbtips)를 실행하여 요청 시 데이터베이스를 분석하고 데이터베이스 성능 및 상태를 개선하기 위한 팁을 제공할 수 있습니다. 일부 팁은 모범 사례에 따라 구성 및 운영 변경을 제안하는 반면, 다른 팁은 고급 데이터베이스 엔진 기능을 사용하도록 설정하는 등 워크로드에 적합한 디자인 변경을 권장합니다.

스크립트에 대해 자세히 알아보고 시작하려면 [wiki](https://aka.ms/sqldbtipswiki) 페이지를 방문하세요.

## <a name="next-steps"></a>다음 단계

- DTU 기반 서비스 계층에 대한 자세한 내용은 [DTU 기반 구매 모델](service-tiers-dtu.md)을 참조하세요.
- vCore 기반 서비스 계층에 대한 자세한 내용은 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.
- 탄력적 풀에 대한 자세한 내용은 [Azure 탄력적 풀이란?](elastic-pool-overview.md)을 참조하세요.
- 성능 및 탄력적 풀에 대한 자세한 내용은 [탄력적 풀을 고려 하는 경우](elastic-pool-overview.md)