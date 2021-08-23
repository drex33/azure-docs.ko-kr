---
title: 데이터 보존 정책 사용 및 사용하지 않음 - Azure SQL Edge
description: Azure SQL Edge에서 데이터 보존 정책을 사용 및 사용하지 않음으로 설정하는 방법을 알아봅니다.
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 2da85385057b470dc933532854998839bdeb6d70
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567381"
---
# <a name="enable-and-disable-data-retention-policies"></a>데이터 보존 정책 사용 및 사용하지 않음

이 항목에서는 데이터베이스 및 테이블에서 데이터 보존 정책을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다.

## <a name="enable-data-retention-for-a-database"></a>데이터베이스에 데이터 보존 사용 설정

다음 예시에서는 [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options)를 사용하여 데이터 보존 정책을 사용하도록 설정하는 방법을 보여 줍니다.

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>데이터베이스에 데이터 보존이 사용되도록 설정되었는지 확인

다음 명령을 사용하여 데이터베이스에 데이터 보존이 사용되는지 여부를 확인할 수 있습니다.
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>데이터베이스에 데이터 보존 사용 설정

데이터를 자동으로 제거하려는 각 테이블에 대해 데이터 보존을 사용하도록 설정해야 합니다. 데이터베이스와 테이블에 데이터 보존이 사용하도록 설정된 경우, 백그라운드 시스템 작업에서 주기적으로 테이블을 검사하여 사용되지 않는(오래된) 행을 식별하고 삭제합니다. [테이블 만들기](/sql/t-sql/statements/create-table-transact-sql) 또는 [Alter Table](/sql/t-sql/statements/alter-table-transact-sql)을 사용하여 테이블을 만드는 동안 테이블에서 데이터 보존을 사용하도록 설정할 수 있습니다.

다음 예시에서는 [테이블 만들기](/sql/t-sql/statements/create-table-transact-sql)를 사용하여 데이터 보존 정책을 사용하도록 설정하는 방법을 보여 줍니다.

```sql
CREATE TABLE [dbo].[data_retention_table]
(
[dbdatetime2] datetime2(7),
[product_code] int,
[value] char(10),
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

테이블 만들기 명령의 `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` 부분은 테이블에 데이터 보존을 설정합니다. 명령은 다음 필수 매개 변수를 사용합니다.

- DATA_DELETION - 데이터 보존이 설정되었는지 여부를 나타냅니다.
- FILTER_COLUMN - 해당 행이 사용되지 않는지 여부를 확인하는 데 사용되는 테이블 열의 이름입니다. 필터 열은 다음 데이터 형식의 열만 될 수 있습니다.
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD - 정수 뒤에 단위 설명자가 있습니다. 허용되는 단위는 일, 일들, 주, 주들, 월, 월들, 연 및 연들입니다.

다음 예시에서는 [Alter Table](/sql/t-sql/statements/alter-table-transact-sql)을 사용하여 데이터 보존 정책을 사용하도록 설정하는 방법을 보여 줍니다.

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>테이블에 데이터 보존이 사용되도록 설정되었는지 확인

다음 명령을 사용하여 데이터 보존이 사용되도록 설정된 테이블을 검사할 수 있습니다.

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

data_retention_period = -1 and data_retention_period_unit as INFINITE의 값은 테이블에 데이터 보존이 설정되어 있지 않음을 나타냅니다.

다음 쿼리를 사용하여 데이터 보존에 대한 filter_column으로 사용되는 열을 식별할 수 있습니다.

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>DB 및 테이블 데이터 보존 설정의 상관 관계

데이터베이스 및 테이블에 대한 데이터 보존 설정은 테이블에서 오래된 행의 자동 정리가 실행되는지 여부를 결정하는 데 함께 사용됩니다.

|데이터베이스 옵션 | 테이블 옵션 | 동작 |
|----------------|--------------|----------|
| OFF | OFF | 데이터 보존 정책을 사용하지 않도록 설정되고, 오래된 레코드의 자동 및 수동 정리를 모두 사용하지 않도록 설정됩니다.|
| OFF | 켜기  | 테이블에 대해 데이터 보존 정책을 사용하도록 설정됩니다. 사용되지 않는 레코드의 자동 정리가 사용되지 않도록 설정되었지만, 수동 정리 메서드를 사용하여 사용되지 않는 레코드를 정리할 수 있습니다. |
| 켜기 | OFF | 데이터베이스 수준에서 데이터 보존 정책을 사용하도록 설정합니다. 그러나 이 옵션은 테이블 수준에서 사용하지 않도록 설정되어 있으므로 오래된 행의 보존 기반 정리가 없습니다.|
| 켜기 | 켜기 | 데이터베이스와 테이블 모두에 대해 데이터 보존 정책을 사용하도록 설정합니다. 사용되지 않는 레코드의 자동 정리가 사용되도록 설정됩니다. |

## <a name="disable-data-retention-on-a-table"></a>테이블에서 데이터 보존 사용하지 않음

[Alter Table](/sql/t-sql/statements/alter-table-transact-sql)을 사용하여 테이블에서 데이터 보존을 사용하지 않도록 설정할 수 있습니다. 다음 명령을 사용하여 테이블에 데이터 보존을 사용하지 않도록 설정할 수 있습니다.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>데이터베이스에서 데이터 보존 사용하지 않음

[Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options)를 사용하여 테이블에 데이터 보존을 사용하지 않도록 설정할 수 있습니다. 다음 명령을 사용하여 테이블에 데이터 보존을 사용하지 않도록 설정할 수 있습니다.

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>다음 단계
- [데이터 보존 및 자동 데이터 제거](data-retention-overview.md)
- [보존 정책을 사용하여 과거 데이터 관리](data-retention-cleanup.md)
