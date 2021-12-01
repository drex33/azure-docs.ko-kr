---
title: Azure SQL Database 업데이트 가능한 원장 테이블
description: 이 문서에서는 Azure SQL Database의 업데이트 가능한 원장 테이블, 원장 스키마 및 원장 보기에 대한 정보를 제공합니다.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 55b2b8f82ae3a08cc102411ec794eedf25c00d34
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361498"
---
# <a name="azure-sql-database-updatable-ledger-tables"></a>Azure SQL Database 업데이트 가능한 원장 테이블

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

업데이트 가능한 원장 테이블은 사용자가 변조 방지 기능을 제공하면서 업데이트 및 삭제를 수행할 수 있는 시스템 버전 테이블입니다. 업데이트 또는 삭제가 발생하면 행의 모든 이전 버전이 기록 테이블이라고 하는 보조 테이블에 유지됩니다. 기록 테이블은 업데이트 가능 원장 테이블의 스키마를 미러링합니다. 행이 업데이트되면 행의 최신 버전은 원장 테이블에 남아 있고, 이전 버전은 시스템에 의해 애플리케이션과 투명하게 기록 테이블에 삽입됩니다. 

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="원장 테이블 아키텍처를 보여 주는 다이어그램":::

## <a name="updatable-ledger-tables-vs-temporal-tables"></a>업데이트 가능 원장 테이블과 임시 테이블의 비교

업데이트 가능 원장 테이블과 [임시 테이블](/sql/relational-databases/tables/temporal-tables)은 둘 다 시스템 버전 테이블이며, 데이터베이스 엔진은 두 테이블에 대한 기록 행 버전을 보조 기록 테이블에 캡처합니다. 두 기술은 각각 고유한 이점을 제공합니다. 업데이트 가능 원장 테이블은 현재 데이터와 기록 데이터의 변조를 방지합니다. 임시 테이블은 현재 시점의 올바른 데이터만이 아니라 임의 시점에 저장된 데이터 쿼리도 지원합니다.

업데이트 가능한 원장 테이블이자 임시 테이블인 테이블을 만들면 두 가지 기술을 함께 사용할 수 있습니다. 업데이트 가능한 원장 테이블은 다음 두 가지 방법으로 만들 수 있습니다.

- Azure Portal에서 새 데이터베이스를 만들 때 원장을 구성하는 동안 **이 데이터베이스의 모든 향후 테이블에서 원장 사용** 을 선택하거나 [CREATE DATABASE(Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) 문에서 `LEDGER = ON` 인수를 지정합니다. 이렇게 하면 원장 데이터베이스가 만들어지고, 데이터베이스에 만들어지는 모든 향후 테이블은 기본적으로 업데이트 가능한 원장 테이블이 됩니다.
- 데이터베이스 수준에서 원장을 사용하지 않는 새 테이블을 데이터베이스에 만들 때 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 문에서 `LEDGER = ON` 인수를 지정합니다.

T-SQL 문에서 `LEDGER` 인수를 지정할 때 사용할 수 있는 옵션에 대한 내용은 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql)을 참조하세요.

> [!IMPORTANT]
> 원장 테이블이 만들어진 후에는 원장 테이블이 아닌 테이블로 되돌릴 수 없습니다. 결과적으로 공격자가 원장 테이블의 원장 기능을 일시적으로 제거하고 변경한 다음, 원장 기능을 다시 사용하도록 설정하지 못하게 됩니다. 

### <a name="updatable-ledger-table-schema"></a>업데이트 가능한 원장 테이블 스키마

업데이트 가능한 원장 테이블에는 테이블을 변경한 트랜잭션과 트랜잭션에 의해 행이 업데이트된 작업의 순서를 알려주는 메타데이터가 포함된 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열이 있어야 합니다. 이 데이터는 시간이 지남에 따라 데이터가 삽입되는 방식을 이해하는 포렌식 용도에 유용합니다.

> [!NOTE]
> [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 문에서 원장 테이블 및 원장 기록 테이블의 필수 `GENERATED ALWAYS` 열을 지정하지 않으면 시스템에서 자동으로 열을 추가하고, 이 열은 다음 기본 이름을 사용합니다. 자세한 내용은 [업데이트 가능한 원장 테이블 만들기](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#x-creating-a-updatable-ledger-table)의 예제를 참조하세요.

| 기본 열 이름 | 데이터 형식 | Description |
| --- | --- | --- |
| ledger_start_transaction_id | bigint | 행 버전을 만든 트랜잭션의 ID입니다. |
| ledger_end_transaction_id | bigint | 행 버전을 삭제한 트랜잭션의 ID입니다. |
| ledger_start_sequence_number | bigint | 트랜잭션 내에서 행 버전을 만든 작업의 시퀀스 번호입니다. |
| ledger_end_sequence_number | bigint | 트랜잭션 내에서 행 버전을 삭제한 작업의 시퀀스 번호입니다. |

## <a name="history-table"></a>기록 테이블

기록 테이블은 업데이트 가능한 원장 테이블을 만들 때 자동으로 생성됩니다. 기록 테이블은 업데이트 가능한 원장 테이블의 업데이트 및 삭제로 인해 변경된 행의 기록 값을 캡처합니다. 기록 테이블의 스키마는 연결된 업데이트 가능한 원장 테이블의 스키마를 미러링합니다.

업데이트 가능한 원장 테이블을 만들 때 기록 테이블을 포함하는 스키마의 이름과 기록 테이블의 이름을 지정할 수도 있고, 시스템에서 기록 테이블의 이름을 생성하고 원장 테이블과 동일한 스키마에 추가하도록 할 수도 있습니다. 시스템에서 생성한 이름을 가진 기록 테이블을 익명 기록 테이블이라고 합니다. 익명 기록 테이블에 대한 명명 규칙은 `<schema>`.`<updatableledgertablename>`.MSSQL_LedgerHistoryFor_`<GUID>`입니다.

## <a name="ledger-view"></a>원장 보기

모든 업데이트 가능한 원장 테이블에서 시스템은 원장 보기를 자동으로 생성합니다. 원장 보기는 업데이트 가능한 원장 테이블과 관련 기록 테이블의 조인입니다. 원장 보기는 기록 데이터를 기록 테이블에 조인하여 업데이트 가능한 원장 테이블에서 발생한 모든 행 수정 사항을 보고합니다. 이 보기에서는 사용자, 파트너 또는 감사자가 모든 기록 작업을 분석하고 잠재적인 변조를 감지할 수 있습니다. 각 행 작업에는 작업이 `DELETE`인지 아니면 `INSERT`인지 여부와 함께 작동 트랜잭션의 ID가 제공됩니다. 사용자는 트랜잭션이 실행된 시간, 트랜잭션을 실행한 사용자의 ID에 대한 자세한 정보를 검색하고 이 트랜잭션에서 수행한 다른 작업과 상관 관계를 지정할 수 있습니다.

예를 들어 뱅킹 시나리오에 대한 트랜잭션 기록을 추적하려는 경우 원장 보기는 시간 경과에 따른 트랜잭션 기록을 제공합니다. 원장 보기를 사용하여 업데이트할 수 있는 원장 테이블 및 기록 테이블을 별도로 보거나 이 작업을 수행하기 위해 자체 보기를 생성할 필요는 없습니다.

원장 보기 사용에 대한 예제는 [업데이트 가능한 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md)을 참조하세요.

원장 보기의 스키마는 업데이트 가능한 원장 및 기록 테이블에 정의된 열을 미러링하지만 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 열은 업데이트 가능한 원장 및 기록 테이블의 열과 다릅니다.

### <a name="ledger-view-schema"></a>원장 보기 스키마

> [!NOTE]
> [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 문에 `<ledger_view_option>` 매개 변수를 사용하여 테이블을 만들 때 원장 보기 열 이름을 사용자 지정할 수 있습니다. 자세한 내용은 [원장 보기 옵션](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options) 및 [CREATE TABLE(Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true)의 해당 예제를 참조하세요.

| 기본 열 이름 | 데이터 형식 | Description |
| --- | --- | --- |
| ledger_transaction_id | bigint | 행 버전을 만들거나 삭제한 트랜잭션의 ID입니다. |
| ledger_sequence_number | bigint | 테이블의 트랜잭션 내에서 행 수준 작업의 시퀀스 번호입니다. |
| ledger_operation_type_id | tinyint | `0`(**INSERT**) 또는 `1`(**DELETE**)을 포함합니다. 원장 테이블에 행을 삽입하면 이 열에 `0`을 포함하는 원장 보기에 새 행이 생성됩니다. 원장 테이블에서 행을 삭제하면 이 열에 `1`을 포함하는 원장 보기에 새 행이 생성됩니다. 원장 테이블에서 행을 업데이트하면 원장 보기에 2개의 새 행이 생성됩니다. 이 열에서 한 행에는 `1`(**DELETE**)이 포함되고 다른 행에는 `1`(**INSERT**)이 포함됩니다. |
| ledger_operation_type_desc | nvarchar(128) | `INSERT` 또는 `DELETE`를 포함합니다. 자세한 내용은 이전 행을 참조하세요. |

## <a name="next-steps"></a>다음 단계
 
- [업데이트 가능한 원장 테이블 만들기 및 사용](ledger-how-to-updatable-ledger-tables.md)
- [추가 전용 원장 테이블 만들기 및 사용](ledger-how-to-append-only-ledger-tables.md) 
