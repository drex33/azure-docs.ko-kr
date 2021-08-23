---
title: 트랜잭션 사용
description: 솔루션 개발을 위해 Azure Synapse Analytics에서 전용 SQL 풀로 트랜잭션을 구현하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 44e4b35d7e67e566b3ccef61cf5fa22077e844c7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537367"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀로 트랜잭션 사용

솔루션 개발을 위해 Azure Synapse Analytics에서 전용 SQL 풀로 트랜잭션을 구현하기 위한 팁입니다.

## <a name="what-to-expect"></a>필요한 항목

예상한 것처럼 전용 SQL 풀은 데이터 웨어하우스 워크로드의 일부로 트랜잭션을 지원합니다. 그러나 전용 SQL 풀의 성능은 SQL Server와 비교할 때 일부 기능이 제한되는 수준으로 유지됩니다. 이 문서는 차이점을 강조 표시하고 다른 부분에 대해 설명합니다.

## <a name="transaction-isolation-levels"></a>트랜잭션 격리 수준

전용 SQL 풀은 ACID 트랜잭션을 구현합니다. 트랜잭션 지원의 격리 수준은 기본적으로 READ UNCOMMITTED로 설정되어 있습니다.  master 데이터베이스에 연결된 경우 사용자 데이터베이스의 READ_COMMITTED_SNAPSHOT 데이터베이스 옵션을 ON으로 설정하여 이 기본값을 READ COMMITTED SNAPSHOT ISOLATION으로 변경할 수 있습니다.  

활성화되면 이 데이터베이스의 모든 트랜잭션이 READ COMMITTED SNAPSHOT ISOLATION으로 실행되고 세션 수준의 READ UNCOMMITTED 설정은 적용되지 않습니다. 자세한 내용은 [ALTER DATABASE SET 옵션(Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?preserve-view=true&view=azure-sqldw-latest)을 참조하세요.

## <a name="transaction-size"></a>트랜잭션 크기
단일 데이터 수정 트랜잭션은 크기가 제한됩니다. 이러한 제한은 배포 기준으로 적용됩니다. 따라서 총 할당량은 한도에 배포 수를 곱하여 계산할 수 있습니다. 

트랜잭션에 포함된 대략적인 최대 행 수를 구하려면 배포 용량을 각 행의 전체 크기로 나눕니다. 가변 길이 열의 경우에는 최대 크기를 사용하는 대신, 평균 열 길이를 사용하는 것을 고려합니다.

아래 표에서는 다음과 같이 가정되었습니다.

* 균일한 데이터 분포가 발생했습니다.
* 평균 행 길이는 250바이트입니다.

## <a name="gen2"></a>2세대

| [DWU](resource-consumption-models.md#data-warehouse-units) | 배포당 용량(GB) | 배포 수 | 최대 트랜잭션 크기(GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>1세대

| [DWU](resource-consumption-models.md#data-warehouse-units) | 배포당 용량(GB) | 배포 수 | 최대 트랜잭션 크기(GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5. |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

트랜잭션 또는 작업 기준으로 트랜잭션 크기 제한이 적용되며 모든 동시 트랜잭션에서 적용되지는 않습니다. 따라서 각 트랜잭션은 이 크기의 데이터를 로그에 쓰도록 허용됩니다.

로그에 기록된 데이터의 양을 최적화하고 최소화하려면 [트랜잭션 모범 사례](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context) 문서를 참조하세요.

> [!WARNING]
> 최대 트랜잭션 크기는 데이터가 균일하게 분산되는 HASH 또는 ROUND_ROBIN 분산 테이블에 대해서만 도달할 수 있습니다. 트랜잭션이 균일하지 않은 분산 방식으로 데이터를 쓰는 경우 최대 트랜잭션 크기에 도달하기 전에 제한에 도달할 가능성이 높습니다.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>트랜잭션 상태

전용 SQL 풀은 XACT_STATE() 함수를 사용하여 값 -2를 사용하는 실패한 트랜잭션을 보고합니다. 이 값은 트랜잭션이 실패하고 롤백만 표시함을 의미합니다.

> [!NOTE]
> XACT_STATE 함수에서-2 사용은 실패한 트랜잭션이 SQL Server와 다른 동작을 표시함을 나타냅니다. SQL Server는 값 -1를 사용하여 커밋할 수 없는 트랜잭션을 나타냅니다. SQL Server는 커밋할 수 없음으로 표시하지 않고 트랜잭션 내 일부 오류를 허용할 수 있습니다. 예를 들어 `SELECT 1/0`은 오류를 발생시키지만 커밋할 수 없는 상태로 트랜잭션을 강제 적용하지 않습니다. 또한 SQL Server는 커밋할 수 없는 트랜잭션에서 읽기를 허용합니다. 그러나 전용 SQL 풀로 인해 이를 수행할 수 없습니다. 전용 SQL 풀의 트랜잭션 내부에서 오류가 발생하는 경우 자동으로 -2 상태가 되며, 해당 명령문이 롤백될 때까지 추가 select 문을 실행할 수 없습니다. 따라서 코드를 수정해야 할 수 있으므로 XACT_STATE()가 사용되는지 알기 위해 해당 애플리케이션 코드를 확인하는 것이 중요합니다.

예를 들어 SQL Server에서 다음과 같은 트랜잭션이 나타날 수 있습니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

위의 코드는 다음과 같은 오류 메시지를 제공합니다.

Msg 111233, Level 16, State 1, Line 1 111233; 현재 트랜잭션이 중단되었으며 보류 중인 변경 내용은 롤백되었습니다. 원인: 롤백 전용 상태의 트랜잭션은 DDL, DML 또는 SELECT 문 이전에 명시적으로 롤백되지 않았습니다.

또한 ERROR_* 함수의 출력도 제공되지 않습니다.

전용 SQL 풀에서는 코드를 약간 변경해야 합니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

이제 예상되는 동작이 진행됩니다. 트랜잭션의 오류가 관리되고 ERROR_* 함수는 예상대로 값을 제공합니다.

변경된 부분은 트랜잭션의 ROLLBACK이 CATCH 블록의 오류 정보를 읽기 전에 발생해야 한다는 것입니다.

## <a name="error_line-function"></a>Error_Line() 함수

전용 SQL 풀이 ERROR_LINE() 함수를 구현하거나 지원하지 않는다는 점도 주목할 가치가 있습니다. 코드에 이 함수가 있는 경우 이를 제거해야 전용 SQL 풀과 호환될 수 있습니다. 동등한 기능을 구현하는 대신 코드에서 쿼리 레이블을 사용합니다. 자세한 내용은 [LABEL](develop-label.md) 문서를 참조하세요.

## <a name="use-of-throw-and-raiserror"></a>THROW 및 RAISERROR 사용

THROW는 전용 SQL 풀에서 예외를 발생시키기 위한 가장 최신 구현이지만 RAISERROR도 지원됩니다. 그러나 다음 몇 가지 사항에 주의해야 합니다.

* 사용자 정의 오류 메시지 번호는 THROW에 대해 100,000 - 150,000 범위에 있을 수 없습니다.
* RAISERROR 오류 메시지는 50,000으로 고정됩니다.
* sys.messages 사용은 지원되지 않습니다.

## <a name="limitations"></a>제한 사항

전용 SQL 풀에는 트랜잭션과 관련된 몇 가지 기타 제한 사항이 있습니다. 다음과 같습니다.

* 분산된 트랜잭션이 없습니다
* 중첩된 트랜잭션이 허용되지 않습니다.
* 저장 점수를 사용할 수 없습니다.
* 명명된 트랜잭션이 없습니다.
* 표시된 트랜잭션이 없습니다.
* 사용자 정의 트랜잭션 내에 CREATE TABLE과 같은 DDL 지원은 없습니다.

## <a name="next-steps"></a>다음 단계

트랜잭션을 최적화하는 방법에 대한 자세한 내용은 [트랜잭션 모범 사례](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)를 참조하세요. [전용 SQL 풀](best-practices-dedicated-sql-pool.md) 및 [서버리스 SQL 풀](best-practices-serverless-sql-pool.md)에 대한 모범 사례 가이드도 추가로 제공됩니다.