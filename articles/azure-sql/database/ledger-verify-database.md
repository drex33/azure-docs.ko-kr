---
title: 원장 테이블을 확인하여 변조 감지
description: 이 문서에서는 Azure SQL Database 테이블의 변조 여부를 확인하는 방법을 설명합니다.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 272138471499cbd62085301df8e518ea47a7ddc3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064457"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>원장 테이블을 확인하여 변조 감지

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure SQL Database 원장 테이블에 있는 데이터의 무결성을 확인합니다. [SQL Database를 만들 때](ledger-create-a-single-database-with-ledger-enabled.md) **자동 다이제스트 스토리지 사용** 을 선택한 경우 Azure Portal 지침을 따라 [쿼리 편집기](connect-query-portal.md)에서 데이터베이스 원장을 확인하는 데 필요한 T-SQL(Transact-SQL) 스크립트를 자동으로 생성해야 합니다. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)나 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 T-SQL 지침을 따라도 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 Azure 구독. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- [원장을 사용하도록 설정된 SQL Database에서 데이터베이스를 만듭니다](ledger-create-a-single-database-with-ledger-enabled.md).
- [업데이트 가능한 원장 테이블을 만들어 사용하거나](ledger-how-to-updatable-ledger-tables.md), [추가 전용 원장 테이블을 만들어 사용합니다](ledger-how-to-append-only-ledger-tables.md).

## <a name="run-ledger-verification-for-sql-database"></a>SQL Database를 대상으로 원장 확인 실행

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)을 열고 **모든 리소스** 를 선택한 다음, 확인할 데이터베이스를 찾습니다. SQL Database에서 해당 데이터베이스를 선택합니다.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="모든 리소스 탭을 선택한 Azure Portal을 보여 주는 스크린샷":::

1. **보안** 에서 **원장** 옵션을 선택합니다.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="보안 원장 탭을 선택한 Azure Portal을 보여 주는 스크린샷":::

1. **원장** 창에서 **</> 데이터베이스 확인** 을 선택하고, 창에 미리 채워진 텍스트에서 **복사** 아이콘을 선택합니다.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure Portal 데이터베이스 확인 단추":::

   > > [!IMPORTANT]
   > 데이터베이스 다이제스트에 대한 자동 다이제스트 스토리지를 구성하지 않고 다이제스트를 수동으로 관리하는 경우 이 스크립트를 복사하지 마세요. 6단계로 계속 진행합니다.

1. 왼쪽 메뉴에서 **쿼리 편집기** 를 엽니다.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Azure Portal 쿼리 편집기 메뉴 옵션을 보여 주는 스크린샷":::

1. 쿼리 편집기에서 3단계에서 복사한 T-SQL 스크립트를 붙여넣고 **실행** 을 선택합니다. 8단계로 계속 진행합니다.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Azure Portal 쿼리 편집기를 실행하여 데이터베이스를 확인하는 것을 보여 주는 스크린샷":::

1. 수동 다이제스트 스토리지를 사용하는 경우 쿼리 편집기에 다음 T-SQL 입력하여 최신 데이터베이스 다이제스트를 검색합니다. 다음 단계를 위해 반환된 결과에서 다이제스트를 복사합니다.

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. 다음 T-SQL에서 `<database_digest>`를 6단계에서 복사한 다이제스트로 바꾸어 쿼리 편집기에 붙여넣고 **실행** 을 선택합니다.

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. 확인하면 **결과** 창에 다음 메시지가 반환됩니다.

   - 데이터베이스에 변조가 없으면 메시지는 다음과 같습니다.

       ```output
       Ledger verification successful
       ```

   - 데이터베이스에 변조가 있는 경우 **메시지** 창에 다음 오류가 표시됩니다.
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[자동 다이제스트 스토리지를 사용하는 T SQL](#tab/t-sql-automatic)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 데이터베이스에 연결합니다.

1. 다음 T-SQL 문을 사용하여 새 쿼리를 만듭니다.

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. 쿼리 실행. **digest_locations** 가 데이터베이스 다이제스트가 저장된 현재 위치와 모든 이전 위치를 반환합니다. **Result** 는 원장 확인 성공 또는 실패를 반환합니다.

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="Azure Data Studio를 사용하여 원장 확인을 실행하는 스크린샷":::

1. **digest_locations** 결과 집합을 열고 다이제스트 위치를 확인합니다. 다음 예제에서는 이 데이터베이스에 대한 두 개의 다이제스트 스토리지 위치를 보여 줍니다. 

   - **path** 는 다이제스트의 위치를 나타냅니다.
   - **last_digest_block_id** 는 **path** 위치에 저장된 마지막 다이제스트의 블록 ID를 나타냅니다.
   - **is_current** 는 **path** 의 위치가 현재(true) 또는 이전(false)인지 나타냅니다.

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > 원장 확인을 실행할 때 **digest_locations** 의 위치를 검사하여 확인에 사용된 다이제스트가 예상 위치에서 검색되는지 확인합니다. 권한 있는 사용자가 불변성 정책 구성 및 잠금 없이, 다이제스트 스토리지의 위치를 보호되지 않는 스토리지 위치(예: Azure Storage)로 변경하지 않았는지 확인하고자 합니다.

1. 확인하면 **결과** 창에 다음 메시지가 반환됩니다.

   - 데이터베이스에 변조가 없으면 메시지는 다음과 같습니다.

       ```output
       Ledger verification successful
       ```

   - 데이터베이스에 변조가 있는 경우 **메시지** 창에 다음 오류가 표시됩니다.
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[수동 다이제스트 스토리지를 사용하는 T-SQL](#tab/t-sql-manual)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)를 사용하여 데이터베이스에 연결합니다.
1. 다음 T-SQL 문을 사용하여 새 쿼리를 만듭니다.

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. 쿼리 실행. 결과에는 최신 데이터베이스 다이제스트가 포함되며 현재 시점의 데이터베이스 해시가 표시됩니다. 다음 단계에서 사용할 결과의 내용을 복사합니다.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Azure Data Studio를 사용한 다이제스트 결과 검색을 보여 주는 스크린샷":::

1. 다음 T-SQL 문을 사용하여 새 쿼리를 만듭니다. `<YOUR DATABASE DIGEST>`를 이전 단계에서 복사한 이름으로 바꿉니다.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. 쿼리 실행. **메시지** 창에 다음 성공 메시지가 포함됩니다.

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Azure Data Studio를 사용하여 원장 확인을 위한 T-SQL 쿼리를 실행한 후의 메시지를 보여 주는 스크린샷":::

   > [!TIP]
   > 최신 다이제스트를 사용하여 원장 확인을 실행하면 다이제스트가 생성된 시점부터 확인이 실행될 때까지의 데이터베이스만 확인됩니다. 데이터베이스의 기록 데이터가 변조되지 않았는지 확인하려면 여러 데이터베이스 다이제스트 파일을 사용하여 확인을 실행합니다. 데이터베이스를 확인하려는 시점부터 시작해야 합니다. 여러 다이제스트를 전달하는 확인은 다음 쿼리와 비슷하게 보입니다.

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 원장 개요](ledger-overview.md)
- [SQL Database 원장](ledger-database-ledger.md)
- [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)
- [Azure Confidential Ledger에 저장된 다이제스트에 액세스](ledger-how-to-access-acl-digest.md)
