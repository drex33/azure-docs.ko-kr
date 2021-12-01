---
title: 원장 사용 테이블이 포함된 Azure SQL Database 감사 이벤트
description: Azure SQL Database 원장 감사 기능 개요
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: a00e783979d9d4b2d10eaaec2fa8836c597e6841
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133355360"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>원장 사용 테이블이 포함된 Azure SQL Database 감사 이벤트

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 공개 미리 보기로 제공됩니다.

원장 사용 테이블을 사용하여 포렌식 활동을 수행하면 원장 보기 및 데이터베이스 원장에 데이터가 캡처됩니다. 다른 작업 ID도 SQL 감사 로그에 추가됩니다. 다음 표에서는 이러한 새로운 감사 로깅 이벤트를 간략하게 설명합니다. 이벤트를 트리거하는 조건은 각 테이블을 따릅니다.

## <a name="enable-ledger"></a>원장 사용

| 열 | 값 |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트를 트리거하는 조건**: 새 원장 테이블을 만들거나 일반 테이블을 원장 테이블로 변환하는 경우.

## <a name="alter-ledger"></a>원장 변경

| 열 | 값 |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트를 트리거하는 조건**: 원장 테이블을 삭제 또는 이름을 변경하거나, 원장 테이블을 일반 테이블로 변환하거나, 원장 테이블의 열을 추가, 삭제 또는 이름을 변경하는 경우.


## <a name="generate-ledger-digest"></a>원장 다이제스트 생성

| 열 | 값 |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**이벤트를 트리거하는 조건**: 원장 다이제스트를 생성하는 경우.

## <a name="verify-ledger"></a>원장 확인

| 열 | 값 |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트를 트리거하는 조건**: 원장 다이제스트를 확인하는 경우.

## <a name="ledger-operation-group"></a>원장 작업 그룹

| 열 | 값 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**이벤트를 트리거하는 조건**: 해당 사항 없음

| 열 | 값 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**이벤트를 트리거하는 조건**: 해당 사항 없음 

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 및 Azure Synapse Analytics에 대한 감사](auditing-overview.md)
- [Azure SQL Database 원장 개요](ledger-overview.md)
- [빠른 시작: 원장이 활성화된 Azure SQL Database에서 데이터베이스 만들기](ledger-create-a-single-database-with-ledger-enabled.md)
