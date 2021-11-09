---
title: 추가 전용 원장 테이블 만들기 및 사용
description: Azure SQL Database에서 추가 전용 원장 테이블을 만들고 사용하는 방법을 알아봅니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: 09b9d4dceabff4b26c586b0035f4c8b8d4c2ba0a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064503"
---
# <a name="create-and-use-append-only-ledger-tables"></a>추가 전용 원장 테이블 만들기 및 사용

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure SQL Database에서 [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)을 만드는 방법을 보여줍니다. 다음으로, 추가 전용 원장 테이블에 값을 삽입한 다음, 데이터를 업데이트하려고 합니다. 마지막으로 원장 보기를 사용하여 결과를 확인합니다. 추가 전용 시스템 패턴인 시설에 대한 카드 키 액세스 시스템의 예를 사용합니다. 이 예에서는 추가 전용 원장 테이블과 해당 원장 뷰 간의 관계를 실용적인 방법으로 보여 줍니다.

자세한 내용은 [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 원장이 활성화된 Azure SQL Database. Azure SQL Database에서 데이터베이스를 아직 만들지 않은 경우 [빠른 시작: 원장이 활성화된 Azure SQL Database에서 데이터베이스 만들기](ledger-create-a-single-database-with-ledger-enabled.md)를 참조하세요.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="create-an-append-only-ledger-table"></a>추가 전용 원장 테이블 만들기

다음 스키마로 `KeyCardEvents` 테이블을 만듭니다.

| 열 이름 | 데이터 형식 | Description |
|--|--|--|
| EmployeeID | int | 건물에 액세스하는 직원의 고유 ID |
| AccessOperationDescription | nvarchar(MAX) | 직원의 액세스 작업 |
| 타임스탬프 | datetime2 | 직원이 건물에 액세스한 날짜 및 시간입니다. |

> [!IMPORTANT]
> 추가 전용 원장 테이블을 만들려면 **ENABLE LEDGER** 권한이 필요합니다. 원장 테이블과 관련된 권한에 대한 자세한 내용은 [권한](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)을 참조하세요. 

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 `[AccessControl].[KeyCardEvents]`라는 새 스키마와 테이블을 만듭니다.

   ```sql
   CREATE SCHEMA [AccessControl] 
   CREATE TABLE [AccessControl].[KeyCardEvents]
       (
           [EmployeeID] INT NOT NULL,
           [AccessOperationDescription] NVARCHAR (MAX) NOT NULL,
           [Timestamp] Datetime2 NOT NULL
       )
       WITH (
          LEDGER = ON (
                       APPEND_ONLY = ON
                       )
         );
   ```

1. 다음 값을 사용하여 새 건물 액세스 이벤트를 `[AccessControl].[KeyCardEvents]` 테이블에 추가합니다.

   ```sql
   INSERT INTO [AccessControl].[KeyCardEvents]
   VALUES ('43869', 'Building42', '2020-05-02T19:58:47.1234567')
   ```

1. KeyCardEvents 테이블의 콘텐츠를 보고 [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)에 추가된 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열을 지정합니다.

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="KeyCardEvents 테이블을 쿼리한 결과를 보여주는 스크린샷.":::

1. `EmployeeID`를 `43869`에서 `34184.`로 변경하여 `KeyCardEvents` 테이블을 업데이트합니다.

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   추가 전용 원장 테이블에 대한 업데이트가 허용되지 않는다는 오류 메시지가 표시됩니다.

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="추가 전용 오류 메시지를 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [데이터베이스 원장](ledger-database-ledger.md) 
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md) 
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)
- [업데이트할 수 있는 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md)
- [ACL(Azure Confidential Ledger)에 저장된 다이제스트에 액세스](ledger-how-to-access-acl-digest.md)
- [원장 테이블을 확인하여 변조 감지](ledger-verify-database.md)
