---
title: 업데이트 가능한 원장 테이블 만들기 및 사용
description: Azure SQL Database에서 업데이트 가능한 원장 테이블을 만들고 사용하는 방법을 알아봅니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 5f33ad5ff4620290b9c857a9c269abc564b3b385
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367021"
---
# <a name="create-and-use-updatable-ledger-tables"></a>업데이트 가능한 원장 테이블 만들기 및 사용

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure SQL Database에서 [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)을 만드는 방법을 보여줍니다. 다음으로, 업데이트 가능한 원장 테이블에 값을 삽입한 다음, 데이터를 업데이트합니다. 마지막으로 원장 보기를 사용하여 결과를 확인합니다. 은행 고객의 계좌 잔액을 추적하는 은행 애플리케이션을 예제로 사용하겠습니다. 이 예제에서는 업데이트 가능한 원장 테이블과 해당 기록 테이블 및 원장 보기 간의 관계를 실용적인 방법으로 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 원장이 활성화된 Azure SQL Database. Azure SQL Database에서 데이터베이스를 아직 만들지 않은 경우 [빠른 시작: 원장이 활성화된 Azure SQL Database에서 데이터베이스 만들기](ledger-create-a-single-database-with-ledger-enabled.md)를 참조하세요.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="create-an-updatable-ledger-table"></a>업데이트 가능한 원장 테이블 만들기

다음 스키마를 사용하여 계정 잔액 테이블을 만듭니다.

| 열 이름 | 데이터 형식      | Description                         |
| ----------- | -------------- | ----------------------------------- |
| CustomerID  | int            | 고객 ID - 클러스터형 기본 키 |
| LastName    | varchar (50)   | 고객 성                  |
| FirstName   | varchar (50)   | 고객 이름                 |
| Balance     | decimal (10,2) | 계정 잔액                     |

> [!IMPORTANT]
> 업데이트 가능한 원장 테이블을 만들려면 **ENABLE LEDGER** 권한이 필요합니다. 원장 테이블과 관련된 권한에 대한 자세한 내용은 [권한](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)을 참조하세요. 

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 `[Account].[Balance]`라는 새 스키마와 테이블을 만듭니다.

   ```sql
   CREATE SCHEMA [Account]
   GO
   
   CREATE TABLE [Account].[Balance]
   (
       [CustomerID] INT NOT NULL PRIMARY KEY CLUSTERED,
       [LastName] VARCHAR (50) NOT NULL,
       [FirstName] VARCHAR (50) NOT NULL,
       [Balance] DECIMAL (10,2) NOT NULL
   )
   WITH 
   (
    SYSTEM_VERSIONING = ON,
    LEDGER = ON
   );
   GO
   ```

    > [!NOTE]
    > SQL Database에서 데이터베이스를 만들 때 원장 데이터베이스를 사용하도록 설정한 경우 `LEDGER = ON` 인수를 지정하는 것은 선택 사항입니다.
    >
    > 앞의 예제에서 시스템은 테이블에서 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열의 이름, [원장 보기](ledger-updatable-ledger-tables.md#ledger-view)의 이름 및 [원장 보기 열](ledger-updatable-ledger-tables.md#ledger-view-schema)의 이름을 생성합니다.
    >
    > [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 문에 `<ledger_view_option>` 매개 변수를 사용하여 테이블을 만들 때 원장 보기 열 이름을 사용자 지정할 수 있습니다. `GENERATED ALWAYS` 열 및 [기록 테이블](ledger-updatable-ledger-tables.md#history-table) 이름을 사용자 지정할 수 있습니다. 자세한 내용은 [원장 보기 옵션](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) 및 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true##x-creating-a-updatable-ledger-table)의 해당 예제를 참조하세요.

1. [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)이 생성되면 해당 기록 테이블 및 원장 보기도 생성됩니다. 다음 T-SQL 명령을 실행하여 새 테이블과 새 보기를 확인합니다.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/ledger-updatable-how-to-new-tables.png" alt-text="새 원장 테이블 쿼리를 보여주는 스크린샷.":::

1. 개시 잔액이 50달러인 신규 고객으로 이름 `Nick Jones`를 삽입합니다.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (1, 'Jones', 'Nick', 50)
   ```

1. 개시 잔액이 각각 500달러, 30달러 및 200달러인 신규 고객으로 `John Smith`, `Joe Smith` 및 `Mary Michaels` 이름을 삽입합니다.

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (2, 'Smith', 'John', 500),
   (3, 'Smith', 'Joe', 30),
   (4, 'Michaels', 'Mary', 200)
   ```

1. `[Account].[Balance]`라는 업데이트 가능한 원장 테이블을 보고 테이블에 추가된 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열을 지정합니다.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   ```

   결과 창에는 T-SQL 명령에 의해 삽입된 값과 데이터 계보 용도로 사용되는 시스템 메타데이터가 차례로 표시됩니다.

   - `ledger_start_transaction_id` 열은 데이터를 삽입한 트랜잭션과 연결된 고유한 트랜잭션 ID를 기록합니다. `John`, `Joe` 및 `Mary`는 동일한 트랜잭션을 사용하여 삽입되었으므로 동일한 트랜잭션 ID를 공유합니다.
   - `ledger_start_sequence_number` 열은 트랜잭션에 의해 값이 삽입된 순서를 기록합니다.

      :::image type="content" source="media/ledger/sql-updatable-how-to-1.png" alt-text="원장 테이블 예제 1을 보여주는 스크린샷.":::

1. `Nick`의 잔액을 `50`에서 `100`으로 업데이트합니다.

   ```sql
   UPDATE [Account].[Balance] SET [Balance] = 100
   WHERE [CustomerID] = 1
   ```

1. 기록 테이블의 고유한 이름을 복사합니다. 이 정보는 다음 단계에서 필요합니다.

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/sql-updatable-how-to-2.png" alt-text="원장 테이블 예제 2를 보여주는 스크린샷.":::

1. `[Account].[Balance]`라는 업데이트 가능한 원장 테이블과 해당 기록 테이블 및 원장 보기를 봅니다.

   > [!IMPORTANT]
   > `<history_table_name>`을 이전 단계에서 복사한 이름으로 바꿉니다.

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   GO
   
   SELECT * FROM [<Your unique history table name>]
   GO 
   
   SELECT * FROM Account.Balance_Ledger
   ORDER BY ledger_transaction_id
   GO
   ```

   > [!TIP]
   > 기록 테이블이 아니라 [원장 보기](ledger-updatable-ledger-tables.md#ledger-view)를 통해 [변경 기록](ledger-updatable-ledger-tables.md#history-table)을 쿼리하는 것이 좋습니다.

1. 업데이트 가능한 원장 테이블에서 `Nick`의 계좌 잔액이 `100`으로 업데이트되었습니다.
1. 이제 기록 테이블에서 `Nick`의 이전 잔액 `50`을 보여 줍니다.
1. 원장 보기는 원장 테이블을 업데이트하는 것이 `DELETE`를 포함하는 원래 행의 `50`임을 보여줍니다. `100`이 있는 새 행의 해당 `INSERT`가 있는 잔액은 `Nick`에 대한 새 잔액을 보여줍니다.

   :::image type="content" source="media/ledger/sql-updatable-how-to-3.png" alt-text="원장 테이블 예제 3을 보여주는 스크린샷.":::


## <a name="next-steps"></a>다음 단계

- [데이터베이스 원장](ledger-database-ledger.md)
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md) 
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md) 
- [추가 전용 원장 테이블 만들기 및 사용](ledger-how-to-append-only-ledger-tables.md) 
- [ACL(Azure Confidential Ledger)에 저장된 다이제스트에 액세스](ledger-how-to-access-acl-digest.md)
- [원장 테이블을 확인하여 변조 감지](ledger-verify-database.md)
