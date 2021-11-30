---
title: 저장 프로시저를 사용합니다.
description: 솔루션 개발을 위한 Azure Synapse Analytics의 Synapse SQL을 사용한 저장 프로시저를 구현하기 위한 팁입니다.
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: emtehran
ms.reviewer: igorstan
ms.openlocfilehash: a037d919b3e930934457766866499a0ee314b4a0
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288717"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse SQL을 사용한 저장 프로시저

Synapse SQL 프로비저닝된 서버리스 풀을 사용하면 복잡한 데이터 처리 논리를 SQL 저장 프로시저에 배치할 수 있습니다. 저장 프로시저는 SQL 코드를 캡슐화하여 데이터 웨어하우스의 데이터 가까이에 저장하는 좋은 방법입니다. 저장 프로시저는 코드를 관리 가능한 단위로 캡슐화하고 코드의 재사용 가능성을 매우 용이하게 해 개발자들이 자신들의 솔루션을 모듈화할 수 있도록 돕습니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.
본 문서에서는 개발 솔루션을 위해 Synapse SQL 풀의 저장 프로시저를 구현하는 몇 가지 팁을 확인할 수 있습니다.

## <a name="what-to-expect"></a>필요한 항목

Synapse SQL은 SQL Server에서 사용하는 T-SQL 기능 여러 가지를 지원합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다. 본 문서에서는 저장 프로시저에 배치할 수 있는 기능에 대해 알아봅니다.

> [!NOTE]
> 프로시저 본문에서는 Synapse SQL 노출 영역에서 지원되는 기능만 사용할 수 있습니다. 저장 프로시저에서 사용할 수 있는 개체와 문을 식별하려면 [본 문서](overview-features.md)를 검토하세요. 본 문서의 예제에서는 서버리스 영역과 전용 노출 영역 둘 다에서 사용할 수 있는 일반 기능을 사용합니다. [프로비저닝된 서버리스 Synapse SQL 풀에 대한 추가 제한 사항](#limitations) 은 본 문서 마지막에서 확인하세요.

SQL 풀의 규모와 성능을 유지하기 위해, 동작의 차이가 있는 일부 기능 및 지원되지 않는 일부 다른 기능도 있습니다.

## <a name="stored-procedures-in-synapse-sql"></a>Synapse SQL의 저장 프로시저

다음 예제에서는 외부 개체가 데이터베이스에 존재할 경우 이를 삭제하는 프로시저를 확인할 수 있습니다.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

위 프로시저는 프로시저 이름과 매개 변수를 지정할 수 있는`EXEC` 문을 사용하여 실행할 수 있습니다.

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL은 단순하고 효율적인 저장 프로시저 구현을 제공합니다. SQL Server와 비교하여 가장 큰 차이점은 저장된 프로시저가 미리 컴파일된 코드가 아닙니다. 데이터 웨어하우스에서 컴파일 시간은 대용량 데이터에 대해 쿼리를 실행하는 데 걸리는 시간에 비해 작습니다. 저장 프로시저 코드가 대형 쿼리에 적절하게 최적화되었는지 확인하는 것은 더욱 중요합니다. 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 그러므로 저장된 프로시저를 SQL 논리에 대한 컨테이너로 생각하는 것이 더욱 도움이 됩니다.

Synapse SQL이 저장 프로시저를 실행할 때 SQL 문은 구문 분석되고 변환되고 런타임 시 최적화됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="encapsulate-validation-rules"></a>유효성 검사 규칙 캡슐화

저장 프로시저를 사용하면 SQL 데이터베이스에 저장된 단일 모듈의 유효성 검사 논리를 찾을 수 있습니다. 다음 예제에서는 매개 변수 값의 유효성을 검사하고 그 기본 값을 변경하는 방법을 확인할 수 있습니다.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

SQL 프로시저의 논리는 해당 프로시저를 호출할 때 입력 매개 변수의 유효성을 검사합니다.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저

저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.
중첩 프로시저의 예제 하나가 다음 코드에 나와 있습니다.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

해당 프로시저는 일부 이름을 나타내는 매개 변수를 받아들인 다음, 같은 이름의 개체를 삭제하는 다른 프로시저를 호출합니다.
Synapse SQL 풀은 최대 여덟 개의 중첩 수준을 지원합니다. 이 기능은 SQL Server와는 약간 다릅니다. SQL Server의 중첩 수준은 32입니다.

상위 수준 저장 프로시저 호출은 중첩 수준 1과 같습니다.

```sql
EXEC clean_up 'mytest'
```

또한 저장 프로시저가 다른 EXEC 호출을 만드는 경우 중첩 수준이 2로 증가합니다.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

그 다음 두 번째 프로시저가 일부 동적 SQL을 실행하면 중첩 수준이 3으로 증가합니다.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> 현재, Synapse SQL에서는 [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 지원하지 않습니다. 중첩 수준을 추적해야 합니다. 8개의 중첩 수준을 초과할 가능성은 없지만 초과하는 경우 이 한도 내에서 중첩 수준에 맞도록 코드를 다시 작업해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE

프로비저닝된 Synapse SQL 풀은 INSERT 문을 사용한 저장 프로시저의 결과 집합을 사용하도록 허용하지 않습니다. 이를 대체하여 사용할 수 있는 방법이 있습니다. 예를 들어 프로비저닝된 Synapse SQL 풀에 대한 [임시 테이블](develop-tables-temporary.md) 관련 문서를 참조하세요.

## <a name="limitations"></a>제한 사항

Transact-SQL 저장 프로시저의 측면 중 Synapse SQL에서는 구현되지 않는, 다음과 같은 것들이 있습니다.

| 기능/옵션 | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- |
| 임시 저장 프로시저 | 예 | 예 |
| 번호가 매겨진 저장 프로시저 | 예 | 예 |
| 확장된 저장 프로시저 | 예 | 예 |
| CLR 저장 프로시저 | 예 | 예 |
| 암호화 옵션 | 예 | 예 |
| 복제 옵션 | 예 | 예 |
| 테이블 반환 매개 변수 | 예 | 예 |
| 읽기 전용 매개 변수 | 예 | 예 |
| 기본 매개 변수 | 예 | 예 |
| 실행 컨텍스트 | 예 | 예 |
| Return 문 | 예 | 예 |
| INSERT INTO EXEC | 예 | 예 |

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.
