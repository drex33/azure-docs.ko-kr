---
title: 데이터베이스 원장
description: 이 문서에서는 Azure SQL Database에서 원장 데이터베이스 테이블 및 관련 보기에 대한 정보를 제공합니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: f5273208104853a276ae78e8debc685fa2a05c4d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064471"
---
# <a name="what-is-the-database-ledger"></a>데이터베이스 원장이란?

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

데이터베이스 원장은 Azure SQL Database 원장 기능의 일부입니다. 데이터베이스 원장이 원장 테이블에서 업데이트가 발생하는 동안 시간이 지남에 따라 데이터베이스 상태가 진화되면 데이터베이스 상태를 증분식으로 캡처합니다. 여기서는 블록체인 및 [Merkle 트리 데이터 구조](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals)를 논리적으로 사용합니다. 

데이터베이스의 상태를 캡처하려면 데이터베이스 원장은 모든 트랜잭션에 대한 항목을 저장합니다. 커밋 타임스탬프 및 실행한 사용자의 ID와 같은 트랜잭션에 대한 메타데이터를 캡처합니다. 또한 각 원장 테이블에서 업데이트된 행의 Merkle 트리 루트도 캡처합니다. 그런 다음, 이러한 항목이 변조 방지 데이터 구조에 추가되어 나중에 무결성을 확인할 수 있습니다.

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="원장 기능의 Merkle 트리를 보여 주는 다이어그램":::

Azure SQL Database 원장이 데이터 무결성을 제공하는 방법에 대한 자세한 내용은 [다이제스트 관리 및 데이터베이스 확인](ledger-digest-management-and-database-verification.md)을 참조하세요.

## <a name="where-are-database-transaction-and-block-data-stored"></a>데이터베이스 트랜잭션 및 블록 데이터는 어디에 저장되어 있나요?

트랜잭션 및 블록과 관련된 데이터는 두 개의 시스템 카탈로그 뷰에 행으로 물리적으로 저장됩니다.

- [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql): 데이터베이스 원장에서 각 트랜잭션의 정보를 포함하는 행을 유지합니다. 이 정보에는 이 트랜잭션이 속한 블록의 ID 및 블록 내의 트랜잭션 서수가 포함됩니다. 
- [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql): 블록 내의 트랜잭션에 대한 Merkle 트리의 루트 및 블록체인을 형성하기 위한 이전 블록의 해시를 포함하여 원장의 모든 블록에 대한 행을 유지 관리합니다.

데이터베이스 원장을 보려면 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)에서 다음 T-SQL 명령문을 실행합니다.

> [!IMPORTANT]
> 데이터베이스 원장을 보려면 **원장 콘텐츠 보기** 권한이 필요합니다. 원장 테이블과 관련된 권한에 대한 자세한 내용은 [권한](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)을 참조하세요. 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

다음은 데이터베이스 원장의 블록체인에서 하나의 블록을 구성하는 4개의 트랜잭션으로 구성된 원장 테이블의 예제입니다.

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="예제 원장 테이블 스크린샷":::

블록은 30초마다 닫히거나 사용자가 [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) 저장 프로시저를 실행하여 수동으로 데이터베이스 다이제스트를 생성하는 경우에 발생합니다. 

블록을 닫으면 새 트랜잭션이 새 블록에 삽입됩니다. 블록 생성 프로세스는 다음을 수행합니다.

1. 메모리 내 큐와 [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) 시스템 카탈로그 뷰의 ‘closed’ 블록에 속하는 모든 트랜잭션을 검색합니다.
1. 이러한 트랜잭션과 이전 블록의 해시에 대한 Merkle 트리 루트를 계산합니다.
1. [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) 시스템 카탈로그 뷰에 closed 블록을 유지합니다. 

이 업데이트는 일반 테이블 업데이트이므로 시스템에서 자동으로 지속성을 보장합니다. 단일 블록 체인을 유지하기 위해 이 작업은 단일 스레드 방식으로 진행됩니다. 그러나 트랜잭션 정보에 대한 해시를 계산하고 비동기식으로 수행되기 때문에 효율적입니다. 트랜잭션 성능에는 영향을 주지 않습니다.   

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 원장 개요](ledger-overview.md) 
- [보안 카탈로그 뷰(Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
