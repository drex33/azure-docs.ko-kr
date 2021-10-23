---
title: temporal 테이블 시작하기
description: Azure SQL Database 및 Azure SQL Managed Instance에서 temporal 테이블 사용을 시작하는 방법을 알아봅니다.
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
ms.openlocfilehash: 945afcb2a4158ee4c80bd9d36a9697d6692d0d19
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250429"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance에서 temporal 테이블 시작하기
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

temporal 테이블은 사용자 지정 코딩을 필요로 하지 않고 데이터의 전체 변경 기록을 추적하고 분석할 수 있는 Azure SQL Database 및 Azure SQL Managed Instance의 프로그래밍 기능입니다. temporal 테이블은 특정 기간 내에서만 유효하기 때문에 저장된 팩트를 해석할 수 있도록 데이터를 시간 컨텍스트와 밀접하게 연결해둡니다. temporal 테이블의 이 속성을 사용하면 효율적인 시간 기반 분석을 수행할 수 있고 데이터 발전 과정에서 인사이트를 얻을 수 있습니다.

## <a name="temporal-scenario"></a>Temporal 시나리오

이 문서에서는 애플리케이션 시나리오에서 temporal 테이블을 활용하는 단계를 보여 줍니다. 처음부터 새로 개발되는 새 웹 사이트 또는 사용자 작업 분석으로 확장하려는 기존 웹 사이트에서 사용자 작업을 추적한다고 가정합니다. 이 간단한 예제에서는 일정 기간 동안 방문한 웹 페이지의 수가 Azure SQL Database 또는 Azure SQL Managed Instance에서 호스트되는 웹 사이트 데이터베이스에서 캡처되고 모니터링되어야 할 표식이라고 가정합니다. 사용자 작업 내역 분석의 목적은 웹 사이트를 다시 디자인하고 방문자에게 더 나은 환경을 제공하기 위한 입력을 가져오는 것입니다.

이 시나리오에 대한 데이터베이스 모델은 매우 간단합니다. 사용자 작업 메트릭은 단일 정수 필드인 **PageVisited** 로 표시되고 사용자 프로필에 대한 기본 정보와 함께 캡처됩니다. 또한 시간 기반 분석의 경우 각 사용자에 대해 일련의 행을 유지하며, 여기서 모든 행은 특정 기간 내에 특정 사용자가 방문한 페이지 수를 나타냅니다.

![스키마](./media/temporal-tables/AzureTemporal1.png)

다행스럽게도 이 작업 정보를 유지하기 위해 앱에서 애쓸 필요가 없습니다. temporal 테이블로 이 프로세스를 자동화하여 웹 사이트를 디자인하는 동안 충분한 유연성을 제공하고 데이터 분석 자체에 초점을 두는 데 시간을 할애합니다. 테이블이 `WebSiteInfo` [temporal 시스템 버전](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table)으로 구성되었는지 확인하는 것만 수행하면 됩니다. 이 시나리오에서 temporal 테이블을 활용하는 정확한 단계는 다음과 같습니다.

## <a name="step-1-configure-tables-as-temporal"></a>1단계: 임시로 테이블 구성

새로운 개발을 시작하거나 기존 애플리케이션을 업그레이드하는 여부에 따라 임시 테이블을 만들거나 임시 특성을 추가하여 기존 템플릿을 수정합니다. 일반적인 경우 시나리오는 이러한 두 옵션을 혼합하여 만들 수 있습니다. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)(SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)(SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 또는 기타 Transact-SQL 개발 도구를 사용하여 이러한 작업을 수행합니다.

> [!IMPORTANT]
> Azure SQL Database 및 Azure SQL Managed Instance에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>새 테이블 만들기

SSMS 개체 탐색기 상황에 맞는 메뉴 항목 "새 System-Versioned 테이블"을 사용하여 임시 테이블 템플릿 스크립트로 쿼리 편집기를 연 다음"템플릿 매개 변수 값 지정"(Ctrl+Shift+M)을 사용하여 템플릿을 채웁다.

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

SSDT에서 데이터베이스 프로젝트에 새 항목을 추가할 때 "임시 테이블(시스템 버전)" 템플릿을 선택합니다. 그러면 테이블 디자이너가 열리고 쉽게 테이블 레이아웃을 지정할 수 있습니다.

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

아래 예제와 같이 Transact-SQL 문을 직접 지정하여 임시 테이블을 만들 수도 있습니다. 모든 임시 테이블의 필수 요소는 기간 정의이며 다른 사용자 테이블에 대한 참조를 포함하는 SYSTEM_VERSIONING 절은 과거 행 버전을 저장합니다.

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

시스템 버전 임시 테이블을 만들 경우 기본 구성과 함께 제공된 기록 테이블은 자동으로 만들어집니다. 기본 기록 테이블은 페이지 압축을 사용하여 기간 열(종료, 시작)에 클러스터된 B-트리 인덱스를 포함합니다. 이 구성은 임시 테이블이 사용되는 대부분의 시나리오, 특히 [데이터 감사](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit)에 대해 최적화됩니다.

이 경우에는 더 큰 데이터 세트으로 더 긴 데이터 기록에 걸친 시간 기반 추세 분석을 수행하려고 합니다. 그러므로 기록 테이블에 대한 스토리지로 클러스터된 columnstore 인덱스를 선택합니다. 클러스터된 columnstore는 분석 쿼리를 훌륭하게 압축하고 좋은 성능을 제공합니다. temporal 테이블은 현재 및 temporal 테이블에서 완전히 독립적으로 인덱스를 구성하는 유연성을 제공합니다.

> [!NOTE]
> Columnstore 인덱스는 중요 비즈니스용, 범용, 프리미엄 계층 및 S3 이상 표준 계층에서 사용할 수 있습니다.

다음 스크립트는 기록 테이블에서 기본 인덱스를 클러스터된 columnstore로 변경하는 방법을 보여줍니다.

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

해당 기록 테이블이 자식 노드로 표시되는 한편, temporal 테이블은 쉽게 식별할 수 있도록 개체 탐색기에 특정 아이콘으로 표시됩니다.

![AlterTable](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>기존 테이블을 임시 테이블로 변경

WebsiteUserInfo 테이블이 이미 있지만 변경 기록을 유지하도록 설계되지 않은 대체 시나리오를 살펴보겠습니다. 이 경우에 다음 예제와 같이 기존 테이블을 간단히 확장하여 임시 테이블을 만들 수 있습니다.

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>2단계: 정기적으로 워크로드 실행

temporal 테이블의 주요 장점은 변경 내용 추적을 사용하는 방식으로 웹 사이트를 변경하거나 조정할 필요가 없다는 것입니다. temporal 테이블은 한 번 만들면 데이터에 수정 작업을 수행할 때마다 이전 행 버전을 분명하게 유지합니다.

이 특정 시나리오에 대한 자동 변경 내용 추적을 활용하기 위해 사용자가 웹 사이트에서 세션을 종료할 때마다 **PagesVisited** 열을 업데이트하겠습니다.

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

업데이트 쿼리는 실제 작업이 발생한 정확한 시간이나 향후 분석을 위해 기록 데이터가 유지되는 방식을 알 필요가 없다는 점에 유의해야 합니다. 두 측면 모두 Azure SQL Database 및 Azure SQL Managed Instance에 의해 자동으로 처리됩니다. 다음 다이어그램에서는 모든 업데이트에서 기록 데이터를 생성하는 방법을 보여줍니다.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3단계: 과거 데이터 분석 수행

이제 임시 시스템 버전 기능을 사용하는 경우 과거 데이터 분석은 하나의 쿼리만 있으면 됩니다. 이 문서에서는 일반 분석 시나리오를 해결하는 몇 가지 예제를 제공합니다. 자세한 내용을 모두 알아보려면 [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) 절에 소개된 다양한 옵션을 탐색합니다.

1시간 전을 기준으로 방문한 웹 페이지의 수로 정렬된 상위 10명의 사용자를 확인하려면 다음 쿼리를 실행합니다.

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

하루 전, 한 달 전 또는 원하는 어떤 시점을 기준으로 사이트 방문을 분석하도록 이 쿼리를 쉽게 수정할 수 있습니다.

이전 날짜에 대한 기본 통계 분석을 수행하려면 다음 예제를 사용합니다.

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

시간 내에서 특정 사용자의 작업을 검색하려면 CONTAINED IN 절을 사용합니다.

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

그래픽 시각화는 경향과 사용 패턴을 직관적인 방식으로 손쉽게 표시할 수 있기에 임시 쿼리에 특히 편리합니다.

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>테이블 스키마 진화

일반적으로 앱 개발을 수행하는 동안 임시 테이블 스키마를 변경해야 합니다. 이를 위해 일반적인 ALTER TABLE 문을 실행하면 Azure SQL Database 또는 Azure SQL Managed Instance가 변경 내용을 기록 테이블에 적절하게 전달합니다. 다음 스크립트는 추적에 대한 추가 특성을 추가하는 방법을 보여줍니다.

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

마찬가지로 워크로드가 활성 상태인 동안 열 정의를 변경할 수 있습니다.

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

마지막으로 더 이상 필요하지 않은 열을 제거할 수 있습니다.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

또는 최신 [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) 를 사용하여 데이터베이스(온라인 모드) 또는 데이터베이스 프로젝트(오프라인 모드)의 일부에 연결되어 있는 동안 임시 테이블 스키마를 변경합니다.

## <a name="controlling-retention-of-historical-data"></a>과거 데이터의 보존 제어

기록 테이블에서는 시스템 버전 임시 테이블로 일반 테이블보다 데이터베이스 크기를 늘릴 수 있습니다. 점점 커지는 기록 테이블은 임시 쿼리에 대한 성능세를 부과할 뿐만 아니라 순수 스토리지 비용으로 인해 문제가 될 수 있습니다. 따라서 기록 테이블에서 데이터를 관리하기 위한 데이터 보존 정책을 개발하는 것이 모든 temporal 테이블의 수명 주기 계획 및 관리의 중요한 요소입니다. Azure SQL Database 및 Azure SQL Managed Instance로 temporal 테이블에서 과거 데이터를 관리하는 데 다음 방법 중 하나를 사용할 수 있습니다.

- [테이블 분할](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [사용자 지정 정리 스크립트](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>다음 단계

- temporal 테이블에 대한 자세한 내용은 [temporal 테이블](/sql/relational-databases/tables/temporal-tables)을 참조하세요.
