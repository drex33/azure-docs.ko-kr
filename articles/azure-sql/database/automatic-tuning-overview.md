---
title: 자동 튜닝 개요
description: Azure SQL Database 및 Azure SQL Managed Instance는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적응합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: NikaKinska
ms.author: nnikolic
ms.reviewer: mathoma, wiassaf
ms.date: 03/23/2021
ms.openlocfilehash: 8ea21993103e5c461a8cea84f7739160338605a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536736"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance의 자동 튜닝
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance 자동 튜닝은 AI 및 기계 학습에 기반한 지속적인 성능 튜닝을 통해 최고의 성능과 안정적인 워크로드를 제공합니다.

자동 조정은 기본 제공 인텔리전스를 사용하여 데이터베이스에서 실행된 쿼리를 지속적으로 모니터링하고 해당 성능을 자동으로 개선하는 완전히 관리되는 지능형 성능 서비스입니다. 이것은 변화하는 워크로드에 동적으로 데이터베이스를 적용하고 튜닝 권장 사항을 적용함으로써 달성됩니다. 자동 조정은 AI를 통해 Azure의 모든 데이터베이스에서 수평적으로 학습하고 동적으로 조정 작업을 개선합니다. 자동 튜닝이 설정된 상태에서 데이터베이스를 오래 실행할수록 성능이 향상됩니다.

Azure SQL Database 및 Azure SQL Managed Instance 자동 튜닝은 안정적이고 최고 성능의 데이터베이스 워크로드를 제공하는 데 사용할 수 있는 가장 중요한 기능 중 하나일 수 있습니다.

## <a name="what-can-automatic-tuning-do-for-you"></a>자동 튜닝으로 무엇을 할 수 있나요?

- 데이터베이스의 자동화된 성능 튜닝
- 성능 향상의 자동화된 확인
- 자동화된 롤백 및 자체 수정
- 조정 기록
- 수동 배포를 위한 작업 T-SQL(Transact-SQL) 스크립트 튜닝
- 자동 관리 워크로드 성능 모니터링
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="safe-reliable-and-proven"></a>안전하고 신뢰할 수 있으며 검증됨

Azure SQL Database의 데이터베이스에 적용되는 튜닝 작업은 가장 집약적인 워크로드의 성능에 대해 완전히 안전합니다. 시스템은 사용자 워크로드를 방해하지 않도록 주의하여 설계되었습니다. 자동 조정 권장 사항은 사용률이 낮은 경우에만 적용됩니다. 시스템 또한 워크로드 성능을 보호하기 위해 일시적으로 자동 조정 작업을 사용하지 않도록 설정할 수도 있습니다. 이런 경우, Azure Portal에서 “시스템에서 사용할 수 없음” 메시지가 표시됩니다. 자동 조정 작업은 리소스 우선 순위가 가장 높은 워크로드를 인식합니다.

자동 조정 메커니즘은 더욱 진보하여 Azure에서 실행되는 수백만 개의 데이터베이스에서 완성도가 높아졌습니다. 적용된 자동 조정 작업이 자동으로 검증되어 워크로드 성능이 긍정적으로 향상됩니다. 회귀된 성능 권장 사항은 동적으로 감지되어 즉시 되돌려집니다. 기록된 튜닝 기록을 통해 Azure SQL Database 및 Azure SQL Managed Instance의 각 데이터베이스에 적용된 튜닝 개선 사항에 대한 명확한 추적이 있습니다.

![자동 조정 작동 방식](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL Database 자동 튜닝은 데이터베이스 엔진의 SQL Server 자동 튜닝 기능과 핵심 논리를 공유합니다. 기본 제공 인텔리전스 메커니즘에 대한 추가 기술 정보는 [SQL Server 자동 조정](/sql/relational-databases/automatic-tuning/automatic-tuning)을 참조하세요.

자동 조정이 작동하는 방법과 일반적인 사용 시나리오에 대한 개요는 다음에 포함된 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>자동 튜닝 사용

- [Azure Portal](automatic-tuning-enable.md) 또는 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) T-SQL 문을 사용하여 Azure SQL Database에 대해 자동 튜닝을 사용하도록 설정합니다.
- [대체 데이터베이스](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current&preserve-view=true) T-SQL 문을 사용하여 Azure SQL Managed Instance에 대한 자동 튜닝을 사용하도록 설정합니다.

## <a name="automatic-tuning-options"></a>자동 조정 옵션

Azure SQL Database 및 Azure SQL Managed Instance에서 사용할 수 있는 자동 튜닝 옵션은 다음과 같습니다.

| 자동 튜닝 옵션 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - 워크로드의 성능을 향상시킬 수 있는 인덱스를 식별하고, 인덱스를 만들며, 쿼리의 성능이 향상되었는지 자동으로 확인합니다. | 예 | 예 |
| **DROP INDEX** - 사용되지 않은 인덱스(지난 90일 동안) 및 중복 인덱스를 삭제합니다. 기본 키 및 고유 제약 조건을 지원하는 인덱스를 포함한 고유 인덱스는 삭제되지 않습니다. 이 옵션은 인덱스 힌트가 있는 쿼리가 워크로드에 있거나 워크로드가 파티션 전환을 수행할 때 자동으로 사용하지 않도록 설정할 수 있습니다. 프리미엄 및 중요 비즈니스용 서비스 계층에서 이 옵션은 사용되지 않는 인덱스를 삭제하지 않지만 중복 인덱스가 있는 경우 삭제합니다. | 예 | 예 |
| **FORCE LAST GOOD PLAN**(자동 계획 수정) - 이전 양호한 계획보다 느린 실행 계획을 사용하는 Azure SQL 쿼리와 회귀된 계획 대신 마지막으로 알려진 양호한 계획을 사용하는 쿼리를 식별합니다. | 예 | 예 |

### <a name="automatic-tuning-for-sql-database"></a>SQL Database에 대한 자동 튜닝

Azure SQL Database에 대한 자동 튜닝은 **CREATE INDEX**, **DROP INDEX** 및 **FORCE LAST GOOD PLAN** 데이터베이스 관리자 권장 사항을 사용하여 데이터베이스 성능을 최적화합니다. 자세한 내용은 [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction), [REST API](/rest/api/sql/serverautomatictuning) 및 [Azure Portal의 데이터베이스 어드바이저 권장 사항](database-advisor-find-recommendations-portal.md)을 참조하세요.

Azure Portal을 사용하여 수동으로 튜닝 권장 사항을 적용하거나 자동 튜닝이 자동으로 튜닝 권장 사항을 적용하도록 할 수 있습니다. 시스템에서 조정 권장 사항이 자율적으로 적용되도록 하는 경우 좋은 점은 워크로드 성능에 긍정적인 이득이 있는지 자동으로 확인하고, 뛰어난 성능 향상이 감지되지 않는 경우에는 조정 권장 사항을 자동으로 되돌립니다. 자주 실행되지 않는 조정 권장 사항의 영향을 받는 쿼리에 대한 유효성 검사 단계는 최대 72시간이 소요될 수 있습니다.

T-SQL을 통해 튜닝 권장 사항을 적용하는 경우 자동 성능 유효성 검사 및 반전 메커니즘을 사용할 수 없습니다. 이러한 방식으로 적용되는 권장 사항은 활성 상태로 유지되며 24~48시간에 대한 튜닝 권장 사항 목록에 표시됩니다. 그 이후에 시스템에서 자동으로 인출합니다. 권장 사항을 더 일찍 제거하려면 Azure Portal에서 취소할 수 있습니다.

자동 튜닝 옵션은 데이터베이스별로 별도로 설정 또는 해제하거나 서버 수준에서 구성하고 서버에서 설정을 상속하는 모든 데이터베이스에서 적용할 수 있습니다. 서버는 자동 튜닝 설정에 대한 Azure 기본값을 상속할 수 있습니다. 현재 Azure에서는 기본적으로 FORCE_LAST_GOOD_PLAN 및 CREATE_INDEX는 사용하고 DROP_INDEX는 사용하지 않도록 설정됩니다.

> [!IMPORTANT]
> 2020년 3월부터 자동 튜닝에 대한 Azure 기본값의 변경 사항은 다음과 같이 적용됩니다.
>
> - 새 Azure 기본값은 다음과 같습니다. FORCE_LAST_GOOD_PLAN = 사용, CREATE_INDEX = 사용 안 함, DROP_INDEX = 사용 안 함.
> - 자동 튜닝 기본 설정이 구성되지 않은 기존 서버는 새 Azure 기본값을 상속하도록 자동으로 구성됩니다. 이는 현재 정의되지 않은 상태에서 자동 튜닝을 위한 서버 설정을 가지고 있는 모든 고객에게 적용됩니다.
> - 만들어진 새 서버는 새 Azure 기본값을 상속하도록 자동으로 구성됩니다(새 서버 생성 시 자동 튜닝 구성이 정의되지 않은 상태에 있었던 이전과 다름).

서버에서 자동 튜닝 옵션을 구성하고 부모 서버에 속한 데이터베이스에서 설정을 상속하면 많은 수의 데이터베이스에서 자동 튜닝 옵션의 관리를 단순화할 수 있으므로 자동 튜닝 구성에 권장되는 방법입니다.

자동 튜닝 권장 구성에 대한 이메일 알림을 작성하는 방법에 대한 자세한 내용은 [자동 튜닝에 대한 이메일 알림](automatic-tuning-email-notifications-configure.md)을 참조하세요.

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 자동 튜닝

SQL Managed Instance에 대한 자동 튜닝은 **FORCE LAST GOOD PLAN** 만 지원합니다. T-SQL을 통한 자동 튜닝 옵션 구성에 대한 자세한 내용은 [자동 튜닝에 자동 계획 수정 도입](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 및 [자동 계획 수정](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 자동 튜닝에 사용된 기본 제공 인텔리전스에 대한 내용은 [인공 지능으로 Azure SQL Databases 튜닝](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)을 참조하세요.
- 내부에서 자동 튜닝이 작동하는 방식을 알아보려면 [Microsoft Azure SQL Database에서 수백만 개의 데이터베이스 자동 인덱스 만들기](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)를 참조하세요.
