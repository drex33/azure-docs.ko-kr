---
title: 다이제스트 관리 및 데이터베이스 확인
description: 이 문서는 Azure SQL Database의 원장 데이터베이스에 대한 다이제스트 정보 및 데이터베이스 확인 관련 정보를 제공합니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: b0c22b5fcaa2566d1c12d0b62667e86f44f44c59
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367890"
---
# <a name="digest-management-and-database-verification"></a>다이제스트 관리 및 데이터베이스 확인

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

Azure SQL Database 원장은 *전달 무결성* 이라는 데이터 무결성의 형태를 제공하며, 이는 원장 테이블의 데이터에 대한 데이터 변조 증명 정보를 제공합니다. 예를 들어, 잔액이 `x` 값으로 업데이트된 원장 테이블에서 은행 거래가 발생하며 공격자가 이후에 잔액이 `x`에서 `y`가 되도록 데이터를 수정하면 데이터베이스 확인 기능이 이 변조 활동을 감지합니다.  

데이터베이스 확인 프로세스는 이전에 생성된 데이터베이스 다이제스트 하나 이상을 입력으로 사용합니다. 그런 후 데이터베이스 원장에 저장된 해시를 원장 테이블의 현재 상태를 기준으로 다시 계산합니다. 계산된 해시가 입력 다이제스트와 일치하지 않는 경우 확인에 실패합니다. 실패는 데이터가 변조되었음을 나타냅니다. 확인 프로세스는 검색된 불일치를 모두 보고합니다.

## <a name="database-digests"></a>데이터베이스 다이제스트

데이터베이스 원장의 최신 블록 해시를 *데이터베이스 다이제스트* 라고 합니다. 이것은 블록이 생성될 때 데이터베이스에 있는 모든 원장 테이블의 상태를 나타냅니다. 데이터베이스 다이제스트를 생성하면 최근에 추가된 블록의 해시를 계산하는 작업만 포함하기 때문에 효율적입니다. 

데이터베이스 다이제스트는 시스템에서 자동으로 생성하거나 사용자가 수동으로 생성할 수 있습니다. 사용자는 나중에 데이터베이스의 데이터 무결성을 확인하는 데 이러한 다이제스트를 사용할 수 있습니다. 

데이터베이스 다이제스트는 블록 ID와 관련된 메타데이터와 함께 최신 블록의 해시가 포함된 JSON 문서 형식으로 생성됩니다. 메타데이터에는 다이제스트가 생성된 시간과 이 블록에서 이루어진 마지막 트랜잭션의 커밋 타임스탬프가 포함됩니다.

확인 프로세스와 데이터베이스의 무결성은 입력 다이제스트의 무결성에 따라 달라집니다. 이를 위해 데이터베이스에서 추출된 데이터베이스 다이제스트는 권한이 높은 사용자 또는 Azure SQL Database 서버의 공격자가 변조할 수 없고 신뢰할 수 있는 스토리지에 저장되어야 합니다.

### <a name="automatic-generation-and-storage-of-database-digests"></a>데이터베이스 다이제스트 자동 생성 및 스토리지

Azure SQL Database 원장은 [Azure Blob Storage의 변경할 수 없는 스토리지 기능](../../storage/blobs/immutable-storage-overview.md) 및 [Azure Confidential Ledger](../../confidential-ledger/index.yml)와 통합됩니다. 이러한 통합을 통해 Azure의 보안 스토리지 서비스를 활용하여 데이터베이스 다이제스트를 잠재적인 변조로부터 보호합니다. 이 통합은 가용성과 지리적 복제에 대한 걱정 없이 사용자가 다이제스트 관리를 자동화할 수 있는 간단하고 비용 효율적인 방법을 제공합니다. 

Azure Portal, PowerShell 또는 Azure CLI를 통해 데이터베이스 다이제스트의 자동 생성 및 스토리지를 구성할 수 있습니다. 자동 생성 및 스토리지를 구성하는 경우 데이터베이스 다이제스트가 미리 정의된 간격(30초)으로 생성되고 선택한 스토리지 서비스에 업로드됩니다. 시스템에서 30초 간격으로 트랜잭션이 발생하지 않으면 데이터베이스 다이제스트가 생성 및 업로드되지 않습니다. 이 메커니즘을 통해 데이터베이스에서 데이터가 업데이트된 경우에만 데이터베이스 다이제스트가 생성됩니다.

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="다이제스트 스토리지를 사용하도록 설정하기 위한 선택 옵션을 보여 주는 스크린샷"::: 

> [!IMPORTANT]
> 데이터베이스 다이제스트가 변조로부터 보호되도록 프로비전한 후에는 컨테이너에 [불변성 정책](../../storage/blobs/immutable-policy-configure-version-scope.md)을 구성합니다.

### <a name="manual-generation-and-storage-of-database-digests"></a>데이터베이스 다이제스트 수동 생성 및 스토리지

또한 Azure SQL Database 원장을 사용하면 사용자가 주문형 데이터베이스 다이제스트를 생성하여 신뢰할 수 있는 스토리지 대상으로 간주되는 모든 서비스 또는 디바이스에 다이제스트를 수동으로 저장할 수 있습니다. 예를 들어 온-프레미스 WORM(여러 번 읽은 후 쓰기) 디바이스를 대상으로 선택할 수 있습니다. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)에서 [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) 저장 프로시저를 실행하여 데이터베이스 다이제스트를 수동으로 생성됩니다.

> [!IMPORTANT]
> 데이터베이스 다이제스트를 생성하려면 **GENERATE LEDGER DIGEST** 권한이 필요합니다. 원장 테이블과 관련된 권한에 대한 자세한 내용은 [권한](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)을 참조하세요. 

```sql
EXECUTE sp_generate_database_ledger_digest
```

반환된 결과 집합은 단일 데이터 행입니다. 이러한 결과 집합을 다음과 같이 신뢰할 수 있는 스토리지 위치에 JSON 문서로 저장해야 합니다.

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>데이터베이스 확인

확인 프로세스는 모든 원장 및 기록 테이블을 검색합니다. 또한 해당 행의 SHA-256 해시를 다시 계산한 다음 확인 저장 프로시저에 전달된 데이터베이스 다이제스트 파일과 비교합니다. 

규모가 더 큰 원장 테이블의 경우 데이터베이스 확인은 리소스를 많이 사용하는 프로세스일 수 있습니다. 따라서 데이터베이스의 무결성을 확인해야 하는 경우에만 실행해야 합니다. 

데이터베이스 무결성을 자주 모니터링해야 하는 경우에는 매시간 또는 매일 확인 프로세스를 실행할 수 있습니다. 또는 데이터를 호스트하는 조직이 감사를 통과하고 데이터 무결성에 대한 암호화 증거를 제공해야 하는 경우에만 실행할 수 있습니다. 확인 비용을 줄이기 위해 원장은 개별 원장 테이블을 확인하거나 원장 테이블의 하위 집합만 확인하는 옵션을 제공합니다. 

[자동 다이제스트 스토리지를 사용](#database-verification-that-uses-automatic-digest-storage)하는지 아니면 [수동으로 다이제스트를 관리](#database-verification-that-uses-manual-digest-storage)하는지에 따라 두 개의 저장 프로시저를 통해 데이터베이스 확인을 수행합니다.

> [!IMPORTANT]
> 데이터베이스 확인에는 *원장 콘텐츠 보기* 권한이 필요합니다. 원장 테이블과 관련된 권한에 대한 자세한 내용은 [권한](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)을 참조하세요. 

### <a name="database-verification-that-uses-automatic-digest-storage"></a>자동 다이제스트 스토리지를 사용하는 데이터베이스 확인

데이터베이스 다이제스트의 생성 및 사용에 자동 다이제스트 스토리지를 사용할 경우 다이제스트 스토리지의 위치는 JSON 개체로 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) 시스템 카탈로그 보기에 있습니다. 데이터베이스 확인 실행은 [sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql) 시스템 저장 프로시저 실행으로 구성됩니다. 데이터베이스 다이제스트가 저장되도록 구성된 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) 시스템 카탈로그 뷰에서 JSON 개체를 지정합니다. 

자동 다이제스트 스토리지를 사용하면 원장 테이블의 수명 주기 동안 스토리지 위치를 변경할 수 있습니다.  예를 들어 Azure 변경 불가능 스토리지를 사용하여 다이제스트 파일을 저장하기 시작한 경우 나중에 Azure Confidential Ledger를 대신 사용하려면 그렇게 할 수 있습니다. 이 위치 변경은 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql)에 저장됩니다. 

여러 다이제스트 스토리지 위치를 사용할 때 실행 확인을 간소화하기 위해 다음 스크립트가 다이제스트의 위치를 가져오고 이러한 위치를 사용하여 확인을 실행합니다.

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-that-uses-manual-digest-storage"></a>수동 다이제스트 스토리지를 사용하는 데이터베이스 확인

데이터베이스 다이제스트를 생성하고 저장하기 위해 수동 다이제스트 스토리지를 사용하는 경우 다음 저장 프로시저를 사용하여 원장 데이터베이스를 확인합니다. 다이제스트의 JSON 콘텐츠가 저장 프로시저에 추가됩니다. 데이터베이스 확인을 실행할 때 데이터베이스의 모든 테이블 또는 특정 테이블을 확인하도록 선택할 수 있습니다. 

다음은 [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) 저장 프로시저의 구문입니다.

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

다음 코드는 확인을 위해 두 개의 다이제스트를 전달하여 [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) 저장 프로시저를 실행하는 예제입니다. 

```sql
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
'
```

`sp_verify_database_ledger` 및 `sp_verify_database_ledger_from_digest_storage`에 대한 반환 코드는 `0`(성공) 또는 `1`(실패)입니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 원장 개요](ledger-overview.md)
- [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)   
- [추가 전용 원장 테이블](ledger-append-only-ledger-tables.md)   
- [데이터베이스 원장](ledger-database-ledger.md)