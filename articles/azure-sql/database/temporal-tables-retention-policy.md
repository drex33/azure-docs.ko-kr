---
title: 임시 테이블에서 과거 데이터 관리
description: 임시 재방문 주기 정책을 사용하여 과거 데이터를 제어하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 10/18/2021
ms.openlocfilehash: d3cf9b884dd1d2610aabe0aa0869ab8ad528da01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216236"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>보존 정책을 사용하여 임시 테이블에서 과거 데이터 관리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

임시 테이블은 특히 과거 데이터를 장기간 보관할 경우에 일반 테이블보다 데이터베이스 크기를 더 늘릴 수 있습니다. 따라서 과거 데이터에 대한 재방문 주기 정책은 모든 임시 테이블의 수명 주기를 계획하고 관리하는 데 있어서 중요한 측면입니다. Azure SQL Database 및 Azure SQL Managed Instance의 임시 테이블은 이 작업을 수행하는 데 유용한 사용하기 쉬운 보존 메커니즘과 함께 제공됩니다.

Temporal 기록 보존은 개별 테이블 수준에서 구성할 수 있으며,이를 통해 사용자는 유연한 에이징 정책을 만들 수 있습니다. Temporal 보존 적용은 간단해서, 테이블을 만들거나 스키마를 변경할 때 매개 변수 하나만 설정하면 됩니다.

보존 정책을 정의한 후에는 Azure SQL Database 및 Azure SQL Managed Instance가 자동으로 데이터 정리에 적합한 기록 행이 있는지 정기적으로 확인하기 시작합니다. 일치하는 행을 식별하고 기록 테이블에서 제거하는 작업은 시스템에서 예약하고 실행하는 백그라운드 태스크로 투명하게 수행됩니다. 기록 테이블 행의 기간 조건은 SYSTEM_TIME 기간의 종료를 나타내는 열을 기준으로 확인됩니다. 예를 들어 보존 기간이 6개월로 설정된 경우 정리할 수 있는 테이블 행은 다음 조건을 만족합니다.

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

앞의 예에서는 **ValidTo** 행이 SYSTEM_TIME 기간의 끝에 해당한다고 가정합니다.

## <a name="how-to-configure-retention-policy"></a>보존 정책을 구성하는 방법

임시 테이블에 대한 재방문 주기 정책을 구성하기 전에 먼저 임시 기록 재방문 주기를 *데이터베이스 수준에서* 사용하도록 설정할지 확인합니다.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

데이터베이스 플래그 **is_temporal_history_retention_enabled** 는 기본적으로 ON으로 설정되어 있지만 사용자가 ALTER DATABASE 문을 사용하여 변경할 수 있습니다. 또한 이 플래그는 [특정 시점 복원](recovery-using-backups.md) 작업 후에 자동적으로 OFF로 설정됩니다. 데이터베이스에 대한 temporal 기록 보존 정리를 사용하도록 설정하려면 다음 문을 실행합니다.

```sql
ALTER DATABASE [<myDB>]
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> **is_temporal_history_retention_enabled** 이 OFF가 되더라도 임시 테이블에 대한 재방문 주기는 구성할 수 있지만 그 경우 오래된 행에 대한 자동 정리는 트리거되지 않습니다.

보존 정책은 테이블을 만들 때 HISTORY_RETENTION_PERIOD 매개 변수 값을 지정하여 구성합니다.

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database 및 Azure SQL Managed Instance를 통해 다른 시간 단위인 DAYS, WEEKS, MONTHS, YEARS를 사용하여 보존 기간을 지정할 수 있습니다. HISTORY_RETENTION_PERIOD를 생략하면 INFINITE 보존으로 간주됩니다. INFINITE 키워드를 명시적으로 사용할 수도 있습니다.

일부 시나리오에서는 테이블을 만든 후 보존을 구성할 수도 있고 이전에 구성한 값을 변경할 수도 있습니다. 이런 경우에 ALTER TABLE 문을 사용합니다.

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> SYSTEM_VERSIONING을 OFF로 설정하면 재방문 주기 기간 값을 *유지하지 않습니다*. HISTORY_RETENTION_PERIOD를 명시적으로 지정하지 않고 SYSTEM_VERSIONING을 ON으로 설정하면 재방문 주기 기간이 INFINITE가 됩니다.

보존 정책의 현재 상태를 살펴보려면 데이터베이스 수준에서 temporal 보존 사용 플래그를 개별 테이블의 보존 기간과 조인하는 다음 쿼리를 사용합니다.

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>행을 삭제하는 방법

정리 프로세스는 기록 테이블의 인덱스 레이아웃에 따라 달라집니다. *클러스터형 인덱스(B-트리 또는 columnstore)를 사용하는 기록 테이블에만 유한 보존 정책을 구성할 수 있다* 는 점을 기억해야 합니다. 보존 기간이 한정된 모든 temporal 테이블에 대한 오래된 데이터 정리를 수행하는 백그라운드 태스크가 만들어집니다.
rowstore(B-트리) 클러스터형 인덱스에 대한 정리 논리를 사용해 오래된 행을 더 작은 청크(최대 10K)로 삭제하여 데이터베이스 로그 및 IO 하위 시스템에 대한 압력을 최소화합니다. 정리 논리에서 필요한 B-트리 인덱스를 활용하긴 하지만 보존 기간보다 오래된 행의 삭제 순서를 확실히 보장할 수는 없습니다. 따라서 *애플리케이션의 정리 순서를 신뢰하지 마세요*.

클러스터형 columnstore에 대한 정리 작업은 전체 [행 그룹](/sql/relational-databases/indexes/columnstore-indexes-overview)(일반적으로 한 그룹에 백만 행 포함)을 한 번에 제거하여 매우 효율적이며 특히 과거 데이터가 고속으로 생성된 경우에 더욱 그렇습니다.

![클러스터형 columnstore 보존](./media/temporal-tables-retention-policy/cciretention.png)

클러스터형 columnstore 인덱스는 데이터 압축이 뛰어나고 보존 정리가 효율적이므로 작업에서 대량의 기록 데이터를 빠르게 생성하는 시나리오에 적합합니다. 해당 패턴은 변경 내용 추적 및 감사, 추세 분석 또는 IoT 데이터 수집에 대해 [임시 테이블을 사용하는 집약적 트랜잭션 처리 워크로드](/sql/relational-databases/tables/temporal-table-usage-scenarios)에 일반적입니다.

## <a name="index-considerations"></a>인덱스 고려 사항

Rowstore 클러스터형 인덱스가 있는 테이블에 대한 정리 작업은 SYSTEM_TIME 기간의 끝에 해당하는 열로 시작하는 인덱스를 필요로 합니다. 이러한 인덱스가 없는 경우 한정된 재방문 주기 기간을 구성할 수 없습니다.

*Msg 13765, Level 16, State 1 <br></br> 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' 기록 테이블에 필수 클러스터형 인덱스가 포함되어 있지 않아 'temporalstagetestdb.dbo.WebsiteUserInfo' 시스템 버전 임시 테이블에서 한정된 보존 기간을 설정하지 못했습니다. SYSTEM_TIME 기간의 끝과 일치하는 열로 시작되는 클러스터형 columnstore 또는 B-트리 인덱스를 기록 테이블에 만들어 보세요.*

Azure SQL Database 및 Azure SQL Managed Instance에서 만든 기본 기록 테이블에 보존 정책과 호환되는 클러스터형 인덱스가 이미 있는지 유의해야 합니다. 재방문 주기 기간이 한정된 테이블에서 해당 인덱스를 제거하려 하면 다음 오류와 함께 작업이 실패합니다.

*Msg 13766, Level 16, State 1 <br></br> 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' 클러스터형 인덱스는 오래된 데이터의 자동 정리에 사용되고 있으므로 삭제할 수 없습니다. 이 인덱스를 삭제해야 하는 경우 해당 시스템 버전 임시 테이블에서 HISTORY_RETENTION_PERIOD를 INFINITE로 설정해 보세요.*

클러스터형 columnstore 인덱스 상의 정리는 기록 행이 오름차순(기간 열 끝의 순서로 정렬됨)으로 삽입된 경우 최적으로 작동합니다. 특히 기록 테이블이 SYSTEM_VERSIONIOING 메커니즘에 의해 단독으로 채워질 때 항상 그렇습니다. 기록 테이블의 행이 기간 열(기존 과거 데이터를 마이그레이션한 경우에 해당될 수 있음)의 끝을 기준으로 정렬되지 경우, 최적의 성능을 얻으려면 제대로 정렬된 B-트리 rowstore 인덱스 위에 클러스터형 columnstore 인덱스를 다시 만들어야 합니다.

시스템 버전 관리 작업에 의해 기본적으로 적용되는 행 그룹의 순서가 변경될 수 있으므로 한정된 재방문 주기를 사용하여 기록 테이블에 클러스터형 columnstore 인덱스를 다시 작성하지 마십시오. 기록 테이블의 클러스터형 columnstore 인덱스를 다시 작성해야 할 경우 호환성 B-트리 인덱스의 상단에 재생성하여 정기적인 데이터 정리를 위해 필요한 행 그룹의 순서를 유지합니다. 데이터 순서가 보장되지 않은 클러스터형 열 인덱스를 가진 기존 기록 테이블로 임시 테이블을 만들면 동일한 방법을 취해야 합니다.

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

클러스터형 columnstore 인덱스가 있는 기록 테이블에 대해 재방문 주기 기간을 한정되게 구성한다면 해당 테이블에 추가로 비클러스터형 B-트리 인덱스를 생성할 수 없습니다.

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

위의 문을 실행하면 다음 오류와 함께 실패합니다.

*Msg 13772, Level 16, State 1 <br></br> 임시 기록 테이블인 'WebsiteUserInfoHistory'에는 한정된 재방문 주기 기간 및 클러스터형 columnstore 인덱스가 정의되어 있기 때문에 비클러스터형 인덱스를 생성할 수 없습니다.*

## <a name="querying-tables-with-retention-policy"></a>재방문 주기 정책을 사용한 테이블 쿼리

임시 테이블에 대한 모든 쿼리는 오래된 행이 정리 작업에 의해 자동으로 *언제든지 임의의 순서로* 삭제될 수 있기 때문에 예측할 수 없고 일관되지 않은 결과를 방지하기 위해 유한한 재방문 주기 정책과 일치하는 기록 행을 자동으로 걸러냅니다.

다음 그림은 간단한 쿼리에 대한 쿼리 계획을 보여줍니다.

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

쿼리 계획에는 기록 테이블(강조 표시)에 있는 Clustered Index Scan 연산자의 기간 열 (ValidTo) 끝에 적용 되는 추가 필터가 포함됩니다. 이 예는 한 달 재방문 주기 기간이 WebsiteUserInfo 테이블에 설정되었다고 가정합니다.

![재방문 주기 쿼리 필터](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

그러나 기록 테이블을 직접 쿼리한다면 지정된 재방문 주기 기간보다 오래된 행을 볼 수도 있을테지만 쿼리 결과가 반복되리라는 보장은 없습니다. 다음 그림은 추가 필터를 적용하지 않고 기록 테이블에 대한 쿼리 계획 실행을 보여줍니다.

![재방문 주기 필터를 사용하지 않은 기록 쿼리](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

일관되지 않거나 예측할 수 없는 결과가 나올 수도 있기 때문에 재방문 주기 기간을 초과하여 기록 테이블을 읽는 비즈니스 논리에 의존하지 마십시오. 임시 테이블에 있는 데이터를 분석하려면 FOR SYSTEM_TIME 절을 사용한 임시 쿼리를 하는 것이 좋습니다.

## <a name="point-in-time-restore-considerations"></a>특정 시점 복원 고려 사항

[기존 데이터베이스를 특정 시점으로 복원](recovery-using-backups.md)하여 새 데이터베이스를 만들 때 데이터베이스 수준에서 비활성화된 임시 재방문 주기가 있습니다. (**is_temporal_history_retention_enabled** 플래그 OFF로 설정). 이 기능을 사용하면 쿼리하기도 전에 오래된 행이 삭제되는 것에 대한 걱정 없이 복원 시 모든 기록 행을 검사할 수 있습니다. *구성된 재방문 주기를 초과하여 과거 데이터를 검사* 하기 위해 사용할 수 있습니다.

임시 테이블의 재방문 주기가 1 MONTH로 지정되었다고 가정해 봅시다. 데이터베이스가 프리미엄 서비스 계층에서 만들어진 경우 데이타베이스 상태를 35일 이전으로 돌려 데이터베이스 복사본을 만들 수 있습니다. 이를 통해 실질적으로 기록 테이블을 직접 쿼리하여 최대 65일 전까지의 기록 행을 분석할 수 있습니다.

임시 재방문 주기 정리를 활성화하고자 한다면 특정 시점 복원을 한 후 다음 TRANSACT-SQL 문을 실행합니다.

```sql
ALTER DATABASE [<myDB>]
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>다음 단계

응용 프로그램에서 임시 테이블을 사용하는 방법을 알아보려면 [임시 테이블 시작](../temporal-tables.md)을 확인하세요.

임시 테이블에 대한 자세한 내용은 [임시 테이블](/sql/relational-databases/tables/temporal-tables)을 검토하세요.
