---
title: Azure SQL Database 추가 전용 원장 테이블
description: 이 문서에서는 Azure SQL Database의 추가 전용 원장 테이블 스키마 및 보기에 대한 정보를 제공합니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 9dc6979c94302a77013673cc7019f550d1fca85b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359189"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Azure SQL Database 추가 전용 원장 테이블

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

추가 전용 원장 테이블은 테이블 `INSERT` 작업만을 허용하여 데이터베이스 관리자와 같은 권한 있는 사용자가 기존 [데이터 조작 언어](/sql/t-sql/queries/queries)를 통해 데이터를 변조할 수 없도록 합니다. 추가 전용 원장 테이블은 보안 정보 이벤트 및 관리 시스템 또는 블록체인에서 데이터베이스로 데이터를 복제해야 하는 블록체인 시스템과 같이 레코드를 업데이트하거나 삭제하지 않는 시스템에 적합합니다. 추가 전용 테이블에 `UPDATE` 또는 `DELETE` 작업이 없기 때문에, [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)이 있으므로 해당하는 기록 테이블이 필요하지 않습니다.

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="원장 테이블의 아키텍처를 보여 주는 다이어그램":::

[CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 문에서 `LEDGER = ON` 인수를 지정하고 `APPEND_ONLY = ON` 옵션을 지정하여 추가 전용 원장 테이블을 만들 수 있습니다.

> [!IMPORTANT]
> 테이블을 원장 테이블로 만든 후에는 원장 기능이 없는 테이블로 되돌릴 수 없습니다. 결과적으로 공격자가 일시적으로 원장 기능을 제거하고 테이블을 변경한 다음, 원장 기능을 다시 사용하도록 설정하지 못하게 됩니다.

### <a name="append-only-ledger-table-schema"></a>추가 전용 원장 테이블 스키마

추가 전용 테이블에는 테이블을 변경한 트랜잭션과 트랜잭션에 의해 행이 업데이트된 작업의 순서를 알려주는 메타데이터가 포함된 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열이 있어야 합니다. 추가 전용 원장 테이블을 만들 때 `GENERATED ALWAYS` 열이 원장 테이블에 생성됩니다. 이 데이터는 시간이 지남에 따라 데이터가 삽입되는 방식을 이해하는 포렌식 용도에 유용합니다.

[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 문에서 `GENERATED ALWAYS` 열의 정의를 지정하지 않은 경우 시스템에서 이를 자동으로 추가하고 다음 기본 이름을 사용합니다.

| 기본 열 이름 | 데이터 형식 | Description |
|--|--|--|
| ledger_start_transaction_id | bigint | 행 버전을 만든 트랜잭션의 ID입니다. |
| ledger_start_sequence_number | bigint | 트랜잭션 내에서 행 버전을 만든 작업의 시퀀스 번호입니다. |

## <a name="ledger-view"></a>원장 보기

모든 추가 전용 원장 테이블에서 시스템은 원장 보기를 자동으로 생성합니다. 원장 보기는 테이블에서 발생한 모든 행 삽입을 보고합니다. 원장 보기는 추가 전용 원장 테이블에 `UPDATE` 또는 `DELETE` 기능이 없기 때문에 추가 전용 원장 테이블보다 [업데이트 가능한 원장 테이블](ledger-updatable-ledger-tables.md)에 주로 유용합니다. 추가 전용 원장 테이블의 원장 보기는 업데이트 가능한 원장 테이블 및 추가 전용 원장 테이블 간의 일관성을 유지하는 데 사용할 수 있습니다.

### <a name="ledger-view-schema"></a>원장 보기 스키마

> [!NOTE]
> [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 문에 `<ledger_view_option>` 매개 변수를 사용하여 테이블을 만들 때 원장 보기 열 이름을 사용자 지정할 수 있습니다. 자세한 내용은 [원장 보기 옵션](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) 및 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true)의 해당 예제를 참조하세요.

| 기본 열 이름 | 데이터 형식 | Description |
| --- | --- | --- |
| ledger_transaction_id | bigint | 행 버전을 만들거나 삭제한 트랜잭션의 ID입니다. |
| ledger_sequence_number | bigint | 테이블의 트랜잭션 내에서 행 수준 작업의 시퀀스 번호입니다. |
| ledger_operation_type_id | tinyint | `0`(**INSERT**) 또는 `1`(**DELETE**)을 포함합니다. 원장 테이블에 행을 삽입하면 이 열에 `0`을 포함하는 원장 보기에 새 행이 생성됩니다. 원장 테이블에서 행을 삭제하면 이 열에 `1`을 포함하는 원장 보기에 새 행이 생성됩니다. 원장 테이블에서 행을 업데이트하면 원장 보기에 2개의 새 행이 생성됩니다. 이 열에서 한 행에는 `1`(**DELETE**)이 포함되고 다른 행에는 `1`(**INSERT**)이 포함됩니다. 추가 전용 원장 테이블에서 DELETE가 발생하면 안 됩니다. |
| ledger_operation_type_desc | nvarchar(128) | `INSERT` 또는 `DELETE`를 포함합니다. 자세한 내용은 이전 행을 참조하세요. |

## <a name="next-steps"></a>다음 단계

- [추가 전용 원장 테이블 만들기 및 사용](ledger-how-to-append-only-ledger-tables.md)
- [업데이트 가능한 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md)
